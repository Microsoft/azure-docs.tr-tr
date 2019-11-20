---
title: Azure Service Fabric kümesini sertifika ortak adını kullanacak şekilde güncelleştirme | Microsoft Docs
description: Sertifika parmak izlerini kullanarak sertifika ortak adını kullanarak bir Service Fabric kümesini nasıl değiştireceğinizi öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: atsenthi
ms.openlocfilehash: a9e2a2c20a8de4bed3876f431566b293cbfa4ef5
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109669"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Sertifika parmak izine ait kümeyi ortak ada değiştirme
İki sertifika aynı parmak izine sahip olamaz, bu da küme sertifikası geçişi veya yönetimini zorlaştırır. Ancak, birden çok sertifika aynı ortak ada veya konuya sahip olabilir.  Sertifika ortak adlarını kullanmak için sertifika parmak izlerini kullanarak dağıtılan bir kümeyi değiştirmek, sertifika yönetimini çok daha kolay hale getirir. Bu makalede, çalışan bir Service Fabric kümesinin sertifika parmak izi yerine sertifika ortak adını kullanacak şekilde güncelleştirilmesi açıklanmaktadır.

>[!NOTE]
> Şablonunuzda belirtilen iki parmak izi varsa iki dağıtım gerçekleştirmeniz gerekir.  İlk dağıtım, bu makaledeki adımlardan önce yapılır.  İlk dağıtım şablondaki **parmak izi** özelliğini, kullanılan sertifikaya ayarlar ve parmak **printsecondary** özelliğini kaldırır.  İkinci dağıtım için, bu makaledeki adımları izleyin.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Sertifika Al
İlk olarak, bir [sertifika yetkilisinden (CA)](https://wikipedia.org/wiki/Certificate_authority)bir sertifika alın.  Sertifikanın ortak adı, kümenin ana bilgisayar adı olmalıdır.  Örneğin, "myclustername.southcentralus.cloudapp.azure.com".  

Sınama amacıyla, ücretsiz veya açık bir sertifika yetkilisinden CA imzalı bir sertifika edinebilirsiniz.

> [!NOTE]
> Azure portal bir Service Fabric kümesi dağıtımında oluşturulanlar dahil, otomatik olarak imzalanan sertifikalar desteklenmez.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Sertifikayı karşıya yükleyin ve ölçek kümesine yükleyin
Azure 'da bir Service Fabric kümesi, sanal makine ölçek kümesine dağıtılır.  Sertifikayı bir anahtar kasasına yükleyin ve ardından kümenin üzerinde çalıştığı sanal makine ölçek kümesine yükleyin.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Ölçek kümesi gizli dizileri, her gizli dizi sürümlü, benzersiz bir kaynak olduğundan, iki ayrı gizli dizi için aynı kaynak KIMLIĞINI desteklemez. 

## <a name="download-and-update-the-template-from-the-portal"></a>Şablonu portalda indirin ve güncelleştirin
Sertifika, temel alınan ölçek kümesine yüklendi, ancak aynı zamanda bu sertifikayı ve ortak adını kullanmak için Service Fabric kümesini güncelleştirmeniz gerekir.  Şimdi, küme dağıtımınızın şablonunu indirin.  [Azure Portal](https://portal.azure.com) oturum açın ve kümeyi barındıran kaynak grubuna gidin.  **Ayarlar**' da **dağıtımlar**' ı seçin.  En son dağıtımı seçin ve **şablonu görüntüle**' ye tıklayın.

![Şablonları görüntüle][image1]

Şablonu ve parametreleri JSON dosyalarını yerel bilgisayarınıza indirin.

İlk olarak, Parameters dosyasını bir metin düzenleyicisinde açın ve aşağıdaki parametre değerini ekleyin:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Sonra, şablon dosyasını bir metin düzenleyicisinde açın ve sertifika ortak adını desteklemek için üç güncelleştirme yapın.

1. **Parametreler** bölümünde, bir *certificatecommonname* parametresi ekleyin:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Ayrıca, *certificateThumbprint*öğesini kaldırmayı da düşünün, bu, artık kaynak yöneticisi şablonunda başvurulmayabilir.

2. **Microsoft. COMPUTE/virtualMachineScaleSets** kaynağında, sanal makine uzantısını parmak izi yerine sertifika ayarları 'nda ortak adı kullanacak şekilde güncelleştirin.  **Virtualmachineprofile**->**extensionprofile**->**extensions**->**Özellikler**->**Ayarlar**->**sertifika**, `"commonNames": ["[parameters('certificateCommonName')]"],` ekleyin ve `"thumbprint": "[parameters('certificateThumbprint')]",`Kaldır ' a bakın.
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  **Microsoft. ServiceFabric/kümeler** KAYNAĞıNDA, API sürümünü "2018-02-01" olarak güncelleştirin.  Ayrıca **commonnames** özelliği Ile bir **certificatecommonnames** ayarı ekleyin ve **sertifika** ayarını (parmak izi özelliği ile) aşağıdaki örnekte olduğu gibi kaldırın:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

Daha fazla bilgi için bkz [. parmak izi yerine sertifika ortak adı kullanan Service Fabric kümesi dağıtma.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>Güncelleştirilmiş şablonu dağıtma
Değişiklikleri yaptıktan sonra güncelleştirilmiş şablonu yeniden dağıtın.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Sonraki adımlar
* [Küme güvenliği](service-fabric-cluster-security.md)hakkında bilgi edinin.
* [Küme sertifikası geçişi](service-fabric-cluster-rollover-cert-cn.md) hakkında bilgi edinin
* [Küme sertifikalarını güncelleştirme ve yönetme](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
