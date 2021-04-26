---
title: Öğretici-Azure kiracınızı kullanarak doğrulanabilen kimlik bilgilerini verme ve doğrulama (Önizleme)
description: Doğrulanabilir kimlik bilgisi kod örneğini Azure kiracınızla çalışacak şekilde değiştirin
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 310c821bf102d267d0b5f77dbf206b896ab2f1c7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739233"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Öğretici-kiracınızı kullanarak doğrulanabilen kimlik bilgilerini verme ve doğrulama (Önizleme)

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure kiracınızı doğrulanabilir kimlik bilgileri hizmeti ile ayarlamış olduğunuza göre, Azure Active Directory (Azure AD), örnek uygulamayı kullanarak doğrulanabilir kimlik bilgileri vermesini sağlamak için gereken adımları adım adım inceleyeceğiz.

Bu makalede şunları yapabilirsiniz:

> [!div class="checklist"]
> * Örnek uygulamayı Azure AD 'nize kaydetme
> * Kural oluşturma ve dosya görüntüleme
> * Karşıya yükleme kuralları ve görüntü dosyaları
> * Doğrulanabilir kimlik bilgileri veren hizmetinizi Azure Key Vault kullanacak şekilde ayarlama
> * Örnek kodu kiracınızın bilgileriyle güncelleştirin.

Örnek kodumuz, kullanıcıların doğrulanmış kimlik bilgisi uzmanı VC 'si verilebilmesi için bir kimlik sağlayıcısı için, özellikle Azure AD B2C kimlik doğrulaması yapmasını gerektirir. Tüm doğrulanabilir kimlik bilgileri verenler kimlik bilgilerini vermeden önce kimlik doğrulaması gerektirmez.

Kimlik belirteçleri kimlik doğrulaması, kullanıcıların kimlik bilgilerini almadan önce nerede olduklarını kanıtlamasını sağlar. Kullanıcılar başarıyla oturum açıp, kimlik sağlayıcısı Kullanıcı hakkında talepler içeren bir güvenlik belirteci döndürür. Yayımlayan hizmeti daha sonra bu güvenlik belirteçlerini ve taleplerini doğrulanabilir bir kimlik bilgisine dönüştürür. Doğrulanabilir kimlik bilgisi veren tarafından imzalandı.

OpenID Connect protokolünü destekleyen herhangi bir kimlik sağlayıcısı desteklenir. Desteklenen kimlik sağlayıcılarının örnekleri [Azure Active Directory](../fundamentals/active-directory-whatis.md)ve [Azure AD B2C](../../active-directory-b2c/overview.md)içerir. Bu öğreticide AAD kullandık.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, [önceki öğreticideki](enable-your-tenant-verifiable-credentials.md) adımları zaten tamamlamış ve kullandığınız ortama erişim sahibi olduğunuz varsayılır.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Kullanıcının oturum açmasını sağlamak için bir uygulamayı kaydetme

Doğrulanabilir bir kimlik bilgisi vermek için, bir uygulamayı, kimlik doğrulayıcı ya da başka bir doğrulanabilir kimlik bilgisi Wallet 'ı, kullanıcıların oturum açmasına izin verilen şekilde kaydetmeniz gerekir.  

Azure AD 'de ' VC cüzdan uygulaması ' adlı bir uygulamayı kaydedin ve bir istemci KIMLIĞI alın.

1. Kayıt sırasında [Azure AD](../develop/quickstart-register-app.md) ile uygulama kaydetme yönergelerini izleyin, aşağıdaki değerleri kullanın.

   - Ad: "VC cüzdan uygulaması"
   - Desteklenen hesap türleri: yalnızca bu kuruluş dizinindeki hesaplar
   - Yeniden yönlendirme URI 'SI: vcclient://openid/

   ![bir uygulamayı kaydetme](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Uygulamayı kaydettikten sonra, uygulama (istemci) KIMLIĞINI yazın. Bu değer daha sonra gerekli olacaktır.

   ![uygulama istemci KIMLIĞI](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. **Uç noktalar** düğmesini seçin ve OpenID Connect meta VERI belgesi URI 'sini kopyalayın. Sonraki bölümde bu bilgilere ihtiyacınız vardır. 

   ![veren uç noktaları](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Azure Key Vault erişimli düğüm uygulamanızı ayarlama

Bir kullanıcının kimlik bilgisi verme isteğinin kimliğini doğrulamak için, veren Web sitesi şifreleme anahtarlarınızı Azure Key Vault kullanır. Azure Key Vault erişmek için, Web sitenizin Azure Key Vault kimlik doğrulamak üzere kullanılabilecek bir istemci KIMLIĞI ve istemci parolası olması gerekir.

1. VC cüzdan uygulamasına genel bakış sayfasını görüntülerken **sertifikalar & gizli** dizileri ' ni seçin.
    ![Sertifikalar ve gizlilikler](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. **İstemci gizli** dizileri bölümünde **yeni istemci parolası** ' nı seçin.
    1. "Node VC istemci sırrı" gibi bir açıklama ekleyin
    1. Süre sonu: bir yılda.
  ![Bir yıllık süre sonu ile uygulama gizli dizisi](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Gizli anahtarı kopyalayın. Örnek düğüm uygulamanızı güncelleştirmek için bu bilgilere ihtiyacınız vardır.

>[!WARNING]
> Gizli dizi kopyalamak için bir şansınız vardır. Gizli dizi bundan sonra karma bir yoldur. KIMLIĞI kopyalamayın. 

Azure AD 'de uygulamanızı ve istemci gizli anahtarını oluşturduktan sonra, Key Vault işlemleri gerçekleştirmek için uygulamaya gerekli izinleri vermeniz gerekir. Web sitesinin burada depolanan özel anahtarlara erişmesini ve bunları kullanmasını sağlamak için bu izin değişikliklerinin yapılması gerekir.

1. Key Vault gidin.
2. Bu öğreticiler için kullandığımız anahtar kasasını seçin.
3. Sol gezinti üzerinde **erişim ilkeleri** ' ni seçin
4. **+ Erişim Ilkesi Ekle**' yi seçin.
5. **Anahtar izinleri** bölümünde **Al** ve **imzala**' yı seçin.
6. **Sorumlu** ' yı seçin ve daha önce Kaydolduğumuz uygulamayı aramak IÇIN uygulama kimliği ' ni kullanın. Kalem simgesini seçin.
7. **Add (Ekle)** seçeneğini belirleyin.
8. **Kaydet**' i seçin.

Key Vault izinleri ve erişim denetimi hakkında daha fazla bilgi için, [anahtar KASASı RBAC kılavuzunu](../../key-vault/general/rbac-guide.md) okuyun

![Anahtar Kasası izinleri atama](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Ortamınızla eşleşecek değişiklikler yapın

Şimdiye kadar, örnek uygulamamız ile çalışıyoruz. Uygulama [Azure Active Directory B2C](../../active-directory-b2c/overview.md) kullanıyor ve artık Azure AD 'yi kullanmak için geçiş yapıyoruz. bu nedenle, yalnızca ortamınız için değil, ancak daha önce kullanılmayan ek talepleri desteklemek için bazı değişiklikler yapmanız gerekir.

1. Aşağıdaki kural dosyasını kopyalayın ve **modified-expertRules.jsüzerine** kaydedin. 

    > [!NOTE]
    > **"scope": "OpenID profile"** bu kurallar dosyasına dahildir ve örnek uygulamanın kurallar dosyasına dahil değildir. Sonraki bölümde Azure Active Directory kiracınızda isteğe bağlı taleplerin nasıl etkinleştirileceği açıklanmaktadır. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Dosyayı açın ve **client_id** ile **yapılandırma** değerlerini, önceki bölümde kopyaladığımız iki değerle değiştirin.

    ![Bu adımın bir parçası olarak değiştirilmesi gereken iki değeri vurgulama](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Değer **yapılandırması** , OpenID Connect meta veri belgesi URI 'sidir.

  Örnek kod Azure Active Directory B2C kullandığından ve Azure Active Directory kullandığımızda, bu taleplerin doğrulanabilir kimlik bilgisine yazılacak KIMLIK belirtecine dahil edilmesi için kapsamlar aracılığıyla isteğe bağlı talepler eklememiz gerekir. 

3. Azure portal geri dönüp Azure Active Directory açın.
4. **Uygulama kayıtları** seçin.
5. Daha önce oluşturduğumuz VC cüzdan uygulamasını açın.
6. **Belirteç yapılandırması**' nı seçin.
7. **+ İsteğe bağlı talep Ekle** seçeneğini belirleyin

     ![belirteç yapılandırması altında, isteğe bağlı bir talep ekleyin](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. **Belirteç türü** ' nden **kimlik** ' i seçin ve kullanılabilir talepler listesinden **given_name** seçin ve **family_name**

     ![isteğe bağlı talepler ekleme](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. **Ekle**’ye basın.
10. Aşağıda gösterildiği gibi bir izin uyarısı alırsanız, kutuyu işaretleyin ve **Ekle**' yi seçin.

     ![isteğe bağlı talepler için izin ekleme](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Artık, bir kullanıcıya "oturum aç" ile, doğrulanabilir kimlik bilgilerinizi verilmek için, VC cüzdan uygulaması belirli talepleri doğrulanabilir kimlik bilgisine yazılacak kapsam parametresi aracılığıyla dahil eder.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Bu kurallar dosyası ve eski görüntüleme dosyası ile yeni bir VC oluştur

1. Yeni kurallar dosyasını kapsayıcınıza yükleyin
1. Doğrulanabilir kimlik bilgileri sayfasında, eski görüntü dosyasını ve yeni kurallar dosyasını (**modified-credentialExpert.js**) kullanarak **modifiedCredentialExpert** adlı yeni bir kimlik bilgisi oluşturun.
1. **Genel bakış** sayfasından kimlik bilgileri oluşturma işlemi tamamlandıktan sonra, **sorun kimlik bilgileri URL 'sini** kopyalayın ve bir sonraki bölümde ihtiyaç duyduğumuz için kaydedin.

## <a name="before-we-continue"></a>Devam etmeden önce

Gerekli kod değişikliklerini yapabilmek için birkaç değeri bir araya koyuyoruz. Örnek kodun kasasında depolanan kendi anahtarlarınızı kullanmasını sağlamak için sonraki bölümde bu değerleri kullanıyoruz. Şimdiye kadar aşağıdaki değerler hazırlanmalıdır.

- Yeni oluşturduğumuz kimlik bilgisi ile **sözleşme URI** 'Si (sorun kimlik bilgileri URL 'si)
- **Uygulama ISTEMCI kimliği** Düğüm uygulamasını kaydettirdiğimiz zaman bunu aldık.
- **İstemci parolası** Uygulamanızı Anahtar Kasası 'na erişim izni verildiğinde daha önce oluşturduk.

Örnek uygulamamız üzerinde bir kez değişiklik yapabilmek için gereken birkaç farklı değer vardır. Şimdi şunları görelim!

### <a name="verifiable-credentials-settings"></a>Doğrulanabilir kimlik bilgileri ayarları

1. Doğrulanabilir kimlik bilgileri sayfasına gidin ve **Ayarlar**' ı seçin.  
1. Aşağıdaki değerleri kopyalayın:

    - Kiracı tanımlayıcısı 
    - Verenin tanımlayıcısı (DıD)
    - Anahtar Kasası (URI)

1. Imzalama anahtarı tanımlayıcısı altında bir URI vardır ancak yalnızca bir bölümü gerekir. Aşağıdaki görüntüde kırmızı dikdörtgenle vurgulanan **ıssuersigningkeyiyon** ifadesini içeren bölümden kopyalayın.

   ![oturum açma anahtarı tanımlayıcısı](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Belge belgesi

1. [DıF on ağ Gezginini](https://identity.foundation/ion/explorer/) açın

2. Arama çubuğuna yaptığınız gibi yapıştırın.

4. Biçimlendirilen yanıttan, doğrulamalar **Icationmethod** adlı bölümü bulun
5. "Idicationmethod" altında, kimliği kopyalayın ve Kvsigningkeyıd olarak etiketleyin
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Şimdi örnek kodumuza değişiklikleri yapmak için ihtiyacımız olan her şeyi sunuyoruz.

- **Veren:** app.js const kimlik bilgilerini yeni sözleşme URI 'siyle güncelleştirme
- **Verifier:** ıssuerdıd 'ı veren tanımlayıcıda güncelleştirin app.js
- **Veren ve Verifier** didconfig.jsaşağıdaki değerlerle güncelleştirin:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Bu bir tanıtım uygulamasıdır ve genellikle uygulamanıza gizli anahtarı doğrudan asla vermeniz gerekir.


Artık, kendi Anahtarlarınıza sahip Azure Active Directory kiracınızdan kendi doğrulanabilir kimlik bilgilerinizi doğrulamak ve bu her şeye sahip olursunuz. 

## <a name="issue-and-verify-the-vc"></a>VC 'yi verme ve doğrulama

Doğrulanabilen kimlik bilgisini vermek ve uygulamanızla doğrulamak için önceki öğreticide izlediğiniz adımların aynısını izleyin. Doğrulama işlemini başarıyla tamamladıktan sonra doğrulanabilir kimlik bilgileri hakkında öğrenmeye devam etmek için hazırsınız.

1. Bir komut istemi açın ve veren klasörünü açın.
2. Güncelleştirilmiş düğüm uygulamasını çalıştırın.

    ```terminal
    node app.js
    ```

3. Farklı bir komut istemi kullanarak 8081 üzerinde bir URL ayarlamak için ngrok komutunu çalıştırın

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Bu uygulamanın veya Web sitesinin riskli olabileceğini bildiren bir uyarı da görebilirsiniz. Sizi henüz etki alanına bağladığımız için ileti şu anda bekleniyor. Bunu yapılandırmak için [DNS bağlama](how-to-dnsbind.md) yönergelerini izleyin.

    
4. Ngrok tarafından oluşturulan HTTPS URL 'sini açın.

    ![NGROK iletme uç noktaları](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. **KIMLIK bilgisi al** 'ı seçin
6. Kod doğrulayıcısı 'nda QR kodunu tarayın.
7. **Bu uygulamada veya Web sitesinde riskli uyarı iletisi olabilir ve** **Gelişmiş**' i seçin.

  ![İlk uyarı](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Riskli Web sitesi uyarısında **yine de devam et (güvensiz)** seçeneğini belirleyin

  ![Veren hakkında ikinci uyarı](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. **Kimlik bilgisi ekle** ekranında birkaç şey fark edebilirsiniz: 
    1. Ekranın üst kısmında, kırmızı **doğrulanmamış** bir ileti görebilirsiniz
    1. Kimlik bilgisi, görüntüleme dosyasında yaptığımız değişikliklere göre özelleştirilir.
    1. **Hesabınızda oturum açın** SEÇENEĞINDE Azure AD oturum açma sayfasına işaret edilir.
    
   ![uyarı içeren kimlik bilgisi ekranı ekle](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. **Hesabınızda oturum açın** ve Azure AD kiracınızdaki bir kullanıcıyı kullanarak kimlik doğrulaması yapın.
11. **Ekleme** düğmesinin başarıyla doğrulanmasından sonra artık gri değildir. **Ekle**' yi seçin.

  ![kimlik doğrulamasından sonra kimlik bilgisi ekranı ekle](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Artık kimlik doğrulaması için özgün B2C kiracısı kullanılırken, kiracınızı oluşturmak üzere kiracınızı kullanarak doğrulanabilir bir kimlik bilgisi vermiş olduğumuz.

  ![Azure AD 'niz tarafından verilen ve Azure B2C örneğimiz tarafından kimliği doğrulanan VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Örnek uygulamayı kullanarak VC 'yi doğrulama testi

Artık Azure AD 'nizden gelen taleplerden doğrulanabilir kimlik bilgilerini verdiğimiz için, bunu örnek uygulamayı kullanarak doğrulayalım.

1. Verenin ngrok hizmetini çalıştırmayı durdurun.

    ```cmd
    control-c
    ```

2. Şimdi ngrok 'yı doğrulayıcı bağlantı noktası 8082 ile çalıştırın.

    ```cmd
    ngrok http 8082
    ```

3. Başka bir Terminal penceresinde, doğrulayıcı uygulamasına gidin ve sertifikayı veren uygulamayı çalıştırdığımız şekilde çalıştırın.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Tarayıcınızda ngrok URL 'sini açın ve mobil cihazınızdaki kimlik doğrulayıcı 'yı kullanarak QR kodunu tarayın.
5. Mobil cihazınızda, **yeni izin isteği** ekranında **izin ver** ' i seçin.

   >[!IMPORTANT]
    > Örnek uygulama, sunu isteğini imzalamak için de Uygulamanızı kullandığından, bu uygulamanın veya Web sitesinin riskli olabileceğini bildiren bir uyarı görürsünüz. Sizi henüz etki alanına bağladığımız için ileti şu anda bekleniyor. Bunu yapılandırmak için [DNS bağlama](how-to-dnsbind.md) yönergelerini izleyin.
    
   ![Yeni izin isteği](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Kimlik bilgilerinizi başarıyla doğruladınız ve Web sitesi Azure AD 'nin Kullanıcı hesabındaki adı ve soyadınızı görüntülemelidir. 

Artık öğreticiyi tamamladınız ve resmi olarak doğrulanmış bir kimlik bilgisi uzmanı! Örnek uygulamanız, Azure AD 'nizden doğrulanabilen bir kimlik bilgisine talepler yazarken hem verme hem de doğrulama için yaptığınız işlemi kullanıyor. 

## <a name="next-steps"></a>Sonraki adımlar

- [Özel kimlik bilgileri](credential-design.md) oluşturmayı öğrenin
- Veren hizmeti iletişim [örnekleri](issuer-openid.md)
