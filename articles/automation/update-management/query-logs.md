---
title: Azure Otomasyonu Güncelleştirme Yönetimi günlüklerini sorgulama
description: Bu makalede, Log Analytics çalışma alanınızdaki Güncelleştirme Yönetimi günlüklerini sorgulama açıklanmaktadır.
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 5eb0c7d72896cc9a27907743b1b9c3d5a40614dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592871"
---
# <a name="query-update-management-logs"></a>Güncelleştirme Yönetimi günlüklerini sorgulama

Güncelleştirme Yönetimi dağıtımı sırasında sağlanmış olan ayrıntılara ek olarak, Log Analytics çalışma alanınızda depolanan günlüklerde arama yapabilirsiniz. Otomasyon hesabınızdan günlükleri aramak için **güncelleştirme yönetimi** ' ni seçin ve dağıtımınızla ilişkili Log Analytics çalışma alanını açın.

Ayrıca günlük sorgularını özelleştirebilir veya farklı istemcilerden kullanabilirsiniz. Bkz. [Log Analytics arama API 'si belgeleri](https://dev.loganalytics.io/).

## <a name="query-update-records"></a>Sorgu güncelleştirme kayıtları

Güncelleştirme Yönetimi, Windows ve Linux VM 'Leri için kayıtları ve günlük araması sonuçlarında görünen veri türlerini toplar. Aşağıdaki bölümlerde bu kayıtlar açıklanır.

### <a name="query-required-updates"></a>Gerekli güncelleştirmeleri sorgula

`RequiredUpdate`Bir makine için gereken güncelleştirmeleri temsil eden, türü olan bir kayıt oluşturulur. Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|----------|-------------|
| Bilgisayar | Raporlama makinesinin tam etki alanı adı. |
| KBID | Windows Update Bilgi Bankası makalesi KIMLIĞI. |
| ManagementGroupName | Operations Manager yönetim grubunun veya Log Analytics çalışma alanının adı. |
| Ürün | Güncelleştirmenin geçerli olduğu ürünler. |
| PublishDate | Güncelleştirmenin Windows Update indirilmeye ve yüklenmeye hazırlanma tarihi. |
| Sunucu | | 
| Sourcehealthserviceıd | Log Analytics Windows Agent KIMLIĞINI temsil eden benzersiz tanımlayıcı. |
| SourceSystem | *OperationsManager* |
| TenantId | Azure Active Directory kurumları örneğinizi temsil eden benzersiz tanımlayıcı. |
| TimeGenerated | Kaydın oluşturulduğu tarih ve saat. |
| Tür | *Güncelleştirme* |
| UpdateClassification | Uygulanabilecek güncelleştirmelerin türünü gösterir. Windows için:<br> *Kritik güncelleştirmeler*<br> *Güvenlik güncelleştirmeleri*<br> *Güncelleştirme paketleri*<br> *Özellik paketleri*<br> *Hizmet paketleri*<br> *Tanım güncelleştirmeleri*<br> *Araçlar*<br> *Güncelleştirmeler*. Linux için:<br> *Kritik güncelleştirmeler ve güvenlik güncelleştirmeleri*<br> *Diğer* |
| Updateönem derecesi | Güvenlik Açığı için önem derecesi. Değerler şunlardır:<br> *Kritik*<br> *Önemli*<br> *Orta*<br> *Düşük* |
| UpdateTitle | Güncelleştirme başlığı.|

### <a name="query-update-record"></a>Sorgu güncelleştirme kaydı

Bir `Update` makine için kullanılabilir güncelleştirmeleri ve bunların yükleme durumlarını temsil eden türü bir kayıt oluşturulur. Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|----------|-------------|
| ApprovalSource | Yalnızca Windows işletim sistemi için geçerlidir. Kayıt için onay kaynağı. Değer Microsoft Update. |
| Onaylandı | Kayıt onaylanırsa true, aksi takdirde false. |
| Sınıflandırma | Onay sınıflandırması. Değer güncelleştirmeler ' dir. |
| Bilgisayar | Raporlama makinesinin tam etki alanı adı. |
| Bilgisayar ortamı | Ortamınızın. Olası değerler Azure veya Azure dışı bir değer. |
| MSRCBulletinID | Güvenlik Bülteni KIMLIK numarası. |
| MSRCSeverity | Güvenlik Açığı için önem derecesi. Değerler şunlardır:<br> Kritik<br> Önemli<br> Orta<br> Düşük |  
| KBID | Windows Update Bilgi Bankası makalesi KIMLIĞI. |
| ManagementGroupName | Operations Manager yönetim grubunun veya Log Analytics çalışma alanının adı. |
| UpdateID | Yazılım güncelleştirmesinin benzersiz tanıtıcısı. |
| RevisionNumber | Bir güncelleştirmenin belirli bir düzeltmesine ait düzeltme numarası. |
| İsteğe Bağlı | Kayıt isteğe bağlı ise true, aksi takdirde false. |
| RebootBehavior | Bir güncelleştirmeyi yükledikten/kaldırdıktan sonra yeniden başlatma davranışı. |
| _ResourceId | Kayıtla ilişkili kaynak için benzersiz tanımlayıcı. |
| Tür | Kayıt türü. Değer Güncelleştir ' dir. |
| VMUUID | Sanal makine için benzersiz tanımlayıcı. |
| MG | Yönetim grubu veya Log Analytics çalışma alanı için benzersiz tanımlayıcı. |
| TenantId | Kuruluşunuzun Azure Active Directory örneğini temsil eden benzersiz tanımlayıcı. |
| SourceSystem | Kayıt için kaynak sistem. Bu durumda değer `OperationsManager` olur. |
| TimeGenerated | Kayıt oluşturmanın tarih ve saati. |
| SourceComputerId | Kaynak bilgisayarı temsil eden benzersiz tanımlayıcı. |
| Başlık | Güncelleştirme başlığı. |
| PublishedDate (UTC) | Güncelleştirmenin Windows Update indirilmeye ve yüklenmeye hazırlanma tarihi.  |
| UpdateState | Güncelleştirmenin geçerli durumu. |
| Ürün | Güncelleştirmenin geçerli olduğu ürünler. |
| SubscriptionId | Azure aboneliğinin benzersiz tanımlayıcısı. |
| adlı yönetilen örnek, | Kaynağın ait olduğu kaynak grubunun adı. |
| ResourceProvider | Kaynak sağlayıcısı. |
| Kaynak | Kaynağın adı. |
| ResourceType | Kaynak türü. |

### <a name="query-update-agent-record"></a>Sorgu güncelleştirme Aracısı kaydı

`UpdateAgent`Makinedeki güncelleştirme aracısının ayrıntılarını sağlayan, türünde bir kayıt oluşturulur. Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Bilgisayar | Raporlama makinesinin tam etki alanı adı. |
| Daysıncelastupdatebucket | |
| ManagementGroupName | Operations Manager yönetim grubunun veya Log Analytics çalışma alanının adı. |
| OSVersion | İşletim sisteminin sürümü. |
| Sunucu | |
| Sourcehealthserviceıd | Log Analytics Windows Agent KIMLIĞINI temsil eden benzersiz tanımlayıcı. |
| SourceSystem | Kayıt için kaynak sistem. Bu durumda değer `OperationsManager` olur. |
| TenantId | Kuruluşunuzun Azure Active Directory örneğini temsil eden benzersiz tanımlayıcı. |
| TimeGenerated | Kayıt oluşturmanın tarih ve saati. |
| Tür | Kayıt türü. Değer Güncelleştir ' dir. |
| WindowsUpdateAgentVersion | Windows Update aracısının sürümü. |
| WSUSServer | Windows Update aracısında sorun gidermeye yardımcı olması için hatalar. |

### <a name="query-update-deployment-status-record"></a>Sorgu güncelleştirme dağıtım durumu kaydı

`UpdateRunProgress`Makinesine göre zamanlanmış bir dağıtımın güncelleştirme dağıtım durumunu sağlayan, türünde bir kayıt oluşturulur. Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|----------|-------------|
| Bilgisayar | Raporlama makinesinin tam etki alanı adı. |
| Bilgisayar ortamı | Ortamınızın. Değerler Azure veya Azure dışı bir değer. |
| CorrelationId | Güncelleştirme için çalışan runbook işinin benzersiz tanıtıcısı. |
| EndTime | Eşitleme işleminin sona erdiği zaman. *Bu özellik şu anda kullanılmıyor. Bkz. TimeGenerated.* |
| ErrorResult | Bir güncelleştirme yüklenemediğinde Windows Update hata kodu oluşturulur. |
| Yüklemedurumu | İstemci bilgisayarda bir güncelleştirmenin olası yükleme durumları,<br> `NotStarted` -iş henüz tetiklenemez.<br> `FailedToStart` -makinede iş başlatılamıyor.<br> `Failed` -iş başlatıldı, ancak bir özel durumla başarısız oldu.<br> `InProgress` -iş devam ediyor.<br> `MaintenanceWindowExceeded` -yürütme kalan, ancak bakım penceresi aralığına ulaşıldı.<br> `Succeeded` -iş başarılı oldu.<br> `InstallFailed` -güncelleştirme başarıyla yüklenemedi.<br> `NotIncluded`<br> `Excluded` |
| KBID | Windows Update Bilgi Bankası makalesi KIMLIĞI. |
| ManagementGroupName | Operations Manager yönetim grubunun veya Log Analytics çalışma alanının adı. |
| OSType | İşletim sisteminin türü. Değerler Windows veya Linux. |
| Ürün | Güncelleştirmenin geçerli olduğu ürünler. |
| Kaynak | Kaynağın adı. |
| ResourceId | Kayıtla ilişkili kaynak için benzersiz tanımlayıcı. |
| ResourceProvider | Kaynak sağlayıcısı. |
| ResourceType | Kaynak türü. |
| SourceComputerId | Kaynak bilgisayarı temsil eden benzersiz tanımlayıcı. |
| SourceSystem | Kayıt için kaynak sistem. Bu durumda değer `OperationsManager` olur. |
| StartTime | Güncelleştirmenin yüklenmek üzere zamanlandığı zaman. *Bu özellik şu anda kullanılmıyor. Bkz. TimeGenerated.* |
| SubscriptionId | Azure aboneliğinin benzersiz tanımlayıcısı. |
| SucceededOnRetry | Güncelleştirme yürütmenin ilk denemede başarısız olup olmadığını ve geçerli işlemin yeniden deneme girişimi olduğunu gösteren değer. |
| TimeGenerated | Kayıt oluşturmanın tarih ve saati. |
| Başlık | Güncelleştirme başlığı. |
| Tür | Güncelleştirmenin türü. Bu durumda değer `UpdateRunProgress` olur. |
| UpdateID | Yazılım güncelleştirmesinin benzersiz tanıtıcısı. |
| VMUUID | Sanal makine için benzersiz tanımlayıcı. |
| ResourceId | Kayıtla ilişkili kaynak için benzersiz tanımlayıcı. |

### <a name="query-update-summary-record"></a>Sorgu güncelleştirme Özeti kaydı

`UpdateSummary`Makineye göre güncelleştirme Özeti sağlayan, türü olan bir kayıt oluşturulur. Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|----------|-------------|
| Bilgisayar | Raporlama makinesinin tam etki alanı adı. |
| Bilgisayar ortamı | Ortamınızın. Değerler Azure veya Azure dışı bir değer. |
| CriticalUpdatesMissing | Eksik olan geçerli kritik güncelleştirme sayısı. |
| ManagementGroupName | Operations Manager yönetim grubunun veya Log Analytics çalışma alanının adı. |
| NETRuntimeVersion | Windows bilgisayarda yüklü .NET Framework sürümü. |
| OldestMissingSecurityUpdateBucket | Eksik olan en eski güvenlik demetini belirleyicisi. Değerler şunlardır:<br> En son değer 30 günden azsa<br> 30 gün önce<br> 60 gün önce<br> 90 gün önce<br> 120 gün önce<br> 150 gün önce<br> 180 gün önce<br> Değer 180 günden daha büyükse daha eski olur. |
| OldestMissingSecurityUpdateInDays | En eski güncelleştirme yüklü olmayan şekilde algılanan toplam gün sayısı. |
| OsVersion | İşletim sisteminin sürümü. |
| OtherUpdatesMissing | Algılanan güncelleştirme sayısı eksik. |
| Kaynak | Kayıt için kaynağın adı. |
| adlı yönetilen örnek, | Kaynağı içeren kaynak grubunun adı. |
| ResourceId | Kayıtla ilişkili kaynak için benzersiz tanımlayıcı. |
| ResourceProvider | Kaynak sağlayıcısı. |
| ResourceType | Kaynak türü. |
| RestartPending | Yeniden başlatma beklendiğinde true, aksi takdirde false. |
| SecurityUpdatesMissing | Geçerli olan eksik güvenlik güncelleştirmelerinin sayısı.|
| SourceComputerId | Sanal makine için benzersiz tanımlayıcı. |
| SourceSystem | Kayıt için kaynak sistem. Bu durumda değer `OpsManager` olur. |
| SubscriptionId | Azure aboneliğinin benzersiz tanımlayıcısı. |
| TimeGenerated | Kayıt oluşturmanın tarih ve saati. |
| TotalUpdatesMissing | Geçerli olan eksik güncelleştirmelerin toplam sayısı. |
| Tür | Kayıt türü. Bu durumda değer `UpdateSummary` olur. |
| VMUUID | Sanal makine için benzersiz tanımlayıcı. |
| WindowsUpdateAgentVersion | Windows Update aracısının sürümü. |
| WindowsUpdateSetting | Windows Update aracısının durumu. Olası değerler şunlardır:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Windows Update aracısında sorun gidermeye yardımcı olması için hatalar. |
| _ResourceId | Kayıtla ilişkili kaynak için benzersiz tanımlayıcı. |

## <a name="sample-queries"></a>Örnek sorgular

Aşağıdaki bölümlerde, Güncelleştirme Yönetimi için toplanan güncelleştirme kayıtlarına yönelik örnek günlük sorguları sağlanmaktadır.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Azure olmayan makinelerin Güncelleştirme Yönetimi için etkinleştirildiğini onaylayın

Doğrudan bağlı makinelerin Azure Izleyici günlükleriyle iletişim kurduğunu onaylamak için aşağıdaki günlük aramalarıyla birini çalıştırın.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Bir Windows bilgisayarında, Azure Izleyici günlükleriyle aracı bağlantısını doğrulamak için aşağıdaki bilgileri gözden geçirebilirsiniz:

1. Denetim Masası 'nda **Microsoft Monitoring Agent** açın. **Azure Log Analytics** sekmesinde, aracı şu iletiyi görüntüler: **Microsoft Monitoring Agent Log Analytics başarıyla bağlandı**.

1. Windows olay günlüğü 'Nü açın. **Uygulama ve hizmetler Logs\Operations Manager** ' a gidin ve kaynak **hizmeti bağlayıcısından** olay KIMLIĞI 3000 ve olay kimliği 5002 ' ni arayın. Bu olaylar, bilgisayarın Log Analytics çalışma alanına kaydolduğunu ve yapılandırmayı aldığını gösterir.

Aracı Azure Izleyici günlükleriyle iletişim kuramıyorsa ve aracı bir güvenlik duvarı veya ara sunucu üzerinden internet ile iletişim kurmak üzere yapılandırılmışsa, güvenlik duvarının veya ara sunucunun düzgün yapılandırıldığını onaylayın. Güvenlik duvarının veya proxy sunucusunun düzgün bir şekilde yapılandırıldığını nasıl doğrulayacağınızı öğrenmek için bkz. [Windows Aracısı Için ağ yapılandırması](../../azure-monitor/agents/agent-windows.md) veya [Linux Aracısı için ağ yapılandırması](../../azure-monitor/vm/quick-collect-linux-computer.md).

> [!NOTE]
> Linux sistemleriniz bir ara sunucu veya Log Analytics ağ geçidiyle iletişim kuracak şekilde yapılandırıldıysa ve Güncelleştirme Yönetimi etkinleştirirken, `proxy.conf` aşağıdaki komutları kullanarak dosya üzerinde omıuser grubuna Okuma izni vermek için izinleri güncelleştirin:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Yeni eklenen Linux aracıları, bir değerlendirme gerçekleştirildikten sonra **güncelleştirilmiş** durumunu gösterir. Bu işlem 6 saat kadar sürebilir.

Operations Manager yönetim grubunun Azure Izleyici günlükleriyle iletişim kurduğunu onaylamak için bkz. [Azure izleyici günlükleriyle Operations Manager tümleştirmeyi doğrulama](../../azure-monitor/agents/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Tek Azure VM değerlendirme sorguları (Windows)

VMUUID değerini, sorguladığınız sanal makinenin VM GUID 'SI ile değiştirin. Azure Izleyici günlüklerinde aşağıdaki sorguyu çalıştırarak kullanılması gereken VMUUID 'yi bulabilirsiniz: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Eksik güncelleştirmeler Özeti

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Eksik güncelleştirmeler listesi

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Tek Azure VM değerlendirmesi sorguları (Linux)

Bazı Linux 'lar için, Azure Resource Manager ve Azure Izleyici günlüklerinde depolanan VMUUID değeri ile bir [bitiçlik](https://en.wikipedia.org/wiki/Endianness) uyumsuzluğu vardır. Aşağıdaki sorgu, her iki durumda da bir eşleşme olup olmadığını denetler. Sonuçları doğru bir şekilde döndürmek için VMUUID değerlerini, GUID 'nin büyük endian ve little-endian biçimiyle değiştirin. Azure Izleyici günlüklerinde aşağıdaki sorguyu çalıştırarak kullanılması gereken VMUUID 'yi bulabilirsiniz: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Eksik güncelleştirmeler Özeti

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Eksik güncelleştirmeler listesi

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Çoklu VM değerlendirmesi sorguları

#### <a name="computers-summary"></a>Bilgisayar Özeti

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Eksik güncelleştirmeler Özeti

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Bilgisayar listesi

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Eksik güncelleştirmeler listesi

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Izleyici günlükleri hakkında daha fazla bilgi için bkz. [Azure izleyici günlükleri](../../azure-monitor/logs/log-query-overview.md).
* Uyarılarla ilgili yardım için bkz. [Uyarıları yapılandırma](configure-alerts.md).
