---
title: Linux C Aracısı 'nı Install & Deploy
description: Linux 'ta IoT C tabanlı güvenlik Aracısı için Defender 'ı yüklemeyi ve dağıtmayı öğrenin
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6f59db7ff24412c66a6a4898b14272ea9540fdd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778823"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Linux için IoT C tabanlı güvenlik Aracısı için Defender 'ı dağıtma

Bu kılavuzda, Linux üzerinde IoT C tabanlı güvenlik aracısının Defender 'ın nasıl yükleneceği ve dağıtılacağı açıklanmaktadır.

- Yükleme
- Dağıtımı doğrulama
- Aracıyı kaldırın
- Sorun giderme

## <a name="prerequisites"></a>Önkoşullar

Diğer platformlar ve aracı türleri için bkz. [doğru güvenlik aracısını seçme](how-to-deploy-agent.md).

1. Güvenlik aracısını dağıtmak için, yüklemek istediğiniz makinede (sudo) yerel yönetici hakları gereklidir.

1. Cihaz için [bir Defender-IoT-mikro aracı oluşturun](quickstart-create-security-twin.md) .

## <a name="installation"></a>Yükleme

Güvenlik aracısını yüklemek ve dağıtmak için aşağıdaki iş akışını kullanın:

1. [GitHub](https://aka.ms/iot-security-github-c)'dan makinenize en son sürümü indirin.

1. Paketin içeriğini ayıklayın ve _/src/installation_ klasörüne gidin.

1. Aşağıdaki komutu çalıştırarak **ınstallsecurityagent betiğine** çalışan izinleri ekleyin:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Sonra, şunu çalıştırın:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Kimlik doğrulama parametreleri hakkında daha fazla bilgi için bkz. [kimlik doğrulamasını yapılandırma](concept-security-agent-authentication-methods.md) .

Bu betik aşağıdaki işlevi gerçekleştirir:

1. Önkoşulları kurar.

1. Bir hizmet kullanıcısı ekler (etkileşimli oturum açma devre dışı).

1. Aracıyı bir **Daemon** olarak yükleme-cihazın hizmet yönetimi için **systemd** 'yi kullandığını varsayar.

1. Aracıyı, belirtilen kimlik doğrulama parametreleriyle yapılandırır.

Ek Yardım için, – help parametresiyle betiği çalıştırın:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Aracıyı kaldırın

Aracıyı kaldırmak için--Uninstall parametresiyle betiği çalıştırın:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Sorun giderme

Aşağıdakileri çalıştırarak dağıtım durumunu denetleyin:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Sonraki adımlar

- IoT hizmetine [genel bakış](overview.md) Için Defender 'ı okuyun
- IoT [mimarisi](architecture.md) için Defender hakkında daha fazla bilgi edinin
- [Hizmeti](quickstart-onboard-iot-hub.md) etkinleştirme
- [SSS](resources-frequently-asked-questions.md) 'yi okuyun
- [Güvenlik uyarılarını](concept-security-alerts.md) anlama
