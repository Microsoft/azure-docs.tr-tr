---
title: Azure Izleyici günlükleri ile performans Izleme
description: Azure Service Fabric kümeleriniz için kapsayıcıları ve performans sayaçlarını izlemek üzere Log Analytics aracısını ayarlamayı öğrenin.
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 9bb89dc2eebe584a0a9f81a6707c0a2e4fa2fc30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626682"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile performans Izleme

Bu makale, Log Analytics aracısını kümenize bir sanal makine ölçek kümesi uzantısı olarak eklemek ve mevcut Azure Log Analytics çalışma alanınıza bağlamak için gereken adımları ele alır. Bu, kapsayıcılar, uygulamalar ve performans izleme hakkında tanılama verileri toplamaya izin vermez. Bunu sanal makine ölçek kümesi kaynağına bir uzantı olarak ekleyerek Azure Resource Manager, kümeyi ölçeklendirirken bile her düğümde yüklü olmasını sağlar.

> [!NOTE]
> Bu makalede, zaten ayarlanmış bir Azure Log Analytics çalışma alanınızın olduğunu varsaymaktadır. Aksi takdirde, [Azure izleyici günlüklerini ayarlamak](service-fabric-diagnostics-oms-setup.md) için üzerine gidin

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Azure CLı aracılığıyla aracı uzantısını ekleme

Log Analytics aracısını kümenize eklemenin en iyi yolu, Azure CLı ile birlikte sunulan sanal makine ölçek kümesi API 'Leri aracılığıyla yapılır. Azure CLı 'yi henüz ayarlayıp, Azure portal ve [Cloud Shell](../cloud-shell/overview.md) bir örnek açın veya [Azure CLI 'yı kurun](/cli/azure/install-azure-cli).

1. Cloud Shell istendiğinde, kaynağınız ile aynı abonelikte çalıştığınızdan emin olun. Bunu ile denetleyin `az account show` ve "ad" değerinin kümenizin aboneliğiyle eşleştiğinden emin olun.

2. Portalda Log Analytics çalışma alanınızın bulunduğu kaynak grubuna gidin. Log Analytics kaynağına tıklayın (kaynağın türü Log Analytics çalışma alanı olacaktır). Kaynağa genel bakış sayfasından sonra sol menüdeki ayarlar bölümünde **Gelişmiş ayarlar** ' a tıklayın.

    ![Log Analytics özellikleri sayfası](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Bir Windows kümesi ve Linux kümesi oluşturuyorsanız, **Linux sunucuları** Için **Windows Server** 'a tıklayın. Bu sayfada size `workspace ID` ve görüntülenir `workspace key` (portalda birincil anahtar olarak listelenir). Bir sonraki adımda her ikisine de ihtiyacınız olacak.

4. API 'yi kullanarak Log Analytics aracısını kümenize yüklemek için komutunu çalıştırın `vmss extension set` :

    Bir Windows kümesi için:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Bir Linux kümesi için:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Bir Windows kümesine eklenen Log Analytics aracısına bir örnek aşağıda verilmiştir.

    ![Log Analytics Agent CLI komutu](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Aracıyı düğümlere başarıyla eklemek için bu 15 dakikadan az sürer. API 'leri kullanarak aracıların eklendiğini doğrulayabilirsiniz `az vmss extension list` :

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Aracıyı Kaynak Yöneticisi şablonu aracılığıyla ekleyin

Azure Log Analytics çalışma alanı dağıtan ve düğümlerinizin her birine bir aracı ekleyen örnek Kaynak Yöneticisi şablonları [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) veya [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS)'ta kullanılabilir.

Bu şablonu, gereksinimlerinize en uygun bir küme dağıtmak için indirebilir ve değiştirebilirsiniz.

## <a name="view-performance-counters"></a>Performans sayaçlarını görüntüleme

Artık Log Analytics aracıyı eklediğine göre, hangi performans sayaçlarını toplamak istediğinizi seçmek için Log Analytics portalına gidin.

1. Azure portal, Service Fabric Analytics çözümünü oluşturduğunuz kaynak grubuna gidin. **Servicefabric \<nameOfLog AnalyticsWorkspace\>**' i seçin.

2. **Log Analytics**’i tıklayın.

3. **Gelişmiş ayarlar**' a tıklayın.

4. **Veriler**' e ve ardından **Windows veya Linux performans sayaçları**' na tıklayın. Etkinleştirmek için seçebileceğiniz varsayılan sayaçların bir listesi vardır ve koleksiyon aralığını da ayarlayabilirsiniz. Ayrıca, toplanacak [ek performans sayaçları](service-fabric-diagnostics-event-generation-perf.md) ekleyebilirsiniz. Bu [makalede](/windows/win32/perfctrs/specifying-a-counter-path)doğru biçime başvurulur.

5. **Kaydet**' e ve ardından **Tamam**' a tıklayın.

6. Gelişmiş ayarlar dikey penceresini kapatın.

7. Genel başlık altında **çalışma alanı Özeti**' ne tıklayın.

8. Bölmeleri, Service Fabric için de dahil olmak üzere her bir çözüm için etkin bir grafik biçiminde görürsünüz. Service Fabric Analytics çözümüne devam etmek için **Service Fabric** grafiğine tıklayın.

9. İşlemsel kanal ve güvenilir hizmetler olaylarında grafik içeren birkaç kutucuk görürsünüz. Seçtiğiniz sayaçlar için akan verilerin grafik gösterimi, düğüm ölçümleri altında görünür.

10. Ek ayrıntıları görmek için kapsayıcı ölçümü grafiğine tıklayın. Ayrıca, küme olaylarına benzer şekilde performans sayacı verilerini sorgulayabilir ve düğümler, performans sayacı adı ve değerler üzerinde kusto sorgu dilini kullanarak filtre uygulayabilirsiniz.

![Log Analytics performans sayacı sorgusu](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Sonraki adımlar

* İlgili [performans sayaçlarını](service-fabric-diagnostics-event-generation-perf.md)toplayın. Log Analytics aracısını belirli performans sayaçlarını toplayacak şekilde yapılandırmak için, [veri kaynaklarını yapılandırmayı](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources)gözden geçirin.
* Algılama ve tanılama konusunda yardımcı olmak üzere [otomatik uyarı](../azure-monitor/alerts/alerts-overview.md) ayarlamak Için Azure izleyici günlüklerini yapılandırma
* Alternatif olarak, Azure Tanılama uzantısı aracılığıyla performans sayaçlarını toplayabilir [ve Application Insights gönderebilirsiniz](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
