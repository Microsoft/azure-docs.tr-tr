---
title: Erişim Apache Hadoop YARN uygulama günlükleri-Azure HDInsight
description: Hem komut satırı hem de bir Web tarayıcısı kullanarak Linux tabanlı HDInsight (Apache Hadoop) kümesinde YARN uygulama günlüklerine erişmeyi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: 263456769ab391cbc0588eed1a714a1ea5788154
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494887"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Linux tabanlı HDInsight 'ta YARN uygulama günlüklerine erişim Apache Hadoop

Azure HDInsight 'ta bir [Apache Hadoop](https://hadoop.apache.org/) kümesindeki [Apache Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (henüz başka bir kaynak Negotiator) uygulamaları için günlüklere erişmeyi öğrenin.

## <a name="YARNTimelineServer"></a>YARN zaman çizelgesi sunucusu

[Apache Hadoop YARN zaman çizelgesi sunucusu](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) , tamamlanan uygulamalarla ilgili genel bilgiler sağlar

YARN zaman çizelgesi sunucusu aşağıdaki veri türlerini içerir:

* Uygulama KIMLIĞI, bir uygulamanın benzersiz tanımlayıcısı
* Uygulamayı başlatan Kullanıcı
* Uygulamayı tamamlamaya yönelik denemelere ilişkin bilgiler
* Belirli bir uygulama denemesi tarafından kullanılan kapsayıcılar

## <a name="YARNAppsAndLogs"></a>YARN uygulamaları ve günlükleri

YARN, kaynak yönetimini uygulama zamanlama/izlemeye ayırarak birden çok programlama modelini ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) ) destekler. YARN, küresel bir *ResourceManager* (RM), Worker-node *nodeyöneticileri* (NMS) ve uygulama başına *applicationmaster* (AMS) kullanır. Uygulama başına, uygulamanızı RM ile çalıştırmaya yönelik kaynaklar (CPU, bellek, disk, ağ) tarafından anlaşma yapılır. RM, *kapsayıcılar*olarak verilen bu kaynakları vermek Için NMS ile birlikte çalışmaktadır. Bu, kendisine atanan kapsayıcıların, RM tarafından ilerlemesini izlemekten sorumludur. Uygulama, uygulamanın yapısına bağlı olarak çok sayıda kapsayıcı gerektirebilir.

Her uygulama birden çok *uygulama denemesinden*oluşabilir. Bir uygulama başarısız olursa, yeni bir deneme olarak yeniden denenebilir. Her deneme bir kapsayıcıda çalışır. Bir kapsayıcı, bir YARN uygulaması tarafından gerçekleştirilen temel çalışma birimi için bağlam sağlar. Bir kapsayıcı bağlamı içinde gerçekleştirilen tüm işler, kapsayıcının ayrıldığı tek çalışan düğümünde gerçekleştirilir. Daha fazla başvuru için [Apache Hadoop YARN kavramlarını](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) inceleyin.

Uygulama günlükleri (ve ilişkili kapsayıcı günlükleri), sorunlu Hadoop uygulamalarında hata ayıklama açısından kritik öneme sahiptir. YARN, uygulama günlüklerinin toplanması, toplanması ve [günlük toplama](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) özelliği ile depolanması için iyi bir çerçeve sağlar. Günlük toplama özelliği, uygulama günlüklerine daha belirleyici bir şekilde erişmenizi sağlar. Bir çalışan düğümündeki tüm kapsayıcılar üzerinde günlükleri toplar ve bunları çalışan düğümü başına bir toplu günlük dosyası olarak depolar. Bir uygulama bittikten sonra günlük varsayılan dosya sisteminde depolanır. Uygulamanız yüzlerce veya binlerce kapsayıcı kullanabilir, ancak tek bir çalışan düğümünde çalıştırılan tüm kapsayıcılar için Günlükler her zaman tek bir dosyaya toplanır. Bu nedenle, uygulamanız tarafından kullanılan her çalışan düğümü için yalnızca 1 günlük vardır. Günlük toplama, HDInsight kümeleri sürüm 3,0 ve üzerinde varsayılan olarak etkindir. Toplanan Günlükler, küme için varsayılan depolamada bulunur. Aşağıdaki yol, günlüklerin bir yolu olarak verilmiştir:

    /app-logs/<user>/logs/<applicationId>

Yolda, uygulamayı başlatan kullanıcının adı `user`. `applicationId`, YARN RM tarafından bir uygulamaya atanan benzersiz tanıtıcıdır.

Toplanan Günlükler, bir [tfile][T-file]dosyasında yazıldığı gibi doğrudan okunamaz, kapsayıcı tarafından dizine alınmış [ikili biçimdedir][binary-format] . Bu günlükleri uygulamalar veya ilgilendiğiniz kapsayıcılar için düz metin olarak görüntülemek üzere YARN ResourceManager günlüklerini veya CLı araçlarını kullanın.

## <a name="yarn-cli-tools"></a>YARN CLı araçları

YARN CLı araçlarını kullanmak için önce SSH kullanarak HDInsight kümesine bağlanmanız gerekir. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

Aşağıdaki komutlardan birini çalıştırarak, bu günlükleri düz metin olarak görüntüleyebilirsiniz:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Bu komutları çalıştırırken &lt;ApplicationId >, &lt;Kullanıcı tarafından başlatılan-uygulama >, &lt;Containerıd > ve &lt;çalışan düğümü-adresi > bilgilerini belirtin.

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager Kullanıcı arabirimi

YARN ResourceManager Kullanıcı arabirimi küme headnode üzerinde çalışır. Bu, ambarı Web Kullanıcı arabirimi üzerinden erişilir. YARN günlüklerini görüntülemek için aşağıdaki adımları kullanın:

1. Web tarayıcınızda https://CLUSTERNAME.azurehdinsight.net' a gidin. CLUSTERNAME değerini HDInsight kümenizin adıyla değiştirin.
2. Soldaki hizmetler listesinden **Yarn**' yi seçin.

    ![Apache ambarı Yarn hizmeti seçildi](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. **Hızlı bağlantılar** açılan listesinden küme baş düğümlerinden birini seçip **ResourceManager günlüğü**' nü seçin.

    ![Apache ambarı Yarn hızlı bağlantıları](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    YARN günlüklerine bağlantıların bir listesi sunulur.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
