---
title: Azure 'da günlüklerin ve ölçümlerin toplanması için tanılama ayarı oluştur | Microsoft Docs
description: Azure platformu günlüklerini Azure Izleyici günlüklerine, Azure depolama 'ya veya Azure Event Hubs iletmek için Tanılama ayarları oluşturun.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: b90e5ccf38e95d33c4b5b6f3b8da0e91a4facb5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023733"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Azure 'da platform günlüklerini ve ölçümlerini toplamak için tanılama ayarı oluştur
Azure 'daki [Platform günlükleri](resource-logs-overview.md) , Azure kaynakları ve bağımlı oldukları Azure platformu için ayrıntılı tanılama ve denetim bilgileri sağlar. Bu makalede, farklı hedeflere platform günlüklerini toplamak için tanılama ayarlarını oluşturma ve yapılandırma hakkında ayrıntılı bilgi verilmektedir.

Her Azure kaynağı kendi tanılama ayarını gerektirir. Tanılama ayarı bu kaynak için aşağıdakileri tanımlar:

- Ayarda tanımlanan hedeflere gönderilen günlük kategorileri ve ölçüm verileri. Kullanılabilir Kategoriler farklı kaynak türleri için farklılık gösterir.
- Günlükleri göndermek için bir veya daha fazla hedef. Geçerli hedeflere Log Analytics çalışma alanı, Event Hubs ve Azure Storage dahildir.
- Azure depolama 'da depolanan veriler için bekletme ilkesi.
 
Tek bir tanılama ayarı, hedeflerin her birinden birini tanımlayabilir. Belirli bir hedef türünden birine (örneğin, iki farklı Log Analytics çalışma alanı) birden fazla veri göndermek istiyorsanız, daha sonra birden çok ayar oluşturun. Her kaynak en fazla 5 tanılama ayarlarına sahip olabilir.

> [!NOTE]
> Etkinlik günlüğü, diğer platform günlükleriyle aynı hedeflere iletilebilir, ancak tanılama ayarlarıyla yapılandırılmamıştır. Ayrıntılar için bkz. [Azure 'Da platform günlüklerine genel bakış](platform-logs-overview.md#destinations) .

> [!NOTE]
> [Platform ölçümleri](metrics-supported.md) , [Azure izleyici ölçümlerine](data-platform-metrics.md)otomatik olarak toplanır. Tanılama ayarları, belirli Azure hizmetleri için Azure Izleyici günlüklerine yönelik ölçümleri, [günlük sorguları](../log-query/log-query-overview.md)kullanılarak diğer izleme verileriyle analiz edilmek üzere toplamak için kullanılabilir.

## <a name="destinations"></a>Yeri 
Platform günlükleri aşağıdaki tablodaki hedeflere gönderilebilir. Her bir hedefin yapılandırması, bu makalede açıklanan tanılama ayarlarını oluşturmak için aynı işlem kullanılarak gerçekleştirilir. Bu hedefe veri gönderme hakkındaki ayrıntılar için aşağıdaki tablodaki her bir bağlantıyı izleyin.

| Hedef | Açıklama |
|:---|:---|
| [Log Analytics çalışma alanı](resource-logs-collect-workspace.md) | Log Analytics çalışma alanında günlüklerin toplanması, güçlü günlük sorguları ve ayrıca uyarılar ve görselleştirmeler gibi diğer Azure Izleyici özelliklerinden yararlanmak için Azure Izleyici tarafından toplanan diğer izleme verileriyle analiz etmenizi sağlar. |
| [Olay Hub 'ları](resource-logs-stream-event-hubs.md) | Günlüklerin Event Hubs gönderilmesi, üçüncü taraf SIG 'ler ve diğer Log Analytics çözümleri gibi dış sistemlere veri akışını sağlar. |
| [Azure depolama hesabı](resource-logs-collect-storage.md) | Günlükleri bir Azure depolama hesabına arşivleme, denetim, statik analiz veya yedekleme için faydalıdır. |


> [!IMPORTANT]
> Azure Data Lake Storage 2. hesapları, Azure portal geçerli bir seçenek olarak listelenseler bile, şu anda Tanılama ayarları için hedef olarak desteklenmemektedir.

## <a name="create-diagnostic-settings-in-azure-portal"></a>Azure portal Tanılama ayarları oluşturma
Azure portal tanılama ayarlarını Azure Izleyici menüsünden ya da kaynak menüsünden yapılandırabilirsiniz.

1. Azure portal Azure Izleyici menüsünde **Ayarlar** ' ın altında **Tanılama ayarları** ' na tıklayın ve ardından kaynağa tıklayın.

    ![Tanılama ayarları](media/diagnostic-settings/menu-monitor.png)

    Ya da Azure portal kaynak menüsünde **izleyici**' nin altındaki **Tanılama ayarları** ' na tıklayın.

    ![Tanılama ayarları](media/diagnostic-settings/menu-resource.png)

2. Hiçbir ayar kaynağı varsa, istenen bir ayar oluşturmak için seçtiğiniz. **Tanılamayı aç**’a tıklayın.

   ![Tanılama ayarını - mevcut hiçbir ayar Ekle](media/diagnostic-settings/add-setting.png)

   Kaynakta mevcut ayarlar varsa, önceden yapılandırılmış ayarların bir listesini görürsünüz. Varolan bir ayarı düzenlemek için yeni bir ayar veya **düzenleme ayarı** eklemek için **Tanılama ayarı Ekle** ' ye tıklayın. Her bir ayarın hedef türlerin her biri birden fazla olamaz.

   ![Tanılama ayarını - var olan ayarları Ekle](media/diagnostic-settings/edit-setting.png)

3. Ayarınız yoksa, bir ad verin.
4. Günlükleri göndermek için her bir hedefin kutusunu işaretleyin. Ayarlarını aşağıdaki tabloda açıklandığı gibi belirtmek için **Yapılandır** ' a tıklayın.

    | Ayar | Açıklama |
    |:---|:---|
    | Log Analytics çalışma alanı | Çalışma alanının adı. |
    | Depolama hesabı | Depolama hesabının adı. |
    | Olay hub’ı ad alanı | Olay Hub 'ının oluşturulduğu ad alanı (Bu, ilk zaman akış günlükleriniz ise) veya akışa (Bu ad alanına ait günlük kategorisini akışa alınmış kaynaklar varsa).
    | Olay hub'ı adı | İsteğe bağlı olarak, ayarda tüm verileri göndermek için bir olay hub 'ı adı belirtin. Bir ad belirtmezseniz, her günlük kategorisi için bir olay hub 'ı oluşturulur. Birden çok kategori gönderiyorsanız, oluşturulan olay hub 'larının sayısını sınırlandırmak için bir ad belirtmek isteyebilirsiniz. Ayrıntılar için bkz. [Azure Event Hubs kotaları ve limitleri](../../event-hubs/event-hubs-quotas.md) . |
    | Olay hub'ı ilke adı | Akış mekanizmanın sahip olduğu izinleri tanımlar. |

    ![Tanılama ayarını - var olan ayarları Ekle](media/diagnostic-settings/setting-details.png)

5. Belirtilen hedeflere gönderilecek veri kategorilerinin her birinin kutusunu işaretleyin. **Bir depolama hesabına arşivleme**seçeneğini belirlediyseniz, [saklama süresini](resource-logs-collect-storage.md#data-retention)de belirtmeniz gerekecektir.



> [!NOTE]
> Çok boyutlu ölçümlerin tanılama ayarları aracılığıyla gönderilmesi şu anda desteklenmemektedir. Boyutlu ölçümler, boyut değerlerinin toplamı alınarak düzleştirilmiş tek yönlü ölçümler olarak dışarı aktarılır.
>
> *Örneğin*: Bir Olay Hub'ındaki 'Gelen İletiler' ölçümü, kuyruk düzeyi temelinde araştırılıp grafiği oluşturulabilir. Ancak, tanılama ayarları aracılığıyla dışarı aktarılan ölçüm, Olay Hub’ındaki tüm kuyruklarda tüm gelen iletiler halinde ifade edilir.

4. **Save (Kaydet)** düğmesine tıklayın.

Birkaç dakika sonra, yeni ayar bu kaynak için ayarlar listenizde görünür ve yeni olay verileri oluşturulmasıyla Günlükler belirtilen hedeflere akışla kaydedilir. Bir olayın ne zaman yayınlandığına ve [bir Log Analytics çalışma alanında göründüğünde](data-ingestion-time.md)on beş dakika sürebilir.



## <a name="create-diagnostic-settings-using-powershell"></a>PowerShell kullanarak tanılama ayarları oluşturma
[Azure PowerShell](powershell-quickstart-samples.md)bir tanılama ayarı oluşturmak için [set-azdiagnosticsetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet 'ini kullanın. Bu cmdlet 'in parametrelerinin açıklamaları için belgelerine bakın.

Aşağıda, üç hedefi kullanarak bir tanılama ayarı oluşturan örnek bir PowerShell cmdlet 'i verilmiştir.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Azure CLı kullanarak tanılama ayarları oluşturma
[Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)ile bir tanılama ayarı oluşturmak için [az Monitor Diagnostic-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) komutunu kullanın. Parametrelerinin açıklamaları için bu komuta yönelik belgelere bakın.

Aşağıda, üç hedefi kullanarak bir tanılama ayarı oluşturmak için örnek bir CLı komutu verilmiştir.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>REST API kullanarak tanılama ayarlarını yapılandırma
[Azure izleyici REST API](https://docs.microsoft.com/rest/api/monitor/)kullanarak tanılama ayarlarını oluşturmak veya güncelleştirmek için bkz. [Tanılama ayarları](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) .


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak tanılama ayarlarını yapılandırma
Bkz. bir Kaynak Yöneticisi şablonuyla tanılama ayarlarını oluşturmak veya güncelleştirmek için [bir kaynak yöneticisi şablonu kullanarak kaynak oluşturmada tanılama ayarlarını otomatik olarak etkinleştir](diagnostic-settings-template.md) .

## <a name="next-steps"></a>Sonraki adımlar

* [Azure platformu günlükleri hakkında daha fazla bilgi edinin](resource-logs-overview.md)