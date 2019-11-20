---
title: 'Hızlı başlangıç: şablon kullanarak Spark kümesi oluşturma-Azure HDInsight'
description: Bu hızlı başlangıçta, Kaynak Yöneticisi şablonu kullanılarak nasıl Azure HDInsight’ta Apache Spark kümesi oluşturulacağı ve basit bir Spark SQL sorgusu çalıştırılacağı gösterilmektedir.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 3d4c3b92139a5d1ba90c58cb998cbba99c8a5746
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494112"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Hızlı başlangıç: Kaynak Yöneticisi şablonu kullanarak Azure HDInsight 'ta Apache Spark kümesi oluşturma

Azure HDInsight 'ta bir [Apache Spark](https://spark.apache.org/) kümesi oluşturmayı ve Spark SQL sorgularını [Apache Hive](https://hive.apache.org/) tablolarında çalıştırmayı öğrenin. Apache Spark, bellek içi işleme kullanarak hızlı veri analizi ve küme hesaplama sağlar. HDInsight’ta Spark hakkında daha fazla bilgi için bkz. [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md).

Bu hızlı başlangıçta, HDInsight Spark kümesi oluşturmak için Kaynak Yöneticisi şablonu kullanırsınız. Benzer şablonlar, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)görüntülenebilir. Şablon başvurusunu [burada](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions)bulabilirsiniz.

Küme, küme depolama alanı olarak Azure Depolama Bloblarını kullanır. Data Lake Storage Gen2'yi kullanma hakkında daha fazla bilgi için bkz. [Hızlı başlangıç: HDInsight'ta kümeleri ayarlama](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> İster kullanın, ister kullanmayın, HDInsight kümeleri faturalaması dakika başına eşit olarak dağıtılmıştır. Kullanmayı bitirdikten sonra kümenizi sildiğinizden emin olun. Daha fazla bilgi için bu makalenin [Kaynakları temizleme](#clean-up-resources) bölümüne bakın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-an-hdinsight-spark-cluster"></a>HDInsight Spark kümesi oluşturma

Azure Resource Manager şablonu kullanarak bir HDInsight Spark kümesi oluşturun. Şablon [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/)' da bulunabilir. Kümenin JSON sözdizimi ve özellikleri için bkz. [Microsoft. HDInsight/kümeler](/azure/templates/microsoft.hdinsight/clusters).

1. Azure portalındaki şablonu yeni bir tarayıcı sekmesinde açmak için aşağıdaki bağlantıyı seçin:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Azure’a Dağıtma</a>

2. Aşağıdaki değerleri girin:

    | Özellik | Değer |
    |---|---|
    |**Abonelik**|Bu kümeyi oluşturmak için kullanılan Azure aboneliğinizi seçin. Bu hızlı başlangıç için kullanılan abonelik, **&lt;Azure abonelik adı>** şeklindedir. |
    | **Kaynak grubu**|Kaynak grubu oluşturun veya var olan bir grubu seçin. Kaynak grubu, projelerinize ait Azure kaynaklarını yönetmek için kullanılır. Bu hızlı başlangıç için kullanılan yeni kaynak grubu adı, **myspark20180403rg** şeklindedir.|
    | **Konum**|Kaynak grubu için bir konum seçin. Şablon hem kümeyi oluşturmak için hem de varsayılan küme depolaması için bu konumu kullanır. Bu hızlı başlangıç için kullanılan konum, **Doğu ABD 2**’dir.|
    | **ClusterName**|Oluşturmak istediğiniz HDInsight kümesi için bir ad girin. Bu hızlı başlangıç için kullanılan yeni küme adı, **myspark20180403** şeklindedir.|
    | **Küme oturum açma adı ve parolası**|Varsayılan oturum açma adı admin ' dir. Küme oturumu açmak için bir parola seçin. Bu hızlı başlangıç için kullanılan oturum açma adı, admin’dir.|
    | **SSH kullanıcı adı ve parolası**|SSH kullanıcısı için bir parola seçin. Bu hızlı başlangıç için kullanılan SSH kullanıcı adı, **sshuser** şeklindedir.|

    ![Azure Resource Manager şablonu kullanarak HDInsight Spark kümesi oluşturma](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Azure Resource Manager şablonu kullanarak HDInsight 'ta Spark kümesi oluşturma")

3. **Yukarıdaki hüküm ve koşulları kabul ediyorum**’u ve ardından **Panoya sabitle**’yi seçip **Satın al**’a tıklayın. Artık **Şablon Dağıtımını dağıtma** başlıklı yeni bir kutucuk görebilirsiniz. Kümenin oluşturulması yaklaşık 20 dakika sürer. Sonraki oturumuna devam etmeden önce küme oluşturulması gerekir.

HDInsight kümelerini oluştururken sorunlarla karşılaşırsanız, bunu yapmak için doğru izinlere sahip olmayabilirsiniz. Daha fazla bilgi için bkz. [Erişim denetimi gereksinimleri](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="install-intellijeclipse-for-spark-application"></a>Spark uygulaması için IntelliJ/Eclipse’i yükleme

[Scala](https://www.scala-lang.org/)'Da yazılmış Spark uygulamaları geliştirmek için Azure Toolkit for IntelliJ/tutulma eklentisini kullanın ve ardından bunları doğrudan IntelliJ/tutulma tümleşik geliştirme ORTAMıNDAN (IDE) bir Azure HDInsight Spark kümesine gönderebilirsiniz. Daha fazla bilgi için bkz. [Spark uygulaması yazmak/göndermek için IntelliJ kullanma](./apache-spark-intellij-tool-plugin.md) ve [Spark uygulaması yazmak/göndermek için Eclipse kullanma](./apache-spark-eclipse-tool-plugin.md).

## <a name="install-vscode-for-pysparkhive-applications"></a>PySpark/hive uygulamaları için VSCode yükleme

Hive toplu işleri, etkileşimli Hive sorguları, PySpark toplu işi ve PySpark etkileşimli betikleri oluşturup göndermek için Visual Studio Code (VSCode) için Azure HDInsight Araçları’nın nasıl kullanılacağını öğrenin. Azure HDInsight Tools, VSCode tarafından desteklenen platformlara yüklenebilir. Bunlar arasında Windows, Linux ve MacOS yer alır. Daha fazla bilgi için bkz. [PySpark uygulaması yazmak/göndermek için VSCode kullanma](../hdinsight-for-vscode.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter not defteri oluşturma

[Jupyter Notebook](https://jupyter.org/) , çeşitli programlama dillerini destekleyen etkileşimli bir not defteri ortamıdır. Not defteri, verilerle etkileşim kurmanıza, kodu markdown metniyle birleştirmenize ve basit görselleştirmeler gerçekleştirmenize olanak sağlar.

1. [Azure portalı](https://portal.azure.com) açın.

2. **HDInsight kümeleri**’ni ve sonra oluşturduğunuz kümeyi seçin.

    ![Azure portalında HDInsight kümesini açma](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Portalda, **Küme panoları** bölümünde **Jupyter Notebook**’a tıklayın. İstendiğinde, küme için küme oturum açma kimlik bilgilerini girin.

   ![Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook açın](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook açın")

4. **Yeni** > **PySpark** seçeneklerini belirleyerek bir not defteri oluşturun.

   ![Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook oluşturma](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook oluşturma")

   Untitled(Untitled.pynb) adıyla yeni bir not defteri oluşturulur ve açılır.

## <a name="run-spark-sql-statements"></a>Spark SQL deyimleri çalıştırma

SQL (Yapılandırılmış Sorgu Dili), veri sorgulama ve dönüştürme için en yaygın kullanılan dildir. Bilinen SQL söz dizimini kullanan Spark SQL, yapısal verileri işleyen bir Apache Spark uzantısı olarak çalışır.

1. Çekirdeğin hazır olduğunu doğrulayın. Not defterinde çekirdek adının yanında boş bir daire görmeniz, çekirdeğin hazır olduğu anlamına gelir. Dolu daire, çekirdeğin meşgul olduğunu belirtir.

    ![çekirdek durumu](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "çekirdek durumu")

    Not defterini ilk kez başlattığınızda, çekirdek arka planda birkaç görev gerçekleştirir. Çekirdeğin hazır olmasını bekleyin.
1. Aşağıdaki kodu boş bir hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + ENTER** tuşlarına basın. Komut, kümedeki Hive tablolarını listeler:

    ```sql
    %%sql
    SHOW TABLES
    ```

    HDInsight Spark kümeniz için yapılandırılmış bir Jupyter not defteri kullanırken, Spark SQL ile Hive sorguları çalıştırmak için kullanabileceğiniz önceden ayarlanmış bir `spark` oturumu alırsınız. `%%sql`, Hive sorgusunu çalıştırmak için Jupyter Not Defteri’ne `spark` oturumu ön ayarını kullanmasını söyler. Sorgu, varsayılan olarak tüm HDInsight kümelerinde sağlanan Hive tablosundaki (**hivesampletable**) ilk 10 satırı getirir. Sorguyu ilk kez gönderdiğinizde Jupyter not defteri için Spark Uygulaması oluşturur. Tamamlanması yaklaşık 30 saniye sürer. Spark uygulaması hazır olduğunda sorgu yaklaşık bir saniyede yürütülür ve sonuçları üretir. Çıkış aşağıdakine benzer olacaktır:

    ![HDInsight Spark 'ta Apache Hive sorgu](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark 'ta Hive sorgusu")

    Jupyter’de bir sorguyu her çalıştırdığınızda web tarayıcınızın pencere başlığında not defteri başlığı ile birlikte **(Meşgul)** durumu gösterilir. Ayrıca sağ üst köşedeki **PySpark** metninin yanında içi dolu bir daire görürsünüz.

1. `hivesampletable` komutundaki verileri görmek için başka bir sorgu çalıştırın.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Sorgu çıkışının görüntülenmesi için ekranın yenilenmesi gerekir.

    ![HDInsight Spark 'ta Hive sorgusu çıkışı](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark 'ta Hive sorgusu çıkışı")

1. Not defterindeki **Dosya** menüsünden **Kapat ve Durdur**’u seçin. Not defterini kapatmak, Spark Uygulaması da dahil olmak üzere küme kaynaklarını serbest bırakır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

HDInsight, verilerinizi ve Jupyter not defterlerini Azure Depolama’da veya Azure Data Lake Store’da depolar; böylece kullanımda olmayan bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır. [Sonraki adımlar](#next-steps) içinde listelenen öğretici üzerinde hemen çalışmayı planlıyorsanız, kümeyi tutmak isteyebilirsiniz.

Azure portalına geri dönüp **Sil**’i seçin.

![HDInsight kümesini silme Azure portal](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight kümesini Sil")

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek hem HDInsight Spark kümesini hem de varsayılan depolama hesabını silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta HDInsight Spark kümesi oluşturmayı ve temel Spark SQL sorgusunu çalıştırmayı öğrendiniz. HDInsight Spark kümesini kullanarak örnek veriler üzerinde etkileşimli sorgular çalıştırma hakkında daha fazla bilgi edinmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
>[Apache Spark etkileşimli sorgular çalıştırma](./apache-spark-load-data-run-query.md)
