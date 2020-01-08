---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "67868978"
---
某些必应响应包括必应提供的缩略图的 URL。 可以调整缩略图的大小，也可以对其进行裁剪。 

> [!NOTE]
> 确保缩略图的大小和裁剪提供了搜索方案并尊重第三方权利，如必应搜索 API 使用和显示要求所要求的那样。


若要调整图像大小, 请在缩略图的 URL 中包括 w (width) 查询参数和/或。 以像素为单位指定宽和高。 例如：  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
如果仅指定宽度或仅指定高度查询参数, 必应保持图像的纵横比。 如果同时指定了 "宽度" 和 "高度" 并且未保持图像的原始纵横比, 必应将空白边距添加到图像的边框。 例如，如果在不裁剪的情况下将 480x359 大小的图像调整成 200x200 大小，则图像会充斥整个宽度，而高则包含 25 像素的白色填充内容，位于图像的顶部和底部。 如果图像大小为 359x480，则情形与上面类似，区别是图像的左右边缘包含白色填充内容。 如果裁剪图像，不会添加白色填充内容。  

 
下图显示缩略图的原始大小 (480x300)。  
  
![原始风景图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
下图显示调整为 200x200 大小后的图像。 保留了纵横比，顶部和底部边框填充了白色（为了显示填充内容，包括了黑色边框）。  
  
![调整大小后的风景图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



如果所指定的尺寸大于图像的原始宽度和高度，则会在图像左边框和顶部边框填充白色。  
  
若要裁剪图像，请包括 c（裁剪）查询参数。 下面是可以指定的可能值。  
  
- 4&mdash;盲比例  
- 7&mdash;智能比例  
  
如果请求进行“智能比例”裁剪 (c=7)，则在对图像进行裁剪时，会在保留图像纵横比的同时，从图像的感兴趣区域的中心向外进行裁剪。 感兴趣区域是图像中被必应确定为包含最重要内容的区域。 下图是感兴趣区域的示例。  
  
![感兴趣区域](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

如果在调整图像大小时请求进行“智能比例”裁剪，则图像在缩成请求的大小时会保留纵横比。 然后会根据调整后的尺寸对图像进行裁剪。 例如，如果调整后的宽度小于或等于高度，则会以感兴趣区域为中心，向左和向右裁剪图像。 否则会以感兴趣区域为中心，向上和向下裁剪图像。  
  
 
下图显示使用“智能比例”裁剪将图像缩成 200x200 大小的情形。  
  
![裁剪成 200x200 大小的风景图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
下图显示使用“智能比例”裁剪将图像缩成 200x100 大小的情形。  
   
![裁剪成 200x100 大小的风景图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
下图显示使用“智能比例”裁剪将图像缩成 100x200 大小的情形。  
  
![裁剪成 100x200 大小的风景图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



如果无法确定图像的感兴趣区域，必应会使用“盲比例”裁剪。  
  
如果请求“盲比例”裁剪 (c=4)，必应会根据以下规则来裁剪图像。  
  
- 如果 (原始图像宽度 / 原始图像高度) < (请求的图像宽度 / 请求的图像高度)，则会从左上角测量图像大小，在底部进行裁剪。  
- 如果 (原始图像宽度 / 原始图像高度) > (请求的图像宽度 / 请求的图像高度)，则会从中心测量图像大小，向左右进行裁剪。  



下面是 225x300 大小的纵向图。  
  
![原始向日葵图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
下图显示使用“盲比例”裁剪将图像缩成 200x200 大小的情形。 图像从左上角进行测量，因此会裁剪其底部。  
  
![裁剪成 200x200 大小的向日葵图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
下图显示使用“盲比例”裁剪将图像缩成 200x100 大小的情形。 图像从左上角进行测量，因此会裁剪其底部。  
  
![裁剪成 200x100 大小的向日葵图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
下图显示使用“盲比例”裁剪将图像缩成 100x200 大小的情形。 图像从中心进行测量，因此会裁剪其左侧和右侧。  
  
![裁剪成 100x200 大小的向日葵图](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

