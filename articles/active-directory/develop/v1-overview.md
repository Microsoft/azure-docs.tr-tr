---
title: Geliştiriciler için Azure Active Directory (v 1.0) genel bakış
description: Bu makalede, Azure Active Directory v 1.0 uç noktası ve platformu kullanılarak Microsoft iş ve okul hesaplarında oturum açmaya ilişkin bir genel bakış sunulmaktadır.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e790728cbc7415532754d10773d466e9a0280422
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373903"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Geliştiriciler için Azure Active Directory (v 1.0) genel bakış

Azure Active Directory (Azure AD), geliştiricilerin Microsoft iş veya okul hesabına sahip kullanıcıların güvenli şekilde oturumunu açan uygulamalar derlemesine olanak sağlayan bir bulut kimlik hizmetidir. Azure AD, tek kiracılı, iş kolu (LOB) uygulamalarını derleyen geliştiricileri ve çok kiracılı uygulamalar geliştirmek isteyen geliştiricileri destekler. Temel oturum açmaya ek olarak Azure AD, uygulamaların hem [Microsoft Graph](https://docs.microsoft.com/graph/overview) gibi Microsoft API’lerini hem de Azure AD platformunda derlenen özel API’leri çağırmasını sağlar. Bu belgeler, OAuth2.0 ve OpenID Connect gibi sektör standardı protokolleri kullanarak Azure AD desteğini uygulamanıza nasıl ekleyeceğinizi göstermektedir.

> [!NOTE]
> Bu sayfadaki içeriğin çoğu, yalnızca Microsoft iş veya okul hesaplarını destekleyen v 1.0 uç noktası ve platformuna odaklanır. Tüketici veya kişisel Microsoft hesaplarında oturum açmak isterseniz, [v 2.0 uç noktası ve platformu](v2-overview.md)hakkında bilgi için bkz. V 2.0 uç noktası, tüm Microsoft kimliklerinizde oturum açmak isteyen uygulamalar için birleştirilmiş bir geliştirici deneyimi sunar.

| | |
| --- | --- |
|[Kimlik doğrulaması temel bilgileri](v1-authentication-scenarios.md) | Azure AD ile kimlik doğrulamaya giriş. |
|[Uygulama türleri](app-types.md) | Azure AD tarafından desteklenen kimlik doğrulama senaryolarına genel bakış. |
| | |

## <a name="get-started"></a>Kullanmaya Başlayın

V 1.0 hızlı başlangıç ve öğreticiler, Azure AD kimlik doğrulama kitaplığı (ADAL) SDK 'sını kullanarak tercih ettiğiniz platformda uygulama oluşturma konusunda size yol gösterir. Başlamak için [Microsoft Identity platform (geliştiriciler için Azure Active Directory)](index.yml) adresindeki **v 1.0 hızlı başlangıç** ve **v 1.0 öğreticilerine** bakın.

## <a name="how-to-guides"></a>Nasıl yapılır kılavuzları

Azure AD 'de en yaygın görevlere yönelik ayrıntılı bilgi ve yönergeler için bkz. **v 1.0 nasıl yapılır kılavuzları** .

## <a name="reference-topics"></a>Başvuru konuları

Aşağıdaki makaleler, Azure AD’de kullanılan API'ler, protokol iletileri ve terimler hakkında ayrıntılı bilgi sağlar.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Kimlik Doğrulama Kitaplıkları (ADAL)](active-directory-authentication-libraries.md)   | Azure AD tarafından sağlanan kitaplıklara ve SDK’lara genel bakış. |
| [Kod örnekleri](sample-v1-code.md)                                  | Tüm Azure AD kod örneklerinin listesi. |
| [Sözlük](developer-glossary.md)                                      | Bu belgelerde kullanılan terminoloji ve sözcük tanımları. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
