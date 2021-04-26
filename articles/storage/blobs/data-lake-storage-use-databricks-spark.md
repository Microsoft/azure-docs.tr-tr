---
title: 'Öğretici: Azure Data Lake Storage 2., Azure Databricks & Spark | Microsoft Docs'
description: Bu öğreticide, bir Azure Data Lake Storage 2. depolama hesabındaki verilere erişmek için bir Azure Databricks kümesinde Spark sorgularının nasıl çalıştırılacağı gösterilmektedir.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: 232e28d3cc8b0bc7427dd035d51743f623e54259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564312"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Öğretici: Azure Data Lake Storage 2., Azure Databricks & Spark

Bu öğreticide, Azure Databricks kümenizi Azure Data Lake Storage 2. etkin olan bir Azure depolama hesabında depolanan verilere nasıl bağlayabilmeniz gösterilmektedir. Bu bağlantı, verilerinizde kümenizdeki sorguları ve Analizi yerel olarak çalıştırmanızı sağlar.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Databricks kümesi oluşturma
> * Yapılandırılmamış verileri bir depolama hesabına alma
> * Blob depolamada verileriniz üzerinde analiz çalıştırma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Azure Data Lake Storage 2. hesabı oluşturun.

  [Azure Data Lake Storage 2. için kullanılacak depolama hesabı oluşturma](create-data-lake-storage-account.md)konusuna bakın.

* Kullanıcı hesabınızda, [Depolama Blobu veri katılımcısı rolü](../common/storage-auth-aad-rbac-portal.md) atanmış olduğundan emin olun.

* AzCopy ile v10 arasındaki 'i yükler. Bkz. [AzCopy ile v10 arasındaki ile veri aktarma](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Hizmet sorumlusu oluşturun. Bkz. [nasıl yapılır: Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](../../active-directory/develop/howto-create-service-principal-portal.md).

  Söz konusu makaledeki adımları gerçekleştirirken yapmanız gereken birkaç şey vardır.

  : heavy_check_mark: makalenin [role uygulamayı atama](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) bölümünde bulunan adımlar gerçekleştirilirken, **Depolama Blobu veri katılımcısı** rolünü hizmet sorumlusuna atadığınızdan emin olun.

  > [!IMPORTANT]
  > Rolü Data Lake Storage 2. depolama hesabının kapsamına atadığınızdan emin olun. Üst kaynak grubuna veya aboneliğine bir rol atayabilirsiniz, ancak bu rol atamaları depolama hesabına yayana kadar izinlerle ilgili hatalar alırsınız.

  : heavy_check_mark: makalenin [oturum açmak için değerleri Al](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) bölümünde bulunan adımları gerçekleştirirken, Kiracı kimliği, uygulama kimliği ve istemci gizli değerlerini bir metin dosyasına yapıştırın. Bu kadar yakında ihtiyacınız olacak.

### <a name="download-the-flight-data"></a>Uçuş verilerini indirme

Bu öğretici, bir ETL işleminin nasıl gerçekleştirileceğini göstermek için, taşıma Istatistikleri bürolarından uçuş verilerini kullanır. Öğreticiyi tamamlayabilmeniz için bu verileri indirmeniz gerekir.

1. [Araştırma ve yenilikçi teknoloji yönetimi, nakliye Istatistikleri Bürosu '](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ)na gidin.

2. Tüm veri alanlarını seçmek için **önceden daraltılmış dosya** onay kutusunu seçin.

3. **İndir** düğmesini seçin ve sonuçları bilgisayarınıza kaydedin. 

4. Daraltılmış dosyanın içeriğini açın ve dosyanın adını ve yolunu bir yere ayıklayın. Daha sonraki bir adımda bu bilgilere ihtiyacınız vardır.

## <a name="create-an-azure-databricks-service"></a>Azure Databricks hizmeti oluşturma

Bu bölümde, Azure portal kullanarak bir Azure Databricks hizmeti oluşturursunuz.

1. Azure Portal, **kaynak**  >  **Analizi** oluştur  >  **Azure Databricks**' u seçin.

    ![Azure portal databricks](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Azure portal databricks")

2. **Azure Databricks hizmeti** altında, Databricks hizmeti oluşturmak için aşağıdaki değerleri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |**Çalışma alanı adı**     | Databricks çalışma alanınız için bir ad sağlayın.  |
    |**Abonelik**     | Açılan listeden Azure aboneliğinizi seçin.        |
    |**Kaynak grubu**     | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. Kaynak grubu, bir Azure çözümüne ilişkin kaynakları tutan bir kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../../azure-resource-manager/management/overview.md). |
    |**Konum**     | **Batı ABD 2**'yi seçin. Kullanılabilir diğer bölgeler için bkz. [Bölgeye göre kullanılabilir Azure hizmetleri](https://azure.microsoft.com/regions/services/).       |
    |**Fiyatlandırma Katmanı**     |  **Standart**' ı seçin.     |

    ![Azure Databricks çalışma alanı oluşturma](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Azure Databricks hizmeti oluşturma")

3. Hesabın oluşturulması birkaç dakika sürer. İşlem durumunu izlemek için üstteki ilerleme çubuğunu görüntüleyin.

4. **Panoya sabitle**’yi ve sonra **Oluştur**’u seçin.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Azure Databricks’te Spark kümesi oluşturma

1. Azure portal, oluşturduğunuz Databricks hizmetine gidin ve **çalışma alanını Başlat**' ı seçin.

2. Azure Databricks portalına yönlendirilirsiniz. Portaldan **Küme**’yi seçin.

    ![Azure 'da databricks](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Azure 'da databricks")

3. **Yeni küme** sayfasında, bir küme oluşturmak için değerleri girin.

    ![Azure 'da Databricks Spark kümesi oluşturma](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Azure 'da Databricks Spark kümesi oluşturma")

    Aşağıdaki alanlara değerleri girin ve diğer alanlar için varsayılan değerleri kabul edin:

    - Küme için bir ad girin.
     
    - **120 dakika işlem yapılmadığında sonlandır** onay kutusunu seçtiğinizden emin olun. Küme kullanılmazsa kümenin sonlandırılması için biz süre (dakika cinsinden) belirtin.

4. **Küme oluştur**' u seçin. Küme çalıştırıldıktan sonra, kümeye Not defterleri ekleyebilir ve Spark işleri çalıştırabilirsiniz.

## <a name="ingest-data"></a>Veriyi çekme

### <a name="copy-source-data-into-the-storage-account"></a>Kaynak verilerini depolama hesabına kopyalama

*. Csv* dosyanızdaki verileri Data Lake Storage 2. hesabınıza kopyalamak Için AzCopy kullanın.

1. Bir komut istemi penceresi açın ve depolama hesabınızda oturum açmak için aşağıdaki komutu girin.

   ```bash
   azcopy login
   ```

   Kullanıcı hesabınızın kimliğini doğrulamak için komut istemi penceresinde görüntülenen yönergeleri izleyin.

2. *. Csv* hesabındaki verileri kopyalamak için aşağıdaki komutu girin.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * `<csv-folder-path>`Yer tutucu değerini *. csv* dosyasının yoluyla değiştirin.

   * `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

   * `<container-name>`Yer tutucusunu, Depolama hesabınızdaki bir kapsayıcının adıyla değiştirin.

## <a name="create-a-container-and-mount-it"></a>Kapsayıcı oluşturma ve bağlama

Bu bölümde, depolama hesabınızda bir kapsayıcı ve bir klasör oluşturacaksınız.

1. [Azure Portal](https://portal.azure.com), oluşturduğunuz Azure Databricks hizmetine gidin ve **çalışma alanını Başlat**' ı seçin.

2. Sol tarafta **çalışma alanı**' nı seçin. **Çalışma Alanı** açılır listesinden **Oluştur** > **Not Defteri**’ni seçin.

    ![Databricks 'te Not defteri oluşturma](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Databricks 'te Not defteri oluşturma")

3. **Not Defteri Oluştur** iletişim kutusunda, not defterinizin adını girin. Dil olarak **Python** ' ı seçin ve daha önce oluşturduğunuz Spark kümesini seçin.

4. **Oluştur**’u seçin.

5. Aşağıdaki kod bloğunu kopyalayıp ilk hücreye yapıştırın, ancak henüz bu kodu çalıştırmayın.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. Bu kod bloğunda, bu `appId` `clientSecret` Kod bloğundaki,, `tenant` ve `storage-account-name` yer tutucu değerlerini, Bu öğreticinin önkoşullarını tamamlarken topladığınız değerlerle değiştirin. `container-name`Yer tutucu değerini kapsayıcının adıyla değiştirin.

19. Bu bloktaki kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

   Daha sonra komutlara ekleyebileceğiniz bu not defterini açık tutun.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Databricks Not Defteri'ni kullanarak CSV'yi Parquet biçimine dönüştürme

Daha önce oluşturduğunuz not defterinde, yeni bir hücre ekleyin ve aşağıdaki kodu bu hücreye yapıştırın. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Verileri inceleme

Yeni bir hücrede, AzCopy aracılığıyla karşıya yüklenen CSV dosyalarının listesini almak için aşağıdaki kodu yapıştırın.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Yeni bir dosya oluşturmak ve *parquet/flights* klasöründeki dosyaları listelemek için şu betiği çalıştırın:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Bu kod örnekleriyle Data Lake Storage 2. Nesil etkin bir depolama hesabında depolanan verileri kullanarak HDFS’nin hiyerarşik özelliklerini keşfettiniz.

## <a name="query-the-data"></a>Verileri sorgulama

Bir sonraki adımda depolama hesabınıza yüklediğiniz verileri sorgulamaya başlayabilirsiniz. Aşağıdaki kod bloklarını **Cmd 1** bölümüne girin ve **Cmd + Enter** tuşlarına basarak Python betiğini çalıştırın.

Veri kaynaklarınız için veri çerçeveleri oluşturmak için aşağıdaki betiği çalıştırın:

* `<csv-folder-path>`Yer tutucu değerini *. csv* dosyasının yoluyla değiştirin.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Verilerin bazı temel analiz sorgularını çalıştırmak için bu betiği girin.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve tüm ilgili kaynakları silin. Bunu yapmak için depolama hesabına ait kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure HDInsight üzerinde Apache Hive kullanarak verileri ayıklama, dönüştürme ve yükleme](data-lake-storage-tutorial-extract-transform-load-hive.md)
