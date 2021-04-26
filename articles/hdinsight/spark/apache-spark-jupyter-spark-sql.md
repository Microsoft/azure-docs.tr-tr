---
title: 'Hızlı başlangıç: şablon kullanarak Apache Spark kümesi oluşturma-Azure HDInsight'
description: Bu hızlı başlangıçta, Azure HDInsight 'ta bir Apache Spark kümesi oluşturmak ve bir Spark SQL sorgusu çalıştırmak için Kaynak Yöneticisi şablonunun nasıl kullanılacağı gösterilmektedir.
ms.date: 03/13/2020
ms.topic: quickstart
ms.service: hdinsight
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6d3f8c3313d9e6be38acdbecb683899ea8dd5376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538010"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Azure HDInsight 'ta Apache Spark kümesi oluşturma

Bu hızlı başlangıçta, Azure HDInsight 'ta bir [Apache Spark](./apache-spark-overview.md) kümesi oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız. Daha sonra bir Jupyter Notebook dosyası oluşturup Apache Hive tablolarında Spark SQL sorguları çalıştırmak için kullanabilirsiniz. Azure HDInsight kuruluşlara yönelik, yönetilen, tam spektrumlu ve açık kaynaklı bir analiz hizmetidir. HDInsight için Apache Spark Framework, bellek içi işleme kullanarak hızlı veri analizi ve küme bilgi işlem desteği sunar. Jupyter Notebook verilerinizle etkileşim kurmanıza, kodu markı metniyle birleştirmenize ve basit görselleştirmeler yapmanıza olanak sağlar.

Birden çok kümeyi birlikte kullanıyorsanız, bir sanal ağ oluşturmak isteyeceksiniz ve bir Spark kümesi kullanıyorsanız, Hive ambarı bağlayıcısını de kullanmak isteyeceksiniz. Daha fazla bilgi için bkz. [Azure HDInsight için bir sanal ağ planlayın](../hdinsight-plan-virtual-network-deployment.md) ve [Hive ambarı Bağlayıcısı ile Apache Spark ve Apache Hive tümleştirin](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure 'a dağıtma":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): bir Azure depolama hesabı oluşturun.
* [Microsoft. HDInsight/Cluster](/azure/templates/microsoft.hdinsight/clusters): HDInsight kümesi oluşturma.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve ARM şablonunu açmak için aşağıdaki **Azure 'A dağıt** düğmesini seçin.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Aşağıdaki değerleri yazın veya seçin:

    |Özellik |Açıklama |
    |---|---|
    |Abonelik|Aşağı açılan listeden, küme için kullanılan Azure aboneliğini seçin.|
    |Kaynak grubu|Aşağı açılan listeden, mevcut kaynak grubunuzu seçin veya **Yeni oluştur**' u seçin.|
    |Konum|Değer, kaynak grubu için kullanılan konum ile tekrar doldurulur.|
    |Küme Adı|Genel olarak benzersiz bir ad girin. Bu şablon için yalnızca küçük harfler ve rakamlar kullanın.|
    |Küme Oturum Açma Kullanıcı Adı|Kullanıcı adını belirtin, varsayılan olarak **admin**' dir.|
    |Küme Oturum Açma Parolası|Bir parola belirtin. Parola en az 10 karakter uzunluğunda olmalıdır ve en az bir rakam, bir büyük harf ve bir küçük harf, bir alfasayısal olmayan karakter (' "' karakterleri dışında) içermelidir. |
    |SSH Kullanıcı adı|Kullanıcı adını belirtin, varsayılan olarak **sshuser**|
    |SSH parolası|Parolayı girin.|

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png " alt-text="Azure Resource Manager şablonu kullanarak HDInsight 'ta Spark kümesi oluşturma" border="true":::

1. **Hüküm ve koşulları** gözden geçirin. Ardından, **yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u ve ardından **satın al**' ı seçin. Dağıtımınızın devam ettiğini belirten bir bildirim alırsınız. Bir küme oluşturmak yaklaşık 20 dakika sürer.

HDInsight kümeleri oluşturma ile ilgili bir sorun yaşıyorsanız, bunu yapmak için doğru izinlere sahip değilsiniz. Daha fazla bilgi için bkz. [Erişim denetimi gereksinimleri](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Küme oluşturulduktan sonra **Kaynağa Git** bağlantısına sahip bir **dağıtım başarılı** bildirimi alırsınız. Kaynak grubu sayfanız yeni HDInsight kümenizi ve kümeyle ilişkili varsayılan depolamayı listeler. Her kümenin bir [Azure depolama alanı](../hdinsight-hadoop-use-blob-storage.md), [Azure Data Lake Storage 1.](../hdinsight-hadoop-use-data-lake-storage-gen1.md)veya [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) bağımlılığı vardır. Bu, varsayılan depolama hesabı olarak adlandırılır. HDInsight kümesi ve varsayılan depolama hesabı aynı Azure bölgesinde birlikte bulunmalıdır. Küme silme, depolama hesabı bağımlılığını silmez. Bu, varsayılan depolama hesabı olarak adlandırılır. HDInsight kümesi ve varsayılan depolama hesabı aynı Azure bölgesinde birlikte bulunmalıdır. Küme silme, depolama hesabını silmez.

## <a name="create-a-jupyter-notebook-file"></a>Jupyter Notebook dosyası oluşturma

[Jupyter Notebook](https://jupyter.org/) , çeşitli programlama dillerini destekleyen etkileşimli bir not defteri ortamıdır. Verilerinizle etkileşim kurmak, kodu markı metniyle birleştirmek ve basit görselleştirmeler gerçekleştirmek için bir Jupyter Notebook dosyası kullanabilirsiniz.

1. [Azure portalını](https://portal.azure.com) açın.

2. **HDInsight kümeleri**’ni ve sonra oluşturduğunuz kümeyi seçin.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png" alt-text="HDInsight kümesini Azure portal açın" border="true":::

3. Portalda, **küme panoları** bölümünde **Jupyter Notebook**' yi seçin. İstendiğinde, küme için küme oturum açma kimlik bilgilerini girin.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook açın" border="true":::

4.   >  Bir not defteri oluşturmak için yeni **pyspark** ' ı seçin.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Etkileşimli Spark SQL sorgusu çalıştırmak için bir Jupyter Notebook dosyası oluşturma" border="true":::

   Untitled(Untitled.pynb) adıyla yeni bir not defteri oluşturulur ve açılır.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL deyimlerini Çalıştır

SQL (Yapılandırılmış Sorgu Dili), veri sorgulama ve dönüştürme için en yaygın kullanılan dildir. Bilinen SQL söz dizimini kullanan Spark SQL, yapısal verileri işleyen bir Apache Spark uzantısı olarak çalışır.

1. Çekirdeğin hazır olduğunu doğrulayın. Not defterinde çekirdek adının yanında boş bir daire görmeniz, çekirdeğin hazır olduğu anlamına gelir. Dolu daire, çekirdeğin meşgul olduğunu belirtir.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="Çekirdek durumu" border="true"::: alt-metin = "çekirdek durumu" Border = "true":::

    Not defterini ilk kez başlattığınızda, çekirdek arka planda birkaç görev gerçekleştirir. Çekirdeğin hazır olmasını bekleyin.

1. Aşağıdaki kodu boş bir hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + ENTER** tuşlarına basın. Komut, kümedeki Hive tablolarını listeler:

    ```sql
    %%sql
    SHOW TABLES
    ```

    HDInsight kümeniz ile bir Jupyter Notebook dosyası kullandığınızda, `spark` Spark SQL kullanarak Hive sorguları çalıştırmak için kullanabileceğiniz önceden ayarlanmış bir oturum alırsınız. `%%sql`, Hive sorgusunu çalıştırmak için Jupyter Not Defteri’ne `spark` oturumu ön ayarını kullanmasını söyler. Sorgu, varsayılan olarak tüm HDInsight kümelerinde sağlanan Hive tablosundaki (**hivesampletable**) ilk 10 satırı getirir. Sorguyu ilk kez gönderdiğinizde, Jupyter Not defteri için bir Spark uygulaması oluşturacaktır. Tamamlanması yaklaşık 30 saniye sürer. Spark uygulaması çalışmaya başladıktan sonra sorgu bir saniye içinde yürütülür ve sonuçları üretir. Çıktı şuna benzer:

    HDInsight 'ta :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png " alt-text="HDInsight y 'de sorgu Apache Hive" border="true":::"Border =" true ":::

    Jupyter’de bir sorguyu her çalıştırdığınızda web tarayıcınızın pencere başlığında not defteri başlığı ile birlikte **(Meşgul)** durumu gösterilir. Ayrıca sağ üst köşedeki **PySpark** metninin yanında içi dolu bir daire görürsünüz.

1. `hivesampletable` komutundaki verileri görmek için başka bir sorgu çalıştırın.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Sorgu çıkışının görüntülenmesi için ekranın yenilenmesi gerekir.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png " alt-text="HDInsight 'Ta Hive sorgusu çıkışı" border="true"::: Insight "Border =" true ":::

1. Not defterindeki **Dosya** menüsünden **Kapat ve Durdur**’u seçin. Not defterini kapatmak Spark uygulaması da dahil olmak üzere küme kaynaklarını serbest bırakır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

Azure portal kümenize gidin ve **Sil**' i seçin.

HDInsight kümesi bir "Border =" true " :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png " alt-text="olan bir HDInsight kümesini silme Azure Portal" border="true"::::::

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek, hem HDInsight kümesini hem de varsayılan depolama hesabını silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, HDInsight 'ta Apache Spark kümesi oluşturmayı ve temel Spark SQL sorgusunu çalıştırmayı öğrendiniz. Örnek verilerde etkileşimli sorgular çalıştırmak için bir HDInsight kümesi kullanmayı öğrenmek üzere bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Apache Spark etkileşimli sorgular çalıştırma](./apache-spark-load-data-run-query.md)
