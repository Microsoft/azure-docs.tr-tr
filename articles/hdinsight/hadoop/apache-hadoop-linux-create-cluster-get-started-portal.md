---
title: 'Hızlı başlangıç: Apache Hadoop, Apache Hive & Azure HDInsight portalı'
description: Bu hızlı başlangıçta, HDInsight Hadoop kümesi oluşturmak için Azure portal kullanırsınız
keywords: hadoop kullanmaya başlama,hadoop linux,hadoop hızlı başlangıç,hive kullanmaya başlama,hive hızlı başlangıç
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: cab46c4fe2ff0bd1f86c6d98a3b37efb3547a3ce
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044579"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Azure HDInsight 'ta Apache Hadoop kümesi oluşturma

Bu makalede, HDInsight 'ta Azure portal kullanarak [Apache Hadoop](https://hadoop.apache.org/) kümeleri oluşturmayı ve sonra hdınsight 'ta Apache Hive işleri çalıştırmayı öğreneceksiniz. Hadoop işlerinin çoğu toplu işlemdir. Bir küme oluşturur, bazı işleri çalıştırır ve kümeyi silersiniz. Bu makalede, üç görevi de gerçekleştirirsiniz.

Bu hızlı başlangıçta, HDInsight Hadoop kümesi oluşturmak için Azure portalını kullanırsınız. [Azure Resource Manager şablonunu](apache-hadoop-linux-tutorial-get-started.md) kullanarak da küme oluşturabilirsiniz.

Şu anda HDInsight [yedi farklı küme türüyle](../hdinsight-overview.md#cluster-types-in-hdinsight) ile birlikte gelir. Her küme türü farklı bir bileşen kümesini destekler. Tüm küme türleri Hive'ı destekler. HDInsight 'ta desteklenen bileşenlerin listesi için bkz. [HDInsight tarafından sunulan Apache Hadoop kümesi sürümlerindeki yenilikler nelerdir?](../hdinsight-component-versioning.md)  

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop kümesi oluşturma

Bu bölümde, Azure portalını kullanarak HDInsight’ta Hadoop kümesi oluşturursunuz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal, **kaynak oluşturma** > **Analytics** > **HDInsight**' a gidin.

    ![Kaynak HDInsight kümesi oluşturma](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Kaynak HDInsight kümesi oluşturma")

1. **Temel bilgiler**altında, aşağıdaki değerleri girin veya seçin:

    |Özellik  |Açıklama  |
    |---------|---------|
    |Abonelik    |  Azure aboneliğinizi seçin. |
    |Kaynak grubu     | Bir kaynak grubu oluşturun veya mevcut bir kaynak grubunu seçin.  Kaynak grubu, Azure bileşenleri için bir kapsayıcıdır.  Bu durumda, kaynak grubu HDInsight kümesini ve bağımlı Azure Depolama hesabını içermektedir. |
    |Küme adı   | Hadoop kümesi için bir ad girin. HDInsight’taki tüm kümeler aynı DNS ad alanını paylaştığından, bu adın benzersiz olması gerekir. Ad, harf, sayı ve kısa çizgi gibi en fazla 59 karakter içerebilir. Adın ilk ve son karakterleri, kısa çizgi olamaz. |
    |Konum    | Kümenizi oluşturmak istediğiniz bir Azure konumunu seçin.  Daha iyi performans için kendinize yakın bir konum seçin. |
    |Küme türü| **Küme türünü seç**' i seçin. Ardından küme türü olarak **Hadoop** ' ı seçin.|
    |Sürüm|Küme türü için varsayılan sürüm belirtilecektir. Farklı bir sürüm belirtmek istiyorsanız, açılan listeden seçim yapın.|
    |Küme oturum açma Kullanıcı adı ve parolası    | Varsayılan oturum açma adı **admin**' dir. Parola en az 10 karakter uzunluğunda olmalıdır ve en az bir rakam, bir büyük harf ve bir küçük harf, bir alfasayısal olmayan karakter (' "\)' karakterleri dışında) içermelidir. "Pass@word1" gibi genel parolalar **sağlamadığınızdan** emin olun.|
    |Secure Shell (SSH) kullanıcı adı | Varsayılan kullanıcı adı **sshuser** şeklindedir.  SSH kullanıcı adı için başka bir ad sağlayabilirsiniz. |
    |SSH için küme oturum açma parolasını kullanma| SSH kullanıcısı için, küme oturum açma kullanıcısı için sağladınız aynı parolayı kullanmak üzere bu onay kutusunu işaretleyin.|

    ![HDInsight Linux kullanmaya başlama küme temel değerlerini sağlama](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "HDInsight kümesi oluşturmak için temel değerler sağlama")

    **Sonraki: depolama > >** depolama ayarlarına ilerlemek için seçin.

1. **Depolama** sekmesinden aşağıdaki değerleri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |Birincil depolama türü|Varsayılan **Azure Storage**değerini kullanın.|
    |Seçim yöntemi|Varsayılan değer **listesinden Seç ' i**kullanın.|
    |Birincil depolama hesabı|Açılan listeyi kullanarak mevcut bir depolama hesabını seçin veya **Yeni oluştur**' u seçin. Yeni bir hesap oluşturursanız, ad 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir|
    |Kapsayıcı|Oto doldurulmuş değeri kullanın.|

    ![HDInsight Linux kullanmaya başlama küme depolama değerlerini sağlama](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage-blank.png "HDInsight kümesi oluşturmak için depolama değerleri sağlama")

    **Gözden geçir + oluştur** sekmesini seçin.

1. **Gözden geçir + oluştur** sekmesinden, önceki adımlarda seçtiğiniz değerleri doğrulayın.

    ![HDInsight Linux kullanmaya başlama kümesi Özeti](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux kullanmaya başlama kümesi Özeti")

1. **Oluştur**'u seçin. Bir küme oluşturmak yaklaşık 20 dakika sürer.

Küme oluşturulduktan sonra, Azure portalında kümeye genel bakış sayfasını görürsünüz.

![HDInsight Linux kullanmaya başlama kümesi ayarları](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "HDInsight kümesi özellikleri")

Her kümenin bir [Azure Depolama hesabı](../hdinsight-hadoop-use-blob-storage.md) veya [Azure Data Lake hesabı](../hdinsight-hadoop-use-data-lake-store.md) bağımlılığı vardır. Bu genellikle varsayılan depolama hesabı olarak ifade edilir. HDInsight kümesi ve varsayılan depolama hesabı aynı Azure bölgesinde birlikte bulunmalıdır. Kümeleri silmek depolama hesabını silmez.

> [!NOTE]  
> Diğer küme oluşturma yöntemleri ve bu hızlı başlangıçta kullanılan özellikleri anlamak için bkz. [HDInsight kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Apache Hive sorguları çalıştırma

[Apache Hive](hdinsight-use-hive.md) HDInsight’ta kullanılan en popüler bileşendir. HDInsight’ta Hive işleri çalıştırmanın birçok yolu vardır. Bu hızlı başlangıçta portaldan ambarı Hive görünümünü kullanırsınız. Hive işlerini göndermenin diğer yöntemleri için bkz. [HDInsight’ta Hive kullanma](hdinsight-use-hive.md).

1. Ambari’yi açmak için, önceki ekran görüntüsünden **Küme Panosu**’nu seçin.  Ayrıca, önceki bölümde oluşturduğunuz küme `ClusterName` `https://ClusterName.azurehdinsight.net`de bulabilirsiniz.

    ![HDInsight Linux kullanmaya başlama kümesi panosu](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux kullanmaya başlama kümesi panosu")

2. Kümeyi oluştururken belirlediğiniz Hadoop kullanıcı adını ve parolasını girin. Varsayılan kullanıcı adı **admin** şeklindedir.

3. Aşağıdaki ekran görüntüsünde gösterildiği gibi **Hive Görünümü**’nü açın:

    ![Ambarı 'ndan Hive görünümü seçme](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "HDInsight Hive Görüntüleyicisi menüsü")

4. Sayfadaki **SORGU** sekmesinde, aşağıdaki HiveQL ifadelerini çalışma sayfasına yapıştırın:

    ```sql
    SHOW TABLES;
    ```

    ![HDInsight Hive görünümü sorgu Düzenleyicisi](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "HDInsight Hive görünümü sorgu Düzenleyicisi")

5. **Yürüt**’ü seçin. **SORGU** sekmesinin altında bir **SONUÇLAR** sekmesi görünür. Bu sekmede işle ilgili bilgiler görüntülenir. 

    Sorgu tamamlandıktan sonra **sorgu** sekmesi işlemin sonuçlarını görüntüler. **hivesampletable** adlı bir tablo görürsünüz. Bu örnek Hive tablosu tüm HDInsight kümeleri ile birlikte gelir.

    ![HDInsight Apache Hive sonuçları görüntüle](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive sonuçları görüntüle")

6. Aşağıdaki sorguyu çalıştırmak için 4. ve 5. adımı yineleyin:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Ayrıca sorgunun sonuçlarını da kaydedebilirsiniz. Sağdaki menü düğmesini seçtikten sonra, sonuçları CSV dosyası olarak indirme veya kümeyle ilişkili depolama hesabında depolama seçeneklerinden birini belirleyin.

    ![Apache Hive sorgusunun sonucunu Kaydet](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Apache Hive sorgusunun sonucunu Kaydet")

Hive işini tamamladıktan sonra, [sonuçları Azure SQL Veritabanı’na veya SQL Server veritabanına aktarabilirsiniz](apache-hadoop-use-sqoop-mac-linux.md), ayrıca [Excel kullanarak sonuçları görselleştirebilirsiniz](apache-hadoop-connect-excel-power-query.md). HDInsight 'ta Hive kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop ile Apache Hive ve HiveQL kullanarak örnek Apache Log4J dosyası çözümleme](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

> [!NOTE]  
> HDInsight 'ta Hadoop kullanarak ETL işlemlerini nasıl çalıştıracağınızı öğrenmek için *hemen* bir sonraki makaleye devam ediyorsanız, kümeyi çalışır durumda tutmak isteyebilirsiniz. Bunun nedeni öğreticide bir Hadoop kümesi oluşturmanız gerekir. Ancak, bir sonraki makaleyi hemen ilerleyecekseniz kümeyi şimdi silmeniz gerekir.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Küme ve/veya varsayılan depolama hesabını silmek için

1. Azure portalın bulunduğu tarayıcı sekmesine dönün. Kümeye genel bakış sayfasında olmalısınız. Yalnızca kümeyi silmek, ancak varsayılan depolama hesabını korumak istiyorsanız **Sil**’i seçin.

    ![Azure HDInsight küme silme](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Azure HDInsight kümesini silme")

2. Kümeyi ve varsayılan depolama hesabını silmek istiyorsanız, kaynak grubu sayfasını açmak için kaynak grubu adını (önceki ekran görüntüsünde vurgulanan) seçin.

3. **Kaynak grubunu sil**’i seçerek, kümeyi ve varsayılan depolama hesabını içeren kaynak grubunu silin. Kaynak grubu silindiğinde depolama hesabının da silindiğini unutmayın. Depolama hesabını tutmak istiyorsanız, yalnızca küme silmeyi seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Kaynak Yöneticisi şablonu kullanarak Linux tabanlı HDInsight kümesi oluşturmayı ve temel Hive sorguları gerçekleştirmeyi öğrendiniz. Sonraki makalede, HDInsight üzerinde Hadoop kullanarak ayıklama, dönüştürme ve yükleme (ETL) işlemi gerçekleştirmeyi öğreneceksiniz.

> [!div class="nextstepaction"]
> [HDInsight üzerinde etkileşimli sorgu kullanarak verileri ayıklama, dönüştürme ve yükleme](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
