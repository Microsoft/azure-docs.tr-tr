---
title: Windows sanal makineler için bir görüntü Galerisi ile Azure Image Builder kullanma (Önizleme)
description: Azure Image Builder ve paylaşılan görüntü Galerisi ile Windows görüntüleri oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 33f13c09a06885523298bd7c23744e79f68e5301
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698678"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Önizleme: Bir Windows görüntüsü oluşturun ve paylaşılan bir görüntü galerisine dağıtın 

Bu makale, [paylaşılan bir görüntü galerisinde](shared-image-galleries.md)bir görüntü sürümü oluşturmak Için Azure görüntü Oluşturucu 'yu nasıl kullanabileceğinizi gösterir ve ardından görüntüyü küresel olarak dağıtabilirsiniz.

Görüntüyü yapılandırmak için bir. JSON şablonu kullanacağız. Kullandığımız. JSON dosyası şu şekildedir: [Helloımagetemplateforwinsig. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Görüntüyü paylaşılan bir görüntü galerisine dağıtmak için şablon, şablon `distribute` bölümünün değeri olarak [Parçalama](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) .

> [!IMPORTANT]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
```

Kayıtlı değilse, aşağıdakileri çalıştırın:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Değişkenleri ve izinleri ayarla 

Bazı bilgi parçalarını sürekli olarak kullanacağız. bu nedenle, bu bilgileri depolamak için bazı değişkenler oluşturacağız. `username` Ve`vmpassword`gibi değişkenlerin değerlerini kendi bilgileriniz ile değiştirin.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Abonelik KIMLIĞINIZ için bir değişken oluşturun. Bunu kullanarak `az account show | grep id`edinebilirsiniz.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Kaynak grubunu oluşturun.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Bu kaynak grubunda kaynak oluşturmak için Azure Image Builder iznini verin. `--assignee` Değer, görüntü Oluşturucu hizmeti için uygulama kayıt kimliğidir. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Görüntü tanımı ve Galeri oluşturma

Görüntü Oluşturucuyu paylaşılan bir görüntü Galerisi ile birlikte kullanmak için, var olan bir görüntü Galerisi ve görüntü tanımınız olması gerekir. Görüntü Oluşturucu, sizin için görüntü Galerisi ve görüntü tanımı oluşturmaz.

Kullanmak üzere bir galeri ve görüntü tanımınız yoksa, bunları oluşturarak başlayın. İlk olarak, bir görüntü galerisi oluşturun.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Ardından, bir görüntü tanımı oluşturun.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>. JSON indirin ve yapılandırın

. JSON şablonunu indirin ve değişkenleriniz ile yapılandırın.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

Sonraki bölümde, galerideki görüntü sürümü oluşturulur. 

Görüntü yapılandırmasını Azure görüntü Oluşturucu hizmetine gönderme.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Görüntü derlemesini başlatın.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Görüntünün oluşturulması ve her iki bölgeye çoğaltılmasının biraz zaman alabilir. VM oluşturma işlemine geçmeden önce Bu bölüm bitene kadar bekleyin.


## <a name="create-the-vm"></a>Sanal makine oluşturma

Azure Image Builder tarafından oluşturulan görüntü sürümünden bir VM oluşturun.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Özelleştirmeyi doğrulama
VM 'yi oluştururken ayarladığınız Kullanıcı adını ve parolayı kullanarak VM 'ye bir Uzak Masaüstü bağlantısı oluşturun. VM 'nin içinde bir komut istemi açın ve şunu yazın:

```console
dir c:\
```

Görüntü özelleştirmesi sırasında oluşturulmuş adlı `buildActions` bir dizin görmeniz gerekir.


## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık aynı görüntünün yeni bir sürümünü oluşturmak için görüntü sürümünü yeniden özelleştirmeyi denemek istiyorsanız, **Bu adımı atlayın** ve [başka bir görüntü sürümü oluşturmak Için Azure Image Builder 'ı kullanma](image-builder-gallery-update-image-version.md)sayfasına gidin.


Bu işlem, oluşturulan görüntüyü ve diğer tüm kaynak dosyalarını siler. Kaynakları silmeden önce bu dağıtımla bitdiğinizden emin olun.

Görüntü Galerisi kaynaklarını silerken, bunları oluşturmak için kullanılan görüntü tanımını silebilmeniz için önce tüm görüntü sürümlerini silmeniz gerekir. Bir galeriyi silmek için, önce galerideki tüm görüntü tanımlarını silmiş olmanız gerekir.

Görüntü Oluşturucu şablonunu silin.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Görüntü Oluşturucu tarafından oluşturulan görüntü sürümünü alın, bu her zaman ile `0.`başlar ve ardından görüntü sürümünü siler

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


Görüntü tanımını silin.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Galeriyi silin.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Kaynak grubunu silin.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Sonraki Adımlar

Oluşturduğunuz görüntü sürümünü güncelleştirme hakkında bilgi edinmek için bkz. [Azure Image Builder 'ı kullanarak başka bir görüntü sürümü oluşturma](image-builder-gallery-update-image-version.md).