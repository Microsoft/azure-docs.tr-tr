---
title: Windows sanal makinesi için Azure Izleyici veri deposuna Konuk işletim sistemi ölçümleri gönderme (klasik)
description: Windows sanal makinesi için Azure Izleyici veri deposuna Konuk işletim sistemi ölçümleri gönderme (klasik)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: cc0c7c4928fb03cb60bb51f74d74fdc1ab914348
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844914"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Windows sanal makinesi için Azure Izleyici veri deposuna Konuk işletim sistemi ölçümleri gönderme (klasik)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Izleyici [Tanılama uzantısı](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) ("wad" veya "Diagnostics" olarak bilinir), bir sanal makinenin, bulut hizmetinin veya Service Fabric kümesinin bir parçası olarak çalışan konuk işletim sisteminden (konuk işletim sistemi) ölçümleri ve günlükleri toplamanıza olanak tanır. Uzantı [birçok farklı konuma](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json) telemetri gönderebilir.

Bu makalede, Azure Izleyici ölçüm deposuna bir Windows sanal makinesi (klasik) için konuk işletim sistemi performans ölçümlerini gönderme işlemi açıklanır. Tanılama sürüm 1,11 ' den başlayarak, ölçümleri doğrudan Azure Izleyici ölçümleri deposuna yazabilirsiniz; burada standart platform ölçümleri zaten toplanır. 

Bu konumda depolamak, platform ölçümleri için yaptığınız aynı eylemlere erişmenizi sağlar. Eylemler, neredeyse gerçek zamanlı uyarı, grafik, yönlendirme, REST API erişimi ve daha fazlasını içerir. Geçmişte, tanılama uzantısı Azure depolama 'ya yazdı, ancak Azure Izleyici veri deposuna değil. 

Bu makalede özetlenen işlem yalnızca Windows işletim sistemini çalıştıran klasik sanal makinelerde çalışır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğinizde bir [Hizmet Yöneticisi veya ortak yönetici](../../billing/billing-add-change-azure-subscription-administrator.md) olmanız gerekir. 

- Aboneliğinizin [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)'a kayıtlı olması gerekir. 

- [Azure PowerShell](/powershell/azure) veya [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) yüklemiş olmanız gerekir.

- VM kaynağınızın [özel ölçümleri destekleyen bir bölgede](metrics-custom-overview.md#supported-regions)olması gerekir.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Klasik sanal makine ve depolama hesabı oluşturma

1. Azure portal kullanarak klasik bir VM oluşturun.
   ![Klasik VM oluşturma](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Bu sanal makineyi oluştururken, yeni bir klasik depolama hesabı oluşturma seçeneğini belirleyin. Bu depolama hesabını sonraki adımlarda kullanırız.

1. Azure portal **depolama hesapları** kaynağı dikey penceresine gidin. **Anahtarlar**' ı seçin ve depolama hesabı adı ile depolama hesabı anahtarını aklınızda yapın. Sonraki adımlarda bu bilgilere ihtiyacınız vardır.
   ![Depolama erişim tuşları](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

[Hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md)bölümündeki yönergeleri kullanarak Azure Active Directory kiracınızda bir hizmet ilkesi oluşturun. Bu işlemden sonra aşağıdaki adımları göz önünde edin: 
- Bu uygulama için yeni bir istemci gizli dizisi oluşturun.
- Daha sonraki adımlarda kullanmak üzere anahtarı ve istemci KIMLIĞINI kaydedin.

Bu uygulamaya, ölçümleri sunmak istediğiniz kaynak için "Izleme ölçümleri yayımcısı" izinleri verin. Bir kaynak grubunu veya tüm aboneliği kullanabilirsiniz.  

> [!NOTE]
> Tanılama uzantısı, Azure Izleyicisine göre kimlik doğrulaması yapmak ve klasik sanal makinenizin ölçümlerini göstermek için hizmet sorumlusunu kullanır.

## <a name="author-diagnostics-extension-configuration"></a>Tanılama uzantısı yapılandırmasını yaz

1. Tanılama uzantısı yapılandırma dosyanızı hazırlayın. Bu dosya, tanılama uzantısının klasik VM 'niz için hangi günlükleri ve performans sayaçlarını toplayacağını belirler. Aşağıda bir örnek verilmiştir:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. Tanılama dosyanızın "SinksConfig" bölümünde, yeni bir Azure Izleyici havuzunu aşağıdaki gibi tanımlayın:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Yapılandırma dosyanızın toplanacak performans sayaçları listesinin listelendiği bölümde, performans sayaçlarını "AzMonSink" Azure Izleyici havuzuna yönlendirin.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Özel yapılandırmada Azure Izleyici hesabını tanımlayın. Daha sonra ölçümleri yayma için kullanılacak hizmet sorumlusu bilgilerini ekleyin.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Bu dosyayı yerel olarak kaydedin.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Tanılama uzantısını bulut hizmetinize dağıtma

1. PowerShell 'i başlatın ve oturum açın.

    ```powershell
    Login-AzAccount
    ```

1. Klasik sanal makinenizin bağlamını ayarlayarak başlayın.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. VM ile oluşturulan klasik depolama hesabının bağlamını ayarlayın.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Aşağıdaki komutu kullanarak tanılama dosya yolunu bir değişkene ayarlayın:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Azure Izleyici havuzunun yapılandırıldığı tanılama dosyası ile klasik VM 'niz için güncelleştirmeyi hazırlayın.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Aşağıdaki komutu çalıştırarak güncelleştirmeyi sanal makinenize dağıtın:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Tanılama uzantısının yüklenmesinin parçası olarak bir depolama hesabı sağlanması yine de zorunludur. Tanılama yapılandırma dosyasında belirtilen tüm Günlükler veya performans sayaçları, belirtilen depolama hesabına yazılır.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Azure portal ölçümleri çizme

1.  Azure portalına gidin. 

1.  Sol taraftaki menüden Izleyici ' yi seçin **.**

1.  **İzleyici** dikey penceresinde **ölçümler**' i seçin.

    ![Ölçümlere git](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Kaynaklar açılan menüsünde, klasik sanal makineyi seçin.

1. Ad alanları açılan menüsünde **Azure. VM. Windows. Guest**' yi seçin.

1. Ölçümler açılan menüsünde, **Bellek\kaydedilmiş bayt kullanımda**' yı seçin.
   ![Ölçümleri çiz](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Sonraki adımlar
- [Özel ölçümler](metrics-custom-overview.md)hakkında daha fazla bilgi edinin.

