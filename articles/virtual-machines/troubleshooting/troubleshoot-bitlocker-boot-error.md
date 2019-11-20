---
title: Azure VM 'de BitLocker önyükleme hatalarını giderme | Microsoft Docs
description: Azure VM 'de BitLocker önyükleme hatalarıyla ilgili sorunları nasıl giderebileceğinizi öğrenin
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.openlocfilehash: 80fd91106530c0150a85d508b24041b2263da925
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749675"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Azure VM 'de BitLocker önyükleme hataları

 Bu makalede, Microsoft Azure ' de bir Windows sanal makinesi (VM) başlattığınızda karşılaşabileceğiniz BitLocker hataları açıklanır.

 

## <a name="symptom"></a>Belirti

 Bir Windows VM 'si başlamıyor. [Önyükleme tanılaması](../windows/boot-diagnostics.md) penceresinde ekran görüntülerini denetlediğinizde, aşağıdaki hata iletilerinden birini görürsünüz:

- BitLocker anahtarına sahip USB sürücüsünü takın

- Kilitlendi! Yeniden almak için kurtarma anahtarını girin (klavye düzeni: US) yanlış oturum açma bilgileri çok fazla girilmiştir, bu nedenle BILGISAYARıNıZ gizliliğinizi korumak için kilitlendi. Kurtarma anahtarını almak için, başka bir BILGISAYARDAN veya mobil cihazdan https://windows.microsoft.com/recoverykeyfaq gidin. İhtiyacınız olması durumunda anahtar KIMLIĞI XXXXXXX ' dir. İsterseniz bilgisayarınızı sıfırlayabilirsiniz.

- Bu sürücünün kilidini açmak için parolayı girin [] yazdığınız parolayı görmek için Ekle tuşuna basın.
- Kurtarma anahtarınızı bir USB cihazdan yükleyin.

## <a name="cause"></a>Nedeni

Bu sorun, sanal makine şifreli diskin şifresini çözmek için BitLocker kurtarma anahtarı (BEK) dosyasını bulamıyorsa oluşabilir.

## <a name="solution"></a>Çözüm

Bu sorunu çözmek için VM 'yi durdurup serbest bırakın ve yeniden başlatın. Bu işlem, VM 'yi Azure Key Vault BEK dosyasını almaya zorlar ve sonra şifrelenmiş diske koyar. 

Bu yöntem sorunu çözmezse, BEK dosyasını el ile geri yüklemek için şu adımları izleyin:

1. Etkilenen VM 'nin sistem diskinin bir anlık görüntüsünü bir yedekleme olarak alın. Daha fazla bilgi için bkz. [disk anlık görüntüsü](../windows/snapshot-copy-managed-disk.md).
2. [Sistem diskini bir kurtarma VM 'Sine bağlayın](troubleshoot-recovery-disks-portal-windows.md). Adım 7 ' de [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) komutunu çalıştırmak için, kurtarma VM 'sinde **BitLocker Sürücü Şifrelemesi** özelliğinin etkinleştirilmesi gerekir.

    Yönetilen bir disk iliştirmeye çalıştığınızda, "şifreleme ayarlarını içerir ve bu nedenle veri diski olarak kullanılamaz" hata iletisi alabilirsiniz. Bu durumda, diski eklemeyi yeniden denemek için aşağıdaki betiği çalıştırın:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Bir blob görüntüsünden geri yüklenen bir VM 'ye yönetilen disk iliştiremezsiniz.

3. Disk eklendikten sonra, bazı Azure PowerShell betikleri çalıştırabilmeniz için kurtarma VM 'sine bir Uzak Masaüstü bağlantısı oluşturun. Kurtarma VM 'sinde [Azure PowerShell en son sürümünün](https://docs.microsoft.com/powershell/azure/overview) yüklü olduğundan emin olun.

4. Yükseltilmiş bir Azure PowerShell oturumu açın (yönetici olarak çalıştır). Azure aboneliğinde oturum açmak için aşağıdaki komutları çalıştırın:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. BEK dosyasının adını denetlemek için aşağıdaki betiği çalıştırın:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Çıktının örneği aşağıda verilmiştir. Eklenen disk için BEK dosya adını bulun. Bu durumda, eklenen diskin sürücü harfinin F olduğunu ve BEK dosyasının EF7B2F5A-50C6-4637-9F13-7F599C12F85C olduğunu varsaytık. BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    İki yinelenen birim görürseniz, daha yeni zaman damgasına sahip birim, kurtarma VM tarafından kullanılan geçerli BEK dosyasıdır.

    **Içerik türü** DEĞERI **SarmaBEK**Ise, [anahtar şifreleme anahtarı (kek) senaryolarına](#key-encryption-key-scenario)gidin.

    Artık sürücü için BEK dosyasının adına sahip olduğunuza göre, gizli anahtar adı oluşturmanız gerekir. Sürücünün kilidini açmak için BEK dosyası.

6.  BEK dosyasını kurtarma diskine indirin. Aşağıdaki örnek, BEK dosyasını C:\BEK klasörüne kaydeder. Betikleri çalıştırmadan önce `C:\BEK\` yolunun var olduğundan emin olun.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  BEK dosyasını kullanarak ekli diskin kilidini açmak için aşağıdaki komutu çalıştırın.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Bu örnekte, bağlı işletim sistemi diski F sürücüsüdür. doğru sürücü harfini kullandığınızdan emin olun. 

8. BEK anahtarı kullanılarak diskin kilidi başarıyla açıldıktan sonra, diski kurtarma VM 'sinden ayırın ve ardından bu yeni işletim sistemi diskini kullanarak VM 'yi yeniden oluşturun.

    > [!NOTE]
    > Disk şifrelemesi kullanan VM 'Lerde işletim sistemi diskini değiştirme desteklenmez.

9. Yeni VM hala normal olarak önyüklenemediğinde, sürücünün kilidini açtıktan sonra aşağıdaki adımlardan birini deneyin:

    - Aşağıdakileri çalıştırarak BitLocker 'ı geçici olarak devre dışı bırakmak için korumayı askıya alın:

                    manage-bde -protectors -disable F: -rc 0
           
    - Sürücünün şifresini tamamen çözün. Bunu yapmak için aşağıdaki komutu çalıştırın:

                    manage-bde -off F:

### <a name="key-encryption-key-scenario"></a>Anahtar şifreleme anahtarı senaryosu

Anahtar şifreleme anahtarı senaryosu için aşağıdaki adımları izleyin:

1. Oturum açmış kullanıcı hesabının, Kullanıcı Key Vault erişim ilkelerinde "sarmalanmamış" iznini gerektirdiğinden emin olun **| Anahtar izinleri | Şifreleme Işlemleri | Anahtar sarmalaması geri alınıyor**.
2. Aşağıdaki betiği bir dosyasına kaydedin. PS1 dosyası:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Parametreleri ayarlayın. Betik, BEK anahtarını oluşturmak için KEK parolasını işleyecek ve ardından kurtarma sanal makinesinde yerel bir klasöre kaydetmeyecektir. Betiği çalıştırdığınızda hata alırsanız, [komut dosyası sorun giderme](#script-troubleshooting) bölümüne bakın.

4. Komut dosyası başladığında aşağıdaki çıktıyı görürsünüz:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Komut dosyası tamamlandığında aşağıdaki çıktıyı görürsünüz:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. BEK dosyasını kullanarak ekli diskin kilidini açmak için aşağıdaki komutu çalıştırın:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Bu örnekte, bağlı işletim sistemi diski F sürücüsüdür. doğru sürücü harfini kullandığınızdan emin olun. 

6. BEK anahtarı kullanılarak diskin kilidi başarıyla açıldıktan sonra, diski kurtarma VM 'sinden ayırın ve ardından bu yeni işletim sistemi diskini kullanarak VM 'yi yeniden oluşturun. 

    > [!NOTE]
    > Disk şifrelemesi kullanan VM 'Lerde işletim sistemi diskini değiştirme desteklenmez.

7. Yeni VM hala normal olarak önyüklenemediğinde, sürücünün kilidini açtıktan sonra aşağıdaki adımlardan birini deneyin:

    - Aşağıdaki komutu çalıştırarak BitLocker 'ı geçici olarak devre dışı bırakmak için korumayı askıya alın:

             manage-bde -protectors -disable F: -rc 0
           
    - Sürücünün şifresini tamamen çözün. Bunu yapmak için aşağıdaki komutu çalıştırın:

                    manage-bde -off F:
## <a name="script-troubleshooting"></a>Betik sorunlarını giderme

**Hata: dosya veya derleme yüklenemedi**

ADAL derlemelerinin yollarının yanlış olduğu için bu hata oluşur. AZ Module yalnızca geçerli kullanıcı için yüklüyse, ADAL derlemeleri `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>`' de yer alır.

Ayrıca, doğru yolu bulmak için `Az.Accounts` klasörü araması yapabilirsiniz.

**Hata: Get-AzKeyVaultSecret veya Get-AzKeyVaultSecret bir cmdlet 'in adı olarak tanınmıyor**

Eski AZ PowerShell modülünü kullanıyorsanız, iki komutu `Get-AzureKeyVaultSecret` ve `Get-AzureKeyVaultSecret`olarak değiştirmeniz gerekir.

**Parametre örnekleri**

| Parametreler  | Değer örneği  |Yorumlar   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Anahtarı depolayan anahtar kasasının adı |
|$kekName   |MyKey   | VM 'yi şifrelemek için kullanılan anahtarın adı|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | VM anahtarının gizli anahtar adı|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |BEK dosyası yazmak için yol.|
|$adTenant  |contoso.onmicrosoft.com   | Anahtar kasasını barındıran Azure Active Directory FQDN veya GUID 'SI |
