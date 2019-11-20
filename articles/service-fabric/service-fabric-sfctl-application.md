---
title: Azure Service Fabric CLı-sfctl uygulaması | Microsoft Docs
description: Service Fabric CLı sfctl uygulama komutlarını açıklar.
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
ms.openlocfilehash: 163faaab8fa2503458503d9f2b72d27a3e5856f0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901279"
---
# <a name="sfctl-application"></a>sfctl application
Uygulamalar ve uygulama türleri oluşturun, silin ve yönetin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| oluşturmaya | Belirtilen açıklamayı kullanarak bir Service Fabric uygulaması oluşturur. |
| delete | Mevcut bir Service Fabric uygulamasını siler. |
| dağıtılan | Service Fabric düğümünde dağıtılan bir uygulamayla ilgili bilgileri alır. |
| dağıtılan-sistem durumu | Service Fabric düğümünde dağıtılan bir uygulamanın sistem durumu hakkındaki bilgileri alır. |
| dağıtılan-liste | Bir Service Fabric düğümünde dağıtılan uygulamaların listesini alır. |
| sağlık | Service Fabric uygulamasının sistem durumunu alır. |
| Bilgisine | Bir Service Fabric uygulaması hakkında bilgi alır. |
| list | Service Fabric kümesinde oluşturulan, belirtilen filtrelerle eşleşen uygulamaların listesini alır. |
| yükleme | Service Fabric bir uygulamayla ilgili yükleme bilgilerini alır. |
| Bildirim | Uygulama türünü tanımlayan bildirimi alır. |
| sağlamaya | Dış depodaki '. sfpkg ' paketini veya görüntü deposundaki uygulama paketini kullanarak kümeyle Service Fabric bir uygulama türü sağlar veya kaydeder. |
| rapor-sistem durumu | Service Fabric uygulamasına bir sistem durumu raporu gönderir. |
| type | Service Fabric kümesinde belirtilen adı tam olarak eşleşen uygulama türlerinin listesini alır. |
| tür listesi | Service Fabric kümesindeki uygulama türlerinin listesini alır. |
| kaldırmak | Service Fabric bir uygulama türünü kümeden kaldırır veya kaydını siler. |
| yükseltmenizi | Service Fabric kümesinde bir uygulamanın yükseltilmesine başlar. |
| yükseltme-özgeçmişi | Service Fabric kümesindeki bir uygulamayı yükseltmeyi sürdürür. |
| yükseltme-geri alma | Service Fabric kümesindeki bir uygulamanın Şu anda yükseltmesini geri almaya başlar. |
| yükseltme-durum | Bu uygulama üzerinde gerçekleştirilen en son yükseltmenin ayrıntılarını alır. |
| karşıya yükle | Service Fabric uygulama paketini görüntü deposuna kopyalayın. |

## <a name="sfctl-application-create"></a>sfctl uygulama oluştur
Belirtilen açıklamayı kullanarak bir Service Fabric uygulaması oluşturur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --App-Name [gerekli] | ' Fabric\:' URI şeması da dahil olmak üzere uygulamanın adı. |
| --App-Type [gerekli] | Uygulama bildiriminde bulunan uygulama türü adı. |
| --App-version [gerekli] | Uygulama bildiriminde tanımlanan şekilde uygulama türü sürümü. |
| --Max-node-Count | Service Fabric bu uygulama için kapasiteyi ayıracağı en fazla düğüm sayısı. Bunun, bu uygulamanın hizmetlerinin tüm düğümlere yerleştirileceği anlamına gelmez. |
| --ölçümler | Bir JSON kodlamalı uygulama kapasitesi ölçüm açıklamaları listesi. Bir ölçüm, uygulamanın bulunduğu her düğüm için bir kapasite kümesiyle ilişkili bir ad olarak tanımlanır. |
| --Min-node-Count | Service Fabric bu uygulama için kapasiteyi ayıracağı en az düğüm sayısı. Bunun, bu uygulamanın hizmetlerinin tüm düğümlere yerleştirileceği anlamına gelmez. |
| --Parametreler | Uygulama için bir JSON kodlamalı uygulama parametresi, uygulamayı oluştururken uygulanacak geçersiz kılmalar listesi. |
| --timeout-t | Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-delete"></a>sfctl uygulaması silme
Mevcut bir Service Fabric uygulamasını siler.

Bir uygulamanın silinebilmesi için önce oluşturulması gerekir. Bir uygulamayı silmek, uygulamanın parçası olan tüm hizmetleri siler. Varsayılan olarak, Service Fabric hizmet çoğaltmalarını düzgün bir şekilde kapatmaya çalışır ve ardından hizmeti silmez. Ancak, bir hizmet çoğaltmayı düzgün bir şekilde kapatmada sorun yaşadığı takdirde, silme işlemi uzun zaman alabilir veya takılmış olabilir. Düzgün kapatma sırasını atlamak ve uygulamayı ve tüm hizmetlerini zorla silmek için isteğe bağlı ForceRemove bayrağını kullanın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
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

## <a name="sfctl-application-deployed"></a>sfctl uygulaması dağıtıldı
Service Fabric düğümünde dağıtılan bir uygulamayla ilgili bilgileri alır.

Bu sorgu, belirtilen uygulama KIMLIĞI sistem uygulaması için ise sistem uygulama bilgilerini döndürür. Sonuçlar, Dağıtılmış uygulamaların etkin, etkinleştiriliyor ve karşıdan yükleme durumlarında dağılması. Bu sorgu, düğüm adının kümedeki bir düğüme karşılık gelmesini gerektirir. Girilen düğüm adı kümedeki etkin Service Fabric düğümlerine işaret etmez sorgu başarısız olur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --include-sağlık-State | Bir varlığın sistem durumunu ekleyin. Bu parametre false ise veya belirtilmemişse, döndürülen sistem durumu "bilinmiyor" şeklindedir. True olarak ayarlandığında, sorgu, sonuçlar birleştirilmeden önce düğüme ve sistem sağlığı Sistem hizmetine paralel olarak geçer. Sonuç olarak, sorgu daha pahalı olur ve uzun zaman alabilir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-deployed-health"></a>sfctl uygulaması dağıtıldı-sistem durumu
Service Fabric düğümünde dağıtılan bir uygulamanın sistem durumu hakkındaki bilgileri alır.

Service Fabric düğümünde dağıtılan bir uygulamanın sistem durumu hakkındaki bilgileri alır. Sistem durumuna bağlı olarak dağıtılan uygulamada raporlanan HealthEvent nesnelerinin toplanmasını sağlamak için EventsHealthStateFilter ' i kullanın. DeployedServicePackagesHealthStateFilter komutunu, isteğe bağlı olarak DeployedServicePackageHealth alt sistem durumuna göre filtrelemek için kullanın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --dağıtılan-hizmet-paketler-sistem durumu-durum-filtre | Dağıtılan uygulama durumu sorgusunun sonucunda döndürülen dağıtılmış hizmet paketi sistem durumu nesnelerinin sistem durumlarına göre filtrelemesine izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen dağıtılmış hizmet paketleri döndürülür. Dağıtılan tüm hizmet paketleri dağıtılan uygulamanın toplanmış sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri OK (2) ve uyarı (4) olan hizmet paketlerinin sistem durumu döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --Events-Sistem durumu-durum-filtre | Sistem durumu temelinde döndürülen HealthEvent nesnelerinin toplanmasını filtrelemeye izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen olaylar döndürülür. Tüm olaylar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri (2) ve uyarı (4) olan tüm olaylar döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --exclude-sistem durumu-istatistikler | Durum istatistiklerinin Sorgu sonucunun bir parçası olarak döndürülüp döndürülmeyeceğini gösterir. Varsayılan olarak false. İstatistikler sistem durumu Tamam, uyarı ve hata içindeki alt öğe varlıklarının sayısını gösterir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-deployed-list"></a>sfctl uygulaması dağıtıldı-liste
Bir Service Fabric düğümünde dağıtılan uygulamaların listesini alır.

Bir Service Fabric düğümünde dağıtılan uygulamaların listesini alır. Sonuçlar, KIMLIĞE göre açıkça sorgulanmadığı takdirde dağıtılan sistem uygulamalarıyla ilgili bilgiler içermez. Sonuçlar, Dağıtılmış uygulamaların etkin, etkinleştiriliyor ve karşıdan yükleme durumlarında dağılması. Bu sorgu, düğüm adının kümedeki bir düğüme karşılık gelmesini gerektirir. Girilen düğüm adı kümedeki etkin Service Fabric düğümlerine işaret etmez sorgu başarısız olur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Node-Name [gerekli] | Düğümün adı. |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --include-sağlık-State | Bir varlığın sistem durumunu ekleyin. Bu parametre false ise veya belirtilmemişse, döndürülen sistem durumu "bilinmiyor" şeklindedir. True olarak ayarlandığında, sorgu, sonuçlar birleştirilmeden önce düğüme ve sistem sağlığı Sistem hizmetine paralel olarak geçer. Sonuç olarak, sorgu daha pahalı olur ve uzun zaman alabilir. |
| --en fazla sonuç | Disk belleği sorgularının bir parçası olarak döndürülecek en fazla sonuç sayısı. Bu parametre döndürülen sonuç sayısı üzerinde üst sınırı tanımlar. Döndürülen sonuçlar, yapılandırmada tanımlanan en fazla ileti boyutu kısıtlamalarına göre iletiye sığmıyor durumunda belirtilen en fazla sonuçtan daha az olabilir. Bu parametre sıfırsa veya belirtilmemişse, disk belleğine alınan sorgu, dönüş iletisine sığan mümkün olduğunca fazla sonuç içerir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-health"></a>sfctl uygulama durumu
Service Fabric uygulamasının sistem durumunu alır.

Service Fabric uygulamasının yığın durumunu döndürür. Yanıt, Tamam, hata ya da Uyarı sistem durumunu bildirir. Varlık sistem durumu deposunda bulunamazsa, hata döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --dağıtılan-uygulamalar-sistem durumu-durum-filtre | Uygulama durumu sorgusunun sonucunda döndürülen dağıtılmış uygulamalar sistem durumu nesnelerinin sistem durumlarına göre filtrelemesine izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen dağıtılan uygulamalar döndürülür. Dağıtılan tüm uygulamalar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri OK (2) ve uyarı (4) olan dağıtılan uygulamaların sistem durumu döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --Events-Sistem durumu-durum-filtre | Sistem durumu temelinde döndürülen HealthEvent nesnelerinin toplanmasını filtrelemeye izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen olaylar döndürülür. Tüm olaylar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri (2) ve uyarı (4) olan tüm olaylar döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --exclude-sistem durumu-istatistikler | Durum istatistiklerinin Sorgu sonucunun bir parçası olarak döndürülüp döndürülmeyeceğini gösterir. Varsayılan olarak false. İstatistikler sistem durumu Tamam, uyarı ve hata içindeki alt öğe varlıklarının sayısını gösterir. |
| --hizmetler-sistem durumu-durum-filtre | Hizmetler sistem durumu sorgusunun sonucunda döndürülen hizmet sistem durumu nesnelerinin sistem durumlarına göre filtrelemesine izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen hizmetler döndürülür. Tüm hizmetler, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri Tamam (2) ve uyarı (4) olan hizmetlerin sistem durumu döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-info"></a>sfctl uygulama bilgileri
Bir Service Fabric uygulaması hakkında bilgi alır.

Service Fabric kümesinde oluşturulma sürecinde veya, adı parametresi olarak belirtilen adla eşleşen uygulama hakkındaki bilgileri döndürür. Yanıt, uygulama hakkındaki adı, türü, durumu, parametreleri ve diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --exclude-uygulama-parametreler | Uygulama parametrelerinin sonuçtan dışlanıp dışlanmayacağını belirten bayrak. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-list"></a>sfctl uygulama listesi
Service Fabric kümesinde oluşturulan, belirtilen filtrelerle eşleşen uygulamaların listesini alır.

Service Fabric kümesinde oluşturulma ve belirtilen filtrelerle eşleşme sürecinde oluşturulan uygulamalar hakkındaki bilgileri alır. Yanıt, uygulama hakkındaki adı, türü, durumu, parametreleri ve diğer ayrıntıları içerir. Uygulamalar bir sayfaya uygun değilse, bir sonuç sayfası ve bir sonraki sayfayı almak için kullanılabilecek bir devamlılık belirteci döndürülür. Aynı anda ApplicationTypeName ve ApplicationDefinition, ' Filter ' filtre belirtilemez.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --uygulama-tanım-tür-filtre | Service Fabric uygulamasını tanımlamak için kullanılan mekanizma olan ApplicationDefinitionKind 'a filtre uygulamak için kullanılır.  <br> -Varsayılan değeri, "tümü" seçeneğini belirleyerek aynı işlevi gerçekleştirir. Değer 0 ' dır.  <br> -Herhangi bir ApplicationDefinitionKind değeri ile girişle eşleşen All filtresi. Değer 65535 ' dir.  <br> -ServiceFabricApplicationDescription-ApplicationDefinitionKind değeri ServiceFabricApplicationDescription ile girişle eşleşen filtre. Değer 1 ' dir.  <br> -Compose-Input, ApplicationDefinitionKind değer Compose ile eşleşen filtre. Değer 2 ' dir. |
| --Uygulama-türü-adı | Sorgulanacak uygulamaları filtrelemek için kullanılan uygulama türü adı. Bu değer uygulama türü sürümünü içermemelidir. |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --exclude-uygulama-parametreler | Uygulama parametrelerinin sonuçtan dışlanıp dışlanmayacağını belirten bayrak. |
| --en fazla sonuç | Disk belleği sorgularının bir parçası olarak döndürülecek en fazla sonuç sayısı. Bu parametre döndürülen sonuç sayısı üzerinde üst sınırı tanımlar. Döndürülen sonuçlar, yapılandırmada tanımlanan en fazla ileti boyutu kısıtlamalarına göre iletiye sığmıyor durumunda belirtilen en fazla sonuçtan daha az olabilir. Bu parametre sıfırsa veya belirtilmemişse, disk belleğine alınan sorgu, dönüş iletisine sığan mümkün olduğunca fazla sonuç içerir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-load"></a>sfctl uygulama yükü
Service Fabric bir uygulamayla ilgili yükleme bilgilerini alır.

Oluşturulan uygulamayla ilgili yükleme bilgisini veya Service Fabric kümesinde oluşturulma işlemini ve adı parametresi olarak belirtilen adla eşleşen yükleme bilgilerini döndürür. Yanıt; adı, minimum düğümleri, maksimum düğümleri, uygulamanın Şu anda işgal eden düğümlerin sayısını ve uygulama hakkında uygulama yük ölçüm bilgilerini içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-manifest"></a>sfctl uygulama bildirimi
Uygulama türünü tanımlayan bildirimi alır.

Yanıt, uygulama bildirimi XML 'sini bir dize olarak içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama-türü-adı [gerekli] | Uygulama türünün adı. |
| --Uygulama-türü-sürüm [gerekli] | Uygulama türünün sürümü. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-provision"></a>sfctl uygulama sağlama
Dış depodaki '. sfpkg ' paketini veya görüntü deposundaki uygulama paketini kullanarak kümeyle Service Fabric bir uygulama türü sağlar veya kaydeder.

Kümeyle birlikte Service Fabric bir uygulama türü sağlar. Yeni uygulamaların örneklendirilmesinden önce sağlama gereklidir. Sağlama işlemi, Relativepathınımafıtemte tarafından belirtilen uygulama paketinde ya da External '. sfpkg ' URI 'SI kullanılarak gerçekleştirilebilir. --Dış sağlama ayarlı değilse, bu komut görüntü deposu sağlamasını bekler.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --uygulama-paket-indir-URI | Uygulama paketinin HTTP veya HTTPS protokolleri kullanılarak indirilebileceği '. sfpkg ' uygulama paketinin yolu. <br><br> Sağlama türü yalnızca dış depo için. Uygulama paketi, dosyayı indirmek için Al işlemini sağlayan bir dış depoda depolanabilir. Desteklenen protokoller HTTP ve HTTPS ve yolun okuma erişimine izin vermelidir. |
| --Application-Type-Build-Path | Yalnızca sağlama türü görüntü deposu için. Önceki karşıya yükleme işlemi sırasında belirtilen görüntü deposundaki uygulama paketi için göreli yol. |
| --Uygulama-türü-adı | Sağlama türü yalnızca dış depo için. Uygulama türü adı, uygulama bildiriminde bulunan uygulama türünün adını temsil eder. |
| --Uygulama-türü-sürümü | Sağlama türü yalnızca dış depo için. Uygulama türü sürümü uygulama bildiriminde bulunan uygulama türünün sürümünü temsil eder. |
| --Dış sağlama | Uygulama paketinin kaydedilebileceği veya sağlanacağı konum. Sağlamanın, daha önce bir dış depoya yüklenmiş bir uygulama paketi için olduğunu gösterir. Uygulama paketi *. sfpkg uzantısıyla biter. |
| --No-wait | Sağlama işleminin zaman uyumsuz olarak yapılıp yapılmayacağını belirtir. <br><br> True olarak ayarlandığında, istek sistem tarafından kabul edildiğinde sağlama işlemi döndürülür ve sağlama işlemi herhangi bir zaman aşımı sınırı olmadan devam eder. Varsayılan değer false 'dur. Büyük uygulama paketleri için değeri true olarak ayarlamayı öneririz. |
| --timeout-t | Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-report-health"></a>sfctl uygulama raporu-sistem durumu
Service Fabric uygulamasına bir sistem durumu raporu gönderir.

Belirtilen Service Fabric uygulamasının sistem durumunu raporlar. Rapor, sistem durumu raporunun ve rapor alındığı özelliğin kaynağı hakkında bilgi içermelidir. Rapor, sistem durumu deposuna ileten bir Service Fabric ağ geçidi uygulamasına gönderilir. Rapor, ağ geçidi tarafından kabul edilebilir, ancak ek doğrulamadan sonra sistem durumu deposu tarafından reddedildi. Örneğin, eski bir sıra numarası gibi geçersiz bir parametre nedeniyle sistem durumu deposu raporu reddedebilir. Raporun sistem durumu deposuna uygulanıp uygulanmadığını görmek için, uygulama durumunu alın ve raporun göründüğünü denetleyin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. <br><br> Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. Sürüm 6,0 ' den başlayarak, hiyerarşik adlar ' \~ ' karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı ' Fabric\:/MyApp/APP1 ' ise, uygulama kimliği önceki sürümlerde 6.0 + ve ' MyApp/APP1 ' içinde ' MyApp\~APP1 ' olur. |
| --Health-Property [gerekli] | Sistem durumu bilgilerinin özelliği. <br><br> Bir varlık, farklı özellikler için sistem durumu raporlarına sahip olabilir. Özelliği, rapor tetikleyen durum koşulunu kategorilere ayırmak için Raporlayıcı esnekliğine izin veren sabit bir sabit listesi olmayan bir dizedir. Örneğin, SourceId "Localizçi" olan bir Raporlayıcı, bir düğümdeki kullanılabilir diskin durumunu izleyebilir, bu nedenle söz konusu düğümdeki "AvailableDisk" özelliğini bildirebilirler. Aynı Reporter, düğüm bağlantısını izleyip aynı düğümde bir özelliği "bağlantı" bildirebilirler. Sistem durumu deposunda, bu raporlar belirtilen düğüm için ayrı sistem durumu olayları olarak kabul edilir. SourceID ile birlikte, özelliği sistem durumu bilgilerini benzersiz şekilde tanımlar. |
| --sistem durumu-durumu [gerekli] | Olası değerler arasında\: ' geçersiz ', ' Tamam ', ' uyarı ', ' Error ', ' Unknown ' verilebilir. |
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

## <a name="sfctl-application-type"></a>sfctl uygulama türü
Service Fabric kümesinde belirtilen adı tam olarak eşleşen uygulama türlerinin listesini alır.

Service Fabric kümesinde sağlanmakta olan uygulama türleriyle ilgili bilgileri döndürür. Bu sonuçlar, adı tam olarak parametresi olarak belirtilen ve belirtilen sorgu parametreleriyle uyumlu olan uygulama türleridir. Uygulama türü adıyla eşleşen uygulama türünün tüm sürümleri, her sürüm tek bir uygulama türü olarak döndürüldüğünden döndürülür. Yanıt, uygulama türü ile ilgili ad, sürüm, durum ve diğer ayrıntıları içerir. Bu, tüm uygulama türlerinin bir sayfaya sığması, bir sonuç sayfasının ve bir sonraki sayfayı almak için kullanılabilecek bir devamlılık belirtecinin döndürülmeyeceği anlamına gelen, disk belleğine alınmış bir sorgudur. Örneğin, 10 uygulama türü varsa ancak bir sayfa yalnızca ilk üç uygulama türüne sığıyorsa veya en fazla sonuç 3 olarak ayarlandıysa, üç değeri döndürülür. Sonuçların geri kalanına erişmek için sonraki sorguda döndürülen devamlılık belirtecini kullanarak sonraki sayfaları alın. Sonraki sayfa yoksa boş bir devamlılık belirteci döndürülür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama-türü-adı [gerekli] | Uygulama türünün adı. |
| --Uygulama-türü-sürümü | Uygulama türünün sürümü. |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --exclude-uygulama-parametreler | Uygulama parametrelerinin sonuçtan dışlanıp dışlanmayacağını belirten bayrak. |
| --en fazla sonuç | Disk belleği sorgularının bir parçası olarak döndürülecek en fazla sonuç sayısı. Bu parametre döndürülen sonuç sayısı üzerinde üst sınırı tanımlar. Döndürülen sonuçlar, yapılandırmada tanımlanan en fazla ileti boyutu kısıtlamalarına göre iletiye sığmıyor durumunda belirtilen en fazla sonuçtan daha az olabilir. Bu parametre sıfırsa veya belirtilmemişse, disk belleğine alınan sorgu, dönüş iletisine sığan mümkün olduğunca fazla sonuç içerir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-type-list"></a>sfctl uygulama türü-listesi
Service Fabric kümesindeki uygulama türlerinin listesini alır.

Service Fabric kümesinde sağlanmakta olan uygulama türleriyle ilgili bilgileri döndürür. Uygulama türünün her sürümü bir uygulama türü olarak döndürülür. Yanıt, uygulama türü ile ilgili ad, sürüm, durum ve diğer ayrıntıları içerir. Bu, tüm uygulama türlerinin bir sayfaya sığması, bir sonuç sayfasının ve bir sonraki sayfayı almak için kullanılabilecek bir devamlılık belirtecinin döndürülmeyeceği anlamına gelen, disk belleğine alınmış bir sorgudur. Örneğin, 10 uygulama türü varsa ancak bir sayfa yalnızca ilk üç uygulama türüne sığıyorsa veya en fazla sonuç 3 olarak ayarlandıysa, üç değeri döndürülür. Sonuçların geri kalanına erişmek için sonraki sorguda döndürülen devamlılık belirtecini kullanarak sonraki sayfaları alın. Sonraki sayfa yoksa boş bir devamlılık belirteci döndürülür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --uygulama-tür tanımı-tür-filtre | Service Fabric uygulama türünü tanımlamak için kullanılan mekanizma olan ApplicationTypeDefinitionKind filtrelemek için kullanılır.  <br> -Varsayılan değeri, "tümü" seçeneğini belirleyerek aynı işlevi gerçekleştirir. Değer 0 ' dır.  <br> -All-FILTER, herhangi bir ApplicationTypeDefinitionKind değeriyle eşleşir. Değer 65535 ' dir.  <br> -ServiceFabricApplicationPackage-ApplicationTypeDefinitionKind değeri ServiceFabricApplicationPackage ile girişle eşleşen filtre. Değer 1 ' dir.  <br> -Compose-Input, ApplicationTypeDefinitionKind değer Compose ile eşleşen bir filtre. Değer 2 ' dir. |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --exclude-uygulama-parametreler | Uygulama parametrelerinin sonuçtan dışlanıp dışlanmayacağını belirten bayrak. |
| --en fazla sonuç | Disk belleği sorgularının bir parçası olarak döndürülecek en fazla sonuç sayısı. Bu parametre döndürülen sonuç sayısı üzerinde üst sınırı tanımlar. Döndürülen sonuçlar, yapılandırmada tanımlanan en fazla ileti boyutu kısıtlamalarına göre iletiye sığmıyor durumunda belirtilen en fazla sonuçtan daha az olabilir. Bu parametre sıfırsa veya belirtilmemişse, disk belleğine alınan sorgu, dönüş iletisine sığan mümkün olduğunca fazla sonuç içerir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-unprovision"></a>sfctl uygulama sağlamayı kaldırma
Service Fabric bir uygulama türünü kümeden kaldırır veya kaydını siler.

Bu işlem, yalnızca uygulama türünün tüm uygulama örnekleri silinmişse gerçekleştirilebilir. Uygulama türü kaydedildikten sonra, bu belirli uygulama türü için yeni uygulama örnekleri oluşturulamaz.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama-türü-adı [gerekli] | Uygulama türünün adı. |
| --Uygulama-türü-sürüm [gerekli] | Uygulama bildiriminde tanımlanan şekilde uygulama türü sürümü. |
| --Async-Parameter | Sağlamayı kaldırma işleminin zaman uyumsuz olarak yapılıp yapılmayacağını belirten bayrak. True olarak ayarlandığında, sağlamayı kaldırma işlemi, istek sistem tarafından kabul edildiğinde döner ve sağlamayı kaldırma işlemi herhangi bir zaman aşımı sınırı olmadan devam eder. Varsayılan değer false 'dur. Ancak, sağlanan büyük uygulama paketleri için bunu true olarak ayarlamayı öneririz. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-upgrade"></a>sfctl uygulama yükseltmesi
Service Fabric kümesinde bir uygulamanın yükseltilmesine başlar.

Sağlanan uygulama yükseltme parametrelerini doğrular ve parametreler geçerliyse uygulamayı yükseltmeye başlar. Yükseltme açıklamasının varolan uygulama açıklamasını değiştirdiğine unutmayın. Bu, parametreler belirtilmemişse, uygulamalardaki mevcut parametrelerin boş parametreler listesi ile üzerine yazılır. Bu, uygulamanın uygulama bildiriminden parametrelerin varsayılan değerini kullanacağından oluşur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. <br><br> Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Uygulama-sürümü [gerekli] | Uygulama yükseltme için hedef uygulama türü sürümü (uygulama bildiriminde bulunur). |
| --parametreler [gerekli] | Uygulamanın yükseltilmesi sırasında uygulanacak bir JSON kodlamalı uygulama parametresi listesi. |
| --Varsayılan-hizmet-sistem durumu-ilke | Bir hizmet türünün sistem durumunu değerlendirmek için varsayılan olarak kullanılan sistem durumu ilkesinin JSON kodlu belirtimi. |
| --hata-eylem | Izlenen bir yükseltme, izleme ilkesi veya sistem durumu ilke ihlalleri ile karşılaştığında gerçekleştirilecek eylem. |
| --zorla-yeniden Başlat | Kod sürümü değiştirilmese bile yükseltme sırasında işlemi zorla yeniden başlatın. |
| --sistem durumu-denetim-yeniden deneme-zaman aşımı | Uygulama veya küme sağlıklı değilse sistem durumu denetimleri gerçekleştirme denemeleri arasındaki süre uzunluğu.  Varsayılan\: PT0H10M0S. |
| --sistem durumu-denetim kararlı-süre | Yükseltme bir sonraki yükseltme etki alanına geçmeden önce uygulamanın veya kümenin sağlıklı kalması gereken süre.  Varsayılan\: PT0H2M0S. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --sistem durumu-denetim bekleme süresi | Durum denetimleri işlemini başlatmadan önce bir yükseltme etki alanını tamamladıktan sonra beklenecek sürenin uzunluğu.  Varsayılan\: 0 ' dır. |
| --en fazla sağlıksız-uygulamalar | Sağlıksız olarak dağıtılan uygulamaların izin verilen en büyük yüzdesi. 0 ile 100 arasında bir sayı olarak temsil edilir. |
| --mod | Sıralı yükseltme sırasında sistem durumunu izlemek için kullanılan mod.  Varsayılan\: Monitortoredauto. |
| --Çoğaltma-ayarlama-denetim-zaman aşımı | Bir yükseltme etki alanının işlenmesi engellenecek en uzun süre (veya beklenmedik sorunlar olduğunda kullanılabilirlik kaybını önleme). Saniyeler içinde ölçülür. |
| --hizmet-sistem durumu-ilke | Hizmet türü adı başına hizmet türü sistem durumu ilkesiyle JSON kodlamalı eşleme. Harita varsayılan olarak boştur. |
| --timeout-t | Varsayılan\: 60. |
| --Upgrade-etki alanı zaman aşımı | Her yükseltme etki alanının, FailureAction yürütülmeden önce tamamlaması gereken süre miktarı.  Varsayılan\: P10675199DT02H48M 05.4775807 S. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --Upgrade-Timeout | FailureAction yürütülmeden önce genel yükseltmenin tamamlaması gereken süre miktarı.  Varsayılan\: P10675199DT02H48M 05.4775807 S. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --hata olarak uyarı | Uyarıların hatalarla aynı önem derecesine sahip olup olmadığını gösterir. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl uygulama yükseltme-özgeçmişi
Service Fabric kümesindeki bir uygulamayı yükseltmeyi sürdürür.

İzlenmeyen bir el ile uygulama yükseltmesini sürdürür Service Fabric. Service Fabric, tek seferde bir yükseltme etki alanını yükseltir. İzlenmeyen el ile yükseltmeler için Service Fabric bir yükseltme etki alanını tamamladıktan sonra, bir sonraki yükseltme etki alanına geçmeden önce bu API 'yi çağırmanız bekler.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Upgrade-etki alanı-adı [gerekli] | Yükseltmenin sürdürüleceği yükseltme etki alanının adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl uygulama yükseltmesi-geri alma
Service Fabric kümesindeki bir uygulamanın Şu anda yükseltmesini geri almaya başlar.

Önceki sürüme geçerli uygulama yükseltmesini geri almaya başlar. Bu API yalnızca yeni sürüme dönük geçerli devam eden yükseltmeyi geri almak için kullanılabilir. Uygulama Şu anda yükseltilmiyorsa, önceki bir sürüme geri dönme dahil olmak üzere, istenen sürüme yükseltmek için StartApplicationUpgrade API kullanın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-upgrade-status"></a>sfctl uygulama yükseltme-durum
Bu uygulama üzerinde gerçekleştirilen en son yükseltmenin ayrıntılarını alır.

Uygulamanın sistem durumu sorunlarını ayıklamaya yardımcı olacak ayrıntılarla birlikte en son uygulama yükseltmesinin durumuyla ilgili bilgileri döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-application-upload"></a>sfctl uygulamasını karşıya yükleme
Service Fabric uygulama paketini görüntü deposuna kopyalayın.

İsteğe bağlı olarak paketteki her dosya için karşıya yükleme ilerlemesini görüntüleyin. Karşıya yükleme ilerlemesi `stderr`gönderilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --yol [gerekli] | Yerel uygulama paketinin yolu. |
| --imagesdeposu-dize | Uygulama paketini yüklemek için hedef görüntü deposu.  Varsayılan\: yapı\:Imate. <br><br> Bir dosya konumuna yüklemek için bu parametreyi ' dosya\:' ile başlatın. Aksi takdirde değer, varsayılan değer gibi görüntü deposu bağlantı dizesi olmalıdır. |
| --ilerlemeyi göster | Büyük paketler için karşıya dosya yükleme ilerlemesini göster. |
| --timeout-t | Saniye cinsinden toplam zaman aşımı. Karşıya yükleme başarısız olur ve karşıya yükleme zaman aşımı süresi geçtikten sonra hata döndürür. Bu zaman aşımı tüm uygulama paketi için geçerlidir ve tek dosya zaman aşımları kalan zaman aşımı süresine eşit olur.  Varsayılan\: 300. |

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
