---
title: Azure Tanılama uzantısı 1,3 ve üzeri yapılandırma şeması
description: Şema sürümü 1,3 ve üzeri Azure tanılama, Microsoft Azure SDK 2,4 ve üzeri bir parçası olarak gönderilir.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2018
ms.openlocfilehash: 3d79fe6a415b7d1f862797bf41caed89bfe50a41
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834741"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure Tanılama 1,3 ve üzeri yapılandırma şeması
> [!NOTE]
> Azure Tanılama uzantısı, ' deki performans sayaçlarını ve diğer istatistikleri toplamak için kullanılan bileşendir:
> - Azure Sanal Makineler
> - Sanal Makine Ölçek Kümeleri
> - Service Fabric
> - Cloud Services
> - Ağ Güvenlik Grupları
>
> Bu sayfa yalnızca bu hizmetlerden birini kullanıyorsanız ilgilidir.

Bu sayfa 1,3 ve üzeri sürümler için geçerlidir (Azure SDK 2,4 ve daha yeni bir sürümü). Daha yeni yapılandırma bölümlerinin eklendiği sürümde gösterilmesi için yorum yapılır. Şemanın sürüm 1,0 ve 1,2 ' i arşivlendi ve artık kullanılamıyor. 

Burada açıklanan yapılandırma dosyası, tanılama İzleyicisi başladığında tanılama yapılandırma ayarlarını yapmak için kullanılır.  

Uzantı, Application Insights ve Log Analytics dahil olmak üzere Azure Izleyici gibi diğer Microsoft tanılama ürünleriyle birlikte kullanılır.

Aşağıdaki PowerShell komutunu yürüterek ortak yapılandırma dosyası şema tanımını indirin:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Azure Tanılama kullanma hakkında daha fazla bilgi için, bkz. [Azure tanılama uzantısı](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Tanılama yapılandırma dosyası örneği  
 Aşağıdaki örnek tipik bir tanılama yapılandırma dosyası göstermektedir:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> Ortak Yapılandırma Azure Izleyici havuzu tanımının iki özelliği, RESOURCEID ve bölgesi vardır. Bunlar yalnızca klasik VM 'Ler ve klasik bulut hizmetleri için gereklidir. Bu özellikler Kaynak Yöneticisi sanal makineler veya sanal makine ölçek kümeleri için kullanılmamalıdır.
> Ayrıca, Azure Izleyici havuzu için bir asıl kimliği ve gizli dizi olarak geçen ek bir özel yapılandırma öğesi de vardır. Bu yalnızca klasik VM 'Ler ve klasik Cloud Services için gereklidir. Kaynak Yöneticisi VM 'Ler ve VMSS için özel yapılandırma öğesindeki Azure Izleyici tanımı hariç tutulamaz.
>

Önceki XML yapılandırma dosyasının JSON eşdeğeri.

PublicConfig ve PrivateConfig, çoğu JSON kullanım durumunda farklı değişkenler olarak geçirildikleri için ayrılmıştır. Bu durumlarda Kaynak Yöneticisi şablonları, sanal makine ölçek kümesi PowerShell ve Visual Studio sayılabilir.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> Ortak Yapılandırma Azure Izleyici havuzu tanımının iki özelliği, RESOURCEID ve bölgesi vardır. Bunlar yalnızca klasik VM 'Ler ve klasik bulut hizmetleri için gereklidir.
> Bu özellikler Kaynak Yöneticisi sanal makineler veya sanal makine ölçek kümeleri için kullanılmamalıdır.
>

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

> [!NOTE]
> Azure Izleyici havuzu için, bir sorumlu kimliği ve gizli dizi olarak geçen ek bir özel yapılandırma öğesi vardır. Bu yalnızca klasik VM 'Ler ve klasik Cloud Services için gereklidir. Kaynak Yöneticisi VM 'Ler ve VMSS için özel yapılandırma öğesindeki Azure Izleyici tanımı hariç tutulamaz.
>


## <a name="reading-this-page"></a>Bu sayfa okunuyor  
 Aşağıdaki Etiketler, önceki örnekte gösterildiği üzere kabaca olarak gösterilmiştir.  İstediğiniz yerde tam bir açıklama görmüyorsanız, sayfada öğe veya öznitelik için arama yapın.  

## <a name="common-attribute-types"></a>Ortak öznitelik türleri  
 **Scheduledtransferperiod** özniteliği birkaç öğe halinde görünür. Bu, depolamaya en yakın dakikaya yuvarlayarak zamanlanan aktarımlar arasındaki aralıktır. Değer bir [XML "Duration veri türüdür."](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration öğesi  
 *Ağaç: kök-DiagnosticsConfiguration*

Sürüm 1,3 ' ye eklenmiştir.  

Tanılama yapılandırma dosyasının en üst düzey öğesi.  

**Öznitelik** xmlns-tanılama yapılandırma dosyası için XML ad alanı:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**PublicConfig**|Gereklidir. Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**PrivateConfig**|İsteğe bağlı. Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**IsEnabled**|Boolean. Bu sayfanın başka bir yerindeki açıklamaya bakın.|  

## <a name="publicconfig-element"></a>PublicConfig öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig*

 Genel Tanılama yapılandırmasını açıklar.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**WadCfg**|Gereklidir. Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**StorageAccount**|Verilerin depolandığı Azure depolama hesabının adı. , Set-Azurezervicediagnokıkıtik cmdlet 'ini yürütürken de parametre olarak belirtilebilir.|  
|**StorageType**|*Tablo*, *BLOB*veya *tableandblob*olabilir. Tablo varsayılandır. TableAndBlob seçildiğinde, her tür için bir kez, tanılama verileri iki kez yazılır.|  
|**LocalResourceDirectory**|Izleme aracısının olay verilerini depoladığı sanal makinedeki dizin. Aksi takdirde, varsayılan dizin kullanılır:<br /><br /> Bir çalışan/Web rolü için: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Bir sanal makine için: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Gerekli öznitelikler şunlardır:<br /><br /> - **yol** -Azure tanılama tarafından kullanılacak sistem üzerindeki dizin.<br /><br /> - **Expandenvironment** -ortam değişkenlerinin yol adında genişletilip genişletilmediğini denetler.|  

## <a name="wadcfg-element"></a>WadCFG öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Toplanacak telemetri verilerini tanımlar ve yapılandırır.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration öğesi
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Gerekli

|Öznitelikler|Açıklama|  
|----------------|-----------------|  
| **Overallquocontainer MB** | Azure Tanılama tarafından toplanan çeşitli tanılama verileri türleri tarafından tüketilen en fazla yerel disk alanı miktarı. Varsayılan ayar 4096 MB 'dir.<br />
|**useProxyServer** | Proxy sunucusu ayarlarını IE ayarlarında ayarlandığı şekilde kullanmak için Azure Tanılama yapılandırın.|
|**yapma** | 1,5 'ye eklendi. İsteğe bağlı. Havuzları destekleyen tüm alt öğeler için tanılama verilerini de göndermek üzere bir havuz konumunu işaret eder. Havuz örneği Application Insights veya Event Hubs.|  


<br /> <br />

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**Formattaki**|Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**DiagnosticInfrastructureLogs**|Azure Tanılama tarafından oluşturulan günlüklerin toplanmasını etkinleştirin. Tanılama altyapısı günlükleri, tanılama sisteminin kendisi için sorun gidermeye yarar. İsteğe bağlı öznitelikler şunlardır:<br /><br /> **Scheduledtransferloglevelfilter** - -toplanan günlüklerin en düşük önem derecesi düzeyini yapılandırır.<br /><br /> **Scheduledtransferperiod** - -depolamaya en yakın dakikaya yuvarlayarak zamanlanan aktarımlar arasındaki Aralık. Değer bir [XML "Duration veri türüdür."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Dizinler**|Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**EtwProviders 'lar**|Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**Ölçümler**|Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**PerformanceCounters**|Bu sayfanın başka bir yerindeki açıklamaya bakın.|  
|**WindowsEventLog**|Bu sayfanın başka bir yerindeki açıklamaya bakın.|
|**DockerSources**|Bu sayfanın başka bir yerindeki açıklamaya bakın. |



## <a name="crashdumps-element"></a>Crashdökümler öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Crashdökümler*

 Kilitlenme dökümleri koleksiyonunu etkinleştirin.  

|Öznitelikler|Açıklama|  
|----------------|-----------------|  
|**Adı**|İsteğe bağlı. Kilitlenme dökümlerini depolamak için kullanılacak Azure Depolama hesabınızdaki blob kapsayıcısının adı.|  
|**crashDumpType**|İsteğe bağlı.  Mini veya tam kilitlenme dökümleri toplamak için Azure Tanılama yapılandırır.|  
|**directoryQuotaPercentage**|İsteğe bağlı.  VM 'deki kilitlenme dökümlerinin ayrılması için **Overallquocontainer MB** yüzdesini yapılandırır.|  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Gereklidir. Her işlem için yapılandırma değerlerini tanımlar.<br /><br /> Aşağıdaki öznitelik de gereklidir:<br /><br /> **ProcessName** -Azure tanılama kilitlenme dökümü toplamasını istediğiniz işlemin adı.|  

## <a name="directories-element"></a>Dizinler öğesi
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-dizinler*

 Bir dizin içeriğinin toplanmasını, IIS, istek günlüklerine ve/veya IIS günlüklerine erişim başarısız oldu.  

 İsteğe bağlı **Scheduledtransferperiod** özniteliği. Daha önce Açıklama bölümüne bakın.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**Iıslogs**|Bu öğenin yapılandırmasına dahil edilmesi, IIS günlüklerinin toplanmasını sunar:<br /><br /> **ContainerName** -IIS günlüklerini depolamak Için kullanılacak Azure Depolama hesabınızdaki blob kapsayıcısının adı.|   
|**FailedRequestLogs**|Bu öğenin yapılandırmasına dahil edilmesi, bir IIS sitesine veya uygulamasına başarısız istekler hakkında günlüklerin toplanmasını sağlar. Ayrıca sistem altında izleme seçeneklerini etkinleştirmeniz gerekir **.**  **Web. config 'de Web**sunucusu.|  
|**Kaynağı**|İzlenecek dizinlerin listesi.|




## <a name="datasources-element"></a>DataSources öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-dizinler-DataSources*

 İzlenecek dizinlerin listesi.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Gereklidir. Gerekli öznitelik:<br /><br /> **ContainerName** -Azure Depolama hesabınızdaki günlük dosyalarını depolamak için kullanılacak blob kapsayıcısının adı.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-dizinler-DataSources-DirectoryConfiguration*

 **Mutlak** ya da **localresource** öğesi içerebilir, ancak ikisini birden içeremez.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**Salt**|İzlenecek dizinin mutlak yolu. Aşağıdaki öznitelikler gereklidir:<br /><br /> - **Path** -izlenecek dizinin mutlak yolu.<br /><br /> - **Expandenvironment** -yoldaki ortam değişkenlerinin genişletilip genişletilmediğini yapılandırır.|  
|**LocalResource**|İzlenecek yerel bir kaynağa göreli yol. Gerekli öznitelikler şunlardır:<br /><br /> - **adı** -izlenecek dizini içeren yerel kaynak<br /><br /> - **relativePath** -izlenecek dizini içeren ada göre yol|  



## <a name="etwproviders-element"></a>EtwProviders öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 EventSource ve/veya ETW bildirim tabanlı sağlayıcılardan ETW olaylarının toplanmasını yapılandırır.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|[EventSource sınıfından](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)oluşturulan olayların koleksiyonunu yapılandırır. Gerekli öznitelik:<br /><br /> **provider** -EventSource olayının sınıf adı.<br /><br /> İsteğe bağlı öznitelikler şunlardır:<br /><br /> **Scheduledtransferloglevelfilter** - -depolama hesabınıza aktarılacak en düşük önem düzeyi.<br /><br /> **Scheduledtransferperiod** - -depolamaya en yakın dakikaya yuvarlayarak zamanlanan aktarımlar arasındaki Aralık. Değer bir [XML "Duration veri türüdür."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Gerekli öznitelik:<br /><br /> **sağlayıcı** -olay sağlayıcısının GUID 'si<br /><br /> İsteğe bağlı öznitelikler şunlardır:<br /><br /> **Scheduledtransferloglevelfilter** - -depolama hesabınıza aktarılacak en düşük önem düzeyi.<br /><br /> **Scheduledtransferperiod** - -depolamaya en yakın dakikaya yuvarlayarak zamanlanan aktarımlar arasındaki Aralık. Değer bir [XML "Duration veri türüdür."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 [EventSource sınıfından](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)oluşturulan olayların koleksiyonunu yapılandırır.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**DefaultEvents**|İsteğe bağlı öznitelik:<br/><br/> **Eventdestination** -olayların depolandığı tablonun adı|  
|**Olay**|Gerekli öznitelik:<br /><br /> **ID** -olayın kimliği.<br /><br /> İsteğe bağlı öznitelik:<br /><br /> **Eventdestination** -olayların depolandığı tablonun adı|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**DefaultEvents**|İsteğe bağlı öznitelik:<br /><br /> **Eventdestination** -olayların depolandığı tablonun adı|  
|**Olay**|Gerekli öznitelik:<br /><br /> **ID** -olayın kimliği.<br /><br /> İsteğe bağlı öznitelik:<br /><br /> **Eventdestination** -olayların depolandığı tablonun adı|  



## <a name="metrics-element"></a>Ölçüm öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-ölçümler*

 Hızlı sorgular için iyileştirilmiş bir performans sayacı tablosu oluşturmanıza olanak sağlar. **PerformanceCounters** öğesinde tanımlanan her performans sayacı, performans sayacı tablosuna ek olarak ölçümler tablosunda depolanır.  

 **RESOURCEID** özniteliği gereklidir.  Azure Tanılama dağıttığınız sanal makinenin veya sanal makine ölçek kümesinin kaynak KIMLIĞI. [Azure Portal](https://portal.azure.com) **RESOURCEID** 'yi alın. **< ad\>**  ->  -> **kaynak gruplarını** **Araştır** ' ı seçin. **Özellikler** kutucuğuna tıklayın ve değeri **kimlik** alanından kopyalayın.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**Metrictoplamasını**|Gerekli öznitelik:<br /><br /> **Scheduledtransferperiod** -depolamaya en yakın dakikaya yuvarlayarak zamanlanan aktarımlar arasındaki Aralık. Değer bir [XML "Duration veri türüdür."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-PerformanceCounters*

 Performans sayaçlarının toplanmasını mümkün.  

 İsteğe bağlı öznitelik:  

 İsteğe bağlı **Scheduledtransferperiod** özniteliği. Daha önce Açıklama bölümüne bakın.

|Alt öğe|Açıklama|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Aşağıdaki öznitelikler gereklidir:<br /><br /> - **Counterbelirleyicisi** -performans sayacının adı. Örneğin, `\Processor(_Total)\% Processor Time`. Ana bilgisayarınızdaki performans sayaçlarının listesini almak için `typeperf`komutunu çalıştırın.<br /><br /> - **örnekler,** sayacın ne sıklıkta örnekleneceği.<br /><br /> İsteğe bağlı öznitelik:<br /><br /> **Unit** -sayacın ölçü birimi.|
|**yapma** | 1,5 'ye eklendi. İsteğe bağlı. Bir havuz konumunu işaret eder ve tanılama verilerini de gönderir. Örneğin, Azure Izleyici veya Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog öğesi
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Windows olay günlükleri koleksiyonunu etkinleştirilir.  

 İsteğe bağlı **Scheduledtransferperiod** özniteliği. Daha önce Açıklama bölümüne bakın.  

|Alt öğe|Açıklama|  
|-------------------|-----------------|  
|**DataSource**|Toplanacak Windows olay günlükleri. Gerekli öznitelik:<br /><br /> **ad** -toplanacak Windows olaylarını açıklayan XPath sorgusu. Örneğin:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Tüm olayları toplamak için "*" belirtin|  




## <a name="logs-element"></a>Logs öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-logs*

 Sürüm 1,0 ve 1,1 ' de mevcuttur. 1,2 içinde yok. 1,3 'ye geri eklendi.  

 Temel Azure günlükleri için arabellek yapılandırmasını tanımlar.  

|Öznitelik|Tür|Açıklama|  
|---------------|----------|-----------------|  
|**Bufferquocontainer MB**|**unsignedInt**|İsteğe bağlı. Belirtilen veriler için kullanılabilen en fazla dosya sistemi depolama miktarını belirtir.<br /><br /> Varsayılan değer 0 ' dır.|  
|**scheduledTransferLogLevelFilter**|**string**|İsteğe bağlı. Aktarılan günlük girişlerinin en düşük önem derecesini belirtir. Varsayılan değer **tanımsızdır**ve tüm günlükleri aktarır. Diğer olası değerler (en fazla bilgi sırasıyla) **verbose**, **bilgi**, **Uyarı**, **hata**ve **kritik öneme**sahiptir.|  
|**scheduledTransferPeriod**|**sürenin**|İsteğe bağlı. Zamanlanan veri aktarımları arasındaki aralığı, en yakın dakikaya yuvarlayarak belirtir.<br /><br /> Varsayılan değer PT0S ' dir.|  
|**yapma** |**string**| 1,5 'ye eklendi. İsteğe bağlı. Bir havuz konumunu işaret eder ve tanılama verilerini de gönderir. Örneğin, Application Insights veya Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 1,9 'ye eklendi.

|Öğe adı|Açıklama|  
|------------------|-----------------|  
|**STA**|Sisteme Docker kapsayıcıları için istatistikler toplamasını söyler|  

## <a name="sinksconfig-element"></a>SinksConfig öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 Tanılama verilerinin gönderileceği konumların listesi ve bu konumların ilişkili yapılandırması.  

|Öğe adı|Açıklama|  
|------------------|-----------------|  
|**Ev**|Bu sayfanın başka bir yerindeki açıklamaya bakın.|  

## <a name="sink-element"></a>Havuz öğesi
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink*

 Sürüm 1,5 ' ye eklenmiştir.  

 Tanılama verilerinin gönderileceği konumları tanımlar. Örneğin, Application Insights hizmeti.  

|Öznitelik|Tür|Açıklama|  
|---------------|----------|-----------------|  
|**ada**|string|Sinkname tanımlayan bir dize.|  

|Öğe|Tür|Açıklama|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Yalnızca Application Insights veri gönderilirken kullanılır. Erişiminiz olan bir etkin Application Insights hesabının Izleme anahtarını içerir.|  
|**Lardan**|string|Size akış yapan her bir ek filtre|  

## <a name="channels-element"></a>Channels öğesi  
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-Channels*

 Sürüm 1,5 ' ye eklenmiştir.  

 Bir havuz üzerinden geçen günlük verilerinin akışları için filtreleri tanımlar.  

|Öğe|Tür|Açıklama|  
|-------------|----------|-----------------|  
|**Kanalla**|string|Bu sayfanın başka bir yerindeki açıklamaya bakın.|  

## <a name="channel-element"></a>Channel öğesi
 *Ağaç: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-Channels-Channel*

 Sürüm 1,5 ' ye eklenmiştir.  

 Tanılama verilerinin gönderileceği konumları tanımlar. Örneğin, Application Insights hizmeti.  

|Öznitelikler|Tür|Açıklama|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Aktarılan günlük girişlerinin en düşük önem derecesini belirtir. Varsayılan değer **tanımsızdır**ve tüm günlükleri aktarır. Diğer olası değerler (en fazla bilgi sırasıyla) **verbose**, **bilgi**, **Uyarı**, **hata**ve **kritik öneme**sahiptir.|  
|**ada**|**string**|İfade edilecek kanalın benzersiz adı|  


## <a name="privateconfig-element"></a>PrivateConfig öğesi
 *Ağaç: root-DiagnosticsConfiguration-PrivateConfig*

 Sürüm 1,3 ' ye eklenmiştir.  

 İsteğe bağlı  

 Depolama hesabının özel ayrıntılarını (ad, anahtar ve uç nokta) depolar. Bu bilgiler sanal makineye gönderilir, ancak bundan alınamaz.  

|Alt öğeler|Açıklama|  
|--------------------|-----------------|  
|**StorageAccount**|Kullanılacak depolama hesabı. Aşağıdaki öznitelikler gereklidir<br /><br /> - **adı** -depolama hesabının adı.<br /><br /> - **anahtar** -depolama hesabına yönelik anahtar.<br /><br /> - **uç noktası** -depolama hesabına erişmek için uç nokta. <br /><br /> -**Sastoken** (eklenen 1.8.1)-özel yapılandırmada bir depolama hesabı anahtarı yerıne bir SAS belirteci belirtebilirsiniz. Sağlanmışsa, depolama hesabı anahtarı yok sayılır. <br />SAS belirteci için gereksinimler: <br />-Yalnızca hesap SAS belirtecini destekler <br />- *b*, *t* hizmeti türleri gereklidir. <br /> - *a*, *c*, *u*, *w* izinleri gereklidir. <br /> - *c*, *o* kaynak türleri gereklidir. <br /> -Yalnızca HTTPS protokolünü destekler <br /> -Başlangıç ve bitiş tarihi geçerli olmalıdır.|  


## <a name="isenabled-element"></a>IsEnabled öğesi  
 *Ağaç: root-DiagnosticsConfiguration-IsEnabled*

 Boolean. Tanılamayı devre dışı bırakmak için tanılamayı veya `false` etkinleştirmek üzere `true` kullanın.

