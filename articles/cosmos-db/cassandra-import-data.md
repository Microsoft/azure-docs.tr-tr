---
title: Verilerinizi Azure Cosmos DB bir Cassandra API hesabına geçirme-öğretici
description: Bu öğreticide, verileri Apache Cassandra 'dan Azure Cosmos DB bir Cassandra API hesabına kopyalamayı öğrenin.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449299"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Öğretici: verilerinizi bir Cassandra API hesabına geçirin
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bir geliştirici olarak, şirket içinde veya bulutta çalışan mevcut Cassandra iş yüklerine sahip olabilirsiniz ve bunları Azure 'a geçirmek isteyebilirsiniz. Bu tür iş yüklerini Azure Cosmos DB bir Cassandra API hesabına geçirebilirsiniz. Bu öğretici, Apache Cassandra verilerinin Azure Cosmos DB Cassandra API hesaba geçirilmesi için kullanılabilecek farklı seçeneklere ilişkin yönergeler sağlar.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Geçiş planlaması
> * Geçiş önkoşulları
> * Komutunu kullanarak verileri geçirme `cqlsh` `COPY`
> * Spark'ı kullanarak veri geçirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites-for-migration"></a>Geçiş önkoşulları

* **Verimlilik Ihtiyaçlarınızı tahmin edin:** Azure Cosmos DB Cassandra API hesabına veri geçirmeden önce, iş yükünüzün işleme ihtiyaçlarını tahmin etmeniz gerekir. Genel olarak, CRUD işlemleri için gereken ortalama aktarım hızı ile başlayın ve ardından, ayıklamayı Ayıkla Load veya çıkmanız gerekirse işlemleri için gereken ek aktarım hızını ekleyin. Geçişi planlamak için şu ayrıntılara ihtiyacınız vardır: 

  * **Mevcut veri boyutu veya tahmini veri boyutu:** Minimum veritabanı boyutunu ve aktarım hızı gereksinimini tanımlar. Yeni bir uygulama için veri boyutu tahmin ediyorsanız, verilerin satırlar arasında eşit bir şekilde dağıtıldığını varsayabilir ve değeri veri boyutuyla çarparak tahmin edebilirsiniz. 

  * **Gerekli aktarım hızı:** Okuma (sorgu/Get) ve yazma (güncelleştirme/silme/ekleme) işlemlerinin yaklaşık aktarım hızı. Bu değer, sabit durum veri boyutuyla birlikte gerekli istek birimlerini hesaplamak için gereklidir.  

  * **Şema:** Aracılığıyla mevcut Cassandra kümenize bağlanın `cqlsh` ve şemayı Cassandra 'dan dışarı aktarın: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Mevcut iş yükünüzün gereksinimlerini tanımladıktan sonra, toplanan verimlilik gereksinimlerine göre Azure Cosmos DB bir hesap, veritabanı ve kapsayıcılar oluşturun.  

  * **Bir işlem IÇIN ru ücreti belirleme:** Cassandra API tarafından desteklenen SDK 'Lardan birini kullanarak RUs 'yi belirleyebilirsiniz. Bu örnekte .NET sürümünün RU ücretleri gösterilmektedir.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Gerekli aktarım hızını ayırma** Gereksinimleriniz arttıkça Azure Cosmos DB depolamayı ve aktarım hızını otomatik olarak ölçeklendirilebilir. Aktarım hızı gereksinimlerinizi tahmin etmek için [Azure Cosmos DB istek birimi hesaplayıcısını](https://www.documentdb.com/capacityplanner) kullanabilirsiniz. 

* **Cassandra API hesapta tablo oluşturun:** Veri geçirmeye başlamadan önce, tüm tablolarınızın Azure portal veya ' den önceden oluşturulmasını önyükleyebilirsiniz `cqlsh` . Veritabanı düzeyinde aktarım hızı olan bir Azure Cosmos DB hesabına geçiş yapıyorsanız, kapsayıcıları oluştururken bir bölüm anahtarı sağladığınızdan emin olun.

* **Aktarım hızını artırma:** Veri geçişinizin süresi, Azure Cosmos DB'deki tablolar için sağladığınız aktarım hızı miktarına bağlıdır. Geçiş süresince aktarım hızını artırın. Daha yüksek aktarım hızı ile, hız sınırlamayı önleyebilir ve daha kısa sürede geçişi tamamlayabilirsiniz. Geçişi tamamladıktan sonra maliyet tasarrufu sağlamak için aktarım hızını azaltın. Ayrıca, kaynak veritabanınız ile aynı bölgede Azure Cosmos DB hesabınızın olması önerilir. 

* **TLS 'Yi etkinleştir:** Azure Cosmos DB katı güvenlik gereksinimleri ve standartları vardır. Hesabınızla etkileşim kurarken TLS ' i etkinleştirdiğinizden emin olun. CQL 'i SSH ile birlikte kullandığınızda TLS bilgilerini sağlama seçeneğiniz vardır.

## <a name="options-to-migrate-data"></a>Verileri geçirme seçenekleri

Mevcut Cassandra iş yüklerinden verileri `cqlsh` `COPY` , komutunu kullanarak veya Spark kullanarak Azure Cosmos DB taşıyabilirsiniz. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Csqlsh COPY komutunu kullanarak verileri geçirme

Yerel verileri Azure Cosmos DB Cassandra API hesabına kopyalamak için [CQL copy komutunu](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) kullanın.

1. Cassandra API hesabınızın bağlantı dizesi bilgilerini alın:

   * [Azure Portal](https://portal.azure.com)oturum açın ve Azure Cosmos DB hesabınıza gidin.

   * **Bağlantı dizesi** bölmesini açın. Buradan Cassandra API hesabınıza bağlanmak için ihtiyacınız olan tüm bilgileri görürsünüz `cqlsh` .

1. `cqlsh`Portalda bağlantı bilgilerini kullanarak oturum açın.

1. `CQL` `COPY` Yerel verileri Cassandra API hesabına kopyalamak için komutunu kullanın.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Spark'ı kullanarak veri geçirme 

Spark ile Cassandra API hesabına veri geçirmek için aşağıdaki adımları kullanın:

1. [Azure Databricks kümesi](cassandra-spark-databricks.md) veya [Azure HDInsight kümesi](cassandra-spark-hdinsight.md)sağlayın. 

1. [Tablo kopyalama işlemini](cassandra-spark-table-copy-ops.md)kullanarak verileri hedef Cassandra API uç noktaya taşıyın. 

Azure sanal makinelerinde veya başka bir bulutta mevcut bir kümede bulunan verileriniz varsa Spark işleri kullanılarak verilerin geçirilmesi önerilen bir seçenektir. Bunu yapmak için Spark 'ı tek seferlik veya düzenli alma için bir aracı olarak ayarlamanız gerekir. Bu geçişi, şirket içi ortamınız ile Azure arasında Azure ExpressRoute bağlantısı kullanarak hızlandırabilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, kaynak grubunu, Azure Cosmos DB hesabını ve tüm ilgili kaynakları silebilirsiniz. Bunu yapmak için, sanal makine için kaynak grubunu seçin, **Sil**' i seçin ve ardından silinecek kaynak grubunun adını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Cosmos DB bir Cassandra API hesabına verilerinizi nasıl geçirebileceğiniz öğrendiniz. Artık Azure Cosmos DB diğer kavramlar hakkında bilgi edinebilirsiniz:

> [!div class="nextstepaction"]
> [Azure Cosmos DB'deki ayarlanabilir tutarlılık düzeyleri](../cosmos-db/consistency-levels.md)




