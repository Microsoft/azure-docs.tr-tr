---
title: Microsoft 365 Kamu 'daki Azure AD yenilikleri nelerdir? | Microsoft Belgeleri
description: Microsoft 365 Kamu bulut örneğindeki Azure Active Directory (Azure AD) üzerinde yapılan bazı değişiklikler hakkında bilgi edinin.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89318939"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 Kamu 'daki Azure Active Directory yenilikleri

Aşağıdaki hizmetleri kullanan müşteriler için geçerli olan Microsoft 365 Kamu Bulutu örneğinde Azure Active Directory (Azure AD) üzerinde bazı değişiklikler yaptık:

- Microsoft Azure Kamu

- Microsoft 365 Kamu – GCC High

- Microsoft 365 Kamu – DoD

Bu makale Microsoft 365 Kamu – GCC müşterileri için geçerlidir.

## <a name="changes-to-the-initial-domain-name"></a>İlk etki alanı adına yapılan değişiklikler

Kuruluşunuzun Microsoft 365 Kamu çevrimiçi hizmeti için ilk kayıt sırasında, kuruluşunuzun etki alanı adını seçmeniz istenir `<your-domain-name>.onmicrosoft.com` . . Com sonekine sahip bir etki alanı adınız varsa, hiçbir şey değişmeyecektir.

Ancak, yeni bir Microsoft 365 Kamu hizmetine kaydoluyorsanız, soneki kullanarak bir etki alanı adı seçmeniz istenir `.us` . Bu nedenle, olacaktır `<your-domain-name>.onmicrosoft.us` .

>[!Note]
>Bu değişiklik, bulut hizmeti sağlayıcıları (CSP 'Ler) tarafından yönetilen tüm müşteriler için geçerlidir.

## <a name="changes-to-portal-access"></a>Portal erişiminde yapılan değişiklikler

Microsoft Azure Kamu, Microsoft 365 Kamu-GCC High ve Microsoft 365 Kamu – DoD için, [uç nokta eşleme tablosunda](#endpoint-mapping)gösterildiği gibi portal uç noktalarını güncelleştirdik.

Daha önce müşteriler Dünya çapındaki Azure (portal.azure.com) ve Office 365 (portal.office.com) portallarını kullanarak oturum açabilecektir. Bu güncelleştirmeyle, müşteriler artık belirli Microsoft Azure Kamu Microsoft 365 Kamu-GCC High ve Microsoft 365 devlet-DoD portallarını kullanarak oturum açması gerekir.

## <a name="endpoint-mapping"></a>Uç nokta eşleme

Aşağıdaki tabloda tüm müşterilerin uç noktaları gösterilmektedir:

| Name | Uç nokta ayrıntıları |
|------|------------------|
| Portallar |Microsoft Azure Kamu: https://portal.azure.us<p>Microsoft 365 Kamu – GCC High: https://portal.office365.us<p>Microsoft 365 Kamu – DoD: https://portal.apps.mil |
| Azure Active Directory yetkilisi uç noktası | https://login.microsoftonline.us |
| Microsoft 365 kamu için Microsoft Graph API-GCC High | https://graph.microsoft.us |
| Microsoft 365 kamu için Microsoft Graph API-DoD | https://dod-graph.microsoft.us |
| Azure Kamu hizmetleri uç noktaları | Ayrıntılar için bkz. [Azure Kamu Geliştirici Kılavuzu](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 Kamu-GCC yüksek uç noktaları | Ayrıntılar için bkz. [Office 365 ABD devlet GCC yüksek uç noktaları](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Kamu-DoD | Ayrıntılar için bkz. [Office 365 ABD kamu DOD uç noktaları](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için şu makalelere bakın:

- [Azure Kamu nedir?](../../azure-government/documentation-government-welcome.md)

- [Azure Kamu AAD yetkilisi uç noktası güncelleştirmesi](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [ABD devlet bulutu 'nda Microsoft Graph uç noktalar](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 ABD kamu GCC High ve DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)