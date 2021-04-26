---
title: Azure depolama 'daki dosyalara erişmek için bir sanal makine görüntüsü oluşturma ve Kullanıcı tarafından atanan yönetilen kimlik kullanma (Önizleme)
description: Azure depolama 'da depolanan dosyalara Kullanıcı tarafından atanan yönetilen kimlik kullanılarak erişebilen Azure Image Builder 'ı kullanarak sanal makine görüntüsü oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9bcb7a94cdf1d5478db32a22ba6e612a90c53ed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695388"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Azure Storage 'da dosyalara erişmek için bir görüntü oluşturma ve Kullanıcı tarafından atanan yönetilen kimlik kullanma 

Azure Image Builder, betikleri kullanmayı veya GitHub ve Azure depolama gibi birden çok konumdan dosya kopyalamayı destekler. Bunları kullanmak için Azure Image Builder 'ın dışarıdan erişimine açık olmaları gerekir, ancak SAS belirteçlerini kullanarak Azure Storage bloblarını koruyabilirsiniz.

Bu makalede, Azure VM görüntü Oluşturucu kullanılarak özelleştirilmiş bir görüntünün nasıl oluşturulduğu gösterilir. burada, hizmet, dosyaları herkese açık bir şekilde erişilebilir hale getirmek veya SAS belirteçleri ayarlamak zorunda kalmadan görüntü özelleştirmesi için Azure depolama 'daki dosyalara erişmek üzere [Kullanıcı tarafından atanan bir yönetilen kimlik](../../active-directory/managed-identities-azure-resources/overview.md) kullanır.

Aşağıdaki örnekte, biri özel görüntü için kullanılacak olan iki kaynak grubu oluşturacaksınız ve diğeri bir betik dosyası içeren bir Azure depolama hesabı barındıracaktır. Bu, görüntü Oluşturucu dışında, derleme yapıtlarının veya farklı depolama hesaplarında görüntü dosyalarının bulunduğu gerçek bir yaşam senaryosuna benzetir. Kullanıcı tarafından atanan bir kimlik oluşturacak ve sonra bu okuma izinlerini betik dosyasında verirsiniz, ancak bu dosyaya hiçbir ortak erişim ayarlayamezsiniz. Daha sonra bu betiği depolama hesabından indirmek ve çalıştırmak için kabuk Özelleştirici 'yi kullanacaksınız.


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


## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bazı bilgi parçalarını sürekli olarak kullanacağız. bu nedenle, bu bilgileri depolamak için bazı değişkenler oluşturacağız.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Abonelik KIMLIĞINIZ için bir değişken oluşturun. Bunu kullanarak edinebilirsiniz `az account show | grep id` .

```console
subscriptionID=<Your subscription ID>
```

Hem görüntü hem de betik depolaması için kaynak grupları oluşturun.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Kullanıcı tarafından atanan bir kimlik oluşturun ve kaynak grubunda izinleri ayarlayın.

Image Builder, görüntüyü kaynak grubuna eklemek için belirtilen [Kullanıcı kimliğini](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) kullanır. Bu örnekte, görüntüyü dağıtmayı gerçekleştirmeye yönelik ayrıntılı eylemlere sahip bir Azure rol tanımı oluşturacaksınız. Rol tanımı daha sonra kullanıcı kimliğine atanır.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Depolama alanını oluşturun ve örnek betiği GitHub 'dan buraya kopyalayın.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Görüntü kaynak grubunda kaynak oluşturmak için görüntü Oluşturucu iznini verin. `--assignee`Değer, Kullanıcı KIMLIĞI kimliğidir.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Örneği değiştirme

Örnek. json dosyasını indirin ve oluşturduğunuz değişkenlerle yapılandırın.

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Görüntü oluşturma

Görüntü yapılandırmasını Azure görüntü Oluşturucu hizmetine gönderme.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Görüntü derlemesini başlatın.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Oluşturma işleminin tamamlanmasını bekleyin. Bu, yaklaşık 15 dakika sürebilir.

## <a name="create-a-vm"></a>VM oluşturma

Görüntüden bir VM oluşturun. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM oluşturulduktan sonra VM ile bir SSH oturumu başlatın.

```console
ssh aibuser@<publicIp>
```

Görüntünün, SSH bağlantınız kurulduğu anda günün Iletisiyle özelleştirildiğini görmeniz gerekir!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Temizleme

İşiniz bittiğinde, kaynakları artık gerekmiyorsa silebilirsiniz.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Image Builder ile ilgili herhangi bir sorununuz varsa bkz. [sorun giderme](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).
