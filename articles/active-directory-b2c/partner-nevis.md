---
title: Nevis Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Parolasız kimlik doğrulaması için Azure AD B2C kimlik doğrulamasını Nevis ile tümleştirmeyi öğrenin
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 42e244249ecb0539637918ae2439bdb4f5da4b38
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588488"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Passwordless kimlik doğrulaması için Azure Active Directory B2C ile Nevis yapılandırma öğreticisi

Bu örnek öğreticide, passwordless kimlik doğrulamasını etkinleştirmek için Azure AD B2C  [Nevis](https://www.nevis.net/en/solution/authentication-cloud) ile genişletmeyi öğrenin. Nevis, daha güçlü müşteri kimlik doğrulaması sağlamak ve ödeme hizmetleri yönerge 2 (PSD2) işlem gereksinimleriyle uyumlu olmak için Nevis Access uygulamasıyla mobil ve tamamen markalı Son Kullanıcı deneyimi sağlar.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Nevis [deneme hesabı](https://www.nevis-security.com/aadb2c/)

- Bir Azure AD aboneliği. Bir [hesabınız yoksa ücretsiz bir hesap](https://azure.microsoft.com/free/)alın.

- Azure aboneliğinize bağlı bir [Azure AD B2C kiracısı](./tutorial-create-tenant.md) .

- Kayıt ilke akışınızla Nevis 'i birleştirmek istiyorsanız, [özel ilkeleri](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)kullanmak için Azure AD B2C ortamı yapılandırıldı.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu senaryoda, passwordless kimlik doğrulaması için arka uç uygulamanıza tam markalı erişim uygulaması ekleyin. Aşağıdaki bileşenler çözümü yapar:

- Arka uca Birleşik oturum açma ve kaydolma ilkesiyle birlikte Azure AD B2C kiracı
- Azure AD B2C iyileştirmek için Nevis örneği ve REST API
- Kendi markalı erişim uygulamanız

Diyagramda uygulama gösterilmektedir.

![Azure AD B2C ve Nevis ile üst düzey parola oturum açma akışı](./media/partner-nevis/nevis-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı oturum açmaya veya Azure AD B2C oturum açma ve kaydolma ilkesi aracılığıyla bir uygulamaya kaydolma girişiminde bulunur.
| 2. | Kaydolma sırasında, Nevis Access uygulaması kullanıcı cihazına bir QR kodu kullanılarak kaydedilir. Kullanıcı cihazında özel bir anahtar oluşturulur ve Kullanıcı isteklerini imzalamak için kullanılır.
| 3. |  Azure AD B2C, açılan bir teknik profili kullanarak, oturum açma bilgilerini Nevis çözümü ile başlatır.
| 4. | Oturum açma isteği, bir anında iletme iletisi, QR kodu ya da derin bağlantı olarak erişim uygulamasına gönderilir.
| 5. | Kullanıcı, biyometri ile oturum açma girişimini onaylar. Daha sonra bir ileti, saklanan ortak anahtarla oturum açmayı doğrulayan Nevis 'e döndürülür.
| 6. | Azure AD B2C, oturum açmanın başarıyla tamamlandığını onaylamak için Nevis 'e son bir istek gönderir.
| 7. |Azure AD B2C kullanıcının başarı/başarısızlık iletisine bağlı olarak uygulamaya erişim izni verilir/reddedilir.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Azure AD B2C kiracınızı tümleştirin

### <a name="onboard-to-nevis"></a>Nevis 'e ekleme 

[Bir Nevis hesabı Için kaydolun](https://www.nevis-security.com/aadb2c/).
İki e-posta alacaksınız:

1. Bir yönetim hesabı bildirimi

2. Bir mobil uygulama davetiyesi.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Azure AD B2C kiracınızı Nevis hesabınıza ekleyin

1. Nevis yönetim hesabı denemesi e-postalarından, yönetim anahtarınızı panonuza kopyalayın.

2. https://console.nevis.cloud/Tarayıcıda açın.

3. Anahtarınızla yönetim konsolunda oturum açın.

4. **Örnek Ekle** ' yi seçin

5. Yeni oluşturulan örneği açmak için seçin.

6. Yan gezinti çubuğunda **özel tümleştirmeler** ' i seçin.

7. **Özel tümleştirme Ekle**' yi seçin.

8. Tümleştirme adı için Azure AD B2C kiracı adınızı girin.

9. URL/etki alanı için şunu girin `https://yourtenant.onmicrosoft.com`

10. **İleri**’yi seçin

>[!NOTE]
>Daha sonra Nevis erişim belirtecine ihtiyacınız olacak.

11. **Bitti** seçeneğini belirleyin.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Telefonunuza Nevis Access uygulamasını yükler

1. Nevis mobil uygulama denemesi e-postasında, **Test Uçuş uygulaması** davetini açın.

2. Uygulamayı yükler.

3. Nevis Access uygulamasını yüklemek için verilen yönergeleri izleyin.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Azure AD B2C Nevis ile tümleştirin

1. [Azure portalını](https://portal.azure.com/) açın.

2. Azure AD B2C kiracınıza geçiş yapın. Azure AD B2C kiracısı genellikle ayrı bir kiracıda olduğundan, doğru kiracıyı seçtiğinizden emin olun.

3. Menüsünde **kimlik deneyimi çerçevesi (ıEF)** öğesini seçin.

4. **Ilke anahtarlarını** seçin

5. **Ekle** ' yi seçin ve aşağıdaki ayarlarla yeni bir anahtar oluşturun:

      a. Seçeneklerde **el ile** Seç

      b. Adı **Authcloudaccesstoken** olarak ayarla

      c. Daha önce depolanan **Nevis erişim belirtecini** gizli alana yapıştırın

      d. Anahtar kullanımı için **şifreleme** seçin

      e. **Oluştur**’u seçin

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>nevis.html 'yi Azure Blob depolama 'ya yapılandırma ve yükleme

1. Kimlik ortamınızda (IDE), [**ilke**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) klasörüne gidin.

2. [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) dosyasını açın.

3. **Authentication_cloud_url** , Nevis yönetici KONSOLUNUZUN URL 'siyle değiştirin `https://<instance_id>.mauth.nevis.cloud` .

4. Değişiklikleri dosyaya **kaydedin** .

5. [Yönergeleri](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) izleyin ve **nevis.html** dosyasını Azure Blob depolama alanına yükleyin.

6. Bu dosya için [yönergeleri](./customize-ui-with-html.md#3-configure-cors) Izleyin ve çıkış noktaları arası kaynak PAYLAŞıMıNı (CORS) etkinleştirin.

7. Karşıya yükleme tamamlandıktan ve CORS etkinleştirildikten sonra, listeden **nevis.html** dosyasını seçin.

8. **Genel bakış** sekmesinde **URL**'nin yanındaki **Bağlantıyı Kopyala** simgesini seçin.

9. Bağlantıyı gri bir kutu görüntülediğinden emin olmak için yeni bir tarayıcı sekmesinde açın.

>[!NOTE]
>Blob bağlantısına daha sonra ihtiyacınız olacak.

### <a name="customize-your-trustframeworkbasexml"></a>TrustFrameworkBase.xml özelleştirin

1. IDE 'nize [**ilke**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) klasörüne gidin.

2. [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) dosyasını açın.

3. Kiracınızdaki Azure kiracı Hesabı adınızla **kiracınızı** **değiştirin.**

4. **Etkikiracınızı** **publicpolicyuri** içindeki Azure kiracı hesabınızın adıyla değiştirin.

5. Tüm **authentication_cloud_url** örnekleri, Nevis yönetici KONSOLUNUZUN URL 'siyle değiştirin

6. Dosyadaki değişiklikleri **kaydedin** .

### <a name="customize-your-trustframeworkextensionsxml"></a>TrustFrameworkExtensions.xml özelleştirin

1. IDE 'nize [**ilke**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) klasörüne gidin.

2. [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) dosyasını açın.

3. Kiracınızdaki Azure kiracı Hesabı adınızla **kiracınızı** **değiştirin.**

4. **Etkikiracınızı** **publicpolicyuri** içindeki Azure kiracı hesabınızın adıyla değiştirin.

5. **Basepolicy** altında, **Tenantıd**'de Azure kiracı Hesabı adınızla de kendi _kiracınızı_ değiştirin.

6. **Buildingblocks** altında, **loaduri** 'yi BLOB depolama hesabınızda _nevis.html_ 'nizin blob bağlantı URL 'siyle değiştirin.

7. Dosyayı **kaydedin** .

### <a name="customize-your-signuporsigninxml"></a>SignUpOrSignin.xml özelleştirin

1. IDE 'nize [**ilke**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) klasörüne gidin.

2. [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) dosyasını açın.

3. Kiracınızdaki Azure kiracı Hesabı adınızla **kiracınızı** **değiştirin.**

4. **Etkikiracınızı** **publicpolicyuri** içindeki Azure kiracı hesabınızın adıyla değiştirin.

5. **Basepolicy** altında, **Tenantıd**'de Azure kiracı Hesabı adınızla de kendi **kiracınızı** değiştirin.

6. Dosyayı **kaydedin** .

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Azure AD B2C için özel ilkelerinizi karşıya yükleyin

1. [Azure AD B2C kiracı](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) evinizi açın.

2. **Kimlik deneyimi çerçevesini** seçin.

3. **Özel Ilkeyi karşıya yükle**' yi seçin.

4. Değiştirdiğiniz **TrustFrameworkBase.xml** dosyasını seçin.

5. **Zaten varsa, özel Ilkenin üzerine yaz** onay kutusunu seçin.
6. **Karşıya Yükle**’yi seçin.

7. **TrustFrameworkExtensions.xml** için 5 ve 6. adımı yineleyin.

8. **SignUpOrSignin.xml** için 5 ve 6. adımı yineleyin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Hesap oluşturma ve Nevis Access uygulama kurulumunu test etme

1. [Azure AD B2C kiracı](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) evinizi açın.

2. **Kimlik deneyimi çerçevesini** seçin.

3. Özel ilkelere aşağı kaydırın ve **B2C_1A_signup_signin**' yi seçin.

4. **Şimdi Çalıştır**' ı seçin.

5. Açılan pencerede **Şimdi kaydolun**' ı seçin.

6. E-posta adresinizi ekleyin.

7. **Doğrulama kodu gönder**' i seçin.

8. E-postadaki doğrulama kodunu kopyalayın.

9. **Doğrula** seçeneğini belirleyin.

10. Formu yeni parolanızla ve görünen adınızla birlikte girin.

11. **Oluştur**’u seçin.

12. QR kod tarama sayfasına yönlendirilirsiniz.

13. Telefonunuzda, **Nevis Access uygulamasını** açın.

14. **Yüz kimliği** seçin.

15. Ekran **kimlik doğrulayıcı kaydı başarılı** olduğunda **devam**' ı seçin.

16. Telefonunuzda bir kez daha yeniden kimlik doğrulaması yapın.

17. Kodu çözülmüş belirteç ayrıntılarınızı görüntüleyen [JWT.MS](https://jwt.ms) giriş sayfasına yönlendirilirsiniz.

### <a name="test-the-pure-passwordless-sign-in"></a>Saf parolasız oturum açmayı test etme

1. **Kimlik deneyimi çerçevesi** altında **B2C_1A_signup_signin** seçin.

2. **Şimdi Çalıştır**' ı seçin.

3. Açılan pencerede, **passwordless kimlik doğrulaması**' nı seçin.

4. E-posta adresinizi girin.

5. **Devam**’ı seçin.

6. Telefonunuzda, bildirimler ' de, **Nevis Access uygulama bildirimi**' ni seçin.

7. Yüzünüzü kullanarak kimlik doğrulaması yapın.

8. Belirteçlerinizi görüntüleyen [JWT.MS](https://jwt.ms) giriş sayfasına otomatik olarak yönlendirilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
