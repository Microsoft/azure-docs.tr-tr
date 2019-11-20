---
title: Sertifika ortak adını kullanarak bir Azure Service Fabric kümesi oluşturma | Microsoft Docs
description: Bir şablondan sertifika ortak adını kullanarak Service Fabric kümesi oluşturmayı öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: atsenthi
ms.openlocfilehash: 73e02b4482f69ec0c9d5a602f30cefea77279778
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764735"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Parmak izi yerine sertifika ortak adı kullanan bir Service Fabric kümesi dağıtma
İki sertifika aynı parmak izine sahip olamaz, bu da küme sertifikası geçişi veya yönetimini zorlaştırır. Ancak, birden çok sertifika aynı ortak ada veya konuya sahip olabilir.  Sertifika ortak adlarını kullanan bir küme, sertifika yönetimini çok daha kolay hale getirir. Bu makalede, sertifika parmak izi yerine sertifika ortak adını kullanmak üzere bir Service Fabric kümesinin nasıl dağıtılacağı açıklanır.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Sertifika Al
İlk olarak, bir [sertifika yetkilisinden (CA)](https://wikipedia.org/wiki/Certificate_authority)bir sertifika alın.  Sertifikanın ortak adı, sahip olduğunuz özel etki alanı için olmalıdır ve bir etki alanı kaydedicisinde satın alınmalıdır. Örneğin, "azureservicefabricbestpractices.com"; Microsoft çalışanları, MS etki alanları için sertifikaları sağlamayamadığından, LB veya Traffic Manager DNS adlarını sertifikanız için ortak adlar olarak kullanamazsınız ve özel etki alanınız için bir [Azure DNS bölgesi](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) sağlamanız gerekir. Azure 'da e. Ayrıca, portalınızın kümeniz için özel etki alanı diğer adını yansıtmasını istiyorsanız, kendi özel etki alanınızı kümenizin "managementEndpoint" olarak bildirmeniz gerekir.

Sınama amacıyla, ücretsiz veya açık bir sertifika yetkilisinden CA imzalı bir sertifika edinebilirsiniz.

> [!NOTE]
> Azure portal bir Service Fabric kümesi dağıtımında oluşturulanlar dahil, otomatik olarak imzalanan sertifikalar desteklenmez. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Sertifikayı bir anahtar kasasına yükleme
Azure 'da bir Service Fabric kümesi, sanal makine ölçek kümesine dağıtılır.  Sertifikayı bir anahtar kasasına yükleyin.  Küme dağıtıldıktan sonra, sertifika kümenin üzerinde çalıştığı sanal makine ölçek kümesine yüklenir.

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

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Örnek şablon indirme ve güncelleştirme
Bu makale, [5 düğümlü güvenli küme örnek](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) şablonunu ve şablon parametrelerini kullanır. *Azuredeploy. JSON* ve *azuredeploy. Parameters. JSON* dosyalarını bilgisayarınıza indirin.

### <a name="update-parameters-file"></a>Parametreleri Güncelleştir dosyası
İlk olarak, *azuredeploy. Parameters. JSON* dosyasını bir metin düzenleyicisinde açın ve şu parametre değerini ekleyin:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Ardından, *Certificatecommonname*, *Sourcevaultvalue*ve *certificateurlvalue* parametre değerlerini önceki komut dosyası tarafından döndürülen değerlere ayarlayın:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Şablon dosyasını güncelleştirme
Sonra, *azuredeploy. JSON* dosyasını bir metin düzenleyicisinde açın ve sertifika ortak adını desteklemek için üç güncelleştirme yapın.

1. **Parametreler** bölümünde, bir *certificatecommonname* parametresi ekleyin:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Ayrıca, *certificateThumbprint*öğesini kaldırmayı düşünün, artık gerekli olmayabilir.

2. *SfrpApiVersion* değişkeninin değerini "2018-02-01" olarak ayarlayın:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. **Microsoft. COMPUTE/virtualMachineScaleSets** kaynağında, sanal makine uzantısını parmak izi yerine sertifika ayarları 'nda ortak adı kullanacak şekilde güncelleştirin.  **Virtualmachineprofile**->**extensionprofile**->**Extensions**Özellikler ayarları sertifikası, Ekle->->-> 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    ve kaldırın `"thumbprint": "[parameters('certificateThumbprint')]",`.

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

4. **Microsoft. ServiceFabric/kümeler** KAYNAĞıNDA, API sürümünü "2018-02-01" olarak güncelleştirin.  Ayrıca **commonnames** özelliği Ile bir **certificatecommonnames** ayarı ekleyin ve **sertifika** ayarını (parmak izi özelliği ile) aşağıdaki örnekte olduğu gibi kaldırın:
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
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > ' Certificateıssuerparmak Izi ' alanı, belirli bir konu ortak adına sahip olan sertifikaların beklenen verenler belirtilmesine izin verir. Bu alan, SHA1 parmak izlerinin virgülle ayrılmış bir listesini kabul eder. Bu, sertifika doğrulamasının güçlendirildiği bir durumdur. veren belirtilmediğinde veya boş olmadığında sertifika, zinciri oluşturulup doğrulanıyorsa kimlik doğrulaması için kabul edilecektir ve doğrulayıcı tarafından güvenilen bir kökte sona erer. Veren belirtilmişse, doğrudan veren 'in parmak izi bu alanda belirtilen değerlerden biriyle eşleşiyorsa, köke güvenilip güvenilmediği bağımsız olarak sertifika kabul edilir. Lütfen bir PKI 'nın aynı konu için sertifika vermek üzere farklı sertifika yetkilileri kullanabileceğini ve bu nedenle, belirli bir konu için beklenen tüm veren parmak izlerinin belirtilmesi önem taşımaktadır.
   >
   > Verenin belirtilmesi en iyi yöntem olarak kabul edilir; Bunu yoksayarak, güvenilir bir köke kadar olan sertifikalar için çalışmaya devam eder. bu davranışın sınırlamaları vardır ve yakın gelecekte kullanıma sunulacaktır. Ayrıca, PKI 'nın sertifika Ilkesi varsa, Azure 'da dağıtılan ve özel bir PKI tarafından verilen ve konuya göre belirtilen x509 sertifikalarıyla güvenli hale getirilmiş kümelerin Azure Service Fabric hizmeti (kümeden hizmete iletişim için) tarafından doğrulanmamış olabileceğini unutmayın. bulunabilir, kullanılabilir ve erişilebilir değildir. 

## <a name="deploy-the-updated-template"></a>Güncelleştirilmiş şablonu dağıtma
Değişiklikleri yaptıktan sonra güncelleştirilmiş şablonu yeniden dağıtın.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Sonraki adımlar
* [Küme güvenliği](service-fabric-cluster-security.md)hakkında bilgi edinin.
* [Küme sertifikası geçişi](service-fabric-cluster-rollover-cert-cn.md) hakkında bilgi edinin
* [Küme sertifikalarını güncelleştirme ve yönetme](service-fabric-cluster-security-update-certs-azure.md)
* [Kümeyi sertifika parmak iziyle ortak ada değiştirerek](service-fabric-cluster-change-cert-thumbprint-to-cn.md) Sertifika yönetimini kolaylaştırın

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
