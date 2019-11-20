---
title: Öncelik trafiği yönlendirmeyi Yapılandırma-Azure Traffic Manager
description: Bu makalede, Traffic Manager ' de öncelik trafiği yönlendirme yönteminin nasıl yapılandırılacağı açıklanmaktadır
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: f9954c7733c30efaea4a74e04949556a3a8617fd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040345"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Traffic Manager 'de öncelik trafiği yönlendirme yöntemini yapılandırma

Azure Web siteleri, Web sitesi modundan bağımsız olarak bir veri merkezindeki (bölge olarak da bilinir) Web siteleri için yük devretme işlevselliği sunmaktadır. Traffic Manager, farklı veri merkezlerinde Web siteleri için yük devretme sağlar.

Hizmet yük devretmesi için ortak bir model, bir birincil hizmete trafik göndermek ve yük devretme için aynı yedekleme Hizmetleri kümesi sağlamaktır. Aşağıdaki adımlarda, bu öncelikli yük devretmeyi Azure bulut hizmetleri ve Web siteleri ile yapılandırma açıklanmaktadır:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Öncelik trafiği yönlendirme yöntemini yapılandırmak için

1. Bir tarayıcıdan [Azure portalında](https://portal.azure.com) oturum açın. Henüz bir hesabınız yoksa, [bir aylık ücretsiz denemeye](https://azure.microsoft.com/free/) kaydolabilirsiniz. 
2. Portalın arama çubuğunda **Traffic Manager profillerini** arayın ve ardından yönlendirme yöntemini yapılandırmak istediğiniz profil adına tıklayın.
3. **Traffic Manager profili** dikey penceresinde, yapılandırmanıza dahil etmek istediğiniz bulut hizmetlerinin ve Web sitelerinin mevcut olduğunu doğrulayın.
4. **Ayarlar** bölümünde **yapılandırma**' ya tıklayın ve **yapılandırma** dikey penceresinde aşağıdaki gibi tamamlanır:
    1. **Trafik yönlendirme yöntemi ayarları**için trafik yönlendirme yönteminin **öncelikli**olduğunu doğrulayın. Aksi takdirde, açılan listeden **Öncelik** ' e tıklayın.
    2. Bu profildeki tüm uç noktaları için aynı **uç nokta izleyici ayarlarını** aşağıdaki şekilde ayarlayın:
        1. Uygun **Protokolü**seçin ve **bağlantı noktası** numarasını belirtin. 
        2. **Yol** türü için eğik çizgi */* . Uç noktaları izlemek için bir yol ve dosya adı belirtmeniz gerekir. Eğik çizgi "/", göreli yol için geçerli bir giriştir ve dosyanın kök dizinde (varsayılan) olduğunu gösterir.
        3. Sayfanın üst kısmında **Kaydet**' e tıklayın.
5. **Ayarlar** bölümünde **uç noktalar**' a tıklayın.
6. **Uç noktalar** dikey penceresinde, uç noktalarınız için öncelik sırasını gözden geçirin. **Öncelik** trafiği yönlendirme yöntemini seçtiğinizde, seçilen uç noktaların sırası önemlidir. Uç noktaların öncelik sırasını doğrulayın.  Birincil uç nokta en üstte. Görüntülenme sırasını iki kez kontrol edin. Tüm istekler ilk uç noktaya yönlendirilir ve Traffic Manager sağlıksız olduğunu algılarsa trafik otomatik olarak sonraki uç noktaya devreder. 
7. Uç nokta önceliği sırasını değiştirmek için uç noktaya tıklayın ve görüntülenen **uç nokta** dikey penceresinde **Düzenle** ' ye tıklayın ve **Öncelik** değerini gerektiği şekilde değiştirin. 
8. Kayıt uç noktası ayarlarını değiştirmek için **Kaydet** ' e tıklayın.
9. Yapılandırma değişikliklerinizi tamamladıktan sonra sayfanın alt kısmındaki **Kaydet** ' e tıklayın.
10. Yapılandırmanızda yaptığınız değişiklikleri aşağıdaki gibi test edin:
    1.  Portalın arama çubuğunda Traffic Manager profili adını arayın ve görüntülenen sonuçlarda Traffic Manager profiline tıklayın.
    2.  **Traffic Manager** profili dikey penceresinde **Genel Bakış ' a**tıklayın.
    3.  **Traffic Manager profili** dikey penceresi, yeni oluşturduğunuz TRAFFIC Manager profilinizin DNS adını görüntüler. Bu, yönlendirme türü tarafından belirlendiği şekilde doğru uç noktaya yönlendirilmek için herhangi bir istemci tarafından (örneğin, bir Web tarayıcısı kullanılarak gezinilirken) kullanılabilir. Bu durumda, tüm istekler ilk uç noktaya yönlendirilir ve Traffic Manager sağlıksız olduğunu algılarsa trafik otomatik olarak sonraki uç noktaya devreder.
11. Traffic Manager profiliniz çalışmaya başladıktan sonra, yetkili DNS sunucunuzdaki DNS kaydını düzenleyerek şirket etki alanı adınızı Traffic Manager etki alanı adına getirin.

![Traffic Manager kullanarak öncelik trafiği yönlendirme yöntemini yapılandırma][1]

## <a name="next-steps"></a>Sonraki adımlar


- [Ağırlıklı trafik yönlendirme yöntemi](traffic-manager-configure-weighted-routing-method.md) hakkında bilgi edinin.
- [Performans yönlendirme yöntemi](traffic-manager-configure-performance-routing-method.md)hakkında bilgi edinin.
- [Coğrafi yönlendirme yöntemi](traffic-manager-configure-geographic-routing-method.md) hakkında bilgi edinin.
- [Traffic Manager ayarlarını test](traffic-manager-testing-settings.md)etme hakkında bilgi edinin.

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png