---
title: Azure Service Fabric CLı-sfctl rpm | Microsoft Docs
description: Service Fabric CLı sfctl RPM komutlarını açıklar.
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
ms.openlocfilehash: 9c37dc8131f14a3b35e68b3e88502c91f96810f6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901028"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Komutları onarma Yöneticisi hizmetine sorgula ve gönder.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| Onayla-zorla | Verilen onarım görevinin onayını zorlar. |
| delete | Tamamlanmış bir onarım görevini siler. |
| list | Verilen filtrelerle eşleşen Onarım görevlerinin listesini alır. |

## <a name="sfctl-rpm-approve-force"></a>sfctl RPM onaylama-zorlama
Verilen onarım görevinin onayını zorlar.

Bu API Service Fabric platformunu destekler; doğrudan kodunuzdan kullanılması amaçlıyordu.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --görev-kimliği [gerekli] | Onarım görevinin KIMLIĞI. |
| --sürüm | Onarım görevinin geçerli sürüm numarası. Sıfır olmayan bir değer varsa, istek yalnızca, onarım görevinin gerçek geçerli sürümüyle eşleşiyorsa başarılı olur. Sıfır ise, sürüm denetimi gerçekleştirilmez. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-rpm-delete"></a>sfctl RPM silme
Tamamlanmış bir onarım görevini siler.

Bu API Service Fabric platformunu destekler; doğrudan kodunuzdan kullanılması amaçlıyordu.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --görev-kimliği [gerekli] | Silinecek tamamlanmış onarım görevinin KIMLIĞI. |
| --sürüm | Onarım görevinin geçerli sürüm numarası. Sıfır olmayan bir değer varsa, istek yalnızca, onarım görevinin gerçek geçerli sürümüyle eşleşiyorsa başarılı olur. Sıfır ise, sürüm denetimi gerçekleştirilmez. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-rpm-list"></a>sfctl RPM listesi
Verilen filtrelerle eşleşen Onarım görevlerinin listesini alır.

Bu API Service Fabric platformunu destekler; doğrudan kodunuzdan kullanılması amaçlıyordu.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Yürütücü-filtre | İstenen görevler listede yer almalıdır onarım yürütücüsü adı. |
| --durum-filtre | Sonuç listesine hangi görev durumlarının ekleneceğini belirten, aşağıdaki değerlerden oluşan bit düzeyinde OR. <br> 1-oluşturuldu <br>2-talep edilen  <br>4-hazırlama  <br>8-onaylanan  <br>16-yürütülüyor  <br>32-geri yükleme  <br>64-tamamlandı |
| --görev kimliği-filtre | Eşleştirilecek onarım görev KIMLIĞI ön eki. |

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