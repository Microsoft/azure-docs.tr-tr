---
title: Kurulum uyarıları ve bildirimleri (Azure portal)
description: Belirttiğiniz koşullar karşılandığında bildirimleri veya Otomasyonu tetikleyebilen SQL veritabanı uyarılarını oluşturmak için Azure portal kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: ec625f203e9282d070e6c1b3b3d712be7ab789cf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810376"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Azure portal kullanarak Azure SQL veritabanı ve veri ambarı için uyarı oluşturma

## <a name="overview"></a>Genel Bakış
Bu makalede, Azure portal kullanarak Azure SQL veritabanı ve veri ambarı uyarılarını ayarlama hakkında yönergeler verilmektedir. Uyarılar bir e-posta gönderebilir veya bazı bir ölçüm (örneğin, veritabanı boyutu veya CPU kullanımı) eşiğe ulaştığında bir Web kancası çağırabilir. Bu makale, uyarı dönemlerini ayarlamaya yönelik en iyi yöntemleri de sağlar.    

> [!IMPORTANT]
> Bu özellik, yönetilen örnekte henüz kullanılamıyor. Alternatif olarak, [dinamik yönetim görünümlerine](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)göre bazı ölçümler için e-posta uyarıları göndermek üzere SQL Agent 'ı kullanabilirsiniz.

Azure hizmetleriniz, veya üzerindeki olaylar için izleme ölçümlerini temel alarak bir uyarı alabilirsiniz.

* **Ölçüm değerleri** -belirtilen bir ölçümün değeri her iki yönde atadığınız bir eşiği aştığında, uyarı tetiklenir. Yani, hem koşul ilk karşılandığında hem de daha sonra bu koşul karşılanamadığında bu durum tetiklenir.    
* **Etkinlik günlüğü olayları** -bir uyarı *her* olay üzerinde veya yalnızca belirli bir sayıda olay meydana geldiğinde tetiklenebilir.

Bir uyarıyı, tetiklendiğinde aşağıdakileri yapmak için yapılandırabilirsiniz:

* hizmet yöneticisine ve ortak yöneticilere e-posta bildirimleri gönderme
* Belirttiğiniz ek e-postalara e-posta gönderin.
* Web kancası çağırma

Kullanarak uyarı kuralları hakkında bilgi alabilir ve bunları alabilirsiniz

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [komut satırı arabirimi (CLı)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Izleyici REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure portal bir ölçümde uyarı kuralı oluşturma
1. [Portalda](https://portal.azure.com/), izlemekte olduğunuz kaynağı bulun ve seçin.
2. Izleme bölümünün altında **Uyarılar (klasik)** öğesini seçin. Metin ve simge farklı kaynaklar için biraz farklılık gösterebilir.  
   
     ![İzleme](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **Yalnızca SQL DW**: **DWU kullanım** grafiğine tıklayın. **Klasik Uyarıları görüntüle** seçeneğini belirleyin

3. **Ölçüm uyarısı Ekle (klasik)** düğmesini seçin ve alanları girin.
   
    ![Uyarı Ekle](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. Uyarı kuralınızı **adlandırın** ve bildirim e-postalarında da gösterildiği bir **Açıklama**seçin.
5. İzlemek istediğiniz **ölçümü** seçin, ardından ölçüm Için bir **koşul** ve **eşik** değeri seçin. Ayrıca **, uyarı** tetikleyiciden önce ölçüm kuralının karşılanması gereken süreyi seçin. Örneğin, "PT5M" dönemini kullanırsanız ve uyarınız %80 ' ün üzerinde CPU 'YU alıyorsa, uyarı, **Ortalama** CPU, 5 dakika boyunca %80 üzerinde olduğunda tetiklenir. İlk tetikleyici oluştuktan sonra, ortalama CPU, 5 dakikadan fazla %80 altına çıktığında yeniden tetiklenir. CPU Ölçümü her 1 dakikada bir gerçekleşir. Desteklenen zaman pencereleri ve her uyarının kullandığı toplama türü için aşağıdaki tabloya başvurun-tüm uyarılar, ortalama değeri kullanmaz.   
6. Uyarı tetiklendiğinde yöneticilerin ve ortak yöneticilerin e-posta ile uyumlu olmasını istiyorsanız **e-posta sahiplerini denetleyin...**
7. Uyarı tetiklendiğinde ek e-postaların bildirim almasını istiyorsanız, bunları **ek yönetici e-postası** alanına ekleyin. Birden çok e-postayı noktalı virgülle ayırın *\@contoso. com; email2\@contoso.com*
8. Uyarı tetiklendiğinde çağırılabilmesi istiyorsanız **Web kancası** alanına GEÇERLI bir URI koyun.
9. Uyarıyı oluşturmak için bittiğinde **Tamam ' ı** seçin.   

Birkaç dakika içinde, uyarı etkin ve daha önce açıklandığı gibi tetikler.

## <a name="managing-your-alerts"></a>Uyarılarınızı yönetme
Bir uyarı oluşturduktan sonra, bunu seçebilirsiniz:

* Ölçüm eşiğini ve önceki günün gerçek değerlerini gösteren bir grafik görüntüleyin.
* Düzenleyin veya silin.
* Bu uyarı için bildirimleri almayı geçici olarak durdurmak veya yeniden başlatmak istiyorsanız, **devre dışı bırakın** veya **etkinleştirin** .


## <a name="sql-database-alert-values"></a>SQL veritabanı uyarı değerleri

| Kaynak Türü | Ölçüm adı | Kolay ad | Toplama türü | En düşük uyarı süresi penceresi|
| --- | --- | --- | --- | --- |
| SQL veritabanı | cpu_percent | CPU yüzdesi | Ortalama | 5 dakika |
| SQL veritabanı | physical_data_read_percent | Veri G/Ç yüzdesi | Ortalama | 5 dakika |
| SQL veritabanı | log_write_percent | Günlük GÇ yüzdesi | Ortalama | 5 dakika |
| SQL veritabanı | dtu_consumption_percent | DTU yüzdesi | Ortalama | 5 dakika |
| SQL veritabanı | depolama | Toplam veritabanı boyutu | Maksimum | 30 dakika |
| SQL veritabanı | connection_successful | Başarılı bağlantılar | Toplam | 10 dakika |
| SQL veritabanı | connection_failed | Başarısız Bağlantılar | Toplam | 10 dakika |
| SQL veritabanı | blocked_by_firewall | Güvenlik duvarı tarafından engellendi | Toplam | 10 dakika |
| SQL veritabanı | Çözül | Çık | Toplam | 10 dakika |
| SQL veritabanı | storage_percent | Veri boyutu yüzdesi | Maksimum | 30 dakika |
| SQL veritabanı | xtp_storage_percent | Bellek içi OLTP depolama yüzdesi (Önizleme) | Ortalama | 5 dakika |
| SQL veritabanı | workers_percent | Çalışan yüzdesi | Ortalama | 5 dakika |
| SQL veritabanı | sessions_percent | Oturum yüzdesi | Ortalama | 5 dakika |
| SQL veritabanı | dtu_limit | DTU sınırı | Ortalama | 5 dakika |
| SQL veritabanı | dtu_used | Kullanılan DTU | Ortalama | 5 dakika |
||||||
| Elastik havuz | cpu_percent | CPU yüzdesi | Ortalama | 10 dakika |
| Elastik havuz | physical_data_read_percent | Veri G/Ç yüzdesi | Ortalama | 10 dakika |
| Elastik havuz | log_write_percent | Günlük GÇ yüzdesi | Ortalama | 10 dakika |
| Elastik havuz | dtu_consumption_percent | DTU yüzdesi | Ortalama | 10 dakika |
| Elastik havuz | storage_percent | Depolama yüzdesi | Ortalama | 10 dakika |
| Elastik havuz | workers_percent | Çalışan yüzdesi | Ortalama | 10 dakika |
| Elastik havuz | eDTU_limit | eDTU sınırı | Ortalama | 10 dakika |
| Elastik havuz | storage_limit | Depolama sınırı | Ortalama | 10 dakika |
| Elastik havuz | eDTU_used | eDTU kullanıldı | Ortalama | 10 dakika |
| Elastik havuz | storage_used | Kullanılan depolama | Ortalama | 10 dakika |
||||||               
| SQL veri ambarı | cpu_percent | CPU yüzdesi | Ortalama | 10 dakika |
| SQL veri ambarı | physical_data_read_percent | Veri G/Ç yüzdesi | Ortalama | 10 dakika |
| SQL veri ambarı | connection_successful | Başarılı bağlantılar | Toplam | 10 dakika |
| SQL veri ambarı | connection_failed | Başarısız Bağlantılar | Toplam | 10 dakika |
| SQL veri ambarı | blocked_by_firewall | Güvenlik duvarı tarafından engellendi | Toplam | 10 dakika |
| SQL veri ambarı | service_level_objective | Veritabanının hizmet katmanı | Toplam | 10 dakika |
| SQL veri ambarı | dwu_limit | DWU sınırı | Maksimum | 10 dakika |
| SQL veri ambarı | dwu_consumption_percent | DWU yüzdesi | Ortalama | 10 dakika |
| SQL veri ambarı | dwu_used | Kullanılan DWU | Ortalama | 10 dakika |
||||||


## <a name="next-steps"></a>Sonraki adımlar
* Toplayacağınız ve izleyebileceği bilgi türlerini içeren [Azure izlemeye genel bakış alın](../monitoring-and-diagnostics/monitoring-overview.md) .
* [Uyarılarda Web kancalarını yapılandırma](../azure-monitor/platform/alerts-webhooks.md)hakkında daha fazla bilgi edinin.
* [Tanılama günlüklerine genel bir bakış](../azure-monitor/platform/resource-logs-overview.md) alın ve hizmetinizde ayrıntılı yüksek frekanslı ölçümler toplayın.
* Hizmetinizin kullanılabilir olduğundan ve yanıt verebilmesini sağlamak için [ölçüm koleksiyonuna genel bakış](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) alın.
