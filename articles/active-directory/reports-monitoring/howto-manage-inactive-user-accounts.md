---
title: Azure AD 'de etkin olmayan kullanıcı hesaplarını yönetme | Microsoft Docs
description: Azure AD 'de kullanımdan kalkmış olan kullanıcı hesaplarını algılama ve işleme hakkında bilgi edinin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ea62a8d602cc472269b52c230529aa3f9b86ed4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535102"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Nasıl yapılır: Azure AD 'de etkin olmayan kullanıcı hesaplarını yönetme

Büyük ortamlarda, çalışanlar bir kuruluştan ayrıldığında Kullanıcı hesapları her zaman silinmez. BT Yöneticisi olarak, bir güvenlik riskini temsil ettiğinden, bu kullanımdan kaldırılmış Kullanıcı hesaplarını algılamak ve işlemek istersiniz.

Bu makalede, Azure AD 'de eski Kullanıcı hesaplarını işlemeye yönelik bir yöntem açıklanmaktadır. 

## <a name="what-are-inactive-user-accounts"></a>Etkin olmayan kullanıcı hesapları nelerdir?

Etkin olmayan hesaplar, kaynaklarınıza erişim kazanmak için artık kuruluşunuzun üyeleri tarafından gerekli olmayan kullanıcı hesaplarıdır. Etkin olmayan hesaplara yönelik bir anahtar tanımlayıcı, ortamınızda oturum açmak için *bir süredir* kullanılmalarıdır. Etkin olmayan hesaplar oturum açma etkinliğine bağlı olduğundan, bunları tespit etmek için başarılı olan son oturum açma işleminin zaman damgasını kullanabilirsiniz. 

Bu yöntemin zorluğuyla, ortamınız için ne kadar *bir süre boyunca* ne olacağı tanımlanır. Örneğin, kullanıcılar tatilde olduklarından *bir ortamda bir* ortamda oturum açabilirler. Etkin olmayan kullanıcı hesaplarının ne kadar Delta olduğunu tanımlarken ortamınızda oturum açmadığınız tüm yasal nedenlerle etken yapmanız gerekir. Birçok kuruluşta, etkin olmayan kullanıcı hesapları için Delta 90 ile 180 gün arasındadır. 

Son başarılı oturum açma, bir kullanıcının kaynaklara erişim gereksinimi hakkında potansiyel öngörüler sağlar.  Grup üyeliği veya uygulama erişiminin hala gerekli olduğunu veya kaldırılıp kaldırılmadığını belirlemeye yardımcı olabilir. Dış Kullanıcı yönetimi için, bir dış kullanıcının kiracı içinde hala etkin olup olmadığını veya temizlenmesi gerektiğini anlayabilirsiniz. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Etkin olmayan kullanıcı hesaplarını algılama

**Microsoft Graph** API 'Sinin **signeylemsizlik** kaynak türü tarafından kullanıma sunulan **lastsignındatetime** özelliğini değerlendirerek etkin olmayan hesapları tespit edersiniz. Bu özelliği kullanarak, aşağıdaki senaryolar için bir çözüm uygulayabilirsiniz:

- **Ada göre kullanıcılar**: Bu senaryoda, Lastsignındatetime değerini değerlendirmenizi sağlayan belirli bir kullanıcıyı ada göre aratın: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Tarihe göre kullanıcılar**: Bu senaryoda, belirli bir tarihten önce bir Lastsignındatetime değeri olan kullanıcıların listesini istemeniz gerekir: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Aşağıdaki senaryoyu kullanabilmeniz için, tüm kullanıcıların son oturum açma tarihinin bir raporunu oluşturma gereksinimi olabilir.
> **Tüm kullanıcılar Için son oturum açma tarihi ve saati**: Bu senaryoda, tüm kullanıcıların bir listesini ve ilgili her Kullanıcı Için Son Lastsignındatetime değerini istemeniz gerekir: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Bilmeniz gerekenler

Bu bölümde, Lastsignındatetime özelliği hakkında bilmeniz gerekenler listelenmiştir.

### <a name="how-can-i-access-this-property"></a>Bu özelliğe nasıl erişebilirim?

**Lastsignındatetime** özelliği, [Microsoft Graph REST API](/graph/overview#whats-in-microsoft-graph) [signeylemsizlik kaynak türü](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) tarafından gösterilir.   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Lastsignındatetime özelliği Get-AzureAdUser cmdlet 'i aracılığıyla kullanılabilir mi?

Hayır.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Bu özelliğe erişmek için hangi Azure AD sürümüne ihtiyacım var?

Bu özelliğe Azure AD 'nin tüm sürümlerinde erişebilirsiniz.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Özelliği okumak için hangi izni almam gerekir?

Bu özelliği okumak için aşağıdaki hakları vermeniz gerekir: 

- AuditLogs. Read. All
- Organi. Read. All  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD, özelliği güncelleştirne zaman?

Başarılı olan her etkileşimli oturum açma işlemi, temel alınan veri deposunun bir güncelleştirmesine neden olur. Genellikle, başarılı oturum açma işlemleri, ilgili oturum açma raporunda 10 dakika içinde görünür.
 

### <a name="what-does-a-blank-property-value-mean"></a>Boş bir özellik değeri ne anlama geliyor?

Bir Lastsignındatetime zaman damgası oluşturmak için başarılı bir oturum açma işlemi gerekir. Lastsignındatetime özelliği yeni bir özellik olduğundan, Lastsignındatetime özelliğinin değeri şu durumlarda boş olabilir:

- Bir kullanıcının son başarılı oturum açma işlemi 2020 Nisan 'dan önce gerçekleşti.
- Etkilenen Kullanıcı hesabı, başarılı bir oturum açma işlemi için hiç kullanılmadı.

## <a name="next-steps"></a>Sonraki adımlar

* [Sertifikalarla Azure Active Directory raporlama API’sini kullanarak veri alma](tutorial-access-api-with-certificates.md)
* [API başvurusunu denetle](/graph/api/resources/directoryaudit) 
* [Oturum açma Etkinliği raporu API başvurusu](/graph/api/resources/signin)
