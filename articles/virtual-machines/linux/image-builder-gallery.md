---
title: Linux VM 'Leri için Azure Image Builder & paylaşılan görüntü Galerisi 'ni kullanma (Önizleme)
description: Paylaşılan görüntü galerisinde bir görüntü sürümü oluşturmak için Azure Image Builder 'ı ve Azure CLı 'yi kullanmayı ve sonra görüntüyü küresel olarak dağıtmayı öğrenin.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: cf7f5ed4a27772a89d0356c60e6f7135786ca07d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695609"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Önizleme: bir Linux görüntüsü oluşturun ve paylaşılan bir görüntü galerisine dağıtın 

Bu makalede, [paylaşılan bir görüntü galerisinde](../shared-image-galleries.md)bir görüntü sürümü oluşturmak Için Azure Image Builder 'ı ve Azure CLI 'yi nasıl kullanabileceğinizi ve görüntüyü küresel olarak nasıl dağıtabileceğiniz gösterilmektedir. Bunu [Azure PowerShell](../windows/image-builder-gallery.md)kullanarak da yapabilirsiniz.


Görüntüyü yapılandırmak için bir Sample. JSON şablonu kullanacağız. Kullandığımız. JSON dosyası şurada: [helloImageTemplateforSIG.json](https://github.com/azure/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Görüntüyü paylaşılan bir görüntü galerisine dağıtmak için şablon, şablon bölümünün [değeri olarak parçalama](image-builder-json.md#distribute-sharedimage) `distribute` .

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

Bazı bilgi parçalarını sürekli olarak kullanacağız. bu nedenle, bu bilgileri depolamak için bazı değişkenler oluşturacağız.

Önizleme için, görüntü Oluşturucu yalnızca kaynak yönetilen görüntüyle aynı kaynak grubunda özel görüntüler oluşturmayı destekleyecektir. Bu örnekteki kaynak grubu adını kaynak yönetilen yansımanız ile aynı kaynak grubu olacak şekilde güncelleştirin.

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

Abonelik KIMLIĞINIZ için bir değişken oluşturun. Bunu kullanarak edinebilirsiniz `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Kaynak grubunu oluşturun.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Kullanıcı tarafından atanan bir kimlik oluşturma ve kaynak grubunda izinleri ayarlama
Image Builder, görüntüyü Azure Paylaşılan görüntü galerisine (SıG) eklemek için belirtilen [Kullanıcı kimliğini](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) kullanır. Bu örnekte, görüntüyü SıG 'a dağıtmayı gerçekleştirmeye yönelik ayrıntılı eylemlere sahip bir Azure rol tanımı oluşturacaksınız. Rol tanımı daha sonra kullanıcı kimliğine atanır.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
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
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>. JSON indirin ve yapılandırın

. JSON şablonunu indirin ve değişkenleriniz ile yapılandırın.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

Sonraki bölümde, galerideki görüntü sürümü oluşturulur. 

Görüntü yapılandırmasını Azure görüntü Oluşturucu hizmetine gönderme.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Görüntü derlemesini başlatın.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Görüntünün oluşturulması ve her iki bölgeye çoğaltılmasının biraz zaman alabilir. VM oluşturma işlemine geçmeden önce Bu bölüm bitene kadar bekleyin.


## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Azure Image Builder tarafından oluşturulan görüntü sürümünden bir VM oluşturun.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM ile SSH bağlantısı kurun.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Görüntünün, SSH bağlantınız kurulduğu anda *günün iletisiyle* özelleştirildiğini görmeniz gerekir!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık aynı görüntünün yeni bir sürümünü oluşturmak için görüntü sürümünü yeniden özelleştirmeyi denemek istiyorsanız, sonraki adımları atlayın ve [başka bir görüntü sürümü oluşturmak Için Azure Image Builder 'ı kullanma](image-builder-gallery-update-image-version.md)bölümüne gidin.


Bu işlem, oluşturulan görüntüyü ve diğer tüm kaynak dosyalarını siler. Kaynakları silmeden önce bu dağıtımla bitdiğinizden emin olun.

Görüntü Galerisi kaynaklarını silerken, bunları oluşturmak için kullanılan görüntü tanımını silebilmeniz için önce tüm görüntü sürümlerini silmeniz gerekir. Bir galeriyi silmek için, önce galerideki tüm görüntü tanımlarını silmiş olmanız gerekir.

Görüntü Oluşturucu şablonunu silin.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

İzin atamalarını, rolleri ve kimliği silme
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Görüntü Oluşturucu tarafından oluşturulan görüntü sürümünü alın, bu her zaman ile başlar `0.` ve ardından görüntü sürümünü siler

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

## <a name="next-steps"></a>Sonraki adımlar

[Azure Paylaşılan görüntü galerileri](../shared-image-galleries.md)hakkında daha fazla bilgi edinin.
