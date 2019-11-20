---
title: Azure Service Fabric CLı-sfctl ayarları telemetrisi | Microsoft Docs
description: Service Fabric CLı sfctl ayarları telemetri komutlarını açıklar.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: d926c71ae8fd9e196b86c14c7fb96cc65b587b65
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900987"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Bu sfctl örneğine yerelde telemetri ayarlarını yapılandırın.

Sfctl telemetrisi parametresiz komut adı ya da değerleri, Sfctl sürümü, işletim sistemi türü, Python sürümü, komutun başarısı veya başarısızlığı, döndürülen hata iletisi.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| Set-telemetri | Telemetriyi açın veya kapatın. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl ayarları telemetri kümesi-telemetri
Telemetriyi açın veya kapatın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --kapalı | Telemetriyi devre dışı bırakın. |
| --Açık | Telemetriyi açın. Varsayılan değer budur. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

### <a name="examples"></a>Örnekler

Telemetriyi devre dışı bırakın.

```
sfctl settings telemetry set_telemetry --off
```

Telemetriyi açın.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Sonraki adımlar
- Service Fabric CLı 'yi [ayarlayın](service-fabric-cli.md) .
- [Örnek betikleri](/azure/service-fabric/scripts/sfctl-upgrade-application)kullanarak Service Fabric CLI 'nın nasıl kullanılacağını öğrenin.