---
title: Azure Izleyici günlükleri ile izlemeyi ayarlama
description: Azure Service Fabric kümelerinizi izlemek üzere olayları görselleştirme ve çözümleme için Azure Izleyici günlüklerini ayarlamayı öğrenin.
ms.topic: conceptual
ms.date: 02/20/2019
ms.openlocfilehash: 79dbc38f9d177bc3decf11673a68ce434f9a83bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625968"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Küme için Azure Izleyici günlüklerini ayarlama

Azure Izleyici günlükleri, küme düzeyindeki olayları izlemek için önerimiz. Log Analytics çalışma alanını Azure Resource Manager, PowerShell veya Azure Marketi aracılığıyla ayarlayabilirsiniz. İleride kullanılmak üzere dağıtımınızın güncelleştirilmiş bir Kaynak Yöneticisi şablonunu korudıysanız, Azure Izleyici günlükleri ortamınızı ayarlamak için aynı şablonu kullanın. Tanılama özelliği etkinken dağıtılmış bir kümeniz zaten varsa, Market aracılığıyla dağıtım daha kolay olur. Dağıtım yaptığınız hesapta abonelik düzeyinde erişim yoksa, PowerShell veya Kaynak Yöneticisi şablonu kullanarak dağıtın.

> [!NOTE]
> Kümenizi izlemek üzere Azure Izleyici günlüklerini ayarlamak için, küme düzeyi veya platform düzeyi olaylarını görüntülemek için tanılamayı etkinleştirmeniz gerekir. [Windows kümelerinde tanılamayı ayarlama](service-fabric-diagnostics-event-aggregation-wad.md) ve daha fazla bilgi için [Linux kümelerinde tanılamayı ayarlama](service-fabric-diagnostics-oms-syslog.md) bölümüne bakın

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Azure Marketi 'ni kullanarak Log Analytics çalışma alanı dağıtma

Bir kümeyi dağıttıktan sonra bir Log Analytics çalışma alanı eklemek istiyorsanız, portalda Azure Marketi ' ne gidin ve **Service Fabric Analytics** arayın. Bu, Service Fabric özgü verileri olan Service Fabric dağıtımlar için özel bir çözümdür. Bu işlemde hem çözümü (öngörüleri görüntülemek için Pano) ve çalışma alanını (temel alınan küme verilerinin toplamı) oluşturacaksınız.

1. Sol gezinti menüsünde **Yeni** ' yi seçin. 

2. **Service Fabric Analytics** arayın. Görüntülenen kaynağı seçin.

3. **Oluştur**’u seçin.

    ![Market 'te Service Fabric Analytics](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Service Fabric Analytics oluşturma penceresinde **OMS çalışma** alanı alanı için **bir çalışma alanı seç** ' i seçin ve ardından **Yeni bir çalışma alanı oluşturun**. Gerekli girdileri doldurun. Buradaki tek gereksinim, Service Fabric kümesi için aboneliğin ve çalışma alanının aynı olduğu gereksinimdir. Girdlarınız doğrulandıktan sonra, çalışma alanınız dağıtmaya başlar. Dağıtım yalnızca birkaç dakika sürer.

5. İşiniz bittiğinde Service Fabric Analytics oluşturma penceresinin alt kısmında yeniden **Oluştur** ' u seçin. Yeni çalışma alanının **OMS çalışma alanı** altında göründüğünden emin olun. Bu eylem, çözümü oluşturduğunuz çalışma alanına ekler.

Windows kullanıyorsanız, Azure Izleyici günlüklerini küme olaylarınızın depolandığı depolama hesabına bağlamak için aşağıdaki adımlarla devam edin. 

>[!NOTE]
>Service Fabric Analytics çözümü yalnızca Windows kümeleri için desteklenir. Linux kümeleri için, [Linux kümeleri Için Azure izleyici günlüklerini ayarlama](service-fabric-diagnostics-oms-syslog.md)makalesindeki makaleye göz atın.  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Log Analytics çalışma alanını kümenize bağlama 

1. Çalışma alanının, kümeinizden gelen tanılama verilerine bağlanması gerekir. Service Fabric Analytics çözümünü oluşturduğunuz kaynak grubuna gidin. **Servicefabric \<nameOfWorkspace\>** ' i seçin ve genel bakış sayfasına gidin. Buradan çözüm ayarlarını, çalışma alanı ayarlarını değiştirebilir ve Log Analytics çalışma alanına erişebilirsiniz.

2. Sol gezinti menüsünde, **çalışma alanı veri kaynakları** altında, **depolama hesapları günlükleri**' ni seçin.

3. **Depolama hesabı günlükleri** sayfasında, kümenin günlüklerini çalışma alanına eklemek için en üstteki **Ekle** ' yi seçin.

4. Kümenizde oluşturulan uygun hesabı eklemek için **depolama hesabı** ' nı seçin. Varsayılan adı kullandıysanız, depolama hesabı **sfdg \<resourceGroupName\>**' dir. Bunu, **Applicationdiagnosticsstorageaccountname** için kullanılan değeri denetleyerek kümenizi dağıtmak için kullanılan Azure Resource Manager şablonuyla da doğrulayabilirsiniz. Ad görünmüyorsa, aşağı kaydırın ve **daha fazla yükle**' yi seçin. Depolama hesabı adını seçin.

5. Veri türünü belirtin. **Olayları Service Fabric** olarak ayarlayın.

6. Kaynağın otomatik olarak **Wadservicefabric \* eventtable** olarak ayarlandığından emin olun.

7. Çalışma alanınızı kümenizin günlüklerine bağlamak için **Tamam ' ı** seçin.

    ![Azure Izleyici günlüklerine depolama hesabı günlükleri ekleme](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Hesap artık çalışma alanınızın veri kaynaklarındaki depolama hesabı günlüklerinizin bir parçası olarak gösterilir.

Service Fabric Analytics çözümünü artık kümenizin platformu ve uygulama günlüğü tablosuna doğru şekilde bağlanmış bir Log Analytics çalışma alanına eklediniz. Aynı şekilde, çalışma alanına başka kaynaklar da ekleyebilirsiniz.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Azure Izleyici günlüklerini Azure Resource Manager ile dağıtma

Bir Kaynak Yöneticisi şablonu kullanarak bir küme dağıttığınızda, şablon yeni bir Log Analytics çalışma alanı oluşturur, Service Fabric çözümünü çalışma alanına ekler ve uygun depolama tablolarından verileri okuyacak şekilde yapılandırır.

Gereksinimlerinizi karşılamak için [Bu örnek şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) kullanabilir ve değiştirebilirsiniz. Bu şablon şunları yapar

* Bir 5 düğümlü Service Fabric kümesi oluşturur
* Log Analytics çalışma alanı ve Service Fabric çözümü oluşturur
* Log Analytics aracısını, çalışma alanına 2 örnek performans sayaçlarını toplayıp gönderecek şekilde yapılandırır
* WAD 'yi Service Fabric toplayacak ve bunları Azure depolama tablolarına (WADServiceFabric * EventTable) gönderecek şekilde yapılandırır
* Bu tablolardaki olayları okumak için Log Analytics çalışma alanını yapılandırır


Azure PowerShell modülündeki API 'yi kullanarak, şablonu kümenize Kaynak Yöneticisi yükseltmesi olarak dağıtabilirsiniz `New-AzResourceGroupDeployment` . Örnek bir komut şöyle olabilir:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager, bu komutun mevcut bir kaynağa yönelik bir güncelleştirme olduğunu algılar. Yalnızca var olan dağıtımı ve sunulan yeni şablonu yönlendiren şablon arasındaki değişiklikleri işler.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Azure Izleyici günlüklerini Azure PowerShell ile dağıtma

Log Analytics kaynağınızı komutunu kullanarak PowerShell aracılığıyla da dağıtabilirsiniz `New-AzOperationalInsightsWorkspace` . Bu yöntemi kullanmak için [Azure PowerShell](/powershell/azure/install-az-ps)yüklediğinizden emin olun. Yeni bir Log Analytics çalışma alanı oluşturmak ve buna Service Fabric çözümü eklemek için bu betiği kullanın: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

İşiniz bittiğinde, Azure Izleyici günlüklerini uygun depolama hesabına bağlamak için yukarıdaki bölümdeki adımları izleyin.

Ayrıca, PowerShell kullanarak diğer çözümleri ekleyebilir veya Log Analytics çalışma alanınızda başka değişiklikler yapabilirsiniz. Daha fazla bilgi için bkz. [PowerShell kullanarak Azure izleyici günlüklerini yönetme](../azure-monitor/logs/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Sonraki adımlar
* Performans sayaçlarını toplamak ve kapsayıcılarınız için Docker istatistiklerini ve günlüklerini toplamak için [Log Analytics aracısını](service-fabric-diagnostics-oms-agent.md) düğümleriniz üzerine dağıtın
* Azure Izleyici günlüklerinin bir parçası olarak sunulan [günlük araması ve sorgulama](../azure-monitor/logs/log-query-overview.md) özellikleriyle familiarized alın
* [Azure Izleyici günlüklerinde özel görünümler oluşturmak için görünüm tasarımcısını kullanma](../azure-monitor/visualize/view-designer.md)
