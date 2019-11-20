---
title: Azure Service Fabric CLı-sfctl düğümü | Microsoft Docs
description: Service Fabric CLı sfctl düğüm komutlarını açıklar.
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
ms.openlocfilehash: 29a66beb354edc96a9ece2bb37766dbde6a9fe49
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901096"
---
# <a name="sfctl-node"></a>sfctl node
Bir küme oluşturan düğümleri yönetin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| dıı | Service Fabric küme düğümünü belirtilen devre dışı bırakma amacına göre devre dışı bırakın. |
| etkinleştirebilir | Şu anda devre dışı bırakılmış bir Service Fabric küme düğümünü etkinleştirin. |
| sağlık | Service Fabric düğümünün sistem durumunu alır. |
| Bilgisine | Service Fabric kümesindeki belirli bir düğüm hakkındaki bilgileri alır. |
| list | Service Fabric kümesindeki düğümlerin listesini alır. |
| yükleme | Service Fabric düğümünün yükleme bilgilerini alır. |
| durumu Kaldır | Bir düğümdeki kalıcı durumun kalıcı olarak kaldırıldığını veya kaybediltiğini Service Fabric bildirir. |
| rapor-sistem durumu | Service Fabric düğümünde bir sistem durumu raporu gönderir. |
| restart | Service Fabric küme düğümünü yeniden başlatır. |
| geçiş | Bir küme düğümünü başlatır veya sonlandırır. |
| geçiş-durum | StartNodeTransition kullanılarak başlatılan bir işlemin ilerleme durumunu alır. |

## <a name="sfctl-node-disable"></a>sfctl düğümü devre dışı
Service Fabric küme düğümünü belirtilen devre dışı bırakma amacına göre devre dışı bırakın.

Service Fabric küme düğümünü belirtilen devre dışı bırakma amacına göre devre dışı bırakın. Devre dışı bırakma işlemi devam ederken, devre dışı bırakma amacı artırılabilir, ancak azalmayabilir (örneğin, duraklatma amacı ile devre dışı bırakılmış bir düğüm, yeniden başlatma ile daha fazla şekilde devre dışı bırakılabilir, ancak başka bir şekilde kullanılamaz. Düğüm devre dışı bırakıldıktan sonra düğümleri etkinleştir işlemi kullanılarak düğümler yeniden etkinleştirilebilir. Devre dışı bırakma işlemi tamamlanmazsa, devre dışı bırakma işlemi iptal edilir. Devre dışı bırakılan ve geri yüklenen bir düğüm, hizmetler söz konusu düğüme yerleştirilmesinden önce yeniden etkinleştirilmeleri gerekir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --devre dışı bırakma-amaç | Düğümü devre dışı bırakma amacını veya nedenini açıklar. Olası değerler aşağıda verilmiştir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-enable"></a>sfctl düğümü etkinleştir
Şu anda devre dışı bırakılmış bir Service Fabric küme düğümünü etkinleştirin.

Şu anda devre dışı bırakılmış bir Service Fabric küme düğümünü etkinleştirir. Etkinleştirildikten sonra, düğüm yeni çoğaltmaları yerleştirmek için uygun bir hedef olur ve düğümde kalan devre dışı bırakılmış çoğaltmalar yeniden etkinleştirilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-health"></a>sfctl düğüm durumu
Service Fabric düğümünün sistem durumunu alır.

Service Fabric düğümünün sistem durumunu alır. Düğümde bildirilen sistem durumu olaylarının koleksiyonunu sistem durumuna göre filtrelemek için EventsHealthStateFilter ' i kullanın. Ada göre belirttiğiniz düğüm sistem durumu deposunda yoksa, bu bir hata döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
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

## <a name="sfctl-node-info"></a>sfctl düğüm bilgisi
Service Fabric kümesindeki belirli bir düğüm hakkındaki bilgileri alır.

Yanıt, ad, durum, KIMLIK, sistem durumu, çalışma süresi ve düğüm hakkında diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-list"></a>sfctl düğüm listesi
Service Fabric kümesindeki düğümlerin listesini alır.

Yanıt, ad, durum, KIMLIK, sistem durumu, çalışma süresi ve düğümlerle ilgili diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --en fazla sonuç | Disk belleği sorgularının bir parçası olarak döndürülecek en fazla sonuç sayısı. Bu parametre döndürülen sonuç sayısı üzerinde üst sınırı tanımlar. Döndürülen sonuçlar, yapılandırmada tanımlanan en fazla ileti boyutu kısıtlamalarına göre iletiye sığmıyor durumunda belirtilen en fazla sonuçtan daha az olabilir. Bu parametre sıfırsa veya belirtilmemişse, disk belleğine alınan sorgu, dönüş iletisine sığan mümkün olduğunca fazla sonuç içerir. |
| --Node-durum-filtre | Düğüm durumuna göre düğümlerin filtrelemesine izin verir. Yalnızca belirtilen filtre değeriyle eşleşen düğümler döndürülür. Filtre değeri aşağıdakilerden biri olabilir.  Varsayılan\: varsayılan. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-load"></a>sfctl düğüm yükü
Service Fabric düğümünün yükleme bilgilerini alır.

Yük veya kapasite tanımlı tüm ölçümler için bir Service Fabric düğümünün yükleme bilgilerini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-remove-state"></a>sfctl düğüm kaldırma-durum
Bir düğümdeki kalıcı durumun kalıcı olarak kaldırıldığını veya kaybediltiğini Service Fabric bildirir.

Bu, düğümün kalıcı durumunu kurtarmanın mümkün olmadığı anlamına gelir. Bu genellikle bir sabit diskin temizlenmesi veya bir sabit diskin kilitlenmesi durumunda meydana gelir. Bu işlemin başarılı olması için düğüm aşağı doğru olmalıdır. Bu işlem, bu düğümdeki çoğaltmaların artık mevcut olmadığını ve bu çoğaltmaların geri dönmesi için Service Fabric durması gerektiğini Service Fabric sağlar. Düğümdeki durum kaldırılmadığı ve düğüm durumu bozulmadan geri dönebileceği takdirde bu cmdlet 'i çalıştırmayın. Service Fabric 6,5 ' den başlayarak çekirdek düğümleri için bu API 'yi kullanmak üzere, çekirdek düğümleri normal (çekirdek olmayan) düğümlere değiştirip düğüm durumunu kaldırmak için bu API 'yi çağırın. Küme Azure 'da çalışıyorsa, çekirdek düğüm kapatıldıktan sonra, Service Fabric otomatik olarak çekirdek olmayan bir düğümle değiştirmeye çalışacaktır. Bunu yapmak için, birincil düğüm türündeki çekirdek olmayan düğümlerin sayısının aşağı çekirdek düğümlerin sayısından küçük olmadığından emin olun. Gerekirse, bunu elde etmek için birincil düğüm türüne daha fazla düğüm ekleyin. Tek başına kümede, aşağı çekirdek düğümünün durumunun bozulmadan geri gelmesi beklenmiyorsa, lütfen düğümü kümeden kaldırın, bkz. https\://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-report-health"></a>sfctl düğüm raporu-sistem durumu
Service Fabric düğümünde bir sistem durumu raporu gönderir.

Belirtilen Service Fabric düğümünün sistem durumunu raporlar. Rapor, sistem durumu raporunun ve rapor alındığı özelliğin kaynağı hakkında bilgi içermelidir. Rapor, sistem durumu deposuna ileten bir Service Fabric ağ geçidi düğümüne gönderilir. Rapor, ağ geçidi tarafından kabul edilebilir, ancak ek doğrulamadan sonra sistem durumu deposu tarafından reddedildi. Örneğin, eski bir sıra numarası gibi geçersiz bir parametre nedeniyle sistem durumu deposu raporu reddedebilir. Raporun sistem durumu deposuna uygulanıp uygulanmadığını görmek için raporun HealthEvents bölümünde göründüğünü kontrol edin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Health-Property [gerekli] | Sistem durumu bilgilerinin özelliği. <br><br> Bir varlık, farklı özellikler için sistem durumu raporlarına sahip olabilir. Özelliği, rapor tetikleyen durum koşulunu kategorilere ayırmak için Raporlayıcı esnekliğine izin veren sabit bir sabit listesi olmayan bir dizedir. Örneğin, SourceId "Localizçi" olan bir Raporlayıcı, bir düğümdeki kullanılabilir diskin durumunu izleyebilir, bu nedenle söz konusu düğümdeki "AvailableDisk" özelliğini bildirebilirler. Aynı Reporter, düğüm bağlantısını izleyip aynı düğümde bir özelliği "bağlantı" bildirebilirler. Sistem durumu deposunda, bu raporlar belirtilen düğüm için ayrı sistem durumu olayları olarak kabul edilir. SourceID ile birlikte, özelliği sistem durumu bilgilerini benzersiz şekilde tanımlar. |
| --sistem durumu-durumu [gerekli] | Olası değerler arasında\: ' geçersiz ', ' Tamam ', ' uyarı ', ' Error ', ' Unknown ' verilebilir. |
| --Node-Name [gerekli] | Düğümün adı. |
| --kaynak-kimliği [gerekli] | Sistem durumu bilgilerini oluşturan istemci/izleme/sistem bileşenini tanımlayan kaynak adı. |
| --Açıklama | Sistem durumu bilgilerinin açıklaması. <br><br> Bu, raporla ilgili okunabilir bilgileri eklemek için kullanılan serbest metni temsil eder. Açıklama için en fazla dize uzunluğu 4096 karakterdir. Belirtilen dize daha uzunsa otomatik olarak kesilir. Kesilmişse, açıklamanın son karakterleri "[kesildi]" işaretçisini ve toplam dize boyutu 4096 karakterdir. İşaretin varlığı, kesilmesinin gerçekleştiği kullanıcılara işaret eden anlamına gelir. Kesilmişse, açıklamanın özgün dizeden 4096 karakterden daha az olduğunu unutmayın. |
| --hemen | Raporun hemen gönderilmesi gerekip gerekmediğini belirten bir bayrak. <br><br> Bir sistem durumu raporu, sistem durumu deposuna ileten bir Service Fabric ağ geçidi uygulamasına gönderilir. Immediate değeri true olarak ayarlanırsa, HTTP ağ geçidi uygulamasının kullandığı doku istemci ayarlarından bağımsız olarak, rapor HTTP ağ geçidinden sistem durumu deposuna anında gönderilir. Bu, mümkün olan en kısa sürede gönderilmesi gereken kritik raporlar için kullanışlıdır. Zamanlama ve diğer koşullara bağlı olarak, raporun gönderilmesi yine de başarısız olabilir, örneğin, HTTP ağ geçidi kapalıysa veya ileti ağ geçidine ulaşmazsa. Anında false değeri ayarlandıysa, rapor HTTP ağ geçidindeki sistem durumu istemci ayarlarına bağlı olarak gönderilir. Bu nedenle, Healthreportsendınterval yapılandırmasına göre toplu hale gelir. Durum istemcisinin sistem durumu raporlama iletilerini sistem durumu rapor işleme ile en iyi hale getirmesine izin verdiğinden, bu önerilen ayardır. Varsayılan olarak, raporlar anında gönderilmez. |
| ----süre sonu | Raporun süresi dolmuşsa sistem durumu deposundan kaldırılıp kaldırılmadığını belirten değer. <br><br> True olarak ayarlanırsa, rapor süresi dolduktan sonra sistem durumu deposundan kaldırılır. False olarak ayarlanırsa rapor, süre dolduğunda hata olarak değerlendirilir. Bu özelliğin değeri varsayılan olarak false 'tur. İstemciler düzenli olarak rapor edildiğinde Removewhenererererfalse (varsayılan) olarak ayarlanmalıdır. Bu şekilde, Raporlayıcı sorunları (örn. kilitlenme) ve raporleyemiyorum, sistem durumu raporunun süresi dolarsa varlık hata olarak değerlendirilir. Bu, varlığı hata sağlık durumunda olduğu şekilde işaretler. |
| --sıra-sayısı | Bu sistem durumu raporunun sayısal bir dize olarak sıra numarası. <br><br> Rapor sıra numarası, eski raporları algılamak için sistem durumu deposu tarafından kullanılır. Belirtilmemişse, bir rapor eklendiğinde sistem durumu istemcisi tarafından otomatik olarak bir sıra numarası oluşturulur. |
| --timeout-t | Varsayılan\: 60. |
| --TTL | Bu sistem durumu raporunun geçerli olduğu süre. Bu alan, süreyi belirtmek için ıSO8601 biçimini kullanır. <br><br> İstemciler düzenli olarak raporladığında, en yüksek sıklıkta raporlar göndermelidir. İstemciler geçişe rapor alıyorsa, zaman sonsuz ' a kadar canlı bir süre ayarlayabilir. Yaşam süresi dolduğunda, sistem durumu bilgilerini içeren sistem durumu olayı sistem durumu deposundan kaldırılır, Removewhenexpires true ise ya da Removewhenexpires false olduğunda hata durumunda değerlendirilir. Belirtilmemişse, yaşam süresi varsayılan olarak sonsuz değer olur. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-restart"></a>sfctl düğümünü yeniden başlatma
Service Fabric küme düğümünü yeniden başlatır.

Zaten başlatılmış bir Service Fabric küme düğümünü yeniden başlatır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --Create-Fabric-dump | Doku düğümü işleminin dökümünü oluşturmak için true değerini belirtin. Bu, büyük/küçük harfe duyarlıdır.  Varsayılan\: false. |
| --düğüm-örnek kimliği | Hedef düğümün örnek KIMLIĞI. Örnek KIMLIĞI belirtilmişse, düğüm yalnızca düğümün geçerli örneğiyle eşleşiyorsa yeniden başlatılır. "0" varsayılan değeri herhangi bir örnek KIMLIĞIYLE eşleşir. Örnek KIMLIĞI, düğüm al sorgusu kullanılarak elde edilebilir.  Varsayılan\: 0 ' dır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-transition"></a>sfctl düğüm geçişi
Bir küme düğümünü başlatır veya sonlandırır.

Bir küme düğümünü başlatır veya sonlandırır.  Küme düğümü, işletim sistemi örneğinin kendisi değil, bir işlemdir.  Bir düğümü başlatmak için Nodegeçişli Tiontype parametresi için "Start" geçirin. Bir düğümü durdurmak için Nodegeçişli Tiontype parametresi için "Durdur" geçirin. Bu API işlemi başlatır-API geri döndüğünde düğümü henüz geçirmeyi bitirmemiş olabilir. İşlemin ilerlemesini almak için aynı operationId ile Getnodeınlationprogress çağrısı yapın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Instance-id [gerekli] | Hedef düğümün düğüm örnek KIMLIĞI. Bu, Getnodeınfo API aracılığıyla belirlenebilir. |
| --Node-Name [gerekli] | Düğümün adı. |
| --Node-geçiş-tür [gerekli] | Gerçekleştirilecek geçişin türünü gösterir.  Nodegeçişli Tiontype. Start, durdurulmuş bir düğüm başlatacak. Nodegeçişli Tiontype. Stop, çalışır olan bir düğümü durdurur. |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --DURATION-saniye cinsinden [gerekli] | Düğümün durdurulduğu süre (saniye cinsinden).  En küçük değer 600, üst sınır 14400 ' dir.  Bu sürenin süresi dolduktan sonra, düğüm otomatik olarak geri alınacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-node-transition-status"></a>sfctl düğüm geçişi-durum
StartNodeTransition kullanılarak başlatılan bir işlemin ilerleme durumunu alır.

Belirtilen operationId kullanarak StartNodeTransition ile başlatılan bir işlemin ilerlemesini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
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