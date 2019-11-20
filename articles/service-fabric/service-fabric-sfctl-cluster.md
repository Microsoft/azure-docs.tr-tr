---
title: Azure Service Fabric CLı-sfctl kümesi | Microsoft Docs
description: Service Fabric CLı sfctl kümesi komutlarını açıklar.
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
ms.openlocfilehash: a42062f6f6b671d853f47e3f170b366799829a62
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901483"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Service Fabric kümelerini seçin, yönetin ve çalıştırın.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| kod sürümleri | Service Fabric kümesinde sağlanan doku kodu sürümlerinin bir listesini alır. |
| yapılandırma-sürümler | Service Fabric kümesinde sağlanan yapı yapılandırma sürümlerinin bir listesini alır. |
| sağlık | Service Fabric kümesinin sistem durumunu alır. |
| Bildirim | Service Fabric küme bildirimini alın. |
| işlem-iptal | Kullanıcı tarafından alınmış bir hata işlemini iptal eder. |
| işlem-liste | Belirtilen girişe göre filtrelenmiş Kullanıcı tarafından gerçekleştirilen hata işlemlerinin bir listesini alır. |
| sağlamaya | Service Fabric kümesinin kodunu veya yapılandırma paketlerini sağlayın. |
| kurtarma-sistem | Service Fabric kümesine, şu anda yetersayı kaybından takılı olan sistem hizmetlerini kurtarmaya çalışılması gerektiğini gösterir. |
| rapor-sistem durumu | Service Fabric kümesinde bir sistem durumu raporu gönderir. |
| seçin | Service Fabric küme uç noktasına bağlanır. |
| bağlantıyı göster | Bu sfctl örneğinin hangi Service Fabric kümesini bağlandığını gösterin. |
| kaldırmak | Service Fabric kümesinin kodunu veya yapılandırma paketlerini sağlamayı kaldırma. |
| yükseltmenizi | Service Fabric kümesinin kodunu veya yapılandırma sürümünü yükseltmeye başlayın. |
| yükseltme-özgeçmişi | Küme yükseltmesini bir sonraki yükseltme etki alanına geçiş yapın. |
| yükseltme-geri alma | Service Fabric kümesinin yükseltmesini geri alma. |
| yükseltme-durum | Geçerli küme yükseltmesinin ilerlemesini alır. |
| yükseltme-güncelleştirme | Service Fabric küme yükseltmesinin yükseltme parametrelerini güncelleştirin. |

## <a name="sfctl-cluster-code-versions"></a>sfctl küme kodu-sürümler
Service Fabric kümesinde sağlanan doku kodu sürümlerinin bir listesini alır.

Kümede sağlanan yapı kodu sürümleri hakkındaki bilgilerin listesini alır. CodeVersion parametresi, isteğe bağlı olarak çıktıyı yalnızca belirli bir sürüme filtrelemek için kullanılabilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Code-Version | Service Fabric ürün sürümü. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-config-versions"></a>sfctl küme yapılandırması-sürümler
Service Fabric kümesinde sağlanan yapı yapılandırma sürümlerinin bir listesini alır.

Kümede sağlanan Fabric yapılandırma sürümleri hakkındaki bilgilerin listesini alır. ConfigVersion parametresi, çıktıyı isteğe bağlı olarak yalnızca belirli bir sürüme filtrelemek için kullanılabilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --config-Version | Service Fabric yapılandırma sürümü. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-health"></a>sfctl küme durumu
Service Fabric kümesinin sistem durumunu alır.

Kümede bildirilen sistem durumu olaylarının toplanmasını sistem durumuna göre filtrelemek için EventsHealthStateFilter ' i kullanın. Benzer şekilde, toplanan sistem durumlarına göre döndürülen düğümlerin ve uygulamaların koleksiyonunu filtrelemek için NodesHealthStateFilter ve ApplicationsHealthStateFilter ' ı kullanın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --uygulamalar-sistem durumu-durum-filtre | Küme durumu sorgusunun sonucunda döndürülen uygulama sistem durumu nesnelerinin sistem durumlarına göre filtrelemesine izin verir. Bu parametre için olası değerler, veya HealthStateFilter numaralandırması üyeleri üzerinde, üye veya bit düzeyinde işlemlerden elde edilen tamsayı değerini içerir. Yalnızca filtreyle eşleşen uygulamalar döndürülür. Tüm uygulamalar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri OK (2) ve uyarı (4) olan uygulamaların sistem durumu döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --Events-Sistem durumu-durum-filtre | Sistem durumu temelinde döndürülen HealthEvent nesnelerinin toplanmasını filtrelemeye izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen olaylar döndürülür. Tüm olaylar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri (2) ve uyarı (4) olan tüm olaylar döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --exclude-sistem durumu-istatistikler | Durum istatistiklerinin Sorgu sonucunun bir parçası olarak döndürülüp döndürülmeyeceğini gösterir. Varsayılan olarak false. İstatistikler sistem durumu Tamam, uyarı ve hata içindeki alt öğe varlıklarının sayısını gösterir. |
| --Include-System-Application-Health-STATISTICS | Sistem durumu istatistiklerinin doku\:/sistem uygulama durumu istatistikleri içerip içermediğini belirtir. Varsayılan olarak false. IncludeSystemApplicationHealthStatistics değeri true olarak ayarlanırsa, sistem durumu istatistikleri doku\:/System uygulamasına ait olan varlıkları içerir. Aksi takdirde, sorgu sonucu yalnızca kullanıcı uygulamaları için sistem durumu istatistiklerini içerir. Bu parametrenin uygulanması için durum istatistiklerinin, sorgu sonucuna dahil olması gerekir. |
| --Nodes-sistem durumu-durum-filtre | Küme durumu sorgusunun sonucunda döndürülen düğüm durumu nesnelerinin sistem durumlarına bağlı olarak filtrelemesine izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen düğümler döndürülür. Tüm düğümler, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri OK (2) ve uyarı (4) olan düğümlerin sistem durumu döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-manifest"></a>sfctl kümesi bildirimi
Service Fabric küme bildirimini alın.

Service Fabric küme bildirimini alın. Küme bildirimi, küme üzerinde farklı düğüm türleri, güvenlik yapılandırması, hata ve yükseltme etki alanı topolojileri vb. dahil olmak üzere kümenin özelliklerini içerir. Bu özellikler, tek başına bir küme dağıtıldığında ClusterConfig. JSON dosyasının bir parçası olarak belirtilir. Ancak, küme bildirimindeki bilgilerin çoğu, diğer dağıtım senaryolarında (örn. Azure portal kullanılırken) küme dağıtımı sırasında Service Fabric tarafından dahili olarak oluşturulur. Küme bildiriminin içeriği yalnızca bilgilendirme amaçlıdır ve kullanıcıların dosya içerikleri veya yorumu için bir bağımlılığı olması beklenmez.

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

## <a name="sfctl-cluster-operation-cancel"></a>sfctl küme işlemi-iptal
Kullanıcı tarafından alınmış bir hata işlemini iptal eder.

Aşağıdaki API 'Ler CancelOperation\: StartDataLoss, Startquorumkaybetme, StartPartitionRestart, StartNodeTransition kullanılarak iptal edilmiş olabilecek hata işlemlerini başlatır. Zorla false ise, belirtilen kullanıcı tarafından alınmış işlem düzgün şekilde durdurulur ve temizlenir.  Zorla true ise, komut iptal edilir ve bazı iç durum arka planda kalabilir.  Doğru olarak zorla belirtilmesi, dikkatli kullanılmalıdır. Bu API, zorla değeri false olarak ayarlanmış olan aynı test komutunda zaten çağrılmadıkça veya test komutunun OperationState. RollingBack öğesinin OperationState 'i zaten mevcut değilse, bu API 'yi true olarak ayarla özelliği true olarak ayarlanmalıdır. OperationState. RollingBack\: açıklama işlemi, sistemin komut yürütülerek oluşan iç sistem durumunu temizleyebileceği anlamına gelir.  Sınama komutu veri kaybına neden olduysa, verileri geri yükleme işlemi olmayacaktır.  Örneğin, StartDataLoss 'yi çağırırsanız bu API 'yi çağırırsanız, sistem yalnızca komutu çalıştırmadan iç durumu temizler. Komut, veri kaybına neden olacak kadar ilerlemedi, hedef bölümün verilerini geri yükler. Önemli\: notta, bu API zorla = = true ile çağrılırsa iç durum arkasında kalabilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --işlem kimliği [gerekli] | Bu API 'nin bir çağrısını tanımlayan GUID.  Bu, karşılık gelen GetProgress API 'sine geçirilir. |
| --zorla | Kullanıcı tarafından düzenlenen işlemi yürüterek, iç sistem durumunun düzgün bir şekilde geri döndürülüp temizlenip temizlenmeyeceğini gösterir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-operation-list"></a>sfctl kümesi işlemi-liste
Belirtilen girişe göre filtrelenmiş Kullanıcı tarafından gerçekleştirilen hata işlemlerinin bir listesini alır.

Belirtilen girişe göre filtrelenmiş Kullanıcı tarafından gerçekleştirilen hata işlemlerinin listesini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --durum-filtre | Kullanıcı tarafından kullanılan işlemler için OperationState 'e filtre uygulamak üzere kullanılır. -65535-1-Select çalışıyor-2-Seçme çalıştırılan-2-Seçme RollingBack-8-Select tamamlandı-16-Select, 64 32 hata  Varsayılan\: 65535. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |
| --tür-filtre | Kullanıcı tarafından kullanılan işlemler için OperationType 'a filtre uygulamak için kullanılır. -65535-1-Select PartitionDataLoss öğesini seçin. -2-Partitionquorumkaybetme seçeneğini belirleyin. -4-PartitionRestart ' ı seçin. -8-NodeTransition seçin.  Varsayılan\: 65535. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-provision"></a>sfctl kümesi sağlama
Service Fabric kümesinin kodunu veya yapılandırma paketlerini sağlayın.

Service Fabric kümesinin kodunu veya yapılandırma paketlerini doğrulayın ve sağlayın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Cluster-manifest-dosya-yolu | Küme bildirim dosyası yolu. |
| --Code-File-Path | Küme kodu paket dosyası yolu. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-recover-system"></a>sfctl kümesi kurtarma-sistem
Service Fabric kümesine, şu anda yetersayı kaybından takılı olan sistem hizmetlerini kurtarmaya çalışılması gerektiğini gösterir.

Service Fabric kümesine, şu anda yetersayı kaybından takılı olan sistem hizmetlerini kurtarmaya çalışılması gerektiğini gösterir. Bu işlem yalnızca, alt kopyaların kurtarılamamış olduğu biliniyorsa gerçekleştirilmelidir. Bu API 'nin yanlış kullanımı olası veri kaybına neden olabilir.

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

## <a name="sfctl-cluster-report-health"></a>sfctl kümesi raporu-sistem durumu
Service Fabric kümesinde bir sistem durumu raporu gönderir.

Service Fabric kümesinde bir sistem durumu raporu gönderir. Rapor, sistem durumu raporunun ve rapor alındığı özelliğin kaynağı hakkında bilgi içermelidir. Rapor, sistem durumu deposuna ileten bir Service Fabric ağ geçidi düğümüne gönderilir. Rapor, ağ geçidi tarafından kabul edilebilir, ancak ek doğrulamadan sonra sistem durumu deposu tarafından reddedildi. Örneğin, eski bir sıra numarası gibi geçersiz bir parametre nedeniyle sistem durumu deposu raporu reddedebilir. Raporun sistem durumu deposuna uygulanıp uygulanmadığını görmek için GetClusterHealth ' ı çalıştırın ve raporun HealthEvents bölümünde görünüp görüntülenmediğini denetleyin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Health-Property [gerekli] | Sistem durumu bilgilerinin özelliği. <br><br> Bir varlık, farklı özellikler için sistem durumu raporlarına sahip olabilir. Özelliği, rapor tetikleyen durum koşulunu kategorilere ayırmak için Raporlayıcı esnekliğine izin veren sabit bir sabit listesi olmayan bir dizedir. Örneğin, SourceId "Localizçi" olan bir Raporlayıcı, bir düğümdeki kullanılabilir diskin durumunu izleyebilir, bu nedenle söz konusu düğümdeki "AvailableDisk" özelliğini bildirebilirler. Aynı Reporter, düğüm bağlantısını izleyip aynı düğümde bir özelliği "bağlantı" bildirebilirler. Sistem durumu deposunda, bu raporlar belirtilen düğüm için ayrı sistem durumu olayları olarak kabul edilir. SourceID ile birlikte, özelliği sistem durumu bilgilerini benzersiz şekilde tanımlar. |
| --sistem durumu-durumu [gerekli] | Olası değerler arasında\: ' geçersiz ', ' Tamam ', ' uyarı ', ' Error ', ' Unknown ' verilebilir. |
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

## <a name="sfctl-cluster-select"></a>sfctl kümesi seçme
Service Fabric küme uç noktasına bağlanır.

Güvenli kümeye bağlanıyorsanız, bir sertifika (. CRT) ve anahtar dosyası (. Key) için mutlak bir yol veya hem (. ped) ile tek bir dosya belirtin. Her ikisini de belirtmeyin. İsteğe bağlı olarak, güvenli bir kümeye bağlanıyorsanız, CA paket dosyası veya güvenilir CA sertifikaları dizini için mutlak bir yol da belirtin.  Localhost bağlantısı da dahil olmak üzere, önce bu komutu çalıştırmadan bir kümeye bağlantı yoktur. Ancak, yerel bir kümeye bağlanmak için açık uç nokta gerekmez.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --aad | Kimlik doğrulaması için Azure Active Directory kullanın. |
| --CA | CA sertifikaları dizinine yönelik mutlak yol, geçerli veya CA paket dosyası olarak değerlendirilir. CA sertifikaları dizini kullanılıyorsa, OpenSSL tarafından belirtilen `c_rehash <directory>`, sertifika karmalarını hesaplamak ve uygun sembolics bağlantılarını oluşturmak için önce çalıştırılmalıdır. Bu, küme tarafından döndürülen sertifikanın geçerli olduğunu doğrulamak için kullanılır. |
| --CERT | İstemci sertifika dosyasının mutlak yolu. |
| --uç nokta | Bağlantı noktası ve HTTP ya da HTTPS ön eki dahil küme uç noktası URL 'SI. Genellikle, uç nokta https\://<-URL 'niz >\:19080 gibi bir şey arayacaktır. Uç nokta verilmezse, varsayılan olarak http\:/localhost\:19080 kullanılır.  Varsayılan\: http\://localhost\:19080. |
| --anahtar | İstemci sertifikası anahtar dosyasının mutlak yolu. |
| --No-doğrula | HTTPS kullanırken sertifikaların doğrulanmasını devre dışı bırak\:, bu güvenli olmayan bir seçenektir ve üretim ortamlarında kullanılmamalıdır. |
| --pek | . Pek dosyası olarak istemci sertifikasına mutlak yol. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-show-connection"></a>sfctl kümesi gösterme-bağlantı
Bu sfctl örneğinin hangi Service Fabric kümesini bağlandığını gösterin.

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-unprovision"></a>sfctl kümesi sağlamayı kaldırma
Service Fabric kümesinin kodunu veya yapılandırma paketlerini sağlamayı kaldırma.

Kod ve yapılandırmanın ayrı ayrı sağlanması desteklenir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Code-Version | Küme kod paketi sürümü. |
| --config-Version | Küme bildirimi sürümü. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-upgrade"></a>sfctl küme yükseltmesi
Service Fabric kümesinin kodunu veya yapılandırma sürümünü yükseltmeye başlayın.

Sağlanan yükseltme parametrelerini doğrulayın ve parametreler geçerliyse Service Fabric kümesinin kodunu veya yapılandırma sürümünü yükseltmeye başlayın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --uygulama-sistem durumu-eşleme | Hata dağıtılmadan önce uygulama adı çiftlerinin ve en yüksek yüzdelerin JSON kodlu sözlüğü. |
| --Uygulama-türü-sistem durumu-eşleme | Hata dağıtılmadan önce uygulama türü adı çiftlerinin ve en yüksek yüzdelerin JSON kodlu sözlüğü. |
| --Code-Version | Küme kod sürümü. |
| --config-Version | Küme yapılandırma sürümü. |
| --Delta-sistem durumu-değerlendirme | Her yükseltme etki alanının tamamlanmasından sonra mutlak sistem durumu değerlendirmesi yerine Delta sistem durumu değerlendirmesini mümkün bir şekilde sunar. |
| --Delta-sağlıksız-düğümler | Küme yükseltmeleri sırasında izin verilen düğümlerin izin verilen en yüksek sınır yüzdesi.  Varsayılan değer 10\:. <br><br> Delta, yükseltmenin başlangıcında düğümlerin durumu ve sistem durumu değerlendirmesi sırasında düğümlerin durumu arasında ölçülür. Denetim, kümenin genel durumunun toleranslı limitlerin içinde olduğundan emin olmak için her yükseltme etki alanı yükseltmesinin tamamlanmasından sonra gerçekleştirilir. |
| --hata-eylem | Olası değerler\: ' geçersiz ', ' geri alma ', ' Manual ' değerleridir. |
| --zorla-yeniden Başlat | Kod sürümü değiştirilmese bile, yükseltme işlemi sırasında süreçler zorla yeniden başlatılır. <br><br> Yükseltme yalnızca yapılandırmayı veya verileri değiştirir. |
| --sistem durumu-denetimi-yeniden dene | Uygulama veya küme sağlıklı değilse sistem durumu denetimleri gerçekleştirme denemeleri arasındaki süre uzunluğu. |
| --Sistem Durumu-Çek-kararlı | Yükseltme bir sonraki yükseltme etki alanına geçmeden önce uygulamanın veya kümenin sağlıklı kalması gereken süre. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --Sistem Durumu-Çek-bekle | Durum denetimleri işlemini başlatmadan önce bir yükseltme etki alanını tamamladıktan sonra beklenecek sürenin uzunluğu. |
| --Çoğaltma-ayarlama-denetim-zaman aşımı | Bir yükseltme etki alanının işlenmesi engellenecek en uzun süre (veya beklenmedik sorunlar olduğunda kullanılabilirlik kaybını önleme). <br><br> Bu zaman aşımı süresi dolduğunda, yükseltme etki alanının işlenmesi, kullanılabilirlik kaybı sorunlarından bağımsız olarak devam edecektir. Zaman aşımı, her yükseltme etki alanının başlangıcında sıfırlanır. Geçerli değerler 0 ile 42949672925 (dahil) arasındadır. |
| --sıralı-yükseltme modu | Olası değerler\: ' geçersiz ', ' Unmonitortoredauto ', ' Unmonitortoredmanual ', ' Izleniyor ' değerleridir.  Varsayılan\: Monitortoredauto. |
| --timeout-t | Varsayılan\: 60. |
| --sağlıksız-uygulamalar | Bir hata raporlanmadan önce, sağlıksız uygulamaların izin verilen en büyük yüzdesi. <br><br> Örneğin, uygulamaların %10 ' un sağlıksız olmasını sağlamak için bu değer 10 olur. Yüzde değeri, küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek uygulamaların en yüksek toleranslı yüzdesini temsil eder. Yüzde oranı dikkate alınmaz ancak en az bir sağlıksız uygulama varsa, sistem durumu uyarı olarak değerlendirilir. Bu, ApplicationTypeHealthPolicyMap 'e dahil edilen uygulama türlerinin uygulamaları hariç, sağlıksız uygulama sayısının kümedeki Toplam uygulama örneği sayısına bölünerek hesaplanır. Hesaplama, küçük sayıda uygulamada bir hata olduğunu kabul etmek için yukarı yuvarlar. |
| --sağlıksız-düğümler | Bir hata raporlanmadan önce, sağlıksız düğümlerin izin verilen en büyük yüzdesi. <br><br> Örneğin, düğümlerin %10 ' un sağlıksız olmasını sağlamak için bu değer 10 olur. Yüzde, küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek düğümlerin maksimum toleranslı yüzdesini temsil eder. Yüzde oranı kabul edilir ancak en az bir sağlıksız düğüm varsa, sistem durumu uyarı olarak değerlendirilir. Yüzde, sağlıksız düğümlerin sayısı kümedeki toplam düğüm sayısına bölünerek hesaplanır. Hesaplama, küçük sayıda düğümde bir hata olduğunu tolerans için yukarı yuvarlar. Büyük kümelerde, bazı düğümler her zaman onarım için kapatılacaktır ve bu nedenle bu yüzdenin bu yüzdeyi kabul edecek şekilde yapılandırılması gerekir. |
| --Upgrade-Domain-Delta-sağlıksız-Nodes | Küme yükseltmeleri sırasında izin verilen yükseltme etki alanı düğümlerinin izin verilen maksimum yüzdesi.  Varsayılan değer 15\:. <br><br> Delta, yükseltmenin başlangıcında yükseltme etki alanı düğümlerinin durumu ve sistem durumu değerlendirmesi sırasında yükseltme etki alanı düğümlerinin durumu arasında ölçülür. Denetim, yükseltme etki alanlarının durumunun toleranslı limitlerin içinde olduğundan emin olmak için tüm tamamlanan yükseltme etki alanlarında her yükseltme etki alanı yükseltme tamamlandıktan sonra gerçekleştirilir. |
| --Upgrade-etki alanı zaman aşımı | Her yükseltme etki alanının, FailureAction yürütülmeden önce tamamlaması gereken süre miktarı. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --Upgrade-Timeout | FailureAction yürütülmeden önce genel yükseltmenin tamamlaması gereken süre miktarı. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --hata olarak uyarı | Uyarıların hatalarla aynı önem derecesine sahip olup olmadığını gösterir. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl kümesi yükseltme-özgeçmişi
Küme yükseltmesini bir sonraki yükseltme etki alanına geçiş yapın.

Uygunsa, küme kodunu veya yapılandırma yükseltmesini bir sonraki yükseltme etki alanına taşımayı unutmayın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Upgrade-etkialanı [gerekli] | Bu küme yükseltmesinin sonraki yükseltme etki alanı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl kümesi yükseltme-geri alma
Service Fabric kümesinin yükseltmesini geri alma.

Service Fabric kümesinin kodunu veya yapılandırma yükseltmesini geri alın.

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

## <a name="sfctl-cluster-upgrade-status"></a>sfctl kümesi yükseltme-durum
Geçerli küme yükseltmesinin ilerlemesini alır.

Devam eden küme yükseltmesinin geçerli ilerlemesini alır. Şu anda devam eden bir yükseltme yoksa, önceki küme yükseltmesinin son durumunu alın.

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

## <a name="sfctl-cluster-upgrade-update"></a>sfctl küme yükseltmesi-güncelleştirme
Service Fabric küme yükseltmesinin yükseltme parametrelerini güncelleştirin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --uygulama-sistem durumu-eşleme | Hata dağıtılmadan önce uygulama adı çiftlerinin ve en yüksek yüzdelerin JSON kodlu sözlüğü. |
| --Uygulama-türü-sistem durumu-eşleme | Hata dağıtılmadan önce uygulama türü adı çiftlerinin ve en yüksek yüzdelerin JSON kodlu sözlüğü. |
| --Delta-sistem durumu-değerlendirme | Her yükseltme etki alanının tamamlanmasından sonra mutlak sistem durumu değerlendirmesi yerine Delta sistem durumu değerlendirmesini mümkün bir şekilde sunar. |
| --Delta-sağlıksız-düğümler | Küme yükseltmeleri sırasında izin verilen düğümlerin izin verilen en yüksek sınır yüzdesi.  Varsayılan değer 10\:. <br><br> Delta, yükseltmenin başlangıcında düğümlerin durumu ve sistem durumu değerlendirmesi sırasında düğümlerin durumu arasında ölçülür. Denetim, kümenin genel durumunun toleranslı limitlerin içinde olduğundan emin olmak için her yükseltme etki alanı yükseltmesinin tamamlanmasından sonra gerçekleştirilir. |
| --hata-eylem | Olası değerler\: ' geçersiz ', ' geri alma ', ' Manual ' değerleridir. |
| --zorla-yeniden Başlat | Kod sürümü değiştirilmese bile, yükseltme işlemi sırasında süreçler zorla yeniden başlatılır. <br><br> Yükseltme yalnızca yapılandırmayı veya verileri değiştirir. |
| --sistem durumu-denetimi-yeniden dene | Uygulama veya küme sağlıklı değilse sistem durumu denetimleri gerçekleştirme denemeleri arasındaki süre uzunluğu. |
| --Sistem Durumu-Çek-kararlı | Yükseltme bir sonraki yükseltme etki alanına geçmeden önce uygulamanın veya kümenin sağlıklı kalması gereken süre. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --Sistem Durumu-Çek-bekle | Durum denetimleri işlemini başlatmadan önce bir yükseltme etki alanını tamamladıktan sonra beklenecek sürenin uzunluğu. |
| --Çoğaltma-ayarlama-denetim-zaman aşımı | Bir yükseltme etki alanının işlenmesi engellenecek en uzun süre (veya beklenmedik sorunlar olduğunda kullanılabilirlik kaybını önleme). <br><br> Bu zaman aşımı süresi dolduğunda, yükseltme etki alanının işlenmesi, kullanılabilirlik kaybı sorunlarından bağımsız olarak devam edecektir. Zaman aşımı, her yükseltme etki alanının başlangıcında sıfırlanır. Geçerli değerler 0 ile 42949672925 (dahil) arasındadır. |
| --sıralı-yükseltme modu | Olası değerler\: ' geçersiz ', ' Unmonitortoredauto ', ' Unmonitortoredmanual ', ' Izleniyor ' değerleridir.  Varsayılan\: Monitortoredauto. |
| --timeout-t | Varsayılan\: 60. |
| --sağlıksız-uygulamalar | Bir hata raporlanmadan önce, sağlıksız uygulamaların izin verilen en büyük yüzdesi. <br><br> Örneğin, uygulamaların %10 ' un sağlıksız olmasını sağlamak için bu değer 10 olur. Yüzde değeri, küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek uygulamaların en yüksek toleranslı yüzdesini temsil eder. Yüzde oranı dikkate alınmaz ancak en az bir sağlıksız uygulama varsa, sistem durumu uyarı olarak değerlendirilir. Bu, ApplicationTypeHealthPolicyMap 'e dahil edilen uygulama türlerinin uygulamaları hariç, sağlıksız uygulama sayısının kümedeki Toplam uygulama örneği sayısına bölünerek hesaplanır. Hesaplama, küçük sayıda uygulamada bir hata olduğunu kabul etmek için yukarı yuvarlar. |
| --sağlıksız-düğümler | Bir hata raporlanmadan önce, sağlıksız düğümlerin izin verilen en büyük yüzdesi. <br><br> Örneğin, düğümlerin %10 ' un sağlıksız olmasını sağlamak için bu değer 10 olur. Yüzde, küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek düğümlerin maksimum toleranslı yüzdesini temsil eder. Yüzde oranı kabul edilir ancak en az bir sağlıksız düğüm varsa, sistem durumu uyarı olarak değerlendirilir. Yüzde, sağlıksız düğümlerin sayısı kümedeki toplam düğüm sayısına bölünerek hesaplanır. Hesaplama, küçük sayıda düğümde bir hata olduğunu tolerans için yukarı yuvarlar. Büyük kümelerde, bazı düğümler her zaman onarım için kapatılacaktır ve bu nedenle bu yüzdenin bu yüzdeyi kabul edecek şekilde yapılandırılması gerekir. |
| --Upgrade-Domain-Delta-sağlıksız-Nodes | Küme yükseltmeleri sırasında izin verilen yükseltme etki alanı düğümlerinin izin verilen maksimum yüzdesi.  Varsayılan değer 15\:. <br><br> Delta, yükseltmenin başlangıcında yükseltme etki alanı düğümlerinin durumu ve sistem durumu değerlendirmesi sırasında yükseltme etki alanı düğümlerinin durumu arasında ölçülür. Denetim, yükseltme etki alanlarının durumunun toleranslı limitlerin içinde olduğundan emin olmak için tüm tamamlanan yükseltme etki alanlarında her yükseltme etki alanı yükseltme tamamlandıktan sonra gerçekleştirilir. |
| --Upgrade-etki alanı zaman aşımı | Her yükseltme etki alanının, FailureAction yürütülmeden önce tamamlaması gereken süre miktarı. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --yükseltme-tür | Olası değerler şunlardır\: ' geçersiz ', ' kayan ', ' Rolling_ForceRestart '.  Varsayılan\: alınıyor. |
| --Upgrade-Timeout | FailureAction yürütülmeden önce genel yükseltmenin tamamlaması gereken süre miktarı. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --hata olarak uyarı | Uyarıların hatalarla aynı önem derecesine sahip olup olmadığını gösterir. |

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