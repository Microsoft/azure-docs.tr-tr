---
title: Kullanıcıları Azure Active Directory B2C sosyal kimliklere geçir | Microsoft Docs
description: Graph API kullanarak sosyal kimliklere sahip kullanıcıların Azure AD B2C uygulamasına geçiş konusunda temel kavramları tartışın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0117a0881422584e3cb949661b1d58cd0257cf67
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853859"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: Kullanıcıları sosyal kimlikleriyle geçirme
Kimlik sağlayıcınızı Azure AD B2C geçirmeyi planlıyorsanız, kullanıcıları sosyal kimliklere geçirmeniz de gerekebilir. Bu makalede, aşağıdaki gibi, var olan sosyal kimlikler hesaplarının nasıl geçirileceği açıklanmaktadır: Azure AD B2C için Facebook, LinkedIn, Microsoft ve Google hesapları. Bu makale, federe kimlikler için de geçerlidir, ancak bu geçişler daha az yaygındır. Bu makalenin geri kalanında, sosyal hesaplar için geçerli olan diğer Federasyon hesabı türlerine de uygulanacak her şeyi göz önünde bulundurun.

## <a name="prerequisites"></a>Önkoşullar
Bu makale, Kullanıcı geçiş makalesinin devamlılığı ve sosyal kimlik geçişine odaklanır. Başlamadan önce [Kullanıcı geçişini](active-directory-b2c-user-migration.md)okuyun.

## <a name="social-identities-migration-introduction"></a>Sosyal kimlikler geçiş tanıtımı

* Azure AD B2C, **yerel hesapların** oturum açma adları (Kullanıcı adı veya e-posta adresi) Kullanıcı kaydındaki `signInNames` koleksiyonda depolanır. , `signInNames` Kullanıcı için oturum açma `signInName` adlarını belirten bir veya daha fazla kayıt içerir. Her oturum açma adı, kiracı genelinde benzersiz olmalıdır.

* **Sosyal hesapların** kimlikleri `userIdentities` koleksiyonda depolanır. Giriş, Facebook.com `issuerUserId`ve `issuer` veren için benzersiz bir kullanıcı tanımlayıcısı olan olan (kimlik sağlayıcısı adı) öğesini belirtir. `userIdentities` Öznitelik, sosyal kimlik sağlayıcısından sosyal hesap türünü ve benzersiz kullanıcı tanımlayıcısını belirten bir veya daha fazla UserIdentity kaydı içerir.

* **Yerel hesabı sosyal kimlik Ile birleştirin**. Belirtildiği gibi, yerel hesap oturum açma adları ve sosyal hesap kimlikleri farklı özniteliklerde depolanır. `signInNames`, `userIdentities` sosyal hesaplar için kullanıldığında yerel hesap için kullanılır. Tek bir Azure AD B2C hesabı yalnızca bir yerel hesap olabilir, yalnızca sosyal hesap veya bir Kullanıcı kaydındaki bir veya daha fazla sosyal kimlik ile yerel bir hesabı birleştirebilir. Bu davranış, bir kullanıcı yerel hesap kimlik bilgileriyle veya sosyal kimliklerle oturum açabilse de tek bir hesabı yönetmenizi sağlar.

* `UserIdentity`Tür-bir Azure AD B2C kiracısındaki sosyal hesap kullanıcısının kimliği hakkındaki bilgileri Içerir:
  * `issuer`Facebook.com gibi kullanıcı tanımlayıcısını veren kimlik sağlayıcısının dize temsili.
  * `issuerUserId`Sosyal kimlik sağlayıcısı tarafından Base64 kodlamalı biçimde kullanılan benzersiz kullanıcı tanımlayıcısı.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Kimlik sağlayıcısına bağlı olarak, **veren kullanıcı kimliği** , uygulama veya geliştirme hesabı başına belirli bir kullanıcı için benzersiz bir değerdir. Azure AD B2C ilkesini, daha önce sosyal sağlayıcı veya aynı geliştirme hesabı içindeki başka bir uygulama tarafından atanan aynı uygulama KIMLIĞIYLE yapılandırın.

## <a name="use-graph-api-to-migrate-users"></a>Kullanıcıları geçirmek için Graph API kullanma
Azure AD B2C Kullanıcı hesabını [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet)aracılığıyla oluşturursunuz. Graph API ile iletişim kurmak için, önce yönetici ayrıcalıklarına sahip bir hizmet hesabına sahip olmanız gerekir. Azure AD 'de bir uygulamayı ve kimlik doğrulamasını Azure AD 'ye kaydedersiniz. Uygulama kimlik bilgileri uygulama KIMLIĞI ve uygulama gizli bilgileridir. Uygulama, Graph API çağırmak için Kullanıcı olarak değil, kendisi gibi davranır. [Kullanıcı geçişi](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) makalesindeki adım 1 ' deki yönergeleri izleyin.

## <a name="required-properties"></a>Gerekli özellikler
Aşağıdaki listede, bir kullanıcı oluştururken gerekli olan özellikler gösterilmektedir.
* **Accountenabled** -true
* **DisplayName** -Kullanıcı için adres defterinde görüntülenecek ad.
* **passwordprofile** -kullanıcının parola profili. 

> [!NOTE]
> Yalnızca sosyal hesaplar için (yerel hesap kimlik bilgileri olmadan), yine de parolayı belirtmeniz gerekir. Azure AD B2C, sosyal hesaplar için belirttiğiniz parolayı yoksayar.

* **userPrincipalName** -Kullanıcı asıl adı (someuser@contoso.com). Kullanıcı asıl adı, kiracının doğrulanmış etki alanlarından birini içermelidir. UPN 'yi belirtmek için yeni GUID değeri oluşturun, ile birlikte `@` ve kiracı adınızı birleştirin.
* **Mailtakma ad** -Kullanıcı için posta diğer adı. Bu değer, userPrincipalName için kullandığınız KIMLIK ile aynı olabilir. 
* **Signınnames** -Kullanıcı için oturum açma adlarını belirten bir veya daha fazla Signınname kaydı. Her oturum açma adı şirket/kiracı genelinde benzersiz olmalıdır. Yalnızca sosyal hesap için bu özellik boş bırakılabilir.
* **Kullanıcı kimlikleri** -sosyal kimlik sağlayıcısından sosyal hesap türünü ve benzersiz kullanıcı tanımlayıcıyı belirten bir veya daha fazla UserIdentity kaydı.
* seçim **Diğer postalar** -yalnızca sosyal hesap için kullanıcının e-posta adresleri 

Daha fazla bilgi için bkz. [Graph API başvurusu](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Sosyal hesabı geçirme (yalnızca)
Yerel hesap kimlik bilgileri olmadan yalnızca sosyal hesap oluşturmak için, Graph API için bir HTTPS POST isteği gönderin. İstek gövdesi, oluşturulacak sosyal hesap kullanıcısının özelliklerini içerir. En azından gerekli özellikleri belirtmeniz gerekir. 


**YAYINLA**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Aşağıdaki form verilerini Gönder: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Sosyal hesabı yerel hesapla geçir
Sosyal kimliklerle birleştirilmiş bir yerel hesap oluşturmak için Graph API bir HTTPS POST isteği gönderin. İstek gövdesi, yerel hesap için oturum açma adı dahil olmak üzere, oluşturulacak sosyal hesap kullanıcısının özelliklerini içerir. En azından gerekli özellikleri belirtmeniz gerekir. 

**YAYINLA**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Aşağıdaki form verilerini Gönder: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular
### <a name="how-can-i-know-the-issuer-name"></a>Verenin adını nasıl öğrenebilirim?
Verenin adı veya kimlik sağlayıcı adı ilkenizde yapılandırılır. İçinde `issuer`belirttiğiniz değeri bilmiyorsanız şu yordamı izleyin:
1. Sosyal hesaplardan biriyle oturum açın
2. JWT belirtecinden `sub` değeri kopyalayın. `sub` Genellikle kullanıcının nesne kimliğini Azure AD B2C içerir. Veya Azure portal, kullanıcının özelliklerini açın ve nesne KIMLIĞINI kopyalayın.
3. [Azure AD Graph Explorer 'ı](https://graphexplorer.azurewebsites.net) açın
4. Yöneticinizle oturum açın.
5. Aşağıdaki GET isteğini çalıştırın. Userobjectıd 'yi kopyaladığınız Kullanıcı KIMLIĞIYLE değiştirin. **AL**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Azure AD B2C JSON dönüşü içindeki öğeyibulun.`userIdentities`
7. Seçim Ayrıca, `issuerUserId` değeri çözmek de isteyebilirsiniz.

> [!NOTE]
> B2C kiracısında yerel olan bir B2C kiracı yönetici hesabı kullanın. Hesap adı sözdizimi admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>Mevcut bir kullanıcıya sosyal kimlik eklemek mümkün mü?
Evet. Azure AD B2C hesap oluşturulduktan sonra sosyal kimliği ekleyebilirsiniz (bunun yerel veya sosyal hesap veya birleşim olduğunu belirtir). HTTPS PATCH isteği çalıştırın. Userobjectıd öğesini güncelleştirmek istediğiniz kullanıcı KIMLIĞIYLE değiştirin. 

**DÜZELTME EKİ**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Aşağıdaki form verilerini Gönder: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Birden çok sosyal kimlik eklemek mümkün mü?
Evet. Tek bir Azure AD B2C hesabı için birden çok sosyal kimlik ekleyebilirsiniz. HTTPS PATCH isteği çalıştırın. Userobjectıd 'yi Kullanıcı KIMLIĞIYLE değiştirin. 

**DÜZELTME EKİ**https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Aşağıdaki form verilerini Gönder: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>Seçim Kullanıcı geçiş uygulaması örneği
[Örnek App v2 'Yi indirip çalıştırın](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Örnek uygulama v2, tek hesapta yerel hesap, sosyal hesap ve yerel & Sosyal kimlikler dahil, sözde Kullanıcı verisi içeren bir JSON dosyası kullanır.  JSON dosyasını düzenlemek için `AADB2C.UserMigration.sln` Visual Studio çözümünü açın. `AADB2C.UserMigration` Projede dosyasını`UsersData.json` açın. Dosya, Kullanıcı varlıklarının bir listesini içerir. Her kullanıcı varlığı aşağıdaki özelliklere sahiptir:
* **Signınname** -yerel hesap için oturum açma için e-posta adresi
* **DisplayName** -kullanıcının görünen adı
* **FirstName** -kullanıcının adı
* **Soyadı** -kullanıcının soyadı
* **parola** Yerel hesap için kullanıcının parolası (boş olabilir)
* **veren** -sosyal hesap, kimlik sağlayıcısı adı
* Sosyal kimlik sağlayıcısı tarafından kullanılan benzersiz kullanıcı tanımlayıcısı olan sosyal hesap için **ıssueruserıd** . Değer düz metin biçiminde olmalıdır. Örnek uygulama bu değeri Base64 olarak kodlar.
* **e-posta** Yalnızca sosyal hesap için (birleştirilmemiş), kullanıcının e-posta adresi

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Örnekteki UsersData. json dosyasını verilerle güncelleştirmezseniz, örnek yerel hesap kimlik bilgileriyle oturum açabilir, ancak sosyal hesap örneklerine sahip olabilirsiniz. Sosyal hesaplarınızı geçirmek için gerçek verileri sağlayın.

Örnek uygulamayı kullanma hakkında daha fazla bilgi için, bkz [. Azure Active Directory B2C: Kullanıcı geçişi](active-directory-b2c-user-migration.md)
