---
title: Yeniden yönlendirme URI/yanıt URL 'SI kısıtlamaları ve sınırlamalar-Microsoft Identity platform
description: Yanıt URL 'Leri/yeniden yönlendirme URL kısıtlamaları & sınırlamaları
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cc6ab0342682bce7befdfe412221ec581312be
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389591"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Yeniden yönlendirme URI’si/yanıt URL’si kısıtlamaları ve sınırlamaları

Bir yeniden yönlendirme URI 'si veya yanıt URL 'SI, uygulama başarıyla yetkilendirildiğinde ve yetkilendirme kodu ya da erişim belirteci verildiğinde, yetkilendirme sunucusunun kullanıcıyı kullanıcıya göndereceği konumdur. Kod veya belirteç yeniden yönlendirme URI 'SI veya Yanıt belirtecinde bulunur, bu nedenle doğru konumu uygulama kayıt işleminin bir parçası olarak kaydetmeniz önemlidir.

## <a name="maximum-number-of-redirect-uris"></a>Maksimum yeniden yönlendirme URI sayısı

Aşağıdaki tabloda, uygulamanızı kaydettiğinizde ekleyebileceğiniz en fazla yeniden yönlendirme URI sayısı gösterilmektedir.

| Oturum açan hesaplar | Maksimum yeniden yönlendirme URI sayısı | Açıklama |
|--------------------------|---------------------------------|-------------|
| Herhangi bir kuruluşun Azure Active Directory (Azure AD) kiracısındaki Microsoft iş veya okul hesapları | 256 | uygulama bildirimindeki `signInAudience` alanı *Azureadmyorg* ya da *Azureadmultipleorgs* olarak ayarlandı |
| Kişisel Microsoft hesapları ve iş ve okul hesapları | 100 | uygulama bildirimindeki `signInAudience` alanı *Azureadandpersonmicrosoftaccount* olarak ayarlandı |

## <a name="maximum-uri-length"></a>En fazla URI uzunluğu

Bir uygulama kaydına eklediğiniz her bir yeniden yönlendirme URI 'SI için en fazla 256 karakter kullanabilirsiniz.

## <a name="supported-schemes"></a>Desteklenen düzenler
Azure AD uygulama modeli, Microsoft iş veya okul hesaplarında herhangi bir kuruluşun Azure Active Directory (Azure AD) kiracısında oturum açmasını sağlayan uygulamalar için hem HTTP hem de HTTPS düzenlerini destekler. Uygulama bildirimindeki `signInAudience` alanı *Azureadmyorg* veya *Azureadmultipleorgs*olarak ayarlanır. Kişisel Microsoft hesaplarında ve iş ve okul hesaplarında oturum açılan uygulamalar (`signInAudience` ' ı *Azureadandpersonmicrosoftaccount*olarak ayarlanmıştır) yalnızca https şemasına izin verilir.

> [!NOTE]
> Yeni [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyimi, geliştiricilerin kullanıcı arabiriminde http düzenine sahip URI 'leri eklemesine izin vermez. İş veya okul hesaplarında oturum açma uygulamaları için HTTP URI 'Leri eklemek yalnızca uygulama bildirimi Düzenleyicisi aracılığıyla desteklenir. İleri giderek, yeni uygulamalar yeniden yönlendirme URI 'sinde HTTP şemaları kullanamaz. Ancak, yeniden yönlendirme URI 'Lerinde HTTP şemaları içeren eski uygulamalar çalışmaya devam edecektir. Geliştiricilerin yeniden yönlendirme URI 'Lerinde HTTPS şemaları kullanması gerekir.

## <a name="restrictions-using-a-wildcard-in-uris"></a>URI 'Ler içinde bir joker karakter kullanan kısıtlamalar

@No__t-0 gibi joker karakter URI 'Leri kullanışlıdır, ancak kaçınılmalıdır. Yeniden yönlendirme URI 'sinde joker karakter kullanılması güvenlik etkilerine sahiptir. OAuth 2,0 belirtimine göre ([RFC 6749 ' nin Bölüm 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2)), bir yeniden yönlendirme uç noktası URI 'si mutlak bir URI olmalıdır. 

Azure AD uygulama modeli, kişisel Microsoft hesaplarında ve iş veya okul hesaplarında oturum açmak üzere yapılandırılan uygulamalar için joker karakter URI 'Lerini desteklemez. Ancak, günümüzde bir kuruluşun Azure AD kiracısında iş veya okul hesaplarında oturum açmak üzere yapılandırılan uygulamalar için joker karakter URI 'Lerinde izin verilir. 
 
> [!NOTE]
> Yeni [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyimi, geliştiricilerin kullanıcı arabirimine joker URI 'ler eklemesine izin vermez. İş veya okul hesaplarında oturum açmak için yalnızca uygulama bildirimi Düzenleyicisi aracılığıyla desteklenen uygulamalar için solcard URI ekleme desteklenir. İleri giderek yeni uygulamalar, yeniden yönlendirme URI 'sinde joker karakterler kullanamaz. Ancak, yeniden yönlendirme URI 'Lerinde joker karakterler içeren eski uygulamalar çalışmaya devam edecektir.

Senaryonuz, izin verilen en yüksek sınırdan daha fazla yeniden yönlendirme URI 'si gerektiriyorsa, joker karakter yeniden yönlendirme URI 'si eklemek yerine aşağıdaki yaklaşımlardan birini göz önünde bulundurun.

### <a name="use-a-state-parameter"></a>Bir durum parametresi kullanın

Bir dizi alt etki alanı varsa ve senaryonuz, başarıyla başlatıldığı sayfada kullanıcıları yeniden yönlendirmenizi gerektiriyorsa, bir durum parametresinin kullanılması faydalı olabilir. 

Bu yaklaşımda:

1. Yetkilendirme uç noktasından aldığınız güvenlik belirteçlerini işlemek için uygulama başına bir "paylaşılan" yeniden yönlendirme URI 'SI oluşturun.
1. Uygulamanız, durum parametresindeki uygulamaya özgü parametreleri (kullanıcının kaynaklandığı veya marka bilgileri gibi) uygulamaya özgü parametreler gönderebilir. Bir durum parametresi kullanırken, [10,12 ' ın RFC 6749 bölümünde](https://tools.ietf.org/html/rfc6749#section-10.12)belirtildiği gibi CSRF korumasına karşı koruma yapın. 
1. Uygulamaya özgü parametreler, uygulamanın kullanıcı için doğru deneyimi işlemesi için gereken tüm bilgileri ve diğer bir deyişle, uygun uygulama durumunu içerir. Azure AD yetkilendirme uç noktası, durum parametresinden HTML 'yi şeritler, bu nedenle bu parametreye HTML içeriği geçirdiğinizden emin olun.
1. Azure AD, "paylaşılan" yeniden yönlendirme URI 'sine bir yanıt gönderdiğinde, durum parametresini uygulamaya geri gönderir.
1. Daha sonra uygulama, kullanıcının daha fazla gönderileceği URL 'YI belirleyebilmek için durum parametresindeki değeri kullanabilir. CSRF koruması için doğrulama yaptığınızdan emin olun.

> [!NOTE]
> Bu yaklaşım, güvenliği aşılmış bir istemcinin durum parametresinde gönderilen ek parametreleri değiştirmesine olanak tanır. böylece Kullanıcı, RFC 6819 ' de açıklanan [Açık yeniden yönlendirici tehdidi](https://tools.ietf.org/html/rfc6819#section-4.2.4) olan farklı bir URL 'ye yeniden yönlendirme sağlar. Bu nedenle, istemci, durumu şifreleyerek veya yeniden yönlendirme URI 'sindeki etki alanı adını belirtece karşı doğrulamak gibi bazı yollarla bu parametreleri korumalıdır.

### <a name="add-redirect-uris-to-service-principals"></a>Hizmet sorumlularına yeniden yönlendirme URI 'Leri ekleme

Diğer bir yaklaşım, herhangi bir Azure AD kiracısında uygulama kaydınızı temsil eden [hizmet sorumlularına](app-objects-and-service-principals.md#application-and-service-principal-relationship) yeniden yönlendirme URI 'leri eklemektir. Bu yaklaşımı, bir durum parametresini kullanmınızda, sizin destekledikleri her yeni kiracı için uygulama kaydınıza yeni yeniden yönlendirme URI 'Leri eklemenizi gerektirdiğinde kullanabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama bildirimi](reference-app-manifest.md) hakkında bilgi edinin
