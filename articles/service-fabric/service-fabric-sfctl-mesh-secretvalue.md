---
title: Azure Service Fabric CLı-sfctl ağ secretvalue | Microsoft Docs
description: Service Fabric CLı sfctl ağ secretvalue komutlarını açıklar.
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
ms.openlocfilehash: 96ce4efe00d89c136a0c11d445170b2f67be6fcd
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901186"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Ağ secretvalue kaynaklarını alın ve silin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| delete | Adlandırılmış gizli kaynağın belirtilen değerini siler. |
| list | Belirtilen gizli kaynağın tüm değerlerinin adlarını listeleyin. |
| göster | Gizli kaynağın belirtilen değerini listeler. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl ağ secretvalue Delete
Adlandırılmış gizli kaynağın belirtilen değerini siler.

Ad tarafından tanımlanan gizli değer kaynağını siler. Kaynağın adı genellikle bu değerle ilişkili sürümdür. Belirtilen değer kullanımda olduğunda silme işlemi başarısız olur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Secret-adı-n [gerekli] | Gizli kaynağın adı. |
| --sürüm-v [gerekli] | Gizli dizi sürümünün adı. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl ağ secretvalue listesi
Belirtilen gizli kaynağın tüm değerlerinin adlarını listeleyin.

Belirtilen gizli kaynağın tüm gizli değer kaynakları hakkında bilgi alır. Bilgiler, gizli değer kaynaklarının adlarını içerir, ancak gerçek değerleri içermez.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Secret-adı-n [gerekli] | Gizli kaynağın adı. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl ağı secretvalue Show
Gizli kaynağın belirtilen değerini listeler.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Secret-adı-n [gerekli] | Gizli kaynağın adı. |
| --sürüm-v [gerekli] | Gizli dizi sürümünün adı. |
| --değer göster | Gizli sürümün gerçek değerini gösterir. |

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