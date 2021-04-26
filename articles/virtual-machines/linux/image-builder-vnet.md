---
title: Mevcut bir Azure sanal ağına erişime izin veren Linux VM 'Leri için Azure Image Builder 'ı kullanma (Önizleme)
description: Mevcut bir Azure sanal ağına erişime izin veren Azure Image Builder ile Linux VM görüntüleri oluşturma
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 500ddec9b84f9d73db45ddb4b7f5a8486a48d3e5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565317"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Mevcut bir Azure sanal ağına erişime izin veren Linux VM 'Leri için Azure Image Builder 'ı kullanma

Bu makalede, bir sanal ağ üzerindeki mevcut kaynaklara erişimi olan temel bir özelleştirilmiş Linux görüntüsü oluşturmak için Azure Image Builder 'ı nasıl kullanabileceğiniz gösterilmektedir. Oluşturduğunuz yapı sanal makinesi, aboneliğinizde belirttiğiniz yeni veya mevcut bir VNET 'e dağıtılır. Mevcut bir Azure VNET kullandığınızda, Azure görüntü Oluşturucu hizmeti genel ağ bağlantısı gerektirmez.

> [!IMPORTANT]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Özellikleri kaydetme

İlk olarak, Azure Image Builder hizmetine kaydolmanız gerekir. Kayıt, hizmet iznini hazırlama kaynak grubu oluşturma, yönetme ve silme izni verir. Ayrıca hizmet, görüntü derlemesi için gerekli olan grubu kaynak ekleme haklarına sahiptir.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Değişkenleri ve izinleri ayarla 

Bazı bilgi parçalarını sürekli olarak kullanacaksınız. Bu bilgileri depolamak için bazı değişkenler oluşturun.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Kaynak grubunu oluşturun.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Ağı yapılandırma

Mevcut bir VNET\Subnet\NSG yoksa, bir tane oluşturmak için aşağıdaki betiği kullanın.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Ağ güvenlik grubu kuralı ekle

Bu kural, Azure görüntü Oluşturucu yük dengeleyicisinden ara sanal makineye bağlantı sağlar. 60001 numaralı bağlantı noktası Linux OSs için ve 60000 numaralı bağlantı noktası Windows OSs içindir. Proxy VM, Linux OSs için 22 numaralı bağlantı noktasını veya Windows OSs için 5986 numaralı bağlantı noktasını kullanarak yapı VM 'sine bağlanır.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Alt ağda özel hizmet Ilkesini devre dışı bırak

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Görüntü Oluşturucu ağı hakkında daha fazla bilgi için bkz. [Azure görüntü Oluşturucu hizmeti ağ seçenekleri](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Örnek şablonu değiştirme ve rol oluşturma

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Kaynak grubunda izinleri ayarla

Image Builder, görüntüyü Azure Paylaşılan görüntü galerisine (SıG) eklemek için belirtilen [Kullanıcı kimliğini](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) kullanır. Bu örnekte, görüntüyü SıG 'a dağıtmayı gerçekleştirmeye yönelik ayrıntılı eylemlere sahip bir Azure rol tanımı oluşturacaksınız. Rol tanımı daha sonra kullanıcı kimliğine atanır.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Görüntü Oluşturucusu daha düşük ayrıntı düzeyi ve daha fazla ayrıcalık vermek yerine iki rol oluşturabilirsiniz. Biri, bir görüntü oluşturmak için Oluşturucu izinleri verir, diğeri ise derleme VM 'sini ve yük dengeleyiciyi sanal ağınıza bağlamanıza olanak tanır.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

İzinler hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure Image Builder hizmet Izinlerini yapılandırma](image-builder-permissions-cli.md) veya [PowerShell kullanarak Azure Image Builder hizmeti izinleri yapılandırma](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Görüntü oluşturma

Görüntü yapılandırmasını Azure görüntü Oluşturucu hizmetine gönderme.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Görüntü derlemesini başlatın.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Görüntünün oluşturulması ve her iki bölgeye çoğaltılmasının biraz zaman alabilir. VM oluşturma işlemine geçmeden önce Bu bölüm bitene kadar bekleyin.


## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Azure Image Builder tarafından oluşturulan görüntü sürümünden bir VM oluşturun.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM ile SSH bağlantısı kurun.

```bash
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

Artık aynı görüntünün yeni bir sürümünü oluşturmak için görüntü sürümünü yeniden ayarlamayı denemek istiyorsanız, sonraki adımları atlayın ve [başka bir görüntü sürümü oluşturmak Için Azure Image Builder 'ı kullanma](image-builder-gallery-update-image-version.md)bölümüne gidin.


Aşağıda, oluşturulan görüntü, diğer tüm kaynak dosyalarıyla birlikte silinir. Kaynakları silmeden önce bu dağıtımla bitdiğinizden emin olun.

Görüntü Galerisi kaynaklarını silerken, bunları oluşturmak için kullanılan görüntü tanımını silebilmeniz için önce tüm görüntü sürümlerini silmeniz gerekir. Bir galeriyi silmek için, önce galerideki tüm görüntü tanımlarını silmiş olmanız gerekir.

Görüntü Oluşturucu şablonunu silin.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

İzin atamalarını, rolleri ve kimliği silme
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Kaynak grubunu silin.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Bu hızlı başlangıç için bir VNET oluşturduysanız, artık kullanılmıyorsa VNET 'i silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Paylaşılan görüntü galerileri](../shared-image-galleries.md)hakkında daha fazla bilgi edinin.
