---
title: Azure Izleyici ölçüm deposunda klasik Cloud Services Konuk işletim sistemi ölçümleri gönderme
description: Azure Izleyici ölçüm deposuna Konuk işletim sistemi ölçümleri gönderin Cloud Services
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 56138277866d3b2bf02733a2c595a5a232faed8c
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844940"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Azure Izleyici ölçüm deposunda klasik Cloud Services Konuk işletim sistemi ölçümleri gönderme 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Izleyici [Tanılama uzantısı](diagnostics-extension-overview.md)ile, bir sanal makinenin, bulut hizmetinin veya Service Fabric kümenin bir parçası olarak çalışan konuk işletim sisteminden (konuk işletim sistemi) ölçümleri ve günlükleri toplayabilirsiniz. Uzantı [birçok farklı konuma](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json) telemetri gönderebilir.

Bu makalede, Azure Izleyici ölçüm deposuna Azure klasik Cloud Services için konuk işletim sistemi performans ölçümlerini gönderme işlemi açıklanır. Tanılama sürüm 1,11 ' den başlayarak, ölçümleri doğrudan Azure Izleyici ölçümleri deposuna yazabilirsiniz; burada standart platform ölçümleri zaten toplanır. 

Bu konumda depolamak, platform ölçümleri için kullanabileceğiniz eylemlere erişmenizi sağlar. Eylemler, neredeyse gerçek zamanlı uyarı, grafik, yönlendirme, REST API erişimi ve daha fazlasını içerir.  Geçmişte, tanılama uzantısı Azure depolama 'ya yazdı, ancak Azure Izleyici veri deposuna değil.  

Bu makalede özetlenen işlem yalnızca Azure Cloud Services performans sayaçları için geçerlidir. Diğer özel ölçümler için çalışmaz. 

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğinizde bir [Hizmet Yöneticisi veya ortak yönetici](~/articles/billing/billing-add-change-azure-subscription-administrator.md) olmanız gerekir. 

- Aboneliğinizin [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)'a kayıtlı olması gerekir. 

- [Azure PowerShell](/powershell/azure) veya [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) yüklemiş olmanız gerekir.

- Bulut hizmetiniz [özel ölçümleri destekleyen bir bölgede](metrics-custom-overview.md#supported-regions)olmalıdır.

## <a name="provision-a-cloud-service-and-storage-account"></a>Bulut hizmeti ve depolama hesabı sağlama 

1. Klasik bir bulut hizmeti oluşturun ve dağıtın. [Azure Cloud Services ve ASP.NET ile çalışmaya başlama adlı](../../cloud-services/cloud-services-dotnet-get-started.md)örnek bir klasik Cloud Services uygulaması ve dağıtımı bulabilirsiniz. 

2. Mevcut bir depolama hesabını kullanabilir veya yeni bir depolama hesabı dağıtabilirsiniz. Depolama hesabının, oluşturduğunuz klasik bulut hizmeti ile aynı bölgede olması en iyisidir. Azure portal **depolama hesapları** kaynağı dikey penceresine gidin ve **anahtarlar**' ı seçin. Depolama hesabı adını ve depolama hesabı anahtarını bir yere göz atın. Sonraki adımlarda bu bilgilere ihtiyacınız olacaktır.

   ![Depolama hesabı anahtarları](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma 

Azure Active Directory kiracınızda, [kaynaklara erişebilen Azure Active Directory bir uygulama ve hizmet sorumlusu oluşturmak için Portal kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)bölümündeki yönergeleri kullanarak bir hizmet ilkesi oluşturun. Bu işlemi yaparken aşağıdakilere göz önünde olabilirsiniz: 

- Oturum açma URL 'si için herhangi bir URL 'YI yerleştirebilirsiniz.  
- Bu uygulama için yeni bir istemci gizli dizisi oluşturun.  
- Daha sonraki adımlarda kullanmak üzere anahtarı ve istemci KIMLIĞINI kaydedin.  

Uygulamanın, ölçümleri sunmak istediğiniz kaynak için önceki adım *Izleme ölçümleri yayımcı* izinlerinde oluşturulmasını sağlayın. Uygulamayı birçok kaynağa karşı özel ölçümleri yayan kullanmayı planlıyorsanız, bu izinleri kaynak grubu veya abonelik düzeyinde verebilirsiniz.  

> [!NOTE]
> Tanılama uzantısı, Azure Izleyicisine göre kimlik doğrulaması yapmak ve bulut hizmetinize yönelik ölçümleri göstermek için hizmet sorumlusunu kullanır.

## <a name="author-diagnostics-extension-configuration"></a>Tanılama uzantısı yapılandırmasını yaz 

Tanılama uzantısı yapılandırma dosyanızı hazırlayın. Bu dosya, tanılama uzantısının bulut hizmetiniz için hangi günlükleri ve performans sayaçlarını toplayacağını belirler. Aşağıda örnek bir tanılama yapılandırma dosyası verilmiştir:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

Tanılama dosyanızın "SinksConfig" bölümünde yeni bir Azure Izleyici havuzu tanımlayın: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Yapılandırma dosyanızın toplanacak performans sayaçlarını listelersiniz bölümünde Azure Izleyici havuzunu ekleyin. Bu giriş, belirttiğiniz tüm performans sayaçlarının Azure Izleyici 'ye ölçüm olarak yönlendirilmesini sağlar. Gereksinimlerinize göre performans sayaçlarını ekleyebilir veya kaldırabilirsiniz. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Son olarak, özel yapılandırma ' da bir *Azure Izleyici hesabı* ekleyin bölümü. Daha önce oluşturduğunuz hizmet sorumlusu istemci KIMLIĞINI ve gizli anahtarını girin. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Bu tanılama dosyasını yerel olarak kaydedin.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Tanılama uzantısını bulut hizmetinize dağıtma 

PowerShell 'i başlatın ve Azure 'da oturum açın. 

```powershell
Login-AzAccount 
```

Daha önce oluşturduğunuz depolama hesabının ayrıntılarını depolamak için aşağıdaki komutları kullanın. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Benzer şekilde, aşağıdaki komutu kullanarak tanılama dosya yolunu bir değişkene ayarlayın:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Tanılama uzantısını aşağıdaki komut kullanılarak yapılandırılmış Azure Izleyici havuzu ile tanılama dosyası ile bulut hizmetinize dağıtın:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Tanılama uzantısının yüklenmesinin parçası olarak bir depolama hesabı sağlanması hala zorunludur. Tanılama yapılandırma dosyasında belirtilen tüm Günlükler veya performans sayaçları, belirtilen depolama hesabına yazılır.  

## <a name="plot-metrics-in-the-azure-portal"></a>Azure portal ölçümleri çizme 

1. Azure portalına gidin. 

   ![Ölçümler Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. Sol taraftaki menüden Izleyici ' yi seçin **.**

3. **İzleyici** dikey penceresinde **ölçüm önizlemesi** sekmesini seçin.

4. Kaynaklar açılan menüsünde, klasik bulut hizmetinizi seçin.

5. Ad alanları açılan menüsünde **Azure. VM. Windows. Guest**' yi seçin. 

6. Ölçümler açılan menüsünde, **Bellek\kaydedilmiş bayt kullanımda**' yı seçin. 

Belirli bir rol veya rol örneği tarafından kullanılan toplam belleği görüntülemek için boyut filtreleme ve bölme yeteneklerini kullanın. 

 ![Ölçümler Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Özel ölçümler](metrics-custom-overview.md)hakkında daha fazla bilgi edinin.

