---
title: Ağlar için Azure Izleyici (Önizleme)
description: Hiçbir yapılandırma olmadan dağıtılan tüm ağ kaynakları için sistem durumu ve ölçümlerinin kapsamlı bir görünümünü sağlayan ağ için Azure Izleyici 'ye hızlı bir genel bakış.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 3bb92b85779366081349dab777be67e063469678
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73840060"
---
# <a name="azure-monitor-for-networks-preview"></a>Ağlar için Azure Izleyici (Önizleme)
Ağ için Azure Izleyici, herhangi bir yapılandırma olmadan dağıtılan tüm ağ kaynakları için sistem durumu ve ölçümlerinin kapsamlı bir görünümünü sağlar. Gelişmiş arama özelliği, yalnızca barındırılan Web sitesi adını arayarak Web sitenizi barındıran kaynakları tanımlama gibi senaryoları etkinleştirerek Kaynak bağımlılıklarını tanımlamaya yardımcı olur.

Ağlara yönelik Azure Izleyici **'ye genel bakış** sayfası, ağ kaynaklarınızın envanterini görselleştirmenin yanı sıra kaynak sistem durumu ve uyarılarla ilgili daha kolay bir yol sağlar. Dört önemli işlevsel alana bölünmüştür:

- Arama ve filtreleme
- Kaynak Durumu ve ölçümler
- Uyarılar 
- Bağımlılık görünümü

![Genel Bakış sayfası](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Arama ve filtreleme
Kaynak durumu ve Uyarılar görünümü, **abonelik**, **kaynak grubu** ve **kaynak türü**gibi filtreler kullanılarak özelleştirilebilir. Arama kutusu, kaynak özellikleri arasında arama yapma yeteneği sağlar.

Arama kutusu, kaynakları ve ilişkili kaynakları aramak için kullanılabilir. Örneğin, genel IP bir Application Gateway ilişkilendirilir. Genel IP DNS adını aramak, hem genel IP hem de ilişkili Application Gateway belirler.

![Arama](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Kaynak Durumu ve metrik
Her kutucuk, kaynak sistem durumu ile birlikte seçilen tüm aboneliklerde dağıtılan örneklerin sayısı ile bir kaynak türünü temsil eder. Aşağıdaki örnekte, 105 ER ve VPN bağlantısı dağıtılır, 103 sağlıklı ve 2 kullanılamaz.

![Kaynak durumu](media/network-insights-overview/resource-health.png)

Kullanılamayan iki ER ve VPN bağlantısına tıkladığınızda bir ölçüm görünümü başlatılır. 

![Ölçüm görünümü](media/network-insights-overview/metric-view.png)

Kılavuz görünümündeki her bir öğeye tıklayabilirsiniz. Bu bağlantının kaynak sistem durumuna yeniden yönlendirmek için sistem durumu simgesine tıklayın. Bu bağlantı için sırasıyla uyarılar ve ölçümler sayfasına yeniden yönlendirmek üzere Uyarılar ' a tıklayın. 

## <a name="alerts"></a>Uyarılar
Sağdaki **Uyarılar** Kılavuzu, tüm abonelikler genelinde seçili kaynaklar için oluşturulan tüm uyarıların bir görünümünü sağlar. Ayrıntılı uyarılar sayfasına gitmek için uyarı sayılarına tıklayın.

## <a name="dependency-view"></a>Bağımlılık görünümü
**Bağımlılık** görünümü, kaynağın nasıl yapılandırıldığını görselleştirmenize yardımcı olur. Şu anda bağımlılık görünümü yalnızca Application Gateway için desteklenir. Bağımlılık görünümüne, ölçümler kılavuz görünümündeki Application Gateway kaynak adına tıklanarak erişilebilir.

![Application Gateway görünümü](media/network-insights-overview/application-gateway.png)

Application Gateway için **bağımlılık** görünümü, ön uç IP 'lerinin dinleyicileri, kuralları ve arka uç havuzunu nasıl bağladığınıza ilişkin basitleştirilmiş bir görünüm sağlar. Bağlanan kenarlar renk kodludur ve arka uç havuzu sistem durumuna göre ek ayrıntılar sağlar. Görünüm Ayrıca, VMSS ve VM örnekleri gibi tüm ilgili arka uç havuzlarının Application Gateway ölçümlerinin ve ölçümlerinin ayrıntılı bir görünümünü sağlar.

![Bağımlılık görünümü](media/network-insights-overview/dependency-view.png)

Bağımlılık grafiği yapılandırma ayarlarına kolay gezinme sağlar. Diğer işlevlere erişmek için bir arka uç havuzuna sağ tıklayın. Örneğin, arka uç havuzu bir sanal makine ise, bağlantı sorunlarını belirlemek için VM öngörülerine ve ağ Izleyicisi bağlantısına doğrudan erişebilirsiniz.

![Bağımlılık Görünüm menüsü](media/network-insights-overview/dependency-view-menu.png)

Bağımlılık görünümündeki arama ve filtre çubuğu, grafikte arama yapmanın kolay bir yolunu sağlar. Örneğin, aşağıdaki örnekte *appgwtestrule* araması, grafik görünümünü *appgwtestrule*ile bağlantılı tüm düğümlere daraltacaktır. 

![Arama örneği](media/network-insights-overview/search-example.png)

Farklı filtreler, belirli bir yol ve duruma göre daraltmak için yardım sağlar. Örneğin, durumun *sağlıksız*olduğu tüm kenarları göstermek için **sistem** durumu açılan listesinden yalnızca *sağlıksız* ' ı seçin.

Uygulama ağ geçidi, tüm arka uç havuzu kaynakları ve ön uç IP 'Leri için ayrıntılı ölçümleri olan önceden yapılandırılmış bir çalışma kitabı başlatmak üzere **ayrıntılı Ölçüm görünümü** ' ne tıklayın. 

## <a name="next-steps"></a>Sonraki adımlar 

- Ağ izleme hakkında daha fazla bilgi edinmek için [Azure Ağ İzleyicisi nedir?](/azure/network-watcher/network-watcher-monitoring-overview).
