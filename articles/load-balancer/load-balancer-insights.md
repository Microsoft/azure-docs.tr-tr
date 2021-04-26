---
title: Azure Load Balancer için Öngörüler
description: Yük dengeleyici öngörülerini kullanarak hızlı hata yerelleştirmesi ve bilinçli tasarım kararları elde edin
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 190a9e431dedfb0f0fe6077e903174f253f5c773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589112"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Azure Load Balancer izlemek ve yapılandırmak için öngörüleri kullanma

Ağlarda Azure Izleyici aracılığıyla, yük dengeleyiciler için işlevsel bağımlılık görselleştirmeleri ve önceden yapılandırılmış ölçüm panosu sunulmaktadır. Bu görseller bilinçli tasarım kararları almanıza ve hataları hızlı bir şekilde yerelleştirmenize, tanılamanıza ve çözmenize yardımcı olur.

>[!NOTE] 
>Bu özelliğin önizlemededir ve işlev bağımlılığı görünümü ve önceden yapılandırılmış Pano bu deneyimi geliştirmek için değişebilir

>[!IMPORTANT]
>Önceden yapılandırılmış ölçümler panosundaki Load Balancer ad alanındaki ölçümleri görmek için Standart Load Balancer gereklidir. Yine de VM, sanal makine ölçek kümesi ve bağlantı Izleyicisi ad alanlarından ölçümleri görebileceksiniz, ancak güçlü Load Balancer ölçümlerinin avantajlarından yararlanmak için herhangi bir üretim iş yükleri için [Standart sürümüne yükseltmeniz](./upgrade-basic-standard.md) önerilir.

## <a name="functional-dependency-view"></a>İşlevsel bağımlılık görünümü

İşlevsel bağımlılık görünümü, en karmaşık yük dengeleyici kurulumları bile resim belirlemenizi sağlar. En son Load Balancer yapılandırmanızla ilgili görsel geri bildirimler sayesinde, yapılandırmanızı aklınızda tutarken güncelleştirmeler yapabilirsiniz.

Azure 'daki Load Balancer kaynağınızın Öngörüler dikey penceresini ziyaret ederek bu görünüme erişebilirsiniz.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="İşlevsel bağımlılık görünümü ' ne göre. Yük dengeleyicinin ön ucu, yapılandırılan kurallar aracılığıyla arka uç havuz üyelerine bağlanabilir. Standart Load Balancer için, arka uç havuzu örneklerine Yük Dengeleme kurallarından oluşan satırlar, sistem durumu araştırma durumuna göre renk kodludur." border="true":::

Standart yük dengeleyiciler için, arka uç havuzu kaynaklarınız, arka uç havuzunuzun trafiği sunacak geçerli kullanılabilirliğini gösteren sistem durumu araştırma durumuyla renk kodludur. Yukarıdaki topolojinin yanı sıra, uygulamanızın durumunun bir anlık görüntüsünü vererek sistem durumu için zaman odaklı bir grafik görüntülenir.

## <a name="metrics-dashboard"></a>Ölçümler panosu

Load Balancer Öngörüler dikey penceresinde, Load Balancer belirli yönleriyle ilgili ölçüm görselleri içeren önceden yapılandırılmış bir [Azure Izleyici çalışma kitabını](../azure-monitor/visualize/workbooks-overview.md) görüntülemek Için daha ayrıntılı ölçümler ' i seçebilirsiniz. Bu panoda, sayfanın üst kısmında Load Balancer durum ve ilgili belgelerin bağlantıları gösterilir.

İlk olarak genel bakış sekmesi görüntülenir. Her birinde, Load Balancer belirli bir yönüyle ilgili görseller içeren kullanılabilir sekmelerde gezinebilirsiniz. Her birinin açık Kılavuzu, panoda her sekmenin en altında bulunur.

Şu anda kullanılabilir olan pano sekmeleri şunlardır:
* Genel Bakış
* Ön Uç ve Arka Uç Kullanılabilirliği
* Veri Aktarım Hızı
* Akış Dağıtımı
* Bağlantı İzleyicileri
* Ölçüm Tanımları 

### <a name="overview-tab"></a>Genel Bakış sekmesi
Genel Bakış sekmesi, Load Balancer bağlı olan her bir ön uç IP 'si için genel veri yolu kullanılabilirliği ve durum araştırma durumu ile aranabilir bir kılavuz içerir. Bu ölçümler, ön uç IP 'nin yanıt verip etmediğini ve arka uç havuzunuzdaki işlem örneklerinin gelen bağlantılara tek tek yanıt verdiğini gösterir.

Ayrıca, bu sayfadaki her bir ön uç IP 'si için genel veri aktarım hızını görüntüleyerek beklenen trafik düzeylerini oluşturup almamanız durumunda olup olmadığını bir fikir edinebilirsiniz. Sayfanın alt kısmındaki kılavuz, herhangi bir düzensiz değer görmeniz için sizi uygun sekmeye yönlendirdirecektir.

### <a name="frontend-and-backend-availability-tab"></a>Ön uç ve arka uç kullanılabilirliği sekmesi
Ön uç ve arka uç kullanılabilirliği sekmeleri, çeşitli yararlı görünümlerde sunulan veri yolu aktarım hızını ve durum araştırma durumu ölçümlerini gösterir. İlk grafik, bir sorun olup olmadığını belirleyebilmeniz için toplam değeri gösterir. Grafiklerin geri kalanı, bu ölçümleri çeşitli boyutlarla ayırarak, tüm gelen kullanılabilirlik sorunlarını giderebilmeniz ve kaynakları belirleyebilmenizi sağlayacak şekilde gösterir.

Bu grafikleri görüntülemek için bir iş akışı, çeşitli belirtilerin yaygın nedenlerine sahip sayfanın alt kısmında verilmiştir. 

### <a name="data-throughput-tab"></a>Veri Işleme sekmesi
Veri Işleme sekmesi, trafik desenlerinizin beklenen şekilde olup olmadığını belirlemek için gelen ve giden aktarım hızını incelemenizi sağlar. Bu, çalıştırdığınız hizmetlerin ayrı ayrı nasıl çalıştığını belirleyebilmeniz için ön uç IP ve ön uç bağlantı noktası tarafından bölünen gelen ve giden veri aktarım hızını gösterir.

### <a name="flow-distribution"></a>Akış Dağıtımı
Akış dağıtımı sekmesi, arka uç örneklerinizin aldığı ve üreteceği akış sayısını görselleştirmenize ve yönetmenize yardımcı olur. Gelen ve giden trafik için akış oluşturma hızını ve akış sayısını, her VM ve sanal makine ölçek kümesi örneğinin aldığı ağ trafiğini gösterir. 

Bu görünümler, Load Balancer yapılandırmanızın veya trafik desenlerinizin imdenen dengeli trafiğe göre önde olup olmadığı hakkında geri bildirim verebilir. Örneğin, oturum benzeşiminin yapılandırılmış olması ve tek bir istemcinin orantısız sayıda istek yapmasını istiyorsanız. Ayrıca, makine boyutunuz için [VM başına akış sınırına](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) yaklaşıp yaklaşmayacağını da öğrenirsiniz.

### <a name="connection-monitors"></a>Bağlantı İzleyicileri
Bağlantı Izleyicileri sekmesi, yapılandırdığınız tüm [bağlantı izlemeleri](../network-watcher/connection-monitor.md)  için bir genel haritada gidiş dönüş gecikme süresini gösterir. Bu görseller, katı gecikme gereksinimlerine sahip hizmetler için yararlı bilgiler sağlar. Gereksinimlerinizi karşılamak için, ek bölgesel dağıtımlar eklemeniz veya bir [çapraz bölgesel yük dengeleme](./cross-region-overview.md) modeline taşımanız gerekebilir

### <a name="metric-definitions"></a>Ölçüm Tanımları
Ölçüm tanımları sekmesi, [çok boyutlu ölçümler makalesinde](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)gösterilen tüm bilgileri içerir.

## <a name="next-steps"></a>Sonraki adımlar
* Daha sonra iyileştirilen bir şey varsa panoyu gözden geçirin ve aşağıdaki bağlantıyı kullanarak geri bildirim sağlayın
* [Her ölçümün nasıl hesaplanacağını anladığınızdan emin olmak için ölçüm belgelerini gözden geçirin](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Load Balancer için bağlantı Izleyicileri oluşturun](../network-watcher/connection-monitor.md)
* [Kendi çalışma kitaplarınızı oluşturun](../azure-monitor/visualize/workbooks-overview.md), ayrıntılı ölçüm panonuzdaki Düzenle düğmesine tıklayarak ilham alma yapabilirsiniz
