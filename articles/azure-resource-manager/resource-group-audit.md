---
title: Kaynakları izlemek için Azure etkinlik günlüklerini görüntüleme
description: Kullanıcı eylemlerini ve hataları gözden geçirmek için etkinlik günlüklerini kullanın. PowerShell, Azure CLı ve REST Azure portal gösterir.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 3ac407fa4cfef1530cb6bbfde2ec666b5a07e324
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150869"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Kaynaklardaki eylemleri izlemek için etkinlik günlüklerini görüntüleme

Etkinlik günlükleri ile aşağıdakileri belirleyebilirsiniz:

* aboneliğinizdeki kaynaklarda gerçekleştirilen işlemler
* işlemi kim başlattı
* işlem oluştuğunda
* işlemin durumu
* işlemi araştırmanıza yardımcı olabilecek diğer özelliklerin değerleri

Etkinlik günlüğü, kaynaklarınız için tüm yazma işlemlerini (PUT, POST, SIL) içerir. Okuma işlemlerini (GET) içermez. Kaynak eylemlerinin bir listesi için bkz. [Azure Resource Manager kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md). Sorun giderme sırasında bir hata bulmak veya kuruluşunuzdaki kullanıcının bir kaynağı nasıl değiştirdiğini izlemek için etkinlik günlüklerini kullanabilirsiniz.

Etkinlik günlükleri 90 gün boyunca tutulur. Başlangıç tarihi 90 günden eski olmamak şartıyla istediğiniz tarih aralığını sorgulayabilirsiniz.

Portal, PowerShell, Azure CLı, Öngörüler REST API veya [Öngörüler .NET kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.Insights/)aracılığıyla etkinlik günlüklerinden bilgi alabilirsiniz.

## <a name="azure-portal"></a>Azure portal

1. Etkinlik günlüklerini Portal üzerinden görüntülemek için **izleyici**' yi seçin.

    ![İzleme Seç](./media/resource-group-audit/select-monitor.png)

1. **Etkinlik günlüğü**' nü seçin.

    ![Etkinlik günlüğü seçin](./media/resource-group-audit/select-activity-log.png)

1. Son işlemlerin özetini görürsünüz. İşlemlere varsayılan bir filtre kümesi uygulanır. Özet hakkındaki bilgilere kimin ne zaman başladığını ve ne zaman oluştuğunu görürsünüz.

    ![Son işlemlerin özetini görüntüle](./media/resource-group-audit/audit-summary.png)

1. Önceden tanımlanmış bir filtre kümesini hızlı bir şekilde çalıştırmak için **hızlı içgörüler**' yi seçin.

    ![Hızlı öngörüler seçin](./media/resource-group-audit/select-quick-insights.png)

1. Seçeneklerden birini belirleyin. Örneğin, dağıtımların hatalarını görmek için **başarısız dağıtımlar** ' ı seçin.

    ![Başarısız dağıtımları seçin](./media/resource-group-audit/select-failed-deployments.png)

1. Son 24 saat içindeki dağıtım hatalarına odaklanmak için filtrelerin değiştirildiğini unutmayın. Yalnızca filtrelerle eşleşen işlemler görüntülenir.

    ![Filtreleri görüntüleme](./media/resource-group-audit/view-filters.png)

1. Belirli işlemlere odaklanmak için filtreleri değiştirin veya yenilerini uygulayın. Örneğin, aşağıdaki görüntüde **TimeSpan** ve **kaynak türü** için yeni bir değer gösterilir ve depolama hesapları olarak ayarlanır.

    ![Filtre seçeneklerini ayarla](./media/resource-group-audit/set-filter.png)

1. Sorguyu daha sonra tekrar çalıştırmanız gerekiyorsa, **Geçerli filtreleri sabitle**' yi seçin.

    ![PIN filtreleri](./media/resource-group-audit/pin-filters.png)

1. Filtreye bir ad verin.

    ![Ad filtreleri](./media/resource-group-audit/name-filters.png)

1. Filtre panoda kullanılabilir.

    ![Panoda filtre göster](./media/resource-group-audit/show-dashboard.png)

1. Portaldan, bir kaynaktaki değişiklikleri görüntüleyebilirsiniz. Izleyici ' de varsayılan görünüme dönün ve bir kaynağı değiştiren bir işlem seçin.

    ![İşlem seçin](./media/resource-group-audit/select-operation.png)

1. **Değişiklik geçmişi (Önizleme)** seçeneğini belirleyin ve kullanılabilir işlemlerden birini seçin.

    ![Değişiklik geçmişini seçin](./media/resource-group-audit/select-change-history.png)

1. Kaynaktaki değişiklikler görüntülenir.

    ![Değişiklikleri göster](./media/resource-group-audit/show-changes.png)

Değişiklik geçmişi hakkında daha fazla bilgi edinmek için bkz. [kaynak değişikliklerini alma](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Günlük girişlerini almak için **Get-AzLog** komutunu çalıştırın. Giriş listesini filtrelemek için ek parametreler sağlarsınız. Başlangıç ve bitiş zamanı belirtmezseniz, son yedi güne ait girişler döndürülür.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Aşağıdaki örnek, belirli bir süre boyunca gerçekleştirilen işlemleri araştırmak için etkinlik günlüğü 'nü nasıl kullanacağınızı gösterir. Başlangıç ve bitiş tarihleri bir tarih biçiminde belirtilir.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Veya tarih işlevlerini, son 14 gün gibi tarih aralığını belirtmek için de kullanabilirsiniz.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Belirli bir kullanıcı tarafından alınan işlemleri arayabilirsiniz.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Başarısız işlemler için filtre uygulayabilirsiniz.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Bu girdinin durum iletisine bakarak bir hataya odaklanırsınız.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Döndürülen verileri sınırlamak için belirli değerler seçebilirsiniz.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Belirttiğiniz başlangıç zamanına bağlı olarak, önceki komutlar kaynak grubu için uzun bir işlem listesi döndürebilir. Arama ölçütü sağlayarak Aradığınız kadar sonuçları filtreleyebilirsiniz. Örneğin, işlem türüne göre filtre uygulayabilirsiniz.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Kaynak grafiğini, bir kaynağın değişiklik geçmişini görmek için kullanabilirsiniz. Daha fazla bilgi için bkz. [kaynak değişikliklerini al](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Azure CLI

Günlük girişlerini almak için [az Monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) komutunu, zaman aralığını belirtmek için bir uzaklığa sahip olarak çalıştırın.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Aşağıdaki örnek, belirli bir süre boyunca gerçekleştirilen işlemleri araştırmak için etkinlik günlüğü 'nü nasıl kullanacağınızı gösterir. Başlangıç ve bitiş tarihleri bir tarih biçiminde belirtilir.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Artık mevcut olmayan bir kaynak grubu için bile belirli bir kullanıcı tarafından alınan eylemleri arayabilirsiniz.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Başarısız işlemler için filtre uygulayabilirsiniz.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Bu girdinin durum iletisine bakarak bir hataya odaklanırsınız.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Döndürülen verileri sınırlamak için belirli değerler seçebilirsiniz.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Belirttiğiniz başlangıç zamanına bağlı olarak, önceki komutlar kaynak grubu için uzun bir işlem listesi döndürebilir. Arama ölçütü sağlayarak Aradığınız kadar sonuçları filtreleyebilirsiniz. Örneğin, işlem türüne göre filtre uygulayabilirsiniz.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Kaynak grafiğini, bir kaynağın değişiklik geçmişini görmek için kullanabilirsiniz. Daha fazla bilgi için bkz. [kaynak değişikliklerini al](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST API

Etkinlik günlüğü ile çalışmaya yönelik REST işlemleri [öngörülerin](/rest/api/monitor/)bir parçasıdır REST API. Etkinlik günlüğü olaylarını almak için bkz. [bir abonelikteki yönetim olaylarını listeleme](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Sonraki adımlar

* Azure etkinlik günlükleri, aboneliğinizdeki eylemler hakkında daha fazla öngörü kazanmak için Power BI ile kullanılabilir. Bkz. [Power BI ve daha fazlasını Azure etkinlik günlüklerini görüntüleme ve çözümleme](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Güvenlik ilkelerini ayarlama hakkında bilgi edinmek için bkz. [Azure rol tabanlı Access Control](../role-based-access-control/role-assignments-portal.md).
* Altyapı katmanından uygulama dağıtımına kadar olan uygulamalardaki değişikliklerle ilgili daha fazla ayrıntı görüntülemek için bkz. [Azure izleyici 'de uygulama değişikliği analizini kullanma](../azure-monitor/app/change-analysis.md).
* Dağıtım işlemlerini görüntüleme komutları hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](resource-manager-deployment-operations.md).
* Tüm kullanıcılar için bir kaynaktaki silme işlemlerini nasıl önleyeceğinizi öğrenmek için bkz. [Azure Resource Manager ile kaynakları kilitleme](resource-group-lock-resources.md).
* Her bir Microsoft Azure Kaynak Yöneticisi sağlayıcısı için kullanılabilen işlemlerin listesini görmek için bkz. [Azure Resource Manager kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md)
