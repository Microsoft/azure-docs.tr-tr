---
title: Lisans self servis parola sıfırlama-Azure Active Directory
description: Self servis parola sıfırlama lisanslama gereksinimleri Azure Active Directory fark hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952380"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory self servis parola sıfırlama için lisans gereksinimleri

Bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltmak için, Azure Active Directory (Azure AD) içindeki kullanıcı hesapları self servis parola sıfırlama (SSPR) için etkinleştirilebilir. SSPR 'yi oluşturan Özellikler parola değiştirme, sıfırlama, kilidini açma ve bir şirket içi dizini geri yazma 'yı içerir. Temel SSPR özellikleri Microsoft 365 İş standart veya daha yüksek ve tüm Azure AD Premium SKU 'Larında ücretsiz olarak kullanılabilir.

Bu makalede, self servis parola sıfırlamanın lisanslanması ve kullanılması için farklı yollar ayrıntılarıyla açıklanır. Fiyatlandırma ve faturalandırma hakkında belirli Ayrıntılar için bkz. [Azure AD fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Sürümleri ve özellikleri karşılaştırma

SSPR yalnızca kiracı için bir lisans gerektirir. 

Aşağıdaki tabloda parola değiştirme, sıfırlama veya şirket içi geri yazma için farklı SSPR senaryoları ve hangi SKU 'Ların özelliği sağladığı özetlenmektedir.

| Özellik | Azure AD Ücretsiz | Microsoft 365 İş standart | Microsoft 365 İş Premium | Azure AD Premium P1 veya P2 |
| --- |:---:|:---:|:---:|:---:|
| **Yalnızca bulutta bulunan Kullanıcı parolası değiştirme**<br />Azure AD 'deki bir Kullanıcı parolasını bilir ve yeni bir şekilde değiştirmek ister. | ● | ● | ● | ● |
| **Yalnızca bulut Kullanıcı parolası sıfırlama**<br />Azure AD 'deki bir Kullanıcı parolasını unutursa ve sıfırlanması gerekir. | | ● | ● | ● |
| **Karma Kullanıcı parolası, şirket içi geri yazma ile değiştirme veya sıfırlama**<br />Azure AD 'deki bir Kullanıcı Azure AD Connect kullanan bir şirket içi dizinden eşitlendiğinde, parolasını değiştirmek veya sıfırlamak ve ayrıca yeni parolayı yeniden şirket içi olarak yazmak ister. | | | ● | ● |

> [!WARNING]
> Tek başına Microsoft 365 temel ve standart lisanslama planları, şirket içi geri yazma ile SSPR 'yi desteklemez. Şirket içi geri yazma özelliği Azure AD Premium P1, Premium P2 veya Microsoft 365 İş Premium gerektirir.

Maliyetler de dahil olmak üzere ek lisans bilgileri için aşağıdaki sayfalara bakın:

* [Azure Active Directory fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory özellikleri ve özellikleri](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Kurumsal](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 İş](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Sonraki adımlar

SSPR 'yi kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Self servis parola sıfırlamayı etkinleştirme (SSPR)](tutorial-enable-sspr.md)