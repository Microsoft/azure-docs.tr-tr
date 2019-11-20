---
title: 'Öğretici: veri ambarına Event Hubs veri gönderme-Event Grid'
description: 'Öğretici: bir SQL veri ambarına veri geçirmek için Azure Event Grid ve Event Hubs kullanımını açıklar. Bir yakalama dosyası almak için bir Azure Işlevi kullanır.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718188"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Öğretici: veri ambarına büyük veri akışı
Azure [Event Grid](overview.md) , uygulamalardan ve hizmetlerden bildirimleri (olayları) tepki etmenizi sağlayan akıllı bir olay yönlendirme hizmetidir. Örneğin, bir Azure Işlevini Azure Blob depolama alanına veya Azure Data Lake Storage yakalanan Event Hubs verileri işleyecek şekilde tetikleyip verileri diğer veri depolarına geçirebilirler. Bu [Event Hubs ve Event Grid tümleştirme örneği](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) , yakalanan Event Hubs verilerini blob DEPOLAMADAN bir SQL veri ambarına sorunsuzca geçirmek için Event Grid ile Event Hubs nasıl kullanacağınızı gösterir.

![Uygulamaya genel bakış](media/event-grid-event-hubs-integration/overview.png)

Bu diyagram, bu öğreticide oluşturduğunuz çözümün iş akışını gösterir: 

1. Azure Olay Hub 'ına gönderilen veriler bir Azure Blob depolama alanında yakalanır.
2. Veri yakalama tamamlandığında bir olay oluşturulup bir Azure Event Grid 'e gönderilir. 
3. Event Grid bu olay verilerini bir Azure işlev uygulamasına iletir.
4. İşlev uygulaması, blob 'u depodan almak için olay verilerinde blob URL 'sini kullanır. 
5. İşlev uygulaması, blob verilerini bir Azure SQL veri ambarına geçirir. 

Bu makalede, aşağıdaki adımları uygulayın:

> [!div class="checklist"]
> * Altyapıyı dağıtmak için bir Azure Resource Manager şablonu kullanın: bir olay hub 'ı, bir depolama hesabı, bir işlev uygulaması, bir SQL veri ambarı.
> * Veri ambarında bir tablo oluşturun.
> * İşlev uygulamasına kod ekleyin.
> * Olaya abone olun. 
> * Olay Hub 'ına veri gönderen uygulamayı çalıştırın.
> * Veri ambarında geçirilen verileri görüntüleyin.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* İçin iş yükleriyle [Visual studio 2019](https://www.visualstudio.com/vs/) : .net masaüstü geliştirme, Azure geliştirme, ASP.net ve Web geliştirme, Node. js geliştirme ve Python geliştirme.
* [Eventhubscaptureeventgriddemo örnek projesini](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) bilgisayarınıza indirin.

## <a name="deploy-the-infrastructure"></a>Altyapıyı dağıtma
Bu adımda, gerekli altyapıyı bir [Kaynak Yöneticisi şablonuyla](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)dağıtırsınız. Şablonu dağıttığınızda aşağıdaki kaynaklar oluşturulur:

* Yakalama özelliği etkinleştirilmiş Olay Hub 'ı.
* Yakalanan dosyalar için depolama hesabı. 
* İşlev uygulamasını barındırmak için App Service planı
* Olayı işlemek için işlev uygulaması
* Veri ambarını barındırmak için SQL Server
* Geçirilen verileri depolamak için SQL Veri Ambarı

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Azure portal Azure Cloud Shell başlatma

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Üstteki **Cloud Shell** düğmesini seçin.

    ![Azure portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. Cloud Shell tarayıcının altında açıldığını görürsünüz.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Cloud Shell, **Bash** ve **PowerShell**arasında seçim yapmak Için bir seçenek görürseniz **Bash**' i seçin. 
5. Cloud Shell ilk kez kullanıyorsanız, **depolama alanı oluştur**' u seçerek bir depolama hesabı oluşturun. Azure Cloud Shell, bazı dosyaları depolamak için bir Azure depolama hesabı gerektirir. 

    ![Cloud Shell için depolama oluşturma](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Cloud Shell başlatılana kadar bekleyin. 

    ![Cloud Shell için depolama oluşturma](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Azure CLI kullanma

1. Aşağıdaki CLı komutunu çalıştırarak bir Azure Kaynak grubu oluşturun: 
    1. Aşağıdaki komutu kopyalayıp Cloud Shell penceresine yapıştırın

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. **Kaynak grubu** için bir ad belirtin
    2. **ENTER**'a basın. 

        Örnek aşağıda verilmiştir:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Önceki bölümde bahsedilen tüm kaynakları (Olay Hub 'ı, depolama hesabı, işlevler uygulaması, SQL veri ambarı) aşağıdaki CLı komutunu çalıştırarak dağıtın: 
    1. Komutu kopyalayıp Cloud Shell penceresine yapıştırın. Alternatif olarak, seçtiğiniz bir düzenleyiciye kopyalamak/yapıştırmak, değerleri ayarlamak ve sonra Cloud Shell komutu kopyalamak isteyebilirsiniz. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Aşağıdaki varlıkların değerlerini belirtin:
        1. Daha önce oluşturduğunuz kaynak grubunun adı.
        2. Olay Hub 'ı ad alanının adı. 
        3. Olay Hub 'ının adı. Değeri olduğu gibi (hubdatamigration) bırakabilirsiniz.
        4. SQL Server için ad.
        5. SQL kullanıcısının ve parolanın adı. 
        6. SQL veri ambarı için ad
        7. Depolama hesabının adı. 
        8. İşlev uygulamasının adı. 
    3.  Komutu çalıştırmak için Cloud Shell penceresinde **ENTER** tuşuna basın. Bu işlem biraz zaman alabilir. Bu, bir dizi kaynak oluştururken biraz zaman alabilir. Komutun sonucunda, hiçbir başarısızlık olmadığından emin olun. 
    

### <a name="use-azure-powershell"></a>Azure PowerShell’i kullanma

1. Azure Cloud Shell ' de PowerShell moduna geçin. Azure Cloud Shell sol üst köşesinde aşağı ok ' i seçin ve **PowerShell**' i seçin.

    ![PowerShell 'e geç](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Aşağıdaki komutu çalıştırarak bir Azure Kaynak grubu oluşturun: 
    1. Aşağıdaki komutu kopyalayıp Cloud Shell penceresine yapıştırın.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. **Kaynak grubu**için bir ad belirtin.
    3. ENTER tuşuna basın. 
3. Önceki bölümde bahsedilen tüm kaynakları (Olay Hub 'ı, depolama hesabı, işlevler uygulaması, SQL veri ambarı) aşağıdaki komutu çalıştırarak dağıtın:
    1. Komutu kopyalayıp Cloud Shell penceresine yapıştırın. Alternatif olarak, seçtiğiniz bir düzenleyiciye kopyalamak/yapıştırmak, değerleri ayarlamak ve sonra Cloud Shell komutu kopyalamak isteyebilirsiniz. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Aşağıdaki varlıkların değerlerini belirtin:
        1. Daha önce oluşturduğunuz kaynak grubunun adı.
        2. Olay Hub 'ı ad alanının adı. 
        3. Olay Hub 'ının adı. Değeri olduğu gibi (hubdatamigration) bırakabilirsiniz.
        4. SQL Server için ad.
        5. SQL kullanıcısının ve parolanın adı. 
        6. SQL veri ambarı için ad
        7. Depolama hesabının adı. 
        8. İşlev uygulamasının adı. 
    3.  Komutu çalıştırmak için Cloud Shell penceresinde **ENTER** tuşuna basın. Bu işlem biraz zaman alabilir. Bu, bir dizi kaynak oluştururken biraz zaman alabilir. Komutun sonucunda, hiçbir başarısızlık olmadığından emin olun. 

### <a name="close-the-cloud-shell"></a>Cloud Shell kapatın 
Cloud Shell penceresinin sağ üst köşesindeki Portal (veya) **X** düğmesindeki **Cloud Shell** düğmesini seçerek Cloud Shell 'i kapatın. 

### <a name="verify-that-the-resources-are-created"></a>Kaynakların oluşturulduğunu doğrulama

1. Azure portal Sol menüdeki **kaynak grupları** ' nı seçin. 
2. Arama kutusuna kaynak grubunuzun adını girerek kaynak grupları listesini filtreleyin. 
3. Listeden kaynak grubunuzu seçin.

    ![Kaynak grubunuzu seçin](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Kaynak grubunda aşağıdaki kaynakları görtığınızdan emin olun:

    ![Kaynak grubundaki kaynaklar](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>SQL Veri Ambarında tablo oluşturma
[Createdatawarehousetable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) betiğini çalıştırarak veri Ambarınızda tablo oluşturun. Betiği çalıştırmak için, Visual Studio 'Yu veya portalda sorgu düzenleyicisini kullanabilirsiniz. Aşağıdaki adımlarda sorgu Düzenleyicisi 'nin nasıl kullanılacağı gösterilmektedir: 

1. Kaynak grubundaki kaynak listesinde SQL veri Ambarınızı seçin. 
2. SQL veri ambarı sayfasında sol menüdeki **sorgu Düzenleyicisi 'ni (Önizleme)** seçin. 

    ![SQL veri ambarı sayfası](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. SQL Server için **Kullanıcı** ve **parola** adını girip **Tamam**' ı seçin. 

    ![SQL Server kimlik doğrulaması](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Sorgu penceresinde aşağıdaki SQL betiğini kopyalayın ve çalıştırın: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![SQL sorgusunu Çalıştır](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Öğreticinin sonunda verilerin oluşturulduğunu doğrulayabilmeniz için bu sekmeyi veya pencereyi açık tutun. 


## <a name="publish-the-azure-functions-app"></a>Azure İşlevleri uygulamasını yayımlama

1. Visual Studio 'Yu başlatın.
2. Önkoşulların bir parçası olarak [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) 'Dan Indirdiğiniz **Eventhubscaptureeventgriddemo. sln** çözümünü açın.
3. Çözüm Gezgini’nde **FunctionEGDWDumper**’a sağ tıklayın ve **Yayımla**’yı seçin.

   ![İşlev uygulaması yayımlama](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Aşağıdaki ekranı görürseniz **Başlat**' ı seçin. 

   ![Yayımla düğmesini Başlat](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. **Bir yayımlama hedefi** seçin sayfasında, **Varolanı Seç** seçeneğini belirleyin ve **Profil oluştur**' u seçin. 

   ![Yayımlama hedefi seçme](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. App Service sayfasında, **Azure aboneliğinizi**seçin, kaynak grubunuzda **işlev uygulamasını** seçin ve **Tamam**' ı seçin. 

   ![App Service sayfası](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Visual Studio profili yapılandırdığında **Yayımla**’yı seçin.

   ![Yayımla’yı seçme](media/event-grid-event-hubs-integration/select-publish.png)

İşlevi yayımladıktan sonra olaya abone olmaya hazır olursunuz.

## <a name="subscribe-to-the-event"></a>Olaya abone olma

1. Yeni bir sekmede veya bir Web tarayıcısının yeni penceresinde [Azure Portal](https://portal.azure.com)gidin.
2. Azure portal Sol menüdeki **kaynak grupları** ' nı seçin. 
3. Arama kutusuna kaynak grubunuzun adını girerek kaynak grupları listesini filtreleyin. 
4. Listeden kaynak grubunuzu seçin.

    ![Kaynak grubunuzu seçin](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Listeden App Service planı seçin. 
5. App Service planı sayfasında, sol taraftaki menüden **uygulamalar** ' ı seçin ve işlev uygulamasını seçin. 

    ![İşlevler uygulamanızı seçin](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. İşlev uygulamasını genişletin, işlevler ' i genişletin ve ardından işlevinizi seçin. 

    ![Azure işlevinizi seçin](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Araç çubuğunda **Event Grid aboneliği Ekle** ' yi seçin. 
8. **Event Grid aboneliği oluştur** sayfasında, aşağıdaki işlemleri yapın: 
    1. **Konu ayrıntıları** bölümünde, aşağıdaki işlemleri yapın:
        1. Azure aboneliğinizi seçin.
        2. Azure kaynak grubunu seçin.
        3. Event Hubs ad alanını seçin.
    2. **Olay ABONELIĞI ayrıntıları** sayfasında, abonelik için bir ad girin (örneğin: captureEventSub) ve **Oluştur**' u seçin. 

        ![Event Grid aboneliği oluştur](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Veri oluşturmak için uygulama çalıştırma
Olay hub’ı, SQL veri ambarı, Azure işlev uygulaması ve olay aboneliğinizi ayarlamayı tamamladınız. Olay hub’ı için veri oluşturan bir uygulamayı çalıştırmadan önce birkaç değeri yapılandırmanız gerekir.

1. Azure portal, daha önce yaptığınız gibi kaynak grubunuza gidin. 
2. Event Hubs ad alanını seçin.
3. **Event Hubs ad alanı** sayfasında sol menüdeki **paylaşılan erişim ilkeleri** ' ni seçin.
4. İlke listesinde **RootManageSharedAccessKey** öğesini seçin. 
5. **Bağlantı dizesi-birincil anahtar** metin kutusunun yanındaki Kopyala düğmesini seçin. 

    ![Olay Hub 'ı ad alanı için bağlantı dizesi](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Visual Studio çözümünüze geri dönün. 
2. WindTurbineDataGenerator projesinde **program.cs** dosyasını açın.
5. İki sabit değeri değiştirin. **EventHubConnectionString** için kopyalanan değeri kullanın. Olay hub’ının adı için **hubdatamigration** kullanın. Olay Hub 'ı için farklı bir ad kullandıysanız, bu adı belirtin. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Çözümü derleyin. **WindTurbineGenerator. exe** uygulamasını çalıştırın. 
7. Birkaç dakika sonra, veri ambarınızdaki tabloda geçirilen verileri sorgulayın.

    ![Sorgu sonuçları](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Olay Hub 'ı tarafından oluşturulan olay verileri
Event Grid, olay verilerini abonelere dağıtır. Aşağıdaki örnek, bir olay hub 'ı üzerinden veri akışı bir blob 'da yakalandıktan sonra oluşturulan olay verilerini gösterir. Özellikle, `data` nesnesi içindeki `fileUrl` özelliğinin depolama alanındaki blobu işaret ettiğini fark eder. İşlev uygulaması, yakalanan verilerle blob dosyasını almak için bu URL 'YI kullanır.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Sonraki adımlar

* Azure mesajlaşma servislerindeki farklar hakkında bilgi sahibi olmak için, bkz. [iletiler teslim eden Azure hizmetleri arasında seçim yapma](compare-messaging-services.md).
* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Hubs Capture’a giriş için bkz. [Azure portalını kullanarak Event Hubs Capture’ı etkinleştirme](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Örneği ayarlama ve çalıştırma hakkında daha fazla bilgi için bkz. [Event Hubs Capture ve Event Grid örneği](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
