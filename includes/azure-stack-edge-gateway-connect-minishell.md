---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081014"
---
İstemci işletim sistemine bağlı olarak, cihaza uzaktan bağlanma yordamları farklıdır.

### <a name="remotely-connect-from-a-windows-client"></a>Bir Windows istemcisinden uzaktan bağlanma


#### <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

- Windows istemciniz Windows PowerShell 5,0 veya üstünü çalıştırıyor.
- Windows istemciniz, cihazda yüklü olan düğüm sertifikasına karşılık gelen imzalama zincirine (kök sertifika) sahiptir. Ayrıntılı yönergeler için bkz. [Windows istemcinizdeki sertifikayı yükler](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- `hosts` `C:\Windows\System32\drivers\etc` Windows istemciniz için konumunda bulunan dosyası aşağıdaki biçimde düğüm sertifikasına karşılık gelen bir girdiye sahiptir:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Dosya için örnek bir giriş aşağıda verilmiştir `hosts` :
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Ayrıntılı adımlar

Bir Windows istemcisinden uzaktan bağlanmak için aşağıdaki adımları izleyin.

1. Yönetici olarak bir Windows PowerShell oturumu çalıştırın.
2. Windows Uzaktan Yönetimi hizmetinin istemciniz üzerinde çalıştığından emin olun. Komut istemine şunları yazın:

    `winrm quickconfig`

    Daha fazla bilgi için bkz. [Windows Uzaktan Yönetimi Için yükleme ve yapılandırma](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Cihaz IP adresine bir değişken atayın.

    $ip = "<device_ip>"

    `<device_ip>`CIHAZıNıZıN IP adresiyle değiştirin.

4. Cihazınızın IP adresini istemcinin güvenilen konaklar listesine eklemek için aşağıdaki komutu yazın:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Cihazda bir Windows PowerShell oturumu başlatın:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Güven ilişkisiyle ilgili bir hata görürseniz, cihazınıza yüklenen düğüm sertifikasının imzalama zincirinin cihazınıza erişen istemciye de yüklenip yüklenmediğini kontrol edin.

    Sertifikaları kullanmıyorsanız (sertifikaları kullanmanız önerilir!), oturum seçeneklerini kullanarak bu denetimi atlayabilirsiniz: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > `-UseSSL`Seçeneğini kullandığınızda, *https* üzerinden PowerShell aracılığıyla uzaktan iletişim oluşturursunuz. PowerShell aracılığıyla uzaktan bağlanmak için her zaman *https* kullanmanızı öneririz. 

6. İstendiğinde parolayı girin. Yerel Web Kullanıcı arabiriminde oturum açmak için kullanılan parolayı kullanın. Varsayılan yerel Web UI parolası *Parola1*'dır. Uzak PowerShell kullanarak cihaza başarıyla bağlandığınızda aşağıdaki örnek çıktıyı görürsünüz:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> Geçerli sürümde, yalnızca bir Windows istemcisi aracılığıyla cihazın PowerShell arabirimine bağlanabilirsiniz. Bu `-UseSSL` seçenek, Linux istemcileriyle birlikte çalışmaz.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->