---
title: "Öğretici: MongoDB 'yi MongoDB için Azure Cosmos DB API 'sine çevrimdışına geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi MongoDB 'den MongoDB için Azure Cosmos DB API 'sine geçiş yapın.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443876"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Öğretici: MongoDB 'yi MongoDB için Azure Cosmos DB API 'ye geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak MongoDB 'nin şirket içi veya bulut örneğinden MongoDB için Azure Cosmos DB API 'sine yönelik çevrimdışı, tek seferlik bir geçiş işlemi gerçekleştirin.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.

Bu öğreticide, bir Azure sanal makinesinde barındırılan MongoDB içindeki bir veri kümesini geçirolursunuz. Azure veritabanı geçiş hizmeti 'ni kullanarak, veri kümesini MongoDB için Azure Cosmos DB API 'sine geçirolursunuz. Zaten bir MongoDB kaynağınız yoksa, bkz. [Azure 'Da WINDOWS VM 'de MongoDB 'Yi kurma ve yapılandırma](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* Aktarım hızını tahmin etme ve bölüm anahtarı seçme gibi [geçiş öncesi adımları doldurun](../cosmos-db/mongodb-pre-migration.md) .
* [MongoDB için Azure Cosmos DB API 'si için bir hesap oluşturun](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Azure veritabanı geçiş hizmeti için Azure Resource Manager kullanarak Microsoft Azure Sanal Ağ oluşturun. Bu dağıtım modeli, [Azure ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınız için siteden siteye bağlantı sağlar. Sanal ağ oluşturma hakkında daha fazla bilgi için [Azure sanal ağ belgelerine](../virtual-network/index.yml), özellikle de adım adım ayrıntılara sahip "hızlı başlangıç" makalelerine bakın.

    > [!NOTE]
    > Sanal ağ kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
    >
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası veya Azure Cosmos DB uç noktası)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* Sanal ağınız için ağ güvenlik grubu (NSG) kurallarınızın şu iletişim bağlantı noktalarını engellemediğinden emin olun: 53, 443, 445, 9354 ve 10000-20000. Daha fazla bilgi için bkz. [Ağ güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Azure veritabanı geçiş hizmeti 'nin kaynak MongoDB sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 27017 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.

## <a name="configure-the-server-side-retry-feature"></a>Sunucu tarafı yeniden deneme özelliğini yapılandırma

MongoDB 'den Azure Cosmos DB 'e geçiş yapıyorsanız kaynak idare özelliğinden yararlanabilirsiniz. Bu özelliklerde, sağlanan istek birimlerinizin (RU/s) aktarım hızını tam olarak kullanabilirsiniz. Azure Cosmos DB, bu istek kapsayıcı tarafından sağlanan RU/s 'yi aşarsa geçiş sırasında belirli bir veritabanı geçiş hizmeti isteğini kısıtlayabilir. Daha sonra bu isteğin yeniden denenilmesi gerekir.

Veritabanı geçiş hizmeti yeniden deneme gerçekleştirebilir. Veritabanı geçiş hizmeti ve Azure Cosmos DB arasındaki gidiş dönüş süresinin, bu isteğin genel yanıt süresini etkilediği anlaşılması önemlidir. Kısıtlanmış istekler için yanıt süresini iyileştirmek, geçiş için gereken toplam süreyi kısaltabilir.

Sunucu tarafı yeniden deneme özelliği Azure Cosmos DB, hizmetin kısıtlama hata kodlarını kesmesini ve daha sonra istek yanıt sürelerini önemli ölçüde iyileştirerek çok daha düşük bir gidiş dönüş süresi ile yeniden denemesini sağlar.

Sunucu tarafı yeniden denemesini kullanmak için Azure Cosmos DB portalında **Özellikler**  >  **sunucu tarafı yeniden dene**' yi seçin.

![Sunucu tarafı yeniden deneme özelliğinin nerede bulunacağını gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Özellik devre dışıysa **Etkinleştir**' i seçin.

![Sunucu tarafı yeniden denemesini nasıl etkinleştireceğinizi gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısının nasıl kaydedileceği gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Örnek oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market'i gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure veritabanı geçiş hizmeti örneğinin nasıl oluşturulacağını gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. **Geçiş hizmeti oluştur**' da hizmet, abonelik ve yeni veya mevcut bir kaynak grubu için bir ad belirtin.

4. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin. 

5. Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun.

    Sanal ağ, kaynak MongoDB örneğine ve hedef Azure Cosmos DB hesabına erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için, bkz. [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md).

6. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure veritabanı geçiş hizmeti örneği için yapılandırma ayarlarını gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmeti oluşturduktan sonra, Azure portal içinde bulun ve açın. Ardından yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

      ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerinin nasıl bulunacağını gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **Azure veritabanı geçiş Hizmetleri** ekranında, oluşturduğunuz Azure veritabanı geçiş hizmeti örneğinin adını arayın ve ardından örneği seçin.

3. **+ Yeni geçiş projesi** seçin.

4. **Yeni geçiş projesinde**, proje için bir ad belirtin ve **kaynak sunucu türü** metin kutusunda **MongoDB**' yi seçin. **Hedef sunucu türü** metin kutusunda **Cosmosdb (MONGODB API)** öğesini seçin ve ardından **etkinlik türü seç**' i seçerek **çevrimdışı veri geçişi**' ni seçin. 

    ![Proje seçeneklerini gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Projeyi oluşturmak ve geçiş etkinliğini çalıştırmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Kaynak ayrıntıları** ekranında, kaynak MongoDB sunucusu için bağlantı ayrıntılarını belirtin.

   > [!IMPORTANT]
   > Azure veritabanı geçiş hizmeti kaynak olarak Azure Cosmos DB desteklemiyor.

    Bir kaynağa bağlanmak için üç mod vardır:
   * Tam etki alanı adı veya IP adresi, bağlantı noktası numarası ve bağlantı kimlik bilgilerini kabul eden **Standart mod**.
   * Bağlantı dizesi [URI biçiminde](https://docs.mongodb.com/manual/reference/connection-string/)açıklandığı şekilde bir MongoDB bağlantı dizesi kabul eden **bağlantı dizesi modu**.
   * Blob kapsayıcı SAS URL 'sini kabul eden **Azure Storage verileri**. Blob kapsayıcısının MongoDB [bsondump aracı](https://docs.mongodb.com/manual/reference/program/bsondump/)tarafından oluşturulan bSon dökümlerini **içermesi durumunda blob bSon dökümleri içerir** ' i seçin. Kapsayıcı JSON dosyaları içeriyorsa bu seçeneği seçmeyin.

     Bu seçeneği belirlerseniz, depolama hesabı bağlantı dizesinin aşağıdaki biçimde göründüğünden emin olun:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Bu blob kapsayıcısı SAS bağlantı dizesini Azure Storage Explorer 'da bulabilirsiniz. İlgili kapsayıcı için SAS oluşturulması istenen biçimde URL 'YI sağlar.
     
     Ayrıca, Azure Storage 'daki tür dökümü bilgilerine göre şunları göz önünde bulundurun:

     * BSON dökümler için, blob kapsayıcısı içindeki verilerin bsondump biçiminde olması gerekir. Veri dosyalarını, *koleksiyon. bSon* biçiminde kapsayan veritabanlarının ardından adlandırılan klasörlere yerleştirin. *collection.metadata.json* biçimini kullanarak tüm meta veri dosyalarını adlandırın.

     * JSON dökümlerinde, blob kapsayıcısındaki dosyaların, kapsayan veritabanlarının ardından adlandırılan klasörlere yerleştirilmesi gerekir. Her veritabanı klasörü içinde, veri dosyalarının *veri* adlı bir alt klasöre yerleştirilmesi ve *collection.jsüzerinde* biçim kullanılarak adlandırılması gerekir. Tüm meta veri dosyalarını *meta veriler* adlı bir alt klasöre yerleştirin ve ' de aynı biçimi kullanarak adı *collection.js*. Meta veri dosyaları MongoDB bsondump aracı tarafından oluşturulan biçimde olmalıdır.

    > [!IMPORTANT]
    > MongoDB sunucusunda otomatik olarak imzalanan bir sertifika kullanmanızı önermiyoruz. Bir tane kullanmanız gerekiyorsa bağlantı dizesi modunu kullanarak sunucuya bağlanın ve bağlantı dizeniz tırnak işaretleri ("") olduğundan emin olun.
    >
    >```
    >&sslVerifyCertificate=false
    >```

   IP adresini, DNS ad çözümlemesi mümkün olmayan durumlar için de kullanabilirsiniz.

   ![Kaynak ayrıntılarını belirtmeyi gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** ekranında, hedef Azure Cosmos DB hesabının bağlantı ayrıntılarını belirtin. Bu hesap, MongoDB verilerinizi geçirdiğiniz MongoDB hesabı için önceden sağlanmış Azure Cosmos DB API 'sidir.

    ![Hedef ayrıntılarını belirtmeyi gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **Kaydet**’i seçin.

## <a name="map-to-target-databases"></a>Hedef veritabanlarıyla eşleyin

1. **Hedef veritabanlarına eşle** ekranında, geçiş için kaynağı ve hedef veritabanını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    Veritabanı adının yanında **Oluştur** görünürse, Azure veritabanı geçiş hizmeti 'nin hedef veritabanını bulmadığını ve hizmetin sizin için veritabanını oluşturacağını belirtir.

    Geçişin bu noktasında [üretilen iş sağlama](../cosmos-db/set-throughput.md)sağlayabilirsiniz. Azure Cosmos DB, veritabanı düzeyinde ya da her bir koleksiyon için ayrı ayrı üretilen iş sağlama sağlayabilirsiniz. Aktarım hızı, [İstek birimleri](../cosmos-db/request-units.md)cinsinden ölçülür. [Azure Cosmos DB fiyatlandırması](https://azure.microsoft.com/pricing/details/cosmos-db/)hakkında daha fazla bilgi edinin.

    ![Hedef veritabanlarına eşlemeyi gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **Kaydet**’i seçin.
3. **Koleksiyon ayarı** ekranında koleksiyonlar listesini genişletin ve geçirilecek koleksiyonların listesini gözden geçirin.

    Azure veritabanı geçiş hizmeti, hedef Azure Cosmos DB hesabında mevcut olmayan kaynak MongoDB örneğinde mevcut olan tüm koleksiyonları otomatik olarak seçer. Zaten veri içeren koleksiyonları yeniden geçirmek istiyorsanız, bu bölmedeki koleksiyonları açıkça seçmeniz gerekir.

    Koleksiyonların kullanmasını istediğiniz RUs sayısını belirtebilirsiniz. Azure veritabanı geçiş hizmeti, koleksiyon boyutuna bağlı olarak akıllı varsayılanlar önerir.

    > [!NOTE]
    > Veritabanı geçişini ve toplamayı paralel olarak gerçekleştirin. Gerekirse, çalıştırmayı hızlandırmak için Azure veritabanı geçiş hizmeti 'nin birden çok örneğini kullanabilirsiniz.

    Ayrıca, en iyi ölçeklenebilirlik için [Azure Cosmos DB bölümlemeden](../cosmos-db/partitioning-overview.md) yararlanmak üzere bir parça anahtarı belirtebilirsiniz. Parça [/bölüm anahtarı seçmek için en iyi uygulamaları](../cosmos-db/partitioning-overview.md#choose-partitionkey)gözden geçirin.

    ![Koleksiyon tablolarını seçmeyi gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **Kaydet**’i seçin.

5. **Geçiş özeti** ekranının **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin.

    ![Nibir özetini gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

**Geçişi çalıştır**'ı seçin. Geçiş etkinliği penceresi görüntülenir ve etkinliğin durumu **başlatılmaz**.

![Etkinlik durumunu gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Geçişi izleme

Geçiş etkinliği ekranında, geçişin durumu **tamamlandı** olarak gösterilene kadar görünümü güncelleştirmek için **Yenile** ' yi seçin.

> [!NOTE]
> Veritabanı ve koleksiyon düzeyinde geçiş ölçümlerinin ayrıntılarını almak için etkinliğini seçebilirsiniz.

![Etkinlik durumunun tamamlandığını gösteren screnshot.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Azure Cosmos DB verileri doğrulama

Geçiş bittikten sonra, tüm koleksiyonların başarıyla geçirildiğini doğrulamak için Azure Cosmos DB hesabınızı kontrol edebilirsiniz.

![Tüm koleksiyonların başarıyla geçirildiğini doğrulamak üzere Azure Cosmos DB hesabınızın nerede kontrol ettiğini gösteren ekran görüntüsü.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Geçiş sonrası iyileştirmesi

MongoDB veritabanında depolanan verileri MongoDB için Azure Cosmos DB API 'sine geçirdikten sonra, Azure Cosmos DB bağlanabilir ve verileri yönetebilirsiniz. Ayrıca, diğer geçiş sonrası iyileştirme adımlarını da gerçekleştirebilirsiniz. Bunlar, dizin oluşturma ilkesini iyileştirmek, varsayılan tutarlılık düzeyini güncelleştirmek veya Azure Cosmos DB hesabınız için genel dağıtımı yapılandırmak içerebilir. Daha fazla bilgi için bkz. [geçiş sonrası iyileştirmesi](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Sonraki adımlar

[Azure veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)'ndaki ek senaryolar için geçiş kılavuzunu gözden geçirin.



