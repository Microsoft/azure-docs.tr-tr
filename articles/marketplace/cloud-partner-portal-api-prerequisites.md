---
title: API önkoşulları-Azure Marketi
description: Bulut İş Ortağı Portalı API 'Leri kullanma önkoşulları.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: f5fc77b65f6a83f4f7ca8ed8b8c9294b95307735
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107321"
---
# <a name="api-prerequisites"></a>API önkoşulları

> [!NOTE]
> Bulut İş Ortağı Portalı API 'Leri ile tümleşiktir ve Iş Ortağı Merkezi 'nde çalışmaya devam edecektir. Geçiş küçük değişiklikler sunar. Iş Ortağı Merkezi 'ne geçtikten sonra kodunuzun çalışmaya devam ettiğinden emin olmak için [bulut iş ortağı PORTALı API başvurusunda](cloud-partner-portal-api-overview.md) listelenen değişiklikleri gözden geçirin. Yalnızca Iş Ortağı Merkezi 'ne geçişten önce tümleştirilmiş mevcut ürünler için CPP API 'Lerini kullanın; Yeni ürünlerin Iş Ortağı Merkezi gönderme API 'Leri kullanması gerekir.

Bulut İş Ortağı Portalı API 'Leri kullanmak için iki zorunlu program varlığı gerekir: bir hizmet sorumlusu ve bir Azure Active Directory (Azure AD) erişim belirteci.

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Azure Active Directory kiracısında hizmet sorumlusu oluşturma

İlk olarak, Azure AD kiracınızda bir hizmet sorumlusu oluşturmanız gerekir. Bu kiracıya, Bulut İş Ortağı Portalı kendi izin kümesi atanır. Kodunuz, kişisel kimlik bilgileriniz yerine bu kiracıyı kullanarak API 'Leri çağırır. Hizmet sorumlusu oluşturma hakkında tam bir açıklama için bkz. [nasıl yapılır: portalı kullanarak kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="add-service-principal-to-your-account"></a>Hesabınıza hizmet sorumlusu ekleyin

Kiracınızda hizmet sorumlusunu oluşturduğunuza göre, bunu Iş Ortağı Merkezi portalı hesabınıza bir kullanıcı olarak ekleyebilirsiniz. Bir kullanıcı gibi, hizmet sorumlusu de Portal için bir sahip veya katılımcı olabilir. Ayrıntılar için aşağıdaki **sonraki adımlara** bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. [Azure AD uygulamalarını yönetme](manage-aad-apps.md).
