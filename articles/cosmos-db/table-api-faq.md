---
title: Azure Cosmos DB Tablo API'si hakkında sık sorulan sorular
description: Azure Cosmos DB Tablo API'si hakkında sık sorulan soruların yanıtlarını alın
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341250"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Azure Cosmos DB Tablo API'si hakkında sık sorulan sorular
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB Tablo API'si [Azure Portal](https://portal.azure.com) önce Azure aboneliğine kaydolmanız gerekir. Kaydolduktan sonra Azure aboneliğinize bir Azure Cosmos DB Tablo API'si hesabı ekleyebilir ve sonra hesabınıza tablolar ekleyebilirsiniz. [Azure Cosmos DB tablo API'si giriş](table-introduction.md)bölümünde desteklenen dilleri ve ilişkili hızlı başlangıç bilgilerini bulabilirsiniz.

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Azure Cosmos DB vs Azure Tablo depolamadaki Tablo API'si

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Tablo API'si ile Azure Tablo depolama alanı arasındaki farklar nelerdir?

Kullanıcıların Azure Cosmos DB Tablo API'si tabloları oluşturmak isteyen Azure Tablo depolamadan gelen bazı davranış farklılıkları vardır:

* Azure Cosmos DB Tablo API'si, garantili bir performans sağlamak için ayrılmış bir kapasite modeli kullanır, ancak bu, kapasite kullanılmasa bile, bu, bir kapasite için bir süre için ödeme yaptığı anlamına gelir. Azure Tablo depolama ile yalnızca kullanılan kapasite için ödeme yapılır. Bu, Azure Tablo depolama alanı 10 saniyelik bir SLA sağlarken, Tablo API'si neden 99 ' luk bir 10.000 ve 15 MS yazma SLA 'Sı sunabileceği açıklanmasına yardımcı olur. Sonuç olarak, Tablo API'si tabloları, hatta herhangi bir istek olmadan boş tablolar bile, kapasiteyi, Azure Cosmos DB tarafından sunulan SLA 'da bunlara yönelik istekleri işlemek için kullanılabilir durumda olmasını sağlamak için maliyet paranız ile ücretlendirilir.

* Tablo API'si tarafından döndürülen sorgu sonuçları, Azure Tablo depolamada olduklarından bölüm anahtarı/satır anahtarı sırasında sıralanmaz.

* Satır anahtarları yalnızca 255 bayta kadar olabilir.

* Toplu işlerin en fazla 2 MB olabilir.

* CORS Şu anda desteklenmiyor.

* Azure Tablo depolama alanındaki tablo adları büyük/küçük harfe duyarlı değildir, ancak bunlar Azure Cosmos DB Tablo API'si.

* İkili alanlar gibi Azure Cosmos DB kodlama bilgileri için bazı iç biçimler, şu anda benzer bir şekilde verimli değildir. Bu nedenle bu, veri boyutuyla ilgili beklenmeyen sınırlamalara neden olabilir. Örneğin, şu anda, kodlama verilerin boyutunu arttığı için, bir tablo varlığının tam bir Meg 'sini, ikili verileri depolamak için kullandık.

* ' ID ' varlık özelliği adı şu anda desteklenmiyor.

* TableQuery TakeCount, 1000 ile sınırlı değildir.

* REST API, Azure Cosmos DB Tablo API'si tarafından desteklenmeyen birkaç uç nokta/sorgu seçeneği vardır:

  | Rest yöntemleri | REST uç noktası/sorgu seçeneği | Belge URL 'Leri | Açıklama |
  | ------------| ------------- | ---------- | ----------- |
  | AL, KOY | `/?restype=service@comp=properties`| [Tablo hizmeti özelliklerini ayarlama](/rest/api/storageservices/set-table-service-properties) ve [Tablo hizmeti özelliklerini al](/rest/api/storageservices/get-table-service-properties) | Bu uç nokta CORS kuralları, depolama Analizi yapılandırması ve günlüğe kaydetme ayarlarını ayarlamak için kullanılır. CORS Şu anda desteklenmiyor ve analiz ve günlüğe kaydetme, Azure depolama tablolarından Azure Cosmos DB farklı şekilde işlendi |
  | Seçenekler | `/<table-resource-name>` | [Uçuş öncesi CORS tablo isteği](/rest/api/storageservices/preflight-table-request) | Bu, Azure Cosmos DB Şu anda desteklemediği CORS 'nin bir parçasıdır. |
  | GET | `/?restype=service@comp=stats` | [Tablo hizmeti Istatistiklerini al](/rest/api/storageservices/get-table-service-stats) | Birincil ve ikincil sunucular arasında verilerin ne kadar hızlı çoğaltılmasının bilgisini sağlar. Çoğaltma, yazma işlemlerinin bir parçası olduğundan, bu Cosmos DB gerekli değildir. |
  | AL, KOY | `/mytable?comp=acl` | [Tablo ACL 'Sini al](/rest/api/storageservices/get-table-acl) ve [tablo ACL 'sini ayarla](/rest/api/storageservices/set-table-acl) | Bu, paylaşılan erişim Imzalarını (SAS) yönetmek için kullanılan saklı erişim ilkelerini alır ve ayarlar. SAS desteklense de farklı şekilde ayarlanır ve yönetilir. |

* Azure Cosmos DB Tablo API'si yalnızca, ATOM değil JSON biçimini destekler.

* Azure Cosmos DB, paylaşılan erişim Imzalarını (SAS) desteklese de, özellikle de yeni tablo oluşturma hakkı gibi yönetim işlemleriyle ilgili olan bazı ilkeler desteklenmez.

* Özellikle .NET SDK için, Azure Cosmos DB Şu anda desteklemediği bazı sınıflar ve yöntemler vardır.

  | Sınıf | Desteklenmeyen Yöntem |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties * |
  |                  | \*ServiceStats * |
  | CloudTable | SetPermissions * |
  |            | GetPermissions * |
  | TableServiceContext | * (Bu sınıf kullanım dışı) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Diğer sık sorulan sorular

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Tablo API'si kullanmak için yeni bir SDK almam gerekiyor mu?

Hayır, mevcut depolama SDK 'Ları hala çalışıyor olmalıdır. Ancak, her zaman en iyi destek için en son SDK 'Ları ve birçok durumda üstün performans elde etmek önerilir. [Azure Cosmos DB giriş tablo API'si](table-introduction.md)kullanılabilir dillerin listesine bakın.

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Tablo API'si bağlanmak için kullanmam gereken bağlantı dizesi nedir?

Bağlantı dizesi:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Bağlantı dizesini Azure portal bağlantı dizesi sayfasından edinebilirsiniz.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Tablo API'si için .NET SDK 'daki istek seçeneklerinin yapılandırma ayarlarını geçersiz Nasıl yaparım?.

Bazı ayarlar CreateCloudTableClient yönteminde ve diğer bir deyişle istemci uygulamasındaki appSettings bölümündeki app.config ile işlenir. Yapılandırma ayarları hakkında bilgi için bkz. [Azure Cosmos DB özellikleri](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Mevcut Azure Tablo depolama SDK 'larını kullanan müşteriler için herhangi bir değişiklik var mı?

Yok. Mevcut Azure Tablo depolama SDK 'larını kullanan mevcut veya yeni müşteriler için herhangi bir değişiklik yoktur.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Nasıl yaparım?, Tablo API'si birlikte kullanmak için Azure Cosmos DB depolanan tablo verilerini görüntüleyebilir mi?

Verilere gözatabilmeniz için Azure portal kullanabilirsiniz. Tablo API'si kodunu veya sonraki cevap içinde bahsedilen araçları da kullanabilirsiniz.

### <a name="which-tools-work-with-the-table-api"></a>Tablo API'si hangi araçlar çalışıyor?

[Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md)kullanabilirsiniz.

Daha önce belirtilen biçimde bir bağlantı dizesi alma esnekliğine sahip araçlar yeni Tablo API'si destekleyebilir. [Azure Storage Istemci araçları](../storage/common/storage-explorers.md) sayfasında tablo araçları listesi sunulmaktadır.

### <a name="is-the-concurrency-on-operations-controlled"></a>Eşzamanlılık işlemleri denetleniyor mu?

Evet, iyimser eşzamanlılık ETag mekanizması kullanılarak sağlanır.

### <a name="is-the-odata-query-model-supported-for-entities"></a>OData sorgu modeli varlıklar için destekleniyor mu?

Evet, Tablo API'si OData sorgusunu ve LINQ sorgusunu destekler.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Azure Tablo depolama 'ya bağlanıp Azure Cosmos DB Tablo API'si aynı uygulamada yan yana bağlanabilir miyim?

Evet, her biri bağlantı dizesi aracılığıyla kendi URI 'sine işaret eden CloudTableClient 'ın iki ayrı örneğini oluşturarak bağlanabilirsiniz.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Mevcut bir Azure Tablo Depolama uygulamasını bu teklife geçirmek Nasıl yaparım? mı?

[AzCopy](../storage/common/storage-use-azcopy-v10.md) ve [Azure Cosmos DB veri geçiş aracı](import-data.md) desteklenir.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Bu hizmet için depolama boyutunun nasıl genişlediği, örneğin *n* GB veri ile başladım ve verilerim zaman IÇINDE 1 TB 'a büyüyecektir?

Azure Cosmos DB, yatay ölçeklendirme kullanılarak sınırsız depolama sağlamak üzere tasarlanmıştır. Hizmet, depolama alanınızı izleyebilir ve etkili bir şekilde artırabilir.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Tablo API'si teklif Nasıl yaparım? İzmi?

İstekleri ve depolama kullanımını izlemek için Tablo API'si **ölçümleri** bölmesini kullanabilirsiniz.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Gerekli olan üretilen iş üretimini Nasıl yaparım? hesaplansın mı?

İşlemler için gereken tablo verimini hesaplamak için kapasite tahmin Aracı ' i kullanabilirsiniz. Daha fazla bilgi için bkz. [Tahmini Istek birimleri ve veri depolama](https://www.documentdb.com/capacityplanner). Genel olarak, varlığınızı JSON olarak gösterebilir ve işlemlerinizin numaralarını sağlayabilirsiniz.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Tablo API'si SDK 'sını öykünücü ile yerel olarak kullanabilir miyim?

Şu anda değil.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Mevcut Uygulamam Tablo API'si çalışabilir mi?

Evet, aynı API desteklenir.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Tablo API'si özelliklerini kullanmak istemiyorum, mevcut Azure Tablo depolama uygulamalarımı SDK 'ya geçirmem gerekir mi?

Hayır, mevcut Azure Tablo depolama varlıklarını herhangi bir kesinti olmadan oluşturabilir ve kullanabilirsiniz. Ancak Tablo API'si kullanmıyorsanız, Otomatik Dizin, ek tutarlılık seçeneği veya küresel dağıtım avantajlarından faydalanabilirsiniz.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Tablo API'si verileri Azure 'un birden fazla bölgesine çoğaltması Nasıl yaparım? eklensin mi?

Uygulamanız için uygun olan bölgeleri eklemek için Azure Cosmos DB portalının [Genel çoğaltma ayarlarını](tutorial-global-distribution-sql-api.md#portal) kullanabilirsiniz. Küresel olarak dağıtılmış bir uygulama geliştirmek için, düşük okuma gecikmesi sağlamak üzere, uygulamanızı PreferredLocation bilgilerini yerel bölgeye de eklemeniz gerekir.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Nasıl yaparım? Tablo API'si hesabın birincil yazma bölgesi değiştirilsin mi?

Bir bölge eklemek ve ardından gerekli bölgeye yük devretmek için Azure Cosmos DB genel çoğaltma portalı bölmesini kullanabilirsiniz. Yönergeler için bkz. [Multi-region Azure Cosmos DB hesaplarıyla geliştirme](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Verilerimi dağıtırken tercih edilen okuma bölgelerini düşük gecikme süresine göre yapılandırma Nasıl yaparım??

Yerel konumdan okumaya yardımcı olması için app.config dosyasındaki PreferredLocation anahtarını kullanın. Mevcut uygulamalar için, LocationMode değeri ayarlandıysa Tablo API'si bir hata oluşturur. Bu kodu kaldırın, çünkü Tablo API'si app.config dosyasından bu bilgileri alır. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Tablo API'si tutarlılık düzeylerini nasıl düşünmem gerekir?

Azure Cosmos DB tutarlılık, kullanılabilirlik ve gecikme süresi arasında iyi bir fikir verir. Azure Cosmos DB, geliştiricilerin Tablo API'si beş tutarlılık düzeyi sunarak, tablo düzeyinde doğru tutarlılık modelini seçebilir ve verileri sorgularken tek tek istekler yapabilirsiniz. İstemci bağlandığı zaman, tutarlılık düzeyi belirtebilir. Bu düzeyi CreateCloudTableClient ' ın ' me düzeyi bağımsız değişkeni aracılığıyla değiştirebilirsiniz.

Tablo API'si, varsayılan olarak sınırlı bir tutarlılık tutarlılığı olan "kendi yazmalarınızı okuma" ile düşük gecikmeli okumalar sağlar. Daha fazla bilgi için bkz. [tutarlılık düzeyleri](consistency-levels.md).

Varsayılan olarak, Azure Tablo Depolaması bir bölge içinde güçlü tutarlılık ve ikincil konumlarda nihai tutarlılık sağlar.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB Tablo API'si Azure Tablo depolamadan daha fazla tutarlılık düzeyi sunuyor mu?

Evet, Azure Cosmos DB dağıtılmış doğası hakkında daha fazla bilgi için bkz. [tutarlılık düzeyleri](consistency-levels.md). Tutarlılık düzeyleri için garantiler sağlandığından, bunları güvenle kullanabilirsiniz.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Genel dağıtım etkinleştirildiğinde verileri çoğaltmak ne kadar sürer?

Azure Cosmos DB, verileri yerel bölgede dursun olarak kaydeder ve verileri birkaç milisaniyeye göre hemen diğer bölgelere gönderir. Bu çoğaltma yalnızca veri merkezinin gidiş dönüş zamanına (RTT) bağımlıdır. Azure Cosmos DB küresel dağıtım özelliği hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB: Azure 'da genel olarak dağıtılmış bir veritabanı hizmeti](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Okuma isteği tutarlılığı düzeyi değiştirilebilir mi?

Azure Cosmos DB, tutarlılık düzeyini kapsayıcı düzeyinde (tabloda) ayarlayabilirsiniz. .NET SDK 'yı kullanarak, app.config dosyasında Tableme düzeyi anahtarı için değer sağlayarak düzeyi değiştirebilirsiniz. Olası değerler şunlardır: güçlü, sınırlanmış Eskime durumu, oturum, tutarlı ön ek ve nihai. Daha fazla bilgi için bkz. [Azure Cosmos dB ayarlanabilir veri tutarlılığı düzeyleri](consistency-levels.md). Önemli fikir, istek tutarlılığı düzeyini tablonun ayarından daha fazla ayarlayamıyoruz. Örneğin, son olarak tablo için tutarlılık düzeyini ve istek tutarlılığı düzeyini güçlü olarak ayarlayamazsınız.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Bir bölge aşağı gittiğinde Tablo API'si yük devretmeyi nasıl işler?

Tablo API'si, Azure Cosmos DB küresel olarak dağıtılmış platformundan yararlanır. Uygulamanızın veri merkezi kapalı kalma süresine yol açabildiğinden emin olmak için, Azure Cosmos DB portalında hesap için [çok bölgeli Azure Cosmos DB hesaplarıyla](high-availability.md)en az bir bölge daha etkinleştirin. Portalın önceliğini, [çok bölgeli Azure Cosmos DB hesapları Ile geliştirmeyi](high-availability.md)kullanarak belirleyebilirsiniz.

Hesap için istediğiniz kadar bölge ekleyebilir ve yük devretme önceliği sağlayarak hesaba yük devredebileceği yeri kontrol edebilirsiniz. Veritabanını kullanmak için, çok fazla bir uygulama sağlamanız gerekir. Bunu yaptığınızda, müşterileriniz kesinti yaşar. [En son .NET istemci SDK 'sı](table-sdk-dotnet.md) otomatik olarak barındırıldır, ancak diğer SDK 'lar değildir. Diğer bir deyişle, alt bölgeyi algılayabilir ve yeni bölgeye otomatik olarak yük devreder.

### <a name="is-the-table-api-enabled-for-backups"></a>Tablo API'si yedeklemeler için etkinleştirildi mi?

Evet, Tablo API'si yedeklemeler için Azure Cosmos DB platformundan yararlanır. Yedeklemeler otomatik olarak yapılır. Daha fazla bilgi için bkz. [Azure Cosmos DB çevrimiçi yedekleme ve geri yükleme](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Tablo API'si, varsayılan olarak bir varlığın tüm özniteliklerini dizinlidir mi?

Evet, bir varlığın tüm öznitelikleri varsayılan olarak dizinlenir. Daha fazla bilgi için bkz. [Azure Cosmos DB: Dizin oluşturma ilkeleri](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Bu, sorguları karşılamak için birden fazla dizin oluşturmak zorunda olmam anlamına geliyor mu?

Evet, Azure Cosmos DB Tablo API'si tüm özniteliklerin şema tanımı olmadan otomatik olarak dizinlemesini sağlar. Bu Otomasyon, geliştiricilerin dizin oluşturma ve yönetimi yerine uygulamaya odaklanmasını boşaltır. Daha fazla bilgi için bkz. [Azure Cosmos DB: Dizin oluşturma ilkeleri](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Dizin oluşturma ilkesini değiştirebilir miyim?

Evet, Dizin tanımını sağlayarak dizin oluşturma ilkesini değiştirebilirsiniz. Ayarları doğru bir şekilde kodlayıp kaçış yapmanız gerekir.

Non-.NET SDK 'Ları için, dizin oluşturma ilkesi yalnızca **Veri Gezgini**' de portalda ayarlanabilir, değiştirmek istediğiniz tabloya gidebilir ve sonra **Ölçek & ayarları**->dizin oluşturma ilkesi ' ne gidebilir, istediğiniz değişikliği yapıp, sonra **tasarruf** edebilirsiniz.

.NET SDK 'dan app.config dosyasında gönderilebilir:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Platform olarak Azure Cosmos DB sıralama, toplamalar, hiyerarşi ve diğer işlevler gibi birçok özelliğe sahip olabilir. Bu özellikleri Tablo API'si ekleysin mi?

Tablo API'si, Azure Tablo depolama ile aynı sorgu işlevlerini sağlar. Azure Cosmos DB ayrıca sıralama, toplamalar, jeo-uzamsal sorgu, hiyerarşi ve çok çeşitli yerleşik işlevleri de destekler. Daha fazla bilgi için bkz. [SQL sorguları](./sql-query-getting-started.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Tablo API'si için tablo verimini ne zaman değiştirmem gerekir?

Aşağıdaki koşullardan biri geçerli olduğunda, Tableverimini değiştirmelisiniz:

* Veri ayıklama, dönüştürme ve yükleme (ETL) işlemi gerçekleştiriyor veya kısa sürede çok fazla veri yüklemek istiyorsunuz.
* Kapsayıcıda veya arka uçtaki bir kapsayıcı kümesinden daha fazla işleme ihtiyacınız vardır. Örneğin, kullanılan üretilen iş üretiminin, sağlanan aktarım hızına göre daha fazla olduğunu ve kısıtlamadığınızı görürsünüz. Daha fazla bilgi için bkz. [Azure Cosmos kapsayıcıları için aktarım hızını ayarlama](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Tablo API'si tablomın verimini büyütme veya azaltma yapabilir miyim?

Evet, üretilen işi ölçeklendirmek için Azure Cosmos DB portalının ölçek bölmesini kullanabilirsiniz. Daha fazla bilgi için bkz. [aktarım hızını ayarlama](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Yeni sağlanan tablolar için ayarlanan varsayılan bir Tableüretilen Iş mi?

Evet, app.config aracılığıyla Tableverimini geçersiz kılamazsınız ve Azure Cosmos DB önceden oluşturulmuş bir kapsayıcı kullanmıyorsanız, hizmet 400 verimini içeren bir tablo oluşturur.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Azure Tablo depolama hizmeti 'nin mevcut müşterileri için fiyatlandırma değişikliği var mı?

Yok. Mevcut Azure Tablo depolama müşterileri için fiyat değişikliği yoktur.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Tablo API'si fiyat nasıl hesaplanır?

Fiyat, ayrılan tablo Işleme göre değişir.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Tablo API'si teklifteki tablolarda sınırlama Nasıl yaparım? mi?

İstek oranı, temel alınan kapsayıcı veya bir kapsayıcı kümesi için sağlanan aktarım hızı kapasitesinden daha büyükse bir hata alırsınız ve SDK yeniden deneme ilkesini uygulayarak çağrıyı yeniden dener.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Azure Cosmos DB teklifinizin Tablo API'si avantajlarından yararlanmak için neden PartitionKey ve RowKey 'tan ayrı bir aktarım hızı seçmem gerekir?

Azure Cosmos DB, app.config dosyasında veya Portal aracılığıyla bir tane sağlamazsanız, kapsayıcınıza yönelik varsayılan bir aktarım hızı belirler.

Azure Cosmos DB, performans ve gecikme için, işlem üzerinde üst sınırlarla garanti sağlar. Bu güvence, altyapının kiracının işlemleri üzerinde idare zorlaması mümkün olduğunda mümkündür. Tablo aktarım hızını ayarlamak, platform bu kapasiteyi ayırdığından ve işletimsel başarıyı garanti ettiğinden garantili aktarım hızı ve gecikme süresi almanızı sağlar.

Verimlilik belirtimini kullanarak, uygulamanızın mevsimselliği avantajlarından yararlanabilir, verimlilik ihtiyaçlarını karşılayabilir ve maliyetleri tasarruf edebilirsiniz.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Yalnızca verileri depolamak için ödeme yapma ve nadiren sorgu oluşturmam için Azure Tablo depolama, benim için ucuzdur. Azure Cosmos DB Tablo API'si teklif, tek bir işlem gerçekleştirmeme ya da herhangi bir şeyi depoladığım halde bile bana ücretlendirme yapıyor gibi görünüyor. Şunları anlayabileceğiniz?

Azure Cosmos DB, kullanılabilirlik, gecikme süresi ve aktarım hızı garantisi olan küresel olarak dağıtılmış, SLA tabanlı bir sistem olacak şekilde tasarlanmıştır. Azure Cosmos DB aktarım hızını ayırdığınızda, diğer sistemlerin aktarım hızını farklı şekilde garanti edilir. Azure Cosmos DB, müşterilerin istediği ikincil dizinler ve küresel dağıtım gibi ek yetenekler sağlar.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Azure Tablo depolama alanına veri aldığımda, hiçbir zaman kota tam "bildirimi (bir bölümün dolu olduğunu gösterir). Tablo API'si, bu iletiyi alıyorum. Bu teklif beni sınırlıyor ve var olan uygulamamı değiştirmem zorunlu midir?

Azure Cosmos DB gecikme süresi, aktarım hızı, kullanılabilirlik ve tutarlılık garantisi sayesinde sınırsız ölçek sağlayan SLA tabanlı bir sistemdir. Garantili Premium performans sağlamak için, veri boyutlarınızın ve dizininizin yönetilebilir ve ölçeklenebilir olduğundan emin olun. Bölüm anahtarı başına varlık veya öğe sayısı için 20 GB 'lik sınır, harika arama ve sorgu performansı sağlamamız sağlamaktır. Azure depolama için bile uygulamanızın iyi ölçeklendirdiğinden emin olmak için, tüm bilgileri tek bir bölümde depolayarak ve sorgulayarak bir sıcak bölüm *oluşturmamalıdır* .

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Bu nedenle PartitionKey ve RowKey Tablo API'si hala gereklidir?

Evet. Tablo API'si yüzey alanı Azure Tablo depolama SDK 'sına benzer olduğundan, bölüm anahtarı verileri dağıtmak için etkili bir yol sağlar. Satır anahtarı bu bölüm içinde benzersizdir. Satır anahtarının mevcut olması gerekir ve standart SDK 'da olduğu gibi null olamaz. RowKey uzunluğu 255 bayttır ve PartitionKey uzunluğu 1 KB 'tır.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Tablo API'si yönelik hata iletileri nelerdir?

Azure Tablo depolama ve Azure Cosmos DB Tablo API'si aynı SDK 'Ları kullanarak hataların çoğu aynı olacaktır.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Tablo API'si bir diğerinden sonra çok sayıda tablo oluşturmaya çalıştığımda neden kısıtlıyorum?

Azure Cosmos DB gecikme süresi, aktarım hızı, kullanılabilirlik ve tutarlılık garantisi sağlayan SLA tabanlı bir sistemdir. Sağlanan bir sistem olduğundan, bu gereksinimleri güvence altına almak için kaynakları ayırır. Tablo oluşturma işlemi hızlı bir şekilde algılanır ve kısıtlanacaktır. Tablo oluşturma hızına bakmanız ve dakika başına 5 ' ten az bir değer vermek tavsiye ederiz. Tablo API'si sağlanan bir sistem olduğunu unutmayın. Bunu sağladığınız süre için ödeme yapmaya başlayabilirsiniz.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Nasıl yaparım? SDK veya hatalar hakkında geri bildirimde bulunmak mı istiyorsunuz?

Görüşlerinizi aşağıdaki yollarla paylaşabilirsiniz:

* [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Soru sayfası Microsoft Q&](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow, programlama sorularına en iyisidir. Sorunuzun [konuyla ilgili](https://stackoverflow.com/help/on-topic) olduğundan emin olun ve mümkün olduğunca [fazla ayrıntı sağlayın ve bu soruyu açık ve answerable](https://stackoverflow.com/help/how-to-ask)yapın.

## <a name="next-steps"></a>Sonraki adımlar

* [.NET SDK ve Azure Cosmos DB Tablo API'si uygulama oluşturma](create-table-dotnet.md)
* [Azure Cosmos DB Tablo API'si verileri yönetmek için bir Java uygulaması oluşturma](create-table-java.md)