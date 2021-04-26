---
title: Azure Image Builder ile Windows VM oluşturma (Önizleme)
description: Azure görüntü Oluşturucu ile bir Windows sanal makinesi oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: b93d236d0b716bfaf7dfb45b21c9524ece75fcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764576"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Önizleme: Azure Image Builder ile Windows VM oluşturma

Bu makale, Azure VM görüntü Oluşturucusu 'nu kullanarak özelleştirilmiş bir Windows görüntüsünü nasıl oluşturabileceğiniz hakkında sizi gösterir. Bu makaledeki örnek, görüntüyü özelleştirmek için [özelleştiriciler](../linux/image-builder-json.md#properties-customize) kullanır:
- PowerShell (ScriptUri)- [PowerShell betiğini](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)indirip çalıştırın.
- Windows yeniden başlatma-VM 'yi yeniden başlatır.
- PowerShell (satır içi)-belirli bir komut çalıştırın. Bu örnekte, kullanarak VM üzerinde bir dizin oluşturur `mkdir c:\\buildActions` .
- Dosya-GitHub 'dan sanal makineye dosya kopyalama. Bu örnek, [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) öğesini `c:\buildArtifacts\index.html` sanal makineye kopyalar.
- Buildtimeoutınminutes-daha uzun süre çalışan derlemeler için derleme süresini artırın, varsayılan değer 240 dakikadır ve derleme süresini daha uzun süre çalışan yapılara izin verecek şekilde artırabilirsiniz.
- vmProfile-bir vmSize ve ağ özelliklerini belirtme
- osDiskSizeGB-görüntünün boyutunu artırabilirsiniz
- kimlik-Azure Image Builder 'ın derleme sırasında kullanması için bir kimlik sağlama


Ayrıca, bir de belirtebilirsiniz `buildTimeoutInMinutes` . Varsayılan değer 240 dakikadır ve bir derleme süresini daha uzun süre çalışan derlemeler için de artırabilirsiniz.

Görüntüyü yapılandırmak için bir Sample. JSON şablonu kullanacağız. Kullandığımız. JSON dosyası şurada: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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


## <a name="set-variables"></a>Değişkenleri ayarla

Bazı bilgi parçalarını sürekli olarak kullanacağız. bu nedenle, bu bilgileri depolamak için bazı değişkenler oluşturacağız.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Abonelik KIMLIĞINIZ için bir değişken oluşturun. Bunu kullanarak edinebilirsiniz `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Bu kaynak grubu, görüntü yapılandırma şablonu yapıtı ve görüntüsünü depolamak için kullanılır.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Kullanıcı tarafından atanan bir kimlik oluşturma ve kaynak grubunda izinleri ayarlama
Image Builder, görüntüyü kaynak grubuna eklemek için belirtilen [Kullanıcı kimliğini](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) kullanır. Bu örnekte, görüntüyü dağıtmayı gerçekleştirmeye yönelik ayrıntılı eylemlere sahip bir Azure rol tanımı oluşturacaksınız. Rol tanımı daha sonra kullanıcı kimliğine atanır.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma ve izin verme 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Görüntü yapılandırma şablonu örneğini indirin

Denemeniz için parametreli bir görüntü yapılandırma şablonu oluşturuldu. Örnek. json dosyasını indirin ve daha önce ayarladığınız değişkenlerle yapılandırın.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Bu örneği terminalde, gibi bir metin düzenleyicisi kullanarak değiştirebilirsiniz `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Kaynak görüntüde her zaman [bir sürüm belirtmeniz](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)gerekir `latest` .
> Görüntünün dağıtıldığı kaynak grubunu ekler veya değiştirirseniz, izinleri kaynak grubunda [ayarlamanız](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) gerekir.
 
## <a name="create-the-image"></a>Görüntü oluşturma

Görüntü yapılandırmasını VM görüntü Oluşturucu hizmetine gönderme

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Bu işlem tamamlandığında, bir başarı iletisini konsola geri döndürür ve içinde bir oluşturur `Image Builder Configuration Template` `$imageResourceGroup` . ' Gizli türleri göster ' i etkinleştirirseniz, bu kaynağı Azure portal kaynak grubunda görebilirsiniz.

Arka planda, görüntü Oluşturucu aynı zamanda aboneliğinizde bir hazırlama kaynak grubu oluşturur. Bu kaynak grubu, görüntü derlemesi için kullanılır. Bu biçimde olacaktır: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Hazırlama kaynak grubunu doğrudan silmemelidir. Önce görüntü şablonu yapıtını silin, bu, hazırlama kaynak grubunun silinmesine neden olur.

Hizmet, görüntü yapılandırma şablonu gönderimi sırasında bir hata bildirirse:
-  Bu [sorun giderme](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) adımlarını gözden geçirin. 
- Gönderimi yeniden denemeden önce aşağıdaki kod parçacığını kullanarak şablonu silmeniz gerekir.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Görüntü derlemesini Başlat
[Az Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action)komutunu kullanarak görüntü oluşturma işlemini başlatın.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Yapı tamamlanana kadar bekleyin. Bu, yaklaşık 15 dakika sürebilir.

Herhangi bir hatayla karşılaşırsanız lütfen bu [sorun giderme](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) adımlarını gözden geçirin.


## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Oluşturduğunuz görüntüyü kullanarak VM 'yi oluşturun. *\<password>* VM üzerindeki için kendi parolanızla değiştirin `aibuser` .

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Özelleştirmeyi doğrulama

VM 'yi oluştururken ayarladığınız Kullanıcı adını ve parolayı kullanarak VM 'ye bir Uzak Masaüstü bağlantısı oluşturun. VM 'nin içinde bir komut istemi açın ve şunu yazın:

```console
dir c:\
```

Görüntü özelleştirmesi sırasında oluşturulan bu iki dizini görmeniz gerekir:
- Builkactions
- Buildartifdavranır

## <a name="clean-up"></a>Temizleme

İşiniz bittiğinde, kaynakları silin.

### <a name="delete-the-image-builder-template"></a>Görüntü Oluşturucu şablonunu silme

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Rol atamasını, rol tanımını ve Kullanıcı kimliğini silin.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Görüntü kaynak grubunu sil

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan. json dosyasının bileşenleri hakkında daha fazla bilgi edinmek için bkz. [Görüntü Oluşturucu şablonu başvurusu](../linux/image-builder-json.md).
