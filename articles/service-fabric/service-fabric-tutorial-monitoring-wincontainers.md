---
title: Windows kapsayıcılarını izleme ve tanılama
description: Bu öğreticide, Azure Service Fabric 'de Windows kapsayıcıları izleme ve Tanılama için Azure Izleyici günlüklerini yapılandırırsınız.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588238"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Öğretici: Azure Izleyici günlüklerini kullanarak Service Fabric Windows kapsayıcılarını Izleme

Bu, öğreticinin üçüncü bölümüdür ve Service Fabric üzerinde düzenlenmiş Windows Kapsayıcılarınızı izlemek için Azure Izleyici günlüklerini yapılandırmanızı sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Service Fabric kümeniz için Azure Izleyici günlüklerini yapılandırma
> * Kapsayıcılarınızdaki ve düğümlerinizdeki günlükleri görüntülemek ve sorgulamak için bir Log Analytics çalışma alanı kullanma
> * Log Analytics aracısını kapsayıcı ve düğüm ölçümlerini alacak şekilde yapılandırma

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce karşılamanız gereken ön koşullar şunlardır:

* Azure’da bir kümeye sahip olmak veya [bu öğreticide küme oluşturmak](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Buna kapsayıcılı bir uygulama dağıtmak](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Kaynak Yöneticisi şablonunda kümemenizle Azure Izleyici günlüklerini ayarlama

Bu öğreticinin ilk bölümünde [sağlanan şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) kullandıysanız, genel bir Service Fabric Azure Resource Manager şablonuna aşağıdaki eklemeler zaten yapılmıştır. Azure Izleyici günlükleri ile kapsayıcıları izlemek için ayarlamak istediğiniz bir kümeniz olması durumunda:

* Resource Manager şablonunuzda aşağıdaki değişiklikleri yapın.
* PowerShell ile [şablonu dağıtarak](./service-fabric-cluster-creation-via-arm.md) kümenizi yükseltin. Azure Resource Manager, kaynağın mevcut olduğunu algılar ve yükseltme olarak kullanıma sunar.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Azure Izleyici günlüklerini küme şablonunuza ekleme

*template.json* dosyanızda aşağıdaki değişiklikleri yapın:

1. *Parametreler* bölümünüze Log Analytics çalışma alanı konumunu ve adını ekleyin:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Bunlardan birinde kullanılan değeri değiştirmek için *template.parameters.json* dosyanıza aynı parametreleri ekleyin ve orada kullanılan değerleri değiştirin.

2. Çözüm adını ve çözümü *değişkenlerinize* ekleyin:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Microsoft Monitoring Agent’ı sanal makine uzantısı olarak ekleyin. Sanal Makine Ölçek Kümeleri kaynağını bul: *kaynaklar*  >  *"apiversion": "[değişkenler (' vmssApiVersion ')]"*.   >  *Virtualmachineprofile*  >  *extensionprofile*  >  *Extensions* Özellikler altında, *servicefabricnode* uzantısının altına aşağıdaki uzantı açıklamasını ekleyin: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Log Analytics çalışma alanını ayrı bir kaynak olarak ekleyin. *resources* kısmında, sanal makine ölçek kümeleri kaynağından sonra şunu ekleyin:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Burada](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json), gerektiğinde başvurmanız için bu değişikliklerin tümünü içeren (ve bu öğreticinin ilk bölümünde kullanılan) bir örnek şablon verilmiştir. Bu değişiklikler, kaynak grubunuza bir Log Analytics çalışma alanı ekler. Çalışma alanı, [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) aracısıyla yapılandırılan depolama tablolarından Service Fabric platform olaylarını alacak şekilde yapılandırılır. Log Analytics aracısı da (Microsoft Monitoring Agent) kümenizdeki her düğüme sanal makine uzantısı olarak eklenmiştir. Böylece siz kümenizi ölçekledikçe aracı her bir makinede otomatik olarak yapılandırılır ve aynı çalışma alanına bağlanır.

Geçerli kümenizi yükseltmek için şablonu yeni değişikliklerinizle dağıtın. Bu tamamlandıktan sonra Log Analytics kaynaklarını kaynak grubunuzda görmeniz gerekir. Küme hazır olduğunda kümeye kapsayıcılı uygulamanızı dağıtın. Sonraki adımda, kapsayıcıları izleme özelliğini ayarlayacağız.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Log Analytics çalışma alanınıza Kapsayıcı İzleme Çözümünü ekleme

Çalışma alanınızda Kapsayıcı çözümünü ayarlamak için *Kapsayıcı İzleme Çözümü* araması yapın ve İzleme ve Yönetim kategorisi altında Kapsayıcılar kaynağı oluşturun.

![Kapsayıcılar çözümü ekleme](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

*Log Analytics çalışma alanı* sorulduğunda, kaynak grubunuzda oluşturulan çalışma alanını seçin ve **Oluştur**' u seçin. Bu, Docker günlüklerini ve istatistiklerini toplamaya başlamak için şablon tarafından dağıtılan Log Analytics aracısını başlatarak çalışma alanınıza bir *kapsayıcı Izleme çözümü* ekler.

*Kaynak grubunuza* geri dönün. Yeni eklenen izleme çözümünü burada görürsünüz. Bunu seçerseniz, giriş sayfasında, çalıştırdığınız kapsayıcı görüntülerinin sayısı gösterilir.

*Öğreticinin [ikinci bölümünden](service-fabric-host-app-in-a-container.md) fabrikam kapsayıcısının beş örneğini çalıştırdığımızda unutmayın*

![Kapsayıcı çözümü giriş sayfası](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

**Kapsayıcı Izleyicisi çözümünü** seçtiğinizde, Azure izleyici günlüklerinde birden çok panelde kaydırma yapmanıza ve sorguları çalıştırmanıza olanak tanıyan daha ayrıntılı bir panoya gidecektir.

Aracı, docker günlüklerini aldığından varsayılan olarak *stdout* ve *stderr*’i gösterecek şekilde ayarlanır. Yatay olarak kaydırırsanız kapsayıcı görüntüsü envanteri, durum, ölçümler ve daha fazla yararlı veri almak için çalıştırabileceğiniz örnek sorgular görüntülenir.

![Kapsayıcı çözümü panosu](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Bu panellerden herhangi birine tıkladığınızda, görüntülenen değeri üreten kusto sorgusuna bu işlem uygulanır. *\** Çekilen tüm farklı günlük türlerini görmek için sorguyu olarak değiştirin. Buradan kapsayıcı performansını ve günlükleri sorgulayabilir, bunlar için filtreleme yapabilir veya Service Fabric platform olaylarına bakabilirsiniz. Aynı zamanda aracılarınız her düğümden sürekli olarak bir sinyal yayar. Böylece, küme yapılandırmanızın değişmesi durumunda verilerin tüm makinelerinizden toplanmaya devam ettiğinden emin olmak için bu sinyallere bakabilirsiniz.

![Kapsayıcı sorgusu](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Log Analytics aracısını performans sayaçlarını alacak şekilde yapılandırma

Log Analytics aracısını kullanmanın başka bir avantajı da, Azure tanılama aracısını yapılandırmak ve her seferinde Kaynak Yöneticisi bir şablon tabanlı yükseltme yapmak yerine, Log Analytics Kullanıcı arabirimi deneyimi aracılığıyla almak istediğiniz performans sayaçlarını değiştirme olanağıdır. Bunu yapmak için, kapsayıcı Izleme (veya Service Fabric) çözümünüzün giriş sayfasında **OMS çalışma alanı** ' nı seçin.

Bu sizi Log Analytics çalışma alanınıza yönlendirir. Burada çözümlerinizi görüntüleyebilir, özel panolar oluşturabilir ve Log Analytics aracısını yapılandırabilirsiniz. 
* Gelişmiş **Ayarlar ' ı seçerek gelişmiş** ayarlar menüsünü açın.
* Bağlı **kaynaklar**  >  **Windows Server** ' ı seçerek *5 Windows bilgisayarının bağlı* olduğunu doğrulayın.
*   >  Arama yapmak ve yeni performans sayaçlarını eklemek için veri **pencereleri performans sayaçlarını** seçin. Burada, Azure Izleyici günlüklerinden, toplayacağınız performans sayaçlarından ve diğer sayaçları aramak için kullanabileceğiniz önerilerin bir listesini göreceksiniz. Doğrulayın **Processor(_Total)\% İşlemci Zamanı** ve **Memory(*)\Available MBytes** sayaçlarının toplandığını doğrulayın.

Kapsayıcı İzleme Çözümünüzü birkaç dakika içinde **yenileyin**. Bunu yaptığınızda *Bilgisayar Performansı* verilerinin geldiğini görmeye başlarsınız. Bu, kaynaklarınızın nasıl kullanıldığını anlamanıza yardımcı olur. Kümenizi ölçeklendirme konusunda doğru kararlar vermek veya bir kümenin yükünüzü beklenen şekilde dengeleyip dengelemediğini doğrulamak için de bu ölçümleri kullanabilirsiniz.

*Not: Zaman filtrelerinin bu ölçümleri kullanabilmeniz için uygun şekilde ayarlandığından emin olun.*

![Performans sayaçları 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Service Fabric kümeniz için Azure Izleyici günlüklerini yapılandırma
> * Kapsayıcılarınızdaki ve düğümlerinizdeki günlükleri görüntülemek ve sorgulamak için bir Log Analytics çalışma alanı kullanma
> * Log Analytics aracısını kapsayıcı ve düğüm ölçümlerini alacak şekilde yapılandırma

Kapsayıcılı uygulamanız için izlemeyi yapılandırdığınıza göre, şunları deneyin:

* Bu öğreticide benzer adımları izleyerek bir Linux kümesi için Azure Izleyici günlüklerini yapılandırma. Kaynak Yöneticisi şablonunuzda değişiklik yapmak için [bu şablona](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) başvurun.
* Algılama ve tanılama konusunda yardımcı olmak üzere [otomatik uyarı](../azure-monitor/alerts/alerts-overview.md) ayarlamak Için Azure izleyici günlüklerini yapılandırın.
* Service Fabric'in kümeleriniz için yapılandırılacak [önerilen performans sayaçları](service-fabric-diagnostics-event-generation-perf.md) listesini keşfedin.
* [Günlük araması ve](../azure-monitor/logs/log-query-overview.md) Azure izleyici günlüklerinin bir parçası olarak sunulan özellikleri sorgulama ile familiarized alın.
