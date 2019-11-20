---
title: 'Hızlı başlangıç: Azure Databricks kullanarak Azure Data Lake Storage 2. verileri çözümleme | Microsoft Docs'
description: Azure portal ve Azure Data Lake Storage 2. depolama hesabı kullanarak Azure Databricks Spark işini çalıştırmayı öğrenin.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.reviewer: jeking
ms.openlocfilehash: 5badd4aeabd8ec322ea5fb847cf134f302269c27
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331006"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Hızlı başlangıç: Azure Databricks kullanarak Azure Data Lake Storage 2. verileri çözümleme

Bu hızlı başlangıçta, Azure Data Lake Storage 2. etkin olan bir depolama hesabında depolanan verileri analiz etmek için Azure Databricks kullanarak Apache Spark işi çalıştırma işlemi gösterilmektedir.

Spark işinin bir parçası olarak, demografik tabanlı ücretsiz/ücretli kullanıma yönelik Öngörüler elde etmek için bir radyo kanalı abonelik verilerini çözümleyebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Data Lake Gen2 depolama hesabı oluşturun. Bkz [. hızlı başlangıç: Azure Data Lake Storage 2. depolama hesabı oluşturma](data-lake-storage-quickstart-create-account.md)

  Depolama hesabının adını bir metin dosyasına yapıştırın. Yakında ihtiyacınız olacak.

* Hizmet sorumlusu oluşturun. Bkz. [nasıl yapılır: Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Söz konusu makaledeki adımları gerçekleştirirken yapmanız gereken birkaç şey vardır.

  : heavy_check_mark: makalenin [bir role uygulamayı atama](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) bölümündeki adımları gerçekleştirirken, **Depolama Blobu veri katılımcısı** rolünü hizmet sorumlusuna atadığınızdan emin olun.

  > [!IMPORTANT]
  > Rolü Data Lake Storage 2. depolama hesabının kapsamına atadığınızdan emin olun. Üst kaynak grubuna veya aboneliğine bir rol atayabilirsiniz, ancak bu rol atamaları depolama hesabına yayana kadar izinlerle ilgili hatalar alırsınız.

  : heavy_check_mark: makalenin [oturum açmak için değerleri Al](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) bölümünde bulunan adımları gerçekleştirirken, Kiracı kimliği, uygulama kimliği ve parola değerlerini bir metin dosyasına yapıştırın. Bu kadar yakında ihtiyacınız olacak.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks çalışma alanı oluşturma

Bu bölümde Azure portalını kullanarak bir Azure Databricks çalışma alanı oluşturursunuz.

1. Azure portalında **Kaynak oluşturun** > **Analiz** > **Azure Databricks**'i seçin.

    ![Azure portalında Databricks](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks on Azure portal")

2. **Azure Databricks Hizmeti** bölümünde, Databricks çalışma alanı oluşturmak için değerler sağlayın.

    ![Azure Databricks çalışma alanı oluşturma](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Create an Azure Databricks workspace")

    Aşağıdaki değerleri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |**Çalışma alanı adı**     | Databricks çalışma alanınız için bir ad sağlayın        |
    |**Abonelik**     | Açılan listeden Azure aboneliğinizi seçin.        |
    |**Kaynak grubu**     | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. Kaynak grubu, bir Azure çözümü için ilgili kaynakları bir arada tutan kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../../azure-resource-manager/resource-group-overview.md). |
    |**Konum**     | **Batı ABD 2**'yi seçin. Tercih ettiğiniz başka bir genel bölgeyi seçebilirsiniz.        |
    |**Fiyatlandırma Katmanı**     |  **Standart** veya **Premium** arasında seçim yapın. Bu katmanlar hakkında daha fazla bilgi için bkz. [Databricks fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Hesabın oluşturulması birkaç dakika sürer. İşlem durumunu izlemek için üstteki ilerleme çubuğunu görüntüleyin.

4. **Panoya sabitle**’yi ve sonra **Oluştur**’u seçin.

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks’te Spark kümesi oluşturma

1. Azure portalında, oluşturduğunuz Databricks çalışma alanına gidin ve sonra **Çalışma Alanını Başlat**’ı seçin.

2. Azure Databricks portalına yönlendirilirsiniz. Portaldan **Yeni** > **Küme**'yi seçin.

    ![Azure’da Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks on Azure")

3. **Yeni küme** sayfasında, bir küme oluşturmak için değerleri girin.

    ![Azure’da Databricks Spark kümesi oluşturma](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

    Aşağıdaki alanlara değerleri girin ve diğer alanlar için varsayılan değerleri kabul edin:

    - Küme için bir ad girin.
     
    - **120 dakika işlem yapılmadığında sonlandır** onay kutusunu seçtiğinizden emin olun. Küme kullanılmazsa kümenin sonlandırılması için biz süre (dakika cinsinden) belirtin.

4. **Küme oluştur**’u seçin. Küme çalışmaya başladıktan sonra kümeye not defterleri ekleyebilir ve Spark işleri çalıştırabilirsiniz.

Küme oluşturma hakkında daha fazla bilgi için bkz. [Azure Databricks üzerinde Spark kümesi oluşturma](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-container"></a>Depolama hesabı kapsayıcısı oluştur

Bu bölümde, Azure Databricks çalışma alanında bir not defteri oluşturacak ve ardından depolama hesabını yapılandırmak için kod parçacıklarını çalıştıracaksınız.

1. [Azure portalında](https://portal.azure.com), oluşturduğunuz Azure Databricks çalışma alanına gidin ve sonra **Çalışma Alanını Başlat**’ı seçin.

2. Sol bölmede **Çalışma Alanı**’nı seçin. **Çalışma Alanı** açılır listesinden **Oluştur** > **Not Defteri**’ni seçin.

    ![Databricks’te not defteri oluşturma](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Create notebook in Databricks")

3. **Not Defteri Oluştur** iletişim kutusunda, not defterinizin adını girin. Dil olarak **Scala**’yı seçin ve daha önce oluşturduğunuz Spark kümesini seçin.

    ![Databricks’te not defteri oluşturma](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Create notebook in Databricks")

    **Oluştur**'u seçin.

4. Aşağıdaki kod bloğunu kopyalayıp ilk hücreye yapıştırın, ancak henüz bu kodu çalıştırmayın.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > Bu kod bloğu, OAuth kullanarak Data Lake Gen2 uç noktasına doğrudan erişir, ancak Databricks çalışma alanını Data Lake Storage 2. hesabınıza bağlamak için başka yollar vardır. Örneğin, OAuth kullanarak kapsayıcıyı bağlayabilir veya paylaşılan anahtarla doğrudan erişim kullanabilirsiniz. <br>Bu yaklaşımlara yönelik örnekleri görmek için Azure Databricks Web sitesindeki [Azure Data Lake Storage 2.](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) makalesine bakın.

5. Bu kod bloğunda, bu kod bloğundaki `storage-account-name`, `appID`, `password` ve `tenant-id` yer tutucu değerlerini hizmet sorumlusunu oluştururken topladığınız değerlerle değiştirin. @No__t-0 yer tutucu değerini, kapsayıcıya vermek istediğiniz herhangi bir ad olarak ayarlayın.

    > [!NOTE]
    > Bir üretim ayarında, kimlik doğrulama anahtarınızı Azure Databricks ' de depolamayı göz önünde bulundurun. Ardından, kimlik doğrulama anahtarı yerine kod blosonra bir arama anahtarı ekleyin. Bu hızlı başlangıcı tamamladıktan sonra, bu yaklaşımın örneklerini görmek için Azure Databricks Web sitesindeki [Azure Data Lake Storage 2.](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) makalesine bakın.

6. Bu bloktaki kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

## <a name="ingest-sample-data"></a>Örnek verileri ekleme

Bu bölüme başlamadan önce aşağıdaki önkoşulları tamamlamanız gerekir:

Aşağıdaki kodu bir not defteri hücresine girin:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Hücrede, kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

Şimdi bu değerin altındaki yeni bir hücrede aşağıdaki kodu girin ve köşeli ayraçlar içinde görüntülenen değerleri daha önce kullandığınız değerlerle değiştirin:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Hücrede, kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

## <a name="run-a-spark-sql-job"></a>Spark SQL işi çalıştırma

Verilerde bir Spark SQL işi çalıştırmak için aşağıdaki görevleri gerçekleştirin.

1. **small_radio_json.json** adlı örnek JSON veri dosyasındaki verileri kullanarak geçici tablo oluşturmak için bir SQL deyimi çalıştırın. Aşağıdaki kod parçacığında yer tutucu değerlerini kapsayıcınızın adı ve depolama hesabı adı ile değiştirin. Daha önceden oluşturduğunuz not defterini kullanarak kod parçacığını not defterindeki yeni bir kod hücresine yapıştırın ve sonra SHIFT + ENTER tuşlarına basın.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
    )
    ```

    Komut başarıyla tamamlandıktan sonra, JSON dosyasındaki tüm verileri Databricks kümesinde tablo olarak görüntüleyebilirsiniz.

    `%sql` dili sihirli komutu, not defteri başka bir türde olsa bile not defterinden bir SQL kodu çalıştırmanızı sağlar. Daha fazla bilgi için bkz. [Bir not defterinde dilleri karıştırma](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

2. Çalıştırdığınız sorguyu daha iyi anlamak için örnek JSON verilerinin bir anlık görüntüsüne bakalım. Kod hücresine aşağıdaki kod parçacığını yapıştırın ve **SHIFT + ENTER** tuşuna basın.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Aşağıdaki ekran görüntüsünde gösterildiği gibi bir tablo çıktısı görürsünüz (yalnızca bazı sütunlar gösterilmiştir):

    ![Örnek JSON verileri](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Sample JSON data")

    Diğer ayrıntıların yanı sıra, örnek veriler bir radyo kanalının (sütun adı, **cinsiyet**) hedef kitlesinin cinsiyetini ve aboneliğin ücretsiz mi yoksa ücretli mi (sütun adı, **düzey**) olduğunu yakalar.

4. Bu durumda, bu verilerin her bir cinsiyet, ücretsiz hesaba sahip kullanıcı sayısı ve ücretli hesabı olan abone sayısı için gösterilecek görsel bir açıklamasını oluşturursunuz. Tablo çıktısının alt kısmında bulunan **Çubuk grafik** simgesine ve sonra **Çizim Seçenekleri**’ne tıklayın.

    ![Çubuk grafik oluşturma](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Create bar chart")

5. **Çizimi Özelleştir** menüsünde, değerleri ekran görüntüsünde gösterilen şekilde sürükleyip bırakın.

    ![Çubuk grafiği özelleştirme](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Customize bar chart")

    - **Anahtarlar**’ı **cinsiyet** olarak ayarlayın.
    - **Seri gruplandırmalar**’ı **düzey** olarak ayarlayın.
    - **Değerler**’ı **düzey** olarak ayarlayın.
    - **Toplama**’yı **SAYI** olarak ayarlayın.

6. **Apply (Uygula)** düğmesine tıklayın.

7. Çıktı aşağıdaki ekran görüntüsünde gösterildiği gibi görsel açıklamayı gösterir:

     ![Çubuk grafiği özelleştirme](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Customize bar chart")

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makaleyle işiniz bittiğinde kümeyi sonlandırabilirsiniz. Azure Databricks çalışma alanında **Kümeler**'i seçin ve sonlandırmak istediğiniz kümeyi bulun. Fare imlecini **Eylemler** sütununun altındaki üç noktanın üzerine götürün ve **Sonlandır** simgesini seçin.

![Databricks kümesini durdurma](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Databricks kümesini durdurma")

Otomatik olarak bu kümeyi sonlandırdıysanız, kümeyi oluştururken **\_ @ no__t-2 dakika etkinlik dışı** onay kutusunu seçerek sonlandırın. Bu seçeneği belirlerseniz küme belirtilen zaman boyunca devre dışı olması halinde durdurulur.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Databricks’te bir Spark kümesi oluşturdunuz ve Data Lake Storage 2. Nesil etkin bir depolama hesabındaki verileri kullanarak bir Spark işi çalıştırdınız. Diğer veri kaynaklarından Azure Databricks’e verileri aktarma hakkında bilgi almak için [Spark veri kaynakları](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) bölümüne de bakabilirsiniz. Azure Databricks kullanılarak bir ETL işleminin (verileri ayıklama, dönüştürme ve yükleme) nasıl gerçekleştirileceğini öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
>[Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
