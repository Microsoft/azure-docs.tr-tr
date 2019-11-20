---
title: Azure Service Fabric CLı-sfctl hizmeti | Microsoft Docs
description: Service Fabric CLı sfctl hizmeti komutlarını açıklar.
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
ms.openlocfilehash: 63f901da3f64e62a55ab27904b8a38156957a7ee
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901000"
---
# <a name="sfctl-service"></a>sfctl service
Hizmeti, hizmet türlerini ve hizmet paketlerini oluşturun, silin ve yönetin.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| uygulama adı | Bir hizmet için Service Fabric uygulamasının adını alır. |
| kod-paket-listesi | Bir Service Fabric düğümünde dağıtılan kod paketlerinin listesini alır. |
| oluşturmaya | Belirtilen Service Fabric hizmetini oluşturur. |
| delete | Mevcut bir Service Fabric hizmetini siler. |
| dağıtılan tür | Service Fabric kümesindeki bir düğüme dağıtılan uygulamanın belirtilen hizmet türüyle ilgili bilgileri alır. |
| dağıtılan tür listesi | Service Fabric kümesindeki bir düğümde dağıtılan uygulamalardan hizmet türleriyle ilgili bilgileri içeren listeyi alır. |
| açıklama | Mevcut bir Service Fabric hizmetinin açıklamasını alır. |
| kapsayıcı-günlüklerinizi al | Service Fabric düğümüne dağıtılan kapsayıcının kapsayıcı günlüklerini alır. |
| sağlık | Belirtilen Service Fabric hizmetinin sistem durumunu alır. |
| Bilgisine | Service Fabric uygulamasına ait olan belirli hizmet hakkındaki bilgileri alır. |
| list | Uygulama KIMLIĞI tarafından belirtilen uygulamaya ait tüm hizmetlerle ilgili bilgileri alır. |
| Bildirim | Hizmet türünü açıklayan bildirimi alır. |
| paket dağıtma | Belirtilen hizmet bildirimiyle ilişkili paketleri belirtilen düğümdeki görüntü önbelleğine indirir. |
| paket-sistem durumu | Bir Service Fabric düğümü ve uygulaması için dağıtılan belirli bir uygulama için bir hizmet paketinin sistem durumu hakkındaki bilgileri alır. |
| paket-bilgi | Belirtilen adı tam olarak eşleşen bir Service Fabric düğümünde dağıtılan hizmet paketlerinin listesini alır. |
| paket listesi | Bir Service Fabric düğümünde dağıtılan hizmet paketlerinin listesini alır. |
| Kurtarılamıyor | Service Fabric kümesine, şu anda yetersayı kaybından takılmış olan hizmeti kurtarmaya çalışmak zorunda olduğunu gösterir. |
| rapor-sistem durumu | Service Fabric hizmetine bir sistem durumu raporu gönderir. |
| çözmek | Service Fabric bölümünü çözün. |
| tür listesi | Service Fabric kümesinde sağlanan uygulama türü tarafından desteklenen hizmet türleriyle ilgili bilgileri içeren listeyi alır. |
| update | Verilen güncelleştirme açıklamasını kullanarak belirtilen hizmeti güncelleştirir. |

## <a name="sfctl-service-app-name"></a>sfctl hizmeti uygulaması-adı
Bir hizmet için Service Fabric uygulamasının adını alır.

Belirtilen hizmet için uygulamanın adını alır. Bir 404 FABRIC_E_SERVICE_DOES_NOT_EXIST hatası, belirtilen hizmet KIMLIĞINE sahip bir hizmet yoksa döndürülür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl hizmet kodu-paket-listesi
Bir Service Fabric düğümünde dağıtılan kod paketlerinin listesini alır.

Verilen uygulama için bir Service Fabric düğümünde dağıtılan kod paketlerinin listesini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --Code-Package-Name | Hizmet bildiriminde belirtilen kod paketinin adı Service Fabric kümesindeki bir uygulama türünün bir parçası olarak kaydedilir. |
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

## <a name="sfctl-service-create"></a>sfctl hizmeti oluşturma
Belirtilen Service Fabric hizmetini oluşturur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --app-id [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. Sürüm 6,0 ' den başlayarak, hiyerarşik adlar ' \~ ' karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı ' Fabric\:/MyApp/APP1 ' ise, uygulama kimliği önceki sürümlerde 6.0 + ve ' MyApp/APP1 ' içinde ' MyApp\~APP1 ' olur. |
| --ad [gerekli] | Hizmetin adı. Bu, uygulama KIMLIĞININ bir alt öğesi olmalıdır. Bu, `fabric\:` URI 'SI de dahil olmak üzere tam addır. Örneğin, hizmet `fabric\:/A/B` bir uygulama `fabric\:/A`alt öğesidir. |
| --hizmet-türü [gerekli] | Hizmet türünün adı. |
| --etkinleştirme-mod | Hizmet paketi için etkinleştirme modu. |
| --kısıtlamalar | Bir dize olarak yerleştirme kısıtlamaları. Yerleştirme kısıtlamaları, düğüm özelliklerindeki Boole ifadeleridir ve hizmet gereksinimlerine bağlı olarak bir hizmetin belirli düğümlere kısıtlanmasının izin verir. Örneğin, NodeType 'in mavi olduğu düğümlere bir hizmet yerleştirmek için aşağıdaki\:"NodeColor = = Blue" belirtin. |
| --bağıntılı-hizmet | İlişki kurmak için hedef hizmetin adı. |
| --bağıntı | Bir hizalama benzeşimi kullanarak hizmetin mevcut bir hizmetle ilişkilendirilmesi. |
| --DNS-adı | Oluşturulacak hizmetin DNS adı. Bu ayar için Service Fabric DNS sistem hizmetinin etkinleştirilmesi gerekir. |
| --örnek-sayısı | Örnek sayısı. Bu yalnızca durum bilgisi olmayan hizmetler için geçerlidir. |
| --int-Scheme | Hizmetin bir dizi işaretsiz tamsayı arasında bir şekilde bölümlenmesi gerektiğini gösterir. |
| --int-Scheme-Count | Tekdüzen tamsayı bölüm düzeni kullanılıyorsa, oluşturulacak tamsayı anahtar aralığı içindeki bölüm sayısı. |
| --int-Scheme-yüksek | Tekdüzen tamsayı bölüm düzeni kullanılıyorsa, anahtar tamsayı aralığının sonu. |
| --int-Scheme-düşük | Tekdüzen tamsayı bölüm düzeni kullanılıyorsa, anahtar tamsayı aralığının başlangıcı. |
| --Yük-ölçümler | Düğümler genelinde Hizmetleri yük dengeleme sırasında kullanılan, JSON kodlamalı ölçüm listesi. |
| --Min-Replication-set-size | Sayı olarak en küçük çoğaltma kümesi boyutu. Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --taşıma-maliyet | Hizmetin taşıma maliyetini belirtir. Olası değerler şunlardır\: ' sıfır ', ' Low ', ' Medium ', ' High '. |
| --adlandırılmış düzen | Hizmetin birden fazla adlandırılmış bölüme sahip olması gerektiğini belirtir. |
| --adlandırılmış-düzen-liste | Adlandırılmış bölüm şeması kullanılıyorsa, hizmetin üzerinde bölümlenmesi için JSON kodlu adların listesi. |
| --kalıcı olmayan-durum | True ise bu, hizmetin yerel diskte depolanan kalıcı bir durum olmadığını veya yalnızca durumu bellekte depoladığını gösterir. |
| --yerleştirme-ilke-liste | Hizmet için yerleştirme ilkelerinin JSON kodlu listesi ve ilişkili tüm etki alanı adları. İlkeler bir veya daha fazla\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain``RequireDomainDistribution`olabilir. |
| --çekirdek kaybı-bekleme | Bir bölümün çekirdek kaybı durumunda olmasına izin verilen en uzun süre (saniye cinsinden). Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --çoğaltma-yeniden Başlat-bekle | Bir çoğaltmanın ne zaman ve yeni bir çoğaltma oluşturulduğu zaman arasındaki saniye cinsinden süre. Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --ölçekleme-ilkeler | Bu hizmet için ölçeklendirme ilkelerinin JSON kodlu listesi. |
| --tek düzen | Hizmetin tek bir bölüme sahip olması veya bölümlenmemiş bir hizmet olması gerektiğini belirtir. |
| --tek çoğaltma-sakla | Kaldırılma çoğaltmaları kaldırılmadan önce tutulabileceği en uzun süre (saniye cinsinden). Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --durum bilgisi olan | Hizmetin durum bilgisi olan bir hizmet olduğunu gösterir. |
| --durum bilgisiz | Hizmetin durum bilgisiz olmayan bir hizmet olduğunu gösterir. |
| --Target-Replication-set-size | Hedef çoğaltma kümesi boyutu sayı olarak. Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --timeout-t | Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-delete"></a>sfctl hizmeti silme
Mevcut bir Service Fabric hizmetini siler.

Silinmeden önce bir hizmetin oluşturulması gerekir. Varsayılan olarak, Service Fabric hizmet çoğaltmalarını düzgün bir şekilde kapatmaya çalışır ve ardından hizmeti silmez. Ancak, hizmet çoğaltmayı düzgün bir şekilde kapatmada sorun yaşadığı takdirde, silme işlemi uzun zaman alabilir veya takılmış olabilir. Düzgün kapatma sırasını atlamak için isteğe bağlı ForceRemove bayrağını kullanın ve hizmeti zorla silin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
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

## <a name="sfctl-service-deployed-type"></a>sfctl hizmeti dağıtıldı-tür
Service Fabric kümesindeki bir düğüme dağıtılan uygulamanın belirtilen hizmet türüyle ilgili bilgileri alır.

Service Fabric kümesindeki bir düğümde dağıtılan uygulamalardan belirli bir hizmet türü hakkındaki bilgileri içeren listeyi alır. Yanıt, hizmet türünün adını, kayıt durumunu, onu kaydettiğiniz kod paketini ve hizmet paketinin etkinleştirme KIMLIĞINI içerir. Her giriş, etkinleştirme KIMLIĞIYLE ayırt edilecek bir hizmet türünün bir etkinleştirmesini temsil eder.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --hizmet-türü-adı [gerekli] | Service Fabric hizmet türünün adını belirtir. |
| --hizmet-bildirim-adı | Dağıtılan hizmet türü bilgilerinin listesini filtrelemek için hizmet bildiriminin adı. Belirtilmişse, yanıt yalnızca bu hizmet bildiriminde tanımlanan hizmet türleriyle ilgili bilgileri içerir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl hizmeti dağıtıldı-tür-listesi
Service Fabric kümesindeki bir düğümde dağıtılan uygulamalardan hizmet türleriyle ilgili bilgileri içeren listeyi alır.

Service Fabric kümesindeki bir düğümde dağıtılan uygulamalardan hizmet türleriyle ilgili bilgileri içeren listeyi alır. Yanıt, hizmet türünün adını, kayıt durumunu, onu kaydettiğiniz kod paketini ve hizmet paketinin etkinleştirme KIMLIĞINI içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --hizmet-bildirim-adı | Dağıtılan hizmet türü bilgilerinin listesini filtrelemek için hizmet bildiriminin adı. Belirtilmişse, yanıt yalnızca bu hizmet bildiriminde tanımlanan hizmet türleriyle ilgili bilgileri içerir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-description"></a>sfctl hizmeti açıklaması
Mevcut bir Service Fabric hizmetinin açıklamasını alır.

Mevcut bir Service Fabric hizmetinin açıklamasını alır. Açıklaması alınabilmesi için önce bir hizmetin oluşturulması gerekir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl hizmeti Get-Container-logs
Service Fabric düğümüne dağıtılan kapsayıcının kapsayıcı günlüklerini alır.

Verilen kod paketi için bir Service Fabric düğümüne dağıtılan kapsayıcının kapsayıcı günlüklerini alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Code-Package-Name [gerekli] | Hizmet bildiriminde belirtilen kod paketinin adı Service Fabric kümesindeki bir uygulama türünün bir parçası olarak kaydedilir. |
| --Node-Name [gerekli] | Düğümün adı. |
| --hizmet-bildirim-adı [gerekli] | Service Fabric kümesinde uygulama türünün bir parçası olarak kaydedilmiş bir hizmet bildiriminin adı. |
| --önceki | Kod paketi örneğinin geçmiş/yok sayılma kapsayıcılarından kapsayıcı günlüklerinin kullanılıp kullanılmayacağını belirtir. |
| --Tail | Günlüklerin sonundan gösterilecek satır sayısı. Varsayılan değer 100 ' dir. Tüm günlükleri göstermek için ' All '. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-health"></a>sfctl hizmet durumu
Belirtilen Service Fabric hizmetinin sistem durumunu alır.

Belirtilen hizmetin sistem durumu bilgilerini alır. Hizmette bildirilen sistem durumu olaylarının toplanmasını sistem durumuna göre filtrelemek için EventsHealthStateFilter ' i kullanın. Döndürülen bölümlerin koleksiyonunu filtrelemek için PartitionsHealthStateFilter kullanın. Sistem durumu deposunda mevcut olmayan bir hizmet belirtirseniz, bu istek bir hata döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --Events-Sistem durumu-durum-filtre | Sistem durumu temelinde döndürülen HealthEvent nesnelerinin toplanmasını filtrelemeye izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen olaylar döndürülür. Tüm olaylar, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerlerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri (2) ve uyarı (4) olan tüm olaylar döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --exclude-sistem durumu-istatistikler | Durum istatistiklerinin Sorgu sonucunun bir parçası olarak döndürülüp döndürülmeyeceğini gösterir. Varsayılan olarak false. İstatistikler sistem durumu Tamam, uyarı ve hata içindeki alt öğe varlıklarının sayısını gösterir. |
| --Partitions-sistem durumu-durum-filtre | Hizmet durumu sorgusunun sonucunda döndürülen bölümler sistem durumu nesnelerinin sistem durumlarına göre filtrelemesine izin verir. Bu parametre için olası değerler aşağıdaki sistem durumlarından birine ait tamsayı değerini içerir. Yalnızca filtreyle eşleşen bölümler döndürülür. Tüm bölümler, toplanan sistem durumunu değerlendirmek için kullanılır. Belirtilmezse, tüm girişler döndürülür. Durum değerleri bayrak tabanlı numaralandırmadır, bu nedenle değer bit düzeyinde ' OR ' işleci kullanılarak elde edilen bu değerin bir birleşimi olabilir. Örneğin, girilen değer 6 ise, HealthState değeri OK (2) ve uyarı (4) olan bölümlerin sistem durumu döndürülür.  <br> -Varsayılan-varsayılan değer. Herhangi bir HealthState ile eşleşir. Değer sıfırdır.  <br> -None-hiçbir HealthState değerle eşleşmeyen filtre. Belirli bir durum koleksiyonuna sonuç döndürmek için kullanılır. Değer 1 ' dir.  <br> -Ok-HealthState değer ile girişle eşleşen filtre. Değer 2 ' dir.  <br> -Warning-değerle HealthState değer uyarısıyla eşleşen filtre. Değer 4 ' dir.  <br> -Error-bir değerle HealthState değer hatası ile eşleşen filtre. Değer 8 ' dir.  <br> -Bir HealthState değer ile girişle eşleşen All filtresi. Değer 65535 ' dir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-info"></a>sfctl hizmet bilgisi
Service Fabric uygulamasına ait olan belirli hizmet hakkındaki bilgileri alır.

Belirtilen Service Fabric uygulamasına ait belirtilen hizmet hakkındaki bilgileri döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
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

## <a name="sfctl-service-list"></a>sfctl hizmet listesi
Uygulama KIMLIĞI tarafından belirtilen uygulamaya ait tüm hizmetlerle ilgili bilgileri alır.

Uygulama KIMLIĞI tarafından belirtilen uygulamaya ait tüm hizmetlerle ilgili bilgileri döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Devamlılık-belirteç | Devam belirteci parametresi, sonraki sonuç kümesini almak için kullanılır. Boş olmayan bir değere sahip devamlılık belirteci, sistemin sonuçları tek bir yanıta sığmıyor olduğunda API 'nin yanıtına dahil edilir. Bu değer sonraki API çağrısına geçirildiğinde, API sonraki sonuç kümesini döndürür. Başka sonuç yoksa devamlılık belirteci bir değer içermez. Bu parametrenin değeri, URL kodlamalı olmamalıdır. |
| --hizmet-tür-adı | Sorgulanacak Hizmetleri filtrelemek için kullanılan hizmet türü adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-manifest"></a>sfctl hizmeti bildirimi
Hizmet türünü açıklayan bildirimi alır.

Hizmet türünü açıklayan bildirimi alır. Yanıt, hizmet bildirimi XML 'sini bir dize olarak içerir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama-türü-adı [gerekli] | Uygulama türünün adı. |
| --Uygulama-türü-sürüm [gerekli] | Uygulama türünün sürümü. |
| --hizmet-bildirim-adı [gerekli] | Service Fabric kümesinde uygulama türünün bir parçası olarak kaydedilmiş bir hizmet bildiriminin adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-package-deploy"></a>sfctl hizmeti paketi-dağıt
Belirtilen hizmet bildirimiyle ilişkili paketleri belirtilen düğümdeki görüntü önbelleğine indirir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --App-Type-Name [gerekli] | İlgili istenen hizmet bildirimi için uygulama bildiriminin adı. |
| --App-Type-sürüm [gerekli] | İlgili istenen hizmet bildirimi için uygulama bildiriminin sürümü. |
| --Node-Name [gerekli] | Düğümün adı. |
| --hizmet-bildirim-adı [gerekli] | İndirilecek paketlerle ilişkili hizmet bildiriminin adı. |
| --Share-Policy | Paylaşım ilkelerinin JSON kodlu listesi. Her paylaşım ilkesi öğesi bir ' name ' ve ' scope ' öğesinden oluşur. Ad, paylaşılacak kod, yapılandırma veya veri paketinin adına karşılık gelir. Kapsam ' none ', ' All ', ' Code ', ' config ' ya da ' Data ' olabilir. |
| --timeout-t | Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-package-health"></a>sfctl hizmet paketi-sistem durumu
Bir Service Fabric düğümü ve uygulaması için dağıtılan belirli bir uygulama için bir hizmet paketinin sistem durumu hakkındaki bilgileri alır.

Service Fabric düğümüne dağıtılan belirli bir uygulama için bir hizmet paketinin sistem durumu hakkındaki bilgileri alır. Sistem durumuna bağlı olarak dağıtılan hizmet paketinde raporlanan HealthEvent nesnelerinin toplanmasını sağlamak için EventsHealthStateFilter ' u kullanın.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --Service-Package-Name [gerekli] | Hizmet paketinin adı. |
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

## <a name="sfctl-service-package-info"></a>sfctl hizmet paketi-bilgi
Belirtilen adı tam olarak eşleşen bir Service Fabric düğümünde dağıtılan hizmet paketlerinin listesini alır.

Verilen uygulama için bir Service Fabric düğümünde dağıtılan hizmet paketleriyle ilgili bilgileri döndürür. Bu sonuçlar, adı parametresi olarak belirtilen hizmet paketi adı ile eşleşen hizmet paketlerindeklerdir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
| --Node-Name [gerekli] | Düğümün adı. |
| --Service-Package-Name [gerekli] | Hizmet paketinin adı. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-package-list"></a>sfctl hizmet paketi-listesi
Bir Service Fabric düğümünde dağıtılan hizmet paketlerinin listesini alır.

Verilen uygulama için bir Service Fabric düğümünde dağıtılan hizmet paketleriyle ilgili bilgileri döndürür.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Uygulama kimliği [gerekli] | Uygulamanın kimliği. Bu genellikle uygulamanın ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, uygulama adı "Fabric\:/MyApp/APP1" ise, uygulama kimliği önceki sürümlerde 6.0 + ve "MyApp/APP1" içinde "MyApp\~APP1" olacaktır. |
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

## <a name="sfctl-service-recover"></a>sfctl hizmeti kurtarma
Service Fabric kümesine, şu anda yetersayı kaybından takılmış olan hizmeti kurtarmaya çalışmak zorunda olduğunu gösterir.

Service Fabric kümesine, şu anda yetersayı kaybından takılmış olan hizmeti kurtarmaya çalışmak zorunda olduğunu gösterir. Bu işlem yalnızca, alt kopyaların kurtarılamamış olduğu biliniyorsa gerçekleştirilmelidir. Bu API 'nin yanlış kullanımı olası veri kaybına neden olabilir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl hizmeti raporu-sistem durumu
Service Fabric hizmetine bir sistem durumu raporu gönderir.

Belirtilen Service Fabric hizmetinin sistem durumunu raporlar. Rapor, sistem durumu raporunun ve rapor alındığı özelliğin kaynağı hakkında bilgi içermelidir. Rapor, sistem durumu deposuna ileten bir Service Fabric ağ geçidi hizmetine gönderilir. Rapor, ağ geçidi tarafından kabul edilebilir, ancak ek doğrulamadan sonra sistem durumu deposu tarafından reddedildi. Örneğin, eski bir sıra numarası gibi geçersiz bir parametre nedeniyle sistem durumu deposu raporu reddedebilir. Raporun sistem durumu deposuna uygulanıp uygulanmadığını görmek için raporun sistem durumu olaylarında göründüğünü kontrol edin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Health-Property [gerekli] | Sistem durumu bilgilerinin özelliği. <br><br> Bir varlık, farklı özellikler için sistem durumu raporlarına sahip olabilir. Özelliği, rapor tetikleyen durum koşulunu kategorilere ayırmak için Raporlayıcı esnekliğine izin veren sabit bir sabit listesi olmayan bir dizedir. Örneğin, SourceId "Localizçi" olan bir Raporlayıcı, bir düğümdeki kullanılabilir diskin durumunu izleyebilir, bu nedenle söz konusu düğümdeki "AvailableDisk" özelliğini bildirebilirler. Aynı Reporter, düğüm bağlantısını izleyip aynı düğümde bir özelliği "bağlantı" bildirebilirler. Sistem durumu deposunda, bu raporlar belirtilen düğüm için ayrı sistem durumu olayları olarak kabul edilir. SourceID ile birlikte, özelliği sistem durumu bilgilerini benzersiz şekilde tanımlar. |
| --sistem durumu-durumu [gerekli] | Olası değerler arasında\: ' geçersiz ', ' Tamam ', ' uyarı ', ' Error ', ' Unknown ' verilebilir. |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. <br><br> Bu genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. Sürüm 6,0 ' den başlayarak, hiyerarşik adlar ' \~ ' karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı ' Fabric\:/MyApp/APP1/svc1 ' ise, hizmet kimliği, önceki sürümlerde 6.0 + ve "MyApp/APP1/svc1" içinde "MyApp\~APP1\~svc1 ' olur. |
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

## <a name="sfctl-service-resolve"></a>sfctl hizmeti çözümle
Service Fabric bölümünü çözün.

Hizmet çoğaltmalarının uç noktalarını almak için Service Fabric hizmet bölümünü çözümleyin.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu KIMLIK genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı "Fabric\:/MyApp/APP1/svc1" ise, hizmet kimliği, önceki sürümlerde "MyApp\~APP1\~svc1" 6.0 + ve "MyApp/APP1/svc1" olacaktır. |
| --Partition-anahtar türü | Bölüm için anahtar türü. Hizmetin bölüm şeması Int64Range veya adlandırılmış ise bu parametre gereklidir. Olası değerler aşağıda verilmiştir. -None (1)-PartitionKeyValue parametresinin belirtilmediğini belirtir. Bu, tek olarak bölümleme şeması olan bölümler için geçerlidir. Varsayılan değer budur. Değer 1 ' dir. -Int64Range (2)-PartitionKeyValue parametresinin bir int64 bölüm anahtarı olduğunu gösterir. Bu, Int64Range olarak bölümleme düzenine sahip bölümler için geçerlidir. Değer 2 ' dir. -Adlandırılmış (3)-PartitionKeyValue parametresinin bölüm adı olduğunu gösterir. Bu, bölümleme düzenine sahip bölümler için adlandırılmış olarak geçerlidir. Değer 3 ' ün. |
| --Partition-anahtar-değer | Bölüm anahtarı. Hizmetin bölüm şeması Int64Range veya adlandırılmış ise bu gereklidir. Bu, bölüm KIMLIĞI değil, tamsayı anahtar değeri ya da bölüm KIMLIĞININ adı değildir. Örneğin, hizmetiniz 0 ile 10 arasında aralıklı bölümler kullanıyorsa, bu durumda PartitionKeyValue bu aralıkta bir tamsayı olur. Aralığı veya adı görmek için sorgu hizmeti açıklaması. |
| --önceki-RSP-Version | Daha önce alınan yanıtın sürüm alanındaki değer. Bu, Kullanıcı daha önce elde edilen sonucun eski olduğunu biliyorsa bu gereklidir. |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-service-type-list"></a>sfctl hizmet türü-liste
Service Fabric kümesinde sağlanan uygulama türü tarafından desteklenen hizmet türleriyle ilgili bilgileri içeren listeyi alır.

Service Fabric kümesinde sağlanan uygulama türü tarafından desteklenen hizmet türleriyle ilgili bilgileri içeren listeyi alır. Belirtilen uygulama türü mevcut olmalıdır. Aksi takdirde, 404 durumu döndürülür.

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

## <a name="sfctl-service-update"></a>sfctl hizmeti güncelleştirmesi
Verilen güncelleştirme açıklamasını kullanarak belirtilen hizmeti güncelleştirir.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --hizmet kimliği [gerekli] | Hizmetin kimliği. Bu genellikle hizmetin ' Fabric\:' URI düzeni olmadan tam adıdır. 6,0 sürümünden başlayarak, hiyerarşik adlar "\~" karakteriyle sınırlandırılmıştır. Örneğin, hizmet adı ' Fabric\:/MyApp/APP1/svc1 ' ise, hizmet kimliği, önceki sürümlerde 6.0 + ve "MyApp/APP1/svc1" içinde "MyApp\~APP1\~svc1 ' olur. |
| --kısıtlamalar | Bir dize olarak yerleştirme kısıtlamaları. Yerleştirme kısıtlamaları, düğüm özelliklerindeki Boole ifadeleridir ve hizmet gereksinimlerine bağlı olarak bir hizmetin belirli düğümlere kısıtlanmasının izin verir. Örneğin, NodeType 'in mavi olduğu düğümlere bir hizmet yerleştirmek için aşağıdaki\: "NodeColor = = Blue" belirtin. |
| --bağıntılı-hizmet | İlişki kurmak için hedef hizmetin adı. |
| --bağıntı | Bir hizalama benzeşimi kullanarak hizmetin mevcut bir hizmetle ilişkilendirilmesi. |
| --örnek-sayısı | Örnek sayısı. Bu yalnızca durum bilgisi olmayan hizmetler için geçerlidir. |
| --Yük-ölçümler | Düğümler arasında yük dengelemesi yaparken kullanılan ölçüm JSON kodlu listesi. |
| --Min-Replication-set-size | Sayı olarak en küçük çoğaltma kümesi boyutu. Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --taşıma-maliyet | Hizmetin taşıma maliyetini belirtir. Olası değerler şunlardır\: ' sıfır ', ' Low ', ' Medium ', ' High '. |
| --yerleştirme-ilke-liste | Hizmet için yerleştirme ilkelerinin JSON kodlu listesi ve ilişkili tüm etki alanı adları. İlkeler bir veya daha fazla\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain``RequireDomainDistribution`olabilir. |
| --çekirdek kaybı-bekleme | Bir bölümün çekirdek kaybı durumunda olmasına izin verilen en uzun süre (saniye cinsinden). Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --çoğaltma-yeniden Başlat-bekle | Bir çoğaltmanın ne zaman ve yeni bir çoğaltma oluşturulduğu zaman arasındaki saniye cinsinden süre. Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --ölçekleme-ilkeler | Bu hizmet için ölçeklendirme ilkelerinin JSON kodlu listesi. |
| --tek çoğaltma-sakla | Kaldırılma çoğaltmaları kaldırılmadan önce tutulabileceği en uzun süre (saniye cinsinden). Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
| --durum bilgisi olan | Hedef hizmetin durum bilgisi olan bir hizmet olduğunu gösterir. |
| --durum bilgisiz | Hedef hizmetin durum bilgisiz olmayan bir hizmet olduğunu gösterir. |
| --Target-Replication-set-size | Hedef çoğaltma kümesi boyutu sayı olarak. Bu yalnızca durum bilgisi olan hizmetler için geçerlidir. |
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
