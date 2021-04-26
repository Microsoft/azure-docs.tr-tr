---
title: include dosyası
description: include dosyası
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854438"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Uygulamaya genel bakış":::

Bu diyagram, bu öğreticide oluşturduğunuz çözümün iş akışını gösterir: 

1. Azure Olay Hub 'ına gönderilen veriler bir Azure Blob depolama alanında yakalanır.
2. Veri yakalama tamamlandığında bir olay oluşturulup bir Azure Event Grid 'e gönderilir. 
3. Event Grid bu olay verilerini bir Azure işlev uygulamasına iletir.
4. İşlev uygulaması, blob 'u depodan almak için olay verilerinde blob URL 'sini kullanır. 
5. İşlev uygulaması, blob verilerini bir Azure SYNAPSE analizinden geçirir. 

Bu makalede, aşağıdaki adımları uygulayın:

> [!div class="checklist"]
> - Öğretici için gerekli altyapıyı dağıtın
> - Kodu bir İşlevler uygulamasında yayımlama
> - Event Grid aboneliği oluşturma 
> - Örnek verileri Event Hubs akışa ekleyin
> - Azure SYNAPSE Analytics 'te yakalanan verileri doğrulama

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlamak için aşağıdakiler gereklidir:

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- İçin iş yükleriyle [Visual studio 2019](https://www.visualstudio.com/vs/) : .net masaüstü geliştirme, Azure geliştirme, ASP.net ve Web geliştirme, Node.js geliştirme ve Python geliştirme.
- [Eventhubscaptureeventgriddemo örnek projesini](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) bilgisayarınıza indirin.
    - WindTurbineDataGenerator: yakalama etkin bir olay hub 'ına örnek rüzgar türbin verileri gönderen basit bir yayımcı
    - FunctionDWDumper - Azure Depolama blobunda bir Avro dosyası yakalandığında Event Grid bildirimi alan Azure İşlevi. Blob 'un URI yolunu alır, içeriğini okur ve bu verileri Azure SYNAPSE Analytics 'e (adanmış SQL havuzu) gönderir.

## <a name="deploy-the-infrastructure"></a>Altyapıyı dağıtma
Bu adımda, gerekli altyapıyı bir [Kaynak Yöneticisi şablonuyla](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)dağıtırsınız. Şablonu dağıttığınızda aşağıdaki kaynaklar oluşturulur:

* Yakalama özelliği etkinleştirilmiş Olay Hub 'ı.
* Yakalanan dosyalar için depolama hesabı. 
* İşlev uygulamasını barındırmak için App Service planı
* Olayı işlemek için işlev uygulaması
* Veri ambarını barındırmak için SQL Server
* Geçirilen verileri depolamak için Azure SYNAPSE Analytics (adanmış SQL havuzu)

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Altyapıyı dağıtmak için Azure CLı 'yi kullanma

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Üstteki **Cloud Shell** düğmesini seçin.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure portalı":::
3. Cloud Shell tarayıcının altında açıldığını görürsünüz.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Cloud Shell, **Bash** ve **PowerShell** arasında seçim yapmak Için bir seçenek görürseniz **Bash**' i seçin. 
5. Cloud Shell ilk kez kullanıyorsanız, **depolama alanı oluştur**' u seçerek bir depolama hesabı oluşturun. Azure Cloud Shell, bazı dosyaları depolamak için bir Azure depolama hesabı gerektirir. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Cloud Shell için depolama oluştur":::
6. Cloud Shell başlatılana kadar bekleyin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell başlatıldı":::
1. Aşağıdaki CLı komutunu çalıştırarak bir Azure Kaynak grubu oluşturun: 
    1. Aşağıdaki komutu kopyalayıp Cloud Shell penceresine yapıştırın. İsterseniz kaynak grubu adını ve konumunu değiştirin.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. **ENTER** tuşuna basın. 

        Aşağıda bir örnek verilmiştir:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Önceki bölümde bahsedilen tüm kaynakları (Olay Hub 'ı, depolama hesabı, işlevler uygulaması, Azure SYNAPSE Analytics) aşağıdaki CLı komutunu çalıştırarak dağıtın: 
    1. Komutu kopyalayıp Cloud Shell penceresine yapıştırın. Alternatif olarak, seçtiğiniz bir düzenleyiciye kopyalamak/yapıştırmak, değerleri ayarlamak ve sonra Cloud Shell komutu kopyalamak isteyebilirsiniz. 

        > [!IMPORTANT]
        > Komutu çalıştırmadan önce aşağıdaki varlıkların değerlerini belirtin: 
        > - Daha önce oluşturduğunuz kaynak grubunun adı.
        > - Olay Hub 'ı ad alanının adı. 
        > - Olay Hub 'ının adı. Değeri olduğu gibi (hubdatamigration) bırakabilirsiniz.
        > - SQL Server için ad.
        > - SQL kullanıcısının ve parolanın adı. 
        > - Veritabanının adı.
        > - Depolama hesabının adı. 
        > - İşlev uygulamasının adı. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Komutu çalıştırmak için Cloud Shell penceresinde **ENTER** tuşuna basın. Bu işlem biraz zaman alabilir. Bu, bir dizi kaynak oluştururken biraz zaman alabilir. Komutun sonucunda, hiçbir başarısızlık olmadığından emin olun. 
1. Cloud Shell penceresinin sağ üst köşesindeki Portal (veya) **X** düğmesinde **Cloud Shell** düğmesini seçerek Cloud Shell kapatın. 

### <a name="verify-that-the-resources-are-created"></a>Kaynakların oluşturulduğunu doğrulama

1. Azure portal Sol menüdeki **kaynak grupları** ' nı seçin. 
2. Arama kutusuna kaynak grubunuzun adını girerek kaynak grupları listesini filtreleyin. 
3. Listeden kaynak grubunuzu seçin.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Kaynak grubunuzu seçin":::
4. Kaynak grubunda aşağıdaki kaynakları görtığınızdan emin olun:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Kaynak grubundaki kaynaklar" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te tablo oluşturma
[Createdatawarehousetable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) betiğini çalıştırarak veri Ambarınızda tablo oluşturun. Betiği çalıştırmak için, Visual Studio 'Yu veya portalda sorgu düzenleyicisini kullanabilirsiniz. Aşağıdaki adımlarda sorgu Düzenleyicisi 'nin nasıl kullanılacağı gösterilmektedir: 

1. Kaynak grubundaki kaynak listesinde, **ADANMıŞ SQL havuzunuzu** seçin. 
2. **ADANMıŞ SQL havuzu** sayfasında sol menüdeki **ortak görevler** bölümünde **sorgu Düzenleyicisi (Önizleme)** öğesini seçin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Azure SYNAPSE Analytics sayfası":::
2. SQL Server için **Kullanıcı** ve **parola** adını girip **Tamam**' ı seçin. İstemcinizin SQL Server 'a erişmesine izin verme hakkında bir ileti görürseniz, aşağıdaki adımları izleyin:
    1. Bağlantıyı seçin: **sunucu güvenlik duvarını ayarla**. 
    2. **Güvenlik duvarı ayarları** sayfasında, araç çubuğunda **Istemci IP 'si Ekle** ' yi seçin ve ardından araç çubuğunda **Kaydet** ' i seçin. 
    3. Başarı iletisinde **Tamam ' ı** seçin.
    4. **ADANMıŞ SQL havuzu** sayfasına geri gidin ve Sol menüdeki **sorgu Düzenleyicisi 'ni (Önizleme)** seçin. 
    5. **Kullanıcı** ve **parola** girin ve **Tamam**' ı seçin. 
1. Sorgu penceresinde aşağıdaki SQL betiğini kopyalayın ve çalıştırın: 

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

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="SQL sorgusunu Çalıştır":::
5. Öğreticinin sonunda verilerin oluşturulduğunu doğrulayabilmeniz için bu sekmeyi veya pencereyi açık tutun. 

### <a name="update-the-function-runtime-version"></a>İşlev çalışma zamanı sürümünü Güncelleştir

1. Web tarayıcısında başka bir sekme açın ve [Azure Portal](https://portal.azure.com)' a gidin.
1. Azure portal Sol menüdeki **kaynak grupları** ' nı seçin.
1. İşlev uygulamasının bulunduğu kaynak grubunu seçin. 
1. Kaynak grubundaki kaynak listesinden **işlev uygulamasını** seçin.
1. Sol menüdeki **Ayarlar** ' ın altında **yapılandırma** ' yı seçin. 
1. Sağ bölmedeki **işlev çalışma zamanı ayarları** sekmesine geçin. 
1. **Çalışma zamanı sürümünü** **~ 3** olarak güncelleştirin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="İşlev çalışma zamanı sürümünü Güncelleştir":::
6. Araç çubuğunda **Kaydet**’i seçin. 
1. **Değişiklikleri Kaydet** onay kutusu üzerinde **devam**' ı seçin. 

## <a name="publish-the-azure-functions-app"></a>Azure İşlevleri uygulamasını yayımlama

1. Visual Studio 'Yu başlatın.
2. Önkoşulların bir parçası olarak [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) 'Dan Indirdiğiniz **Eventhubscaptureeventgriddemo. sln** çözümünü açın. `/samples/e2e/EventHubsCaptureEventGridDemo`Klasörü klasöründe bulabilirsiniz. 
3. Çözüm Gezgini, **Functionegdwdumper** projesi öğesine sağ tıklayın ve **Yayımla**' yı seçin.
4. Aşağıdaki ekranı görürseniz **Başlat**' ı seçin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Yayımla bölümündeki Başlat düğmesi.":::
5. **Yayımla** Iletişim kutusunda **hedef** için **Azure** ' u seçin ve **İleri**' yi seçin. 
6. **Azure işlev uygulaması (Windows)** öğesini seçin ve **İleri**' yi seçin.
7. **İşlevler örneği** sekmesinde Azure aboneliğinizi seçin, kaynak grubunu genişletin ve işlev uygulaması ' nı seçin ve ardından **son**' u seçin. Henüz yapmadıysanız Azure hesabınızda oturum açmanız gerekir. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="İşlev uygulamanızı seçin":::
8. **Yayımla** sayfasında, **hizmet bağımlılıkları** bölümünde, **depolama** için **Yapılandır** ' ı seçin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Depolama hizmeti bağımlılığı için bağlantıyı Yapılandır ' ı seçin":::
1. **Bağımlılığı Yapılandır** sayfasında, aşağıdaki adımları izleyin: 
    1. daha önce oluşturduğunuz **Depolama hesabını** seçin ve ardından **İleri**' yi seçin. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Depolama hesabı seçme":::
    10. **Bağlantı dizesi için bir ad** belirtin ve **bağlantı dizesini kaydet** seçeneği için **hiçbiri** ' ni seçin ve ardından **İleri**' yi seçin. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Bağlantı dizesi adını belirtin":::      
    1. **C# kod dosyası** ve gizli dizileri **depolama** seçeneğini belirleyip **son**' u seçin.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Değişikliklerin özetini gözden geçirin":::
1. Visual Studio profili yapılandırdığında **Yayımla**’yı seçin.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Yayımla ' yı seçin":::
2. **Azure işlevi** sayfası açık olan sekmede Sol menüdeki **işlevler** ' i seçin. **Eventgridtriggermigratedata** işlevinin listede görüntülendiğini doğrulayın. Bunu görmüyorsanız, Visual Studio 'dan yeniden yayımlamayı deneyin ve ardından portalda sayfayı yenileyin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="İşlev oluşturmayı Onayla":::    

İşlevi yayımladıktan sonra olaya abone olmaya hazır olursunuz.

## <a name="subscribe-to-the-event"></a>Olaya abone olma

1. Yeni bir sekmede veya bir Web tarayıcısının yeni penceresinde [Azure Portal](https://portal.azure.com)gidin.
2. Azure portal Sol menüdeki **kaynak grupları** ' nı seçin. 
3. Arama kutusuna kaynak grubunuzun adını girerek kaynak grupları listesini filtreleyin. 
4. Listeden kaynak grubunuzu seçin.
1. Kaynak listesinden **Event Hubs ad alanını** seçin.
1. **Event Hubs ad alanı** sayfasında, sol taraftaki menüden **Olaylar** ' ı seçin ve ardından araç çubuğunda **+ olay aboneliği** ' ni seçin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Event Hubs ad alanı için olaylar sayfasında olay aboneliği Ekle bağlantısı":::
1. **Olay aboneliği oluştur** sayfasında, aşağıdaki adımları izleyin:
    1. **Olay aboneliği** için bir ad girin. 
    1. **Sistem konusu** için bir ad girin. Bir sistem konusu, gönderenin olayları gönderebilmesi için bir uç nokta sağlar. Daha fazla bilgi için bkz. [sistem konuları](../articles/event-grid/system-topics.md)
    1. **Uç nokta türü** Için **Azure işlevi**' ni seçin.
    1. **Uç nokta** için bağlantıyı seçin.
    1. **Azure Işlevi Seç** sayfasında, otomatik olarak doldurulmamışsa bu adımları izleyin.
        1. Azure işlevi olan Azure aboneliğini seçin. 
        1. İşlevin kaynak grubunu seçin. 
        1. İşlev uygulamasını seçin.
        1. Dağıtım yuvasını seçin. 
        1. **Eventgridtriggermigratedata** işlevini seçin. 
    1. **Azure Işlevi Seç** sayfasında **Seçimi Onayla**' yı seçin.
    1. Ardından, **olay aboneliği oluştur** sayfasına dönün, **Oluştur**' u seçin. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="İşlevini kullanarak bir olay aboneliği oluşturma" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Olay aboneliğinin oluşturulduğunu doğrulayın. Event Hubs ad alanı için **Olaylar** sayfasındaki **olay abonelikleri** sekmesine geçin. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Olay aboneliğini Onayla" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Kaynak grubundaki kaynak listesinde App Service planı (App Service değil) seçin. 

## <a name="run-the-app-to-generate-data"></a>Veri oluşturmak için uygulama çalıştırma
Olay Hub 'ınızı ayarlamayı tamamladınız, SQL havuzunu (eski adıyla SQL veri ambarı), Azure işlev uygulamasını ve olay aboneliğini ayırabilirsiniz. Olay hub’ı için veri oluşturan bir uygulamayı çalıştırmadan önce birkaç değeri yapılandırmanız gerekir.

1. Azure portal, daha önce yaptığınız gibi kaynak grubunuza gidin. 
2. Event Hubs ad alanını seçin.
3. **Event Hubs ad alanı** sayfasında sol menüdeki **paylaşılan erişim ilkeleri** ' ni seçin.
4. İlke listesinde **RootManageSharedAccessKey** öğesini seçin. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Event Hubs ad alanı için paylaşılan erişim ilkeleri sayfası":::    
1. **Bağlantı dizesi-birincil anahtar** metin kutusunun yanındaki Kopyala düğmesini seçin. 
1. Visual Studio çözümünüze geri dönün. 
1. **WindTurbineDataGenerator** projesine sağ tıklayın ve **Başlangıç projesi olarak ayarla**' yı seçin. 
1. WindTurbineDataGenerator projesinde **program.cs** dosyasını açın.
1. `<EVENT HUBS NAMESPACE CONNECTION STRING>`Portaldan kopyaladığınız bağlantı dizesiyle değiştirin. 
1. `<EVENT HUB NAME>`Olay Hub 'ının adıyla değiştirin. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Çözümü derleyin. **WindTurbineGenerator.exe** uygulamasını çalıştırın. 
7. Birkaç dakika sonra sorgu penceresini açık olan diğer tarayıcı sekmesinde, geçirilen veriler için veri ambarınızdaki tabloyu sorgulayın.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Sorgu sonuçları](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Çözümü izleme
Bu bölüm, çözümü izlemeye veya sorunlarını gidermenize yardımcı olur. 

### <a name="view-captured-data-in-the-storage-account"></a>Depolama hesabındaki yakalanan verileri görüntüleme
1. Kaynak grubuna gidin ve olay verilerini yakalamak için kullanılan depolama hesabını seçin. 
1. **Depolama hesabı** sayfasında sol menüdeki **Depolama Gezgini (Önizleme**) öğesini seçin.
1. **BLOB kapsayıcıları**' nı genişletin ve **windturbinecapture**' ı seçin. 
1. Sağ bölmedeki **Event Hubs ad alanıyla** aynı adlı klasörü açın. 
1. Olay Hub 'ınız (**hubdatamigration**) ile aynı adlı klasörü açın. 
1. Klasörlerin ayrıntılarına gidin ve AVRO dosyalarını görürsünüz. Aşağıda bir örnek verilmiştir:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Depolamadaki yakalanan dosya" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Event Grid tetikleyicisinin işlevi çağırılacağını doğrulayın
1. Kaynak grubuna gidin ve işlev uygulamasını seçin. 
1. Sol menüdeki **işlevler** ' i seçin.
1. Listeden **Eventgridtriggermigratedata** işlevini seçin. 
1. **İşlev** sayfasında, sol taraftaki menüden **izleyici** ' yi seçin. 
1. Uygulama öngörülerini, çağırma günlüklerini yakalamak üzere yapılandırmak için **Yapılandır** ' ı seçin. 
1. Yeni bir **Application Insights** kaynağı oluşturun veya var olan bir kaynağı kullanın. 
1. İşlevin **izleyici** sayfasına geri gidin. 
1. Olayları gönderen istemci uygulamasının (**WindTurbineDataGenerator**) hala çalıştığını doğrulayın. Aksi takdirde, uygulamayı çalıştırın. 
1. Birkaç dakika bekleyin (5 dakika veya daha fazla) ve işlev etkinleştirmeleri görmek için **Yenile** düğmesini seçin.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="İşlev etkinleştirmeleri":::
1. Ayrıntıları görmek için bir çağrı seçin.

    Event Grid, olay verilerini abonelere dağıtır. Aşağıdaki örnek, bir olay hub 'ı üzerinden veri akışı bir blob 'da yakalandıktan sonra oluşturulan olay verilerini gösterir. Özellikle, `fileUrl` nesne içindeki özelliğinin `data` depolama alanındaki blobu işaret ettiğini unutmayın. İşlev uygulaması, yakalanan verilerle blob dosyasını almak için bu URL 'YI kullanır.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Verilerin adanmış SQL havuzunda depolandığını doğrulama
Sorgu penceresinin açık olduğu tarayıcı sekmesinde, geçirilen veriler için özel SQL havuzunuzdaki tabloyu sorgulayın.

![Sorgu sonuçları](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

