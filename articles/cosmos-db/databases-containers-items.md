---
title: Azure Cosmos DB veritabanları, kapsayıcılar ve öğelerle çalışma
description: Bu makalede, Azure Cosmos DB veritabanlarının, kapsayıcıların ve öğelerin nasıl oluşturulacağı ve kullanılacağı açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 4d970e61fc2628843ef847526dcdb4e44bf9796f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176922"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Azure Cosmos DB veritabanları, kapsayıcılar ve öğelerle çalışma

Azure aboneliğinizde bir [Azure Cosmos DB hesabı](account-overview.md) oluşturduktan sonra veritabanları, kapsayıcılar ve öğeler oluşturarak hesabınızdaki verileri yönetebilirsiniz. Bu makalede bu varlıkların her biri açıklanmaktadır. 

Aşağıdaki görüntüde Azure Cosmos DB hesabındaki farklı varlıkların hiyerarşisi gösterilmektedir:

![Azure Cosmos hesabı varlıkları](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos veritabanları

Hesabınızda bir veya daha fazla Azure Cosmos veritabanı oluşturabilirsiniz. Bir veritabanı bir ad alanına benzer. Veritabanı, bir dizi Azure Cosmos kapsayıcısı için yönetim birimidir. Aşağıdaki tabloda, bir Azure Cosmos veritabanının, API 'ye özgü çeşitli varlıklarla nasıl eşlendiği gösterilmektedir:

| Azure Cosmos varlığı | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos veritabanı | Database | anahtar alanı | Database | Database | Yok |

> [!NOTE]
> Tablo API'si hesapları ile ilk tablonuzu oluşturduğunuzda, Azure Cosmos hesabınızda otomatik olarak varsayılan bir veritabanı oluşturulur.

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos veritabanında işlemler

Aşağıdaki tabloda açıklandığı gibi Azure Cosmos API 'si ile Azure Cosmos veritabanıyla etkileşime geçebilirsiniz:

| İşlem | Azure CLI | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- | --- |
|Tüm veritabanlarını listeleme| Yes | Yes | Evet (veritabanı bir keyspace ile eşlendi) | Yes | Yok | Yok |
|Veritabanını oku| Yes | Yes | Evet (veritabanı bir keyspace ile eşlendi) | Yes | Yok | Yok |
|Yeni veritabanı oluştur| Yes | Yes | Evet (veritabanı bir keyspace ile eşlendi) | Yes | Yok | Yok |
|Veritabanını güncelleştir| Yes | Yes | Evet (veritabanı bir keyspace ile eşlendi) | Yes | Yok | Yok |


## <a name="azure-cosmos-containers"></a>Azure Cosmos kapsayıcıları

Azure Cosmos kapsayıcısı, sağlanan aktarım hızı ve depolama için ölçeklenebilirlik birimidir. Bir kapsayıcı yatay olarak bölümlenmiştir ve daha sonra birden çok bölgede çoğaltılır. Kapsayıcıya eklediğiniz öğeler ve üzerinde sağladığınız aktarım hızı, Bölüm anahtarını temel alan bir mantıksal bölümler kümesi üzerinden otomatik olarak dağıtılır. Bölümlendirme ve bölüm anahtarları hakkında daha fazla bilgi için bkz. [bölüm verileri](partition-data.md). 

Bir Azure Cosmos kapsayıcısı oluşturduğunuzda, aşağıdaki modlardan birinde üretilen işi yapılandırın:

* **Adanmış sağlanan aktarım hızı modu**: bir kapsayıcıda sağlanan aktarım hızı, bu kapsayıcı için özel olarak ayrılmıştır ve SLA 'lar tarafından desteklenir. Daha fazla bilgi edinmek için bkz. [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md).

* **Paylaşılan sağlanan aktarım hızı modu**: Bu kapsayıcılar, sağlanan aktarım hızını aynı veritabanındaki diğer kapsayıcılarla paylaşır (adanmış sağlanan aktarım hızı ile yapılandırılmış kapsayıcılar hariç). Diğer bir deyişle, veritabanında sağlanan aktarım hızı "paylaşılan verimlilik" kapsayıcıları arasında paylaşılır. Daha fazla bilgi edinmek için bkz. [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md).

> [!NOTE]
> Yalnızca veritabanı ve kapsayıcı oluştururken paylaşılan ve ayrılmış aktarım hızını yapılandırabilirsiniz. Kapsayıcı oluşturulduktan sonra adanmış aktarım hızı modundan paylaşılan verimlilik moduna (ve tam tersi) geçiş yapmak için yeni bir kapsayıcı oluşturmanız ve verileri yeni kapsayıcıya geçirmeniz gerekir. Azure Cosmos DB akışı Değiştir özelliğini kullanarak verileri geçirebilirsiniz.

Bir Azure Cosmos kapsayıcısı, özel veya paylaşılan sağlanmış verimlilik modlarını kullanarak kapsayıcılar oluşturup esnek ölçeklendirebilirler.

Azure Cosmos kapsayıcısı, öğelerin şema belirsiz bir kapsayıcısıdır. Bir kapsayıcıdaki öğelerin rastgele şemaları olabilir. Örneğin, bir kişiyi temsil eden bir öğe ve bir otomobil öğesini temsil eden bir öğe *aynı kapsayıcıya*yerleştirilebilir. Varsayılan olarak, bir kapsayıcıya eklediğiniz tüm öğeler açık dizin veya şema yönetimine gerek kalmadan otomatik olarak dizinlenir. Bir kapsayıcıda [Dizin oluşturma ilkesini](index-overview.md) yapılandırarak dizin oluşturma davranışını özelleştirebilirsiniz. 

Bir Azure Cosmos kapsayıcısındaki seçili öğelerde [yaşam süresi (TTL)](time-to-live.md) ayarlayabilir veya tüm kapsayıcının bu öğeleri sistemden düzgün bir şekilde temizlemesini sağlayabilirsiniz. Azure Cosmos DB süreleri dolduğunda öğeleri otomatik olarak siler. Ayrıca, kapsayıcıda gerçekleştirilen bir sorgunun, süresi geçen öğelerin sabit bir bağlı olarak döndürülmeyeceğini güvence altına alır. Daha fazla bilgi için bkz. [KAPSAYıCıDA TTL yapılandırma](how-to-time-to-live.md).

[Değişiklik akışını](change-feed.md) , kapsayıcının her bir mantıksal bölümü için yönetilen işlemler günlüğüne abone olmak için kullanabilirsiniz. Değişiklik akışı, kapsayıcıda gerçekleştirilen tüm güncelleştirmelerin günlüğünü, öğelerin önceki ve sonraki görüntülerinden birlikte sağlar. Daha fazla bilgi için bkz. [değişiklik akışını kullanarak reaktif uygulamalar oluşturma](serverless-computing-database.md). Değişiklik akışı için saklama süresini, kapsayıcıda değişiklik akışı ilkesini kullanarak da yapılandırabilirsiniz. 

Azure Cosmos Kapsayıcınız için [saklı yordamları, Tetikleyicileri, Kullanıcı tanımlı işlevleri (UDF 'ler)](stored-procedures-triggers-udfs.md)ve [birleştirme yordamlarını](how-to-manage-conflicts.md) kaydedebilirsiniz. 

Azure Cosmos kapsayıcıda [benzersiz bir anahtar kısıtlaması](unique-keys.md) belirtebilirsiniz. Benzersiz bir anahtar ilkesi oluşturarak mantıksal bölüm anahtarı başına bir veya daha fazla değerin benzersizliği olduğundan emin olursunuz. Benzersiz anahtar ilkesi kullanarak bir kapsayıcı oluşturursanız, benzersiz anahtar kısıtlaması tarafından belirtilen değerleri yineleyen yeni veya güncelleştirilmiş bir öğe oluşturulabilir. Daha fazla bilgi için bkz. [benzersiz anahtar kısıtlamaları](unique-keys.md).

Aşağıdaki tabloda gösterildiği gibi, bir Azure Cosmos kapsayıcısı, API 'ye özgü varlıklar için özelleştirilmiştir:

| Azure Cosmos varlığı | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos kapsayıcısı | Kapsayıcı | Tablo | Koleksiyon | Graph | Tablo |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos kapsayıcısının özellikleri

Bir Azure Cosmos kapsayıcısının sistem tarafından tanımlanan özellikler kümesi vardır. Kullandığınız API 'ye bağlı olarak, bazı özellikler doğrudan sunulmayabilir. Aşağıdaki tabloda sistem tarafından tanımlanan özelliklerin listesi açıklanmaktadır:

| Sistem tanımlı özellik | Sistem tarafından oluşturulan veya Kullanıcı tarafından yapılandırılabilen | Amaç | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_kimliği | Sistem tarafından oluşturulan | Kapsayıcının benzersiz tanımlayıcısı | Yes | Hayır | Hayır | Hayır | Hayır |
|\_ETag | Sistem tarafından oluşturulan | İyimser eşzamanlılık denetimi için kullanılan varlık etiketi | Yes | Hayır | Hayır | Hayır | Hayır |
|\_TS | Sistem tarafından oluşturulan | Kapsayıcının son güncelleştirilme zaman damgası | Yes | Hayır | Hayır | Hayır | Hayır |
|\_kendine | Sistem tarafından oluşturulan | Kapsayıcının adreslenebilir URI 'SI | Yes | Hayır | Hayır | Hayır | Hayır |
|id | Kullanıcı tarafından yapılandırılabilir | Kapsayıcının Kullanıcı tanımlı benzersiz adı | Yes | Yes | Yes | Yes | Yes |
|ındexingpolicy | Kullanıcı tarafından yapılandırılabilir | Dizin yolunu, dizin türünü ve Dizin modunu değiştirme olanağı sağlar | Yes | Hayır | Hayır | Hayır | Yes |
|TimeToLive | Kullanıcı tarafından yapılandırılabilir | Bir zaman aralığı sonrasında bir kapsayıcıdan otomatik olarak öğe silme yeteneği sağlar. Ayrıntılar için bkz. [yaşam süresi](time-to-live.md). | Yes | Hayır | Hayır | Hayır | Yes |
|changeFeedPolicy | Kullanıcı tarafından yapılandırılabilir | Bir kapsayıcıdaki öğelerde yapılan değişiklikleri okumak için kullanılır. Ayrıntılar için bkz. [akışı değiştirme](change-feed.md). | Yes | Hayır | Hayır | Hayır | Yes |
|uniqueKeyPolicy | Kullanıcı tarafından yapılandırılabilir | Mantıksal bir bölümdeki bir veya daha fazla değerin benzersizliğini sağlamak için kullanılır. Daha fazla bilgi için bkz. [benzersiz anahtar kısıtlamaları](unique-keys.md). | Yes | Hayır | Hayır | Hayır | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos kapsayıcısındaki işlemler

Azure Cosmos kapsayıcısı, Azure Cosmos API 'Lerinden herhangi birini kullandığınızda aşağıdaki işlemleri destekler:

| İşlem | Azure CLI | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- | --- |
| Bir veritabanındaki kapsayıcıları listeleme | Yes | Yes | Yes | Yes | Yok | Yok |
| Kapsayıcıyı oku | Yes | Yes | Yes | Yes | Yok | Yok |
| Yeni kapsayıcı oluştur | Yes | Yes | Yes | Yes | Yok | Yok |
| Kapsayıcıyı güncelleştirme | Yes | Yes | Yes | Yes | Yok | Yok |
| Kapsayıcı silme | Yes | Yes | Yes | Yes | Yok | Yok |

## <a name="azure-cosmos-items"></a>Azure Cosmos öğeleri

Kullandığınız API 'ye bağlı olarak, bir Azure Cosmos öğesi koleksiyondaki bir belgeyi, tablodaki bir satırı veya bir grafikteki bir düğümü ya da kenarı temsil edebilir. Aşağıdaki tabloda, bir Azure Cosmos öğesine yönelik API 'ye özgü varlıkların eşleştirmesi gösterilmektedir:

| Cosmos varlığı | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos öğesi | Belge | sırada | Belge | Düğüm veya kenar | Öğe |

### <a name="properties-of-an-item"></a>Bir öğenin özellikleri

Her Azure Cosmos öğesi, sistem tarafından tanımlanan aşağıdaki özelliklere sahiptir. Kullandığınız API 'ye bağlı olarak, bunlardan bazıları doğrudan sunulmayabilir.

| Sistem tanımlı özellik | Sistem tarafından oluşturulan veya Kullanıcı tarafından yapılandırılabilen| Amaç | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_kimliği | Sistem tarafından oluşturulan | Öğenin benzersiz tanımlayıcısı | Yes | Hayır | Hayır | Hayır | Hayır |
|\_ETag | Sistem tarafından oluşturulan | İyimser eşzamanlılık denetimi için kullanılan varlık etiketi | Yes | Hayır | Hayır | Hayır | Hayır |
|\_TS | Sistem tarafından oluşturulan | Öğenin son güncelleştirme zaman damgası | Yes | Hayır | Hayır | Hayır | Hayır |
|\_kendine | Sistem tarafından oluşturulan | Öğenin adreslenebilir URI 'SI | Yes | Hayır | Hayır | Hayır | Hayır |
|id | Kullanabilir | Mantıksal bölümde Kullanıcı tanımlı benzersiz ad. | Yes | Yes | Yes | Yes | Yes |
|Rastgele Kullanıcı tanımlı özellikler | Kullanıcı tanımlı | API yerel gösteriminde temsil edilen Kullanıcı tanımlı Özellikler (JSON, BSON ve CQL dahil) | Yes | Yes | Yes | Yes | Yes |

> [!NOTE]
> `id` özelliğinin benzersizliği yalnızca her mantıksal bölüm içinde zorlanır. Birden çok belge, farklı bölüm anahtarı değerleriyle aynı `id` özelliğine sahip olabilir.

### <a name="operations-on-items"></a>Öğeler üzerinde işlemler

Azure Cosmos öğeleri aşağıdaki işlemleri destekler. İşlemleri gerçekleştirmek için Azure Cosmos API 'Lerinden herhangi birini kullanabilirsiniz.

| İşlem | Azure CLI | SQL API’si | Cassandra API’si | MongoDB için Azure Cosmos DB API | Gremlin API | Tablo API’si |
| --- | --- | --- | --- | --- | --- | --- |
| INSERT, Replace, DELETE, upsert, Read | Hayır | Yes | Yes | Yes | Yes | Yes |

## <a name="next-steps"></a>Sonraki adımlar

Bu görevler ve kavramlar hakkında bilgi edinin:

* [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md)
* [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md)
* [Mantıksal bölümlerle çalışma](partition-data.md)
* [Azure Cosmos kapsayıcısında TTL yapılandırma](how-to-time-to-live.md)
* [Değişiklik akışını kullanarak reaktif uygulamalar oluşturun](change-feed.md)
* [Azure Cosmos kapsayıcıda benzersiz bir anahtar kısıtlaması yapılandırma](unique-keys.md)
