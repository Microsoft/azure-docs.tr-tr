---
title: Microsoft 365 bağlı olan taraf güveni için imza karma algoritmasını Değiştir-Azure
description: Bu sayfa, Microsoft 365 ile federasyon güveni için SHA algoritmasını değiştirmeye ilişkin yönergeler sağlar.
keywords: SHA1, SHA256, M365, Federasyon, AADConnect, ADFS, AD FS, değişim Sha, Federasyon güveni, bağlı olan taraf güveni
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89660917"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Microsoft 365 bağlı olan taraf güveni için imza karma algoritmasını Değiştir
## <a name="overview"></a>Genel Bakış
Active Directory Federasyon Hizmetleri (AD FS) (AD FS), üzerinde oynanmamasını sağlamak için belirteçlerini Microsoft Azure Active Directory imzalar. Bu imza, SHA1 veya SHA256 tabanlı olabilir. Azure Active Directory artık bir SHA256 algoritmasıyla imzalanmış belirteçleri destekliyor ve en yüksek güvenlik düzeyi için belirteç imzalama algoritmasını SHA256 olarak ayarlamayı öneririz. Bu makalede, belirteç imzalama algoritmasını daha güvenli SHA256 düzeyine ayarlamak için gereken adımlar açıklanmaktadır.

>[!NOTE]
>Microsoft SHA256 kullanımını, SHA1 'den daha güvenli olduğu ancak SHA1 hala desteklenen bir seçenek olmaya devam ettiğinden belirteçleri imzalama algoritması olarak önerir.

## <a name="change-the-token-signing-algorithm"></a>Belirteç imzalama algoritmasını değiştirme
İmza algoritmasını aşağıdaki iki işlemlerden biriyle ayarladıktan sonra, AD FS Microsoft 365 bağlı olan taraf güveni için belirteçleri, SHA256 ile imzalar. Herhangi bir ek yapılandırma değişikliği yapmanız gerekmez ve bu değişikliğin Microsoft 365 veya diğer Azure AD uygulamalarına erişme olanağınızda hiçbir etkisi yoktur.

### <a name="ad-fs-management-console"></a>AD FS Yönetim Konsolu
1. Birincil AD FS sunucusunda AD FS Yönetim konsolunu açın.
2. AD FS düğümünü genişletin ve **bağlı olan taraf güvenleri**' ne tıklayın.
3. Microsoft 365/Azure bağlı olan taraf güvenine sağ tıklayın ve **Özellikler**' i seçin.
4. **Gelişmiş** sekmesini seçin ve güvenli karma algoritması SHA256 ' nı seçin.
5. **Tamam**'a tıklayın.

![SHA256 imzalama algoritması--MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell cmdlet 'leri
1. Herhangi bir AD FS sunucusunda, yönetici ayrıcalıkları altında PowerShell ' i açın.
2. **Set-ADFSRelyingPartyTrust** cmdlet 'ini kullanarak güvenli karma algoritmasını ayarlayın.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Ayrıca şunu okuyun
* [Azure AD Connect Microsoft 365 güveni onarma](how-to-connect-fed-management.md#repairthetrust)

