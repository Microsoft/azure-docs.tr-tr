---
title: Azure Service Fabric CLı-sfctl bölümü | Microsoft Docs
description: Service Fabric CLı sfctl bölüm komutlarını açıklar.
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
ms.openlocfilehash: 2c2ebb7cb08cb6b6b2130290c81fa9e07766b5e2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901076"
---
# <a name="sfctl-partition"></a>sfctl partition
Tüm hizmet için bölümleri sorgulayın ve yönetin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| veri kaybı | Bu API, belirtilen bölüm için veri kaybına yol açar. |
| veri kaybı-durum | StartDataLoss API kullanılarak başlatılan bir bölüm veri kaybı işleminin ilerlemesini alır. |
| sağlık | Belirtilen Service Fabric bölümünün sistem durumunu alır. |
| Bilgisine | Service Fabric bölüm hakkındaki bilgileri alır. |
| list | Service Fabric bir hizmetin bölümlerinin listesini alır. |
| yükleme | Belirtilen Service Fabric bölümünün yükleme bilgilerini alır. |
| Yükleme-sıfırlama | Service Fabric bölümün geçerli yükünü sıfırlar. |
| çekirdek kaybı | Belirli bir durum bilgisi olan hizmet bölümü için çekirdek kaybına yol açar. |
| çekirdek kaybı-durum | Startquorumkaybetme API 'SI kullanılarak başlatılan bir bölümdeki çekirdek kaybı işleminin ilerlemesini alır. |
| Kurtarılamıyor | Service Fabric kümesine, şu anda çekirdek kaybına takılmış olan belirli bir bölümü kurtarmaya çalışılması gerektiğini gösterir. |
| tümünü kurtar | Service Fabric kümesine, şu anda çekirdek kaybına takılmış olan Hizmetleri (sistem hizmetleri dahil) kurtarmaya çalışılması gerektiğini gösterir. |
| rapor-sistem durumu | Service Fabric bölümünde bir sistem durumu raporu gönderir. |
| restart | Bu API, belirtilen bölümün bazı veya tüm çoğaltmalarını veya örneklerini yeniden başlatacak. |
| yeniden başlatma durumu | StartPartitionRestart kullanılarak başlatılan bir PartitionRestart işleminin ilerlemesini alır. |
| svc-adı | Bir bölüm için Service Fabric hizmetinin adını alır. |

## <a name="sfctl-partition-data-loss"></a>sfctl bölüm verileri-kayıp
Bu API, belirtilen bölüm için veri kaybına yol açar.

Bölümün OnDataLossAsync API 'sine bir çağrı tetikler.  Bu API, belirtilen bölüm için veri kaybına yol açar. Bölümün OnDataLoss API 'sine bir çağrı tetikler. Gerçek veri kaybı, belirtilen DataLossMode 'a bağlı olacaktır.  <br> -PartialDataLoss-yalnızca bir çoğaltma çekirdeği kaldırılır ve bölüm için OnDataLoss tetiklenir, ancak gerçek veri kaybı, uçuş için çoğaltma varlığına bağlıdır.  <br> -FullDataLoss-tüm çoğaltmalar kaldırılır, bu nedenle tüm veriler kaybedilir ve OnDataLoss tetiklenir. Bu API, hedef olarak yalnızca durum bilgisi olan bir hizmet ile çağrılmalıdır. Hedef olarak bu API 'YI bir sistem hizmetiyle çağırmak önerilmez.

> [!NOTE]   
> Bu API çağrıldıktan sonra geri alınamaz. CancelOperation çağrısı yalnızca yürütmeyi durdurur ve iç sistem durumunu temizler. Komutun veri kaybına neden olmak için yeterince ilerlemedi varsa verileri geri yükleme işlemi olmayacaktır. Bu API ile başlatılan işlem hakkında bilgi döndürmek için aynı operationId ile GetDataLossProgress API 'sini çağırın.
### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --veri kaybı modu [gerekli] | Bu Enum, ne tür veri kaybı olduğunu göstermek için StartDataLoss API 'sine geçirilir. |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl bölüm verileri-kayıp-durum
StartDataLoss API kullanılarak başlatılan bir bölüm veri kaybı işleminin ilerlemesini alır.

OperationId kullanarak, StartDataLoss ile başlatılan bir veri kaybı işleminin ilerleme durumunu alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-health"></a>sfctl bölüm durumu
Belirtilen Service Fabric bölümünün sistem durumunu alır.

Hizmette bildirilen sistem durumu olaylarının toplanmasını sistem durumuna göre filtrelemek için EventsHealthStateFilter ' i kullanın. Bölüm üzerindeki ReplicaHealthState nesnelerinin koleksiyonunu filtrelemek için ReplicasHealthStateFilter kullanın. Sistem durumu deposunda mevcut olmayan bir bölüm belirtirseniz bu istek bir hata döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --Events-Sistem durumu-durum-filtre | Sistem durumu temelinde döndürülen HealthEvent nesnelerinin toplanmasını filtrelemeye izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen olaylar döndürülür. Tüm olaylar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri (2) ve uyarı (4) olan tüm olaylar döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --exclude-sistem durumu-istatistikler | Durum istatistiklerinin Sorgu sonucunun bir parçası olarak döndürülüp döndürülmeyeceğini gösterir. Varsayılan olarak false. İstatistikler sistem durumu Tamam, uyarı ve hata içindeki alt öğe varlıklarının sayısını gösterir. |
| --çoğaltmalar-sistem durumu-filtre | Bölümdeki ReplicaHealthState nesnelerinin koleksiyonunu filtrelemesine izin verir. Değer, HealthStateFilter üyeleri üzerinde Üyeler veya bit düzeyinde işlemlerden elde edilebilir. Yalnızca filtreyle eşleşen çoğaltmalar döndürülür. Tüm çoğaltmalar, toplanan sistem durumunu değerlendirmek için kullanılacaktır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri (2) ve uyarı (4) olan tüm olaylar döndürülür. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-info"></a>sfctl bölüm bilgisi
Service Fabric bölüm hakkındaki bilgileri alır.

Belirtilen bölüm hakkındaki bilgileri alır. Yanıt, bölüm KIMLIĞINI, bölümleme şeması bilgilerini, Bölüm tarafından desteklenen anahtarları, durumu, durumu ve bölüm hakkındaki diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-list"></a>sfctl Bölüm listesi
Service Fabric bir hizmetin bölümlerinin listesini alır.

Yanıt, bölüm KIMLIĞINI, bölümleme şeması bilgilerini, Bölüm tarafından desteklenen anahtarları, durumu, durumu ve bölüm hakkındaki diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-load"></a>sfctl bölüm yüklemesi
Belirtilen Service Fabric bölümünün yükleme bilgilerini alır.

Belirtilen bölümün yüklenmesi hakkındaki bilgileri döndürür. Yanıt, Service Fabric bölüm için yükleme raporlarının bir listesini içerir. Her rapor, UTC 'de yük ölçümü adı, değeri ve son raporlanan saati içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-load-reset"></a>sfctl bölüm yükleme-sıfırlama
Service Fabric bölümün geçerli yükünü sıfırlar.

Bir Service Fabric bölümünün geçerli yükünü hizmetin varsayılan yüküne sıfırlar.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl bölüm çekirdeği-kayıp
Belirli bir durum bilgisi olan hizmet bölümü için çekirdek kaybına yol açar.

Bu API, hizmetinize geçici bir çekirdek kaybı durumu için faydalıdır. Bu API ile başlatılan işlem hakkında bilgi döndürmek için aynı operationId ile GetQuorumLossProgress API 'sini çağırın. Bu, yalnızca durum bilgisi olan kalıcı (HasPersistedState = = true) hizmetlerde çağrılabilir.  Bu API 'yi durum bilgisi olmayan hizmetlerde veya yalnızca bellek içi hizmetlerde kullanmayın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çekirdek-kayıp-süre [gerekli] | Bölüm çekirdek kaybına göre tutulacak zaman miktarı.  Bu, saniyeler içinde belirtilmelidir. |
| --çekirdek-kayıp modu [gerekli] | Bu Enum, ne tür çekirdek kaybı olduğunu göstermek için Startquorumkaybetme API 'sine geçirilir. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl bölüm çekirdeği-kayıp-durum
Startquorumkaybetme API 'SI kullanılarak başlatılan bir bölümdeki çekirdek kaybı işleminin ilerlemesini alır.

Belirtilen operationId kullanılarak Startquorumkaybetme ile başlatılan bir çekirdek kaybı işleminin ilerlemesini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-recover"></a>sfctl bölüm kurtarma
Service Fabric kümesine, şu anda çekirdek kaybına takılmış olan belirli bir bölümü kurtarmaya çalışılması gerektiğini gösterir.

Bu işlem yalnızca, alt kopyaların kurtarılamamış olduğu biliniyorsa gerçekleştirilmelidir. Bu API 'nin yanlış kullanımı olası veri kaybına neden olabilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-recover-all"></a>sfctl bölüm kurtarma-tümü
Service Fabric kümesine, şu anda çekirdek kaybına takılmış olan Hizmetleri (sistem hizmetleri dahil) kurtarmaya çalışılması gerektiğini gösterir.

Bu işlem yalnızca, alt kopyaların kurtarılamamış olduğu biliniyorsa gerçekleştirilmelidir. Bu API 'nin yanlış kullanımı olası veri kaybına neden olabilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-report-health"></a>sfctl bölüm raporu-sistem durumu
Service Fabric bölümünde bir sistem durumu raporu gönderir.

Belirtilen Service Fabric bölümünün sistem durumunu raporlar. Rapor, sistem durumu raporunun ve rapor alındığı özelliğin kaynağı hakkında bilgi içermelidir. Rapor, sistem durumu deposuna ileten bir Service Fabric ağ geçidi bölümüne gönderilir. Rapor, ağ geçidi tarafından kabul edilebilir, ancak ek doğrulamadan sonra sistem durumu deposu tarafından reddedildi. Örneğin, eski bir sıra numarası gibi geçersiz bir parametre nedeniyle sistem durumu deposu raporu reddedebilir. Raporun sistem durumu deposuna uygulanıp uygulanmadığını görmek için, raporun Olaylar bölümünde görünüp göründüğünü kontrol edin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Health-Property [gerekli] | Sistem durumu bilgilerinin özelliği. <br><br> Bir varlık, farklı özellikler için sistem durumu raporlarına sahip olabilir. Özelliği, rapor tetikleyen durum koşulunu kategorilere ayırmak için Raporlayıcı esnekliğine izin veren sabit bir sabit listesi olmayan bir dizedir. Örneğin, SourceId "Localizçi" olan bir Raporlayıcı, bir düğümdeki kullanılabilir diskin durumunu izleyebilir, bu nedenle söz konusu düğümdeki "AvailableDisk" özelliğini bildirebilirler. Aynı Reporter, düğüm bağlantısını izleyip aynı düğümde bir özelliği "bağlantı" bildirebilirler. Sistem durumu deposunda, bu raporlar belirtilen düğüm için ayrı sistem durumu olayları olarak kabul edilir. SourceID ile birlikte, özelliği sistem durumu bilgilerini benzersiz şekilde tanımlar. |
| --sistem durumu-durumu [gerekli] | Olası değerler arasında\: ' geçersiz ', ' Tamam ', ' uyarı ', ' Error ', ' Unknown ' verilebilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --kaynak-kimliği [gerekli] | Sistem durumu bilgilerini oluşturan istemci/izleme/sistem bileşenini tanımlayan kaynak adı. |
| --Açıklama | Sistem durumu bilgilerinin açıklaması. <br><br> Bu, raporla ilgili okunabilir bilgileri eklemek için kullanılan serbest metni temsil eder. Açıklama için en fazla dize uzunluğu 4096 karakterdir. Belirtilen dize daha uzunsa otomatik olarak kesilir. Kesilmişse, açıklamanın son karakterleri "[kesildi]" işaretçisini ve toplam dize boyutu 4096 karakterdir. İşaretin varlığı, kesilmesinin gerçekleştiği kullanıcılara işaret eden anlamına gelir. Kesilmişse, açıklamanın özgün dizeden 4096 karakterden daha az olduğunu unutmayın. |
| --hemen | Raporun hemen gönderilmesi gerekip gerekmediğini belirten bir bayrak. <br><br> Bir sistem durumu raporu, sistem durumu deposuna ileten bir Service Fabric ağ geçidi uygulamasına gönderilir. Immediate değeri true olarak ayarlanırsa, HTTP ağ geçidi uygulamasının kullandığı doku istemci ayarlarından bağımsız olarak, rapor HTTP ağ geçidinden sistem durumu deposuna anında gönderilir. Bu, mümkün olan en kısa sürede gönderilmesi gereken kritik raporlar için kullanışlıdır. Zamanlama ve diğer koşullara bağlı olarak, raporun gönderilmesi yine de başarısız olabilir, örneğin, HTTP ağ geçidi kapalıysa veya ileti ağ geçidine ulaşmazsa. Anında false değeri ayarlandıysa, rapor HTTP ağ geçidindeki sistem durumu istemci ayarlarına bağlı olarak gönderilir. Bu nedenle, Healthreportsendınterval yapılandırmasına göre toplu hale gelir. Durum istemcisinin sistem durumu raporlama iletilerini sistem durumu rapor işleme ile en iyi hale getirmesine izin verdiğinden, bu önerilen ayardır. Varsayılan olarak, raporlar anında gönderilmez. |
| ----süre sonu | Raporun süresi dolmuşsa sistem durumu deposundan kaldırılıp kaldırılmadığını belirten değer. <br><br> True olarak ayarlanırsa, rapor süresi dolduktan sonra sistem durumu deposundan kaldırılır. False olarak ayarlanırsa rapor, süre dolduğunda hata olarak değerlendirilir. Bu özelliğin değeri varsayılan olarak false 'tur. İstemciler düzenli olarak rapor edildiğinde Removewhenererererfalse (varsayılan) olarak ayarlanmalıdır. Bu şekilde, Raporlayıcı sorunları (örn. kilitlenme) ve raporleyemiyorum, sistem durumu raporunun süresi dolarsa varlık hata olarak değerlendirilir. Bu, varlığı hata sağlık durumunda olduğu şekilde işaretler. |
| --sıra-sayısı | Bu sistem durumu raporunun sayısal bir dize olarak sıra numarası. <br><br> Rapor sıra numarası, eski raporları algılamak için sistem durumu deposu tarafından kullanılır. Belirtilmemişse, bir rapor eklendiğinde sistem durumu istemcisi tarafından otomatik olarak bir sıra numarası oluşturulur. |
| --timeout-t | Varsayılan\: 60. |
| --TTL | Bu sistem durumu raporunun geçerli olduğu süre. Bu alan, süreyi belirtmek için ıSO8601 biçimini kullanır. <br><br> İstemciler düzenli olarak raporladığında, en yüksek sıklıkta raporlar göndermelidir. İstemciler geçişe rapor alıyorsa, süresi sonsuz olarak ayarlayabilir. Yaşam süresi dolduğunda, sistem durumu bilgilerini içeren sistem durumu olayı sistem durumu deposundan kaldırılır, Removewhenexpires true ise ya da Removewhenexpires false olduğunda hata durumunda değerlendirilir. Belirtilmemişse, yaşam süresi varsayılan olarak sonsuz değer olur. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-restart"></a>sfctl bölümünün yeniden başlatılması
Bu API, belirtilen bölümün bazı veya tüm çoğaltmalarını veya örneklerini yeniden başlatacak.

Bu API, yük devretmeyi test etmek için kullanışlıdır. Durum bilgisi olmayan bir hizmet bölümünü hedeflemek için kullanılırsa, RestartPartitionMode Allreplicasorınstances olmalıdır. İlerlemeyi almak için aynı operationId 'YI kullanarak GetPartitionRestartProgress API 'sini çağırın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --Restart-Partition-Mode [gerekli] | Hangi bölümlerin yeniden başlatılması gerektiğini açıklama. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-restart-status"></a>sfctl bölümünün yeniden başlatılması-durum
StartPartitionRestart kullanılarak başlatılan bir PartitionRestart işleminin ilerlemesini alır.

Belirtilen operationId kullanılarak StartPartitionRestart ile başlatılan PartitionRestart işleminin ilerlemesini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-partition-svc-name"></a>sfctl bölüm svc-adı
Bir bölüm için Service Fabric hizmetinin adını alır.

Belirtilen bölüm için hizmetin adını alır. Bölüm KIMLIĞI kümede yoksa 404 hatası döndürülür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

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
