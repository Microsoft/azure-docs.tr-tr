---
title: Kimlik yönetimi için Azure Active Directory kiracınızı kullanarak uygulamaları görüntüleme
description: Kimlik yönetimi için Azure Active Directory kiracınızı kullanarak tüm uygulamaları görüntülemeyi öğrenin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: b11f2dab97b3c45502c6050e06f0f39312f8c3c0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378083"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Kimlik yönetimi için Azure AD kiracınızı kullanarak uygulamaları görüntüleme
[Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md) size temel bilgileri gösterir. Bu durumda, kimlik yönetimi için Azure AD kiracınızı kullanarak tüm uygulamaları görüntülemeyi öğreneceksiniz. Bu makale, bulacağınız uygulama türlerinin biraz daha ayrıntılı bir şekilde bir bölümünü ele alır.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Tüm uygulamalar listesinde neden belirli bir uygulama görünüyor?
**Tüm uygulamalara** filtre uygulandığında **tüm uygulamalar** **listesi** kiracınızdaki tüm hizmet sorumlusu nesnelerini gösterir. Hizmet sorumlusu nesneleri, bu listede çeşitli yollarla görünebilir:
- Uygulama galerisinden herhangi bir uygulamayı eklediğinizde şunlar da dahildir:
   - **Azure AD-kurumsal uygulamalar** – Azure AD portalındaki **Kurumsal uygulamalar** seçeneği kullanılarak kiracınıza eklenen uygulamalar. Genellikle SAML standardı kullanılarak tümleştirilmiş uygulamalar.
   - **Azure AD-uygulama kayıtları** : Azure ad portalındaki **uygulama kayıtları** seçeneği kullanılarak kiracınıza eklenen uygulamalar. Genellikle açık KIMLIK Connect ve OAuth standartlarını kullanan özel geliştirmiş uygulamalar.
   - **Uygulama proxy 'Si uygulamaları** – dışarıdan güvenli çoklu oturum açma sağlamak istediğiniz şirket içi ortamınızda çalışan bir uygulamadır
- İçin kaydolurken veya ' de oturum açarken, Azure Active Directory ile tümleştirilmiş üçüncü taraf bir uygulama. [Smartsheet](https://app.smartsheet.com/b/home) veya [Docusign](https://www.docusign.net/member/MemberLogin.aspx)bir örnektir.
- Microsoft 365 gibi Microsoft uygulamaları.
- [Uygulama kayıt defteri](../develop/quickstart-register-app.md) 'ni kullanarak özel olarak geliştirilmiş bir uygulama oluşturarak yeni bir uygulama kaydı eklediğinizde
- [V 2.0 uygulama kayıt portalını](../develop/quickstart-register-app.md) kullanarak özel olarak geliştirilmiş bir uygulama oluşturarak yeni bir uygulama kaydı eklediğinizde
- Bir uygulama eklediğinizde, Visual Studio 'nun [ASP.NET kimlik doğrulama yöntemlerini](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) veya [bağlı hizmetleri](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) kullanarak geliştirdiğinizi
- [Azure AD PowerShell modülünü](/powershell/azure/active-directory/install-adv2) kullanarak bir hizmet sorumlusu nesnesi oluşturduğunuzda
- [Bir uygulamayı](../develop/howto-convert-app-to-be-multi-tenant.md) kiracınızdaki verileri kullanmak üzere yönetici olarak kabul ettiğinizde
- Bir Kullanıcı kiracınızdaki verileri kullanmak üzere [bir uygulamaya onay verirse](../develop/howto-convert-app-to-be-multi-tenant.md)
- Kiracınızda veri depolayan belirli hizmetleri etkinleştirdiğinizde. Parola sıfırlama ilkenizi güvenli bir şekilde depolamak için bir hizmet sorumlusu olarak modellenen parola sıfırlama bir örnektir.

Uygulamaların nasıl ve neden eklenediğine ilişkin daha fazla bilgi edinin, bkz. [uygulamalar Azure AD 'ye nasıl eklenir](../develop/active-directory-how-applications-are-added.md).

## <a name="next-steps"></a>Sonraki adımlar
[Uygulamaları Azure Active Directory ile yönetme](what-is-application-management.md)
