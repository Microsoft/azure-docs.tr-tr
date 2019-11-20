---
title: Azure Service Fabric CLı-sfctl kapsayıcısı | Microsoft Docs
description: Service Fabric CLı sfctl kapsayıcı komutlarını açıklar.
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
ms.openlocfilehash: 13de6ff7b3e5a41eced5ca49a3af38fab60ba0a0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901307"
---
# <a name="sfctl-container"></a>sfctl container
Kapsayıcı ile ilgili komutları bir küme düğümünde çalıştırın.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| Invoke-API | Verilen kod paketi için bir Service Fabric düğümüne dağıtılan bir kapsayıcıda kapsayıcı API 'sini çağırma. |
| günlükler | Service Fabric düğümüne dağıtılan kapsayıcının kapsayıcı günlüklerini alır. |

## <a name="sfctl-container-invoke-api"></a>sfctl kapsayıcısı çağırma-API
Verilen kod paketi için bir Service Fabric düğümüne dağıtılan bir kapsayıcıda kapsayıcı API 'sini çağırma.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. <br><br> Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Code-Package-Instance-id [gerekli] | Service Fabric düğümüne dağıtılan bir kod paketi örneğini benzersiz bir şekilde tanımlayan KIMLIK. <br><br> ' Service Code-Package-List ' tarafından alınabilir. |
| --Code-Package-Name [gerekli] | Hizmet bildiriminde belirtilen kod paketinin adı Service Fabric kümesindeki bir uygulama türünün bir parçası olarak kaydedilir. |
| --Container-api-Uri-Path [gerekli] | Kapsayıcı REST API URI yolu, kapsayıcı adı/kimliği yerine ' {ID} ' kullanın. |
| --Node-Name [gerekli] | Düğümün adı. |
| --hizmet-bildirim-adı [gerekli] | Service Fabric kümesinde uygulama türünün bir parçası olarak kaydedilmiş bir hizmet bildiriminin adı. |
| --Container-api-Body | Kapsayıcı REST API için HTTP istek gövdesi. |
| --Container-api-Content-Type | Kapsayıcı REST API için içerik türü, varsayılan olarak ' Application/JSON ' olarak belirlenmiştir. |
| --Container-api-http-fiil | Kapsayıcı REST API için HTTP fiili, varsayılan olarak al. |
| --timeout-t | Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-container-logs"></a>sfctl kapsayıcı günlükleri
Service Fabric düğümüne dağıtılan kapsayıcının kapsayıcı günlüklerini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. <br><br> Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Code-Package-Instance-id [gerekli] | ' Hizmet kodu-paket-listesi ' tarafından alınabilecek kod paketi örnek KIMLIĞI. |
| --Code-Package-Name [gerekli] | Hizmet bildiriminde belirtilen kod paketinin adı Service Fabric kümesindeki bir uygulama türünün bir parçası olarak kaydedilir. |
| --Node-Name [gerekli] | Düğümün adı. |
| --hizmet-bildirim-adı [gerekli] | Service Fabric kümesinde uygulama türünün bir parçası olarak kaydedilmiş bir hizmet bildiriminin adı. |
| --Tail | Günlüklerin sonundan gösterilecek satır sayısı. Varsayılan değer 100 ' dir. Tüm günlükleri göstermek için ' All '. |
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