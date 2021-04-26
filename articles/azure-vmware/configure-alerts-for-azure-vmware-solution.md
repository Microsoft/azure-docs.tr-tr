---
title: Azure VMware çözümünde uyarıları yapılandırma ve ölçülerle çalışma
description: Bildirimleri almak için uyarıları nasıl kullanacağınızı öğrenin. Ayrıca, Azure VMware Çözüm özel bulutunuz hakkında daha derin Öngörüler elde etmek için ölçümlerle nasıl çalışacağınızı öğrenin.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: 486f25eba017b2d4e37c0796909a0d26adee6ba8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309637"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Azure VMware çözümünde Azure uyarılarını yapılandırma 

Bu makalede, [Microsoft Azure uyarılarında](/azure/azure-monitor/alerts/alerts-overview) [Azure eylem gruplarını](/azure/azure-monitor/alerts/action-groups) , tanımladığınız tetiklenmiş olayların bildirimlerini almak için nasıl yapılandıracağınızı öğreneceksiniz. Azure VMware Çözüm özel bulutunuz hakkında daha derin öngörüler edinmek için [Azure Izleyici ölçümlerini](/azure/azure-monitor/essentials/data-platform-metrics) kullanma hakkında da bilgi edineceksiniz.


## <a name="supported-metrics-and-activities"></a>Desteklenen ölçümler ve Etkinlikler

Aşağıdaki ölçümler Azure Izleyici ölçümleri aracılığıyla görülebilir.

| **Sinyal adı**                                                         | **Sinyal türü** | **Hizmeti izle** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Veri deposu diski toplam kapasitesi                                           | Metric          | Platform            |
| Kullanılan veri deposu disk yüzdesi                                          | Metric          | Platform            |
| CPU yüzdesi                                                          | Metric          | Platform            |
| Ortalama etkin bellek                                                | Metric          | Platform            |
| Ortalama bellek ek yükü                                                 | Metric          | Platform            |
| Ortalama toplam bellek                                                    | Metric          | Platform            |
| Ortalama bellek kullanımı                                                    | Metric          | Platform            |
| Kullanılan veri deposu diski                                                     | Metric          | Platform            |
| Tüm yönetim işlemleri                                           | Etkinlik Günlüğü    | Yönetim      |
| Microsoft. AVS kaynak sağlayıcısını kaydedin. (Microsoft. AVS/Privatebulutlar) | Etkinlik Günlüğü    | Yönetim      |
| Bir PrivateCloud oluşturun veya güncelleştirin. (Microsoft. AVS/Privatebulutlar)          | Etkinlik Günlüğü    | Yönetim      |
| Bir PrivateCloud 'ı silin. (Microsoft. AVS/Privatebulutlar)                    | Etkinlik Günlüğü    | Yönetim      |

## <a name="configure-an-alert-rule"></a>Uyarı kuralı yapılandırma
1. Azure VMware çözümünüz özel bulutunuzda **izleme**  >  **uyarıları**' nı ve ardından **Yeni uyarı kuralı**' nı seçin.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Azure VMware Çözüm özel bulutunuzda bir uyarı kuralının nerede yapılandırılacağını gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Yeni bir yapılandırma ekranı açılarak şunları yapabilirsiniz:
   - Kapsamı tanımlama
   - Koşul yapılandırma
   - Eylem grubunu ayarlama
   - Uyarı kuralı ayrıntılarını tanımlayın
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="Uyarı kuralı oluştur penceresini gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. **Kapsam** altında, izlemek istediğiniz hedef kaynağı seçin. Varsayılan olarak, uyarılar menüsünü açtığınız Azure VMware çözümü özel bulutu tanımlanmıştır.

1. **Koşul**' ın altında **Koşul Ekle**' yi seçin ve açılan pencerede, uyarı kuralı için oluşturmak istediğiniz sinyali seçer. 

   Örneğimizde, [Azure VMware Çözüm SLA](https://aka.ms/avs/sla) perspektifinden Ilgili olarak **kullanılan yüzde veri deposu diskini** seçtik. 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Önceden tanımlı sinyal adlarıyla sinyal mantığını Yapılandır penceresini gösteren ekran görüntüsü."::: 

1. Uyarıyı tetikleyecek mantığı tanımlayın ve **bitti**' yi seçin. 

   Örneğimizde, yalnızca değerlendirme **eşiği** ve **sıklığı** düzeltildi. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Seçilen sinyal mantığı için bilgileri gösteren ekran görüntüsü."::: 

1. **Eylemler** altında **eylem grupları Ekle**' yi seçin. Eylem grubu, bildirimin *nasıl* *alındığını ve onu* alacağını tanımlar.   E-posta, SMS, [Azure Mobil uygulama anında Iletme bildirimi](https://azure.microsoft.com/features/azure-portal/mobile-app/) veya sesli iletiyle bildirim alabilirsiniz.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Mevcut eylem gruplarını gösteren ve yeni bir eylem grubunun oluşturulacağı ekran görüntüsü.":::

1. Açılan pencerede **eylem grubu oluştur**' u seçin.

   >[!TIP]
   > Mevcut bir eylem grubunu da kullanabilirsiniz.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="Uyarı için seçilecek eylem gruplarını gösteren ekran görüntüsü."::: 

 

 
1. Açılan pencerede, **temel bilgiler** sekmesinde, eylem grubuna bir ad ve bir görünen ad verin.

1. **Bildirimler** sekmesini seçin, bir **bildirim türü** ve **adı** seçin. Ardından **Tamam**’ı seçin.

   Örneğimiz e-posta bildirimine dayalıdır.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="Uyarı için e-posta, SMS mesajı, gönderim ve ses ayarlarını gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. Seçim Etkinlik üzerinde proaktif eylemler gerçekleştirmek ve bildirim almak istiyorsanız **eylemleri** yapılandırın. Kullanılabilir bir **eylem türü** seçin ve ardından **gözden geçir + oluştur**' u seçin. 
   - Otomasyon Runbook'ları
   - Azure Işlevleri – özel olay odaklı sunucusuz kod yürütme için
   - ITSM: Bilet oluşturmak için ServiceNow gibi bir hizmet sağlayıcısıyla tümleştirme
   - Mantıksal uygulama-daha karmaşık iş akışı düzenlemesi için
   - Web kancaları-başka bir hizmette bir işlemi tetiklemek için

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Eylem türü açılır penceresinde odak ile eylem grubu Oluştur penceresini gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. **Uyarı kuralı ayrıntıları**' nın altında, uyarı kuralının depolanması için bir ad, açıklama, kaynak grubu ve önem derecesi belirtin. Ardından **Uyarı kuralı oluştur**' u seçin.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="Uyarı kuralının ayrıntılarını gösteren ekran görüntüsü."::: 
 
   Uyarı kuralı görünür ve Azure portal yönetilebilir.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Kurallar penceresindeki yeni uyarı kuralını gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Bir ölçüm bir uyarı kuralında tanımlandığı şekilde eşiğe ulaştığında, **Uyarılar** menüsü güncelleştirilir ve görünür hale gelir.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Tanımlanan eşiğe ulaştıktan sonra uyarıyı gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   Yapılandırılan eylem grubuna bağlı olarak, yapılandırılmış ortam aracılığıyla bir bildirim alırsınız. Bizim örneğimizde, e-posta yapılandırdık.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Hata dizesiyle bir Azure Izleyici uyarısının ekran görüntüsü ve Tarih ve saat olayı tetiklendi."::: 

## <a name="work-with-metrics"></a>Ölçümler ile çalışma

1. Azure VMware Çözüm özel bulutunuzda **izleme**  >  **ölçümleri**' ni seçin. Ardından, açılır listeden istediğiniz ölçümü seçin.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Ölçüm penceresini ve metrik açılan bir odağı gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. **Zaman aralığı** veya **zaman ayrıntı düzeyi** gibi diyagramın parametrelerini değiştirebilirsiniz. 

   Diğer seçenekler şunlardır:
   - **Günlüklere detaya gitme** ve ilgili Log Analytics çalışma alanındaki verileri sorgulama
   - Kolaylık olması için **Bu diyagramı** bir Azure panosuna sabitleyin.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Ölçüm için zaman aralığı ve zaman ayrıntı düzeyi seçeneklerini gösteren ekran görüntüsü." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Sonraki adımlar

Artık Azure VMware Çözüm özel bulutunuz için bir uyarı kuralı yapılandırdığınıza göre, daha fazla bilgi edinmek isteyebilirsiniz:
- [Azure İzleyici Ölçümleri](/azure/azure-monitor/essentials/data-platform-metrics)
- [Azure İzleyici Uyarıları](/azure/azure-monitor/alerts/alerts-overview)
- [Azure eylem grupları](/azure/azure-monitor/alerts/action-groups)

Diğer [Azure VMware Çözüm](index.yml) nasıl yapılır kılavuzlarından biriyle de devam edebilirsiniz.





