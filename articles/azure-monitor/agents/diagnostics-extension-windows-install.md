---
title: Windows Azure tanılama uzantısı 'nı (WAD) yükleyip yapılandırma
description: Windows Tanılama uzantısı 'nı yükleme ve yapılandırma hakkında bilgi edinin. Ayrıca, verilerin nasıl depolandığını ve Azure Storage hesabında nasıl bir açıklama alabileceğinizi öğrenin.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3ff752b673c49047551c48c4c8693b00d7b5edeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787414"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Windows Azure tanılama uzantısı 'nı (WAD) yükleyip yapılandırma
Azure [Tanılama uzantısı](diagnostics-extension-overview.md) , Azure izleyici 'de Konuk işletim sisteminden ve Azure sanal makinelerinin ve diğer işlem kaynaklarının iş yüklerinden izleme verilerini toplayan bir aracıdır. Bu makalede, Windows Tanılama uzantısı 'nı yükleme ve yapılandırma hakkında ayrıntılar ve verilerin ve Azure depolama hesabında nasıl depolandığı hakkında bir açıklama sağlanmaktadır.

Tanılama uzantısı, Azure 'da bir [sanal makine uzantısı](../../virtual-machines/extensions/overview.md) olarak uygulanır, bu nedenle Kaynak Yöneticisi şablonları, POWERSHELL ve CLI kullanarak aynı yükleme seçeneklerini destekler. Sanal makine uzantılarının yüklenmesi ve bakımında ilgili ayrıntılar için bkz. [Windows Için sanal makine uzantıları ve özellikleri](../../virtual-machines/extensions/features-windows.md) .

## <a name="overview"></a>Genel Bakış
Windows Azure tanılama uzantısı 'nı yapılandırırken, belirtilen tüm verilerin gönderileceği bir depolama hesabı belirtmeniz gerekir. Verileri farklı konumlara göndermek için isteğe bağlı olarak bir veya daha fazla *veri havuzları* ekleyebilirsiniz.

- Azure Izleyici havuzu-Azure Izleyici ölçümlerine konuk performans verileri gönderin.
- Olay Hub 'ı havuzu-Azure 'un dışından iletmek için konuk performansı ve günlük verilerini Azure Olay Hub 'larına gönderin. Bu havuz Azure portal yapılandırılamaz.


## <a name="install-with-azure-portal"></a>Azure portal ile yüklensin
Tanılama uzantısını, yapılandırma ile doğrudan çalışmaktan farklı bir arabirim sağlayan Azure portal tek bir sanal makineye yükleyebilir ve yapılandırabilirsiniz. Tanılama uzantısını etkinleştirdiğinizde, en yaygın performans sayaçları ve olayları ile otomatik olarak varsayılan bir yapılandırma kullanılır. Bu varsayılan yapılandırmayı, özel gereksinimlerinize göre değiştirebilirsiniz.

> [!NOTE]
> Aşağıda, tanılama uzantısı için en yaygın ayarların açıklaması verilmiştir. Tüm yapılandırma seçenekleri hakkında ayrıntılı bilgi için bkz. [Windows Tanılama uzantısı şeması](diagnostics-extension-schema-windows.md).

1. Azure portal bir sanal makinenin menüsünü açın.

2. VM menüsünün **izleme** bölümünde **Tanılama ayarları** ' na tıklayın.

3. Tanılama uzantısı zaten etkinleştirilmemişse **Konuk düzeyinde Izlemeyi etkinleştir** ' e tıklayın.

   ![İzlemeyi etkinleştirme](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Ada sahip VM için yeni bir Azure depolama hesabı oluşturulacak, sanal makine için kaynak grubunun adını temel alır ve varsayılan bir grup konuk performans sayacı ve günlüğü seçilir.

   ![Tanılama ayarları](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. **Performans sayaçları** sekmesinde, bu sanal makineden toplamak istediğiniz konuk ölçümlerini seçin. Daha gelişmiş seçim için **özel** ayarı kullanın.

   ![Performans sayaçları](media/diagnostics-extension-windows-install/performance-counters.png)

6. **Günlükler** sekmesinde, sanal makineden toplanacak günlükleri seçin. Günlükler depolama veya Olay Hub 'larına gönderilebilir, ancak Azure Izleyici 'ye gönderilemez. Azure Izleyici 'de Konuk günlüklerini toplamak için [Log Analytics aracısını](../agents/log-analytics-agent.md) kullanın.

   ![Ekran görüntüsü, bir sanal makine için farklı Günlükler seçili olan Günlükler sekmesini gösterir.](media/diagnostics-extension-windows-install/logs.png)

7. **Kilitlenme dökümleri** sekmesinde, kilitlenme sonrasında bellek dökümlerini toplamak için herhangi bir işlem belirtin. Veriler, tanılama ayarı için depolama hesabına yazılır ve isteğe bağlı olarak bir blob kapsayıcısı belirtebilirsiniz.

   ![Kilitlenme bilgi dökümleri](media/diagnostics-extension-windows-install/crash-dumps.png)

8. **Havuzlar** sekmesinde, verileri Azure depolama dışındaki konumlara göndermek isteyip istemediğinizi belirtin. **Azure izleyici**' yi seçerseniz, konuk performans verileri Azure Izleyici ölçümlerine gönderilir. Azure portal kullanarak Olay Hub 'ları havuzunu yapılandıramazsınız.

   ![Ekran görüntüsü, Azure 'a Tanılama verileri gönder seçeneğinin etkin olduğu havuzlar sekmesini gösterir.](media/diagnostics-extension-windows-install/sinks.png)
   
   Sanal makineniz için yapılandırılmış bir sistem tarafından atanan kimliği etkinleştirmediyseniz, Azure Izleyici havuzu ile bir yapılandırmayı kaydettiğinizde aşağıdaki uyarıyı görebilirsiniz. Sistem tarafından atanan kimliği etkinleştirmek için başlık üzerine tıklayın.
   
   ![Yönetilen varlık](media/diagnostics-extension-windows-install/managed-entity.png)

9. **Aracıda**, depolama hesabını değiştirebilir, disk kotasını ayarlayabilir ve tanılama altyapısı günlüklerinin toplanmasını isteyip istemediğinizi belirtebilirsiniz.  

   ![Ekran görüntüsü, depolama hesabını ayarlama seçeneğiyle Aracı sekmesini gösterir.](media/diagnostics-extension-windows-install/agent.png)

10. Yapılandırmayı kaydetmek için **Kaydet** ' e tıklayın. 

> [!NOTE]
> Tanılama uzantısının yapılandırması JSON veya XML içinde biçimlendirilese de, Azure portal yapılan tüm yapılandırmalar her zaman JSON olarak depolanır. Başka bir yapılandırma yöntemiyle XML kullanırsanız ve sonra Azure portal yapılandırmanızı değiştirirseniz, ayarlar JSON olarak değiştirilir.

## <a name="resource-manager-template"></a>Resource Manager şablonu
Tanılama uzantısını Azure Resource Manager şablonlarıyla dağıtma hakkında [bir Windows sanal makinesi ve Azure Resource Manager şablonları ile izleme ve tanılamayı kullanma](../../virtual-machines/extensions/diagnostics-template.md) konusuna bakın. 

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı
Azure CLı, aşağıdaki örnekte olduğu gibi [az VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) kullanılarak mevcut bir sanal makineye Azure tanılama uzantısını dağıtmak için kullanılabilir. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Korunan ayarlar, yapılandırma şemasının [Privateconfig öğesinde](diagnostics-extension-schema-windows.md#privateconfig-element) tanımlanmıştır. Aşağıda, depolama hesabını tanımlayan korumalı ayarlar dosyasının en az bir örneği verilmiştir. Özel ayarların tüm ayrıntıları için bkz. [örnek yapılandırma](diagnostics-extension-schema-windows.md#privateconfig-element) .

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

Ortak ayarlar, yapılandırma şemasının [ortak öğesinde](diagnostics-extension-schema-windows.md#publicconfig-element) tanımlanmıştır. Aşağıda, tanılama altyapı günlüklerinin toplanması, tek bir performans sayacı ve tek bir olay günlüğü sağlayan bir genel ayarlar dosyası örneği verilmiştir. Genel ayarların tüm ayrıntıları için bkz. [örnek yapılandırma](diagnostics-extension-schema-windows.md#publicconfig-element) .

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell dağıtımı
PowerShell, aşağıdaki örnekte olduğu gibi [set-Azvmdiagnosticsextenma](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) kullanılarak mevcut bir sanal makineye Azure tanılama uzantısını dağıtmak için kullanılabilir. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Özel ayarlar [Privateconfig öğesinde](diagnostics-extension-schema-windows.md#privateconfig-element)tanımlanır, ancak genel ayarlar yapılandırma şemasının [ortak öğesinde](diagnostics-extension-schema-windows.md#publicconfig-element) tanımlanmıştır. Ayrıca, depolama hesabının ayrıntılarını özel ayarlara dahil etmek yerine Set-AzVMDiagnosticsExtension cmdlet 'inin parametreleri olarak belirtmeyi de tercih edebilirsiniz.

Aşağıda, tanılama altyapısı günlüklerinin, tek bir performans sayacının ve tek bir olay günlüğünün toplanmasını sağlayan bir yapılandırma dosyasının en az bir örneği verilmiştir. Özel ve genel ayarların tüm ayrıntıları için bkz. [örnek yapılandırma](diagnostics-extension-schema-windows.md#publicconfig-element) . 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Ayrıca bkz. [Windows çalıştıran bir sanal makinede Azure tanılama etkinleştirmek Için PowerShell 'ı kullanma](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Veri depolama
Aşağıdaki tabloda, tanılama uzantısından toplanan farklı veri türleri ve bunların bir tablo ya da blob olarak saklanıp saklanmayacağı listelenmiştir. Tablolarda depolanan veriler ayrıca, ortak yapılandırmanızda [StorageType ayarına](diagnostics-extension-schema-windows.md#publicconfig-element) bağlı olarak bloblarda depolanabilir.


| Veriler | Depolama türü | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tablo | Tanılama İzleyicisi ve yapılandırma değişiklikleri. |
| WADDirectoriesTable | Tablo | Tanılama izleyicisinin izlediği dizinler.  Buna IIS günlükleri, IIS başarısız istek günlükleri ve özel dizinler dahildir.  Blob günlük dosyasının konumu kapsayıcı alanında belirtilir ve BLOB adı RelativePath alanında bulunur.  AbsolutePath alanı, Azure sanal makinesinde var olan dosyanın konumunu ve adını gösterir. |
| WadLogsTable | Tablo | İzleme dinleyicisi kullanılarak kodda yazılan günlükler. |
| WADPerformanceCountersTable | Tablo | Performans sayaçları. |
| WADWindowsEventLogsTable | Tablo | Windows olay günlükleri. |
| WAD-IIS-failedreqlogfiles | Blob | IIS başarısız Istek günlüklerinden bilgiler içerir. |
| WAD-IIS-LogFiles | Blob | IIS günlükleriyle ilgili bilgiler içerir. |
| Özel | Blob | Tanılama İzleyicisi tarafından izlenen dizinleri yapılandırmaya dayalı özel bir kapsayıcı.  Bu blob kapsayıcısının adı WADDirectoriesTable içinde belirtilecektir. |

## <a name="tools-to-view-diagnostic-data"></a>Tanılama verilerini görüntülemek için Araçlar
Verileri depolama alanına aktarıldıktan sonra görüntülemek için kullanabileceğiniz çeşitli araçlar vardır. Örnek:

* Visual Studio 'da Sunucu Gezgini-Azure araçlarını Microsoft Visual Studio yüklediyseniz, Azure depolama hesaplarınızdan salt okunurdur blob ve tablo verilerini görüntülemek için Sunucu Gezgini Azure Storage düğümünü kullanabilirsiniz. Yerel depolama öykünücü hesabınızdan ve ayrıca Azure için oluşturduğunuz depolama hesaplarından verileri görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Sunucu Gezgini Ile depolama kaynaklarına göz atma ve yönetme](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md) , Windows, OSX ve Linux 'Ta Azure Depolama verileriyle kolayca çalışabilmenizi sağlayan tek başına bir uygulamadır.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) , Azure üzerinde çalışan uygulamalar tarafından toplanan tanılama verilerini görüntülemenize, yüklemenize ve yönetmenize olanak tanıyan Azure tanılama yöneticisini içerir.

## <a name="next-steps"></a>Sonraki adımlar
- İzleme verilerini Azure Event Hubs 'a iletme hakkında daha fazla bilgi için bkz. [Windows Azure tanılama uzantısından veri gönderme Event Hubs](diagnostics-extension-stream-event-hubs.md) .
