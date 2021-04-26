---
title: Traffic Manager ile birden fazla uç nokta arasında yük devretme
titleSuffix: Azure Content Delivery Network
description: Azure Traffic Manager kullanarak birden çok Azure Content Delivery Network uç noktası arasında yük devretmeyi yapılandırmayı öğrenin.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a003becba0bc1e42d8fe0c0c5b199402a430a8e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034767"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Azure Traffic Manager ile birden fazla uç nokta arasında yük devretme

Azure Content Delivery Network (CDN) yapılandırdığınızda, gereksinimlerinize göre en uygun sağlayıcıyı ve fiyatlandırma katmanını seçebilirsiniz. 

Azure CDN, genel olarak dağıtılmış altyapıyla, hizmet kullanılabilirliğini ve performansını geliştirmek için varsayılan olarak yerel ve coğrafi artıklık ve küresel Yük Dengeleme oluşturur. 

İçerik hizmeti için bir konum kullanılamazsa, istekler otomatik olarak başka bir konuma yönlendirilir. Her istemci isteğine hizmeti sağlamak için en iyi varlık noktası (POP) kullanılır. Otomatik yönlendirme, istek konumu ve sunucu yükü olarak faktörleri temel alır.
 
Birden çok CDN profiliniz varsa Azure Traffic Manager ile kullanılabilirliği ve performansı daha da artırabilirsiniz. 

Birden çok CDN uç noktası arasında dengeyi yük dengelemek için Azure Traffic Manager Azure CDN kullanın:
 
* Yük devretme
* Coğrafi Yük Dengeleme 

Tipik bir yük devretme senaryosunda, tüm istemci istekleri birincil CDN profiline yönlendirilir. 

Profil kullanılamıyorsa, istekler ikincil profile yönlendirilir.  İstekler, yeniden çevrimiçi olduğunda birincil profilinize karşı sürdürülür.

Azure Traffic Manager 'yi bu şekilde kullanmak Web uygulamanızın her zaman kullanılabilir olmasını sağlar. 

Bu makalede, ' den profillerin yük devretmesini yapılandırma hakkında yönergeler ve bir örnek verilmiştir: 

* **Verizon 'ten standart Azure CDN**
* **Akamai 'ten standart Azure CDN**

**Microsoft 'tan Azure CDN** de desteklenir.

## <a name="create-azure-cdn-profiles"></a>Azure CDN profilleri oluşturma
Farklı sağlayıcılarla iki veya daha fazla Azure CDN profili ve uç noktası oluşturun.

1. İki CDN profili oluşturun:
    * **Verizon 'ten standart Azure CDN**
    * **Akamai 'ten standart Azure CDN** 

    [Yeni BIR CDN profili oluşturma](cdn-create-new-endpoint.md#create-a-new-cdn-profile)bölümündeki adımları izleyerek profilleri oluşturun.
 
   ![CDN birden çok profil](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Yeni profillerin her birinde [Yeni BIR CDN uç noktası oluşturma](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)bölümündeki adımları izleyerek en az bir uç nokta oluşturun.

## <a name="create-traffic-manager-profile"></a>Traffic Manager profili oluşturma
Azure Traffic Manager profili oluşturun ve CDN uç noktalarınızda yük dengelemeyi yapılandırın. 

1. [Traffic Manager profili oluşturma](../traffic-manager/quickstart-create-traffic-manager-profile.md)bölümündeki adımları Izleyerek bir Azure Traffic Manager profili oluşturun. 

    * **Yönlendirme yöntemi**, **Öncelik**' i seçin.

2. [Traffic Manager uç noktaları ekleme](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints) bölümündeki adımları izleyerek TRAFFIC Manager profilinize CDN uç noktalarınızı ekleyin

    * **Yazın**, **dış uç noktaları**' nı seçin.
    * **Öncelik**, bir sayı girin.

    Örneğin, önceliği **2** olan **1** ve **cdndemo101verizon.azureedge.net** ile **cdndemo101akamai.azureedge.net** oluşturun.

   ![CDN Traffic Manager uç noktaları](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Azure CDN ve Azure 'da özel etki alanı yapılandırma Traffic Manager
CDN ve Traffic Manager profillerinizi yapılandırdıktan sonra, DNS eşlemesi eklemek ve CDN uç noktalarına özel etki alanını kaydetmek için aşağıdaki adımları izleyin. Bu örnekte, özel etki alanı adı **cdndemo101.dustydogpetcare.Online**' dir.

1. GoDaddy gibi özel etki alanının etki alanı sağlayıcısı için Web sitesine gidin ve iki DNS CNAME girişi oluşturun. 

    a. İlk CNAME girişi için, cdnverify alt etki alanı ile özel etki alanınızı CDN uç noktanıza eşleyin. Bu giriş, adım 2 ' de Traffic Manager eklenen CDN uç noktasına özel etki alanını kaydetmek için gerekli bir adımdır.

      Örnek: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. İkinci CNAME girdisi için, cdnverify alt etki alanı olmadan özel etki alanınızı CDN uç noktanıza eşleyin. Bu giriş, Traffic Manager özel etki alanını eşler. 

      Örnek: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Etki alanınız Şu anda etkin ve kesintiye uğrarsa, bu adımı son yapın. Özel etki alanı DNS 'nizi Traffic Manager için güncelleştirmeden önce CDN uç noktaları ve Traffic Manager etki alanlarının canlı olduğunu doğrulayın.
    >
   
    > [!NOTE]
    > Bu yük devretmek için, her iki uç noktanın farklı profillerde olması gerekir ve etki alanı adı çakışmalarını önlemek için farklı profillerin farklı CDN sağlayıcısı tarafından olması gerekir.
    > 

2.  Azure CDN profilinizde, ilk CDN uç noktasını (Akamai) seçin. **Özel etki alanı Ekle** ve giriş **cdndemo101.dustydogpetcare.Online**' ı seçin. Özel etki alanını doğrulama onay işareti yeşil olduğunu doğrulayın. 

    Azure CDN, bu kayıt işlemini tamamlaması için DNS eşlemesini doğrulamak üzere **cdnverify** alt etki alanını kullanır. Daha fazla bilgi için bkz. [CNAME DNS kaydı oluşturma](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Bu adım Azure CDN, isteklerine yanıt verebilmeleri için özel etki alanını tanımasını sağlar.
    
    > [!NOTE]
    > Akamai profillerinden bir **Azure CDN** TLS etkinleştirmek için, özel etki alanı için uç noktanıza doğrudan CNAME uygulamanız gerekir. TLS etkinleştirmek için cdnverify henüz desteklenmiyor. 
    >

3.  Özel etki alanının etki alanı sağlayıcısı için Web sitesine geri dönün. Oluşturduğunuz ilk DNS eşlemesini güncelleştirin. Özel etki alanını ikinci CDN uç noktanızla eşleyin.
                             
    Örnek: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Azure CDN profilinizde ikinci CDN uç noktasını (Verizon) seçin ve 2. adımı tekrarlayın. **Özel etki alanı Ekle**' yi seçin ve **cdndemo101.dustydogpetcare.Online** girin.
 
Bu adımları tamamladıktan sonra, yük devretme özelliklerine sahip çoklu CDN hizmetiniz Azure Traffic Manager ile yapılandırılır. 

Özel etki alanından test URL 'Lerine erişebilirsiniz. 

İşlevselliği test etmek için, birincil CDN uç noktasını devre dışı bırakın ve isteğin ikincil CDN uç noktasına doğru şekilde taşındığını doğrulayın. 

## <a name="next-steps"></a>Sonraki adımlar
Yükü farklı CDN uç noktaları arasında dengelemek için coğrafi gibi diğer yönlendirme yöntemlerini yapılandırabilirsiniz. 

Daha fazla bilgi için bkz. [Traffic Manager kullanarak coğrafi trafik yönlendirme yöntemini yapılandırma](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).
