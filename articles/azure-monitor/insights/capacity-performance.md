---
title: Azure Izleyici 'de çözüm Kapasite ve Performans | Microsoft Docs
description: Hyper-V sunucularınızın kapasitesini anlamanıza yardımcı olması için Izleyici 'deki Kapasite ve Performans çözümünü kullanın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/13/2017
ms.openlocfilehash: 8b130b800b53afadc40e0c9b9a2b730f24da396e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899052"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Hyper-V sanal makine kapasitesini Kapasite ve Performans çözümüyle planlayın (kullanım dışı)

![Kapasite ve Performans simgesi](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Kapasite ve Performans Çözümü kullanım dışı bırakılmıştır.  Çözümü zaten yüklemiş olan müşteriler bu uygulamayı kullanmaya devam edebilir, ancak Kapasite ve Performans hiçbir yeni çalışma alanına eklenemez.

Hyper-V sunucularınızın kapasitesini anlamanıza yardımcı olması için Izleyici 'deki Kapasite ve Performans çözümünü kullanabilirsiniz. Bu çözüm, ana bilgisayarların genel kullanımını (CPU, bellek ve disk) ve bu Hyper-V konaklarında çalışan VM 'Leri göstererek, Hyper-V ortamınız hakkında öngörüler sağlar. Ölçümler, tüm konaklarınızdaki ve üzerinde çalışan VM 'lerde CPU, bellek ve diskler için toplanır.

Çözüm:

-   En yüksek ve en düşük CPU ve bellek kullanımı olan Konakları gösterir
-   En yüksek ve en düşük CPU ve bellek kullanımı olan VM 'Leri gösterir
-   En yüksek ve en düşük ıOPS ve aktarım hızı kullanımı olan VM 'Leri gösterir
-   Hangi sanal makinelerin çalıştığını gösterir
-   Küme Paylaşılan birimlerinde yüksek aktarım hızı, ıOPS ve gecikme süresine sahip en üstteki diskleri gösterir
- Grupları temel alarak özelleştirmenizi ve filtrelemenizi sağlar

> [!NOTE]
> Kapasite ve Performans çözümünün kapasite yönetimi olarak adlandırılan önceki sürümü hem System Center Operations Manager hem de System Center Virtual Machine Manager gerektirir. Bu güncelleştirilmiş çözümde bu bağımlılıklar yok.


## <a name="connected-sources"></a>Bağlı kaynaklar

Aşağıdaki tabloda bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır.

| Bağlı Kaynak | Destek | Açıklama |
|---|---|---|
| [Windows aracıları](../../azure-monitor/platform/agent-windows.md) | Yes | Bu çözüm, Windows aracılarından kapasite ve performans veri bilgilerini toplar. |
| [Linux aracıları](../../azure-monitor/learn/quick-collect-linux-computer.md) | Hayır    | Çözüm, doğrudan Linux aracılarından kapasite ve performans veri bilgilerini toplamaz.|
| [SCOM yönetim grubu](../../azure-monitor/platform/om-agents.md) | Yes |Çözüm, bağlı bir SCOM yönetim grubundaki aracılardan kapasite ve performans verilerini toplar. SCOM aracısından Log Analytics 'e doğrudan bir bağlantı gerekli değildir.|
| [Azure depolama hesabı](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Hayır | Azure depolama, kapasite ve performans verilerini içermez.|

## <a name="prerequisites"></a>Önkoşullar

- Windows veya Operations Manager aracıları, sanal makinelere değil, Windows Server 2012 veya daha yüksek bir Hyper-V konaklarında yüklü olmalıdır.


## <a name="configuration"></a>Yapılandırma

Kapasite ve Performans çözümünü çalışma alanınıza eklemek için aşağıdaki adımı gerçekleştirin.

- [Çözüm Galerisi Log Analytics çözümleri ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan işlemi kullanarak Log Analytics çalışma alanınıza kapasite ve performans çözümünü ekleyin.

## <a name="management-packs"></a>Yönetim paketleri

SCOM yönetim grubunuz Log Analytics çalışma alanınıza bağlıysa, bu çözümü eklediğinizde aşağıdaki yönetim paketleri SCOM 'ye yüklenir. Bu yönetim paketleri için bir yapılandırma veya bakım gerekmez.

- Microsoft. ıntelligencepacks. Capacityperformansı

1201 olayı şuna benzer:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Kapasite ve Performans çözüm güncelleştirildiği zaman, sürüm numarası değişir.

Çözüm yönetim paketlerini güncelleştirme hakkında daha fazla bilgi için bkz. [Operations Manager'ı Log Analytics’e Bağlama](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Çözümü kullanma

Kapasite ve Performans çözümünü çalışma alanınıza eklediğinizde, Kapasite ve Performans genel bakış panosuna eklenir. Bu kutucuk, şu anda etkin olan Hyper-V konaklarının sayısını ve seçilen dönem için izlenen etkin sanal makine sayısını görüntüler.

![Kapasite ve Performans kutucuğu](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Kullanımı gözden geçir

Kapasite ve Performans panosunu açmak için Kapasite ve Performans kutucuğuna tıklayın. Pano aşağıdaki tabloda gösterilen sütunları içerir. Her sütun, sütunun belirtilen kapsam ve zaman aralığına yönelik kriterleriyle eşleşen en fazla on öğe listeler. Sütunun altındaki **Tümünü gör**’e tıklayarak veya sütun başlığına tıklayarak tüm kayıtları döndüren bir günlük araması gerçekleştirebilirsiniz.

- **Bilgisayarlarınızı**
    - **Ana BILGISAYAR CPU kullanımı** Seçilen döneme bağlı olarak konak bilgisayarların CPU kullanımının ve konaklar listesinin grafik eğilimini gösterir. Belirli bir zaman noktasına ilişkin ayrıntıları görüntülemek için çizgi grafiğinin üzerine gelin. Günlük aramasında daha fazla ayrıntı görüntülemek için grafiğe tıklayın. Konak aramasını açmak ve barındırılan VM 'Ler için CPU sayacı ayrıntılarını görüntülemek için herhangi bir ana bilgisayar adına tıklayın.
    - **Ana bilgisayar bellek kullanımı** Seçili dönem temelinde konak bilgisayarların bellek kullanımının ve bir konak listesinin grafik eğilimini gösterir. Belirli bir zaman noktasına ilişkin ayrıntıları görüntülemek için çizgi grafiğinin üzerine gelin. Günlük aramasında daha fazla ayrıntı görüntülemek için grafiğe tıklayın. Konak aramasını açmak ve barındırılan VM 'Ler için bellek sayacı ayrıntılarını görüntülemek için herhangi bir ana bilgisayar adına tıklayın.
- **Sanal Makineler**
    - **VM CPU kullanımı** Seçilen döneme bağlı olarak, sanal makinelerin CPU kullanımının ve sanal makinelerin listesinin grafik eğilimini gösterir. İlk 3 VM için belirli bir zaman noktasına ilişkin ayrıntıları görüntülemek için çizgi grafiğinin üzerine gelin. Günlük aramasında daha fazla ayrıntı görüntülemek için grafiğe tıklayın. Günlük aramasını açmak ve VM 'nin toplu CPU sayacı ayrıntılarını görüntülemek için herhangi bir VM adına tıklayın.
    - **VM bellek kullanımı** Seçilen döneme bağlı olarak, sanal makinelerin bellek kullanımının ve sanal makinelerin listesinin grafik eğilimini gösterir. İlk 3 VM için belirli bir zaman noktasına ilişkin ayrıntıları görüntülemek için çizgi grafiğinin üzerine gelin. Günlük aramasında daha fazla ayrıntı görüntülemek için grafiğe tıklayın. Günlük aramasını açmak ve VM için toplanan bellek sayacı ayrıntılarını görüntülemek için herhangi bir VM adına tıklayın.
    - **VM toplam DISK IOPS** Seçilen dönem temelinde, sanal makineler için toplam disk ıOPS ve her biri için ıOPS olan sanal makinelerin bir listesini gösterir. İlk 3 VM için belirli bir zaman noktasına ilişkin ayrıntıları görüntülemek için çizgi grafiğinin üzerine gelin. Günlük aramasında daha fazla ayrıntı görüntülemek için grafiğe tıklayın. Günlük aramasını açmak ve VM için toplam disk ıOPS sayacı ayrıntılarını görüntülemek için herhangi bir VM adına tıklayın.
    - **VM toplam disk işleme** Seçilen dönem temelinde, sanal makineler için toplam disk aktarım hızı ve her biri için toplam disk işleme olan sanal makinelerin bir listesini gösteren grafik eğilimi gösterir. İlk 3 VM için belirli bir zaman noktasına ilişkin ayrıntıları görüntülemek için çizgi grafiğinin üzerine gelin. Günlük aramasında daha fazla ayrıntı görüntülemek için grafiğe tıklayın. Günlük aramasını açmak ve VM 'nin toplam disk işleme sayacı ayrıntılarını görüntülemek için herhangi bir VM adına tıklayın.
- **Kümelenmiş paylaşılan birimler**
    - **Toplam Verimlilik** Kümelenmiş paylaşılan birimlerde hem okuma hem de yazma toplamlarını gösterir.
    - **Toplam IOPS** Kümelenmiş paylaşılan birimlerde saniye başına giriş/çıkış işlemlerinin toplamını gösterir.
    - **Toplam gecikme süresi** Kümelenmiş paylaşılan birimlerde toplam gecikme süresini gösterir.
- **Ana bilgisayar yoğunluğu** En üstteki kutucuk, çözümün kullanabildiği toplam ana bilgisayar ve sanal makine sayısını gösterir. Günlük aramasında ek ayrıntılar görüntülemek için üstteki kutucuğa tıklayın. Ayrıca, tüm konakları ve barındırılan sanal makine sayısını listeler. Bir günlük aramasında VM sonuçlarının detayına gitmek için bir konağa tıklayın.


![Pano ana bilgisayarları dikey penceresi](./media/capacity-performance/dashboard-hosts.png)

![Pano sanal makineler dikey penceresi](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Performansı değerlendirme

Üretim hesaplama ortamları, bir kuruluştan diğerine büyük ölçüde farklılık gösterir. Ayrıca kapasite ve performans iş yükleri, sanal makinelerinizin nasıl çalıştığı ve normal olarak ne düşünbileceğinize bağlı olarak değişebilir. Performansı ölçmenize yardımcı olacak belirli yordamlar, büyük olasılıkla ortamınıza uygulanmaz. Bu nedenle, yardım için daha Genelleştirilmiş öngörülü rehberlik daha uygundur. Microsoft, performansı ölçmenize yardımcı olmak için çeşitli öngörülü rehberlik makaleleri yayımlar.

Özetlemek gerekirse, çözüm, performans sayaçları dahil çeşitli kaynaklardan kapasite ve performans verilerini toplar. Çözümdeki çeşitli yüzeylerde sunulan kapasite ve performans verilerini kullanın ve sonuçları [Hyper-V makalesindeki ölçüdekilerle](https://msdn.microsoft.com/library/cc768535.aspx) karşılaştırın. Makale bir süre önce yayımlansa da ölçümler, önemli noktalar ve yönergeler hala geçerlidir. Makale, diğer yararlı kaynakların bağlantılarını içerir.


## <a name="sample-log-searches"></a>Örnek günlük aramaları

Aşağıdaki tabloda, bu çözüm tarafından toplanan ve hesaplanan kapasite ve performans verilerine yönelik örnek günlük aramaları sunulmaktadır.


| Sorgu | Açıklama |
|:--- |:--- |
| Tüm konak belleği yapılandırması | &#124; NesneAdı, ObjectName = = "kapasite ve performans" ve CounterName = = "ana bilgisayar ATANMıŞ bellek MB &#124; " özetleme MB = Ort (CounterValue) |
| Tüm VM belleği yapılandırması | &#124; ObjectName = = "kapasite ve performans" ve CounterName = = "VM atanan bellek MB 'si" &#124; , InstanceName 'e göre MB = AVG (CounterValue). |
| Tüm VM 'lerde toplam disk ıOPS dökümü | &#124; {1 & gt; ObjectName = = "kapasite ve performans" ve (CounterName = = "VHD okumaları/s" ya da CounterName = = "VHD yazmaları &#124; /s"), bin (TimeGenerated, 1h), CounterName, InstanceName için Aggreg, value = Ort (CounterValue) özetleme |
| Tüm VM 'lerde toplam disk aktarım hızı dökümü | { &#124; 1 & gt; ObjectName = = "kapasite ve performans" ve (CounterName = = "VHD Read MB/s" ya da CounterName = = "VHD yazma MB &#124; /s") performansı, bin (TimeGenerated, 1h), CounterName, InstanceName için Aggreg |
| Tüm CSV 'lerde toplam ıOPS dökümü | ObjectName &#124; = = "kapasite ve performans" ve (CounterName = = "CSV okumaları/s" ya da CounterName = = "CSV yazmaları/s") &#124; , konum (TimeGenerated, 1h), CounterName, InstanceName için Aggreg, değer = AVG (CounterValue) özetleme |
| Tüm CSV 'lerde toplam Işleme dökümü | ObjectName &#124; = = "kapasite ve performans" ve (CounterName = = "CSV okumaları/s" ya da CounterName = = "CSV yazmaları/s") &#124; , konum (TimeGenerated, 1h), CounterName, InstanceName için Aggreg, değer = AVG (CounterValue) özetleme |
| Tüm CSV 'lerde toplam gecikme süresinin dökümünü | { &#124; 1 & gt; ObjectName = = "kapasite ve performans" ve (CounterName = = "CSV okuma gecikmesi" ya da CounterName = = "CSV &#124; yazma gecikme süresi"), bin (TimeGenerated, 1h), CounterName, InstanceName öğesine göre Aggreg, value = Ort (CounterValue) özetleme |


## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı Kapasite ve Performans verileri görüntülemek için [Log Analytics 'de günlük aramalarını](../../azure-monitor/log-query/log-query-overview.md) kullanın.
