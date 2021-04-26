---
title: Azure Izleyici 'de çözümleri izleme | Microsoft Docs
description: Azure Izleyici 'de izleme çözümleri, belirli bir sorun alanı etrafında ölçüm sağlayan bir mantık, görselleştirme ve veri alma kuralları koleksiyonudur.  Bu makale, izleme çözümlerini yükleme ve kullanma hakkında bilgi sağlar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 6a98571f513e25d801acd4f4a9c2901dcd56fabc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869230"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Izleyici 'de çözümleri izleme

Azure Izleyici 'de çözüm izleme, belirli bir Azure uygulaması veya hizmeti işleminin analizini sağlar. Bu makalede, Azure 'daki çözümleri izleme hakkında kısa bir genel bakış ve bunları kullanma ve yükleme hakkındaki ayrıntılar sunulmaktadır. Azure Izleyici 'ye, kullandığınız tüm uygulama ve hizmetler için izleme çözümleri ekleyebilirsiniz. Bunlar genellikle ücretsiz olarak kullanılabilir ancak kullanım ücretlerini çağırabilecek veri toplar.

## <a name="use-monitoring-solutions"></a>İzleme çözümlerini kullanma

Azure Izleyici 'de çözümlere **genel bakış** sayfası, Log Analytics çalışma alanında yüklü her bir çözüm için bir kutucuk görüntüler. Bu sayfayı açmak için [Azure Portal](https://ms.portal.azure.com) **Azure izleyici** 'ye gidin. **Öngörüler** menüsünde, **Öngörüler** merkezini açmak için **diğer** ' i seçin ve ardından **Log Analytics çalışma alanları**' na tıklayın.

[![Öngörüler Merkezi](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Çalışma alanını veya kutucuklar için kullanılan zaman aralığını değiştirmek için ekranın üst kısmındaki açılan kutuları kullanın. Bir çözümün kutucuğuna tıklayarak toplanan verilerin daha ayrıntılı analizini içeren görünümünü açın.

[![Ekran görüntüsü, çözümler bölmesinde seçili çözümlerin ve çözümlerin gösterildiği Azure portal menüsünü gösterir.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

İzleme çözümleri birden çok Azure kaynağı türü içerebilir ve bir çözüme dahil olan tüm kaynakları diğer kaynaklar gibi görüntüleyebilirsiniz. Örneğin, çözüme dahil edilen tüm günlük sorguları, [sorgu Gezgini](../logs/log-analytics-tutorial.md)'Ndeki **çözüm sorguları** altında listelenir. [Günlük sorgularıyla](../logs/log-query-overview.md)geçici analiz gerçekleştirirken bu sorguları kullanabilirsiniz.

## <a name="list-installed-monitoring-solutions"></a>Yüklü izleme çözümlerini Listele

### <a name="portal"></a>[Portal](#tab/portal)

Aboneliğinizde yüklü izleme çözümlerini listelemek için aşağıdaki yordamı kullanın.

1. [Azure Portal](https://ms.portal.azure.com) gidin. **Çözümler** arayın ve seçin.
1. Çalışma alanlarınızın tümünde yüklü olan çözümler listelenir. Çözümün adının ardından yüklendiği çalışma alanının adı gelir.
1. Abonelik veya kaynak grubuna göre filtrelemek için ekranın üst kısmındaki açılan kutuları kullanın.

![Tüm çözümleri Listele](media/solutions/list-solutions-all.png)

Bir çözümün adına tıklayarak Özet sayfasını açın. Bu sayfada çözüme dahil edilen görünümler görüntülenir ve çözümün kendisi ve çalışma alanı için farklı seçenekler sunulur. Çözümleri listelemek için Yukarıdaki yordamlardan birini kullanarak bir çözümün Özet sayfasını görüntüleyin ve sonra çözümün adına tıklayın.

![Çözüm özellikleri](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aboneliğinizde yüklü izleme çözümlerini listelemek için [az Monitor Log-Analytics çözüm List](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) komutunu kullanın.   Komutu çalıştırmadan önce `list` , [Izleme çözümü yüklerken](#install-a-monitoring-solution)bulunan önkoşulları izleyin.

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Aboneliğinizde yüklü izleme çözümlerini listelemek için [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) cmdlet 'ini kullanın. Bu komutları çalıştırmadan önce, [izleme çözümü yüklerken](#install-a-monitoring-solution)bulunan önkoşulları izleyin.

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>İzleme çözümü yüklemesi

### <a name="portal"></a>[Portal](#tab/portal)

Microsoft ve iş ortaklarından izleme çözümlerini [Azure Marketi](https://azuremarketplace.microsoft.com)' nden edinebilirsiniz. Aşağıdaki yordamı kullanarak, kullanılabilir çözümleri arayabilir ve bunları yükleyebilirsiniz. Bir çözüm yüklediğinizde, çözümün yükleneceği ve verilerinin toplanacağı bir [Log Analytics çalışma alanı](../logs/manage-access.md) seçmeniz gerekir.

1. [Aboneliğiniz için çözümler listesinden](#list-installed-monitoring-solutions) **Ekle**' ye tıklayın.
1. Bir çözüme gözatıp arayın. Ayrıca, [Bu arama bağlantısından](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)çözümlere da gidebilirsiniz.
1. İstediğiniz izleme çözümünü bulun ve açıklamasını okuyun.
1. Yükleme işlemini başlatmak için **Oluştur** ' a tıklayın.
1. Yükleme işlemi başladığında Log Analytics çalışma alanını belirtmeniz ve çözüm için gerekli tüm yapılandırmaları sağlamanız istenir.

![Çözüm yüklemesi](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Topluluktan bir çözüm yükler

Topluluk üyeleri, yönetim çözümlerini Azure hızlı başlangıç şablonlarına gönderebilir. Bu çözümleri doğrudan yükleyebilir veya daha sonra yüklenmek üzere şablonları indirebilirsiniz.

1. Çalışma alanını ve hesabı bağlamak için [Log Analytics çalışma alanı ve Otomasyon hesabı](#log-analytics-workspace-and-automation-account) ' nda açıklanan süreci izleyin.
2. [Azure hızlı başlangıç şablonlarına](https://azure.microsoft.com/documentation/templates/)gidin.
3. İlgilendiğiniz bir çözüm arayın.
4. Ayrıntılarını görüntülemek için sonuçlardan çözümü seçin.
5. **Azure 'A dağıt** düğmesine tıklayın.
6. Çözümdeki parametrelerin değerlerinin yanı sıra kaynak grubu ve konum gibi bilgileri sağlamanız istenir.
7. Çözümü yüklemek için **satın al** ' a tıklayın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Ortamınızı hazırlama

1. Azure CLI'yi yükleme

   CLı başvuru komutlarını çalıştırmadan önce [Azure CLI 'yı yüklemeniz](/cli/azure/install-azure-cli) gerekir.  İsterseniz, bu makaledeki adımları tamamlayabilmeniz için Azure Cloud Shell de kullanabilirsiniz.  Azure Cloud Shell, tarayıcınız aracılığıyla kullandığınız etkileşimli bir kabuk ortamıdır.  Aşağıdaki yöntemlerden birini kullanarak Cloud Shell başlatın:

   - Cloud Shell giderek açın [https://shell.azure.com](https://shell.azure.com)

   - [Azure Portal](https://portal.azure.com) sağ üst köşedeki menü çubuğunda bulunan **Cloud Shell** düğmesini seçin

1. Oturum açın.

   CLı 'nın yerel bir yüklemesini kullanıyorsanız [az Login](/cli/azure/reference-index#az_login) komutunu kullanarak oturum açın.  Terminalinizde görüntülenen adımları uygulayarak kimlik doğrulama işlemini tamamlayın.

    ```azurecli
    az login
    ```

1. Uzantıyı yükler `log-analytics-solution`

   Bu `log-analytics-solution` komut, çekirdek Azure CLI 'nın deneysel uzantısıdır. Uzantı başvuruları hakkında daha fazla bilgi için bkz. [Azure CLI ile uzantı kullanımı](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   Aşağıdaki uyarı beklenmektedir.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Azure CLı ile bir çözüm yüklemesi

Bir çözüm yüklediğinizde, çözümün yükleneceği ve verilerinin toplanacağı bir [Log Analytics çalışma alanı](../logs/manage-access.md) seçmeniz gerekir.  Azure CLı ile, [az Monitor Log-Analytics çalışma alanı](/cli/azure/monitor/log-analytics/workspace) başvuru komutlarını kullanarak çalışma alanlarını yönetirsiniz.  Çalışma alanını ve hesabı bağlamak için [Log Analytics çalışma alanı ve Otomasyon hesabı](#log-analytics-workspace-and-automation-account) ' nda açıklanan süreci izleyin.

İzleme çözümü yüklemek için [az Monitor Log-Analytics çözüm Create](/cli/azure/monitor/log-analytics/solution) ' i kullanın.  Köşeli parantezler içindeki parametreler isteğe bağlıdır.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Bu, OMSGallery/Container plan ürünü için bir Log Analytics çözümü oluşturan bir kod örneğidir.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Ortamınızı hazırlama

1. Azure PowerShell'i yükleme

   Azure PowerShell başvuru komutlarını çalıştırmadan önce [Azure PowerShell yüklemeniz](/powershell/azure/install-az-ps) gerekir. İsterseniz, bu makaledeki adımları tamamlayabilmeniz için Azure Cloud Shell de kullanabilirsiniz. Azure Cloud Shell, tarayıcınız aracılığıyla kullandığınız etkileşimli bir kabuk ortamıdır. Aşağıdaki yöntemlerden birini kullanarak Cloud Shell başlatın:

   - Cloud Shell giderek açın [https://shell.azure.com](https://shell.azure.com)

   - [Azure Portal](https://portal.azure.com) sağ üst köşedeki menü çubuğunda bulunan **Cloud Shell** düğmesini seçin

   > [!IMPORTANT]
   > **Az. MonitoringSolutions** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Oturum açın.

   PowerShell 'in yerel bir yüklemesini kullanıyorsanız [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'ini kullanarak oturum açın. Kimlik doğrulama işlemini gerçekleştirmek için PowerShell 'de görünen adımları izleyin.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Azure PowerShell bir çözüm yüklerken

Bir çözüm yüklediğinizde, çözümün yükleneceği ve verilerinin toplanacağı bir [Log Analytics çalışma alanı](../logs/manage-access.md) seçmeniz gerekir. Azure PowerShell, [az. MonitoringSolutions](/powershell/module/az.monitoringsolutions) PowerShell modülündeki cmdlet 'leri kullanarak çalışma alanlarını yönetirsiniz. Çalışma alanını ve hesabı bağlamak için [Log Analytics çalışma alanı ve Otomasyon hesabı](#log-analytics-workspace-and-automation-account) ' nda açıklanan süreci izleyin.

Bir izleme çözümü yüklemek için [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) cmdlet 'ini kullanın. Köşeli parantezler içindeki parametreler isteğe bağlıdır.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

Aşağıdaki örnek, Log Analytics çalışma alanı için bir izleme günlüğü analizi çözümü oluşturur.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics çalışma alanı ve Otomasyon hesabı

Tüm izleme çözümleri, çözüm tarafından toplanan verileri depolamak ve günlük aramalarını ve görünümlerini barındırmak için bir [Log Analytics çalışma alanı](../logs/manage-access.md) gerektirir. Bazı çözümler ayrıca runbook 'ları ve ilgili kaynakları içeren bir [Otomasyon hesabı](../../automation/automation-security-overview.md) gerektirir. Çalışma alanı ve hesap aşağıdaki gereksinimlere uymalıdır.

* Her bir çözümün yüklemesi yalnızca bir Log Analytics çalışma alanı ve bir Otomasyon hesabı kullanabilir. Çözümü birden çok çalışma alanına ayrı olarak yükleyebilirsiniz.
* Bir çözüm bir Otomasyon hesabı gerektiriyorsa, Log Analytics çalışma alanı ve Otomasyon hesabı bir diğeri ile bağlantılı olmalıdır. Log Analytics çalışma alanı yalnızca bir Otomasyon hesabına bağlanabilir ve bir Otomasyon hesabı yalnızca bir Log Analytics çalışma alanına bağlanabilir.

Azure Marketi aracılığıyla bir çözüm yüklediğinizde, sizden bir çalışma alanı ve Otomasyon hesabı istenir. Bunlar arasındaki bağlantı, henüz bağlanmadıysa oluşturulur.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics çalışma alanı ve Otomasyon hesabı arasındaki bağlantıyı doğrulama

Aşağıdaki yordamı kullanarak bir Log Analytics çalışma alanı ve Otomasyon hesabı arasındaki bağlantıyı doğrulayabilirsiniz.

1. Azure portal Otomasyon hesabını seçin.
1. Menünün **Ilgili kaynaklar** bölümüne gidin ve **bağlantılı çalışma alanı**' nı seçin.
1. **Çalışma alanı** bir Otomasyon hesabına bağlanmışsa, Bu sayfa bağlandığı çalışma alanını listeler. Listelenen çalışma alanının adını seçerseniz, bu çalışma alanının genel bakış sayfasına yönlendirilirsiniz.

## <a name="remove-a-monitoring-solution"></a>İzleme çözümünü kaldırma

### <a name="portal"></a>[Portal](#tab/portal)

Portalı kullanarak yüklü bir çözümü kaldırmak için, [yüklü çözümler listesinde](#list-installed-monitoring-solutions)bulun. Çözümün adına tıklayarak Özet sayfasını açın ve ardından **Sil**' e tıklayın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı 'yı kullanarak yüklü bir çözümü kaldırmak için [az Monitor Log-Analytics çözüm Delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete) komutunu kullanın.

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell kullanarak yüklü bir çözümü kaldırmak için [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) cmdlet 'ini kullanın.

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft 'un izleme çözümlerinin bir listesini](../monitor-reference.md)alın.
* İzleme çözümleri tarafından toplanan verileri analiz etmek için [sorgular oluşturmayı](../logs/log-query-overview.md) öğrenin.
* [Azure izleyici için tüm Azure CLI komutlarına](/cli/azure/azure-cli-reference-for-monitor)bakın.