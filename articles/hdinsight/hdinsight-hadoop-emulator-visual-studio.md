---
title: Visual Studio için Data Lake araçları & Hortonçalışmalar-Azure HDInsight
description: Yerel bir VM 'de çalışan Hortonrunning korumalı alanı ile Visual Studio için Azure Data Lake araçları 'nı nasıl kullanacağınızı öğrenin. Bu araçlarla, Hive ve Pig işlerini sanal alanda oluşturup çalıştırabilir ve iş çıktısını ve geçmişini görüntüleyebilirsiniz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500161"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Hortonçalışmalar korumalı alanı ile Visual Studio için Azure Data Lake araçları 'nı kullanma

Azure Data Lake, genel Apache Hadoop kümeleriyle çalışmaya yönelik araçlar içerir. Bu belge, yerel bir sanal makinede çalışan Hortonrunning korumalı alanı ile Data Lake araçlarını kullanmak için gereken adımları sağlar.

Hortonçalışmalar korumalı alanının kullanılması, geliştirme ortamınızda yerel olarak Hadoop ile çalışmanıza olanak sağlar. Bir çözüm geliştirdikten ve ölçeği ölçekli olarak dağıtmak istediğinizde, bir HDInsight kümesine geçebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Geliştirme ortamınızdaki bir sanal makinede çalışan Hortonrunning korumalı alanı. Bu belge, Oracle VirtualBox 'da çalışan korumalı alanda yazılmıştır ve test edilmiştir. Korumalı alanı ayarlama hakkında daha fazla bilgi için bkz [. Hortonçalışmalarla çalışmaya başlama alanı.](hadoop/apache-hadoop-emulator-get-started.md) belgedeki.

* Visual Studio.

* .NET 2.7.1 veya üzeri [Için Azure SDK](https://azure.microsoft.com/downloads/) .

* [Visual Studio için Azure Data Lake araçları](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Korumalı alan için parolaları yapılandırma

Hortonçalışmalar korumalı alanının çalıştığından emin olun. Ardından, [Horton, çalışma alanı belgesinde kullanmaya başlama](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) bölümündeki adımları izleyin. Bu adımlar SSH `root` hesabının parolasını ve Apache ambarı `admin` hesabını yapılandırır. Bu parolalar, Visual Studio 'dan korumalı alana bağlandığınızda kullanılır.

## <a name="connect-the-tools-to-the-sandbox"></a>Araçları korumalı alana bağlama

1. Visual Studio 'yu açın, **Görünüm**' ü seçin ve ardından **Sunucu Gezgini**' yi seçin.

2. **Sunucu Gezgini**, **HDInsight** girdisini sağ tıklatın ve ardından **HDInsight Emulator 'a Bağlan**' ı seçin.

    ![Sunucu Gezgini, HDInsight Emulator 'a bağlanma vurgulandı](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. **HDInsight öykünücüsüne Bağlan** iletişim kutusuna, ambarı için yapılandırdığınız parolayı girin.

    ![Ambarı parolası metin kutusu vurgulanmış iletişim kutusunun ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Devam etmek için **İleri**’yi seçin.

4. `root` hesabı için yapılandırdığınız parolayı girmek için **parola** alanını kullanın. Diğer alanları varsayılan değerde bırakın.

    ![Kök parola metin kutusu vurgulanmış iletişim kutusunun ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Devam etmek için **İleri**’yi seçin.

5. Hizmetlerin doğrulanmasının bitmesini bekleyin. Bazı durumlarda, doğrulama başarısız olabilir ve yapılandırmayı güncelleştirmenizi isteyebilir. Doğrulama başarısız olursa **Güncelleştir**' i seçin ve hizmetin bitmesini ve doğrulanması için bekleyin.

    ![Güncelleştirme düğmesi vurgulanmış şekilde iletişim kutusunun ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Güncelleştirme işlemi, Hortonçalışmalar korumalı alanı yapılandırmasını Visual Studio için Data Lake araçları tarafından beklenildiği şekilde değiştirmek için ambarı kullanır.

6. Doğrulama bittikten sonra, yapılandırmayı tamamladıktan sonra **son** ' u seçin.
    Son düğme vurgulanmış şekilde iletişim kutusunun ![ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > Geliştirme ortamınızın hızına ve sanal makineye ayrılan bellek miktarına bağlı olarak, hizmetleri yapılandırmak ve doğrulamak birkaç dakika sürebilir.

Bu adımları tamamladıktan sonra, **HDInsight** bölümü altında Sunucu Gezgini bir **HDInsight yerel küme** girişi vardır.

## <a name="write-an-apache-hive-query"></a>Apache Hive bir sorgu yazın

Hive yapılandırılmış verilerle çalışmak için SQL benzeri bir sorgu dili (HiveQL) sağlar. İsteğe bağlı sorguları yerel kümede çalıştırmayı öğrenmek için aşağıdaki adımları kullanın.

1. **Sunucu Gezgini**' de, daha önce eklediğiniz yerel kümenin girişine sağ tıklayın ve ardından **Hive sorgusu yaz**' ı seçin.

    ![Hive sorgusu yazma vurgulanmış Sunucu Gezgini ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Yeni bir sorgu penceresi görüntülenir. Burada, yerel kümeye hızlı bir şekilde bir sorgu yazabilir ve gönderebilirsiniz.

2. Yeni sorgu penceresinde, aşağıdaki komutu girin:

        select count(*) from sample_08;

    Sorguyu çalıştırmak için pencerenin üst kısmında **Gönder** ' i seçin. Varsayılan değerlerde diğer değerleri (**Batch** ve sunucu adı) bırakın.

    ![Gönder düğmesi vurgulanmış şekilde sorgu penceresinin ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    **Gelişmiş**' i seçmek için **Gönder** ' in yanındaki açılan menüyü de kullanabilirsiniz. Gelişmiş Seçenekler işi gönderdiğinizde ek seçenekler sağlamanıza olanak tanır.

    ![Betik Gönder iletişim kutusu Hive ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Sorguyu gönderdikten sonra, iş durumu görüntülenir. İş durumu, Hadoop tarafından işlendiği gibi iş hakkındaki bilgileri görüntüler. **Iş durumu** işin durumunu sağlar. Durum düzenli olarak güncelleştirilir veya yenileme simgesini kullanarak durumu el ile yenileyebilirsiniz.

    ![İş durumu vurgulanmış şekilde Iş görünümü iletişim kutusunun ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    **Iş durumu** **tamamlandı**olarak değiştirildikten sonra, yönlendirilmiş bir ÇEVRIMSIZ Graf (DAG) görüntülenir. Bu diyagram, Hive sorgusu işlenirken tez tarafından belirlenen yürütme yolunu açıklar. Tez, yerel kümedeki Hive için varsayılan yürütme altyapısıdır.

    > [!NOTE]  
    > Linux tabanlı HDInsight kümeleri kullanırken Apache Tez de varsayılandır. Windows tabanlı HDInsight 'ta varsayılan değildir. Burada kullanmak için, Hive sorgunuzun başlangıcına satır `set hive.execution.engine = tez;` eklemeniz gerekir.

    Çıktıyı görüntülemek için **Iş çıkışı** bağlantısını kullanın. Bu durumda, sample_08 tablosundaki satır sayısı 823 ' dir. İş **günlüğünü** kullanarak iş hakkında tanılama bilgilerini görüntüleyebilir ve **Yarn günlük bağlantılarını indirebilirsiniz** .

4. **Toplu iş** alanını **etkileşimli**olarak değiştirerek Hive işlerini etkileşimli olarak da çalıştırabilirsiniz. Ardından **Yürüt**' ü seçin.

    ![Etkileşimli ve yürütme düğmelerinin vurgulandığı ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Etkileşimli bir sorgu, işleme sırasında oluşturulan çıkış günlüğünü **HiveServer2 çıkış** penceresine akıp.

    > [!NOTE]  
    > Bilgi, iş bittikten sonra **Iş günlüğü** bağlantısından kullanılabilir.

    ![HiveServer2 çıktısının ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Hive projesi oluşturma

Ayrıca, birden çok Hive komut dosyası içeren bir proje da oluşturabilirsiniz. İlgili betikleriniz varsa veya komut dosyalarını bir sürüm denetim sisteminde depolamak istediğinizde bir proje kullanın.

1. Visual Studio 'da **Dosya**, **Yeni**ve ardından **Proje**' yi seçin.

2. Projeler listesinden **Şablonlar**' ı genişletin, **Azure Data Lake**' ı genişletin ve **HIVE (HDInsight)** öğesini seçin. Şablon listesinden **Hive örneği**' ni seçin. Bir ad ve konum girip **Tamam**' ı seçin.

    ![Azure Data Lake, Hive örneği ve Tamam ile yeni proje penceresi](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

**Hive örnek** projesi iki komut dosyası Içerir, **webloganalysis. HQL** ve **sensordataanalysis. HQL**. Pencerenin üst kısmındaki **Gönder** düğmesini kullanarak bu betikleri gönderebilirsiniz.

## <a name="create-an-apache-pig-project"></a>Apache Pig projesi oluşturma

Hive, yapılandırılmış verilerle çalışmak için SQL benzeri bir dil sağlarken, Pig veriler üzerinde dönüşümler gerçekleştirerek çalışır. Pig, bir dönüştürme işlem hattı geliştirmenize olanak sağlayan bir dil (Pig Latin) sağlar. Yerel küme ile Pig kullanmak için şu adımları izleyin:

1. Visual Studio 'yu açın ve **Dosya**, **Yeni**ve ardından **Proje**' yi seçin. Projeler listesinden **Şablonlar**' ı genişletin, **Azure Data Lake**' ı genişletin ve ardından **Pig (HDInsight)** öğesini seçin. Şablonlar listesinden **Pig uygulaması**' nı seçin. Bir ad ve konum girin ve **Tamam**' ı seçin.

    ![Azure Data Lake, Pig, Pig uygulaması ve Tamam vurgulanmış yeni proje penceresinin ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Bu projeyle oluşturulan **Script. Pig** dosyasının içeriği olarak aşağıdaki metni girin.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig, Hive 'den farklı bir dil kullandığında, işleri nasıl çalıştıracağınız, **Gönder** düğmesi aracılığıyla her iki dil arasında tutarlıdır. **Gönder** ' in yanındaki açılan kutuyu seçtiğinizde, Pig için bir Gelişmiş gönderme iletişim kutusu görüntülenir.

    ![Betik Gönder iletişim kutusunun ekran görüntüsü Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. İş durumu ve çıkışı Ayrıca bir Hive sorgusuyla aynı şekilde görüntülenir.

    ![Tamamlanmış bir Pig işinin ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>İşleri görüntüle

Data Lake araçları ayrıca Hadoop 'ta çalıştırılan işlerle ilgili bilgileri kolayca görüntülemenizi sağlar. Yerel kümede çalıştırılmış işleri görmek için aşağıdaki adımları kullanın.

1. **Sunucu Gezgini**, yerel kümeye sağ tıklayın ve ardından **işleri görüntüle**' yi seçin. Kümeye gönderilen işlerin bir listesi görüntülenir.

    ![Görünüm Işleri vurgulanmış şekilde Sunucu Gezgini ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. İş listesinden, iş ayrıntılarını görüntülemek için bir tane seçin.

    ![Iş tarayıcısının, her biri vurgulanmış işlerin ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    Görüntülenecek bilgiler, çıktı ve günlük bilgilerini görüntüleme bağlantıları dahil olmak üzere Hive veya Pig sorgusu çalıştırdıktan sonra gördüklerinize benzer.

3. Ayrıca işi değiştirebilir ve buradan yeniden gönderebilirsiniz.

## <a name="view-hive-databases"></a>Hive veritabanlarını görüntüle

1. **Sunucu Gezgini**, **HDInsight yerel küme** girişini genişletin ve **Hive veritabanları**' nı genişletin. Yerel kümedeki **varsayılan** ve **xademo** veritabanları görüntülenir. Bir veritabanını genişletmek, veritabanının içindeki tabloları gösterir.

    ![Veritabanları genişletilmiş Sunucu Gezgini ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Bir tablonun genişletilmesi, bu tablo için sütunları görüntüler. Verileri hızlı bir şekilde görüntülemek için bir tabloya sağ tıklayın ve **ilk 100 satırı görüntüle**' yi seçin.

    ![Sunucu Gezgini, tablo genişletilmiş ve en üstteki 100 satırları görüntüleme seçili](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Veritabanı ve tablo özellikleri

Bir veritabanının veya tablonun özelliklerini görüntüleyebilirsiniz. **Özellikler** ' i seçmek, Özellikler penceresinde seçili öğenin ayrıntılarını görüntüler. Örneğin, aşağıdaki ekran görüntüsünde gösterilen bilgilere bakın:

![Özellikler penceresi ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Bir tablo oluşturma

Bir tablo oluşturmak için, bir veritabanına sağ tıklayın ve ardından **tablo oluştur**' u seçin.

![Tablo oluştur vurgulanarak Sunucu Gezgini ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Daha sonra formu kullanarak tablo oluşturabilirsiniz. Aşağıdaki ekran görüntüsünün en altında, tabloyu oluşturmak için kullanılan ham HiveQL ' yi görebilirsiniz.

![Tablo oluşturmak için kullanılan formun ekran görüntüsü](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Hortonçalışmalar korumalı alanının servis 'i öğrenme](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop öğreticisi-HDP ile çalışmaya başlama](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
