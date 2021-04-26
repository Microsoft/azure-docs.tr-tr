---
title: Ölçümler ve uyarılar-Azure DNS
description: Bu öğrenme yoluyla Azure DNS ölçümler ve uyarılar ile çalışmaya başlayın.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 391109727877544a4e94bae376ecef4d33a13cfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575288"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS ölçümleri ve uyarıları
Azure DNS, Microsoft Azure altyapısını kullanarak ad çözümlemesi sağlayan DNS etki alanları için bir barındırma hizmetidir. Bu makalede Azure DNS hizmetine yönelik ölçümler ve uyarılar açıklanmaktadır.

## <a name="azure-dns-metrics"></a>Azure DNS ölçümleri

Azure DNS, müşterilerin, hizmette barındırılan DNS bölgelerinin belirli yönlerini izlemelerine olanak tanımak için ölçümler sağlar. Ayrıca, Azure DNS ölçümler sayesinde, ilgi koşullarına göre uyarıları yapılandırabilir ve alabilirsiniz. Ölçümler, [Azure İzleyici hizmeti](../azure-monitor/index.yml)aracılığıyla sağlanır. Azure DNS, DNS bölgeleriniz için Azure Izleyici aracılığıyla aşağıdaki ölçümleri sağlar:

-   QueryVolume
-   Kayıt kümesi sayısı
-   Recordsetkapaıyutilileştirme

Ayrıca, [Bu ölçümlerin tanımını](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones) Azure izleyici belgeleri sayfasında görebilirsiniz.
>[!NOTE]
> Şu anda, bu ölçümler yalnızca Azure DNS barındırılan genel DNS bölgelerinde kullanılabilir. Azure DNS içinde barındırılan özel bölgeniz varsa, bu ölçümler bu bölgeler için veri sağlamacaktır. Ayrıca, ölçümler ve uyarı özelliği yalnızca Azure genel bulutunda desteklenir. Sogeign bulutları için destek daha sonra takip edilecek. 

İçin ölçümleri görebileceğiniz en parçalı öğe bir DNS bölgesidir. Bir bölgedeki tek tek kaynak kayıtları için ölçümleri Şu anda göremezsiniz.

### <a name="query-volume"></a>Sorgu hacmi

Azure DNS ' deki *sorgu hacmi* ölçümü, DNS bölgeniz için Azure DNS tarafından alınan DNS sorgularının (sorgu trafiği) hacmini gösterir. Ölçüm birimi sayılır ve toplama, bir süre içinde alınan tüm sorguların toplamıdır. 

Bu ölçümü görüntülemek için Azure portal Monitor sekmesinden ölçümler (Önizleme) gezgin deneyimi ' ni seçin. Kaynak açılan listesinden DNS bölgenizi seçin, toplu ölçüm ' i seçin ve toplama olarak Sum ' ı seçin. Aşağıdaki ekran görüntüsünde bir örnek gösterilmektedir.  Ölçüm Gezgini deneyimi ve grafik hakkında daha fazla bilgi için bkz. [Azure izleyici ölçüm Gezgini](../azure-monitor/essentials/metrics-charts.md).

![Sorgu hacmi](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Şekil: Azure DNS sorgu birimi ölçümleri*

### <a name="record-set-count"></a>Kayıt kümesi sayısı
*Kayıt kümesi sayısı* ölçümü, DNS bölgeniz Için Azure DNS kayıt kümesi sayısını gösterir. Bölgeniz tanımlı tüm kayıt kümeleri sayılır. Ölçüm birimi sayılır ve toplama tüm kayıt kümelerinin en fazla sayısıdır. Bu ölçümü görüntülemek için Azure portal **Monitor** sekmesinden **ölçümler (Önizleme)** gezgin deneyimi ' ni seçin. **Kaynak** AÇıLAN listesinden DNS bölgenizi seçin, **kayıt kümesi sayısı** ölçümünü seçin ve ardından **toplama** olarak **Max** ' ı seçin. Ölçüm Gezgini deneyimi ve grafik hakkında daha fazla bilgi için bkz. [Azure izleyici ölçüm Gezgini](../azure-monitor/essentials/metrics-charts.md). 

![Kayıt kümesi sayısı](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Şekil: Azure DNS kayıt kümesi sayısı ölçümleri*


### <a name="record-set-capacity-utilization"></a>Kayıt kümesi kapasite kullanımı
Azure DNS ' de *kayıt kümesi kapasitesi kullanım* ölçümü, bir DNS bölgesi Için kayıt kümesi kapasitenizin kullanım yüzdesini gösterir. Azure DNS içindeki her DNS bölgesi, bölge için izin verilen en fazla kayıt kümesi sayısını tanımlayan bir kayıt kümesi sınırına tabidir (bkz. [DNS limitleri](dns-zones-records.md#limits)). Bu nedenle, bu ölçüm, kayıt kümesi sınırına vurmaya ne kadar yakın olduğunu gösterir. Örneğin, DNS bölgeniz için 500 Kayıt kümeniz varsa ve bölgede varsayılan kayıt kümesi sınırı 5000 ise, Recordsetkapaıyutilileştirme ölçümü, %10 değerini gösterir (Bu işlem, 500 5000 ' i bölerek elde edilir). Ölçü birimi **yüzde** , **toplama** türü ise **en fazla**. Bu ölçümü görüntülemek için Azure portal Monitor sekmesinden ölçümler (Önizleme) gezgin deneyimi ' ni seçin. Kaynak açılan listesinden DNS bölgenizi seçin, kayıt kümesi kapasitesi kullanım ölçümünü seçin ve toplama olarak en fazla ' yı seçin. Aşağıdaki ekran görüntüsünde bir örnek gösterilmektedir. Ölçüm Gezgini deneyimi ve grafik hakkında daha fazla bilgi için bkz. [Azure izleyici ölçüm Gezgini](../azure-monitor/essentials/metrics-charts.md). 

![Ölçümlerin nasıl görüntüleneceği hakkında bir örnek gösteren ekran görüntüsü.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Şekil: Azure DNS kayıt kümesi kapasite kullanım ölçümleri*

## <a name="alerts-in-azure-dns"></a>Azure DNS uyarılar
Azure Izleyici, kullanılabilir ölçüm değerlerine karşı uyarı verebilir özelliğini sağlar. DNS ölçümleri yeni uyarı yapılandırma deneyiminde kullanılabilir. [Azure izleyici uyarıları belgelerinde](../azure-monitor/alerts/alerts-metric.md)ayrıntılı olarak açıklandığı gibi, kaynak olarak DNS Bölgesi ' ni seçebilir, ölçüm sinyali türünü seçebilir ve uyarı mantığını ve **Dönem** ve **Sıklık** gibi diğer parametreleri yapılandırabilirsiniz. Uyarı koşulunun karşılandığı zaman, uyarının Seçili eylemler aracılığıyla teslim edileceği bir [eylem grubu](../azure-monitor/alerts/action-groups.md) tanımlayabilirsiniz. Azure Izleyici ölçümleri için uyarı yapılandırma hakkında daha fazla bilgi için bkz. [Azure izleyici kullanarak uyarı oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-metric.md). 

## <a name="next-steps"></a>Sonraki adımlar
- [Azure DNS](dns-overview.md)hakkında daha fazla bilgi edinin.