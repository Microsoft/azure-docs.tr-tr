---
title: Data Lake Storage 1. Portal ile Azure HDInsight kümeleri oluşturma
description: HDInsight kümelerini Azure Data Lake Storage 1. ile oluşturmak ve kullanmak için Azure portal kullanma
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838187"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Azure portal kullanarak Azure Data Lake Storage 1. ile HDInsight kümeleri oluşturma

> [!div class="op_single_selector"]
> * [Azure portal’ı kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell kullanma (varsayılan depolama için)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell kullanma (ek depolama için)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Kaynak Yöneticisi kullan](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Varsayılan depolama alanı veya ek depolama alanı olarak bir Azure Data Lake Storage 1. hesabıyla HDInsight kümesi oluşturmak için Azure portal nasıl kullanacağınızı öğrenin. HDInsight kümesi için ek depolama isteğe bağlı olsa da, iş verilerinizi ek depolama hesaplarında depolamanız önerilir.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce, aşağıdaki gereksinimleri karşıladığınızı doğrulayın:

* **Bir Azure aboneliği**. [Azure Ücretsiz deneme sürümünü alın](https://azure.microsoft.com/pricing/free-trial/)sayfasına gidin.
* **Data Lake Storage 1. hesabı**. [Azure Portal kullanarak Azure Data Lake Storage 1. ile çalışmaya başlama](data-lake-store-get-started-portal.md)yönergelerini izleyin. Ayrıca hesapta bir kök klasör oluşturmanız gerekir.  Bu makalede, __/kümeler__ adlı bir kök klasör kullanılır.
* **Bir Azure Active Directory hizmet sorumlusu**. Bu nasıl yapılır Kılavuzu, Azure Active Directory (Azure AD) içinde hizmet sorumlusu oluşturma hakkında yönergeler sağlar. Bununla birlikte, bir hizmet sorumlusu oluşturmak için bir Azure AD yöneticisi olmanız gerekir. Yönetici değilseniz, bu önkoşulu atlayıp devam edebilirsiniz.

>[!NOTE]
>Yalnızca bir Azure AD yöneticisiyseniz bir hizmet sorumlusu oluşturabilirsiniz. Data Lake Storage 1. ile bir HDInsight kümesi oluşturabilmeniz için Azure AD yöneticinizin bir hizmet sorumlusu oluşturması gerekir. Ayrıca hizmet sorumlusu, [sertifikayla birlikte hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)bölümünde açıklandığı gibi bir sertifikayla oluşturulmalıdır.
>

## <a name="create-an-hdinsight-cluster"></a>HDInsight kümesi oluşturma

Bu bölümde, varsayılan veya ek depolama alanı olarak Data Lake Storage 1. hesapları olan bir HDInsight kümesi oluşturacaksınız. Bu makale yalnızca Data Lake Storage 1. hesaplarını yapılandırma bölümüne odaklanır. Genel küme oluşturma bilgileri ve yordamları için bkz. [HDInsight 'Ta Hadoop kümeleri oluşturma](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage 1. varsayılan depolama alanı olarak bir küme oluşturma

Varsayılan depolama hesabı olarak Data Lake Storage 1. hesabıyla bir HDInsight kümesi oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. HDInsight kümeleri oluşturma hakkında genel bilgi için [küme oluşturma](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ' yı izleyin.
3. **Depolama** dikey penceresinde, **birincil depolama türü**' nün altında **Azure Data Lake Storage 1.** ' yi seçin ve ardından aşağıdaki bilgileri girin:

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "HDInsight kümesine hizmet sorumlusu ekleme")

    * **Data Lake Store hesabı seçin**: mevcut bir Data Lake Storage 1. hesabı seçin. Mevcut bir Data Lake Storage 1. hesabı gereklidir.  [Ön koşullara](#prerequisites) bakın.
    * **Kök yolu**: kümeye özgü dosyaların depolanacağı bir yol girin. Ekran görüntüsünde, __/Clusters/myhdiadlcluster/__ olur ve bu, __/kümeler__ klasörünün mevcut olması ve portalın *myhdicluster* klasörünü oluşturmasıdır.  *Myhdicluster* küme adıdır.
    * **Data Lake Store erişim**: Data Lake Storage 1. hesabı ve HDInsight kümesi arasında erişimi yapılandırın. Yönergeler için bkz. [Data Lake Storage 1. erişimi yapılandırma](#configure-data-lake-storage-gen1-access).
    * **Ek depolama hesapları**: Azure depolama hesaplarını küme için ek depolama hesapları olarak ekleyin. Ek Data Lake Storage 1. hesapları eklemek için, birincil depolama türü olarak bir Data Lake Storage 1. hesabı yapılandırılırken daha fazla Data Lake Storage 1. hesabında verilere küme izinleri vererek yapılır. Bkz. [Data Lake Storage 1. erişimi yapılandırma](#configure-data-lake-storage-gen1-access).

4. **Data Lake Store erişimi**için **Seç**' e tıklayın ve ardından [HDInsight 'ta Hadoop kümeleri oluşturma](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)bölümünde açıklandığı gibi küme oluşturmaya devam edin.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Ek depolama alanı olarak Data Lake Storage 1. bir küme oluşturma

Aşağıdaki yönergeler, varsayılan depolama alanı olarak Azure depolama hesabı ve ek depolama alanı olarak Data Lake Storage 1. bir hesap içeren bir HDInsight kümesi oluşturur.

Ek depolama hesabı olarak Data Lake Storage 1. hesabıyla bir HDInsight kümesi oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. HDInsight kümeleri oluşturma hakkında genel bilgi için [küme oluşturma](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ' yı izleyin.
3. **Depolama** dikey penceresinde, **birincil depolama türü**altında **Azure Storage**' ı seçin ve ardından aşağıdaki bilgileri girin:

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight kümesine hizmet sorumlusu ekleme")

    * **Seçim yöntemi** -Azure aboneliğinizin bir parçası olan bir depolama hesabı belirtmek Için **Aboneliklerim**' i seçin ve ardından Depolama hesabını seçin. Azure aboneliğinizin dışında bir depolama hesabı belirtmek için, **erişim anahtarı**' nı seçin ve ardından dış depolama hesabı için bilgileri sağlayın.

    * **Varsayılan kapsayıcı** -varsayılan değeri kullanın veya kendi adınızı belirtin.
    * **Ek depolama hesapları** -ek depolama alanı olarak daha fazla Azure depolama hesabı ekleyin.
    * **Data Lake Store erişim** -Data Lake Storage 1. hesabı ve HDInsight kümesi arasında erişimi yapılandırın. Yönergeler için bkz. [Data Lake Storage 1. erişimi yapılandırma](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage 1. erişimini yapılandırma

Bu bölümde, bir Azure Active Directory hizmet sorumlusu kullanarak HDInsight kümelerinden Data Lake Storage 1. erişimi yapılandırırsınız.

### <a name="specify-a-service-principal"></a>Hizmet sorumlusu belirtme

Azure portal, var olan bir hizmet sorumlusunu kullanabilir veya yeni bir tane oluşturabilirsiniz.

Azure portal bir hizmet sorumlusu oluşturmak için:

1. Depolama dikey penceresinden **Data Lake Store erişim** ' i seçin.
1. **Data Lake Storage 1. erişim** dikey penceresinde **Yeni oluştur**' u seçin.
1. **Hizmet sorumlusu**' nı seçin ve ardından hizmet sorumlusu oluşturmak için yönergeleri izleyin.
1. Daha sonra yeniden kullanmaya karar verirseniz sertifikayı indirin. Ek HDInsight kümeleri oluştururken aynı hizmet sorumlusunu kullanmak istiyorsanız, sertifikayı indirme yararlı olur.

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight kümesine hizmet sorumlusu ekleme")

1. Klasör erişimini yapılandırmak için **erişim** ' i seçin.  Bkz. [Dosya Izinlerini yapılandırma](#configure-file-permissions).

Azure portal mevcut bir hizmet sorumlusunu kullanmak için:

1. **Data Lake Store erişim**' i seçin.
1. **Data Lake Storage 1. erişim** dikey penceresinde **Varolanı kullan**' ı seçin.
1. **Hizmet sorumlusu**' nı seçin ve ardından hizmet sorumlusu ' nı seçin.
1. Seçtiğiniz hizmet sorumlusu ile ilişkili sertifikayı (. pfx dosyası) karşıya yükleyin ve sertifika parolasını girin.

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight kümesine hizmet sorumlusu ekleme")

1. Klasör erişimini yapılandırmak için **erişim** ' i seçin.  Bkz. [Dosya Izinlerini yapılandırma](#configure-file-permissions).

### <a name="configure-file-permissions"></a>Dosya izinlerini yapılandırma

Yapılandırma, hesabın varsayılan depolama alanı veya ek bir depolama hesabı olarak kullanılıp kullanıldığına bağlı olarak değişir:

* Varsayılan depolama alanı olarak kullanılır

  * Data Lake Storage 1. hesabının kök düzeyinde izin
  * HDInsight küme depolamanın kök düzeyinde izin. Örneğin, öğreticide daha önce kullanılan __/kümeler__ klasörü.

* Ek depolama alanı olarak kullanma

  * Dosya erişiminizin gerektiği klasörlerde izin.

Data Lake Storage 1. hesabı kök düzeyinde izin atamak için:

1. **Data Lake Storage 1. erişim** dikey penceresinde **erişim**' i seçin. **Dosya Izinlerini seçin** dikey penceresi açılır. Aboneliğinizdeki tüm Data Lake Storage 1. hesapları listelenir.
1. Onay kutusunun görünür olması için Data Lake Storage 1. hesabın adının üzerine gelin (tıklamayın) ve onay kutusunu seçin.

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight kümesine hizmet sorumlusu ekleme")

   Varsayılan olarak, __okuma__, __yazma__ve __yürütme__ tümü seçilidir.

1. Sayfanın alt kısmındaki **Seç** ' e tıklayın.
1. İzin atamak için **Çalıştır** ' ı seçin.
1. **Done** (Bitti) öğesini seçin.

HDInsight kümesi kök düzeyinde izin atamak için:

1. **Data Lake Storage 1. erişim** dikey penceresinde **erişim**' i seçin. **Dosya Izinlerini seçin** dikey penceresi açılır. Aboneliğinizdeki tüm Data Lake Storage 1. hesapları listelenir.
1. **Dosya Izinlerini seçin** dikey penceresinde, içeriğini göstermek için Data Lake Storage 1. hesap adını seçin.
1. Klasörün solundaki onay kutusunu seçerek HDInsight kümesi depolama kökünü seçin. Daha önce ekran görüntüsüne göre, küme depolama kökü, varsayılan depolama olarak Data Lake Storage 1. seçerken belirttiğiniz __/kümeler__ klasörüdür.
1. Klasör üzerindeki izinleri ayarlayın.  Varsayılan olarak, okuma, yazma ve yürütme tümü seçilidir.
1. Sayfanın alt kısmındaki **Seç** ' e tıklayın.
1. **Çalıştır**'ı seçin.
1. **Done** (Bitti) öğesini seçin.

Ek depolama alanı olarak Data Lake Storage 1. kullanıyorsanız, yalnızca HDInsight kümesinden erişmek istediğiniz klasörlere izin atamanız gerekir. Örneğin, aşağıdaki ekran görüntüsünde yalnızca bir Data Lake Storage 1. hesabındaki **myNewFolder** klasörüne erişim sağlarsınız.

![HDInsight kümesine hizmet sorumlusu izinleri atama](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "HDInsight kümesine hizmet sorumlusu izinleri atama")

## <a name="verify-cluster-set-up"></a>Küme kurulumunu doğrulama

Küme kurulumu tamamlandıktan sonra, küme dikey penceresinde, aşağıdaki adımlardan birini veya her ikisini gerçekleştirerek sonuçlarınızı doğrulayın:

* Küme için ilişkili depolamanın belirttiğiniz Data Lake Storage 1. hesabı olduğunu doğrulamak için sol bölmedeki **depolama hesapları** ' nı seçin.

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "HDInsight kümesine hizmet sorumlusu ekleme")

* Hizmet sorumlusunun HDInsight kümesiyle doğru şekilde ilişkilendirildiğini doğrulamak için sol bölmede **Data Lake Storage 1. erişim** ' i seçin.

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight kümesine hizmet sorumlusu ekleme")

## <a name="examples"></a>Örnekler

Depolama alanı olarak Data Lake Storage 1. kümesini ayarladıktan sonra, HDInsight kümesini kullanarak Data Lake Storage 1. depolanan verileri analiz etmek için bu örneklere bakın.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Bir Data Lake Storage 1. hesabındaki verilerde (birincil depolama alanı olarak) bir Hive sorgusu çalıştırma

Hive sorgusu çalıştırmak için, ambarı portalındaki Hive görünümleri arabirimini kullanın. Ambarı Hive Görünümlerini kullanma hakkında yönergeler için bkz. [HDInsight 'Ta Hadoop Ile Hive görünümünü kullanma](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Data Lake Storage 1. hesapta verilerle çalışırken, değiştirmek için birkaç dize vardır.

Örneğin, ile oluşturduğunuz küme, birincil depolama olarak Data Lake Storage 1. kullanıyorsanız, verilerin yolu: *adl://< data_lake_storage_gen1_account_name >/azuredatalakestore.net/Path/to/file*. Data Lake Storage 1. hesapta depolanan örnek verilerden tablo oluşturmak için bir Hive sorgusu aşağıdaki deyime benzer şekilde görünür:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Tanımlarını

* `adl://hdiadlsg1storage.azuredatalakestore.net/`, Data Lake Storage 1. hesabının köküdür.
* `/clusters/myhdiadlcluster`, kümeyi oluştururken belirttiğiniz küme verilerinin köküdür.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`, sorguda kullandığınız örnek dosyanın konumudur.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Data Lake Storage 1. hesabındaki verilerde bir Hive sorgusu çalıştırma (ek depolama alanı olarak)

Oluşturduğunuz küme, varsayılan depolama olarak blob Storage kullanıyorsa, örnek veriler ek depolama alanı olarak kullanılan Data Lake Storage 1. hesabında yer alır. Böyle bir durumda, önce verileri blob depolamadan Data Lake Storage 1. hesabına aktarın ve ardından önceki örnekte gösterildiği gibi sorguları çalıştırın.

Blob depolamadan bir Data Lake Storage 1. hesabına veri kopyalama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure depolama Blobları ve Data Lake Storage 1. arasında veri kopyalamak için Distcp kullanma](data-lake-store-copy-data-wasb-distcp.md)
* [Azure depolama Bloblarından veri kopyalamak için AdlCopy kullanın Data Lake Storage 1.](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Spark kümesi ile Data Lake Storage 1. kullanma

Spark işlerini Data Lake Storage 1. hesapta depolanan veriler üzerinde çalıştırmak için bir Spark kümesi kullanabilirsiniz. Daha fazla bilgi için bkz. [Data Lake Storage 1. verileri çözümlemek Için HDInsight Spark kümesini kullanma](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Bir fırtınası topolojisinde Data Lake Storage 1. kullanma

Data Lake Storage 1. hesabını bir fırtınası topolojisinden veri yazmak için kullanabilirsiniz. Bu senaryoya nasıl ulaşmanız hakkındaki yönergeler için bkz. [HDInsight ile Apache Storm ile Azure Data Lake Storage 1. kullanma](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Ayrıca bkz.

* [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: kullanmak için bir HDInsight kümesi oluşturma Data Lake Storage 1.](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
