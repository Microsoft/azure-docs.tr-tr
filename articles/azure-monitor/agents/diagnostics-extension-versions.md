---
title: Windows Azure Tanılama uzantısı (WAD) yapılandırma şema sürümü geçmişi
description: Azure sanal makineler, VM Ölçek Kümeleri, Service Fabric ve Cloud Services performans sayaçlarını toplamaya uygun.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 57ba1c760fe8919fff95493c5c81da4368894f73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719823"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Windows Azure Tanılama uzantısı (WAD) yapılandırma şeması sürümleri ve geçmişi
Bu makalede, Microsoft Azure SDK 'sının bir parçası olarak gönderilen [Windows (WAD) şema sürümlerinin Azure tanılama uzantısının](diagnostics-extension-overview.md) sürüm geçmişi sağlanmaktadır.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK ve tanılama sürümleri sevkiyat grafiği  

|Azure SDK sürümü | Tanılama uzantısı sürümü | Modelleme|  
|------------------|-------------------------------|------|  
|'in               |1.0                            |eklenti|  
|2,0-2,4         |1.0                            |eklenti|  
|2.5               |1.2                            |uzantı|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1,5                            |"|  
|2.9               |1.6                            |"|
|2,96              |1.7                            |"|
|2,96              |1.8                            |"|
|2,96              |1.8.1                          |"|
|2,96              |1.9                            |"|
|2,96              |1,11                           |"|


 Azure Tanılama sürüm 1,0 ilk olarak bir eklenti modelinde gönderilir--Azure SDK 'yı yüklediğinizde, Azure tanılama 'nın bu sürümü ile birlikte sevk ettiğiniz anlamına gelir.  

 SDK 2,5 (Tanılama sürümü 1,2) ile başlayarak, Azure tanılama bir uzantı modeline gitti. Yeni özellikleri kullanmaya yönelik araçlar yalnızca daha yeni Azure SDK 'larında sunulmaktadır, ancak Azure tanılama kullanan herhangi bir hizmet, en son teslim sürümünü doğrudan Azure 'dan çeker. Örneğin, yine de SDK 2,5 kullanan herkes, daha yeni özellikleri kullanmalarından bağımsız olarak önceki tabloda gösterilen en son sürümü yüklüyor.  

## <a name="schemas-index"></a>Şemalar dizini  
Farklı Azure tanılama sürümleri farklı yapılandırma şemaları kullanır. Şema 1,0 ve 1,2 kullanım dışı bırakılmıştır. Sürüm 1,3 ve üzeri hakkında daha fazla bilgi için bkz. [tanılama 1,3 ve sonraki yapılandırma şeması](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Sürüm geçmişi

### <a name="diagnostics-extension-111"></a>Tanılama uzantısı 1,11
Azure Izleyici havuzu için destek eklendi. Bu havuz yalnızca performans sayaçları için geçerlidir. VM, VMSS veya bulut hizmetinizde toplanan performans sayaçlarının özel ölçümler olarak Azure Izleyicisine gönderilmesini sağlar. Azure Izleyici havuzu şunları destekler:
* Azure izleyici [ölçümleri API 'leri](/rest/api/monitor/metrics/list) aracılığıyla Azure izleyici 'ye gönderilen tüm performans sayaçlarını alma.
* Azure izleyici 'de yeni [birleşik uyarılar deneyimi](../alerts/alerts-overview.md) aracılığıyla Azure izleyici 'ye gönderilen tüm performans sayaçlarında uyarı verme
* Performans sayaçlarındaki joker karakter operatörü, ölçümünüzün "örnek" boyutu olarak değerlendiriliyor. Örneğin, "MantıksalDisk ( \* )/Diskwrites/SEC" sayacını topladıysanız, her mantıksal disk için (C:, D:, vb.) disk yazma/sn üzerinde çizim yapmak veya uyarı vermek üzere "örnek" boyutunda filtre uygulayabilir ve bölebilirsiniz.

Azure Izleyici 'yi tanılama uzantısı yapılandırmanızda yeni bir havuz olarak tanımlama
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Klasik VM 'Ler ve klasik bulut hizmeti için Azure Izleyici havuzunu yapılandırmak, tanılama uzantısının özel yapılandırmasında daha fazla parametre tanımlanmasını gerektirir.
>
> Daha fazla ayrıntı için lütfen [ayrıntılı tanılama uzantısı şeması belgelerine başvurun.](diagnostics-extension-schema-windows.md)

Daha sonra, performans Sayaçlarınızı Azure Izleyici havuzuna yönlendirilecek şekilde yapılandırabilirsiniz.
```json
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
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Tanılama uzantısı 1,9
Docker desteği eklendi.


### <a name="diagnostics-extension-181"></a>Tanılama uzantısı 1.8.1
Özel yapılandırmada bir depolama hesabı anahtarı yerine bir SAS belirteci belirtebilir. Bir SAS belirteci sağlanmışsa, depolama hesabı anahtarı yok sayılır.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Tanılama uzantısı 1,8
PublicConfig 'e depolama türü eklendi. StorageType *tablo*, *BLOB*, *tableandblob* olabilir. *Tablo* varsayılandır.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Tanılama uzantısı 1,7
EventHub 'e yönlendirme özelliği eklendi.

### <a name="diagnostics-extension-15"></a>Tanılama uzantısı 1,5
Uygulama ve altyapı düzeyinin yanı sıra uygulamanızdaki sorunları tanılamaya daha kolay hale getirmek [Application Insights](../app/cloudservices.md) ' ye Tanılama verileri gönderme özelliği eklendi.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2,6 ve tanılama uzantısı 1,3
Visual Studio 'da bulut hizmeti projeleri için aşağıdaki değişiklikler yapılmıştır. (Bu değişiklikler Azure SDK 'nın sonraki sürümleri için de geçerlidir.)

* Yerel öykünücü artık tanılamayı desteklemektedir. Bu değişiklik, tanılama verilerini toplayabilmeniz ve Visual Studio 'da geliştirme ve test yaparken uygulamanızın doğru izlemeleri oluşturmasıdır. Bağlantı dizesi, `UseDevelopmentStorage=true` Azure depolama öykünücüsü 'nü kullanarak Visual Studio 'da bulut hizmeti projenizi çalıştırırken tanılama veri toplamayı mümkün bir şekilde sunar. Tüm Tanılama verileri (geliştirme depolaması) depolama hesabında toplanır.
* Tanılama depolama hesabı bağlantı dizesi (Microsoft. WindowsAzure. eklenti. Diagnostics. ConnectionString), hizmet yapılandırma (. cscfg) dosyasında bir kez daha saklanır. Azure SDK 2,5 ' de tanılama depolama hesabı tanılama. wadcfgx dosyasında belirtilmiştir.

Bağlantı dizesinin Azure SDK 2,4 ve önceki sürümlerde çalıştığı ve Azure SDK 2,6 ve sonrasında nasıl çalıştığı hakkında bazı önemli farklılıklar vardır.

* Azure SDK 2,4 ve önceki sürümlerde bağlantı dizesi, tanılama günlüklerini aktarmaya yönelik depolama hesabı bilgilerini almak için tanılama eklentisi tarafından çalışma zamanında kullanıldı.
* Azure SDK 2,6 ve sonraki sürümlerinde, Visual Studio tanılama uzantısını yayımlama sırasında uygun depolama hesabı bilgileriyle yapılandırmak için tanılama bağlantı dizesini kullanır. Bağlantı dizesi, Visual Studio 'Nun yayımlarken kullanacağı farklı hizmet yapılandırmalarına yönelik farklı depolama hesapları tanımlamanızı sağlar. Ancak, tanılama eklentisi artık kullanılamadığından (Azure SDK 2,5 ' den sonra),. cscfg dosyası kendisi için tanılama uzantısını etkinleştiremez. Uzantıyı Visual Studio veya PowerShell gibi araçlarla ayrı olarak etkinleştirmeniz gerekir.
* PowerShell ile tanılama uzantısını yapılandırma işlemini basitleştirmek için, Visual Studio 'daki paket çıktısı her rolün tanılama uzantısının ortak yapılandırma XML 'sini de içerir. Visual Studio, genel yapılandırmadaki mevcut depolama hesabı bilgilerini doldurmak için tanılama bağlantı dizesini kullanır. Ortak yapılandırma dosyaları, uzantılar klasöründe oluşturulur ve bu stili izler `PaaSDiagnostics.<RoleName>.PubConfig.xml` . PowerShell tabanlı dağıtımlar, her yapılandırmayı bir role eşlemek için bu kalıbı kullanabilir.
* . Cscfg dosyasındaki bağlantı dizesi, Azure portal tarafından, **izleme** sekmesinde görünebilmesi için tanılama verilerine erişmek üzere de kullanılır. Hizmeti portalda ayrıntılı izleme verilerini gösterecek şekilde yapılandırmak için bağlantı dizesi gerekir.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projeleri Azure SDK 2,6 ve üzeri sürümlere geçirme
Azure SDK 2,5 ' den Azure SDK 2,6 veya sonraki bir sürüme geçiş yaparken,. wadcfgx dosyasında belirtilen bir tanılama depolama hesabınız varsa, bu durumda kalır. Farklı depolama yapılandırmalarının farklı depolama hesaplarını kullanma esnekliğinden faydalanmak için bağlantı dizesini projenize el ile eklemeniz gerekir. Bir projeyi Azure SDK 2,4 veya önceki sürümlerden Azure SDK 2,6 ' ye geçiriyorsanız, tanılama bağlantı dizeleri korunur. Bununla birlikte, bağlantı dizelerinin önceki bölümde belirtildiği gibi Azure SDK 2,6 ' de nasıl ele alındığına ilişkin değişikliklere göz önünde olduğunu aklınızda edin.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Visual Studio 'Nun tanılama depolama hesabını nasıl belirlediği
* . Cscfg dosyasında bir tanılama bağlantı dizesi belirtilmişse, Visual Studio bunu yayımlarken tanılama uzantısını yapılandırmak ve paketleme sırasında ortak yapılandırma XML dosyalarını oluştururken kullanır.
* . Cscfg dosyasında bir tanılama bağlantı dizesi belirtilmemişse, Visual Studio yayımlama sırasında tanılama uzantısını yapılandırmak ve paketleme sırasında ortak yapılandırma XML dosyalarını oluşturmak için. wadcfgx dosyasında belirtilen depolama hesabını kullanmaya geri döner.
* . Cscfg dosyasındaki tanılama bağlantı dizesi. wadcfgx dosyasındaki depolama hesabından önceliklidir. . Cscfg dosyasında bir tanılama bağlantı dizesi belirtilmişse, Visual Studio bunu kullanır ve. wadcfgx içindeki depolama hesabını yoksayar.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>"Geliştirme depolama bağlantı dizelerini güncelleştir..." onay kutusu yapılsın mı?
**Microsoft Azure yayımlarken Microsoft Azure depolama hesabı kimlik bilgileriyle tanılama ve önbelleğe alma Için güncelleştirme geliştirme depolama bağlantı dizeleri** , yayımlama sırasında belirtilen Azure depolama hesabı ile herhangi bir geliştirme depolama hesabı bağlantı dizesini güncelleştirmek için kullanışlı bir yol sağlar.

Örneğin, bu onay kutusunu seçtiğinizi ve tanılama bağlantı dizesinin gerektiğini varsayın `UseDevelopmentStorage=true` . Projeyi Azure 'da yayımladığınızda, Visual Studio tanılama bağlantı dizesini Yayımla sihirbazında belirttiğiniz depolama hesabıyla otomatik olarak güncelleştirir. Ancak, tanılama bağlantı dizesi olarak gerçek bir depolama hesabı belirtilmişse, bunun yerine bu hesap kullanılır.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Azure SDK 2,4 ve öncesi ile Azure SDK 2,5 ve üzeri arasındaki tanılama işlevselliği farkları
Projenizi Azure SDK 2,5 2,4 veya sonraki bir sürüme yükseltiyorsanız, aşağıdaki tanılama işlevleri farklılıklarını göz önünde bulundurmanız gerekir.

* **Yapılandırma API 'leri kullanım dışı** ; azure SDK 2,4 veya önceki sürümlerde tanılama yapılandırması kullanılabilir, ancak azure SDK 2,5 ve sonrasında kullanım dışı bırakılmıştır. Tanılama yapılandırmanız Şu anda kodda tanımlanmışsa, tanılamaların çalışmaya devam etmesini sağlamak için, geçirilen projedeki bu ayarları sıfırdan yeniden yapılandırmanız gerekir. Azure SDK 2,4 için tanılama yapılandırma dosyası, Azure SDK 2,5 ve üzeri için tanılama. wadcfg ve Diagnostics. wadcfgx 'tir.
* **Bulut hizmeti uygulamalarına yönelik Tanılamalar, örnek düzeyinde değil yalnızca rol düzeyinde yapılandırılabilir.**
* **Uygulamanızı her dağıttığınızda tanılama yapılandırması güncelleştirilir** . Bu, tanılama yapılandırmanızı Sunucu Gezgini değiştirirseniz ve sonra uygulamanızı yeniden dağıtmanız durumunda eşlik sorunlarına neden olabilir.
* **Azure sdk 2,5 ve sonraki sürümlerinde kilitlenme dökümleri tanılama yapılandırma dosyasında, kodda değil, yapılandırılır** . kodda yapılandırılmış kilitlenme dökümlerinin varsa, azure SDK 2,6 ' e geçiş sırasında kilitlenme dökümleri aktarılmadığı için yapılandırmayı koddan yapılandırma dosyasına el ile aktarmanız gerekir.