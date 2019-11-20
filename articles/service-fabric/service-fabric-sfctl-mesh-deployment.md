---
title: Azure Service Fabric CLı-sfctl kafes dağıtımı | Microsoft Docs
description: CLı sfctl ağ dağıtım komutlarını Service Fabric açıklar.
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
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901245"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Service Fabric kafes kaynakları oluşturun.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| oluşturmaya | Service Fabric kafes kaynakları dağıtımı oluşturur. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl kafes dağıtımı oluşturma
Service Fabric kafes kaynakları dağıtımı oluşturur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Input-YAML-Files [gerekli] | Tüm YAML dosyalarının virgülle ayrılmış göreli/mutlak dosya yolları veya YAML dosyaları içeren dizinin (özyinelemeli) göreli/mutlak yolu. |
| --Parametreler | YAML dosyasına yönelik göreli/mutlak yol veya geçersiz kılınabilmesi gereken parametreleri içeren bir JSON nesnesi. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

### <a name="examples"></a>Örnekler

YAML dosyasında bahsedilen parametreleri geçersiz kılarak kümeleyerek tüm kaynakları birleştirir ve dağıtır
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

YAML dosyasında bahsedilen parametreleri geçersiz kılarak kümeleyerek bir dizindeki tüm kaynakları birleştirir ve dağıtır

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Doğrudan JSON nesnesi olarak geçirilen parametreleri geçersiz kılarak kümelemek için bir dizindeki tüm kaynakları birleştirir ve dağıtır
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Sonraki adımlar
- Service Fabric CLı 'yi [ayarlayın](service-fabric-cli.md) .
- [Örnek betikleri](/azure/service-fabric/scripts/sfctl-upgrade-application)kullanarak Service Fabric CLI 'nın nasıl kullanılacağını öğrenin.