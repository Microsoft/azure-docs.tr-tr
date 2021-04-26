---
title: Windows cihaza C# Aracısı 'nı yükler
description: 32-bit veya 64 bit Windows cihazlarına IoT Aracısı için Defender 'ı nasıl yükleyeceğiniz hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: b5efc9ce675fa068f378cdc2bdd5a077d3437a93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778772"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Windows için IoT C# tabanlı güvenlik Aracısı için bir Defender dağıtma

Bu kılavuzda, Windows 'da IoT C# tabanlı güvenlik aracısının Defender 'ın nasıl yükleneceği açıklanmaktadır.

Bu kılavuzda şunların nasıl yapıldığını öğrenirsiniz:

- Yükleme
- Dağıtımı doğrulama
- Aracıyı kaldırın
- Sorun giderme

## <a name="prerequisites"></a>Önkoşullar

Diğer platformlar ve aracı türleri için bkz. [doğru güvenlik aracısını seçme](how-to-deploy-agent.md).

1. Yüklemek istediğiniz makinede yerel yönetici hakları.

1. Cihaz için [bir Defender-IoT-mikro aracı oluşturun](quickstart-create-security-twin.md) .

## <a name="installation"></a>Yükleme

Güvenlik aracısını yüklemek için aşağıdaki iş akışını kullanın:

1. Cihaza IoT Windows C# Aracısı için Defender 'ı yükler. IoT [GitHub deposu](https://github.com/Azure/Azure-IoT-Security-Agent-CS)için Defender ' dan en son sürümü makinenize indirin.

1. Paketin içeriğini ayıklayın ve/Install klasörüne gidin.

1. Windows PowerShell 'i yönetici olarak açın.
1. Şu komutu çalıştırarak ınstallsecurityagent betiğine çalışan izinleri ekleyin:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    ardından şunu çalıştırın:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Örnek:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Kimlik doğrulama parametreleri hakkında daha fazla bilgi için bkz. [kimlik doğrulamasını yapılandırma](concept-security-agent-authentication-methods.md).

Bu betik aşağıdaki eylemleri yapar:

* Önkoşulları kurar.
* Bir hizmet kullanıcısı ekler (etkileşimli oturum açma devre dışı bırakılır).
* Aracıyı bir **sistem hizmeti** olarak kurar.
* Aracıyı, belirtilen kimlik doğrulama parametreleriyle yapılandırır.

Ek Yardım için PowerShell 'de Get-Help komutunu kullanın.

Get-Help örnek:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Dağıtım durumunu doğrula

Şunu çalıştırarak aracı dağıtım durumunu denetleyin:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Aracıyı kaldırın

Aracıyı kaldırmak için:

1. Aşağıdaki PowerShell betiğini, **-Mode** parametresi **Uninstall** olarak ayarlanmış şekilde çalıştırın.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Sorun giderme

Aracı başlamazsa, daha fazla bilgi almak için günlüğü açın (varsayılan olarak günlüğe kaydetme *kapalıdır* ).

Günlüğe kaydetmeyi açmak için:

1. Yapılandırma dosyasını (General.config) standart bir dosya Düzenleyicisi kullanarak düzenleme için açın.

1. Aşağıdaki değerleri düzenleyin:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Sorun giderme işlemi tamamlandıktan sonra oturumu **kapatmayı** öneririz. Günlüğe kaydetmeyi **bırakmak günlük dosyası** boyutunu ve veri kullanımını artırır.

1. Aşağıdaki PowerShell veya komut satırını çalıştırarak aracıyı yeniden başlatın:

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   veya

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Hata hakkında daha fazla bilgi için günlük dosyasını gözden geçirin. Günlük dosyası, betiği çalıştırdığımız çalışma dizininde mevcut olacaktır. 

   Günlük dosyası konumu: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Sonraki adımlar

* IoT hizmetine [genel bakış](overview.md) Için Defender 'ı okuyun
* IoT [mimarisi](architecture.md) için Defender hakkında daha fazla bilgi edinin
* [Hizmeti](quickstart-onboard-iot-hub.md) etkinleştirme
* [SSS](resources-frequently-asked-questions.md) 'yi okuyun
* [Uyarıları](concept-security-alerts.md) anlama
