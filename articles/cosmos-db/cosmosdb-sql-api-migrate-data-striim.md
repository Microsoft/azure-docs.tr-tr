---
title: Anlık ileti kullanarak Azure Cosmos DB SQL API hesabına veri geçirme
description: Bir Oracle veritabanından Azure Cosmos DB bir SQL API hesabına veri geçirmek için nasıl çaba ım kullanacağınızı öğrenin.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003282"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Anlık ileti kullanarak Azure Cosmos DB SQL API hesabına veri geçirme
 
Azure Marketi 'ndeki çarpıcı anlık ileti resmi, veri ambarlarından ve veritabanlarından Azure 'a sürekli gerçek zamanlı veri hareketi sunar. Verileri taşırken, satır içi normalleştirmeyi, veri dönüştürmeyi ve gerçek zamanlı analizleri ve veri raporlama senaryolarını sağlayabilirsiniz. Kurumsal verileri sürekli olarak Azure Cosmos DB SQL API 'sine taşımaya kadar hızlı bir şekilde çalışmaya başlamak kolaydır. Azure, hızlı anlık ileti dağıtmayı ve verilerin Azure Cosmos DB geçişini kolaylaştıran bir market teklifi sunar. 

Bu makalede, verileri bir **Oracle veritabanından** **Azure Cosmos DB bir SQL API hesabına**geçirmek için nasıl çaba ım kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure aboneliğiniz](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

* Şirket içinde, içindeki bazı verilerle çalışan bir Oracle veritabanı.

## <a name="deploy-the-striim-marketplace-solution"></a>Çarpıcı anlık ileti marketi çözümünü dağıtma

1. [Azure portal](https://portal.azure.com/) oturum açın.

1. **Kaynak oluştur** ' u seçin ve Azure Marketi 'nde **anlık ileti** araması yapın. İlk seçeneği seçin ve **oluşturun**.

   ![Çarpıcı anlık ileti marketi öğesi bulun](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Sonra, çaba anlık ileti örneğinin yapılandırma özelliklerini girin. Çalışır durumda anlık ileti ortamı bir sanal makinede dağıtılır. **Temel bilgiler** bölmesinden VM **Kullanıcı adı**' nı, **VM PAROLASıNı** gırın (Bu parola VM 'ye SSH için kullanılır). E-mesajlaşma dağıtmak istediğiniz **abonelik**, **kaynak grubu**ve **konum ayrıntılarınızı** seçin. Tamamlandıktan sonra **Tamam**' ı seçin.

   ![Çarpıcı anlık ileti için temel ayarları yapılandırma](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. **Anlık Ileti kümesi ayarları** bölmesinde, çaba anlık ileti dağıtımı türünü ve sanal makine boyutunu seçin.

   |Ayar | Değer | Açıklama |
   | ---| ---| ---|
   |Anlık ileti dağıtım türü |Bağımsız | Anlık ileti, **tek başına** veya **küme** dağıtım türlerinde çalıştırılabilir. Tek başına modu, tek bir sanal makineye çok anlık ileti sunucusu dağıtır ve veri biriminize bağlı olarak VM 'lerin boyutunu seçebilirsiniz. Küme modu, her iki veya daha fazla VM üzerinde, bir veya daha fazla sanal makine için, seçilen boyuta sahip 2 ' den fazla düğümü olan küme ortamları otomatik yüksek kullanılabilirlik ve yük devretme sağlar.</br></br> Bu öğreticide tek başına seçeneğini belirleyebilirsiniz. Varsayılan "Standard_F4s" boyut VM 'sini kullanın.  | 
   | Çarpıcı anlık ileti kümesinin adı|    < Striim_cluster_Name >|  Çarpıcı anlık ileti kümesinin adı.|
   | Anlık ileti kümesi parolası|   < Striim_cluster_password >|  Küme için parola.|

   Formu doldurduktan sonra devam etmek için **Tamam** ' ı seçin.

1. **Anlık ileti erişimi ayarları** bölmesinde, **genel IP adresini** (varsayılan değerleri seçin), her şeye **yönelik etki alanı adını**, çarpıcı ım Kullanıcı arabiriminde oturum açmak için kullanmak istediğiniz **yönetici parolasını** yapılandırın. VNET ve alt ağ yapılandırın (varsayılan değerleri seçin). Ayrıntıları doldurduktan sonra devam etmek için **Tamam** ' ı seçin.

   ![Anlık ileti erişim ayarları](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure dağıtımı doğrular ve her şeyin iyi göründüğünden emin olur; doğrulamanın tamamlanmasının birkaç dakika sürer. Doğrulama tamamlandıktan sonra **Tamam**' ı seçin.
  
1. Son olarak kullanım koşullarını gözden geçirin ve **Oluştur** ' u seçerek çarpıcı anlık ileti örneğinizi oluşturun. 

## <a name="configure-the-source-database"></a>Kaynak veritabanını yapılandırma 

Bu bölümde, Oracle veritabanını veri taşıma kaynağı olarak yapılandırırsınız.  Oracle 'a bağlanmak için [Oracle JDBC sürücüsüne](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) ihtiyacınız olacak. Kaynak Oracle veritabanınızdan değişiklikleri okumak için [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) ya da [XStream API 'lerini](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)kullanabilirsiniz. Oracle veritabanı 'ndaki verileri okumak, yazmak veya sürdürmek için, bir mini ım 'in Java sınıfsıya 'da Oracle JDBC sürücüsü bulunmalıdır.

[Ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) sürücüsünü yerel makinenize indirin. Bunu daha sonra çaba anlık ileti kümesine yükleyeceksiniz.

## <a name="configure-the-target-database"></a>Hedef veritabanını yapılandırma

Bu bölümde, Azure Cosmos DB SQL API hesabını veri taşıma hedefi olarak yapılandıracaksınız.

1. Azure portal kullanarak [Azure Cosmos DB BIR SQL API hesabı](create-cosmosdb-resources-portal.md) oluşturun.

1. Azure Cosmos hesabınızdaki **Veri Gezgini** bölmesine gidin. Yeni kapsayıcı oluşturmak için **yeni kapsayıcı** ' yı seçin. Oracle veritabanından Azure Cosmos DB ' ye *ürün* ve *sipariş* verileri geçirmekte olduğunuz varsayılmaktadır. **Orders**adlı bir kapsayıcı ile **ilginç imdemo** adlı yeni bir veritabanı oluşturun. Kapsayıcıyı **1000** ru ile sağlayın (bu örnekte 1000 ru kullanılır, ancak iş yükünüz için tahmin edilen aktarım hızı ve bölüm anahtarı olarak **/Order_ıd** kullanılır). Bu değerler, kaynak verilerinize göre farklılık gösterir. 

   ![SQL API hesabı oluşturma](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Oracle 'ı veri akışı Azure Cosmos DB için yapılandırma

1. Şimdi anlık ileti almaya geri bakalım. Bir anlık ileti ile etkileşime geçmeden önce, daha önce indirdiğiniz Oracle JDBC sürücüsünü yükleyebilirsiniz.

1. Azure portal dağıttığınız çarpıcı anlık ileti örneğine gidin. Üstteki menü çubuğunda **Bağlan** düğmesini seçin ve **SSH** sekmesinden **VM yerel hesabı alanını kullanarak oturum açma** içindeki URL 'yi kopyalayın.

   ![SSH URL 'sini al](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Yeni bir Terminal penceresi açın ve Azure portal kopyaladığınız SSH komutunu çalıştırın. Bu makale bir MacOS 'ta Terminal kullanır, bir Windows makinesinde PuTTY veya farklı bir SSH istemcisi kullanarak benzer yönergeleri izleyebilirsiniz. İstendiğinde, devam etmek için **Evet** yazın ve önceki adımda sanal makine için ayarladığınız **parolayı** girin.

   ![E-mesajlaşma sanal makinesine bağlanma](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Şimdi, daha önce indirdiğiniz **ojdbc8. jar** dosyasını kopyalamak için yeni bir Terminal sekmesi açın. Aşağıdaki SCP komutunu kullanarak jar dosyasını yerel makinenizden Azure 'da çalışan Çabaım örneğinin tmp klasörüne kopyalayın:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Jar dosyasını konum makinesinden, anlık ım 'e kopyalayın](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Daha sonra, çarpıcı anlık ileti örneği için SSH yaptığınız ve sudo olarak oturum açmanın bulunduğu pencereye geri gidin. **Ojdbc8. jar** dosyasını **/tmp** dizininden, aşağıdaki komutları kullanarak, diğer anlık ileti örneğinizin **lib** dizinine taşıyın:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Jar dosyasını LIB klasörüne taşı](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Aynı Terminal penceresinde, aşağıdaki komutları yürüterek Çabaım sunucusunu yeniden başlatın:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Kısa ım 'in başlaması bir dakika sürer. Durumu görmek isterseniz, aşağıdaki komutu çalıştırın: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Şimdi Azure 'a geri gidin ve çarpıcı ım VM 'nizin genel IP adresini kopyalayın. 

   ![Çalışır anlık ileti VM IP adresini kopyala](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. E-postayla Web Kullanıcı arabirimine gitmek için, tarayıcıda yeni bir sekme açın ve ardından genel IP 'yi kopyalayın: 9080. **Yönetici** Kullanıcı adını kullanarak, Azure Portal belirttiğiniz yönetici parolasıyla birlikte oturum açın.

   ![Anlık ileti almak için oturum açın](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Şimdi bir ım ana sayfasına ulaşacağız. **Panolar**, **uygulamalar**ve **sourcepreview**olmak üzere üç farklı bölme vardır. Panolar bölmesi, verileri gerçek zamanlı olarak taşımanızı ve görselleştirmenizi sağlar. Uygulamalar bölmesi, akış verileri işlem hatlarınızı veya veri akışlarını içerir. Sayfanın sağ tarafında, verilerinizi taşımadan önce önizlemeniz için SourcePreview bulunur.

1. **Uygulamalar** bölmesini seçin, şimdilik bu bölmeye odaklanacağız. Anlık ileti alma hakkında bilgi edinmek için kullanabileceğiniz çeşitli örnek uygulamalar vardır, ancak bu makalede kendinizuzu oluşturacaksınız. Sağ üst köşedeki **Uygulama Ekle** düğmesini seçin.

   ![Çarpıcı anlık ileti uygulaması ekleme](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Çarpıcı anlık ileti uygulamaları oluşturmanın birkaç farklı yolu vardır. Mevcut bir şablonla başlamak için **şablonla Başlat** ' ı seçin.

   ![Uygulamayı şablonla Başlat](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. **Şablon ara** alanında "Cosmos" yazın ve hedef ' i seçin **: Azure Cosmos DB** ve ardından **Azure Cosmos DB Oracle CDC**öğesini seçin.

   ![Cosmos DB Oracle CDC 'yi seçin](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. Sonraki sayfada, uygulamanızı adlandırın. **Oratocosmosdb** gibi bir ad sağlayabilir ve ardından **Kaydet**' i seçebilirsiniz.

1. Ardından, kaynak Oracle örneğinizin kaynak yapılandırmasını girin. **Kaynak adı**için bir değer girin. Kaynak adı, gitim uygulaması için yalnızca bir adlandırma kuralıdır, **src_onPremOracle**gibi bir ad kullanabilirsiniz. Geri kalan kaynak parametreleri **URL 'si**, **Kullanıcı adı**, **parola**değerlerini girin, Oracle 'Dan verileri okumak için okuyucu olarak **LogMiner** ' ı seçin. Devam etmek için **İleri**’yi seçin.

   ![Kaynak parametrelerini Yapılandır](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. Anlık ileti, ortamınızı denetlecektir ve kaynak Oracle örneğinizle bağlantı kurmak için doğru ayrıcalıklara sahip olduğundan ve CDC 'nin doğru şekilde yapılandırıldığından emin olmanızı sağlar. Tüm değerler doğrulandıktan sonra **İleri**' yi seçin.

   ![Kaynak parametrelerini doğrula](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Geçirmek istediğiniz Oracle veritabanından tabloları seçin. Örneğin, Orders tablosunu seçip **İleri**' yi seçelim. 

   ![Kaynak tabloları Seç](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. Kaynak tabloyu seçtikten sonra eşleme ve filtreleme gibi daha karmaşık işlemler gerçekleştirebilirsiniz. Bu durumda, yalnızca Azure Cosmos DB kaynak tablonuzun bir çoğaltmasını oluşturacaksınız. Bu nedenle, hedefi yapılandırmak için **İleri** ' yi seçin

1. Şimdi hedefi yapılandıralim:

   * **Hedef adı** -hedef için bir kolay ad sağlayın. 
   * **Giriş kaynağı** -açılan listeden, kaynak Oracle yapılandırmasında oluşturduğunuz bir giriş akışını seçin. 
   * **Koleksiyonlar**-hedef Azure Cosmos DB yapılandırma özelliklerini girin. Koleksiyonlar sözdizimi, **sourceschema. SourceTable, TargetDatabase. TargetContainer '** dir. Bu örnekte, değer "SISTEM" olur. SIPARIŞLER, Ilginç tanıtım. Orders ". 
   * **AccessKey** -Azure Cosmos hesabınızın PrimaryKey 'i.
   * **ServiceEndpoint** – Azure Cosmos hesabınızın URI 'si, Azure Portal **anahtarlar** bölümü altında bulunabilir. 

   **Kaydet** ve **İleri ' yi**seçin.

   ![Hedef parametrelerini Yapılandır](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Daha sonra akış tasarlayıcısından geleceksiniz ve akış uygulamalarınızı oluşturmak için kutu bağlayıcılarını sürükleyip bırakabilirsiniz. Akışta bu noktada herhangi bir değişiklik yapamazsınız. Bu nedenle uygulamayı **Dağıt** düğmesini seçerek uygulamayı dağıtın.
 
   ![Uygulamayı dağıtma](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. Dağıtım penceresinde, uygulamanızın belirli kısımlarını dağıtım topolojinizin belirli bölümlerinde çalıştırmak istediğinizi belirtebilirsiniz. Azure aracılığıyla basit bir dağıtım topolojisinde çalıştığımız için varsayılan seçeneği kullanacağız.

   ![Varsayılan seçeneği kullanın](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. Dağıttıktan sonra, veri akışını görmek için akışın önizlemesini görüntüleyebilirsiniz. Yanındaki **dalga** simgesini ve eyebol simgesini seçin. Üstteki menü çubuğunda **dağıtılan** düğmesini seçin ve **Uygulamayı Başlat**' ı seçin.

   ![Uygulamayı başlatma](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. **CDC (değişiklik verilerini yakalama)** okuyucusunu kullanarak, anlık ileti, veritabanında yalnızca yeni değişiklikler seçer. Kaynak Tablolarınızda veri akışı varsa, bunu görürsünüz. Ancak, bu bir demo tablosu olduğundan, kaynak herhangi bir uygulamaya bağlı değildir. Örnek veri Oluşturucu kullanıyorsanız, Oracle veritabanınıza bir olay zinciri ekleyebilirsiniz.

1. Çarpıcı anlık ileti platformunda veri akışını görürsünüz. Anlık ileti, tablonuz ile ilişkili tüm meta verileri de alır ve bu da verileri izlemek ve verilerin doğru hedefte olmasını sağlamak için yararlıdır.

   ![CDC ardışık düzeni yapılandırma](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Son olarak Azure 'da oturum açalım ve Azure Cosmos hesabınıza gitmeniz gerekir. Veri Gezgini yenileyin ve verilerin geldiğini görebilirsiniz.  

   ![Azure 'da geçirilen verileri doğrulama](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Azure 'da çabam çözümünü kullanarak verileri, Oracle, Cassandra, MongoDB gibi çeşitli kaynaklardan sürekli olarak Azure Cosmos DB Azure Cosmos DB ve çeşitli diğer kaynaklardan sürekli olarak geçirebilirsiniz. Daha fazla bilgi edinmek için lütfen bir anlık ileti [Web sitesini](https://www.striim.com/)ziyaret edin, [ücretsiz bir 30 günlük deneme sürümü indirin](https://go2.striim.com/download-free-trial)ve geçiş yolunu sorunsuz bir şekilde ayarlarken bir [destek isteği yapın.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Cosmos DB SQL API 'sine veri geçiriyorsanız, bkz. [anlık ileti kullanarak verileri Cassandra API hesabına geçirme](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Azure Cosmos DB ölçümleriyle verilerinizi izleyin ve hatalarını ayıklayın](use-metrics.md)
