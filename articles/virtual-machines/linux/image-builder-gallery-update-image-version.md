---
title: Azure görüntü Oluşturucu (Önizleme) kullanarak var olan bir görüntü sürümünden yeni bir VM görüntü sürümü oluşturma
description: Linux 'ta Azure Image Builder kullanarak var olan bir görüntü sürümünden yeni bir VM görüntü sürümü oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 0887051ffa396f1eac8bc00dc2437b8e92bec45a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695643"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Önizleme: Linux 'ta Azure Image Builder kullanarak var olan bir görüntü sürümünden yeni bir VM görüntü sürümü oluşturma

Bu makalede, [paylaşılan görüntü galerisinde](../shared-image-galleries.md)var olan bir görüntü sürümü alma, güncelleştirme ve Galeri için yeni bir görüntü sürümü olarak yayımlama işlemlerinin nasıl yapılacağı gösterilir.

Görüntüyü yapılandırmak için bir Sample. JSON şablonu kullanacağız. Kullandığımız. JSON dosyası şurada: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Özellikleri kaydetme
Önizleme sırasında Azure Image Builder 'ı kullanmak için yeni özelliği kaydetmeniz gerekir.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Özellik kaydının durumunu denetleyin.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kaydınızı denetleyin.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Kayıtlı değilse, aşağıdakileri çalıştırın:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables-and-permissions"></a>Değişkenleri ve izinleri ayarla

Paylaşılan görüntü galerinizi oluşturmak için [görüntü oluştur ve paylaşılan görüntü galerisine dağıt](image-builder-gallery.md) ' ı kullandıysanız, ihtiyacımız olan değişkenlerden bazılarını zaten oluşturdunuz. Aksi takdirde, lütfen bu örnek için kullanılacak bazı değişkenleri ayarlayın.


```console
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

Abonelik KIMLIĞINIZ için bir değişken oluşturun. Bunu kullanarak edinebilirsiniz `az account show | grep id` .

```console
subscriptionID=<Subscription ID>
```

Güncelleştirmek istediğiniz görüntü sürümünü alın.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Kullanıcı tarafından atanan bir kimlik oluşturma ve kaynak grubunda izinleri ayarlama
Önceki örnekte Kullanıcı kimliğini ayarlamışsınız gibi, yalnızca kaynak KIMLIĞINI almanız gerekir, bu daha sonra şablona eklenecektir.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Zaten kendi paylaşılan görüntü galeriniz varsa ve önceki örneği izmediyseniz, kaynak grubuna erişmek için görüntü Oluşturucu için izinler atamanız gerekir, bu nedenle galeriye erişebilir. Lütfen [görüntü oluşturma ve paylaşılan görüntü galerisine dağıtma](image-builder-gallery.md) gibi adımları gözden geçirin.


## <a name="modify-helloimage-example"></a>Merhaba görüntü örneğini değiştirme
Burada. json dosyasını açarak kullanmak üzere olduğumuz örneği inceleyebilirsiniz: [Görüntü Oluşturucu şablon başvurusuyla](image-builder-json.md)birlikte [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) . 


. JSON örneğini indirin ve değişkenlerinizi yapılandırın. 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Görüntü oluşturma

Görüntü yapılandırmasını VM görüntü Oluşturucu hizmetine gönderme.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Görüntü derlemesini başlatın.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Sonraki adıma geçmeden önce görüntünün oluşturulup çoğaltılıncaya kadar bekleyin.


## <a name="create-the-vm"></a>Sanal makineyi oluşturma

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM 'nin genel IP adresini kullanarak VM 'ye bir SSH bağlantısı oluşturun.

```console
ssh azureuser@<pubIp>
```

SSH bağlantınız kurulduunda görüntünün "günün Iletisi" ile özelleştirildiğini görmeniz gerekir.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

`exit`Ssh bağlantısını kapatmak için yazın.

Ayrıca, galerinizin artık kullanabildiği görüntü sürümlerini de listeleyebilirsiniz.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan. json dosyasının bileşenleri hakkında daha fazla bilgi edinmek için bkz. [Görüntü Oluşturucu şablonu başvurusu](../linux/image-builder-json.md).
