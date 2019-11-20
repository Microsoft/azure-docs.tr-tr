---
title: Azure Traffic Manager Trafik Görünümü
description: Bu giriş bölümünde Traffic Manager trafik görünümünün nasıl çalıştığı hakkında bilgi edinin.
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 1cdad5873ea0f6d2fab129aef7be012ea11e8a60
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023256"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Trafik Görünümü

Traffic Manager, son kullanıcılarınızın profili oluştururken belirtilen yönlendirme yöntemine bağlı olarak sağlıklı uç noktalara yönlendirilmeleri için DNS düzeyinde yönlendirme sağlar. Trafik Görünümü, Kullanıcı tabanlarınızın (bir DNS çözümleyici ayrıntı düzeyi düzeyinde) ve bunların trafik deseninin bir görünümü ile Traffic Manager sağlar. Trafik Görünümü etkinleştirdiğinizde bu bilgiler, eyleme dönüştürülebilir içgörüler sağlamak üzere işlenir. 

Trafik Görünümü kullanarak şunları yapabilirsiniz:
- Kullanıcı temellerinizin nerede olduğunu anlayın (yerel bir DNS çözümleyici düzeyi ayrıntı düzeyine kadar).
- Bu bölgelerden kaynaklanan trafik hacmini (Azure Traffic Manager tarafından işlenen DNS sorguları olarak gözlemlendi) görüntüleyin.
- Bu kullanıcılar tarafından karşılaşılan temsilci gecikme süresi hakkında öngörüler elde edin.
- uç noktalarınız olan Azure bölgelerine bu kullanıcı temellerinden gelen belirli trafik desenlerine derinlemesine bakış. 

Örneğin, hangi bölgelerin çok sayıda trafiğe sahip olduğunu, ancak daha yüksek gecikme sürelerinin ne olduğunu anlamak için Trafik Görünümü kullanabilirsiniz. Daha sonra bu bilgileri kullanarak, bu kullanıcıların daha düşük gecikme süresine sahip olması için parmak izinizi yeni Azure bölgelerine göre planlamanız için kullanabilirsiniz.

## <a name="how-traffic-view-works"></a>Trafik Görünümü nasıl kullanılır?

Trafik Görünümü, bu özelliğin etkin olduğu bir profilde son yedi gün içinde alınan gelen sorgulara Traffic Manager bakarak işe yarar. Gelen sorgular bilgilerden Trafik Görünümü, kullanıcıların konumunun temsili olarak kullanılan DNS Çözümleyicisinin kaynak IP 'sini ayıklar. Bunlar daha sonra, Traffic Manager tarafından tutulan IP adreslerinin coğrafi bilgilerini kullanarak Kullanıcı temel bölgeleri oluşturmak için bir DNS çözümleyici düzeyi ayrıntı düzeyinde birlikte gruplandırılır. Traffic Manager, sorgunun yönlendirildiği Azure bölgelerine bakar ve bu bölgelerdeki kullanıcılar için bir trafik akışı haritası oluşturur.  
Sonraki adımda Traffic Manager, Kullanıcı temel bölgesini farklı Son Kullanıcı ağları için sakladığı ağ zekası gecikme tablolarıyla Azure bölge eşleme ile eşleyerek, bu bölgelerden kullanıcılar tarafından karşılaşılan ortalama gecikme süresini anlamak için Azure bölgelerine bağlanma. Bu hesaplamaların tamamı, size sunulmadan önce yerel DNS çözümleyici IP düzeyinde birleştirilir. Bilgileri çeşitli yollarla kullanabilirsiniz.

Trafik görünümü veri güncelleştirme sıklığı, birden çok iç hizmet değişkenine bağlıdır. Ancak, veriler genellikle her 24 saatte bir güncelleştirilir.

>[!NOTE]
>Trafik Görünümü açıklanan gecikme süresi, son kullanıcı ile bağlı oldukları Azure bölgeleri arasında temsili bir gecikme süresi ve DNS arama gecikmesi değildir. Trafik Görünümü, yerel DNS çözümleyici ile sorgunun yönlendirildiği Azure bölgesi arasındaki gecikme süresini en iyi şekilde tahmin etmek için yeterli veri yoksa, döndürülen gecikme null olur. 

## <a name="visual-overview"></a>Görsel genel bakış

Traffic Manager sayfanızdaki **trafik görünümü** bölümüne gittiğinizde, trafik görünümü öngörülerinin yer aldığı bir coğrafi harita sunulur. Eşleme, Traffic Manager profilinizin kullanıcı tabanı ve uç noktaları hakkında bilgi sağlar.

![Traffic Manager Trafik Görünümü coğrafi görünüm][1]

### <a name="user-base-information"></a>Kullanıcı temel bilgileri

Konum bilgilerinin kullanılabildiği yerel DNS çözümleyiciler için, haritada gösterilir. DNS Çözümleyicisinin rengi, Traffic Manager sorguları için bu DNS çözümleyicisini kullanan son kullanıcıların yaşadığı ortalama gecikme süresini gösterir.

Haritada bir DNS çözümleyici konumunun üzerine geldiğinizde şunu gösterir:
- DNS Çözümleyicisinin IP adresi
- Traffic Manager tarafından görülen DNS sorgu trafiği hacmi
- uç nokta ve DNS Çözümleyicisi arasında bir çizgi olarak DNS çözümleyicisinden gelen trafiğin yönlendirildiği uç noktalar 
- Bu konumdan, birbirine bağlanan çizginin rengi olarak temsil edilen ortalama gecikme bitiş noktası

### <a name="endpoint-information"></a>Uç nokta bilgileri

Uç noktaların bulunduğu Azure bölgeleri, haritada mavi noktalar olarak gösterilir. Uç noktanız dış ise ve kendisine eşlenmiş bir Azure bölgesi yoksa, eşlemenin en üstünde gösterilir. Trafiğin bu uç noktaya yönlendirildiği farklı konumları (kullanılan DNS Çözümleyicisi temelinde) görmek için herhangi bir uç noktaya tıklayın. Bağlantılar, uç nokta ve DNS çözümleyici konumu arasında bir çizgi olarak gösterilir ve bu çift arasındaki temsilci gecikmesine göre renklendirilir. Ayrıca, uç noktanın adını, çalıştığı Azure bölgesini ve bu Traffic Manager profili tarafından kendisine yönlendirilen isteklerin toplam hacmini görebilirsiniz.


## <a name="tabular-listing-and-raw-data-download"></a>Tablosal listeleme ve ham veri yükleme

Trafik Görünümü verilerini Azure portal tablo biçiminde görüntüleyebilirsiniz. DNS Çözümleyicisinin IP adresini, uç noktanın bulunduğu Azure bölgesinin adı ve coğrafi konumunu (varsa), bu DNS çözümleyici ile ilişkili isteklerin hacmini ve ilgili DNS ile ilgili temsilci gecikmesini (varsa) kullanan her DNS çözümleyici IP/uç noktası çifti için bir giriş vardır. Trafik Görünümü verilerini, tercih ettiğiniz analiz iş akışının bir parçası olarak kullanılabilecek bir CSV dosyası olarak da indirebilirsiniz.

## <a name="billing"></a>Faturalama

Trafik Görünümü kullandığınızda, sunulan öngörüleri oluşturmak için kullanılan veri noktası sayısına göre faturalandırılırsınız. Şu anda kullanılan tek veri noktası türü Traffic Manager profilinize göre alınan sorgulardır. Fiyatlandırma hakkında daha fazla bilgi için [Traffic Manager fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/traffic-manager/)ziyaret edin.

## <a name="faqs"></a>SSS

* [Trafik Görünümü ne yapar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Trafik Görünümü kullanmanın avantajlarından faydalanabilirsiniz.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Azure izleyici aracılığıyla kullanılabilen Traffic Manager ölçümlerinden farklı Trafik Görünümü nedir?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Trafik Görünümü EDNS Istemci alt ağ bilgilerini kullanır mi?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Kaç gün veri Trafik Görünümü kullanır?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Trafik Görünümü dış uç noktaları nasıl işler?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Aboneliğimde her profil için Trafik Görünümü etkinleştirmem gerekir mi?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Trafik Görünümü nasıl kapatırım?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Trafik Görünümü Faturalandırma nasıl çalışır?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Sonraki adımlar

- [Traffic Manager nasıl çalıştığını](traffic-manager-overview.md) öğrenin
- Traffic Manager tarafından desteklenen [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md) hakkında daha fazla bilgi edinin
- [Traffic Manager profili oluşturmayı](traffic-manager-create-profile.md) öğrenin

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png