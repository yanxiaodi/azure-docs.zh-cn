---
title: Azure Key Vault 限制指南
description: Key Vault 限制可限制并发调用数，以防止过度使用资源。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: f10f40551701cafd94692afc0916972b1fd73aff
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883051"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 限制指南

限制进程可在启动后，用于限定对 Azure 服务发出的并发调用数，以防止过度使用资源。 Azure Key Vault (AKV) 能够处理大量请求。 在请求数过多的情况下，限制客户端请求可帮助 AKV 服务保持最佳性能和可靠性。

限制值因情况而异。 例如，如果正在执行大量写入，则发生限制的可能性会比仅执行读取高。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何处理其限制？

Key Vault 中的服务限制用于防止资源滥用，确保所有 Key Vault 客户端的服务质量。 当超过服务阈值时，Key Vault 会在一段时间内限制该客户端发出其他任何请求。 在这种情况下，Key Vault 返回 HTTP 状态代码 429（请求过多），请求失败。 此外，Key Vault 会跟踪向限制值返回 429 计数的失败请求。 

如果出现限制值较高的有效业务用例，请与我们联系。


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何针对服务限制来限制应用

以下是在服务受到限制时应实施的**最佳做法**：
- 减少每个请求的操作数。
- 减少请求频率。
- 避免立即重试。 
    - 发出的所有请求要符合使用限制。

实现应用的错误处理时，请使用 HTTP 错误代码 429 检测是否需要限制客户端。 如果请求再次失败，错误代码为 HTTP 429，则仍会遇到 Azure 服务限制。 请继续使用推荐的客户端限制方法，重试请求直至成功。

实现指数退避的代码如下所示。 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


在客户端 C\# 应用程序中使用此代码很简单。 下面的示例演示使用 HttpClient 类的方法。

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

请记住，此代码仅适用于概念证明。 

### <a name="recommended-client-side-throttling-method"></a>推荐的客户端限制方法

出现 HTTP 错误代码 429 时，请使用指数延迟方法开始限制客户端：

1. 等待 1 秒，然后重试请求
2. 如果仍受限制，请等待 2 秒，然后重试请求
3. 如果仍受限制，请等待 4 秒，然后重试请求
4. 如果仍受限制，请等待 8 秒，然后重试请求
5. 如果仍受限制，请等待 16 秒，然后重试请求

此时，应不会收到 HTTP 429 响应代码。

## <a name="see-also"></a>请参阅

若要深入了解 Microsoft 云中的限制，请参阅[限制模式](https://docs.microsoft.com/azure/architecture/patterns/throttling)。

