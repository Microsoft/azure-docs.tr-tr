---
title: Uygulama rolleri ekleme ve bir belirteçten edinme | Mavisi
titleSuffix: Microsoft identity platform
description: Azure Active Directory kayıtlı bir uygulamaya uygulama rolleri ekleme, bu rollere Kullanıcı ve Grup atama ve bunları belirteçteki ' Roller ' talebinde alma hakkında bilgi edinin.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fce963bd9ffdc6f768d7b3de4a9e4870add06136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104255"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Nasıl yapılır: uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma

Rol tabanlı erişim denetimi (RBAC), uygulamalarda yetkilendirmeyi zorlamak için popüler bir mekanizmadır. RBAC kullanılırken, yönetici, bireysel kullanıcılara veya gruplara değil, rollere izinler verir. Yönetici daha sonra, hangi içeriğe ve işlevlere kimlerin erişebileceğini denetlemek için farklı kullanıcılara ve gruplara roller atayabilir.

Uygulama rolleri ve rol talepleri ile RBAC kullanarak, geliştiriciler uygulamalarında yetkilendirmeyi daha az çaba ile güvenli bir şekilde uygulayabilir.

Diğer bir yaklaşım, GitHub 'daki [Active-Directory-aspnetcore-WebApp-openıdconnect-v2](https://aka.ms/groupssample) kod örneğinde gösterildiği gıbı Azure AD gruplarını ve grup taleplerini kullanmaktır. Azure AD grupları ve uygulama rolleri birbirini dışlamalı değildir; daha da ayrıntılı erişim denetimi sağlamak için birlikte kullanılabilir.

## <a name="declare-roles-for-an-application"></a>Bir uygulama için roller bildirme

Uygulama rollerini [Azure Portal](https://portal.azure.com)kullanarak tanımlarsınız. Uygulama rolleri genellikle bir hizmeti, uygulamayı veya API 'yi temsil eden bir uygulama kaydında tanımlanmıştır. Kullanıcı uygulamada oturum açtığında Azure AD, `roles` her rol için Kullanıcı veya hizmet sorumlusunun kullanıcıya ve grup üyeliğinden ayrı ayrı verildiği bir talep yayar. Bu, talep tabanlı yetkilendirme uygulamak için kullanılabilir. Uygulama rolleri, [bir kullanıcıya veya kullanıcı grubuna](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app)atanabilir. Ayrıca, uygulama rolleri başka bir uygulama için hizmet sorumlusuna veya [yönetilen bir kimliğin hizmet sorumlusu](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md)için de atanabilir.

> [!IMPORTANT]
> Şu anda bir gruba bir hizmet sorumlusu ekler ve sonra bu gruba bir uygulama rolü atarsanız, Azure AD, `roles` BT 'nin verdiği belirteçlere talebi eklemez.

Azure portal kullanarak uygulama rollerini belirtmenin iki yolu vardır:

* [Uygulama rolleri Kullanıcı arabirimi](#app-roles-ui--preview) | Önizleme
* [Uygulama bildirimi Düzenleyicisi](#app-manifest-editor)

Eklediğiniz rol sayısı, Azure Active Directory tarafından zorlanan uygulama bildirimi sınırlarına doğru sayılır. Bu sınırlamalar hakkında daha fazla bilgi için [Azure Active Directory uygulama bildirimi başvurusunun](reference-app-manifest.md) [bildirim sınırları](./reference-app-manifest.md#manifest-limits) bölümüne bakın.

### <a name="app-roles-ui--preview"></a>Uygulama rolleri Kullanıcı arabirimi | Önizleme

> [!IMPORTANT]
> Uygulama rolleri portalı Kullanıcı Arabirimi özelliği [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Azure portal Kullanıcı arabirimini kullanarak bir uygulama rolü oluşturmak için:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresi ' ni seçin ve ardından uygulama rolü eklemek istediğiniz uygulama kaydını içeren Azure Active Directory kiracıyı seçin.
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet**' in altında **uygulama kayıtları**' yi seçin ve ardından uygulama rollerini tanımlamak istediğiniz uygulamayı seçin.
1. **Uygulama rollerini seçin | Önizleme** yapın ve ardından **uygulama rolü oluştur**' u seçin.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure portal bir uygulama kaydının uygulama rolleri bölmesi":::
1. **Uygulama rolü oluştur** bölmesinde, rolün ayarlarını girin. Görüntüyü izleyen tablo her bir ayarı ve parametrelerini açıklar.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Uygulama kaydının uygulama rolleri Azure portal bağlam bölmesi oluşturma":::

    | Alan | Açıklama | Örnek |
    |-------|-------------|---------|
    | **Görünen ad** | Yönetici onayı ve uygulama atama deneyimlerinde görünen uygulama rolü için görünen ad. Bu değer boşluk içerebilir. | `Survey Writer` |
    | **İzin verilen üye türleri** | Bu uygulama rolünün kullanıcılara, uygulamalara veya her ikisine de atanıp atanamayacağını belirtir.<br/><br/>İçin kullanılabilir olduğunda `applications` , uygulama rolleri bir uygulama kaydının **Yönet** bölümünde uygulama izinleri olarak görünür > API IZINLERI > bir **API > uygulama izinleri seçin > bir izin >**. | `Users/Groups` |
    | **Değer** | Uygulamanın belirteçte beklediği rol talebinin değerini belirtir. Değer, uygulamanın kodunda başvurulan dize ile tam olarak eşleşmelidir. Değer boşluk içeremez. | `Survey.Create` |
    | **Açıklama** | Yönetici uygulama atama ve onay deneyimleri sırasında görünen uygulama rolünün daha ayrıntılı bir açıklaması. | `Writers can create surveys.` |
    | **Bu uygulama rolünü etkinleştirmek istiyor musunuz?** | Uygulama rolünün etkinleştirilip etkinleştirilmeyeceğini belirtir. Bir uygulama rolünü silmek için bu onay kutusunun işaretini kaldırın ve silme işlemini denemeden önce değişikliği uygulayın. | *Edildikten* |

1. Yaptığınız değişiklikleri kaydetmek için **Apply**'ı (Uygula) seçin.

### <a name="app-manifest-editor"></a>Uygulama bildirimi Düzenleyicisi

Bildirimi doğrudan düzenleyerek roller eklemek için:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresi ' ni seçin ve ardından uygulama rolü eklemek istediğiniz uygulama kaydını içeren Azure Active Directory kiracıyı seçin.
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet**' in altında **uygulama kayıtları**' yi seçin ve ardından uygulama rollerini tanımlamak istediğiniz uygulamayı seçin.
1. **Yönet** altında, **bildirim**' ı seçin.
1. `appRoles`Ayarları bularak ve uygulama rollerinizi ekleyerek uygulama bildirimini düzenleyin. `users`, `applications` Veya her ikisini de hedefleyen uygulama rollerini tanımlayabilirsiniz. Aşağıdaki JSON parçacıkları her ikisinin de örneklerini gösterir.
1. Bildirimi kaydedin.

Bildirimdeki her uygulama rolü tanımının değeri için benzersiz bir GUID olması gerekir `id` .

`value`Her uygulama rolü tanımının özelliği, uygulamadaki kodda kullanılan dizelerle tam olarak eşleşmelidir. `value`Özellik boşluk içeremez. Bunu yaparsanız, bildirimi kaydettiğinizde bir hata alırsınız.

#### <a name="example-user-app-role"></a>Örnek: Kullanıcı uygulama rolü

Bu örnek, bir uygulamasına atayabileceğiniz adlı bir uygulama rolü tanımlar `Writer` `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Örnek: uygulama uygulama rolü

İçin kullanılabilir olduğunda `applications` , uygulama rolleri bir uygulama kaydının **Yönet** bölümünde uygulama izinleri olarak görünür > API IZINLERI > bir **API > uygulama izinleri seçin > bir izin >**.

Bu örnek, şunu hedefleyen bir uygulama rolü gösterir `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Rollere Kullanıcı ve Grup atama

Uygulamanıza uygulama rolleri ekledikten sonra, rollere kullanıcılar ve gruplar atayabilirsiniz. Kullanıcılara ve grupların rollere atanması, portalın kullanıcı arabiriminden ya da program aracılığıyla [Microsoft Graph](/graph/api/user-post-approleassignments)kullanılarak yapılabilir. Çeşitli uygulama rollerine atanan kullanıcılar uygulamada oturum açtığında, belirteçlerinin talep içinde kendilerine atanmış rolleri olur `roles` .

Azure portal kullanarak kullanıcılara ve gruplara roller atamak için:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. **Azure Active Directory**, sol taraftaki gezinti menüsünde **Kurumsal uygulamalar** ' ı seçin.
1. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin. Uygulamanız listede görünmezse, listeyi kısıtlamak için **tüm uygulamalar** listesinin en üstündeki filtreleri kullanın veya uygulamanızı bulmak için listeyi aşağı kaydırın.
1. Rollere kullanıcı veya güvenlik grubu atamak istediğiniz uygulamayı seçin.
1. **Yönet** bölümünde **Kullanıcılar ve gruplar**'ı seçin.
1. **Atama Ekle** bölmesini açmak Için **Kullanıcı Ekle** ' yi seçin.
1. **Atama Ekle** bölmesinden **Kullanıcılar ve gruplar** seçicisini seçin. Kullanıcıların ve güvenlik gruplarının listesi görüntülenir. Belirli bir kullanıcı veya grup için arama yapabilir ve listede görünen birden çok kullanıcı ve grubu seçebilirsiniz.
1. Kullanıcılar ve gruplar ' ı seçtikten sonra, devam etmek için **Seç** düğmesini seçin.
1. **Atama Ekle** bölmesinde **bir rol Seç** ' i seçin. Uygulama için tanımladığınız tüm roller görüntülenir.
1. Bir rol seçin ve **Seç** düğmesini seçin.
1. Kullanıcılara ve grupların uygulamaya atanmasını tamamlayacak **ata** düğmesini seçin.

Eklediğiniz kullanıcı ve grupların **Kullanıcılar ve gruplar** listesinde göründüğünü doğrulayın.

## <a name="assign-app-roles-to-applications"></a>Uygulamalara uygulama rolleri atama

Uygulamanıza uygulama rolleri ekledikten sonra, [Microsoft Graph](/graph/api/user-post-approleassignments)kullanarak bir istemci uygulamasına Azure Portal veya program aracılığıyla bir uygulama rolü atayabilirsiniz.

Uygulama rollerini bir uygulamaya atadığınızda, *Uygulama izinleri* oluşturursunuz. Uygulama izinleri genellikle kullanıcı etkileşimi olmadan, kimlik doğrulaması yapması ve yetkili API çağrıları yapmak zorunda olması gereken arka plan uygulamaları veya arka uç hizmetleri tarafından kullanılır.

Azure portal kullanarak uygulama rollerini bir uygulamaya atamak için:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. **Azure Active Directory**, sol taraftaki gezinti menüsünde **uygulama kayıtları** ' i seçin.
1. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin. Uygulamanız listede görünmezse, listeyi kısıtlamak için **tüm uygulamalar** listesinin en üstündeki filtreleri kullanın veya uygulamanızı bulmak için listeyi aşağı kaydırın.
1. Uygulama rolü atamak istediğiniz uygulamayı seçin.
1. **API izinlerini** seçin  >  **izin Ekle**.
1. **API 'Lerim** sekmesini seçin ve ardından uygulama rollerini tanımladığınız uygulamayı seçin.
1. **Uygulama izinleri**' ni seçin.
1. Atamak istediğiniz rol (ler) i seçin.
1. Rol (ler) in eklenmesi için **Izin Ekle** düğmesini seçin.

Yeni eklenen roller, uygulama kaydınızın **API izinleri** bölmesinde görünmelidir.

#### <a name="grant-admin-consent"></a>Yönetici onayı verme

Bunlar *Uygulama izinleri* olmadığından, temsilci izinleri olmadığından, yönetici uygulamaya atanan uygulama rollerini kullanmak için izin vermelidir.

1. Uygulama kaydının **API izinleri** bölmesinde, **Için \<tenant name\> yönetici onayı ver**' i seçin.
1. İstenen izinler için izin vermek isteyip istemediğiniz sorulduğunda **Evet** ' i seçin.

**Durum** sütunu **için \<tenant name\> izin verildiğini** yansıtmalıdır.

## <a name="use-app-roles-in-your-web-api"></a>Web API 'niz içindeki uygulama rollerini kullanma

Uygulama rollerini tanımladıktan ve bunları bir Kullanıcı, Grup veya uygulamaya atadıktan sonra, bir sonraki adımınız, API çağrıldığında bu rolleri denetleyen Web API 'nize kod eklemektir. Diğer bir deyişle, bir istemci uygulama bir API işlemi istediğinde kimlik doğrulama gerektirdiğinde, API 'nin kodu, kapsamın istemci uygulamanın çağrısında sunulan erişim simgesinde olduğunu doğrulamalıdır.

Web API 'nize yetkilendirme eklemeyi öğrenmek için bkz. [Protected Web API: kapsamları ve uygulama rollerini doğrulama](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>Uygulama rolleri ve gruplar

Yetkilendirme için uygulama rollerini veya grupları kullanabilirsiniz, ancak bunlar arasındaki önemli farklılıklar, senaryolarınız için kullanmaya karar vereceğiniz bir etkisi olabilir.

| Uygulama rolleri                                                                          | Gruplar                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Bunlar bir uygulamaya özeldir ve uygulama kaydında tanımlanmıştır. Bunlar uygulamayla birlikte taşınır. | Bunlar bir uygulamaya özgü değildir, ancak bir Azure AD kiracısıdır. |
| Uygulama rolleri, uygulama kayıtları kaldırıldığında kaldırılır.                      | Uygulama kaldırılsa bile gruplar bozulmadan kalır.            |
| Talep içinde verilmiştir `roles` .                                                     | Talep içinde verilmiştir `groups` .                                 |

Geliştiriciler, bir kullanıcının uygulamada oturum açıp açamayacağını denetlemek için uygulama rollerini kullanabilir veya bir uygulama, bir Web API 'SI için erişim belirteci elde edebilir. Bu güvenlik denetimini gruplara genişletmek için, geliştiriciler ve yöneticiler da uygulama rollerine güvenlik grupları atayabilir.

Uygulama rolleri, geliştiricilerin kendi uygulamalarında yetkilendirme parametrelerini tanıtmak ve denetlemek istediklerinde geliştiriciler tarafından tercih edilir. Örneğin, yetkilendirme grupları kullanan bir uygulama, hem Grup KIMLIĞI hem de ad farklı olduğundan bir sonraki kiracıya kesilir. Uygulama rollerini kullanan bir uygulama güvenli kalır. Aslında, uygulama rollerine gruplar atamak, SaaS uygulamalarıyla aynı nedenlerden dolayı popüler bir uygulamadır.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki kaynaklarla uygulama rolleri hakkında daha fazla bilgi edinin.

* GitHub’daki kod örnekleri
  * [ASP.NET Core Web uygulamasına grupları ve grup taleplerini kullanarak yetkilendirme ekleme](https://aka.ms/groupssample)
  * [Angular tek sayfalı uygulama (SPA) bir .NET Core Web API 'sini çağırma ve uygulama rollerini ve güvenlik gruplarını kullanma](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Başvuru belgeleri
  * [Azure AD uygulama bildirimi](./reference-app-manifest.md)
  * [Azure AD erişim belirteçleri](access-tokens.md)
  * [Azure AD KIMLIK belirteçleri](id-tokens.md)
  * [Uygulamanıza isteğe bağlı talepler sağlama](active-directory-optional-claims.md)
* Video: [Microsoft Identity platform (1:01:15) ile uygulamalarınızda yetkilendirme uygulama](https://www.youtube.com/watch?v=LRoc-na27l0)
