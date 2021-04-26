---
title: Azure Active Directory B2C özel öznitelikleri tanımlayın | Microsoft Docs
description: Müşterileriniz hakkında bilgi toplamak için Azure Active Directory B2C ' de uygulamanız için özel öznitelikler tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607926"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel öznitelikleri tanımlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[Özel ilkeler kullanarak talep ekleme ve Kullanıcı girişini özelleştirme](configure-user-input.md) makalesinde yerleşik [Kullanıcı profili özniteliklerini](user-profile-attributes.md)nasıl kullanacağınızı öğreneceksiniz. Bu makalede, Azure Active Directory B2C (Azure AD B2C) dizininizde özel bir özniteliği etkinleştirirsiniz. Daha sonra, yeni özniteliğini [Kullanıcı akışlarında](user-flow-overview.md) veya [özel ilkelerde](custom-policy-get-started.md) aynı anda özel bir talep olarak kullanabilirsiniz.

Azure AD B2C dizininizde [yerleşik öznitelikler](user-profile-attributes.md) bulunur. Bununla birlikte, belirli senaryonuzu yönetmek için genellikle kendi öznitelerinizi oluşturmanız gerekir, örneğin:

* Müşteriye yönelik bir uygulamanın **Loyaltyıd** özniteliğini kalıcı hale getirmek gerekir.
* Bir kimlik sağlayıcısı, kalıcı olması gereken benzersiz bir Kullanıcı tanımlayıcısına sahip olan **Uniqueuserguid** öğesine sahiptir.
* Özel bir Kullanıcı yolculuğu, diğer mantığın üzerinde çalışması için kullanıcının, **Migrationstatus** durumunu kalıcı hale getirebilmesini gerektirir.

Terimler *uzantı özelliği*, *özel öznitelik* ve *özel talep* , bu makalenin bağlamıyla aynı şeyi ifade eder. Ad, uygulama, nesne veya ilke gibi içeriğe göre değişir.

Azure AD B2C, her kullanıcı hesabında depolanan özniteliklerin kümesini genişletmenizi sağlar. Ayrıca, [MICROSOFT Graph API](microsoft-graph-operations.md)'sini kullanarak bu öznitelikleri okuyabilir ve yazabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Özel öznitelik oluştur

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olmak için Azure portalın sağ üst köşesinde bu dizine geçin. Abonelik bilgilerinizi ve ardından **Dizin Değiştir**’i seçin.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kullanıcı öznitelikleri**' ni seçin ve ardından **Ekle**' yi seçin.
1. Özel öznitelik için bir **ad** sağlayın (örneğin, "shoesize")
1. Bir **veri türü** seçin. Yalnızca **String**, **Boolean** ve **Int** kullanılabilir.
1. İsteğe bağlı olarak, bilgilendirici amaçlar için bir **Açıklama** girin.
1. **Oluştur**’a tıklayın.

Özel öznitelik artık **Kullanıcı öznitelikleri** listesinde ve Kullanıcı akışlarınızda kullanılmak üzere kullanılabilir. Özel bir öznitelik, **Kullanıcı öznitelikleri** listesine eklediğinizde değil, yalnızca herhangi bir Kullanıcı akışında kullanıldığı zaman oluşturulur.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Kullanıcı akışındaki özel bir öznitelik kullanma

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. İlkeyi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. **Kullanıcı öznitelikleri** ' ni seçin ve ardından özel özniteliği seçin (örneğin, "shoesize"). **Kaydet**’e tıklayın.
1. **Uygulama talepleri** ' ı seçin ve ardından özel özniteliği seçin.
1. **Kaydet**’e tıklayın.

Yeni oluşturulan özel özniteliği kullanan bir Kullanıcı akışı kullanarak yeni bir kullanıcı oluşturduktan sonra, nesne [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)'da sorgulanabilir. Alternatif olarak, müşteri deneyimini doğrulamak için Kullanıcı akışındaki [Kullanıcı akışını Çalıştır](./tutorial-create-user-flows.md) özelliğini de kullanabilirsiniz. Şimdi kayıt yolculuğu sırasında toplanan öznitelikler listesinde **Showesize** ' i görmeniz ve uygulamanıza geri gönderilen belirteçte onu görmeniz gerekir.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C uzantıları uygulaması

Uzantı öznitelikleri, bir kullanıcı için veri içerse de, yalnızca bir uygulama nesnesine kaydedilebilir. Extension özniteliği adlı uygulamaya iliştirilir `b2c-extensions-app` . Bu uygulamayı, Kullanıcı verilerini depolamak için Azure AD B2C tarafından kullanıldığından değiştirmeyin. Bu uygulamayı Azure AD B2C, uygulama kayıtları altında bulabilirsiniz. Uygulama özelliklerini al:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları**' yi seçin ve ardından **tüm uygulamalar**' ı seçin.
1. `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` uygulamasını seçin.
1. Aşağıdaki tanımlayıcıları panonuza kopyalayın ve kaydedin:
    * **Uygulama kimliği**. Örnek: `11111111-1111-1111-1111-111111111111`.
    * **Nesne kimliği**. Örnek: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Özel ilkenizi değiştirme

İlkenizde özel öznitelikleri etkinleştirmek için AAD-Common teknik profil meta verilerinde **uygulama kimliği** ve uygulama **nesne kimliği** sağlayın. *AAD ortak* teknik profili, temel [Azure Active Directory](active-directory-technical-profile.md) teknik PROFILINDE bulunur ve Azure AD Kullanıcı yönetimi için destek sağlar. Diğer Azure AD teknik profilleri, yapılandırmasından yararlanmak için AAD-Common içerir. Uzantı dosyasındaki AAD-Common teknik profilini geçersiz kılın.

1. İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. ClaimsProviders öğesini bulun. ClaimsProviders öğesine yeni bir ClaimsProvider ekleyin.
1. Açılış ve kapanış öğeleri arasında daha önce kaydettiğiniz **uygulama kimliğini** ekleyin `<Item Key="ClientId">` `</Item>` .
1. Açılış ve kapanış öğeleri arasında daha önce kaydettiğiniz **uygulama ObjectID** 'yi ekleyin `<Item Key="ApplicationObjectId">` `</Item>` .

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="AAD-Common">
            <Metadata>
              <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
              <Item Key="ClientId"></Item>
              <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
              <Item Key="ApplicationObjectId"></Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles> 
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Özel ilkenizi karşıya yükleyin

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip Azure AD B2C kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
4. **Kimlik deneyimi çerçevesini** seçin.
5. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından değiştirdiğiniz TrustFrameworkExtensions.xml ilke dosyalarını karşıya yükleyin.

> [!NOTE]
> Azure AD teknik profili, Dizin talebini ilk kez sürdüren, özel özniteliğin mevcut olup olmadığını denetler. Aksi takdirde, özel özniteliğini oluşturur.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Azure portal aracılığıyla özel öznitelik oluşturma

Aynı uzantı öznitelikleri, yerleşik ve özel ilkeler arasında paylaşılır. Portal deneyimi aracılığıyla özel öznitelikler eklediğinizde, bu öznitelikler her B2C kiracısında bulunan **B2C-Extensions-App** kullanılarak kaydedilir.

Bu öznitelikleri, özel ilkeleriniz içinde kullanmadan önce veya sonra Portal Kullanıcı arabirimini kullanarak oluşturabilirsiniz. Portalda **Loyaltyıd** özniteliğini oluşturduğunuzda, aşağıdaki şekilde buna başvurmanız gerekir:

|Name     |Kullanıldığı yer |
|---------|---------|
|`extension_loyaltyId`  | Özel ilke|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

Aşağıdaki örnek, özel özniteliklerin Azure AD B2C özel bir ilke talep tanımında kullanımını gösterir.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Aşağıdaki örnek, bir teknik profilde, girişte, çıkışta ve kalıcı taleplerde Azure AD B2C özel ilkede özel bir özniteliğin kullanımını gösterir.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>MS Graph API ile özel öznitelik kullanma

Microsoft Graph API, uzantı öznitelikleri olan bir kullanıcının oluşturulmasını ve güncelleştirilmesini destekler. Graph API uzantı öznitelikleri, yöntemi kullanılarak adlandırılır `extension_ApplicationClientID_attributename` ; burada, `ApplicationClientID` uygulamanın uygulama **(istemci) kimliğidir** `b2c-extensions-app` . Uzantı öznitelik adında temsil edilen **uygulama (istemci) kimliğinin** hiçbir tire içerdiğini unutmayın. Örnek:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Sonraki adımlar

[Özel ilkeler kullanarak talepler ekleme ve Kullanıcı girişini özelleştirme](configure-user-input.md)kılavuzunu izleyin. Bu örnek, yerleşik bir ' City ' talebi kullanır. Özel bir öznitelik kullanmak için ' City ' değerini kendi özel nitelikleriyle değiştirin.
