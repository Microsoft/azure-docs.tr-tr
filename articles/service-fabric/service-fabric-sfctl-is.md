---
title: Azure Service Fabric CLı-sfctl | Microsoft Docs
description: CLı sfctl komutlarının Service Fabric açıklar.
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
ms.openlocfilehash: 9a09d8c36fd282450767880a9ad144e1192dcd2e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901577"
---
# <a name="sfctl-is"></a>sfctl is
Altyapı hizmetine komutları sorgulayın ve gönderin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| command | Verilen altyapı hizmeti örneğinde bir yönetim komutu çağırır. |
| sorgu | Verilen altyapı hizmeti örneğinde salt okunurdur bir sorgu çağırır. |

## <a name="sfctl-is-command"></a>sfctl komutu
Verilen altyapı hizmeti örneğinde bir yönetim komutu çağırır.

Altyapı hizmeti 'nin yapılandırılmış bir veya daha fazla örneği olan kümeler için bu API, altyapı hizmeti 'nin belirli bir örneğine altyapıya özgü komutlar göndermek için bir yol sağlar. Kullanılabilir komutlar ve bunlara karşılık gelen yanıt biçimleri, kümenin üzerinde çalıştığı altyapıya bağlı olarak değişiklik gösterir. Bu API Service Fabric platformunu destekler; doğrudan kodunuzdan kullanılması amaçlıyordu.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --komut [gerekli] | Çağrılacak komutun metni. Komutun içeriği altyapıya özgüdür. |
| --hizmet kimliği | Altyapı hizmetinin kimliği. <br><br> Bu, altyapı hizmetinin ' Fabric\:' URI düzeni olmadan tam adıdır. Bu parametre yalnızca, çalışan bir altyapı hizmeti örneğine sahip olan küme için gereklidir. |
| --timeout-t | Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-is-query"></a>sfctl sorgu
Verilen altyapı hizmeti örneğinde salt okunurdur bir sorgu çağırır.

Altyapı hizmeti 'nin yapılandırılmış bir veya daha fazla örneği olan kümeler için bu API, altyapı hizmeti 'nin belirli bir örneğine altyapıya özgü sorgular göndermek için bir yol sağlar. Kullanılabilir komutlar ve bunlara karşılık gelen yanıt biçimleri, kümenin üzerinde çalıştığı altyapıya bağlı olarak değişiklik gösterir. Bu API Service Fabric platformunu destekler; doğrudan kodunuzdan kullanılması amaçlıyordu.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --komut [gerekli] | Çağrılacak komutun metni. Komutun içeriği altyapıya özgüdür. |
| --hizmet kimliği | Altyapı hizmetinin kimliği. <br><br> Bu, altyapı hizmetinin ' Fabric\:' URI düzeni olmadan tam adıdır. Bu parametre yalnızca, çalışan bir altyapı hizmeti örneğine sahip olan küme için gereklidir. |
| --timeout-t | Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |


## <a name="next-steps"></a>Sonraki adımlar
- Service Fabric CLı 'yi [ayarlayın](service-fabric-cli.md) .
- [Örnek betikleri](/azure/service-fabric/scripts/sfctl-upgrade-application)kullanarak Service Fabric CLI 'nın nasıl kullanılacağını öğrenin.