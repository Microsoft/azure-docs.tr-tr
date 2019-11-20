---
title: Azure Görüntü Oluşturucu (Önizleme) kullanarak mevcut bir görüntü sürümden yeni bir görüntü sürümü oluşturma
description: Yeni bir görüntü sürümü, Azure görüntü Oluşturucusu kullanarak mevcut bir görüntü sürümden oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d60a7680bc283ba015d0649fb1d2671f8e5cf793
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718626"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Önizleme: Azure görüntü Oluşturucusu kullanarak mevcut bir görüntü sürümden yeni bir görüntü sürümü oluşturma

Bu makalede nasıl varolan bir görüntü sürümü yapılacağını gösteren bir [paylaşılan görüntü Galerisi](shared-image-galleries.md)güncelleştirmek ve galeri için yeni bir görüntü sürümü olarak yayımlayın.

Biz örnek .json şablon görüntüsünü yapılandırmak için kullanır. Kullandığımız .json dosyası aşağıda verilmiştir: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

> [!IMPORTANT]
> Azure Görüntü Oluşturucu şu anda genel Önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
az provider show -n Microsoft.Compute | grep registrationState
```

Kayıtlı diyor değil, aşağıdaki komutu çalıştırın:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Değişkenleri ayarlama ve izinleri

Kullandıysanız [bir görüntü oluşturmak ve dağıtmak için paylaşılan bir görüntü Galerisine](image-builder-gallery.md) , paylaşılan görüntü Galerisi oluşturma için zaten ihtiyacımız değişkenleri oluşturdunuz. Aksi halde, lütfen bu örnek için kullanılan bazı değişkenleri ayarlayın.

Önizleme için Görüntü Oluşturucu yalnızca aynı kaynak grubunda kaynak yönetilen görüntüyü özel görüntü oluşturmada destekleyecektir. Bu örnekte, kaynak yönetilen bir görüntü ile aynı kaynak grubunda olması için kaynak grubu adını güncelleştirin.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Abonelik kimliğiniz için bir değişken oluşturun Bu kullanarak elde edebilirsiniz `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Güncelleştirmek istediğiniz görüntü sürümü alın.

```azurecli-interactive
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
Duyuyoruz hakkında burada .json dosyasını açarak kullanmak için örnek gözden geçirebilirsiniz: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) ile birlikte [Görüntü Oluşturucu şablon başvurusu](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


.Json örneği indirin ve değişkenleri ile yapılandırın. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Görüntü oluşturma

VM Görüntü Oluşturucu hizmeti görüntü yapılandırmaya gönderin.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Görüntü derlemeyi Başlat.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Ve sonraki adıma geçmeden önce çoğaltma görüntüsü oluşturulana kadar bekleyin.


## <a name="create-the-vm"></a>Sanal makine oluşturma

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Özelleştirme doğrulayın
Kullanıcı adı ve parola VM oluşturduğunuz sırada belirlediğiniz kullanarak VM'ye Uzak Masaüstü bağlantısı oluşturun. Sanal makine içinde bir komut istemi açıp:

```console
dir c:\
```

Şimdi iki dizini de görmeniz gerekir:
- `buildActions` Bu ilk görüntü sürümünde oluşturuldu.
- `buildActions2` Bu, ikinci görüntü sürümünü oluşturmak için ilk görüntü sürümü güncelleştirme oluşturan bir parçası olarak oluşturuldu.


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan .json dosyası bileşenleri hakkında daha fazla bilgi için bkz. [Görüntü Oluşturucu şablon başvurusu](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).