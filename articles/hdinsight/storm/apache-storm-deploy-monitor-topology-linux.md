---
title: Azure HDInsight 'ta Apache Storm topolojileri dağıtma ve yönetme
description: Linux tabanlı HDInsight 'ta fırtınası panosunu kullanarak Apache Storm topolojilerini dağıtmayı, izlemeyi ve yönetmeyi öğrenin. Visual Studio için Hadoop araçları 'nı kullanın.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 1c219c85836eb4730fa90918385555c433a12449
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915076"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache Storm topolojileri dağıtma ve yönetme 

Bu belgede, HDInsight kümelerinde fırtınası üzerinde çalışan [Apache Storm](https://storm.apache.org/) topolojilerinin yönetilmesi ve izlenmesi hakkında temel bilgileri öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Storm kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü**için **fırtınası** seçme.


* Seçim SSH ve SCP ile benzerlik: Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Seçim Visual Studio: Azure SDK 2.5.1 veya daha yeni bir sürümü ve Visual Studio için Data Lake araçları. Daha fazla bilgi için bkz. [Visual Studio için Data Lake araçları 'nı kullanmaya başlama](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Visual Studio 'nun aşağıdaki sürümlerinden biri:

  * Güncelleştirme 4 ile Visual Studio 2012

  * Güncelleştirme 4 veya [Visual Studio 2013 topluluğuyla](https://go.microsoft.com/fwlink/?LinkId=517284) Visual Studio 2013
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (herhangi bir sürüm)

  * Visual Studio 2017 (herhangi bir sürümü). Visual Studio 2017 için Data Lake araçları, Azure Iş yükünün bir parçası olarak yüklenir.

## <a name="submit-a-topology-visual-studio"></a>Topoloji gönder: Visual Studio

HDInsight araçları, bir veya daha fazla topolojinizi fırtınası kümenize göndermek C# için kullanılabilir. Aşağıdaki adımlarda örnek bir uygulama kullanılır. HDInsight araçlarını kullanma hakkında daha fazla bilgi için bkz. [Visual Studio C# için HDInsight araçlarını kullanarak topoloji geliştirme](apache-storm-develop-csharp-visual-studio-topology.md).

1. Visual Studio için Data Lake araçları 'nın en son sürümünü henüz yüklemediyseniz bkz. [Visual Studio için Data Lake araçları 'nı kullanmaya başlama](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Visual Studio için Data Lake araçları, daha önce Visual Studio için HDInsight araçları olarak adlandırılmıştı.
    >
    > Visual Studio için Data Lake araçları, Visual Studio 2017 için __Azure Iş yüküne__ eklenmiştir.

2. Visual Studio 'yu açın, **Dosya** > **Yeni** > **Proje**' yi seçin.

3. **Yeni proje** iletişim kutusunda, **yüklü** > **Şablonlar**' ı genişletin ve ardından **HDInsight**' ı seçin. Şablonlar listesinden, **fırtınası örneği**' ni seçin. İletişim kutusunun alt kısmındaki uygulama için bir ad yazın.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

4. **Çözüm Gezgini**, projeye sağ tıklayın ve **HDInsight 'Ta fırtınası 'ya gönder**' i seçin.

   > [!NOTE]  
   > İstenirse, Azure aboneliğinizin oturum açma kimlik bilgilerini girin. Birden fazla aboneliğiniz varsa, HDInsight kümesi için bir fırtınası içeren bir oturum açın.

5. Aşağı **fırtınası** kümesi aşağı açılan listesinden HDInsight kümesi ' ni seçin ve ardından **Gönder**' i seçin. **Çıkış** penceresini kullanarak gönderimin başarılı olup olmadığını izleyebilirsiniz.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Topoloji gönder: SSH ve fırtınası komutu

1. HDInsight kümesine bağlanmak için SSH kullanın. **Kullanıcı adı** ' nı SSH oturum açma adınızın adı ile değiştirin. **Clustername** değerini HDInsight kümenizin adıyla değiştirin:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    HDInsight kümenize bağlanmak için SSH kullanma hakkında daha fazla bilgi için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Örnek bir topoloji başlatmak için aşağıdaki komutu kullanın:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Bu komut, kümede örnek WordCount topolojisini başlatır. Bu topoloji rastgele cümleler oluşturur ve cümlelerde her sözcüğün geçtiği yeri sayar.

   > [!NOTE]  
   > Kümeye topoloji gönderirken `storm` komutunu kullanmadan önce kümeyi içeren jar dosyasını kopyalamanız gerekir. Dosyayı kümeye kopyalamak için `scp` komutunu kullanabilirsiniz. Örneğin, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > WordCount örneği ve diğer storm starter örnekleri `/usr/hdp/current/storm-client/contrib/storm-starter/` konumunda kümenize zaten dahil edilmiştir.

## <a name="submit-a-topology-programmatically"></a>Topoloji gönderme: programlı

Nimbus hizmetini kullanarak bir topolojiyi programlı bir şekilde dağıtabilirsiniz. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)Nimbus hizmeti aracılığıyla bir topolojinin nasıl dağıtılacağını ve başlatılacağını gösteren örnek bir Java uygulaması sağlar.

## <a name="monitor-and-manage-visual-studio"></a>İzleme ve yönetme: Visual Studio

Bir topoloji Visual Studio kullanılarak gönderildiğinde, **fırtınası topolojileri** görünümü görüntülenir. Çalışan topoloji hakkındaki bilgileri görüntülemek için listeden topolojiyi seçin.

![Visual Studio izleyici](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Ayrıca, **Azure**  > HDInsight ' ı genişleterek ve ardından HDInsight kümesinde bir fırtınası üzerine sağ tıklayıp ve sonra da **fırtınası topolojilerini göster**' i seçerek Sunucu Gezgini fırtınası topolojilerini görüntüleyebilirsiniz.

Bu bileşenler hakkındaki bilgileri görüntülemek için spomalar veya cıvatları şeklini seçin. Seçili her öğe için yeni bir pencere açılır.

### <a name="deactivate-and-reactivate"></a>Devre dışı bırakma ve yeniden etkinleştirme

Bir topolojinin devre dışı bırakılması, sonlandıralınana veya yeniden etkinleştirilinceye kadar duraklatılır. Bu işlemleri gerçekleştirmek için __topoloji özetinin__en üstündeki __devre dışı bırak__ ve __yeniden etkinleştir__ düğmelerini kullanın.

### <a name="rebalance"></a>Yeniden Dengele

Bir topolojinin yeniden dengelenmesi sistemin, topolojinin paralelliğini değiştirmesine olanak tanır. Örneğin, daha fazla not eklemek için kümeyi yeniden boyutlandırdıysanız, yeniden dengeleme bir topolojinin yeni düğümleri görmesini sağlar.

Bir topolojiyi yeniden dengelemek için __topoloji özetinin__en üstündeki yeniden __Dengeleme__ düğmesini kullanın.

> [!WARNING]  
> Bir topolojinin yeniden dengelenmesi öncelikle topolojiyi devre dışı bırakır ve sonra çalışanları küme genelinde eşit olarak yeniden dağıtır, son olarak, yeniden dengeleme gerçekleştirilmeden önce topolojiyi içinde olduğu duruma döndürür. Bu nedenle topoloji etkin ise, tekrar etkin hale gelir. Devre dışı bırakılmışsa, devre dışı kalır.

### <a name="kill-a-topology"></a>Topolojiyi sonlandırma

Fırtınası topolojileri durduruluncaya veya küme silinene kadar çalışmaya devam eder. Bir topolojiyi durdurmak için __topoloji özetinin__en üstündeki __KILL__ düğmesini kullanın.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>İzleme ve yönetme: SSH ve fırtınası komutu

Yardımcı `storm` programı, çalışan topolojilerle komut satırından çalışmanıza olanak sağlar. Komutların `storm -h` tam listesi için kullanın.

### <a name="list-topologies"></a>Topolojileri Listele

Çalışan tüm topolojileri listelemek için aşağıdaki komutu kullanın:

    storm list

Bu komutun aşağıdaki metne benzer bilgiler döndürmesi gerekir:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Devre dışı bırakma ve yeniden etkinleştirme

Bir topolojinin devre dışı bırakılması, sonlandıralınana veya yeniden etkinleştirilinceye kadar duraklatılır. Devre dışı bırakmak ve yeniden etkinleştirmek için aşağıdaki komutu kullanın:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Çalışan bir topolojiyi Sonlandır

Fırtınası topolojileri, başlatıldıktan sonra durduruluncaya kadar çalışmaya devam eder. Bir topolojiyi durdurmak için aşağıdaki komutu kullanın:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Yeniden Dengele

Bir topolojinin yeniden dengelenmesi sistemin, topolojinin paralelliğini değiştirmesine olanak tanır. Örneğin, daha fazla not eklemek için kümeyi yeniden boyutlandırdıysanız, yeniden dengeleme bir topolojinin yeni düğümleri görmesini sağlar.

> [!WARNING]  
> Bir topolojinin yeniden dengelenmesi öncelikle topolojiyi devre dışı bırakır ve sonra çalışanları küme genelinde eşit olarak yeniden dağıtır, son olarak, yeniden dengeleme gerçekleştirilmeden önce topolojiyi içinde olduğu duruma döndürür. Bu nedenle topoloji etkin ise, tekrar etkin hale gelir. Devre dışı bırakılmışsa, devre dışı kalır.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>İzleme ve yönetme: Fırtınası Kullanıcı arabirimi

Storm Kullanıcı Arabirimi çalışan topolojilerle çalışmaya yönelik bir web arabirimi sağlar ve HDInsight kümenize dahil edilir. Fırtınası Kullanıcı arabirimini görüntülemek için, açmak **https://CLUSTERNAME.azurehdinsight.net/stormui** üzere bir Web tarayıcısı kullanın, burada **clustername** kümenizin adıdır.

> [!NOTE]  
> Bir kullanıcı adı ve parola girmeniz istenirse kümeyi oluştururken kullandığınız küme yöneticisi (yönetici) ve parolayı girin.

### <a name="main-page"></a>Ana sayfa

Fırtınası Kullanıcı arabiriminin ana sayfası aşağıdaki bilgileri sağlar:

* **Küme Özeti**: Fırtınası kümesiyle ilgili temel bilgiler.
* **Topoloji Özeti**: Çalışan topolojilerden oluşan bir liste. Belirli topolojilerle ilgili daha fazla bilgi görüntülemek için bu bölümdeki bağlantıları kullanın.
* **Gözetmen Özeti**: Fırtınası hakkında bilgi.
* **Nimbus yapılandırması**: Küme için Nimbus yapılandırması.

### <a name="topology-summary"></a>Topoloji Özeti

**Topoloji Özeti** bölümünden bir bağlantı seçmek topolojiyle ilgili aşağıdaki bilgileri görüntüler:

* **Topoloji Özeti**: Topolojiyle ilgili temel bilgiler.
* **Topoloji eylemleri**: Topoloji için gerçekleştirebileceğiniz yönetim eylemleri.

  * **Etkinleştir**: Devre dışı bırakılan bir topoloji işlemeyi sürdürür.
  * **Devre dışı bırak**: Çalışan bir topolojiyi duraklatır.
  * Yeniden **Dengeleme**: Topolojinin paralelliğini ayarlar. Kümedeki düğüm sayısını değiştirdikten sonra çalışan topolojileri yeniden dengelemeniz gerekir. Bu işlem, topolojide küme içindeki artan veya azaltılmış düğüm sayısını dengelemek için paralellik ayarlanmasını sağlar.

    Daha fazla bilgi için bkz. <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm topolojisinin paralelliğini anlama</a>.
  * **Sonlandır**: Belirtilen zaman aşımından sonra bir fırtınası topolojisini sonlandırır.
* **Topoloji istatistikleri**: Topoloji hakkında istatistikler. Sayfadaki kalan girişlerin zaman çerçevesini ayarlamak için **pencere** sütunundaki bağlantıları kullanın.
* **Spout**: Topoloji tarafından kullanılan spomalar. Belirli spomalar hakkında daha fazla bilgi görüntülemek için bu bölümdeki bağlantıları kullanın.
* **Cıvatalar**: Topoloji tarafından kullanılan cıvatları. Belirli cıvatları hakkında daha fazla bilgi görüntülemek için bu bölümdeki bağlantıları kullanın.
* **Topoloji yapılandırması**: Seçilen topoloji yapılandırması.

### <a name="spout-and-bolt-summary"></a>Spout ve cıvata Özeti

**Spout** veya **cıvatalar** bölümlerinden bir biriktirmede seçim yapmak seçili öğeyle ilgili aşağıdaki bilgileri görüntüler:

* **Bileşen özeti**: Spout veya cıvam hakkındaki temel bilgiler.
* **Spout/cıvata istatistikleri**: Spout veya cıvata ilişkin istatistikler. Sayfadaki kalan girişlerin zaman çerçevesini ayarlamak için **pencere** sütunundaki bağlantıları kullanın.
* **Giriş İstatistikleri** (yalnızca cıvata): Sürgüsü tarafından tüketilen giriş akışları hakkında bilgi.
* **Çıkış istatistikleri**: Spout veya cıvata yayılan akışlar hakkında bilgiler.
* **Yürüticileri**: Spout veya cıvatanın örnekleri hakkında bilgi. Bu örnek için üretilen tanılama bilgilerinin günlüğünü görüntülemek üzere belirli bir yürütücü için **bağlantı noktası** girişini seçin.
* **Hatalar**: Spout veya cıvata ilgili tüm hata bilgileri.

## <a name="monitor-and-manage-rest-api"></a>İzleme ve yönetme: REST API

Fırtınası Kullanıcı arabirimi REST API üzerine kurulmuştur, böylece REST API kullanarak benzer yönetim ve izleme işlevlerini gerçekleştirebilirsiniz. REST API, fırtınası topolojilerini yönetmek ve izlemek için özel araçlar oluşturmak üzere kullanabilirsiniz.

Daha fazla bilgi için bkz. [Apache Storm uı REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Aşağıdaki bilgiler, HDInsight üzerinde Apache Storm REST API kullanımı için özeldir.

> [!IMPORTANT]  
> Bu REST API, internet üzerinden genel kullanıma açık değildir ve HDInsight kümesi baş düğümüne SSH tüneli kullanılarak erişilmelidir. SSH tüneli oluşturma ve kullanma hakkında bilgi için bkz. [Apache ambarı Web UI, ResourceManager, JobHistory, süs Code, Apache Oozie ve diğer Web UG erişimi IÇIN SSH tüneli kullanma](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Taban URI 'SI

Linux tabanlı HDInsight kümelerinde REST API için temel URI, **https\/:/headnodefqdn: 8744/API/v1/** konumundaki baş düğümde bulunur. Baş düğümün etki alanı adı, küme oluşturma sırasında oluşturulur ve statik değildir.

Küme baş düğümü için tam etki alanı adını (FQDN) birkaç farklı yolla bulabilirsiniz:

* **BIR SSH oturumundan**: Kümeden bir SSH `headnode -f` oturumunda komutunu kullanın.
* **Ambarı Web 'den**: Sayfanın üst kısmından **Hizmetler** ' i seçin ve ardından **fırtınası**' yi seçin. **Özet** sekmesinden, **fırtınası Kullanıcı arabirimi sunucusu**' nu seçin. Fırtınası Kullanıcı arabirimini barındıran düğümün FQDN 'SI ve REST API sayfanın en üstünde görüntülenir.
* **Ambarı REST API**: Fırtınası Kullanıcı arabiriminin `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` ve REST API üzerinde çalıştığı düğüm hakkında bilgi almak için komutunu kullanın. **Clustername** değerini küme adıyla değiştirin. İstendiğinde, oturum açma (yönetici) hesabının parolasını girin. Yanıtta, "host_name" girdisi düğümün FQDN 'sini içerir.

### <a name="authentication"></a>Authentication

REST API istekleri **temel kimlik doğrulaması**kullanmalıdır, bu nedenle HDInsight kümesi yönetici adını ve parolasını kullanın.

> [!NOTE]  
> Temel kimlik doğrulaması şifresiz metin kullanılarak gönderildiğinden, kümeyle iletişimleri güvenli hale getirmek için **her zaman** HTTPS kullanmanız gerekir.

### <a name="return-values"></a>Döndürülen değerler

REST API döndürülen bilgiler yalnızca küme içinden kullanılabilir olabilir. Örneğin, [Apache ZooKeeper](https://zookeeper.apache.org/) sunucuları için döndürülen tam etki alanı ADıNA (FQDN) Internet 'ten erişilemez.

## <a name="next-steps"></a>Sonraki Adımlar

[Apache Maven kullanarak Java tabanlı topolojiler geliştirmeyi](apache-storm-develop-java-topology.md)öğrenin.

Daha fazla örnek topolojilerinin listesi için bkz. [HDInsight 'ta Apache Storm Için örnek topolojiler](apache-storm-example-topology.md).
