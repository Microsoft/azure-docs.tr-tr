---
title: Azure Service Fabric CLı-sfctl çoğaltması | Microsoft Docs
description: Service Fabric CLı sfctl çoğaltma komutlarını açıklar.
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
ms.openlocfilehash: 34e53311e5338e540c1d987896112de39890de3b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901043"
---
# <a name="sfctl-replica"></a>sfctl replica
Hizmet bölümlerine ait çoğaltmaları yönetin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| dağıtılan | Service Fabric düğümüne dağıtılan çoğaltmanın ayrıntılarını alır. |
| dağıtılan-liste | Bir Service Fabric düğümüne dağıtılan çoğaltmaların listesini alır. |
| sağlık | Service Fabric durum bilgisi olan bir hizmet çoğaltmasının veya durum bilgisi olmayan hizmet örneğinin sistem durumunu alır. |
| Bilgisine | Service Fabric bölümün bir yinelemesi hakkındaki bilgileri alır. |
| list | Service Fabric hizmet bölümünün çoğaltmaları hakkındaki bilgileri alır. |
| temizlenmesine | Düğüm üzerinde çalışan bir hizmet çoğaltmasını kaldırır. |
| rapor-sistem durumu | Service Fabric çoğaltmasında bir sistem durumu raporu gönderir. |
| restart | Bir düğümde çalışan kalıcı bir hizmetin hizmet çoğaltmasını yeniden başlatır. |

## <a name="sfctl-replica-deployed"></a>sfctl çoğaltması dağıtıldı
Service Fabric düğümüne dağıtılan çoğaltmanın ayrıntılarını alır.

Service Fabric düğümüne dağıtılan çoğaltmanın ayrıntılarını alır. Bu bilgiler hizmet türü, hizmet adı, geçerli hizmet işlemi, geçerli hizmet işlemi başlangıç tarihi saati, bölüm KIMLIĞI, çoğaltma/örnek KIMLIĞI, bildirilen yük ve diğer bilgileri içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çoğaltma-kimliği [gerekli] | Çoğaltmanın tanımlayıcısı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-replica-deployed-list"></a>sfctl çoğaltması dağıtıldı-liste
Bir Service Fabric düğümüne dağıtılan çoğaltmaların listesini alır.

Bir Service Fabric düğümüne dağıtılan çoğaltmalar hakkındaki bilgileri içeren listeyi alır. Bilgi, bölüm KIMLIĞI, çoğaltma KIMLIĞI, çoğaltmanın durumu, hizmetin adı, hizmet türünün adı ve diğer bilgiler içerir. Bu parametreler için belirtilen değerlerle eşleşen dağıtılan çoğaltmalar hakkında bilgi döndürmek için PartitionID veya ServiceManifestName sorgu parametrelerini kullanın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --bölüm kimliği | Bölümün kimliği. |
| --hizmet-bildirim-adı | Service Fabric kümesinde uygulama türünün bir parçası olarak kaydedilmiş bir hizmet bildiriminin adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-replica-health"></a>sfctl çoğaltma sistem durumu
Service Fabric durum bilgisi olan bir hizmet çoğaltmasının veya durum bilgisi olmayan hizmet örneğinin sistem durumunu alır.

Service Fabric çoğaltmasının sistem durumunu alır. Sistem durumuna bağlı olarak çoğaltmada bildirilen sistem durumu olaylarının toplanmasını filtrelemek için EventsHealthStateFilter kullanın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çoğaltma-kimliği [gerekli] | Çoğaltmanın tanımlayıcısı. |
| --Events-Sistem durumu-durum-filtre | Sistem durumu temelinde döndürülen HealthEvent nesnelerinin toplanmasını filtrelemeye izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen olaylar döndürülür. Tüm olaylar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri (2) ve uyarı (4) olan tüm olaylar döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-replica-info"></a>sfctl çoğaltma bilgileri
Service Fabric bölümün bir yinelemesi hakkındaki bilgileri alır.

Yanıt, KIMLIK, rol, durum, durum, düğüm adı, çalışma süresi ve çoğaltma hakkında diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çoğaltma-kimliği [gerekli] | Çoğaltmanın tanımlayıcısı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-replica-list"></a>sfctl çoğaltma listesi
Service Fabric hizmet bölümünün çoğaltmaları hakkındaki bilgileri alır.

Getçoğaltmalar uç noktası, belirtilen bölümün çoğaltmaları hakkında bilgi döndürür. Yanıt, KIMLIK, rol, durum, durum, düğüm adı, çalışma süresi ve çoğaltma hakkında diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Partition-id [gerekli] | Bölümün kimliği. |
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

## <a name="sfctl-replica-remove"></a>sfctl Çoğaltma kaldırma
Düğüm üzerinde çalışan bir hizmet çoğaltmasını kaldırır.

Bu API, bir çoğaltmayı Service Fabric kümeden kaldırarak Service Fabric çoğaltma hatasına benzetir. Kaldırma işlemi çoğaltmayı kapatır, çoğaltmayı None rolüne geçirir ve sonra çoğaltmanın tüm durum bilgilerini kümeden kaldırır. Bu API, çoğaltma durumu kaldırma yolunu sınar ve istemci API 'Leri aracılığıyla rapor hatası kalıcı yolunu benzetir. Uyarı-Bu API kullanıldığında gerçekleştirilen bir güvenlik denetimi yoktur. Bu API 'nin yanlış kullanımı, durum bilgisi olan hizmetler için veri kaybına neden olabilir. Buna ek olarak, forceRemove bayrağı aynı işlemde barındırılan tüm diğer çoğaltmaları etkiler.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çoğaltma-kimliği [gerekli] | Çoğaltmanın tanımlayıcısı. |
| --Zorla-Kaldır | Düzgün kapanma sırasından çıkmadan Service Fabric uygulamayı veya hizmeti zorla kaldırın. Bu parametre, çoğaltmaları düzgün bir şekilde kapatmayı önleyen hizmet kodundaki sorunlar nedeniyle, silme işlemi zaman aşımına uğramış bir uygulamayı veya hizmeti zorla silmek için kullanılabilir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-replica-report-health"></a>sfctl çoğaltma raporu-sistem durumu
Service Fabric çoğaltmasında bir sistem durumu raporu gönderir.

Belirtilen Service Fabric çoğaltmasının sistem durumunu raporlar. Rapor, sistem durumu raporunun ve rapor alındığı özelliğin kaynağı hakkında bilgi içermelidir. Rapor, sistem durumu deposuna ileten bir Service Fabric ağ geçidi çoğaltmasına gönderilir. Rapor, ağ geçidi tarafından kabul edilebilir, ancak ek doğrulamadan sonra sistem durumu deposu tarafından reddedildi. Örneğin, eski bir sıra numarası gibi geçersiz bir parametre nedeniyle sistem durumu deposu raporu reddedebilir. Raporun sistem durumu deposuna uygulanıp uygulanmadığını görmek için, çoğaltma durumunu Al ' ı çalıştırın ve raporun HealthEvents bölümünde görünüp görüntülenmediğini denetleyin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Health-Property [gerekli] | Sistem durumu bilgilerinin özelliği. <br><br> Bir varlık, farklı özellikler için sistem durumu raporlarına sahip olabilir. Özelliği, rapor tetikleyen durum koşulunu kategorilere ayırmak için Raporlayıcı esnekliğine izin veren sabit bir sabit listesi olmayan bir dizedir. Örneğin, SourceId "Localizçi" olan bir Raporlayıcı, bir düğümdeki kullanılabilir diskin durumunu izleyebilir, bu nedenle söz konusu düğümdeki "AvailableDisk" özelliğini bildirebilirler. Aynı Reporter, düğüm bağlantısını izleyip aynı düğümde bir özelliği "bağlantı" bildirebilirler. Sistem durumu deposunda, bu raporlar belirtilen düğüm için ayrı sistem durumu olayları olarak kabul edilir. SourceID ile birlikte, özelliği sistem durumu bilgilerini benzersiz şekilde tanımlar. |
| --sistem durumu-durumu [gerekli] | Olası değerler arasında\: ' geçersiz ', ' Tamam ', ' uyarı ', ' Error ', ' Unknown ' verilebilir. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çoğaltma-kimliği [gerekli] | Bölümün kimliği. |
| --kaynak-kimliği [gerekli] | Sistem durumu bilgilerini oluşturan istemci/izleme/sistem bileşenini tanımlayan kaynak adı. |
| --Açıklama | Sistem durumu bilgilerinin açıklaması. <br><br> Bu, raporla ilgili okunabilir bilgileri eklemek için kullanılan serbest metni temsil eder. Açıklama için en fazla dize uzunluğu 4096 karakterdir. Belirtilen dize daha uzunsa otomatik olarak kesilir. Kesilmişse, açıklamanın son karakterleri "[kesildi]" işaretçisini ve toplam dize boyutu 4096 karakterdir. İşaretin varlığı, kesilmesinin gerçekleştiği kullanıcılara işaret eden anlamına gelir. Kesilmişse, açıklamanın özgün dizeden 4096 karakterden daha az olduğunu unutmayın. |
| --hemen | Raporun hemen gönderilmesi gerekip gerekmediğini belirten bir bayrak. <br><br> Bir sistem durumu raporu, sistem durumu deposuna ileten bir Service Fabric ağ geçidi uygulamasına gönderilir. Immediate değeri true olarak ayarlanırsa, HTTP ağ geçidi uygulamasının kullandığı doku istemci ayarlarından bağımsız olarak, rapor HTTP ağ geçidinden sistem durumu deposuna anında gönderilir. Bu, mümkün olan en kısa sürede gönderilmesi gereken kritik raporlar için kullanışlıdır. Zamanlama ve diğer koşullara bağlı olarak, raporun gönderilmesi yine de başarısız olabilir, örneğin, HTTP ağ geçidi kapalıysa veya ileti ağ geçidine ulaşmazsa. Anında false değeri ayarlandıysa, rapor HTTP ağ geçidindeki sistem durumu istemci ayarlarına bağlı olarak gönderilir. Bu nedenle, Healthreportsendınterval yapılandırmasına göre toplu hale gelir. Durum istemcisinin sistem durumu raporlama iletilerini sistem durumu rapor işleme ile en iyi hale getirmesine izin verdiğinden, bu önerilen ayardır. Varsayılan olarak, raporlar anında gönderilmez. |
| ----süre sonu | Raporun süresi dolmuşsa sistem durumu deposundan kaldırılıp kaldırılmadığını belirten değer. <br><br> True olarak ayarlanırsa, rapor süresi dolduktan sonra sistem durumu deposundan kaldırılır. False olarak ayarlanırsa rapor, süre dolduğunda hata olarak değerlendirilir. Bu özelliğin değeri varsayılan olarak false 'tur. İstemciler düzenli olarak rapor edildiğinde Removewhenererererfalse (varsayılan) olarak ayarlanmalıdır. Bu şekilde, Raporlayıcı sorunları (örn. kilitlenme) ve raporleyemiyorum, sistem durumu raporunun süresi dolarsa varlık hata olarak değerlendirilir. Bu, varlığı hata sağlık durumunda olduğu şekilde işaretler. |
| --sıra-sayısı | Bu sistem durumu raporunun sayısal bir dize olarak sıra numarası. <br><br> Rapor sıra numarası, eski raporları algılamak için sistem durumu deposu tarafından kullanılır. Belirtilmemişse, bir rapor eklendiğinde sistem durumu istemcisi tarafından otomatik olarak bir sıra numarası oluşturulur. |
| --hizmet-tür | Sistem durumunun bildirildiği hizmet çoğaltması türü (durum bilgisiz veya durum bilgisi olmayan). ' Durumsuz ', ' Stateful '\: olası değerler aşağıda verilmiştir.  Durum bilgisi olan varsayılan\:. |
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

## <a name="sfctl-replica-restart"></a>sfctl çoğaltma yeniden başlatması
Bir düğümde çalışan kalıcı bir hizmetin hizmet çoğaltmasını yeniden başlatır.

Bir düğümde çalışan kalıcı bir hizmetin hizmet çoğaltmasını yeniden başlatır. Uyarı-Bu API kullanıldığında gerçekleştirilen bir güvenlik denetimi yoktur. Bu API 'nin yanlış kullanımı, durum bilgisi olan hizmetler için kullanılabilirlik kaybına neden olabilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --Partition-id [gerekli] | Bölümün kimliği. |
| --çoğaltma-kimliği [gerekli] | Çoğaltmanın tanımlayıcısı. |
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
