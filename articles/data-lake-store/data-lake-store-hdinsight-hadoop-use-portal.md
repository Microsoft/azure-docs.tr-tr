---
title: Data Lake Storage 1. Portal ile Azure HDInsight kümeleri oluşturma
description: HDInsight kümelerini Azure Data Lake Storage 1. ile oluşturmak ve kullanmak için Azure portal kullanma
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 666b39e2a600fe6ca004798ed4f8371cdd1dfe5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96340263"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Azure portal kullanarak Azure Data Lake Storage 1. ile HDInsight kümeleri oluşturma

> [!div class="op_single_selector"]
> * [Azure portal kullanın](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell kullanma (varsayılan depolama için)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell kullanma (ek depolama için)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Kaynak Yöneticisi kullan](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Varsayılan depolama alanı veya ek depolama alanı olarak Azure Data Lake Storage 1. sahip bir HDInsight kümesi oluşturmak için Azure portal nasıl kullanacağınızı öğrenin. HDInsight kümesi için ek depolama isteğe bağlı olsa da, iş verilerinizi ek depolama hesaplarında depolamanız önerilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki gereksinimleri karşıladığınızı doğrulayın:

* **Bir Azure aboneliği**. [Azure Ücretsiz deneme sürümünü alın](https://azure.microsoft.com/pricing/free-trial/)sayfasına gidin.
* **Azure Data Lake Storage 1. hesabı**. [Azure Portal kullanarak Azure Data Lake Storage 1. ile çalışmaya başlama](data-lake-store-get-started-portal.md)yönergelerini izleyin. Ayrıca hesapta bir kök klasör oluşturmanız gerekir.  Bu makalede, __/kümeler__ adlı bir kök klasör kullanılır.
* **Bir Azure Active Directory hizmet sorumlusu**. Bu nasıl yapılır Kılavuzu, Azure Active Directory (Azure AD) içinde hizmet sorumlusu oluşturma hakkında yönergeler sağlar. Bununla birlikte, bir hizmet sorumlusu oluşturmak için bir Azure AD yöneticisi olmanız gerekir. Yönetici değilseniz, bu önkoşulu atlayıp devam edebilirsiniz.

>[!NOTE]
>Yalnızca bir Azure AD yöneticisiyseniz bir hizmet sorumlusu oluşturabilirsiniz. Data Lake Storage 1. ile bir HDInsight kümesi oluşturabilmeniz için Azure AD yöneticinizin bir hizmet sorumlusu oluşturması gerekir. Ayrıca hizmet sorumlusu, [sertifikayla birlikte hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)bölümünde açıklandığı gibi bir sertifikayla oluşturulmalıdır.
>

## <a name="create-an-hdinsight-cluster"></a>HDInsight kümesi oluşturma

Bu bölümde, varsayılan veya ek depolama alanı olarak Data Lake Storage 1. bir HDInsight kümesi oluşturacaksınız. Bu makale yalnızca Data Lake Storage 1. yapılandırma bölümüne odaklanır. Genel küme oluşturma bilgileri ve yordamları için bkz. [HDInsight 'Ta Hadoop kümeleri oluşturma](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage 1. varsayılan depolama alanı olarak bir küme oluşturma

Varsayılan depolama hesabı olarak Data Lake Storage 1. bir HDInsight kümesi oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. HDInsight kümeleri oluşturma hakkında genel bilgi için [küme oluşturma](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ' yı izleyin.
3. **Depolama** dikey penceresinde, **birincil depolama türü**' nün altında **Azure Data Lake Storage 1.**' yi seçin ve ardından aşağıdaki bilgileri girin:

    ![HDInsight depolama hesabı ayarları](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **Data Lake Store hesabı seçin**: mevcut bir Data Lake Storage 1. hesabı seçin. Mevcut bir Data Lake Storage 1. hesabı gereklidir.  [Ön koşullara](#prerequisites) bakın.
    * **Kök yolu**: kümeye özgü dosyaların depolanacağı bir yol girin. Ekran görüntüsünde, __/Clusters/myhdiadlcluster/__ olur ve bu, __/kümeler__ klasörünün mevcut olması ve portalın *myhdicluster* klasörünü oluşturmasıdır.  *Myhdicluster* küme adıdır.
    * **Data Lake Store erişim**: Data Lake Storage 1. hesabı ve HDInsight kümesi arasında erişimi yapılandırın. Yönergeler için bkz. [Data Lake Storage 1. erişimi yapılandırma](#configure-data-lake-storage-gen1-access).
    * **Ek depolama hesapları**: Azure depolama hesaplarını küme için ek depolama hesapları olarak ekleyin. Ek Data Lake Storage 1. hesapları eklemek için, birincil depolama türü olarak bir Data Lake Storage 1. hesabı yapılandırılırken daha fazla Data Lake Storage 1. hesabında verilere küme izinleri vererek yapılır. Bkz. [Data Lake Storage 1. erişimi yapılandırma](#configure-data-lake-storage-gen1-access).

4. **Data Lake Store erişimi** için **Seç**' e tıklayın ve ardından [HDInsight 'ta Hadoop kümeleri oluşturma](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)bölümünde açıklandığı gibi küme oluşturmaya devam edin.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Ek depolama alanı olarak Data Lake Storage 1. bir küme oluşturma

Aşağıdaki yönergeler varsayılan depolama alanı olarak bir Azure Blob depolama hesabı ve ek depolama alanı olarak Data Lake Storage 1. olan bir depolama hesabı içeren bir HDInsight kümesi oluşturur.

Ek depolama hesabı olarak Data Lake Storage 1. bir HDInsight kümesi oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. HDInsight kümeleri oluşturma hakkında genel bilgi için [küme oluşturma](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) ' yı izleyin.
3. **Depolama** dikey penceresinde, **birincil depolama türü** altında **Azure Storage**' ı seçin ve ardından aşağıdaki bilgileri girin:

    ![HDInsight depolama hesabı ayarları ek depolama](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **Seçim yöntemi** -Azure aboneliğinizin bir parçası olan bir depolama hesabı belirtmek Için **Aboneliklerim**' i seçin ve ardından Depolama hesabını seçin. Azure aboneliğinizin dışında bir depolama hesabı belirtmek için, **erişim anahtarı**' nı seçin ve ardından dış depolama hesabı için bilgileri sağlayın.

    * **Varsayılan kapsayıcı** -varsayılan değeri kullanın veya kendi adınızı belirtin.
    * **Ek depolama hesapları** -ek depolama alanı olarak daha fazla Azure depolama hesabı ekleyin.
    * **Data Lake Store erişim** -Data Lake Storage 1. hesabı ve HDInsight kümesi arasında erişimi yapılandırın. Yönergeler için bkz. [Data Lake Storage 1. erişimi yapılandırma](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage 1. erişimini yapılandırma

Bu bölümde, bir Azure Active Directory hizmet sorumlusu kullanarak HDInsight kümelerinden Data Lake Storage 1. erişimi yapılandırırsınız.

### <a name="specify-a-service-principal"></a>Hizmet sorumlusu belirtme

Azure portal, var olan bir hizmet sorumlusunu kullanabilir veya yeni bir tane oluşturabilirsiniz.

Azure portal bir hizmet sorumlusu oluşturmak için:
1. Bkz. Azure Active Directory kullanarak [hizmet sorumlusu ve sertifikaları oluşturma](../active-directory/develop/howto-create-service-principal-portal.md) .

Azure portal mevcut bir hizmet sorumlusunu kullanmak için:

1. Hizmet sorumlusu, depolama hesabında sahip izinlerine sahip olmalıdır. [Hizmet sorumlusu için depolama hesabında sahip olacak Izinleri ayarlama](#configure-serviceprincipal-permissions)bölümüne bakın.
1. **Data Lake Store erişim**' i seçin.
1. **Data Lake Storage 1. erişim** dikey penceresinde **Varolanı kullan**' ı seçin.
1. **Hizmet sorumlusu**' nı seçin ve ardından hizmet sorumlusu ' nı seçin.
1. Seçtiğiniz hizmet sorumlusu ile ilişkili sertifikayı (. pfx dosyası) karşıya yükleyin ve sertifika parolasını girin.

    ![HDInsight kümesine hizmet sorumlusu ekleme](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. Klasör erişimini yapılandırmak için **erişim** ' i seçin.  Bkz. [Dosya Izinlerini yapılandırma](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Depolama hesabında sahip olması için hizmet sorumlusu için izinleri ayarlayın
1. Depolama hesabının Access Control (ıAM) dikey penceresinde rol ataması Ekle ' ye tıklayın. 
2. Rol ataması Ekle dikey penceresinde, rolü ' sahip ' olarak seçin ve SPN 'yi seçip Kaydet ' e tıklayın.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Dosya izinlerini yapılandırma

Yapılandırma, hesabın varsayılan depolama alanı veya ek bir depolama hesabı olarak kullanılıp kullanıldığına bağlı olarak değişir:

* Varsayılan depolama alanı olarak kullanılır

  * Data Lake Storage 1. hesabının kök düzeyinde izin
  * HDInsight küme depolamanın kök düzeyinde izin. Örneğin, öğreticide daha önce kullanılan __/kümeler__ klasörü.

* Ek depolama alanı olarak kullanma

  * Dosya erişiminizin gerektiği klasörlerde izin.

Depolama hesabındaki Data Lake Storage 1. kök düzeyinde izin atamak için:

1. **Data Lake Storage 1. erişim** dikey penceresinde **erişim**' i seçin. **Dosya Izinlerini seçin** dikey penceresi açılır. Aboneliğinizdeki tüm depolama hesaplarını listeler.
1. Onay kutusunun görünür olması için Data Lake Storage 1. ile hesap adının üzerine gelin (tıklamayın) ve onay kutusunu seçin.

    ![Dosya izinlerini seçin](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   Varsayılan olarak, __okuma__, __yazma__ ve __yürütme__ tümü seçilidir.

1. Sayfanın alt kısmındaki **Seç** ' e tıklayın.
1. İzin atamak için **Çalıştır** ' ı seçin.
1. **Bitti** seçeneğini belirleyin.

HDInsight kümesi kök düzeyinde izin atamak için:

1. **Data Lake Storage 1. erişim** dikey penceresinde **erişim**' i seçin. **Dosya Izinlerini seçin** dikey penceresi açılır. Aboneliğinizde Data Lake Storage 1. olan tüm depolama hesaplarını listeler.
1. **Dosya Izinlerini seçin** dikey penceresinde, içeriğini göstermek için Data Lake Storage 1. adına sahip depolama hesabını seçin.
1. Klasörün solundaki onay kutusunu seçerek HDInsight kümesi depolama kökünü seçin. Daha önce ekran görüntüsüne göre, küme depolama kökü, varsayılan depolama olarak Data Lake Storage 1. seçerken belirttiğiniz __/kümeler__ klasörüdür.
1. Klasör üzerindeki izinleri ayarlayın.  Varsayılan olarak, okuma, yazma ve yürütme tümü seçilidir.
1. Sayfanın alt kısmındaki **Seç** ' e tıklayın.
1. **Çalıştır**'ı seçin.
1. **Bitti** seçeneğini belirleyin.

Ek depolama alanı olarak Data Lake Storage 1. kullanıyorsanız, yalnızca HDInsight kümesinden erişmek istediğiniz klasörlere izin atamanız gerekir. Örneğin, aşağıdaki ekran görüntüsünde yalnızca Data Lake Storage 1. olan bir depolama hesabındaki **myNewFolder** klasörüne erişim sağlarsınız.

![HDInsight kümesine hizmet sorumlusu izinleri atama](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Küme kurulumunu doğrulama

Küme kurulumu tamamlandıktan sonra, küme dikey penceresinde, aşağıdaki adımlardan birini veya her ikisini gerçekleştirerek sonuçlarınızı doğrulayın:

* Küme için ilişkili depolamanın belirttiğiniz Data Lake Storage 1. hesap olduğunu doğrulamak için, sol bölmedeki **depolama hesapları** ' nı seçin.

    ![İlişkili depolamayı doğrulama](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* Hizmet sorumlusunun HDInsight kümesiyle doğru şekilde ilişkilendirildiğini doğrulamak için sol bölmede **Data Lake Storage 1. erişim** ' i seçin.

    ![Hizmet sorumlusunu doğrulama](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>Örnekler

Depolama alanı olarak Data Lake Storage 1. kümesini ayarladıktan sonra, HDInsight kümesini kullanarak Data Lake Storage 1. depolanan verileri analiz etmek için bu örneklere bakın.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>Data Lake Storage 1. verilerde bir Hive sorgusu çalıştırma (birincil depolama olarak)

Hive sorgusu çalıştırmak için, ambarı portalındaki Hive görünümleri arabirimini kullanın. Ambarı Hive Görünümlerini kullanma hakkında yönergeler için bkz. [HDInsight 'Ta Hadoop Ile Hive görünümünü kullanma](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Data Lake Storage 1. verilerle çalışırken, değiştirmek için birkaç dize vardır.

Örneğin, ile oluşturduğunuz küme birincil depolama alanı olarak Data Lake Storage 1., verilerin yolu: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/Path/to/file*. Data Lake Storage 1. depolanan örnek verilerden tablo oluşturmak için bir Hive sorgusu aşağıdaki ifadeye benzer şekilde görünür:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Tanımlarını

* `adl://hdiadlsg1storage.azuredatalakestore.net/` Data Lake Storage 1. olan hesabın köküdür.
* `/clusters/myhdiadlcluster` , kümeyi oluştururken belirttiğiniz küme verilerinin köküdür.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` , sorguda kullandığınız örnek dosyanın konumudur.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage 1. verilerde bir Hive sorgusu çalıştırma (ek depolama olarak)

Oluşturduğunuz küme, varsayılan depolama olarak blob Storage kullanıyorsa, örnek veriler, ek depolama alanı olarak kullanılan Data Lake Storage 1. depolama hesabında yer alır. Böyle bir durumda, önce verileri blob depolamadan Data Lake Storage 1. depolama hesabına aktarın ve ardından önceki örnekte gösterildiği gibi sorguları çalıştırın.

Blob depolamadan Data Lake Storage 1. depolama hesabına veri kopyalama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure Blob depolama ve Data Lake Storage 1. arasında veri kopyalamak için Distcp kullanma](data-lake-store-copy-data-wasb-distcp.md)
* [Azure Blob depolamadan Data Lake Storage 1. ' ye veri kopyalamak için AdlCopy kullanın](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Spark kümesi ile Data Lake Storage 1. kullanma

Spark işlerini bir Data Lake Storage 1. depolanan veriler üzerinde çalıştırmak için bir Spark kümesi kullanabilirsiniz. Daha fazla bilgi için bkz. [Data Lake Storage 1. verileri çözümlemek Için HDInsight Spark kümesini kullanma](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Bir fırtınası topolojisinde Data Lake Storage 1. kullanma

Bir fırtınası topolojisinden veri yazmak için Data Lake Storage 1. ile depolama hesabını kullanabilirsiniz. Bu senaryoya nasıl ulaşmanız hakkındaki yönergeler için bkz. [HDInsight ile Apache Storm ile Azure Data Lake Storage 1. kullanma](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Ayrıca bkz.

* [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: kullanmak için bir HDInsight kümesi oluşturma Data Lake Storage 1.](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx