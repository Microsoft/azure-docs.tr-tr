---
title: API önkoşulları | Azure Marketi
description: Önkoşullar Bulut İş Ortağı Portalı API 'Leri kullanarak.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d3c2d89d3c3561e86047529e5b284e4481fc1652
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819696"
---
<a name="api-prerequisites"></a>API önkoşulları
================

Bulut İş Ortağı Portalı API 'Leri kullanmanız gereken iki adet gerekli program varlığı vardır: bir hizmet sorumlusu ve bir Azure Active Directory (Azure AD) erişim belirteci.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Azure Active Directory kiracınızda hizmet sorumlusu oluşturma
----------------------------------------------------------------

İlk olarak, Azure AD kiracınızda bir hizmet sorumlusu oluşturmanız gerekir. Bu kiracıya, Bulut İş Ortağı Portalı kendi izin kümesi atanır. Kodunuz, kişisel kimlik bilgilerinizi kullanmak yerine bu kiracı olarak kullanan API 'Leri çağırır.  Hizmet sorumlusu oluşturma hakkında tam bir açıklama için bkz. [Portal 'ı kullanarak kaynaklara erişebilen Azure Active Directory uygulaması ve hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Hizmet sorumlusunu hesabınıza ekleyin
-----------------------------------------

Kiracınızda hizmet sorumlusunu oluşturduğunuza göre, onu Bulut İş Ortağı Portalı hesabınıza bir kullanıcı olarak ekleyebilirsiniz. Bir kullanıcı gibi, hizmet sorumlusu de Portal için bir sahip veya katılımcı olabilir.

Hizmet sorumlusunu eklemek için aşağıdaki adımları kullanın:

1. Bulut İş Ortağı Portalı oturum açın. 
2. Sol menü çubuğunda **Kullanıcılar** ' a tıklayın ve **Kullanıcı Ekle**' yi seçin.

   ![Portala kullanıcı ekleme](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. **Tür** açılır listesinden **hizmet sorumlusu** ' nı seçin ve aşağıdaki ayrıntıları ekleyin:

-   Hizmet sorumlusu için **kolay bir ad** (örneğin `spAccount`).
-   **Uygulama kimliği**. Bu tanımlayıcıyı bulmak için [Azure portalına](https://portal.azure.com)gidin, **Azure Active Directory**' a tıklayın, **uygulama kayıtları**' i seçin ve uygulamanıza tıklayın.
-   Azure AD kiracınız için **DIZIN kimliği**olarak da BILINEN **Kiracı kimliği**. Bu tanımlayıcıyı, [Azure portal](https://portal.azure.com)Azure Active Directory sayfasında, **Özellikler**altında bulabilirsiniz.
-   Hizmet sorumlusu nesnenizin **nesne kimliği** . Bu tanımlayıcıyı Azure portal edinebilirsiniz. **Azure Active Directory**' e gidin, **uygulama kayıtları**' i seçin, uygulamanıza tıklayın ve **yerel dizinde yönetilen uygulama**altında uygulama adı ' na tıklayın. Ardından, nesne KIMLIĞINI bulmak için **Özellikler** sayfasına gidin. Uygulamanızdaki ilk nesne KIMLIĞINI yakalayıp olmadığından ve bunun yerine yönetilen uygulamadaki nesne KIMLIĞI olmadığından emin olun.
-   RBAC için kullanılacak hesapla ilişkili **rol** .

     ![Portala yönetilen uygulama ekleme](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Hizmet sorumlusunu hesabınıza eklemek için **Ekle** ' ye tıklayın.

   ![Hizmet sorumlusu ekleme](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Azure AD erişim belirteci alın
----------------------------

Bulut İş Ortağı Portalı API 'Leri, kimlik doğrulaması sırasında aşağıdaki varlıkları ve protokolleri kullanır:

- Kaynaklara erişim istemek için bir JSON Web Token (JWT) taşıyıcı belirteci
- Kimliği doğrulamak için [OpenID Connect](https://openid.net/connect/) (OIDC) protokolü
- Kimlik yetkilisi olarak [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)

Program aracılığıyla JWT belirtecini almak için iki prensibi yaklaşım vardır:

- .NET için Microsoft kimlik doğrulama kitaplığı 'nı ([msal.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)) kullanın.  Bu üst düzey yaklaşım .NET geliştiricileri için önerilir. 
- Şu formu alan Azure AD OAuth **belirteç** uç noktasına BIR **http post** isteği oluşturun:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Şimdi, API istekleri için yetkilendirme üstbilgisinin bir parçası olarak bu belirteci geçirebilirsiniz.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Bu başvurudaki tüm API 'Ler için, yetkilendirme üst bilgisi her zaman geçti olarak kabul edilir, bu nedenle açıkça bahsedilmez.

İstekinizdeki kimlik doğrulama hatalarıyla karşılaşırsanız bkz. [kimlik doğrulama hatalarını giderme](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
