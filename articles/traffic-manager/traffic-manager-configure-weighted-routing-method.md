---
title: Ağırlıklı hepsini bir kez deneme trafik yönlendirmeyi Yapılandırma-Azure Traffic Manager
description: Bu makalede, Traffic Manager ' de bir hepsini bir kez deneme yöntemi kullanarak trafiğin yükünü dengelemek açıklanmaktadır
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 0bfed558ec8db0ef715dad044c3965c1b1d8052b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040327"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Traffic Manager ağırlıklı trafik yönlendirme yöntemini yapılandırma

Ortak trafik yönlendirme yöntemi, bulut hizmetleri ve Web siteleri dahil olmak üzere aynı uç noktalar kümesi sağlamaktır ve her eşit trafiği eşit bir şekilde gönderir. Aşağıdaki adımlarda, bu tür trafik yönlendirme yönteminin nasıl yapılandırılacağı ana hatlarıyla gösterilmiştir.

> [!NOTE]
> Azure Web App, bir Azure bölgesindeki (birden çok veri merkezini kapsayan) Web siteleri için hepsini bir kez deneme Yük Dengeleme işlevi sunmaktadır. Traffic Manager, farklı veri merkezlerinde Web siteleri arasında trafik dağıtmanıza izin verir.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Ağırlıklı trafik yönlendirme yöntemini yapılandırmak için

1. Bir tarayıcıdan [Azure portalında](https://portal.azure.com) oturum açın. Henüz bir hesabınız yoksa, [bir aylık ücretsiz denemeye](https://azure.microsoft.com/free/) kaydolabilirsiniz. 
2. Portalın arama çubuğunda **Traffic Manager profillerini** arayın ve ardından yönlendirme yöntemini yapılandırmak istediğiniz profil adına tıklayın.
3. **Traffic Manager profili** dikey penceresinde, yapılandırmanıza dahil etmek istediğiniz bulut hizmetlerinin ve Web sitelerinin mevcut olduğunu doğrulayın.
4. **Ayarlar** bölümünde **yapılandırma**' ya tıklayın ve **yapılandırma** dikey penceresinde aşağıdaki gibi tamamlanır:
    1. **Trafik yönlendirme yöntemi ayarları**için trafik yönlendirme yönteminin **ağırlıklı**olduğunu doğrulayın. Değilse, açılan listeden **ağırlıklı** ' a tıklayın.
    2. Bu profildeki tüm uç noktaları için aynı **uç nokta izleyici ayarlarını** aşağıdaki şekilde ayarlayın:
        1. Uygun **Protokolü**seçin ve **bağlantı noktası** numarasını belirtin. 
        2. **Yol** türü için eğik çizgi */* . Uç noktaları izlemek için bir yol ve dosya adı belirtmeniz gerekir. Eğik çizgi "/", göreli yol için geçerli bir giriştir ve dosyanın kök dizinde (varsayılan) olduğunu gösterir.
        3. Sayfanın üst kısmında **Kaydet**' e tıklayın.
5. Yapılandırmanızda yaptığınız değişiklikleri aşağıdaki gibi test edin:
    1.  Portalın arama çubuğunda Traffic Manager profili adını arayın ve görüntülenen sonuçlarda Traffic Manager profiline tıklayın.
    2.  **Traffic Manager** profili dikey penceresinde **Genel Bakış ' a**tıklayın.
    3.  **Traffic Manager profili** dikey penceresi, yeni oluşturduğunuz TRAFFIC Manager profilinizin DNS adını görüntüler. Bu, yönlendirme türü tarafından belirlendiği şekilde doğru uç noktaya yönlendirilmek için herhangi bir istemci tarafından (örneğin, bir Web tarayıcısı kullanılarak gezinilirken) kullanılabilir. Bu durumda tüm istekler her bitiş noktasını hepsini bir kez kez yönlendirilir.
6. Traffic Manager profiliniz çalışmaya başladıktan sonra, yetkili DNS sunucunuzdaki DNS kaydını düzenleyerek şirket etki alanı adınızı Traffic Manager etki alanı adına getirin.

![Traffic Manager kullanarak ağırlıklı trafik yönlendirme yöntemini yapılandırma][1]

## <a name="next-steps"></a>Sonraki adımlar

- [Öncelik trafiği yönlendirme yöntemi](traffic-manager-configure-priority-routing-method.md)hakkında bilgi edinin.
- [Performans trafiği yönlendirme yöntemi](traffic-manager-configure-performance-routing-method.md)hakkında bilgi edinin.
- [Coğrafi yönlendirme yöntemi](traffic-manager-configure-geographic-routing-method.md) hakkında bilgi edinin.
- [Traffic Manager ayarlarını test](traffic-manager-testing-settings.md)etme hakkında bilgi edinin.

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
