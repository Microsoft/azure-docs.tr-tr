---
title: Mongo DB için Azure Cosmos DB API 'sindeki yaygın hataların sorunlarını giderme
description: Bu belgede, MongoDB için Azure Cosmos DB API 'sinde karşılaşılan yaygın sorunları gidermeye yönelik yollar ele alınmaktadır.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692248"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sindeki yaygın sorunları giderme
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Aşağıdaki makalede, MongoDB için Azure Cosmos DB API kullanılarak gerçekleştirilen dağıtımlar için yaygın hatalar ve çözümler açıklanmaktadır.

>[!Note]
> Azure Cosmos DB MongoDB altyapısını barındırmıyor. MongoDB [kablo protokol sürümü 4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)ve [tel protokol sürüm 3,2](mongodb-feature-support.md)için eski destek desteği sunar. Bu nedenle, bu hatalardan bazıları yalnızca MongoDB için Azure Cosmos DB API 'sinde bulunur.

## <a name="common-errors-and-solutions"></a>Genel hatalar ve çözümleri

| Kod       | Hata                | Açıklama  | Çözüm  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | Bunun yaygın bir nedeni, belirtilen öğeye göre sipariş değerine karşılık gelen dizin yolu dışlanması veya sıralama ölçütü sorgusunun hizmet verebilecek ilgili bileşik dizini olmamasıdır. Sorgu dizine alınmamış bir alana göre sıralama istiyor. | Denenen sıralama sorgusu için eşleşen bir dizin (veya bileşik dizin) oluşturun. |
| 2 | İşlem etkin değil | Çok belgeli işlem 5 saniyelik sabit süre sınırını aştı. | Çok belgeli işlemi yeniden deneyin veya çok belgeli işlemin içinde yer alan işlemlerin kapsamını sınırlayarak 5 saniyelik zaman sınırında tamamlanmasını sağlayın. |
| 13 | Yetkisiz | İsteğin tamamlanması için gerekli izinler yok. | Doğru anahtarları kullandığınızdan emin olun.  |
| 26 | NamespaceNotFound | Sorguda başvurulan veritabanı veya koleksiyon bulunamadı. | Veritabanı/Koleksiyon adınızın sorgunuzdakiyle bire bir eşleştiğinden emin olun.|
| 50 | ExceededTimeLimit | İstek 60 saniyelik yürütme zaman aşımı süresini aştı. |  Bu hatanın birçok nedeni olabilir. Nedenlerinden biri geçerli ayrılmış istek birimi kapasitesinin isteği tamamlamak için yeterli olmamasıdır. Bu durum söz konusu koleksiyonun veya veritabanının istek birimlerini artırarak çözülebilir. Diğer durumlarda, büyük bir istek daha küçük isteklere bölünerek bu hatadan kaçınılabilir. Bu hatayı alan yazma işlemini yeniden denemek, yinelenen yazma işlemine neden olabilir. <br><br>RU’ları etkilemeden büyük miktarda veri silmeye çalışıyorsanız: <br>- TTL'yi kullanmayı düşünün (Zaman Damgası temelinde): [MongoDB için Azure Cosmos DB API'siyle verilerde süre sonu](mongodb-time-to-live.md) <br>- Silme işlemini gerçekleştirmek için İmleç/Toplu İş boyutunu kullanın. Tek seferde tek bir belge getirip bir döngü aracılığıyla bunu silebilirsiniz. Bu, üretim uygulamanızı etkilemeden verileri yavaş bir şekilde silmenize yardımcı olur.|
| 61 | ShardKeyNotFound | İsteğinizdeki belge, koleksiyonun parça anahtarını (Azure Cosmos DB bölüm anahtarı) içermiyor. | Koleksiyonun parça anahtarının istekte kullanıldığından emin olun.|
| 66 | ImmutableField | İstek, sabit bir alanı değiştirmeye çalışıyor | "_id" alanları sabittir. İsteğinizin bu alanı veya parça anahtarı alanını güncelleştirmeye çalışmadığından emin olun. |
| 67 | CannotCreateIndex | Dizin oluşturma isteği tamamlanamıyor. | Bir kapsayıcıda en fazla 500 tek alan dizini oluşturulabilir. Bileşik dizinde en fazla sekiz alan bulunabilir (bileşik dizinler 3.6+ sürümünde desteklenir). |
| 112 | WriteConflict | Çok belgeli işlem çakışan başka bir çok belgeli işlem nedeniyle başarısız oldu | Çok belgeli işlemi başarılı olana kadar yeniden deneyin. |
| 115 | CommandNotSupported | Denenen istek desteklenmiyor. | Hatada ek ayrıntılar sağlanıyor olmalıdır. Bu işlevsellik dağıtımlarınız için önemliyse, [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bir destek bileti oluşturun ve Azure Cosmos DB ekibi size geri dönecek. |
| 11000 | DuplicateKey | Eklediğiniz belgenin parça anahtarı (Azure Cosmos DB bölüm anahtarı) koleksiyonda zaten var veya bir benzersiz dizin alanı kısıtlaması ihlal edildi. | Mevcut belgeyi güncelleştirmek için update() işlevini kullanın. Benzersiz dizin alanı kısıtlaması ihlal edildiyse, parçada/bölümde var olmayan bir alan değeri ekleyin veya belgeyi bu değerle güncelleştirin. Bir diğer seçenek de kimlik ve parça anahtarı alanlarının bileşimini içeren bir alan kullanmaktır. |
| 16500 | TooManyRequests  | Kullanılan toplam istek birimi sayısı, koleksiyon için sağlanan istek birimi hızından fazla ve kısıtlanmış. | Azure portaldan bir kapsayıcıya veya kapsayıcı kümesine atanan aktarım hızını ölçeklendirmeyi göz önünde bulundurabilir veya işlemi yeniden deneyebilirsiniz. SSR’yi (sunucu tarafı yeniden denemesi) etkinleştirirseniz Azure Cosmos DB otomatik olarak bu hata nedeniyle başarısız olan istekleri yeniden dener. |
| 16501 | ExceededMemoryLimit | Çok kiracılı bir hizmet olarak, işlem istemcinin bellek hizmet birimini aşıyor. Bu yalnızca MongoDB sürüm 3.2 için Azure Cosmos DB API'sinde geçerlidir. | Daha kısıtlayıcı sorgu ölçütleriyle işlemin kapsamını daraltın veya [Azure portaldan](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) destekle iletişime geçin. Örnek: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Tanınmayan işlem hattı aşaması adı. | Toplama işlem hattı isteğinizdeki aşama adı tanınmadı. | İsteğinizdeki tüm toplama işlem hattı adlarının geçerli olduğundan emin olun. |
| - | MongoDB kablo sürümü sorunları | MongoDB sürücülerinin eski sürümleri bağlantı dizelerinde Azure Cosmos hesabının adını algılayamıyor. | `appName=@accountName@`Bağlantı dizeniz sonunda `accountName` Azure Cosmos DB hesap adınızın sonuna ekleyin. |
| - | MongoDB istemci ağı sorunları (yuva veya endOfStream özel durumları gibi)| Ağ isteği başarısız oldu. Bu genellikle MongoDB istemcisinin kullanmaya çalıştığı bir TCP bağlantısının etkin olmamasından kaynaklanır. MongoDB sürücüleri genellikle bağlantı havuzunu kullanır ve bu, istek için havuzdan seçilen rastgele bir bağlantının kullanılmasıyla sonuçlanır. Etkin olmayan bağlantılar normalde Azure Cosmos DB ucunda dört dakika sonra zaman aşımına uğrar. | Bu başarısız istekleri uygulama kodunuzda yeniden deneyebilir, MongoDB istemci (sürücü) ayarlarınızı dört dakikalık zaman aşımı penceresinden önce etkin olmayan TCP bağlantılarını kapatacak şekilde değiştirebilir veya işletim sisteminizin `keepalive` ayarlarını TCP bağlantılarını etkin durumda tutmak üzere yapılandırabilirsiniz.<br><br>Bağlantı iletilerini önlemek için, bağlantı dizesini değiştirip `maxConnectionIdleTime` değerini 1-2 dakika olarak ayarlamayı isteyebilirsiniz.<br>- Mongo sürücüsü: `maxIdleTimeMS=120000` yapılandırması uygulayın <br>- Node.JS: `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 minutes yapılandırması uygulayın
| - | Mongo Kabuğu Azure portalda çalışmıyor | Kullanıcı Mongo kabuğunu açmayı denediğinde hiçbir şey olmuyor ve sekme boş kalıyor.  | Güvenlik Duvarı’nı denetleyin. Azure portalda Mongo kabuğuyla güvenlik duvarı desteklenmez. <br>- Mongo kabuğunu güvenlik duvarı kuralları kapsamında yerel bilgisayara yükleyin <br>- Eski Mongo kabuğunu kullanın
| - | Bağlantı dizesiyle bağlanılamıyor  | Bağlantı dizesi 3.2’den 3.6’ya yükseltildiğinde değişti | MongoDB hesapları için Azure Cosmos DB'nin API'sini kullanırken, hesapların 3.6 sürümünün uç noktası `*.mongo.cosmos.azure.com` biçimindeyken, hesapların 3.2 sürümünün uç noktası `*.documents.azure.com` biçimindedir.  

## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'SI ile [Robo 3T kullanmayı](mongodb-robomongo.md) öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
