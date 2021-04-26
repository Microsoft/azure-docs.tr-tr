---
title: Azure AD Uygulama Ara Sunucusu CORS sorunlarını anlama ve çözme
description: Azure AD Uygulama Ara Sunucusu CORS 'yi anlama ve CORS sorunlarını belirleme ve çözme hakkında bilgiler sağlar.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: b57fc7e3af99819c9b27b6bc796e501d1db02818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259177"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Azure Active Directory Uygulama Ara Sunucusu CORS sorunlarını anlama ve çözme

[Çıkış noktaları arası kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/) bazen Azure Active Directory uygulama ara sunucusu aracılığıyla yayımladığınız uygulamalar ve API 'ler için zorluk sunabilir. Bu makalede Azure AD Uygulama Ara Sunucusu CORS sorunları ve çözümleri açıklanmaktadır.

Tarayıcı güvenliği genellikle bir Web sayfasının başka bir etki alanına AJAX istekleri yapmasını engeller. Bu kısıtlamaya *aynı-Origin ilkesi* adı verilir ve kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını önler. Ancak, bazen diğer sitelerin Web API 'nizi çağırmasını sağlamak isteyebilirsiniz. CORS, bir sunucunun aynı kaynaklı ilkeyi daha rahat ve bazı çapraz kaynak isteklerine izin veren bir W3C standardıdır.

## <a name="understand-and-identify-cors-issues"></a>CORS sorunlarını anlayın ve belirler

Aynı şemalara, ana bilgisayarlara ve bağlantı noktalarına ([RFC 6454](https://tools.ietf.org/html/rfc6454)) sahip olmaları durumunda iki URL aynı kaynağa sahiptir:

-   http: \/ /contoso.com/foo.html
-   http: \/ /contoso.com/bar.html

Aşağıdaki URL 'Lerin önceki iki değerden farklı kaynakları vardır:

-   http: \/ /contoso.net-farklı etki alanı
-   http: \/ /contoso.com:9000/foo.html-farklı bağlantı noktası
-   https: \/ /contoso.com/foo.html-farklı düzen
-   http: \/ /www.contoso.com/foo.html-farklı alt etki alanı

Aynı kaynak ilkesi, doğru erişim denetimi üstbilgilerini kullanmadıkları takdirde uygulamaların diğer kaynaklardan kaynaklara erişmesini önler. CORS üst bilgileri eksik veya yanlış ise, çapraz kaynak istekleri başarısız olur. 

CORS sorunlarını tarayıcı hata ayıklama araçlarını kullanarak tanımlayabilirsiniz:

1. Tarayıcıyı başlatın ve web uygulamasına göz atın.
1. **F12** tuşuna basarak hata ayıklama konsolunu açın.
1. İşlemi yeniden oluşturmayı deneyin ve konsol iletisini gözden geçirin. CORS ihlali, kaynak hakkındaki konsol hatasını yeniden oluşturur.

Aşağıdaki ekran görüntüsünde, **TRY It** düğmesinin seçilmesi, https: \/ /corswebclient-contoso.msappproxy.net 'In erişim-denetim-izin-Origin üstbilgisinde BULUNAMADıĞıNı belirten bir CORS hata iletisine neden oldu.

![CORS sorunu](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Uygulama proxy 'Si ile CORS sorunları

Aşağıdaki örnekte, tipik bir Azure AD Uygulama Ara Sunucusu CORS senaryosu gösterilmektedir. İç sunucu **corswebservice** Web API denetleyicisi ve **corswebservice** çağıran bir **corswebclient** barındırır. **Corswebclient** 'Dan **corswebservice**'a yönelik bir AJAX isteği var.

![Şirket içi aynı kaynak isteği](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient uygulaması, şirket içinde barındırdığınızda çalışacaktır, ancak Azure AD Uygulama Ara Sunucusu aracılığıyla yayımlandığında yükleme veya hata giderme işlemleri başarısız olur. CORSWebClient ve CORSWebService uygulamalarını uygulama proxy 'Si aracılığıyla farklı uygulamalar olarak yayımladıysanız, iki uygulama farklı etki alanlarında barındırılır. CORSWebClient 'dan CORSWebService 'a yönelik bir AJAX isteği, bir çapraz kaynak isteği ve başarısız olur.

![Uygulama proxy CORS isteği](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Uygulama ara sunucusu CORS sorunları için çözümler

Yukarıdaki CORS sorununu çeşitli yollarla çözebilirsiniz.

### <a name="option-1-set-up-a-custom-domain"></a>Seçenek 1: özel bir etki alanı ayarlama

Uygulama kaynakları, kod veya başlıklarda herhangi bir değişiklik yapmak zorunda kalmadan aynı kaynaktan yayımlamak için Azure AD Uygulama Ara Sunucusu [özel etki alanını](./application-proxy-configure-custom-domain.md) kullanın. 

### <a name="option-2-publish-the-parent-directory"></a>Seçenek 2: üst dizini yayımlama

Her iki uygulamanın üst dizinini yayımlayın. Bu çözüm, Web sunucusunda yalnızca iki uygulamanız varsa oldukça iyi bir şekilde geçerlidir. Her uygulamayı ayrı ayrı yayımlamak yerine, aynı kaynak ile sonuçlanan ortak üst dizini yayımlayabilirsiniz.

Aşağıdaki örneklerde CORSWebClient uygulaması için Portal Azure AD Uygulama Ara Sunucusu sayfası gösterilmektedir.  **Iç URL** *contoso.com/CORSWebClient* olarak ayarlandığında, uygulama, cross-Origin olduklarından *contoso.com/CORSWebService* dizinine başarılı istekler yapamaz. 

![Uygulamayı tek tek Yayımla](./media/application-proxy-understand-cors-issues/image4.png)

Bunun yerine, hem *Corswebclient* hem de *corswebservice* dizinlerini içeren üst dizini yayımlamak için **İç URL 'yi** ayarlayın:

![Üst dizini Yayımla](./media/application-proxy-understand-cors-issues/image5.png)

Elde edilen uygulama URL 'Leri, CORS sorununu etkili bir şekilde çözümler:

- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebService
- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Seçenek 3: HTTP üstbilgilerini güncelleştirme

Kaynak isteğiyle eşleşmesi için Web hizmetine özel bir HTTP yanıt üst bilgisi ekleyin. Internet Information Services (IIS) içinde çalışan Web siteleri için, üstbilgiyi değiştirmek için IIS Yöneticisi 'Ni kullanın:

![IIS Yöneticisi 'nde özel yanıt üst bilgisi ekle](./media/application-proxy-understand-cors-issues/image6.png)

Bu değişiklik herhangi bir kod değişikliği gerektirmez. Bunu Fiddler izlerinde doğrulayabilirsiniz:

**Üst bilgi eklemeyi gönder**\
HTTP/1.1 200 TAMAM \
Cache-Control: No-Cache \
Pragma: No-Cache \
İçerik türü: metin/düz; charset = UTF-8 \
Süre sonu:-1 \
Farklılık: kabul-kodlama \
Sunucu: Microsoft-IIS/8.5 Microsoft-HTTPAPı/2.0 \
**Erişim-denetim-Izin-kaynak: https \: //corswebclient-contoso.msappproxy.net**\
X-AspNet-sürüm: 4.0.30319 \
X-destekli-by: ASP.NET \
İçerik-Uzunluk: 17

### <a name="option-4-modify-the-app"></a>4. seçenek: uygulamayı değiştirme

Erişim-denetim-Izin-kaynak üst bilgisini ve uygun değerleri ekleyerek, uygulamanızı CORS 'yi destekleyecek şekilde değiştirebilirsiniz. Üstbilgiyi ekleme yöntemi uygulamanın kod diline bağlıdır. Kodu değiştirmek en az önerilen seçenektir, çünkü en fazla çaba gerektirir.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Seçenek 5: erişim belirtecinin ömrünü uzatma

Uygulamanızın kimlik doğrulaması için *login.microsoftonline.com* 'e yeniden yönlendirildiği ve erişim belirtecinin süresi dolduğu gıbı bazı CORS sorunları çözülemez. CORS çağrısı başarısız olur. Bu senaryonun geçici çözümü, bir kullanıcının oturumu sırasında erişim belirtecinin süresinin dolmasını önlemek için belirtecin yaşam süresini uzatmaktır. Bunun nasıl yapılacağı hakkında daha fazla bilgi için bkz. [Azure AD 'de yapılandırılabilir belirteç yaşam süreleri](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Ayrıca bkz.
- [Öğretici: Azure Active Directory içindeki uygulama proxy 'Si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md) 
- [Azure AD Uygulama Ara Sunucusu dağıtımı planlama](application-proxy-deployment-plan.md) 
- [Azure Active Directory Uygulama Ara Sunucusu aracılığıyla şirket içi uygulamalara uzaktan erişim](application-proxy.md)