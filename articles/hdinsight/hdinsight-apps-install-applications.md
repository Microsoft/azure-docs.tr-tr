---
title: Azure HDInsight 'a üçüncü taraf uygulamaları yükler
description: Azure HDInsight 'a üçüncü taraf Apache Hadoop uygulamaları yüklemeyi öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 46a22b78ade3d137f25b14e5cbdaff3264208228
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868401"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight 'a üçüncü taraf Apache Hadoop uygulamaları yüklemeyin

Azure HDInsight 'a bir üçüncü taraf [Apache Hadoop](https://hadoop.apache.org/) uygulaması yüklemeyi öğrenin. Kendi uygulamanızı yükleme yönergeleri için bkz. [Özel HDInsight uygulamaları yükleme](hdinsight-apps-install-custom-applications.md).

An HDInsight uygulama, kullanıcıların bir HDInsight kümesine yükleyebir uygulamadır. Bu uygulamalar Microsoft veya bağımsız yazılım satıcıları (ISV) tarafından ya da sizin tarafınızdan geliştirilebilir.  

Aşağıdaki listede yayımlanan uygulamalar gösterilmektedir:

|Uygulama |Küme türleri | Description |
|---|---|---|
|[AtScale zeka platformu](https://aws.amazon.com/marketplace/pp/AtScale-AtScale-Intelligence-Platform/B07BWWHH18) |Hadoop |AtScale, HDInsight kümenizi genişleme bir OLAP sunucusuna dönüştürür ve Microsoft Excel, Power BI, Tableau Software to QlikView ' den daha önce bildiğiniz, sahip olduğunuz ve sevdiğiniz BI araçlarını kullanarak milyarlarca verileri etkileşimli olarak sorgulamanızı sağlar. |
|[HDInsight için CDAP](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP, Hadoop için zaman değerini hızlandıran ve self servis verileri sağlamasına olanak tanıyan büyük verilere yönelik ilk Birleşik tümleştirme platformudur. Açık kaynak ve genişletilebilir, CDAP yeniliklere yönelik engelleri kaldırır. Gereksinimler: 4 bölge düğümü, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Analiz için verilerinizi hazırlamaya, keşfetmeye ve yapılandırmaya yönelik self servis ölçeklenebilir platformu, karmaşık çok kaynaklı verileri, kurumsal ölçekte daha hızlı ve daha akıllı Öngörüler sunarak değerli iş için hazır bilgilere açmayı hızlandırır. |
|[HDInsight üzerinde dataıku DSS](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Veri bilimcilerinin ve veri analistlerinin yeni veri ürünlerini ve hizmetlerini daha verimli bir şekilde tasarlamak ve çalıştırmak ve ham verileri kesin tahmine dayalı tahmine dayalı olarak yürütmek üzere işbirliği yapmasına imkan tanıyan bir kurumsal veri bilimi platformunda dataiku DSS. |
|[WANdisco Fusion HDI uygulaması](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark, HBase, fırtınası, Kafka |Verilerin dağıtılmış bir ortamda tutarlı tutulması çok büyük bir veri işlemleri sınamadır. Kurumsal sınıf yazılım platformu olan WANdisco Fusion, hiçbir ortamda yapılandırılmamış veri tutarlılığını etkinleştirerek bu sorunu çözer. |
|[HDInsight için H2O mini Susu](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Suze su aşağıdaki dağıtılmış algoritmaları destekler: GLM, Naïve Bayes, dağıtılmış rastgele orman, gradyan arttırma makinesi, derin sinir ağlar, derin öğrenme, K-anlamı, PCA, genelleştirilmiş düşük sıra modelleri, anomali algılama, oto kodlayıcılar. |
|[HDInsight 'a Real-Time veri tümleştirmesi için anlık ileti çabam](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop, HBase, fırtınası, Spark, Kafka |Anlık ileti ("Stream"), uçtan uca bir akış veri tümleştirmesi + zekası platformudur ve farklı veri akışlarının sürekli alımı, işlenmesi ve analizine olanak sağlar. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Yüksek düzeyde, Jumbe, kuruluşlara, 1 ' e yardımcı olur. Tez, MapReduce & Spark Engine tabanlı Hive, Java, Scala iş yükü performansı hızlandırıcı. 2. Proaktif Hadoop kümesi Izleme, 3. Dağıtılmış dosya sisteminde veri kalitesi yönetimi oluşturma. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Apache Kylin tarafından desteklenen Kırgızca kurumsal, büyük veriler için bı sağlar. Hadoop 'ta kurumsal bir OLAP altyapısı olarak, Kırgızca kurumsal analistleri, sektör standardı veri ambarı ve bı metodolojisi ile Hadoop üzerinde bı 'ı mimariye kadar işletmelerini sağlar. |
|[Yıldız yağmuru Azure HDInsight için](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto, hızlı ve ölçeklenebilir bir dağıtılmış SQL sorgu altyapısıdır. Depolama ve işlem ayrımı için tasarlanmış, Presto, Azure Data Lake Storage, Azure Blob Storage, SQL ve NoSQL veritabanları ve diğer veri kaynaklarında veri sorgulamak için mükemmeldir. |
|[HDInsight bulutu için StreamSets veri toplayıcısı](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets veri toplayıcısı, verileri gerçek zamanlı olarak akıp basit ve güçlü bir altyapıdır. Veri akışlarındaki verileri yönlendirmek ve işlemek için veri toplayıcıyı kullanın. 30 günlük deneme lisansı ile birlikte gelir. |
|[Trifacta Wrangler kurumsal](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark, HBase |HDInsight için trifacta Wrangler Enterprise, herhangi bir veri ölçeği için kurumsal çapta veri wrangini destekler. Azure üzerinde Trifacta çalıştırmanın maliyeti, üç aylık abonelik maliyetlerinin yanı sıra sanal makineler için Azure altyapı maliyetlerinin bir birleşimidir. |
|[Sınıfı olmayan veri platformu](https://www.crunchbase.com/organization/unifi-software) |Hadoop, HBase, fırtınası, Spark |Sınıfı olmayan veri platformu, iş kullanıcısına artımlı gelir sağlayan veri zorluklarını ortadan kaldırmak, maliyetleri azaltmak veya işlemsel karmaşıklığı düşürmek için tasarlanan, sorunsuz bir şekilde tümleştirilmiş self servis veri araçları paketidir. |
|[Unkıveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |HDInsight Spark kümesi için veri uygulamasını kaldırma. |
|[Su hattı AI-Driven Veri Kataloğu](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |İş terimleriyle verileri otomatik olarak etiketlemek için AI kullanarak verileri düzenler, düzenler ve yönetir. Sulu boya iş literı kataloğu, self servis analiz, uyumluluk ve idare ve BT yönetimi girişimleri için kritik, başarılı bir bileşendir. |

Bu makalede verilen yönergeler Azure portalı kullanmaktadır. Ayrıca, portaldan Azure Resource Manager şablonunu dışarı aktarabilir veya satıcılardan Kaynak Yöneticisi şablonunun bir kopyasını alabilir ve şablonu dağıtmak için Azure PowerShell ve Azure klasik CLı kullanabilirsiniz.  Bkz. [Kaynak Yöneticisi şablonları kullanarak HDInsight 'ta Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Önkoşullar
HDInsight uygulamalarını mevcut bir HDInsight kümesine yüklemek istiyorsanız bir HDInsight kümesine sahip olmanız gerekir. Küme oluşturmak için bkz. [Küme oluşturma](hadoop/apache-hadoop-linux-tutorial-get-started.md). HDInsight uygulamalarını ayrıca bir HDInsight kümesi oluştururken yükleyebilirsiniz.

## <a name="install-applications-to-existing-clusters"></a>Var olan kümelere uygulama yükleme
Aşağıdaki yordamda var olan bir HDInsight kümesine HDInsight uygulamalarının nasıl yükleneceği gösterilmektedir.

**HDInsight uygulaması yüklemesi**

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol menüden **Tüm Service**  >  **Analytics**  >  **HDInsight kümelerine** gidin.
3. Listeden bir HDInsight kümesi seçin.  Henüz yoksa öncelikle bir tane oluşturmanız gerekir.  bkz. [Küme oluşturma](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. **Ayarlar** kategorisi altında **uygulamalar**' ı seçin. Ana pencerede yüklü uygulamaların bir listesini görebilirsiniz. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png" alt-text="HDInsight uygulamaları portal menüsü":::
5. Menüden **+ Ekle** ' yi seçin. Kullanılabilir uygulamaların bir listesini görebilirsiniz.  **+ Ekle** gri ise bu, HDInsight kümesinin bu sürümü için bir uygulama olmadığı anlamına gelir.
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png" alt-text="HDInsight uygulamaları kullanılabilir uygulamalar":::
6. Kullanılabilir uygulamalardan birini seçin ve ardından geçerli koşulları kabul etmek için yönergeleri izleyin.

Yükleme durumunu Portal bildirimlerinden görebilirsiniz (portalın üst kısmındaki zil simgesini seçin). Uygulama yüklendikten sonra, uygulama yüklü uygulamalar listesinde görünür.

## <a name="install-applications-during-cluster-creation"></a>Küme oluşturma sırasında uygulama yükleme

Bir küme oluştururken HDInsight uygulamaları yükleme seçeneğine sahipsiniz. İşlem sırasında, küme oluşturulup çalışır duruma geldikten sonra HDInsight uygulamaları yüklenir. Azure portal kullanarak küme oluşturma sırasında uygulamaları yüklemek için **yapılandırma + fiyatlandırma** sekmesinden **+ Uygulama Ekle**' yi seçin.

:::image type="content" source="./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png" alt-text="Azure portal kümesi yapılandırma uygulamaları":::

## <a name="list-installed-hdinsight-apps-and-properties"></a>Yüklü HDInsight uygulamalarını ve özelliklerini listeleme
Portal bir küme için yüklü HDInsight uygulamalarının listesini ve yüklü olan her bir uygulamanın özelliklerini gösterir.

**HDInsight uygulamasını listeleme ve özellikleri görüntüleme**

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol menüden **Tüm Service**  >  **Analytics**  >  **HDInsight kümelerine** gidin.
3. Listeden bir HDInsight kümesi seçin.
4. **Ayarlar** kategorisi altında **uygulamalar**' ı seçin. Ana pencerede yüklü uygulamaların bir listesini görebilirsiniz. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png" alt-text="HDInsight uygulamaları yüklü uygulamalar":::
5. Özelliği göstermek için yüklü uygulamalardan birini seçin. Özellik listeleri:

    |Özellik | Açıklama |
    |---|---|
    |Uygulama adı |Uygulama adı. |
    |Durum |Uygulama durumu. |
    |Web |Kenar düğümüne dağıttığınız Web uygulamasının URL 'SI. Kimlik bilgisi, küme için yapılandırdığınız HTTP kullanıcısı kimlik bilgileri ile aynıdır. |
    |SSH uç noktası |Kenar düğümüne bağlanmak için SSH kullanabilirsiniz. SSH kimlik bilgileri, küme için yapılandırdığınız SSH kullanıcısı kimlik bilgileriyle aynıdır. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Description | Uygulama açıklaması. |

6. Bir uygulamayı silmek için, uygulamaya sağ tıklayın ve ardından bağlam menüsünden **Sil** ' e tıklayın.

## <a name="connect-to-the-edge-node"></a>Kenar düğümüne bağlanma
HTTP ve SSH kullanarak kenar düğümüne bağlanabilirsiniz. Uç nokta bilgileri [portalda](#list-installed-hdinsight-apps-and-properties) bulunabilir. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

HTTP uç noktası kimlik bilgileri, HDInsight kümesi için yapılandırdığınız HTTP kullanıcı kimlik bilgileridir; SSH uç noktası kimlik bilgileri, HDInsight kümesi için yapılandırdığınız SSH kimlik bilgileridir.

## <a name="troubleshoot"></a>Sorun giderme
Bkz. [Yükleme sorunlarını giderme](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Sonraki adımlar
* [Özel HDInsight uygulamaları yüklemek](hdinsight-apps-install-custom-applications.md): yayımlanmamış bir HDInsight uygulamasını HDInsight 'a dağıtmayı öğrenin.
* [HDInsight uygulamalarını yayımlama](hdinsight-apps-publish-applications.md): Özel HDInsight uygulamalarınızı Azure Marketi’nde nasıl yayımlayacağınızı öğrenin.
* [MSDN: HDInsight uygulaması yükleme](/rest/api/hdinsight/hdinsight-application): HDInsight uygulamalarını nasıl tanımlayacağınızı öğrenin.
* [Betik Eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md): ek uygulamalar yüklemek için Betik Eyleminin nasıl kullanılacağını öğrenin.
* [HDInsight 'ta Kaynak Yöneticisi şablonları kullanarak Linux tabanlı Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md): HDInsight kümeleri oluşturmak için Kaynak Yöneticisi şablonlarının nasıl çağrılacağını öğrenin.
* [HDInsight’ta boş kenar düğümleri kullanma](hdinsight-apps-use-edge-node.md): HDInsight kümesine erişmek, HDInsight uygulamaları test etmek ve HDInsight uygulamalarını barındırmak için boş bir kenar düğümünü kullanmayı öğrenin.