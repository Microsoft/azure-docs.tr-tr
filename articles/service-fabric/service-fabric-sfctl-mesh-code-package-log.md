---
title: Azure Service Fabric CLı-sfctl ağ kodu-paket-günlük | Microsoft Docs
description: CLı sfctl ağ kodu-paket-günlük komutlarını Service Fabric açıklar.
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
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901274"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Verilen hizmet çoğaltması için belirtilen kod paketinin kapsayıcısına ait günlükleri al.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| Al | Kapsayıcıdan günlükleri alır. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl ağ kodu-paket-günlük al
Kapsayıcıdan günlükleri alır.

Hizmet çoğaltmasının belirtilen kod paketinin kapsayıcısına ait günlükleri alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --App-Name--uygulama-adı [gerekli] | Uygulamanın adı. |
| --Code-Package-Name [gerekli] | Hizmetin kod paketinin adı. |
| --çoğaltma-adı [gerekli] | Service Fabric çoğaltma adı. |
| --hizmet-adı [gerekli] | Hizmetin adı. |
| --Tail | Günlüklerin sonundan gösterilecek satır sayısı. Varsayılan değer 100 ' dir. Tüm günlükleri göstermek için ' All '. |

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