---
title: 'Öğretici: Azure Databricks Delta tablosunu güncelleştirmek için Data Lake Capture modelini uygulama | Microsoft Docs'
description: Bu öğreticide, Azure DataLake Storage Gen2 'de depolanan bir tabloya veri satırları eklemek için bir Event Grid aboneliği, bir Azure Işlevi ve bir Azure Databricks işinin nasıl kullanılacağı gösterilmektedir.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 03a07e70c967f92fe5dcc7c951aeea299b050405
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71326985"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Öğretici: bir Databricks Delta tablosunu güncelleştirmek için Data Lake Capture modelini uygulama

Bu öğreticide, hiyerarşik ad alanı olan bir depolama hesabındaki olayların nasıl işleneceği gösterilmektedir.

Bir kullanıcının bir Databricks Delta tablosunu, bir satış siparişi açıklayan bir virgülle ayrılmış değerler (CSV) dosyasını karşıya yükleyerek doldurmasına olanak sağlayan küçük bir çözüm oluşturacaksınız. Bu çözümü, Azure Databricks bir Event Grid aboneliği, bir Azure Işlevi ve bir [işi](https://docs.azuredatabricks.net/user-guide/jobs.html) birbirine bağlayarak oluşturacaksınız.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Azure Işlevi çağıran Event Grid abonelik oluşturun.
> * Bir olaydan bildirim alan ve sonra Azure Databricks işi çalıştıran bir Azure Işlevi oluşturun.
> * Depolama hesabında bulunan bir Databricks Delta tablosuna bir müşteri siparişi ekleyen bir Databricks işi oluşturun.

Bu çözümü, Azure Databricks çalışma alanıyla başlayarak ters sırada oluşturacağız.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* Hiyerarşik ad alanı (Azure Data Lake Storage 2.) olan bir depolama hesabı oluşturun. Bu öğretici `contosoorders` adlı bir depolama hesabı kullanır. Kullanıcı hesabınızda, [Depolama Blobu veri katılımcısı rolü](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) atanmış olduğundan emin olun.

  Bkz. [Azure Data Lake Storage 2. hesap oluşturma](data-lake-storage-quickstart-create-account.md).

* Hizmet sorumlusu oluşturun. Bkz. [nasıl yapılır: Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Söz konusu makaledeki adımları gerçekleştirirken yapmanız gereken birkaç şey vardır.

  : heavy_check_mark: makalenin [bir role uygulamayı atama](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) bölümündeki adımları gerçekleştirirken, **Depolama Blobu veri katılımcısı** rolünü hizmet sorumlusuna atadığınızdan emin olun.

  > [!IMPORTANT]
  > Rolü Data Lake Storage 2. depolama hesabının kapsamına atadığınızdan emin olun. Üst kaynak grubuna veya aboneliğine bir rol atayabilirsiniz, ancak bu rol atamaları depolama hesabına yayana kadar izinlerle ilgili hatalar alırsınız.

  : heavy_check_mark: makalenin [oturum açmak için değerleri Al](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) bölümünde bulunan adımları gerçekleştirirken, Kiracı kimliği, uygulama kimliği ve parola değerlerini bir metin dosyasına yapıştırın. Bu değerlere daha sonra ihtiyacınız olacak.

## <a name="create-a-sales-order"></a>Satış siparişi oluşturma

İlk olarak, satış siparişi açıklayan bir CSV dosyası oluşturun ve ardından bu dosyayı depolama hesabına yükleyin. Daha sonra, Databricks Delta tablolarımızda ilk satırı doldurmak için bu dosyadaki verileri kullanacaksınız.

1. Azure Depolama Gezgini açın. Ardından depolama hesabınıza gidin ve **BLOB kapsayıcıları** bölümünde **veri**adlı yeni bir kapsayıcı oluşturun.

   ![veri klasörü](./media/data-lake-storage-events/data-container.png "veri klasörü")

   Depolama Gezgini kullanma hakkında daha fazla bilgi için bkz. [bir Azure Data Lake Storage 2. hesabındaki verileri yönetmek için Azure Depolama Gezgini kullanma](data-lake-storage-explorer.md).

2. **Veri** kapsayıcısında, **giriş**adlı bir klasör oluşturun.

3. Aşağıdaki metni bir metin düzenleyicisine yapıştırın.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Bu dosyayı yerel bilgisayarınıza kaydedin ve **Data. csv**adına verin.

5. Depolama Gezgini, bu dosyayı **giriş** klasörüne yükleyin.  

## <a name="create-a-job-in-azure-databricks"></a>Azure Databricks iş oluşturma

Bu bölümde, şu görevleri gerçekleştirirsiniz:

* Azure Databricks çalışma alanı oluşturun.
* Bir not defteri oluşturun.
* Bir Databricks Delta tablosu oluşturun ve doldurun.
* Databricks Delta tablosuna satır ekleyen kodu ekleyin.
* Iş oluşturun.

### <a name="create-an-azure-databricks-workspace"></a>Azure Databricks çalışma alanı oluşturma

Bu bölümde Azure portalını kullanarak bir Azure Databricks çalışma alanı oluşturursunuz.

1. Azure portalında **Kaynak oluşturun** > **Analiz** > **Azure Databricks**'i seçin.

    ![Azure portal databricks](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Azure portal databricks")

2. **Azure Databricks Hizmeti** bölümünde, Databricks çalışma alanı oluşturmak için değerler sağlayın.

    ![Azure Databricks çalışma alanı oluşturma](./media/data-lake-storage-events/new-databricks-service.png "Azure Databricks çalışma alanı oluşturma")

    Çalışma alanının oluşturulması birkaç dakika sürer. İşlem durumunu izlemek için üstteki ilerleme çubuğunu görüntüleyin.

### <a name="create-a-spark-cluster-in-databricks"></a>Databricks’te Spark kümesi oluşturma

1. [Azure Portal](https://portal.azure.com), oluşturduğunuz Azure Databricks çalışma alanına gidin ve sonra **çalışma alanını Başlat**' ı seçin.

2. Azure Databricks portalına yönlendirilirsiniz. Portaldan **Yeni** > **Küme**'yi seçin.

    ![Azure 'da databricks](./media/data-lake-storage-events/databricks-on-azure.png "Azure 'da databricks")

3. **Yeni küme** sayfasında, bir küme oluşturmak için değerleri girin.

    ![Azure 'da Databricks Spark kümesi oluşturma](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Azure 'da Databricks Spark kümesi oluşturma")

    Aşağıdakiler dışında diğer tüm varsayılan değerleri kabul edin:

    * Küme için bir ad girin.
    * **120 dakika işlem yapılmadığında sonlandır** onay kutusunu seçtiğinizden emin olun. Küme kullanılmazsa kümenin sonlandırılması için biz süre (dakika cinsinden) belirtin.

4. **Küme oluştur**’u seçin. Küme çalışmaya başladıktan sonra kümeye not defterleri ekleyebilir ve Spark işleri çalıştırabilirsiniz.

Küme oluşturma hakkında daha fazla bilgi için bkz. [Azure Databricks üzerinde Spark kümesi oluşturma](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

### <a name="create-a-notebook"></a>Not defteri oluşturma

1. Sol bölmede **Çalışma Alanı**’nı seçin. **Çalışma Alanı** açılır listesinden **Oluştur** > **Not Defteri**’ni seçin.

    ![Databricks 'te Not defteri oluşturma](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Databricks 'te Not defteri oluşturma")

2. **Not Defteri Oluştur** iletişim kutusunda, not defterinizin adını girin. Dil olarak **Python** ' ı seçin ve daha önce oluşturduğunuz Spark kümesini seçin.

    ![Databricks 'te Not defteri oluşturma](./media/data-lake-storage-events/new-databricks-notebook.png "Databricks 'te Not defteri oluşturma")

    **Oluştur**'u seçin.

### <a name="create-and-populate-a-databricks-delta-table"></a>Databricks Delta tablosu oluşturma ve doldurma

1. Oluşturduğunuz not defterinde, aşağıdaki kod bloğunu kopyalayın ve ilk hücreye yapıştırın, ancak henüz bu kodu çalıştırmayın.  

   Bu kod bloğundaki `appId`, `password`, `tenant` yer tutucu değerlerini, Bu öğreticinin önkoşullarını tamamlarken topladığınız değerlerle değiştirin.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Bu kod, **source_file**adlı bir pencere öğesi oluşturur. Daha sonra, bu kodu çağıran ve bu pencere öğesine bir dosya yolu geçiren bir Azure Işlevi oluşturacaksınız.  Bu kod ayrıca depolama hesabıyla hizmet sorumlunuzu doğrular ve diğer hücrelerde kullanacağınız bazı değişkenler oluşturur.

    > [!NOTE]
    > Bir üretim ayarında, kimlik doğrulama anahtarınızı Azure Databricks ' de depolamayı göz önünde bulundurun. Ardından, kimlik doğrulama anahtarı yerine kod blosonra bir arama anahtarı ekleyin. <br><br>Örneğin, bu kod satırını kullanmak yerine şu kod satırını kullanın: `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`. <br><br>Bu Öğreticiyi tamamladıktan sonra, bu yaklaşımın örneklerini görmek için Azure Databricks Web sitesindeki [Azure Data Lake Storage 2.](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) makalesine bakın.

2. Bu bloktaki kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

3. Aşağıdaki kod bloğunu kopyalayıp farklı bir hücreye yapıştırın ve sonra bu bloktaki kodu çalıştırmak için **SHIFT + enter** tuşlarına basın.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Bu kod, depolama hesabınızda Databricks Delta tablosunu oluşturur ve daha önce karşıya yüklediğiniz CSV dosyasından bazı ilk verileri yükler.

4. Bu kod bloğu başarıyla çalıştıktan sonra bu kod bloğunu Not defterinizden kaldırın.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Databricks Delta tablosuna satır ekleyen kodu ekleyin

1. Aşağıdaki kod bloğunu kopyalayıp farklı bir hücreye yapıştırın, ancak bu hücreyi çalıştırmayın.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Bu kod, bir CSV dosyasındaki verileri kullanarak geçici bir tablo görünümüne veri ekler. Bu CSV dosyasının yolu, önceki bir adımda oluşturduğunuz giriş pencere öğesinden gelir.

2. Geçici tablo görünümünün içeriğini Databricks Delta tablosu ile birleştirmek için aşağıdaki kodu ekleyin.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Iş oluşturma

Daha önce oluşturduğunuz Not defterini çalıştıran bir Iş oluşturun. Daha sonra, bir olay ortaya çıktığında bu işi çalıştıran bir Azure Işlevi oluşturacaksınız.

1. **İşler**' e tıklayın.

2. **İşler** sayfasında, **iş oluştur**' a tıklayın.

3. İşe bir ad verin ve sonra `upsert-order-data` çalışma kitabını seçin.

   ![İş oluşturma](./media/data-lake-storage-events/create-spark-job.png "Bir iş oluşturma")

## <a name="create-an-azure-function"></a>Azure İşlevi oluşturma

Işi çalıştıran bir Azure Işlevi oluşturun.

1. Databricks çalışma alanının üst köşesinde kişiler simgesini ve ardından **Kullanıcı ayarları**' nı seçin.

   ![Hesabı yönetme](./media/data-lake-storage-events/generate-token.png "Kullanıcı ayarları")

2. **Yeni belirteç oluştur** düğmesine tıklayın ve ardından **Oluştur** düğmesine tıklayın.

   Belirteci güvenli bir yere kopyalamadığınızdan emin olun. Azure Işlevinizde bu belirtecin, Işi çalıştırabilmesi için Databricks ile kimlik doğrulaması yapması gerekir.
  
3. Azure portal sol üst köşesinde bulunan **kaynak oluştur** düğmesini seçin ve ardından **işlem > işlev uygulaması**' yı seçin.

   ![Azure işlevi oluşturma](./media/data-lake-storage-events/function-app-create-flow.png "Azure işlevi oluşturma")

4. İşlev Uygulaması **Oluştur** sayfasında, çalışma zamanı yığını Için **.NET Core** ' u seçtiğinizden emin olun ve bir Application Insights örneği yapılandırdığınızdan emin olun.

   ![İşlev uygulamasını yapılandırma](./media/data-lake-storage-events/new-function-app.png "İşlev uygulamasını yapılandırma")

5. İşlev Uygulaması **genel bakış** sayfasında **yapılandırma**' ya tıklayın.

   ![İşlev uygulamasını yapılandırma](./media/data-lake-storage-events/configure-function-app.png "İşlev uygulamasını yapılandırma")

6. **Uygulama ayarları** sayfasında, her bir ayarı eklemek için **Yeni uygulama ayarı** düğmesini seçin.

   ![Yapılandırma ayarı Ekle](./media/data-lake-storage-events/add-application-setting.png "Yapılandırma ayarı Ekle")

   Aşağıdaki ayarları ekleyin:

   |Ayar adı | Değer |
   |----|----|
   |**DBX_INSTANCE**| Databricks çalışma alanınızın bölgesi. Örneğin, `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Daha önce oluşturduğunuz kişisel erişim belirteci. |
   |**DBX_JOB_ID**|Çalışan işin tanımlayıcısı. Bu durumda, bu değer `1`.|
7. İşlev uygulamasının genel bakış sayfasında, **yeni işlev** düğmesine tıklayın.

   ![Yeni işlev](./media/data-lake-storage-events/new-function.png "Yeni işlev")

8. **Azure Event Grid tetikleyiciyi**seçin.

   İstenirse **Microsoft. Azure. WebJobs. Extensions. EventGrid** uzantısını yükleyebilirsiniz. Yüklemek gerekirse, işlevi oluşturmak için **Azure Event Grid tetikleyiciyi** yeniden seçmeniz gerekir.

   **Yeni işlev** bölmesi görüntülenir.

9. **Yeni işlev** bölmesinde, **UpsertOrder**Işlevini adlandırın ve ardından **Oluştur** düğmesine tıklayın.

10. Kod dosyasının içeriğini bu kodla değiştirin ve **Kaydet** düğmesine tıklayın:

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Bu kod, yükseltilen depolama olayı hakkında bilgi ayrıştırır ve sonra olayı tetikleyen dosyanın URL 'si ile bir istek iletisi oluşturur. İleti bir parçası olarak, işlev daha önce oluşturduğunuz **source_file** pencere öğesine bir değer geçirir. işlev kodu, iletiyi Databricks Işine gönderir ve daha önce kimlik doğrulaması olarak edindiğiniz belirteci kullanır.

## <a name="create-an-event-grid-subscription"></a>Event Grid aboneliği oluşturma

Bu bölümde, depolama hesabına dosyalar yüklendiğinde Azure Işlevini çağıran bir Event Grid aboneliği oluşturacaksınız.

1. İşlev kodu sayfasında **Event Grid abonelik Ekle** düğmesine tıklayın.

   ![Yeni olay aboneliği](./media/data-lake-storage-events/new-event-subscription.png "Yeni olay aboneliği")

2. **Olay aboneliği oluştur** sayfasında, aboneliği adlandırın ve ardından depolama hesabınızı seçmek için sayfadaki alanları kullanın.

   ![Yeni olay aboneliği](./media/data-lake-storage-events/new-event-subscription-2.png "Yeni olay aboneliği")

3. **Olay türlerine Filtre Uygula** aşağı açılan listesinde, **oluşturulan blobu**ve **BLOB silinen** olayları seçin ve ardından **Oluştur** düğmesine tıklayın.

## <a name="test-the-event-grid-subscription"></a>Event Grid aboneliğini test etme

1. @No__t_0 adlı bir dosya oluşturun, aşağıdaki bilgileri bu dosyaya yapıştırın ve yerel bilgisayarınıza kaydedin.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Depolama Gezgini, bu dosyayı depolama hesabınızın **giriş** klasörüne yükleyin.

   Bir dosyayı karşıya yüklemek, **Microsoft. Storage. BlobCreated** olayını oluşturur. Event Grid tüm abonelere bu olaya bildirir. Bu durumda, Azure Işlevi tek abonemdir. Azure Işlevi, hangi olayın oluştuğunu belirleyen olay parametrelerini ayrıştırır. Daha sonra dosyanın URL 'sini Databricks Işine geçirir. Databricks Işi dosyayı okur ve depolama hesabınızda bulunan Databricks Delta tablosuna bir satır ekler.

3. İşin başarılı olup olmadığını denetlemek için, databricks çalışma alanınızı açın, **işler** düğmesine tıklayın ve ardından işinizi açın.

4. İş sayfasını açmak için işi seçin.

   ![Spark işi](./media/data-lake-storage-events/spark-job.png "Spark işi")

   İş tamamlandığında, bir tamamlanma durumu görürsünüz.

   ![İş başarıyla tamamlandı](./media/data-lake-storage-events/spark-job-completed.png "İş başarıyla tamamlandı")

5. Yeni bir çalışma kitabı hücresinde, güncelleştirilmiş Delta tablosunu görmek için bu sorguyu bir hücrede çalıştırın.

   ```
   %sql select * from customer_data
   ```

   Döndürülen tabloda en son kayıt gösterilmektedir.

   ![Tabloda en son kayıt görünür](./media/data-lake-storage-events/final_query.png "Tabloda en son kayıt görünür")

6. Bu kaydı güncelleştirmek için `customer-order-update.csv` adlı bir dosya oluşturun, aşağıdaki bilgileri bu dosyaya yapıştırın ve yerel bilgisayarınıza kaydedin.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Bu CSV dosyası, siparişin miktarı `228` `22` olarak değiştirilmedikçe öncekiyle neredeyse aynıdır.

7. Depolama Gezgini, bu dosyayı depolama hesabınızın **giriş** klasörüne yükleyin.

8. Güncelleştirilmiş Delta tablosunu görmek için `select` sorguyu yeniden çalıştırın.

   ```
   %sql select * from customer_data
   ```

   Döndürülen tablo, güncelleştirilmiş kaydı gösterir.

   ![Güncelleştirilmiş kayıt tabloda görüntülenir](./media/data-lake-storage-events/final_query-2.png "Güncelleştirilmiş kayıt tabloda görüntülenir")

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve tüm ilgili kaynakları silin. Bunu yapmak için depolama hesabına ait kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Blob depolama olaylarına yanıt verme](storage-blob-event-overview.md)
