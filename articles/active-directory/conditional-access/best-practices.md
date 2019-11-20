---
title: Azure Active Directory 'de koşullu erişim için en iyi yöntemler | Microsoft Docs
description: Bilmeniz gerekenler ve koşullu erişim ilkelerini yapılandırırken yapmanız gerekenler hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b8402279b5c2717b1f73a28f2efc02ade5e479c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175771"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 'de koşullu erişim için en iyi yöntemler

[Azure Active Directory (Azure AD) koşullu erişim](../active-directory-conditional-access-azure-portal.md)ile, yetkili kullanıcıların bulut uygulamalarınıza nasıl erişdiğini denetleyebilirsiniz. Bu makalede hakkında bilgi verilmektedir:

- Bilmeniz gerekenler 
- Koşullu erişim ilkelerini yapılandırırken yapmaktan kaçının. 

Bu makalede, [Azure Active Directory ' de koşullu erişim nedir?](../active-directory-conditional-access-azure-portal.md) başlıklı kavramlar ve terminoloji hakkında bilgi sahibi olduğunuz varsayılır.

## <a name="whats-required-to-make-a-policy-work"></a>Bir ilkenin çalışması için gerekenler ne gerekir?

Yeni bir ilke oluşturduğunuzda, hiçbir Kullanıcı, Grup, uygulama veya erişim denetimi seçili değildir.

![Bulut uygulamaları](./media/best-practices/02.png)

İlkenizi çalışır hale getirmek için şunları yapılandırmanız gerekir:

| Nesne           | Oluşturulacağı                                  | Kaydol |
| :--            | :--                                  | :-- |
| **Bulut uygulamaları** |Bir veya daha fazla uygulama seçin.  | Koşullu erişim ilkesinin hedefi, yetkili kullanıcıların bulut uygulamalarına nasıl erişebileceğini denetlemenize olanak sağlamaktır.|
| **Kullanıcılar ve gruplar** | Seçtiğiniz bulut uygulamalarınıza erişim yetkisi olan en az bir kullanıcı veya grup seçin. | Hiçbir Kullanıcı ve grup atanmamış koşullu erişim ilkesi hiçbir şekilde tetiklenmez. |
| **Erişim denetimleri** | En az bir erişim denetimi seçin. | Koşullarınız karşılandıysanız, ilke işlemcinizin ne yapılacağını bilmeleri gerekir. |

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

### <a name="how-are-conditional-access-policies-applied"></a>Koşullu erişim ilkeleri nasıl uygulanır?

Bir bulut uygulamasına eriştiğinizde, birden fazla koşullu erişim ilkesi uygulanabilir. Bu durumda, uygulanan tüm ilkeler karşılanmalıdır. Örneğin, bir ilke MFA gerektiriyorsa ve ikincisi uyumlu bir cihaz gerektiriyorsa, MFA 'ya gitmeniz ve uyumlu bir cihaz kullanmanız gerekir. 

Tüm ilkeler iki aşamada zorlanır:

- **İlk** aşamada, tüm ilkeler değerlendirilir ve karşılanmayan tüm erişim denetimleri toplanır. 

- **İkinci** aşamada, karşılamadığınız gereksinimleri karşılamanız istenir. İlkelerden herhangi biri erişimi engellediyse, engellenir ve diğer ilke denetimlerini karşılamanız istenmez. İlkelerden hiçbiri sizi engelliyorsa, diğer ilke denetimlerini aşağıdaki sırayla karşılamanız istenir:

   ![Sipariş](./media/best-practices/06.png)
    
   Dış MFA sağlayıcıları ve kullanım koşulları bundan sonra gelir.

### <a name="how-are-assignments-evaluated"></a>Atamalar nasıl değerlendirilir?

Tüm **atamalar mantıksal olarak**da kullanılır. Birden çok atama yapılandırdıysanız, bir ilkenin tetiklenmesi için tüm atamaların karşılanması gerekir.  

Kuruluşunuzun ağı dışından yapılan tüm bağlantılara uygulanan bir konum koşulu yapılandırmanız gerekirse:

- **Tüm konumları** dahil et
- **Tüm güvenilen IP 'leri** Dışla

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Azure AD yönetici portalından kilitlediyseniz ne yapmanız gerekir?

Bir koşullu erişim ilkesinde yanlış bir ayar nedeniyle Azure AD portalından kilitlediyseniz:

- Denetim, kuruluşunuzda henüz engellenmeyen başka yöneticiler var. Azure portal erişimi olan bir yönetici, oturum açmanızı etkileyen ilkeyi devre dışı bırakabilir. 
- Kuruluşunuzdaki yöneticilerin hiçbiri ilkeyi güncelleştirebildiğinden, bir destek isteği göndermeniz gerekir. Microsoft desteği, erişimi engelleyen koşullu erişim ilkelerini gözden geçirebilir ve güncelleştirebilir.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Klasik Azure portalında ilkeler varsa ve Azure portal yapılandırılmışsa ne olur?  

Her iki ilke de Azure Active Directory tarafından zorlanır ve Kullanıcı yalnızca tüm gereksinimler karşılandığında erişim edinir.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Intune Silverlight portalında ve Azure portal ilkelerinize sahipseniz ne olur?

Her iki ilke de Azure Active Directory tarafından zorlanır ve Kullanıcı yalnızca tüm gereksinimler karşılandığında erişim edinir.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Aynı kullanıcı için birden çok ilke yapılandırılmışsa ne olur?  

Her oturum açma için, Azure Active Directory tüm ilkeleri değerlendirir ve kullanıcıya erişim verilmeden önce tüm gereksinimlerin karşılanmasını sağlar. Diğer tüm yapılandırma ayarları için baskındır yani erişimini engelleyin. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Koşullu erişim Exchange ActiveSync ile çalışıyor mu?

Evet, Exchange ActiveSync 'i koşullu erişim ilkesinde kullanabilirsiniz.

SharePoint Online ve Exchange Online gibi bazı bulut uygulamaları da eski kimlik doğrulama protokollerini destekler. Bir istemci uygulaması bir bulut uygulamasına erişmek için eski bir kimlik doğrulama protokolü kullanabilir, Azure AD bu erişim denemesinde koşullu erişim ilkesini zorunlu kılamaz. Bir istemci uygulamasının ilkelerin uygulanmasını atlamasını engellemek için, etkilenen bulut uygulamalarında yalnızca modern kimlik doğrulamayı etkinleştirip etkinleştiremeyeceğinizi denetlemeniz gerekir.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Office 365 uygulamalarıyla koşullu erişimi nasıl yapılandırmanız gerekir?

Office 365 uygulamaları birbirine bağlı olduğundan, ilke oluştururken yaygın olarak kullanılan uygulamaların birlikte atanmasını öneririz.

Yaygın bağlantılı uygulamalar Microsoft Flow, Microsoft Planner, Microsoft ekipleri, Office 365 Exchange Online, Office 365 SharePoint Online ve Office 365 Yammer içerir.

Erişim, bir oturumun veya görevin başlangıcında denetleniyorsa, çok faktörlü kimlik doğrulaması gibi kullanıcı etkileşimi gerektiren ilkeler için önemlidir. Aksi takdirde, kullanıcılar bir uygulama içindeki bazı görevleri tamamlayamaz. Örneğin, yönetilmeyen cihazlarda SharePoint 'e erişmek için çok faktörlü kimlik doğrulamasının olması, ancak e-postaya erişmesi gerekiyorsa, e-postasında çalışan kullanıcılar, bir iletiye SharePoint dosyaları iliştiremez. Makalede, [koşullu erişim Azure Active Directory hizmet bağımlılıkları nelerdir?](service-dependencies.md)makalesinde daha fazla bilgi bulunabilir.

## <a name="what-you-should-avoid-doing"></a>Yapmanız gerekenler

Koşullu erişim çerçevesi size harika bir yapılandırma esnekliği sağlar. Ancak harika esneklik, istenmeyen sonuçlara engel olmak için, her yapılandırma ilkesini serbest bırakmadan önce dikkatli bir şekilde incelemeniz gerektiği anlamına da gelir. Bu bağlamda, **tüm kullanıcılar/gruplar/bulut uygulamaları gibi tüm grupları**etkileyen atamalara özel dikkat etmeniz gerekir.

Ortamınızda aşağıdaki yapılandırmalardan kaçınmanız gerekir:

**Tüm kullanıcılar için tüm bulut uygulamaları:**

- **Erişimi engelle** -bu yapılandırma, kesinlikle iyi bir fikir olmayan tüm kuruluşunuzu engeller.
- **Uyumlu cihaz gerektir** -cihazlarını henüz kaydetmemiş kullanıcılar için, bu ilke Intune portalına erişim dahil tüm erişimi engeller. Kayıtlı bir cihaz olmadan yöneticisiyseniz, bu ilke ilkeyi değiştirmek için Azure portal geri almanızı engeller.
- **Etki alanına katılım gerektir** -Bu ilke bloğu erişimi, henüz etki alanına katılmış bir cihazınız yoksa kuruluşunuzdaki tüm kullanıcılar için erişimi engellemeyi de mümkün kılıyor.
- **Uygulama koruma Ilkesi gerektir** -Intune ilkeniz yoksa, bu ilke engelleme erişimi, kuruluşunuzdaki tüm kullanıcılar için erişimi engellemeyi de mümkün değildir. Bir Intune uygulama koruma ilkesine sahip bir istemci uygulaması olmadan yöneticisiyseniz, bu ilke Intune ve Azure gibi portallara geri alma işlemi uygulamanızı engeller.

**Tüm kullanıcılar, tüm bulut uygulamaları ve tüm cihaz platformları için:**

- **Erişimi engelle** -bu yapılandırma, kesinlikle iyi bir fikir olmayan tüm kuruluşunuzu engeller.

## <a name="how-should-you-deploy-a-new-policy"></a>Yeni bir ilkeyi nasıl dağıtmanız gerekir?

İlk adım olarak, ilkeyi [durum aracını](what-if-tool.md)kullanarak değerlendirmelisiniz.

Ortamınız için yeni ilkeler hazırlandığınızda, bunları aşamalar halinde dağıtın:

1. Bir ilkeyi küçük bir kullanıcı kümesine uygulayın ve beklendiği gibi davrandığını doğrulayın. 
1. Bir ilkeyi daha fazla Kullanıcı içerecek şekilde genişlettiğinizde. Hala erişime sahip olduklarından emin olmak için tüm yöneticileri ilkeden hariç tutmak ve bir değişiklik gerekliyse bir ilkeyi güncelleştirebilmeleri için devam edin.
1. Yalnızca gerekli olduğunda bir ilkeyi tüm kullanıcılara uygulayın. 

En iyi uygulama olarak, şu şekilde bir kullanıcı hesabı oluşturun:

- İlke yönetimine ayrılmıştır 
- Tüm ilkeleriniz hariç tutuldu

## <a name="policy-migration"></a>İlke geçişi

Azure portal oluşturduğunuz ilkeleri şu nedenle geçirmeyi düşünün:

- Artık, daha önce işleyemeyen senaryolara adres ekleyebilirsiniz.
- Bunları birleştirerek yönetmeniz gereken ilke sayısını azaltabilirsiniz.   
- Tüm koşullu erişim ilkelerinizi tek bir merkezi konumda yönetebilirsiniz.
- Klasik Azure portalı kullanımdan kaldırılmıştır.   

Daha fazla bilgi için, [Azure Portal klasik Ilkeleri geçirme](policy-migration.md)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Şunları bildirmek istiyorsanız:

- Koşullu erişim ilkesini yapılandırma, bkz. [koşullu erişim Azure Active Directory belirli uygulamalar IÇIN MFA gerektirme](app-based-mfa.md).
- Koşullu erişim ilkelerinizi nasıl planlayacaksanız, bkz. [Azure Active Directory Koşullu erişim dağıtımınızı nasıl planlayacaksanız](plan-conditional-access.md).
