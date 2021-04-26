---
title: Küme boyutlarını ölçeklendirme-Azure HDInsight
description: Azure HDInsight 'taki iş yükünüzü eşleştirmek için bir Apache Hadoop kümesi esnek ölçeklendirme
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1c388cb070c66fc3a2322c358bc4113ed2106c77
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761858"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight kümelerini ölçeklendirme

HDInsight, kümelerinizdeki çalışan düğümlerinin sayısını artırma ve azaltma seçenekleri sayesinde esneklik sağlar. Bu esneklik, bir kümeyi saat veya hafta sonları ile küçültmenize olanak sağlar. Ve yoğun iş taleplerine göre genişletin.

Kümede yeterli kaynak olması için periyodik toplu işlemeden önce kümenizi ölçeklendirin.  İşlem tamamlandıktan sonra ve kullanım azaldığında, HDInsight kümesini daha az çalışan düğümü olarak ölçeklendirin.

Aşağıda özetlenen yöntemlerden birini kullanarak bir kümeyi el ile ölçekleyebilirsiniz. Ayrıca, belirli ölçümlere yanıt olarak ölçeği otomatik olarak genişletmek ve daraltmak için otomatik [ölçeklendirme](hdinsight-autoscale-clusters.md) seçeneklerini de kullanabilirsiniz.

> [!NOTE]  
> Yalnızca HDInsight sürümü 3.1.3 veya üzeri olan kümeler desteklenir. Kümenizin sürümünden emin değilseniz Özellikler sayfasını kontrol edebilirsiniz.

## <a name="utilities-to-scale-clusters"></a>Kümeleri ölçeklendirmek için yardımcı programlar

Microsoft, kümeleri ölçeklendirmek için aşağıdaki yardımcı programları sağlar:

|Yardımcı Program | Açıklama|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az_hdinsight_resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure Klasik CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure portalı](https://portal.azure.com)|HDInsight kümesi bölmesini açın, sol taraftaki menüden **küme boyutu** ' nu seçin, ardından küme boyutu bölmesinde çalışan düğümlerinin sayısını yazın ve Kaydet ' i seçin.|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure portal ölçeği kümesi seçeneği":::

Bu yöntemlerin herhangi birini kullanarak, HDInsight kümenizi dakikalar içinde yukarı veya aşağı ölçeklendirebilirsiniz.

> [!IMPORTANT]  
> * Klasik Azure CLı kullanım dışıdır ve yalnızca klasik dağıtım modeliyle kullanılmalıdır. Diğer tüm dağıtımlar için [Azure CLI](/cli/azure/)'yi kullanın.
> * PowerShell Azurerd modülü kullanım dışıdır.  Lütfen mümkün olan her durumda [az Module](/powershell/azure/new-azureps-module-az) kullanın.

## <a name="impact-of-scaling-operations"></a>Ölçeklendirme işlemlerinin etkisi

Çalışan HDInsight kümenize düğüm **eklediğinizde** (ölçeği büyütme), işler etkilenmez. Yeni işler, ölçeklendirme işlemi çalışırken güvenle gönderilebilir. Ölçeklendirme işlemi başarısız olursa, hata kümeniz işlevsel bir durumda bırakılır.

Düğümleri **kaldırırsanız** (ölçeği Azalt), ölçeklendirme işlemi tamamlandığında bekleyen veya çalışan işler başarısız olur. Bu hata, ölçeklendirme işlemi sırasında bazı hizmetler yeniden başlatılırken oluşur. Kümeniz el ile ölçeklendirme işlemi sırasında güvenli moda takılmış olabilir.

Veri düğümlerinin sayısını değiştirmenin etkisi, HDInsight tarafından desteklenen her küme türü için farklılık gösterir:

* Apache Hadoop

    Çalışan bir Hadoop kümesindeki çalışan düğümlerinin sayısını herhangi bir işi etkilemeden sorunsuzca artırabilirsiniz. İşlem devam ederken yeni işler de gönderilebilir. Ölçeklendirme işlemindeki başarısızlıklar düzgün şekilde işlenir. Küme her zaman işlevsel bir durumda bırakılır.

    Bir Hadoop kümesi daha az veri düğümüyle azaltıtıldığına göre bazı hizmetler yeniden başlatılır. Bu davranış, tüm çalışan ve bekleyen işlerin ölçeklendirme işleminin tamamlanmasından sonra başarısız olmasına neden olur. Ancak, işlem tamamlandıktan sonra işleri yeniden gönderebilirsiniz.

* Apache HBase

    Çalışırken, HBase kümenize düğümleri sorunsuzca ekleyebilir veya kaldırabilirsiniz. Bölgesel sunucular, ölçeklendirme işleminin tamamlanması birkaç dakika içinde otomatik olarak dengelenir. Ancak bölgesel sunucuları el ile dengeleyebilirsiniz. Küme baş düğümüne ' da oturum açın ve aşağıdaki komutları çalıştırın:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase kabuğunu kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'Ta Apache HBase örneği ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Fırtınası çalışırken veri düğümlerini sorunsuzca ekleyebilir veya kaldırabilirsiniz. Ancak, ölçeklendirme işleminin başarılı bir şekilde tamamlandıktan sonra, topolojiyi yeniden dengelemeniz gerekir. Yeniden dengeleme, topolojinin kümedeki yeni düğüm sayısına göre yalnızca [paralellik ayarları](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) ayarlamasına olanak tanır. Çalışan topolojileri yeniden dengelemek için aşağıdaki seçeneklerden birini kullanın:

  * Fırtınası Web Kullanıcı arabirimi

    Bir topolojiyi fırtınası Kullanıcı arabirimini kullanarak yeniden dengelemek için aşağıdaki adımları kullanın.

    1. `https://CLUSTERNAME.azurehdinsight.net/stormui`Web tarayıcınızda açın, burada, `CLUSTERNAME` fırtınası kümenizin adıdır. İstenirse, kümeyi oluştururken belirttiğiniz HDInsight Küme Yöneticisi (yönetici) adını ve parolasını girin.

    1. Yeniden dengelemek istediğiniz topolojiyi seçin, sonra yeniden **Dengeleme** düğmesini seçin. Yeniden dengeleme işlemi yapılmadan önce gecikme girin.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HDInsight fırtınası ölçeği yeniden dengeleme":::

  * Komut satırı arabirimi (CLı) aracı

    Sunucuya bağlanın ve bir topolojiyi yeniden dengelemek için aşağıdaki komutu kullanın:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Ayrıca, ilk olarak topoloji tarafından sağlanmış paralellik ipuçlarını geçersiz kılmak için parametreler belirtebilirsiniz. Örneğin, aşağıdaki kod, `mytopology` topolojiyi 5 çalışan işlem, mavi-Spout bileşeni için 3 yürütme ve sarı-cıvata bileşeni için 10 yürütme sayısı olarak yeniden yapılandırır.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Ölçeklendirme işlemlerinden sonra bölüm çoğaltmalarını yeniden dengelemeniz gerekir. Daha fazla bilgi için bkz. [HDInsight 'ta Apache Kafka verilerin yüksek kullanılabilirliği](./kafka/apache-kafka-high-availability.md) belgesi.

* Apache Hive LLAP

    Çalışan düğümlerine ölçeklendirdikten sonra `N` , HDInsight aşağıdaki konfigürasyonları otomatik olarak ayarlar ve Hive 'yi yeniden başlatır.

  * Toplam eşzamanlı sorgu sayısı üst sınırı: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Hive 'ın LLAP tarafından kullanılan düğüm sayısı: `num_llap_nodes  = N`
  * Hive LLAP cini çalıştırmak için düğüm sayısı: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Bir kümenin güvenle ölçeğini azaltma

### <a name="scale-down-a-cluster-with-running-jobs"></a>Çalışan işler ile küme ölçeğini azaltma

Bir ölçek azaltma işlemi sırasında çalışan işlerinizin başarısız olmasına engel olmak için üç şeyi deneyebilirsiniz:

1. Kümenizin ölçeğini ölçeklendirmeden önce işlerin tamamlanmasını bekleyin.
1. İşleri el ile sonlandırın.
1. Ölçeklendirme işlemi tamamlandıktan sonra işleri yeniden gönderin.

Bekleyen ve çalışan işlerin bir listesini görmek için, aşağıdaki adımları izleyerek YARN **Kaynak Yöneticisi Kullanıcı arabirimini** kullanabilirsiniz:

1. [Azure Portal](https://portal.azure.com/), kümenizi seçin.  Küme yeni bir portal sayfasında açılır.
2. Ana görünümden, **küme panoları**  >  **ambarı giriş** sayfasına gidin. Küme kimlik bilgilerinizi girin.
3. Ambarı kullanıcı arabiriminden, sol taraftaki menüdeki hizmetler listesinden **Yarn** ' yi seçin.  
4. YARN sayfasında **hızlı bağlantılar** ' ı seçin ve etkin baş düğümün üzerine gelin ve **Kaynak Yöneticisi Kullanıcı arabirimi**' ni seçin.

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Apache ambarı hızlı bağlantılar Kaynak Yöneticisi Kullanıcı arabirimi":::

Kaynak Yöneticisi Kullanıcı arabirimine doğrudan erişim sağlayabilirsiniz `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

İşlerin bir listesini geçerli durumuyla birlikte görürsünüz. Ekran görüntüsünde Şu anda çalışan bir iş vardır:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Kaynak Yöneticisi UI uygulamaları":::

Çalışan uygulamayı el ile sonlandırmak için SSH kabuğundan aşağıdaki komutu yürütün:

```bash
yarn application -kill <application_id>
```

Örnek:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Güvenli modda takılıyor

Bir kümenin ölçeğini ölçeklendirirseniz, HDInsight, önce ek çalışan düğümlerin yetkisini almak için Apache ambarı yönetim arabirimlerini kullanır. Düğümler, öbekler bloklarını diğer çevrimiçi çalışan düğümlerine çoğaltır. Bu tarihten sonra HDInsight, kümeyi güvenle ölçeklendirir. , Ölçeklendirme işlemi sırasında güvenli moda gider. Ölçeklendirmenin tamamlanmasının bir kez gelmesi gerekir. Ancak, bazı durumlarda,-çoğaltma altındaki dosya bloğu nedeniyle bir ölçeklendirme işlemi sırasında, bu işlem güvenli modda takıldığında.

Varsayılan olarak,, `dfs.replication` her bir dosya bloğunun kaç kopyasının kullanılabilir olduğunu denetleyen 1 ayarıyla yapılandırılır. Bir dosya bloğunun her kopyası, kümenin farklı bir düğümünde depolanır.

Beklenen blok kopyası sayısı kullanılabilir olmadığında, bu güvenli moda girer ve ambarı uyarı oluşturur. , Ölçeklendirme işlemi için güvenli mod girebilir. Çoğaltma için gereken düğüm sayısı saptanmıyorsa küme güvenli moda takılmış olabilir.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Güvenli Mod açıkken oluşan örnek hatalar

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

`/var/log/hadoop/hdfs/`Klasörden, güvenli mod girdiği zamana yaklaşarak, küme ölçeklendiği zaman yakınında, ad düğümü günlüklerini gözden geçirebilirsiniz. Günlük dosyaları adlandırılır `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

Kök nedeni, Hive 'in sorguları çalıştırırken bir geçici dosyalara göre, IBir güvenli moda girdiğinde Hive, IBU ' ye yazamadığı için sorguları çalıştıramıyorum. Çalışma alanındaki geçici dosyalar, tek tek çalışan düğümü VM 'lerine bağlanmış yerel sürücüde bulunur. Dosyalar, en az üç çoğaltmaındaki diğer çalışan düğümleri arasında çoğaltılır.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>HDInsight 'ın güvenli modda takılmasını engelleme

HDInsight 'ın güvenli modda ayrılmasının birkaç yolu vardır:

* HDInsight 'ı ölçeklendirmeden önce tüm Hive işlerini durdurun. Alternatif olarak, Hive işlerinin çalıştırıldığı ile çakışmaktan kaçınmak için ölçek azaltma sürecini zamanlayın.
* `tmp`Ölçeği ölçeklendirmeden önce, Hive 'nin karalama dizin dosyalarını el ile temizleyin.
* HDInsight 'ı yalnızca üç çalışan düğümüne (minimum) ölçeklendirin. Bir çalışan düğümü kadar düşük bir şekilde gitmemeye özen gösterin.
* Gerekirse güvenli moddan çıkmak için komutunu çalıştırın.

Aşağıdaki bölümlerde bu seçenekler açıklanır.

#### <a name="stop-all-hive-jobs"></a>Tüm Hive işlerini durdur

Bir çalışan düğümüne ölçeklendirmeden önce tüm Hive işlerini durdurun. İş yükünüz zamanlanırsa, Hive işi yapıldıktan sonra ölçeği aşağı doğru yürütün.

Ölçeklendirmeden önce Hive işleri durdurulduğunda, tmp klasöründeki (varsa) karalama dosyalarının sayısını en aza indirmenize yardımcı olur.

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive 'nin karalama dosyalarını el ile Temizleme

Hive geçici dosyaların arkasında bırakılırsa, güvenli moddan kaçınmak için ölçeği ölçeklendirmeden önce bu dosyaları el ile temizleyebilirsiniz.

1. Yapılandırma özelliğine bakarak Hive geçici dosyaları için hangi konumun kullanıldığını kontrol edin `hive.exec.scratchdir` . Bu parametre içinde ayarlanır `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive hizmetlerini durdurun ve tüm sorguların ve işlerin tamamlandığından emin olun.

1. Yukarıda bulunan karalama dizininin içeriğini listeleyin ve `hdfs://mycluster/tmp/hive/` herhangi bir dosya içerip içermediğinden onları görüntüleyin:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Dosyalar mevcut olduğunda örnek bir çıktı aşağıda verilmiştir:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Hive 'nin bu dosyalarla yapıldığını biliyorsanız, bunları kaldırabilirsiniz. Yarn Kaynak Yöneticisi UI sayfasına bakarak Hive 'nin çalışan herhangi bir sorgu içermediğinden emin olun.

    Dosyaları bir sunucudan kaldırmak için örnek komut satırı:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight 'ı üç veya daha fazla çalışan düğümüne ölçeklendirin

En az üç çalışan düğümüne ölçeklendirirken kümeleriniz güvenli modda takıldığı takdirde, en az üç çalışan düğümü saklayın.

Üç çalışan düğümünün olması, ölçeklendirmenin yalnızca bir çalışan düğümüne kıyasla daha pahalı olmasını sağlar. Ancak bu eylem, kümenizin güvenli modda takılmasını engeller.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>HDInsight 'ı bir çalışan düğümüne kadar ölçeklendirin

Küme, bir düğüme aşağı ölçeklendirildiğinde bile, çalışan düğümü 0 devam eder. Çalışan düğümü 0, hiçbir şekilde kullanımdan kaldırılamıyor.

#### <a name="run-the-command-to-leave-safe-mode"></a>Güvenli moddan çıkmak için komutu çalıştırın

Son seçenek, güvenli mod bırak komutunun yürütülmektir. Bu, çoğaltma altında Hive dosyası nedeniyle güvenli mod olarak girilmişse, güvenli moddan çıkmak için aşağıdaki komutu yürütün:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase kümesinin ölçeğini azaltma

Bir ölçeklendirme işlemi tamamlandıktan sonra bölge sunucuları birkaç dakika içinde otomatik olarak dengelenir. Bölge sunucularını el ile dengelemek için aşağıdaki adımları izleyin:

1. SSH kullanarak HDInsight kümesine bağlanın. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

2. HBase kabuğunu başlatın:

    ```bash
    hbase shell
    ```

3. Bölge sunucularını el ile dengelemek için aşağıdaki komutu kullanın:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight kümelerini otomatik olarak ölçeklendirme](hdinsight-autoscale-clusters.md)

HDInsight kümenizi ölçeklendirmeyle ilgili belirli bilgiler için, bkz.:

* [HDInsight 'ta Apache Hadoop kümelerini Azure portal kullanarak yönetin](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Azure CLı kullanarak HDInsight 'ta Apache Hadoop kümelerini yönetme](hdinsight-administer-use-command-line.md#scale-clusters)