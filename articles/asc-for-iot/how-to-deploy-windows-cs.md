---
title: IoT Aracısı için Azure Güvenlik Merkezi 'nin Windows yüklemesi | Microsoft Docs
description: 32-bit veya 64 bit Windows cihazlarına IoT Aracısı için Azure Güvenlik Merkezi 'nin nasıl yükleneceği hakkında bilgi edinin.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597217"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Windows için IoT C#tabanlı güvenlik aracısına yönelik bir Azure Güvenlik Merkezi dağıtma

Bu kılavuzda, Windows 'da IoT C#tabanlı güvenlik aracısının Azure Güvenlik Merkezi 'nin nasıl yükleneceği açıklanmaktadır.

Bu kılavuzda şunların nasıl yapıldığını öğrenirsiniz: 
> [!div class="checklist"]
> * Yükleme
> * Dağıtımı doğrulama
> * Aracıyı Kaldırma
> * Sorun giderme 

## <a name="prerequisites"></a>Önkoşullar

Diğer platformlar ve aracı türleri için bkz. [doğru güvenlik aracısını seçme](how-to-deploy-agent.md).

1. Yüklemek istediğiniz makinede yerel yönetici hakları. 

1. Cihaz için [bir güvenlik modülü oluşturun](quickstart-create-security-twin.md) .

## <a name="installation"></a>Yükleme 

Güvenlik aracısını yüklemek için aşağıdaki iş akışını kullanın:

1. IoT Windows C# Aracısı Için Azure Güvenlik Merkezi ' ni cihaza yükler. IoT [GitHub deposu](https://github.com/Azure/Azure-IoT-Security-Agent-CS)Için Azure Güvenlik Merkezi ' nden makinenize en son sürümü indirin.

1. Paketin içeriğini ayıklayın ve/Install klasörüne gidin.

1. Windows PowerShell 'i yönetici olarak açın. 
1. Şu komutu çalıştırarak ınstallsecurityagent betiğine çalışan izinleri ekleyin:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    ardından şunu çalıştırın:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Örneğin:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Kimlik doğrulama parametreleri hakkında daha fazla bilgi için bkz. [kimlik doğrulamasını yapılandırma](concept-security-agent-authentication-methods.md).

Bu betik aşağıdaki eylemleri yapar:

- Önkoşulları kurar.

- Bir hizmet kullanıcısı ekler (etkileşimli oturum açma devre dışı).

- Aracıyı bir **sistem hizmeti**olarak kurar.

- Aracıyı, belirtilen kimlik doğrulama parametreleriyle yapılandırır.


Ek Yardım için PowerShell 'de Get-Help komutunu kullanın <br>Get-Help örneği:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Dağıtım durumunu doğrula

- Şunu çalıştırarak aracı dağıtım durumunu denetleyin:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Aracıyı Kaldırma

Aracıyı kaldırmak için:

1. Aşağıdaki PowerShell betiğini, **-Mode** parametresi **Uninstall**olarak ayarlanmış şekilde çalıştırın.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Sorun giderme

Aracı başlamazsa, daha fazla bilgi almak için günlüğü açın (varsayılan olarak günlüğe kaydetme *kapalıdır* ).

Günlüğe kaydetmeyi açmak için:

1. Standart bir dosya Düzenleyicisi kullanarak düzenleme için yapılandırma dosyasını (genel. config) açın.

1. Aşağıdaki değerleri düzenleyin:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Sorun giderme işlemi tamamlandıktan sonra oturumu **kapatmayı** öneririz. Günlüğe kaydetmeyi **bırakmak** günlük dosyası boyutunu ve veri kullanımını artırır. 

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

1. Hata hakkında daha fazla bilgi için günlük dosyasını gözden geçirin.

   Günlük dosyası konumu:`%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Sonraki adımlar
- IoT hizmetine [genel bakış](overview.md) Için Azure Güvenlik Merkezi 'ni okuyun
- IoT [mimarisi](architecture.md) Için Azure Güvenlik Merkezi hakkında daha fazla bilgi edinin
- [Hizmeti](quickstart-onboard-iot-hub.md) etkinleştirme
- [SSS](resources-frequently-asked-questions.md) 'yi okuyun
- [Uyarıları](concept-security-alerts.md) anlama