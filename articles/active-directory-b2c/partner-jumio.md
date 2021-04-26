---
title: Jumio ile Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Bu öğreticide, istemci verilerini koruma, otomatik KIMLIK doğrulama için Jumio ile Azure Active Directory B2C yapılandırırsınız.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: e344c849a8e9021daea9caebacec3289b99d03e6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256676"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile Jumio yapılandırma öğreticisi

Bu örnek öğreticide, Azure Active Directory B2C (Azure AD B2C) [Jumio](https://www.jumio.com/)ile tümleştirme hakkında rehberlik sağlıyoruz. Jumio, müşteri verilerinin korunmasına yardımcı olmak için gerçek zamanlı otomatik KIMLIK doğrulamaya olanak sağlayan bir KIMLIK doğrulama hizmetidir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- Azure aboneliğinize bağlı bir [Azure AD B2C kiracısı](./tutorial-create-tenant.md) .

## <a name="scenario-description"></a>Senaryo açıklaması

Jumio tümleştirmesi aşağıdaki bileşenleri içerir:

- Azure AD B2C: kullanıcının kimlik bilgilerini doğrulamaktan sorumlu olan yetkilendirme sunucusu. Kimlik sağlayıcısı olarak da bilinir.

- Jumio: Kullanıcı tarafından sunulan KIMLIK ayrıntılarını alan ve bunları doğrulayan hizmet.

- Ara REST API: Azure AD B2C ile Jumio hizmeti arasındaki tümleştirmeyi uygulayan API.

- Azure Blob depolama: Azure AD B2C ilkelerine özel kullanıcı arabirimi dosyaları sağlayan hizmet.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![Jumio ile Azure AD B2C tümleştirme mimarisinin diyagramı.](./media/partner-jumio/jumio-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı oturum açmak veya hesap oluşturmak için kaydolmak üzere bir sayfaya ulaşır. Azure AD B2C Kullanıcı özniteliklerini toplar.
| 2. | Azure AD B2C, orta katman API 'sini çağırır ve Kullanıcı özniteliklerine geçirir.
| 3. | Orta katman API 'SI Kullanıcı özniteliklerini toplar ve bunları Jumio API 'sinin tüketebileceği bir biçime dönüştürür. Ardından, öznitelikleri Jumıo 'ya gönderir.
| 4. | Jumio, bilgileri kullandıktan ve işleyerek, sonucu orta katman API 'sine döndürür.
| 5. | Orta katman API 'SI bilgileri işler ve ilgili bilgileri Azure AD B2C geri gönderir.
| 6. | Azure AD B2C, bilgileri ortadaki katman API 'sinden geri alır. Hata yanıtı gösteriyorsa, kullanıcıya bir hata iletisi görüntülenir. Başarılı bir yanıt gösteriyorsa, kullanıcının kimliği doğrulanır ve dizine yazılır.

## <a name="sign-up-with-jumio"></a>Jumio ile kaydolun

Jumıo hesabı oluşturmak için [Jumıo](https://www.jumio.com/contact/)ile iletişime geçin.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Jumio ile Azure AD B2C yapılandırma

Bir Jumıo hesabı oluşturduktan sonra, Azure AD B2C yapılandırmak için hesabını kullanırsınız. Aşağıdaki bölümlerde sırasıyla işlem açıklanır.

### <a name="deploy-the-api"></a>API 'YI dağıtma

Belirtilen [API kodunu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) bir Azure hizmetine dağıtın. [Bu yönergeleri](/visualstudio/deployment/quickstart-deploy-to-azure)Izleyerek kodu Visual Studio 'dan yayımlayabilirsiniz.

>[!NOTE]
>Azure AD 'yi gerekli ayarlarla yapılandırmak için dağıtılan hizmetin URL 'sine ihtiyacınız vardır.

### <a name="deploy-the-client-certificate"></a>İstemci sertifikasını dağıtma

1. İstemci sertifikası Jumio API çağrısını korumanıza yardımcı olur. Aşağıdaki PowerShell örnek kodunu kullanarak kendinden imzalı bir sertifika oluşturun:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   Daha sonra sertifika için belirtilen konuma verilir ``{your-local-path}`` .

3. [Bu makaledeki](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)yönergeleri izleyerek Azure App Service sertifikayı içeri aktarın.

### <a name="create-a-signingencryption-key"></a>İmzalama/şifreleme anahtarı oluşturma

Yalnızca harf ve rakam içeren 64 karakterden büyük uzunlukta rastgele bir dize oluşturun.

Örnek: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Dizeyi oluşturmak için aşağıdaki PowerShell betiğini kullanın:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>API 'YI yapılandırma

[Azure App Service içinde uygulama ayarlarını yapılandırabilirsiniz](../app-service/configure-common.md#configure-app-settings). Bu yöntemle ayarları bir depoya iade etmeden güvenli bir şekilde yapılandırabilirsiniz. REST API için aşağıdaki ayarları sağlamanız gerekir:

| Uygulama ayarları | Kaynak | Notlar |
| :-------- | :------------| :-----------|
|JumioSettings: AuthUsername | Jumıo hesap yapılandırması |     |
|JumioSettings: AuthPassword | Jumıo hesap yapılandırması |     |
|AppSettings: Signingcertparmak Izi|Oluşturulan otomatik olarak imzalanan sertifikanın parmak izi|  |
|AppSettings: ıdtokensigningkey| İmzalama anahtarı PowerShell kullanılarak oluşturuldu | |
| AppSettings: ıdtokenencryptionkey |PowerShell kullanılarak oluşturulan şifreleme anahtarı
| AppSettings: ıdtokenıssuer | JWT belirteci için kullanılacak veren (bir GUID değeri tercih edilir) |
| AppSettings: ıdtokenaudience  | JWT belirteci için kullanılacak hedef kitle (bir GUID değeri tercih edilir) |
|AppSettings: BaseRedirectUrl | Azure AD B2C ilkesinin temel URL 'SI | https://{-kiracı-adı}. b2clogin. com/{sizin-uygulama kimliği}|
| WEBSITE_LOAD_CERTIFICATES| Oluşturulan otomatik olarak imzalanan sertifikanın parmak izi |

### <a name="deploy-the-ui"></a>Kullanıcı arabirimini dağıtma

1. [Depolama hesabınızda bir BLOB depolama kapsayıcısı](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)ayarlayın.

2. Kullanıcı arabirimi dosyalarını blob kapsayıcısındaki [Kullanıcı arabirimi klasöründen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) depolayın.

#### <a name="update-ui-files"></a>UI dosyalarını Güncelleştir

1. Kullanıcı arabirimi dosyalarında [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)klasöre gidin.

2. Her bir HTML dosyasını açın.

3. `{your-ui-blob-container-url}`BLOB KAPSAYıCıNıN URL 'siyle bulun ve değiştirin.

4. `{your-intermediate-api-url}`Ara API App Service 'ın URL 'siyle bulun ve değiştirin.

>[!NOTE]
> En iyi uygulama olarak, öznitelik koleksiyonu sayfasına onay bildirimi eklemenizi öneririz. Kimlik doğrulaması için bilgilerin üçüncü taraf hizmetlere gönderileceğini kullanıcıya bildirin.

### <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C ilkesini yapılandırma

1. Ilkeler klasöründeki [Azure AD B2C ilkesine](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) gidin.

2. [LocalAccounts başlangıç paketini](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)indirmek için [Bu makaleyi](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) izleyin.

3. Azure AD B2C kiracı için ilkeyi yapılandırın.

>[!NOTE]
>Belirli kiracınızla ilgili olarak belirtilen ilkeleri güncelleştirin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.

2. Daha önce oluşturduğunuz **Signupsignın**' ı seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ardından:

   a. **Uygulama** için kayıtlı uygulamayı (örnek JWT) seçin.

   b. **Yanıt URL 'si** için **yeniden yönlendirme URL**'sini seçin.

   c. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışı ' na gidip bir hesap oluşturun.

5. Kullanıcı özniteliği oluşturulduktan sonra, akış sırasında Jumıo hizmeti çağrılacaktır. Akış tamamlanmamışsa, kullanıcının dizine kaydedilip kaydedilmediğini kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
