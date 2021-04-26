---
title: Azure Blob depolamayı izleme | Microsoft Docs
description: Azure Blob depolama 'nın performansını ve kullanılabilirliğini izlemeyi öğrenin. Azure Blob depolama verilerini izleyin, yapılandırma hakkında bilgi edinin ve ölçümü ve günlük verilerini çözümleyin.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: subject-monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 464b5a6fddb724500e27a4b7d5e35fd84549565b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771632"
---
# <a name="monitoring-azure-blob-storage"></a>Azure Blob depolamayı izleme

Azure kaynaklarına dayanan önemli uygulamalarınız ve iş süreçleriniz olduğunda, bu kaynakları kullanılabilirlik, performans ve işlem için izlemek istersiniz. Bu makalede, Azure Blob depolama tarafından oluşturulan izleme verileri ve bu verilerdeki uyarıları çözümlemek için Azure Izleyici 'nin özelliklerini nasıl kullanabileceğiniz açıklanır.

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, Bloblar (Azure Data Lake Storage 2.), dosyalar, kuyruklar ve tablolar için Günlükler sunar. Bu özellik, Azure Resource Manager dağıtım modeliyle oluşturulan tüm depolama hesapları için kullanılabilir. Bkz. [depolama hesabına genel bakış](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>İzlemeye genel bakış

Her blob depolama kaynağı için Azure portal **genel bakış** sayfası, istekler ve saatlik faturalandırma gibi kaynak kullanımının kısa bir görünümünü içerir. Bu bilgiler faydalıdır, ancak yalnızca küçük miktarda izleme verisi kullanılabilir. Bu verilerden bazıları otomatik olarak toplanır ve kaynağı oluşturur almaz analiz için kullanılabilir. Bazı yapılandırma ile ek veri koleksiyonu türlerini etkinleştirebilirsiniz.

## <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?
Azure Blob depolama, Azure 'da tam yığın izleme hizmeti olan [Azure izleyici](../../azure-monitor/overview.md)'yi kullanarak izleme verileri oluşturur. Azure Izleyici, diğer bulutlardaki ve Şirket içindeki Azure kaynaklarınızı ve kaynaklarınızı izlemek için tam bir özellik kümesi sağlar. 

Azure [izleyici Ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md) makalesini izleyerek şunları açıklayan makaleyi başlatın:

- Azure İzleyici nedir?
- İzleme ile ilişkili maliyetler
- Azure 'da toplanan verileri izleme
- Veri toplamayı yapılandırma
- İzleme verilerini analiz etmek ve uyarı vermek için Azure 'da standart araçlar

Aşağıdaki bölümler, Azure depolama 'dan toplanan belirli verileri açıklayarak bu makalede derleme oluşturur. Örnekler, veri toplamayı yapılandırmayı ve bu verileri Azure araçları ile çözümlemeyi gösterir.

## <a name="monitoring-data"></a>Verileri izleme

Azure Blob depolama, [Azure kaynaklarından gelen verileri izleme](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar. 

Azure Blob depolama tarafından oluşturulan ölçümler ve günlük ölçümleri hakkında ayrıntılı bilgi için bkz. [Azure Blob depolama izleme verileri başvurusu](monitor-blob-storage-reference.md) .

Azure Izleyici 'de ölçümler ve Günlükler yalnızca depolama hesaplarını Azure Resource Manager destekler. Azure Izleyici, klasik depolama hesaplarını desteklemez. Klasik bir depolama hesabında ölçümleri veya günlükleri kullanmak istiyorsanız bir Azure Resource Manager depolama hesabına geçiş yapmanız gerekir. Bkz. [Azure Resource Manager 'ye geçirme](../../virtual-machines/migration-classic-resource-manager-overview.md).

İsterseniz klasik ölçümleri ve günlükleri kullanmaya devam edebilirsiniz. Aslında, klasik ölçümler ve Günlükler Azure Izleyici 'de ölçümler ve Günlükler ile paralel olarak kullanılabilir. Azure depolama hizmeti eski ölçümler ve günlüklerde sona erene kadar destek yerinde kalır.

## <a name="collection-and-routing"></a>Toplama ve yönlendirme

Platform ölçümleri ve etkinlik günlüğü otomatik olarak toplanır, ancak bir tanılama ayarı kullanılarak diğer konumlara yönlendirilebilir. 

Kaynak günlüklerini toplamak için bir tanılama ayarı oluşturmanız gerekir. Ayarı oluşturduğunuzda, günlüklerini etkinleştirmek istediğiniz depolama türü olarak **BLOB** ' u seçin. Ardından, günlüklerini toplamak istediğiniz aşağıdaki işlem kategorisinden birini belirtin. 

| Kategori | Açıklama |
|:---|:---|
| StorageRead | Nesnelerde okuma işlemleri. |
| StorageWrite | Nesneler üzerinde yazma işlemleri. |
| StorageDelete | Nesneler üzerinde silme işlemleri. |

> [!NOTE]
> Data Lake Storage 2., depolama türü olarak görünmez. Bunun nedeni, Data Lake Storage 2. blob depolama için kullanılabilen bir özellik kümesidir. 

## <a name="creating-a-diagnostic-setting"></a>Tanılama ayarı oluşturma

Azure portal, PowerShell, Azure CLı veya bir Azure Resource Manager şablonunu kullanarak bir tanılama ayarı oluşturabilirsiniz. 

Genel rehberlik için bkz. [Azure 'da platform günlüklerini ve ölçümlerini toplamak için tanılama ayarı oluşturma](../../azure-monitor/essentials/diagnostic-settings.md).

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, Bloblar (Azure Data Lake Storage 2.), dosyalar, kuyruklar ve tablolar için Günlükler sunar. Bu özellik, Azure Resource Manager dağıtım modeliyle oluşturulan tüm depolama hesapları için kullanılabilir. Bkz. [depolama hesabına genel bakış](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure portalı](#tab/azure-portal)

1. Azure portalında oturum açın.

2. Depolama hesabınıza gidin.

3. **İzleme** bölümünde **Tanılama ayarları (Önizleme)** öğesine tıklayın.

   > [!div class="mx-imgBorder"]
   > ![Portal-tanılama günlükleri](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. Günlüklerini etkinleştirmek istediğiniz depolama türü olarak **BLOB** ' u seçin.

5. **Tanılama ayarı Ekle**' ye tıklayın.

   > [!div class="mx-imgBorder"]
   > ![Portal-kaynak günlükleri-tanılama ayarı ekleme](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   **Tanılama ayarları** sayfası görüntülenir.

   > [!div class="mx-imgBorder"]
   > ![Kaynak günlükleri sayfası](media/monitor-blob-storage/diagnostic-logs-page.png)

6. Sayfanın **ad** alanına bu kaynak günlüğü ayarı için bir ad girin. Ardından, kütüğe kaydedilmesini istediğiniz işlemleri (okuma, yazma ve silme işlemleri) ve günlüklerin gönderilmesini istediğiniz yeri seçin.

#### <a name="archive-logs-to-a-storage-account"></a>Günlükleri bir depolama hesabına Arşivle

Günlüklerinizi bir depolama hesabına arşivlemeyi seçerseniz, depolama hesabına gönderilen günlük hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

1. **Depolama hesabı Arşivi** onay kutusunu seçin ve ardından **Yapılandır** düğmesine tıklayın.

   > [!div class="mx-imgBorder"]   
   > ![Tanılama ayarları sayfa arşiv depolaması](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. **Depolama hesabı** açılan listesinde, günlüklerinizi arşivlemek istediğiniz depolama hesabını seçin, **Tamam** düğmesine tıklayın ve ardından **Kaydet** düğmesine tıklayın.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Verme hedefi olarak bir depolama hesabı seçmeden önce, depolama hesabındaki önkoşulları anlamak için bkz. [Azure Kaynak günlüklerini arşivleme](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) .

#### <a name="stream-logs-to-azure-event-hubs"></a>Günlükleri Azure Event Hubs akış

Günlüklerinizi bir olay hub 'ına akışı yapmayı seçerseniz, Olay Hub 'ına gönderilen günlüklerin hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

1. **Bir olay hub 'ı Için akış** onay kutusunu seçin ve ardından **Yapılandır** düğmesine tıklayın.

2. **Bir olay hub 'ı seçin** bölmesinde günlüklerinizi akışa almak istediğiniz olay hub 'ının ad alanını, adını ve ilke adını seçin. 

   > [!div class="mx-imgBorder"]
   > ![Tanılama ayarları sayfası Olay Hub 'ı](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. **Tamam** düğmesine tıklayın ve ardından **Kaydet** düğmesine tıklayın.

#### <a name="send-logs-to-azure-log-analytics"></a>Azure Log Analytics günlük gönder

1. **Log Analytics gönder** onay kutusunu seçin, bir Log Analytics çalışma alanı seçin ve **Kaydet** düğmesine tıklayın.

   > [!div class="mx-imgBorder"]   
   > ![Tanılama ayarları sayfa günlüğü Analizi](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

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

`<storage-service-resource--id>`Bu kod parçacığındaki yer tutucuyu Blob hizmetinin kaynak kimliğiyle değiştirin. Depolama hesabınızın **Özellikler** sayfasını açarak Azure Portal kaynak kimliğini bulabilirsiniz.

`StorageRead` `StorageWrite` `StorageDelete` **Kategori** parametresinin değeri için, ve kullanabilirsiniz.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Aşağıda bir örnek verilmiştir:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Her parametrenin açıklaması için bkz. [Azure PowerShell aracılığıyla Azure Kaynak günlüklerini arşivleme](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Günlükleri bir olay hub 'ına akış

Günlüklerinizi bir olay hub 'ına akışı yapmayı seçerseniz, Olay Hub 'ına gönderilen günlüklerin hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 'ini parametresiyle kullanarak günlükleri etkinleştirin `EventHubAuthorizationRuleId` .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Aşağıda bir örnek verilmiştir:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Her parametrenin açıklaması için bkz. [PowerShell cmdlet 'leri aracılığıyla Event Hubs Için akış verileri](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Günlükleri Log Analytics’e gönderme

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 'ini parametresiyle kullanarak günlükleri etkinleştirin `WorkspaceId` .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Aşağıda bir örnek verilmiştir:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true }]'
```

`<storage-service-resource--id>`Bu kod parçacığındaki yer tutucuyu kaynak kimliği BLOB depolama hizmeti ile değiştirin. Depolama hesabınızın **Özellikler** sayfasını açarak Azure Portal kaynak kimliğini bulabilirsiniz.

`StorageRead` `StorageWrite` `StorageDelete` **Kategori** parametresinin değeri için, ve kullanabilirsiniz.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Aşağıda bir örnek verilmiştir:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite}]'`

Her parametrenin açıklaması için bkz. [Azure CLI aracılığıyla arşiv kaynak günlükleri](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Günlükleri bir olay hub 'ına akış

Günlüklerinizi bir olay hub 'ına akışı yapmayı seçerseniz, Olay Hub 'ına gönderilen günlüklerin hacmi için ödeme yaparsınız. Belirli fiyatlandırma için, [Azure izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) sayfasının **Platform günlükleri** bölümüne bakın.

[Az Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) komutunu kullanarak günlükleri etkinleştirin.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Aşağıda bir örnek verilmiştir:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Her parametrenin açıklaması için bkz. [Azure CLI aracılığıyla Event Hubs Için akış verileri](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Günlükleri Log Analytics’e gönderme

[Az Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) komutunu kullanarak günlükleri etkinleştirin.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Aşağıda bir örnek verilmiştir:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Daha fazla bilgi için bkz. Azure [izleyici 'de Log Analytics çalışma alanına Azure Kaynak günlüklerini akış](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Şablon](#tab/template)

Bir tanılama ayarı oluşturan Azure Resource Manager şablonunu görüntülemek için bkz. [Azure Storage Için tanılama ayarı](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Ölçümler çözümleniyor

Azure depolama ölçümlerini, Ölçüm Gezgini kullanarak diğer Azure hizmetlerinden alınan ölçümlerle çözümleyebilirsiniz. **Azure izleyici** menüsünden **ölçümler** ' i seçerek Ölçüm Gezgini açın. Bu aracı kullanma hakkında daha fazla bilgi için bkz. [Azure Ölçüm Gezgini](../../azure-monitor/essentials/metrics-getting-started.md)kullanmaya başlama. 

Bu örnek, **işlemlerin** hesap düzeyinde nasıl görüntüleneceğini gösterir.

![Azure portal ölçümlere erişme ekran görüntüsü](./media/monitor-blob-storage/access-metrics-portal.png)

Boyutları destekleyen ölçümler için, ölçüyü istenen boyut değeriyle filtreleyebilirsiniz. Bu örnek, **API adı** boyutunun değerlerini seçerek belirli bir işlemdeki hesap düzeyinde **işlemlerin** nasıl görüntüleneceğini gösterir.

![Azure portal Dimension ile ölçümlere erişme ekran görüntüsü](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Azure depolamanın desteklediği boyutların tüm listesi için bkz. [ölçüm boyutları](monitor-blob-storage-reference.md#metrics-dimensions).

Azure Blob depolama ölçümleri şu ad alanlarında: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices

Azure Blob depolamayı içeren tüm Azure Izleyici destek ölçümlerinin bir listesi için bkz. [Azure izleyici desteklenen ölçümler](../../azure-monitor/essentials/metrics-supported.md).


### <a name="accessing-metrics"></a>Ölçümlere erişme

> [!TIP]
> Azure CLı veya .NET örneklerini görüntülemek için burada listelenen ilgili sekmeleri seçin.

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Izleyici, ölçüm tanımını ve değerlerini okumak için [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) sağlar. [Örnek kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) , SDK 'nın farklı parametrelerle nasıl kullanılacağını gösterir. `0.18.0-preview`Depolama ölçümleri için veya sonraki bir sürümü kullanmanız gerekir.
 
Bu örneklerde, `<resource-ID>` yer tutucuyu tüm depolama hesabının kaynak kimliğiyle veya blob Storage hizmeti ile değiştirin. Bu kaynak kimliklerini, Azure portal depolama hesabınızın **Özellikler** sayfalarında bulabilirsiniz.

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
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Ölçüm tanımını listeleyin

Depolama hesabınızın veya blob depolama hizmetinin ölçüm tanımını listeleyebilirsiniz. [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) cmdlet 'ini kullanın.

Bu örnekte, `<resource-ID>` yer tutucuyu tüm depolama hesabının kaynak kimliğiyle veya blob depolama hizmetinin kaynak kimliği ile değiştirin.  Bu kaynak kimliklerini, Azure portal depolama hesabınızın **Özellikler** sayfalarında bulabilirsiniz.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Ölçüm değerlerini okuma

Depolama hesabınızın veya blob depolama hizmetinin hesap düzeyi ölçüm değerlerini okuyabilirsiniz. [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) cmdlet 'ini kullanın.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Hesap düzeyi ölçüm tanımını listeleyin

Depolama hesabınızın veya blob depolama hizmetinin ölçüm tanımını listeleyebilirsiniz. [Az Monitor ölçümleri List-Definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) komutunu kullanın.
 
Bu örnekte, `<resource-ID>` yer tutucuyu tüm depolama hesabının kaynak kimliğiyle veya blob depolama hizmetinin kaynak kimliği ile değiştirin. Bu kaynak kimliklerini, Azure portal depolama hesabınızın **Özellikler** sayfalarında bulabilirsiniz.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Hesap düzeyindeki ölçüm değerlerini oku

Depolama hesabınızın veya blob depolama hizmetinin ölçüm değerlerini okuyabilirsiniz. [Az Monitor ölçümleri List](/cli/azure/monitor/metrics#az_monitor_metrics_list) komutunu kullanın.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[Şablon](#tab/template)

Yok.

---

## <a name="analyzing-logs"></a>Günlükler çözümleniyor

Kaynak günlüklerine bir depolama hesabında blob olarak, olay verileri olarak veya log analitik sorguları aracılığıyla erişebilirsiniz.

Bu günlüklerde görünen alanların ayrıntılı bir başvurusu için bkz. [Azure Blob depolama izleme verileri başvurusu](monitor-blob-storage-reference.md).

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, blob 'lar (Azure Data Lake Storage 2.), dosyalar, kuyruklar, tablolar, genel amaçlı v1 için Premium Depolama hesapları ve genel amaçlı v2 depolama hesapları için günlük Günlükler sunar. Klasik depolama hesapları desteklenmez.

Günlük girişleri yalnızca hizmet uç noktasında yapılan istekler varsa oluşturulur. Örneğin, bir depolama hesabının kendi BLOB uç noktasında etkinliği varsa ancak tablo veya sıra uç noktalarında etkinlik varsa, yalnızca blob hizmetine ait günlükler oluşturulur. Azure depolama günlükleri, başarılı ve başarısız istekler hakkında bir depolama hizmetine ilişkin ayrıntılı bilgiler içerir. Bu bilgileri kullanarak istekleri ayrı ayrı izleyebilir ve depolama hizmetiyle ilgili sorunları tanılayabilirsiniz. İstekler en iyi çaba temelinde günlüğe kaydedilir.

### <a name="log-authenticated-requests"></a>Kimliği doğrulanmış istekleri günlüğe yaz

 Aşağıdaki türden kimliği doğrulanmış istekler kaydedilir:

- Başarılı istekler
- Zaman aşımı, azaltma, ağ, yetkilendirme hatalarını ve diğer hataları içeren başarısız istekler
- Başarısız ve başarılı istekler dahil, paylaşılan erişim imzası (SAS) veya OAuth kullanan istekler
- Analiz verilerine yönelik istekler ( **$logs** kapsayıcısı içindeki klasik günlük verileri ve **$Metric** tablolarındaki sınıf ölçüm verileri)

BLOB depolama hizmetinin kendisi tarafından yapılan, günlük oluşturma veya silme gibi istekleri günlüğe kaydedilmez. Günlüğe kaydedilen verilerin tam listesi için bkz. [depolama oturumu açma işlemleri ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) ve [depolama günlüğü biçimi](monitor-blob-storage-reference.md).

### <a name="log-anonymous-requests"></a>Anonim istekleri günlüğe kaydet

 Aşağıdaki anonim istek türleri günlüğe kaydedilir:

- Başarılı istekler
- Sunucu hataları
- İstemci ve sunucu için zaman aşımı hataları
- 304 hata koduyla başarısız olan istekler ALıNAMADı (değiştirilmedi)

Tüm başarısız anonim istekler günlüğe kaydedilmez. Günlüğe kaydedilen verilerin tam listesi için bkz. [depolama oturumu açma işlemleri ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) ve [depolama günlüğü biçimi](monitor-blob-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Depolama hesabındaki günlüklere erişme

Günlükler, hedef depolama hesabındaki bir kapsayıcıya depolanan BLOB olarak görünür. Veriler, tek bir Blobun içinde, satır sınırlı bir JSON yükü olarak toplanır ve depolanır. Blobun adı şu adlandırma kuralını izler:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Aşağıda bir örnek verilmiştir:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Bir olay hub 'ında günlüklere erişme

Bir olay hub 'ına gönderilen Günlükler dosya olarak depolanmaz, ancak olay hub 'ının günlük bilgilerini aldığını doğrulayabilirsiniz. Azure portal, Olay Hub 'ınıza gidin ve **gelen iletilerin** sayısının sıfırdan büyük olduğunu doğrulayın. 

![Denetim günlükleri](media/monitor-blob-storage/event-hub-log.png)

Güvenlik bilgileri ve olay yönetimi ve izleme araçlarını kullanarak, Olay Hub 'ınıza gönderilen günlük verilerine erişebilir ve bu verileri okuyabilirsiniz. Daha fazla bilgi için bkz. [Olay Hub 'ma gönderilen izleme verileriyle ne yapabilirim?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Log Analytics çalışma alanındaki günlüklere erişme

Azure Izleyici günlük sorgularını kullanarak, bir Log Analytics çalışma alanına gönderilen günlüklere erişebilirsiniz.

Daha fazla bilgi için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](../../azure-monitor/logs/log-analytics-tutorial.md).

Veriler **Storagebloblog** tablosunda depolanır. Data Lake Storage 2. Günlükler adanmış bir tabloda görünmez. Çünkü Data Lake Storage 2. hizmet değildir. Depolama hesabınızda etkinleştirebileceğiniz bir dizi özellik vardır. Bu özellikleri etkinleştirdiyseniz, Günlükler Storagebloblogları tablosunda görünmeye devam edecektir. 

#### <a name="sample-kusto-queries"></a>Örnek kusto sorguları

BLOB depolama alanınızı izlemenize yardımcı olması için **günlük araması** çubuğuna girebileceğiniz bazı sorgular aşağıda verilmiştir. Bu sorgular [Yeni dille](../../azure-monitor/logs/log-query-overview.md)çalışır.

> [!IMPORTANT]
> Depolama hesabı kaynak grubu menüsünden **Günlükler** ' i seçtiğinizde Log Analytics, geçerli kaynak grubu için ayarlanan sorgu kapsamıyla açılır. Bu, günlük sorgularının yalnızca söz konusu kaynak grubundaki verileri dahil edecek anlamına gelir. Diğer kaynaklardan veya diğer Azure hizmetlerinden verileri içeren bir sorgu çalıştırmak istiyorsanız, **Azure izleyici** menüsünden **Günlükler** ' i seçin. Ayrıntılar için bkz. [Azure izleyici 'de günlük sorgusu kapsamı ve zaman aralığı Log Analytics](../../azure-monitor/logs/scope.md) .

Azure depolama hesaplarınızı izlemenize yardımcı olması için bu sorguları kullanın:

* Son üç gün içinde en sık kullanılan 10 hatayı listelemek için.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Son üç gün içinde en çok hataya neden olan ilk 10 işlemi listelemek için.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Son üç güne kadar en uzun uçtan uca gecikme süresi ile ilk 10 işlemi listelemek için.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Son üç güne ait sunucu tarafı azaltma hatalarına neden olan tüm işlemleri listelemek için.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Son üç güne ait anonim erişime sahip tüm istekleri listelemek için.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Son üç gün içinde kullanılan bir dizi pasta grafiği oluşturmak için.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>SSS

**Azure depolama, yönetilen diskler veya yönetilmeyen diskler için ölçümleri destekliyor mu?**

Hayır. Azure Işlem, disklerdeki ölçümleri destekler. Daha fazla bilgi için bkz. [yönetilen ve yönetilmeyen diskler için disk başına ölçümler](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Blob depolama tarafından oluşturulan günlüklere ve ölçümlere yönelik bir başvuru için bkz. [Azure Blob depolama izleme verileri başvurusu](monitor-blob-storage-reference.md).
- Azure kaynaklarını izleme hakkında daha fazla bilgi için bkz. Azure [izleyici Ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md).
- Ölçüm geçişi hakkında daha fazla bilgi için bkz. [Azure Storage ölçümleri geçişi](../common/storage-metrics-migration.md).
