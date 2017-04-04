---
title: "Azure HDInsight’ta Apache Storm ile çalışmaya başlama | Microsoft Docs"
description: "Linux tabanlı HDInsight’ta Apache Storm ve Storm Starter örneklerini kullanarak büyük veri analizini kullanmaya başlayın. Verileri gerçek zamanlı olarak işlemek için Storm’u nasıl kullanacağınızı öğrenin."
keywords: "apache storm,apache storm öğreticisi,büyük veri analizi,storm başlatıcısı"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 5d74f2c130eeddb1022acf9673c6a2006af2db58
ms.lasthandoff: 03/25/2017

---
#<a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-linux-based-hdinsight"></a>Linux tabanlı HDInsight'ta büyük veri analizi için Storm Starter örneklerini kullanmaya başlama

Apache Storm, veri akışlarını işlemeye yönelik ölçeklenebilir, hataya dayanıklı, dağıtılmış ve gerçek zamanlı bir işlem sistemidir. Azure HDInsight’ta Storm ile büyük veri analizini gerçek zamanlı olarak gerçekleştiren bulut tabanlı bir Storm kümesi oluşturabilirsiniz.

> [!IMPORTANT]
> Linux, HDInsight sürüm 3.4 ve üzerinde kullanılan tek işletim sistemidir. Daha fazla bilgi için bkz. [Windows'da HDInsight'ın Kullanım Dışı Bırakılması](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **SSH ve SCP hakkında bilgi**. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="access-control-requirements"></a>Erişim denetimi gereksinimleri

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Storm kümesi oluşturma

HDInsight kümesinde Storm oluşturmak için aşağıdaki adımları kullanın:

1. [Azure portalı](https://portal.azure.com)’ndan **+YENİ**, **Akıllı Özellikler ve Analiz** ve ardından **HDInsight**’ı seçin.
   
    ![HDInsight kümesi oluşturma](./media/hdinsight-apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. **Temel bilgiler** dikey penceresinde aşağıdaki bilgileri girin:

    * **Küme Adı**: HDInsight kümesinin adı.
    * **Abonelik**: Kullanılacak abonelik.
    * **Küme oturumu kullanıcı adı** ve **Küme oturumu parolası**: HTTPS üzerinden kümeye erişirken kullanılan oturum açma bilgileri. Ambari Web kullanıcı arabirimi veya REST API gibi hizmetlere erişmek için bu kimlik bilgilerini kullanın.
    * **Güvenli Kabuk (SSH) kullanıcı adı**: SSH üzerinden kümeye erişirken kullanılan oturum açma bilgileri. Varsayılan olarak parola, küme oturum açma parolası ile aynıdır.
    * **Kaynak Grubu**: Kümenin oluşturulduğu kaynak grubu.
    * **Konum**: Kümenin oluşturulacağı Azure bölgesi.
   
    ![Abonelik seçme](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. **Küme türü**’nü seçin, ardından **Küme yapılandırması** dikey penceresinde aşağıdaki değerleri ayarlayın:
   
    * **Küme Türü**: Storm

    * **İşletim Sistemi**: Linux

    * **Sürüm**: Storm 1.0.1 (HDI 3.5)

    * **Küme Katmanı**: Standart
     
    Son olarak, **Seç** düğmesini kullanarak ayarları kaydedin.
     
    ![Küme türü seçme](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. Küme türünü seçtikten sonra __Seç__ düğmesini kullanarak küme türünü ayarlayın. Ardından, __İleri__ düğmesini kullanarak temel yapılandırmayı tamamlayın.

5. **Depolama** dikey penceresinden bir depolama hesabı seçin veya oluşturun. Bu belgedeki adımlar için bu dikey penceredeki diğer alanları varsayılan değerlerinde bırakın. __İleri__ düğmesini kullanarak depolama yapılandırmasını kaydedin.

    ![HDInsight depolama hesabı ayarlarını belirleme](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. **Özet** dikey penceresinden kümenin yapılandırmasını gözden geçirin. Yanlış olan ayarları değiştirmek için __Düzenle__ bağlantılarını kullanın. Son olarak, __Oluştur__ düğmesini kullanarak kümeyi oluşturun.
   
    ![Küme yapılandırma özeti](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Kümenin oluşturulması 20 dakika sürebilir.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>HDInsight’ta bir Storm Starter örneği çalıştırma

1. SSH kullanarak HDInsight kümesine bağlanma:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    SSH kullanıcı hesabınızın güvenliğini sağlamak için parola kullandıysanız parolayı girmeniz istenir. Bir ortak anahtar kullandıysanız eşleşen özel anahtarı belirtmek için `-i` parametresini kullanmanız gerekebilir. Örneğin, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Örnek bir topoloji başlatmak için aşağıdaki komutu kullanın:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > HDInsight’ın önceki sürümlerinde, topolojinin sınıf adı `storm.starter.WordCountTopology` yerine `org.apache.storm.starter.WordCountTopology` şeklindedir.
   
    Bu komut kümede "wordcount" kolay adı ile örnek WordCount topolojisini başlatır. Rastgele cümleler oluşturur ve cümlelerde her bir sözcüğün kaç kez geçtiğini sayar.
   
    > [!NOTE]
    > Kümeye kendi topolojilerinizi gönderirken `storm` komutunu kullanmadan önce kümeyi içeren jar dosyasını kopyalamanız gerekir. Dosyayı kopyalamak için `scp` komutunu kullanın. Örneğin, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > WordCount örneği ve diğer storm starter örnekleri `/usr/hdp/current/storm-client/contrib/storm-starter/` konumunda kümenize zaten dahil edilmiştir.

Storm başlangıç örneklerinin kaynağını görüntülemek istiyorsanız kaynak kodu [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter) adresinde bulabilirsiniz. Bu bağlantı, HDInsight 3.5 ile birlikte sağlanan Storm 1.0.x içindir. Diğer Storm sürümleri için sayfanın üstündeki __Dal__ düğmesini kullanarak farklı bir Storm sürümü seçin.

## <a name="monitor-the-topology"></a>Topolojiyi izleme

Storm Kullanıcı Arabirimi çalışan topolojilerle çalışmaya yönelik bir web arabirimi sağlar ve HDInsight kümenize dahil edilir.

Storm Kullanıcı Arabirimini kullanarak topolojiyi izlemek için aşağıdaki adımları kullanın:

1. Storm Kullanıcı Arabirimini görüntülemek için bir web tarayıcı açarak https://CLUSTERNAME.azurehdinsight.net/stormui adresine gidin. **CLUSTERNAME** değerini kümenizin adıyla değiştirin.
    
    > [!NOTE]
    > Bir kullanıcı adı ve parola girmeniz istenirse kümeyi oluştururken kullandığınız küme yöneticisi (yönetici) ve parolayı girin.

2. **Topoloji özeti** altında **Ad** sütunundaki **wordcount** girişini seçin. Topoloji hakkında bilgiler görüntülenir.
    
    ![Storm Starter WordCount topoloji bilgilerini içeren Storm Panosu.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    Bu sayfa aşağıdaki bilgileri sağlar:
    
    * **Topoloji istatistikleri** - Topoloji performansı hakkında zaman pencereleri halinde düzenlenmiş temel bilgiler.
     
        > [!NOTE]
        > Belirli bir zaman penceresinin seçilmesi sayfanın diğer bölümlerinde gösterilen bilgiler için zaman penceresini değiştirir.

    * **Spout’lar** - Her bir spout’un döndürdüğü son hata dahil olmak üzere spout’lar hakkında temel bilgi.
    
    * **Cıvatalar** - Cıvatalar hakkında temel bilgiler.
    
    * **Topoloji yapılandırması** - Topoloji yapılandırması hakkında ayrıntılı bilgi.
     
    Bu sayfa ayrıca topoloji üzerinde gerçekleştirilebilen eylemleri sağlar:
   
    * **Etkinleştir** - Devre dışı bırakılan bir topolojiyi işlemeyi sürdürür.
    
    * **Devre dışı bırak** - Çalışan topolojiyi duraklatır.
    
    * **Yeniden dengele** - Topolojinin paralelliğini ayarlar. Kümedeki düğüm sayısını değiştirdikten sonra çalışan topolojileri yeniden dengelemeniz gerekir. Yeniden dengeleme, kümede artan/azalan düğüm sayısını dengelemek üzere paralelliği ayarlamaya imkan tanır. Daha fazla bilgi için bkz. [Bir Storm topolojisinin paralelliğini anlama](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
    
    * **Sonlandır** - Belirtilen zaman aşımından sonra Storm topolojisini sonlandırır.

3. Bu sayfada **Spout’lar** veya **Cıvatalar** bölümünden bir giriş seçin. Seçilen bileşen hakkında bilgiler görüntülenir.
   
    ![Seçili bileşenler hakkında bilgi içeren Storm Panosu.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    Bu sayfa aşağıdaki bilgileri gösterir:
   
    * **Spout/Cıvata istatistikleri** - Bileşen performansı hakkında zaman pencereleri halinde düzenlenmiş temel bilgiler.
     
        > [!NOTE]
        > Belirli bir zaman penceresinin seçilmesi sayfanın diğer bölümlerinde gösterilen bilgiler için zaman penceresini değiştirir.
     
    * **Girdi istatistikleri** (yalnızca cıvata) - Cıvata tarafından kullanılan verileri üreten bileşenler hakkında bilgi.
    
    * **Çıktı istatistikleri** - Bu cıvata tarafından yayılan veriler hakkında bilgi.
    
    * **Yürütücüler** - Bu bileşenin örnekleri hakkında bilgi.
    
    * **Hatalar** - Bu bileşenden kaynaklanan hatalar.

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

## <a name="delete-the-cluster"></a>Küme silme

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a id="next"></a>Sonraki adımlar

Bu Apache Storm öğreticisinde HDInsight üzerinde Storm ile çalışma hakkındaki temel bilgileri edindiniz. Ardından, [Maven kullanarak Java tabanlı topolojiler geliştirme](hdinsight-storm-develop-java-topology.md) hakkında bilgi edindiniz.

Java tabanlı topolojiler geliştirme hakkında zaten bilgi sahibiyseniz ve mevcut bir topolojiyi HDInsight’a dağıtmak istiyorsanız bkz. [HDInsight’ta Apache Storm topolojilerini dağıtma ve yönetme](hdinsight-storm-deploy-monitor-topology-linux.md).

.NET geliştiricisiyseniz Visual Studio'yu kullanarak C# veya karma C#/Java topolojileri oluşturabilirsiniz. Daha fazla bilgi edinmek için bkz. [Visual Studio için Hadoop araçlarını kullanarak HDInsight'ta Apache Storm için C# topolojileri geliştirme](hdinsight-storm-develop-csharp-visual-studio-topology.md).

HDInsight üzerinde Storm ile kullanılabilecek örnek topolojiler için şu örneklere bakın:

* [HDInsight üzerinde Storm için örnek topolojiler](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

