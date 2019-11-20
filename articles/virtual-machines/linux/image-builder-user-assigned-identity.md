---
title: Bir sanal makine görüntüsü oluşturma ve (Önizleme) Azure depolamadaki dosyalara erişmek için bir kullanıcı tarafından atanan bir yönetilen kimliği kullanma
description: Kullanıcı tarafından atanan yönetilen kimlik kullanarak Azure depolama alanında depolanmış dosyaları erişebileceği Azure görüntü Oluşturucusu kullanarak sanal makine görüntüsü oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: b6347765f8d2e21c352834dc8d28b65c28f99758
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671440"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Görüntü oluşturma ve Azure depolamadaki dosyalara erişmek için bir kullanıcı tarafından atanan bir yönetilen kimliği kullanma 

Betik kullanarak ya da birden fazla konuma, GitHub ve Azure depolama vb. gibi dosyaları kopyalama, azure Görüntü Oluşturucu destekler. Bunları kullanmak için Azure görüntü oluşturucusu için harici olarak erişilebilen verilmiş olması gerekir, ancak, SAS belirteçleri kullanarak Azure depolama blobları koruyabilir.

Bu makalede, Azure VM görüntüsü burada kullanılacak oluşturucu kullanılarak özelleştirilmiş bir görüntü oluşturma işlemi gösterilmektedir bir [yönetilen kullanıcı tarafından atanan kimliği](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure depolamadaki dosyalara görüntü özelleştirme olmadan olun gerek kalmadan erişmek için dosyalar genel anlamda erişilebilen veya SAS belirteçlerini ayarlama.

Aşağıdaki örnekte, iki kaynak grubu oluşturur, bir özel görüntü için kullanılır ve başka bir komut dosyası içeren bir Azure depolama hesabını barındıracak. Bu derleme yapıtları veya görüntü dosyaları Görüntü Oluşturucu dışında farklı depolama hesaplarında burada sahip olabilen bir gerçek yaşam senaryo benzetimini yapar. Bir kullanıcı tarafından atanan kimlik ve ardından komut dosyasını okuma izni oluşturur, ancak o dosya için herhangi bir genel erişimi ayarlamaz. Ardından, indirmek ve depolama hesabından bu betiği çalıştırmak için kabuk Özelleştirici kullanacaktır.


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
```

Kayıtlı diyor değil, aşağıdaki komutu çalıştırın:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bu bilgileri depolamak için bazı değişkenler oluşturacağız. böylece biz bazı bilgilere tekrar tekrar kullanacaklardır.


```azurecli-interactive
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

Abonelik kimliğiniz için bir değişken oluşturun Bu kullanarak elde edebilirsiniz `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Hem yansıma hem de betik depolama için kaynak grupları oluşturun.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Depolama alanı oluşturun ve içine Github'dan örnek komut dosyasını kopyalayın.

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
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Görüntü kaynak grubunda kaynak oluşturmak üzere Görüntü Oluşturucu izin verin. `--assignee` Uygulama kayıt kimliği Görüntü Oluşturucu hizmeti için bir değerdir. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Kimlik oluşturun ve betik depolama hesabı için izinler atayın. Daha fazla bilgi için [User-Assigned yönetilen kimliği](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Örneği değiştirin

Örnek .json dosyasını indirin ve oluşturduğunuz değişkenleri ile yapılandırın.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Görüntü oluşturma

Görüntü yapılandırma Azure Görüntü Oluşturucu hizmete gönderin.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Görüntü derlemeyi Başlat.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Derlemenin tamamlanmasını bekleyin. Bu işlem yaklaşık 15 dakika sürebilir.

## <a name="create-a-vm"></a>VM oluşturma

Görüntüden bir VM oluşturun. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM oluşturulduktan sonra VM ile bir SSH oturumu başlatın.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Görüntü içeren bir ileti, SSH bağlantı hemen sonra günün özelleştirildiğinden görmeniz gerekir!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Temizleme

İşlemi tamamladığınızda, kaynaklar artık gerekli değilse silebilirsiniz.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Görüntü Oluşturucu ile çalışan herhangi bir sorun varsa, bkz: [sorun giderme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).