---
title: Traffic Manager ile birden çok Azure CDN uç noktası arasında yük devretme
description: Azure Traffic Manager Azure CDN uç noktalarıyla ayarlama hakkında bilgi edinin.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: de91f61385942db077bc98721eabe9f3f0b8624c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083000"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Azure Traffic Manager birden çok Azure CDN uç noktası arasında yük devretmeyi ayarlama

Azure Content Delivery Network (CDN) yapılandırdığınızda, gereksinimlerinize göre en uygun sağlayıcıyı ve fiyatlandırma katmanını seçebilirsiniz. Azure CDN, genel olarak dağıtılmış altyapıyla, hizmet kullanılabilirliğini ve performansını geliştirmek için varsayılan olarak yerel ve coğrafi artıklık ve küresel Yük Dengeleme oluşturur. Bir konum içerik hizmeti için kullanılabilir değilse, istekler otomatik olarak başka bir konuma yönlendirilir ve her istemci isteğine hizmeti sağlamak için en iyi POP (istek konumu ve sunucu yükü gibi etkenlere bağlı olarak) kullanılır. 
 
Birden çok CDN profiliniz varsa Azure Traffic Manager ile kullanılabilirliği ve performansı daha da artırabilirsiniz. Yük devretme, coğrafi Yük Dengeleme ve diğer senaryolar için birden çok CDN uç noktası arasında dengeyi yük dengelemek için Azure Traffic Manager Azure CDN kullanabilirsiniz. Tipik bir yük devretme senaryosunda, tüm istemci istekleri öncelikle birincil CDN profiline yönlendirilir; profil kullanılamıyorsa, birincil CDN profiliniz yeniden çevrimiçi olana kadar istekler daha sonra ikincil CDN profiline geçirilir. Azure Traffic Manager 'yi bu şekilde kullanmak Web uygulamanızın her zaman kullanılabilir olmasını sağlar. 

Bu makalede, Verizon **Azure CDN ve Akamai profillerindeki standart** **Azure CDN Standart** ile yük devretmeyi ayarlama hakkında yönergeler ve bir örnek verilmektedir.

## <a name="set-up-azure-cdn"></a>Azure CDN ayarlama 
Farklı sağlayıcılarla iki veya daha fazla Azure CDN profili ve uç noktası oluşturun.

1. [Yeni BIR CDN profili oluşturma](cdn-create-new-endpoint.md#create-a-new-cdn-profile)bölümündeki adımları izleyerek Verizon **Azure CDN ve Akamai profilinden standart** bir **Azure CDN standardı** oluşturun.
 
   ![CDN birden çok profil](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Yeni profillerin her birinde [Yeni BIR CDN uç noktası oluşturma](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)bölümündeki adımları izleyerek en az bir uç nokta oluşturun.

## <a name="set-up-azure-traffic-manager"></a>Azure Traffic Manager ayarlama
Azure Traffic Manager profili oluşturun ve CDN uç noktalarınızda yük dengelemeyi ayarlayın. 

1. [Traffic Manager profili oluşturma](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)bölümündeki adımları Izleyerek bir Azure Traffic Manager profili oluşturun. 

    **Yönlendirme yöntemi**için **Öncelik**' i seçin.

2. [Traffic Manager uç noktaları ekleme](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints) bölümündeki adımları izleyerek TRAFFIC Manager profilinize CDN uç noktalarınızı ekleyin

    **Tür**için **dış uç noktalar**' ı seçin. **Öncelik**için bir sayı girin.

    Örneğin, önceliği *2*olan *1* ve *cdndemo101verizon.azureedge.net* ile *cdndemo101akamai.azureedge.net* oluşturun.

   ![CDN Traffic Manager uç noktaları](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Azure CDN ve Azure Traffic Manager için özel etki alanı ayarlama
CDN ve Traffic Manager profillerinizi ayarladıktan sonra, DNS eşlemesi eklemek ve CDN uç noktalarına özel etki alanını kaydetmek için aşağıdaki adımları izleyin. Bu örnekte, özel etki alanı adı *cdndemo101. alacatydogpetilgilen. Online*olur.

1. GoDaddy gibi özel etki alanının etki alanı sağlayıcısı için Web sitesine gidin ve iki DNS CNAME girişi oluşturun. 

    a. İlk CNAME girişi için, cdnverify alt etki alanı ile özel etki alanınızı CDN uç noktanıza eşleyin. Bu giriş, adım 2 ' de Traffic Manager eklenen CDN uç noktasına özel etki alanını kaydetmek için gerekli bir adımdır.

      Örneğin: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. İkinci CNAME girdisi için, cdnverify alt etki alanı olmadan özel etki alanınızı CDN uç noktanıza eşleyin. Bu giriş, Traffic Manager özel etki alanını eşler. 

      Örneğin: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Etki alanınız Şu anda etkin ve kesintiye uğrarsa, bu adımı son yapın. Özel etki alanı DNS 'nizi Traffic Manager için güncelleştirmeden önce CDN uç noktaları ve Traffic Manager etki alanlarının canlı olduğunu doğrulayın.
    >


2.  Azure CDN profilinizde, ilk CDN uç noktasını (Akamai) seçin. **Özel etki alanı Ekle** ve Input *cdndemo101. alacatydogpetilgilen. Online*öğesini seçin. Özel etki alanını doğrulama onay işareti yeşil olduğunu doğrulayın. 

    Azure CDN, bu kayıt işlemini tamamlaması için DNS eşlemesini doğrulamak üzere *cdnverify* alt etki alanını kullanır. Daha fazla bilgi için bkz. [CNAME DNS kaydı oluşturma](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Bu adım Azure CDN, isteklerine yanıt verebilmeleri için özel etki alanını tanımasını sağlar.
    
    > [!NOTE]
    > Akamai profillerindeki bir **Azure CDN** SSL 'yi etkinleştirmek için, özel etki alanı için uç noktanıza doğrudan CNAME uygulamanız gerekir. SSL 'yi etkinleştirmek için cdnverify henüz desteklenmiyor. 
    >

3.  Özel etki alanının etki alanı sağlayıcısı için Web sitesine geri dönün ve ' de oluşturduğunuz ilk DNS eşlemesini, özel etki alanının ikinci CDN uç noktanıza eşlenmesi için güncelleştirin.
                             
    Örneğin: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Azure CDN profilinizde ikinci CDN uç noktasını (Verizon) seçin ve 2. adımı tekrarlayın. **Özel etki alanı Ekle**ve Input *cdndemo101. alacatydogpetilgilen. Online*' ı seçin.
 
Bu adımları tamamladıktan sonra, yük devretme özelliklerine sahip çoklu CDN hizmetiniz Azure Traffic Manager ile ayarlanır. Özel etki alanından test URL 'Lerine erişebileceksiniz. İşlevselliği test etmek için, birincil CDN uç noktasını devre dışı bırakın ve isteğin ikincil CDN uç noktasına doğru şekilde taşındığını doğrulayın. 

## <a name="next-steps"></a>Sonraki adımlar
Ayrıca, yükü farklı CDN uç noktaları arasında dengelemek için coğrafi gibi diğer yönlendirme yöntemlerini de ayarlayabilirsiniz. Daha fazla bilgi için bkz. [Traffic Manager kullanarak coğrafi trafik yönlendirme yöntemini yapılandırma](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



