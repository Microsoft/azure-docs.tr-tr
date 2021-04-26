---
title: B2B işbirliği sınırlamaları-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B işbirliği için geçerli sınırlamalar
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4f960819aa208dcc8d3e476fc45a766452b612c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168959"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B işbirliğinin sınırlamaları
Azure Active Directory (Azure AD) B2B işbirliği Şu anda bu makalede açıklanan sınırlamalara tabidir.

## <a name="possible-double-multi-factor-authentication"></a>Olası çift Multi-Factor Authentication
Azure AD B2B ile, çok faktörlü kimlik doğrulamasını kaynak kuruluşta (kuruluşun davet eden) zorunlu kılabilirsiniz. Bu yaklaşımın nedenleri, [B2B işbirliği kullanıcıları Için koşullu erişim](conditional-access.md)bölümünde ayrıntılı olarak açıklanmıştır. Bir iş ortağının zaten çok faktörlü kimlik doğrulaması ayarlandıysa ve zorlanmışsa, kullanıcıların kimlik doğrulamasını ana kuruluşlarındaki bir kez ve daha sonra bir kez gerçekleştirmesi gerekebilir.

## <a name="instant-on"></a>Anında
B2B işbirliği akışlarında, kullanıcıları dizine ekler ve davet satın alma, uygulama ataması vb. sırasında bunları dinamik olarak güncelleştirir. Güncelleştirmeler ve yazma işlemleri normalde tek bir dizin örneğinde gerçekleşir ve tüm örneklerde çoğaltılmalıdır. Tüm örnekler güncelleştirildikten sonra çoğaltma tamamlanır. Bazen nesne bir örnekte yazıldığında veya güncelleştirilirse ve bu nesneyi almak için yapılan çağrı başka bir örneğe ise çoğaltma gecikmeleri oluşabilir. Bu durumda, yenileyin veya yardım için yeniden deneyin. API 'imizi kullanarak bir uygulama yazıyorsanız, bu sorunu giderebilmeniz için bazı geri denemelerle yeniden denemeler yapmak iyi bir savunma yöntemidir.

## <a name="azure-ad-directories"></a>Azure AD dizinleri
Azure AD B2B, Azure AD hizmeti dizin sınırlarına tabidir. Bir kullanıcının oluşturabileceğiniz dizin sayısı ve bir kullanıcının veya Konuk kullanıcının ait olduğu dizin sayısı hakkında daha fazla bilgi için bkz. [Azure AD hizmet limitleri ve kısıtlamaları](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="national-clouds"></a>Ulusal bulutlar
[Ulusal bulutlar](../develop/authentication-national-cloud.md) , Azure 'un fiziksel olarak yalıtılmış örnekleridir. B2B işbirliği Ulusal bulut sınırları genelinde desteklenmez. Örneğin, Azure kiracınız genel, genel bulutda ise, hesabı ulusal bir bulutta olan bir kullanıcıyı davet edebilirsiniz. Kullanıcıyla işbirliği yapmak için, başka bir e-posta adresi isteyin veya dizininizdeki kullanıcılar için bir üye kullanıcı hesabı oluşturun.

## <a name="azure-us-government-clouds"></a>Azure ABD kamu bulutları
Azure ABD kamu bulutu dahilinde B2B işbirliği, hem Azure ABD devlet bulutu dahilinde hem de B2B işbirliğinin desteklendiği kiracılar arasında desteklenir. B2B işbirliğini destekleyen Azure ABD kamu kiracılar, Microsoft veya Google hesapları kullanan sosyal kullanıcılarla da işbirliği yapabilir. Bir kullanıcıyı bu grupların dışında davet ederseniz (örneğin, Kullanıcı Azure ABD kamu bulutunun parçası olmayan veya henüz B2B işbirliğini desteklemiyorsa), davet başarısız olur veya Kullanıcı daveti kullanamaz. Diğer sınırlamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory Premium P1 ve P2 Çeşitlemeler](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>B2B işbirliğinin Azure ABD kamu kiracısında kullanılabilir olup olmadığını nasıl anlayabilirim?
Azure ABD kamu bulutu kiracınızın B2B işbirliğini destekleyip desteklemediğini öğrenmek için aşağıdakileri yapın:

1. Bir tarayıcıda aşağıdaki URL 'ye giderek, *&lt; TenantName &gt;* için kiracı adınızı değiştirin:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. `"tenant_region_scope"`JSON yanıtında bul:

   - `"tenant_region_scope":"USGOV”`Görüntülenirse, B2B desteklenir.
   - `"tenant_region_scope":"USG"`Görünürse, B2B desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [B2B işbirliği davetlerini devretmek](delegate-invitations.md)