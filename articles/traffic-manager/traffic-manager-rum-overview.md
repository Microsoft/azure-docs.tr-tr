---
title: Azure Traffic Manager Gerçek Kullanıcı Ölçümleri
description: Bu giriş bölümünde Azure Traffic Manager Gerçek Kullanıcı Ölçümleri nasıl çalıştığını öğrenin.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183719"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager Gerçek Kullanıcı Ölçümleri’ne genel bakış

Performans yönlendirme yöntemini kullanmak için bir Traffic Manager profili ayarladığınızda, hizmet DNS sorgu isteklerinin nereden geldiğini arar ve bu talep eden istekleri en düşük gecikme süresine sahip Azure bölgesine yönlendirmeye yönelik yönlendirme kararları verir. Bu, farklı Son Kullanıcı ağları için Traffic Manager koruduğu ağ gecikmesi zekası kullanılarak gerçekleştirilir.

Gerçek Kullanıcı Ölçümleri, Azure bölgelerine, son kullanıcılarınızın kullandığı istemci uygulamalarından gelen ağ gecikmesi ölçümlerini ölçmenizi Traffic Manager ve yönlendirme kararları verirken bu bilgileri de göz önünde bulundurmanızı sağlar. Gerçek Kullanıcı Ölçümleri kullanmayı seçtiğinizde, son kullanıcılarınızın bulunduğu ağlardan gelen isteklerin yönlendirmesinin doğruluğunu artırabilirsiniz. 

## <a name="how-real-user-measurements-work"></a>Gerçek Kullanıcı Ölçümleri nasıl çalışır?

Gerçek Kullanıcı Ölçümleri, istemci uygulamalarınızın kullanıldıkları Son Kullanıcı ağlarından görüldüğü gibi Azure bölgelerine gecikme süresi ölçerek çalışır. Örneğin, farklı konumlardaki (örneğin, Kuzey Amerika bölgelerinde) kullanıcılar tarafından erişilen bir Web sayfanız varsa, bunları sunucu uygulamanızın barındırıldığı en iyi Azure bölgesine almak için performans yönlendirme yöntemiyle birlikte Gerçek Kullanıcı Ölçümleri kullanabilirsiniz.

Web sayfalarınıza bir Azure tarafından sunulan JavaScript (benzersiz bir anahtar ile) katıştırarak başlatılır. Bu işlem yapıldıktan sonra, Kullanıcı Web sayfasını ziyaret ettiğinde JavaScript sorguları, ölçmesi gereken Azure bölgeleri hakkında bilgi almak için Traffic Manager. Hizmet, bu bölgeleri, bu Azure bölgelerinde barındırılan tek bir piksel görüntüsünü indirerek ve isteğin gönderildiği zaman ile ilk baytın alındığı zaman arasındaki gecikme süresini belirterek, betiğe daha sonra bu alanları ardışık olarak ölçen bir uç nokta kümesi döndürür. Bu ölçümler daha sonra Traffic Manager hizmetine geri bildirilir.

Zaman içinde, bu, son kullanıcılarınızın bulunduğu ağların gecikme süresi özellikleriyle ilgili daha doğru bilgi almak Traffic Manager için birçok kez ve birçok ağ üzerinde önde gelir. Bu bilgiler Traffic Manager tarafından yapılan yönlendirme kararlarında yer almaya başlar. Sonuç olarak, gönderilen Gerçek Kullanıcı Ölçümleri göre bu kararların doğruluğu artar.

Gerçek Kullanıcı Ölçümleri kullandığınızda, Traffic Manager gönderilen ölçüm sayısına göre faturalandırılırsınız. Fiyatlandırma hakkında daha fazla bilgi için [Traffic Manager fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/traffic-manager/)ziyaret edin.

## <a name="faqs"></a>SSS

* [Gerçek Kullanıcı Ölçümleri kullanmanın avantajları nelerdir?](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [Azure olmayan bölgelerle Gerçek Kullanıcı Ölçümleri kullanabilir miyim?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [Gerçek Kullanıcı Ölçümleri hangi yönlendirme yöntemi avantajları?](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [Her profili ayrı Gerçek Kullanıcı Ölçümleri etkinleştirmem gerekir mi?](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Nasıl yaparım? Aboneliğim için Gerçek Kullanıcı Ölçümleri devre dışı bırakmak istiyor musunuz?](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Gerçek Kullanıcı Ölçümleri Web sayfaları dışındaki istemci uygulamalarla kullanabilir miyim?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Gerçek Kullanıcı Ölçümleri etkinleştirilmiş Web sayfası her işlendiğinde kaç ölçüm yapıldı?](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Web sayfamda Gerçek Kullanıcı Ölçümleri betiği çalışmadan önce bir gecikme var mı?](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Gerçek Kullanıcı Ölçümleri yalnızca ölçmek istediğim Azure bölgeleriyle kullanabilir miyim?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Belirli bir numaraya yapılan ölçümlerin sayısını sınırlayabilir miyim?](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [İstemci uygulamamın Gerçek Kullanıcı Ölçümleri bir parçası olarak alınan ölçümleri görebilir miyim?](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Traffic Manager tarafından sunulan ölçüm betiğini değiştirebilir miyim?](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Başkalarının Gerçek Kullanıcı Ölçümleri ile kullandığım anahtarı görmesi mümkün olacaktır mi?](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Diğerleri RUM anahtarımı kötüye kullanabilir mi?](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [Ölçüm JavaScript 'ı tüm Web sayfalarıma yerleştirmem gerekir mi?](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Gerçek Kullanıcı Ölçümleri kullanırsam son kullanıcılarım hakkındaki bilgiler Traffic Manager tarafından tanımlanabilir mi?](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Gerçek Kullanıcı Ölçümleri Web sayfasının yönlendirme için Traffic Manager kullanıyor olması gerekiyor mu?](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Azure bölgelerinde Gerçek Kullanıcı Ölçümleri kullanmak için herhangi bir hizmeti barındırmalıyım miyim?](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Gerçek Kullanıcı Ölçümleri kullandığım zaman Azure bant genişliği kullanımı artar mi?](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Sonraki adımlar
- [Gerçek Kullanıcı ölçümleri Web sayfalarıyla](traffic-manager-create-rum-web-pages.md) kullanmayı öğrenin
- [Traffic Manager nasıl çalıştığını](traffic-manager-overview.md) öğrenin
- [Mobile Center](/mobile-center/) hakkında daha fazla bilgi edinin
- Traffic Manager tarafından desteklenen [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md) hakkında daha fazla bilgi edinin
- [Traffic Manager profili oluşturmayı](./quickstart-create-traffic-manager-profile.md) öğrenin