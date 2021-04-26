---
title: Windows VM 'Leri için Azure disk şifrelemesi örnek betikleri
description: Bu makalede, Windows VM 'Leri için Microsoft Azure disk şifrelemesi eki bulunur.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 413553165fcf74fa4590cb4661212b885a277579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550646"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Şifrelemesi örnek betikleri 

Bu makalede önceden şifrelenen VHD 'ler ve diğer görevler için örnek betikler sağlanmaktadır.

> [!NOTE]
> Tüm betikler, belirtilenler dışında, en son AAD olmayan ADE sürümüne başvurur.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Azure disk şifrelemesi için örnek PowerShell betikleri 


- **Aboneliğinizdeki tüm şifreli VM 'Leri listeleyin**

  [Bu PowerShell betiği](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1)kullanılarak, bir abonelikte bulunan tüm kaynak GRUPLARıNDAKI tüm Ade şifreli VM 'leri ve uzantı sürümünü bulabilirsiniz.

  Alternatif olarak, bu cmdlet 'ler tüm ADE şifreli VM 'Leri gösterir (ancak uzantı sürümü değil):

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **Aboneliğinizdeki tüm şifreli VMSS örneklerini listeleyin**
    
    [Bu PowerShell betiği](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1)kullanılarak, bir abonelikte bulunan tüm kaynak GRUPLARıNDAKI tüm Ade şifreli VMSS örneklerini ve uzantı sürümünü bulabilirsiniz.
 
- **Bir anahtar kasasındaki VM 'Leri şifrelemek için kullanılan tüm disk şifreleme gizli dizilerini listeleyin**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Azure disk şifrelemesi önkoşulları PowerShell betiğini kullanma

Azure disk şifrelemesi önkoşullarını zaten biliyorsanız, [Azure disk şifrelemesi önkoşulları PowerShell betiğini](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )kullanabilirsiniz. Bu PowerShell betiğini kullanmayla ilgili bir örnek için bkz. [VM 'Yi şifreleme hızlı başlangıç](disk-encryption-powershell-quickstart.md). Mevcut bir kaynak grubundaki mevcut VM 'Ler için tüm diskleri şifrelemek üzere, 211. satırdan başlayarak betiğin bir bölümünden açıklamaları kaldırabilirsiniz. 

Aşağıdaki tabloda, PowerShell komut dosyasında hangi parametrelerin kullanılabileceği gösterilmektedir: 

|Parametre|Açıklama|Girilmesi?|
|------|------|------|
|$resourceGroupName| Anahtar kasasının ait olduğu kaynak grubunun adı.  Mevcut değilse, bu ada sahip yeni bir kaynak grubu oluşturulur.| Doğru|
|$keyVaultName|Şifreleme anahtarlarının yerleştirileceği anahtar kasasının adı. Bu ada sahip yeni bir kasa, mevcut değilse oluşturulur.| Doğru|
|$location|Anahtar kasasının konumu. Şifrelenecek anahtar kasası ve VM 'Lerin aynı konumda olduğundan emin olun. `Get-AzLocation` komutu ile bir konum listesi alın.|Doğru|
|$subscriptionId|Kullanılacak Azure aboneliğinin tanımlayıcısı.  Abonelik Kimliğinizi `Get-AzSubscription` komutu ile alabilirsiniz.|Doğru|
|$aadAppName|Anahtar kasasına gizli diziler yazmak için kullanılacak Azure AD uygulamasının adı. Bu ada sahip bir uygulama yoksa yeni bir uygulama oluşturulur. Bu uygulama zaten varsa, aadClientSecret parametresini betiğe geçirin.|Yanlış|
|$aadClientSecret|Daha önce oluşturulan Azure AD uygulamasının istemci gizli anahtarı.|Yanlış|
|$keyEncryptionKeyName|Anahtar Kasası 'nda isteğe bağlı anahtar şifreleme anahtarının adı. Bu ada sahip yeni bir anahtar, yoksa oluşturulur.|Yanlış|

## <a name="resource-manager-templates"></a>Resource Manager şablonları

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Azure AD uygulaması olmadan VM 'Leri şifreleme veya şifrelerini çözme

- [Mevcut veya çalışan bir Windows VM 'de disk şifrelemeyi etkinleştirme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Çalışan bir Windows VM 'de şifrelemeyi devre dışı bırakma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>VM 'Leri bir Azure AD uygulamasıyla şifreleme veya şifrelerini çözme (önceki sürüm) 
 
- [Mevcut veya çalışan bir Windows VM 'de disk şifrelemeyi etkinleştirme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Çalışan bir Windows VM 'de şifrelemeyi devre dışı bırakma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Önceden şifrelenen bir VHD/depolama blobundan yeni bir şifrelenmiş yönetilen disk oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Önceden şifrelenen bir VHD ve buna karşılık gelen şifreleme ayarları sağlanmış olan yeni bir şifrelenmiş yönetilen disk oluşturur

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Önceden şifrelenmiş bir Windows VHD hazırlama
Aşağıdaki bölümler, Azure IaaS 'de şifrelenmiş bir VHD olarak dağıtıma önceden şifrelenmiş bir Windows VHD 'si hazırlamak için gereklidir. Azure Site Recovery veya Azure 'da yeni bir Windows VM (VHD) hazırlamak ve önyüklemek için bu bilgileri kullanın. Bir VHD 'yi hazırlama ve karşıya yükleme hakkında daha fazla bilgi için bkz. [Genelleştirilmiş BIR VHD 'Yi karşıya yükleme ve Azure 'da yeni VM 'ler oluşturmak için kullanma](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Grup ilkesini, işletim sistemi koruması için TPM olmayan bir şekilde güncelleştirme
 **Yerel bilgisayar ilkesi**  >  **bilgisayar yapılandırması**  >  **Yönetim Şablonları**  >  **Windows bileşenleri** altında bulacağınız BitLocker Grup İlkesi ayarını BitLocker Sürücü Şifrelemesi yapılandırın. Bu ayarı **işletim sistemi sürücüleri** olarak değiştirin  >  , başlangıçta aşağıdaki şekilde gösterildiği gibi, uyumlu bir TPM olmadan BitLocker 'a **ek kimlik doğrulaması gerektir**  >  :

![Azure’da Microsoft Kötü Amaçlı Yazılımdan Koruma](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>BitLocker özellik bileşenlerini yükler
Windows Server 2012 ve üzeri için aşağıdaki komutu kullanın:

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

Windows Server 2008 R2 için aşağıdaki komutu kullanın:

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Kullanarak BitLocker için işletim sistemi birimini hazırlama `bdehdcfg`
İşletim sistemi bölümünü sıkıştırmak ve makineyi BitLocker 'a hazırlamak için gerekirse [BdeHdCfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) ' yi yürütün:

```console
bdehdcfg -target c: shrink -quiet 
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>BitLocker 'ı kullanarak işletim sistemi birimini koruma
[`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11))Bir dış anahtar koruyucusu kullanarak önyükleme biriminde şifrelemeyi etkinleştirmek için komutunu kullanın. Dış anahtarı (. bek dosyası) dış sürücüye veya birime da yerleştirin. Şifreleme, bir sonraki yeniden başlatmanın ardından sistem/önyükleme biriminde etkinleştirilir.

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> BitLocker 'ı kullanarak dış anahtarı almak için VM 'yi ayrı bir veri/kaynak VHD ile hazırlayın.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Şifrelenmiş VHD 'YI bir Azure depolama hesabına yükleme
DM-Crypt şifreleme etkinleştirildikten sonra, yerel şifreli VHD 'nin depolama hesabınıza yüklenmesi gerekir.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Önceden şifrelenen VM 'nin parolasını anahtar kasanıza yükleme
Daha önce edindiğiniz disk şifrelemesi gizli anahtarı, anahtar kasanıza gizli bir parola olarak yüklenmelidir.  Bu, gizli dizi kümesini ve wrapkey iznini, gizli dizileri yükleyecek hesaba vermeyi gerektirir.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Disk şifreleme parolası bir KEK ile şifrelenmedi
Anahtar kasasında gizli dizi ayarlamak için [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)kullanın. Parola Base64 dizesi olarak kodlanır ve sonra anahtar kasasına yüklenir. Ayrıca, anahtar kasasında gizli dizi oluşturduğunuzda aşağıdaki etiketlerin ayarlandığından emin olun.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


`$secretUrl` [Kek kullanmadan işletim sistemi diski eklemek](#without-using-a-kek)için bir sonraki adımda kullanın.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Disk şifrelemesi gizli anahtarı bir KEK ile şifrelendi
Gizli anahtarı anahtar kasasına yüklemeden önce, anahtar şifreleme anahtarını kullanarak isteğe bağlı olarak şifreleyebilirsiniz. Anahtar şifreleme anahtarını kullanarak parolayı ilk kez şifrelemek için wrap [API](/rest/api/keyvault/wrapkey) 'sini kullanın. Bu Wrap işleminin çıktısı Base64 URL kodlamalı bir dizedir ve cmdlet 'ini kullanarak bir gizli dizi olarak karşıya yükleyebilirsiniz [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

`$KeyEncryptionKey` `$secretUrl` [Kek kullanarak işletim sistemi diskini eklemek](#using-a-kek)için bir sonraki adımda ve kullanın.

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Bir işletim sistemi diski iliştirmeye çalıştığınızda gizli bir URL belirtin

###  <a name="without-using-a-kek"></a>KEK kullanmadan
İşletim sistemi diskini iliştirirken geçirmeniz gerekir `$secretUrl` . URL, "bir KEK ile şifrelenmemiş disk şifrelemesi" bölümünde oluşturulmuştur.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK kullanma
İşletim sistemi diskini iliştirmeniz durumunda, `$KeyEncryptionKey` ve geçirin `$secretUrl` . URL, "bir KEK ile şifrelenen disk şifreleme gizli dizisi" bölümünde oluşturulmuştur.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
