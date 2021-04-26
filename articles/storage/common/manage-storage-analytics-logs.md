---
title: Azure Depolama Analizi günlüklerini etkinleştirme ve yönetme (klasik) | Microsoft Docs
description: Azure Depolama Analizi kullanarak Azure 'da bir depolama hesabını izlemeyi öğrenin.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 0c182e1093c29206d27a0e55a46dd9a5607fa6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701714"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Azure Depolama Analizi günlüklerini etkinleştirme ve yönetme (klasik)

[Azure depolama Analizi](storage-analytics.md) Bloblar, kuyruklar ve tablolar için Günlükler sağlar. Hesabınız için günlükleri yapılandırmak üzere [Azure Portal](https://portal.azure.com) kullanabilirsiniz. Bu makalede günlüklerin nasıl etkinleştirileceği ve yönetileceği gösterilmektedir. Ölçümleri nasıl etkinleştireceğinizi öğrenmek için bkz. [Azure depolama Analizi ölçümlerini etkinleştirme ve yönetme (klasik)]().  Azure portal izleme verilerini İnceleme ve depolama ile ilişkili maliyetler vardır. Daha fazla bilgi için bkz. [Depolama Analizi](storage-analytics.md).

> [!NOTE]
> Azure depolama günlüklerini Depolama Analizi günlükleri yerine Azure Izleyici 'de kullanmanızı öneririz. Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, Bloblar (Azure Data Lake Storage 2.), dosyalar, kuyruklar ve tablolar için Günlükler sunar. Daha fazla bilgi edinmek için aşağıdaki makalelerden birine bakın:
>
> - [Azure Blob depolamayı izleme](../blobs/monitor-blob-storage.md)
> - [Azure dosyalarını izleme](../files/storage-files-monitoring.md)
> - [Azure kuyruk depolamayı izleme](../queues/monitor-queue-storage.md)
> - [Azure Tablo depolamayı izleme](../tables/monitor-table-storage.md)

Azure depolama ile ilgili sorunları tanımlamak, tanılamak ve sorunlarını gidermek için Depolama Analizi ve diğer araçları kullanma hakkında ayrıntılı bir kılavuz için bkz. [izleme, tanılama ve sorun giderme Microsoft Azure depolama](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Günlükleri etkinleştirme

Blob, tablo ve kuyruk Hizmetleri için okuma, yazma ve silme istekleri için Azure Storage 'ın tanılama günlüklerini kaydetmesini söyleyebilirsiniz. Ayarladığınız veri saklama ilkesi bu Günlükler için de geçerlidir.

> [!NOTE]
> Azure dosyaları şu anda Depolama Analizi ölçümleri destekler, ancak Depolama Analizi günlüğe kaydetmeyi desteklemez.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)depolama **hesapları**' nı seçin ve depolama hesabı dikey penceresini açmak için depolama hesabı adı ' nı seçin.

2. Menü dikey penceresinin **izleme (klasik)** bölümünde **Tanılama ayarları (klasik)** seçeneğini belirleyin.

    ![Azure portal Izleme altındaki tanılama menü öğesi.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. **Durumun** **Açık** olarak ayarlandığından emin olun ve günlük kaydını etkinleştirmek istediğiniz **Hizmetleri** seçin.

   > [!div class="mx-imgBorder"]
   > ![Azure portal günlüğe kaydetmeyi yapılandırın.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. **Verileri Sil** onay kutusunun seçili olduğundan emin olun.  Daha sonra, denetim kutusunun altına kaydırıcı denetimini taşıyarak ya da kaydırıcı denetiminin yanındaki metin kutusunda görüntülenen değeri doğrudan değiştirerek günlük verilerinin bekletilmesi için gereken gün sayısını ayarlayın. Yeni depolama hesapları için varsayılan değer yedi gündür. Bir bekletme ilkesi ayarlamak istemiyorsanız, sıfır girin. Bekletme ilkesi yoksa, günlük verilerini silmek size ait olur.

   > [!WARNING]
   > Günlükler, hesabınızda veri olarak depolanır. günlük verileri, zaman içinde hesabınızda birikir ve bu da depolama maliyetini artırabilir. Yalnızca küçük bir süre için günlük verilerine ihtiyacınız varsa, veri saklama ilkesini değiştirerek maliyetlerinizi azaltabilirsiniz. Eski günlük verileri (bekletme ilkenizde daha eski veriler) sistem tarafından silinir. Hesabınız için günlük verilerinin ne kadar süreyle tutulması istediğinize bağlı olarak bir bekletme ilkesi ayarlamayı öneririz. Daha fazla bilgi için bkz. [depolama ölçümlerinde faturalandırma](storage-analytics-metrics.md#billing-on-storage-metrics) .

4. **Kaydet**’e tıklayın.

   Tanılama günlükleri, depolama hesabınızda *$logs* adlı bir blob kapsayıcısına kaydedilir. Günlük verilerini, [Microsoft Azure Depolama Gezgini](https://storageexplorer.com)gibi bir Depolama Gezgini kullanarak veya depolama istemci kitaplığı ya da PowerShell kullanarak program aracılığıyla görüntüleyebilirsiniz.

   $Logs kapsayıcısına erişme hakkında daha fazla bilgi için bkz. [Storage Analytics günlüğü](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Bir Windows PowerShell komut penceresi açın.

2. `Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

3. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi ayarlayın.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

5. Kullanmak istediğiniz depolama hesabını tanımlayan depolama hesabı bağlamını alın.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>`Yer tutucu değerini kaynak grubunuzun adıyla değiştirin.

   * `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin. 

6. Geçerli günlük ayarlarını değiştirmek için **set-AzStorageServiceLoggingProperty** kullanın. Depolama günlüğünü denetleyen cmdlet 'ler, günlüğe kaydedilecek istek türleri için virgülle ayrılmış bir liste içeren bir **Loggingoperations** parametresi kullanır. Olası üç istek türü **okuma**, **yazma** ve **silme**. Günlüğe kaydetmeyi devre dışı bırakmak için, **Loggingoperations** parametresi için **none** değerini kullanın.  

   Aşağıdaki komut, bekletme için beş güne ayarlanmış varsayılan depolama hesabınızdaki Kuyruk hizmeti okuma, yazma ve silme istekleri için günlüğe kaydetme yapar:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Günlükler, hesabınızda veri olarak depolanır. günlük verileri, zaman içinde hesabınızda birikir ve bu da depolama maliyetini artırabilir. Yalnızca küçük bir süre için günlük verilerine ihtiyacınız varsa, veri saklama ilkesini değiştirerek maliyetlerinizi azaltabilirsiniz. Eski günlük verileri (bekletme ilkenizde daha eski veriler) sistem tarafından silinir. Hesabınız için günlük verilerinin ne kadar süreyle tutulması istediğinize bağlı olarak bir bekletme ilkesi ayarlamayı öneririz. Daha fazla bilgi için bkz. [depolama ölçümlerinde faturalandırma](storage-analytics-metrics.md#billing-on-storage-metrics) .
   
   Aşağıdaki komut, varsayılan depolama hesabınızda tablo hizmeti için günlüğe kaydetmeyi kapatır:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Azure PowerShell cmdlet 'lerinin Azure aboneliğinizle çalışacak şekilde nasıl yapılandırılacağı ve kullanılacak varsayılan depolama hesabını nasıl seçeceksiniz hakkında bilgi için bkz.: [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Günlük veri saklama süresini değiştirme

Günlük verileri, zaman içinde hesabınızda birikir ve bu da depolama maliyetini artırabilir. Günlük verilerine yalnızca küçük bir süre için ihtiyaç duyuyorsanız günlük verilerinin bekletme süresini değiştirerek maliyetlerinizi azaltabilirsiniz. Örneğin, yalnızca üç gün için günlüklere ihtiyacınız varsa, günlük veri saklama döneminizin değerini değerine ayarlayın `3` . Bu şekilde Günlükler, 3 gün sonra hesabınızdan otomatik olarak silinir. Bu bölümde, geçerli günlük veri saklama döneminizin nasıl görüntüleneceği gösterilir ve ardından bu süre ne yapmak istiyorsam bu dönemi güncelleştirebilirsiniz.

> [!NOTE]
> Bu adımlar yalnızca **hiyerarşik ad alanı** ayarı etkin olmayan hesaplar için geçerlidir. Hesabınızda bu ayarı etkinleştirdiyseniz, saklama günleri ayarı henüz desteklenmez. Bunun yerine, Azure Depolama Gezgini, REST veya SDK gibi desteklenen herhangi bir aracı kullanarak günlükleri el ile silmeniz gerekir. Depolama hesabınızda bu günlükleri bulmak için bkz. [günlüklerin nasıl depolandığı](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)depolama **hesapları**' nı seçin ve depolama hesabı dikey penceresini açmak için depolama hesabı adı ' nı seçin.
2. Menü dikey penceresinin **izleme (klasik)** bölümünde **Tanılama ayarları (klasik)** seçeneğini belirleyin.

    ![Azure portal Izleme altındaki tanılama menü öğesi](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. **Verileri Sil** onay kutusunun seçili olduğundan emin olun.  Daha sonra, denetim kutusunun altına kaydırıcı denetimini taşıyarak ya da kaydırıcı denetiminin yanındaki metin kutusunda görüntülenen değeri doğrudan değiştirerek günlük verilerinin bekletilmesi için gereken gün sayısını ayarlayın.

   > [!div class="mx-imgBorder"]
   > ![Azure portal bekletme süresini değiştirin](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Yeni depolama hesapları için varsayılan gün sayısı yedi gündür. Bir bekletme ilkesi ayarlamak istemiyorsanız, sıfır girin. Bekletme ilkesi yoksa, izleme verilerini silmek size ait olur.
   
4. **Kaydet**’e tıklayın.

   Tanılama günlükleri, depolama hesabınızda *$logs* adlı bir blob kapsayıcısına kaydedilir. Günlük verilerini, [Microsoft Azure Depolama Gezgini](https://storageexplorer.com)gibi bir Depolama Gezgini kullanarak veya depolama istemci kitaplığı ya da PowerShell kullanarak program aracılığıyla görüntüleyebilirsiniz.

   $Logs kapsayıcısına erişme hakkında daha fazla bilgi için bkz. [Storage Analytics günlüğü](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Bir Windows PowerShell komut penceresi açın.

2. `Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

3. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi ayarlayın.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

5. Depolama hesabını tanımlayan depolama hesabı bağlamını alın.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>`Yer tutucu değerini kaynak grubunuzun adıyla değiştirin.

   * `<storage-account-name>`Yer tutucu değerini depolama hesabınızın adıyla değiştirin. 

6. Geçerli günlük tutma ilkesini görüntülemek için [Get-AzStorageServiceLoggingProperty](/powershell/module/az.storage/get-azstorageserviceloggingproperty) kullanın. Aşağıdaki örnek, konsola blob ve kuyruk depolama hizmetleri için saklama süresini yazdırır.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   Konsol çıkışında, saklama süresi `RetentionDays` sütun başlığının altında görünür.

   > [!div class="mx-imgBorder"]
   > ![PowerShell çıkışında bekletme ilkesi](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Saklama süresini değiştirmek için [set-AzStorageServiceLoggingProperty](/powershell/module/az.storage/set-azstorageserviceloggingproperty) kullanın. Aşağıdaki örnek, saklama süresini 4 gün olarak değiştirir.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Azure PowerShell cmdlet 'lerinin Azure aboneliğinizle çalışacak şekilde nasıl yapılandırılacağı ve kullanılacak varsayılan depolama hesabını nasıl seçeceksiniz hakkında bilgi için bkz.: [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Aşağıdaki örnek, konsola blob ve kuyruk depolama hizmetleri için saklama süresini yazdırır.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

Aşağıdaki örnek, saklama süresini 4 gün olarak değiştirir. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Aşağıdaki örnek, konsola blob ve kuyruk depolama hizmetleri için saklama süresini yazdırır.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

Aşağıdaki örnek, blob ve kuyruk depolama hizmetleri için günlüklere yönelik saklama süresini 4 gün olarak değiştirir. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Günlük verilerinin silinmekte olduğunu doğrulama

Depolama hesabınızın kapsayıcısının içeriğini görüntüleyerek günlüklerin silinmekte olduğunu doğrulayabilirsiniz `$logs` . Aşağıdaki görüntüde, kapsayıcıdaki bir klasörün içerikleri gösterilmektedir `$logs` . Klasör, 2021 Ocak 'a karşılık gelir ve her klasör bir gün için günlükleri içerir. Bugünkü gün 29 Ocak 2021 ve bekletme ilkeniz yalnızca bir gün olarak ayarlandıysa, bu klasör yalnızca bir gün için günlükleri içermelidir.

> [!div class="mx-imgBorder"]
> ![Azure portalındaki günlük klasörlerinin listesi](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Günlük verilerini görüntüleme

 Günlük verilerinizi görüntülemek ve analiz etmek için ilgilendiğiniz günlük verilerini içeren Blobları yerel bir makineye indirmeniz gerekir. Birçok depolama Tarama Aracı, depolama hesabınızdan blob 'ları indirmelerini sağlar; Günlük verilerinizi indirmek için Azure depolama ekibi tarafından sunulan komut satırı Azure kopyalama aracı [AzCopy](storage-use-azcopy-v10.md) ' i de kullanabilirsiniz.  
 
>[!NOTE]
> `$logs`Kapsayıcı Event Grid tümleştirmemişse, günlük dosyaları yazıldığında bildirim almazsınız. 

 İlgilendiğiniz günlük verilerini indirdiğinizden ve aynı günlük verilerinin birden çok kez indirilmesinden kaçınmak için:  

-   Aynı verileri birden çok kez yeniden indirmeyi önlemek için, analiz için zaten indirdiğiniz blob 'ları izlemek üzere günlük verilerini içeren Blobların tarih ve saat adlandırma kuralını kullanın.  

-   Blob 'un, indirme için gereken tam blobu tanımlamak üzere günlük verilerini tuttuğu belirli bir dönemi belirlemek için günlük verilerini içeren bloblarda bulunan meta verileri kullanın.  

AzCopy kullanmaya başlamak için bkz. [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md) 

Aşağıdaki örnek, 15 Mayıs, 10:00 ve 11 Mayıs 2014 Mayıs 'ta başlayan saat için kuyruk hizmeti günlük verilerini nasıl indirebileceğiniz gösterilmektedir.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Belirli dosyaları indirme hakkında daha fazla bilgi edinmek için bkz. [AzCopy ile v10 arasındaki kullanarak Azure Blob depolamadan Blobları indirme](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Günlük verilerinizi indirdiyseniz, dosyalardaki günlük girişlerini görüntüleyebilirsiniz. Bu günlük dosyaları, çok sayıda günlük okuma aracının ayrıştırabildiği sınırlı bir metin biçimi kullanır (daha fazla bilgi için bkz. kılavuz [izleme, tanılama ve sorun giderme Microsoft Azure depolama](storage-monitoring-diagnosing-troubleshooting.md)). Farklı araçların, günlük dosyalarınızın içeriğini biçimlendirmek, filtrelemek, sıralamak ve ad aramak için farklı özellikleri vardır. Depolama günlüğü günlük dosyası biçimi ve içeriği hakkında daha fazla bilgi için bkz. [depolama Analizi günlük biçimi](/rest/api/storageservices/storage-analytics-log-format) ve [depolama Analizi günlüğe kaydedilmiş Işlemler ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Sonraki adımlar

* Depolama Analizi hakkında daha fazla bilgi edinmek için bkz. Depolama Analizi [depolama Analizi](storage-analytics.md) .
* Depolama günlüğünü yapılandırmak üzere .NET dili kullanma hakkında daha fazla bilgi için bkz. [depolama Istemci kitaplığı başvurusu](/previous-versions/azure/dn261237(v=azure.100)). 
* REST API kullanarak depolama günlüğünü yapılandırma hakkında genel bilgi için, bkz. [depolama Analizi etkinleştirme ve yapılandırma](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Depolama Analizi günlüklerinin biçimi hakkında daha fazla bilgi edinin. [Depolama Analizi günlük biçimine](/rest/api/storageservices/storage-analytics-log-format)bakın.
