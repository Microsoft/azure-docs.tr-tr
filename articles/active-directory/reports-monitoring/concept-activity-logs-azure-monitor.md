---
title: Azure Izleyici 'de etkinlik günlüklerini Azure Active Directory | Microsoft Docs
description: Azure Izleyici 'de etkinlik günlüklerine Azure Active Directory giriş
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/22/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46e29fff3308f35b16dbff2f9cead82abc222a5c
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014512"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure Izleyici 'de Azure AD etkinlik günlükleri

Uzun süreli saklama ve veri öngörüleri için Azure Active Directory (Azure AD) etkinlik günlüklerini çeşitli uç noktalara yönlendirebilirsiniz. Bu özellik şunları yapmanıza olanak sağlar:

* Azure AD etkinlik günlüklerini, verileri uzun bir süre bekletmek için bir Azure depolama hesabına arşivleyin.
* Azure AD etkinlik günlüklerini, splunk ve QRadar gibi popüler güvenlik bilgilerini ve olay yönetimi (SıEM) araçlarını kullanarak analiz için bir Azure Olay Hub 'ına kaydeder.
* Azure AD etkinlik günlüklerini bir olay hub 'ına aktararak kendi özel günlük çözümlerinizle tümleştirin.
* Bağlantılı verilerde zengin görselleştirmeler, izleme ve uyarı verme özelliğini etkinleştirmek için Azure AD etkinlik günlüklerini Azure Izleyici günlüklerine gönderin.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Desteklenen raporlar

Azure AD denetim günlüklerini ve oturum açma günlüklerini bu özelliği kullanarak Azure depolama hesabınıza, Olay Hub 'ınıza, Azure Izleyici günlüklerine veya özel çözüme yönlendirebilirsiniz. 

* **Denetim günlükleri**: [Denetim günlükleri etkinlik raporu](concept-audit-logs.md), kiracınızda gerçekleştirilen her görevin geçmişine erişmenizi sağlar.
* **Oturum açma günlükleri**: [Oturum açma işlemleri etkinlik raporuyla](concept-sign-ins.md), denetim günlüklerinde bildirilen görevleri kimlerin gerçekleştirdiğini saptayabilirsiniz.

> [!NOTE]
> B2C ile ilgili denetim ve oturum açma işlemleri etkinlik günlükleri şu an için desteklenmemektedir.
>

## <a name="prerequisites"></a>Önkoşullar

Bu özelliği kullanmak için şunlara ihtiyacınız vardır:

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz denemeye kaydolabilirsiniz](https://azure.microsoft.com/free/).
* Azure portaldan Azure AD denetim günlüklerine erişmek için Azure AD Ücretsiz, Temel, Premium 1 veya Premium 2 [lisansı](https://azure.microsoft.com/pricing/details/active-directory/). 
* Azure AD kiracısı.
* Azure AD kiracısında **genel yönetici** veya **güvenlik yöneticisi** olan bir kullanıcı.
* Azure portaldan Azure AD oturum açma günlüklerine erişmek için Azure AD Premium 1 veya Premium 2 [lisansı](https://azure.microsoft.com/pricing/details/active-directory/). 

Denetim günlüğü verilerinizi yönlendirmek istediğiniz yere bağlı olarak aşağıdakilerden birine ihtiyacınız olacaktır:

* *ListKeys* izinlerine sahip olduğunuz bir Azure depolama hesabı. Blob depolama hesabı değil genel bir depolama hesabı kullanmanızı öneririz. Depolamayla fiyatlandırma bilgileri için bkz. [Azure Depolama fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Üçüncü taraf çözümlerle tümleştirmek için Azure Event Hubs ad alanı.
* Azure Izleyici günlüklerine Günlükler göndermek için bir Azure Log Analytics çalışma alanı.

## <a name="cost-considerations"></a>Maliyetle ilgili konular

Azure AD lisansınız varsa, depolama hesabı ve olay hub'ı kurulumu için bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliği ücretsizdir, ancak arşivleme için kullandığınız depolama hesabı ve akış için kullandığınız olay hub'ı gibi Azure kaynaklarını kullandığınızda ücret ödemeniz gerekir. Veri miktarı ve dolayısıyla alınacak ücret, kiracı boyutuna göre değişiklik gösterebilir. 

### <a name="storage-size-for-activity-logs"></a>Etkinlik günlükleri için depolama boyutu

Her denetim günlüğü olayı yaklaşık 2 KB veri depolama alanı kullanır. Oturum açma olay günlüklerinde yaklaşık 4 KB veri depolama alanı bulunur. 100.000 kullanıcıdan oluşan ve her gün yaklaşık 1,5 milyon olay gerçekleşecek bir kiracıda günlük yaklaşık 3 GB veri depolama alanına ihtiyaç duyulur. Yazma işlemleri yaklaşık beş dakikalık toplu işlemler halinde gerçekleştiğinden, ayda yaklaşık 9000 yazma işlemi olmasını bekleyebilirsiniz. 


Aşağıdaki tabloda, Batı ABD bölgesindeki bir genel amaçlı sürüm 2 depolama hesabında en az bir yıl saklama için kiracının boyutuna bağlı olarak yaklaşık bir maliyet hesabı verilmiştir. Uygulamanızın veri hacmine göre daha doğru bir yaklaşık değer elde etmek için [Azure depolama fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/details/storage/blobs/) kullanın.


| Günlük kategorisi | Kullanıcı sayısı | Günlük olay sayısı | Aylık veri hacmi (tahmini) | Aylık maliyet (tahmini) | Yıllık maliyet (tahmini) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Denetim | 100.000 | 1,5&nbsp;milyon | 90 GB | $1,93 | $23,12 |
| Denetim | 1000 | 15.000 | 900 MB | $0,02 | $0,24 |
| Oturum açma işlemleri | 1000 | 34.800 | 4 GB | $0,13 | $1,56 |
| Oturum açma işlemleri | 100.000 | 15&nbsp;milyon | 1,7 TB | $35,41 | $424,92 |
 









### <a name="event-hub-messages-for-activity-logs"></a>Etkinlik günlükleri için olay hub'ı iletileri

Olaylar yaklaşık beş dakikalık aralıklarla toplanır ve bu zaman aralığındaki tüm olayları içeren tek bir ileti halinde gönderilir. Olay hub'ındaki bir ileti en fazla 256 KB boyutunda olur ve ilgili zaman aralığındaki tüm iletilerin toplam boyutunun bu düzeyi aşması halinde birden fazla ileti gönderilir. 

Örneğin 100.000’den fazla kullanıcısı olan büyük bir kiracıda saniyede yaklaşık 18 olay oluşur ve bu da beş dakikada bir 5400 olay yapar. Denetim günlükleri olay başına 2 KB boyutunda olduğundan toplam veri boyutu 10,8 MB olur. Bu nedenle bu beş dakikalık süre için 43 ileti gönderilir. 

Aşağıdaki tabloda Batı ABD bölgesinde yer alan temel bir olay hub'ı için olay verileri hacmine göre yaklaşık aylık maliyet hesabı gösterilmiştir. Uygulamanızın veri hacmine göre daha doğru bir yaklaşık değer hesaplamak için [Olay hub'ı fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/details/event-hubs/) kullanın.

| Günlük kategorisi | Kullanıcı sayısı | Saniye başına olay sayısı | Beş dakikalık aralık başına olay sayısı | Aralık başına boyut | Aralık başına ileti sayısı | Aylık ileti sayısı | Aylık maliyet (tahmini) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Denetim | 100.000 | 18 | 5400 | 10,8 MB | 43 | 371.520 | $10,83 |
| Denetim | 1000 | 0.1 | 52 | 104 KB | 1 | 8640 | $10,80 |
| Oturum açma işlemleri | 1000 | 178 | 53.400 | 106,8&nbsp;MB | 418 | 3\.611.520 | $11,06 |  

### <a name="azure-monitor-logs-cost-considerations"></a>Azure Izleyici günlükleri maliyet konuları



| Günlük kategorisi       | Kullanıcı sayısı | Günlük olay sayısı | Aylık olay sayısı (30 gün) | ABD Doları (EST) cinsinden aylık maliyet |
| :--                | ---             | ---            | ---                        | --:                          |
| Denetim ve oturum açma işlemleri | 100.000         | 16.500.000     | 495.000.000                |  $1093,00                       |
| Denetim              | 100.000         | 1\.500.000      | 45,000,000                 |  $246,66                     |
| Oturum açma işlemleri           | 100.000         | 15,000,000     | 450.000.000                |  $847,28                     |










Azure Izleyici günlüklerini yönetme ile ilgili maliyetleri gözden geçirmek için bkz. [Azure izleyici günlüklerinde veri hacmi ve bekletme denetimi yaparak maliyeti yönetme](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

Bu bölümde, Azure İzleyici'deki Azure AD günlükleriyle ilgili sık sorulan soruların yanıtları ve bilinen sorunlar yer almaktadır.

**S: Bu özelliğe hangi günlükler dahildir?**

**Y**: Hem oturum açma etkinliği hem de denetim günlükleri bu özellik üzerinden yönlendirilebilir ancak B2C ile ilgili denetim olayları şu an için dahil değildir. Desteklenen günlük türlerini ve özellik tabanlı günlükleri öğrenmek için [Denetim günlüğü şemasını](reference-azure-monitor-audit-log-schema.md) ve [Oturum açma günlüğü şemasını](reference-azure-monitor-sign-ins-log-schema.md) inceleyin. 

---

**S: bir eylem ne kadar kısa bir süre sonra Olay Hub 'imde ilgili Günlükler görünür?**

**A**: Günlüklerin eylem gerçekleştirildikten sonra iki ila beş dakika içinde olay hub'ınızda gösterilmesi gerekir. Event Hubs hakkında daha fazla bilgi için bkz. [Azure Event Hubs nedir?](../../event-hubs/event-hubs-about.md)

---

**S: bir eylem ne kadar kısa bir süre sonra, depolama hesabmda ilgili Günlükler ne kadar görünür?**

**Y**: Azure depolama hesapları için gecikme süresi eylemin gerçekleştirilmesinden itibaren 5 ile 15 dakika arasındadır.

---

**S: bir yönetici bir tanılama ayarının bekletme süresini değiştirirse ne olur?**

Y **: yeni**bekletme ilkesi, değişiklikten sonra toplanan günlüklere uygulanır. İlke değişikliğinden önce toplanan Günlükler etkilenmeyecektir.

---

**S: Verilerimin depolama maliyeti ne kadar olacaktır?**

**Y**: Depolama maliyeti, günlüklerinizin boyutuna ve seçtiğiniz saklama süresine göre değişir. Kiracılarınızda üretilen günlük hacmine dayalı maliyet tahmini için bkz. [Etkinlik günlükleri için depolama boyutu](#storage-size-for-activity-logs).

---

**S: Verilerimin akışını olay hub'ına yapmanın maliyeti nedir?**

**Y**: Akış maliyeti, dakika başına aldığınız ileti sayısına göre değişir. Bu makalede maliyetlerin nasıl hesaplandığı gösterilmekte ve ileti sayısına dayalı maliyet tahminleri listelenmektedir. 

---

**S: Azure AD etkinlik günlüklerini SIEM sistemimle nasıl tümleştirebilirim?**

**Y**: Bunu iki yoldan birini kullanarak yapabilirsiniz:

- Azure İzleyici ile Event Hubs'ı birlikte kullanarak günlüklerinizin akışını SIEM sisteminize yapabilirsiniz. İlk olarak [günlüklerin akışını olay hub'ına yapın](tutorial-azure-monitor-stream-logs-to-event-hub.md) ve ardından yapılandırılan olay hub'ını kullanarak [SIEM aracınızı ayarlayın](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub). 

- [Raporlama Graph API’sini](concept-reporting-api.md) kullanarak verilere erişebilir ve kendi betiklerinizi kullanarak verileri SIEM sistemine gönderebilirsiniz.

---

**S: Hangi SIEM araçları desteklenmektedir?** 

**Y**: Azure İzleyici şu anda [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar ve [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory) tarafından desteklenmektedir. Bağlayıcıların çalışma şekli hakkında daha fazla bilgi için bkz. [Azure izleme verilerini bir dış araç tarafından kullanılmak üzere bir olay hub'ına aktarma](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

---

**S: Azure AD etkinlik günlüklerini Splunk örneğimle nasıl tümleştirebilirim?**

**Y**: Öncelikle [Azure AD etkinlik günlüklerini bir olay hub'ına yönlendirip](quickstart-azure-monitor-stream-logs-to-event-hub.md) ardından [Etkinlik günlüklerini Splunk ile tümleştirme](tutorial-integrate-activity-logs-with-splunk.md) adımlarını izleyin.

---

**S: Azure AD etkinlik günlüklerini Sumo Logic ile nasıl tümleştirebilirim?** 

**Y**: Öncelikle [Azure AD etkinlik günlüklerini bir olay hub'ına yönlendirip](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory) ardından [Azure AD uygulamasını yükleme ve panoları SumoLogic'te görüntüleme](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) adımlarını izleyin.

---

**S: Olay hub'ı verilerine harici bir SIEM aracı kullanmadan erişebilir miyim?** 

**Y**: Evet. Günlüklere özel uygulamanızdan erişmek için [Event Hubs API](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)’sini kullanabilirsiniz. 

---


## <a name="next-steps"></a>Sonraki adımlar

* [Etkinlik günlüklerini depolama hesabında arşivleme](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Etkinlik günlüklerini olay hub'ına yönlendirme](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Etkinlik günlüklerini Azure Izleyici ile tümleştirme](howto-integrate-activity-logs-with-log-analytics.md)
