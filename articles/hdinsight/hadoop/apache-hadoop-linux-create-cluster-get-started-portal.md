---
title: 'Hızlı başlangıç: Apache Hadoop, Apache Hive & Azure HDInsight portalı'
description: Bu hızlı başlangıçta, HDInsight Hadoop kümesi oluşturmak için Azure portal kullanırsınız
keywords: hadoop kullanmaya başlama,hadoop linux,hadoop hızlı başlangıç,hive kullanmaya başlama,hive hızlı başlangıç
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 92520be7e9d2bb83cb61856fe3df38487445ad9a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863896"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Azure HDInsight 'ta Apache Hadoop kümesi oluşturma

Bu makalede, HDInsight 'ta Azure portal kullanarak Apache Hadoop kümeleri oluşturmayı ve sonra HDInsight 'ta Apache Hive işleri çalıştırmayı öğreneceksiniz. Hadoop işlerinin çoğu toplu işlemdir. Bir küme oluşturur, bazı işleri çalıştırır ve kümeyi silersiniz. Bu makalede, üç görevi de gerçekleştirirsiniz. Kullanılabilir yapılandırmaların derinlemesine açıklamaları için bkz. [HDInsight 'ta kümeleri ayarlama](../hdinsight-hadoop-provision-linux-clusters.md). Küme oluşturmak üzere portalın kullanımıyla ilgili daha fazla bilgi için bkz. [portalda kümeler oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md).

Bu hızlı başlangıçta, HDInsight Hadoop kümesi oluşturmak için Azure portalını kullanırsınız. [Azure Resource Manager şablonunu](apache-hadoop-linux-tutorial-get-started.md) kullanarak da küme oluşturabilirsiniz.

HDInsight Şu anda [yedi farklı küme türüyle](../hdinsight-overview.md#cluster-types-in-hdinsight)birlikte gelir. Her küme türü farklı bir bileşen kümesini destekler. Tüm küme türleri Hive'ı destekler. HDInsight 'ta desteklenen bileşenlerin listesi için bkz. [HDInsight tarafından sunulan Apache Hadoop kümesi sürümlerindeki yenilikler nelerdir?](../hdinsight-component-versioning.md)  

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop kümesi oluşturma

Bu bölümde, Azure portalını kullanarak HDInsight’ta Hadoop kümesi oluşturursunuz.

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. Üstteki menüden **+ kaynak oluştur**' u seçin.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png" alt-text="Kaynak HDInsight kümesi oluşturma" border="true":::

1. **Analiz**  >  **Azure HDInsight** ' ı seçerek **HDInsight kümesi oluşturma** sayfasına gidin.

1. **Temel bilgiler** sekmesinde, aşağıdaki bilgileri sağlayın:

   |Özellik  |Açıklama  |
   |---------|---------|
   |Abonelik    |  Aşağı açılan listeden, küme için kullanılan Azure aboneliğini seçin. |
   |Kaynak grubu     | Aşağı açılan listeden, mevcut kaynak grubunuzu seçin veya **Yeni oluştur**' u seçin.|
   |Küme adı   | Genel olarak benzersiz bir ad girin. Ad, harf, sayı ve kısa çizgi gibi en fazla 59 karakter içerebilir. Adın ilk ve son karakterleri kısa çizgi olamaz. |
   |Region    | Aşağı açılan listeden, kümenin oluşturulduğu bir bölge seçin.  Daha iyi performans için kendinize yakın bir konum seçin. |
   |Küme türü| **Küme türünü seç**' i seçin. Ardından küme türü olarak **Hadoop** ' ı seçin.|
   |Sürüm|Aşağı açılan listeden bir **Sürüm** seçin. Ne seçeceğimizi bilmiyorsanız varsayılan sürümü kullanın.|
   |Küme oturum açma kullanıcı adı ve parolası    | Varsayılan oturum açma adı **admin**' dir. Parola en az 10 karakter uzunluğunda olmalıdır ve en az bir rakam, bir büyük harf ve bir küçük harf, bir alfasayısal olmayan karakter (' "' karakterleri dışında) içermelidir \) . "Pass@word1" gibi genel parolalar **sağlamadığınızdan** emin olun.|
   |Secure Shell (SSH) kullanıcı adı | Varsayılan kullanıcı adı **sshuser** şeklindedir.  SSH kullanıcı adı için başka bir ad sağlayabilirsiniz. |
   |SSH için küme oturum açma parolasını kullanma| SSH kullanıcısı için, küme oturum açma kullanıcısı için sağladınız aynı parolayı kullanmak üzere bu onay kutusunu işaretleyin.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png" alt-text="HDInsight Linux kullanmaya başlama küme temel değerlerini sağlama" border="true":::

   **Sonraki: depolama >>** depolama ayarlarına ilerlemek için seçin.

1. **Depolama** sekmesinden aşağıdaki değerleri sağlayın:

   |Özellik  |Açıklama  |
   |---------|---------|
   |Birincil depolama türü|Varsayılan **Azure Storage** değerini kullanın.|
   |Seçim yöntemi|Varsayılan değer **listesinden Seç ' i** kullanın.|
   |Birincil depolama hesabı|Açılan listeyi kullanarak mevcut bir depolama hesabını seçin veya **Yeni oluştur**' u seçin. Yeni bir hesap oluşturursanız, ad 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir|
   |Kapsayıcı|Oto doldurulmuş değeri kullanın.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png" alt-text="HDInsight Linux kullanmaya başlama küme depolama değerlerini sağlama" border="true":::

   Her kümenin bir [Azure depolama hesabı](../hdinsight-hadoop-use-blob-storage.md), bir [Azure Data Lake Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)veya bir bağımlılığı vardır [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) . Bu, varsayılan depolama hesabı olarak adlandırılır. HDInsight kümesi ve varsayılan depolama hesabı aynı Azure bölgesinde birlikte bulunmalıdır. Küme silme, depolama hesabını silmez.

   **Gözden geçir + oluştur** sekmesini seçin.

1. **Gözden geçir + oluştur** sekmesinden, önceki adımlarda seçtiğiniz değerleri doğrulayın.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="HDInsight Linux kullanmaya başlama kümesi Özeti" border="true":::

1. **Oluştur**’u seçin. Bir küme oluşturmak yaklaşık 20 dakika sürer.

   Küme oluşturulduktan sonra, Azure portalında kümeye genel bakış sayfasını görürsünüz.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png" alt-text="HDInsight Linux kullanmaya başlama küme ayarları" border="true":::

## <a name="run-apache-hive-queries"></a>Apache Hive sorguları çalıştırma

[Apache Hive](hdinsight-use-hive.md) HDInsight’ta kullanılan en popüler bileşendir. HDInsight’ta Hive işleri çalıştırmanın birçok yolu vardır. Bu hızlı başlangıçta portaldan ambarı Hive görünümünü kullanırsınız. Hive işlerini göndermenin diğer yöntemleri için bkz. [HDInsight’ta Hive kullanma](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive görünümü HDInsight 4,0 ' de kullanılamaz.

1. Ambari’yi açmak için, önceki ekran görüntüsünden **Küme Panosu**’nu seçin.  Ayrıca,  `https://ClusterName.azurehdinsight.net` `ClusterName` önceki bölümde oluşturduğunuz kümenin bulunduğu yere de gidebilirsiniz.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png" alt-text="HDInsight Linux kullanmaya başlama kümesi panosu" border="true":::

2. Kümeyi oluştururken belirlediğiniz Hadoop kullanıcı adını ve parolasını girin. Varsayılan kullanıcı adı **admin** şeklindedir.

3. Aşağıdaki ekran görüntüsünde gösterildiği gibi **Hive Görünümü**’nü açın:

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png" alt-text="Ambarı 'ndan Hive görünümü seçme" border="true":::

4. Sayfadaki **SORGU** sekmesinde, aşağıdaki HiveQL ifadelerini çalışma sayfasına yapıştırın:

   ```sql
   SHOW TABLES;
   ```

    :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png" alt-text="HDInsight Hive görünümü sorgu Düzenleyicisi" border="true":::

5. **Yürüt**’ü seçin. **SORGU** sekmesinin altında bir **SONUÇLAR** sekmesi görünür. Bu sekmede işle ilgili bilgiler görüntülenir.

   Sorgu tamamlandıktan sonra **sorgu** sekmesi işlemin sonuçlarını görüntüler. **hivesampletable** adlı bir tablo görürsünüz. Bu örnek Hive tablosu tüm HDInsight kümeleri ile birlikte gelir.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png" alt-text="HDInsight Apache Hive sonuçları görüntüle" border="true":::

6. Aşağıdaki sorguyu çalıştırmak için 4. ve 5. adımı yineleyin:

   ```sql
   SELECT * FROM hivesampletable;
   ```

7. Ayrıca sorgunun sonuçlarını da kaydedebilirsiniz. Sağdaki menü düğmesini seçtikten sonra, sonuçları CSV dosyası olarak indirme veya kümeyle ilişkili depolama hesabında depolama seçeneklerinden birini belirleyin.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png" alt-text="Apache Hive sorgusunun sonucunu Kaydet" border="true":::

Bir Hive işini tamamladıktan sonra, [sonuçları Azure SQL veritabanı 'na veya SQL Server veritabanına aktarabilirsiniz](apache-hadoop-use-sqoop-mac-linux.md), ayrıca [Excel kullanarak sonuçları görselleştirebilirsiniz](apache-hadoop-connect-excel-power-query.md). HDInsight 'ta Hive kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop ile Apache Hive ve HiveQL kullanarak örnek Apache Log4J dosyası çözümleme](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

> [!NOTE]  
> HDInsight 'ta Hadoop kullanarak ETL işlemlerini nasıl çalıştıracağınızı öğrenmek için *hemen* bir sonraki makaleye devam ediyorsanız, kümeyi çalışır durumda tutmak isteyebilirsiniz. Bunun nedeni öğreticide bir Hadoop kümesi oluşturmanız gerekir. Ancak, bir sonraki makaleyi hemen ilerleyecekseniz kümeyi şimdi silmeniz gerekir.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Küme ve/veya varsayılan depolama hesabını silmek için

1. Azure portalın bulunduğu tarayıcı sekmesine dönün. Kümeye genel bakış sayfasında olmalısınız. Yalnızca kümeyi silmek, ancak varsayılan depolama hesabını korumak istiyorsanız **Sil**’i seçin.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png" alt-text="Azure HDInsight küme silme" border="true":::

2. Kümeyi ve varsayılan depolama hesabını silmek istiyorsanız, kaynak grubu sayfasını açmak için kaynak grubu adını (önceki ekran görüntüsünde vurgulanan) seçin.

3. **Kaynak grubunu sil**’i seçerek, kümeyi ve varsayılan depolama hesabını içeren kaynak grubunu silin. Kaynak grubu silindiğinde depolama hesabının da silindiğini unutmayın. Depolama hesabını tutmak istiyorsanız, yalnızca küme silmeyi seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Kaynak Yöneticisi şablonu kullanarak Linux tabanlı HDInsight kümesi oluşturmayı ve temel Hive sorguları gerçekleştirmeyi öğrendiniz. Sonraki makalede, HDInsight üzerinde Hadoop kullanarak ayıklama, dönüştürme ve yükleme (ETL) işlemi gerçekleştirmeyi öğreneceksiniz.

> [!div class="nextstepaction"]
> [HDInsight üzerinde etkileşimli sorgu kullanarak verileri ayıklama, dönüştürme ve yükleme](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
