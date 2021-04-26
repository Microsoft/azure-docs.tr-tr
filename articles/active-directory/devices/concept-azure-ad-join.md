---
title: Azure AD 'ye katılmış cihaz nedir?
description: Azure AD 'ye katılmış cihazlar ve cihaz kimlik yönetimi 'nin ortamınızdaki kaynaklara erişen cihazları yönetmenize nasıl yardımcı olabileceğini öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90601461"
---
# <a name="azure-ad-joined-devices"></a>Azure AD’ye katılmış cihazlar

Azure AD katılımı, yalnızca bulutta veya bulutta olmak isteyen kuruluşlar için tasarlanmıştır. Herhangi bir kuruluş, boyut veya sektöre bakılmaksızın Azure AD 'ye katılmış cihazları dağıtabilir. Azure AD JOIN, Karma ortamda bile çalışarak hem buluta hem de şirket içi uygulamalara ve kaynaklara erişimi etkinleştirir.

| Azure AD Join | Description |
| --- | --- |
| **Tanım** | Yalnızca cihazda oturum açmak için kurumsal hesap gerektiren Azure AD 'ye katılmış |
| **Birincil hedef kitle** | Yalnızca bulut ve hibrit kuruluşlar için uygundur. |
|   | Bir kuruluştaki tüm kullanıcılar için geçerlidir |
| **Cihaz sahipliği** | Kuruluş |
| **İşletim sistemleri** | Windows 10 Home dışındaki tüm Windows 10 cihazları |
|   | [Azure 'da çalışan Windows Server 2019 sanal makineleri](howto-vm-sign-in-azure-ad-windows.md) (Sunucu Çekirdeği desteklenmez) |
| **Sağlanıyor** | Self Servis: Windows OOBE veya Settings |
|   | Toplu kayıt |
|   | Windows Autopilot |
| **Cihaz oturum açma seçenekleri** | Kullanılarak kurumsal hesaplar: |
|   | Parola |
|   | İş İçin Windows Hello |
|   | FıDO 2.0 güvenlik anahtarları (Önizleme) |
| **Cihaz yönetimi** | Mobil cihaz yönetimi (örnek: Microsoft Intune) |
|   | Microsoft Intune ve Microsoft uç noktası ile ortak yönetim Configuration Manager |
| **Temel işlevler** | Hem bulut hem de şirket içi kaynaklara yönelik SSO |
|   | MDM kaydı ve MDM uyumluluk değerlendirmesi aracılığıyla koşullu erişim |
|   | Kilitleme ekranında self servis parola sıfırlama ve Windows Hello PIN sıfırlama |
|   | Cihazlar arasında Enterprise State Roaming |

Azure AD 'ye katılmış cihazlar, kurumsal bir Azure AD hesabı kullanarak oturum açtı. Kuruluştaki kaynaklara erişim, bu Azure AD hesabına ve cihaz kimliğine uygulanan [koşullu erişim ilkelerine](../conditional-access/howto-conditional-access-policy-compliant-device.md) göre daha da sınırlı olabilir.

Yöneticiler, Microsoft Intune gibi mobil cihaz yönetimi (MDM) araçlarını kullanarak Azure AD 'ye katılmış cihazları güvenlik altına alabilir ve daha fazla denetleyebilir (Microsoft uç nokta Configuration Manager kullanan ortak yönetim senaryolarında). Bu araçlar, depolamanın şifreli, parola karmaşıklığı, yazılım yüklemeleri ve yazılım güncelleştirmeleri gibi kuruluş gerektiren yapılandırmaların zorlanmasını sağlamak için bir yol sağlar. Yöneticiler [, iş ve eğitim için Microsoft Store uygulamaları yönetmek](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)üzere Configuration Manager kullanarak kuruluş UYGULAMALARıNı Azure AD 'ye katılmış cihazlara kullanabilir hale getirir.

Azure AD JOIN, hazır olmayan deneyim (OOBE), toplu kayıt veya [Windows Autopilot](/intune/enrollment-autopilot)gibi self servis seçenekleri kullanılarak gerçekleştirilebilir.

Azure AD 'ye katılmış cihazlar, kuruluşun ağı üzerinde olduklarında şirket içi kaynaklara yönelik çoklu oturum açma erişimi sürdürmeye devam edebilir. Azure AD 'ye katılmış cihazlar, dosya, yazdırma ve diğer uygulamalar gibi şirket içi sunucularda kimlik doğrulaması yapabilir.

## <a name="scenarios"></a>Senaryolar

Azure AD’ye katılma özelliği temel olarak bir şirket içi Windows Server Active Directory altyapısı bulunmayan kuruluşlar için tasarlanmıştır ancak aşağıdaki senaryolarda siz de bu özellikten yararlanabilirsiniz:

- Azure AD ve MDM benzeri Intune kullanarak bulut tabanlı altyapıya geçiş yapmak istediğinizde.
- Şirket içi etki alanına katılma özelliğini kullanamadığınız durumlarda; örneğin, tabletler ve telefonlar gibi mobil cihazlar üzerinde denetim sağlamanız gerektiğinde.
- Kullanıcılarınızın öncelikle Microsoft 365 veya Azure AD ile tümleştirilmiş diğer SaaS uygulamalarına erişmesi gerekir.
- Active Directory yerine Azure AD’de bir kullanıcı grubunu yönetmek istediğinizde. Bu senaryo, örneğin, dönemsel çalışanlar, yükleniciler veya öğrenciler için uygulanabilir.
- Sınırlı şirket içi altyapısı olan uzak şube ofislerindeki çalışanlara katılma özellikleri sağlamak istediğinizde.

Tüm Windows 10 cihazları için Azure AD 'ye katılmış cihazları Windows 10 Home dışında yapılandırabilirsiniz.

Azure AD'ye katılmış cihazların hedefi şu işlemlerde basitleştirme sağlamaktır:

- İşe ait cihazların Windows dağıtımları
- Herhangi bir Windows cihazından kuruluş uygulamalarına ve kaynaklarına erişim
- İşe ait cihazların bulut tabanlı yönetimi
- Kullanıcıların cihazlarında Azure AD 'ye oturum açması veya Active Directory iş veya okul hesaplarıyla eşitlenmesi.

![Azure AD’ye katılmış cihazlar](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Aşağıdaki yöntemlerden birini kullanarak Azure AD'ye Katılım dağıtımı yapılabilir:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Toplu dağıtım](/intune/windows-bulk-enroll)
- [Self servis deneyimi](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD katılımınızı uygulamayı planlama](azureadjoin-plan.md)
- [Azure AD 'ye katılmış cihazlarda yerel Yöneticiler grubunu yönetme](assign-local-admin.md)
- [Azure portalını kullanarak cihaz kimliklerini yönetme](device-management-azure-portal.md)
- [Azure AD’de eski cihazları yönetme](manage-stale-devices.md)
