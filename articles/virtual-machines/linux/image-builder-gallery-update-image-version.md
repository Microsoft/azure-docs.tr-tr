---
title: Azure Görüntü Oluşturucu (Önizleme) kullanarak mevcut bir görüntü sürümden yeni bir görüntü sürümü oluşturma
description: Yeni bir görüntü sürümü, Azure görüntü Oluşturucusu kullanarak mevcut bir görüntü sürümden oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 9155f6fc1243f0d2e4d63f2718ccfd6846ebbc50
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671493"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Önizleme: Azure görüntü Oluşturucusu kullanarak mevcut bir görüntü sürümden yeni bir görüntü sürümü oluşturma

Bu makalede nasıl varolan bir görüntü sürümü yapılacağını gösteren bir [paylaşılan görüntü Galerisi](shared-image-galleries.md)güncelleştirmek ve galeri için yeni bir görüntü sürümü olarak yayımlayın.

Biz örnek .json şablon görüntüsünü yapılandırmak için kullanır. Kullandığımız .json dosyası aşağıda verilmiştir: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Özellikleri kaydetme
Önizleme sırasında Azure Görüntü Oluşturucu kullanmak için yeni özelliği'ni kaydetmeniz gerekir.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Özellik kaydı durumunu denetleyin.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kaydınızı denetleyin.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Kayıtlı diyor değil, aşağıdaki komutu çalıştırın:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Değişkenleri ayarlama ve izinleri

Kullandıysanız [bir görüntü oluşturmak ve dağıtmak için paylaşılan bir görüntü Galerisine](image-builder-gallery.md) , paylaşılan görüntü Galerisi oluşturmak için zaten ihtiyacımız değişkenlerinin bazıları oluşturdunuz. Aksi halde, lütfen bu örnek için kullanılan bazı değişkenleri ayarlayın.

Önizleme için Görüntü Oluşturucu yalnızca aynı kaynak grubunda kaynak yönetilen görüntüyü özel görüntü oluşturmada destekleyecektir. Bu örnekte, kaynak yönetilen bir görüntü ile aynı kaynak grubunda olması için kaynak grubu adını güncelleştirin.


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Abonelik kimliğiniz için bir değişken oluşturun Bu kullanarak elde edebilirsiniz `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Güncelleştirmek istediğiniz görüntü sürümü alın.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Zaten kendi paylaşılan görüntü Galerisi varsa ve önceki örnekte izleyin değil, kaynak grubunu, Galeri erişebilmesi erişmek Görüntü Oluşturucu izinleri atamanız gerekir.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>HelloImage örneği değiştirin
Duyuyoruz hakkında burada .json dosyasını açarak kullanmak için örnek gözden geçirebilirsiniz: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) ile birlikte [Görüntü Oluşturucu şablon başvurusu](image-builder-json.md). 


.Json örneği indirin ve değişkenleri ile yapılandırın. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Görüntü oluşturma

VM Görüntü Oluşturucu hizmeti görüntü yapılandırmaya gönderin.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Görüntü derlemeyi Başlat.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Ve sonraki adıma geçmeden önce çoğaltma görüntüsü oluşturulana kadar bekleyin.


## <a name="create-the-vm"></a>Sanal makine oluşturma

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Bir VM'nin genel IP adresini kullanarak VM'ye SSH bağlantısı oluşturun.

```azurecli-interactive
ssh azureuser@<pubIp>
```

SSH bağlantınız kurulur hemen sonra görüntüyü "İletisi, günlük bir" özelleştirildiğinden görmeniz gerekir.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Tür `exit` SSH bağlantısını kapatın.

Ayrıca, galeride kullanıma sunulmuştur yansıma sürümü listeleyebilirsiniz.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan .json dosyası bileşenleri hakkında daha fazla bilgi için bkz. [Görüntü Oluşturucu şablon başvurusu](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).