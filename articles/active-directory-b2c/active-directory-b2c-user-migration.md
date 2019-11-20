---
title: Azure Active Directory B2C 'de Kullanıcı geçişi yaklaşımları
description: Azure AD Graph API ve isteğe bağlı olarak Azure AD B2C özel ilkeleri kullanarak Kullanıcı geçişi üzerinde hem çekirdek hem de gelişmiş kavramları açıklar.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c8e4027bd8892ff3bf5c598573b7736aea42953f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602570"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Kullanıcı geçişi

Kimlik sağlayıcınızı Azure Active Directory B2C (Azure AD B2C) olarak geçirdiğinizde, Kullanıcı hesaplarını da geçirmeniz gerekebilir. Bu makalede, mevcut kullanıcı hesaplarının herhangi bir kimlik sağlayıcısından Azure AD B2C ' ye nasıl geçirileceği açıklanır. Makalenin öngörücü olmaması gerekmez, ancak bunun yerine birkaç senaryo açıklanmaktadır. Geliştirici her yaklaşımın uygunluğunu sağlamaktan sorumludur.

## <a name="user-migration-flows"></a>Kullanıcı geçiş akışları

Azure AD B2C, [Azure AD Graph API][B2C-GraphQuickStart]aracılığıyla kullanıcıları geçirebilirsiniz. Kullanıcı geçiş işlemi iki akışa denk gelir:

- **Geçiş öncesi**: Bu akış, bir kullanıcının kimlik bilgilerine (Kullanıcı adı ve parola) açık erişime sahip olduğunuzda veya kimlik bilgileri şifrelendiğinde, ancak şifrelerini çözebilmeniz durumunda geçerlidir. Geçiş öncesi işlem, eski kimlik sağlayıcısından kullanıcıları okumayı ve Azure AD B2C dizininde yeni hesaplar oluşturmayı içerir.

- **Geçiş öncesi ve parola sıfırlama**: Bu akış, bir kullanıcının parolası erişilebilir olmadığında geçerlidir. Örneğin:
  - Parola, karma biçiminde depolanır.
  - Parola, erişemiyoruz bir kimlik sağlayıcısında depolanır. Eski kimlik sağlayıcınız bir Web hizmetini çağırarak kullanıcı kimlik bilgilerini doğrular.

Her iki akışda, önce geçiş öncesi işlemi çalıştırın, eski kimlik sağlayıcınızdan kullanıcıları okuyun ve Azure AD B2C dizininde yeni hesaplar oluşturun. Parolanız yoksa, rastgele oluşturulan bir parolayı kullanarak hesabı oluşturursunuz. Ardından kullanıcıdan parolayı değiştirmesini ister veya Kullanıcı ilk kez oturum açtığında Azure AD B2C kullanıcıyı sıfırlamayı ister.

## <a name="password-policy"></a>Parola ilkesi

Azure AD B2C parola ilkesi (yerel hesaplar için), Azure AD ilkesini temel alır. Azure AD B2C kaydolma veya oturum açma ve parola sıfırlama ilkeleri "güçlü" parola gücünü kullanır ve herhangi bir parolayı sona ermez. Daha fazla bilgi için bkz. [Azure AD parola ilkesi][AD-PasswordPolicies].

Geçirmek istediğiniz hesaplar [Azure AD B2C tarafından zorlanan güçlü parola kuvvetinden][AD-PasswordPolicies]daha zayıf bir parola gücü kullanıyorsa, güçlü parola gereksinimini devre dışı bırakabilirsiniz. Varsayılan parola ilkesini değiştirmek için `passwordPolicies` özelliğini `DisableStrongPassword`olarak ayarlayın. Örneğin, kullanıcı oluştur isteğini aşağıdaki şekilde değiştirebilirsiniz:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>1\. Adım: kullanıcıları geçirmek için Azure AD Graph API kullanma

Azure AD B2C Kullanıcı hesabını Graph API (parola ile veya rastgele bir parola ile) ile oluşturursunuz. Bu bölümde Graph API kullanarak Azure AD B2C dizininde kullanıcı hesapları oluşturma işlemi açıklanmaktadır.

### <a name="step-11-register-your-application-in-your-tenant"></a>Adım 1,1: uygulamanızı kiracınıza kaydetme

Graph API ile iletişim kurmak için, önce yönetici ayrıcalıklarına sahip bir hizmet hesabına sahip olmanız gerekir. Azure AD 'de, bir uygulamayı kaydeder ve dizine yazma erişimini etkinleştirirsiniz. Uygulama kimlik bilgileri uygulama **kimliği** ve **uygulama gizli**bilgileridir. Uygulama, Graph API çağırmak için Kullanıcı olarak değil, kendisi gibi davranır.

İlk olarak, Kullanıcı Geçişi gibi yönetim görevleri için kullanabileceğiniz bir uygulamayı kaydedin.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Adım 1,2: uygulamanıza yönetici izni verme

Ardından, uygulamaya, dizine yazmak için gereken Azure AD Graph API izinlerini verin.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Adım 1,3: uygulama gizli anahtarını oluşturma

Daha sonraki bir adımda yapılandırdığınız Kullanıcı geçiş uygulaması tarafından kullanılmak üzere bir istemci gizli anahtarı (anahtar) oluşturun.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Artık Azure AD B2C kiracınızda Kullanıcı oluşturma, okuma ve güncelleştirme izinlerine sahip bir uygulamanız var.

### <a name="step-14-optional-environment-cleanup"></a>Adım 1,4: (Isteğe bağlı) ortam temizleme

*Dizin verilerini okuma ve yazma* *izni, kullanıcıları silme hakkını içermez.* Uygulamanıza kullanıcıları silme yeteneği vermek için (ortamınızı temizlemek için), PowerShell çalıştırmayı ve Kullanıcı hesabı yönetici izinlerini ayarlamayı içeren ek bir adım gerçekleştirmeniz gerekir. Aksi halde, bir sonraki bölüme atlayabilirsiniz.

> [!IMPORTANT]
> B2C kiracısında *Yerel* olan bir B2C kiracı yönetici hesabı kullanmanız gerekir. Hesap adı sözdizimi *admin\@contosob2c.onmicrosoft.com*.

[Azure AD PowerShell V2 modülünü][AD-Powershell]gerektiren bu PowerShell betiğinden şunları yapın:

1. Çevrimiçi hizmetinize bağlanın. Bunu yapmak için, Windows PowerShell komut isteminde `Connect-AzureAD` cmdlet 'ini çalıştırın ve kimlik bilgilerinizi sağlayın.

1. Uygulamanın kullanıcı hesabı yöneticisi rolünü atamak için **uygulama kimliği** ' ni kullanın. Bu roller iyi bilinen tanımlayıcılara sahiptir, bu nedenle tüm yapmanız gereken **uygulama kimliğinizi** betiğe girer.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

`$AppId` değerini Azure AD **Uygulama Kimliğiniz**ile değiştirin.

## <a name="step-2-pre-migration-application-sample"></a>2\. Adım: geçiş öncesi uygulama örneği

Geçiş öncesi kod örneğini, topluluk tarafından tutulan `azure-ad-b2c/user-migration` GitHub deposunda bulabilirsiniz:

[Azure-AD-B2C/Kullanıcı geçişi/geçiş öncesi][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Adım 2,1: geçiş veri dosyasını düzenleme

Örnek uygulama, kukla Kullanıcı verisi içeren bir JSON dosyası kullanır. Örneği başarıyla çalıştırdıktan sonra, kodu kendi veritabanınızdaki verileri kullanmak üzere değiştirebilirsiniz. Ya da kullanıcı profilini bir JSON dosyasına aktarabilir ve ardından uygulamayı bu dosyayı kullanacak şekilde ayarlayabilirsiniz.

JSON dosyasını düzenlemek için `AADB2C.UserMigration.sln` Visual Studio çözümünü açın. `AADB2C.UserMigration` projesinde, `UsersData.json` dosyasını açın.

![UsersData. json dosyasının iki kullanıcının JSON bloklarını gösteren bölümü](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Gördüğünüz gibi, dosya Kullanıcı varlıklarının bir listesini içerir. Her kullanıcı varlığı aşağıdaki özelliklere sahiptir:

- e-posta
- displayName
- firstName
- Soyadı
- parola (boş olabilir)

> [!NOTE]
> Derleme zamanında, Visual Studio dosyayı `bin` dizinine kopyalar.

### <a name="step-22-configure-the-application-settings"></a>Adım 2,2: uygulama ayarlarını yapılandırma

`AADB2C.UserMigration` projesi altında *app. config* dosyasını açın. Aşağıdaki uygulama ayarlarını kendi değerlerinizle değiştirin:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Bir Azure Tablo bağlantı dizesinin kullanımı, sonraki bölümlerde açıklanmıştır.
> - B2C kiracı adınız, kiracı oluşturma sırasında girdiğiniz etki alanıdır ve Azure portal görüntülenir. Kiracı adı genellikle suffix *. onmicrosoft.com* ile biter (örneğin, *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Adım 2,3: geçiş öncesi işlemi çalıştırma

`AADB2C.UserMigration` çözümüne sağ tıklayın ve ardından örneği yeniden derleyin. Başarılı olursanız, artık `AADB2C.UserMigration\bin\Debug\net461`bulunan `UserMigration.exe` çalıştırılabilir bir dosyanız olmalıdır. Geçiş işlemini çalıştırmak için aşağıdaki komut satırı parametrelerinden birini kullanın:

- **Kullanıcıları parolayla geçirmek**için `UserMigration.exe 1` komutunu kullanın.

- **Kullanıcıları rastgele parola ile geçirmek**için `UserMigration.exe 2` komutunu kullanın. Bu işlem bir Azure Tablo varlığı da oluşturur. Daha sonra, REST API hizmetini çağırmak için ilkeyi yapılandırırsınız. Hizmet, geçiş işlemini izlemek ve yönetmek için bir Azure tablosu kullanır.

![UserMigration. exe komutunun çıkışını gösteren komut Istemi penceresi](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Adım 2,4: geçiş öncesi işlemini denetleyin

Geçişi doğrulamak için aşağıdaki iki yöntemden birini kullanın:

- Bir kullanıcıyı görünen ada göre aramak için Azure portal kullanın:

   1. **Azure AD B2C**açın ve ardından **Kullanıcılar**' ı seçin.
   1. Arama kutusuna kullanıcının görünen adını yazın ve ardından kullanıcının profilini görüntüleyin.

- Bir kullanıcıyı oturum açma e-posta adresi ile almak için örnek uygulamayı kullanın:

   1. Şu komutu çalıştırın:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Ayrıca, aşağıdaki komutu kullanarak Kullanıcı görünen ada göre de alabilirsiniz: `UserMigration.exe 4 "<Display name>"`.

   1. Kullanıcının bilgilerini görmek için, bir JSON düzenleyicisinde USERPROFILE. json dosyasını açın.

      ![Visual Studio Code düzenleyicisinde açık olan UserProfile. JSON dosyası](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Adım 2,5: (Isteğe bağlı) ortam temizleme

Azure AD kiracınızı temizlemek ve Azure AD dizininden kullanıcıları kaldırmak istiyorsanız, `UserMigration.exe 5` komutunu çalıştırın.

> [!NOTE]
> * Kiracınızı temizlemek için, uygulamanız için Kullanıcı hesabı yönetici izinlerini yapılandırın.
> * Örnek geçiş uygulaması, JSON dosyasında listelenen tüm kullanıcıları temizler.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Adım 2,6: geçirilen kullanıcılarla oturum açın (parola ile)

Geçiş öncesi işlemi Kullanıcı parolalarıyla çalıştırdıktan sonra, hesaplar kullanıma hazırsa ve kullanıcılar Azure AD B2C kullanarak uygulamanızda oturum açabilirler. Kullanıcı parolalara erişiminiz yoksa, sonraki bölüme geçin.

## <a name="step-3-help-users-reset-their-password"></a>3\. Adım: kullanıcıların parolalarını sıfırlamasına yardımcı olun

Kullanıcıları rastgele bir parolayla geçirirseniz, parolalarını sıfırlamalıdır. Parolayı sıfırlamasına yardımcı olmak için, parolayı sıfırlamak üzere bağlantı içeren bir hoş geldiniz e-postası gönderin.

Parola sıfırlama ilkenizin bağlantısını almak için aşağıdaki adımları izleyin. Bu yordamda daha önce bir parola sıfırlama [özel ilkesi](active-directory-b2c-get-started-custom.md)oluşturmuş olduğunuz varsayılmaktadır.

1. [Azure Portal](https://portal.azure.com)sağ üst bölümündeki **Dizin + abonelik** filtresini kullanarak Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol taraftaki menüden (veya **tüm hizmetler**içinden) **Azure AD B2C** ' yi seçin.
1. **İlkeler**altında **kimlik deneyimi çerçevesi**' ni seçin.
1. Parola sıfırlama ilkenizi seçin. Örneğin, *B2C_1A_PasswordReset*.
1. **Uygulama Seç** açılır penceresinde uygulamanızı seçin.

    > [!NOTE]
    > **Şimdi Çalıştır** , kiracınızda en az bir uygulamanın kaydedilmesini gerektirir. Uygulamaları nasıl kaydedeceğinizi öğrenmek için bkz. [öğretici: uygulamayı Azure Active Directory B2C kaydetme][B2C-AppRegister].

1. **Şimdi Çalıştır uç noktası** metin kutusunda gösterilen URL 'yi kopyalayın ve sonra kullanıcılarınıza gönderin.

    ![Şimdi Çalıştır uç noktası vurgulanmış şekilde parola sıfırlama ilkesi sayfası](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>4\. Adım: (Isteğe bağlı) Kullanıcı geçiş durumunu denetlemek ve ayarlamak için ilkenizi değiştirin

> [!NOTE]
> Kullanıcı geçiş durumunu denetlemek ve değiştirmek için özel bir ilke kullanmanız gerekir. [Özel ilkelerle çalışmaya başlama][B2C-GetStartedCustom] bölümündeki kurulum yönergelerinin tamamlanması gerekir.

Kullanıcılar ilk olarak parolayı sıfırlamadan oturum açmaya çalıştığında, ilkenizin kolay bir hata iletisi döndürmelidir. Örneğin:

> *Parolanızın süresi doldu. Sıfırlamak için parola sıfırlama bağlantısını seçin.*

Bu isteğe bağlı adım, [özel Ilkelerle Başlarken][B2C-GetStartedCustom] makalesinde açıklandığı gibi Azure AD B2C özel ilkelerin kullanılmasını gerektirir.

Bu bölümde, oturum açma sırasında kullanıcı geçiş durumunu denetlemek için ilkeyi değiştirirsiniz. Kullanıcı parolayı değiştirmediyse, kullanıcıdan **parolanızı unuttum?** bağlantısını seçmesini ısteyen bir http 409 hata iletisi döndürür.

Parola değişikliğini izlemek için bir Azure tablosu kullanırsınız. `2`komut satırı parametresiyle geçiş öncesi işlemi çalıştırdığınızda, bir Azure tablosunda bir kullanıcı varlığı oluşturursunuz. Hizmetiniz şunları yapar:

- Oturum açma sırasında, Azure AD B2C ilkesi geçiş yeniden hizmeti 'ni çağırır ve giriş talebi olarak bir e-posta iletisi gönderir. Hizmet, Azure tablosundaki e-posta adresini arar. Adres varsa, hizmet bir hata iletisi atar: *parolayı değiştirmeniz gerekir*.

- Kullanıcı parolayı başarıyla değiştirdikten sonra, varlığı Azure tablosundan kaldırın.

> [!NOTE]
> Örneği basitleştirmek için bir Azure tablosu kullanıyoruz. Geçiş durumunu herhangi bir veritabanında veya Azure AD B2C hesapta özel bir özellik olarak saklayabilirsiniz.

### <a name="41-update-your-application-setting"></a>4,1: uygulama ayarınızı güncelleştirin

1. Yeniden takip eden API tanıtımına test etmek için `AADB2C.UserMigration.sln` Visual Studio 'da açın.
1. `AADB2C.UserMigration.API` projesinde *Web. config* dosyasını açın. Ayarı, [2,2. adımdaki](#step-22-configure-the-application-settings)bir yapılandırma ile değiştirin:

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Adım 4,2: Web uygulamanızı Azure App Service dağıtma

Çözüm Gezgini ' de, `AADB2C.UserMigration.API`sağ tıklayın, "Yayımla..." öğesini seçin. Azure App Service yayımlamak için yönergeleri izleyin. Daha fazla bilgi için bkz. [Azure App Service uygulamanızı dağıtma][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Adım 4,3: ilkenize teknik profil ve teknik profil doğrulama ekleme

1. Çözüm Gezgini ' de, "çözüm öğeleri" ni genişletin ve *TrustFrameworkExtensions. xml* ilke dosyasını açın.
1. `yourtenant.onmicrosoft.com` `TenantId`, `PublicPolicyUri` ve `<TenantId>` alanları kiracınızın adına değiştirin.
1. `<TechnicalProfile Id="login-NonInteractive">` öğesi altında, tüm `ProxyIdentityExperienceFrameworkAppId` ve `IdentityExperienceFrameworkAppId` örneklerini [özel Ilkelerle Başlarken][B2C-GetStartedCustom]bölümünde yapılandırılan uygulama kimlikleriyle değiştirin.
1. `<ClaimsProviders>` düğümü altında aşağıdaki XML kod parçacığını bulun. `ServiceUrl` değerini Azure App Service URL 'nize işaret etmek üzere değiştirin.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Önceki teknik profil bir giriş talebini tanımlar: `signInName` (e-posta olarak gönder). Oturum açma sırasında talep, yeniden deneme uç noktanıza gönderilir.

Yeniden deneme API 'niz için teknik profili tanımladıktan sonra, Azure AD B2C ilkenize teknik profili çağıracağınızı söyleyin. XML kod parçacığı, temel ilkede tanımlanan `SelfAsserted-LocalAccountSignin-Email`geçersiz kılar. XML kod parçacığı Ayrıca teknik profil `LocalAccountUserMigration`işaret eden Referenceıd ile `ValidationTechnicalProfile`de ekler.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Adım 4,4: ilkeyi kiracınıza yükleme

1. [Azure Portal][Portal], [Azure AD B2C kiracınızın bağlamına][B2C-NavContext]geçin ve ardından **Azure AD B2C**' yı seçin.
1. **Kimlik deneyimi çerçevesini**seçin.
1. **Tüm ilkeler**' i seçin.
1. **Ilkeyi karşıya yükle**' yi seçin.
1. Varsa **Ilkenin üzerine yaz** onay kutusunu seçin.
1. *TrustFrameworkExtensions. xml* dosyasını karşıya yükleyin ve doğrulamayı geçirdiğinizden emin olun.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Adım 4,5: Şimdi Çalıştır 'ı kullanarak özel ilkeyi test etme

1. **Azure AD B2C**' yi ve ardından **kimlik deneyimi çerçevesi**' ni seçin.
1. Karşıya yüklediğiniz *B2C_1A_signup_signin*, bağlı olan taraf (RP) özel ilkesini açın ve **Şimdi Çalıştır**' ı seçin.
1. Geçirilmiş kullanıcılardan birinin kimlik bilgilerini girin ve **oturum aç**' ı seçin. REST API aşağıdaki hata iletisini oluşturması gerekir:

    ![Parolayı değiştirme hata iletisini gösteren oturum açma sayfası](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Adım 4,6: (Isteğe bağlı) REST API sorunlarını giderme

Günlük bilgilerini neredeyse gerçek zamanlı olarak görüntüleyebilir ve izleyebilirsiniz.

1. Yeniden Izlenen uygulamanızın ayarlar menüsünde, **izleme**altında **tanılama günlükleri**' ni seçin.
1. **Uygulama günlüğünü (FileSystem)** **Açık**olarak ayarlayın.
1. **Düzeyi** **verbose**olarak ayarlayın.
1. **Kaydet**’i seçin

    ![Azure portal 'de tanılama günlükleri yapılandırma sayfası](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. **Ayarlar** menüsünde **günlük akışı**' nı seçin.
1. Yeniden takip eden API 'nin çıkışını kontrol edin.

> [!IMPORTANT]
> Tanılama günlüklerini yalnızca geliştirme ve test sırasında kullanın. Yeniden sunulan API çıktısı, üretimde gösterilmemesi gereken gizli bilgiler içerebilir.

## <a name="optional-download-the-complete-policy-files"></a>Seçim Tüm ilke dosyalarını indirin

[Özel ilkeleri kullanmaya başlama][B2C-GetStartedCustom] Kılavuzu ' nu tamamladıktan sonra, kendi özel ilke dosyalarınızı kullanarak senaryonuzu oluşturmanızı öneririz. Başvurunuz için [örnek ilke dosyaları][UserMigrationSample-policy]sağladık.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
