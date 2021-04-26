---
title: include dosyası
description: include dosyası
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 7b5034f2163e8478d7ddb7b9271402b094a809d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558305"
---
## <a name="generate-the-certificate-signing-request-file"></a>Sertifika imzalama istek dosyasını oluşturma

Apple Anında İletilen Bildirim Servisi (APNs), anında iletme bildirimlerinizin kimliğini doğrulamak için sertifikaları kullanır. Bildirim gönderip almak için gereken bildirim sertifikasını oluşturacak bu talimatları uygulayın. Bu kavramlar hakkında daha fazla bilgi için resmi [Apple Anında İletilen Bildirim servisi](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) belgelerine bakın.

Apple 'ın imzalı bir anında iletme sertifikası oluşturmak için kullandığı sertifika Imzalama Isteği (CSR) dosyasını oluşturun.

1. Mac’inizde Anahtar Zinciri Erişimi aracını çalıştırın. Bu, **yardımcı programlar** klasöründen veya başlatma panelindeki **diğer** klasörden açılabilir.

1. **Anahtarlık erişimi** seçin, **sertifika Yardımcısı**' nı genişletin ve bir **sertifika yetkilisinden sertifika iste**' yi seçin.

    ![Anahtarlık Erişimi kullanarak yeni sertifika isteğinde bulunma](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Varsayılan olarak, Anahtarlık erişimi listedeki ilk öğeyi seçer. Bu, **Sertifikalar** **kategorisindeyseniz ve Apple Dünya genelinde geliştirici Ilişkileri sertifika yetkilisiyseniz** listedeki ilk öğe değilse bir sorun olabilir. CSR 'yi (sertifika Imzalama Isteği) oluşturmadan önce, anahtar olmayan bir öğeye sahip olduğunuzdan veya **Apple Dünya genelinde geliştirici Ilişkileri sertifika yetkilisi** anahtarının seçildiğinden emin olun.

1. **Kullanıcı e-posta adresinizi** seçin, **ortak ad** değerini girin, **diske kaydedilmiş**' ı belirttiğinizden emin olun ve ardından **devam**' ı seçin. **CA e-posta adresini** gerekli olmadığından boş bırakın.

    ![Gerekli sertifika bilgileri](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. **Farklı kaydet**' de CSR dosyası için bir ad girin, **konum ' u** seçin ve ardından **Kaydet**' i seçin.

    ![Sertifika için bir dosya adı seçin](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Bu eylem, CSR dosyasını seçilen konuma kaydeder. Varsayılan konum **Desktop**' dır. Bu dosya için seçilen konumu unutmayın.

Ardından, uygulamanızı Apple 'a kaydedin, anında iletme bildirimlerini etkinleştirin ve bir anında iletme sertifikası oluşturmak için, içeri aktarılmış CSR 'yi karşıya yükleyin.

## <a name="register-your-app-for-push-notifications"></a>Anında iletme bildirimleri için uygulamanızı kaydetme

Bir iOS uygulamasına anında iletme bildirimleri göndermek için uygulamanızı Apple 'a kaydedin ve anında iletme bildirimleri için kaydolun.  

1. Uygulamanızı henüz kaydetmediyseniz, Apple Geliştirici Merkezi ' nde [IOS sağlama portalı](https://go.microsoft.com/fwlink/p/?LinkId=272456) ' na gidin. Apple KIMLIĞINIZLE portalda oturum açın ve **tanımlayıcılar**' ı seçin. Sonra **+** Yeni bir uygulama kaydetmeyi seçin.

    ![iOS Hazırlama Portalı Uygulama Kimlikleri sayfası](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. **Yeni bir tanımlayıcı kaydet** ekranında, **uygulama kimlikleri** radyo düğmesini seçin. Daha sonra **Devam** seçeneğini belirleyin.

    ![iOS sağlama portalı kayıt yeni KIMLIK sayfası](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Yeni uygulamanız için aşağıdaki üç değeri güncelleştirin ve ardından **devam**' ı seçin:

   * **Açıklama**: uygulamanız için açıklayıcı bir ad yazın.

   * **Paket kimliği**: [uygulama dağıtım kılavuzunda](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)belirtilen şekilde, **kuruluş tanımlayıcısı. ürün adı** form için bir paket kimliği girin. *Kuruluş tanımlayıcısı* ve *ürün adı* değerleri, Xcode projenizi oluştururken kullandığınız kuruluş tanımlayıcısı ve ürün adıyla eşleşmelidir. Aşağıdaki ekran görüntüsünde, **Notificationhub 'lar** değeri bir kuruluş tanımlayıcısı olarak kullanılır ve **getstarted** değeri ürün adı olarak kullanılır. **Paket tanımlayıcı** değerinin Xcode projenizdeki değerle eşleştiğinden emin olun, böylece Xcode doğru yayımlama profilini kullanır.

      ![iOS sağlama portalı kayıt uygulama KIMLIĞI sayfası](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Anında Iletme bildirimleri**: **yetenekler** bölümündeki **anında iletme bildirimleri** seçeneğini işaretleyin.

      ![Yeni Uygulama Kimliği kaydetme formu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Bu eylem, uygulama KIMLIĞINIZI ve bilgileri onaylamak için isteklerinizi oluşturur. **Devam**' ı seçin ve ardından yenı uygulama kimliğini onaylamak için **Kaydet** ' i seçin.

      ![Yeni uygulama KIMLIĞINI Onayla](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      **Kaydet**' i seçtikten sonra, yenı uygulama kimliğini **sertifikalar, tanımlayıcılar & profiller** sayfasında bir satır öğesi olarak görürsünüz.

4. **Sertifikalar, tanımlayıcılar & profiller** sayfasında, **tanımlayıcılar**' ın altında, az önce oluşturduğunuz uygulama kimliği satırı öğesini bulun ve ardından **uygulama kimliği yapılandırmasını düzenleme** ekranınızı göstermek için satırını seçin.

## <a name="creating-a-certificate-for-notification-hubs"></a>Notification Hubs için sertifika oluşturma
Bildirim Hub 'ının **APNs** ile çalışmasını sağlamak için bir sertifika gerekir. Bu, iki şekilde yapılabilir:

1. Doğrudan Notification Hub 'ına yüklenebilen bir **. p12** oluşturun.  
2. [Belirteç tabanlı kimlik doğrulaması](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md) (*daha yeni yaklaşım*) için kullanılabilecek bir **. P8** oluşturun.

Yeni yaklaşım, [APNs Için belirteç tabanlı (http/2) kimlik doğrulaması](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md)bölümünde belgelendiği gibi bir dizi avantaja sahiptir (sertifikaların kullanılmasıyla karşılaştırıldığında). Ancak, her iki yaklaşım için de adımlar verilmiştir. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>SEÇENEK 1: doğrudan Bildirim Hub 'ına yüklenebilen bir. p12 Push sertifikası oluşturma

1. Gözden geçirilen **anında Iletme bildirimleri** seçeneğine kaydırın ve ardından sertifikayı oluşturmak için **Yapılandır** ' ı seçin.

    ![Uygulama Kimliğini Düzenle sayfası](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. **Apple Anında Iletilen bildirim HIZMETI SSL sertifikaları** penceresi görüntülenir. **GELIŞTIRME SSL sertifikası** bölümünün altında **sertifika oluştur** düğmesini seçin.

    ![Uygulama Kimliği için sertifika oluştur düğmesi](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    **Yeni sertifika oluştur** ekranı görüntülenir.

    > [!NOTE]
    > Bu öğretici, uygulamanızın benzersiz bir cihaz belirteci oluşturmak için kullandığı bir geliştirme sertifikası kullanır. Aynı işlem üretim sertifika kaydedildiğinde de kullanılır. Bildirimleri gönderirken aynı sertifika türünü kullandığınızdan kesinlikle emin olun.

3. **Dosya Seç**' i seçin, Ilk görevden CSR dosyasını kaydettiğiniz konuma gidin ve ardından bu sertifikayı yüklemek için sertifika adına çift tıklayın. Daha sonra **Devam** seçeneğini belirleyin.

4. Portal sertifikayı oluşturduktan sonra **İndir** düğmesini seçin. Sertifikayı kaydedin ve kaydedildiği konumu unutmayın.

    ![Oluşturulan sertifika indirme sayfası](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Sertifika indirilir ve **indirmeler** klasörünüzde bilgisayarınıza kaydedilir.

    ![İndirilenler klasöründe sertifika dosyasını bulma](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Varsayılan olarak, indirilen geliştirme sertifikası **aps_development. cer** olarak adlandırılır.

5. İndirilen **aps_development.cer** bildirim sertifikasına çift tıklayın. Bu eylem yeni sertifikayı Anahtar Zinciri’ne aşağıdaki resimde gösterildiği gibi yüklenir:

    ![Yeni sertifikanın gösterildiği anahtarlık erişim sertifikaları listesi](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Sertifikanıza ilişkin ad farklı olabilir, ancak ad **Apple Development IOS anında Iletme Hizmetleri**'ne önek olarak eklenir.

6. Anahtar Zinciri Erişimi’nde **Sertifikalar** kategorisinde oluşturduğunuz yeni bildirim sertifikasına sağ tıklayın. **Dışarı aktar**' ı seçin, dosyayı adlandırın, **. p12** biçimini seçin ve ardından **Kaydet**' i seçin.

    ![Sertifikayı p12 biçiminde dışarı aktarma](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Sertifikayı parolayla korumayı seçebilirsiniz, ancak bu isteğe bağlıdır. Parola oluşturmayı atlamak istiyorsanız **Tamam** ' ı tıklatın. Dışarı aktarılan .p12 sertifikanın dosya adını ve konumunu not edin. Bunlar, APNs ile kimlik doğrulamayı etkinleştirmek için kullanılır.

    > [!NOTE]
    > . P12 dosyanızın adı ve konumunuz, bu öğreticide görüntülenenden farklı olabilir.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>2. seçenek: belirteç tabanlı kimlik doğrulaması için kullanılabilen bir. P8 sertifikası oluşturma

1. Aşağıdaki ayrıntıları unutmayın:

    - **Uygulama kimliği öneki** (Bu BIR **Takım kimliğidir**)
    - **Paket Kimliği**
    
2. **Sertifikalar, tanımlayıcılar & profillere** geri dönüp **anahtarlar**' a tıklayın.

   > [!NOTE]
   > **APNs** için yapılandırılmış bir anahtarınız zaten varsa, oluşturulduktan hemen sonra indirdiğiniz. P8 sertifikasını yeniden kullanabilirsiniz. Bu durumda **3** ile **5** arasındaki adımları yoksayabilirsiniz.

3. **+** Yeni bir anahtar oluşturmak için düğmeye tıklayın (veya **anahtar oluştur** düğmesi).
4. Uygun bir **anahtar adı** değeri sağlayın, ardından **Apple Anında İletilen Bildirim hizmeti (APNs)** seçeneğini işaretleyin ve ardından **devam**' a ve ardından bir sonraki ekrana **Kaydol** ' a tıklayın.
5. **İndir** ' e tıklayın ve ardından **. p8** dosyasını ( *AuthKey_* ön eki) güvenli bir yerel dizine taşıyın ve **bitti**' ye tıklayın.

   > [!NOTE] 
   > . P8 dosyanızı güvenli bir yerde sakladığınızdan emin olun (ve bir yedek kaydedin). Anahtarınızı indirdikten sonra sunucu kopyası kaldırıldığından yeniden karşıdan yüklenemez.
  
6. **Anahtarlar**' da, az önce oluşturduğunuz anahtara (veya bunun yerine kullanmayı seçtiyseniz var olan bir anahtara) tıklayın.
7. **Anahtar kimliği** değerini bir yere getirin.
8. . P8 sertifikanızı, [**Visual Studio Code**](https://code.visualstudio.com) gibi tercih ettiğiniz uygun bir uygulamada açın, sonra anahtar değerini unutmayın. Bu, **-----Başlangıç özel anahtarı-----** ve **-----son özel anahtar-----** arasındaki değerdir.

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Bu, daha sonra **Bildirim Hub 'ı** yapılandırmak için kullanılacak **belirteç değeridir** . 

Bu adımların sonunda, [Bildirim Hub 'ınızı APNs bilgileriyle yapılandırma](#configure-your-notification-hub-with-apns-information)bölümünde daha sonra kullanmak için aşağıdaki bilgilere sahip olmanız gerekir:

- **Takım Kimliği** (bkz. 1. adım)
- **Paket kimliği** (bkz. 1. adım)
- **Anahtar kimliği** (bkz. Adım 7)
- **Belirteç değeri** örn. P8 anahtar değeri (bkz. 8. adım)

## <a name="create-a-provisioning-profile-for-the-app"></a>Uygulama için bir sağlama profili oluşturun

1. [IOS sağlama portalı](https://go.microsoft.com/fwlink/p/?LinkId=272456)' na dönün, **sertifikalar, tanımlayıcılar & profiller**' i seçin, sol menüden **profiller** ' i seçin ve ardından **+** Yeni bir profil oluşturmak için öğesini seçin. **Yeni bir sağlama profili kaydet** ekranı görüntülenir.

1. Sağlama profili türü olarak **geliştirme** bölümünde **iOS uygulama geliştirmeyi** seçin ve ardından **devam**' ı seçin.

    ![Sağlama profili listesi](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Ardından, **uygulama kimliği** açılır listesinden oluşturduğunuz uygulama kimliğini seçin ve **devam**' ı seçin.

    ![Uygulama Kimliği'ni seçin](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. **Sertifika seç** penceresinde, kod imzalama için kullandığınız geliştirme sertifikasını seçin ve **devam**' ı seçin. Bu sertifika, oluşturduğunuz gönderim sertifikası değil. Bir tane yoksa, onu oluşturmanız gerekir. Bir sertifika varsa, sonraki adıma atlayın. Yoksa bir geliştirme sertifikası oluşturmak için:

    1. **Kullanılabilir sertifika yoksa** **sertifika oluştur**' u seçin.
    2. **Yazılım** bölümünde **Apple geliştirme**' yi seçin. Daha sonra **Devam** seçeneğini belirleyin.
    3. **Yeni sertifika oluştur** ekranında **Dosya Seç**' i seçin.
    4. Daha önce oluşturduğunuz **sertifika Imzalama isteği** sertifikasına gidin, seçin ve sonra **Aç**' ı seçin.
    5. **Devam**’ı seçin.
    6. Geliştirme sertifikasını indirin ve kaydedildiği konumu unutmayın.

1. **Sertifikalar, tanımlayıcılar & profiller** sayfasına dönün, sol menüden **profiller** ' i seçin ve ardından **+** Yeni bir profil oluşturmak için öğesini seçin. **Yeni bir sağlama profili kaydet** ekranı görüntülenir.

1. **Sertifika seç** penceresinde, az önce oluşturduğunuz geliştirme sertifikasını seçin. Daha sonra **Devam** seçeneğini belirleyin.

1. Ardından, test için kullanılacak cihazları seçin ve **devam**' ı seçin.

1. Son olarak, **sağlama profili adı** bölümünde profil için bir ad seçin ve **Oluştur**' u seçin.

    ![Sağlama profili adını seçin](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Yeni sağlama profili oluşturulduğunda **İndir**' i seçin. Kaydedildiği konumu unutmayın.

1. Sağlama profilinin konumuna göz atın ve ardından çift tıklayarak Xcode geliştirme makinenize yüklemeyi seçin.

## <a name="create-a-notification-hub"></a>Bildirim hub’ı oluşturma

Bu bölümde,. p12 Push Sertifikası veya belirteç tabanlı kimlik doğrulaması kullanarak APNs ile kimlik doğrulaması oluşturacaksınız ve bir Bildirim Hub 'ı oluşturursunuz. Zaten oluşturduğunuz bir Bildirim Hub 'ı kullanmak istiyorsanız, 5. adıma geçebilirsiniz.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Bildirim Hub 'ınızı APNs bilgileriyle yapılandırma

**Bildirim hizmetleri** altında **Apple (APNs)** öğesini seçin ve ardından [Notification Hubs için sertifika oluşturma](#creating-a-certificate-for-notification-hubs) bölümünde seçtiğiniz yaklaşıma göre ilgili adımları izleyin.  

> [!NOTE]
> Uygulamanızı Uygulama Mağazası veya geçici dağıtım profiliyle derleyebilir, **uygulama modu** için **üretimi** kullanın. Bu, cihazınızın uygulamanızı mağazadan satın alan kullanıcılara anında iletme bildirimleri göndermesini sağlar.

### <a name="option-1-using-a-p12-push-certificate"></a>SEÇENEK 1:. p12 anında iletme sertifikası kullanma

1. **Sertifika**’yı seçin.

1. Dosya simgesini seçin.

1. Daha önce verdiğiniz. P12 dosyasını seçin ve sonra **Aç**' ı seçin.

1. Gerekirse, doğru parolayı belirtin.

1. **Korumalı alan** modunu seçin.

    ![Azure portal'da APNs sertifikasını yapılandırma](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. **Kaydet**’i seçin.

### <a name="option-2-using-token-based-authentication"></a>Seçenek 2: belirteç tabanlı kimlik doğrulaması kullanma

1. **Belirteç** seçin.
1. Daha önce aldığınız aşağıdaki değerleri girin:

    - **Anahtar KIMLIĞI**
    - **Paket Kimliği**
    - **Takım KIMLIĞI**
    - **Belirteç** 

1. **Korumalı alan** seçin
1. **Kaydet**’i seçin. 

Artık Bildirim Hub 'ınızı APNs ile yapılandırdınız. Ayrıca, uygulamanızı kaydetmek ve anında iletme bildirimleri göndermek için bağlantı dizelerine sahip olursunuz.