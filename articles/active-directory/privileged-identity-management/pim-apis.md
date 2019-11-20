---
title: PıM için Microsoft Graph API 'Leri (Önizleme)-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management için Microsoft Graph API 'Leri (PıM) (Önizleme) kullanma hakkında bilgi sağlar.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: aea9fbb14d3892419af104c44c8bfd5528eef421
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021920"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Privileged Identity Management için Microsoft Graph API 'Leri (Önizleme)

Azure Active Directory için [Microsoft Graph API 'lerini](https://developer.microsoft.com/graph/docs/concepts/overview) kullanarak tüm Privileged Identity Management görevlerini gerçekleştirebilirsiniz. Bu makalede Privileged Identity Management için Microsoft Graph API 'Lerini kullanmaya yönelik önemli kavramlar açıklanmaktadır.

Microsoft Graph API 'Leri hakkında ayrıntılar için [Azure AD PRIVILEGED IDENTITY Management API başvurusunu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)inceleyin.

> [!IMPORTANT]
> Microsoft Graph 'deki/beta sürümündeki API 'Ler önizlemededir ve değişikliğe tabidir. Üretim uygulamalarında bu API 'lerin kullanılması desteklenmez.

## <a name="required-permissions"></a>Gerekli izinler

Privileged Identity Management için Microsoft Graph API 'Leri çağırmak için aşağıdaki izinlerden **bir veya daha fazlasına** sahip olmanız gerekir:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>İzinleri ayarla

Uygulamaların Privileged Identity Management Microsoft Graph API 'Lerini çağırması için, gerekli izinlere sahip olmaları gerekir. Gerekli izinleri belirtmenin en kolay yolu, [Azure AD onay çerçevesini](../develop/consent-framework.md)kullanmaktır.

### <a name="set-permissions-in-graph-explorer"></a>Graph Explorer 'da izinleri ayarla

Çağrılarınızı test etmek için grafik Gezginini kullanıyorsanız, aracında izinleri belirtebilirsiniz.

1. [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 'Da genel yönetici olarak oturum açın.

1. **İzinleri değiştir**' e tıklayın.

    ![Grafik Gezgini-izinleri değiştir](./media/pim-apis/graph-explorer.png)

1. Dahil etmek istediğiniz izinlerin yanındaki onay kutularını seçin. `PrivilegedAccess.ReadWrite.AzureAD`, Graph Explorer 'da henüz kullanılamıyor.

    ![Grafik Gezgini-izinleri değiştir](./media/pim-apis/graph-explorer-modify-permissions.png)

1. İzin değişikliklerini uygulamak için **Izinleri Değiştir** ' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Privileged Identity Management API başvurusu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
