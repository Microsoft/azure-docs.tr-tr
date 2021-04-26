---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019164"
---
|Parametre Adı| Tür | Description| Olası Değerler|
|-|-|-|-|
| /ServerMode|Zorunlu|Hem yapılandırma hem de işlem sunucusunun mu yoksa yalnızca işlem sunucusunun mu yükleneceğini belirtir|CS<br>PS|
|/InstallLocation|Zorunlu|Bileşenlerin yüklendiği klasör| Bilgisayardaki herhangi bir klasör|
|/MySQLCredsFilePath|Zorunlu|MySQL sunucusu kimlik bilgilerinin depolandığı dosya yolu|Dosya aşağıda belirtilen biçimde olmalıdır|
|/VaultCredsFilePath|Zorunlu|Kasa kimlik bilgileri dosyasının yolu|Geçerli dosya yolu|
|/EnvType|Zorunlu|Korumak istediğiniz ortam türü |VMware<br>NonVMware|
|/PSIP|Zorunlu|Çoğaltma veri aktarımı için kullanılacak NIC’nin IP adresi| Herhangi bir geçerli IP adresi|
|/CSIP|Zorunlu|Yapılandırma sunucusunun dinleme yaptığı NIC’nin IP adresi| Herhangi bir geçerli IP adresi|
|/PassphraseFilePath|Zorunlu|Parola dosyası konumunun tam yolu|Geçerli dosya yolu|
|/BypassProxy|İsteğe Bağlı|Yapılandırma sunucusunun Azure'a bir ara sunucu olmadan bağlandığını belirtir||
|/ProxySettingsFilePath|İsteğe Bağlı|Ara sunucu ayarları (Varsayılan ara sunucu kimlik doğrulaması gerektirir ya da özel bir ara sunucu kullanılır)|Dosya aşağıda belirtilen biçimde olmalıdır|
|DataTransferSecurePort|İsteğe Bağlı|Çoğaltma verileri için kullanılacak PSIP’deki bağlantı noktası numarası| Geçerli Bağlantı Noktası Numarası (varsayılan değer: 9433)|
|/SkipSpaceCheck|İsteğe Bağlı|Önbellek diski için alan denetimini atlama| |
|/AcceptThirdpartyEULA|Zorunlu|Bayrak, üçüncü taraf EULA'nın kabul edildiğini gösterir| |
|/ShowThirdpartyEULA|İsteğe Bağlı|Üçüncü taraf EULA belgesini görüntüler. Giriş olarak sağlanırsa, diğer tüm parametreler yoksayılır| |
