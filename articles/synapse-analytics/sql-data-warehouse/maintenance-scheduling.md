---
title: SYNAPSE SQL havuzu için bakım zamanlamaları
description: Bakım zamanlaması, müşterilerin Azure SYNAPSE Analytics 'in yeni özellikleri, yükseltmeleri ve düzeltme eklerini almak için kullandığı gerekli zamanlanmış bakım olaylarını planlayabilmesini sağlar.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 886e99d6f56a008c694b3bcb366cfb4049f3528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590209"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Bakım zamanlamalarını kullanarak hizmet güncelleştirmelerini ve bakımını yönetme

Bakım zamanlaması özelliği, Azure SYNAPSE Analytics içindeki SYNAPSE SQL Havuzu (veri ambarı) için hizmet durumu planlı bakım bildirimlerini, Kaynak Durumu denetim Izleyicisini ve bakım zamanlama hizmetini tümleştirir.

Yeni özellikleri, yükseltmeleri ve düzeltme eklerini almak için uygun olduğunda bir zaman penceresi seçmek üzere bakım zamanlaması 'nı kullanmanız gerekir. Yedi günlük bir dönemde birincil ve ikincil bakım penceresi seçmeniz gerekir, her pencere ayrı gün aralıklarında olmalıdır.

Örneğin, Cumartesi 22:00 ' nin birincil bir penceresini Pazar 01:00 ' e zamanlayabilir ve sonra da Çarşamba 19:00 ikincil penceresini 22:00 olarak zamanlayabilirsiniz. Birincil bakım pencerenizde bakım gerçekleştirilemiyorsa, ikincil bakım pencereniz sırasında Bakımı yeniden dener. Hizmet bakımı, birincil ve ikincil pencereler sırasında bazen meydana gelebilir. Tüm bakım işlemlerinin hızlı bir şekilde tamamlanmasını sağlamak için, DW400c ve daha düşük veri ambarı katmanları, belirtilen bakım penceresi dışında bakım işlemini tamamlayabilir.

Yeni oluşturulan tüm veri ambarı örneklerinin, dağıtım sırasında uygulanan sistem tanımlı bir bakım zamanlaması olacaktır. Zamanlama, dağıtım tamamlandıktan hemen sonra düzenlenebilir.

Bakım penceresi üç ve sekiz saat arasında olabilir, ancak bu, veri ambarının süre boyunca çevrimdışı olacağı anlamına gelmez. Bakım, bu pencerede herhangi bir zamanda gerçekleşebilir ve hizmet, veri ambarınıza yeni kod dağıttığı için bu 5 -6 dönemde tek bir bağlantı kesmeyi beklemeniz gerekir. DW400c ve Lower, bakım penceresi sırasında çeşitli zamanlarda bağlantıda birden çok kısa kayıplar olabilir. Bakım başladığında, tüm etkin oturumlar iptal edilir ve teslim edilmemiş işlemler geri alınacaktır. Örnek kapalı kalma süresini en aza indirmek için, veri ambarınızın seçtiğiniz bakım süresinden önce uzun süre çalışan işlemlere sahip olmadığından emin olun.

Zamana duyarlı bir güncelleştirme dağıtmadığımızda, tüm bakım işlemleri belirtilen bakım pencereleri içinde bitmelidir. Zamanlanan Bakım sırasında veri ambarınız duraklatıldıysa, sürdürme işlemi sırasında güncelleştirilir. Veri ambarı bakım işlemi tamamlandıktan hemen sonra bilgilendirilirsiniz.

## <a name="alerts-and-monitoring"></a>Uyarılar ve izleme

Hizmet durumu bildirimleri ve Kaynak Durumu Check Monitor ile tümleştirme, müşterilerin yaklaşan bakım etkinliklerinden haberdar olmasına olanak sağlar. Bu Otomasyon Azure Izleyici 'den yararlanır. Yaklaşan bakım olayları hakkında nasıl bilgilendirilmek istediğinize karar verebilirsiniz. Ayrıca, hangi otomatik akışların kapalı kalma süresini yönetmenize ve işlemsel etkiyi en aza indirmenize yardımcı olacağını seçebilirsiniz.

24 saatlik bir öncelikli bildirim, DWC400c ve alt katmanlar için olmayan tüm bakım olaylarının önüne gelir.

> [!NOTE]
> Zaman açısından kritik güncelleştirme dağıtımı için gerekli olan olayda gelişmiş bildirim süreleri önemli ölçüde azaltılabilir. Bu, güncelleştirmenin kritik doğası nedeniyle, tanımlanan bir bakım penceresinin dışında gerçekleşebilir.

Bakım gerçekleştireceğiz, ancak bildirimde bir süre boyunca bakım gerçekleştirilemediği takdirde, bir iptal bildirimi alırsınız. Bakım daha sonra zamanlanan bir sonraki bakım döneminde sürdürülecek.

Tüm etkin bakım olayları, **hizmet durumu-planlı bakım** bölümünde görünür. Hizmet durumu geçmişi geçmiş olayların tam kaydını içerir. Etkin bir olay sırasında Azure hizmet durumu denetimi Portal panosu aracılığıyla Bakımı izleyebilirsiniz.

### <a name="maintenance-schedule-availability"></a>Bakım zamanlaması kullanılabilirliği

Bakım zamanlaması seçtiğiniz bölgede mevcut olmasa bile, bakım zamanlamanızı dilediğiniz zaman görüntüleyebilir ve düzenleyebilirsiniz. Bölgede bakım zamanlaması kullanılabilir hale geldiğinde, tanımlanan zamanlama hemen SYNAPSE SQL havuzunuzdaki etkin olur.

## <a name="view-a-maintenance-schedule"></a>Bakım zamanlaması görüntüleme

Varsayılan olarak, yeni oluşturulan tüm veri ambarı örneklerinin dağıtım sırasında bir sekiz saat birincil ve ikincil bakım penceresi uygulanmış olması gerekir. Yukarıda belirtildiği gibi, en kısa sürede dağıtımı tamamlandıktan sonra pencereleri değiştirebilirsiniz. Belirtilen bakım pencerelerinin dışında, önceden bildirimde bulunmadan hiçbir bakım yapılmaz.

SYNAPSE SQL havuzunuza uygulanan bakım zamanlamasını görüntülemek için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Görüntülemek istediğiniz SYNAPSE SQL havuzunu seçin.
3. Seçilen SYNAPSE SQL havuzu genel bakış dikey penceresinde açılır. Veri ambarına uygulanan bakım zamanlaması **bakım zamanlaması** altında görünür.

![Genel Bakış dikey penceresi](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Bakım zamanlamasını değiştirme

Bakım zamanlaması dilediğiniz zaman güncelleştirilebilirler veya değiştirilebilir. Seçili örnek etkin bir bakım döngüsüyle gezinecekse, ayarlar kaydedilir. Bir sonraki tanımlanan bakım döneminde etkin hale gelirler. Etkin bir bakım olayı sırasında veri Ambarınızı izleme hakkında [daha fazla bilgi edinin](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

## <a name="identifying-the-primary-and-secondary-windows"></a>Birincil ve ikincil pencereleri tanımlama

Birincil ve ikincil pencerelerin ayrı gün aralıkları olmalıdır. Örnek olarak, Salı – Perşembe ve Cumartesi – Pazar ayının ikincili bir pencere yer aldığı bir örnektir.

SYNAPSE SQL havuzunuzun bakım zamanlamasını değiştirmek için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Güncelleştirmek istediğiniz SYNAPSE SQL havuzunu seçin. Sayfa, genel bakış dikey penceresinde açılır.
Genel Bakış dikey penceresinde **bakım zamanlaması Özeti** bağlantısını seçerek bakım zamanlaması ayarları sayfasını açın. Ya da sol taraftaki kaynak menüsünde **bakım zamanlaması** seçeneğini belirleyin.

    ![Genel Bakış dikey penceresi seçenekleri](./media/maintenance-scheduling/maintenance-change-option.png)

3. Sayfanın üst kısmındaki seçenekleri kullanarak birincil bakım pencereniz için tercih edilen gün aralığını belirler. Bu seçim, birincil pencerenizin hafta içi veya hafta sonu üzerinde mi gerçekleşeceğini belirler. Seçiminiz, açılan değerleri güncelleştirecek.
Önizleme süresince bazı bölgeler, kullanılabilir **gün** seçeneklerinin tam kümesini henüz desteklemeyebilir.

   ![Bakım ayarları dikey penceresi](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Açılan liste kutularını kullanarak tercih ettiğiniz birincil ve ikincil bakım pencerelerini seçin:
   - **Gün**: seçili pencere sırasında bakım gerçekleştirmek için tercih edilen gün.
   - **Başlangıç zamanı**: bakım penceresi için tercih edilen başlangıç zamanı.
   - **Zaman penceresi**: zaman pencerenizin tercih edilen süresi.

   Dikey pencerenin alt kısmındaki **zamanlama Özeti** alanı, seçtiğiniz değerlere göre güncelleştirilir.
  
5. **Kaydet**’i seçin. Yeni zamanlamanız artık etkin olduğunu onaylayan bir ileti görüntülenir.

   Bakım zamanlamasını desteklemeyen bir bölgede bir zamanlama kaydediyorsanız aşağıdaki ileti görüntülenir. Ayarlarınız kaydedilir ve bu özellik seçtiğiniz bölgede kullanılabilir hale geldiğinde etkin hale gelir.

   ![Bölge kullanılabilirliği hakkında ileti](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Sonraki adımlar

- Azure Izleyici 'yi kullanarak uyarı oluşturma, görüntüleme ve yönetme hakkında [daha fazla bilgi edinin](../../azure-monitor/alerts/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .
- Günlük uyarı kuralları için Web kancası eylemleri hakkında [daha fazla bilgi edinin](../..//azure-monitor/alerts/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .
- [Daha fazla bilgi](../..//azure-monitor/alerts/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Eylem grupları oluşturma ve yönetme.
- Azure hizmet durumu hakkında [daha fazla bilgi edinin](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .
