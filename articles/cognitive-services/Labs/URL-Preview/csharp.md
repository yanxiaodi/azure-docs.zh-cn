---
title: 快速入门：项目 URL 预览，C#
titlesuffix: Azure Cognitive Services
description: 通过 C# 开始使用项目 URL 预览。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 7f900b95b30f6ab5298e98661e6922e4e4d360c7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706967"
---
# <a name="quickstart-url-preview-query-in-c"></a>快速入门：采用 C# 的 URL 预览查询

以下 C# 示例创建 SwiftKey 网站的 URL 预览： https://swiftkey.com/en 。

## <a name="prerequisites"></a>先决条件

需要使用 [Visual Studio 2017 或更高版本](https://www.visualstudio.com/downloads/)才能在 Windows 上运行此代码。 （免费的社区版也可以。）

获取[认知服务实验室](https://labs.cognitive.microsoft.com/en-us/project-answer-search)免费试用版的访问密钥

## <a name="code-scenario"></a>代码方案

以下 C# 代码创建 SwiftKey 网站的 URL 预览： https://swiftkey.com/en 。 

它是通过以下步骤实现的：
1. 声明变量，用于指定终结点和要预览的查询 URL。  
2. 创建请求。
3. 添加 Ocp-Apim-Subscription-Key 标头  。 
4. 以异步方式运行 Web 请求。 
5. 读取响应。
6. 将标头和 JSON 结果输出到控制台。

**源代码**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>运行应用程序

运行应用程序：

1. 在 Visual Studio 中创建一个新的控制台解决方案。
2. 将 `Program.cs` 替换为所提供的代码。
3. 将 `YOUR-ACCESS-KEY` 值替换为订阅的有效访问密钥。
4. 运行该程序。

## <a name="next-steps"></a>后续步骤
- [Java 快速入门](java-quickstart.md)
- [JavaScript 快速入门](javascript.md)
- [Node 快速入门](node-quickstart.md)
- [Python 快速入门](python-quickstart.md)
