---
title: Azure disk şifrelemesi örnek betikleri
description: Bu makalede, Windows VM 'Leri için Microsoft Azure disk şifrelemesi eki bulunur.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749471"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure disk şifrelemesi örnek betikleri 

Bu makalede önceden şifrelenen VHD 'ler ve diğer görevler için örnek betikler sağlanmaktadır.

 

## <a name="list-vms-and-secrets"></a>VM 'Leri ve gizli dizileri listeleyin

Aboneliğinizdeki tüm şifreli VM 'Leri listeleyin:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Bir anahtar kasasındaki VM 'Leri şifrelemek için kullanılan tüm disk şifreleme gizli dizilerini listeleyin:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Azure disk şifrelemesi önkoşul betikleri
Azure disk şifrelemesi önkoşullarını zaten biliyorsanız, [Azure disk şifrelemesi önkoşulları PowerShell betiğini](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )kullanabilirsiniz. Bu PowerShell betiğini kullanmayla ilgili bir örnek için bkz. [VM 'Yi şifreleme hızlı başlangıç](disk-encryption-powershell-quickstart.md). Mevcut bir kaynak grubundaki mevcut VM 'Ler için tüm diskleri şifrelemek üzere, 211. satırdan başlayarak betiğin bir bölümünden açıklamaları kaldırabilirsiniz. 

Aşağıdaki tabloda, PowerShell komut dosyasında hangi parametrelerin kullanılabileceği gösterilmektedir: 

|Parametre|Açıklama|Girilmesi?|
|------|------|------|
|$resourceGroupName| Anahtar kasasının ait olduğu kaynak grubunun adı.  Mevcut değilse, bu ada sahip yeni bir kaynak grubu oluşturulur.| True|
|$keyVaultName|Şifreleme anahtarlarının yerleştirileceği anahtar kasasının adı. Bu ada sahip yeni bir kasa, mevcut değilse oluşturulur.| True|
|$location|Anahtar kasasının konumu. Şifrelenecek anahtar kasası ve VM 'Lerin aynı konumda olduğundan emin olun. `Get-AzLocation` komutu ile bir konum listesi alın.|True|
|$subscriptionId|Kullanılacak Azure aboneliğinin tanımlayıcısı.  Abonelik Kimliğinizi `Get-AzSubscription` komutu ile alabilirsiniz.|True|
|$aadAppName|Anahtar kasasına gizli diziler yazmak için kullanılacak Azure AD uygulamasının adı. Bu ada sahip bir uygulama yoksa yeni bir uygulama oluşturulur. Bu uygulama zaten varsa, aadClientSecret parametresini betiğe geçirin.|False|
|$aadClientSecret|Daha önce oluşturulan Azure AD uygulamasının istemci gizli anahtarı.|False|
|$keyEncryptionKeyName|Anahtar Kasası 'nda isteğe bağlı anahtar şifreleme anahtarının adı. Bu ada sahip yeni bir anahtar, yoksa oluşturulur.|False|

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
**Yerel bilgisayar ilkesi** > **bilgisayar yapılandırma** > **Yönetim Şablonları** > Windows altında bulacağınız BitLocker Grup İlkesi ayarını **BitLocker Sürücü Şifrelemesi**yapılandırın  **Bileşenler**. Bu ayarı, aşağıdaki şekilde gösterildiği gibi, > **Başlangıçta ek kimlik doğrulaması gerektirmek** > **işletim sistemi sürücüleri** , **uyumlu bir TPM olmadan BitLocker 'a izin vermek**için değiştirin:

![Azure’da Microsoft Kötü Amaçlı Yazılımdan Koruma](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>BitLocker özellik bileşenlerini yükler
Windows Server 2012 ve üzeri için aşağıdaki komutu kullanın:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2 için aşağıdaki komutu kullanın:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>`bdehdcfg` kullanarak işletim sistemi birimini BitLocker için hazırlama
İşletim sistemi bölümünü sıkıştırmak ve makineyi BitLocker 'a hazırlamak için gerekirse [BdeHdCfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) ' yi yürütün:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>BitLocker 'ı kullanarak işletim sistemi birimini koruma
Dış anahtar koruyucusu kullanarak önyükleme biriminde şifrelemeyi etkinleştirmek için [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) komutunu kullanın. Dış anahtarı (. bek dosyası) dış sürücüye veya birime da yerleştirin. Şifreleme, bir sonraki yeniden başlatmanın ardından sistem/önyükleme biriminde etkinleştirilir.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> BitLocker 'ı kullanarak dış anahtarı almak için VM 'yi ayrı bir veri/kaynak VHD ile hazırlayın.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Şifrelenmiş VHD 'YI bir Azure depolama hesabına yükleme
DM-Crypt şifrelemesi etkinleştirildikten sonra, yerel şifreli VHD 'nin depolama hesabınıza yüklenmesi gerekir.
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


[Kek kullanmadan işletim sistemi diskini eklemek](#without-using-a-kek)için bir sonraki adımda `$secretUrl` kullanın.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Disk şifrelemesi gizli anahtarı bir KEK ile şifrelendi
Gizli anahtarı anahtar kasasına yüklemeden önce, anahtar şifreleme anahtarını kullanarak isteğe bağlı olarak şifreleyebilirsiniz. Anahtar şifreleme anahtarını kullanarak parolayı ilk kez şifrelemek için wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) 'sini kullanın. Bu Wrap işleminin çıktısı, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet 'ini kullanarak bir gizli dizi olarak karşıya yükleyebileceğiniz Base64 URL kodlamalı bir dizedir.

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

[Kek kullanarak işletim sistemi diskini eklemek](#using-a-kek)için bir sonraki adımda `$KeyEncryptionKey` ve `$secretUrl` kullanın.

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Bir işletim sistemi diski iliştirmeye çalıştığınızda gizli bir URL belirtin

###  <a name="without-using-a-kek"></a>KEK kullanmadan
İşletim sistemi diskini iliştirirken `$secretUrl`geçirmeniz gerekir. URL, "bir KEK ile şifrelenmemiş disk şifrelemesi" bölümünde oluşturulmuştur.
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
İşletim sistemi diskini iliştirmeniz durumunda `$KeyEncryptionKey` geçirin ve `$secretUrl`. URL, "bir KEK ile şifrelenen disk şifreleme gizli dizisi" bölümünde oluşturulmuştur.
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
