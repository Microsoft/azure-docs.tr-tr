---
title: "Öğretici: olay verilerini SQL veri ambarı 'na geçirme-Azure Event Hubs"
description: "Öğretici: Bu öğreticide, bir olay kılavuzuyla tetiklenen bir Azure işlevi kullanarak Olay Hub 'ından bir SQL veri ambarına veri yakalama işlemi gösterilmektedir."
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 11/05/2019
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 92c414afbb8121eb03353c79dfe3a51e0cfa7ec0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718876"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Öğretici: Event Grid ve Azure Işlevlerini kullanarak yakalanan Event Hubs verilerini SQL veri ambarı 'na geçirme

Event Hubs [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) özelliği, Event Hubs'da akışı yapılan verileri bir Azure Blob depolama alanına veya Azure Data Lake Store'a otomatik olarak iletmenin en kolay yoludur. Ardından, verileri işleyebilir ve SQL Veri Ambarı veya Cosmos DB gibi istediğiniz bir diğer depolama hedefine aktarabilirsiniz. Bu öğreticide, olay hub'ınızdaki verilerin [olay kılavuzu](https://docs.microsoft.com/azure/event-grid/overview) tarafından tetiklenen bir Azure işlevi kullanılarak SQL veri ambarında nasıl yakalandığını öğreneceksiniz.

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   Öncelikle, **Capture** özelliğinin etkin olduğu bir olay hub'ı oluşturun ve hedef olarak bir Azure Blob depolama alanını belirleyin. WindTurbineGenerator tarafından oluşturulan verilerin olay hub'ına akışı yapılır ve bu veriler Azure Depolama'da otomatik olarak Avro dosyaları biçiminde yakalanır. 
*   Ardından, kaynağı Event Hubs ad alanı, hedefi ise Azure İşlevi uç noktası olan bir Azure Event Grid aboneliği oluşturun.
*   Azure Depolama blobuna Event Hubs Capture özelliği ile yeni bir Avro dosyası aktarıldığında Event Grid, Azure İşlevi'ne blob URI'sini bildirir. Ardından, işlev blob verilerini bir SQL veri ambarına geçirir.

Bu öğreticide, aşağıdaki eylemleri gerçekleştireceksiniz: 

> [!div class="checklist"]
> * Altyapıyı dağıtma
> * Kodu bir İşlevler uygulamasında yayımlama
> * İşlevler uygulamasında bir Event Grid aboneliği oluşturma
> * Event Hub'a örnek veri akışı yapma. 
> * Yakalanan verileri SQL Veri Ambarı'nda doğrulama

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual studio 2019](https://www.visualstudio.com/vs/). Yükleme işlemi sırasında şu iş yüklerini de yüklediğinizden emin olun: .NET masaüstü geliştirme, Azure geliştirme, ASP.NET ve web geliştirme, Node.js geliştirme ve Python geliştirme
- [Git örneğini](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) indirin. Örnek çözümde şu bileşenler yer almaktadır:
    - *WindTurbineDataGenerator* - Capture özelliğinin etkin olduğu bir olay hub'ına örnek rüzgar türbini verisi gönderen basit bir yayımcı
    - *FunctionDWDumper* - Azure Depolama blobunda bir Avro dosyası yakalandığında Event Grid bildirimi alan Azure İşlevi. Blobun URI yolunu alır, içeriğini okur ve bu verileri bir SQL Veri Ambarı'na gönderir.

### <a name="deploy-the-infrastructure"></a>Altyapıyı dağıtma
Bu [Azure Resource Manager şablonunu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json) kullanarak söz konusu öğretici için gerekli altyapıyı dağıtmak üzere Azure PowerShell veya Azure CLI'yi kullanın. Bu şablon aşağıdaki kaynakları oluşturur:

-   Capture özelliği etkin Event Hub
-   Yakalanan olay verileri için depolama hesabı
-   İşlevler uygulamasının barındırılması için Azure App Service planı
-   Yakalanan etkinlik verilerinin işlenmesi için İşlev uygulaması
-   Veri Ambarının barındırılması için SQL Server
-   Geçirilen verileri depolamak için SQL Veri Ambarı

Aşağıdaki bölümlerde, öğretici için gerekli altyapının dağıtılmasına yönelik Azure CLI ve Azure PowerShell komutları sunulmuştur. Komutları çalıştırmadan önce aşağıdaki nesnelerin adlarını güncelleştirin: 

- Azure kaynak grubu 
- Kaynak grubu bölgesi
- Event Hubs ad alanı
- Olay hub'ı
- Azure SQL sunucusu
- SQL kullanıcısı (ve parolası)
- Azure SQL veritabanı
- Azure Storage 
- Azure İşlevleri Uygulaması

Bu betiklerin tüm Azure yapıtlarını oluşturması biraz zaman alır. Betik tamamlanana kadar başka işlem yapmayın. Dağıtım bir nedenle başarısız olursa kaynak grubunu silin, bildirilen sorunu çözün ve komutu yeniden çalıştırın. 

#### <a name="azure-cli"></a>Azure CLI
Şablonu Azure CLI kullanarak dağıtmak için şu komutları kullanın:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Şablonu PowerShell kullanarak dağıtmak için şu komutları kullanın:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>SQL Veri Ambarında tablo oluşturma 
[Visual Studio](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql), [SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md) veya portaldaki Sorgu Düzenleyicisi ile [CreateDataWarehouseTable.sql](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) betiğini çalıştırarak SQL veri ambarınızda bir tablo oluşturun. 

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

## <a name="publish-code-to-the-functions-app"></a>Kodu İşlevler Uygulamasında yayımlama

1. Visual Studio 2019 ' de *Eventhubscaptureeventgriddemo. sln* çözümünü açın.

1. Çözüm Gezgini’nde *FunctionEGDWDumper*’a sağ tıklayın ve **Yayımla**’yı seçin.

   ![İşlev uygulaması yayımlama](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. **Azure İşlev Uygulaması** ve **Var Olanı Seç**’i seçin. **Yayımla**’yı seçin.

   ![Hedef işlev uygulaması](./media/store-captured-data-data-warehouse/pick-target.png)

1. Şablon aracılığıyla dağıttığınız işlev uygulamasını seçin. **Tamam**’ı seçin.

   ![İşlev uygulaması seçme](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Visual Studio profili yapılandırdığında **Yayımla**’yı seçin.

   ![Yayımla’yı seçme](./media/store-captured-data-data-warehouse/select-publish.png)

İşlevi yayımladıktan sonra Event Hubs'daki yakalama olayına abone olmaya hazır olursunuz.


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>İşlevler uygulamasında bir Event Grid aboneliği oluşturma
 
1. [Azure Portal](https://portal.azure.com/) gidin. Kaynak grubunuzu ve işlev uygulamanızı seçin.

   ![İşlev uygulamasını görüntüleme](./media/store-captured-data-data-warehouse/view-function-app.png)

1. İşlevi seçin.

   ![İşlev seçme](./media/store-captured-data-data-warehouse/select-function.png)

1. **Event Grid aboneliği ekle**’yi seçin.

   ![Abonelik ekleme](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Event grid aboneliğine bir ad verin. Olay türü olarak **Event Hubs Ad Alanları**’nı kullanın. Event Hubs ad alanı örneğinizi seçmek için değerler sağlayın. Abone uç noktasını sağlanan değer olarak bırakın. **Oluştur**'u seçin.

   ![Abonelik oluşturma](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Örnek veri oluşturma  
Artık Olay Hub'ı, SQL veri ambarı, Azure İşlev Uygulaması ve Event Grid aboneliğinizi ayarlamayı tamamladınız. Kaynak kodunda bağlantı dizesini ve olay hub'ınızın adını güncelleştirdikten sonra Olay Hub'ına yönelik veri akışları oluşturmak için WindTurbineDataGenerator.exe dosyasını çalıştırabilirsiniz. 

1. Portalda olay hub'ı ad alanınızı seçin. **Bağlantı Dizeleri**’ni seçin.

   ![Bağlantı dizelerini seçme](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. **RootManageSharedAccessKey** öğesini seçin

   ![Anahtar seçme](./media/store-captured-data-data-warehouse/show-root-key.png)

3. **Bağlantı dizesi - birincil anahtar** değerini kopyalayın

   ![Anahtarı kopyalama](./media/store-captured-data-data-warehouse/copy-key.png)

4. Visual Studio projenize geri dönün. *WindTurbineDataGenerator* projesinde *program.cs* dosyasını açın.

5. **EventHubConnectionString** ve **EventHubName** değerlerini bağlantı dizesi ve olay hub'ınızın adıyla güncelleştirin. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Çözümü derleyin ve ardından, WindTurbineGenerator.exe uygulamasını çalıştırın. 

## <a name="verify-captured-data-in-data-warehouse"></a>Yakalanan verileri veri ambarında doğrulama
Birkaç dakika sonra, SQL veri ambarınızdaki tabloyu sorgulayın. WindTurbineDataGenerator tarafından oluşturulan verilerin Olay Hub'ınıza akışının yapıldığına, bir Azure Depolama kapsayıcısında yakalandığına ve ardından, Azure İşlevi tarafından SQL Data Warehouse tablosuna geçirildiğine dikkat edin.  

## <a name="next-steps"></a>Sonraki adımlar 
Eyleme dönüştürülebilir içgörüler edinmek için veri ambarınızla güçlü veri görselleştirme araçları kullanabilirsiniz.

Bu makalede [SQL Veri Ambarı ile Power BI'ın](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi) nasıl kullanılacağı gösterilmektedir



