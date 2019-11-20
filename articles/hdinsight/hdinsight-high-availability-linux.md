---
title: Hadoop için yüksek kullanılabilirlik-Azure HDInsight
description: HDInsight kümelerinin ek bir baş düğüm kullanarak güvenilirliği ve kullanılabilirliği nasıl artıracağınızı öğrenin. Bunun, her bir baş düğüme tek tek nasıl bağlanacağınızı, örneğin, hem ambarı hem de Hive gibi Hadoop hizmetlerini nasıl etkilediği hakkında bilgi edinin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Hadoop yüksek kullanılabilirlik
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 8b914b8ffe995cf31f8a22b6f80250431facc770
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682241"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>HDInsight 'ta Apache Hadoop kümelerinin kullanılabilirliği ve güvenilirliği

HDInsight kümeleri, çalıştıran Apache Hadoop Hizmetleri ve işlerin kullanılabilirliğini ve güvenilirliğini artırmak için iki baş düğüm sağlar.

Hadoop, bir kümedeki birden çok düğümdeki Hizmetleri ve verileri çoğaltarak yüksek kullanılabilirlik ve güvenilirlik elde eder. Ancak, Hadoop 'un standart dağıtımları genellikle yalnızca tek bir baş düğüme sahiptir. Tek baş düğümden herhangi bir kesinti, kümenin çalışmayı durdurmasına neden olabilir. HDInsight, Hadoop 'un kullanılabilirliğini ve güvenilirliğini artırmak için iki headnode sağlar.

## <a name="availability-and-reliability-of-nodes"></a>Düğümlerin kullanılabilirliği ve güvenilirliği

HDInsight kümesindeki düğümler Azure sanal makineleri kullanılarak uygulanır. Aşağıdaki bölümlerde HDInsight ile kullanılan tek düğüm türleri ele alınmaktadır.

> [!NOTE]  
> Küme türü için tüm düğüm türleri kullanılmaz. Örneğin, bir Hadoop kümesi türünün herhangi bir Nimbus düğümü yoktur. HDInsight küme türleri tarafından kullanılan düğümler hakkında daha fazla bilgi için bkz. [HDInsight 'Ta Linux tabanlı Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) ' nın küme türleri bölümü.

### <a name="head-nodes"></a>Baş düğümler

HDInsight, Hadoop Hizmetleri üzerinde yüksek kullanılabilirlik sağlamak için iki baş düğüm sağlar. Her iki baş düğüm de aynı anda HDInsight kümesi içinde etkin ve çalışır. Apache, veya Apache Hadoop YARN gibi bazı hizmetler, belirli bir zamanda yalnızca bir baş düğümde bulunan ' etkin' ' dir. HiveServer2 veya Hive meta veri deposu gibi diğer hizmetler aynı anda her iki baş düğümde de etkindir.

Baş düğümlerin (ve HDInsight 'taki diğer düğümlerin), düğümün ana bilgisayar adının bir parçası olarak sayısal bir değeri vardır. Örneğin, `hn0-CLUSTERNAME` veya `hn4-CLUSTERNAME`.

> [!IMPORTANT]  
> Sayısal değeri bir düğümün birincil veya ikincil olup olmadığı ile ilişkilendirmeyin. Sayısal değer yalnızca her düğüm için benzersiz bir ad sağlamak üzere mevcuttur.

### <a name="nimbus-nodes"></a>Nimbus Düğümleri

Nimbus düğümleri Apache Storm kümeleriyle kullanılabilir. Nimbus düğümleri, çalışan düğümleri arasında işlemeyi dağıtarak ve izleyerek Hadoop JobTracker 'e benzer işlevler sağlar. HDInsight, fırtınası kümeleri için iki Nimbus düğümü sağlar

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper düğümleri

[ZooKeeper](https://zookeeper.apache.org/) düğümleri, baş düğümlerdeki ana hizmetlerin öncü seçimi için kullanılır. Ayrıca, bu hizmetler, veri (çalışan) düğümlerinin ve ağ geçitlerinin, ana hizmetin hangi başlık düğümüne etkin olduğunu bilmesini sağlamak için de kullanılır. HDInsight, varsayılan olarak üç ZooKeeper düğümü sağlar.

### <a name="worker-nodes"></a>Çalışan düğümleri

Çalışan düğümleri, kümeye bir iş gönderildiğinde gerçek veri analizini gerçekleştirir. Bir çalışan düğümü başarısız olursa, gerçekleştirdiği görev başka bir çalışan düğümüne gönderilir. HDInsight, varsayılan olarak dört çalışan düğümü oluşturur. Bu numarayı, küme oluşturma sırasında ve sonrasında gereksinimlerinize uyacak şekilde değiştirebilirsiniz.

### <a name="edge-node"></a>Kenar düğümü

Bir Edge düğümü, küme içindeki veri analizine etkin bir şekilde katılmaz. Bu, Hadoop ile çalışırken geliştiriciler veya veri bilimcileri tarafından kullanılır. Edge düğümü, kümedeki diğer düğümlerle aynı Azure sanal ağında yer alabilir ve diğer tüm düğümlere doğrudan erişebilir. Edge düğümü, önemli Hadoop hizmetlerinden veya analiz işlerinin dışında, kaynakları almadan kullanılabilir.

Şimdilik, HDInsight üzerindeki ML Hizmetleri varsayılan olarak bir kenar düğümü sağlayan tek küme türüdür. HDInsight üzerinde ML Hizmetleri için, Edge düğümü, dağıtılmış işleme için kümeye göndermeden önce düğüm üzerinde yerel olarak test R kodu kullanır.

Diğer küme türleriyle bir Edge düğümü kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Edge düğümlerini kullanma](hdinsight-apps-use-edge-node.md) belgesi.

## <a name="accessing-the-nodes"></a>Düğümlere erişme

Internet üzerinden kümeye erişim, genel bir ağ geçidi üzerinden sağlanır. Erişim, baş düğümlere ve varsa kenar düğümüne bağlanmak için sınırlıdır. Baş düğümlerde çalışan hizmetlere erişim, birden fazla baş düğüm olmadan etkilenmez. Ortak ağ geçidi, istekleri istenen hizmeti barındıran baş düğüme yönlendirir. Örneğin, Apache ambarı Şu anda ikincil baş düğümünde barındırılıyorsa, ağ geçidi, ambarı için gelen istekleri bu düğüme yönlendirir.

Ortak ağ geçidi üzerinden erişim 443 (HTTPS), 22 ve 23 bağlantı noktalarıyla sınırlıdır.

|Bağlantı noktası |Açıklama |
|---|---|
|443|Baş düğümlerde barındırılan ambarı ve diğer Web Kullanıcı arabirimine veya REST API 'Lerine erişmek için kullanılır.|
|22|SSH ile birincil baş düğüme veya kenar düğümüne erişmek için kullanılır.|
|23|SSH ile ikincil baş düğüme erişmek için kullanılır. Örneğin `ssh username@mycluster-ssh.azurehdinsight.net`, **MyCluster**adlı kümenin birincil baş düğümüne bağlanır.|

SSH kullanma hakkında daha fazla bilgi için bkz. [HDInsight Ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md) belgesi.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>İç tam etki alanı adları (FQDN)

HDInsight kümesindeki düğümlerin iç IP adresi ve yalnızca kümeden erişilebilen bir FQDN vardır. İç FQDN veya IP adresini kullanarak kümedeki hizmetlere erişirken, hizmete erişirken kullanılacak IP veya FQDN 'yi doğrulamak için ambarı 'nı kullanmanız gerekir.

Örneğin, Apache Oozie hizmeti yalnızca bir baş düğümde çalışabilir ve bir SSH oturumundan `oozie` komutunun kullanılması, hizmetin URL 'sini gerektirir. Bu URL, aşağıdaki komutu kullanarak ambarı 'ndan alınabilir:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Bu komut, `oozie` komutuyla kullanılacak iç URL 'YI içeren aşağıdakine benzer bir değer döndürür:

```output
"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"
```

REST API ambarı ile çalışma hakkında daha fazla bilgi için bkz. [HDInsight 'ı Apache ambarı REST API kullanarak izleme ve yönetme](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Diğer düğüm türlerine erişme

Aşağıdaki yöntemleri kullanarak internet üzerinden doğrudan erişilemeyen düğümlere bağlanabilirsiniz:

|Yöntem |Açıklama |
|---|---|
|SSH|SSH kullanarak bir baş düğüme bağlandıktan sonra, kümedeki diğer düğümlere bağlanmak için baş düğümden SSH kullanabilirsiniz. Daha fazla bilgi için [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md) belgesine bakın.|
|SSH tüneli|İnternet 'e açık olmayan düğümlerden birinde barındırılan bir Web hizmetine erişmeniz gerekiyorsa, bir SSH tüneli kullanmanız gerekir. Daha fazla bilgi için [HDInsight Ile SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md) belgesine bakın.|
|Azure Sanal Ağ|HDInsight kümeniz bir Azure sanal ağının parçasıysa, aynı sanal ağ üzerindeki herhangi bir kaynak kümedeki tüm düğümlere doğrudan erişebilir. Daha fazla bilgi için bkz. [HDInsight için sanal ağ planlaması](hdinsight-plan-virtual-network-deployment.md) belgesi.|

## <a name="how-to-check-on-a-service-status"></a>Hizmet durumunu denetleme

Baş düğümlerde çalışan hizmetlerin durumunu denetlemek için, ambarı Web Kullanıcı arabirimi veya ambarı REST API kullanın.

### <a name="ambari-web-ui"></a>Ambarı Web Kullanıcı arabirimi

Ambarı Web Kullanıcı arabirimi `https://CLUSTERNAME.azurehdinsight.net`görüntülenebilir. **CLUSTERNAME** değerini kümenizin adıyla değiştirin. İstenirse, kümenizin HTTP Kullanıcı kimlik bilgilerini girin. Varsayılan HTTP Kullanıcı adı **yönetici** ve parola, kümeyi oluştururken girdiğiniz paroladır.

Ambarı sayfasına geldiğinizde, yüklü hizmetler sayfanın solunda listelenir.

![Apache ambarı yüklü hizmetler](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Durumu göstermek için bir hizmetin yanında görünebilen bir dizi simge vardır. Bir hizmetle ilgili tüm uyarılar, sayfanın üst kısmındaki **Uyarılar** bağlantısı kullanılarak görüntülenebilir.  Ambarı önceden tanımlanmış birkaç uyarı sunar.

Aşağıdaki uyarılar bir kümenin kullanılabilirliğini izlemeye yardımcı olur:

| Uyarı adı                               | Açıklama                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ölçüm Izleyici durumu                    | Bu uyarı, izleme durumu betiği tarafından belirlendiği şekilde ölçüm Izleyici işleminin durumunu gösterir.                                                                                   |
| Ambarı aracı sinyali                   | Bu uyarı, sunucuda bir aracıyla ilgili iletişim kesildiğinde tetiklenir.                                                                                                                        |
| ZooKeeper sunucusu Işlemi                 | Bu ana bilgisayar düzeyi uyarı, ZooKeeper sunucu işleminin ağa açık ve dinleme için belirlenemediği durumlarda tetiklenir.                                                               |
| IOCache meta veri sunucusu durumu           | IOCache meta veri sunucusunun istemci isteklerine yanıt verip vermediğini belirlenemediği durumlarda bu konak düzeyi uyarısı tetiklenir                                                            |
| JournalNode Web Kullanıcı arabirimi                       | Bu konak düzeyi uyarısı, JournalNode Web Kullanıcı arabirimine ulaşılamıyorsa tetiklenir.                                                                                                                 |
| Spark2 Thrift sunucusu                     | Bu konak düzeyi uyarısı, Spark2 Thrift sunucusu etkin olarak belirlenemiyorsa tetiklenir.                                                                                                |
| Geçmiş sunucu Işlemi                   | Bu konak düzeyi uyarısı, geçmiş sunucusu işleminin ağ üzerinde dinlenecek ve dinlemek için kurulamazsa tetiklenir.                                                                |
| Geçmiş sunucusu Web Kullanıcı arabirimi                    | Bu konak düzeyi uyarı, geçmiş sunucusu Web Kullanıcı arabirimine ulaşılamıyorsa tetiklenir.                                                                                                              |
| `ResourceManager` Web Kullanıcı arabirimi                   | `ResourceManager` Web Kullanıcı arabirimine ulaşılamıyorsa bu konak düzeyi uyarı tetiklenir.                                                                                                             |
| NodeManager sistem durumu Özeti               | Sağlıksız NodeManager varsa, bu hizmet düzeyi uyarı tetiklenir                                                                                                                    |
| Uygulama zaman çizelgesi Web Kullanıcı arabirimi                      | Bu konak düzeyi uyarı, uygulama zaman çizelgesi sunucusu Web Kullanıcı arabirimine ulaşılamıyorsa tetiklenir.                                                                                                         |
| Dadtanode sistem durumu Özeti                  | Sağlıksız bir kades varsa, bu hizmet düzeyi uyarı tetiklenir                                                                                                                       |
| Süs Code Web Kullanıcı arabirimi                          | Bu konak düzeyi uyarısı, süs Code Web Kullanıcı arabirimine ulaşılamıyorsa tetiklenir.                                                                                                                    |
| ZooKeeper yük devretme denetleyicisi Işlemi    | Bu ana bilgisayar düzeyi uyarı, ZooKeeper yük devretme denetleyicisi işleminin ağ üzerinde çalışıyor ve dinlemek için onaylanmazsa tetiklenir.                                                   |
| Oozie sunucusu Web Kullanıcı arabirimi                      | Bu konak düzeyi uyarı, Oozie sunucu Web Kullanıcı arabirimine ulaşılamıyorsa tetiklenir.                                                                                                                |
| Oozie sunucu durumu                      | Bu konak düzeyi uyarısı, Oozie sunucusunun istemci isteklerine yanıt verip vermeyeceğini tespit edilebilir ise tetiklenir.                                                                      |
| Hive meta veri deposu Işlemi                   | Bu konak düzeyi uyarısı, Hive meta veri deposu işlemi, ağı dinleyerek ve dinlemeden saptanamıyor.                                                                 |
| HiveServer2 Işlemi                      | Bu konak düzeyi uyarısı, HiveServer 'ın istemci isteklerine yanıt verip vermediğini tespit edilebilir ise tetiklenir.                                                                        |
| WebHCat sunucu durumu                    | `templeton` sunucusu durumu sağlıklı değilse bu konak düzeyi uyarısı tetiklenir.                                                                                                            |
| Kullanılabilir ZooKeeper sunucusu yüzdesi      | Kümedeki aşağı ZooKeeper sunucularının sayısı yapılandırılan kritik eşikten büyükse bu uyarı tetiklenir. ZooKeeper işlem denetimlerinin sonuçlarını toplar.     |
| Spark2 Livy sunucusu                       | Bu konak düzeyi uyarı, Livy2 sunucusu çalışır olarak belirlenemiyorsa tetiklenir.                                                                                                        |
| Spark2 geçmiş sunucusu                    | Bu ana bilgisayar düzeyi uyarı, Spark2 geçmiş sunucusunun olarak belirlenemediği takdirde tetiklenir.                                                                                               |
| Ölçüm toplayıcı Işlemi                | Bu uyarı, ölçüm toplayıcısının yapılandırılmış bağlantı noktasında eşiğe eşit olması onaylanmazsa tetiklenemez ve dinlenemez.                                 |
| Ölçüm toplayıcısı-HBase Master Işlemi | Bu uyarı, ölçüm toplayıcısının HBase ana işlemlerinin, saniye olarak verilen yapılandırılmış kritik eşik için ağ üzerinde çalışmaya ve dinlemek onaylanmazsa tetiklenir. |
| Kullanılabilir ölçüm Izleyicileri yüzdesi       | Bu uyarı, ölçüm Izleme işlemlerinin bir yüzdesi, yapılandırılan uyarı ve kritik eşikler için ağda dinlemediğinde tetiklenir.                             |
| Kullanılabilir düğüm yöneticileri yüzdesi           | Bu uyarı, kümedeki aşağı aşağı NodeManager sayısı yapılandırılan kritik eşikten fazlaysa tetiklenir. NodeManager işlem denetimlerinin sonuçlarını toplar.        |
| NodeManager sistem durumu                       | Bu konak düzeyinde uyarı, NodeManager bileşeninden kullanılabilen düğüm durumu özelliğini denetler.                                                                                              |
| NodeManager Web Kullanıcı arabirimi                       | NodeManager Web Kullanıcı arabirimine ulaşılamıyorsa bu konak düzeyi uyarısı tetiklenir.                                                                                                                 |
| Süs Yot yüksek kullanılabilirlik durumu        | Bu hizmet düzeyi uyarı, etkin bir süs Code veya standby süs ODE çalışmıyorsa tetiklenir.                                                                                     |
| Dadtanode Işlemi                         | Bu ana bilgisayar düzeyi uyarı, tek tek bir ağ üzerinde bir işlem ve ağ üzerinde dinleme yapmak üzere ayrı bir Davode işlemi kurulamazsa tetiklenir.                                                         |
| Dadtanode Web Kullanıcı arabirimi                          | Bu konak düzeyi uyarısı, Dadtanode Web Kullanıcı arabirimine ulaşılamıyorsa tetiklenir.                                                                                                                    |
| Kullanılabilir JournalNodes yüzdesi           | Bu uyarı, kümedeki aşağı yönelik JournalNodes sayısı yapılandırılan kritik eşikten fazlaysa tetiklenir. Bu, JournalNode işlem denetimlerinin sonuçlarını toplar.        |
| Kullanılabilir gün yüzdesi              | Bu uyarı, kümedeki aşağı doğru eşik sayısı yapılandırılan kritik eşikten büyükse tetiklenir. Bu, Davtanode işlem denetimlerinin sonuçlarını toplar.              |
| Zeppelin sunucu durumu                   | Bu konak düzeyi uyarısı, Zeppelin sunucusunun istemci isteklerine yanıt verip vermeyeceğini tespit edilebilir ise tetiklenir.                                                                   |
| HiveServer2 etkileşimli Işlemi          | Hiveserverınteractive 'in istemci isteklerine yanıt verip vermediğini tespit etmediği durumlarda bu konak düzeyi uyarısı tetiklenir.                                                             |
| LLAP uygulaması                         | Bu uyarı, LLAP uygulamasının isteklere yanıt verip vermeyeceğini tespit edilebilir ise tetiklenir.                                                                                    |

Hakkında daha fazla bilgi görüntülemek için her bir hizmeti seçebilirsiniz.

Hizmet sayfası her bir hizmetin durumu ve yapılandırması hakkında bilgi sağladığından, hizmetin üzerinde çalıştığı baş düğüm hakkında bilgi sağlamaz. Bu bilgileri görüntülemek için sayfanın üst kısmındaki **konaklar** bağlantısını kullanın. Bu sayfa, baş düğümler de dahil olmak üzere küme içindeki Konakları görüntüler.

![Apache ambarı baş düğümüne ana bilgisayar listesi](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Baş düğümlerden birine yönelik bağlantıyı seçmek o düğümde çalışan hizmetleri ve bileşenleri görüntüler.

![Apache ambarı bileşen durumu](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Ambarı kullanma hakkında daha fazla bilgi için bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight 'ı izleme ve yönetme](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambarı REST API

REST API ambarı Internet üzerinden kullanılabilir. HDInsight genel ağ geçidi, yönlendirme isteklerini REST API Şu anda barındırmakta olan baş düğüme işler.

Bir hizmetin durumunu REST API ambarı aracılığıyla denetlemek için aşağıdaki komutu kullanabilirsiniz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* **Parolayı** http Kullanıcı (yönetici) hesabı parolasıyla değiştirin.
* **CLUSTERNAME** değerini kümenin adıyla değiştirin.
* **HizmetAdı** durumunu denetlemek istediğiniz hizmetin adıyla değiştirin.

Örneğin, **MyCluster**adlı bir kümede, bir **parola parolasıyla**, **Bu hizmetin durumunu** denetlemek için aşağıdaki komutu kullanabilirsiniz:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

Yanıt aşağıdaki JSON ile benzerdir:

```json
{
    "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

URL, hizmetin **hn0-clustername**adlı bir baş düğümde çalışmakta olduğunu bize söyler.

Durum, hizmetin Şu anda çalıştığını veya **başlatıldığını**söyler.

Kümede hangi hizmetlerin yüklü olduğunu bilmiyorsanız, bir liste almak için aşağıdaki komutu kullanabilirsiniz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

REST API ambarı ile çalışma hakkında daha fazla bilgi için bkz. [HDInsight 'ı Apache ambarı REST API kullanarak izleme ve yönetme](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Hizmet bileşenleri

Hizmetler, tek tek durumunu denetlemek istediğiniz bileşenleri içerebilir. Örneğin,,, bir süs Code bileşeni içerir. Bir bileşen hakkındaki bilgileri görüntülemek için, komut şöyle olur:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Bir hizmet tarafından hangi bileşenlerin sağlandığını bilmiyorsanız, bir liste almak için aşağıdaki komutu kullanabilirsiniz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Baş düğümlerdeki günlük dosyalarına erişme

### <a name="ssh"></a>SSH

SSH aracılığıyla bir baş düğüme bağlıyken günlük dosyaları **/var/log**altında bulunabilir. Örneğin, **/var/log/Hadoop-Yarn/Yarn** Yarn için günlükleri içerir.

Her bir baş düğüm benzersiz günlük girişlerine sahip olabilir, bu nedenle günlükleri her ikisinde de denetlemeniz gerekir.

### <a name="sftp"></a>SFTP

Ayrıca SSH Dosya Aktarım Protokolü veya güvenli Dosya Aktarım Protokolü (SFTP) kullanarak baş düğüme bağlanabilir ve günlük dosyalarını doğrudan indirebilirsiniz.

SSH istemcisi kullanmaya benzer şekilde, kümeye bağlanırken SSH kullanıcı hesabı adını ve kümenin SSH adresini sağlamanız gerekir. Örneğin, `sftp username@mycluster-ssh.azurehdinsight.net`. İstendiğinde hesabın parolasını sağlayın veya `-i` parametresini kullanarak bir ortak anahtar sağlayın.

Bağlandıktan sonra bir `sftp>` istemiyle karşılaşırsınız. Bu istem içinden dizinleri değiştirebilir, karşıya yükleyebilir ve dosyaları indirebilirsiniz. Örneğin, aşağıdaki komutlar dizinleri **/var/log/Hadoop/HDFS** diziniyle değiştirir ve sonra dizindeki tüm dosyaları indirir.

    cd /var/log/hadoop/hdfs
    get *

Kullanılabilir komutların listesi için `sftp>` istemine `help` girin.

> [!NOTE]  
> Ayrıca, SFTP kullanarak bağlıyken dosya sistemini görselleştirmenize imkan tanıyan grafiksel arabirimler de vardır. Örneğin, [MobaXterm](https://mobaxterm.mobatek.net/) , Windows Gezgini ' ne benzer bir arabirim kullanarak dosya sistemine gözatmanızı sağlar.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Ambarı kullanarak günlük dosyalarına erişmek için bir SSH tüneli kullanmanız gerekir. Bireysel hizmetlerin Web arabirimleri, Internet üzerinde herkese açık bir şekilde gösterilmez. SSH tüneli kullanma hakkında daha fazla bilgi için bkz. [SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md) belgesi.

Ambarı web kullanıcı arabiriminden, günlükleri görüntülemek istediğiniz hizmeti seçin (örneğin, YARN). Ardından, günlüklerin görüntüleneceği baş düğümü seçmek için **hızlı bağlantılar** ' ı kullanın.

![Günlükleri görüntülemek için hızlı bağlantıları kullanma](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Düğüm boyutunu yapılandırma

Bir düğümün boyutu yalnızca küme oluşturma sırasında seçilebilir. HDInsight [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/hdinsight/)HDInsight için KULLANILABILIR farklı VM boyutlarının listesini bulabilirsiniz.

Bir küme oluştururken düğümlerin boyutunu belirtebilirsiniz. Aşağıdaki bilgiler [Azure Portal](https://portal.azure.com/), [Azure PowerShell modül az](/powershell/azureps-cmdlets-docs)ve [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)kullanarak boyutun nasıl belirtilbileceğine ilişkin yönergeler sağlar:

* **Azure Portal**: bir küme oluştururken, küme tarafından kullanılan düğümlerin boyutunu ayarlayabilirsiniz:

    ![Düğüm boyutu seçimiyle küme oluşturma Sihirbazı görüntüsü](./media/hdinsight-high-availability-linux/hdinsight-headnodesize.png)

* **Azure CLI**: [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) komutunu kullanırken, `--headnode-size`, `--workernode-size`ve `--zookeepernode-size` parametrelerini kullanarak baş, çalışan ve ZooKeeper düğümlerinin boyutunu ayarlayabilirsiniz.

* **Azure PowerShell**: [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet 'ini kullanırken, `-HeadNodeSize`, `-WorkerNodeSize`ve `-ZookeeperNodeSize` parametrelerini kullanarak baş, çalışan ve ZooKeeper düğümlerinin boyutunu ayarlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede ele alınan öğeler hakkında daha fazla bilgi edinmek için bkz.:

* [Apache ambarı REST başvurusu](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Azure CLı 'yı yükleyip yapılandırma](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure PowerShell modülünü yükleyip yapılandırın az](/powershell/azure/overview)
* [Apache ambarı kullanarak HDInsight 'ı yönetme](hdinsight-hadoop-manage-ambari.md)
* [Linux tabanlı HDInsight kümeleri sağlama](hdinsight-hadoop-provision-linux-clusters.md)
