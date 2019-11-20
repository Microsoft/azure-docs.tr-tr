---
title: Azure AD Kimlik Koruması verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure AD Kimlik Koruması verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 5d73ce12d509b2ccc534e19a27124980f3b60044
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158890"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Azure AD Kimlik Koruması verileri bağlama



Panoları görüntülemek, özel uyarılar oluşturmak ve araştırmayı geliştirmek için uyarıları Azure Sentinel 'e aktarmak üzere Azure Sentinel 'e [Azure AD kimlik koruması](https://docs.microsoft.com/azure/information-protection/reports-aip) Günlükler akışını sağlayabilirsiniz. Azure Active Directory Kimlik Koruması, risk kullanıcılarının, risk algılamaları ve güvenlik açıklarına göre birleştirilmiş bir görünüm sağlar ve gelecekteki olayları otomatik olarak düzeltmek için ilkeler ayarlayabilir. Hizmet, Microsoft 'un tüketici kimliklerini koruma deneyiminden oluşturulmuştur ve noktadan itibaren 13.000.000.000 günlük bir oturum açma sinyalinden çok fazla doğruluk kazandır. 


## <a name="prerequisites"></a>Önkoşullar

- [Azure Active Directory Premium P1 veya P2 lisansınızın](https://azure.microsoft.com/pricing/details/active-directory/) olması gerekir
- Genel yönetici veya Güvenlik Yöneticisi izinlerine sahip Kullanıcı


## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD Kimlik Koruması Bağlan

Zaten Azure AD Kimlik Koruması varsa, [Ağınızda etkinleştirildiğinden](../active-directory/identity-protection/overview-identity-protection.md)emin olun.
Azure AD Kimlik Koruması dağıtılırsa ve veri alıyorsanız, uyarı verileri kolayca Azure Sentinel 'e akışla eklenebilir.


1. Azure Sentinel 'de **veri bağlayıcıları** ' nı seçin ve **Azure AD kimlik koruması** kutucuğuna tıklayın.

2. Azure Sentinel 'e Azure AD Kimlik Koruması olayları akışa başlamak için **Bağlan** ' a tıklayın.


6. Azure AD Kimlik Koruması uyarıları için Log Analytics ilgili şemayı kullanmak için, **Securityalert**aratın.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Azure AD Kimlik Koruması Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
