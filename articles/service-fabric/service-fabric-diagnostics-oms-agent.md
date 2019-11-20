---
title: Azure Service Fabric-Azure Izleyici günlükleriyle performans Izleme | Microsoft Docs
description: Azure Service Fabric kümeleriniz için kapsayıcıları ve performans sayaçlarını izlemek üzere Log Analytics aracısını ayarlamayı öğrenin.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 9ec68712c898eefc37a8f7b2fe2dbfdb119592de
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934000"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile performans Izleme

Bu makale, Log Analytics aracısını kümenize bir sanal makine ölçek kümesi uzantısı olarak eklemek ve mevcut Azure Log Analytics çalışma alanınıza bağlamak için gereken adımları ele alır. Bu, kapsayıcılar, uygulamalar ve performans izleme hakkında tanılama verileri toplamaya izin vermez. Bunu sanal makine ölçek kümesi kaynağına bir uzantı olarak ekleyerek Azure Resource Manager, kümeyi ölçeklendirirken bile her düğümde yüklü olmasını sağlar.

> [!NOTE]
> Bu makalede, zaten ayarlanmış bir Azure Log Analytics çalışma alanınızın olduğunu varsaymaktadır. Aksi takdirde, [Azure izleyici günlüklerini ayarlamak](service-fabric-diagnostics-oms-setup.md) için üzerine gidin

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Azure CLı aracılığıyla aracı uzantısını ekleme

Log Analytics aracısını kümenize eklemenin en iyi yolu, Azure CLı ile birlikte sunulan sanal makine ölçek kümesi API 'Leri aracılığıyla yapılır. Azure CLı 'yi henüz ayarlayıp, Azure portal ve [Cloud Shell](../cloud-shell/overview.md) bir örnek açın veya [Azure CLI 'yı kurun](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Cloud Shell istendiğinde, kaynağınız ile aynı abonelikte çalıştığınızdan emin olun. Bunu `az account show` denetleyin ve "ad" değerinin kümenizin aboneliğiyle eşleştiğinden emin olun.

2. Portalda Log Analytics çalışma alanınızın bulunduğu kaynak grubuna gidin. Log Analytics kaynağına tıklayın (kaynağın türü Log Analytics çalışma alanı olacaktır). Kaynağa genel bakış sayfasından sonra sol menüdeki ayarlar bölümünde **Gelişmiş ayarlar** ' a tıklayın.

    ![Log Analytics özellikleri sayfası](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Bir Windows kümesi ve Linux kümesi oluşturuyorsanız, **Linux sunucuları** Için **Windows Server** 'a tıklayın. Bu sayfada `workspace ID` ve `workspace key` gösterilir (portalda birincil anahtar olarak listelenir). Bir sonraki adımda her ikisine de ihtiyacınız olacak.

4. Cloud Shell `vmss extension set` API 'sini kullanarak Log Analytics aracısını kümenize yüklemek için komutunu çalıştırın:

    Bir Windows kümesi için:

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Bir Linux kümesi için:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Bir Windows kümesine eklenen Log Analytics aracısına bir örnek aşağıda verilmiştir.

    ![Log Analytics Agent CLI komutu](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Aracıyı düğümlere başarıyla eklemek için bu 15 dakikadan az sürer. `az vmss extension list` API 'sini kullanarak aracıların eklendiğini doğrulayabilirsiniz:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Aracıyı Kaynak Yöneticisi şablonu aracılığıyla ekleyin

Azure Log Analytics çalışma alanı dağıtan ve düğümlerinizin her birine bir aracı ekleyen örnek Kaynak Yöneticisi şablonları [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) veya [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS)'ta kullanılabilir.

Bu şablonu, gereksinimlerinize en uygun bir küme dağıtmak için indirebilir ve değiştirebilirsiniz.

## <a name="view-performance-counters"></a>Performans sayaçlarını görüntüleme

Artık Log Analytics aracıyı eklediğine göre, hangi performans sayaçlarını toplamak istediğinizi seçmek için Log Analytics portalına gidin.

1. Azure portal, Service Fabric Analytics çözümünü oluşturduğunuz kaynak grubuna gidin. **Servicefabric\<nameOfLog analiz Ticsworkspace\>** seçin.

2. **Log Analytics**’i tıklayın.

3. **Gelişmiş ayarlar**' a tıklayın.

4. **Veriler**' e ve ardından **Windows veya Linux performans sayaçları**' na tıklayın. Etkinleştirmek için seçebileceğiniz varsayılan sayaçların bir listesi vardır ve koleksiyon aralığını da ayarlayabilirsiniz. Ayrıca, toplanacak [ek performans sayaçları](service-fabric-diagnostics-event-generation-perf.md) ekleyebilirsiniz. Bu [makalede](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)doğru biçime başvurulur.

5. **Kaydet**' e ve ardından **Tamam**' a tıklayın.

6. Gelişmiş ayarlar dikey penceresini kapatın.

7. Genel başlık altında **çalışma alanı Özeti**' ne tıklayın.

8. Bölmeleri, Service Fabric için de dahil olmak üzere her bir çözüm için etkin bir grafik biçiminde görürsünüz. Service Fabric Analytics çözümüne devam etmek için **Service Fabric** grafiğine tıklayın.

9. İşlemsel kanal ve güvenilir hizmetler olaylarında grafik içeren birkaç kutucuk görürsünüz. Seçtiğiniz sayaçlar için akan verilerin grafik gösterimi, düğüm ölçümleri altında görünür.

10. Ek ayrıntıları görmek için kapsayıcı ölçümü grafiğine tıklayın. Ayrıca, küme olaylarına benzer şekilde performans sayacı verilerini sorgulayabilir ve düğümler, performans sayacı adı ve değerler üzerinde kusto sorgu dilini kullanarak filtre uygulayabilirsiniz.

![Log Analytics performans sayacı sorgusu](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Sonraki adımlar

* İlgili [performans sayaçlarını](service-fabric-diagnostics-event-generation-perf.md)toplayın. Log Analytics aracısını belirli performans sayaçlarını toplayacak şekilde yapılandırmak için, [veri kaynaklarını yapılandırmayı](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources)gözden geçirin.
* Algılama ve tanılama konusunda yardımcı olmak üzere [otomatik uyarı](../log-analytics/log-analytics-alerts.md) ayarlamak Için Azure izleyici günlüklerini yapılandırma
* Alternatif olarak, Azure Tanılama uzantısı aracılığıyla performans sayaçlarını toplayabilir [ve Application Insights gönderebilirsiniz](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
