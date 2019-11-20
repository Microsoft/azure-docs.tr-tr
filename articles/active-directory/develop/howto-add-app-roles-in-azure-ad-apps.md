---
title: Azure Active Directory kayıtlı uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma
titleSuffix: Microsoft identity platform
description: Azure Active Directory kayıtlı bir uygulamaya uygulama rolleri ekleme, bu rollere Kullanıcı ve Grup atama ve bunları belirteçteki `roles` talebine alma hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c838c6fe256e2960842c5b40f58f8a5d5b1fdf3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473774"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma

Rol tabanlı erişim denetimi (RBAC), uygulamalarda yetkilendirmeyi zorlamak için popüler bir mekanizmadır. RBAC kullanılırken, yönetici, bireysel kullanıcılara veya gruplara değil, rollere izinler verir. Yönetici daha sonra, hangi içeriğe ve işlevlere kimlerin erişebileceğini denetlemek için farklı kullanıcılara ve gruplara roller atayabilir.

Uygulama rolleri ve rol talepleri ile RBAC kullanarak, geliştiriciler uygulamalarında daha az çaba harcayacak şekilde yetkilendirmeyi güvenli bir şekilde uygulayabilir.

Diğer bir yaklaşım ise, [WebApp-Groupclaim-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet)bölümünde gösterildiği gıbı Azure AD gruplarını ve grup taleplerini kullanmaktır. Azure AD grupları ve uygulama rollerinin hiçbir şekilde birbirini dışlamalı olması gerekmez; daha da ayrıntılı erişim denetimi sağlamak için birlikte kullanılabilir.

## <a name="declare-roles-for-an-application"></a>Bir uygulama için roller bildirme

Bu uygulama rolleri, uygulamanın kayıt bildiriminde [Azure Portal](https://portal.azure.com) tanımlanır.  Kullanıcı uygulamada oturum açtığında Azure AD, her rol için kullanıcıya tek tek ve grup üyeliğinden bir `roles` talebi yayar.  Kullanıcılara ve grupların rollere atanması, portalın kullanıcı arabiriminden ya da program aracılığıyla [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview)kullanılarak yapılabilir.

### <a name="declare-app-roles-using-azure-portal"></a>Azure portal kullanarak uygulama rolleri bildirme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üstteki çubukta hesabınızı seçin ve ardından dizin ' i **değiştirin**.
1. **Dizin + abonelik** bölmesi açıldıktan sonra, **Sık Kullanılanlar** veya **tüm dizinler** listesinden uygulamanızı kaydetmek istediğiniz Active Directory kiracıyı seçin.
1. Sol taraftaki Gezinti bölmesinde **tüm hizmetler** ' i seçin ve **Azure Active Directory**' yi seçin.
1. **Azure Active Directory** bölmesinde, tüm uygulamalarınızın listesini görüntülemek için **uygulama kayıtları (eski)** seçeneğini belirleyin.

     Burada görünmesini istediğiniz uygulamayı görmüyorsanız, listeyi kısıtlamak için **uygulama kayıtları (eski)** listesinin en üstünde bulunan çeşitli filtreleri kullanın veya uygulamanızı bulmak için listeyi aşağı kaydırın.

1. İçinde uygulama rollerini tanımlamak istediğiniz uygulamayı seçin.
1. Uygulamanızın dikey penceresinde, **bildirim**' ı seçin.
1. `appRoles` ayarını bularak ve tüm uygulama rollerinizi ekleyerek uygulama bildirimini düzenleyin.

     > [!NOTE]
     > Bu Bildirimdeki her uygulama rolü tanımının `id` özelliği için farklı bir GUID 'e sahip olması gerekir. 
     > 
     > Her uygulama rolü tanımının `value` özelliği, uygulamadaki kodda kullanılan dizelerle tam olarak eşleşmelidir. `value` özelliği boşluk içeremez. Bunu yaparsanız, bildirimi kaydettiğinizde bir hata alırsınız.
     
1. Bildirimi kaydedin.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek, `users`atayabilmeniz için `appRoles` gösterir.

> [!NOTE]
>`id` benzersiz bir GUID olmalıdır.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>`displayName` boşluk içeremez.

`users`, `applications`veya her ikisini de hedeflemek için uygulama rolleri tanımlayabilirsiniz. `applications`için kullanılabilir olduğunda, uygulama rolleri **gerekli izinler** dikey penceresinde uygulama izinleri olarak görünür. Aşağıdaki örnek, `Application`yönelik bir uygulama rolünü gösterir.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Tanımlanan rol sayısı, uygulama bildiriminin sahip olduğu limitleri etkiler. [Bildirim limitleri](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) sayfasında ayrıntılı olarak ele alınmıştır.

### <a name="assign-users-and-groups-to-roles"></a>Rollere Kullanıcı ve Grup atama

Uygulamanıza uygulama rolleri ekledikten sonra, bu rollere kullanıcılar ve gruplar atayabilirsiniz.

1. **Azure Active Directory** bölmesinde, **Azure Active Directory** sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.
1. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

     Burada görünmesini istediğiniz uygulamayı görmüyorsanız, listeyi kısıtlamak için **tüm uygulamalar** listesinin en üstündeki çeşitli filtreleri kullanın veya uygulamanızı bulmak için listeyi aşağı kaydırın.

1. Rollere kullanıcı veya güvenlik grubu atamak istediğiniz uygulamayı seçin.
1. Uygulamanın sol taraftaki gezinti menüsünde **Kullanıcılar ve gruplar** bölmesini seçin.
1. **Kullanıcılar ve gruplar** listesinin en üstünde, **atama Ekle** bölmesini açmak için **Kullanıcı Ekle** düğmesini seçin.
1. **Atama Ekle** bölmesinden **Kullanıcılar ve gruplar** seçicisini seçin.

     Kullanıcı ve güvenlik gruplarının listesi, belirli bir kullanıcı veya grubu aramak ve bulmak için bir metin kutusuyla birlikte gösterilir. Bu ekran, tek bir go içinde birden çok kullanıcı ve grup seçmenizi sağlar.

1. Kullanıcıları ve grupları seçmeyi tamamladıktan sonra, sonraki bölüme geçmek için alt kısımdaki **Seç** düğmesine basın.
1. **Atama Ekle** bölmesinden **rol seçicisini Seç ' i** seçin. Uygulama bildiriminde daha önce belirtilen tüm roller görünür.
1. Bir rol seçin ve **Seç** düğmesine basın.
1. Uygulamanın kullanıcı ve grupların atamalarını sona erdirmeyi sağlamak için alt kısımdaki **ata** düğmesine basın.
1. Eklediğiniz kullanıcı ve grupların, güncelleştirilmiş **Kullanıcılar ve gruplar** listesinde görüntülendiğini doğrulayın.

## <a name="more-information"></a>Daha fazla bilgi

- [Rol taleplerini &amp; Azure AD uygulama rollerini kullanarak bir Web uygulamasında yetkilendirme (örnek)](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)
- [Uygulamalarınızda güvenlik gruplarını ve uygulama rollerini kullanma (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, artık grup talepleri ve uygulama rolleriyle birlikte](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Azure Active Directory uygulama bildirimi](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD erişim belirteçleri](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
