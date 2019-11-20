---
title: Azure Izleyici 'de çözüm DNS Analizi | Microsoft Docs
description: Güvenlik, performans ve işlemlerde DNS altyapısına Öngörüler toplamak için Azure Izleyici 'de DNS Analizi çözümünü ayarlayın ve kullanın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/20/2018
ms.openlocfilehash: a4123ed4633cbb8195639766e7b23e3ea9f14e71
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899101"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>DNS Analizi Preview çözümüyle DNS altyapınız hakkında Öngörüler toplayın

![DNS Analizi simgesi](./media/dns-analytics/dns-analytics-symbol.png)

Bu makalede, güvenlik, performans ve işlemlerde DNS altyapısına Öngörüler toplamak için Azure Izleyici 'de Azure DNS Analytics çözümünün nasıl ayarlanacağı ve kullanılacağı açıklanmaktadır.

DNS Analizi şunları yapmanıza yardımcı olur:

- Kötü amaçlı etki alanı adlarını çözümlemeye çalışır olan istemcileri belirler.
- Eski kaynak kayıtlarını tanımla.
- Sık sorgulanan etki alanı adlarını ve DNS istemcilerini belirler.
- DNS sunucularındaki istek yükünü görüntüleyin.
- Dinamik DNS kaydı başarısızlıklarını görüntüleyin.

Bu çözüm, Windows DNS analitik ve denetim günlüklerini ve DNS sunucularınızdaki diğer ilgili verileri toplar, analiz eder ve ilişkilendirir.

## <a name="connected-sources"></a>Bağlı kaynaklar

Aşağıdaki tabloda, bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır:

| **Bağlı kaynak** | **Destek** | **Açıklama** |
| --- | --- | --- |
| [Windows aracıları](../platform/agent-windows.md) | Yes | Bu çözüm, Windows aracılarından DNS bilgilerini toplar. |
| [Linux aracıları](../learn/quick-collect-linux-computer.md) | Hayır | Çözüm, doğrudan Linux aracılarından DNS bilgileri toplamaz. |
| [System Center Operations Manager yönetim grubu](../platform/om-agents.md) | Yes | Çözüm, bağlı bir Operations Manager yönetim grubundaki aracılardan DNS bilgilerini toplar. Operations Manager aracısından Azure Izleyici 'ye doğrudan bağlantı gerekli değildir. Veriler, yönetim grubundan Log Analytics çalışma alanına iletilir. |
| [Azure depolama hesabı](../platform/collect-azure-metrics-logs.md) | Hayır | Azure depolama, çözüm tarafından kullanılmıyor. |

### <a name="data-collection-details"></a>Veri toplama ayrıntıları

Çözüm, bir Log Analytics aracısının yüklendiği DNS sunucularından DNS envanterini ve DNS olay ile ilgili verileri toplar. Bu veriler daha sonra Azure Izleyici 'ye yüklenir ve çözüm panosunda görüntülenir. DNS PowerShell cmdlet 'leri çalıştırılarak, DNS sunucularının, bölgelerin ve kaynak kayıtlarının sayısı gibi stokla ilgili veriler toplanır. Veriler her iki günde bir güncelleştirilir. Olayla ilgili veriler, Windows Server 2012 R2 'de Gelişmiş DNS günlüğü ve tanılama tarafından verilen [analitik ve denetim günlüklerinden](https://technet.microsoft.com/library/dn800669.aspx#enhanc) gerçek zamanlı olarak toplanır.

## <a name="configuration"></a>Yapılandırma

Çözümü yapılandırmak için aşağıdaki bilgileri kullanın:

- İzlemek istediğiniz her DNS sunucusunda bir [Windows](../platform/agent-windows.md) veya [Operations Manager](../platform/om-agents.md) aracısına sahip olmanız gerekir.
- DNS Analizi çözümünü [Azure Marketi](https://aka.ms/dnsanalyticsazuremarketplace)'nden Log Analytics çalışma alanınıza ekleyebilirsiniz. Ayrıca, [Çözüm Galerisi Azure izleyici çözümlerini ekleme](solutions.md)bölümünde açıklanan süreci de kullanabilirsiniz.

Çözüm, daha fazla yapılandırmaya gerek kalmadan veri toplamaya başlar. Ancak, veri toplamayı özelleştirmek için aşağıdaki yapılandırmayı kullanabilirsiniz.

### <a name="configure-the-solution"></a>Çözümü yapılandırma

Çözüm panosunda **yapılandırma** ' ya tıklayarak DNS Analizi yapılandırma sayfasını açın. Yapabileceğiniz iki yapılandırma değişikliği türü vardır:

- **Beyaz listelenmiş etki alanı adları**. Çözüm, tüm arama sorgularını işlemez. Etki alanı adı soneklerinin beyaz bir listesini tutar. Bu beyaz listedeki etki alanı adı sonekleri ile eşleşen etki alanı adlarına çözümlenerek arama sorguları çözüm tarafından işlenmez. Beyaz listeye alınan etki alanı adlarının işlenmemesi, Azure Izleyici 'ye gönderilen verileri iyileştirmenize yardımcı olur. Varsayılan beyaz liste, www.google.com ve www.facebook.com gibi popüler genel etki alanı adlarını içerir. Kaydırma yaparak, tüm varsayılan listeyi görüntüleyebilirsiniz.

  Arama öngörülerini görüntülemek istediğiniz herhangi bir etki alanı adı sonekini eklemek için listeyi değiştirebilirsiniz. Ayrıca, arama öngörülerini görüntülemek istemediğiniz tüm etki alanı adı sonekini da kaldırabilirsiniz.

- **Kative Istemci eşiği**. Arama istekleri sayısının eşiğini aşan DNS istemcileri **DNS istemcileri** dikey penceresinde vurgulanır. Varsayılan eşik 1.000 ' dir. Eşiği düzenleyebilirsiniz.

    ![Beyaz listelenmiş etki alanı adları](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Yönetim paketleri

Log Analytics çalışma alanınıza bağlanmak için Microsoft Monitoring Agent kullanıyorsanız, aşağıdaki yönetim paketi yüklenir:

- Microsoft DNS veri toplayıcı zekası paketi (Microsoft. ıntelligencepacks. DNS)

Operations Manager yönetim grubunuz Log Analytics çalışma alanınıza bağlıysa, bu çözümü eklediğinizde aşağıdaki yönetim paketleri Operations Manager yüklenir. Bu yönetim paketlerinin gerekli bir yapılandırması veya bakımı yoktur:

- Microsoft DNS veri toplayıcı zekası paketi (Microsoft. ıntelligencepacks. DNS)
- Microsoft System Center Advisor DNS Analizi yapılandırması (Microsoft. ıntelligencepack. DNS. Configuration)

Çözüm yönetim paketlerini güncelleştirme hakkında daha fazla bilgi için bkz. [Operations Manager'ı Log Analytics’e Bağlama](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>DNS Analizi çözümünü kullanma

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


DNS kutucuğu, verilerin toplandığı DNS sunucusu sayısını içerir. Ayrıca, son 24 saat içindeki kötü amaçlı etki alanlarını çözümlemek için istemciler tarafından yapılan isteklerin sayısını da içerir. Kutucuğa tıkladığınızda çözüm panosu açılır.

![DNS Analizi kutucuğu](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Çözüm panosu

Çözüm panosu, çözümün çeşitli özelliklerine ilişkin özetlenen bilgileri gösterir. Ayrıca, adli analiz ve Tanılama için ayrıntılı görünüme bağlantılar da içerir. Veriler, son yedi gün için varsayılan olarak gösterilir. Tarih ve saat aralığını, aşağıdaki görüntüde gösterildiği gibi **Tarih-Saat seçim denetimini**kullanarak değiştirebilirsiniz:

![Zaman seçim denetimi](./media/dns-analytics/dns-time.png)

Çözüm Panosu aşağıdaki dikey pencereleri gösterir:

**DNS güvenliği**. Kötü amaçlı etki alanlarıyla iletişim kurmaya çalışan DNS istemcilerini raporlar. DNS Analizi, Microsoft Threat Intelligence akışlarını kullanarak, kötü amaçlı etki alanlarına erişmeye çalışan istemci IP 'lerini algılayabilir. Çoğu durumda, kötü amaçlı yazılımdan etkilenen cihazlar kötü amaçlı yazılım etki alanı adını çözümleyerek kötü amaçlı etki alanının "komut ve denetim" merkezine "çevirme".

![DNS güvenliği dikey penceresi](./media/dns-analytics/dns-security-blade.png)

Listedeki bir istemci IP 'sini tıklattığınızda, günlük araması açılır ve ilgili sorgunun arama ayrıntılarını gösterir. Aşağıdaki örnekte, DNS Analizi bir [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot)ile iletişimin yapıldığını algıladı:

![IRCBot 'ı gösteren günlük arama sonuçları](./media/dns-analytics/ircbot.png)

Bilgiler şunları belirlemenize yardımcı olur:

- İletişimi başlatan istemci IP 'si.
- Kötü amaçlı IP 'ye çözümlenen etki alanı adı.
- Etki alanı adının çözümlediği IP adresleri.
- Kötü amaçlı IP adresi.
- Sorunun önem derecesi.
- Kötü amaçlı IP listeleme nedeni.
- Algılama süresi.

**Sorgulanan etki alanları**. , Ortamınızdaki DNS istemcileri tarafından sorgulanmakta olan en sık kullanılan etki alanı adlarını sağlar. Sorgulanan tüm etki alanı adlarının listesini görebilirsiniz. Ayrıca, günlük aramasında belirli bir etki alanı adının arama isteği ayrıntılarına gidebilirsiniz.

![Sorgulanan etki alanları dikey penceresi](./media/dns-analytics/domains-queried-blade.png)

**DNS istemcileri**. Seçilen dönemdeki sorgu sayısı için *eşiğe ulaşan* istemcileri bildirir. Tüm DNS istemcilerinin listesini ve bunların günlük aramasında yaptığı sorguların ayrıntılarını görebilirsiniz.

![DNS Istemcileri dikey penceresi](./media/dns-analytics/dns-clients-blade.png)

**Dınamık DNS kayıtları**. Rapor adı kayıt sorunları. Adres [kaynak kayıtları](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (tür A ve AAAA) için tüm kayıt hatalarının kayıt isteklerini gerçekleştiren Istemci IP 'leri ile birlikte vurgulanacaktır. Daha sonra bu bilgileri kullanarak kayıt hatasının kök nedenini bulmak için şu adımları izleyin:

1. İstemcinin güncelleştirmeye çalıştığı ad için yetkili olan bölgeyi bulun.

1. Bu bölgenin envanter bilgilerini denetlemek için çözümü kullanın.

1. Bölgenin dinamik güncelleştirmesinin etkinleştirildiğini doğrulayın.

1. Bölgenin güvenli dinamik güncelleştirme için yapılandırılıp yapılandırılmadığını denetleyin.

    ![Dinamik DNS kayıtları dikey penceresi](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Ad kayıt istekleri**. Üstteki kutucuk, başarılı ve başarısız DNS dinamik güncelleştirme istekleri için bir eğilim çizgisi gösterir. Alt kutucuk, DNS sunucularına başarısız DNS güncelleştirme istekleri gönderen ilk 10 istemciyi, hata sayısına göre sıralanmış olarak listeler.

![Ad kayıt istekleri dikey penceresi](./media/dns-analytics/name-reg-req-blade.png)

**Örnek DDI Analizi sorguları**. Ham analiz verilerini doğrudan getiren en yaygın arama sorgularının bir listesini içerir.


![Örnek sorgular](./media/dns-analytics/queries.png)

Bu sorguları, özelleştirilmiş raporlama için kendi sorgularınızı oluşturmak için başlangıç noktası olarak kullanabilirsiniz. Sorgular, sonuçların görüntülendiği DNS Analizi günlük arama sayfasına bağlanır:

- **DNS sunucularının listesi**. İlişkili FQDN, etki alanı adı, Orman adı ve sunucu IP 'Leri ile tüm DNS sunucularının listesini gösterir.
- **DNS bölgeleri listesi**. İlişkili bölge adı, dinamik güncelleştirme durumu, ad sunucuları ve DNSSEC imzalama durumuyla tüm DNS bölgelerinin listesini gösterir.
- **Kullanılmayan kaynak kayıtları**. Kullanılmayan/eski kaynak kayıtlarının listesini gösterir. Bu liste, kaynak kayıt adı, kaynak kayıt türü, ilişkili DNS sunucusu, kayıt oluşturma süresi ve bölge adını içerir. Artık kullanımda olmayan DNS kaynak kayıtlarını belirlemek için bu listeyi kullanabilirsiniz. Bu bilgilere bağlı olarak, bu girdileri DNS sunucularından kaldırabilirsiniz.
- **DNS sunucuları sorgu yükü**. DNS sunucularınızda DNS yükünün perspektifini alabilmeniz için bilgileri gösterir. Bu bilgiler sunucular için kapasiteyi planlamada size yardımcı olabilir. Görünümü grafik görselleştirmeye dönüştürmek için **ölçümler** sekmesine gidebilirsiniz. Bu görünüm, DNS yükünün DNS sunucularınız genelinde nasıl dağıtıldığını anlamanıza yardımcı olur. Her sunucu için DNS sorgu hız eğilimlerini gösterir.

    ![DNS sunucuları sorgu günlüğü arama sonuçları](./media/dns-analytics/dns-servers-query-load.png)

- **Sorgu yükü DNS bölgeleri**. Çözüm tarafından yönetilmekte olan DNS sunucularındaki tüm bölgelerin, DNS bölge-sorgu/saniye istatistiklerini gösterir. Ayrıntılı kayıtlardan görünümü sonuçların grafik görselleştirmesine değiştirmek için **ölçümler** sekmesine tıklayın.
- **Yapılandırma olayları**. Tüm DNS yapılandırma değişikliği olaylarını ve ilişkili iletileri gösterir. Daha sonra bu olayları olay, olay KIMLIĞI, DNS sunucusu veya görev kategorisi zamanına göre filtreleyebilirsiniz. Veriler belirli zamanlarda belirli DNS sunucularında yapılan değişiklikleri denetlemeye yardımcı olabilir.
- **DNS analitik günlüğü**. Çözüm tarafından yönetilen tüm DNS sunucularındaki tüm analitik olayları gösterir. Daha sonra bu olayları olay, olay KIMLIĞI, DNS sunucusu, arama sorgusunu yapan istemci IP ve sorgu türü görev kategorisi temelinde filtreleyebilirsiniz. DNS sunucusu analitik olayları, DNS sunucusunda etkinlik izlemeyi etkinleştirir. Sunucunun DNS bilgilerini göndermesi veya aldığı her seferinde bir analitik olay günlüğe kaydedilir.

### <a name="search-by-using-dns-analytics-log-search"></a>DNS Analizi günlük aramasını kullanarak ara

Günlük araması sayfasında, bir sorgu oluşturabilirsiniz. , Model denetimlerini kullanarak arama sonuçlarınızı filtreleyebilirsiniz. Sonuçlarınızı dönüştürmek, filtrelemek ve raporlamak için gelişmiş sorgular da oluşturabilirsiniz. Aşağıdaki sorguları kullanarak başlayın:

1. **Arama sorgusu kutusuna**, çözüm tarafından yönetilen DNS sunucuları tarafından oluşturulan tüm DNS olaylarını görüntülemek için `DnsEvents` yazın. Sonuçlar, arama sorguları, dinamik kayıtlar ve yapılandırma değişiklikleriyle ilgili tüm olaylar için günlük verilerini listeler.

    ![DnsEvents günlük araması](./media/dns-analytics/log-search-dnsevents.png)  

    a. Arama sorgularının günlük verilerini görüntülemek için, sol taraftaki model denetiminden **alt tür** filtresi olarak **lookupquery** ' yi seçin. Seçilen zaman dilimi için tüm arama sorgusu olaylarını listeleyen bir tablo görüntülenir.

    b. Dinamik kayıtlara ait günlük verilerini görüntülemek için, sol taraftaki model denetiminden **alt tür** filtresi olarak **dynamicregistration** ' u seçin. Seçilen zaman dilimi için tüm dinamik kayıt olaylarını listeleyen bir tablo görüntülenir.

    c. Yapılandırma değişikliklerinin günlük verilerini görüntülemek için, sol taraftaki model denetiminden **alt tür** filtresi olarak **ConfigurationChange** ' ı seçin. Seçilen zaman dilimi için tüm yapılandırma değişikliği olaylarını listeleyen bir tablo görüntülenir.

1. **Arama sorgusu kutusuna**, çözüm tarafından yönetilen DNS sunucuları IÇIN tüm DNS envanteriyle ilgili verileri görüntülemek üzere `DnsInventory` yazın. Sonuçlar, DNS sunucuları, DNS bölgeleri ve kaynak kayıtları için günlük verilerini listeler.

    ![Dnsınventory günlük araması](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Sorun giderme

Ortak sorun giderme adımları:

1. Eksik DNS arama verileri-bu sorunu gidermek Için yapılandırmayı sıfırlamayı veya portalda yalnızca yapılandırma sayfasını yüklemeyi deneyin. Sıfırlamak için, bir ayarı başka bir değerle değiştirmeniz, sonra yeniden özgün değere değiştirmeniz ve yapılandırmayı kaydetmeniz yeterlidir.

## <a name="feedback"></a>Geri Bildirim

Geri bildirim sağlamak için [Log Analytics UserVoice sayfasını](https://aka.ms/dnsanalyticsuservoice) ziyaret ederek DNS analizi özellikleri üzerinde çalışmak üzere fikirler gönderin. 

## <a name="next-steps"></a>Sonraki adımlar

Ayrıntılı DNS günlüğü kayıtlarını görüntülemek için [sorgu günlükleri](../log-query/log-query-overview.md) .
