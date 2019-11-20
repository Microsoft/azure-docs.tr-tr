---
title: Azure Service Fabric küme sertifikasını alma | Microsoft Docs
description: Sertifika ortak adı tarafından tanımlanan Service Fabric kümesi sertifikasını nasıl alabileceğinizi öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: atsenthi
ms.openlocfilehash: d6ead6aaa5d4c0e864126bf63d4cc0e9339464f2
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773363"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Service Fabric kümesi sertifikasını el ile alma
Service Fabric küme sertifikasının süresi dolmak üzere kapatıldığında, sertifikayı güncelleştirmeniz gerekir.  Küme, ortak ada (parmak izi yerine) [göre sertifikalar kullanmak üzere ayarlandıysa](service-fabric-cluster-change-cert-thumbprint-to-cn.md) Sertifika geçişi basittir.  Yeni bir sona erme tarihine sahip bir sertifika yetkilisinden yeni bir sertifika alın.  Otomatik olarak imzalanan sertifikalar, Azure portal kümesi oluşturma iş akışı sırasında oluşturulan sertifikaları dahil etmek için üretim Service Fabric kümeleri için desteklenmez. Yeni sertifika, eski sertifikayla aynı ortak ada sahip olmalıdır. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric küme, gelecek süre sonu tarihi ile birlikte otomatik olarak tanımlanan sertifikayı kullanır; konakta birden fazla doğrulama sertifikası yüklü olduğunda. En iyi uygulama, Azure kaynaklarını sağlamak için Kaynak Yöneticisi şablonu kullanmaktır. Üretim dışı ortamda, bir anahtar kasasına yeni bir sertifika yüklemek ve ardından sertifikayı sanal makine ölçek kümesine yüklemek için aşağıdaki betik kullanılabilir: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

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

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Sanal makine ölçek kümesi gizli dizileri, her gizli sürümü sürümlü benzersiz bir kaynak olduğundan, bu, iki ayrı gizli dizi için aynı kaynak kimliğini desteklemez. 

## <a name="next-steps"></a>Sonraki Adımlar

* [Küme güvenliği](service-fabric-cluster-security.md)hakkında bilgi edinin.
* [Küme sertifikalarını güncelleştirme ve yönetme](service-fabric-cluster-security-update-certs-azure.md)
