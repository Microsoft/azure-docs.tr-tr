---
title: Koşullu erişim-Azure yönetimi için MFA gerektir-Azure Active Directory
description: Azure yönetim görevleri için çok faktörlü kimlik doğrulaması gerektirmek üzere özel bir koşullu erişim ilkesi oluşturma
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad44cc83d65206ec1f199e57685b0dcc3cbb07a5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151177"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Koşullu erişim: Azure yönetimi için MFA gerektir

Kuruluşlar çeşitli Azure hizmetlerini kullanır ve bunları şu şekilde Azure Resource Manager tabanlı araçlarla yönetebilir:

* Azure portalı
* Azure PowerShell
* Azure CLI

Bu araçlar, abonelik genelinde yapılandırmaların, hizmet ayarlarının ve abonelik faturalandırmasını değiştirebilecek kaynaklara son derece ayrıcalıklı erişim sağlayabilir. Bu ayrıcalıklı kaynakları korumak için Microsoft, bu kaynaklara erişen herhangi bir kullanıcı için Multi-Factor Authentication kullanılmasını gerektirir.

## <a name="user-exclusions"></a>Kullanıcı dışlamaları

Koşullu erişim ilkeleri güçlü araçlardır ve ilkenizde aşağıdaki hesapların dışlanmasını öneririz:

* Kiracı genelindeki hesap kilitlenmesini engellemek için **acil durum erişimi** veya **kesme camı** hesapları. Olası olmayan senaryoda tüm yöneticiler kiracınızın dışında kilitlendiğinden, acil durum erişimi yönetim hesabınız kiracıya oturum açmak için kullanılabilir ve erişimi kurtarmak için gerekli adımları uygulayın.
   * Daha fazla bilgi için [Azure AD 'de acil durum erişim hesaplarını yönetme](../users-groups-roles/directory-emergency-access.md)makalesinde bulabilirsiniz.
* Azure AD Connect eşitleme hesabı gibi **hizmet hesapları** ve **hizmet ilkeleri**. Hizmet hesapları, belirli bir kullanıcıya bağlı olmayan etkileşimli olmayan hesaplardır. Bunlar normalde arka uç hizmetleri tarafından kullanılır ve uygulamalara programlı erişim sağlar. MFA programlı olarak tamamlanmadığı için hizmet hesapları dışlanmalıdır.
   * Kuruluşunuzun komut dosyalarında veya kodda kullanımda olan bu hesapları varsa, bunları [yönetilen kimliklerle](../managed-identities-azure-resources/overview.md)değiştirmeyi göz önünde bulundurun. Geçici bir çözüm olarak, bu belirli hesapları temel ilkeden hariç bırakabilirsiniz.

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

Aşağıdaki adımlar, atanan yönetim rollerinin Multi-Factor Authentication gerçekleştirmesini gerektirmek için bir koşullu erişim ilkesi oluşturmanıza yardımcı olur.

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory** > **koşullu erişime**gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar**altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.
   1. **Dışla**altında, **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
   1. **Done** (Bitti) öğesini seçin.
1. **Bulut uygulamaları veya > eylemler** ' in altında, **Uygulama Seç** **' i**seçin ve **Microsoft Azure Yönetim**' i seçin ve ardından **Tamam** **' ı seçin** .
1. **Erişim denetimleri** > **izin**' ın altında, **erişim ver**' i seçin, **Multi-Factor Authentication gerektir**' i seçin ve **Seç**
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık**olarak ayarlayın.
1. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
