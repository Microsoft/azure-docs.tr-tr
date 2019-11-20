---
title: HDInsight 'ta Apache Hadoop Hizmetleri için yığın dökümlerini etkinleştirme-Azure
description: Hata ayıklama ve analiz için Linux tabanlı HDInsight kümelerinden Apache Hadoop Hizmetleri için yığın dökümlerini etkinleştirin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 90de0b4bfad4c5096ebc38eb3d31fc41bca6649b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494846"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Linux tabanlı HDInsight 'ta Apache Hadoop Hizmetleri için yığın dökümlerini etkinleştirme

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Yığın dökümleri, döküm oluşturulduğu sırada değişkenlerin değerleri de dahil olmak üzere, uygulamanın belleğinin anlık görüntüsünü içerir. Bu nedenle, çalışma zamanında oluşan sorunları tanılamak için faydalıdır.

## <a name="whichServices"></a>Servislere

Aşağıdaki hizmetler için yığın dökümlerini etkinleştirebilirsiniz:

* **Apache hcatalog** -tempelton
* **Apache Hive** -hiveserver2, meta veri deposu, derbyserver
* **MapReduce** -jobgeçmişini sunucusu
* **Apache Yarn** -ResourceManager, NodeManager, timelineserver
* **Apache** , secondarynamenode, süs Code

Ayrıca eşleme için yığın dökümlerini etkinleştirebilir ve HDInsight tarafından çalıştırılan işlemlerin azalmasını sağlayabilirsiniz.

## <a name="configuration"></a>Yığın dökümü yapılandırmasını anlama

Yığın dökümleri, bir hizmet başlatıldığında JVM 'ye seçenekleri (bazen opts veya parametreler olarak bilinir) geçirerek etkinleştirilir. Çoğu [Apache Hadoop](https://hadoop.apache.org/) hizmet için, bu seçenekleri geçirmek üzere hizmeti başlatmak için kullanılan kabuk betiğini değiştirebilirsiniz.

Her betikte, JVM 'ye geçirilen seçenekleri içeren **\*\_OPTS**için bir dışarı aktarma vardır. Örneğin, **Hadoop-env.sh** betiğinde, `export HADOOP_NAMENODE_OPTS=` ile başlayan çizgi, süs Code hizmeti seçeneklerini içerir.

Bu işlemler MapReduce hizmetinin alt işlemi olduğundan, eşleme ve azaltma işlemleri biraz farklıdır. Her eşleme veya azaltma işlemi bir alt kapsayıcıda çalışır ve JVM seçeneklerini içeren iki giriş vardır. Hem **mapred-site. xml**içinde bulunur:

* **MapReduce. admin. Map. child. Java. opts**
* **MapReduce. admin. küçültme. child. Java. opts**

> [!NOTE]  
> Ambarı işlerken, değişiklikleri kümedeki düğümlerde çoğaltarak, hem betikler hem de mapred-site. xml ayarlarını değiştirmek için [Apache ambarı](https://ambari.apache.org/) kullanmanızı öneririz. Belirli adımlar için [Apache ambarı 'Nı kullanma](#using-apache-ambari) bölümüne bakın.

### <a name="enable-heap-dumps"></a>Yığın dökümlerini etkinleştirme

Aşağıdaki seçenek bir OutOfMemoryError gerçekleştiğinde yığın dökümünü sunar:

    -XX:+HeapDumpOnOutOfMemoryError

**+** , bu seçeneğin etkinleştirildiğini gösterir. Varsayılan olarak devre dışı seçeneği kullanılır.

> [!WARNING]  
> Döküm dosyaları büyük olduğu için varsayılan olarak HDInsight 'ta Hadoop Hizmetleri için yığın dökümleri etkinleştirilmemiştir. Sorun gidermeye izin vermek istiyorsanız, sorunu yeniden oluşturduktan ve döküm dosyalarını topladıktan sonra bunları devre dışı bırakmayı unutmayın.

### <a name="dump-location"></a>Döküm konumu

Döküm dosyasının varsayılan konumu geçerli çalışma dizinidir. Dosyanın nerede depolandığını aşağıdaki seçeneği kullanarak denetleyebilirsiniz:

    -XX:HeapDumpPath=/path

Örneğin, `-XX:HeapDumpPath=/tmp` kullanmak dökümlerinin/tmp dizininde depolanmasına neden olur.

### <a name="scripts"></a>Betikler

Bir **OutOfMemoryError** gerçekleştiğinde de bir komut dosyası tetikleyebilirsiniz. Örneğin, hatanın oluştuğunu bilmeniz için bir bildirim tetikleniyor. Bir __OutOfMemoryError__üzerinde bir betik tetiklemek için aşağıdaki seçeneği kullanın:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Apache Hadoop Dağıtılmış bir sistem olduğundan, kullanılan tüm betiklerin, kümenin üzerinde çalıştığı kümedeki tüm düğümlere yerleştirilmesi gerekir.
> 
> Betik Ayrıca hizmetin çalıştığı hesap tarafından erişilebilen bir konumda olmalıdır ve yürütme izinleri sağlamalıdır. Örneğin, `/usr/local/bin` betikleri depolamak ve okuma ve yürütme izinleri vermek için `chmod go+rx /usr/local/bin/filename.sh` kullanmak isteyebilirsiniz.

## <a name="using-apache-ambari"></a>Apache ambarı 'nı kullanma

Bir hizmetin yapılandırmasını değiştirmek için aşağıdaki adımları kullanın:

1. Kümeniz için ambarı Web Kullanıcı arabirimini açın. URL https://YOURCLUSTERNAME.azurehdinsight.net.

    İstendiğinde, kümenizin HTTP hesabı adını (varsayılan: yönetici) ve parolayı kullanarak kimlik doğrulaması yapın.

   > [!NOTE]  
   > Kullanıcı adı ve parola için ambarı tarafından ikinci bir kez sorulabilir. Bu durumda, aynı hesap adını ve parolayı girin.

2. Sol taraftaki listesini kullanarak, değiştirmek istediğiniz hizmet alanını seçin. Örneğin **, bir**. Orta alanda, **configs** sekmesini seçin.

    ![Şu kadar bir Web ambarı](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. **Filter...** girişini kullanarak **OptIn**'yi girin. Yalnızca bu metni içeren öğeler görüntülenir.

    ![Apache ambarı yapılandırması filtrelenmiş listesi](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Yığın dökümlerini etkinleştirmek istediğiniz hizmet için **\*\_OPTS** girişini bulun ve etkinleştirmek istediğiniz seçenekleri ekleyin. Aşağıdaki görüntüde, **HADOOP\_süs yot\_OPTS** girdisine `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` ekledik:

    ![Apache ambarı Hadoop-süs Yot-opts](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Eşleme için yığın dökümlerini etkinleştirirken veya alt işlemi azalttığında **MapReduce. admin. Map. child. Java. opts** ve **MapReduce. admin. küçültme. child. Java. opts**adlı alanları arayın.

    Değişiklikleri kaydetmek için **Kaydet** düğmesini kullanın. Değişiklikleri açıklayan kısa bir nota girebilirsiniz.

5. Değişiklikler uygulandıktan sonra, bir veya daha fazla hizmetin yanında **yeniden başlatma gerekiyor** simgesi görüntülenir.

    ![yeniden başlatma gerekli simgesi ve yeniden Başlat düğmesi](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Yeniden başlatma gerektiren her hizmeti seçin ve **bakım modunu açmak**Için **hizmet eylemleri** düğmesini kullanın. Bakım modu, yeniden başlattığınızda uyarıların hizmetten oluşturulmasını engeller.

    ![HDI bakım modu menüsünü aç](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Bakım modunu etkinleştirdikten sonra, hizmetin **tüm efektleri yeniden başlatması** Için **yeniden başlatma** düğmesini kullanın

    ![Apache ambarı etkilenen tüm girişleri yeniden Başlat](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > **Yeniden başlatma** düğmesi girişleri, diğer hizmetler için farklı olabilir.

8. Hizmetler yeniden başlatıldıktan sonra **bakım modunu**kapatmak Için **hizmet eylemleri** düğmesini kullanın. Bu ambarı, hizmet uyarılarını izlemeyi sürdürür.

