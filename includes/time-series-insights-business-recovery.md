---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 04/01/2021
ms.openlocfilehash: 6529aa49d06e64947deb5ae54db0c39ad2575569
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106288612"
---
## <a name="business-disaster-recovery"></a>İş olağanüstü durum kurtarma

Bu bölümde, bir olağanüstü durum meydana gelirse ( *iş olağanüstü durum kurtarma* olarak bilinir) uygulamaların ve hizmetlerin çalışır durumda tutulması Azure Time Series Insights özellikleri açıklanmaktadır.

### <a name="high-availability"></a>Yüksek kullanılabilirlik

Azure hizmeti olarak Azure Time Series Insights, Azure bölge düzeyinde artıklıkları kullanarak belirli *yüksek kullanılabilirlik* özellikleri sağlar. Örneğin, Azure, Azure 'un *çapraz bölge kullanılabilirliği* özelliği aracılığıyla olağanüstü durum kurtarma özelliklerini destekler.

Azure (ve ayrıca tüm Azure Time Series Insights örnekleri için de kullanılabilir) ile sağlanan diğer yüksek kullanılabilirlik özellikleri şunlardır:

- **Yük devretme**: Azure, [coğrafi çoğaltma ve yük dengeleme](/azure/architecture/resiliency/recovery-loss-azure-region)sağlar.
- **Veri geri yükleme** ve **depolama kurtarma**: Azure, [verileri korumak ve kurtarmak için çeşitli seçenekler](/azure/architecture/resiliency/recovery-data-corruption)sunar.
- **Azure Site Recovery**: Azure, [Azure Site Recovery](../articles/site-recovery/index.yml)üzerinden kurtarma özellikleri sağlar.
- **Azure Backup**: [Azure Backup](../articles/backup/backup-architecture.md) hem şirket Içi hem de Azure VM 'lerinin bulut yedeklemesini destekler.

Cihazlarınız ve kullanıcılarınız için küresel ve çapraz bölge yüksek kullanılabilirlik sağlamak üzere ilgili Azure özelliklerini etkinleştirdiğinizden emin olun.

> [!NOTE]
> Azure, bölgeler arası kullanılabilirliği etkinleştirecek şekilde yapılandırıldıysa Azure Time Series Insights ek çapraz bölge kullanılabilirlik yapılandırması gerekmez.

### <a name="iot-and-event-hubs"></a>IoT ve Olay Hub 'ları

Bazı Azure IoT Hizmetleri, yerleşik iş olağanüstü durum kurtarma özelliklerini de içerir:

- Azure IoT Hub, bölge içi artıklık içeren [yüksek kullanılabilirliğe sahip olağanüstü durum kurtarma](../articles/iot-hub/iot-hub-ha-dr.md)
- [Azure Event Hubs ilkeleri](../articles/event-hubs/event-hubs-geo-dr.md)
- [Azure Depolama yedekliliği](../articles/storage/common/storage-redundancy.md)

Diğer hizmetlerle Azure Time Series Insights tümleştirme, ek olağanüstü durum kurtarma olanakları sağlar. Örneğin, Olay Hub 'ınıza gönderilen telemetri, yedek bir Azure Blob depolama veritabanına kalıcı olabilir.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Azure Time Series Insights verilerinizi, uygulamalarınızı ve hizmetlerinizi, kesintiye uğrasa bile çalışır durumda tutmanın birkaç yolu vardır.

Ancak, aşağıdaki amaçlar için Azure zaman serisi ortamınızın tamamen bir yedek kopyasının de gerekli olduğunu belirleyebilirsiniz:

- Özel bir *Yük devretme örneği* olarak, verileri ve trafiği yeniden yönlendirme Azure Time Series Insights
- Veri ve denetim bilgilerini korumak için

Genel olarak, bir Azure Time Series Insights ortamını yinelemek için en iyi yol, yedekleme Azure bölgesinde ikinci bir Azure Time Series Insights ortamı oluşturmaktır. Ayrıca, olaylar birincil olay kaynağınızdan bu ikincil ortama gönderilir. İkinci bir adanmış Tüketici grubu kullandığınızdan emin olun. Daha önce açıklandığı gibi kaynağın iş olağanüstü durum kurtarma yönergelerini izleyin.

Yinelenen bir ortam oluşturmak için:

1. İkinci bir bölgede bir ortam oluşturun. Daha fazla bilgi için [Azure Portal yeni Azure Time Series Insights ortamı oluştur](../articles/time-series-insights/time-series-insights-get-started.md)' u okuyun.
1. Olay kaynağınız için ikinci bir ayrılmış Tüketici grubu oluşturun.
1. Bu olay kaynağını yeni ortama bağlayın. İkinci adanmış tüketici grubunu seçtiğinizden emin olun.
1. Azure Time Series Insights [IoT Hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) ve [Event Hubs](../articles/time-series-insights/concepts-access-policies.md) belgelerini gözden geçirin.

Bir olay oluşursa:

1. Birincil bölgeniz bir olağanüstü durum olayı sırasında etkileniyorsa, işlemleri yedekleme Azure Time Series Insights ortamına yeniden yönlendir.
1. Hub sıra numaraları, yük devretmeden sonra 0 ' dan yeniden başlatıldığında, yinelenen olaylar gibi görünbilecekleri şeyleri oluşturmaktan kaçınmak için olay kaynağını farklı tüketici gruplarıyla her iki bölgede/ortamda yeniden oluşturun.
1. Tüm Azure Time Series Insights telemetri ve sorgu verilerini yedeklemek ve kurtarmak için ikinci bölgenizi kullanın.

> [!IMPORTANT]
> Yük devretme gerçekleşirse:
>
> - Bir gecikme da oluşabilir.
> - İşlemler tekrar yönlendirilmesiyle ileti işleme içindeki bir kopan ani meydana gelebilir.
>
> Daha fazla bilgi için [Azure Time Series Insights gecikme süresini azaltır](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md).