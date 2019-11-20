---
title: HDInsight 'ta Hadoop ile ton Linux tabanlı kümeler-Azure
description: HDInsight kümelerine ton yüklemeyi öğrenin ve istekleri tona yönlendirmek için Tüneli kullanın. Depolamaya gözatıp Hive veya Pig çalıştırmak için ton kullanın.
keywords: ton Hadoop
author: hrasheed-msft
ms.date: 12/11/2017
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.openlocfilehash: 8795d28cb36bacf4f57dba2048e30219047a9ac5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098644"
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
* İş Tarayıcısı

> [!WARNING]  
> HDInsight kümesiyle sağlanan bileşenler tam olarak desteklenir ve Microsoft Desteği bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Özel bileşenler, sorunu gidermeye yardımcı olmak için ticari açıdan makul destek alır. Bu durum sorunu çözmeye veya bu teknolojinin derin uzmanlığı bulunan açık kaynaklı teknolojiler için kullanılabilir kanalları size sormaya neden olur. Örneğin, şu şekilde kullanılabilecek birçok topluluk sitesi vardır: [HDInsight Için MSDN Forumu](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Ayrıca Apache projelerinin üzerinde [https://apache.org](https://apache.org)proje siteleri vardır, örneğin: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Betik eylemlerini kullanarak ton yüklemesi

Linux tabanlı HDInsight kümesine ton yüklemeye yönelik betik adresinde https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh bulunabilir. Bu betiği, Azure depolama Blobları (ıLB) veya varsayılan depolama alanı olarak Azure Data Lake Storage kümelere ton yüklemek için kullanabilirsiniz.

Bu bölümde, Azure portal kullanılarak küme sağlanırken betiğin nasıl kullanılacağına ilişkin yönergeler sağlanmaktadır.

> [!NOTE]  
> Azure PowerShell, Azure klasik CLı, HDInsight .NET SDK veya Azure Resource Manager şablonları, betik eylemlerini uygulamak için de kullanılabilir. Zaten çalışan kümelere betik eylemleri de uygulayabilirsiniz. Daha fazla bilgi için bkz. [HDInsight kümelerini betik eylemleriyle özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

1. [Linux 'Ta HDInsight kümeleri sağlama](hdinsight-hadoop-provision-linux-clusters.md)bölümündeki adımları kullanarak bir kümeyi sağlamaya başlayın, ancak sağlamayı tamamlamayın.

   > [!NOTE]  
   > HDInsight kümelerine ton yüklemek için önerilen baş düğüm boyutu en az A4 (8 çekirdek, 14 GB bellek) olur.

1. **Isteğe bağlı yapılandırma** dikey penceresinde **betik eylemleri**' ni seçin ve bilgileri aşağıda gösterildiği gibi sağlayın:

    ![Ton için betik eylem parametreleri sağlama](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Ton için betik eylem parametreleri sağlama")

   * **AD**: Betik eylemi için kolay bir ad girin.
   * **BETİK URI 'Sİ**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **BAŞ**: Bu seçeneği işaretleyin.
   * **ÇALIŞAN**: Burayı boş bırakın.
   * **ZOOKEEPER**: Burayı boş bırakın.
   * **PARAMETRELER**: Burayı boş bırakın.

1. **Betik eylemlerinin**en altında, yapılandırmayı kaydetmek için **Seç** düğmesini kullanın. Son olarak, isteğe bağlı yapılandırma bilgilerini kaydetmek için **Isteğe bağlı yapılandırma** dikey penceresinin altındaki **Seç** düğmesini kullanın.

1. [Linux 'Ta HDInsight kümeleri sağlama](hdinsight-hadoop-provision-linux-clusters.md)bölümünde açıklandığı gibi kümeyi sağlamaya devam edin.

## <a name="use-hue-with-hdinsight-clusters"></a>HDInsight kümeleriyle ton kullanma

SSH tüneli, bir kez çalışmaya başladıktan sonra, kümedeki tona erişmenin tek yoludur. SSH aracılığıyla tünel oluşturma, trafiğin ton 'un çalıştığı kümenin baş düğümüne doğrudan geçmesine izin verir. Kümenin hazırlanması tamamlandıktan sonra, bir HDInsight Linux kümesinde ton kullanmak için aşağıdaki adımları kullanın.

> [!NOTE]  
> Aşağıdaki yönergeleri izlemek için Firefox Web tarayıcısının kullanılması önerilir.

1. [Apache ambarı Web UI, ResourceManager, JobHistory, süs Code, Oozie ve diğer Web Kullanıcı arabirimine erişmek IÇIN SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md) bölümündeki bilgileri kullanarak Istemci sisteminizden HDInsight KÜMESINE bir SSH tüneli oluşturun ve ardından Web tarayıcınızı kullanarak Ara sunucu olarak SSH tüneli.

2. Bir SSH tüneli oluşturup tarayıcınızı bir üzerinden proxy trafiğine yapılandırdıktan sonra, birincil baş düğümün ana bilgisayar adını bulmanız gerekir. Bunu, bağlantı noktası 22 ' de SSH kullanarak kümeye bağlanarak yapabilirsiniz. Örneğin, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` **Kullanıcı** adı SSH kullanıcı adınız ve **clustername** kümenizin adıdır.

    Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Bağlandıktan sonra, birincil headnode 'un tam etki alanı adını almak için aşağıdaki komutu kullanın:

        hostname -f

    Bu, aşağıdakine benzer bir ad döndürür:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Bu, ton Web sitesinin bulunduğu birincil yayın düğümünün ana bilgisayar adıdır.

4. {\/1 & gt; 8888 & lt; 1} konumundaki ton Portalını açmak için tarayıcıyı kullanın. Ana BILGISAYAR adını önceki adımda edindiğiniz adla değiştirin.

   > [!NOTE]  
   > İlk kez oturum açtığınızda, ton portalında oturum açmak için bir hesap oluşturmanız istenir. Burada belirttiğiniz kimlik bilgileri portalla sınırlandırılır ve kümeyi sağlarken belirttiğiniz yönetici veya SSH kullanıcı kimlik bilgileriyle ilgili değildir.

    ![HDInsight ton portalı oturum açma penceresi](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Ton portalı için kimlik bilgilerini belirtin")

### <a name="run-a-hive-query"></a>Hive sorgusu çalıştırma

1. Ton portalından **Sorgu Düzenleyicileri**' ne ve ardından **Hive ' ye tıklayarak Hive düzenleyicisini** açın.

    ![HDInsight ton portalı Hive düzenleyicisini kullanma](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Hive kullanma")
2. **Yardım** sekmesinde, **veritabanı**altında, **hivesampletable**' ı görmeniz gerekir. Bu, HDInsight üzerinde tüm Hadoop kümeleriyle birlikte gelen örnek bir tablodur. Sağ bölmede örnek bir sorgu girin ve ekran yakalama bölümünde gösterildiği gibi aşağıdaki bölmedeki **sonuçlar** sekmesinde yer alan çıktıyı görüntüleyin.

    ![HDInsight ton portalı Hive sorgusu](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Hive sorgusu Çalıştır")

    Ayrıca, sonucun görsel gösterimini görmek için **grafik** sekmesini de kullanabilirsiniz.

### <a name="browse-the-cluster-storage"></a>Küme depolamaya gözatamıyorum

1. Ton portalından, menü çubuğunun sağ üst köşesindeki **dosya tarayıcısı** ' na tıklayın.
2. Varsayılan olarak, dosya tarayıcısı **/User/MyUser** dizininde açılır. Yoldaki Kullanıcı dizininden, kümeyle ilişkili Azure depolama kapsayıcısının köküne gitmek için yukarıdaki eğik çizgiye sağ tıklayın.

    ![HDInsight ton portalı dosya tarayıcısı](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Dosya tarayıcısını kullan")

3. Kullanılabilir işlemleri görmek için bir dosyaya veya klasöre sağ tıklayın. Dosyaları geçerli dizine yüklemek için sağ köşedeki **karşıya yükle** düğmesini kullanın. Yeni dosya veya dizin oluşturmak için **Yeni** düğmesini kullanın.

> [!NOTE]  
> Ton dosya tarayıcısı yalnızca HDInsight kümesiyle ilişkili varsayılan kapsayıcının içeriğini gösterebilir. Kümeyle ilişkili olabileceğiniz tüm ek depolama hesaplarına/kapsayıcılara dosya tarayıcısı kullanılarak erişilemeyecektir. Ancak, küme ile ilişkili ek kapsayıcılar her zaman Hive işleri için erişilebilir olacaktır. Örneğin, Hive düzenleyicide komutunu `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` girerseniz, ek kapsayıcıların içeriğini de görebilirsiniz. Bu komutta **newcontainer** bir kümeyle ilişkili varsayılan kapsayıcı değildir.

## <a name="important-considerations"></a>Önemli konular

1. Tonu yüklemek için kullanılan komut dosyası, yalnızca kümenin birincil yayın düğümüne yüklenir.

2. Yükleme sırasında, yapılandırmayı güncelleştirmek için birden çok Hadoop hizmeti (, YARN, MR2, Oozie) yeniden başlatılır. Komut dosyası ton yükleme işlemini tamamladıktan sonra, diğer Hadoop hizmetlerinin başlatılması biraz zaman alabilir. Bu, başlangıçta ton performansını etkileyebilir. Tüm hizmetler başlatıldıktan sonra ton tamamen işlevsel olur.
3. Ton, Hive için geçerli varsayılan olan Apache Tez işleri anlamaz. MapReduce 'yi Hive yürütme altyapısı olarak kullanmak istiyorsanız, komut dosyanızı komut dosyasında aşağıdaki komutu kullanacak şekilde güncelleştirin:

        set hive.execution.engine=mr;

4. Linux kümeleriyle, Kaynak Yöneticisi ikincil üzerinde çalışırken hizmetlerinizin birincil baş düğümüne üzerinde çalıştığı bir senaryoya sahip olabilirsiniz. Bu tür bir senaryo, kümedeki çalışan işlerin ayrıntılarını görüntülemek için ton kullanılırken hatalara neden olabilirler (aşağıda gösterilen). Ancak, iş tamamlandığında iş ayrıntılarını görüntüleyebilirsiniz.

   ![Ton portalı hatası örnek iletisi](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Ton portalı hatası")

   Bunun nedeni bilinen bir sorundur. Geçici bir çözüm olarak, Active Kaynak Yöneticisi birincil headnode üzerinde de çalışacak şekilde ambarı değiştirin.
5. Bir ton, HDInsight kümeleri kullanarak `wasb://`Azure Storage kullanırken Web 'e anlamıştır. Bu nedenle, komut dosyası eylemiyle kullanılan özel betik, bir Weblerile uyumlu bir hizmet olan WEBLERB 'yi yükleyerek. Bu nedenle, ton portalı bir yerde (farenizi **Dosya tarayıcısının**üzerine getirdiğinizde olduğu gibi), o da, It b olarak yorumlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerine Apache Giraph 'Yi yükler](hdinsight-hadoop-giraph-install-linux.md). HDInsight Hadoop kümelerine Giraph 'yi yüklemek için küme özelleştirmesi ' nı kullanın. Giraph, Hadoop kullanarak grafik işleme gerçekleştirmenize olanak tanır ve Azure HDInsight ile birlikte kullanılabilir.
* [HDInsight kümelerine R 'Yi yükler](hdinsight-hadoop-r-scripts-linux.md). HDInsight Hadoop kümelerine R 'yi yüklemek için küme özelleştirmesi ' nı kullanın. R, istatistiksel bilgi işlem için açık kaynaklı bir dildir ve ortamıdır. İşlevsel ve nesne odaklı programlamanın yönlerini birleştiren yüzlerce yerleşik istatistiksel işlev ve kendi programlama dili sağlar. Ayrıca kapsamlı grafik özellikleri de sağlar.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
