---
title: MFA veya 2FA ve Privileged Identity Management-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) çok faktörlü kimlik doğrulamasını (MFA) nasıl doğrulayacağını öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835008"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multi-Factor Authentication ve Privileged Identity Management

Tüm yöneticileriniz için Multi-Factor Authentication (MFA) gerektirmenizi öneririz. Bu, güvenliği aşılmış bir parola nedeniyle saldırının riskini azaltır.

Kullanıcıların oturum açtıklarında bir Multi-Factor Authentication sınamasını tamamlamasını zorunlu kılabilirsiniz. Ayrıca, Azure Active Directory (Azure AD) Privileged Identity Management (PıM) ' de bir rolü etkinleştirdiklerinde kullanıcıların Multi-Factor Authentication sınamasını tamamlamasını de isteyebilirsiniz. Bu şekilde, Kullanıcı oturum açtıklarında bir Multi-Factor Authentication sınaması tamammadıysa, Privileged Identity Management tarafından bunu yapması istenir.

> [!IMPORTANT]
> Şu anda Azure AD Multi-Factor Authentication, Microsoft kişisel hesapları değil yalnızca iş veya okul hesaplarıyla çalışır (genellikle Skype, Xbox veya Outlook.com gibi Microsoft hizmetlerinde oturum açmak için kullanılan kişisel bir hesaptır). Bu nedenle, kişisel hesap kullanan herkes, rollerini etkinleştirmek için Multi-Factor Authentication 'ı kullanamadığından uygun yönetici olamaz. Bu kullanıcıların bir Microsoft hesabı kullanarak iş yüklerini yönetmeye devam etmesi gerekiyorsa, bunları şimdilik kalıcı yöneticilerle yükseltin.

## <a name="how-pim-validates-mfa"></a>PıM, MFA 'yı nasıl doğrular

Kullanıcı bir rolü etkinleştirdiğinde Multi-Factor Authentication 'ı doğrulamaya yönelik iki seçenek vardır.

En basit seçenek, ayrıcalıklı bir rolü etkinleştiren kullanıcılar için Azure AD Multi-Factor Authentication ' i temel alır. Bunu yapmak için önce bu kullanıcıların lisanslanmış olup olmadığını ve Azure AD Multi-Factor Authentication için kaydolmadığını kontrol edin. Azure AD Multi-Factor Authentication dağıtma hakkında daha fazla bilgi için bkz. [bulut tabanlı Azure ad Multi-Factor Authentication dağıtma](../authentication/howto-mfa-getstarted.md). Azure AD 'yi, oturum açtıklarında bu kullanıcılar için Multi-Factor Authentication 'ı zorlamak üzere yapılandırmanız önerilir, ancak gerekli değildir. Bunun nedeni, Multi-Factor Authentication denetimlerinin Privileged Identity Management tarafından yapılması olacaktır.

Alternatif olarak, kullanıcılar şirket içinde kimlik doğrulamasından getir, kimlik sağlayıcınızın Multi-Factor Authentication 'dan sorumlu olmasını sağlayabilirsiniz. Örneğin, Azure AD 'ye erişmeden önce akıllı kart tabanlı kimlik doğrulaması gerektirmek için AD Federasyon Hizmetleri 'ni yapılandırdıysanız, [bulut kaynaklarının Azure ad Multi-Factor Authentication Ile güvenliğini sağlama ve AD FS](../authentication/howto-mfa-adfs.md) Azure AD 'ye talepler göndermek için AD FS yapılandırma yönergelerini içerir. Bir Kullanıcı bir rolü etkinleştirmeye çalıştığında, uygun talepler alındıktan sonra Privileged Identity Management, Kullanıcı için bu Multi-Factor Authentication 'ın zaten doğrulanmasını kabul eder.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rol ayarlarını yapılandırma](pim-how-to-change-default-settings.md)
- [Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma](pim-resource-roles-configure-role-settings.md)
