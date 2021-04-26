---
title: HDInsight 'ta Hadoop ile ton Linux tabanlı kümeler-Azure
description: HDInsight kümelerine ton yüklemeyi öğrenin ve istekleri tona yönlendirmek için Tüneli kullanın. Depolamaya gözatıp Hive veya Pig çalıştırmak için ton kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 97a8dd2476642e693b589b4046f612c5569b9c0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865137"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop kümelerinde ton 'u yükleyip kullanma

HDInsight kümelerine ton yüklemeyi öğrenin ve istekleri tona yönlendirmek için Tüneli kullanın.

## <a name="what-is-hue"></a>Ton nedir?

Ton Apache Hadoop bir kümeyle etkileşim kurmak için kullanılan bir Web uygulamaları kümesidir. Bir Hadoop kümesiyle ilişkili depolamaya (HDInsight kümeleri söz konusu olduğunda, Hive işleri ve Pig betikleri) ve bu şekilde devam etmek için ton kullanabilirsiniz. Aşağıdaki bileşenler bir HDInsight Hadoop kümesindeki ton yüklemeleri ile kullanılabilir.

* Beeswax Hive Düzenleyicisi
* Apache Pig
* Meta veri deposu Yöneticisi
* Apache Oozie
* FileBrowser (varsayılan kapsayıcı ile iletişim kuran)
* İş tarayıcısı

> [!WARNING]  
> HDInsight kümesiyle sağlanan bileşenler tam olarak desteklenir ve Microsoft Desteği bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Özel bileşenler, sorunu gidermeye yardımcı olmak için ticari açıdan makul destek alır. Bu durum sorunu çözmeye veya bu teknolojinin derin uzmanlığı bulunan açık kaynaklı teknolojiler için kullanılabilir kanalları size sormaya neden olur. Örneğin, şu şekilde kullanılabilecek birçok topluluk sitesi vardır: [Microsoft Q&HDInsight için soru sayfası](/answers/topics/azure-hdinsight.html) [https://stackoverflow.com](https://stackoverflow.com) . Ayrıca Apache projelerinin üzerinde proje siteleri vardır [https://apache.org](https://apache.org) , örneğin: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Betik eylemlerini kullanarak ton yüklemesi

Betik eyleminiz için aşağıdaki tablodaki bilgileri kullanın. Betik eylemlerini kullanma hakkında belirli yönergeler için bkz. [HDInsight kümelerini betik eylemleriyle özelleştirme](hdinsight-hadoop-customize-cluster-linux.md) .

> [!NOTE]  
> HDInsight kümelerine ton yüklemek için önerilen baş düğüm boyutu en az A4 (8 çekirdek, 14 GB bellek) olur.

|Özellik |Değer |
|---|---|
|Betik türü:|-Özel|
|Name|Ton 'yi yükler|
|Bash betiği URI 'SI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Düğüm türleri:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>HDInsight kümeleriyle ton kullanma

Normal kümeler üzerinde ton ile yalnızca bir Kullanıcı hesabınız olabilir. Çoklu Kullanıcı erişimi için kümede [Kurumsal güvenlik paketi](./domain-joined/hdinsight-security-overview.md) etkinleştirin. SSH tüneli, bir kez çalışmaya başladıktan sonra, kümedeki tona erişmenin tek yoludur. SSH aracılığıyla tünel oluşturma, trafiğin ton 'un çalıştığı kümenin baş düğümüne doğrudan geçmesine izin verir. Kümenin hazırlanması tamamlandıktan sonra, bir HDInsight kümesinde ton kullanmak için aşağıdaki adımları kullanın.

> [!NOTE]  
> Aşağıdaki yönergeleri izlemek için Firefox Web tarayıcısının kullanılması önerilir.

1. [Apache ambarı Web UI, ResourceManager, JobHistory, süs Code, Oozie ve diğer Web Kullanıcı arabirimine erişmek IÇIN SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md) bölümündeki bilgileri kullanarak Istemci sisteminizden HDInsight KÜMESINE bir SSH tüneli oluşturun ve ardından Web tarayıcınızı bir proxy olarak SSH tüneli kullanacak şekilde yapılandırın.

1. Kümenize bağlanmak için [SSH komutunu](./hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Bağlandıktan sonra, birincil headnode 'un tam etki alanı adını almak için aşağıdaki komutu kullanın:

    ```bash
    hostname -f
    ```

    Bu, aşağıdakine benzer bir ad döndürür:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Bu, ton Web sitesinin bulunduğu birincil yayın düğümünün ana bilgisayar adıdır.

1. ' De ton Portalını açmak için tarayıcıyı kullanın `http://HOSTNAME:8888` . Ana BILGISAYAR adını önceki adımda edindiğiniz adla değiştirin.

   > [!NOTE]  
   > İlk kez oturum açtığınızda, ton portalında oturum açmak için bir hesap oluşturmanız istenir. Burada belirttiğiniz kimlik bilgileri portalla sınırlandırılır ve kümeyi sağlarken belirttiğiniz yönetici veya SSH kullanıcı kimlik bilgileriyle ilgili değildir.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png" alt-text="HDInsight ton portalı oturum açma penceresi":::

### <a name="run-a-hive-query"></a>Hive sorgusu çalıştırma

1. Ton portalından **Sorgu Düzenleyicileri**' ni seçin ve Hive düzenleyicisini açmak için **Hive** ' ı seçin.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png" alt-text="HDInsight ton portalı Hive düzenleyicisini kullanma":::

2. **Yardım** sekmesinde, **veritabanı** altında, **hivesampletable**' ı görmeniz gerekir. Bu, HDInsight üzerinde tüm Hadoop kümeleriyle birlikte gelen örnek bir tablodur. Sağ bölmede örnek bir sorgu girin ve ekran yakalama bölümünde gösterildiği gibi aşağıdaki bölmedeki **sonuçlar** sekmesinde yer alan çıktıyı görüntüleyin.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png" alt-text="HDInsight ton portalı Hive sorgusu":::

    Ayrıca, sonucun görsel gösterimini görmek için **grafik** sekmesini de kullanabilirsiniz.

### <a name="browse-the-cluster-storage"></a>Küme depolamaya gözatamıyorum

1. Ton portalından, menü çubuğunun sağ üst köşesindeki **dosya tarayıcısı** ' nı seçin.
2. Varsayılan olarak, dosya tarayıcısı **/User/MyUser** dizininde açılır. Yoldaki Kullanıcı dizininden, kümeyle ilişkili Azure depolama kapsayıcısının köküne gitmek için hemen eğik çizgi seçin.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png" alt-text="HDInsight ton portalı dosya tarayıcısı":::

3. Kullanılabilir işlemleri görmek için bir dosyaya veya klasöre sağ tıklayın. Dosyaları geçerli dizine yüklemek için sağ köşedeki **karşıya yükle** düğmesini kullanın. Yeni dosya veya dizin oluşturmak için **Yeni** düğmesini kullanın.

> [!NOTE]  
> Ton dosya tarayıcısı yalnızca HDInsight kümesiyle ilişkili varsayılan kapsayıcının içeriğini gösterebilir. Kümeyle ilişkili olabileceğiniz tüm ek depolama hesaplarına/kapsayıcılara dosya tarayıcısı kullanılarak erişilemeyecektir. Ancak, küme ile ilişkili ek kapsayıcılar her zaman Hive işleri için erişilebilir olacaktır. Örneğin, `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` Hive düzenleyicide komutunu girerseniz, ek kapsayıcıların içeriğini de görebilirsiniz. Bu komutta **newcontainer** bir kümeyle ilişkili varsayılan kapsayıcı değildir.

## <a name="important-considerations"></a>Önemli noktalar

1. Tonu yüklemek için kullanılan komut dosyası, yalnızca kümenin birincil yayın düğümüne yüklenir.

1. Yükleme sırasında, yapılandırmayı güncelleştirmek için birden çok Hadoop hizmeti (, YARN, MR2, Oozie) yeniden başlatılır. Komut dosyası ton yükleme işlemini tamamladıktan sonra, diğer Hadoop hizmetlerinin başlatılması biraz zaman alabilir. Bu, başlangıçta ton performansını etkileyebilir. Tüm hizmetler başlatıldıktan sonra ton tamamen işlevsel olur.

1. Ton, Hive için geçerli varsayılan olan Apache Tez işleri anlamıyor. MapReduce 'yi Hive yürütme altyapısı olarak kullanmak istiyorsanız, komut dosyanızı komut dosyasında aşağıdaki komutu kullanacak şekilde güncelleştirin:

   `set hive.execution.engine=mr;`

1. Linux kümeleriyle, Kaynak Yöneticisi ikincil üzerinde çalışırken hizmetlerinizin birincil baş düğümüne üzerinde çalıştığı bir senaryoya sahip olabilirsiniz. Bu tür bir senaryo, kümedeki çalışan işlerin ayrıntılarını görüntülemek için ton kullanılırken hatalara neden olabilirler (aşağıda gösterilen). Ancak, iş tamamlandığında iş ayrıntılarını görüntüleyebilirsiniz.

   :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png" alt-text="Ton portalı hatası örnek iletisi":::

   Bunun nedeni bilinen bir sorundur. Geçici bir çözüm olarak, Active Kaynak Yöneticisi birincil headnode üzerinde de çalışacak şekilde ambarı değiştirin.

1. Bir ton, HDInsight kümeleri kullanarak Azure Storage kullanırken Web 'e anlamıştır `wasbs://` . Bu nedenle, komut dosyası eylemiyle kullanılan özel betik, bir Weblerile uyumlu bir hizmet olan WEBLERB 'yi yükleyerek. Bu nedenle, ton portalı bir yerde (farenizi **Dosya tarayıcısının** üzerine getirdiğinizde olduğu gibi), o da, It b olarak yorumlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

[HDInsight kümelerine R 'Yi yükler](./r-server/r-server-overview.md). HDInsight Hadoop kümelerine R 'yi yüklemek için küme özelleştirmesi ' nı kullanın. R, istatistiksel bilgi işlem için açık kaynaklı bir dildir ve ortamıdır. İşlevsel ve nesne odaklı programlamanın yönlerini birleştiren yüzlerce yerleşik istatistiksel işlev ve kendi programlama dili sağlar. Ayrıca kapsamlı grafik özellikleri de sağlar.