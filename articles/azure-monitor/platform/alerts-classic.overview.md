---
title: Microsoft Azure ve Azure Izleyici 'de klasik uyarılara genel bakış
description: Klasik uyarılar kullanım dışı bırakılıyor. Uyarılar, Azure Kaynak ölçümlerini, olaylarını veya günlüklerini izlemenize ve belirttiğiniz bir koşula uyulduğunda bildirimde bulunulmasını sağlar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/19/2018
ms.openlocfilehash: 3ad0bdc2ec5db2f34a3bb6a1b8fda54cf9037483
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555688"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure 'da klasik uyarılar nelerdir?

> [!NOTE]
> Bu makalede, klasik eski ölçüm uyarılarının nasıl oluşturulacağı açıklanır. Azure Izleyici artık [daha yeni bir neredeyse gerçek zamanlı ölçüm uyarılarını ve yeni bir uyarı deneyimini](../../azure-monitor/platform/alerts-overview.md)desteklemektedir. Klasik uyarılar [kullanımdan kaldırılacak şekilde zamanlanır](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).  
>

Uyarılar, veriler üzerinde koşullar yapılandırmanıza ve koşullar en son izleme verileriyle eşleşiyorsa bildirimde bulunulmasına olanak tanır.

## <a name="old-and-new-alerting-capabilities"></a>Eski ve yeni uyarı özellikleri

Geçmiş Azure Izleyicisinde, Application Insights, Log Analytics ve hizmet durumu ayrı uyarı yeteneklerine sahipti. Fazla mesai, Azure tarafından geliştirildi ve hem Kullanıcı arabirimini hem de farklı uyarı yöntemlerini birleştirildi. Birleştirme işlemi hala devam ediyor.

Klasik uyarıları yalnızca Azure portalındaki klasik uyarılar Kullanıcı ekranında görüntüleyebilirsiniz. Bu ekranı, uyarılar ekranındaki **Klasik Uyarıları görüntüle** düğmesinden alırsınız. 

 ![Azure portal uyarı seçimleri](media/alerts-classic.overview/monitor-alert-screen2.png)

Yeni uyarılar kullanıcı deneyimi, klasik uyarılar deneyimine göre aşağıdaki avantajlara sahiptir:
-   **Daha iyi bildirim sistemi** -tüm yeni uyarılar, birden fazla uyarı içinde yeniden kullanılabilen bildirimler ve eylemler grupları adlı eylem gruplarını kullanır. Klasik ölçüm uyarıları ve daha eski Log Analytics Uyarıları eylem gruplarını kullanmaz.
-   **Birleştirilmiş bir yazma deneyimi** -Azure izleyici, Log Analytics ve Application Insights ölçümler, Günlükler ve etkinlik günlüğü için tüm uyarı oluşturma işlemi tek bir yerdir.
-   **Azure portal Log Analytics uyarı** tetiklenme, artık aboneliğinizde tetiklenen Log Analytics uyarıları da görebilirsiniz. Bunlar, daha önce ayrı bir portalda vardı.
-   **Tetiklenen uyarıların ve uyarı kurallarının ayrımı** -uyarı kuralları (bir uyarıyı tetikleyen koşulun tanımı) ve başlatılan uyarılar (uyarı kuralı tetiklemesini bir örnek) farklılaştırdığında işlemsel ve yapılandırma görünümleri ayrılır.
-   **Daha iyi iş akışı** -yeni uyarı yazma deneyimi, kullanıcıyı bir uyarı kuralı yapılandırma sürecinde rehberlik eder ve bu da uyarı almak için doğru şeyleri bulmayı kolaylaştırır.
-   **Akıllı uyarılar birleştirme** ve **uyarı durumunu ayarlama** -daha yeni uyarılar, Kullanıcı arabirimindeki aşırı yüklemeyi azaltmak için benzer uyarıları gösteren otomatik gruplandırma işlevlerini içerir. 

Daha yeni ölçüm uyarıları klasik ölçüm uyarıları üzerinde aşağıdaki avantajlara sahiptir:
-   **İyileştirilmiş gecikme**: daha yeni ölçüm uyarıları, her dakika daha sık çalıştırılabilir. Daha eski ölçüm uyarıları her zaman 5 dakikalık bir sıklıkta çalışır. Daha yeni uyarılar, sorun oluşma süresinden bildirim veya eyleme (3 ila 5 dakika) karşı daha küçük gecikmeyi artırır. Daha eski uyarılar, türe bağlı olarak 5 ila 15 dakika sürer.  Günlük uyarıları genellikle günlüklerin alınması için gereken süre nedeniyle 10 ila 15 dakikalık gecikme olur, ancak daha yeni işleme yöntemleri bu süreyi azaltmaktadır. 
-   **Çok boyutlu ölçümler Için destek**: ölçüm hakkında ilgi çekici bir segmenti izlemenize olanak tanıyan boyut ölçümlerini uyarabilir.
-   **Ölçüm koşulları üzerinde daha fazla denetim: daha**zengin uyarı kuralları tanımlayabilirsiniz. Yeni uyarılar, ölçümlerin maksimum, minimum, ortalama ve toplam değerlerini izlemeyi destekler.
-   **Birden çok ölçüm Için Birleşik izleme**: birden çok ölçümü (Şu anda en fazla iki ölçüm) tek bir kuralla izleyebilirsiniz. Her iki ölçüm de belirli bir süre boyunca ilgili eşiklerini ihlal ederseniz bir uyarı tetiklenir.
-   **Daha iyi bildirim sistemi**: tüm yeni uyarılar, birden fazla uyarı içinde yeniden kullanılabilen bildirimler ve eylemler grupları adlı [eylem gruplarını](../../azure-monitor/platform/action-groups.md)kullanır.  Klasik ölçüm uyarıları ve daha eski Log Analytics Uyarıları eylem gruplarını kullanmaz. 
-   **Günlüklerden alınan ölçümler** (Genel Önizleme): Log Analytics olan günlük verileri artık ayıklanıp Azure izleyici ölçümlerine dönüştürülüp diğer ölçümler gibi uyarı verebilir. Klasik uyarılara özgü terminoloji için bkz. [Uyarılar (klasik)](alerts-classic.overview.md) . 


## <a name="classic-alerts-on-azure-monitor-data"></a>Azure Izleyici verilerinde klasik uyarılar
İki tür klasik uyarı mevcuttur-ölçüm uyarıları ve etkinlik günlüğü uyarıları.

* **Klasik ölçüm uyarıları** -bu uyarı, belirtilen bir ölçümün değeri atadığınız bir eşiği aştığında tetiklenir. Uyarı, bu eşik geçildiğinde uyarı koşulu karşılandığında bir bildirim oluşturur. Bu noktada, uyarı "etkinleştirildi" olarak değerlendirilir. "Çözümlenirse" diğer bir bildirim oluşturur. Bu, eşiğin yeniden geçildiğinde ve koşulun artık karşılanmadığını belirten başka bir bildirim oluşturur.

* **Klasik etkinlik günlüğü uyarıları** -filtre ölçütlerinizle eşleşen bir etkinlik günlüğü olay girişi üzerinde tetiklenen bir akış günlüğü uyarısı. Bu uyarıların yalnızca bir durumu vardır, "etkinleştirildi". Uyarı altyapısı, filtre ölçütlerini yalnızca yeni bir olaya uygular. Eski girdileri bulmak için arama yapmaz. Bu uyarılar, yeni bir hizmet durumu olayı gerçekleştiğinde veya bir Kullanıcı ya da uygulamanın aboneliğinizde bir işlem gerçekleştirdiğinde (örneğin, "sanal makineyi Sil") size bildirimde bulunabilir.

Azure Izleyici aracılığıyla kullanılabilen tanılama günlük verileri için verileri Log Analytics (eski adıyla OMS) ile yönlendirin ve Log Analytics bir sorgu uyarısı kullanın. Log Analytics artık [Yeni uyarı yöntemini](../../azure-monitor/platform/alerts-overview.md) kullanmaktadır 

Aşağıdaki diyagramda, Azure Izleyici 'deki verilerin kaynakları özetlenmektedir ve kavramsal olarak bu verilerin nasıl uyarabilir.

![Açıklanan uyarılar](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Uyarı sınıflandırması (klasik)
Azure, klasik uyarıları ve bunların işlevlerini anlatmak için aşağıdaki terimleri kullanır:
* **Uyarı** -karşılandığında etkinleştirilen bir ölçüt (bir veya daha fazla kural veya koşul) tanımı.
* **Etkin** -klasik bir uyarı tarafından tanımlanan ölçüt karşılandığında durum.
* **Çözüldü** -klasik uyarı tarafından tanımlanan kriterler daha önce karşılandıktan sonra karşılanmadığında durum.
* **Bildirim** -klasik bir uyarının etkin hale gelmesine yönelik eylem.
* **Eylem** -bir bildirimin alıcısına gönderilen belirli bir çağrı (örneğin, bir adresi veya bir Web kancası URL 'sine gönderme). Bildirimler genellikle birden çok eylemi tetikleyebilir.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Nasıl yaparım? Azure Monitor klasik uyarısından bildirim almak mı istiyorsunuz?
Geçmişte, farklı hizmetlerden gelen Azure uyarıları kendi yerleşik bildirim yöntemlerini kullandı. 

Azure Izleyici, *eylem grupları*adlı yeniden kullanılabilir bir bildirim gruplandırması oluşturdu. Eylem grupları bir bildirim için bir alıcı kümesi belirtir. Eylem grubuna başvuran bir uyarının etkinleştirildiği her zaman, tüm alıcılar bu bildirimi alır. Eylem grupları birçok uyarı nesnesi arasında alıcı gruplandırmasını (örneğin, çağrı mühendisi listeniz) yeniden kullanmanıza olanak tanır. Eylem grupları, e-posta adreslerine, SMS numaralarına ve bir dizi diğer eyleme ek olarak bir Web kancası URL 'sine göndererek bildirimi destekler.  Daha fazla bilgi için bkz. [eylem grupları](../../azure-monitor/platform/action-groups.md). 

Eski klasik etkinlik günlüğü uyarıları eylem gruplarını kullanır.

Ancak, eski ölçüm uyarıları eylem gruplarını kullanmaz. Bunun yerine, aşağıdaki eylemleri yapılandırabilirsiniz: 
- Hizmet yöneticisine, ortak yöneticilere veya belirttiğiniz ek e-posta adreslerine e-posta bildirimleri gönderin.
- Ek otomasyon eylemleri başlatmanıza olanak sağlayan bir Web kancası çağırın.

Web kancaları Otomasyon ve düzeltme imkanı sunar, örneğin:
- Azure Otomasyonu Runbook
- Azure Işlevi
- Azure mantıksal uygulaması
- üçüncü taraf bir hizmet

## <a name="next-steps"></a>Sonraki adımlar
Uyarı kuralları ve bunları kullanarak yapılandırma hakkında bilgi alın:

* [Ölçümler](data-platform.md) hakkında daha fazla bilgi edinin
* [Azure Portal aracılığıyla klasik ölçüm uyarılarını](alerts-classic-portal.md) yapılandırma
* [Klasik ölçüm uyarılarını yapılandırma PowerShell](alerts-classic-portal.md)
* [Klasik ölçüm uyarılarını yapılandırma komut satırı arabirimi (CLI)](alerts-classic-portal.md)
* [Klasik ölçüm uyarılarını Yapılandırma Azure izleyici REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* [Etkinlik günlüğü](activity-logs-overview.md) hakkında daha fazla bilgi edinin
* [Etkinlik günlüğü uyarılarını Azure Portal aracılığıyla](activity-log-alerts.md) yapılandırma
* [Etkinlik günlüğü uyarılarını Kaynak Yöneticisi aracılığıyla](alerts-activity-log.md) yapılandırma
* [Etkinlik günlüğü uyarısı Web kancası şemasını](activity-log-alerts-webhook.md) gözden geçirme
* [Eylem grupları](action-groups.md) hakkında daha fazla bilgi edinin
* [Daha yeni uyarıları](alerts-metric.md) Yapılandır
