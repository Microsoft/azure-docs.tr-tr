---
title: Azure AD uygulama geliştiricileri için destek ve yardım seçenekleri | Microsoft Docs
description: Microsoft kimlikleri ile tümleştirilen uygulama oluştururken geliştirmeyle ilgili sorular ve sorunlar için nasıl yardım ve destek edineceğimizi öğrenin (Azure Active Directory ve Microsoft hesabı)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a160f78f69ed559c576147e9758c837d5ae9b77e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429972"
---
# <a name="support-and-help-options-for-developers"></a>Geliştiriciler için destek ve yardım seçenekleri

Azure Active Directory (Azure AD), Microsoft kimlikleri veya Microsoft Graph API 'SI ile tümleştirmeye başladıysanız veya uygulamanıza yeni bir özellik uyguladığınızda, topluluktan yardım almanız veya bunu anlamanız gereken durumlar vardır. geliştirici olarak sahip olduğunuz destek seçenekleri. Bu makale, aşağıdakiler dahil olmak üzere bu seçenekleri anlamanıza yardımcı olur:

> [!div class="checklist"]
> * Sorunuzun topluluk tarafından yanıtlanmadığı veya uygulamaya çalıştığınız özelliğe yönelik mevcut bir belge zaten var olup olmadığını nasıl arayıyoruz
> * Bazı durumlarda, belirli bir sorunu ayıklamanıza yardımcı olması için yalnızca destek araçlarımızı kullanmak isteyeceksiniz
> * İhtiyaç duyduğunuz yanıtı bulamazsanız, *Stack Overflow* bir soru sormak isteyebilirsiniz
> * Kimlik doğrulama kitaplıklarımızdan biriyle ilgili bir sorun bulursanız, bir *GitHub* sorunu oluştur
> * Son olarak, birisiyle konuşmanız gerekiyorsa, bir destek isteği açmak isteyebilirsiniz

## <a name="search"></a>Arama

Geliştirmeyle ilgili bir sorunuz varsa, yanıtı belgeler, [GitHub örnekleri](https://github.com/azure-samples)veya [Stack Overflow](https://www.stackoverflow.com) sorulara yanıtlar halinde bulabilirsiniz.

### <a name="scoped-search"></a>Kapsamlı arama

Daha hızlı sonuçlar için aramanızın kapsamını Stack Overflow, belgeleri ve kod örneklerini kullanarak en sevdiğiniz arama motorunda aşağıdaki sorguyu kullanın:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

*{Arama koşullarınız}* arama anahtar kelimelerinizle aynıdır.

## <a name="use-the-development-support-tools"></a>Geliştirme destek araçları 'nı kullanma

| Araç  | Açıklama  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Talep adlarını ve değerlerini çözmek için bir KIMLIK veya erişim belirteci yapıştırın. |
| [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)| Microsoft Graph API 'sine karşı istek yapmanızı ve yanıtları görmenizi sağlayan araç. |

## <a name="post-a-question-to-stack-overflow"></a>Stack Overflow bir soru gönderin

Stack Overflow, geliştirmeyle ilgili sorular için tercih edilen kanaldır. Burada, geliştirici topluluğu ve Microsoft takım üyelerinin üyeleri, sorunlarınızı çözmenize yardımcı olmak için doğrudan ilgilidir.

Arama ile sorunuz için bir yanıt bulamazsanız, Stack Overflow için yeni bir soru gönderin. Topluluğun sorunuzu daha hızlı belirlemesine ve yanıt vermesi için soru sorarken aşağıdaki etiketlerden birini kullanın:

|Bileşen/alan  | Etiketler |
|---------|---------|
| ADAL kitaplığı | [adal](https://stackoverflow.com/questions/tagged/adal) |
| MSAL kitaplığı     | [msal](https://stackoverflow.com/questions/tagged/msal) |
| OWıN ara yazılımı  | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[Azure-AD-B2B]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-AD-B2C]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API'si](https://developer.microsoft.com/graph/) | [[Microsoft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Kimlik doğrulama veya yetkilendirme konularıyla ilgili başka bir alan | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Stack Overflow 'den aşağıdaki gönderiler, soru sorma ve kaynak kodu ekleme hakkında ipuçları içermektedir. Topluluk üyelerinin sorunuzu hızlı bir şekilde değerlendirmesine ve yanıt verebilmesini artırmak için bu yönergeleri izleyin:

* [Nasıl yaparım? iyi bir soru sorun](https://stackoverflow.com/help/how-to-ask)
* [En az, tamamlanmış ve doğrulanabilir örnek oluşturma](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>GitHub sorunu oluştur

Kitaplıklarımızla ilgili bir hata veya sorun bulursanız, GitHub depolarımızda bir sorun oluşturabilir. Kitaplıklarımız açık kaynak olduğundan, çekme isteği de gönderebilirsiniz.

Kitaplıkların ve GitHub depolarının listesi için aşağıdakilere bakın:

* [Adal](active-directory-authentication-libraries.md) kitaplıkları ve GitHub depoları
* [Msal.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [msal. Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)ve [msal. obj _C](https://github.com/AzureAD/microsoft-authentication-library-for-objc) kitaplıkları ve GitHub depoları

## <a name="open-a-support-request"></a>Bir destek isteği açın

Birisiyle konuşmanız gerekiyorsa, bir destek isteği açabilirsiniz. Bir Azure müşterisiyseniz, çeşitli destek seçenekleri mevcuttur. Planları karşılaştırmak için [Bu sayfaya](https://azure.microsoft.com/support/plans/)bakın. Azure müşterileri için de Geliştirici desteği sunulmaktadır. Geliştirici destek planlarını satın alma hakkında daha fazla bilgi için [Bu sayfaya](https://azure.microsoft.com/support/plans/developer/)bakın.

* Zaten bir Azure Destek planınız varsa, [burada bir destek isteği açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Bir Azure müşterisiyseniz, [ticari destek](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)aracılığıyla Microsoft ile bir destek isteği de açabilirsiniz.

Ayrıca, destek almak veya soru sormak için bir [sanal aracıyı](https://support.microsoft.com/contactus/?ws=support) deneyebilirsiniz.
