---
title: Blitzz kullanarak Oracle 'dan Azure Cosmos DB Cassandra API verileri geçirme
description: Blitzz kullanarak Oracle veritabanından Azure Cosmos DB Cassandra API nasıl veri geçirebileceğinizi öğrenin.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: fe132ee6ab90fdae99463f11ecf46f352690b810
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984361"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Blitzz kullanarak Oracle 'dan Azure Cosmos DB Cassandra API hesabına veri geçirme

Azure Cosmos DB Cassandra API, Oracle üzerinde çalışan kurumsal iş yükleri için, şunlar gibi çeşitli nedenlerle harika bir seçenek haline geldi:

* **Daha iyi ölçeklenebilirlik ve kullanılabilirlik:** Tek hata noktalarını, daha iyi ölçeklenebilirliği ve uygulamalarınız için kullanılabilirliği ortadan kaldırır.

* **Önemli maliyet tasarrufları:** VM 'nin, bant genişliğinin ve ilgili Oracle lisanslarının maliyetini içeren Azure Cosmos DB maliyeti tasarrufu sağlayabilirsiniz. Ayrıca, veri merkezlerini, sunucuları, SSD depolamayı, ağ iletişimini ve elektrik maliyetlerini yönetmeniz gerekmez.

* **Yönetme ve izleme için ek yük yoktur:** Tam olarak yönetilen bir bulut hizmeti olarak Azure Cosmos DB, bir grup ayarı yönetme ve izleme yükünü ortadan kaldırır.

Veritabanı iş yüklerini bir platformdan diğerine geçirmek için çeşitli yollar vardır. [Blitzz](https://www.blitzz.io) , çeşitli veritabanlarından Azure Cosmos DB için sıfır kesinti süresi geçişi gerçekleştirmeye yönelik güvenli ve güvenilir bir yol sunan bir araçtır. Bu makalede, Blitzz kullanarak verileri Oracle veritabanından Azure Cosmos DB Cassandra API geçirmek için gereken adımlar açıklanmaktadır.

## <a name="benefits-using-blitzz-for-migration"></a>Geçiş için Blitzz kullanma avantajları

Blitzz 'nin geçiş çözümü karmaşık işletimsel iş yüklerini geçirme adımları adım adım yaklaşımla uyar. Aşağıda, Blitzz 'nin sıfır kapalı kalma süresi geçiş planının bazı önemli yönleri verilmiştir:

* Oracle veritabanından Azure Cosmos DB 'e otomatik olarak iş mantığı (tablolar, dizinler, görünümler) geçişi sağlar. Şemaları el ile oluşturmanız gerekmez.

* Blitzz, yüksek hacimli ve paralel veritabanı çoğaltması sunar. Kaynak ve hedef platformların geçiş sırasında değiştirme-Data-Capture (CDC) adlı bir teknik kullanılarak eşitlenmesini sağlar. CDC kullanarak, Blitzz sürekli olarak kaynak veritabanından (Oracle) bir değişiklik akışı çeker ve hedef veritabanına (Azure Cosmos DB) uygular.

* Hataya dayanıklı olur ve sistemdeki bir donanım ya da yazılım arızası sırasında bile verilerin tam olarak bir kez teslim edilmesini sağlar.

* SSL, şifreleme gibi çeşitli güvenlik yöntemlerini kullanarak aktarım sırasında verilerin güvenliğini sağlar.

* PL/SQL 'de yazılan karmaşık iş mantığını Azure Cosmos DB ' deki eşdeğer iş mantığına dönüştürmek için hizmetler sunar.

## <a name="steps-to-migrate-data"></a>Verileri geçirme adımları

Bu bölümde, Blitzz 'yi kurmak ve verileri Oracle veritabanından Azure Cosmos DB 'e geçirirken gereken adımlar açıklanmaktadır.

1. Blitzz Replicant 'i yüklemeyi planladığınız bilgisayardan bir güvenlik sertifikası ekleyin. Bu sertifika, belirtilen Azure Cosmos DB hesabıyla bir SSL bağlantısı kurmak için Blitzz Replicant tarafından gerektirilir. Sertifikayı aşağıdaki adımlarla ekleyebilirsiniz:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. OU, blitzz [Web sitesinde](https://www.blitzz.io)bir demo Isteyerek blitzz yüklemesini ve ikili dosyaları alabilir. Alternatif olarak, takıma [e-posta](mailto:success@blitzz.io) da gönderebilirsiniz.

   ![Blitzz Replicant aracı indirmesi](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz Replicant dosyaları](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. CLı terminalinde, kaynak veritabanı yapılandırmasını ayarlayın. Yapılandırma dosyasını komutunu kullanarak **`vi conf/conn/oracle.yml`** açın ve Oracle düğümlerinin, bağlantı noktası numarasının, Kullanıcı adının, parolasının ve diğer gerekli ayrıntıların IP adreslerinin virgülle ayrılmış bir listesini ekleyin. Aşağıdaki kod, örnek bir yapılandırma dosyası göstermektedir:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   ![Oracle bağlantı düzenleyicisini aç](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Oracle bağlantı yapılandırması](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   Yapılandırma ayrıntılarını doldurduktan sonra dosyayı kaydedin ve kapatın.

1. İsteğe bağlı olarak, kaynak veritabanı filtre dosyasını ayarlayabilirsiniz. Filtre dosyası geçirilecek şemaları veya tabloları belirtir. Komutunu kullanarak **`vi filter/oracle_filter.yml`** yapılandırma dosyasını açın ve aşağıdaki yapılandırma ayrıntılarını girin:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Veritabanı filtresi ayrıntılarını doldurduktan sonra dosyayı kaydedin ve kapatın.

1. Daha sonra hedef veritabanının yapılandırmasını ayarlayacaksınız. Yapılandırmayı tanımladıktan önce [bir Azure Cosmos DB Cassandra API hesabı oluşturun](create-cassandra-dotnet.md#create-a-database-account). Verilerinizde [doğru bölüm anahtarını seçin](partitioning-overview.md#choose-partitionkey) ve ardından bir keyspace ve geçirilen verileri depolamak için bir tablo oluşturun.

1. Verileri geçirmeden önce, uygulamanızın hızla geçirilmesi için gereken miktarda kapsayıcı aktarım hızını artırın. Örneğin, aktarım hızını 100000 ru olarak artırabilirsiniz. Geçiş işlemine başlamadan önce üretilen iş verimini ölçeklendirirken verilerinizi daha az zaman geçirmeye yardımcı olur. 

   ![Genelinde Azure Cosmos kapsayıcısını ölçeklendirin](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   Geçiş işlemi tamamlandıktan sonra aktarım hızını azaltmalısınız. Her işlem için gerekli olan ve RUs veri miktarına bağlı olarak, veri geçişten sonra gereken aktarım hızını tahmin edebilirsiniz. Gerekli olan RUs 'yi tahmin etme hakkında daha fazla bilgi edinmek için bkz. [kapsayıcılar ve veritabanları üzerinde üretilen Iş sağlama](set-throughput.md) ve [Azure Cosmos DB kapasite PLANLAYıCıSı makalelerini kullanarak ru/s 'yi tahmin](estimate-ru-with-capacity-planner.md) etme.

1. **Bağlantı dizesi** bölmesinden Azure Cosmos hesabınızın **Iletişim noktasını, bağlantı noktasını, Kullanıcı adını**ve **birincil parolasını** alın. Yapılandırma dosyasında bu değerleri kullanacaksınız.

1. CLı terminalinde hedef veritabanı yapılandırmasını ayarlayın. Yapılandırma dosyasını komutunu kullanarak **`vi conf/conn/cosmosdb.yml`** açın ve konak URI 'si, bağlantı noktası numarası, Kullanıcı adı, parola ve diğer gerekli parametrelerin virgülle ayrılmış bir listesini ekleyin. Yapılandırma dosyasındaki içeriklerin bir örneği aşağıda verilmiştir:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Sonra Blitzz kullanarak verileri geçirin. Blizz Replicant öğesini **tam** veya **anlık görüntü** modunda çalıştırabilirsiniz:

   * **Tam mod** – bu modda, replıant geçiş sonrasında çalışmaya devam eder ve kaynak Oracle sisteminde herhangi bir değişiklik için dinleme yapılır. Herhangi bir değişiklik algılarsa, hedef Azure Cosmos hesabında gerçek zamanlı olarak çoğaltılır.

   * **Anlık görüntü modu** – bu modda, şema geçişi ve tek seferlik veri çoğaltması gerçekleştirebilirsiniz. Bu seçenekle gerçek zamanlı çoğaltma desteklenmez.


   Yukarıdaki iki modu kullanarak, geçiş sıfır kapalı kalma süresiyle gerçekleştirilebilir.

1. Verileri geçirmek için, Blitzz Replicant CLı terminalden aşağıdaki komutu çalıştırın:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Replicant Kullanıcı arabirimi, çoğaltma ilerlemesini gösterir. Şema geçişi ve anlık görüntü işlemi tamamlandıktan sonra, ilerleme% 100 gösterir. Geçiş işlemi tamamlandıktan sonra hedef Azure Cosmos veritabanındaki verileri doğrulayabilirsiniz.

   ![Oracle veri geçiş çıkışı](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Geçiş için tam modu kullandığınız için, kaynak Oracle veritabanında veri ekleme, güncelleştirme veya silme gibi işlemler gerçekleştirebilirsiniz. Daha sonra, hedef Azure Cosmos veritabanında gerçek zamanlı olarak çoğaltıldıklarından emin olabilirsiniz. Geçişten sonra, Azure Cosmos Kapsayıcınız için yapılandırılmış aktarım hızını azalttığınızdan emin olun.

1. Herhangi bir noktayı durdurup **--özgeçmişi** anahtarıyla yeniden başlatabilirsiniz. Çoğaltma, veri tutarlılığından ödün vermeden durdurulan noktadan devam eder. Aşağıdaki komut, sürdürülmesi anahtarının nasıl kullanılacağını gösterir.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Hedefe yönelik veri geçişi hakkında daha fazla bilgi edinmek için, bkz. [Blitzz Replicant tanıtımı](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Sonraki adımlar

* [Kapsayıcılar ve veritabanları üzerinde üretilen iş sağlama](set-throughput.md) 
* [Bölüm anahtarı en iyi uygulamaları](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB kapasite planlayıcısı makalelerini kullanarak ru/s 'Yi tahmin](estimate-ru-with-capacity-planner.md) etme