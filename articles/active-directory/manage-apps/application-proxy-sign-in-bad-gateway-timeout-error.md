---
title: Uygulama proxy 'Si uygulamasıyla bu kurumsal uygulama hatasına erişilemiyor
description: Azure AD Uygulama Ara Sunucusu uygulamalarıyla ilgili yaygın erişim sorunlarını çözme.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b9fb68643c48c685194fa7ba1f1e5050d2d3cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254940"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Uygulama proxy 'Si uygulaması kullanılırken "Bu kurumsal uygulamaya erişilemiyor" hatası

Bu makale, bir Azure AD Uygulama Ara Sunucusu uygulamasında "Bu şirket uygulamasına erişilemiyor" hatası için sık karşılaşılan sorunları gidermenize yardımcı olur.

## <a name="overview"></a>Genel Bakış

Bu hatayı gördüğünüzde hata sayfasında durum kodunu bulun. Bu kod büyük olasılıkla aşağıdaki durum kodlarından biridir:

- **Ağ geçidi zaman aşımı**: uygulama proxy 'si hizmeti bağlayıcıya ulaşamıyor. Bu hata genellikle bağlayıcı atama, bağlayıcının kendisi veya bağlayıcı etrafındaki ağ kuralları ile ilgili bir sorun olduğunu gösterir.
- **Hatalı ağ geçidi**: bağlayıcı, arka uç uygulamasına ulaşamıyor. Bu hata uygulamanın bir yanlış yapılandırılması olduğunu gösteriyor olabilir.
- **Yasak**: Kullanıcı uygulamaya erişim yetkisine sahip değil. Bu hata, Kullanıcı Azure Active Directory uygulamaya atanmadığı zaman ya da arka uçta kullanıcının uygulamaya erişim izni olmadığında meydana gelebilir.

Kodu bulmak için, "durum kodu" alanı için hata iletisinin sol alt kısmındaki metne bakın. Ayrıca sayfanın en altında yer alarak ek ipuçları olup olmadığına bakın.

![Örnek: ağ geçidi zaman aşımı hatası](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Bu hataların asıl nedeni ile ilgili sorunların nasıl giderileceği ve önerilen düzeltmeler hakkında daha fazla ayrıntı hakkında ayrıntılar için aşağıdaki ilgili bölüme bakın.

## <a name="gateway-timeout-errors"></a>Ağ geçidi zaman aşımı hataları

Hizmet bağlayıcıya ulaşmaya çalıştığında ve zaman aşımı penceresinde bağlantı kuramadığınızda, bir ağ geçidi zaman aşımı oluşur. Bu hata genellikle, çalışma bağlayıcıları olmayan bir bağlayıcı grubuna atanan bir uygulama veya bağlayıcı için gereken bazı bağlantı noktaları açık değildir.

## <a name="bad-gateway-errors"></a>Hatalı ağ geçidi hataları

Hatalı ağ geçidi hatası, bağlayıcının arka uç uygulamasına ulaşamayacağını gösterir. doğru uygulamayı yayımladığınızdan emin olun. Bu hataya neden olan yaygın hatalar şunlardır:

- İç URL 'de bir yazım hatası veya hata
- Uygulamanın kökü yayınlanmıyor. Örneğin, yayınlama `http://expenses/reimbursement` ancak erişmeye çalışılıyor `http://expenses`
- Kerberos kısıtlanmış temsili (KCD) yapılandırmasıyla ilgili sorunlar
- Arka uç uygulamasıyla ilgili sorunlar

## <a name="forbidden-errors"></a>Yasak hatalar

Yasak bir hata görürseniz, Kullanıcı uygulamaya atanmaz. Bu hata Azure Active Directory veya arka uç uygulamasında olabilir.

Azure 'da uygulamaya Kullanıcı atamayı öğrenmek için [yapılandırma belgelerine](application-proxy-add-on-premises-application.md#test-the-application)bakın.

Kullanıcının Azure 'da uygulamaya atandığını onaylamanız durumunda, arka uç uygulamasındaki Kullanıcı yapılandırmasını kontrol edin. Kerberos kısıtlı temsilcisi/tümleşik Windows kimlik doğrulaması kullanıyorsanız, yönergeler için KCD sorun giderme sayfasına bakın.

## <a name="check-the-applications-internal-url"></a>Uygulamanın iç URL 'sini denetleyin

İlk hızlı adım olarak, uygulamayı **Kurumsal uygulamalar** aracılığıyla açıp ardından **uygulama proxy 'si** menüsünü seçerek iç URL 'yi iki kez kontrol edin ve onarın. Uygulamaya erişmek için şirket içi ağınızdan kullanılan dahili URL 'nin olduğunu doğrulayın.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Uygulamanın çalışan bir bağlayıcı grubuna atandığını denetleyin

Uygulamanın çalışan bir bağlayıcı grubuna atandığını doğrulamak için:

1. Uygulamayı portalda açın **Azure Active Directory**, **Kurumsal uygulamalar**' a ve ardından **tüm uygulamalar** ' a tıklayın. Uygulamayı açın, sonra sol menüden **uygulama proxy 'si** ' ni seçin.
1. Bağlayıcı grubu alanına bakın. Grupta etkin bağlayıcı yoksa bir uyarı görürsünüz. Herhangi bir uyarı görmüyorsanız, [gerekli tüm bağlantı noktalarına](application-proxy-add-on-premises-application.md) izin verildiğini doğrulamak için üzerine gidin.
1. Yanlış bağlayıcı grubu gösteriyorsa, doğru grubu seçmek için açılan eklentiyi kullanın ve artık herhangi bir uyarı görmediğini onaylayın. Hedeflenen bağlayıcı grubu gösteriyorsa, sayfayı bağlayıcı yönetimi ile açmak için uyarı iletisine tıklayın.
1. Buradan daha fazla ayrıntıya geçmek için birkaç yol vardır:

   - Etkin bir bağlayıcıyı gruba taşıma: Bu gruba ait olması ve hedef arka uç uygulamasına bir görüş satırı olması gereken etkin bir bağlayıcınız varsa, bağlayıcıyı atanan gruba taşıyabilirsiniz. Bunu yapmak için bağlayıcıya tıklayın. "Bağlayıcı grubu" alanında, doğru grubu seçmek için açılan eklentiyi kullanın ve Kaydet ' e tıklayın.
   - Bu grup için yeni bir bağlayıcı indirin: Bu sayfadan [Yeni bir bağlayıcı indirme](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)bağlantısını alabilirsiniz. Bağlayıcıyı, arka uç uygulamasına doğrudan görüş satırı olan bir makineye yükler. Genellikle bağlayıcı, uygulamayla aynı sunucuya yüklenir. Hedef makineye bir bağlayıcı indirmek için bağlayıcıyı İndir bağlantısını kullanın. Ardından, bağlayıcıya tıklayın ve sağ gruba ait olduğundan emin olmak için "bağlayıcı grubu" açılan simgesini kullanın.
   - Etkin olmayan bir bağlayıcıyı araştırın: bir bağlayıcının etkin değil olarak gösterdiği, hizmete ulaşılamıyor. Bu hata genellikle, bazı gerekli bağlantı noktalarının engellenmesi nedeniyle yapılır. Bu sorunu gidermek için, gerekli tüm bağlantı noktalarına izin verildiğini doğrulamak için üzerine gidin.

Uygulamanın çalışan bağlayıcılarla bir gruba atandığından emin olmak için bu adımları kullandıktan sonra, uygulamayı yeniden test edin. Hala çalışmıyorsa sonraki bölüme geçin.

## <a name="check-all-required-ports-are-open"></a>Tüm gerekli bağlantı noktalarının açık olduğundan emin olun

Tüm gerekli bağlantı noktalarının açık olduğunu doğrulayın. Gerekli bağlantı noktaları için, öğreticinin açık bağlantı noktaları bölümüne bakın [: Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md). Gerekli tüm bağlantı noktaları açıksa sonraki bölüme geçin.

## <a name="check-for-other-connector-errors"></a>Diğer bağlayıcı hatalarını denetle

Yukarıdaki adımlardan hiçbiri sorunu çözmezse, bir sonraki adım bağlayıcının kendisiyle ilgili sorunları veya hataları aramaya yöneliktir. [Sorun giderme belgesinde](./application-proxy-troubleshoot.md#connector-errors)bazı yaygın hataları görebilirsiniz.

Ayrıca, hataları belirlemek için doğrudan bağlayıcı günlüklerine bakabilirsiniz. Hata iletilerinin birçoğu düzeltmeler için belirli öneriler paylaşır. Günlükleri görüntülemek için [Bağlayıcılar belgelerine](application-proxy-connectors.md#under-the-hood)bakın.

## <a name="additional-resolutions"></a>Ek çözümler

Yukarıdaki sorun sorunu çözmezse, olası birkaç nedeni olabilir. Sorunu belirlemek için:

Uygulamanız tümleşik Windows kimlik doğrulaması (ıWA) kullanmak üzere yapılandırıldıysa, uygulamayı çoklu oturum açma olmadan test edin. Aksi takdirde, sonraki paragrafa geçin. Çoklu oturum açma olmadan uygulamayı denetlemek için, uygulamanızı **Kurumsal uygulamalar** aracılığıyla açın ve **Çoklu oturum açma** menüsüne gidin. "Tümleşik Windows kimlik doğrulaması" olan açılan eklentiyi "Azure AD çoklu oturum açma devre dışı" olarak değiştirin.

Şimdi bir tarayıcı açın ve uygulamaya yeniden erişmeyi deneyin. Kimlik doğrulaması yapmanız ve uygulamaya girmeniz istenir. Kimlik doğrulaması yapabilirseniz, sorun, çoklu oturum açmayı sağlayan Kerberos kısıtlanmış temsili (KCD) yapılandırmasıyla aynıdır. Daha fazla bilgi için bkz. KCD sorun giderme sayfası.

Hatayı görmeye devam ederseniz, bağlayıcının yüklendiği makineye gidin, bir tarayıcı açın ve uygulama için kullanılan iç URL 'ye ulaşmaya çalışın. Bağlayıcı aynı makineden başka bir istemci gibi davranır. Uygulamaya erişemiyorsanız, bu makinenin uygulamaya erişemediğini araştırın veya uygulamaya erişebilen bir sunucuda bir bağlayıcı kullanın.

Uygulamaya bu makineden ulaşabilseniz, bağlayıcının kendisiyle ilgili sorunları veya hataları aramak için. [Sorun giderme belgesinde](application-proxy-troubleshoot.md#connector-errors)bazı yaygın hataları görebilirsiniz. Ayrıca, hataları belirlemek için doğrudan bağlayıcı günlüklerine bakabilirsiniz. Hata iletilerimizin birçoğu düzeltmeler için daha belirgin öneriler paylaşabilir. Günlükleri görüntülemeyi öğrenmek için [Bağlayıcılar belgelerimize](application-proxy-connectors.md#under-the-hood)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](application-proxy-connectors.md)