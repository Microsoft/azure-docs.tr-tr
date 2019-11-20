---
title: Azure Izleyici 'de IIS ve tablo depolaması için blob depolamayı kullanma | Microsoft Docs
description: Azure Izleyici, tablo depolama veya blob depolamaya yazılan IIS günlüklerine tanılama yazan Azure hizmetleri için günlükleri okuyabilir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 8f70ecc96269783c29c566fb89bd617f034316b1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932682"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Azure depolama 'dan Azure tanılama günlükleri toplama

Azure Izleyici, tablo depolama veya blob depolamaya yazılan IIS günlüklerine tanılama yazan aşağıdaki hizmetlere ait günlükleri okuyabilir:

* Service Fabric kümeleri (Önizleme)
* Virtual Machines (Sanal Makineler)
* Web/çalışan rolleri

Azure Izleyici 'nin bu kaynaklar için bir Log Analytics çalışma alanına veri toplayabilmesi için Azure tanılama 'nın etkinleştirilmesi gerekir.

Tanılama etkinleştirildikten sonra, Azure portal veya PowerShell 'i kullanarak günlükleri toplayacağınız çalışma alanını yapılandırabilirsiniz.

Azure Tanılama, Azure 'da çalışan bir çalışan rolünden, web rolünden veya sanal makineden Tanılama verileri toplamanıza olanak sağlayan bir Azure uzantısıdır. Veriler bir Azure depolama hesabında depolanır ve daha sonra Azure Izleyici tarafından toplanabilir.

Azure Izleyici 'nin bu Azure Tanılama günlüklerini toplaması için günlüklerin aşağıdaki konumlarda olması gerekir:

| Günlük türü | Kaynak Türü | Konum |
| --- | --- | --- |
| IIS günlükleri |Virtual Machines (Sanal Makineler) <br> Web rolleri <br> Çalışan rolleri |WAD-IIS-LogFiles (BLOB depolama) |
| Syslog |Virtual Machines (Sanal Makineler) |LinuxsyslogVer2v0 (tablo depolama) |
| Işlem olaylarını Service Fabric |Service Fabric düğümleri |WADServiceFabricSystemEventTable |
| Güvenilir aktör olaylarını Service Fabric |Service Fabric düğümleri |WADServiceFabricReliableActorEventTable |
| Güvenilir hizmet olaylarını Service Fabric |Service Fabric düğümleri |WADServiceFabricReliableServiceEventTable |
| Windows olay günlükleri |Service Fabric düğümleri <br> Virtual Machines (Sanal Makineler) <br> Web rolleri <br> Çalışan rolleri |WADWindowsEventLogsTable (tablo depolama) |
| Windows ETW günlükleri |Service Fabric düğümleri <br> Virtual Machines (Sanal Makineler) <br> Web rolleri <br> Çalışan rolleri |Wadelenebilir Venttable (tablo depolama) |

> [!NOTE]
> Azure Web sitelerindeki IIS günlükleri Şu anda desteklenmiyor.
>
>

Sanal makineler için, ek öngörüleri etkinleştirmek üzere sanal makinenize [Log Analytics aracısını](../../azure-monitor/learn/quick-collect-azurevm.md) yükleme seçeneğiniz vardır. IIS günlüklerini ve olay günlüklerini çözümleyebilmenin yanı sıra, yapılandırma değişiklik izleme, SQL değerlendirmesi ve güncelleştirme değerlendirmesi dahil olmak üzere ek analizler gerçekleştirebilirsiniz.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Olay günlüğü ve IIS günlük koleksiyonu için bir sanal makinede Azure tanılamayı etkinleştirme

Microsoft Azure portal kullanarak olay günlüğü ve IIS günlük toplama için bir sanal makinede Azure tanılamayı etkinleştirmek üzere aşağıdaki yordamı kullanın.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Azure portal bir sanal makinede Azure tanılama 'yı etkinleştirmek için

1. Sanal makine oluştururken VM aracısını yükler. Sanal makine zaten varsa, VM aracısının zaten yüklü olduğunu doğrulayın.

   * Azure portal sanal makineye gidin, **Isteğe bağlı yapılandırma**' yı seçin, sonra **Tanılama** ve **durumu** **Açık**olarak ayarla ' yı seçin.

     Tamamlandıktan sonra VM 'nin Azure Tanılama uzantısı yüklüdür ve çalışır. Bu uzantı, tanılama verilerinizi toplamaktan sorumludur.
2. Var olan bir VM 'de izlemeyi ve olay günlüğünü yapılandırmayı etkinleştirin. Tanılamayı VM düzeyinde etkinleştirebilirsiniz. Tanılamayı etkinleştirmek ve olay günlüğünü yapılandırmak için aşağıdaki adımları uygulayın:

   1. VM’yi seçin.
   2. **İzleme**' ye tıklayın.
   3. **Tanılama**' ya tıklayın.
   4. **Durumu** **Açık**olarak ayarlayın.
   5. Toplamak istediğiniz her tanılama günlüğünü seçin.
   6. **Tamam**’a tıklayın.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>IIS günlüğü ve olay toplama için bir Web rolünde Azure tanılamayı etkinleştirme

Azure tanılamayı etkinleştirme hakkında genel adımlar için [bir bulut hizmetinde tanılamayı etkinleştirme](../../cloud-services/cloud-services-dotnet-diagnostics.md) bölümüne bakın. Aşağıdaki yönergeler bu bilgileri kullanır ve Log Analytics kullanılmak üzere özelleştirir.

Azure tanılama etkin:

* IIS günlükleri, varsayılan olarak, scheduledTransferPeriod aktarım aralığında aktarılan günlük verileriyle birlikte depolanır.
* Windows olay günlükleri varsayılan olarak aktarılmaz.

### <a name="to-enable-diagnostics"></a>Tanılamayı etkinleştirmek için

Windows olay günlüklerini etkinleştirmek veya scheduledTransferPeriod 'ı değiştirmek için, XML yapılandırma dosyasını (Diagnostics. wadcfg) kullanarak Azure Tanılama [4. Adım: tanılama yapılandırma dosyanızı oluşturma ve uzantıyı yükleme](../../cloud-services/cloud-services-dotnet-diagnostics.md) bölümünde gösterildiği gibi yapılandırın

Aşağıdaki örnek yapılandırma dosyası, uygulama ve sistem günlüklerinden IIS günlüklerini ve tüm olayları toplar:

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Aşağıdaki örnekte olduğu gibi ConfigurationSettings 'in bir depolama hesabı belirttiğinden emin olun:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** ve **accountkey** değerleri, depolama hesabı panosundaki Azure Portal erişim anahtarlarını Yönet altında bulunur. Bağlantı dizesinin Protokolü **https**olmalıdır.

Güncelleştirilmiş tanılama Yapılandırması bulut hizmetinize uygulandıktan sonra Azure depolama 'ya tanılama yazıyor ve bu durumda Log Analytics çalışma alanını yapılandırmaya hazırız.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Azure depolama 'dan günlükleri toplamak için Azure portal kullanma

Aşağıdaki Azure hizmetleri için günlükleri toplamak üzere Azure Izleyici 'de bir Log Analytics çalışma alanı yapılandırmak için Azure portal kullanabilirsiniz:

* Service Fabric kümeleri
* Virtual Machines (Sanal Makineler)
* Web/çalışan rolleri

Azure portal, Log Analytics çalışma alanınıza gidin ve aşağıdaki görevleri gerçekleştirin:

1. *Depolama hesapları günlükleri* ' ne tıklayın
2. Görev *Ekle* ' ye tıklayın
3. Tanılama günlüklerini içeren depolama hesabını seçin
   * Bu hesap, klasik bir depolama hesabı ya da bir Azure Resource Manager depolama hesabı olabilir
4. Günlükleri toplamak istediğiniz veri türünü seçin
   * Seçenekler IIS günlüklerdir; Olayları Syslog (Linux); ETW günlükleri; Service Fabric olaylar
5. Kaynak değeri veri türüne göre otomatik olarak doldurulur ve değiştirilemez
6. Yapılandırmayı kaydetmek için Tamam 'a tıklayın

Çalışma alanına toplamak istediğiniz ek depolama hesapları ve veri türleri için 2-6 arasındaki adımları yineleyin.

Yaklaşık 30 dakika içinde, Log Analytics çalışma alanındaki depolama hesabından verileri görebilirsiniz. Yalnızca yapılandırma uygulandıktan sonra depolamaya yazılan veriler görüntülenir. Çalışma alanı, depolama hesabından önceden var olan verileri okuyamıyor.

> [!NOTE]
> Portal, kaynağın depolama hesabında mevcut olduğunu veya yeni verilerin yazılmakta olduğunu doğrulamaz.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>PowerShell kullanarak olay günlüğü ve IIS günlük koleksiyonu için bir sanal makinede Azure tanılamayı etkinleştirme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure tanılama 'yı yapılandırmak için Azure [Izleyicisini yapılandırma](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) bölümündeki adımları kullanarak, tablo depolamaya yazılan Azure tanılama 'dan okumak için PowerShell 'i kullanın.

Azure PowerShell kullanarak, Azure depolama 'ya yazılan olayları daha kesin şekilde belirtebilirsiniz.
Daha fazla bilgi için bkz. [Azure sanal makinelerinde tanılamayı etkinleştirme](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Aşağıdaki PowerShell komut dosyasını kullanarak Azure tanılamayı etkinleştirebilir ve güncelleştirebilirsiniz.
Bu betiği özel bir günlük yapılandırmasıyla de kullanabilirsiniz.
Depolama hesabını, hizmet adını ve sanal makine adını ayarlamak için betiği değiştirin.
Betik, klasik sanal makineler için cmdlet 'leri kullanır.

Aşağıdaki betik örneğini gözden geçirin, kopyalayın, gerektiği gibi değiştirin, örneği bir PowerShell betik dosyası olarak kaydedin ve betiği çalıştırın.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Sonraki adımlar

* Desteklenen Azure hizmetleri için [Azure hizmetleri için günlükleri ve ölçümleri toplayın](collect-azure-metrics-logs.md) .
* Verilerle ilgili Öngörüler sağlamak için [çözümleri etkinleştirin](../../azure-monitor/insights/solutions.md) .
* Verileri çözümlemek için [arama sorguları](../../azure-monitor/log-query/log-query-overview.md) ' nı kullanın.