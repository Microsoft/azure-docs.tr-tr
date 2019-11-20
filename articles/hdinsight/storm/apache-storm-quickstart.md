---
title: 'Hızlı başlangıç: Apache Storm topolojisi oluşturma/yönetme-Azure HDInsight'
description: Hızlı başlangıçta Azure HDInsight 'ta Apache Storm topolojisi oluşturma ve izleme hakkında bilgi edinin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241232"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Hızlı başlangıç: Azure HDInsight 'ta Apache Storm topolojisi oluşturma ve izleme

Apache Storm, veri akışlarını işlemeye yönelik ölçeklenebilir, hataya dayanıklı, dağıtılmış ve gerçek zamanlı bir işlem sistemidir. Azure HDInsight’ta Storm ile büyük veri analizini gerçek zamanlı olarak gerçekleştiren bulut tabanlı bir Storm kümesi oluşturabilirsiniz.

Bu hızlı başlangıçta, var olan bir Apache Storm kümesine Apache Storm topolojisi oluşturmak ve izlemek için Apache [fırtınası-başlangıç](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projesinden bir örnek kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Storm kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü**için **fırtınası** seçme.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Topolojiyi oluşturma

1. Fırtınası kümenize bağlanın. `CLUSTERNAME`, fırtınası kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. **WORDCOUNT** örneği `/usr/hdp/current/storm-client/contrib/storm-starter/`HDInsight kümenize eklenmiştir. Topoloji rastgele cümleler oluşturur ve sözcüklerin kaç kez meydana geçtiğini sayar. Kümede **WORDCOUNT** topolojisini başlatmak için aşağıdaki komutu kullanın:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Topolojiyi izleme

Fırtınası, çalışan topolojilerle çalışmaya yönelik bir Web arabirimi sağlar ve HDInsight kümenize dahildir.

Storm Kullanıcı Arabirimini kullanarak topolojiyi izlemek için aşağıdaki adımları kullanın:

1. Storm kullanıcı arabirimini görüntülemek için bir web tarayıcısı açarak `https://CLUSTERNAME.azurehdinsight.net/stormui` adresine gidin. `CLUSTERNAME` değerini kümenizin adıyla değiştirin.

2. **Topoloji Özeti**altında **ad** sütununda **WORDCOUNT** girişini seçin. Topoloji hakkında bilgiler görüntülenir.

    ![Storm-starter WordCount topoloji bilgilerini içeren Storm Panosu.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Yeni sayfa aşağıdaki bilgileri sağlar:

    |Özellik | Açıklama |
    |---|---|
    |Topoloji istatistikleri|Topoloji performansı hakkında zaman pencereleri halinde düzenlenmiş temel bilgiler. Belirli bir zaman penceresinin seçilmesi sayfanın diğer bölümlerinde gösterilen bilgiler için zaman penceresini değiştirir.|
    |Spout|Her Spout tarafından döndürülen son hata dahil olmak üzere spomalar hakkındaki temel bilgiler.|
    |Cıvatalar|Cıvatları hakkında temel bilgiler.|
    |Topoloji yapılandırması|Topoloji yapılandırması hakkında ayrıntılı bilgi.|
    |Etkinleştir|Devre dışı bırakılan bir topoloji işlemeyi sürdürür.|
    |Çıkarma|Çalışan bir topolojiyi duraklatır.|
    |Dengeleme|Topolojinin paralelliğini ayarlar. Kümedeki düğüm sayısını değiştirdikten sonra çalışan topolojileri yeniden dengelemeniz gerekir. Yeniden dengeleme, kümede artan/azalan düğüm sayısını dengelemek üzere paralelliği ayarlamaya imkan tanır. Daha fazla bilgi için bkz. [Apache Storm topolojisinin paralelliğini anlama](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Kapatmayın|Belirtilen zaman aşımından sonra bir fırtınası topolojisini sonlandırır.|

3. Bu sayfada **Spout’lar** veya **Cıvatalar** bölümünden bir giriş seçin. Seçilen bileşen hakkında bilgiler görüntülenir.

    ![Seçili bileşenler hakkında bilgi içeren Storm Panosu.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Yeni sayfa aşağıdaki bilgileri görüntüler:

    |Özellik | Açıklama |
    |---|---|
    |Spout/cıvata istatistikleri|Zaman pencereleri halinde düzenlenmiş bileşen performansı hakkındaki temel bilgiler. Belirli bir zaman penceresinin seçilmesi sayfanın diğer bölümlerinde gösterilen bilgiler için zaman penceresini değiştirir.|
    |Giriş İstatistikleri (yalnızca cıvatlar)|Cıvata tüketilen verileri üreten bileşenler hakkında bilgi.|
    |Çıkış istatistikleri|Bu cıvata yayılan verilerle ilgili bilgiler.|
    |Yürütücüler|Bu bileşenin örnekleri hakkında bilgi.|
    |Hatalar|Bu bileşen tarafından oluşturulan hatalar.|

4. Spout veya cıvata ayrıntılarını görüntülerken bileşenin belirli bir örneğine ilişkin ayrıntıları görmek için **Yürütücüler** bölümündeki **Bağlantı Noktası** sütunundan bir giriş seçin.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    Bu örnekte **seven** kelimesi 1493957 kez geçmiştir. Bu sayı, bu topoloji başlatıldığından beri kelimeyle kaç kez karşılaşıldığını gösterir.

## <a name="stop-the-topology"></a>Topolojiyi durdurma

Word-count topolojisi için **Topoloji özeti** sayfasına geri dönün ve ardından **Topoloji eylemleri** bölümünden **Sonlandır** düğmesini seçin. İstendiğinde, topolojiyi durdurmadan önce beklenecek saniye sayısı için 10 girin. Zaman aşımı süresinden sonra panonun **Storm Kullanıcı Arabirimi** bölümünü ziyaret ettiğinizde topoloji bir daha görünmez.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, var olan bir Apache Storm kümesine Apache Storm topolojisi oluşturmak ve izlemek için Apache [fırtınası-başlangıç](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projesinden bir örnek kullandınız. Apache Storm topolojileri yönetme ve izleme hakkında temel bilgileri öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
>[Azure HDInsight 'ta Apache Storm topolojileri dağıtma ve yönetme](./apache-storm-deploy-monitor-topology-linux.md)