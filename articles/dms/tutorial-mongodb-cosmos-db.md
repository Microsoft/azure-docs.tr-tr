---
title: "Öğretici: MongoDB 'yi Azure Cosmos DB için Azure veritabanı geçiş hizmeti 'ni kullanarak MongoDB için API 'yi çevrimdışı yapma | Microsoft Docs"
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi MongoDB 'den MongoDB 'ye yönelik API Azure Cosmos DB 'ye geçiş yapmayı öğrenin.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 96540a8ea40efcc3a2d115980999c8d470b85180
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265939"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Öğretici: DMS kullanarak MongoDB 'yi Azure Cosmos DB, MongoDB için API 'ye geçirme

Azure veritabanı geçiş hizmeti 'ni, MongoDB 'nin şirket içi veya bulut örneğinden MongoDB 'nin API 'sine Azure Cosmos DB bir çevrimdışı (tek seferlik) geçişi gerçekleştirmek için kullanabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.

Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak bir Azure sanal makinesinde barındırılan MongoDB 'deki bir veri kümesini Azure Cosmos DB MongoDB için API 'sine geçireceğiniz. Zaten bir MongoDB kaynağınız yoksa, [Azure 'Da WINDOWS VM 'de MongoDB 'Yi kurma ve yapılandırma](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb)makalesine bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* Aktarım hızını tahmin etme, bölüm anahtarı seçme ve dizin oluşturma ilkesi gibi [geçiş öncesi adımları doldurun](../cosmos-db/mongodb-pre-migration.md) .
* [MongoDB hesabı için bir Azure Cosmos DB API 'Si oluşturun](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)kullanarak şirket içi kaynak sunucularınız için siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti Için bir Azure sanal ağı (VNet) oluşturun. VNet oluşturma hakkında daha fazla bilgi için, [sanal ağ belgelerine](https://docs.microsoft.com/azure/virtual-network/)ve özellikle de adım adım ayrıntılara sahip olan hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > VNet kurulumu sırasında, ExpressRoute 'u Microsoft 'a ağ eşlemesi ile kullanıyorsanız, aşağıdaki hizmet [uç noktalarını](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) hizmetin sağlanacağı alt ağa ekleyin:
    >
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* VNet ağ güvenlik grubu (NSG) kurallarınızın aşağıdaki iletişim bağlantı noktalarını engellemediğinden emin olun: 53, 443, 445, 9354 ve 10000-20000. Azure VNet NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)makalesine bakın.
* Azure veritabanı geçiş hizmeti 'nin kaynak MongoDB sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 27017 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler** seçeneğini belirleyin ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Azure Veritabanı Geçiş Hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve sonra **Kaynak sağlayıcıları**’nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydet](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Örnek oluşturma

1. Azure portalda +**Kaynak oluştur**'u seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve açılan listeden **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure Market](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin. 

5. Mevcut bir VNet seçin veya yeni bir sanal ağ oluşturun.

    VNet, kaynak MongoDB örneğine ve hedef Azure Cosmos DB hesabına erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    Azure portal VNet oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](https://aka.ms/DMSVnet)makalesine bakın.

6. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

      ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **Azure veritabanı geçiş Hizmetleri** ekranında, oluşturduğunuz Azure veritabanı geçiş hizmeti örneğinin adını arayın ve ardından örneği seçin.

3. +**Yeni Geçiş Projesi**'ni seçin.

4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **MongoDB**' yi seçin, **hedef sunucu türü** metin kutusunda, **cosmosdb (MongoDB API)** öğesini seçin ve ardından **tür Seç ' e tıklayın. Etkinlik**, **çevrimdışı veri geçişi**' ni seçin. 

    ![Veritabanı geçiş hizmeti projesi oluştur](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Projeyi oluşturmak ve geçiş etkinliğini çalıştırmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Kaynak ayrıntıları** ekranında, kaynak MongoDB sunucusu için bağlantı ayrıntılarını belirtin.

   > [!IMPORTANT]
   > Azure veritabanı geçiş hizmeti kaynak olarak Azure Cosmos DB desteklemez.

    Bir kaynağa bağlanmak için üç mod vardır:
   * Tam etki alanı adı veya IP adresi, bağlantı noktası numarası ve bağlantı kimlik bilgilerini kabul eden **Standart mod**.
   * Makale [bağlantı DIZESI URI biçimi](https://docs.mongodb.com/manual/reference/connection-string/)bölümünde açıklandığı gibi MongoDB bağlantı dizesini kabul eden **bağlantı dizesi modu**.
   * Blob kapsayıcı SAS URL 'sini kabul eden **Azure Storage verileri**. Blob kapsayıcısının MongoDB [bsondump aracı](https://docs.mongodb.com/manual/reference/program/bsondump/)tarafından üretilen bSon dökümlerini içermesi ve kapsayıcıda JSON dosyaları varsa onu yeniden seçmesini istiyorsanız **BLOB** ' u seçin.

     Bu seçeneği belirlerseniz, depolama hesabı bağlantı dizesinin şu biçimde göründüğünden emin olun:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Bu blob kapsayıcısı SAS bağlantı dizesi, Azure Depolama Gezgini 'nde bulunabilir. İlgili kapsayıcı için SAS oluşturmak, size istenen biçimdeki URL 'YI sağlar.
     
     Ayrıca, Azure Storage 'daki tür dökümü bilgilerine göre aşağıdaki ayrıntıyı göz önünde bulundurun.

     * BSON dökümler için, blob kapsayıcısı içindeki verilerin bsondump biçiminde olması gerekir, bu nedenle veri dosyaları koleksiyon. bSon biçiminde kapsayan veritabanlarının ardından adlandırılan klasörlere yerleştirilir. Meta veri dosyaları (varsa), *koleksiyon*. Metadata. JSON biçimi kullanılarak adlandırılmalıdır.

     * JSON dökümlerinde, blob kapsayıcısındaki dosyaların, kapsayan veritabanlarının ardından adlandırılan klasörlere yerleştirilmesi gerekir. Her veritabanı klasörü içinde, veri dosyalarının "Data" adlı bir alt klasöre yerleştirilmesi ve *Collection*. JSON biçimi kullanılarak adlandırılması gerekir. Meta veri dosyaları (varsa), "metadata" adlı bir alt klasöre yerleştirilmelidir ve aynı biçim olan *Collection*. JSON kullanılarak adlandırılmalıdır. Meta veri dosyaları MongoDB bsondump aracı tarafından oluşturulan biçimde olmalıdır.

    > [!IMPORTANT]
    > Mongo sunucusunda kendinden imzalı bir sertifika kullanılması önerilmez. Ancak, bir tane kullanılmışsa **bağlantı dizesi modunu** kullanarak sunucuya bağlanın ve bağlantı dizeniz "" olduğundan emin olun
    >
    >```
    >&sslVerifyCertificate=false
    >```

   DNS ad çözümlemenin mümkün olmadığı durumlarda IP Adresini de kullanabilirsiniz.

   ![Kaynak ayrıntılarını belirtme](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** ekranında, MongoDB verilerinizi geçirdiğiniz MongoDB hesabı için önceden sağlanmış Azure Cosmos DB API 'si olan hedef Azure Cosmos DB hesabının bağlantı ayrıntılarını belirtin.

    ![Hedef ayrıntılarını belirtme](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **Kaydet**’i seçin.

## <a name="map-to-target-databases"></a>Hedef veritabanlarıyla eşleyin

1. **Hedef veritabanlarına eşle** ekranında, geçiş için kaynağı ve hedef veritabanını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    Dize **oluşturma** veritabanı adının yanında görünürse, Azure veritabanı geçiş hizmeti 'nin hedef veritabanını bulmadığını ve hizmetin sizin için veritabanını oluşturacağını belirtir.

    Geçişin bu noktasında [üretilen iş sağlama](https://docs.microsoft.com/azure/cosmos-db/set-throughput)sağlayabilirsiniz. Cosmos DB ' de, her koleksiyon için veritabanı düzeyinde veya ayrı ayrı üretilen iş sağlama sağlayabilirsiniz. Aktarım hızı, [Istek birimleri](https://docs.microsoft.com/azure/cosmos-db/request-units) (ru) cinsinden ölçülür. [Azure Cosmos DB fiyatlandırması](https://azure.microsoft.com/pricing/details/cosmos-db/)hakkında daha fazla bilgi edinin.

    ![Hedef veritabanlarıyla eşleyin](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **Kaydet**’i seçin.
3. **Koleksiyon ayarı** ekranında koleksiyonlar listesini genişletin ve geçirilecek koleksiyonların listesini gözden geçirin.

    Azure veritabanı geçiş hizmeti, hedef Azure Cosmos DB hesabında mevcut olmayan kaynak MongoDB örneğinde mevcut olan tüm koleksiyonları otomatik olarak seçer. Zaten veri içeren koleksiyonları yeniden geçirmek istiyorsanız, bu dikey penceredeki koleksiyonları açıkça seçmeniz gerekir.

    Koleksiyonların kullanmasını istediğiniz RUs miktarını belirtebilirsiniz. Azure veritabanı geçiş hizmeti, koleksiyon boyutuna bağlı olarak akıllı varsayılanlar önerir.

    > [!NOTE]
    > Gerekirse, çalışmayı hızlandırmak için Azure veritabanı geçiş hizmeti 'nin birden çok örneğini kullanarak veritabanı geçişini ve toplamayı paralel olarak gerçekleştirin.

    Ayrıca, en iyi ölçeklenebilirlik için [Azure Cosmos DB bölümlemeden](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) yararlanmak üzere bir parça anahtarı belirtebilirsiniz. Parça [/bölüm anahtarı seçmek için en iyi uygulamaları](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)gözden geçirdiğinizden emin olun.

    ![Koleksiyon tablolarını Seç](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **Kaydet**’i seçin.

5. **Geçiş özeti** ekranının **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin.

    ![Geçiş özeti](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

    Geçiş etkinliği penceresi görüntülenir ve etkinliğin **durumu** **başlatılmaz**.

    ![Etkinlik durumu](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Geçişi izleme

* Geçiş etkinliği ekranında **Yenile**'yi seçerek geçişin **Durum** bilgisi **Tamamlandı** olana kadar gösterilen verileri güncelleştirebilirsiniz.

   > [!NOTE]
   > Veritabanı ve koleksiyon düzeyinde geçiş ölçümlerinin ayrıntılarını almak için etkinliğini seçebilirsiniz.

    ![Etkinlik durumu tamamlandı](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DB verileri doğrulama

* Geçiş tamamlandıktan sonra, tüm koleksiyonların başarıyla geçirildiğini doğrulamak için Azure Cosmos DB hesabınızı kontrol edebilirsiniz.

    ![Etkinlik durumu tamamlandı](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Geçiş sonrası iyileştirmesi

MongoDB veritabanında depolanan verileri MongoDB için Azure Cosmos DB API 'sine geçirdikten sonra, Azure Cosmos DB bağlanabilir ve verileri yönetebilirsiniz. Ayrıca, dizin oluşturma ilkesini iyileştirmek, varsayılan tutarlılık düzeyini güncelleştirmek veya Azure Cosmos DB hesabınız için genel dağıtımı yapılandırmak gibi geçiş sonrası en iyi duruma getirme adımlarını da gerçekleştirebilirsiniz. Daha fazla bilgi için, [geçiş sonrası iyileştirme](../cosmos-db/mongodb-post-migration.md) makalesine bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Cosmos DB hizmeti bilgileri](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Sonraki adımlar

* Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)'ndaki ek senaryolar için geçiş kılavuzunu gözden geçirin.
