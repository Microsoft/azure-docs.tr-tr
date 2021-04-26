---
title: Azure Cosmos DB öykünücü indirme ve sürüm notları
description: Farklı sürümler ve indirme bilgileri için Azure Cosmos DB öykünücü sürüm notlarını alın.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 442fa1c76d4667a54f1c13b411b1c052c9a5b134
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566651"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB öykünücü-sürüm notları ve indirme bilgileri
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bu makalede, Azure Cosmos DB öykünücü sürüm notları her sürümde yapılan Özellik güncelleştirmelerinin listesi ile gösterilir. Ayrıca, indirme ve kullanma öykünücünün en son sürümünü de listeler.

## <a name="download"></a>İndir

| | Bağlantı |
|---------|---------|
|**MSI indirme**|[Microsoft İndirme Merkezi](https://aka.ms/cosmosdb-emulator)|
|**Kullanmaya başlama**|[Azure Cosmos DB öykünücü ile yerel olarak geliştirme](local-emulator.md)|

## <a name="release-notes"></a>Sürüm notları

### <a name="21111-22-february-2021"></a>2.11.11 (22 Şubat 2021)

 - Bu sürüm, yerel Veri Gezgini içeriğini en son Azure portal sürümüne güncelleştirir.


### <a name="21110-5-january-2021"></a>2.11.10 (5 Ocak 2021)

 - Bu sürüm, yerel Veri Gezgini içeriğini en son Azure portal sürümüne güncelleştirir ve öykünücü kullanıcının genel öykünücü sertifikasını bir olarak doğrudan dışarı aktarmaya izin veren "/ExportPemCert" adlı yeni bir ortak seçenek ekler. PEK dosyası.

### <a name="2119-3-december-2020"></a>2.11.9 (3 Aralık 2020)

 - Bu sürüm, Azure Cosmos DB ' deki en son özellikleri ve geliştirmeleri yansıtan genel içerik güncelleştirmesine ek olarak Azure Cosmos DB öykünücü işlevselliğiyle ilgili her iki sorunu ele alınmaktadır:
 * Doğrudan mod ve Java istemci uygulamaları kullanılırken büyük belge yükü isteklerinin başarısız olduğu bir sorunu giderme.
 * .NET tabanlı uygulamalar tarafından hedeflenen MongoDB uç noktası sürüm 3,6 ile bağlantı sorunu için çözüm.

### <a name="2118-6-november-2020"></a>2.11.8 (6 Kasım 2020)

 - Bu sürüm, Cosmos öykünücüsü Veri Gezgini bir güncelleştirme içerir ve TLS 1,3 istemcilerinin Veri Gezgini açmaya çalıştığı bir sorunu düzeltir.

### <a name="2116-6-october-2020"></a>2.11.6 (6 Ekim 2020)

 - Bu sürüm, aynı anda birden çok kapsayıcı oluşturulanışında eşzamanlılık ile ilgili bir sorunu ele alınmaktadır. Bu tür durumlarda, öykünücü verileri bozulmuş bir durumda bırakılır ve öykünücü uç noktasına yönelik API isteklerinin aşağıdaki "hizmet kullanılamıyor" hatalarıyla başarısız olması gerekir. yeniden başlatma ve öykünücü yerel verilerinin sıfırlanması.

### <a name="2115-23-august-2020"></a>2.11.5 (23 Ağustos 2020)

Bu sürüm iki yeni Cosmos öykünücü başlatma seçeneği ekler: 

* "/EnablePreview"-öykünücü için Önizleme özellikleri sunar. Hala geliştirme aşamasında olan ve bunlara CI ve örnek yazma aracılığıyla erişilebilen Önizleme özellikleri.
* "/EnableAadAuthentication"-öykünücü, Azure Cosmos birincil anahtarlarına alternatif olarak özel Azure Active Directory belirteçlerini kabul etmesine olanak sağlar. Bu özellik hala geliştirme aşamasındadır; belirli rol atamaları ve diğer izinlerle ilgili ayarlar şu anda desteklenmemektedir.

### <a name="2112-07-july-2020"></a>2.11.2 (07 Temmuz 2020)

- Bu sürüm, Cosmos öykünücüsünün sorunlarını giderirken ETL izlemelerinin toplanması için gereken değişiklikleri değiştirir. WPR (Windows performans çalışma zamanı araçları) artık, eski LOGMAN tabanlı yakalama kullanım dışı durumdayken ETL tabanlı izlemeleri yakalamaya yönelik varsayılan araçlardır. En son Windows güvenlik güncelleştirmelerinde, Cosmos öykünücüsü aracılığıyla yürütüldüğünde, LOGMAN 'in nasıl çalıştığı konusunda beklenmedik bir etkiye sahip olduğundan bu değişiklik kısmen zorunludur.

### <a name="2111-10-june-2020"></a>2.11.1 (10 Haziran 2020)

- Bu sürüm, öykünücü Veri Gezgini ilgili birkaç hatayı düzeltir. Bir Web tarayıcısı aracılığıyla öykünücü Veri Gezgini kullanılırken, Cosmos öykünücü uç noktasına bağlanamaz ve bir veritabanı ya da kapsayıcı oluşturma gibi tüm ilgili eylemler hataya neden olur. Düzeltilen ikinci sorun, Veri Gezgini karşıya yükleme eylemini kullanarak bir JSON dosyasından bir öğe oluşturmakla ilgilidir.

### <a name="2110"></a>2.11.0

- Bu sürüm, otomatik ölçeklendirme sağlanan aktarım hızı için destek sunar. Bu yeni özellikler, istek birimleri (RU/s) içinde özel bir en yüksek sağlanan aktarım hızı düzeyi ayarlamanıza, mevcut veritabanlarında ve kapsayıcılarda otomatik ölçeklendirmeyi etkinleştirmesine ve Azure Cosmos DB SDK 'lar aracılığıyla programlı destek sağlanmasına olanak tanır.
- Çok sayıda belge (1 GB üzerinde) sorgulanırken bir sorunu çözme, öykünücü iç hata durum kodu 500 ile başarısız olur.

### <a name="292"></a>2.9.2

- Bu sürüm, MongoDb uç noktası sürüm 3,2 desteğini etkinleştirirken bir hatayı düzeltir. Ayrıca, LOGMAN yerine WPR kullanarak sorun giderme amacıyla ETL izlemeleri oluşturmaya yönelik destek ekler.

### <a name="291"></a>2.9.1

- Bu sürüm, sorgu API 'SI desteğinin her iki sorununu düzeltir ve Windows Server 2012 gibi eski OSs ile uyumluluğu geri yükler.

### <a name="290"></a>2.9.0

- Bu sürüm, tutarlılığı tutarlı ön ek olarak ayarlama ve kullanıcılar ve izinler için maksimum sınırları artırma seçeneğini ekler.

### <a name="272"></a>2.7.2

- Bu sürüm, Cosmos öykünücüsüne MongoDB sürüm 3,6 sunucu desteği ekler. Hizmetin 3,6 sürümünü hedef alan bir MongoDB uç noktasını başlatmak için, "/EnableMongoDBEndpoint = 3.6" seçeneğiyle yönetici komut satırından öykünücü 'yı başlatın.

### <a name="270"></a>2.7.0

- Bu sürüm, .NET Core veya x86 .NET tabanlı istemciler kullanılırken kullanıcıların öykünücü aracılığıyla SQL API hesabına karşı sorgu yürütmesini önleyen bir gerileme düzeltir.

### <a name="246"></a>2.4.6

- Bu sürüm, [Azure Cosmos DB öykünücü ile yerel olarak geliştirme](local-emulator.md)bölümünde belirtilen özel durumlar Ile 2019 Temmuz Itibariyle Azure Cosmos hizmetindeki özelliklerle eşlik sağlar. Ayrıca, komut satırı ve doğrudan mod bağlantısı kullanan SDK istemcileri için iç IP adresi geçersiz kılmaları aracılığıyla çağrıldığında öykünücü kapanıyla ilgili çeşitli hataları düzeltir.

### <a name="243"></a>2.4.3

- MongoDB hizmetinin varsayılan olarak başlatılması devre dışı bırakıldı. Yalnızca SQL uç noktası varsayılan olarak etkindir. Kullanıcı, öykünücü "/EnableMongoDbEndpoint" komut satırı seçeneğini kullanarak uç noktayı el ile başlatmalıdır. Artık Gremlin, Cassandra ve Table gibi diğer tüm hizmet uç noktalarına benzer.
- Gremlin, Cassandra ve tablo uç noktalarının dış istemcilerden gelen istekleri doğru bir şekilde işleyemediğinde "/AllowNetworkAccess" ile Başlarken öykünücüsünde hata düzeltildi.
- Güvenlik duvarı kuralları ayarlarına doğrudan bağlantı bağlantı noktaları ekleyin.

### <a name="240"></a>2.4.0

- Ana bilgisayarda Pulse Istemcisi gibi ağ izleme uygulamaları varsa öykünücü ile ilgili bir sorun düzeltildi.
