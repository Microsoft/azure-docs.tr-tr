---
title: Azure Izleyici 'de tel veri çözümü | Microsoft Docs
description: Kablo verileri, Log Analytics aracıları olan bilgisayarlardan birleştirilmiş ağ ve performans verileri. Verilerin bağıntısını sağlamanıza yardımcı olmak için ağ verileri günlük verilerinizle birleştirilir.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732032"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Azure Izleyici 'de Wire Data 2.0 (Önizleme) çözümü (kullanımdan kaldırıldı)

![Wire Data sembolü](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>Tel veri çözümü, [sanal makine öngörüleri](../vm/vminsights-overview.md) ve [hizmet eşlemesi çözümüyle](../vm/service-map.md)değiştirilmiştir.  Her ikisi de Log Analytics Aracısı ve bağımlılık Aracısı 'nı kullanarak ağ bağlantısı verilerini Azure Izleyici 'ye toplayın.
>
>Tel veri çözümü desteği **31 mart 2022** tarihinde sona acaktır.  Kullanımdan kaldırma tarihine kadar, Wire Data 2.0 (Önizleme) çözümünü kullanan mevcut müşteriler bu uygulamayı kullanmaya devam edebilir.
>
>Yeni ve mevcut müşteriler [VM öngörülerini](../vm/vminsights-enable-overview.md) veya [hizmet eşlemesi çözümünü](../vm/service-map.md)yüklemelidir.  Topladıkları harita veri kümesi, Wire Data 2.0 (Önizleme) veri kümesiyle karşılaştırılabilir.  VM öngörüleri, analiz için ek performans verileri ve özellikleriyle birlikte Hizmet Eşlemesi veri kümesini içerir. Her iki teklifde [Azure Sentinel ile bağlantı](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options)vardır.
 

Çevre verileri, ortamınızdaki Operations Manager izlenenler de dahil olmak üzere, Windows bağlı ve Linux bağlantılı bilgisayarlardan Log Analytics aracısıyla toplanan birleştirilmiş ağ ve performans verileri. Verilerin bağıntısını sağlamanıza yardımcı olmak için ağ verileri diğer günlük verilerinizle birleştirilir.

Log Analytics aracısına ek olarak, kablo verileri çözümü BT altyapınızdaki bilgisayarlara yüklediğiniz Microsoft bağımlılık aracılarını kullanır. Bağımlılık Aracıları [OSI modelinde](https://en.wikipedia.org/wiki/OSI_model) 2-3 ağ düzeyleri için bilgisayarlarınıza ve bilgisayarlarınızdan gönderilen ağ verilerini, ayrıca kullanılan çeşitli protokollerle bağlantı noktalarını izler. Veriler daha sonra Aracılar kullanılarak Azure Izleyici 'ye gönderilir.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Azure Izleyici VM öngörülerine veya Hizmet Eşlemesi geçirme

Çoğu durumda, müşterilerin aynı VM 'lerde zaten etkinleştirilmiş Wire Data 2.0 (Önizleme) ve  [VM öngörülerine](../vm/vminsights-overview.md) veya [hizmet eşlemesi çözümüne](../vm/service-map.md) sahip olduğunu görüyoruz.  Bu, değişiklik teklifini VM 'niz üzerinde etkinleştirmiş olduğunuz anlamına gelir.  [Wire Data 2.0 (Önizleme) çözümünü Log Analytics çalışma alanınızdan kaldırmanız](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution)yeterlidir.

Üzerinde yalnızca Wire Data 2.0 (Önizleme) etkin olan sanal makineler varsa, [VM 'Leri VM öngörülerine](../vm/vminsights-enable-overview.md) veya [hizmet eşlemesi çözümüne](../vm/service-map.md) ekleyebilir ve ardından [Log Analytics çalışma alanınızdan Wire Data 2.0 (Önizleme) çözümünü kaldırabilirsiniz](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Azure Izleyici VM öngörülerine yönelik sorgularınızı VMConnection tablosuna geçirin

### <a name="agents-providing-data"></a>Veri sağlayan aracılar

#### <a name="wire-data-20-query"></a>Wire Data 2.0 sorgu

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>VM öngörüleri ve Hizmet Eşlemesi sorgusu

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>Veri sağlayan aracıların IP adresleri

#### <a name="wire-data-20-query"></a>Wire Data 2.0 sorgu

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM öngörüleri ve Hizmet Eşlemesi sorgusu

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Uzak IP adresine göre tüm giden iletişimler

#### <a name="wire-data-20-query"></a>Wire Data 2.0 sorgu

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM öngörüleri ve Hizmet Eşlemesi sorgusu

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Protokol adı tarafından alınan bayt sayısı

#### <a name="wire-data-20-query"></a>Wire Data 2.0 sorgu

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>VM öngörüleri ve Hizmet Eşlemesi sorgusu

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Işleme göre ağ trafiği miktarı (bayt)

#### <a name="wire-data-20-query"></a>Wire Data 2.0 sorgu

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>VM öngörüleri ve Hizmet Eşlemesi sorgusu

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Daha fazla örnek sorgulama

Ek örnek sorgular için [VM öngörüleri günlük arama belgelerine](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) ve [VM öngörüleri uyarı belgelerine](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) bakın.

## <a name="uninstall-wire-data-20-solution"></a>Wire Data 2.0 çözümünü kaldır

Wire Data 2.0 kaldırmak için, çözümü Log Analytics çalışma alanınızdan kaldırmanız yeterlidir.  Bunun sonucunda şunlar olur:

* Çalışma alanına bağlı VM 'lerden çıkarılan tel Veri Yönetimi Paketi 
* Tel veri veri türü artık çalışma alanınızda görünmüyor

Tel veri çözümünü kaldırmak için [Bu yönergeleri](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) izleyin.

>[!NOTE]
>Çalışma alanınızda Hizmet Eşlemesi veya VM öngörüleri çözümünüz varsa, bu çözümler bu yönetim paketini de kullanırken yönetim paketi kaldırılmaz.

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 yönetim paketleri

Log Analytics çalışma alanında Wire Data etkinleştirildiğinde, söz konusu çalışma alanındaki tüm Windows sunucularına 300 KB'lık bir yönetim paketi gönderilir. System Center Operations Manager aracılarını bir [bağlı yönetim grubunda](../agents/om-agents.md) kullanıyorsanız, System Center Operations Manager'dan Bağımlılık İzleyicisi yönetim paketi dağıtılır. Aracılar doğrudan bağlandıysa, Azure Izleyici yönetim paketini sunar.

Yönetim paketinin adı Microsoft.IntelligencePacks.ApplicationDependencyMonitor'dır. Şu konuma yazılır: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Yönetim paketi şu veri kaynağını kullanır: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>Bağımlılık aracısını kaldırma

>[!NOTE]
>Tel verileri Hizmet Eşlemesi veya VM öngörüleri ile değiştirmeyi planlıyorsanız, bağımlılık aracısını kaldırmamalıdır.

Bağımlılık aracısını kaldırmanıza yardımcı olması için aşağıdaki bölümleri kullanın.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows 'da bağımlılık aracısını kaldırma

Yönetici, Windows için bağımlılık aracısını Denetim Masası üzerinden kaldırabilir.

Yönetici, bağımlılık aracısını kaldırmak için%Programfiles%\Microsoft Dependency Agent\Uninstall.exe de çalıştırabilir.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux 'ta bağımlılık aracısını kaldırma

Bağımlılık aracısını Linux 'tan tamamen kaldırmak için aracının kendisini ve aracı ile otomatik olarak yüklenen bağlayıcıyı kaldırmanız gerekir. Aşağıdaki tek komutu kullanarak ikisini de kaldırabilirsiniz:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 çözümünü kullanma

Azure portalında Log Analytics çalışma alanınızın **Genel bakış** sayfasında **Wire Data 2.0** kutucuğuna tıklayarak Wire Data panosunu açın. Pano aşağıdaki tabloda gösterilen dikey pencereleri içerir. Her dikey pencerede, dikey pencerenin belirtilen kapsam ve zaman aralığına yönelik ölçütleriyle eşleşen en fazla 10 öğe listelenir. Dikey pencerenin altındaki **Tümünü göster**’e tıklayarak veya dikey pencere başlığına tıklayarak tüm kayıtları döndüren bir günlük araması yapabilirsiniz.

| **Dikey pencere** | **Açıklama** |
| --- | --- |
| Ağ trafiğini yakalayan aracılar | Ağ trafiğini yakalayan aracıların sayısını gösterir ve trafiği yakalayan ilk 10 bilgisayarı listeler. <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> günlük araması çalıştırmak için sayıya tıklayın. Yakalanan toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir bilgisayara tıklayın. |
| Yerel Alt Ağlar | Aracıların keşfettiği yerel alt ağların sayısını gösterir.  Tüm alt ağları ve her birinden gönderilen bayt sayısını listeleyen bir <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> günlük araması çalıştırmak için sayıya tıklayın. Alt ağ üzerinden gönderilen toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir alt ağa tıklayın. |
| Uygulama Düzeyi Protokolleri | Aracılar tarafından keşfedilen, kullanımdaki uygulama düzeyi protokollerinin sayısını gösterir. <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> günlük araması çalıştırmak için sayıya tıklayın. Protokol kullanılarak gönderilen toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir protokole tıklayın. |

![Wire Data panosu](./media/wire-data/wire-data-dash.png)

**Ağ trafiğini yakalayan aracılar** dikey penceresini kullanarak bilgisayarlar tarafından kullanılmakta olan ağ genişliğini miktarını belirleyebilirsiniz. Bu dikey pencere ortamınızdaki en _geveze_ bilgisayarı kolayca bulmanıza yardımcı olabilir. Bu tür bilgisayarlar aşırı yüklenmiş, anormal çalışıyor veya normalin üzerinde ağ kaynağı kullanıyor olabilir.

![Her bilgisayar tarafından kullanılan ağ bant genişliğini gösteren Wire Data 2.0 panosunda ağ trafiği yakalama dikey penceresindeki aracıların ekran görüntüsü.](./media/wire-data/log-search-example01.png)

Benzer biçimde, **Yerel Alt Ağlar** dikey penceresini kullanarak alt ağlarınız üzerinden ne kadar ağ trafiği taşındığını belirleyebilirsiniz. Kullanıcılar alt ağları çoğunlukla uygulamalarının kritik alanları çevresinde tanımlar. Bu dikey pencere söz konusu alanların görülmesini sağlar.

![Her bir LocalSubnet tarafından tüketilen ağ bant genişliğini gösteren Wire Data 2.0 panosundaki yerel alt ağlar dikey penceresinin ekran görüntüsü.](./media/wire-data/log-search-example02.png)

**Uygulama Düzeyi Protokolleri** hangi protokollerin kullanımda olduğunu öğrenmenize yardımcı olduğundan, yararlı bir dikey penceredir. Örneğin, ağ ortamınızda SSH'nin kullanımda olmamasını bekliyor olabilirsiniz. Dikey pencerede sağlanan bilgileri görüntüleyerek bu beklentinizin doğru olup olmadığını hızla anlayabilirsiniz.

![Wire Data 2.0 panosunda, her protokol tarafından tüketilen ağ bant genişliğini gösteren uygulama düzeyi protokolleri dikey penceresinin ekran görüntüsü.](./media/wire-data/log-search-example03.png)

Ayrıca protokol trafiğinin zaman içinde arttığını mı yoksa azaldığını mı bilmek de yararlı olur. Örneğin, bir uygulama tarafından iletilen verilerin miktarı artıyorsa, bu farkında olmanız gereken bir durum veya dikkate değer bulduğunuz bir bilgi olabilir.

## <a name="input-data"></a>Giriş verileri

Sinyal verileri, etkinleştirilmiş olan aracıları kullanarak ağ trafiği hakkındaki meta verileri toplar. Her aracı yaklaşık her 15 saniyede bir veri gönderir.

## <a name="output-data"></a>Çıktı verileri

Her giriş verileri türü için _WireData_ türünde bir kayıt oluşturulur. Aşağıdaki tabloda WireData kayıtlarının özellikleri gösterilmiştir:

| Özellik | Açıklama |
|---|---|
| Bilgisayar | Verilerin toplandığı bilgisayarın adı |
| TimeGenerated | Kaydın zamanı |
| LocalIP | Yerel bilgisayarın IP adresi |
| SessionState | Bağlantılı veya bağlantısı kesilmiş |
| ReceivedBytes | Alınan bayt miktarı |
| ProtocolName | Kullanılan ağ protokolünün adı |
| IPVersion | IP sürümü |
| Yön | Gelen veya giden |
| MaliciousIP | Bilinen kötü amaçlı kaynağın IP adresi |
| Önem derecesi | Kötü amaçlı olduğundan şüphe edilen yazılımın önem derecesi |
| RemoteIPCountry | Uzak IP adresinin ülkesi/bölgesi |
| ManagementGroupName | Operations Manager yönetim grubunun adı |
| SourceSystem | Verilerin toplandığı kaynak |
| SessionStartTime | Oturumun başlangıç saati |
| SessionEndTime | Oturumun bitiş saati |
| LocalSubnet | Verilerin toplandığı alt ağ |
| LocalPortNumber | Yerel bağlantı noktası numarası |
| RemoteIP | Uzak bilgisayar tarafından kullanılan uzak IP adresi |
| RemotePortNumber | Uzak IP adresi tarafından kullanılan bağlantı noktası numarası |
| SessionID | İki IP adresi arasındaki iletişim oturumunu tanımlayan benzersiz bir değer |
| SentBytes | Gönderilen bayt sayısı |
| TotalBytes | Oturum sırasında gönderilen toplam bayt sayısı |
| ApplicationProtocol | Kullanılan ağ protokolünün türü   |
| ProcessID | Windows işlem kimliği |
| ProcessName | İşlemin yıl ve dosya adı |
| RemoteIPLongitude | IP boylam değeri |
| RemoteIPLatitude | IP enlem değeri |

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. sanal makineleriniz için izlemeyi etkinleştiren gereksinimler ve yöntemler için [VM öngörülerini dağıtma](./vminsights-enable-overview.md) .
- Ayrıntılı sinyal verileri arama kayıtlarını görüntülemek için [günlüklerde arama yapın](../logs/log-query-overview.md).
