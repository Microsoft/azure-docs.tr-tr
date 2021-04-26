---
title: Azure AD Kimlik Koruması verilerini Azure Sentinel 'e bağlama
description: Panoları görüntülemek, özel uyarılar oluşturmak ve araştırmayı geliştirmek için Azure AD Kimlik Koruması günlüklerin ve uyarıların Azure Sentinel 'e akışını nasıl sağlayacağınızı öğrenin.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632250"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Azure Active Directory (Azure AD) kimlik korumasından verileri bağlama

Panoları görüntülemek, özel uyarılar oluşturmak ve araştırmayı geliştirmek için uyarıları Azure Sentinel 'e aktarmak üzere Azure Sentinel 'e [Azure AD kimlik koruması](../active-directory/identity-protection/overview-identity-protection.md) Günlükler akışını sağlayabilirsiniz. Azure Active Directory Kimlik Koruması, risk kullanıcılarının, risk algılamaları ve güvenlik açıklarına göre birleştirilmiş bir görünüm sağlar ve gelecekteki olayları otomatik olarak düzeltmek için ilkeler ayarlayabilir. Hizmet, Microsoft 'un tüketici kimliklerini koruma deneyiminden oluşturulmuştur ve noktadan itibaren 13.000.000.000 günlük bir oturum açma sinyalinden çok fazla doğruluk kazandır. 

## <a name="prerequisites"></a>Önkoşullar

- Bir [Azure AD Premium P2 aboneliğine](https://azure.microsoft.com/pricing/details/active-directory/)sahip olmanız gerekir.
- Genel yönetici veya Güvenlik Yöneticisi izinlerine sahip bir kullanıcıya sahip olmanız gerekir.

## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD Kimlik Koruması Bağlan

Bir Azure AD Premium P2 aboneliğiniz varsa Azure AD Kimlik Koruması dahil edilir. Herhangi bir [ilke etkinse](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) ve uyarılar üretiyorsa, uyarı verileri kolayca Azure Sentinel 'e akışla eklenebilir.

1. Azure Sentinel 'de **veri bağlayıcıları** ' nı seçin ve **Azure AD kimlik koruması** kutucuğuna tıklayın.

1. Azure Sentinel 'e Azure AD Kimlik Koruması olayları akışa başlamak için **Bağlan** ' a tıklayın.

1. Azure AD Kimlik Koruması uyarıları için Log Analytics ilgili şemayı kullanmak için, **Securityalert** aratın.

Bağlayıcıyı test etmek isterseniz, Azure Sentinel 'e akışa eklenecek örnek uyarılar oluşturmak için [algılamaları benzeleyebilirsiniz](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) .

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Azure AD Kimlik Koruması Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
