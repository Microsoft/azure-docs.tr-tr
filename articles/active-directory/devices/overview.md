---
title: Azure Active Directory cihaz kimliği nedir?
description: Cihaz kimlik yönetimi 'nin ortamınızdaki kaynaklara erişen cihazları yönetmenize nasıl yardımcı olabileceğini öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8399f7101697af429b8c073c101dbfea203e98ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87025600"
---
# <a name="what-is-a-device-identity"></a>Cihaz kimliği nedir?

Tüm şekil ve boyutlardaki cihazların ve kendi cihazını getir (KCG) kavramıyla, BT uzmanları iki ayrı rakip hedefle birlikte başlatılacaktır:

- Son kullanıcıların her yerde ve her yerde üretken olmasına izin ver
- Kuruluşun varlıklarını koruma

Bu varlıkları korumak için BT personelinin öncelikle cihaz kimliklerini yönetmesi gerekir. BT personeli, güvenlik ve uyumluluk standartlarının karşılanmasını sağlamak için Microsoft Intune gibi araçlarla cihaz kimliği oluşturabilir. Azure Active Directory (Azure AD), cihazlar, uygulamalar ve hizmetlerde bu cihazlarda her yerden çoklu oturum açma imkanı sağlar.

- Kullanıcılarınız, kuruluşunuzun ihtiyaç duydukları varlıklara erişim sağlar. 
- BT personeliniz, kuruluşunuzun güvenliğini sağlamak için ihtiyaç duydukları denetimleri alır.

Cihaz kimliği yönetimi, [cihaz tabanlı koşullu erişim](../conditional-access/require-managed-devices.md)için temel bir temelidir. Cihaz tabanlı koşullu erişim ilkeleriyle, ortamınızdaki kaynaklara erişimin yalnızca yönetilen cihazlarla yapılabilmesini sağlayabilirsiniz.

## <a name="getting-devices-in-azure-ad"></a>Azure AD 'de cihazları alma

Azure AD 'de bir cihaz almak için, birden çok seçeneğiniz vardır:

- **Azure AD kayıtlı**
   - Azure AD kaydı olan cihazlar genellikle kişisel olarak sahip olunan ve mobil cihazlardır ve kişisel Microsoft hesabı veya başka bir yerel hesapla oturum açanlar.
      - Windows 10
      - iOS
      - Android
      - macOS
- **Azure AD'ye katılanlar**
   - Azure AD 'ye katılmış cihazlar bir kuruluşa aittir ve bu kuruluşa ait bir Azure AD hesabıyla oturum açanlar. Bunlar yalnızca bulutta bulunur.
      - Windows 10 
      - [Azure 'da çalışan Windows Server 2019 sanal makineleri](howto-vm-sign-in-azure-ad-windows.md) (Sunucu Çekirdeği desteklenmez)
- **Hibrit Azure AD'ye katılmış**
   - Karma Azure AD 'ye katılmış cihazlar bir kuruluşa aittir ve bu kuruluşa ait bir Active Directory Domain Services hesabıyla oturum açanlar. Bunlar bulutta ve şirket içinde mevcuttur.
      - Windows 7, 8,1 veya 10
      - Windows Server 2008 veya üzeri

![Azure AD cihazları Dikey penceresinde görünen cihazlar](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> Karma durum, bir cihazın yalnızca durumuna göre ifade eder. Karma durumun geçerli olması için, geçerli bir Azure AD kullanıcısı da gereklidir.

## <a name="device-management"></a>Cihaz yönetimi

Azure AD 'deki cihazlar Microsoft Intune, Microsoft uç noktası Configuration Manager, grup ilkesi (Hibrit Azure AD katılımı), mobil uygulama yönetimi (MAM) araçları veya diğer üçüncü taraf araçları gibi mobil cihaz yönetimi (MDM) araçları kullanılarak yönetilebilir.

## <a name="resource-access"></a>Kaynak erişimi

Cihazları Azure AD 'ye kaydetme ve birleştirme, kullanıcılarınıza bulut kaynaklarına sorunsuz oturum açma (SSO) sağlar. Bu işlem, yöneticilerin eriştiği cihaza bağlı olarak kaynaklara koşullu erişim ilkeleri uygulama olanağı da sağlar. 

> [!NOTE]
> Cihaz tabanlı koşullu erişim ilkeleri, karma Azure AD 'ye katılmış cihazlar veya uyumlu Azure AD 'ye katılmış ya da Azure AD kayıtlı cihazları gerektirir.

Birincil yenileme belirteci (PRT) cihaz hakkındaki bilgileri içerir ve SSO için gereklidir. Bir uygulamada ayarlanmış cihaz tabanlı koşullu erişim ilkeniz varsa, PRT olmadan erişim reddedilir. Karma koşullu erişim ilkeleri, karma durum cihazı ve oturum açan geçerli bir kullanıcı gerektirir.

Azure AD 'ye katılmış veya karma Azure AD 'ye katılmış cihazlar, SSO 'dan kuruluşunuzun şirket içi kaynaklarına ve bulut kaynaklarına kadar faydalanır. Makalede daha fazla bilgi bulunabilir, [Şirket içi kaynaklara YÖNELIK SSO 'Nun Azure AD 'ye katılmış cihazlarda nasıl çalıştığı](azuread-join-sso.md).

## <a name="device-security"></a>Cihaz güvenliği

- **Azure AD kayıtlı cihazlar** Son Kullanıcı tarafından yönetilen bir hesabı kullanır, bu hesap bir veya bir veya daha fazla ile korunan bir Microsoft hesabı ya da yerel olarak yönetilen bir kimlik bilgisi olur.
   - Parola
   - PIN
   - Desen
   - Windows Hello
- **Azure AD 'ye katılmış veya hibrit Azure AD 'ye katılmış cihazlar** , aşağıdakilerden biri veya birkaçı ile güvenli bir şekılde Azure AD 'de kurumsal hesap kullanır.
   - Parola
   - İş İçin Windows Hello

## <a name="provisioning"></a>Sağlama

Cihazların Azure AD 'ye alınması self servis bir şekilde veya yöneticiler tarafından denetlenen sağlama sürecinde yapılabilir.

## <a name="summary"></a>Özet

Azure AD 'de cihaz kimlik yönetimi ile şunları yapabilirsiniz:

- Azure AD 'de cihazları getirme ve yönetme sürecini kolaylaştırın
- Kullanıcılarınıza kuruluşunuzun bulut tabanlı kaynaklara erişimi kolay bir şekilde sağlayın

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD kayıtlı cihazlar](concept-azure-ad-register.md) hakkında daha fazla bilgi edinin
- [Azure AD 'ye katılmış cihazlar](concept-azure-ad-join.md) hakkında daha fazla bilgi edinin
- [Karma Azure AD 'ye katılmış cihazlar](concept-azure-ad-join-hybrid.md) hakkında daha fazla bilgi edinin
- Azure portal cihaz kimliklerini yönetme hakkında genel bilgi almak için, bkz. [Azure Portal kullanarak cihaz kimliklerini yönetme](device-management-azure-portal.md).
- Cihaz tabanlı koşullu erişim hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory cihaz tabanlı koşullu erişim Ilkelerini yapılandırma](../conditional-access/require-managed-devices.md).
