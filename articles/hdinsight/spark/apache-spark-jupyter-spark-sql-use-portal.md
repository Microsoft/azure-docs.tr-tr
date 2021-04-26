---
title: "Hızlı başlangıç: Azure portal kullanarak HDInsight 'ta Spark kümesi oluşturma"
description: Bu hızlı başlangıçta, Azure HDInsight 'ta bir Apache Spark kümesi oluşturmak ve bir Spark SQL sorgusu çalıştırmak için Azure portal nasıl kullanılacağı gösterilmektedir.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 145dffea50040c86a4af9d77ba8f68cccc8d2958
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866055"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Azure HDInsight 'ta Apache Spark kümesi oluşturma

Bu hızlı başlangıçta, Azure HDInsight 'ta bir Apache Spark kümesi oluşturmak için Azure portal kullanırsınız. Daha sonra bir Jupyter Notebook oluşturup Apache Hive tablolarda Spark SQL sorguları çalıştırmak için kullanabilirsiniz. Azure HDInsight kuruluşlara yönelik, yönetilen, tam spektrumlu ve açık kaynaklı bir analiz hizmetidir. HDInsight için Apache Spark Framework, bellek içi işleme kullanarak hızlı veri analizi ve küme bilgi işlem desteği sunar. Jupyter Notebook verilerinizle etkileşim kurmanıza, kodu markı metniyle birleştirmenize ve basit görselleştirmeler yapmanıza olanak sağlar.

Kullanılabilir yapılandırmaların derinlemesine açıklamaları için bkz. [HDInsight 'ta kümeleri ayarlama](../hdinsight-hadoop-provision-linux-clusters.md). Küme oluşturmak üzere portalın kullanımıyla ilgili daha fazla bilgi için bkz. [portalda kümeler oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md).

Birden çok kümeyi birlikte kullanıyorsanız, bir sanal ağ oluşturmak isteyeceksiniz ve bir Spark kümesi kullanıyorsanız, Hive ambarı bağlayıcısını de kullanmak isteyeceksiniz. Daha fazla bilgi için bkz. [Azure HDInsight için bir sanal ağ planlayın](../hdinsight-plan-virtual-network-deployment.md) ve [Hive ambarı Bağlayıcısı ile Apache Spark ve Apache Hive tümleştirin](../interactive-query/apache-hive-warehouse-connector.md).

> [!IMPORTANT]  
> İster kullanın, ister kullanmayın, HDInsight kümeleri faturalaması dakika başına eşit olarak dağıtılmıştır. Kullanmayı bitirdikten sonra kümenizi sildiğinizden emin olun. Daha fazla bilgi için bu makalenin [Kaynakları temizleme](#clean-up-resources) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>HDInsight 'ta Apache Spark kümesi oluşturma

Küme depolama birimi olarak Azure depolama Blobları kullanan bir HDInsight kümesi oluşturmak için Azure portal kullanın. Data Lake Storage Gen2'yi kullanma hakkında daha fazla bilgi için bkz. [Hızlı başlangıç: HDInsight'ta kümeleri ayarlama](../hdinsight-hadoop-provision-linux-clusters.md).

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Üstteki menüden **+ kaynak oluştur**' u seçin.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png " alt-text="Azure Portal kaynak kaynağı oluşturma" border="true":::"Border =" true ":::

1. **Analiz**  >  **Azure HDInsight** ' ı seçerek **HDInsight kümesi oluşturma** sayfasına gidin.

1. **Temel bilgiler** sekmesinde, aşağıdaki bilgileri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |Abonelik  | Aşağı açılan listeden, küme için kullanılan Azure aboneliğini seçin. |
    |Kaynak grubu | Aşağı açılan listeden, mevcut kaynak grubunuzu seçin veya **Yeni oluştur**' u seçin.|
    |Küme adı | Genel olarak benzersiz bir ad girin.|
    |Region   | Aşağı açılan listeden, kümenin oluşturulduğu bir bölge seçin. |
    |Küme türü| Liste açmak için küme türünü seç ' i seçin. Listeden **Spark**' ı seçin.|
    |Küme sürümü|Bu alan, küme türü seçildikten sonra otomatik olarak varsayılan sürümle doldurulur.|
    |Küme oturum açma kullanıcı adı| Küme oturum açma kullanıcı adını girin.  Varsayılan ad **admin**' dir. Bu hesabı daha sonra hızlı başlangıçta Jupyter Notebook oturum açmak için kullanırsınız. |
    |Küme oturum açma parolası| Küme oturum açma parolasını girin. |
    |Secure Shell (SSH) kullanıcı adı| SSH kullanıcı adını girin. Bu hızlı başlangıç için kullanılan SSH kullanıcı adı, **sshuser** şeklindedir. Varsayılan olarak bu hesap, *Küme Oturum Açma kullanıcı adı* hesabıyla aynı parolayı paylaşır. |

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png " alt-text="Ekran görüntüsünde temel bilgiler sekmesi seçili olan H-Insight Cluster oluştur görüntülenir." border="true":::

    Ileri ' yi seçin: **depolama sayfasına devam** etmek için **depolama >>** .

1. **Depolama** bölümünde aşağıdaki değerleri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |Birincil depolama türü|Varsayılan **Azure Storage** değerini kullanın.|
    |Seçim yöntemi|Varsayılan değer **listesinden Seç ' i** kullanın.|
    |Birincil depolama hesabı|Otomatik doldurulmuş değeri kullanın.|
    |Kapsayıcı|Otomatik doldurulmuş değeri kullanın.|

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png " alt-text="Ekran görüntüsü, depolama sekmesi seçili olan H D Insight kümesini gösterir." border="true":::

    Devam etmek için **gözden geçir + oluştur** ' u seçin.

1. **Gözden geçir + oluştur** altında **Oluştur**' u seçin. Kümenin oluşturulması yaklaşık 20 dakika sürer. Sonraki oturumuna devam etmeden önce küme oluşturulması gerekir.

HDInsight kümeleri oluşturma ile ilgili bir sorun yaşıyorsanız, bunu yapmak için doğru izinlere sahip değilsiniz. Daha fazla bilgi için bkz. [Erişim denetimi gereksinimleri](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook oluşturma

Jupyter Notebook, çeşitli programlama dillerini destekleyen etkileşimli bir not defteri ortamıdır. Not defteri, verilerle etkileşim kurmanıza, kodu markdown metniyle birleştirmenize ve basit görselleştirmeler gerçekleştirmenize olanak sağlar.

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net/jupyter` , `CLUSTERNAME` Kümenizin adı olan ' a gidin. İstendiğinde, küme için küme oturum açma kimlik bilgilerini girin.

1.   >  Bir not defteri oluşturmak için yeni **pyspark** ' ı seçin.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook oluşturma" border="true":::

   Untitled(Untitled.pynb) adıyla yeni bir not defteri oluşturulur ve açılır.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL deyimlerini Çalıştır

SQL (Yapılandırılmış Sorgu Dili), veri sorgulama ve tanımlama için en çok kullanılan dildir. Bilinen SQL söz dizimini kullanan Spark SQL, yapısal verileri işleyen bir Apache Spark uzantısı olarak çalışır.

1. Çekirdeğin hazır olduğunu doğrulayın. Not defterinde çekirdek adının yanında boş bir daire görmeniz, çekirdeğin hazır olduğu anlamına gelir. Dolu daire, çekirdeğin meşgul olduğunu belirtir.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="Ekran görüntüsünde, PySpark göstergesi olan bir Jupyıter penceresi gösterilir." border="true"::: ark gösterge. " Border = "true":::

    Not defterini ilk kez başlattığınızda, çekirdek arka planda birkaç görev gerçekleştirir. Çekirdeğin hazır olmasını bekleyin.

1. Aşağıdaki kodu boş bir hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + ENTER** tuşlarına basın. Komut, kümedeki Hive tablolarını listeler:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    HDInsight kümeniz ile bir Jupyter Notebook kullandığınızda, `sqlContext` Spark SQL kullanarak Hive sorguları çalıştırmak için kullanabileceğiniz bir ön ayar alırsınız. `%%sql`, Hive sorgusunu çalıştırmak için Jupyter Not Defteri’ne `sqlContext` ön ayarını kullanmasını söyler. Sorgu, varsayılan olarak tüm HDInsight kümelerinde sağlanan Hive tablosundaki (**hivesampletable**) ilk 10 satırı getirir. Sonuçları almak 30 saniye kadar sürer. Çıktı şuna benzer:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png " alt-text="Ekran görüntüsünde, bu hızlı başlangıçta oluşturulan Not defteri için bir Jupyter penceresi gösterilir." border="true"::: hızlı başlangıç. " Border = "true":::

    Jupyter’de bir sorguyu her çalıştırdığınızda web tarayıcınızın pencere başlığında not defteri başlığı ile birlikte **(Meşgul)** durumu gösterilir. Ayrıca sağ üst köşedeki **PySpark** metninin yanında içi dolu bir daire görürsünüz.

1. `hivesampletable` komutundaki verileri görmek için başka bir sorgu çalıştırın.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Sorgu çıkışının görüntülenmesi için ekranın yenilenmesi gerekir.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png " alt-text="HDInsight 'Ta Hive sorgusu çıkışı" border="true"::: Insight "Border =" true ":::

1. Not defterindeki **Dosya** menüsünden **Kapat ve Durdur**’u seçin. Not defterini kapatmak, küme kaynaklarını serbest bırakır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

HDInsight, verileri Azure depolama alanına veya Azure Data Lake Storage kaydederek bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar. [Sonraki adımlar](#next-steps) içinde listelenen öğretici üzerinde hemen çalışmayı planlıyorsanız, kümeyi tutmak isteyebilirsiniz.

Azure portalına geri dönüp **Sil**’i seçin.

HDInsight kümesi bir "Border =" true " :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png " alt-text="olan bir HDInsight kümesini silme Azure Portal" border="true"::::::

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek, hem HDInsight kümesini hem de varsayılan depolama hesabını silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, HDInsight 'ta Apache Spark kümesi oluşturmayı ve temel Spark SQL sorgusunu çalıştırmayı öğrendiniz. Örnek verilerde etkileşimli sorgular çalıştırmak için bir HDInsight kümesi kullanmayı öğrenmek üzere bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Apache Spark etkileşimli sorgular çalıştırma](./apache-spark-load-data-run-query.md)