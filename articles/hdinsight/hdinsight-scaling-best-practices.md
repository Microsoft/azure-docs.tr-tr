---
title: Küme boyutlarını ölçeklendirme-Azure HDInsight
description: Azure HDInsight 'taki iş yükünüzü eşleştirmek için bir Apache Hadoop kümesi esnek ölçeklendirme
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 4a1d835ebe47ec36bb839da8dcbcd107ffcb9c4c
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161966"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight kümelerini ölçeklendirme

HDInsight, kümelerinizdeki çalışan düğümlerinin sayısını artırma ve azaltma seçeneği sunarak esneklik sağlar. Bu esneklik, bir kümeyi saat veya hafta sonları ile küçültmenize ve yoğun iş talepleri sırasında genişletmenize olanak tanır.

Dönemsel toplu işleme sahipseniz, bu işlemden önce HDInsight kümesi birkaç dakika kadar ölçeklendirilebilir ve bu sayede kümenizin yeterli bellek ve CPU gücü vardır.  Daha sonra, işlem yapıldıktan sonra, kullanım yeniden geçtikten sonra HDInsight kümesini daha az çalışan düğümüne ölçeklendirebilirsiniz.

Aşağıda özetlenen yöntemlerden birini kullanarak bir kümeyi el ile ölçeklendirebilir veya CPU, bellek ve diğer ölçümlere yanıt olarak sistemin otomatik olarak ölçeğini ve kapatılmasını sağlamak için otomatik [ölçeklendirme](hdinsight-autoscale-clusters.md) seçeneklerini kullanabilirsiniz.

> [!NOTE]  
> Yalnızca HDInsight sürümü 3.1.3 veya üzeri olan kümeler desteklenir. Kümenizin sürümünden emin değilseniz Özellikler sayfasını kontrol edebilirsiniz.

## <a name="utilities-to-scale-clusters"></a>Kümeleri ölçeklendirmek için yardımcı programlar

Microsoft, kümeleri ölçeklendirmek için aşağıdaki yardımcı programları sağlar:

|Utility | Açıklama|
|---|---|
|[PowerShell az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -clustername \<Cluster name >-targetınstancecount \<newSize >|
|[PowerShell Azurerd](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -clustername \<Cluster name >-targetınstancecount \<newSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az HDInsight Resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --kaynak grubu \<kaynak grubu >--ad \<küme adı >--Target-Instance-Count \<newSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|Azure HDInsight kümesi yeniden \<boyutlandırma clusterName \<> hedef örnek sayısı > |
|[Azure portal](https://portal.azure.com)|HDInsight kümesi bölmesini açın, sol taraftaki menüden **küme boyutu** ' nu seçin, ardından küme boyutu bölmesinde çalışan düğümlerinin sayısını yazın ve Kaydet ' i seçin.|  

![Azure portal ölçeği kümesi seçeneği](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

Bu yöntemlerin herhangi birini kullanarak, HDInsight kümenizi dakikalar içinde yukarı veya aşağı ölçeklendirebilirsiniz.

> [!IMPORTANT]  
> * Klasik Azure CLı kullanım dışıdır ve yalnızca klasik dağıtım modeliyle kullanılmalıdır. Diğer tüm dağıtımlar için [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)'yi kullanın.  
> * PowerShell Azurerd modülü kullanım dışıdır.  Lütfen mümkün olan her durumda [az Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) kullanın.

## <a name="impact-of-scaling-operations"></a>Ölçeklendirme işlemlerinin etkisi

Çalışan HDInsight kümenize düğüm **eklediğinizde** (ölçeği büyütme), bekleyen veya çalışan tüm işler etkilenmez. Yeni işler, ölçeklendirme işlemi çalışırken güvenle gönderilebilir. Ölçeklendirme işlemi herhangi bir nedenle başarısız olursa, bu hata, kümenizi işlevsel bir durumda bırakmak için işlenir.

Düğümleri **kaldırırsanız** (ölçeği Azalt), ölçeklendirme işlemi tamamlandığında bekleyen veya çalışan tüm işler başarısız olur. Bu hata, ölçeklendirme sürecinde bazı hizmetlerin yeniden başlatılması nedeniyle oluşur. Ayrıca, bir el ile ölçeklendirme işlemi sırasında kümenizin güvenli modda takılmasına da bir risk vardır.

Veri düğümlerinin sayısını değiştirmenin etkisi, HDInsight tarafından desteklenen her küme türü için farklılık gösterir:

* Apache Hadoop

    Bekleyen veya çalışan bir işi etkilemeden, çalışan bir Hadoop kümesinde çalışan düğümlerinin sayısını sorunsuzca artırabilirsiniz. İşlem devam ederken yeni işler de gönderilebilir. Ölçeklendirme işlemindeki başarısızlık, kümenin her zaman işlevsel bir durumda bırakılması için düzgün şekilde işlenir.

    Bir Hadoop kümesi, veri düğümleri sayısını azaltarak aşağı ölçeklendirilirse, kümedeki hizmetlerin bazıları yeniden başlatılır. Bu davranış, tüm çalışan ve bekleyen işlerin ölçeklendirme işleminin tamamlanmasından sonra başarısız olmasına neden olur. Ancak, işlem tamamlandıktan sonra işleri yeniden gönderebilirsiniz.

* Apache HBase

    Çalışırken HBase kümenize düğümleri sorunsuzca ekleyebilir veya kaldırabilirsiniz. Bölgesel sunucular, ölçeklendirme işleminin tamamlanması birkaç dakika içinde otomatik olarak dengelenir. Ancak, kümenin baş düğümüne ' da oturum açarak ve bir komut istemi penceresinden aşağıdaki komutları çalıştırarak bölgesel sunucuları el ile dengeleyebilirsiniz:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase kabuğunu kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'Ta Apache HBase örneği ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Veri düğümlerini, çalışırken fırtınası kümenize sorunsuzca ekleyebilir veya kaldırabilirsiniz. Ancak, ölçeklendirme işleminin başarılı bir şekilde tamamlanmasından sonra topolojiyi yeniden dengelemeniz gerekir.

    Yeniden dengeleme iki şekilde sağlanabilir:

  * Fırtınası Web Kullanıcı arabirimi
  * Komut satırı arabirimi (CLı) aracı

    Daha fazla bilgi için [Apache Storm belgelerine](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) bakın.

    Fırtınası Web Kullanıcı arabirimi HDInsight kümesinde kullanılabilir:

    ![HDInsight fırtınası ölçeği yeniden dengeleme](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Aşağıda, fırtınası topolojisini yeniden dengelemek için örnek bir CLı komutu verilmiştir:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Bir kümenin güvenle ölçeğini azaltma

### <a name="scale-down-a-cluster-with-running-jobs"></a>Çalışan işler ile küme ölçeğini azaltma

Bir ölçek azaltma işlemi sırasında çalışan işlerinizin başarısız olmasına engel olmak için üç şeyi deneyebilirsiniz:

1. Kümenizin ölçeğini ölçeklendirmeden önce işlerin tamamlanmasını bekleyin.
1. İşleri el ile sonlandırın.
1. Ölçeklendirme işlemi tamamlandıktan sonra işleri yeniden gönderin.

Bekleyen ve çalışan işlerin bir listesini görmek için, aşağıdaki adımları izleyerek YARN **Kaynak Yöneticisi Kullanıcı arabirimini**kullanabilirsiniz:

1. [Azure Portal](https://portal.azure.com/), kümenizi seçin.  Yönergeler için bkz. [liste ve kümeleri gösterme](./hdinsight-administer-use-portal-linux.md#showClusters) . Küme yeni bir portal sayfasında açılır.
2. Ana görünümden, **küme panoları** > **ambarı giriş**sayfasına gidin. Küme kimlik bilgilerinizi girin.
3. Ambarı kullanıcı arabiriminden, sol taraftaki menüdeki hizmetler listesinden **Yarn** ' yi seçin.  
4. YARN sayfasında **hızlı bağlantılar** ' ı seçin ve etkin baş düğümünün üzerine gelin, sonra **ResourceManager Kullanıcı arabirimi**' ni seçin.

    ![Apache ambarı hızlı bağlantılar ResourceManager Kullanıcı arabirimi](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

ResourceManager Kullanıcı arabirimine doğrudan ile `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`erişebilirsiniz.

İşlerin bir listesini geçerli durumuyla birlikte görürsünüz. Ekran görüntüsünde Şu anda çalışan bir iş vardır:

![ResourceManager UI uygulamaları](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Çalışan uygulamayı el ile sonlandırmak için SSH kabuğundan aşağıdaki komutu yürütün:

```bash
yarn application -kill <application_id>
```

Örneğin:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Güvenli modda takılarak

Bir kümenin ölçeğini ölçeklendirirseniz, HDInsight, her zaman ek çalışan düğümlerini kullanımdan kaldırmak için Apache ambarı yönetim arabirimlerini kullanır. Bu tarihten sonra HDInsight, kümeyi güvenle ölçeklendirir. , Ölçeklendirme işlemi sırasında güvenli moda gider ve ölçekleme tamamlandıktan sonra bu duruma gelmesi gerekir. Ancak, bazı durumlarda,-çoğaltma altındaki dosya bloğu nedeniyle bir ölçeklendirme işlemi sırasında, bu işlem güvenli modda takıldığında.

Varsayılan olarak,, her bir `dfs.replication` dosya bloğunun kaç kopyasının kullanılabilir olduğunu denetleyen 3 ayarıyla yapılandırılır. Bir dosya bloğunun her kopyası, kümenin farklı bir düğümünde depolanır.

Ne zaman, beklenen sayıda blok kopyası yoksa, Kip güvenli moda girer ve ambarı uyarı oluşturur. , Bir ölçeklendirme işlemi için güvenli moda girerse, ancak gerekli düğüm sayısı çoğaltma için algılanmadığından güvenli moddan çıkamıyorsa, küme güvenli modda kalabilir.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Güvenli Mod açıkken oluşan örnek hatalar

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

`/var/log/hadoop/hdfs/` Klasörden, güvenli mod girdiği zamana yaklaşarak, küme ölçeklendiği zaman yakınında, ad düğümü günlüklerini gözden geçirebilirsiniz. Günlük dosyaları adlandırılır `Hadoop-hdfs-namenode-hn0-clustername.*`.

Önceki hataların kök nedeni, Hive 'in sorguları çalıştırırken, geçici dosyalara bir süre içinde bağlı olmasına bağlıdır. IBir güvenli moda girdiğinde Hive, IBU öğesine yazamadığından sorguları çalıştıramıyor. Çalışma alanındaki geçici dosyalar, tek tek çalışan düğümü VM 'lerine bağlı olan yerel sürücüde bulunur ve en az üç çoğaltmadan diğer çalışan düğümleri arasında çoğaltılır.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>HDInsight 'ın güvenli modda takılmasını engelleme

HDInsight 'ın güvenli modda ayrılmasının birkaç yolu vardır:

* HDInsight 'ı ölçeklendirmeden önce tüm Hive işlerini durdurun. Alternatif olarak, Hive işlerinin çalıştırıldığı ile çakışmaktan kaçınmak için ölçek azaltma sürecini zamanlayın.
* Ölçeği ölçeklendirmeden önce, Hive `tmp` 'nin karalama dizin dosyalarını el ile temizleyin.
* HDInsight 'ı yalnızca üç çalışan düğümüne (minimum) ölçeklendirin. Bir çalışan düğümü kadar düşük bir şekilde gitmemeye özen gösterin.
* Gerekirse güvenli moddan çıkmak için komutunu çalıştırın.

Aşağıdaki bölümlerde bu seçenekler açıklanır.

#### <a name="stop-all-hive-jobs"></a>Tüm Hive işlerini durdur

Bir çalışan düğümüne ölçeklendirmeden önce tüm Hive işlerini durdurun. İş yükünüz zamanlanırsa, Hive işi yapıldıktan sonra ölçeği aşağı doğru yürütün.

Ölçeklendirmeden önce Hive işleri durdurulduğunda, tmp klasöründeki (varsa) karalama dosyalarının sayısını en aza indirmenize yardımcı olur.

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive 'nin karalama dosyalarını el ile Temizleme

Hive geçici dosyaların arkasında bırakılırsa, güvenli moddan kaçınmak için ölçeği ölçeklendirmeden önce bu dosyaları el ile temizleyebilirsiniz.

1. `hive.exec.scratchdir` Yapılandırma özelliğine bakarak Hive geçici dosyaları için hangi konumun kullanıldığını kontrol edin. Bu parametre içinde `/etc/hive/conf/hive-site.xml`ayarlanır:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive hizmetlerini durdurun ve tüm sorguların ve işlerin tamamlandığından emin olun.
2. Yukarıda bulunan karalama dizininin içeriğini listeleyin ve `hdfs://mycluster/tmp/hive/` herhangi bir dosya içerip içermediğinden onları görüntüleyin:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Dosyalar mevcut olduğunda örnek bir çıktı aşağıda verilmiştir:

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Hive 'nin bu dosyalarla yapıldığını biliyorsanız, bunları kaldırabilirsiniz. Hive 'nin Yarn ResourceManager UI sayfasına bakarak herhangi bir sorgu içermediğinden emin olun.

    Dosyaları bir sunucudan kaldırmak için örnek komut satırı:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight 'ı üç veya daha fazla çalışan düğümüne ölçeklendirin

En az üç çalışan düğümüne ölçeklendirirken kümeleriniz güvenli modda takılıyorsa ve önceki adımlar çalışmazsa, en az üç çalışan düğümü tutarak kümenizin güvenli moda tamamen devam etmesinden kaçınabilirsiniz.

Üç çalışan düğümünü koruma, yalnızca bir çalışan düğümüne ölçeklendirmeden daha maliyetlidir, ancak kümenizin güvenli modda takılmasını önler.

#### <a name="run-the-command-to-leave-safe-mode"></a>Güvenli moddan çıkmak için komutu çalıştırın

Son seçenek, güvenli mod bırak komutunun yürütülmektir. Güvenli moda girme nedeninin, çoğaltma altındaki Hive dosyası nedeniyle olduğunu biliyorsanız, güvenli moddan çıkmak için aşağıdaki komutu çalıştırabilirsiniz:

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

* [Azure HDInsight kümelerini otomatik ölçeklendirme](hdinsight-autoscale-clusters.md)
* [Azure HDInsight 'a giriş](hadoop/apache-hadoop-introduction.md)
