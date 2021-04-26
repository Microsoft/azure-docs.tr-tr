---
title: Windows sanal masaüstü maliyet fiyatlandırma tahminlerini izleme-Azure
description: Windows sanal masaüstü için Azure Izleyici 'yi kullanmaya yönelik maliyetleri ve fiyatlandırmayı tahmin etme.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 26262f83e14602d6ea93f96ec47630ef870c357d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309314"
---
# <a name="estimate-azure-monitor-costs"></a>Azure Izleyici maliyetlerini tahmin etme

Azure Izleyici günlükleri, ortamınız tarafından oluşturulan verileri toplayan, dizinleyen ve depolayan bir hizmettir. Bu nedenle, Azure Izleyici fiyatlandırma modeli, Log Analytics çalışma alanınız tarafından günde alınan ve işlenen (veya "alınan") veri miktarına göre günde bir gigabayt olarak hesaplanır. Log Analytics çalışma alanının maliyeti, yalnızca toplanan veri hacmine ve seçtiğiniz Azure ödeme planına ve ortamınızın oluşturduğu verileri depolamayı ne kadar tercih ettiğiniz hakkında temel değildir.

Bu makalede, Azure Izleyici 'deki fiyatlandırmayı nasıl çalıştığı hakkında bilgi edinmek için aşağıdaki noktalar açıklanmaktadır:

- Bu özelliği etkinleştirmeden önce veri alımı ve depolama maliyetlerini ön tahmin etme
- Bu özelliği kullanırken maliyetleri azaltmak için alma ve depolama alanınızı ölçme ve denetleme

>[!NOTE]
> Bu makalede listelenen tüm boyutlar ve fiyatlar, tahmine göre nasıl çalıştığını göstermek için yalnızca örnektir. Azure Izleyici Log Analytics fiyatlandırma modelinize ve Azure bölgesine göre daha doğru bir değerlendirme için bkz. [Azure Monitor fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Veri alımı ve depolama maliyetlerini tahmin etme

Log Analytics çalışma alanınızda Günlükler olarak yazılmış, önceden tanımlanmış bir veri kümesi kullanmanızı öneririz. Aşağıdaki örnek tahminlerde varsayılan yapılandırmadaki faturalandırılabilir verilere bakacağız

Windows sanal masaüstü için Azure Izleyici için önceden tanımlanmış veri kümeleri şunları içerir:

- Oturum konaklarından performans sayaçları
- Oturum konaklarından Windows olay günlükleri
- Hizmet altyapısından Windows sanal masaüstü tanılaması

Veri alımı ve depolama maliyetleriniz, ortamınızın boyutuna, sistem durumuna ve kullanımınıza bağlıdır. Bu makalede, bekleneceğiniz maliyet aralıklarını hesaplamak için, [sanal makine boyutlandırma kılavuzlarımızı](/windows-server/remote/remote-desktop-services/virtual-machine-recs)temel alarak, beklediğimiz bir dizi veri alımı ve depolama maliyetlerini hesaplamak için, güç kullanımına açık çalışan sanal makinelere bağlı olan örnek tahminlerden de yararlanabilirsiniz.

Örneğimizde kullanacağınız hafif kullanım sanal makinesi aşağıdaki bileşenleri içerir:

- 4 vCPU, 1 disk
- gün başına 16 oturum
- Ortalama oturum süresi 2 saat (120 dakika)
- oturum başına 100 işlem

Örneğimizde kullanılacak güç kullanım sanal makinesi aşağıdaki bileşenleri içerir:

- 6 vCPU, 1 disk
- gün başına 6 oturum
- Ortalama oturum süresi 4 saat (240 dakika)
- oturum başına 200 işlem

## <a name="estimating-performance-counter-ingestion"></a>Performans sayacı alımı tahmin ediliyor

Performans sayaçları, sistem kaynaklarının nasıl çalıştığını gösterir. Performans sayacı veri alımı, ortamınızın boyutuna ve kullanımına bağlıdır. Çoğu durumda, performans sayaçları Windows sanal masaüstü için Azure Izleyici için veri alımı %80 ' e kadar %99 ' i göstermelidir.

Tahmin etmeye başlamadan önce her bir performans sayacının verileri belirli bir sıklıkta göndereceğini anlamanız önemlidir. Varsayılan örnek olarak dakika başına bir değer belirledik (bu oranı ayarlarınızda da düzenleyebilirsiniz), ancak bu oran sayaca bağlı olarak farklı çarpma faktörlerine uygulanır. Aşağıdaki faktörler oranı etkiler:

- Her sayaç sanal makine başına (VM) faktörü için, VM çalışırken varsayılan örnek hızda ortamınızda VM başına veri gönderir. Bu sayaçların gün başına gönderdiği kayıt sayısını, ortamınızdaki VM sayısına göre dakika başına varsayılan örnek hızını çarparak tahmin edebilirsiniz ve bu sayı, günde ortalama VM çalışma süresi kadar çarpılarak çarpılır.

   Özetlemek gerekirse:

   Dakika başına varsayılan örnek oranı × sanal makine SKU 'sunda CPU çekirdekleri sayısı × ortalama VM çalışma süresi = gün başına gönderilen kayıt sayısı

- CPU başına faktör için her sayaç, sanal makine çalışırken ortamınızdaki her VM 'de vCPU başına varsayılan örnek hızda gönderilir. Sanal makine SKU 'sunda CPU çekirdekleri sayısına göre dakika başına varsayılan örnek hızını çarparak, sayaçların her gün göndereceği kayıt sayısını tahmin edebilirsiniz, daha sonra bu sayıyı VM 'nin çalıştığı dakika sayısıyla ve ortamınızdaki sanal makine sayısına göre çarpar.

   Özetlemek gerekirse:
   
   Dakika başına varsayılan örnek oranı × sanal makine SKU 'su için CPU çekirdekleri sayısı x $ dakika sayısı $ sanal makine sayısı (günde gönderilen kayıt sayısı)

- Her sayaç, her bir sanal makinenin ortamınızdaki her bir disk için varsayılan örnek hızda her sayaç için verileri gönderir. Bu sayaçların gün başına göndereceği kayıt sayısı, VM SKU 60 'sunda disk sayısıyla çarpılarak dakika başına varsayılan örnek hızına eşittir ve son olarak bir VM için Ortalama etkin saatlere çarpılır.

   Özetlemek gerekirse:

   Dakikada varsayılan örnek oranı × $ sanal makine SKU 'SU için disk sayısı × 60 dakika, VM sayısı × ortalama VM çalışma süresi = günde gönderilen kayıt sayısı

- Oturum başına faktör için her sayaç, oturum bağlıyken ortamınızdaki her oturum için varsayılan örnek hızda verileri gönderir. Gün başına ortalama oturum sayısı ve ortalama oturum süresi arasındaki varsayılan örnek hızını çarparak, bu sayaçların günde kaç kayıt olarak gönderileceğini tahmin edebilirsiniz.

   Özetlemek gerekirse:

   Dakikada varsayılan örnek hız, gün başına ortalama oturum sayısı × ortalama oturum süresi = günde gönderilen kayıt sayısı

- İşlem başına faktör için her sayaç ortamınızdaki her oturumdaki her bir işlem için varsayılan hızda verileri gönderir. Günde ortalama oturum sayısı ile dakika başına varsayılan örnek hızını çarparak, bu sayaçların günde kaç kayıt sayısını tahmin edebilirsiniz. böylece, ortalama oturum süresine ve oturum başına ortalama işlem sayısına göre çarpılır.
  
   Özetlemek gerekirse:

   Gün başına varsayılan örnek hız, günde ortalama oturum sayısı × ortalama oturum süresi × oturum başına ortalama işlem sayısı = günde gönderilen kayıt sayısı

Aşağıdaki tabloda, Windows sanal masaüstü için Azure Izleyici 'nin topladığı 20 performans sayaçları ve varsayılan ücretleri listelenmektedir:

| Sayaç adı | Varsayılan örnek hızı | Sıklık faktörü |
|--------------|---------------------|------------------|
| Mantıksal disk (C:) \\ % boş alan | 60 saniye  | Disk başına             |
| Mantıksal disk (C:) \\ Ort. disk kuyruğu uzunluğu   | 30 saniye    | Disk başına             |
| Mantıksal disk (C:) \\ Ortalama Disk sn/Aktarım  | 60 saniye       | Disk başına             |
| Mantıksal disk (C:) \\ geçerli disk kuyruğu uzunluğu  | 30 saniye      | Disk başına             |
| Bellek ( \* ) \\ kullanılabilir MBayt | 30 saniye    | VM başına  |
| Bellek ( \* ) \\ sayfa hatası/sn | 30 saniye   | VM başına  |
| Bellek ( \* ) \\ Sayfa/sn | 30 saniye   | VM başına  |
| Bellek ( \* ) \\ % kullanılan kaydedilmiş bayt | 30 saniye    | VM başına  |
| \*FizikselDisk () \\ Ort. disk kuyruğu uzunluğu | 30 saniye      | Disk başına             |
| \*FizikselDisk () \\ Ortalama Disk sn/okuma | 30 saniye  | Disk başına             |
| \*FizikselDisk () \\ Ortalama Disk sn/Aktarım | 30 saniye  | Disk başına             |
| \*FizikselDisk () \\ Ortalama Disk sn/yazma | 30 saniye | Disk başına             |
| İşlemci bilgileri (_Total) \\ % Işlemci zamanı | 30 saniye | Çekirdek/CPU başına         |
| Terminal Hizmetleri ( \* ) \\ etkin oturumlar          | 60 saniye | VM başına  |
| Terminal Hizmetleri ( \* ) \\ etkin olmayan oturumlar        | 60 saniye | VM başına  |
| Terminal Hizmetleri ( \* ) \\ Toplam oturum sayısı | 60 saniye | VM başına  |
| Işlem başına kullanıcı girişi gecikmesi ( \* ) \\ en fazla giriş gecikmesi         | 30 saniye | İşlem başına          |
| Oturum başına kullanıcı girişi gecikmesi ( \* ) \\ en fazla giriş gecikmesi        | 30 saniye | Oturum başına          |
| RemoteFX ağı ( \* ) \\ GEÇERLI TCP RTT | 30 saniye | VM başına  |
| RemoteFX ağı ( \* ) \\ geçerli UDP bant genişliği     | 30 saniye | VM başına  |

Her kayıt boyutunu 200 bayt olacak şekilde tahmin etmemiz durumunda varsayılan örnek hızda hafif bir iş yükü çalıştıran örnek bir VM, sanal makine başına günde kabaca 90 megabayt performans sayacı verilerini gönderir. Bu arada, bir güç yükü çalıştıran örnek bir VM, VM başına günde yaklaşık 130 megabayt performans sayacı verileri gönderir. Ancak, kayıt boyutu ve ortam kullanımı farklılık gösterebilir, bu nedenle dağıtımınızın kullandığı gün başına megabayt farklı olabilir.

Giriş gecikmesi performans sayaçları hakkında daha fazla bilgi edinmek için bkz. [Kullanıcı girişi gecikmesi performans sayaçları](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>Windows olay günlüğü alımı tahmin ediliyor

Windows olay günlükleri, Windows sanal makinelerinde Log Analytics aracıları tarafından toplanan veri kaynaklarıdır. Sistem ve uygulama gibi standart günlüklerdeki olayları, izlemeniz gereken uygulamalar tarafından oluşturulan özel günlükleri de toplayabilirsiniz.

Windows sanal masaüstü için Azure Izleyici için varsayılan Windows olayları şunlardır:

- Uygulama
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/operasyonel
- Sistem
- Microsoft-FSLogix-Apps/Işletimsel
- Microsoft-FSLogix-uygulamalar/yönetici

Her olay koşulları ortamda karşılandığında Windows olayları gönderilir. Sağlıklı durumdaki makineler, sağlıksız durumlarda makinelerden daha az olay gönderir. Olay sayısı tahmin edilemez olduğundan, bu tahmine yönelik sağlıklı ortamların örneklerine bağlı olarak günde bir VM başına 1.000 ile 10.000 olay aralığı kullanıyoruz. Örneğin, bu örnekteki her bir olay kaydı boyutunu 1.500 bayt olacak şekilde tahmin ettik, bu, belirtilen ortam için günde yaklaşık 2 ile 15 megabayt olay verilerini kullanıma sunar.

Windows olayları hakkında daha fazla bilgi için bkz. [Windows olay kayıtları özellikleri](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Tanılama alımı tahmin ediliyor

Tanılama hizmeti hem Kullanıcı hem de yönetim eylemleri için etkinlik günlükleri oluşturur.

Bunlar, tanılama sayacının izlediği etkinlik günlüklerinin adlarıdır:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

Hizmet, bir kayıt oluşturmak için gereken koşulları her karşıladığı zaman tanılama bilgilerini gönderir. Tanılama kayıt sayısı tahmin edilemediği için, bu tahmine yönelik sağlıklı ortamların örneklerine bağlı olarak günde bir VM başına 500 ile 1000 olay arasında bir Aralık kullanıyoruz.

Örneğin, bu örnekteki her bir tanılama kaydı boyutunu 200 bayt olacak şekilde tahmin etmemiz halinde toplam alınan veriler, VM başına günde 1 MB 'tan az olacaktır.

Etkinlik günlüğü kategorileri hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü tanılama](diagnostics-log-analytics.md).

## <a name="estimating-total-costs"></a>Toplam maliyet tahmin ediliyor

Son olarak, toplam maliyeti tahmin edelim. Bu örnekte, önceki bölümlerdeki örnek değerlere göre aşağıdaki sonuçlarla karşılaştık.

| Veri kaynağı        | Gün başına tahmini boyut (MB)   |
|-------------------------------------|------------------------------------------|
| Performans sayaçları   | 90-130 |
| Ekinlikler    | 2-15 |
| Windows sanal masaüstü tanılaması | \< 1 |

Bu örnekte, Windows sanal masaüstü için Azure Izleyici 'nin Toplam alınan verileri, VM başına günde 92 ile 145 megabayt arasındadır. Diğer bir deyişle, her bir sanal makine yaklaşık 31 günde bir, yaklaşık 3 GB veri alır.

[Log Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/)Için varsayılan Kullandıkça Öde modelini kullanarak, Azure izleyici veri toplamayı ve aylık depolama maliyetini tahmin edebilirsiniz. Veri alma isteğinize bağlı olarak, Log Analytics fiyatlandırma için kapasite ayırma modelini de göz önünde bulundurmayabilirsiniz.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Maliyetleri azaltmak için veri alımını yönetin

Bu bölümde, maliyetleri azaltmak için veri alımını ölçme ve yönetme işlemi açıklanmaktadır.

Çalışma kitabı için hakları ve izinleri yönetme hakkında bilgi edinmek için bkz. [erişim denetimi](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>Veri noktalarının kaldırılması, Windows sanal masaüstü için Azure Izleyici 'de karşılık gelen görselleri etkiler.

### <a name="log-analytics-settings"></a>Log Analytics Ayarları

Veri alımını yönetmek için Log Analytics ayarlarınızı iyileştirmek için bazı öneriler aşağıda verilmiştir:

- Log Analytics yalnızca Windows sanal masaüstü dağıtımınızdaki sanal makineler için performans sayaçlarını ve olayları toplayıp toplamayacağını sağlamak için Windows sanal masaüstü kaynaklarınız için tasarlanmış bir Log Analytics çalışma alanı kullanın.
- Maliyetleri yönetmek için Log Analytics depolama ayarlarınızı yapın. Saklama süresini azaltabilir, sabit bir depolama fiyatlandırma katmanının daha uygun maliyetli olup olmadığını değerlendirebilir ya da sağlıksız bir dağıtımın etkisini sınırlamak için ne kadar veri elde edebileceğinizi belirleyin. Daha fazla bilgi edinmek için bkz. [Azure Izleyici günlükleri için kullanımı ve maliyetleri yönetme](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Fazla veriyi kaldır

Varsayılan yapılandırması, Windows sanal masaüstü için Azure Izleyici için önerdiğimiz tek veri kümesidir. Her zaman ek veri noktaları ekleme ve bunları konak tanılama 'da görüntüleme seçeneğiniz vardır: konak tarayıcısı veya bunlar için özel grafikler oluşturun, ancak eklenen veriler Log Analytics maliyetinizi artırır. Bunlar, maliyet tasarrufları için kaldırılabilir.

### <a name="measure-and-manage-your-performance-counter-data"></a>Performans sayacı verilerinizi ölçün ve yönetin

Doğru izleme maliyetleriniz, ortamınızın boyutuna, kullanımına ve sistem durumuna bağlıdır. Log Analytics çalışma alanınızda veri alımı ölçümünü anlamak için bkz. giriş [verilerini anlama](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)

Oturum ana bilgisayarlarının kullanması için performans sayaçları, büyük olasılıkla Windows sanal masaüstü için Azure Izleyici 'nin en büyük veri kaynağınız olacaktır. Bir Log Analytics çalışma alanı için aşağıdaki özel sorgu şablonu, son gün içinde performans sayacı başına düşen sıklık ve megabayt değerini izleyebilir:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Şablonun yer tutucu değerlerini ortamınızın kullandığı değerlerle değiştirdiğinizden emin olun, aksi takdirde sorgu çalışmaz.

Bu sorgu, yalnızca Windows sanal masaüstü için Azure Izleyici için varsayılan olanları değil, ortamda etkinleştirdiğiniz tüm performans sayaçlarını gösterir. Bu bilgiler, bir sayacın sıklığını azaltma veya tamamen kaldırma gibi maliyetleri azaltmak için hangi alanların hedeflenecek olduğunu anlamanıza yardımcı olabilir.

Ayrıca, performans sayaçlarını kaldırarak maliyetleri azaltabilirsiniz. Performans sayaçlarını kaldırma veya mevcut sayaçları düzenleme hakkında bilgi edinmek için bkz. [performans sayaçlarını yapılandırma](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Windows olay günlüklerini yönetme

Windows olayları, tüm konaklar sağlıklı olduğunda veri alımı sırasında ani bir artış oluşmasına neden olabilir. Sağlıksız bir ana bilgisayar, günlüğe gönderilen olay sayısını artırabilir, ancak bilgilerin ana bilgisayarın sorunlarını gidermek için önemli olabilir. Bunları tutmanız önerilir. Windows olay günlüklerini yönetme hakkında daha fazla bilgi edinmek için bkz. [Windows olay günlüklerini yapılandırma](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Tanılamayı yönetme

Windows sanal masaüstü Tanılaması, veri depolama maliyetlerinizin %1 ' inden daha az bir hale gelmelidir, bu nedenle bunları kaldırmanızı önermeyiz. Windows sanal masaüstü tanılamayı yönetmek için [Tanılama özelliği için Log Analytics kullanın](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerde Windows sanal masaüstü için Azure Izleyici hakkında daha fazla bilgi edinin:

- [Dağıtımınızı izlemek Için Windows sanal masaüstü Için Azure izleyici 'Yi kullanın](azure-monitor.md).
- Hüküm ve kavramlar hakkında daha fazla bilgi edinmek için [sözlüğü](azure-monitor-glossary.md) kullanın.
- Bir sorunla karşılaşırsanız yardım için [sorun giderme kılavuzumuzu](troubleshoot-azure-monitor.md) inceleyin.
- İzleme maliyetlerinizi yönetme hakkında daha fazla bilgi edinmek için [Azure izleyici 'de izleme kullanımı ve tahmini maliyetlere](../azure-monitor/usage-estimated-costs.md) göz atın.
