---
title: Hizmet durumuna genel bakış | Microsoft Docs
description: Azure uygulamalarınızın geçerli ve gelecekteki Azure hizmet sorunlarından ve bakımda nasıl etkilendiğine ilişkin kişiselleştirilmiş bilgiler.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 3e3c83b7233ad4da263dec31c6012209ebaca0fe
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515854"
---
# <a name="service-health-overview"></a>Hizmet durumuna genel bakış

Hizmet durumu size, Azure hizmetlerinizin sistem durumunu kullandığınız bölgelerde izleyen özelleştirilebilir bir pano sağlar. Bu panoda, devam eden hizmet sorunları, gelecek planlı bakım veya ilgili sağlık danışma belgeleri gibi etkin olayları izleyebilirsiniz. Olaylar etkin olmadığında, 90 güne kadar sistem durumu geçmişinize yerleştirilirler. Son olarak, hizmet sorunları ne zaman etkilentiğinizde size bildirimde bulunan hizmet durumu uyarılarını oluşturmak ve yönetmek için hizmet durumu panosunu kullanabilirsiniz.

## <a name="service-health-events"></a>Hizmet durumu olayları

Hizmet durumu, kaynaklarınızı etkileyebilecek üç tür sistem durumu olayını izler:

1. **Hizmet sorunları** -Şu anda sizi etkileyen Azure hizmetlerinde sorunlar. 
2. **Planlı bakım** -gelecekte hizmetlerinizin kullanılabilirliğini etkileyebilecek yaklaşan bakım.  
3. **Durum Danışma belgeleri** -Azure hizmetlerinde ilgilenmeniz gereken değişiklikler. Azure özelliklerinin kullanım dışı olduğu veya kullanım kotasını aştığınız durumlarda örneklere örnek verilebilir.

> [!NOTE]
> Hizmet durumu olaylarını görüntülemek için kullanıcılara bir abonelik üzerinde [okuyucu rolü verilmelidir](../role-based-access-control/role-assignments-portal.md) .

## <a name="get-started-with-service-health"></a>Hizmet durumu ile çalışmaya başlama

Hizmet sistem durumu panonuzu başlatmak için Portal panonuzda hizmet durumu kutucuğunu seçin. Kutucuğu daha önce kaldırdıysanız veya özel pano kullanıyorsanız, "diğer hizmetler" (panoda sol alt) bölümünde hizmet sistem durumu hizmetini aratın.

![Hizmet durumu ile çalışmaya başlama](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Hizmetlerinizi etkileyen geçerli sorunları görün

**Hizmet sorunları** görünümü, Azure Hizmetlerindeki kaynakları etkileyen devam eden sorunları gösterir. Sorunun başladığı zamanı ve hangi hizmet ve bölgelerin etkilendiğini anlayabilirsiniz. Sorunu çözmek için Azure 'un ne yaptığını anlamak için en son güncelleştirmeyi de okuyabilirsiniz. 

![Hizmet sorununu yönetme](./media/service-health-overview/azure-service-health-overview-2.png)

Sahip olduğunuz ve sorundan etkilenmiş olabileceğiniz kaynakların belirli bir listesini görmek için **olası etki** sekmesini seçin. Takımınızla paylaşmak için bu kaynakların CSV listesini indirebilirsiniz.

![Hizmet sorununu yönetme-etki](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Bağlantıları ve indirilebilir açıklamaları alın 

Sorun yönetim sisteminizde kullanmak için sorun için bir bağlantı edinebilirsiniz. Azure portal erişimi olmayan kişilerle paylaşmak için PDF ve bazen CSV dosyalarını indirebilirsiniz.   

![Hizmet sorununu yönetme-sorun yönetimi](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Microsoft 'tan destek alın

Sorun çözümlendikten sonra bile kaynağınız hatalı bir durumda bırakılırsa desteğe başvurun.  Sayfanın sağ tarafındaki destek bağlantılarını kullanın.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Panonuza kişiselleştirilmiş bir sistem durumu haritasını sabitleme

İş açısından kritik abonelikleri, bölgeleri ve kaynak türlerinizi göstermek için hizmet durumunu filtreleyin. Filtre kaydedin ve kişiselleştirilmiş bir sistem durumu dünya haritasını Portal panonuza sabitleyin. 

![Kişiselleştirilmiş sistem durumu haritasını filtrele](./media/service-health-overview/azure-service-health-overview-6a.png)

![Kişiselleştirilmiş bir sistem durumu haritasını sabitleme](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Hizmet durumu uyarılarını yapılandırma

Hizmet durumu, iş açısından kritik kaynaklarınız etkilendiğinde e-posta, kısa mesaj ve Web kancası bildirimleri aracılığıyla sizi uyarmak için Azure Izleyici ile tümleşir. Uygun hizmet sistem durumu olayı için bir etkinlik günlüğü uyarısı ayarlayın. Işlem gruplarını kullanarak kuruluşunuzdaki uygun kişilere uyarı yönlendirin. Daha fazla bilgi için bkz. [hizmet durumu uyarılarını yapılandırma](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Sonraki adımlar

Sistem durumu sorunları hakkında bildirim almak için uyarıları ayarlayın. Daha fazla bilgi için bkz. [Azure hizmet durumu uyarılarını ayarlamaya yönelik en iyi uygulamalar](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
