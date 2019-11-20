---
title: Şifrelenmiş VM için Key Vault anahtarı & gizliliğini geri yükleme
description: PowerShell kullanarak Azure Backup Key Vault anahtarı ve gizli anahtarı geri yüklemeyi öğrenin
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 55e20d861eedde19946d2c99dfc1cd8ff33f6b0b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172760"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Azure Backup kullanarak şifrelenmiş VM 'Ler için Key Vault anahtarı ve gizli anahtarı geri yükleme

Bu makalede, anahtar ve gizli anahtarı anahtar kasasında yoksa, şifrelenmiş Azure VM 'lerini geri yükleme işlemini gerçekleştirmek için Azure VM yedeklemesini kullanma hakkında bilgi sağlar. Bu adımlar, geri yüklenen VM için anahtarın ayrı bir kopyasını (anahtar şifreleme anahtarı) ve gizli dizi (BitLocker şifreleme anahtarı) korumak istiyorsanız da kullanılabilir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

* **Yedekleme şifreli VM 'ler** -şifrelenmiş Azure vm 'ler Azure Backup kullanılarak yedeklendi. Şifrelenmiş Azure sanal makinelerini yedekleme hakkında ayrıntılı bilgi için [PowerShell 'i kullanarak Azure VM 'lerini yedekleme ve geri yükleme makalesini yönetme](backup-azure-vms-automation.md) makalesine bakın.
* **Azure Key Vault yapılandırma** – anahtarların ve parolaların geri yüklenmesi gereken anahtar kasasının zaten mevcut olduğundan emin olun. Anahtar Kasası yönetimi hakkında ayrıntılı bilgi [edinmek için Azure Key Vault kullanmaya başlama](../key-vault/key-vault-get-started.md) makalesine başvurun.
* **Diski geri yükleme** - [PowerShell ADıMLARıNı](backup-azure-vms-automation.md#restore-an-azure-vm)kullanarak şifrelenmiş VM için diskleri geri yüklemek üzere geri yükleme işini tetiklemiş olduğunuzdan emin olun. Bunun nedeni, bu işin depolama hesabınızda şifreli VM 'nin geri yüklenecek anahtarlar ve gizli dizileri içeren bir JSON dosyası oluşturması.

## <a name="get-key-and-secret-from-azure-backup"></a>Azure Backup anahtar ve gizli dizi alın

> [!NOTE]
> Şifrelenmiş VM için disk geri yüklendikten sonra aşağıdakileri doğrulayın:
>
> * $details, [diskleri geri yükleme bölümündeki PowerShell adımlarında](backup-azure-vms-automation.md#restore-an-azure-vm) bahsedildiği gibi, disk geri yükleme ayrıntıları ile doldurulur.
> * VM, geri yüklenen disklerden yalnızca **anahtar ve gizli Anahtar Kasası 'na geri yüklendikten sonra**oluşturulmalıdır.

İş ayrıntıları için geri yüklenen disk özelliklerini sorgulayın.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Azure Storage bağlamını ayarlayın ve şifreli VM için anahtar ve gizli dizi ayrıntılarını içeren JSON yapılandırma dosyasını geri yükleyin.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Anahtarı geri yükle

JSON dosyası yukarıda belirtilen hedef yolda oluşturulduktan sonra, JSON 'dan anahtar BLOB dosyası oluşturun ve anahtarı (KEK) anahtar kasasına geri yerleştirmek için anahtar cmdlet 'ini geri yüklemek üzere onu akışa alın.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Gizli anahtarı geri yükleme

Gizli dizi adı ve değeri almak için yukarıda oluşturulan JSON dosyasını kullanın ve gizli anahtarı (BEK) anahtar kasasında geri yerleştirmek için gizli cmdlet 'i ayarlayın. **VM 'NIZ bek ve kek kullanılarak şifrelendiyse**bu cmdlet 'leri kullanın.

**Windows VM 'niz BEK ve KEK kullanılarak şifrelendiyse bu cmdlet 'leri kullanın.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Linux VM 'niz BEK ve KEK kullanılarak şifrelendiyse bu cmdlet 'leri kullanın.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Gizli dizi adı ve değeri almak için yukarıda oluşturulan JSON dosyasını kullanın ve gizli anahtarı (BEK) anahtar kasasında geri yerleştirmek için gizli cmdlet 'i ayarlayın. VM 'niz yalnızca **bek kullanılarak şifrelendiyse** bu cmdlet 'leri kullanın.

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * $Secretname değeri, $encryptionObject. OsDiskKeyAndSecretDetails. SecretUrl çıktısına başvurarak elde edilebilir ve gizli dizileri/örn. çıktı gizli URL 'SI https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 ve gizli dizi adı B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * DiskEncryptionKeyFileName etiketinin değeri gizli adı ile aynı.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Geri yüklenen diskten sanal makine oluştur

Azure VM yedeklemesi kullanarak şifrelenmiş VM 'yi yedeklediyseniz, yukarıda bahsedilen PowerShell cmdlet 'leri anahtar kasasına geri ve gizli anahtarı geri yüklemenize yardımcı olur. Geri yükledikten sonra, geri yüklenen disk, anahtar ve gizli dizi ile şifrelenmiş VM 'Ler oluşturmak için [PowerShell kullanarak Azure VM 'lerinin yedeklenmesini ve geri yüklenmesini yönetme](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) makalesine bakın.

## <a name="legacy-approach"></a>Eski yaklaşım

Yukarıda bahsedilen yaklaşım tüm kurtarma noktaları için çalışır. Ancak, kurtarma noktasından anahtar ve gizli bilgilerin alınması için eski yaklaşım, BEK ve KEK kullanılarak şifrelenmiş VM 'Ler için 11 Temmuz 2017 ' den eski kurtarma noktaları için geçerlidir. [PowerShell adımları](backup-azure-vms-automation.md#restore-an-azure-vm)KULLANıLARAK şifrelenmiş VM için disk geri yükleme işlemi tamamlandıktan sonra $RP geçerli bir değerle doldurulduğundan emin olun.

### <a name="restore-key"></a>Anahtarı geri yükle

Kurtarma noktasından anahtar (KEK) bilgileri almak için aşağıdaki cmdlet 'leri kullanın ve anahtarı anahtar kasasına geri döndürmek için anahtar cmdlet 'ini geri yüklemek üzere akışa alın.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Gizli anahtarı geri yükleme

Kurtarma noktasından gizli (BEK) bilgileri almak için aşağıdaki cmdlet 'leri kullanın ve anahtar kasasında geri dönmek için gizli cmdlet 'i ayarlayın.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * $Secretname değeri, $rp 1 ' in çıktısına başvurarak elde edilebilir. KeyAndSecretDetails. SecretUrl ve parolalar/örn. çıktı gizli URL 'SI https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 ve gizli dizi adı B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * DiskEncryptionKeyFileName etiketinin değeri gizli adı ile aynı.
> * Node Encryptionkeyencryptionkeyurl değeri, anahtarları geri yükledikten ve [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) cmdlet 'i kullanılarak anahtar kasasından elde edilebilir
>
>

## <a name="next-steps"></a>Sonraki adımlar

Anahtar ve gizli anahtarı anahtar kasasına geri yükledikten sonra, geri yüklenen disk, anahtar ve gizli bilgisayarlardan şifrelenmiş VM 'Ler oluşturmak için [PowerShell kullanarak Azure VM 'lerinin yedeklenmesini ve geri yüklenmesini yönetme](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) makalesine bakın.
