---
title: Azure dosyalarını izleme | Microsoft Docs
description: Azure dosyalarının performansını ve kullanılabilirliğini izlemeyi öğrenin. Azure dosyaları verilerini izleyin, yapılandırma hakkında bilgi edinin ve ölçüm ve günlük verilerini çözümleyin.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 620ee3bc5978da4b274aed9a412679ae0835f0b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759836"
---
# <a name="monitoring-azure-files"></a>Azure dosyalarını izleme

Azure kaynaklarına dayanan önemli uygulamalarınız ve iş süreçleriniz olduğunda, bu kaynakları kullanılabilirlik, performans ve işlem için izlemek istersiniz. Bu makalede, Azure dosyaları tarafından oluşturulan izleme verileri ve bu verilerdeki uyarıları çözümlemek için Azure Izleyici 'nin özelliklerini nasıl kullanabileceğiniz açıklanır.

## <a name="monitor-overview"></a>İzlemeye genel bakış

Her Azure dosya kaynağı için Azure portal **genel bakış** sayfası, istekler ve saatlik faturalandırma gibi kaynak kullanımının kısa bir görünümünü içerir. Bu bilgiler faydalıdır, ancak yalnızca küçük miktarda izleme verisi kullanılabilir. Bu verilerden bazıları otomatik olarak toplanır ve kaynağı oluşturur almaz analiz için kullanılabilir. Bazı yapılandırma ile ek veri koleksiyonu türlerini etkinleştirebilirsiniz.

## <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?
Azure dosyaları, Azure 'da tam yığın izleme hizmeti olan [Azure izleyici](../../azure-monitor/overview.md)'yi kullanarak izleme verileri oluşturur. Azure Izleyici, diğer bulutlardaki ve Şirket içindeki Azure kaynaklarınızı ve kaynaklarınızı izlemek için tam bir özellik kümesi sağlar. 

Azure [izleyici Ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md)makalesini izleyerek şunları açıklar:

- Azure İzleyici nedir?
- İzleme ile ilişkili maliyetler
- Azure 'da toplanan verileri izleme
- Veri toplamayı yapılandırma
- İzleme verilerini analiz etmek ve uyarı vermek için Azure 'da standart araçlar

Aşağıdaki bölümlerde, Azure dosyalarından toplanan belirli veriler açıklanarak bu makalede derleme yapılır. Örnekler, veri toplamayı yapılandırmayı ve bu verileri Azure araçları ile çözümlemeyi gösterir.

## <a name="monitoring-data"></a>Verileri izleme

Azure dosyaları, [Azure kaynaklarından gelen verileri izleme](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar. 

Azure dosyaları tarafından oluşturulan ölçümler ve günlük ölçümleri hakkında ayrıntılı bilgi için bkz. [Azure dosya izleme veri başvurusu](storage-files-monitoring-reference.md) .

Azure Izleyici 'de ölçümler ve Günlükler yalnızca depolama hesaplarını Azure Resource Manager destekler. Azure Izleyici, klasik depolama hesaplarını desteklemez. Klasik bir depolama hesabında ölçümleri veya günlükleri kullanmak istiyorsanız bir Azure Resource Manager depolama hesabına geçiş yapmanız gerekir. Bkz. [Azure Resource Manager 'ye geçirme](../../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="collection-and-routing"></a>Toplama ve yönlendirme

Platform ölçümleri ve etkinlik günlüğü otomatik olarak toplanır, ancak bir tanılama ayarı kullanılarak diğer konumlara yönlendirilebilir. 

Kaynak günlüklerini toplamak için bir tanılama ayarı oluşturmanız gerekir. Ayarı oluşturduğunuzda, günlüklerini etkinleştirmek istediğiniz depolama türü olarak **Dosya** ' yı seçin. Ardından, günlüklerini toplamak istediğiniz aşağıdaki işlem kategorisinden birini belirtin. 

| Kategori | Açıklama |
|:---|:---|
| StorageRead | Nesnelerde okuma işlemleri. |
| StorageWrite | Nesneler üzerinde yazma işlemleri. |
| StorageDelete | Nesneler üzerinde silme işlemleri. |

Günlüğe kaydedilen SMB ve REST işlemlerinin listesini almak için bkz. [depolama günlüğe kaydedilmiş işlemler ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) ve [Azure dosyaları izleme veri başvurusu](storage-files-monitoring-reference.md).

## <a name="creating-a-diagnostic-setting"></a>Tanılama ayarı oluşturma

Azure portal, PowerShell, Azure CLı veya bir Azure Resource Manager şablonunu kullanarak bir tanılama ayarı oluşturabilirsiniz.

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, Bloblar (Azure Data Lake Storage 2.), dosyalar, kuyruklar ve tablolar için Günlükler sunar. Bu özellik, Azure Resource Manager dağıtım modeliyle oluşturulan tüm depolama hesapları için kullanılabilir. Bkz. [depolama hesabına genel bakış](../common/storage-account-overview.md).

Genel rehberlik için bkz. [Azure 'da platform günlüklerini ve ölçümlerini toplamak için tanılama ayarı oluşturma](../../azure-monitor/essentials/diagnostic-settings.md).

### <a name="azure-portal"></a>[Azure portalı](#tab/azure-portal)

1. Azure portalında oturum açın.

2. Depolama hesabınıza gidin.

3. **İzleme** bölümünde **Tanılama ayarları (Önizleme)** öğesine tıklayın.

   > [!div class="mx-imgBorder"]
   > ![Portal-tanılama günlükleri](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Günlüklerini etkinleştirmek istediğiniz depolama türü olarak **Dosya** ' yı seçin.

5. **Tanılama ayarı Ekle**' ye tıklayın.

   > [!div class="mx-imgBorder"]
   > ![Portal-kaynak günlükleri-tanılama ayarı ekleme](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   **Tanılama ayarları** sayfası görüntülenir.

   > [!div class="mx-imgBorder"]
   > ![Kaynak günlükleri sayfası](media/storage-files-monitoring/diagnostic-logs-page.png)

6. Sayfanın **ad** alanına bu kaynak günlüğü ayarı için bir ad girin. Ardından, kütüğe kaydedilmesini istediğiniz işlemleri (okuma, yazma ve silme işlemleri) ve günlüklerin gönderilmesini istediğiniz yeri seçin.

#### <a name="archive-logs-to-a-storage-account"></a>Günlükleri bir depolama hesabına Arşivle

Günlüklerinizi bir depolama hesabına arşivlemeyi seçerseniz, depolama hesabına gönderilen günlük hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

1. **Depolama hesabı Arşivi** onay kutusunu seçin ve ardından **Yapılandır** düğmesine tıklayın.

   > [!div class="mx-imgBorder"]   
   > ![Tanılama ayarları sayfa arşiv depolaması](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. **Depolama hesabı** açılan listesinde, günlüklerinizi arşivlemek istediğiniz depolama hesabını seçin, **Tamam** düğmesine tıklayın ve ardından **Kaydet** düğmesine tıklayın.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Verme hedefi olarak bir depolama hesabı seçmeden önce, depolama hesabındaki önkoşulları anlamak için bkz. [Azure Kaynak günlüklerini arşivleme](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) .

#### <a name="stream-logs-to-azure-event-hubs"></a>Günlükleri Azure Event Hubs akış

Günlüklerinizi bir olay hub 'ına akışı yapmayı seçerseniz, Olay Hub 'ına gönderilen günlüklerin hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

1. **Bir olay hub 'ı Için akış** onay kutusunu seçin ve ardından **Yapılandır** düğmesine tıklayın.

2. **Bir olay hub 'ı seçin** bölmesinde günlüklerinizi akışa almak istediğiniz olay hub 'ının ad alanını, adını ve ilke adını seçin. 

   > [!div class="mx-imgBorder"]
   > ![Tanılama ayarları sayfası Olay Hub 'ı](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. **Tamam** düğmesine tıklayın ve ardından **Kaydet** düğmesine tıklayın.

#### <a name="send-logs-to-azure-log-analytics"></a>Azure Log Analytics günlük gönder

1. **Log Analytics gönder** onay kutusunu seçin, bir Log Analytics çalışma alanı seçin ve ardından **Kaydet** düğmesine tıklayın.

   > [!div class="mx-imgBorder"]   
   > ![Tanılama ayarları sayfa günlüğü Analizi](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Bir Windows PowerShell komut penceresi açın ve komutunu kullanarak Azure aboneliğinizde oturum açın `Connect-AzAccount` . Ardından ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

2. Etkin aboneliğinizi, günlük kaydını etkinleştirmek istediğiniz depolama hesabının aboneliğine ayarlayın.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Günlükleri bir depolama hesabına Arşivle

Günlüklerinizi bir depolama hesabına arşivlemeyi seçerseniz, depolama hesabına gönderilen günlük hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 'ini parametresiyle birlikte kullanarak günlükleri etkinleştirin `StorageAccountId` .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

`<storage-service-resource--id>`Bu kod parçacığındaki yer tutucuyu Azure dosya hizmetinin kaynak kimliğiyle değiştirin. Depolama hesabınızın **Özellikler** sayfasını açarak Azure Portal kaynak kimliğini bulabilirsiniz.

`StorageRead` `StorageWrite` `StorageDelete` **Kategori** parametresinin değeri için, ve kullanabilirsiniz.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Aşağıda bir örnek verilmiştir:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Her parametrenin açıklaması için bkz. [Azure PowerShell aracılığıyla Azure Kaynak günlüklerini arşivleme](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Günlükleri bir olay hub 'ına akış

Günlüklerinizi bir olay hub 'ına akışı yapmayı seçerseniz, Olay Hub 'ına gönderilen günlüklerin hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 'ini parametresiyle kullanarak günlükleri etkinleştirin `EventHubAuthorizationRuleId` .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Aşağıda bir örnek verilmiştir:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Her parametrenin açıklaması için bkz. [PowerShell cmdlet 'leri aracılığıyla Event Hubs Için akış verileri](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Günlükleri Log Analytics’e gönderme

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 'ini parametresiyle kullanarak günlükleri etkinleştirin `WorkspaceId` .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Aşağıda bir örnek verilmiştir:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Daha fazla bilgi için bkz. Azure [izleyici 'de Log Analytics çalışma alanına Azure Kaynak günlüklerini akış](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. İlk olarak, [Azure Cloud Shell](../../cloud-shell/overview.md)açın veya Azure CLI 'yı yerel olarak [yüklediyseniz](/cli/azure/install-azure-cli) , Windows PowerShell gibi bir komut konsol uygulaması açın.

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi günlüklerini etkinleştirmek istediğiniz depolama hesabının aboneliğine ayarlayın.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

#### <a name="archive-logs-to-a-storage-account"></a>Günlükleri bir depolama hesabına Arşivle

Günlüklerinizi bir depolama hesabına arşivlemeyi seçerseniz, depolama hesabına gönderilen günlük hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

[Az Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) komutunu kullanarak günlükleri etkinleştirin.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

`<storage-service-resource--id>`Bu kod parçacığındaki yer tutucuyu kaynak kimliği BLOB depolama hizmeti ile değiştirin. Depolama hesabınızın **Özellikler** sayfasını açarak Azure Portal kaynak kimliğini bulabilirsiniz.

`StorageRead` `StorageWrite` `StorageDelete` **Kategori** parametresinin değeri için, ve kullanabilirsiniz.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Aşağıda bir örnek verilmiştir:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Her parametrenin açıklaması için bkz. [Azure CLI aracılığıyla arşiv kaynak günlükleri](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Günlükleri bir olay hub 'ına akış

Günlüklerinizi bir olay hub 'ına akışı yapmayı seçerseniz, Olay Hub 'ına gönderilen günlüklerin hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

[Az Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) komutunu kullanarak günlükleri etkinleştirin.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Aşağıda bir örnek verilmiştir:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Her parametrenin açıklaması için bkz. [Azure CLI aracılığıyla Event Hubs Için akış verileri](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Günlükleri Log Analytics’e gönderme

[Az Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) komutunu kullanarak günlükleri etkinleştirin.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Aşağıda bir örnek verilmiştir:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Daha fazla bilgi için bkz. Azure [izleyici 'de Log Analytics çalışma alanına Azure Kaynak günlüklerini akış](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Şablon](#tab/template)

Bir tanılama ayarı oluşturan Azure Resource Manager şablonunu görüntülemek için bkz. [Azure Storage Için tanılama ayarı](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Ölçümler çözümleniyor

Azure depolama ölçümlerini, Ölçüm Gezgini kullanarak diğer Azure hizmetlerinden alınan ölçümlerle çözümleyebilirsiniz. **Azure izleyici** menüsünden **ölçümler** ' i seçerek Ölçüm Gezgini açın. Bu aracı kullanma hakkında daha fazla bilgi için bkz. [Azure Ölçüm Gezgini](../../azure-monitor/essentials/metrics-getting-started.md)kullanmaya başlama. 

Boyutları destekleyen ölçümler için, ölçüyü istenen boyut değeriyle filtreleyebilirsiniz.  Azure depolamanın desteklediği boyutların tüm listesi için bkz. [ölçüm boyutları](storage-files-monitoring-reference.md#metrics-dimensions). Azure dosyaları için ölçümler şu ad uzaylardır: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/fileServices

Azure dosyalarını içeren tüm Azure Izleyici destek ölçümlerinin bir listesi için bkz. [Azure izleyici desteklenen ölçümler](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Ölçümlere erişme

> [!TIP]
> Azure CLı veya .NET örneklerini görüntülemek için burada listelenen ilgili sekmeleri seçin.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Ölçüm tanımını listeleyin

Depolama hesabınızın veya Azure dosyaları hizmetinin ölçüm tanımını listeleyebilirsiniz. [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) cmdlet 'ini kullanın.

Bu örnekte, `<resource-ID>` yer tutucuyu tüm depolama hesabının kaynak kimliği veya Azure dosyaları hizmetinin kaynak kimliği ile değiştirin.  Bu kaynak kimliklerini, Azure portal depolama hesabınızın **Özellikler** sayfalarında bulabilirsiniz.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Ölçüm değerlerini okuma

Depolama hesabınızın veya Azure dosyaları hizmetinin hesap düzeyi ölçüm değerlerini okuyabilirsiniz. [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) cmdlet 'ini kullanın.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Hesap düzeyi ölçüm tanımını listeleyin

Depolama hesabınızın veya Azure dosyaları hizmetinin ölçüm tanımını listeleyebilirsiniz. [Az Monitor ölçümleri List-Definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) komutunu kullanın.
 
Bu örnekte, `<resource-ID>` yer tutucuyu tüm depolama hesabının kaynak kimliği veya Azure dosyaları hizmetinin kaynak kimliği ile değiştirin. Bu kaynak kimliklerini, Azure portal depolama hesabınızın **Özellikler** sayfalarında bulabilirsiniz.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Hesap düzeyindeki ölçüm değerlerini oku

Depolama hesabınızın veya Azure dosyaları hizmetinin ölçüm değerlerini okuyabilirsiniz. [Az Monitor ölçümleri List](/cli/azure/monitor/metrics#az_monitor_metrics_list) komutunu kullanın.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Izleyici, ölçüm tanımını ve değerlerini okumak için [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) sağlar. [Örnek kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) , SDK 'nın farklı parametrelerle nasıl kullanılacağını gösterir. `0.18.0-preview`Depolama ölçümleri için veya sonraki bir sürümü kullanmanız gerekir.
 
Bu örneklerde, `<resource-ID>` yer tutucuyu depolama hesabının tamamının veya Azure dosyaları hizmetinin kaynak kimliği ile değiştirin. Bu kaynak kimliklerini, Azure portal depolama hesabınızın **Özellikler** sayfalarında bulabilirsiniz.

Değişkeni, `<subscription-ID>` ABONELIĞINIZIN kimliğiyle değiştirin. , Ve değerlerini elde etme konusunda rehberlik için `<tenant-ID>` `<application-ID>` `<AccessKey>` bkz. [Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](../../active-directory/develop/howto-create-service-principal-portal.md). 

#### <a name="list-the-account-level-metric-definition"></a>Hesap düzeyi ölçüm tanımını listeleyin

Aşağıdaki örnek, hesap düzeyinde bir ölçüm tanımının nasıl ekleneceğini göstermektedir:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Hesap düzeyindeki ölçüm değerlerini okuma

Aşağıdaki örnek, `UsedCapacity` Hesap düzeyinde verilerin nasıl okunacağını gösterir:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Çok boyutlu ölçüm değerlerini okuma

Çok boyutlu ölçümler için, belirli boyut değerlerinde ölçüm verilerini okumak istiyorsanız meta veri filtreleri tanımlamanız gerekir.

Aşağıdaki örnek, Multidimension destekleyen ölçümde ölçüm verilerinin nasıl okunacağını gösterir:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

# <a name="template"></a>[Şablon](#tab/template)

Yok.

---

## <a name="analyzing-logs"></a>Günlükler çözümleniyor

Kaynak günlüklerine bir depolama hesabında blob olarak, olay verileri olarak veya log analitik sorguları aracılığıyla erişebilirsiniz.

Günlüğe kaydedilen SMB ve REST işlemlerinin listesini almak için bkz. [depolama günlüğe kaydedilmiş işlemler ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) ve [Azure dosyaları izleme veri başvurusu](storage-files-monitoring-reference.md).

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, blob 'lar (Azure Data Lake Storage 2.), dosyalar, kuyruklar, tablolar, genel amaçlı v1 için Premium Depolama hesapları ve genel amaçlı v2 depolama hesapları için günlük Günlükler sunar. Klasik depolama hesapları desteklenmez.

Günlük girişleri yalnızca hizmet uç noktasında yapılan istekler varsa oluşturulur. Örneğin, bir depolama hesabının dosya uç noktasında etkinliği varsa ancak tablo ya da sıra uç noktalarında etkinlik varsa, yalnızca Azure dosya hizmeti 'ne ait günlükler oluşturulur. Azure depolama günlükleri, başarılı ve başarısız istekler hakkında bir depolama hizmetine ilişkin ayrıntılı bilgiler içerir. Bu bilgileri kullanarak istekleri ayrı ayrı izleyebilir ve depolama hizmetiyle ilgili sorunları tanılayabilirsiniz. İstekler en iyi çaba temelinde günlüğe kaydedilir.

### <a name="log-authenticated-requests"></a>Kimliği doğrulanmış istekleri günlüğe yaz

 Aşağıdaki türden kimliği doğrulanmış istekler kaydedilir:

- Başarılı istekler
- Zaman aşımı, azaltma, ağ, yetkilendirme hatalarını ve diğer hataları içeren başarısız istekler
- Başarısız ve başarılı istekler dahil olmak üzere Kerberos, NTLM veya paylaşılan erişim imzası (SAS) kullanan istekler
- Analiz verileri istekleri ( **$logs** kapsayıcısında klasik günlük verileri ve **$Metric** tablolarındaki klasik ölçüm verileri)

Azure dosyaları hizmeti tarafından yapılan, günlük oluşturma veya silme gibi bir istek günlüğe kaydedilmez. Günlüğe kaydedilen SMB ve REST isteklerinin tam listesi için bkz. [depolama günlüğe kaydedilmiş işlemler ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) ve [Azure dosyaları izleme veri başvurusu](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Depolama hesabındaki günlüklere erişme

Günlükler, hedef depolama hesabındaki bir kapsayıcıya depolanan BLOB olarak görünür. Veriler, tek bir Blobun içinde, satır sınırlı bir JSON yükü olarak toplanır ve depolanır. Blobun adı şu adlandırma kuralını izler:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Aşağıda bir örnek verilmiştir:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Bir olay hub 'ında günlüklere erişme

Bir olay hub 'ına gönderilen Günlükler dosya olarak depolanmaz, ancak olay hub 'ının günlük bilgilerini aldığını doğrulayabilirsiniz. Azure portal, Olay Hub 'ınıza gidin ve **gelen iletilerin** sayısının sıfırdan büyük olduğunu doğrulayın. 

![Denetim günlükleri](media/storage-files-monitoring/event-hub-log.png)

Güvenlik bilgileri ve olay yönetimi ve izleme araçlarını kullanarak, Olay Hub 'ınıza gönderilen günlük verilerine erişebilir ve bu verileri okuyabilirsiniz. Daha fazla bilgi için bkz. [Olay Hub 'ma gönderilen izleme verileriyle ne yapabilirim?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Log Analytics çalışma alanındaki günlüklere erişme

Azure Izleyici günlük sorgularını kullanarak, bir Log Analytics çalışma alanına gönderilen günlüklere erişebilirsiniz. Veriler **Storagefilelogs** tablosunda depolanır. 

Daha fazla bilgi için bkz. [Log Analytics öğreticisi](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="sample-kusto-queries"></a>Örnek kusto sorguları

Azure dosyalarınızı izlemenize yardımcı olması için **günlük araması** çubuğuna girebileceğiniz bazı sorgular aşağıda verilmiştir. Bu sorgular [Yeni dille](../../azure-monitor/logs/log-query-overview.md)çalışır.

> [!IMPORTANT]
> Depolama hesabı kaynak grubu menüsünden **Günlükler** ' i seçtiğinizde Log Analytics, geçerli kaynak grubu için ayarlanan sorgu kapsamıyla açılır. Bu, günlük sorgularının yalnızca söz konusu kaynak grubundaki verileri dahil edecek anlamına gelir. Diğer kaynaklardan veya diğer Azure hizmetlerinden verileri içeren bir sorgu çalıştırmak istiyorsanız, **Azure izleyici** menüsünden **Günlükler** ' i seçin. Ayrıntılar için bkz. [Azure izleyici 'de günlük sorgusu kapsamı ve zaman aralığı Log Analytics](../../azure-monitor/logs/scope.md) .

Azure dosya paylaşımlarınızı izlemenize yardımcı olması için bu sorguları kullanın:

- Son hafta içinde SMB hatalarını görüntüleme

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Son hafta içinde SMB işlemleri için bir pasta grafiği oluşturun

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Son hafta içinde REST hatalarını görüntüleme

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Son hafta içinde bir dizi REST işlemi oluşturun

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Azure dosyaları için sütun adları ve açıklamaları listesini görüntülemek için bkz. [Storagefilelogs](/azure/azure-monitor/reference/tables/storagefilelogs).

Sorguları yazma hakkında daha fazla bilgi için bkz. [Log Analytics öğreticisi](../../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="alerts"></a>Uyarılar

İzleme verilerinizde önemli koşullar bulunduğunda Azure Izleyici uyarıları size önceden bildirimde bulunur. Bunlar, müşterilerinizin sorunları fark etmeden önce sisteminizdeki sorunları tanımlamanızı ve ele belirlemenizi sağlar. [Ölçümler](../../azure-monitor/alerts/alerts-metric-overview.md), [Günlükler](../../azure-monitor/alerts/alerts-unified-log.md)ve [etkinlik günlüğü](../../azure-monitor/alerts/activity-log-alerts.md)hakkında uyarı ayarlayabilirsiniz. 

Aşağıdaki tabloda, izlemek için bazı örnek senaryolar ve uyarı için kullanılacak uygun ölçüm listelenmektedir:

| Senaryo | Uyarı için kullanılacak ölçüm |
|-|-|
| Dosya paylaşma kısıtlandı. | Ölçüm: Işlemler<br>Boyut adı: yanıt türü <br>Boyut adı: FileShare (yalnızca Premium dosya paylaşımı) |
| Dosya paylaşımının boyutu kapasitenin %80 ' dir. | Ölçüm: dosya kapasitesi<br>Boyut adı: FileShare (yalnızca Premium dosya paylaşımı) |
| Dosya paylaşımının çıkışı bir gün 500 GiB 'yi aştı. | Ölçüm: çıkış<br>Boyut adı: FileShare (yalnızca Premium dosya paylaşımı) |

### <a name="how-to-create-alerts-for-azure-files"></a>Azure dosyaları için uyarılar oluşturma

1. **Azure Portal** **depolama hesabınıza** gidin. 

2. **Uyarılar** ' a ve ardından **+ Yeni uyarı kuralı**' na tıklayın.

3. **Kaynağı Düzenle**' ye tıklayın, **dosya kaynağı türünü** seçin ve **bitti**' ye tıklayın. 

4. **Koşul Ekle** ' ye tıklayın ve uyarı için aşağıdaki bilgileri sağlayın: 

    - **Ölçüm**
    - **Boyut adı**
    - **Uyarı mantığı**

5. **Eylem grupları Ekle** ' ye tıklayın ve var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu (e-posta, SMS, vb.) ekleyin.

6. Uyarı **kuralının adı**, **açıklaması** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.

7. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' a tıklayın.

> [!NOTE]  
> Bir uyarı oluşturursanız ve çok gürültülü bir uyarı oluşturursanız eşik değerini ve uyarı mantığını ayarlayın.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Dosya paylaşma kısıtlandığında uyarı oluşturma

1. **Azure Portal** **depolama hesabınıza** gidin.
2. **İzleme** bölümünde **Uyarılar**' a ve ardından **+ Yeni uyarı kuralı**' na tıklayın.
3. **Kaynağı Düzenle**' ye tıklayın, depolama hesabı için **dosya kaynağı türünü** seçin ve **bitti**' ye tıklayın. Örneğin, depolama hesabı adı ise `contoso` `contoso/file` kaynağı seçin.
4. Koşul eklemek için **Koşul Ekle** ' ye tıklayın.
5. Depolama hesabı için desteklenen sinyallerin bir listesini göreceksiniz, **işlem** ölçümünü seçin.
6. **Sinyal mantığını Yapılandır** dikey penceresinde **Boyut adı** açılan düğmesine tıklayın ve **yanıt türü**' nü seçin.
7. **Boyut değerleri** açılan düğmesine tıklayın ve dosya paylaşımınız için uygun yanıt türlerini seçin.

    Standart dosya paylaşımları için aşağıdaki yanıt türlerini seçin:

    - Başarılı bir şekilde daraltma
    - Başarılı bir kısıtlama
    - Clientshareıopskısıtlar Lingerror

    Premium dosya paylaşımları için aşağıdaki yanıt türlerini seçin:

    - Başarılı bir şekilde daraltma
    - Başarılı bir şekilde daraltma
    - Başarılı bir şekilde daraltma
    - Clientshareegresskısıtlar Lingerror
    - Clientshareingresskısıtlar Lingerror
    - Clientshareıopskısıtlar Lingerror

   > [!NOTE]
   > Yanıt türleri **boyut değerleri** açılan listesinde listelenmemişse, kaynağın kısıtlanmayacağı anlamına gelir. Boyut değerlerini eklemek için, **boyut değerleri** açılan listesinin yanındaki **özel değer Ekle**' yi seçin, bir yönlü bir tür girin (örneğin, başarılı bir şekilde **daraltma**), **Tamam**' ı seçin ve ardından dosya paylaşımınız için geçerli tüm yanıt türlerini eklemek üzere bu adımları tekrarlayın.

8. **Premium dosya paylaşımları** için **Boyut adı** açılan düğmesine tıklayın ve **dosya paylaşımı**' nı seçin. **Standart dosya paylaşımları** için **#10 adıma** atlayın.

   > [!NOTE]
   > Dosya paylaşımı standart bir dosya paylaşımındaysa, paylaşım başına ölçümler standart dosya paylaşımları için kullanılabilir olmadığından **Dosya** paylaşımı boyutu dosya paylaşımlarını listeetmez. Depolama hesabı içindeki herhangi bir dosya paylaşımı kısıtlanırsa ve uyarı hangi dosya paylaşımının kısıtlanmadığını tanımlamıyorsa standart dosya paylaşımları için azaltma uyarıları tetiklenecektir. Standart dosya paylaşımları için paylaşım başına ölçümler kullanılamadığından, her depolama hesabı için bir dosya paylaşımının olması önerilir.

9. **Boyut değerleri** açılan düğmesine tıklayın ve uyarı vermek istediğiniz dosya paylaşımları seçin.
10. **Uyarı parametrelerini** tanımlayın (eşik değeri, işleç, toplama ayrıntı düzeyi ve değerlendirme sıklığı) ve **bitti**' ye tıklayın.

    > [!TIP]
    > Statik bir eşik kullanıyorsanız, ölçüm grafiği dosya paylaşımının kısıtlandığı durumlarda makul bir eşik değeri belirlemesine yardımcı olabilir. Dinamik bir eşik kullanıyorsanız, ölçüm grafiği, hesaplanan eşikleri son verilere göre görüntüler.

11. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya **eylem grubu** (e-posta, SMS vb.) eklemek için **eylem grupları Ekle** ' ye tıklayın.
12. Uyarı **kuralının adı**, **açıklaması** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
13. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' a tıklayın.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Azure dosya paylaşımının boyutu kapasitenin %80 ise uyarı oluşturma

1. **Azure Portal** **depolama hesabınıza** gidin.
2. **İzleme** bölümünde **Uyarılar** ' a ve ardından **+ Yeni uyarı kuralı**' na tıklayın.
3. **Kaynağı Düzenle**' ye tıklayın, depolama hesabı için **dosya kaynağı türünü** seçin ve **bitti**' ye tıklayın. Örneğin, depolama hesabı adı ise `contoso` `contoso/file` kaynağı seçin.
4. Koşul eklemek için **Koşul Ekle** ' ye tıklayın.
5. Depolama hesabı için desteklenen sinyallerin bir listesini göreceksiniz, **dosya kapasitesi** ölçümünü seçin.
6. **Premium dosya paylaşımları** için **Boyut adı** açılan düğmesine tıklayın ve **dosya paylaşımı**' nı seçin. **Standart dosya paylaşımları** için **#8 adıma** atlayın.

   > [!NOTE]
   > Dosya paylaşımı standart bir dosya paylaşımındaysa, paylaşım başına ölçümler standart dosya paylaşımları için kullanılabilir olmadığından **Dosya** paylaşımı boyutu dosya paylaşımlarını listeetmez. Standart dosya paylaşımları için uyarılar, depolama hesabındaki tüm dosya paylaşımlarını temel alır. Standart dosya paylaşımları için paylaşım başına ölçümler kullanılamadığından, her depolama hesabı için bir dosya paylaşımının olması önerilir.

7. **Boyut değerleri** açılan düğmesine tıklayın ve uyarı vermek istediğiniz dosya paylaşımları seçin.
8. **Eşik değerini** bayt cinsinden girin. Örneğin, dosya paylaşımının boyutu 100 TiB ise ve dosya paylaşımının boyutu kapasitenin %80 olduğunda bir uyarı almak istiyorsanız, bayt cinsinden eşik değeri 87960930222080.
9. **Uyarı parametrelerinin** geri kalanını tanımlayın (toplama ayrıntı düzeyi ve değerlendirme sıklığı) ve **bitti**' ye tıklayın.
10. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya **eylem grubu** (e-posta, SMS vb.) eklemek için **eylem grupları Ekle** ' ye tıklayın.
11. Uyarı **kuralının adı**, **açıklaması** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
12. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' a tıklayın.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Azure dosya paylaşımının çıkışı günde 500 GiB 'yi aşarsa uyarı oluşturma

1. **Azure Portal** **depolama hesabınıza** gidin.
2. Izleme bölümünde **Uyarılar** ' a ve ardından **+ Yeni uyarı kuralı**' na tıklayın.
3. **Kaynağı Düzenle**' ye tıklayın, depolama hesabı için **dosya kaynağı türünü** seçin ve **bitti**' ye tıklayın. Örneğin, depolama hesabı adı contoso ise, contoso/dosya kaynağını seçin.
4. Koşul eklemek için **Koşul Ekle** ' ye tıklayın.
5. Depolama hesabı için desteklenen sinyallerin bir listesini göreceksiniz, **Çıkış** ölçümünü seçin.
6. **Premium dosya paylaşımları** için **Boyut adı** açılan düğmesine tıklayın ve **dosya paylaşımı**' nı seçin. **Standart dosya paylaşımları** için **#8 adıma** atlayın.

   > [!NOTE]
   > Dosya paylaşımı standart bir dosya paylaşımındaysa, paylaşım başına ölçümler standart dosya paylaşımları için kullanılabilir olmadığından **Dosya** paylaşımı boyutu dosya paylaşımlarını listeetmez. Standart dosya paylaşımları için uyarılar, depolama hesabındaki tüm dosya paylaşımlarını temel alır. Standart dosya paylaşımları için paylaşım başına ölçümler kullanılamadığından, her depolama hesabı için bir dosya paylaşımının olması önerilir.

7. **Boyut değerleri** açılan düğmesine tıklayın ve uyarı vermek istediğiniz dosya paylaşımları seçin.
8. Eşik değeri için **536870912000** bayt girin. 
9. **Toplama ayrıntı düzeyi** açılan düğmesine tıklayın ve **24 saat**' i seçin.
10. **Değerlendirme sıklığını** seçin ve **bitti ' ye tıklayın**.
11. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya **eylem grubu** (e-posta, SMS vb.) eklemek için **eylem grupları Ekle** ' ye tıklayın.
12. Uyarı **kuralının adı**, **açıklaması** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.
13. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure dosyaları izleme veri başvurusu](storage-files-monitoring-reference.md)
- [Azure Izleyici ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure depolama ölçümleri geçişi](../common/storage-metrics-migration.md)
- [Azure Dosyalar dağıtımını planlama](./storage-files-planning.md)
- [Azure Dosyaları’nı dağıtma](./storage-how-to-create-file-share.md)
- [Windows’ta Azure Dosyaları Sorunlarını Giderme](./storage-troubleshoot-windows-file-connection-problems.md)
- [Linux’ta Azure Dosyaları Sorunlarını Giderme](./storage-troubleshoot-linux-file-connection-problems.md)
