---
title: Erişim Apache Hadoop YARN uygulama günlükleri-Azure HDInsight
description: Hem komut satırı hem de bir Web tarayıcısı kullanarak Linux tabanlı HDInsight (Apache Hadoop) kümesinde YARN uygulama günlüklerine erişmeyi öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 9558e6c5ddd58b1d5fd70da03187caef50d1275d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865579"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Linux tabanlı HDInsight 'ta YARN uygulama günlüklerine erişim Apache Hadoop

Azure HDInsight 'ta bir Apache Hadoop kümesindeki [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (henüz başka bir kaynak Negotiator) uygulamaları için günlüklere erişmeyi öğrenin.

## <a name="what-is-apache-yarn"></a>Apache YARN nedir?

YARN, kaynak yönetimini uygulama zamanlama/izlemeye ayırarak birden çok programlama modelini (Apache Hadoop MapReduce) destekler. YARN *`ResourceManager`* , küresel (RM), çalışan-düğüm *nodeyöneticileri* (NMS) ve uygulama başına *applicationmaster* (AMS) kullanır. Uygulama başına, uygulamanızı RM ile çalıştırmaya yönelik kaynaklar (CPU, bellek, disk, ağ) tarafından anlaşma yapılır. RM, *kapsayıcılar* olarak verilen bu kaynakları vermek Için NMS ile birlikte çalışmaktadır. Bu, kendisine atanan kapsayıcıların, RM tarafından ilerlemesini izlemekten sorumludur. Uygulama, uygulamanın yapısına bağlı olarak çok sayıda kapsayıcı gerektirebilir.

Her uygulama birden çok *uygulama denemesinden* oluşabilir. Bir uygulama başarısız olursa, yeni bir deneme olarak yeniden denenebilir. Her deneme bir kapsayıcıda çalışır. Bir kapsayıcı, bir YARN uygulaması tarafından gerçekleştirilen temel çalışma birimi için bağlam sağlar. Kapsayıcının bağlamı içinde gerçekleştirilen tüm işler, kapsayıcının verildiği tek çalışan düğümünde yapılır. Bkz. [Hadoop: Yarn uygulamaları yazma](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)veya daha fazla başvuru için [Yarn Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) .

Daha fazla işleme verimini desteklemek üzere kümenizi ölçeklendirmek için, birkaç farklı dil kullanarak [Otomatik ölçeklendirmeyi](hdinsight-autoscale-clusters.md) veya [kümelerinizi el ile ölçeklendirdirebilirsiniz](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>YARN zaman çizelgesi sunucusu

[Apache Hadoop YARN zaman çizelgesi sunucusu](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) , tamamlanan uygulamalarla ilgili genel bilgiler sağlar

YARN zaman çizelgesi sunucusu aşağıdaki veri türlerini içerir:

* Uygulama KIMLIĞI, bir uygulamanın benzersiz tanımlayıcısı
* Uygulamayı başlatan Kullanıcı
* Uygulamayı tamamlamaya yönelik denemelere ilişkin bilgiler
* Belirli bir uygulama denemesi tarafından kullanılan kapsayıcılar

## <a name="yarn-applications-and-logs"></a>YARN uygulamaları ve günlükleri

Uygulama günlükleri (ve ilişkili kapsayıcı günlükleri), sorunlu Hadoop uygulamalarında hata ayıklama açısından kritik öneme sahiptir. YARN, uygulama günlüklerini toplama, toplama ve günlük toplama ile depolama için iyi bir çerçeve sağlar.

Günlük toplama özelliği, uygulama günlüklerine daha belirleyici bir şekilde erişmenizi sağlar. Bir çalışan düğümündeki tüm kapsayıcılar üzerinde günlükleri toplar ve bunları çalışan düğümü başına bir toplu günlük dosyası olarak depolar. Bir uygulama bittikten sonra günlük varsayılan dosya sisteminde depolanır. Uygulamanız yüzlerce veya binlerce kapsayıcı kullanabilir, ancak tek bir çalışan düğümünde çalıştırılan tüm kapsayıcılar için Günlükler her zaman tek bir dosyaya toplanır. Bu nedenle, uygulamanız tarafından kullanılan her çalışan düğümü için yalnızca 1 günlük bir oturum vardır. Günlük toplama, HDInsight kümeleri sürüm 3,0 ve üzerinde varsayılan olarak etkindir. Toplanan Günlükler, küme için varsayılan depolamada bulunur. Aşağıdaki yol, günlüklerin bir yolu olarak verilmiştir:

```
/app-logs/<user>/logs/<applicationId>
```

Yolda, `user` uygulamayı başlatan kullanıcının adıdır. , `applicationId` YARN RM tarafından bir uygulamaya atanan benzersiz tanıtıcıdır.

Toplanan Günlükler, bir TFile dosyasında yazıldığı gibi doğrudan okunamaz, kapsayıcı tarafından dizine alınmış ikili biçimdedir. `ResourceManager`Bu günlükleri uygulamalar veya ilgilendiğiniz kapsayıcılar için düz metin olarak görüntülemek üzere YARN günlükleri veya CLI araçları 'nı kullanın.

## <a name="yarn-logs-in-an-esp-cluster"></a>Bir ESP kümesindeki Yarn günlükleri

İki yapılandırmanın, ambarı 'nda özel olarak eklenmesi gerekir `mapred-site` .

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net` , `CLUSTERNAME` Kümenizin adı olan ' a gidin.

1. Ambarı kullanıcı arabiriminden **MapReduce2**  >  **configs**  >  **Gelişmiş**  >  **özel mapred-site** bölümüne gidin.

1. Aşağıdaki özellik kümelerinden *birini* ekleyin:

    **1 ayarla**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **2 ayarla**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Değişiklikleri kaydedin ve etkilenen tüm hizmetleri yeniden başlatın.

## <a name="yarn-cli-tools"></a>YARN CLı araçları

1. Kümenize bağlanmak için [SSH komutunu](./hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Şu komutu kullanarak şu anda çalışan Yarn uygulamalarının tüm uygulama kimliklerini listeleyin:

    ```bash
    yarn top
    ```

    Günlüklerin indirileceği sütundan uygulama KIMLIĞI ' ni aklınızda edin `APPLICATIONID` .

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Aşağıdaki komutlardan birini çalıştırarak, bu günlükleri düz metin olarak görüntüleyebilirsiniz:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    &lt; &lt; Bu komutları çalıştırırken Application>, Kullanıcı tarafından başlatılan-uygulama>, &lt; containerıd> ve &lt; çalışan düğümü-adresi> bilgilerini belirtin.

### <a name="other-sample-commands"></a>Diğer örnek komutlar

1. Aşağıdaki komutla tüm uygulama yöneticileri için Yarn kapsayıcıları günlüklerini indirin. Bu adım, metin biçiminde adlı günlük dosyasını oluşturur `amlogs.txt` .

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Yarn kapsayıcı günlüklerini yalnızca en son uygulama ana için aşağıdaki komutla indirin:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Aşağıdaki komutla, ilk iki uygulama ana için YARN kapsayıcı günlüklerini indirin:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Tüm Yarn kapsayıcı günlüklerini aşağıdaki komutla indirin:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Aşağıdaki komutla belirli bir kapsayıcı için Yarn kapsayıcı günlüğünü indirin:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>YARN `ResourceManager` Kullanıcı arabirimi

YARN `ResourceManager` UI, küme headnode üzerinde çalışır. Bu, ambarı Web Kullanıcı arabirimi üzerinden erişilir. YARN günlüklerini görüntülemek için aşağıdaki adımları kullanın:

1. Web tarayıcınızda öğesine gidin `https://CLUSTERNAME.azurehdinsight.net` . CLUSTERNAME değerini HDInsight kümenizin adıyla değiştirin.

2. Soldaki hizmetler listesinden **Yarn**' yi seçin.

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png" alt-text="Apache ambarı Yarn hizmeti seçildi":::

3. **Hızlı bağlantılar** açılan listesinden küme baş düğümlerinden birini seçin ve ardından öğesini seçin **`ResourceManager Log`** .

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png" alt-text="Apache ambarı Yarn hızlı bağlantıları":::

    YARN günlüklerine bağlantıların bir listesini görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight’ta Apache Hadoop mimarisi](hdinsight-hadoop-architecture.md)
* [Azure HDInsight kullanarak Apache Hadoop YARN sorunlarını giderme](hdinsight-troubleshoot-yarn.md)
