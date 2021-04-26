---
title: Öğretici-Azure Active Directory doğrulanabilir kimlik bilgileri verecek şekilde yapılandırma (Önizleme)
description: Bu öğreticide, kiracınızda doğrulanabilir kimlik bilgilerini dağıtmak için gereken ortamı oluşturacaksınız
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: cd39f6c484ebe116918611bb1d543c1919a3cb0a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222954"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Öğretici-Azure Active Directory doğrulanabilir kimlik bilgileri verecek şekilde yapılandırma (Önizleme)

Bu öğreticide, [kullanmaya başlama](get-started-verifiable-credentials.md) makalesinin bir parçası olarak yapılan çalışmayı geliştirdik ve Azure Active Directory (Azure AD) kendi kendine bağlı olmayan [tanımlayıcı](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID) ile ayarlanıyor. Örnek uygulamayı ve vereninizi kullanarak doğrulanabilir bir kimlik bilgisi vermek için merkezileşmemiş tanımlayıcıyı kullanırız; Bununla birlikte, bu öğreticide, kimlik doğrulaması için örnek Azure B2C kiracısı 'ni kullanmaya devam ediyoruz.  Sonraki öğreticimizde, uygulamayı Azure AD 'niz ile çalışacak şekilde yapılandırmak için ek adımlar kullanacağız.

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu makalede:

> [!div class="checklist"]
> * Doğrulanabilir kimlik bilgileri için Azure AD 'nizi eklemek üzere gerekli hizmetleri oluşturun 
> * Biz de YAPTıK
> * Kuralları ve görüntü dosyalarını özelleştiriyoruz
> * Azure AD 'de doğrulanabilen kimlik bilgilerini yapılandırın.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için öncelikle şunları yapmanız gerekir:

- [Başlarken](get-started-verifiable-credentials.md)' i doldurun.
- Etkin aboneliği olan bir Azure hesabına sahip olmanız gerekir. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD ile P2 [lisansı](https://azure.microsoft.com/pricing/details/active-directory/). Bir tane yoksa [Ücretsiz geliştirici hesabı oluşturma](how-to-create-a-free-developer-account.md) ' yı izleyin.
- Anahtar ve gizli dizi oluşturma haklarınızın olduğu [Azure Key Vault](../../key-vault/general/overview.md) örneği.

## <a name="azure-active-directory"></a>Azure Active Directory

Başlamadan önce bir Azure AD kiracısına ihtiyacımız var. Kiracınız doğrulanabilir kimlik bilgileri için etkinleştirildiğinde, bir merkezi olmayan tanımlayıcı (DıD) atanır ve doğrulanabilir kimlik bilgilerini veren bir veren hizmeti ile etkinleştirilir. Aldığınız herhangi bir doğrulanabilir kimlik bilgisi, kiracınız ve kendi DıD tarafından verilir. Ayrıca, doğrulanabilir kimlik bilgileri doğrulanırken de kullanılır.
Yalnızca bir test Azure aboneliği oluşturduysanız, kiracınızın Kullanıcı hesaplarıyla doldurulması gerekmez, ancak sonraki öğreticileri tamamlayabilmeniz için en az bir Kullanıcı test hesabına sahip olmanız gerekir.

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

Doğrulanabilir kimlik bilgileriyle çalışırken, kiracınızın doğrulanabilen kimlik bilgilerini dijital olarak imzalamak için kullandığı şifreleme anahtarlarının denetimini ve yönetimini tamamlayabilirsiniz. Kimlik bilgilerini vermek ve doğrulamak için, Azure AD 'nin kendi Azure Key Vault örneğine erişimi sağlamanız gerekir.

1. Azure portal menüsünde veya **giriş** sayfasından **kaynak oluştur**' u seçin.
2. Arama kutusuna **Anahtar Kasası** girin.
3. Sonuç listesinden **Key Vault**’u seçin.
4. Key Vault bölümünde **Oluştur**’u seçin.
5. **Anahtar kasası oluşturma** bölümünde aşağıdaki bilgileri sağlayın:
    - **Abonelik**: Bir abonelik seçin.
    - **Kaynak grubu** altında **Yeni oluştur** ' u seçin ve **VC-Resource-Group** gibi bir kaynak grubu adı girin. Aynı kaynak grubu adını birden çok makale genelinde kullanıyoruz.
    - **Ad**: Benzersiz bir ad gereklidir. **Woodgroup-VC-kV** ' i kullanıyoruz, bu değeri kendi benzersiz adınızla değiştirin.
    - **Konum** aşağı açılan menüsünde bir konum seçin.
    - Diğer seçenekleri varsayılan değerlerinde bırakın.
6. Yukarıdaki bilgileri sağladıktan sonra **erişim ilkesi** ' ni seçin.

    ![Anahtar Kasası sayfası oluşturma](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. Erişim Ilkesi ekranında, **erişim Ilkesi Ekle** ' yi seçin.

    >[!NOTE]
    > Varsayılan olarak, anahtar kasasını oluşturan hesap yalnızca erişim içeren bir hesaptır. Doğrulanabilir kimlik bilgisi hizmeti 'nin anahtar kasasına erişmesi gerekir. Anahtar kasasının, yöneticinin **anahtar oluşturmasına** izin veren bir erişim ilkesi olması gerekir, devre dışı bırakırsanız **anahtarları silebilir** ve doğrulanabilir kimlik bilgileri için etki alanı bağlamayı oluşturmak için **oturum açın** . Test sırasında aynı hesabı kullanıyorsanız, kasa oluşturucularına verilen varsayılan izinlere ek olarak hesap **oturum açma** izni vermek için varsayılan ilkeyi değiştirdiğinizden emin olun.

8. Kullanıcı Yöneticisi için, anahtar izinleri bölümünde **oluşturma**, **silme** ve **imza** özelliğinin etkinleştirildiğinden emin olun. Varsayılan olarak oluştur ve Sil özelliği zaten etkinleştirilmiştir ve Imza, güncelleştirilmesi gereken tek anahtar Izni olmalıdır. 

    ![Key Vault izinleri](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. **Gözden geçir ve oluştur**’u seçin.
10. **Oluştur**’u seçin.
11. Kasaya gidin ve kasa adını ve URI 'yi bir yere göz atın

Aşağıda listelenen iki özelliği not edin:

- **Kasa adı**: örnekte, değer adı **Woodgrove-VC-kV**' dir. Diğer adımlar için bu adı kullanırsınız.
- **Kasa URI 'si**: örnekte bu değer https://woodgrove-vc-kv.vault.azure.net/ . REST API'si aracılığıyla kasanızı kullanan uygulamaların bu URI'yi kullanması gerekir.

>[!NOTE]
> Her Anahtar Kasası işlemi, ek Azure abonelik maliyetlerine neden olur. Daha fazla ayrıntı için [Key Vault fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/key-vault/) gözden geçirin.

>[!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri önizlemesi sırasında, kasasında oluşturulan anahtarlar ve gizli diziler oluşturulduktan sonra değiştirilmemelidir. Anahtarlarınızı ve sırlarınızı silme, devre dışı bırakma veya güncelleştirme, verilen tüm kimlik bilgilerini geçersiz kılar. Önizleme sırasında anahtarlarınızı veya gizli dizileri değiştirmeyin.

## <a name="create-a-modified-rules-and-display-file"></a>Değiştirilen kurallar oluşturma ve dosya görüntüleme

Bu bölümde, kuralları kullanıyoruz ve örnek veren uygulamasından dosyaları görüntüler ve kiracınızın ilk doğrulanabilir kimlik bilgisini oluşturmak için bunları biraz daha değiştirin.

1. Her iki kuralı da kopyalayın ve JSON dosyalarını geçici bir klasöre görüntüleyin ve sırasıyla **MyFirstVC-display.js** ve **MyFirstVC-rules.js** yeniden adlandırın. Her iki dosyayı da **veren \ issuer_config** bulabilirsiniz

   ![dosyaları örnek uygulama dizininin bir parçası olarak görüntüleme ve kuralları](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![dosyaları bir geçici klasörde görüntüleme ve kuralları oluşturma](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Kod Düzenleyicinizde dosya MyFirstVC-rules.jsaçın. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

Şimdi tür alanını "MyFirstVC" olarak değiştirelim. 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Bu değişikliği kaydedin.

 >[!NOTE]
   > Öğreticide bu noktada **"yapılandırmayı"** veya **"client_id"** öğesini değiştirmedik. Kullanmaya devam etmek için kullanmaya [başladığımız](get-started-verifiable-credentials.md)Microsoft B2C kiracısı hala kullanılmaktadır. Sonraki öğreticide Azure AD 'nizi kullanacağız.

3. Kod Düzenleyicinizde dosya MyFirstVC-display.jsaçın.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Bu doğrulanabilir kimlik bilgisinin örnek kodun sürümünden daha farklı görünmesini sağlamak için birkaç değişiklik yapmanızı sağlar. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Bu değişiklikleri kaydedin.
## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

İlk doğrulanabilir kimlik bilgilerini oluşturmadan önce yapılandırma ve kural dosyalarını tutabilecek bir BLOB depolama kapsayıcısı oluşturmamız gerekir.

1. Aşağıda gösterilen seçenekleri kullanarak bir depolama hesabı oluşturun. Ayrıntılı adımlar için [depolama hesabı oluşturma](../../storage/common/storage-account-create.md?tabs=azure-portal) makalesini inceleyin.

   - **Abonelik:** Bu öğreticiler için kullandığımız aboneliği seçin.
   - **Kaynak grubu:** Önceki öğreticilerde kullandığımız aynı kaynak grubunu seçin (**VC-Resource-Group**).
   - **Ad:**  Benzersiz bir ad.
   - **Konum:** (US) Doğu ABD.
   - **Performans:** Stand.
   - **Hesap türü:** Depolama v2.
   - **Çoğaltma:** Yerel olarak yedekli.
 
   ![Yeni depolama hesabı oluşturma](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Depolama hesabını oluşturduktan sonra bir kapsayıcı oluşturmamız gerekir. **BLOB depolama** altında **kapsayıcılar** ' ı seçin ve aşağıda belirtilen değerleri kullanarak bir kapsayıcı oluşturun:

    - **Ad:** VC-Container
    - **Genel erişim düzeyi:** Özel (anonim erişim yok)

   ![Kapsayıcı oluşturma](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Şimdi yeni kapsayıcınızı seçin ve daha önce oluşturduğumuz üzerinde **MyFirstVC-display.js** ve **MyFirstVC-rules.js** yeni kuralları ve görüntü dosyalarını karşıya yükleyin.

   ![karşıya yükleme kuralları dosyası](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Blob rolü ata

Kimlik bilgisini oluşturmadan önce, Depolama Blobu içindeki dosyalara erişebilmeleri için önce oturum açan kullanıcıya doğru rol atamasını vermemiz gerekir.

1. **Depolama**  >  **kapsayıcısına** gidin.
2. Soldaki menüden **Access Control (IAM)** öğesini seçin.
3. **Rol atamaları**' nı seçin.
4. **Add (Ekle)** seçeneğini belirleyin.
5. **Rol** bölümünde **Depolama Blobu veri okuyucusu**' nu seçin.
6. **Kullanıcı, Grup veya hizmet ilkesini** seçmek **için erişim ata** altında.
7. **Seç** bölümünde: Bu adımları gerçekleştirmek için kullandığınız hesabı seçin.
8. Rol atamasını gerçekleştirmek için **Kaydet** ' i seçin.


   ![Rol ataması ekleyin](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Varsayılan olarak, kapsayıcı oluşturucuları atanan **sahip** rolünü alır. **Sahip** rolü kendi kendine yeterince fazla değil. Hesabınız **Depolama Blobu veri okuyucusu** rolüne ihtiyaç duyuyor. Daha fazla bilgi için [, blob ve kuyruk verilerine erişim için bir Azure rolü atamak üzere Azure Portal kullanın](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Doğrulanabilir kimlik bilgilerini ayarlama (Önizleme)

Şimdi, kiracınızı doğrulanabilir kimlik bilgileri için ayarlamaya yönelik son adımı gerçekleştirmeniz gerekir.

1. Azure portal, **doğrulanabilir kimlik bilgilerini** arayın.
2. **Doğrulanabilen kimlik bilgileri (Önizleme)** öğesini seçin.
3. **Kullanmaya** başlayın seçeneğini belirleyin
4. Kuruluşunuzu ayarlamanız ve kuruluş adı, etki alanı ve Anahtar Kasası sağlamanız gerekir. Her bir değerden birine bakalım.

      - **kuruluş adı**: Bu ad, bu adı doğrulanabilir kimlik bilgisi hizmeti içinde işletmenize nasıl başvurunuzun adıdır. Bu değer müşteriye yönelik değildir.
      - **Etki alanı:** Girilen etki alanı, bu belgenizdeki bir hizmet uç noktasına eklenir. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) ve diğer walbu bilgileri, uygulamanızın [etki alanına bağlı](how-to-dnsbind.md)olduğunu doğrulamak için kullanır. Cüzdan olduğunu doğrulayabiliyorsanız, doğrulanmış bir simge görüntüler. Cüzdan sizi doğrulayamadığından, kimlik bilgisinin doğrulayamayan bir kuruluş tarafından verildiğini kullanıcıya bildirir. Etki alanı, sizi, kullanıcının işletmeniz hakkında bilbileceği somut bir şeye bağlar.
      - **Anahtar Kasası:** Daha önce oluşturduğumuz Key Vault adını belirtin.
 
   >[!IMPORTANT]
   > Etki alanı yeniden yönlendirme olamaz, aksi halde, ve etki alanı bağlanamaz. Biçimini kullandığınızdan emin olun https://www.domain.com .
    
5. **Kaydet ve kimlik bilgisi oluştur** seçeneklerini belirleyin

    ![Kuruluş Kimliğinizi ayarlama](media/enable-your-tenant-verifiable-credentials/save-create.png)

Tebrikler, kiracınız artık doğrulanabilir kimlik bilgisi önizlemesi için etkinleştirilmiştir!

## <a name="create-your-vc-in-the-portal"></a>Portalda VC 'nizi oluşturma

Önceki adım sizi **yeni kimlik bilgisi oluştur** sayfasında bırakır. 

   ![doğrulanabilir kimlik bilgileri Başlarken](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Kimlik bilgisi adı altında, **Myfirstvc** adını ekleyin. Bu ad, portalda doğrulanabilir kimlik bilgilerinizi tanımlamak için kullanılır ve doğrulanabilir kimlik bilgileri sözleşmesinin bir parçası olarak dahil edilmiştir.
2. Görüntü dosyası bölümünde, **görüntü dosyasını Yapılandır** ' ı seçin.
3. **Depolama hesapları** bölümünde **woodgrovevcstorage**' ı seçin.
4. Kullanılabilir kapsayıcılar listesinden **VC-Container**' ı seçin.
5. Daha önce oluşturduğumuz dosyada **MyFirstVC-display.js** seçin.
6. **Kurallar dosyasındaki** **yeni kimlik bilgisi oluştur** bölümünde **kural dosyasını Yapılandır** ' ı seçin.
7. **Depolama hesapları** bölümünde **woodgrovecstorage** ' ı seçin.
8. **VC-Container**' ı seçin.
9. **MyFirstVC-rules.js** seçin
10. **Yeni kimlik bilgisi oluştur** ekranında **Oluştur**' a tıklayın.

   ![Yeni kimlik bilgisi oluştur](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>Kimlik bilgisi URL 'SI

Artık yeni bir kimlik bilgisine sahip olduğunuza göre, kimlik bilgileri URL 'sini kopyalayın.

   ![Sorun kimlik bilgileri URL 'SI](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>Kimlik bilgisi URL 'SI kuralların ve görüntü dosyalarının birleşimidir. Bu, kullanıcı doğrulanabilir kimlik bilgileri verme gereksinimlerine görüntülemeden önce Doğrulayıcı tarafından değerlendirilen URL 'dir.  

## <a name="update-the-sample-app"></a>Örnek uygulamayı güncelleştirme

Şimdi, örnek uygulamanın veren kodunda değişiklik yapıp, bunu doğrulanabilir kimlik bilgileri URL 'niz ile güncelleştirebilirsiniz. Bu, kendi kiracınızı kullanarak doğrulanabilir kimlik bilgileri vermenize olanak tanır.

1. Örnek uygulamanın dosyalarını yerleştirdiğiniz klasöre gidin.
2. Sertifikayı veren klasörünü açın ve Visual Studio Code app.js açın.
3. ' Credential ' sabitini yeni kimlik bilgileri URL 'siyle güncelleştirin ve credentialType sabitini ' MyFirstVC ' olarak ayarlayın ve dosyayı kaydedin.

    ![vurgulanmış ilgili alanların gösterildiği Visual Studio Code görüntüsü](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Bir komut istemi açın ve veren klasörünü açın.
5. Güncelleştirilmiş düğüm uygulamasını çalıştırın.

    ```terminal
    node app.js
    ```

6. Farklı bir komut istemi kullanarak 8081 üzerinde bir URL ayarlamak için ngrok komutunu çalıştırın

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Bu uygulamanın veya Web sitesinin riskli olabileceğini bildiren bir uyarı da görebilirsiniz. Sizi henüz etki alanına bağladığımız için ileti şu anda bekleniyor. Bunu yapılandırmak için [DNS bağlama](how-to-dnsbind.md) yönergelerini izleyin.

    
7. Ngrok tarafından oluşturulan HTTPS URL 'sini açın.

    ![NGROK iletme uç noktaları](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. **KIMLIK bilgisi al** 'ı seçin
9. Kod doğrulayıcısı 'nda QR kodunu tarayın.
10. **Bu uygulamada veya Web sitesinde riskli uyarı iletisi olabilir ve** **Gelişmiş**' i seçin.

  ![İlk uyarı](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. Riskli Web sitesi uyarısında **yine de devam et (güvensiz)** seçeneğini belirleyin

  ![Veren hakkında ikinci uyarı](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. **Kimlik bilgisi ekle** ekranında birkaç şey fark edebilirsiniz: 
    1. Ekranın üst kısmında, kırmızı **doğrulanmamış** bir ileti görebilirsiniz
    1. Kimlik bilgisi, görüntüleme dosyasında yaptığımız değişikliklere göre özelleştirilir.
    1. **Hesapta oturum açın** seçeneğinde **didplayground.b2clogin.com**' a işaret edilir.
    
   ![uyarı içeren kimlik bilgisi ekranı ekle](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. **Hesabınızda oturum aç** ' ı seçin ve [Başlarken öğreticisinde](get-started-verifiable-credentials.md)verdiğiniz kimlik bilgileri bilgilerini kullanarak kimlik doğrulaması yapın.
14. **Ekleme** düğmesinin başarıyla doğrulanmasından sonra artık gri değildir. **Ekle**' yi seçin.

  ![kimlik doğrulamasından sonra kimlik bilgisi ekranı ekle](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Şimdi, kimlik doğrulaması için B2C kiracımızı kullanırken, VC 'imizi oluşturmak üzere kiracımızı kullanarak bir doğrulanabilen kimlik bilgisi verdik.

  ![Azure AD 'niz tarafından verilen ve Azure B2C örneğimiz tarafından kimliği doğrulanan VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Örnek uygulamayı kullanarak VC 'yi doğrulama testi

Artık kendi kiracımız doğrulanabilir kimlik bilgilerini verdiğimiz için, bunu örnek uygulamamız ile doğrulayalım.

>[!IMPORTANT]
> Test edilirken, [Başlarken](get-started-verifiable-credentials.md) makalesi sırasında kullandığınız e-posta ve parolayı kullanın. Kiracınız VC 'yi yayınlarken, giriş, Microsoft B2C kiracısı tarafından verilen bir kimlik belirtecinden geliyor. İki öğreticide, kiracınıza belirteç vermeyi geçiyoruz

1. **Ayarları** Azure Portal doğrulanabilir kimlik bilgileri dikey penceresinde açın. Merkezi olmayan tanımlayıcıyı (DıD) kopyalayın.

   ![DıD 'yi kopyalayın](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Şimdi örnek uygulama dosyalarının Doğrulayıcı klasörü bölümünü açın. Doğrulayıcı örnek kodundaki app.js dosyasını güncelleştirmemiz ve aşağıdaki değişiklikleri yapmanız gerekir:

    - **kimlik bilgisi**: KIMLIK bilgisi URL 'nize değiştirme
    - **CredentialType**: ' Myfirstvc '
    - **ıssuerdıd**: bu değeri Azure Portal>doğrulanabilir kimlik bilgileri (önizleme) >ayarlar>merkezi olmayan tanımlayıcı (DID) ' den kopyalayın
    
   ![Sabit ıssuerdıd 'i veren tanımlayıcıda eşleşecek şekilde güncelleştirin](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Verenin ngrok hizmetini çalıştırmayı durdurun.

    ```cmd
    control-c
    ```

4. Şimdi ngrok 'yı doğrulayıcı bağlantı noktası 8082 ile çalıştırın.

    ```cmd
    ngrok http 8082
    ```

5. Başka bir Terminal penceresinde, doğrulayıcı uygulamasına gidin ve sertifikayı veren uygulamayı çalıştırdığımız şekilde çalıştırın.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Tarayıcınızda ngrok URL 'sini açın ve mobil cihazınızdaki kimlik doğrulayıcı 'yı kullanarak QR kodunu tarayın.
7. Mobil cihazınızda, **yeni izin isteği** ekranında **izin ver** ' i seçin.

    >[!NOTE]
    > Bu VC 'yi imzalayan hala Microsoft B2C. Doğrulayıcı hala Microsoft örnek uygulama kiracısından. Microsoft 'un sahip olduğu bir etki alanıyla bağlantılı olduğundan, verme akışı sırasında Karşılaşdığımız uyarıyı görmezsiniz. Bu, sonraki bölümde güncelleştirilecektir.
    
   ![Yeni izin isteği](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Kimlik bilgilerinizi başarıyla doğrulamadı.

## <a name="next-steps"></a>Sonraki adımlar

Artık verenden bir VC veren örnek koda sahip olduğunuza göre, doğrulanabilir kimlik bilgilerini almaya çalışan kullanıcıların kimliğini doğrulamak ve sunum isteklerini imzalamak için uygulamanızı kullanmak üzere kendi kimlik sağlayıcınızı kullandığınız bir sonraki bölüme devam etmenizi sağlar.

> [!div class="nextstepaction"]
> [Öğretici-kiracınızı kullanarak doğrulanabilir kimlik bilgilerini verme ve doğrulama](issue-verify-verifiable-credentials-your-tenant.md)


