---
title: Azure AD kimlik doğrulaması Ulusal bulutlar & | Mavisi
titleSuffix: Microsoft identity platform
description: Ulusal bulutlar için uygulama kaydı ve kimlik doğrulama uç noktaları hakkında bilgi edinin.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: 41bb7287aaa045e191d61abb00f470018fda9cbe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031605"
---
# <a name="national-clouds"></a>Ulusal bulutlar

Ulusal bulutlar, Azure 'un fiziksel olarak yalıtılmış örnekleridir. Azure 'un bu bölgeleri, verilerin yer aldığı, egemenlik ve uyumluluk gereksinimlerinin coğrafi sınırlar içinde kabul edilen şekilde sağlandığından emin olmak için tasarlanmıştır.

Genel bulut dahil, Azure Active Directory (Azure AD) aşağıdaki Ulusal bulutlara dağıtılır:  

- Azure Kamu
- Azure Almanya
- Azure China 21Vianet

Ulusal bulutlar benzersizdir ve Azure Global 'in ayrı bir ortamıdır. Uygulamanızı bu ortamlar için geliştirirken önemli farklılıkları bilmeniz önemlidir. Farklı uygulamalar kaydetme, belirteçleri alma ve uç noktaların yapılandırılması dahildir.

## <a name="app-registration-endpoints"></a>Uygulama kayıt uç noktaları

Ulusal bulutların her biri için ayrı bir Azure portal vardır. Uygulamaları ulusal bir bulutta Microsoft Identity platformu ile bütünleştirmek için, uygulamanızı ortama özgü her bir Azure portal ayrı olarak kaydetmeniz gerekir.

Aşağıdaki tabloda, her bir ulusal bulut için bir uygulamayı kaydetmek üzere kullanılan Azure AD uç noktaları için temel URL 'Ler listelenmektedir.

| Ulusal bulut | Azure AD Portal uç noktası |
|----------------|--------------------------|
| ABD kamu için Azure AD | `https://portal.azure.us` |
| Azure AD Almanya | `https://portal.microsoftazure.de` |
| 21Vianet tarafından çalıştırılan Azure AD Çin | `https://portal.azure.cn` |
| Azure AD (küresel hizmet) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD kimlik doğrulama uç noktaları

Tüm ulusal bulutlar, kullanıcıların kimliklerini her ortamda ayrı olarak doğrular ve ayrı kimlik doğrulama uç noktaları vardır.

Aşağıdaki tabloda, her bir ulusal bulut için belirteçleri elde etmek üzere kullanılan Azure AD uç noktaları için temel URL 'Ler listelenmektedir.

| Ulusal bulut | Azure AD kimlik doğrulama uç noktası |
|----------------|-------------------------|
| ABD kamu için Azure AD | `https://login.microsoftonline.us` |
| Azure AD Almanya| `https://login.microsoftonline.de` |
| 21Vianet tarafından çalıştırılan Azure AD Çin | `https://login.partner.microsoftonline.cn` |
| Azure AD (küresel hizmet)| `https://login.microsoftonline.com` |

Uygun bölgeye özgü temel URL 'YI kullanarak Azure AD yetkilendirmesi veya belirteç uç noktalarına istek oluşturabilirsiniz. Örneğin, Azure Almanya için:

- Yetkilendirme ortak uç noktası `https://login.microsoftonline.de/common/oauth2/v2.0/authorize` .
- Belirteç ortak uç noktası `https://login.microsoftonline.de/common/oauth2/v2.0/token` .

Tek kiracılı uygulamalarda, önceki URL 'Lerdeki "ortak" i kiracı KIMLIĞINIZLE veya adınızla değiştirin. `https://login.microsoftonline.de/contoso.com` bunun bir örneğidir.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Ulusal bir bulut ortamında Microsoft Graph API 'Lerini nasıl çağıracağınızı öğrenmek için, [Ulusal bulut dağıtımlarında Microsoft Graph](/graph/deployments)' a gidin.

> [!IMPORTANT]
> Küresel hizmetin belirli bölgelerinde bulunan belirli hizmetler ve özellikler, tüm ulusal bulutlarda kullanılamayabilir. Hangi hizmetlerin kullanılabilir olduğunu öğrenmek için [bölgeye göre kullanılabilir ürünlere](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)gidin.

Microsoft Identity platformunu kullanarak bir uygulama oluşturmayı öğrenmek için, [Microsoft kimlik doğrulama kitaplığı (msal) öğreticisini](msal-national-cloud.md)izleyin. Özellikle, bu uygulama bir kullanıcıya oturum açacaktır ve Microsoft Graph API 'sini çağırmak için bir erişim belirteci alır.

## <a name="next-steps"></a>Sonraki adımlar

[Microsoft kimlik doğrulama kitaplığı 'nı (msal) ulusal bir bulut ortamında](msal-national-cloud.md)nasıl kullanacağınızı öğrenin.

Ulusal bulut belgeleri:

- [Azure Devlet Kurumları](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Almanya](../../germany/index.yml)
