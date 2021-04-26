---
title: Azure AD 'de Microsoft hesabı (MSA) kimlik sağlayıcısı
description: Bir dış kullanıcının (konuk) Azure AD uygulamalarınızda Microsoft hesabı (MSA) ile oturum açmasını sağlamak için Azure AD 'yi kullanın.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693269"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Dış kimlikler için Microsoft hesabı (MSA) kimlik sağlayıcısı (Önizleme)

> [!NOTE]
> Microsoft hesabı kimlik sağlayıcısı, Azure Active Directory genel önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

B2B Konuk kullanıcılarınız, daha fazla yapılandırma olmadan B2B işbirliği için kendi kişisel Microsoft hesaplarını kullanabilir. Konuk kullanıcılar B2B işbirliği davetlerinizi kullanabilir veya kendi kişisel Microsoft hesabı kullanarak kaydolma Kullanıcı akışlarınızı tamamlayabilir.

Microsoft hesapları, müşteri odaklı Microsoft ürünlerine ve Outlook, OneDrive, Xbox LIVE veya Microsoft 365 gibi bulut hizmetlerine erişim sağlamak için bir kullanıcı tarafından ayarlanır. Hesap oluşturulup Microsoft tarafından çalıştırılan Microsoft tüketici kimliği hesap sisteminde depolanır.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Microsoft hesaplarını kullanarak Konuk oturumu açma

Microsoft hesabı, dış kimlikler kimlik sağlayıcıları listesinde varsayılan olarak kullanılabilir. Konuk kullanıcıların, davet akışı veya self servis kaydolma Kullanıcı akışı kullanarak Microsoft hesabı oturum açmalarına izin vermek için başka yapılandırma gerekmez.

![Kimlik sağlayıcıları listesinde Microsoft hesabı](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Davet akışında Microsoft hesabı

[Bir konuk KULLANıCıYı](add-users-administrator.md) B2B işbirliğiyle davet ettiğinizde Microsoft hesabı, oturum açmak için kullandıkları e-posta adresi olarak belirtebilirsiniz.

![Microsoft hesabı kullanarak davet etme](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Self Servis kaydolma Kullanıcı akışlarında Microsoft hesabı

Microsoft hesabı, self servis kaydolma Kullanıcı akışlarınız için bir kimlik sağlayıcı seçeneğidir. Kullanıcılar kendi Microsoft hesaplarını kullanarak uygulamalarınıza kaydolabilir. İlk olarak, kiracınız için [self servis kaydolma 'yı etkinleştirmeniz](self-service-sign-up-user-flow.md) gerekir. Ardından, uygulama için bir Kullanıcı akışı ayarlayabilir ve oturum açma seçeneklerinden biri olarak Microsoft hesabı ' nı seçebilirsiniz.

![Self Servis kaydolma Kullanıcı akışında Microsoft hesabı](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory B2B işbirliği kullanıcıları ekleyin](add-users-administrator.md)
- [Bir uygulamaya self servis kaydolma ekleme](self-service-sign-up-user-flow.md)