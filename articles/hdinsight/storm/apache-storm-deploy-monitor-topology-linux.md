---
title: Azure HDInsight 'ta Apache Storm topolojileri dağıtma ve yönetme
description: Linux tabanlı HDInsight 'ta fırtınası panosunu kullanarak Apache Storm topolojilerini dağıtmayı, izlemeyi ve yönetmeyi öğrenin. Visual Studio için Hadoop araçları 'nı kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: fb57992c8e26560061faf68443c4993801f9713d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871580"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache Storm topolojileri dağıtma ve yönetme

Bu belgede, HDInsight kümelerinde fırtınası üzerinde çalışan [Apache Storm](https://storm.apache.org/) topolojilerinin yönetilmesi ve izlenmesi hakkında temel bilgileri öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Storm kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü** için **fırtınası** seçme.

* Seçim Secure Shell (SSH) ve güvenli kopya (SCP) ile benzerlik. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Seçim Visual Studio, Azure SDK 2.5.1 veya daha yeni bir sürümü ve Visual Studio için Data Lake araçları. Daha fazla bilgi için bkz. [Visual Studio Data Lake araçları & Apache Hadoop](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Visual Studio kullanarak bir topoloji gönderme

Visual Studio için Data Lake araçlarını kullanarak, fırtınası kümenize C# veya hibrit topolojilerini gönderebilirsiniz. Aşağıdaki adımlarda örnek bir uygulama kullanılır. Data Lake araçlarını kullanarak topoloji oluşturma hakkında daha fazla bilgi için bkz. [Visual Studio ve C# ile Apache Storm topolojileri](apache-storm-develop-csharp-visual-studio-topology.md).

1. Visual Studio için Data Lake araçları 'nın en son sürümünü henüz yüklemediyseniz bkz. [Visual Studio için Data Lake araçları kullanma](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Azure Data Lake ve Stream Analytics araçları, daha önce Visual Studio için HDInsight araçları olarak adlandırılmıştı.
    >
    > Visual Studio için Azure Data Lake ve Stream Analytics araçları, Visual Studio 2019 için **Azure geliştirme** iş yüküne dahildir.

1. Visual Studio’yu çalıştırın.

1. **Başlangıç** penceresinde **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur** penceresinde arama kutusunu seçin ve girin `Storm` . Sonra sonuç listesinden **fırtınası örneği** ' ni seçin ve **İleri**' yi seçin.

1. Yeni projeyi **yapılandırın** penceresinde, bir **Proje adı** girin ve ' a gidin veya yeni projenin kaydedileceği **konumu** oluşturun. Ardından **Oluştur**’u seçin.

    :::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png" alt-text="Yeni proje pencerenizi, Visual Studio 'yu yapılandırın" border="true":::

1. **Sunucu Gezgini**, **Azure** ' a sağ tıklayın ve **Microsoft Azure aboneliğine Bağlan...** öğesini seçin ve oturum açma işlemini doldurun.

1. **Çözüm Gezgini**, projeye sağ tıklayın ve **HDInsight 'Ta fırtınası 'ya gönder**' i seçin.

    > [!NOTE]  
    > İstenirse, Azure aboneliğinizin oturum açma kimlik bilgilerini girin. Birden fazla aboneliğiniz varsa, HDInsight kümesi için bir fırtınası içeren bir oturum açın.

1. **Topolojiyi gönder** iletişim kutusunda, **fırtınası kümesi** aşağı açılan listesinde, HDInsight kümesinde fırtınası ' ı seçin ve ardından **Gönder**' i seçin. **Çıkış** bölmesini görüntüleyerek gönderimin başarılı olup olmadığını izleyebilirsiniz.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>SSH ve fırtınası komutunu kullanarak bir topoloji gönderme

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH oturumınızdan, **WORDCOUNT** örnek topolojisini başlatmak için aşağıdaki komutu kullanın:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Bu komut, kümede örnek WordCount topolojisini başlatır. Bu topoloji rastgele cümleler oluşturur ve cümlelerde her sözcüğün geçtiği yeri sayar.

    > [!NOTE]  
    > Kümeye topoloji gönderirken, önce komutunu kullanmadan önce kümeyi içeren. jar dosyasını kopyalamanız gerekir `storm` . Dosyayı kümeye kopyalamak için `scp` komutunu kullanabilirsiniz. Örneğin, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar` girin.
    >
    > *WORDCOUNT* örneği ve diğer fırtınası başlangıç örnekleri, ' de kümenize zaten dahil edilmiştir `/usr/hdp/current/storm-client/contrib/storm-starter/` .

## <a name="submit-a-topology-programmatically"></a>Bir topolojiyi programlı olarak gönderme

Nimbus hizmetini kullanarak bir topolojiyi programlı bir şekilde dağıtabilirsiniz. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) Nimbus hizmeti aracılığıyla bir topolojinin nasıl dağıtılacağını ve başlatılacağını gösteren örnek bir Java uygulaması sağlar.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Visual Studio 'da topolojiyi izleme ve yönetme

Visual Studio kullanarak bir topoloji gönderdiğinizde, **fırtınası topolojileri görünümü** penceresi görüntülenir. Çalışan topoloji hakkındaki bilgileri görüntülemek için listeden topolojiyi seçin.

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png" alt-text="İzleyici topolojisi, fırtınası topolojileri Görünüm penceresi, Visual Studio" border="true":::

> [!NOTE]  
> Ayrıca, **Sunucu Gezgini** karşı **fırtınası topolojilerini** görüntüleyebilirsiniz. **Azure**  >  **HDInsight**' ı genişletin, HDInsight kümesinde bir fırtınası ' a sağ tıklayın ve ardından **fırtınası topolojilerini görüntüle**' yi seçin.

Bu bileşenler hakkındaki bilgileri görüntülemek için spomalar veya cıvatları şeklini seçin. Seçilen öğe için bileşen bilgileri içeren bir araç ipucu görüntülenir.

### <a name="deactivate-and-reactivate-a-topology"></a>Topolojiyi devre dışı bırakma ve yeniden etkinleştirme

Topolojiyi devre dışı bırakmak topoloji sonlandırana veya yeniden etkinleştirilinceye kadar duraklatılır. Bu işlemleri yapmak için, **fırtınası topolojileri görünümü** penceresinin en üstündeki **Eylemler** alanında **devre dışı bırak** ve **yeniden etkinleştir** düğmelerini kullanın.

### <a name="rebalance-a-topology"></a>Topolojiyi yeniden dengeleme

Bir topolojinin yeniden dengelenmesi sistemin, topolojinin paralelliğini değiştirmesine olanak tanır. Örneğin, daha fazla not eklemek için kümeyi yeniden boyutlandırdıysanız, yeniden dengeleme bir topolojinin yeni düğümleri görmesini sağlar.

Bir topolojiyi yeniden dengelemek için, **fırtınası topolojileri görünümü** penceresinin **Eylemler** alanındaki yeniden **Dengeleme** düğmesini kullanın.

> [!WARNING]  
> Bir topolojinin yeniden dengelenmesi, topolojiyi devre dışı bırakır, çalışanları küme genelinde eşit olarak yeniden dağıtır ve sonra yeniden dengeleme gerçekleştirilmeden önce içinde olduğu duruma geri döner. Topoloji etkin ise, tekrar etkin hale gelir. Topoloji devre dışı bırakıldıysa devre dışı kalır.

### <a name="kill-a-running-topology"></a>Çalışan bir topolojiyi Sonlandır

Fırtınası topolojileri durduruluncaya veya küme silinene kadar çalışmaya devam eder. Bir topolojiyi durdurmak için, **Eylemler** alanındaki **Sonlandır** düğmesini kullanın.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>SSH ve fırtınası komutunu kullanarak topolojiyi izleme ve yönetme

`storm`Yardımcı programı, çalışan topolojilerle komut satırından çalışmanıza olanak sağlar. `storm -h`Komutların tam listesi için kullanın.

### <a name="list-topologies"></a>Topolojileri Listele

Çalışan tüm topolojileri listelemek için aşağıdaki komutu kullanın:

```shell
storm list
```

Bu komutun aşağıdaki metne benzer bilgiler döndürmesi gerekir:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Topolojiyi devre dışı bırakma ve yeniden etkinleştirme

Topolojiyi devre dışı bırakmak topoloji sonlandırana veya yeniden etkinleştirilinceye kadar duraklatılır. Devre dışı bırakmak veya yeniden etkinleştirmek için aşağıdaki komutları kullanın:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Çalışan bir topolojiyi Sonlandır

Fırtınası topolojileri, başlatıldıktan sonra durduruluncaya kadar çalışmaya devam eder. Bir topolojiyi durdurmak için aşağıdaki komutu kullanın:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Topolojiyi yeniden dengeleme

Bir topolojinin yeniden dengelenmesi sistemin, topolojinin paralelliğini değiştirmesine olanak tanır. Örneğin, daha fazla not eklemek için kümeyi yeniden boyutlandırdıysanız, yeniden dengeleme bir topolojinin yeni düğümleri görmesini sağlar.

> [!WARNING]  
> Bir topolojinin yeniden dengelenmesi, topolojiyi devre dışı bırakır, çalışanları küme genelinde eşit olarak yeniden dağıtır ve sonra yeniden dengeleme gerçekleştirilmeden önce içinde olduğu duruma geri döner. Topoloji etkin ise, tekrar etkin hale gelir. Devre dışı bırakılmışsa, devre dışı kalır.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Bir topolojiyi, fırtınası Kullanıcı arabirimini kullanarak izleme ve yönetme

Fırtınası Kullanıcı arabirimi, çalışan topolojilerle çalışmaya yönelik bir Web arabirimi sağlar ve HDInsight kümenize eklenmiştir. Fırtınası Kullanıcı arabirimini görüntülemek için, açmak üzere bir Web tarayıcısı kullanın `https://CLUSTERNAME.azurehdinsight.net/stormui` , burada *clustername* kümenizin adıdır.

> [!NOTE]  
> Bir Kullanıcı adı ve parola sağlamanız isteniyorsa, kümeyi oluştururken kullandığınız küme yönetici kullanıcı adını ve parolasını girin.

### <a name="storm-ui-main-page"></a>Fırtınası Kullanıcı arabirimi ana sayfası

Fırtınası Kullanıcı arabiriminin ana sayfası aşağıdaki bilgileri sağlar:

| Section | Description |
| --- | --- |
| Küme özeti| Fırtınası kümesiyle ilgili temel bilgiler. |
| Nimbus Özeti | Temel Nimbus bilgilerinin listesi. |
| Topoloji Özeti | Çalışan topolojilerden oluşan bir liste. Belirli bir topoloji hakkında daha fazla bilgi görüntülemek için **ad** sütununda bağlantısını seçin. |
| Gözetmen Özeti | Fırtınası hakkında bilgi. Belirli bir gözetmen ile ilişkili çalışan kaynaklarını görmek için **konak** veya **kimlik** sütunundaki bağlantısını seçin. |
| Nimbus yapılandırması | Küme için Nimbus yapılandırması. |

Fırtınası Kullanıcı arabirimi ana sayfası bu Web sayfasına benzer şekilde görünür:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png" alt-text="Ana sayfa, fırtınası Kullanıcı arabirimi, Apache Storm topolojileri, Azure" border="true":::

#### <a name="topology-summary"></a>Topoloji Özeti

**Topoloji Özeti** bölümünden bir bağlantı seçmek topolojiyle ilgili aşağıdaki bilgileri görüntüler:

| Section | Description |
| --- | --- |
| Topoloji Özeti | Topolojiyle ilgili temel bilgiler. |
| Topoloji eylemleri| Topoloji için yapabileceğiniz yönetim eylemleri. Kullanılabilir eylemler, bu bölümün ilerleyen kısımlarında açıklanmıştır. |
| Topoloji istatistikleri | Topoloji hakkında istatistikler. Bu bölümdeki bir girdinin zaman çerçevesini ayarlamak için **pencere** sütununda bağlantısını seçin. |
| Spout *(zaman dilimi)* | Topoloji tarafından kullanılan spomalar. Belirli bir Spout hakkında daha fazla bilgi görüntülemek için **kimlik** sütunundaki bağlantısını seçin. |
| Cıvatalar *(zaman dilimi)* | Topoloji tarafından kullanılan cıvatları. Belirli bir cıvata ilişkin daha fazla bilgi görüntülemek için **kimlik** sütunundaki bağlantısını seçin. |
| Çalışan kaynakları | Çalışan kaynaklarının bir listesi. Belirli bir çalışan kaynağı hakkında daha fazla bilgi görüntülemek için **konak** sütunundaki bağlantısını seçin. |
| Topoloji görselleştirme | Topoloji görselleştirmesini görüntüleyen bir **görselleştirmeyi göster** düğmesi. |
| Topoloji yapılandırması | Seçilen topoloji yapılandırması. |

Fırtınası topolojisi Özet sayfası, bu Web sayfasına benzer şekilde görünür:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png" alt-text="Topoloji Özet sayfası, fırtınası Kullanıcı arabirimi, Apache Storm, Azure" border="true":::

**Topoloji eylemleri** bölümünde, bir eylem yapmak için aşağıdaki düğmeleri seçebilirsiniz:

| Düğme | Description |
| --- | --- |
| Etkinleştir | Devre dışı bırakılan bir topoloji işlemeyi sürdürür. |
| Devre dışı bırak | Çalışan bir topolojiyi duraklatır. |
| Dengeleme | Topolojinin paralelliğini ayarlar. Kümedeki düğüm sayısını değiştirdikten sonra çalışan topolojileri yeniden dengelemeniz gerekir. Bu işlem, topolojinin kümedeki ek veya azaltılan düğüm sayısını dengelemek üzere paralellik ayarlamasını sağlar.<br/><br/>Daha fazla bilgi için bkz. <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm topolojisinin paralelliğini anlama</a>.
| Kapatmayın | Belirtilen zaman aşımından sonra bir fırtınası topolojisini sonlandırır. |
| Hata Ayıklama | Çalışan topoloji için bir hata ayıklama oturumu başlatır. |
| Hata ayıklamayı Durdur | Çalışan topoloji için hata ayıklama oturumunu sonlandırır. |
| Günlük düzeyini Değiştir | Hata ayıklama günlüğü düzeyini değiştirir. |

##### <a name="spout-and-bolt-summary"></a>Spout ve cıvata Özeti

**Spout** veya **cıvatalar** bölümlerinden bir biriktirmede seçim yapmak seçili öğeyle ilgili aşağıdaki bilgileri görüntüler:

| Section | Description |
| --- | --- |
| Bileşen Özeti | Spout veya cıvam hakkındaki temel bilgiler. |
| Bileşen eylemleri | **Hata Ayıkla ve** **Hata Ayıkla** düğmeleri. |
| Spout istatistikleri veya cıvatlar istatistikleri | Spout veya cıvata ilişkin istatistikler. Bu bölümdeki bir girdinin zaman çerçevesini ayarlamak için **pencere** sütununda bağlantısını seçin. |
| (Yalnızca cıvata)<br/>Giriş İstatistikleri *(zaman dilimi)* | Sürgüsü tarafından tüketilen giriş akışları hakkında bilgi. |
| Çıkış istatistikleri *(zaman dilimi)* | Spout veya cıvata yayılan akışlar hakkında bilgiler. |
| Profil oluşturma ve hata ayıklama | Bu sayfadaki bileşenlerin profilini oluşturma ve hata ayıklama için denetimler. **Durum/zaman aşımı (dakika)** değerini ayarlayabilir ve **jstack** için düğmeler, **çalışan** ve **yığın**' ı seçebilirsiniz. |
| Yürüticileri *(zaman dilimi)* | Spout veya cıvatanın örnekleri hakkında bilgi. Bu örnek için üretilen tanılama bilgilerinin günlüğünü görüntülemek için, belirli bir yürütücü için **bağlantı noktası** girişini seçin. Ayrıca, **konak** sütunundaki bağlantısını seçerek belirli bir yürütücü ile ilişkili çalışan kaynaklarını da görebilirsiniz. |
| Hatalar | Spout veya cıvata ilgili tüm hata bilgileri. |

Fırtınası Özeti sayfası, bu Web sayfasına benzer şekilde görünür:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png" alt-text="Cıvata Özet sayfası, fırtınası Kullanıcı arabirimi, Apache Storm, Azure" border="true":::

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>REST API kullanarak topolojiyi izleyin ve yönetin

Fırtınası Kullanıcı arabirimi REST API üzerine kurulmuştur, bu sayede REST API kullanarak benzer yönetim ve izleme görevleri gerçekleştirebilirsiniz. REST API, fırtınası topolojilerini yönetmek ve izlemek için özel araçlar oluşturmak üzere kullanabilirsiniz.

Daha fazla bilgi için bkz. [Apache Storm uı REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Aşağıdaki bilgiler, HDInsight üzerinde Apache Storm REST API kullanımı için özeldir.

> [!IMPORTANT]  
> REST API fırtınası internet üzerinden genel kullanıma açık değildir. HDInsight kümesi baş düğümüne SSH tüneli kullanılarak erişilmelidir. SSH tüneli oluşturma ve kullanma hakkında bilgi için bkz. [Azure HDInsight 'a erişmek IÇIN SSH tüneli kullanma](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Taban URI 'SI

Linux tabanlı HDInsight kümelerinde REST API için temel URI, `https://HEADNODEFQDN:8744/api/v1/` *Headnodefqdn* 'yi baş düğümle değiştirdiğiniz URL adresinde bulabilirsiniz. Baş düğümün etki alanı adı, küme oluşturma sırasında oluşturulur ve statik değildir.

Küme baş düğümü için tam etki alanı adını (FQDN) çeşitli yollarla bulabilirsiniz:

| FQDN bulma yöntemi | Description |
| --- | --- |
| SSH oturumu | `headnode -f`Kümeden BIR SSH oturumunda komutunu kullanın. |
| Ambarı web | Ambarı küme Web sayfasında ( `https://CLUSTERNAME.azurehdinsight.net` ), sayfanın üst kısmından **Hizmetler** ' i seçin ve ardından **fırtınası**' yi seçin. **Özet** sekmesinden, **fırtınası Kullanıcı arabirimi sunucusu**' nu seçin. Fırtınası Kullanıcı arabirimini barındıran düğümün FQDN 'SI ve REST API sayfanın en üstünde görüntülenir. |
| Ambarı REST API | `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"`Fırtınası Kullanıcı arabiriminin ve REST API üzerinde çalıştığı düğüm hakkında bilgi almak için komutunu kullanın. *Clustername* öğesinin iki örneğini küme adıyla değiştirin. İstendiğinde, Kullanıcı (yönetici) hesabının parolasını girin. Yanıtta, JSON çıktısının "host_name" girdisi düğümün FQDN 'sini içerir. |

### <a name="authentication"></a>Kimlik Doğrulaması

REST API istekleri *temel kimlik doğrulaması* kullanmalıdır, bu nedenle HDInsight kümesi için yönetici adını ve parolasını kullanmanız gerekir.

> [!NOTE]  
> Temel kimlik doğrulaması şifresiz metin kullanılarak gönderildiğinden, kümeyle iletişimleri güvenli hale getirmek için *her zaman* HTTPS kullanmanız gerekir.

### <a name="return-values"></a>Dönüş değerleri

REST API döndürülen bilgiler yalnızca küme içinden kullanılabilir olabilir. Örneğin, [Apache ZooKeeper](https://zookeeper.apache.org/) sunucuları için döndürülen tam etki alanı ADıNA (FQDN) internet 'ten erişilemez.

## <a name="next-steps"></a>Sonraki adımlar

[Apache Maven kullanarak Java tabanlı topolojiler geliştirmeyi](apache-storm-develop-java-topology.md)öğrenin.

Daha fazla örnek topolojilerden oluşan bir liste için bkz. [Azure HDInsight 'Ta örnek Apache Storm topolojileri](apache-storm-example-topology.md).
