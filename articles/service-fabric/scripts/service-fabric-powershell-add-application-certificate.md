---
title: PowerShell 'de bir kümeye uygulama sertifikası ekleme
description: Azure PowerShell Betiği Örneği - Service Fabric kümesine uygulama sertifikası ekleme.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0f5a7d49200c7dec08f71f9cb0256630728e4711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090151"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Service Fabric kümesine uygulama sertifikası ekleme

Bu örnek betik, Key Vault bir sertifikanın nasıl oluşturulacağını ve sonra kümenizin üzerinde çalıştığı sanal makine ölçek kümelerinden birine nasıl dağıtılacağını açıklar. Bu senaryo doğrudan Service Fabric kullanmaz, bunun yerine Key Vault ve sanal makine ölçek kümelerine bağımlıdır.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/) bulunan yönergeleri kullanarak Azure PowerShell’i yükleyin ve ardından Azure ile bir bağlantı oluşturmak için `Connect-AzAccount` öğesini çalıştırın. 

## <a name="create-a-certificate-in-key-vault"></a>Key Vault bir sertifika oluşturun

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Veya var olan bir sertifikayı Key Vault içine yükleyin

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$bytes = [System.IO.File]::ReadAllBytes($PathToPFX)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Sanal Makine Ölçek Kümeleri profilini sertifikayla Güncelleştir

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

# If you have added your certificate to the keyvault certificates, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore

# Otherwise, if you have added your certificate to the keyvault secrets, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultSecret -VaultName $VaultName -Name $CertName).Id -CertificateStore $CertStore

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($CertConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>Sanal makine ölçek kümesini Güncelleştir
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Sertifikanın kümenizdeki birden çok düğüm türüne yerleştirilmesini isterseniz, bu betiğin ikinci ve üçüncü bölümlerinin sertifikaya sahip olması gereken her düğüm türü için yinelenmesi gerekir.

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Sertifikayı temsil eden bellek içi bir ilke oluşturur |
| [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| İlkeyi Key Vault sertifikalara dağıtır |
| [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/Set-AzKeyVaultSecret)| İlkeyi Key Vault gizli dizi için dağıtır |
| [New-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Bir VM 'de sertifikayı temsil eden bellek içi bir yapılandırma oluşturur |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Sertifikayı, sanal makine ölçek kümesinin bellek içi tanımına ekler |
| [Güncelleştirme-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Sanal makine ölçek kümesinin yeni tanımını dağıtır |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).

Azure Service Fabric için ek Azure PowerShell örneklerini [Azure PowerShell örnekleri](../service-fabric-powershell-samples.md) bölümünde bulabilirsiniz.
