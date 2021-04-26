---
title: Hizmet sınırlarını ve kısıtlamalarını Azure AD B2C
titleSuffix: Azure AD B2C
description: Hizmet sınırları ve Azure Active Directory B2C hizmeti kısıtlamaları için başvuru.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 190d88e62069a34b61017a0079f75696d67f6c82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979921"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Hizmet sınırlarını ve kısıtlamalarını Azure Active Directory B2C

Bu makale Azure Active Directory B2C (Azure AD B2C) hizmeti için kullanım kısıtlamalarını ve diğer hizmet sınırlarını içerir.

## <a name="end-userconsumption-related-limits"></a>Son Kullanıcı/tüketim ile ilgili sınırlar

Aşağıdaki Son Kullanıcı ile ilgili hizmet limitleri, SAML, Open ID Connect, OAuth2 ve ROPC dahil Azure AD B2C tarafından desteklenen tüm kimlik doğrulama ve yetkilendirme protokolleri için geçerlidir.

|Kategori |Sınır    |
|---------|---------|
|Azure AD B2C kiracı başına IP adresi başına istek sayısı       |6000/5 dk          |
|Azure AD B2C kiracı başına toplam istek sayısı     |12000/dk          |

İstek sayısı, Azure AD B2C Kullanıcı yolculuğu sırasında oluşan dizin okuma ve yazma sayısına göre farklılık gösterebilir. Örneğin, dizinden okuyan basit bir oturum açma yolculuğu, 1 istekten oluşur. Oturum açma yolculuğunun de dizini güncelleştirmesi gerekiyorsa, bu işlem ek bir istek olarak sayılır.

## <a name="azure-ad-b2c-configuration-limits"></a>Yapılandırma sınırlarını Azure AD B2C

Aşağıdaki tabloda Azure AD B2C hizmetindeki yönetim yapılandırma sınırları listelenmektedir.

|Kategori  |Sınır  |
|---------|---------|
|Uygulama başına kapsam sayısı        |1000          |
|Kullanıcı başına [özel özniteliklerin](user-profile-attributes.md#extension-attributes)sayısı   <sup>1</sup>       |100         |
|Uygulama başına yeniden yönlendirme URL sayısı       |100         |
|Uygulama başına oturum kapatma URL 'Lerinin sayısı        |1          |
|Öznitelik başına dize sınırı      |250 karakter          |
|Abonelik başına B2C kiracılarının sayısı      |20         |
|Özel ilkelerdeki [Devralma](custom-policy-overview.md#inheritance-model) düzeyleri     |10         |
|Azure AD B2C kiracı başına ilke sayısı      |200          |
|En fazla ilke dosyası boyutu      |400 KB          |

<sup>1</sup> Ayrıca bkz. [Azure AD hizmet limitleri ve kısıtlamaları](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Graph azaltma kılavuzunu](/graph/throttling) öğrenin 
- [Azure AD B2C uygulamalar için doğrulama farklılıkları](../active-directory/develop/supported-accounts-validation.md) hakkında bilgi edinin













