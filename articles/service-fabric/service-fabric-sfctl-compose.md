---
title: Azure Service Fabric CLı-sfctl oluştur | Microsoft Docs
description: Service Fabric CLı sfctl oluşturma komutlarını açıklar.
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
ms.openlocfilehash: 561616fca7401f5251c4fbac67173260a665b602
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901663"
---
# <a name="sfctl-compose"></a>sfctl compose
Docker Compose uygulamalar oluşturun, silin ve yönetin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| oluşturmaya | Service Fabric oluşturma dağıtımı oluşturur. |
| list | Service Fabric kümesinde oluşturulan oluşturma dağıtımlarının listesini alır. |
| temizlenmesine | Kümeden varolan bir Service Fabric oluşturma dağıtımını siler. |
| durum | Service Fabric oluşturma dağıtımı hakkında bilgi alır. |
| yükseltmenizi | Service Fabric kümesinde bir oluşturma dağıtımını yükseltmeye başlar. |
| yükseltme-geri alma | Service Fabric kümesinde bir dağıtım yükseltmesini oluşturma ile çalışmaya başlar. |
| yükseltme-durum | Bu Service Fabric oluşturma dağıtımı üzerinde gerçekleştirilen en son yükseltmenin ayrıntılarını alır. |

## <a name="sfctl-compose-create"></a>sfctl oluşturma oluştur
Service Fabric oluşturma dağıtımı oluşturur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Dağıtım-adı [gerekli] | Dağıtımın adı. |
| --File-Path [gerekli] | Hedef Docker Compose dosyasının yolu. |
| --şifrelenmiş-Pass | Bir kapsayıcı kayıt defteri parolası istemek yerine, zaten şifrelenmiş bir geçiş ifadesi kullanın. |
| --sahip-Pass | , Kapsayıcı kayıt defteri için bir parola sorar. |
| --timeout-t | Varsayılan\: 60. |
| --kullanıcı | Kapsayıcı kayıt defterine bağlanmak için Kullanıcı adı. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-compose-list"></a>sfctl oluşturma listesi
Service Fabric kümesinde oluşturulan oluşturma dağıtımlarının listesini alır.

Service Fabric kümesinde oluşturulma sürecinde veya oluşturulurken oluşturulan dağıtımların durumunu alır. Yanıt, oluşturma dağıtımları hakkında ad, durum ve diğer ayrıntıları içerir. Dağıtım listesi bir sayfaya uygun değilse, bir sonuç sayfası ve bir sonraki sayfayı almak için kullanılabilecek bir devamlılık belirteci döndürülür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
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

## <a name="sfctl-compose-remove"></a>sfctl oluşturma kaldırma
Kümeden varolan bir Service Fabric oluşturma dağıtımını siler.

Var olan bir Service Fabric Compose dağıtımını siler.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Dağıtım-adı [gerekli] | Dağıtımın kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-compose-status"></a>sfctl oluşturma durumu
Service Fabric oluşturma dağıtımı hakkında bilgi alır.

Oluşturulan veya Service Fabric kümesinde oluşturulma sürecinde ve adı parametresi olarak belirtilen adla eşleşen oluşturma dağıtımının durumunu döndürür. Yanıt, dağıtım ile ilgili ad, durum ve diğer ayrıntıları içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Dağıtım-adı [gerekli] | Dağıtımın kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-compose-upgrade"></a>sfctl oluşturma yükseltmesi
Service Fabric kümesinde bir oluşturma dağıtımını yükseltmeye başlar.

Sağlanan yükseltme parametrelerini doğrular ve parametreler geçerliyse dağıtımı yükseltmeye başlar.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Dağıtım-adı [gerekli] | Dağıtımın adı. |
| --File-Path [gerekli] | Hedef Docker Compose dosyasının yolu. |
| --Varsayılan-svc-tür-sistem durumu-eşleme | Hizmetlerin sistem durumunu değerlendirmek için kullanılan sistem durumu ilkesini tanımlayan JSON kodlu sözlük. |
| --şifrelenmiş-Pass | Bir kapsayıcı kayıt defteri parolası istemek yerine, zaten şifrelenmiş bir geçiş ifadesi kullanın. |
| --hata-eylem | Olası değerler\: ' geçersiz ', ' geri alma ', ' Manual ' değerleridir. |
| --zorla-yeniden Başlat | Kod sürümü değiştirilmese bile, yükseltme işlemi sırasında süreçler zorla yeniden başlatılır. <br><br> Yükseltme yalnızca yapılandırmayı veya verileri değiştirir. |
| --sahip-Pass | , Kapsayıcı kayıt defteri için bir parola sorar. |
| --sistem durumu-denetimi-yeniden dene | Uygulama veya küme sağlıklı değilse sistem durumu denetimleri gerçekleştirme denemeleri arasındaki süre uzunluğu. |
| --Sistem Durumu-Çek-kararlı | Yükseltme bir sonraki yükseltme etki alanına geçmeden önce uygulamanın veya kümenin sağlıklı kalması gereken süre. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --Sistem Durumu-Çek-bekle | Durum denetimleri işlemini başlatmadan önce bir yükseltme etki alanını tamamladıktan sonra beklenecek sürenin uzunluğu. |
| --çoğaltma-Set-Check | Bir yükseltme etki alanının işlenmesi engellenecek en uzun süre (veya beklenmedik sorunlar olduğunda kullanılabilirlik kaybını önleme). <br><br> Bu zaman aşımı süresi dolduğunda, yükseltme etki alanının işlenmesi, kullanılabilirlik kaybı sorunlarından bağımsız olarak devam edecektir. Zaman aşımı, her yükseltme etki alanının başlangıcında sıfırlanır. Geçerli değerler 0 ile 42949672925 (dahil) arasındadır. |
| --svc-tür-sistem durumu-eşleme | Farklı hizmet türlerinin sistem durumunu değerlendirmek için kullanılan sistem durumu ilkelerini tanımlayan nesnelerin JSON kodlu listesi. |
| --timeout-t | Varsayılan\: 60. |
| --sağlıksız-uygulama | Bir hata raporlanmadan önce, sağlıksız uygulamaların izin verilen en büyük yüzdesi. <br><br> Örneğin, uygulamaların %10 ' un sağlıksız olmasını sağlamak için bu değer 10 olur. Yüzde değeri, küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek uygulamaların en yüksek toleranslı yüzdesini temsil eder. Yüzde oranı dikkate alınmaz ancak en az bir sağlıksız uygulama varsa, sistem durumu uyarı olarak değerlendirilir. Bu, sağlıksız uygulama sayısı kümedeki Toplam uygulama örneği sayısına bölünerek hesaplanır. |
| --Upgrade-etki alanı zaman aşımı | Her yükseltme etki alanının, FailureAction yürütülmeden önce tamamlaması gereken süre miktarı. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --yükseltme-tür | Varsayılan\: alınıyor. |
| --yükseltme modu | Olası değerler\: ' geçersiz ', ' Unmonitortoredauto ', ' Unmonitortoredmanual ', ' Izleniyor ' değerleridir.  Varsayılan\: Monitortoredauto. |
| --Upgrade-Timeout | FailureAction yürütülmeden önce genel yükseltmenin tamamlaması gereken süre miktarı. <br><br> İlk olarak ISO 8601 süresini temsil eden bir dize olarak yorumlanır. Başarısız olursa, Toplam milisaniye sayısını temsil eden bir sayı olarak yorumlanır. |
| --kullanıcı | Kapsayıcı kayıt defterine bağlanmak için Kullanıcı adı. |
| --hata olarak uyarı | Uyarıların hatalarla aynı önem derecesine sahip olup olmadığını gösterir. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl oluşturma yükseltmesi-geri alma
Service Fabric kümesinde bir dağıtım yükseltmesini oluşturma ile çalışmaya başlar.

Service Fabric oluşturma dağıtım yükseltmesini geri alın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Dağıtım-adı [gerekli] | Dağıtımın kimliği. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl oluşturma yükseltme-durum
Bu Service Fabric oluşturma dağıtımı üzerinde gerçekleştirilen en son yükseltmenin ayrıntılarını alır.

Dağıtım yükseltmesinin oluşturma durumuyla birlikte, uygulama sistem durumu sorunlarını ayıklamaya yardımcı olacak ayrıntılarla ilgili bilgileri döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Dağıtım-adı [gerekli] | Dağıtımın kimliği. |
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