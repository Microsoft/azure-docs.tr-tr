---
title: 'Öğretici: Azure Databricks kullanarak ETL işlemleri yapma'
description: Data Lake Storage 2. verileri Azure Databricks ' dan ayıklama, verileri dönüştürme ve sonra verileri Azure SQL veri ambarı 'na yükleme hakkında bilgi edinin.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8ceb9e940584ae4c41484fe31ce5cb1832bb3400
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605707"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Öğretici: Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme

Bu öğreticide, Azure Databricks kullanarak bir ETL (ayıklama, dönüştürme ve veri yükleme) işlemi gerçekleştirirsiniz. Azure Data Lake Storage 2. verileri Azure Databricks ayıklayın, Azure Databricks verilerde dönüşümler çalıştırın ve dönüştürülmüş verileri Azure SQL veri ambarı 'na yükleyin.

Bu öğreticideki adımlarda, verileri Azure Databricks'e aktarmak üzere Azure Databricks için SQL Veri Ambarı bağlayıcısı kullanılır. Bu bağlayıcı da, Azure Databricks kümesiyle Azure SQL Veri Ambarı arasında aktarılan veriler için geçici depolama alanı olarak Azure Blob Depolama'yı kullanır.

Aşağıdaki şekilde uygulama akışı gösterilmektedir:

![Data Lake Store ve SQL veri ambarı ile Azure Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Data Lake Store ve SQL veri ambarı ile Azure Databricks")

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Azure Databricks bir hizmet oluşturun.
> * Azure Databricks bir Spark kümesi oluşturun.
> * Data Lake Storage 2. hesapta bir dosya sistemi oluşturun.
> * Örnek verileri Azure Data Lake Storage 2. hesabına yükleyin.
> * Hizmet sorumlusu oluşturun.
> * Azure Data Lake Storage 2. hesabından veri ayıklayın.
> * Azure Databricks verileri dönüştürme.
> * Azure SQL veri ambarı 'na veri yükleme.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!Note]
> Bu öğretici **Azure Ücretsiz deneme aboneliği**kullanılarak gerçekleştirilemez.
> Ücretsiz hesabınız varsa, profilinize gidin ve aboneliğinizi **Kullandıkça Öde**ile değiştirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/). Ardından, [harcama limitini kaldırın](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)ve bölgenizdeki vCPU 'lar için [bir kota artışı isteyin](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) . Azure Databricks çalışma alanınızı oluşturduğunuzda, çalışma alanına 14 gün boyunca ücretsiz Premium Azure Databricks DBUs erişimi sağlamak için **deneme (Premium-14 gün ücretsiz DBUs)** fiyatlandırma katmanını seçebilirsiniz.
     
## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce bu görevleri doldurun:

* Azure SQL veri ambarı oluşturun, sunucu düzeyinde bir güvenlik duvarı kuralı oluşturun ve sunucu yöneticisi olarak sunucuya bağlanın. Bkz. [hızlı başlangıç: Azure Portal Azure SQL veri ambarı oluşturma ve sorgulama](../sql-data-warehouse/create-data-warehouse-portal.md).

* Azure SQL veri ambarı için bir ana anahtar oluşturun. Bkz. [veritabanı ana anahtarı oluşturma](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Azure Blob depolama hesabı ve bu hesabın içinde bir kapsayıcı oluşturun. Ayrıca, depolama hesabına erişmek için erişim anahtarını alın. Bkz. [hızlı başlangıç: Azure Portal Blobları karşıya yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-portal.md).

* Azure Data Lake Storage 2. depolama hesabı oluşturun. Bkz. [hızlı başlangıç: Azure Data Lake Storage 2. depolama hesabı oluşturma](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Hizmet sorumlusu oluşturun. Bkz. [nasıl yapılır: Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Söz konusu makaledeki adımları gerçekleştirirken yapmanız gereken birkaç şey vardır.

   * Makalenin [role uygulama atama](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) bölümünde yer aldığı adımları gerçekleştirirken, Data Lake Storage 2. hesabının kapsamındaki **Depolama Blobu veri katılımcısı** rolünü hizmet sorumlusuna atadığınızdan emin olun. Rolü üst kaynak grubuna veya aboneliğine atarsanız, bu rol atamaları depolama hesabına yayana kadar izinlerle ilgili hatalar alırsınız.

      Hizmet sorumlusunu belirli bir dosya veya dizin ile ilişkilendirmek için bir erişim denetim listesi (ACL) kullanmayı tercih ediyorsanız, [Azure Data Lake Storage 2. erişim denetimi](../storage/blobs/data-lake-storage-access-control.md)'ne başvurun.

   * Makalenin [oturum açma için değerleri Al](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) bölümünde bulunan adımları gerçekleştirirken, Kiracı kimliği, uygulama kimliği ve parola değerlerini bir metin dosyasına yapıştırın. Bu kadar yakında ihtiyacınız olacak.

* [Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="gather-the-information-that-you-need"></a>İhtiyaç duyduğunuz bilgileri toplayın

Bu öğreticinin önkoşullarını tamamladığınızdan emin olun.

   Başlamadan önce şu bilgi öğelerine sahip olmanız gerekir:

   : heavy_check_mark: veritabanı adı, veritabanı sunucu adı, Kullanıcı adı ve Azure SQL veri ambarınızın parolası.

   : heavy_check_mark: BLOB depolama hesabınızın erişim anahtarı.

   : heavy_check_mark: Data Lake Storage 2. depolama hesabınızın adı.

   : heavy_check_mark: aboneliğinizin kiracı KIMLIĞI.

   : heavy_check_mark: Azure Active Directory (Azure AD) ile kaydettiğiniz uygulamanın uygulama KIMLIĞI.

   : heavy_check_mark: Azure AD ile kaydettiğiniz uygulamanın kimlik doğrulama anahtarı.

## <a name="create-an-azure-databricks-service"></a>Azure Databricks hizmeti oluşturma

Bu bölümde, Azure portal kullanarak bir Azure Databricks hizmeti oluşturursunuz.

1. Azure portal menüsünde **kaynak oluştur**' u seçin.

    ![Azure portal kaynak oluşturma](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Ardından **analiz** > **Azure Databricks**' ni seçin.

    ![Azure portal üzerinde Azure Databricks oluşturma](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. **Azure Databricks hizmeti**altında, Databricks hizmeti oluşturmak için aşağıdaki değerleri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |**Çalışma alanı adı**     | Databricks çalışma alanınız için bir ad sağlayın.        |
    |**Abonelik**     | Açılan listeden Azure aboneliğinizi seçin.        |
    |**Kaynak grubu**     | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. Kaynak grubu, bir Azure çözümü için ilgili kaynakları bir arada tutan kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../azure-resource-manager/resource-group-overview.md). |
    |**Konum**     | **Batı ABD 2**'yi seçin.  Kullanılabilir diğer bölgeler için bkz. [Bölgeye göre kullanılabilir Azure hizmetleri](https://azure.microsoft.com/regions/services/).      |
    |**Fiyatlandırma Katmanı**     |  **Standart**' ı seçin.     |

3. Hesabın oluşturulması birkaç dakika sürer. İşlem durumunu izlemek için üstteki ilerleme çubuğunu görüntüleyin.

4. **Panoya sabitle**’yi ve sonra **Oluştur**’u seçin.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Azure Databricks’te Spark kümesi oluşturma

1. Azure portal, oluşturduğunuz Databricks hizmetine gidin ve **çalışma alanını Başlat**' ı seçin.

2. Azure Databricks portalına yönlendirilirsiniz. Portaldan **Küme**’yi seçin.

    ![Azure 'da databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Azure 'da databricks")

3. **Yeni küme** sayfasında, bir küme oluşturmak için değerleri girin.

    ![Azure 'da Databricks Spark kümesi oluşturma](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Azure 'da Databricks Spark kümesi oluşturma")

4. Aşağıdaki alanlara değerleri girin ve diğer alanlar için varsayılan değerleri kabul edin:

    * Küme için bir ad girin.

    * **\_sonra \_ dakika etkinliksizlik** onay kutusunu seçtiğinizden emin olun. Küme kullanılmıyorsa, kümeyi sonlandırmak için bir süre (dakika cinsinden) belirtin.

    * **Küme oluştur**’u seçin. Küme çalıştırıldıktan sonra, kümeye Not defterleri ekleyebilir ve Spark işleri çalıştırabilirsiniz.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage 2. hesapta bir dosya sistemi oluşturma

Bu bölümde, Azure Databricks çalışma alanında bir not defteri oluşturun ve ardından Depolama hesabını yapılandırmak için kod parçacıklarını çalıştırın

1. [Azure Portal](https://portal.azure.com), oluşturduğunuz Azure Databricks hizmetine gidin ve **çalışma alanını Başlat**' ı seçin.

2. Sol tarafta **çalışma alanı**' nı seçin. **Çalışma Alanı** açılır listesinden **Oluştur** > **Not Defteri**’ni seçin.

    ![Databricks 'te Not defteri oluşturma](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Databricks 'te Not defteri oluşturma")

3. **Not Defteri Oluştur** iletişim kutusunda, not defterinizin adını girin. Dil olarak **Scala**’yı seçin ve daha önce oluşturduğunuz Spark kümesini seçin.

    ![Databricks içindeki bir not defterinin ayrıntılarını sağlama](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Databricks içindeki bir not defterinin ayrıntılarını sağlama")

4. **Oluştur**'u seçin.

5. Aşağıdaki kod bloğu, Spark oturumunda erişilen herhangi bir ADLS Gen 2 hesabı için varsayılan hizmet sorumlusu kimlik bilgilerini ayarlar. İkinci kod bloğu, belirli bir ADLS Gen 2 hesabı için kimlik bilgilerini belirtmek üzere hesap adını ayara ekler.  Kod bloğunu kopyalayıp Azure Databricks not defterinizin ilk hücresine yapıştırın.

   **Oturum yapılandırması**

   ```scala
   val appID = "<appID>"
   val password = "<password>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Hesap yapılandırması**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val password = "<password>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + password + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. Bu kod bloğunda, Bu öğreticinin önkoşullarını tamamlarken, bu kod bloğundaki `<app-id>`, `<password>`, `<tenant-id>`ve `<storage-account-name>` yer tutucu değerlerini topladığınız değerlerle değiştirin. `<file-system-name>` yer tutucu değerini, dosya sistemine vermek istediğiniz herhangi bir adla değiştirin.

   * `<app-id>`ve `<password>`, hizmet sorumlusu oluşturmanın bir parçası olarak Active Directory ile kaydettiğiniz uygulamadan alınır.

   * `<tenant-id>` aboneliğinizden.

   * `<storage-account-name>`, Azure Data Lake Storage 2. depolama hesabınızın adıdır.

7. Bu bloktaki kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage 2. hesaba örnek verileri alma

Bu bölüme başlamadan önce aşağıdaki önkoşulları tamamlamanız gerekir:

Aşağıdaki kodu bir not defteri hücresine girin:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Hücrede, kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

Şimdi bu değerin altındaki yeni bir hücrede aşağıdaki kodu girin ve köşeli ayraçlar içinde görüntülenen değerleri daha önce kullandığınız değerlerle değiştirin:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

Hücrede, kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage 2. hesabından veri Ayıkla

1. Örnek json dosyasını artık Azure Databricks bir veri çerçevesi olarak yükleyebilirsiniz. Aşağıdaki kodu yeni bir hücreye yapıştırın. Köşeli ayraçlar içinde gösterilen yer tutucuları değerlerinizle değiştirin.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```
2. Bu bloktaki kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

3. Veri çerçevesinin içeriğini görmek için aşağıdaki kodu çalıştırın:

    ```scala
    df.show()
    ```
   Aşağıdaki kod parçacığına benzer bir çıkış görürsünüz:

   ```bash
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Artık verileri Azure Data Lake Storage Gen2'den Azure Databricks'e ayıkladınız.

## <a name="transform-data-in-azure-databricks"></a>Azure Databricks'te verileri dönüştürme

Ham örnek Data **small_radio_json. JSON** dosyası bir radyo istasyonu için izleyiciyi yakalar ve çeşitli sütunlara sahiptir. Bu bölümde, verileri yalnızca veri kümesinden belirli sütunları almak üzere dönüştürürler.

1. İlk olarak, oluşturduğunuz veri çerçevesinin yalnızca **FirstName**, **LastName**, **cinsiyet**, **Location**ve **Level** sütunlarını alın.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Aşağıdaki kod parçacığında gösterildiği gibi çıkış alırsınız:

   ```bash
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. Bu verilerde başka dönüştürmeler de yaparak **level** sütununun adını **subscription_type** olarak değiştirebilirsiniz.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   Aşağıdaki kod parçacığında gösterildiği gibi çıkış alırsınız.

   ```bash
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Veri Ambarı’na veri yükleme

Bu bölümde, dönüştürülen verileri Azure SQL Veri Ambarı'na yüklersiniz. Bir veri çerçevesini bir SQL veri ambarına tablo olarak doğrudan yüklemek için Azure Databricks için Azure SQL veri ambarı bağlayıcısını kullanırsınız.

Daha önce belirtildiği gibi, SQL Data Warehouse Connector, Azure Databricks ile Azure SQL veri ambarı arasında veri yüklemek için geçici depolama olarak Azure Blob depolamayı kullanır. Bu nedenle, depolama hesabına bağlanmak için kullanılacak yapılandırmayı sağlayarak başlarsınız. Bu makalenin önkoşulların bir parçası olarak zaten hesabı oluşturmuş olmanız gerekir.

1. Azure Databricks'ten Azure Depolama hesabına erişmek için yapılandırmayı sağlayın.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Verileri Azure Databricks ve Azure SQL veri ambarı arasında taşırken kullanılacak geçici bir klasör belirtin.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Aşağıdaki kod parçacığını çalıştırarak Azure Blob depolama erişim anahtarlarını yapılandırmada depolayın. Bu eylem, erişim anahtarını not defterinde düz metin olarak saklamanız gerekmez.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Azure SQL Veri Ambarı örneğine bağlanmak için değerleri sağlayın. Önkoşul olarak bir SQL veri ambarı oluşturmuş olmanız gerekir. **DWServer**için tam sunucu adını kullanın. Örneğin, `<servername>.database.windows.net`.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Dönüştürülmüş veri çerçevesini, **renamedColumnsDF**, bir SQL veri ambarına tablo olarak yüklemek için aşağıdaki kod parçacığını çalıştırın. Bu kod parçacığı SQL veritabanında **SampleTable** adlı bir tablo oluşturur.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Bu örnek, SQL veri ambarı 'nın bir erişim anahtarı kullanarak blob depolamadan veriye erişmesine neden olan `forward_spark_azure_storage_credentials` bayrağını kullanır. Bu, kimlik doğrulama için desteklenen tek yöntemdir.
   >
   > Azure Blob depolama alanı sanal ağları seçme kısıtlanmışsa, SQL veri ambarı [erişim tuşları yerine yönetilen hizmet kimliği](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)gerektirir. Bu, "Bu istek bu işlemi gerçekleştirmek için yetkilendirilmemiş." hatasına neden olur.

6. SQL veritabanına bağlanın ve **SampleTable**adlı bir veritabanı gördiğinizi doğrulayın.

   ![Örnek tabloyu doğrulama](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Örnek tabloyu doğrula")

7. Tablonun içindekileri doğrulamak için bir seçme sorgusu çalıştırın. Tablo, **renamedColumnsDF** dataframe ile aynı verilere sahip olmalıdır.

    ![Örnek tablo içeriğini doğrulama](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Örnek tablo içeriğini doğrulama")

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi tamamladıktan sonra kümeyi sonlandırabilirsiniz. Azure Databricks çalışma alanından, sol taraftaki **kümeler** ' ı seçin. Kümenin sonlandırılması için, **Eylemler**altında üç nokta (...) üzerine gelin ve **Sonlandır** simgesini seçin.

![Databricks kümesini durdurma](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks kümesini durdurma")

Kümeyi el ile sonlandırmazsanız, kümeyi oluştururken **\_\_ dakika Etkinliksiz** onay kutusunu seçtiğiniz takdirde otomatik olarak duraklar. Böyle bir durumda, belirtilen süre boyunca etkin olmadığında küme otomatik olarak duraklar.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure Databricks hizmeti oluşturma
> * Azure Databricks’te Spark kümesi oluşturma
> * Azure Databricks’te not defteri oluşturma
> * Data Lake Storage 2. hesabından veri ayıklama
> * Azure Databricks'te verileri dönüştürme
> * Azure SQL Veri Ambarı’na veri yükleme

Azure Event Hubs kullanarak Azure Databricks'e gerçek zamanlı veri akışı yapmayı öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
>[Event Hubs kullanarak Azure Databricks’e veri akışı sağlama](databricks-stream-from-eventhubs.md)
