---
title: 将 Azure 映像生成器与 Linux 虚拟机的映像库配合使用 (预览)
description: 通过 Azure 映像生成器和共享映像库创建 Linux 映像。
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 9fc624ab24cd98d0025fe2a34bf48c29b47c50e9
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695406"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>预览版：创建 Linux 映像并将其分发给共享映像库 

本文介绍如何使用 Azure 映像生成器在[共享映像库](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)中创建映像版本, 并在全局分布映像。


我们将使用示例 json 模板来配置映像。 我们使用的 json 文件是: [helloImageTemplateforSIG](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json)。 

若要将图像分发到共享图像库, 模板使用[sharedImage](image-builder-json.md#distribute-sharedimage)作为模板的`distribute`部分的值。

> [!IMPORTANT]
> Azure 映像生成器目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>注册功能
若要在预览期间使用 Azure 映像生成器, 需要注册新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

检查你的注册。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果未注册, 请运行以下内容:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>设置变量和权限 

我们将重复使用某些信息, 因此我们将创建一些变量来存储该信息。

对于预览, 映像生成器将仅支持在与源托管映像相同的资源组中创建自定义映像。 将此示例中的资源组名称更新为与源托管映像相同的资源组。

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

为订阅 ID 创建一个变量。 你可以使用`az account show | grep id`获取此。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

创建资源组。

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


向 Azure 映像生成器授予在该资源组中创建资源的权限。 `--assignee`该值是映像生成器服务的应用注册 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>创建映像定义和库

若要将图像生成器用于共享图像库, 需要具有现有的映像库和映像定义。 映像生成器将不会为您创建映像库和映像定义。

如果还没有要使用的库和图像定义, 请首先创建它们。 首先, 创建一个映像库。

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

然后, 创建映像定义。

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>下载并配置 json

下载 json 模板并将其配置为你的变量。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>创建映像版本

下一部分将在库中创建映像版本。 

将映像配置提交到 Azure 映像生成器服务。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

启动映像生成。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

创建图像并将其复制到这两个区域可能需要一段时间。 等待此部分完成, 然后再继续创建 VM。


## <a name="create-the-vm"></a>创建 VM

使用 Azure 映像生成器创建的映像版本创建 VM。

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

通过 SSH 登录到 VM。

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

一旦建立 SSH 连接, 就会看到该映像已自定义一*天的消息*!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>清理资源

如果现在想要尝试重新自定义映像版本来创建同一映像的新版本, 请跳过后续步骤, 并继续[使用 Azure 映像生成器创建另一个映像版本](image-builder-gallery-update-image-version.md)。


这将删除已创建的映像以及所有其他资源文件。 请确保在删除资源之前已经完成了此部署。

删除映像库资源时, 需要先删除所有映像版本, 然后才能删除用于创建它们的映像定义。 若要删除库, 首先需要删除库中的所有图像定义。

删除图像生成器模板。

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

获取映像生成器创建的映像版本, 该版本始终以`0.`开头, 然后删除映像版本

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


删除映像定义。

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

删除库。

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

删除该资源组。

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>后续步骤

了解有关[Azure 共享映像库](shared-image-galleries.md)的详细信息。