---
title: Azure Active Directory B2C | özel ilkelerine kendi özniteliklerini ekleyin | Microsoft Docs
description: Uzantı özellikleri ve özel öznitelikler kullanma ve bunları kullanıcı arayüzüne ekleme hakkında bir anlatım.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 82a796a3252a4de6eacabcad45c61c864e963fe0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066166"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Özel bir profilde özel öznitelikler kullanma ilke düzenleme

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) dizininizde özel bir öznitelik oluşturacaksınız. Bu yeni özniteliği Profil düzenleme Kullanıcı yolculuğunda özel bir talep olarak kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Makalesindeki [adımları izleyin Azure Active Directory B2C: Özel ilkeler](active-directory-b2c-get-started-custom.md)ile çalışmaya başlayın.

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Özel ilkeler kullanarak Azure AD B2C müşterileriniz hakkında bilgi toplamak için özel öznitelikler kullanma
Azure AD B2C dizininiz yerleşik bir öznitelikler kümesiyle birlikte gelir. Örneklere **ad**, **Soyadı**, **şehir**, **posta kodu**ve **userPrincipalName**verilebilir. Genellikle aşağıdaki örnekler gibi kendi öznitelerinizi oluşturmanız gerekir:
* **Loyaltynumber** gibi bir öznitelik için müşteriye yönelik bir uygulamanın kalıcı olması gerekir.
* Bir kimlik sağlayıcısı, kaydedilmesi gereken **Uniqueuserguid** gibi benzersiz bir Kullanıcı tanımlayıcısına sahiptir.
* Özel Kullanıcı yolculuğu, **Migrationstatus**gibi bir kullanıcının durumu için kalıcı hale getirilmesi gerekir.

Azure AD B2C her kullanıcı hesabında depolanan özniteliklerin kümesini genişletir. Ayrıca, [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)kullanarak bu öznitelikleri okuyabilir ve yazabilirsiniz.

Uzantı özellikleri, dizindeki Kullanıcı nesnelerinin şemasını genişletir. Terimler *uzantı özelliği*, *özel öznitelik*ve *özel talep* , bu makalenin bağlamıyla aynı şeyi ifade eder. Ad, uygulama, nesne veya ilke gibi içeriğe göre değişir.

Uzantı özellikleri, bir kullanıcı için veri içerse de yalnızca bir uygulama nesnesine kaydedilebilir. Özelliği uygulamaya eklenir. Uygulama nesnesinin, bir uzantı özelliğini kaydetmek için yazma erişimi olmalıdır. Tüm türler ve tüm uygulamalar genelindeki yüz uzatma özellikleri tek bir nesneye yazılabilir. Uzantı özellikleri hedef dizin türüne eklenir ve Azure AD B2C Dizin kiracısında hemen erişilebilir hale gelir.
Uygulama silinirse, tüm kullanıcılar için içerdikleri tüm verilerle birlikte bu uzantı özellikleri de kaldırılır. Bir uzantı özelliği uygulama tarafından silinirse, hedef dizin nesnelerinde kaldırılır ve değerler silinir.

Uzantı özellikleri yalnızca Kiracıdaki kayıtlı bir uygulama bağlamında bulunur. Uygulamanın nesne KIMLIĞI, kendisini kullanan **teknisyen profiline** eklenmelidir.

>[!NOTE]
>Azure AD B2C dizin genellikle adlı `b2c-extensions-app`bir Web uygulaması içerir. Bu uygulama öncelikle Azure portal aracılığıyla oluşturulan özel talepler için B2C yerleşik ilkeleri tarafından kullanılır. Bu uygulamayı kullanarak yalnızca gelişmiş kullanıcıların B2C özel ilkelerine yönelik uzantıları kaydetmesini öneririz.
Yönergeler, bu makaledeki **sonraki adımlar** bölümüne dahildir.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Uzantı özelliklerini depolamak için yeni bir uygulama oluşturma

1. Bir gözatma oturumu açın ve [Azure Portal](https://portal.azure.com)gidin. Yapılandırmak istediğiniz B2C dizininin yönetici kimlik bilgileriyle oturum açın.
2. Sol gezinti menüsünde **Azure Active Directory** ' yi seçin. **Daha fazla hizmet**seçerek bunu bulmanız gerekebilir.
3. **Uygulama kayıtları**'nı seçin. **Yeni uygulama kaydı**’nı seçin.
4. Aşağıdaki girişleri sağlayın:
    * Web uygulaması için bir ad: **WebApp-Graphapı-DirectoryExtensions**.
    * Uygulama türü: **Web uygulaması/API**.
    * Oturum açma URL 'SI: **https://{tenantName}. onmicrosoft. com/webapp-Graphapı-DirectoryExtensions**.
5. **Oluştur**’u seçin.
6. Yeni oluşturulan Web uygulamasını seçin.
7. **Ayarlar** > **gerekli izinler**' i seçin.
8. API **Windows Azure Active Directory**seçin.
9. Uygulama Izinlerinde onay işareti girin: **Dizin verilerini okuyun ve yazın**. Daha sonra **Kaydet**’e tıklayın.
10. **Izin ver** ' i seçin ve **Evet**' i onaylayın.
11. Aşağıdaki tanımlayıcıları panonuza kopyalayın ve kaydedin:
    * **Uygulama kimliği**. Örnek: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Nesne kimliği**. Örnek: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>**Applicationobjectıd** eklemek için özel ilkenizi değiştirme

Azure Active Directory B2C içindeki [adımları izlediyseniz: Özel ilkeleri](active-directory-b2c-get-started-custom.md)kullanmaya başlama, **TrustFrameworkBase. xml**, **TrustFrameworkExtensions. xml**, **signuporsign. xml**, **profileedit. xml** [](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) **adlı örnek dosyaları indirmiş ve değiştirmiş olursunuz. PasswordReset. xml**. Bu adımda, bu dosyalarda daha fazla değişiklik yaparsınız.

* **TrustFrameworkBase. xml** dosyasını açın ve aşağıdaki örnekte gösterildiği `Metadata` gibi bölümü ekleyin. Değer için önceden kaydettiğiniz `ApplicationObjectId` nesne kimliğini ve `ClientId` değer için kaydettiğiniz uygulama kimliğini ekleyin:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> **Teknisyen** , yeni oluşturulan uzantı özelliğine ilk kez yazıyorsa, bir kerelik hata yaşayabilirsiniz. Uzantı özelliği ilk kez kullanıldığında oluşturulur.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Kullanıcı yolculuğunda yeni uzantı özelliğini veya özel özniteliği kullanın

1. **Profileedit. xml** dosyasını açın.
2. Özel bir talep `loyaltyId`ekleyin. Özel talebi `<RelyingParty>` öğesine ekleyerek, uygulamanın belirtecine dahil edilir.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. **TrustFrameworkExtensions. xml** dosyasını açın ve öğesini ve`<ClaimsSchema>` `BuildingBlocks` alt öğelerini öğesine ekleyin:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Aynı `ClaimType` tanımı **TrustFrameworkBase. xml**' ye ekleyin. Hem temel hem de uzantı dosyalarında bir `ClaimType` tanım eklemek gerekli değildir. Bununla birlikte, sonraki adımlar temel dosyadaki `extension_loyaltyId` teknisyen ' e ait **profilleri** ' ne ekler. Bu nedenle, ilke doğrulayıcısı temel dosyayı olmadan karşıya yüklemeyi reddeder. **TrustFrameworkBase. xml** dosyasında **profileedit** adlı Kullanıcı yolculuğunun yürütülmesini izlemek faydalı olabilir. Düzenleyicinizde aynı ada sahip Kullanıcı yolculuğu için arama yapın. Düzenleme adımının 5 **' ın TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate '** i çağırdığından emin olmak. Flow hakkında bilgi edinmek için bu **teknisyen** ' i arayın ve inceleyin.

5. **TrustFrameworkBase. xml** dosyasını açın ve **teknisyen**' `loyaltyId` de bir giriş ve çıkış talebi olarak ekleyin:

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. **TrustFrameworkBase. xml** dosyasında, `loyaltyId` **teknisyen-userwriteprofileusingobjectıd**' ye talebi ekleyin. Bu ek olarak, dizindeki geçerli kullanıcı için uzantı özelliğindeki talebin değeri devam ettirir:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. **TrustFrameworkBase. xml** dosyasında, bir Kullanıcı her oturum `loyaltyId` açtığında uzantı özniteliğinin değerini okumak için, **teknisyen-userreadusingobjectıd** talebini ekleyin. Şimdiye kadar, **teknisyen** yalnızca yerel hesapların akışında değiştirilmiştir. Yeni özniteliği sosyal veya Federasyon hesabının akışında istiyorsanız, farklı bir **teknisyen** kümesinin değiştirilmesi gerekir. **Sonraki adımlar** bölümüne bakın.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Özel ilkeyi test etme

1. Azure AD B2C dikey penceresini açın ve **kimlik deneyimi çerçevesi** > **özel ilkeleri**' ne gidin.
1. Karşıya yüklediğiniz özel ilkeyi seçin. **Şimdi Çalıştır**' ı seçin.
1. Bir e-posta adresi kullanarak kaydolun.

Uygulamanıza geri gönderilen KIMLIK belirteci, yeni uzantı özelliğini **extension_loyaltyId**önünde özel bir talep olarak içerir. Aşağıdaki örneğe bakın:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Sonraki adımlar

1. Aşağıdaki **teknisyen profilini**değiştirerek sosyal hesaplarda oturum açmak için akışlara yeni talebi ekleyin. Sosyal ve Federasyon hesapları, oturum açmak için bu iki **teknisyen** kullanır. Kullanıcı nesnesinin Bulucu olarak **Alternativesecurityıd** kullanarak Kullanıcı verilerini yazar ve okur.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Yerleşik ve özel ilkeler arasında aynı uzantı özniteliklerini kullanın. Portal deneyimi aracılığıyla uzantı veya özel öznitelikler eklediğinizde, bu öznitelikler her B2C kiracısında bulunan **B2C-Extensions-App** kullanılarak kaydedilir. Özel ilkenizde uzantı özniteliklerini kullanmak için aşağıdaki adımları uygulayın:

   a. Portal.azure.com ' deki B2C kiracınızda **Azure Active Directory** gidin ve **uygulama kayıtları**' i seçin.
   b. **B2C-Extensions-uygulamanızı** bulun ve seçin.
   c. **Temel**bileşenler altında **uygulama KIMLIĞINI** ve **nesne kimliğini**girin.
   d. Bunları **AAD-Common** teknisyen \ profil meta verilerinize dahil edin:

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Portal deneyimiyle tutarlı kalın. Özel ilkeleriniz içinde kullanmadan önce Portal Kullanıcı arabirimini kullanarak bu öznitelikleri oluşturun. Portalda bir öznitelik **ActivationStatus** oluşturduğunuzda, buna aşağıdaki şekilde başvurmanız gerekir:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Başvuru

Uzantı özellikleri hakkında daha fazla bilgi için [Dizin şeması uzantıları | makalesine bakın. Graph API kavramlar](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * Bir **teknisyen** , bir uç noktanın adını, meta verilerini ve protokolünü tanımlayan bir öğe türü veya işlevdir. **Teknisyen** , kimlik deneyimi çerçevesinin gerçekleştirdiği taleplerin değişimini ayrıntıdan yaşayın. Bu işlev bir düzenleme adımında ya da başka bir **teknisyen**tarafından çağrıldığında, **ınputclaim** ve **outputclaim** , çağıran tarafından parametre olarak sağlanır.
> * Graph API uzantı öznitelikleri, kuralı `extension_ApplicationObjectID_attributename`kullanılarak adlandırılır.
> * Özel ilkeler, **extension_attributename**olarak uzantı özniteliklerine başvurur. Bu başvuru, XML 'deki **Applicationobjectıd** değerini atlar.
