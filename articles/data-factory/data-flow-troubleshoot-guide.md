---
title: Veri akışlarını eşleme sorunlarını giderme
description: Azure Data Factory veri akışı sorunlarını giderme hakkında bilgi edinin.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/25/2021
ms.openlocfilehash: 72ab685b58f7d940fe4d682cacba6212fe80ced8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933092"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory veri akışlarını eşleme sorunlarını giderme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure Data Factory veri akışlarını eşlemek için ortak sorun giderme yöntemleri incelenmektedir.

## <a name="common-error-codes-and-messages"></a>Ortak hata kodları ve iletileri 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hata kodu: DF-yürütücü-Sourceınvalidpayload
- **İleti**: kapsayıcı mevcut olmadığından veri önizleme, hata ayıklama ve işlem hattı veri akışı yürütme başarısız oldu
- **Neden**: bir veri kümesi depolamada mevcut olmayan bir kapsayıcı içeriyor.
- **Öneri**: veri kümenizde başvurulan kapsayıcının var olduğundan ve erişilebilir olduğundan emin olun.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hata kodu: DF-yürütücü-SystemInvalidJson

- **İleti**: JSON ayrıştırma hatası, desteklenmeyen kodlama veya çok satırlı
- **Neden**: JSON dosyası ile ilgili olası sorunlar: desteklenmeyen kodlama, bozuk bayt veya JSON kaynağı birçok iç içe satırda tek bir belge olarak kullanılıyor.
- **Öneri**: JSON dosyasının kodlamasının desteklendiğini doğrulayın. JSON veri kümesi kullanan kaynak dönüşümünde **JSON ayarları** ' nı genişletin ve **tek belgeyi** açın.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Hata kodu: DF-yürütücü-Yayınzaman aşımı

- **İleti**: yayın birleşimi zaman aşımı hatası, yayın akışının hata ayıklama çalıştırmaları ve 300 saniye içinde iş çalıştırmaları sırasında 60 saniye içinde veri ürettiğinden emin olun
- **Neden**: yayın, hata ayıklama çalıştırmaları ve iş çalıştırmaları için 300 saniye varsayılan zaman aşımı süresi 60 saniyedir. Yayın için seçilen akış bu sınırın içinde veri üretmede çok büyük.
- **Öneri**: JOIN, Exists ve Lookup için veri akışı dönüştürmelerinizin **en iyileştirme** sekmesini kontrol edin. Yayın için varsayılan seçenek **Auto**' dır. **Otomatik** olarak ayarlandıysa ya da sol veya sağ tarafını **sabit** altında yayınla el ile ayarlıyorsanız, daha büyük bir Azure TÜMLEŞTIRME çalışma zamanı (IR) yapılandırması ayarlayabilir veya yayını kapatabilirsiniz. Veri akışlarında en iyi performansı elde etmek için, **Otomatik** kullanarak Spark 'ın yayınlanmasını ve bellek için iyileştirilmiş bir Azure IR kullanmanızı öneririz. 
 
  Hata ayıklama işlem hattı çalıştırmasıyla veri akışını bir hata ayıklama testi yürütmesinde çalıştırıyorsanız, Bu koşulla daha sık çalıştırabilirsiniz. Bunun nedeni, Azure Data Factory daha hızlı bir hata ayıklama deneyimi sağlamak için yayının zaman aşımını 60 saniyeye kısıtlar. Zaman aşımını tetiklenen bir çalıştırmanın 300 saniyelik zaman aşımı süresine genişletebilirsiniz. Bunu yapmak için,   >  veri akışı işlem hattı yürütme etkinliğinizdeki tanımlı Azure IR kullanmak için **etkinlik çalışma zamanını kullan** seçeneğini kullanabilirsiniz.

- **İleti**: yayın birleştirmesi zaman aşımı hatası, bu sorundan kaçınmak için JOIN/Exists/Lookup dönüşümünde yayın ' ı ' seçeneğini belirleyebilirsiniz. Performansı artırmak için JOIN seçeneğini yayınlamak istiyorsanız, yayın akışının hata ayıklama çalıştırmaları ve 300 saniye içinde, iş çalıştırmaları sırasında 60 saniye içinde veri oluşturabilmesine dikkat edin.
- **Neden**: yayın, hata ayıklama çalıştırmaları ve iş çalıştırmaları 300 saniye için varsayılan zaman aşımı süresi olan 60 saniyedir. Yayın birleşimi üzerinde, yayın için seçilen akış bu sınırın içinde veri üretmede çok büyük. Bir yayın birleşimi kullanılmazsa, veri akışı tarafından varsayılan yayın aynı sınıra ulaşabilirler.
- **Öneri**: yayın seçeneğini devre dışı bırakın veya işlemenin 60 saniyeden fazla sürebileceği büyük veri akışlarını yayınlamaktan kaçının. Yayınlanacak daha küçük bir akış seçin. Büyük Azure SQL veri ambarı tabloları ve kaynak dosyaları genellikle iyi bir seçimdir. Bir yayın katılımı yokluğunda, bu hata oluşursa daha büyük bir küme kullanın.

### <a name="error-code-df-executor-conversion"></a>Hata kodu: DF-yürütücü-dönüştürme

- **İleti**: geçersiz bir karakter nedeniyle tarih veya saate dönüştürme başarısız oldu
- **Neden**: veriler beklenen biçimde değil.
- **Öneri**: doğru veri türünü kullanın.

### <a name="error-code-df-executor-invalidcolumn"></a>Hata kodu: DF-yürütücü-ınvalidcolumn
- **İleti**: sorguda sütun adının belirtilmesi gerekir, bir SQL işlevi kullanılıyorsa bir diğer ad ayarlayın
- **Neden**: sütun adı belirtilmedi.
- **Öneri**: min () veya Max () gıbı bir SQL işlevi kullanıyorsanız, bir diğer ad belirleyin.

### <a name="error-code-df-executor-drivererror"></a>Hata kodu: DF-yürütücü-DriverError
- **İleti**: INT96, ADF veri akışı tarafından desteklenmeyen eski zaman damgası türüdür. Lütfen sütun türünü en son türlere yükseltmeyi göz önünde bulundurun.
- **Neden**: sürücü hatası.
- **Öneri**: INT96, Azure Data Factory veri akışı tarafından desteklenmeyen eski bir zaman damgası türüdür. Sütun türünü en son türe yükseltmeyi düşünün.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Hata kodu: DF-yürütücü-Blockcountexceedslimıterror
- **İleti**: kaydedilmemiş blok sayısı 100.000 blok üst sınırını aşamaz. Blob yapılandırmasını denetleyin.
- **Neden**: bir Blobun en fazla işlenmemiş blok sayısı 100.000 ' dir.
- **Öneri**: Bu sorunla ilgili daha fazla bilgi için Microsoft ürün ekibine başvurun.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Hata kodu: DF-yürütücü-PartitionDirectoryError
- **İleti**: belirtilen kaynak yolu birden çok bölümlenmiş dizine sahip (örn. <Source Path> /<bölüm kök dizini 1>/a = 10/b = 20, <Source Path> /<bölüm kök dizin 2>/c = 10/d = 30) veya diğer dosya ya da bölümlenmemiş dizin ile bölümlenmiş dizin (örneğin <Source Path> /<bölüm kök dizini 1>/a = 10/b = 20, <Source Path> /Dizin 2/FILE1), bölüm kök dizinini kaynak yolundan kaldırın ve ayrı kaynak dönüşümünde okuyun.
- **Neden**: kaynak yolunda birden çok bölümlenmiş dizin ya da başka bir dosya ya da bölümlenmemiş dizin içeren bölümlenmiş bir dizin bulunur.
- **Öneri**: bölümlenmiş kök dizini kaynak yoldan kaldırın ve ayrı kaynak dönüşümünde okuyun.

### <a name="error-code-df-executor-invalidtype"></a>Hata kodu: DF-yürütücü-ınvalidtype
- **İleti**: lütfen parametre türünün geçirilen değer türüyle eşleştiğinden emin olun. İşlem hatlarından kayan parametrelerin geçirilmesi Şu anda desteklenmiyor.
- **Neden**: belirtilen tür için veri türü gerçek parametre değeri ile uyumlu değil.
- **Öneri**: veri akışına geçirilen parametre değerlerinin, belirtilen türle eşleştiğinden emin olun.

### <a name="error-code-df-executor-parseerror"></a>Hata kodu: DF-yürütücü-ParseError
- **İleti**: ifade ayrıştırılamıyor
- **Neden**: yanlış biçimlendirme nedeniyle bir ifade Ayrıştırma hataları oluşturdu.
- **Öneri**: ifadedeki biçimlendirmeyi denetleyin.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hata kodu: DF-yürütücü-Systemımplicıtkartezyen
- **İleti**: iç birleşim için örtülü Kartezyen ürün desteklenmiyor, bunun yerıne çapraz birleşim kullanın. Birleşimde kullanılan sütunlar, satırlar için benzersiz bir anahtar oluşturmaktır.
- **Neden**: mantıksal planlar arasında iç birleştirmeler için örtülü Kartezyen ürünler desteklenmez. Birleşimde sütunları kullanıyorsanız, benzersiz bir anahtar oluşturun.
- **Öneri**: eşitlik tabanlı olmayan BIRLEŞTIRMELER IÇIN çapraz birleştirme kullanın.

### <a name="error-code-getcommand-outputasync-failed"></a>Hata kodu: GetCommand OutputAsync başarısız oldu
- **İleti**: veri akışı hata ayıklama ve veri önizleme sırasında: GetCommand OutputAsync ile başarısız oldu...
- **Neden**: Bu hata bir arka uç hizmet hatasıdır. 
- **Öneri**: işlemi yeniden deneyin ve hata ayıklama oturumunuzu yeniden başlatın. Yeniden deneme ve yeniden başlatma sorunu çözmezse, müşteri desteğine başvurun. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Hata kodu: DF-yürütücü-OutOfMemoryError
 
- **İleti**: küme, yürütme sırasında yetersiz bellek sorunuyla karşılaştı, lütfen daha büyük çekirdek sayısı ve/veya bellek için iyileştirilmiş işlem türü ile bir tümleştirme çalışma zamanı kullanmayı deneyin
- **Neden**: kümede bellek kalmadı.
- **Öneri**: hata ayıklama kümelerinin geliştirilmesi için tasarlanmıştır. Yük çalıştırmak için veri örneklemesi ve uygun bir işlem türü ve boyutu kullanın. Performans ipuçları için bkz. [veri akışı performans kılavuzunu eşleme](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Hata kodu: DF-yürütücü-düzeneği Galargument

- **İleti**: lütfen bağlı hizmetinizdeki erişim anahtarının doğru olduğundan emin olun
- **Neden**: hesap adı veya erişim anahtarı yanlış.
- **Öneri**: bağlı hizmetinizde belirtilen hesap adının veya erişim anahtarının doğru olduğundan emin olun. 

### <a name="error-code-df-executor-columnunavailable"></a>Hata kodu: DF-yürütücü-ColumnUnavailable
- **İleti**: ifadede kullanılan sütun adı kullanılamıyor veya geçersiz.
- **Neden**: bir ifadede geçersiz veya kullanılamayan bir sütun adı kullanılıyor.
- **Öneri**: ifadelerde kullanılan sütun adlarını denetleyin.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Hata kodu: DF-yürütücü-OutOfDiskSpaceError
- **İleti**: iç sunucu hatası
- **Neden**: kümede disk alanı kalmadı.
- **Öneri**: işlem hattını yeniden deneyin. Bunu yapmak sorunu çözmezse müşteri desteği 'ne başvurun.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Hata kodu: DF-yürütücü-StoreIsNotDefined
- **İleti**: depo yapılandırması tanımlı değil. Bu hata, ardışık düzende geçersiz parametre atamasından kaynaklanıyor olabilir.
- **Neden**: belirlenmedi.
- **Öneri**: işlem hattının parametre değeri atamasını denetleyin. Bir parametre ifadesi geçersiz karakterler içeriyor olabilir.


### <a name="error-code-4502"></a>Hata kodu: 4502
- **İleti**: Integration Runtime altında azaltma nedeniyle hatalara neden olan çok fazla eşzamanlı MappingDataflow yürütmesi vardır.
- **Neden**: aynı anda çok sayıda veri akışı etkinliği çalıştırması, tümleştirme çalışma zamanı üzerinde eşzamanlı olarak gerçekleştirilir. Daha fazla bilgi için bkz. [Azure Data Factory sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Öneri**: daha fazla veri akışı etkinliğini paralel olarak çalıştırmak istiyorsanız, bunları birden çok tümleştirme çalışma zamanı arasında dağıtın.


### <a name="error-code-invalidtemplate"></a>Hata kodu: ınvalidtemplate
- **İleti**: ardışık düzen ifadesi değerlendirilemiyor.
- **Neden**: veri akışı etkinliğinde geçirilen ardışık düzen ifadesi bir sözdizimi hatası nedeniyle doğru işlenmiyor.
- **Öneri**: ifadeyi doğrulamak için etkinlik izlemede etkinliğinizi denetleyin.

### <a name="error-code-2011"></a>Hata kodu: 2011
- **İleti**: etkinlik Azure Integration Runtime çalışıyor ve veri deposunun kimlik bilgilerinin şifresini çözemedi veya şirket içinde barındırılan bir Integration Runtime ile bağlı işlem başarısız oldu. Lütfen bu etkinlikle ilişkili bağlı hizmetlerin yapılandırmasını denetleyin ve uygun tümleştirme çalışma zamanı türünü kullandığınızdan emin olun.
- **Neden**: veri akışı, şirket içinde barındırılan tümleştirme çalışma zamanları üzerinde bağlı Hizmetleri desteklemez.
- **Öneri**: veri akışını yönetilen bir sanal ağ tümleştirme çalışma zamanı üzerinde çalışacak şekilde yapılandırın.

### <a name="error-code-df-xml-invalidvalidationmode"></a>Hata kodu: DF-XML-ınvalidvalidationmode
- **İleti**: geçersiz XML doğrulama modu belirtildi.
- **Öneri**: parametre değerini denetleyin ve doğru doğrulama modunu belirtin.

### <a name="error-code-df-xml-invaliddatafield"></a>Hata kodu: DF-XML-ınvaliddatafield
- **İleti**: bozuk kayıtlar alanı dize türü ve null atanabilir olmalıdır.
- **Öneri**: `\"_corrupt_record\"` kaynak projedeki sütunun bir dize veri türüne sahip olduğundan emin olun.

### <a name="error-code-df-xml-malformedfile"></a>Hata kodu: DF-XML-MalformedFile
- **İleti**: ' FailFastMode ' içinde hatalı biçimlendirilmiş XML.
- **Öneri**: XML dosyasının içeriğini doğru biçimde güncelleştirin.

### <a name="error-code-df-xml-invaliddatatype"></a>Hata kodu: DF-XML-ınvaliddatatype
- **İleti**: XML öğesinde alt öğeler veya öznitelikler bulunur ve bu öğe dönüştürülemez.

### <a name="error-code-df-xml-invalidreferenceresource"></a>Hata kodu: DF-XML-InvalidReferenceResource
- **İleti**: XML veri dosyasındaki başvuru kaynağı çözümlenemiyor.
- **Öneri**: XML veri dosyasında başvuru kaynağını denetlemeniz gerekir.

### <a name="error-code-df-xml-invalidschema"></a>Hata kodu: DF-XML-ınvalidschema
- **İleti**: şema doğrulaması başarısız oldu.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Hata kodu: DF-XML-UnsupportedExternalReferenceResource
- **İleti**: XML veri dosyasında dış başvuru kaynağı desteklenmez.
- **Öneri**: dış başvuru kaynağı şu anda DESTEKLENMEDIĞINDEN XML dosya içeriğini güncelleştirin.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Hata kodu: DF-GEN2-ınvalidaccountconfiguration
- **İleti**: Account Key veya Tenant/SpnId/SpnCredential/spnCredentialType ya da Miservıceuri/Miservvoicetoken seçeneklerinden biri belirtilmelidir.
- **Öneri**: ilgili Gen2 bağlı hizmetinde doğru hesabı yapılandırın.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>Hata kodu: DF-GEN2-ınvalidauthconfiguration
- **İleti**: üç auth yönteminden yalnızca biri (anahtar, ServicePrincipal ve mı) belirtilebilir. 
- **Öneri**: ilgili Gen2 bağlantılı hizmetinde doğru kimlik doğrulama türünü seçin.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Hata kodu: DF-GEN2-ınvalidservicesprincipalcredentialtype
- **İleti**: serviceprincipalcredentialtype geçersiz.

### <a name="error-code-df-gen2-invaliddatatype"></a>Hata kodu: DF-GEN2-ınvaliddatatype
- **İleti**: bulut türü geçersiz.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>Hata kodu: DF-blob-ınvalidaccountconfiguration
- **İleti**: hesap anahtarından biri veya sas_token belirtilmelidir.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>Hata kodu: DF-blob-ınvalidauthconfiguration
- **İleti**: iki auth yönteminden (anahtar, SAS) yalnızca biri belirtilebilir.

### <a name="error-code-df-blob-invaliddatatype"></a>Hata kodu: DF-blob-ınvaliddatatype
- **İleti**: bulut türü geçersiz.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>Hata kodu: DF-Cosmos-Partitionkeykaçırılan
- **İleti**: güncelleştirme ve silme işlemleri için bölüm anahtarı yolu belirtilmelidir.
- **Öneri**: Cosmos havuz ayarları 'nda bölüm sağlama anahtarını kullanın.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Hata kodu: DF-Cosmos-ınvalidpartitionkey
- **İleti**: güncelleştirme ve silme işlemleri için bölüm anahtarı yolu boş olamaz.
- **Öneri**: Cosmos havuz ayarları 'nda bölüm sağlama anahtarını kullanın.

### <a name="error-code-df-cosmos-idpropertymissed"></a>Hata kodu: DF-Cosmos-ıdpropertykaçırıldı
- **İleti**: ' id ' özelliği, silme ve güncelleştirme işlemleri için eşlenmelidir.
- **Öneri**: giriş verilerinde `id` Cosmos havuz ayarlarında bir sütun bulunduğundan emin olun. Hayır ise, havuza başlamadan önce bu sütunu oluşturmak için **Select veya türeme dönüşümünü** kullanın.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Hata kodu: DF-Cosmos-ınvalidpartitionkeycontent
- **İleti**: bölüm anahtarı/ile başlamalıdır.
- **Öneri**: Bölüm anahtarını `/` Cosmos havuz ayarları 'nda başlangıç yapın, örneğin: `/movieId` .

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Hata kodu: DF-Cosmos-ınvalidpartitionkey
- **İleti**: partitionkey, DELETE ve Update işlemleri için havuzda eşlenmedi.
- **Öneri**: Cosmos havuz ayarları ' nda, kapsayıcının bölüm anahtarıyla aynı olan bölüm anahtarını kullanın.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>Hata kodu: DF-Cosmos-ınvalidconnectionmode
- **İleti**: geçersiz connectionmode.
- **Öneri**: desteklenen modun, Cosmos ayarlarında **ağ geçidi** ve **DirectHttps** olduğunu doğrulayın.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Hata kodu: DF-Cosmos-ınvalidaccountconfiguration
- **İleti**: AccountName ya da accountEndpoint belirtilmelidir.

### <a name="error-code-df-github-writenotsupported"></a>Hata kodu: DF-GitHub-WriteNotSupported
- **İleti**: GitHub Mağazası yazmaya izin vermez.

### <a name="error-code-df-pgsql-invalidcredential"></a>Hata kodu: DF-PGSQL-ınvalidcredential
- **İleti**: Kullanıcı/parola belirtilmelidir.
- **Öneri**: ilgili PostgreSQL bağlı hizmetinde kimlik bilgisi ayarlarının doğru olduğundan emin olun.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Hata kodu: DF-kar ke-ınvalidstageconfiguration
- **İleti**: yalnızca BLOB depolama türü, kar tanesi okuma/yazma işleminde aşama olarak kullanılabilir.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Hata kodu: DF-kar ke-ınvalidstageconfiguration
- **İleti**: Azure Blob + SAS kimlik doğrulamasıyla kar tanesi aşama özellikleri belirtilmelidir.

### <a name="error-code-df-snowflake-invaliddatatype"></a>Hata kodu: DF-kar tanesi-ınvaliddatatype
- **İleti**: Spark türü, kar tanesi içinde desteklenmez.
- **Öneri**: giriş verilerinin ilgili sütununu, kar havuzundan önce dize türüne dönüştürmek için **türet dönüşümünü** kullanın. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Hata kodu: DF-Hive-ınvalidblobstagingconfiguration
- **İleti**: BLOB depolama hazırlama özellikleri belirtilmelidir.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Hata kodu: DF-Hive-InvalidGen2StagingConfiguration
- **İleti**: ADLS 2. depolama hazırlama yalnızca hizmet sorumlusu anahtar kimlik bilgisini destekler.
- **Öneri**: hizmet sorumlusu anahtar kimlik bilgisini, hazırlama olarak kullanılan ADLS 2. bağlantılı hizmette uygulacağınızı onaylayın.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Hata kodu: DF-Hive-InvalidGen2StagingConfiguration
- **İleti**: ADLS 2. depolama hazırlama özellikleri belirtilmelidir. Key veya Tenant/spnId/spnKey ya da Miservıceuri/miServiceToken seçeneklerinden biri gereklidir.
- **Öneri**: ilgili ADLS 2. bağlı hizmetindeki Hive 'de hazırlama olarak kullanılan doğru kimlik bilgisini uygulayın. 

### <a name="error-code-df-hive-invaliddatatype"></a>Hata kodu: DF-Hive-ınvaliddatatype
- **İleti**: desteklenmeyen sütunlar.
- **Öneri**: giriş verileri sütununu Hive tarafından desteklenen veri türüyle eşleşecek şekilde güncelleştirin.

### <a name="error-code-df-hive-invalidstoragetype"></a>Hata kodu: DF-Hive-ınvalidstoragetype
- **İleti**: depolama türü blob veya Gen2 olabilir.

### <a name="error-code-df-delimited-invalidconfiguration"></a>Hata kodu: DF ile ayrılmış-ınvalidconfiguration
- **İleti**: boş satırlardan biri ya da özel üst bilgi belirtilmelidir.
- **Öneri**: CSV ayarlarında boş satırlar veya özel üstbilgiler belirtin.

### <a name="error-code-df-delimited-columndelimitermissed"></a>Hata kodu: DF-Delimited-Columnlımıterkaçırılan
- **İleti**: ayrıştırma için sütun sınırlayıcısı gereklidir.
- **Öneri**: CSV ayarlarınızda sütun sınırlayıcısı olduğunu onaylayın.

### <a name="error-code-df-mssql-invalidcredential"></a>Hata kodu: DF-MSSQL-ınvalidcredential
- **İleti**: user/PWD ya da Tenant/SpnId/spnKey ya da Miservıceuri/Miservvoicetoken seçeneklerinden biri belirtilmelidir.
- **Öneri**: ilgili MSSQL bağlantılı hizmetinde doğru kimlik bilgilerini uygulayın.

### <a name="error-code-df-mssql-invaliddatatype"></a>Hata kodu: DF-MSSQL-ınvaliddatatype
- **İleti**: desteklenmeyen alan (ler).
- **Öneri**: giriş VERISI sütununu MSSQL tarafından desteklenen veri türüyle eşleşecek şekilde değiştirin.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>Hata kodu: DF-MSSQL-ınvalidauthconfiguration
- **İleti**: üç auth yönteminden yalnızca biri (anahtar, ServicePrincipal ve mı) belirtilebilir.
- **Öneri**: ilgili MSSQL bağlantılı hizmetinde yalnızca üç auth yönteminden birini (anahtar, ServicePrincipal ve mı) belirtebilirsiniz.

### <a name="error-code-df-mssql-invalidcloudtype"></a>Hata kodu: DF-MSSQL-ınvalidcloudtype
- **İleti**: bulut türü geçersiz.
- **Öneri**: ilgili MSSQL bağlantılı hizmetinde bulut türünü denetleyin.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Hata kodu: DF-SQLDW-ınvalidblobstagingconfiguration
- **İleti**: BLOB depolama hazırlama özellikleri belirtilmelidir.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>Hata kodu: DF-SQLDW-ınvalidstoragetype
- **İleti**: depolama türü blob veya Gen2 olabilir.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Hata kodu: DF-SQLDW-InvalidGen2StagingConfiguration
- **İleti**: ADLS 2. depolama hazırlama yalnızca hizmet sorumlusu anahtar kimlik bilgisini destekler.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>Hata kodu: DF-SQLDW-ınvalidconfiguration
- **İleti**: ADLS 2. depolama hazırlama özellikleri belirtilmelidir. Key veya Tenant/spnId/spnCredential/spnCredentialType veya Miservıceuri/Miservvoicetoken seçeneklerinden biri gereklidir.

### <a name="error-code-df-delta-invalidconfiguration"></a>Hata kodu: DF-DELTA-ınvalidconfiguration
- **İleti**: zaman damgası ve sürüm aynı anda ayarlanamaz.

### <a name="error-code-df-delta-keycolumnmissed"></a>Hata kodu: DF-DELTA-Keycolumnkaçırılan
- **İleti**: insertable olmayan işlemler için anahtar sütun (ler) i belirtilmelidir.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>Hata kodu: DF-DELTA-ınvalidtableoperationsettings
- **İleti**: yeniden oluşturma ve kesme seçenekleri her ikisi de belirtilemez.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>Hata kodu: DF-Excel-Worksheetconfigkaçırıldı
- **İleti**: Excel sayfası adı veya dizini gereklidir.
- **Öneri**: parametre değerini denetleyin ve Excel verilerini okumak için sayfa adını ya da dizini belirtin.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Hata kodu: DF-Excel-ınvalidworksheetconfiguration
- **İleti**: Excel sayfası adı ve dizini aynı anda mevcut olamaz.
- **Öneri**: parametre değerini denetleyin ve Excel verilerini okumak için sayfa adını ya da dizini belirtin.

### <a name="error-code-df-excel-invalidrange"></a>Hata kodu: DF-Excel-ınvalidrange
- **İleti**: geçersiz Aralık belirtildi.
- **Öneri**: parametre değerini denetleyin ve şu başvuruya göre geçerli aralığı belirtin: [Azure Data Factory-Dataset Özellikler 'de Excel biçimi](./format-excel.md#dataset-properties).

### <a name="error-code-df-excel-worksheetnotexist"></a>Hata kodu: DF-Excel-WorksheetNotExist
- **İleti**: Excel çalışma sayfası yok.
- **Öneri**: parametre değerini denetleyin ve Excel verilerini okumak için geçerli sayfa adını ya da dizini belirtin.

### <a name="error-code-df-excel-differentschemanotsupport"></a>Hata kodu: DF-Excel-farklı Entde Manotsupport
- **İleti**: farklı şemaya sahip Excel dosyalarını okuma işlemi şu anda desteklenmiyor.

### <a name="error-code-df-excel-invaliddatatype"></a>Hata kodu: DF-Excel-ınvaliddatatype
- **İleti**: veri türü desteklenmiyor.

### <a name="error-code-df-excel-invalidfile"></a>Hata kodu: DF-Excel-ınvalidfile
- **İleti**: yalnızca. xlsx ve. xls desteklenirken geçersiz Excel dosyası sağlanır.


## <a name="miscellaneous-troubleshooting-tips"></a>Çeşitli sorun giderme ipuçları
- **Sorun**: beklenmeyen özel durum oluştu ve yürütme başarısız oldu.
    - **İleti**: veri akışı etkinlik yürütmesi sırasında: beklenmeyen bir özel durum ve yürütme başarısız oldu.
    - **Neden**: Bu hata bir arka uç hizmet hatasıdır. İşlemi yeniden deneyin ve hata ayıklama oturumunuzu yeniden başlatın.
    - **Öneri**: yeniden deneme ve yeniden başlatma sorunu çözmezse, müşteri desteğine başvurun.

-  **Sorun**: hata ayıklama veri önizlemesi sırasında birleşimde çıkış verisi yok.
    - **İleti**: çok az sayıda satır örneklenmemesinin nedeni büyük miktarda null değer veya eksik değer vardır. Hata ayıklama satırı sınırını güncelleştirmeyi ve verileri yenilemeyi deneyin.
    - **Neden**: JOIN koşulu hiçbir satırla eşleşmedi veya veri önizlemesi sırasında çok sayıda null değer ile sonuçlandı.
    - **Öneri**: **hata ayıklama ayarları**' nda, kaynak satır sınırdaki satır sayısını artırın. Daha fazla veri işlemek için yeterince büyük olan bir veri akışı kümesine sahip bir Azure IR seçtiğinizden emin olun.
    
- **Sorun**: çok satırlı CSV dosyaları olan kaynakta doğrulama hatası. 
    - **İleti**: şu hata iletilerinden birini görebilirsiniz:
        - Son sütun null veya eksik.
        - Kaynaktaki şema doğrulaması başarısız oluyor.
        - Şema içeri aktarma, UX 'de doğru şekilde gösterilemedi ve son sütunda adında yeni bir satır karakteri bulunur.
    - **Neden**: eşleme veri akışında, çok satırlı CSV kaynak dosyaları şu anda, \r\n satır sınırlayıcısı olarak kullanıldığında çalışmaz. Bazen satır dönüşlerinde ek satırlar hatalara neden olabilir. 
    - **Öneri**: \n 'yi, \ ' i kullanarak bir satır sınırlayıcısı olarak kullanarak kaynak üzerinde dosya oluşturun. Ya da CSV dosyasını bir satır sınırlayıcısı olarak \n kullanacak şekilde dönüştürmek için kopyalama etkinliğini kullanın.

## <a name="general-troubleshooting-guidance"></a>Genel sorun giderme kılavuzu
1. Veri kümesi bağlantılarınızın durumunu denetleyin. Her kaynak ve havuz dönüşümünde, kullanmakta olduğunuz her veri kümesi için bağlı hizmete gidin ve bağlantıları test edin.
2. Veri akışı tasarımcısında dosya ve tablo bağlantılarınızın durumunu denetleyin. Verilerinize erişebildiğinizden emin olmak için, hata ayıklama modunda kaynak dönüşümlerinizin **veri önizlemesi** ' ni seçin.
3. Her şey veri önizlemede doğru görünüyorsa, işlem hattı tasarımcısına gidin ve veri akışınızı bir işlem hattı etkinliğine yerleştirin. Uçtan uca bir test için işlem hattının hatalarını ayıklayın.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>Veri akışında CSV/CDM biçiminde geliştirme 

**Azure Data Factory v2 'de veri akışını eşlemek Için sınırlandırılmış metin veya CDM biçimlendirmesi** kullanırsanız, **1 Mayıs 2021**' den başlayarak veri akışında sınırlandırılmış metin/CDM 'nin geliştirmesi nedeniyle, davranış varolan işlem hatlarınız üzerinde değişir. 

İyileştirmadan önce aşağıdaki sorunlarla karşılaşabilirsiniz, ancak geliştirme sonrasında sorunlar düzeltildi. Bu iyileştirmesinin sizi etkileyip etkilemediğini anlamak için aşağıdaki içeriği okuyun. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>Senaryo 1: beklenmeyen satır sınırlayıcısı sorunuyla karşılaşıyorsunuz

 Aşağıdaki koşullarda siz etkilenmezsiniz:
 - Çok satırlı ayarı doğru veya kaynak olarak CDM olarak ayarlanan ayrılmış metni kullanma.
 - İlk satırda 128 karakterden fazlası vardır. 
 - Veri dosyalarındaki satır sınırlayıcısı değil `\n` .

 İyileştirmesinden önce, varsayılan satır sınırlayıcısı `\n` sınırlandırılmış metin dosyalarını ayrıştırmak için beklenmedik şekilde kullanılamıyor, çünkü çok satırlı ayar true olarak ayarlandığında, satır sınırlayıcı ayarını geçersiz kılar ve satır sınırlayıcısı ilk 128 karaktere göre otomatik olarak algılanır. Gerçek satır sınırlayıcısını algılayamazsa, öğesine geri dönecek `\n` .  

 Geliştirme sonrasında, üç satır sınırlayıcıdan herhangi biri: `\r` , `\n` , `\r\n` çalışması gerekir.
 
 Aşağıdaki örnek, geliştirme sonrasında bir işlem hattı davranışı değişikliğini gösterir:

 **Örnek**:<br/>
   Aşağıdaki sütun için:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   İyileştirmadan önce `\r` sütun değerinde tutulur. Ayrıştırılmış sütun sonucu:<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   İyileştirmelerden sonra, ayrıştırılmış sütun sonucu şu olmalıdır:<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>Senaryo 2: ' \r\n ' içeren sütun değerlerini yanlış okuma sorunuyla karşılaşıyorsunuz

 Aşağıdaki koşullarda siz etkilenmezsiniz:
 - Çok satırlı ayarı doğru veya kaynak olarak CDM olarak ayarlanan ayrılmış metni kullanma. 
 - Satır sınırlayıcısı `\r\n` .

 İyileştirmadan önce, sütun değerini okurken, `\r\n` içindeki içinde hatalı olarak değiştirilmiş olabilir `\n` . 

 Gelişmelerden sonra, `\r\n` sütun değerindeki değeri ile değiştirilmez `\n` .

 Aşağıdaki örnek, geliştirme sonrasında bir işlem hattı davranışı değişikliğini gösterir:
 
 **Örnek**:<br/>
  
 Aşağıdaki sütun için:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 İyileştirmadan önce, ayrıştırılmış sütun sonucu:<br/>
  **`A\n`**` B C`<br/>

 İyileştirmelerden sonra, ayrıştırılmış sütun sonucu şu olmalıdır:<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>Senaryo 3: ' \n ' içeren sütun değerlerini yanlış yazma sorunuyla karşılaşıyorsunuz

 Aşağıdaki koşullarda siz etkilenmezsiniz:
 - Ayrılmış metni havuz olarak kullanma.
 - Sütun değeri içerir `\n` .
 - Satır sınırlayıcısı olarak ayarlanır `\r\n` .
 
 İyileştirmadan önce, sütun değerini yazarken, `\n` içindeki içinde hatalı olarak değiştirilmiş olabilir `\r\n` . 

 Gelişmelerden sonra, `\n` sütun değerindeki değeri ile değiştirilmez `\r\n` .
 
 Aşağıdaki örnek, geliştirme sonrasında bir işlem hattı davranışı değişikliğini gösterir:

 **Örnek**:<br/>

 Aşağıdaki sütun için:<br/>
 **`A\n`**` B C`<br/>

 Geliştirme öncesinde CSV havuzu şu şekilde olur:<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 Geliştirme sonrasında CSV havuzunun olması gerekir:<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>Senaryo 4: boş dizeyi NULL olarak okumada bir sorunla karşılaşıyorsunuz
 
 Aşağıdaki koşullarda siz etkilenmezsiniz:
 - Ayrılmış metni kaynak olarak kullanma. 
 - NULL değeri boş olmayan bir değer olarak ayarlanmış. 
 - Sütun değeri boş bir dizedir ve tırnak içine alınmış. 
 
 İyileştirmadan önce, tırnak işaretsiz boş dizenin sütun değeri NULL olarak okunurdur. 

 Geliştirme sonrasında boş dize NULL değer olarak ayrıştırılmaz. 
 
 Aşağıdaki örnek, geliştirme sonrasında bir işlem hattı davranışı değişikliğini gösterir:

 **Örnek**:<br/>

 Aşağıdaki sütun için:<br/>
  `A, ,B, `<br/>

 İyileştirmadan önce, ayrıştırılmış sütun sonucu:<br/>
  `A null B null`<br/>

 İyileştirmelerden sonra, ayrıştırılmış sütun sonucu şu olmalıdır:<br/>
  `A "" (empty string) B "" (empty string)`<br/>


## <a name="next-steps"></a>Sonraki adımlar

Sorun giderme konusunda daha fazla yardım için şu kaynaklara bakın:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)
