---
title: Azure Log Analytics Uyarı Yönetimi çözümü | Microsoft Docs
description: Log Analytics Uyarı Yönetimi çözümü ortamınızdaki tüm uyarıları çözümlemenize yardımcı olur.  Log Analytics içinde oluşturulan uyarıların birleştirimesinin yanı sıra, bağlantılı System Center Operations Manager yönetim gruplarından gelen uyarıları Log Analytics 'e aktarır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: fe484d8b5a06946b844acb5e506ec4dcc99ebc23
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932719"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Azure Log Analytics Uyarı Yönetimi çözümü

![Uyarı Yönetimi simgesi](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Izleyici, [System Center Operations Manager, Zabbix veya Nagios gibi izleme araçlarıyla](https://aka.ms/managing-alerts-other-monitoring-services)oluşturulanlar da dahil olmak üzere, [uyarılarınızı ölçeklendirmeye](https://aka.ms/azure-alerts-overview)yönelik gelişmiş özellikleri desteklemektedir.
>  


Uyarı Yönetimi çözümü, Log Analytics Deponuzdaki tüm uyarıları çözümlemenize yardımcı olur.  Bu uyarılar, [Log Analytics tarafından oluşturulan](../../azure-monitor/platform/alerts-overview.md) veya [Nagios ya da Zabbix 'ten içeri aktarılan](../../azure-monitor/learn/quick-collect-linux-computer.md)kaynaklar dahil olmak üzere çeşitli kaynaklardan gelmiş olabilir. Çözüm Ayrıca, [bağlı System Center Operations Manager yönetim gruplarından](../../azure-monitor/platform/om-agents.md)gelen uyarıları içeri aktarır.

## <a name="prerequisites"></a>Önkoşullar
Çözüm, Log Analytics deposundaki kayıtlarla birlikte **çalışarak, bu**kayıtları toplamak için hangi yapılandırmanın gerekli olduğunu yapmanız gerekir.

- Log Analytics uyarılar için, doğrudan depoda uyarı kayıtları oluşturmak üzere [Uyarı kuralları oluşturun](../../azure-monitor/platform/alerts-overview.md) .
- Nagios ve Zabbix uyarıları için, [Bu sunucuları](../../azure-monitor/learn/quick-collect-linux-computer.md) Log Analytics Uyarıları gönderecek şekilde yapılandırın.
- System Center Operations Manager uyarılar için [Operations Manager yönetim grubunuzu Log Analytics çalışma alanınıza bağlayın](../../azure-monitor/platform/om-agents.md).  System Center Operations Manager oluşturulan tüm uyarılar Log Analytics içine aktarılır.  

## <a name="configuration"></a>Yapılandırma
Çözüm [ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan işlemi kullanarak Log Analytics çalışma alanınıza uyarı yönetimi çözümünü ekleyin. Başka bir yapılandırma işlemi gerekmez.

## <a name="management-packs"></a>Yönetim paketleri
System Center Operations Manager yönetim grubunuz Log Analytics çalışma alanınıza bağlıysa, bu çözümü eklediğinizde aşağıdaki yönetim paketleri System Center Operations Manager yüklenir.  Gerekli yönetim paketlerinin yapılandırması veya bakımı yok.

* Microsoft System Center Advisor Uyarı Yönetimi (Microsoft. ıntelligencepacks. AlertManagement)

Çözüm yönetim paketlerini güncelleştirme hakkında daha fazla bilgi için bkz. [Operations Manager'ı Log Analytics’e Bağlama](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Veri toplama
### <a name="agents"></a>Aracılar
Aşağıdaki tabloda bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır.

| Bağlı Kaynak | Destek | Açıklama |
|:--- |:--- |:--- |
| [Windows aracıları](agent-windows.md) | Hayır |Doğrudan Windows aracıları uyarı oluşturmaz.  Log Analytics uyarılar, Windows aracılarından toplanan olaylar ve performans verilerinden oluşturulabilir. |
| [Linux aracıları](../../azure-monitor/learn/quick-collect-linux-computer.md) | Hayır |Doğrudan Linux aracıları uyarı oluşturmaz.  Log Analytics uyarılar, Linux aracılarından toplanan olaylar ve performans verilerinden oluşturulabilir.  Nagios ve Zabbix uyarıları, Linux Aracısı gerektiren sunuculardan toplanır. |
| [System Center Operations Manager yönetim grubu](../../azure-monitor/platform/om-agents.md) |Yes |Operations Manager aracılarında oluşturulan uyarılar yönetim grubuna teslim edilir ve sonra Log Analytics iletilir.<br><br>Operations Manager aracılarından Log Analytics doğrudan bir bağlantı gerekli değildir. Uyarı verileri yönetim grubundan Log Analytics deposuna iletilir. |


### <a name="collection-frequency"></a>Toplama sıklığı
- Uyarı kayıtları, depoda depolandıklarında çözüm için kullanılabilir.
- Uyarı verileri, her üç dakikada bir Log Analytics Operations Manager yönetim grubundan gönderilir.  

## <a name="using-the-solution"></a>Çözümü kullanma
Uyarı Yönetimi çözümünü Log Analytics çalışma alanınıza eklediğinizde **uyarı yönetimi** kutucuğu panonuza eklenir.  Bu kutucuk, son 24 saat içinde oluşturulan geçerli etkin uyarı sayısının sayısını ve grafik temsilini görüntüler.  Bu zaman aralığını değiştiremezsiniz.

![Uyarı Yönetimi kutucuğu](media/alert-management-solution/tile.png)

**Uyarı yönetimi** panosunu açmak için **uyarı yönetimi** kutucuğuna tıklayın.  Pano aşağıdaki tabloda gösterilen sütunları içerir.  Her sütunda, belirtilen kapsam ve zaman aralığı için sütunun ölçütlerine uyan ilk 10 uyarı, sayıma göre listelenir.  Listenin en altında bulunan **Tümünü görüntüle ' ye** tıklayarak veya sütun başlığına tıklayarak, tüm listeyi sağlayan bir günlük araması çalıştırabilirsiniz.

| Sütun | Açıklama |
|:--- |:--- |
| Kritik Uyarılar |Uyarı adına göre gruplandırılmış kritik önem derecesine sahip tüm uyarılar.  Bu uyarının tüm kayıtlarını döndüren bir günlük araması çalıştırmak için bir uyarı adına tıklayın. |
| Uyarı uyarıları |Uyarı adına göre gruplandırılmış bir uyarı önem derecesine sahip tüm uyarılar.  Bu uyarının tüm kayıtlarını döndüren bir günlük araması çalıştırmak için bir uyarı adına tıklayın. |
| Etkin System Center Operations Manager uyarıları |Uyarıyı oluşturan kaynağa *kapalı* olarak gruplandırılmış dışında bir durum ile Operations Manager toplanan tüm uyarılar. |
| Tüm etkin uyarılar |Uyarı adına göre gruplandırılan tüm önem derecesine sahip tüm uyarılar. Yalnızca *kapalı*dışında bir durum Operations Manager uyarıları içerir. |

Sağa kaydırırsanız panoda, uyarı verileri için [günlük araması](../../azure-monitor/log-query/log-query-overview.md) gerçekleştirmek üzere tıkladığınız birkaç ortak sorgu listelenir.

![Uyarı Yönetimi panosu](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics kayıtları
Uyarı Yönetimi çözümü, bir **Uyarı**türü ile tüm kayıtları analiz eder.  Nagios veya Zabbix öğesinden Log Analytics tarafından oluşturulan veya toplanan uyarılar doğrudan çözüm tarafından toplanmaz.

Çözüm, uyarıları System Center Operations Manager içeri aktarır ve her biri için bir **Uyarı** türü ve **Opsmanager**dir için karşılık gelen bir kayıt oluşturur.  Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:  

| Özellik | Açıklama |
|:--- |:--- |
| `Type` |*Daki* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Uyarının XML biçiminde oluşturulmasına neden olan veri öğesinin ayrıntıları. |
| `AlertDescription` |Uyarının ayrıntılı açıklaması. |
| `AlertId` |Uyarının GUID 'SI. |
| `AlertName` |Uyarının adı. |
| `AlertPriority` |Uyarının öncelik düzeyi. |
| `AlertSeverity` |Uyarının önem düzeyi. |
| `AlertState` |Uyarının en son çözümlenme durumu. |
| `LastModifiedBy` |Uyarıyı son değiştiren kullanıcının adı. |
| `ManagementGroupName` |Uyarının oluşturulduğu yönetim grubunun adı. |
| `RepeatCount` |Çözümlenmesinden bu yana aynı izlenen nesne için aynı uyarının üretilme sayısı. |
| `ResolvedBy` |Uyarıyı çözen kullanıcının adı. Uyarı henüz çözümlenmediyse boştur. |
| `SourceDisplayName` |Uyarıyı oluşturan izleme nesnesinin görünen adı. |
| `SourceFullName` |Uyarıyı oluşturan izleme nesnesinin tam adı. |
| `TicketId` |System Center Operations Manager ortamı, uyarılar için bilet atamaya yönelik bir işlemle tümleşikse, uyarının bilet KIMLIĞI.  Bilet KIMLIĞI atanmamış boş değer yok. |
| `TimeGenerated` |Uyarının oluşturulduğu tarih ve saat. |
| `TimeLastModified` |Uyarının en son değiştirildiği tarih ve saat. |
| `TimeRaised` |Uyarının oluşturulduğu tarih ve saat. |
| `TimeResolved` |Uyarının çözümlendiği tarih ve saat. Uyarı henüz çözümlenmediyse boştur. |

## <a name="sample-log-searches"></a>Örnek günlük aramaları
Aşağıdaki tabloda, bu çözüm tarafından toplanan uyarı kayıtları için örnek günlük aramaları sunulmaktadır: 

| Sorgu | Açıklama |
|:---|:---|
| Uyarı &#124; dir = = "OpsManager" ve Alertönem derecesi = = "Error" ve TimeRaised > önce (24h) |Son 24 saatte oluşturulan kritik uyarılar |
| Alertönem derecesi = = "Warning" ve TimeRaised > önce (24 h) olduğunda uyarır &#124; |Son 24 saat boyunca oluşan uyarı uyarıları |
| Dir &#124; = = "OpsManager" ve AlertState! = "Closed" ve TimeRaised > önce (24h) &#124; özetleme Count = Count () tarafından sourcedisplayname 'e göre uyarı |Son 24 saatte oluşturulan etkin uyarılara sahip kaynaklar |
| Dir &#124; = = "OpsManager" ve Alertönem derecesi = = "Error" ve timeraised > önce (24h) ve AlertState! = "Closed" olduğunda uyarır |Son 24 saat içinde hala etkin olan kritik uyarılar |
| Dir &#124; = = "OpsManager" ve TimeRaised > önce (24h) ve AlertState = = "Closed" olduğunda uyarır |Son 24 saatte oluşturulan ve şu anda kapatılan uyarılar |
| Dir &#124; = = "OpsManager" ve TimeRaised > önce (1D) &#124; özetleme Count = Count () ile alertönem derecesine sahip uyarı |Son 1 gün boyunca önem derecesine göre gruplandırılmış uyarılar |
| Dir &#124; = = "OpsManager" ve TimeRaised > önce (1D) &#124; repeatCount DESC olarak sıralama uyarısı |Son 1 gün içinde oluşturulan ve yineleme sayısı değeri tarafından sıralanan uyarılar |



## <a name="next-steps"></a>Sonraki adımlar
* Log Analytics’ten uyarı oluşturma hakkında daha ayrıntılı bilgi edinmek için bkz. [Log Analytics’teki Uyarılar](../../azure-monitor/platform/alerts-overview.md) .
