---
title: Azure geçiş sunucusu geçişini kullanarak VMware sanal makinelerini sunucu tarafı şifreleme (SSE) ve müşteri tarafından yönetilen anahtarlar (CMK) ile Azure 'a geçirme
description: Azure geçişi sunucu geçişini kullanarak VMware VM 'lerini sunucu tarafı şifreleme (SSE) ve müşteri tarafından yönetilen anahtarlar (CMK) ile Azure 'a geçirmeyi öğrenin
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751096"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>VMware VM 'lerini, sunucu tarafı şifreleme ve müşteri tarafından yönetilen anahtarlarla etkinleştirilen Azure VM 'lerine geçirin

Bu makalede, Azure geçişi sunucu geçişini (aracısız çoğaltma) kullanarak, müşteri tarafından yönetilen anahtarlar (CMK) ile sunucu tarafı şifreleme (SSE) kullanılarak, VMware VM 'lerinin Azure sanal makinelerine nasıl geçirileceği açıklanır.

Azure geçişi sunucusu geçiş portalı deneyimi, [VMware VM 'lerini aracısız çoğaltma Ile Azure 'a geçirmenize](tutorial-migrate-vmware.md) olanak sağlar. Portal deneyimi şu anda Azure 'daki çoğaltılan diskleriniz için SSE 'yi CMK ile açma özelliği sunmaz. Çoğaltılan diskler için CMK ile SSE 'yi açma özelliği şu anda yalnızca REST API aracılığıyla kullanılabilir. Bu makalede bir VMware sanal makinesini çoğaltmak ve Azure 'daki çoğaltılan diskleri CMK ile SSE kullanacak şekilde yapılandırmak için bir [Azure Resource Manager şablonu](../azure-resource-manager/templates/overview.md) oluşturma ve dağıtma hakkında bilgi edineceksiniz.

Bu makaledeki örneklerde, Kaynak Yöneticisi şablonu oluşturmak ve dağıtmak için gereken görevleri gerçekleştirmek üzere [Azure PowerShell](/powershell/azure/new-azureps-module-az) kullanılır.

Yönetilen diskler için müşteri tarafından yönetilen anahtarlarla (CMK) sunucu tarafı şifreleme (SSE) hakkında [daha fazla bilgi edinin](../virtual-machines/disk-encryption.md) .

## <a name="prerequisites"></a>Önkoşullar

- Araç gereksinimlerini anlamak üzere aracısız çoğaltma ile VMware VM 'lerini Azure 'a geçirme [öğreticisini gözden geçirin](tutorial-migrate-vmware.md) .
- Azure geçişi projesi oluşturmak ve projeye **Azure geçişi: sunucu geçiş** aracını eklemek için [Bu yönergeleri izleyin](./create-manage-projects.md) .
- Şirket içi ortamınızda VMware için Azure geçişi gereci ayarlamak ve bulma işlemini gerçekleştirmek için [Bu yönergeleri izleyin](how-to-set-up-appliance-vmware.md) .

## <a name="prepare-for-replication"></a>Çoğaltmaya hazırlanma

VM bulma işlemi tamamlandıktan sonra, sunucu geçiş kutucuğunda bulunan sunucular satırı, Gereç tarafından bulunan VMware VM 'lerinin sayısını gösterir.

VM 'Leri çoğaltmaya başlayabilmeniz için önce çoğaltma altyapısının hazırlanması gerekir.

1. Hedef bölgede Service Bus bir örnek oluşturun. Service Bus, çoğaltma ve geçişi koordine etmek üzere sunucu geçiş hizmeti ile iletişim kurmak için şirket içi Azure geçişi gereci tarafından kullanılır.
2. İşlem günlüklerinin çoğaltmadan aktarılması için bir depolama hesabı oluşturun.
3. Azure geçişi gerecinin çoğaltma verilerini uygulamasına karşıya yüklemesi için bir depolama hesabı oluşturun.
4. Key Vault oluşturun ve adım 3 ve 4 ' te oluşturulan depolama hesaplarında blob erişimi için paylaşılan erişim imza belirteçlerini yönetmek üzere Key Vault yapılandırın.
5. Adım 1 ' de oluşturulan Service Bus için bir paylaşılan erişim imza belirteci oluşturun ve önceki adımda oluşturulan Key Vault belirteç için bir gizli dizi oluşturun.
6. Şirket içi Azure geçiş gereci (gereç AAD uygulaması kullanılarak) ve sunucu geçiş hizmeti 'nin Key Vault erişimini sağlamak için Key Vault erişim ilkesi oluşturun.
7. Bir çoğaltma ilkesi oluşturun ve önceki adımda oluşturulan çoğaltma altyapısının ayrıntıları ile sunucu geçiş hizmetini yapılandırın.

Çoğaltma altyapısının geçiş için hedef Azure bölgesinde ve VM 'Lerin geçirildiği hedef Azure aboneliğinde oluşturulması gerekir.

Sunucu geçiş portalı deneyimi, bir VM 'yi bir projede ilk kez çoğalttığınızda otomatik olarak bunu yaparak çoğaltma altyapısının hazırlanmasını basitleştirir. Bu makalede, Portal deneyimini kullanarak bir veya daha fazla VM 'yi zaten çoğalttığınızı ve çoğaltma altyapısının önceden oluşturulduğunu varsayacağız. Mevcut çoğaltma altyapısının ayrıntılarını bulma ve bu ayrıntıları CMK ile çoğaltmayı ayarlamak için kullanılacak Kaynak Yöneticisi şablonuna giriş olarak nasıl kullanacağınızı inceleyeceğiz.

### <a name="identifying-replication-infrastructure-components"></a>Çoğaltma altyapısı bileşenlerini tanımlama

1. Azure portal, kaynak grupları sayfasına gidin ve Azure geçişi projesinin oluşturulduğu kaynak grubunu seçin.
2. Sol menüden **dağıtımlar** ' ı seçin ve *"Microsoft. MigrateV2. VMwareV2EnableMigrate"* dizesiyle başlayarak bir dağıtım adı arayın. Bu projedeki VM 'Lerin çoğaltmasını ayarlamak için Portal deneyimi tarafından oluşturulan Kaynak Yöneticisi şablonlarının bir listesini görürsünüz. Bu tür bir şablonu indirecek ve şablonu CMK ile çoğaltmaya hazırlamak için temel olarak kullanacaksınız.
3. Şablonu indirmek için önceki adımda dize düzeniyle eşleşen herhangi bir dağıtım seçin > sol menüden **şablon** ' u seçin > en üstteki menüden **Indir** ' e tıklayın. template.jsdosyayı yerel olarak kaydedin. Bu şablon dosyasını son adımda düzenlersiniz.

## <a name="create-a-disk-encryption-set"></a>Disk şifreleme kümesi oluşturma

Bir disk şifreleme kümesi nesnesi yönetilen diskleri, SSE için kullanılacak CMK 'yi içeren bir Key Vault eşler. VM 'Leri CMK ile çoğaltmak için bir disk şifreleme kümesi oluşturacak ve bunu çoğaltma işlemine girdi olarak geçireceğiz.

Azure PowerShell kullanarak bir disk şifreleme kümesi oluşturmak için [buradaki](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) örneği izleyin. Disk şifrelemesi kümesinin, sanal makinelerin geçirildiği hedef abonelikte ve geçiş için hedef Azure bölgesinde oluşturulduğundan emin olun.

Disk şifreleme kümesi, yönetilen diskleri müşteri tarafından yönetilen bir anahtarla şifrelemek veya müşteri tarafından yönetilen anahtar ve bir platform anahtarı ile çift şifreleme için yapılandırılabilir. Rest 'de çift şifrelemeyi kullanmak için disk şifreleme kümesini [burada](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)açıklandığı gibi yapılandırın.

Aşağıda gösterilen örnekte, disk şifreleme kümesi, müşteri tarafından yönetilen bir anahtar kullanacak şekilde yapılandırılmıştır.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Geçirilecek VMware VM 'sinin ayrıntılarını alın

Bu adımda, geçirilmesi gereken sanal makinenin ayrıntılarını almak için Azure PowerShell kullanacaksınız. Bu ayrıntılar, çoğaltma için Kaynak Yöneticisi şablonu oluşturmak üzere kullanılacaktır. Özellikle, ilgilendiğiniz iki özellik şunlardır:

- Bulunan VM 'Ler için makine kaynak KIMLIĞI.
- Sanal makine ve bunların disk tanımlayıcıları için disk listesi.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

VM 'nin bulduğu Azure geçiş gerecine karşılık gelen siteID dizesinin değerini kopyalayın. Yukarıda gösterilen örnekte, siteID *"/Subscriptions/509099b2-9d2c-4636-B43E-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/Providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"* olur

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Geçirilecek makinenin RESOURCEID, ad ve disk UUID değerlerini kopyalayın.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Çoğaltma için Kaynak Yöneticisi şablonu oluşturma

- **Çoğaltma altyapısı bileşenlerini belirleme** adımında indirdiğiniz Kaynak Yöneticisi Şablon dosyasını seçtiğiniz bir düzenleyicide açın.
- *"Microsoft. RecoveryServices/Vaults/Replicationyapılar/replicationProtectionContainers/Replicationmigrationıtems"* türünde kaynaklar hariç tüm kaynak tanımlarını şablondan kaldırın
- Yukarıdaki türün birden fazla kaynak tanımı varsa, bunlardan birini kaldırın. Kaynak tanımından tüm **Bağımlıdson** özellik tanımlarını kaldırın.
- Bu adımın sonunda, aşağıdaki örnekte olduğu gibi görünen ve aynı özellik kümesine sahip bir dosyanız olmalıdır.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Kaynak tanımındaki **ad** özelliğini düzenleyin. Name özelliğindeki Last "/" öğesini izleyen dizeyi $machine değeriyle değiştirin *. Ad*(önceki adımdan).
- **Properties. providerSpecificDetails. Vmwaremachineıd** özelliğinin değerini $Machine değeri ile değiştirin *. RESOURCEID*(önceki adımdan).
- **Targetresourcegroupıd**, **targetnetworkıd**, **targetsubnetname** değerlerini, hedef kaynak grubu KIMLIĞI, hedef sanal ağ kaynak kimliği ve hedef alt ağ adı sırasıyla ayarlayın.
- Bu VM için Azure Hibrit Avantajı uygulamak için **LicenseType** değerini "windowsserver" olarak ayarlayın. Bu VM Azure Hibrit Avantajı uygun değilse, **LicenseType** değerini nolicensetype olarak ayarlayın.
- **Targetvmname** özelliğinin DEĞERINI geçirilen VM 'Nin istenen Azure sanal makine adıyla değiştirin.
- İsteğe bağlı olarak **Targetvmname** özelliğinin altında **targetvmsize** adlı bir özellik ekleyin. **Targetvmsize** özelliğinin DEĞERINI geçirilen VM Için istenen Azure sanal makine boyutu olarak ayarlayın.
- **Diskstoınclude** özelliği, tek bir şirket içi diski temsil eden her liste öğesiyle çoğaltma için disk girişlerinin bir listesidir. Şirket içi VM 'deki disk sayısı kadar çok sayıda liste öğesi oluşturun. Liste öğesindeki **DiskID** özelliğini, önceki adımda tanımlanan disklerin UUID 'si ile değiştirin. **Iosdisk** değerini VM 'nin işletim sistemi diski için "true", diğer tüm diskler için "false" olarak ayarlayın. **Logstorageaccountıd** ve **Logstorageaccountsassecretname** özelliklerini değiştirmeden bırakın. **Disktype** değerini disk Için kullanılacak Azure yönetilen disk türü (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) olarak ayarlayın. CMK ile şifrelenmesi gereken diskler için **Diskencryptionsetıd** adlı bir özellik ekleyin ve değeri oluşturulan disk şifreleme KÜMESININ kaynak kimliği ($des olarak ayarlayın **. Kimliği**) *disk şifreleme kümesi oluşturma* adımında
- Düzenlenmiş şablon dosyasını kaydedin. Yukarıdaki örnekte, düzenlenmiş şablon dosyası şu şekilde görünür:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Çoğaltmayı ayarlama

Artık, sanal makine için çoğaltmayı ayarlamak üzere düzenlenmiş Kaynak Yöneticisi şablonunu proje kaynak grubuna dağıtabilirsiniz. [Azure Resource Manager şablonları ve Azure PowerShell ile kaynak dağıtmayı](../azure-resource-manager/templates/deploy-powershell.md) öğrenin

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Sonraki adımlar

Portal deneyimi aracılığıyla [çoğaltma durumunu izleyin](tutorial-migrate-vmware.md#track-and-monitor) ve test geçişleri ile geçişini gerçekleştirin.