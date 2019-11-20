---
title: Azure portal yeni Uygulama kayıtları deneyimine Uygulama kayıtları (eski) geçiş için eğitim kılavuzu
description: Azure portal yeni uygulama kayıt deneyimine giriş
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01d543262a9eb358643c0860b24ac4306d2c5edf
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927087"
---
# <a name="transitioning-from-app-registrations-legacy-to-the-new-app-registrations-experience-in-the-azure-portal"></a>Uygulama kayıtları (eski) 'den yeni Uygulama kayıtları deneyimine geçiş yapılıyor Azure portal

Azure portal yeni [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde birçok geliştirme bulabilirsiniz. Azure portal Uygulama kayıtları (eski) deneyim hakkında bilgi sahibiyseniz, yeni deneyimi kullanmaya başlamak için bu eğitim kılavuzunu kullanın.

Azure Active Directory, burada açıklanan yeni uygulama kayıt deneyimi genel kullanıma sunulmuştur (GA). Azure Active Directory B2C (Azure AD B2C) ' de bu deneyim önizlemededir.

## <a name="key-changes"></a>Anahtar değişiklikleri

- Uygulama kayıtları, bir *Web uygulaması/API 'si* ya da *Yerel* bir uygulama ile sınırlı değildir. İlgili yeniden yönlendirme URI 'Lerini kaydederek bu uygulamalar için aynı uygulama kaydını kullanabilirsiniz.

- Eski deneyim yalnızca kuruluş (Azure AD) hesaplarını kullanarak oturum açmak için desteklenen uygulamalar. Uygulamalar tek kiracılı olarak kaydedildi. Uygulamalar yalnızca uygulamanın kaydedildiği dizinden kurumsal hesapları destekliyordu. Uygulamalar çok kiracılı olacak şekilde değiştirilebilir ve tüm kurumsal hesaplar desteklenir. Yeni deneyim, bu seçeneklerin yanı sıra üçüncü bir seçenek de destekleyebilen uygulamalar kaydetmenizi sağlar: tüm kurumsal hesaplar ve kişisel Microsoft hesapları.

- Eski deneyim yalnızca kurumsal hesap kullanılarak Azure portal oturum açmışsa kullanılabilir. Yeni deneyimle, bir dizinle ilişkili olmayan kişisel Microsoft hesaplarını kullanabilirsiniz.

## <a name="list-of-applications"></a>Uygulama listesi

Yeni uygulama listesi, Azure portal eski uygulama kayıtları deneyimi aracılığıyla kaydedilmiş uygulamaları gösterir. Bu uygulamalar, Azure AD hesaplarını kullanarak oturum açın. Yeni uygulama listesi ayrıca uygulama kayıt portalı 'nda kayıtlı olan uygulamaları da gösterir. Bu uygulamalar, Azure AD ve kişisel Microsoft hesaplarını kullanarak oturum açın.

>[!NOTE]
>Uygulama kayıt portalı kullanım dışı bırakıldı.

Tek bir uygulama kaydı birçok türde olabileceğinden, yeni uygulama listesinin bir **uygulama türü** sütunu yoktur. Listede iki ek sütun vardır: **üzerinde oluşturulur** ve **Sertifikalar &** . **Sertifikalar & gizlilikler** , uygulamada kayıtlı olan kimlik bilgilerinin durumunu gösterir. Durumlar arasında **geçerli**, **süresi dolmak üzere**ve **süresi sona erer**.

## <a name="new-app-registration"></a>Yeni uygulama kaydı

Eski deneyimde, sağlamanız gereken bir uygulamayı kaydetmek için: **ad**, **uygulama türü**ve **oturum açma URL 'SI/yeniden yönlendirme URI 'si**. Oluşturulan uygulamalar Azure AD yalnızca tek kiracılı uygulamalardır. Bunlar yalnızca uygulamanın kaydedildiği dizinden kurumsal hesapları destekliyordu.

Yeni deneyimde uygulama için bir **ad** belirtmeniz ve **Desteklenen hesap türlerini**seçmeniz gerekir. İsteğe bağlı olarak bir **yeniden yönlendirme URI 'si**sağlayabilirsiniz. Yeniden yönlendirme URI 'SI sağlarsanız, bunun Web/genel (mobil ve Masaüstü) olup olmadığını belirtmeniz gerekir. Daha fazla bilgi için bkz. [hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme](quickstart-register-app.md). Azure AD B2C için bkz. [bir uygulamayı Azure Active Directory B2C kaydetme](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Uygulama kayıt portalı ve Uygulama kayıtları sayfası arasındaki farklar

### <a name="the-legacy-properties-page"></a>Eski Özellikler sayfası

Eski deneyim bir **Özellikler** sayfasına sahipti. **Özellikler** aşağıdaki alanlara sahipti:

- **Adı**
- **Nesne KIMLIĞI**
- **Uygulama KIMLIĞI**
- **Uygulama Kimliği URI'si**
- **Le**
- **Giriş sayfası URL 'SI**
- **Oturum kapatma URL 'SI**
- **Hizmet koşulları URL 'SI**
- **Gizlilik bildirimi URL 'SI**
- **Uygulama türü**
- **Çok kiracılı**

Yeni deneyim bu sayfaya sahip değildir. Eşdeğer işlevselliği bulabileceğiniz yer:

- **Ad**, **logo**, **giriş sayfası URL**'si, **hizmet koşulları URL 'si**ve **Gizlilik bildirimi URL 'si** artık uygulamanın **marka** sayfasıdır.
- **Nesne kimliği** ve **uygulama (Istemci) kimliği** **genel bakış** sayfamı.
- Eski deneyimdeki **çok kiracılı** geçiş tarafından denetlenen Işlevsellik, **kimlik doğrulama** sayfasındaki **Desteklenen hesap türleriyle** değiştirilmiştir. Daha fazla bilgi için bkz. [hızlı başlangıç: bir uygulama tarafından desteklenen hesapları değiştirme](quickstart-modify-supported-accounts.md).
- **Oturum kapatma URL 'si** Şu anda **kimlik doğrulama** sayfasıdır.
- **Uygulama türü** artık geçerli bir alan değil. Bunun yerine, **kimlik doğrulama** sayfasında bulabileceğiniz ve hangi uygulama türlerinin desteklendiğini belirleyen URI 'leri yeniden yönlendirin.
- Uygulama **KIMLIĞI URI** 'Si artık **uygulama kimliği URI 'si** olarak adlandırıldığından **API 'yi açığa çıkarmak**için bulabilirsiniz. Eski deneyimde, bu özellik şu biçim kullanılarak tekrar kaydettirildi: `https://{tenantdomain}/{appID}`, örneğin `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. Yeni deneyimde `api://{appID}`olarak yeniden oluşturulur, ancak açıkça kaydedilmesi gerekir. Azure AD B2C kiracılarda, `https://{tenantdomain}/{appID}` biçimi hala kullanılmaktadır.

### <a name="reply-urlsredirect-urls"></a>Yanıt URL 'Leri/yeniden yönlendirme URL 'Leri

Eski deneyimde bir uygulamada bir **yanıt URL** 'si sayfası vardı. Yeni deneyimde bir uygulamanın **kimlik doğrulama** sayfasında yanıt URL 'leri bulunabilir. Bunlar artık **yeniden yönlendirme URI 'leri**olarak adlandırılır.

Yeniden yönlendirme URI 'Leri biçimi değişti. Bunların Web veya ortak bir uygulama türüyle ilişkilendirilmesi gerekir. Güvenlik nedenleriyle, *http://localhost* dışında joker karakterler ve `http://` şemaları desteklenmez.

### <a name="keyscertificates--secrets"></a>Anahtarlar/sertifikalar & gizli dizileri

Eski deneyimde bir uygulamada **anahtarlar** sayfası vardı. Yeni deneyimde **sertifikalar & gizli**dizi olarak yeniden adlandırılmıştır.

**Ortak anahtarlara** artık **sertifika**denir. **Parolalar** artık **istemci gizli**dizileri olarak adlandırılır.

### <a name="required-permissionsapi-permissions"></a>Gerekli izinler/API izinleri

Eski deneyimde bir uygulamada **gerekli izinler** sayfası vardı. Yeni deneyimde, **API izinleri**olarak yeniden adlandırılmıştır.

Eski deneyimde bir API seçtiğinizde, Microsoft API 'lerinin küçük bir listesinden seçim yapabilirsiniz. Ayrıca, Kiracıdaki hizmet sorumluları arasında arama yapabilirsiniz. Yeni deneyimde birden çok sekmeden birini seçebilirsiniz: **Microsoft API**'leri, **Kuruluşumun kullandığı API 'Ler**veya **API 'lerim**. **API 'lerim** üzerindeki arama çubuğu, Kiracıdaki hizmet sorumluları aracılığıyla sekme aramalarını kullanır.

> [!NOTE]
> Uygulamanız bir kiracı ile ilişkili değilse bu sekmeyi görmezsiniz. İzin isteme hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Web API 'lerine erişmek için istemci uygulaması yapılandırma](quickstart-configure-app-access-web-apis.md).

Eski deneyim, **istenen izinler** sayfasının en üstünde **izin ver** düğmesine sahipti. Yeni deneyimde, **Izin verme** sayfasında, bir uygulamanın **API izinleri** bölümünde **yönetici izni verme** düğmesi vardır. Düğmeler işlevinin bazı farklılıkları da vardır.

Eski deneyimde, mantık oturum açan kullanıcıya ve istenmekte olan izinlere bağlı olarak değişir. Mantık:

- Yalnızca Kullanıcı onaylamasına izin isteniyorsa ve oturum açan kullanıcı yönetici değilse, kullanıcı istenen izinler için Kullanıcı izni verebilir.
- Yönetici onayı gerektiren en az bir izin isteniyorsa ve oturum açan kullanıcı yönetici değilse, Kullanıcı izin vermeye çalışırken bir hata ile karşılaştı.
- Oturum açmış olan Kullanıcı bir yöneticiydi, istenen tüm izinler için yönetici onayı verildi.

Yeni deneyimde yalnızca bir yönetici izin verebilir. Bir yönetici **yönetici onayı ver**' i seçtiğinde, istenen tüm izinlere yönetici izni verilir.

## <a name="deleting-an-app-registration"></a>Uygulama kaydını silme

Eski deneyimde yalnızca tek kiracılı uygulamaları silebilirsiniz. Çok kiracılı uygulamalar için Sil düğmesi devre dışı bırakıldı. Yeni deneyimde, uygulamaları herhangi bir durumda silebilirsiniz, ancak eylemi onaylamanız gerekir. Daha fazla bilgi için bkz. [hızlı başlangıç: Microsoft Identity platformu ile kaydedilmiş bir uygulamayı kaldırma](quickstart-remove-app.md).

## <a name="application-manifest"></a>Uygulama bildirimi

Eski ve yeni deneyimler, bildirim düzenleyicisinde JSON biçimi için farklı sürümler kullanır. Daha fazla bilgi için bkz. [uygulama bildirimi Azure Active Directory](reference-app-manifest.md).

## <a name="new-ui"></a>Yeni Kullanıcı arabirimi

Yeni deneyim, aşağıdaki özellikler için UI denetimleri ekler:

- **Kimlik doğrulama** sayfasında **örtük verme akışı** (`oauth2AllowImplicitFlow`) vardır. Eski deneyimden farklı olarak, **erişim belirteçlerini** veya **kimlik belirteçlerini**veya her ikisini de etkinleştirebilirsiniz.
- **BIR API 'Yi kullanıma** sunma sayfası, bu apı (`oauth2Permissions`) ve **yetkilendirilmiş istemci uygulamaları** (`preAuthorizedApplications`) **tarafından tanımlanan kapsamları** içerir. Bir uygulamayı Web API 'SI olarak yapılandırma ve izinleri/kapsamları kullanıma sunma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Web API 'leri göstermek için uygulama yapılandırma](quickstart-configure-app-expose-web-apis.md).
- **Marka** sayfası **Yayımcı etki alanını**içerir. Yayımcı etki alanı kullanıcılara [uygulamanın izin isteminde](application-consent-experience.md)görüntülenir. Daha fazla bilgi için bkz. [nasıl yapılır: uygulamanın yayımcı etki alanını yapılandırma](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Sınırlamalar

Yeni deneyim aşağıdaki sınırlamalara sahiptir:

- İstemci gizli dizileri (Uygulama parolaları) biçimi, eski deneyimden farklı ve CLı 'yi bozabilir.
- Desteklenen hesapların değerini değiştirmek Kullanıcı arabiriminde desteklenmez. Azure AD tek kiracılı ve çok kiracılı bir şekilde geçiş yapmadığınız takdirde uygulama bildirimini kullanmanız gerekir.
