---
title: Cloud Services (klasik) ile Azure tanılama (.NET) kullanma | Microsoft Docs
description: Azure Tanılama kullanarak hata ayıklama, performans, izleme, trafik analizi ve daha fazlasını yapmak için Azure bulut hizmetlerinden veri toplayın.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 97e68d338580132b6927c4cc8b206db60fe93ba2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703516"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services-classic"></a>Azure Cloud Services Azure Tanılama etkinleştirme (klasik)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Azure Tanılama bir arka plana [Azure tanılama genel bakış](../azure-monitor/agents/diagnostics-extension-overview.md) konusuna bakın.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Çalışan rolünde tanılamayı etkinleştirme
Bu izlenecek yol, .NET EventSource sınıfını kullanarak telemetri verileri veren bir Azure Worker rolünün nasıl uygulanacağını açıklar. Azure Tanılama telemetri verilerini toplamak ve bir Azure depolama hesabında depolamak için kullanılır. Bir çalışan rolü oluştururken, Visual Studio, .NET 2,4 ve önceki sürümleri için Azure SDK 'larında çözümün parçası olarak tanılama 1,0 ' i otomatik olarak sunar. Aşağıdaki yönergeler, çalışan rolünü oluşturma, çözümünden tanılama 1,0 'yi devre dışı bırakma ve çalışan rolünüzde tanılama 1,2 ya da 1,3 dağıtma sürecini anlatmaktadır.

### <a name="prerequisites"></a>Önkoşullar
Bu makalede bir Azure aboneliğiniz olduğunu ve Azure SDK ile Visual Studio 'Yu kullandığınızı varsaymaktadır. Azure aboneliğiniz yoksa [ücretsiz deneme sürümüne][Free Trial]kaydolabilirsiniz. [Azure PowerShell sürüm 0.8.7 veya üstünü][Install and configure Azure PowerShell version 0.8.7 or later]yüklediğinizden emin olun.

### <a name="step-1-create-a-worker-role"></a>1. Adım: çalışan rolü oluşturma
1. **Visual Studio 'yu** başlatın.
2. **Bulut** şablonundan .NET Framework 4,5 ' i hedefleyen bir **Azure bulut hizmeti** projesi oluşturun.  Projeyi "WADEX," olarak adlandırın ve Tamam ' a tıklayın.
3. **Çalışan rolü** ' nü seçin ve Tamam ' a tıklayın. Proje oluşturulur.
4. **Çözüm Gezgini**, **WorkerRole1** Properties dosyasına çift tıklayın.
5. **Yapılandırma** sekmesinde, tanılamayı devre dışı bırak 1,0 (Azure SDK 2,4 ve önceki sürümler) **seçeneğini işaretleyin.**
6. Herhangi bir hata olmadığını doğrulamak için çözümünüzü derleyin.

### <a name="step-2-instrument-your-code"></a>2. Adım: kodunuzu Işaretleme
WorkerRole. cs içeriğini aşağıdaki kodla değiştirin. [EventSource sınıfından][EventSource Class]devralınan SampleEventSourceWriter sınıfı dört günlük yöntemi uygular: **sendenums**, **messagemethod**, **setother** ve **highfreq**. **WriteEvent** yönteminin ilk parametresi ılgılı olayın kimliğini tanımlar. Run yöntemi, her 10 saniyede bir **Sampleeventsourcewriter** sınıfında uygulanan günlüğe kaydetme yöntemlerinin her birini çağıran sonsuz bir döngü uygular.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>3. Adım: çalışan rolünüzü dağıtma

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Çalışan rolünüzü Visual Studio içinden Azure 'a dağıtarak Çözüm Gezgini **Wadexbol** projesini seçerek **derleme** menüsünden **yayımlayın** .
2. Aboneliğinizi seçin.
3. **Microsoft Azure yayımlama ayarları** Iletişim kutusunda **Yeni oluştur...** seçeneğini belirleyin.
4. **Bulut hizmeti ve depolama hesabı oluştur** iletişim kutusunda bir **ad** girin (örneğin, "wadexexample") ve bir bölge veya benzeşim grubu seçin.
5. **Ortamı** **hazırlık** olarak ayarlayın.
6. Diğer **ayarları** uygun şekilde değiştirip **Yayımla**' ya tıklayın.
7. Dağıtım tamamlandıktan sonra, Azure portal bulut hizmetinizin **çalışır** durumda olduğunu doğrulayın.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>4. Adım: tanılama yapılandırma dosyanızı oluşturma ve uzantıyı yüklemesi
1. Aşağıdaki PowerShell komutunu yürüterek ortak yapılandırma dosyası şema tanımını indirin:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. **WorkerRole1** projesine sağ tıklayıp    ->  **Yeni öğe Ekle... öğesini** seçerek WorkerRole1 projenize bir XML dosyası ekleyin. -> **Visual C# öğeleri**  ->  **Veri**  ->  **XML dosyası**. "WadExample.xml" dosyasını adlandırın.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. WadConfig. xsd dosyasını yapılandırma dosyası ile ilişkilendirin. WadExample.xml Düzenleyicisi penceresinin etkin pencere olduğundan emin olun. **Özellikler** penceresini açmak için **F4** tuşuna basın. **Özellikler** penceresinde **şemalar** özelliğine tıklayın. **..** . Öğesine tıklayın. **şema** özelliğinde. **Ekle...** öğesine tıklayın. düğmesine tıklayın ve XSD dosyasını kaydettiğiniz konuma gidin ve WadConfig. xsd dosyasını seçin. **Tamam**'a tıklayın.

4. WadExample.xml yapılandırma dosyasının içeriğini aşağıdaki XML ile değiştirin ve dosyayı kaydedin. Bu yapılandırma dosyası, bir tane CPU kullanımı ve diğeri bellek kullanımı için toplanacak birkaç performans sayacını tanımlar. Sonra yapılandırma, SampleEventSourceWriter sınıfındaki yöntemlere karşılık gelen dört olayı tanımlar.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>5. Adım: çalışan rolünüze tanılama 'Yı yüklemeyin
Bir Web veya çalışan rolünde tanılamayı yönetmeye yönelik PowerShell cmdlet 'leri şunlardır: set-Azurezervicediagnokısexten, Get-azurezervicediagnoçıkartsextenma ve Remove-azurezervicediagnokısextenma.

1. Azure PowerShell açın.
2. Çalışan rolünüze tanılama yüklemek için betiği yürütün ( *Storageaccountkey* değerini WADEX, depolama hesabınızın depolama hesabı anahtarıyla değiştirin ve *WadExample.xml* dosyasının yoluyla *config_path* ):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>6. Adım: telemetri verilerinize bakın
Visual Studio **Sunucu Gezgini**, WADEX, depolama hesabına gidin. Bulut hizmeti yaklaşık beş (5) dakika çalıştıktan sonra, **Wadenumstable**, **wadhighfreqtable**, **wadmessagetable**, **WADPerformanceCountersTable** ve **wadsetothertable** tablolarını görmeniz gerekir. Toplanan Telemetriyi görüntülemek için tablolardan birine çift tıklayın.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Yapılandırma Dosyası Şeması
Tanılama yapılandırma dosyası, tanılama Aracısı başladığında tanılama yapılandırma ayarlarını başlatmak için kullanılan değerleri tanımlar. Geçerli değerler ve örnekler için [en son şema başvurusuna](../azure-monitor/agents/diagnostics-extension-versions.md) bakın.

## <a name="troubleshooting"></a>Sorun giderme
Sorun yaşıyorsanız, genel sorunlar hakkında yardım için bkz. [sorun giderme Azure tanılama](../azure-monitor/agents/diagnostics-extension-troubleshooting.md) .

## <a name="next-steps"></a>Sonraki Adımlar
Topladığınız verileri değiştirmek, sorunları gidermek veya genel olarak tanılama hakkında daha fazla bilgi edinmek için [Ilgili Azure sanal makine tanılama makalelerinin listesini inceleyin](../azure-monitor/agents/diagnostics-extension-overview.md) .

[EventSource Class]: /dotnet/api/system.diagnostics.tracing.eventsource

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: /previous-versions/azure/gg433048(v=azure.100)
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: /powershell/azure/