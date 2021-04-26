---
title: Azure Traffic Manager kullanarak performans trafiği yönlendirme yöntemini yapılandırma | Microsoft Docs
description: Bu makalede, trafiği en düşük gecikme süresine sahip uç noktaya yönlendirmek üzere Traffic Manager nasıl yapılandırılacağı açıklanmaktadır
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994919"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Performans trafiği yönlendirme yöntemini yapılandırma

Performans trafiği yönlendirme yöntemi, trafiği uç noktaya yönlendirmenizi sağlar ve istemcinin ağındaki en düşük gecikme süresine sahip olursunuz. Genellikle en düşük gecikme süresine sahip veri merkezi, coğrafi uzaklığın en yakınsıdır. Bu trafik yönlendirme yöntemi, ağ yapılandırmasındaki veya yükteki gerçek zamanlı değişiklikleri hesaba alamaz.

##  <a name="to-configure-performance-routing-method"></a>Performans yönlendirme yöntemini yapılandırmak için

1. Bir tarayıcıdan [Azure portalında](https://portal.azure.com) oturum açın. Henüz bir hesabınız yoksa, [bir aylık ücretsiz denemeye](https://azure.microsoft.com/free/) kaydolabilirsiniz. 
2. Portalın arama çubuğunda **Traffic Manager profillerini** arayın ve ardından yönlendirme yöntemini yapılandırmak istediğiniz profil adına tıklayın.
3. **Traffic Manager profili** dikey penceresinde, yapılandırmanıza dahil etmek istediğiniz bulut hizmetlerinin ve Web sitelerinin mevcut olduğunu doğrulayın.
4. **Ayarlar** bölümünde **yapılandırma**' ya tıklayın ve **yapılandırma** dikey penceresinde aşağıdaki gibi tamamlanır:
    1. **Trafik yönlendirme yöntemi ayarları** Için, **yönlendirme yöntemi** için **performans**' ı seçin.
    2. Bu profildeki tüm uç noktaları için aynı **uç nokta izleyici ayarlarını** aşağıdaki şekilde ayarlayın:
        1. Uygun **Protokolü** seçin ve **bağlantı noktası** numarasını belirtin. 
        2. **Yol** için eğik çizgi yazın */* . Uç noktaları izlemek için bir yol ve dosya adı belirtmeniz gerekir. Eğik çizgi "/", göreli yol için geçerli bir giriştir ve dosyanın kök dizinde (varsayılan) olduğunu gösterir.
        3. Sayfanın üst kısmında **Kaydet**' e tıklayın.
5.  Yapılandırmanızda yaptığınız değişiklikleri aşağıdaki gibi test edin:
    1.  Portalın arama çubuğunda Traffic Manager profili adını arayın ve görüntülenen sonuçlarda Traffic Manager profiline tıklayın.
    2.  **Traffic Manager** profili dikey penceresinde **Genel Bakış ' a** tıklayın.
    3.  **Traffic Manager profili** dikey penceresi, yeni oluşturduğunuz TRAFFIC Manager profilinizin DNS adını görüntüler. Bu, yönlendirme türü tarafından belirlendiği şekilde doğru uç noktaya yönlendirilmek için herhangi bir istemci tarafından (örneğin, bir Web tarayıcısı kullanılarak gezinilirken) kullanılabilir. Bu durumda tüm istekler, istemcinin ağındaki en düşük gecikme süresine sahip uç noktaya yönlendirilir.
6. Traffic Manager profiliniz çalışmaya başladıktan sonra, yetkili DNS sunucunuzdaki DNS kaydını düzenleyerek şirket etki alanı adınızı Traffic Manager etki alanı adına getirin.

![Traffic Manager kullanarak performans trafiği yönlendirme yöntemini yapılandırma][1]

## <a name="next-steps"></a>Sonraki adımlar

- [Ağırlıklı trafik yönlendirme yöntemi](traffic-manager-configure-weighted-routing-method.md) hakkında bilgi edinin.
- [Öncelik yönlendirme yöntemi](traffic-manager-configure-priority-routing-method.md) hakkında bilgi edinin.
- [Coğrafi yönlendirme yöntemi](traffic-manager-configure-geographic-routing-method.md) hakkında bilgi edinin.
- [Traffic Manager ayarlarını test](traffic-manager-testing-settings.md)etme hakkında bilgi edinin.

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png