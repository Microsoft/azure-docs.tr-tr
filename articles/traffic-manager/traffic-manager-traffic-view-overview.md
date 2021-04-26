---
title: Azure Traffic Manager Trafik Görünümü
description: Bu giriş bölümünde Traffic Manager trafik görünümünün nasıl çalıştığı hakkında bilgi edinin.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743058"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Trafik Görünümü

Traffic Manager, DNS (etki alanı adı sistemi) düzeyinde yönlendirme sağlar. Bu hizmet, son kullanıcılarınızın seçtiğiniz yönlendirme yöntemine göre sağlıklı uç noktalara yönlendirilmenizi sağlar. Trafik Görünümü, Kullanıcı tabanlarınızın (bir DNS çözümleyici ayrıntı düzeyi düzeyinde) ve bunların trafik deseninin bir görünümü ile Traffic Manager sağlar. Trafik Görünümü etkinleştirdiğinizde bu bilgiler, eyleme dönüştürülebilir içgörüler sağlamak üzere işlenir. 

Trafik Görünümü kullanarak şunları yapabilirsiniz:
- Kullanıcı temellerinizin nerede olduğunu anlayın (yerel bir DNS çözümleyici düzeyi ayrıntı düzeyine kadar).
- Bu bölgelerden kaynaklanan trafik hacmini (Azure Traffic Manager tarafından işlenen DNS sorguları olarak gözlemlendi) görüntüleyin.
- Bu kullanıcılar tarafından karşılaşılan temsilci gecikme süresi hakkında öngörüler elde edin.
- uç noktalarınız olan Azure bölgelerine bu kullanıcı temellerinden gelen belirli trafik desenlerine derinlemesine bakış. 

Örneğin, hangi bölgelerin büyük miktarda trafiğe sahip olduğunu anlamak için Trafik Görünümü kullanabilirsiniz, ancak daha yüksek gecikme sürelerinden çok daha fazla gecikme olur. Daha sonra bu bilgileri, parmak izinizi yeni Azure bölgelerine göre planlamak için kullanırsınız. Bu şekilde kullanıcılarınızın gecikme süresi daha düşük olur.

## <a name="how-traffic-view-works"></a>Trafik Görünümü nasıl kullanılır?

Trafik Görünümü, bir profil için son yedi gün içinde alınan gelen sorgulara bakarak işe yarar. Gelen sorgular bilgilerden Trafik Görünümü, kullanıcıların konumunu temsil etmek için kullanılan DNS Çözümleyicisinin kaynak IP 'sini ayıklar. Bu bilgiler, Kullanıcı tabanlı bölgeler oluşturmak için bir DNS çözümleyici düzeyinde birlikte gruplandırılır. Traffic Manager, IP adreslerinin coğrafi bilgilerini korur. Traffic Manager, sorgunun yönlendirildiği Azure bölgelerine bakar ve bu bölgelerdeki kullanıcılar için trafik akışı haritası oluşturur.
 
Sonraki adımda, Traffic Manager Kullanıcı temel bölgesini ağ zekası gecikme tablolarıyla Azure bölge eşlemesi ile ilişkilendirir. Bu tablo, Azure bölgelerine bağlanırken bu bölgelerdeki kullanıcıların yaşadığı ortalama gecikme süresini anlamak üzere farklı Son Kullanıcı ağlarının bakımını sağlar. Bu hesaplamaların tamamı, size sunulmadan önce yerel DNS çözümleyici IP düzeyinde birleştirilir. Bilgileri çeşitli yollarla kullanabilirsiniz.

Trafik görünümü veri güncelleştirme sıklığı, birden çok iç hizmet değişkenine bağlıdır. Ancak, veriler her 24 saatte bir güncelleştirilir.

>[!NOTE]
>Trafik Görünümü açıklanan gecikme süresi, son kullanıcı ile bağlı oldukları Azure bölgeleri arasında temsili bir gecikme süresi ve DNS arama gecikmesi değildir. Trafik Görünümü, yerel DNS çözümleyici ile sorgunun yönlendirildiği Azure bölgesi arasındaki gecikme süresini en iyi şekilde tahmin etmek için yeterli veri yoksa, döndürülen gecikme null olur. 

## <a name="visual-overview"></a>Görsel genel bakış

Traffic Manager sayfanızdaki **trafik görünümü** bölümüne gittiğinizde, trafik görünümü öngörülerinin yer aldığı bir coğrafi harita görürsünüz. Eşleme, Traffic Manager profilinizin kullanıcı tabanı ve uç noktaları hakkında bilgi sağlar.

![Traffic Manager Trafik Görünümü coğrafi görünüm][1]

### <a name="user-base-information"></a>Kullanıcı temel bilgileri

Konum bilgilerinin kullanılabildiği yerel DNS çözümleyicilerine yönelik olarak haritada gösterilirler. DNS Çözümleyicisinin rengi, Traffic Manager sorguları için bu DNS çözümleyicisini kullanan son kullanıcıların yaşadığı ortalama gecikme süresini gösterir.

Haritada bir DNS çözümleyici konumunun üzerine geldiğinizde şunu gösterir:
- DNS Çözümleyicisinin IP adresi
- Traffic Manager tarafından görülen DNS sorgu trafiği hacmi
- uç nokta ve DNS Çözümleyicisi arasında bir çizgi olarak DNS çözümleyicisinden gelen trafiğin yönlendirildiği uç noktalar 
- Bu konumdan, birbirine bağlanan çizginin rengi olarak temsil edilen ortalama gecikme bitiş noktası

### <a name="endpoint-information"></a>Uç nokta bilgileri

Uç noktaların bulunduğu Azure bölgeleri, haritada mavi noktalar olarak gösterilir. Uç noktanız dış ise ve kendisine bir Azure bölgesi eşlemesi yoksa, eşlemenin en üstünde gösterilir. Trafiğin bu uç noktaya yönlendirildiği farklı konumları (kullanılan DNS Çözümleyicisi temelinde) görmek için herhangi bir uç nokta seçin. Bağlantılar, uç nokta ve DNS çözümleyici konumu arasında bir çizgi olarak gösterilir. Bu çift arasındaki temsilci gecikmesine göre renklendirilir. Uç noktanın adını ve çalıştığı Azure bölgesini görebilirsiniz. Bu Traffic Manager profili tarafından kendisine yönlendirilme isteklerinin toplam hacmi de görüntülenir.


## <a name="tabular-listing-and-raw-data-download"></a>Tablosal listeleme ve ham veri yükleme

Trafik Görünümü verilerini Azure portal tablo biçiminde görüntüleyebilirsiniz. Her DNS çözümleyici IP/uç nokta çifti için şunu gösteren bir giriş vardır:

* DNS Çözümleyicisinin IP adresi.
* Ad.
* Uç noktanın bulunduğu Azure bölgesinin coğrafi konumu (varsa).
* Bu DNS Çözümleyicisinin ilgili uç noktayla ilişkili isteklerin hacmi.
* Bu DNS kullanılarak son kullanıcılarla ilişkili temsilci gecikmesi (varsa). 

Trafik Görünümü verilerini, tercih ettiğiniz analiz iş akışının bir parçası olarak kullanılabilecek bir CSV dosyası olarak da indirebilirsiniz.

## <a name="billing"></a>Faturalandırma

Trafik Görünümü kullandığınızda, sunulan öngörüleri oluşturmak için kullanılan veri noktası sayısına göre faturalandırılırsınız. Şu anda kullanılan tek veri noktası türü Traffic Manager profilinize göre alınan sorgulardır. Fiyatlandırma hakkında daha fazla bilgi için [Traffic Manager fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/traffic-manager/)ziyaret edin.

## <a name="faqs"></a>SSS

* [Trafik Görünümü ne yapar?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Trafik Görünümü kullanmanın avantajlarından faydalanabilirsiniz.](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Azure izleyici aracılığıyla kullanılabilen Traffic Manager ölçümlerinden farklı Trafik Görünümü nedir?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Trafik Görünümü EDNS Istemci alt ağ bilgilerini kullanır mi?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Kaç gün veri Trafik Görünümü kullanır?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Trafik Görünümü dış uç noktaları nasıl işler?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [Aboneliğimde her profil için Trafik Görünümü etkinleştirmem gerekir mi?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Trafik Görünümü nasıl kapatırım?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Trafik Görünümü Faturalandırma nasıl çalışır?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Sonraki adımlar

- [Traffic Manager nasıl çalıştığını](traffic-manager-overview.md) öğrenin
- Traffic Manager tarafından desteklenen [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md) hakkında daha fazla bilgi edinin
- [Traffic Manager profili oluşturmayı](./quickstart-create-traffic-manager-profile.md) öğrenin

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png