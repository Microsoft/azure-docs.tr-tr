---
title: Öğretici-örnek uygulama kullanarak Azure Active Directory doğrulanabilir kimlik bilgilerini kullanmaya başlama (Önizleme)
description: Bu öğreticide, örnek uygulama ve test kiracımızı kullanarak doğrulanabilir kimlik bilgileri verme hakkında bilgi edineceksiniz
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222894"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Öğretici-örnek uygulama kullanarak Azure Active Directory doğrulanabilir kimlik bilgilerini kullanmaya başlama (Önizleme)

Bu öğreticide, ilk doğrulanabilir kimlik bilgilerinizi vermek için gereken adımlara geçeceğiz: doğrulanmış bir kimlik bilgisi uzmanı kartı. Daha sonra bu kartı kullanarak, dijital credentialing 'in bir bölümünde ana, doğrulanmış bir kimlik bilgisi uzmanı olduğunuzu doğrulayan bir doğrulayıcı olduğunu kanıtlayabilirsiniz. İlk doğrulanabilir kimlik bilgilerinizi vermek için doğrulanabilir kimlik bilgileri örnek uygulamasını kullanarak Azure Active Directory doğrulanabilir kimlik bilgilerini kullanmaya başlayın.

![Bu bir örnek kartın görüntüsüdür](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

- [NodeJS](https://nodejs.org/en/download/) sürüm 10,14 veya üzeri test sistemimize yüklendi.
- Örnek uygulamayı barındıran depoyu kopyalamak istiyorsanız [GIT](https://git-scm.com/downloads) 'in yüklü olması gerekir,
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Örnek sitemizi barındırmak için bir sistem.
- Microsoft Authenticator sürüm 6.2005.3599 veya üzeri yüklü bir mobil cihaz.
- [Ngrok](https://ngrok.com/) boş.

## <a name="download-the-sample-code"></a>Örnek kodu indirin

Kendi kendinize doğrulanmış bir kimlik bilgisi uzmanı kartı vermek için yerel makinenizde bir Web sitesi çalıştırmanız gerekir. Web sitesi, doğrulanabilir kimlik bilgisi verme işlemini başlatmak için kullanılır. Bu öğretici genelinde kullandığımız NodeJS ' de yazılmış basit bir Web sitesi sunuyoruz.

İlk olarak, GitHub 'dan örnek kodumuzu [buradan](https://github.com/Azure-Samples/active-directory-verifiable-credentials)indirin veya depoyu yerel makinenize kopyalayın:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Örnek Web sitelerindeki kodu tanımak isteyebilirsiniz. `issuer`Klasör, doğrulanabilir kimlik bilgisi vermek için kullanılan tüm kodu içerir. Örnek [Benioku](https://github.com/Azure-Samples/active-directory-verifiable-credentials)dosyasında daha fazla ayrıntı bulunabilir.

## <a name="run-the-issuer-website"></a>Veren Web sitesini çalıştırın

Adımları Visual Studio Code içinden veya işletim sisteminizde bulunan herhangi bir komut satırından çalıştırabilirsiniz. 

1. `issuer` klasörüne gidin. 

    ```terminal
    cd issuer
    ```

2. Tüm gerekli paketleri yüklememiz ve siteyi başlatmanız gerekir.

   ```terminal
    npm install
    node app.js
    ```

3. Terminal 'de, artık sertifika veren uygulamanızın 8081 numaralı bağlantı noktasını dinlediğini görürsünüz. Şimdi Ngrok ile bir ters proxy ayarlayalim ve bu nedenle kimlik doğrulayıcı uygulamanızla iletişim kurabilir. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Ngrok ile ters proxy oluşturma

Örnek Web sitesini çalıştırdığınızda, cihazınızın yerel makinenizde çalışan düğüm sunucusuyla iletişim kurması gerekir. Yerel geliştirme sunucunuzu Internet üzerinden kullanılabilir hale getirmenin kolay bir yolu olarak [ngrok](https://ngrok.com/) kullanmanızı öneririz.

1.  **Ngrok** indirip ayıkladıktan sonra şunu çalıştırdık:

    ```terminal
     ngrok http 8081
    ```

Varsayılan olarak, örnek Web sitesi bağlantı noktasında çalışır `8081` . **Ngrok** , sunucunuz için Iki Iletme URL 'si verir. URL 'YI `https://` ön eke kopyalayın.

![ngrok, uygulama bitiş noktalarınızı Internet üzerinden kullanılabilir hale getirme olanağı sağlar](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> PowerShell kullanıyorsanız, `./ngrok` tanınması için komut yazmanız gerekebilir.

Artık yerel bağlantı noktanızın ngrok kullanılarak internet 'e açık olduğuna göre, örnek site ngrok tarafından oluşturulan ana bilgisayar adını otomatik olarak kullanır. Tarayıcınızı açın ve ngrok https iletme URL 'sine gidin. Örnek sitenin giriş sayfasını başarıyla ziyaret edebilmelisiniz. Sayfa açılırsa, cihazınız yerel sunucunuzda çalışan örnek uygulamayla iletişim kurabilir. Artık doğrulanmış bir kimlik bilgisi uzmanı kartı oluşturmaya hazırsınız.

## <a name="issue-a-credential"></a>Kimlik bilgisi verme

1. Mobil cihazınıza kimlik doğrulayıcı yükler. Microsoft Authenticator, ilgilenen taraflara doğrulanabilir kimlik bilgilerinizi almak, depolamak ve sunmak için kullanılır.

2. Sonra, kendinizi doğrulanabilir bir kimlik bilgisi olarak verin. **Kimlik bilgisi al** düğmesine **tıklayın** . **Kimlik bilgisi al** düğmesine tıkladığınızda örnek Web sitesi, kimlik doğrulayıcı 'yı kullanarak tarayabileceğiniz bir QR kodu görüntüler. Siteyi mobil cihazınızda tarayıcıdan görüntülediğinizde, **kimlik bilgisi al** düğmesine tıklamak, Authenticator uygulamasını açan ve QR kodu taramasını gerektirmeyen derin bir bağlantıyı tetikler.

   ![Kimlik bilgisi Al düğmesi](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Kimlik doğrulayıcı 'yı kullanarak Web sitesinin QR kodunu tarayın veya Web sitesine bir mobil aracılığıyla erişiyorsanız derin bağlantıyı tetiklemek için kimlik bilgisi al düğmesine tıklayın. 

   ![QR Kodu ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. **Ekle** düğmesinin Şu anda gri olduğunu fark edeceksiniz. Kart görüntüsünün altındaki **hesabınızda oturum aç '** ı seçin.

   ![Oturum açın ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Kimlik bilgisi uzmanı kartınızı almadan önce kullandığımız Kiracı kimlik doğrulama bilgilerini sağlamanızı gerektirir. İlk kez öğreticide bir kimlik bilgisi uzmanı hesabınız yoksa B2C kiracımızda yeni bir kullanıcı hesabı oluşturun.

   ![devam etmeden önce kimlik doğrulaması yapın](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Oturum açtıktan sonra **Ekle** düğmesi artık gri değildir. Yeni VC 'nizi kabul etmek için **Ekle** ' yi seçin.

   ![Kimlik doğrulamasından sonra Ekle 'yi seçin](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Tebrikler! Artık doğrulanmış bir Credential Expert VC 'si var.

   ![Credential Expert VC eklendi](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Ardından, kimlik bilgilerinizin doğrulanması zaman alabilir.

## <a name="validate-credentials"></a>Kimlik bilgilerini doğrulama

Öğreticinin verme bölümünü tamamladığınıza ve kimlik doğrulayıcısında doğrulanabilir bir kimlik bilgilerinizin olduğuna göre, bu, kendi Doğrulayıcı uygulamanızda doğrulanması zaman alabilir.

1.  Verenin ngrok hizmetini çalıştırmayı durdurun.

    ```terminal
     control+c
    ```


2. Başka bir Terminal penceresinde, doğrulayıcı uygulama klasörünü açın ve sertifikayı veren uygulamayı çalıştırdığımız şekilde çalıştırın.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Şimdi ngrok 'yı doğrulayıcı bağlantı noktası 8082 ile çalıştırın.

    ```terminal
    ngrok http 8082
    ```

4. Tarayıcınızda ngrok https iletme URL 'sini açın ve **KIMLIK bilgilerini doğrula** düğmesine dokunun.  

   ![Credential Expert düğmesini doğrula](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Authenticator açın ve QR kodunu tarayın.

   ![kimlik bilgilerini doğrulamak için QR kodunu tarayın](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > İOS 'ta bu, en sağdaki ve Android 'in en sağ alt tarafında yer alır. QR kodunu tarayın.

6. Kimlik Doğrulayıcıdaki yeni izin isteği ekranında **Izin ver** ' i seçin. İzin ver ' e basılarak, bu doğrulanabilir kimlik bilgilerini gerçekten denetlemek için, yaptığınız bir doğrulanabilir sunuyu (merkezi olmayan tanımlayıcı) imzalayadınız.

   ![Yeni izin isteği](media/get-started-verifiable-credentials/new-permission-request.png)

    Başarılı bir sunum sonrasında üç şey güncelleştirildikten sonra:

   1. Web sayfası şimdi "Tebrikler, adınız" ve doğrulanmış bir Credential Expert! "görüntülemesi gerekir.
   
    ![Tebrikler, yeniden doğrula](media/get-started-verifiable-credentials/congratulations.png)


   2. Verifier App terminalinizde Ayrıca günlüklerden aynı ileti görüntülenmelidir.
   
    ![komut isteminde uygulama etkinliği](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. Authenticator ' de, bu sununun son etkinliği için bir giriş olmalıdır.

    ![Authenticator içindeki etkinlik](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Doğrulayıcı uygulamasını çalıştırırken ngrok çalışmayı durdurabilir ve çok fazla bağlantı olduğunu belirten bir hata gösterebilir. Hesabınızı ngrok ile kaydederek bu, kaçınılmaz. 

## <a name="next-steps"></a>Sonraki adımlar

Hızlı Başlangıç kılavuzunu başarıyla tamamladığınıza göre, Azure AD doğrulanabilir kimlik bilgileri hizmetinde kendi merkezi olmayan tanımlayıcıyı oluşturmanız ve kendi doğrulanabilir kimlik bilgilerinizi vermesi zaman atalım.

>[!div class="nextstepaction"] 
>[Doğrulanabilir kimlik bilgileri örnek uygulamasını kullanarak kendi sertifika vereninizi yapılandırın](./enable-your-tenant-verifiable-credentials.md)
