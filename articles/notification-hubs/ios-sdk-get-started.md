---
title: Azure Notification Hubs ve iOS SDK 'sını kullanarak iOS 'a anında iletme bildirimleri gönderme
description: Bu öğreticide, iOS cihazlarına anında iletme bildirimleri göndermek için Azure Notification Hubs ve Apple Anında Iletilen bildirim hizmetini nasıl kullanacağınızı öğreneceksiniz.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581749"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Öğretici: Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme

Bu öğreticide, Azure Notification Hubs ayarlama ve [Apple Anında Iletilen bildirim hizmeti (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)aracılığıyla bir iOS cihazına bildirim göndermek için kimlik bilgilerini yapılandırma gösterilmektedir. 

Bu öğreticiyi çalıştırmak, sonraki amaç C ve Swift iOS öğreticilerine yönelik bir önkoşuldur ve aşağıdaki adımları içerir:

- Sertifika imzalama istek dosyasını oluşturun.
- Uygulamanızı anında iletme bildirimleri için isteyin.
- Uygulama için bir sağlama profili oluşturun.
- Bildirim hub’ı oluşturma.
- Bildirim Hub 'ını APNS bilgileriyle yapılandırın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/free/).

Ayrıca şunlar gerekir:

- Etkin bir [Apple geliştirici](https://developer.apple.com/) hesabı.
- Anahtarınıza yüklenmiş geçerli bir geliştirici sertifikasıyla birlikte [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)çalıştıran bir Mac.
- İOS sürüm 10 veya üstünü çalıştıran bir iPhone veya iPad.
- Fiziksel cihazınız [Apple portalında](https://developer.apple.com/) kayıtlı ve sertifikanız ile ilişkilendirilmiştir.

Hizmeti bilmiyorsanız [Azure Notification Hubs genel bakış ' ı](notification-hubs-push-notification-overview.md) okuduğunuzdan emin olun.

> [!NOTE]
> Bildirim Hub 'ı yalnızca korumalı alan kimlik doğrulama modunu kullanacak şekilde yapılandırılacak. Bu kimlik doğrulama modunu üretim iş yükleri için kullanmamalısınız.

## <a name="generate-the-certificate-signing-request-file"></a>Sertifika imzalama istek dosyasını oluşturma

Apple Anında İletilen Bildirim Servisi (APNS), anında iletme bildirimlerinizi doğrulamak için sertifikaları kullanır. Bildirim gönderip almak için gereken bildirim sertifikasını oluşturacak bu talimatları uygulayın. Bu kavramlar hakkında daha fazla bilgi için resmi [Apple Anında İletilen Bildirim servisi](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) belgelerine bakın.

Apple 'ın imzalı bir anında iletme sertifikası oluşturmak için kullandığı sertifika Imzalama Isteği (CSR) dosyasını oluşturun:

1. Mac’inizde Anahtar Zinciri Erişimi aracını çalıştırın. Bu, **yardımcı programlar** klasöründen veya başlatma panelindeki **diğer** klasörden açılabilir.

2. **Anahtarlık erişimi** seçin, **sertifika Yardımcısı**' nı genişletin ve bir **sertifika yetkilisinden sertifika iste**' yi seçin.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Sertifika yetkilisi 'nden sertifika ISTEME menü seçeneğinden oluşan ekran görüntüsü.":::

   > [!NOTE]
   > Varsayılan olarak, Anahtarlık erişimi listedeki ilk öğeyi seçer. Bu, **Sertifikalar** **kategorisindeyseniz ve Apple Dünya genelinde geliştirici Ilişkileri sertifika yetkilisiyseniz** listedeki ilk öğe değilse bir sorun olabilir. CSR 'yi (sertifika Imzalama Isteği) oluşturmadan önce, anahtar olmayan bir öğeye sahip olduğunuzdan veya **Apple Dünya genelinde geliştirici Ilişkileri sertifika yetkilisi** anahtarının seçildiğinden emin olun.

3. **Kullanıcı e-posta adresinizi** seçin, **ortak ad** değerini girin, **diske kaydedilmiş**' ı belirttiğinizden emin olun ve ardından **devam**' ı seçin. **CA e-posta adresini** gerekli olmadığından boş bırakın.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Gerekli sertifika bilgileri":::

4. **Farklı kaydet**' de CSR dosyası için bir ad girin, **konum ' u** seçin ve ardından **Kaydet**' i seçin.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Dosya adı seçin":::

   Bu eylem, CSR dosyasını seçilen konuma kaydeder. Varsayılan konum **Desktop**' dır. Bu dosya için seçilen konumu unutmayın.

Ardından, uygulamanızı Apple 'a kaydedin, anında iletme bildirimlerini etkinleştirin ve bir anında iletme sertifikası oluşturmak için, içeri aktarılmış CSR 'yi karşıya yükleyin.

## <a name="register-your-app-for-push-notifications"></a>Anında iletme bildirimleri için uygulamanızı kaydetme

Bir iOS uygulamasına anında iletme bildirimleri göndermek için uygulamanızı Apple 'a kaydedin ve anında iletme bildirimleri için kaydolun.

1. Uygulamanızı henüz kaydetmediyseniz, Apple Geliştirici Merkezi ' nde [IOS sağlama portalı](https://go.microsoft.com/fwlink/p/?LinkId=272456) ' na gidin. Apple KIMLIĞINIZLE portalda oturum açın ve **tanımlayıcılar**' ı seçin. Sonra **+** Yeni bir uygulama kaydetmeyi seçin.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Uygulama kimlikleri sayfası":::

2. **Yeni bir tanımlayıcı kaydet** ekranında, **uygulama kimlikleri** radyo düğmesini seçin. Daha sonra **Devam** seçeneğini belirleyin.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Yeni KIMLIK kaydetme sayfası":::

3. Yeni uygulamanız için aşağıdaki üç değeri güncelleştirin ve ardından **devam**' ı seçin:

   - **Açıklama**: uygulamanız için açıklayıcı bir ad yazın.
   - **Paket kimliği**: [uygulama dağıtım kılavuzunda](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)belirtilen şekilde, **kuruluş tanımlayıcısı. ürün adı** form için bir paket kimliği girin. **Kuruluş tanımlayıcısı** ve **ürün adı** değerleri, Xcode projenizi oluştururken kullandığınız kuruluş tanımlayıcısı ve ürün adıyla eşleşmelidir. Aşağıdaki ekran görüntüsünde, **Notificationhub 'lar** değeri bir kuruluş tanımlayıcısı olarak kullanılır ve **getstarted** değeri ürün adı olarak kullanılır. **Paket tanımlayıcı** değerinin Xcode projenizdeki değerle eşleştiğinden emin olun, böylece Xcode doğru yayımlama profilini kullanır.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Uygulama KIMLIĞINI Kaydet":::

   - **Anında Iletme bildirimleri**: **yetenekler** bölümündeki **anında iletme bildirimleri** seçeneğini işaretleyin.

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Yeni uygulama KIMLIĞINI Kaydet":::

      Bu eylem, uygulama KIMLIĞINIZI ve bilgileri onaylamak için isteklerinizi oluşturur. **Devam**' ı seçin ve ardından yenı uygulama kimliğini onaylamak için **Kaydet** ' i seçin.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Yeni uygulama KIMLIĞINI Onayla":::

      **Kaydet**' i seçtikten sonra, yenı uygulama kimliğini **sertifikalar, tanımlayıcılar & profiller** sayfasında bir satır öğesi olarak görürsünüz.

4. **Sertifikalar, tanımlayıcılar & profiller** sayfasında, **tanımlayıcılar**' ın altında, az önce oluşturduğunuz uygulama kimliği satırı öğesini bulun ve ardından **uygulama kimliği yapılandırmasını düzenleme** ekranınızı göstermek için satırını seçin.

## <a name="create-a-certificate-for-notification-hubs"></a>Notification Hubs için bir sertifika oluşturun

> [!NOTE]
> İOS 13 ' ün piyasaya çıkmasıyla, belirteç tabanlı kimlik doğrulaması kullanarak yalnızca sessiz bildirimleri alabilirsiniz. APNS kimlik bilgileriniz için sertifika tabanlı kimlik doğrulaması kullanıyorsanız, belirteç tabanlı kimlik doğrulaması ile geçiş yapmanız gerekir.

Bildirim Hub 'ının **APNs** ile çalışmasını sağlamak için bir sertifika gerekir. Bu, iki şekilde yapılabilir:

- Notification Hubs doğrudan yüklenebilen bir **. p12** dosyası oluşturun.

- [Belirteç tabanlı kimlik doğrulaması](notification-hubs-push-notification-http2-token-authentication.md) (daha yeni yaklaşım) için kullanılabilecek bir **. P8** dosyası oluşturun.

İkinci seçenekte, [APNs Için belirteç tabanlı (http/2) kimlik doğrulaması](notification-hubs-push-notification-http2-token-authentication.md)bölümünde belgelendiği gibi, sertifikaların kullanılmasıyla karşılaştırıldığında birçok avantaj bulunur. Ancak, her iki yaklaşım için de adımlar sağlanır.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Seçenek 1: doğrudan Notification Hubs yüklenebilen bir. p12 Push Sertifikası oluşturun

1. Gözden geçirilen **anında Iletme bildirimleri** seçeneğine kaydırın ve ardından sertifikayı oluşturmak için **Yapılandır** ' ı seçin.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="Uygulama Kimliği":::

2. **Apple Anında Iletilen bildirim HIZMETI SSL sertifikaları** penceresi görüntülenir. **GELIŞTIRME SSL Sertifikası** bölümünde **sertifika oluştur** düğmesini seçin.

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Sertifika Oluştur":::

   **Yeni sertifika oluştur** ekranı görüntülenir.

   > [!NOTE]
   > Bu öğretici geliştirme sertifikası kullanır. Aynı işlem üretim sertifika kaydedildiğinde de kullanılır. Bildirimleri gönderirken aynı sertifika türünü kullandığınızdan emin olun.

3. **Dosya Seç**' i seçin, Ilk görevden CSR dosyasını kaydettiğiniz konuma gidin ve ardından yüklemek için sertifika adına çift tıklayın. Daha sonra **Devam** seçeneğini belirleyin.

4. Portal sertifikayı oluşturduktan sonra **İndir** düğmesini seçin. Sertifikayı kaydedin ve kaydedildiği konumu unutmayın.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Sertifikayı indir":::

   Sertifika indirilir ve **indirmeler** klasörünüze kaydedilir.

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Sertifika dosyasını bul":::

   Varsayılan olarak, indirilen geliştirme sertifikası **aps_development. cer** olarak adlandırılır.

5. İndirilen gönderim sertifikası **AP \_ geliştirme. cer**' ye çift tıklayın. Bu eylem yeni sertifikayı Anahtar Zinciri’ne aşağıdaki resimde gösterildiği gibi yüklenir:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Anahtarlık erişimi":::

   Sertifikanıza ilişkin ad farklı olabilir, ancak ad **Apple Development IOS anında Iletme Hizmetleri**'ne önek olarak eklenir.

6. Anahtar Zinciri Erişimi’nde **Sertifikalar** kategorisinde oluşturduğunuz yeni bildirim sertifikasına sağ tıklayın. **Dışarı aktar**' ı seçin, dosyayı adlandırın, **. p12** biçimini seçin ve ardından **Kaydet**' i seçin.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Sertifikayı dışarı aktarma":::

   Sertifikayı parolayla korumayı seçebilirsiniz, ancak bu isteğe bağlıdır. Parola oluşturmayı atlamak istiyorsanız **Tamam** ' ı tıklatın. Dışarı aktarılan .p12 sertifikanın dosya adını ve konumunu not edin. Bunlar, APNS ile kimlik doğrulamayı etkinleştirmek için kullanılır.

   > [!NOTE]
   > . P12 dosyanızın adı ve konumunuz, bu öğreticide görüntülenenden farklı olabilir.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>2. seçenek: belirteç tabanlı kimlik doğrulaması için kullanılabilecek bir. P8 sertifikası oluşturma

1. Aşağıdaki ayrıntıları unutmayın:

   - **Uygulama kimliği öneki** (Bu BIR **Takım kimliğidir**)
   - **Paket Kimliği**

2. **Sertifikalar, tanımlayıcılar & profillere** geri dönüp **anahtarlar**' a tıklayın. **APNs** için yapılandırılmış bir anahtarınız zaten varsa, oluşturulduktan hemen sonra indirdiğiniz. P8 sertifikasını yeniden kullanabilirsiniz. Bu durumda 3 ile 5 arasındaki adımları yoksayabilirsiniz.

3. **+** Yeni bir anahtar oluşturmak için düğmeye tıklayın (veya **anahtar oluştur** düğmesi).

4. Uygun bir **anahtar adı** değeri sağlayın, **Apple Anında iletilen BILDIRIM hizmeti (APNs)** seçeneğini işaretleyin ve ardından **devam**' a ve ardından sonraki ekranda **Kaydet** ' e tıklayın.

5. **İndir** ' e tıklayın ve ardından **. P8** dosyasını (önekli `AuthKey_` ) güvenli bir yerel dizine taşıyın ve **bitti**' ye tıklayın.

   > [!IMPORTANT]
   > . P8 dosyanızı güvenli bir yerde sakladığınızdan emin olun (ve bir yedek kaydedin). Anahtarınızı indirdikten sonra yeniden karşıdan yüklenemez; sunucu kopyası kaldırılır.

6. **Anahtarlar**' da, az önce oluşturduğunuz anahtara (veya bunun yerine kullanmayı seçtiyseniz var olan bir anahtara) tıklayın.

7. **Anahtar kimliği** değerini bir yere getirin.

8. . P8 sertifikanızı, [Visual Studio Code](https://code.visualstudio.com/)gibi istediğiniz uygun bir uygulamada açın, sonra anahtar değerini unutmayın. Bu, **-----Başlangıç özel anahtarı-----** ve **-----son özel anahtar-----** arasındaki değerdir.

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Bu, daha sonra Notification Hubs yapılandırmak için kullanılacak belirteç değeridir.

Bu adımların sonunda, [Bildirim Hub 'ıNıZı APNs bilgileriyle yapılandırma](#configure-the-notification-hub-with-apns-information)bölümünde daha sonra kullanmak için aşağıdaki bilgilere sahip olmanız gerekir:

- **Takım Kimliği** (bkz. 1. adım)
- **Paket kimliği** (bkz. 1. adım)
- **Anahtar kimliği** (bkz. Adım 7)
- **Belirteç değeri** (. P8 anahtar değeri, bkz. 8. adım)

## <a name="create-a-provisioning-profile"></a>Sağlama profili oluşturma

1. [IOS sağlama portalı](https://go.microsoft.com/fwlink/p/?LinkId=272456)' na dönün, **sertifikalar, tanımlayıcılar & profiller**' i seçin, sol menüden **profiller** ' i seçin ve ardından **+** Yeni bir profil oluşturmak için öğesini seçin. **Yeni bir sağlama profili kaydet** ekranı görüntülenir.

2. Sağlama profili türü olarak **geliştirme** bölümünde **iOS uygulama geliştirmeyi** seçin ve ardından **devam**' ı seçin.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Sağlama profili listesi":::

3. Ardından, **uygulama kimliği** açılan listesinden oluşturduğunuz uygulama kimliğini seçin ve ardından **devam**' ı seçin.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Uygulama KIMLIĞI seçin":::

4. **Sertifika seç** penceresinde, kod imzalama için kullandığınız geliştirme sertifikasını seçin ve **devam**' ı seçin. Bu sertifika, oluşturduğunuz gönderim sertifikası değil. Bir tane yoksa, onu oluşturmanız gerekir. Bir sertifika varsa, sonraki adıma atlayın. Yoksa bir geliştirme sertifikası oluşturmak için:

   1. **Kullanılabilir sertifika yoksa** **sertifika oluştur**' u seçin.
   2. **Yazılım** bölümünde **Apple geliştirme**' yi seçin. Daha sonra **Devam** seçeneğini belirleyin.
   3. **Yeni sertifika oluştur** ekranında **Dosya Seç**' i seçin.
   4. Daha önce oluşturduğunuz **sertifika Imzalama isteği** sertifikasına gidin, seçin ve sonra **Aç**' ı seçin.
   5. **Devam**’ı seçin.
   6. Geliştirme sertifikasını indirin ve kaydedildiği konumu unutmayın.

5. **Sertifikalar, tanımlayıcılar & profiller** sayfasına dönün, sol menüden **profiller** ' i seçin ve ardından **+** Yeni bir profil oluşturmak için öğesini seçin. **Yeni bir sağlama profili kaydet** ekranı görüntülenir.

6. **Sertifika seç** penceresinde, az önce oluşturduğunuz geliştirme sertifikasını seçin. Daha sonra **Devam** seçeneğini belirleyin.

7. Ardından, test için kullanılacak cihazları seçin ve **devam**' ı seçin.

8. Son olarak, **sağlama profili adı** bölümünde profil için bir ad seçin ve ardından **Oluştur**' u seçin.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Sağlama profili adını seçin":::

9. Yeni sağlama profili oluşturulduğunda **İndir**' i seçin. Kaydedildiği konumu unutmayın.

10. Sağlama profilinin konumuna göz atın ve ardından çift tıklayarak Xcode geliştirme makinenize yüklemeyi seçin.

## <a name="create-a-notification-hub"></a>Bildirim hub’ı oluşturma

Bu bölümde,. p12 Push Sertifikası veya belirteç tabanlı kimlik doğrulaması kullanarak APNS ile kimlik doğrulaması oluşturacaksınız ve bir Bildirim Hub 'ı oluşturursunuz. Zaten oluşturduğunuz bir Bildirim Hub 'ı kullanmak istiyorsanız, 5. adıma geçebilirsiniz.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. Sol taraftaki menüden **tüm hizmetler** ' i seçin ve ardından **Mobil** bölümünde **Notification Hubs** ' yi seçin. Hizmet adının yanındaki yıldız simgesini seçerek hizmeti Sol menüdeki **Sık Kullanılanlar** bölümüne ekleyin. **Sık kullanılanlara** **Notification Hubs** ekledikten sonra seçin.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure portalı":::

3. **Notification Hubs** sayfasında, araç çubuğunda **Ekle** ' yi seçin.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Araç çubuğu düğmesi Ekle":::

4. **Notification Hubs** sayfasında, aşağıdaki adımları uygulayın:

   1. **Bildirim Hub 'ına** bir ad girin.
   2. **Yeni ad alanı oluştur**' a bir ad girin. Bir ad alanı bir veya daha fazla Bildirim Hub 'ı içerir.
   3. **Konum** açılır listesinden bir değer seçin. Bu değer, Bildirim Hub 'ını oluşturmak istediğiniz konumu belirtir.
   4. **Kaynak grubunda** var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturun.
   5. **Oluştur**’u seçin.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Özellikleri ayarlama":::

5. **Bildirimleri** (zil simgesi) seçin ve ardından **Kaynağa Git**' i seçin. Ayrıca **Notification Hubs** sayfasında listeyi yenileyebilir ve hub 'ınızı seçebilirsiniz.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Portal bildirimleri":::

6. Listeden **Erişim İlkeleri**'ni seçin. İki bağlantı dizesinin sizin için kullanılabilir olduğunu unutmayın. Anında iletme bildirimlerini işlemek için onlara daha sonra ihtiyacınız olacak.

   > [!IMPORTANT]
   > Uygulamanızda **Defaultfullsharedaccesssignature** ilkesini kullanmayın. Bu, yalnızca arka uçta kullanılmak üzere tasarlanmıştır.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Bağlantı dizeleri":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Bildirim Hub 'ını APNS bilgileriyle yapılandırma

**Bildirim hizmetleri** altında **Apple (APNs)** öğesini seçin ve ardından [Notification Hubs için sertifika oluşturma](#create-a-certificate-for-notification-hubs) bölümünde daha önce seçtiğiniz yaklaşıma göre uygun adımları izleyin.

> [!NOTE]
> Yalnızca uygulamanızı mağazadan satın alan kullanıcılara anında iletme bildirimleri göndermek istiyorsanız **uygulama modu** için **üretimi** kullanın.

### <a name="option-1-use-a-p12-push-certificate"></a>Seçenek 1: bir. p12 anında iletme sertifikası kullanın

1. **Sertifika**’yı seçin.

2. Dosya simgesini seçin.

3. Daha önce verdiğiniz. P12 dosyasını seçin ve sonra **Aç**' ı seçin.

4. Gerekirse, doğru parolayı belirtin.

5. **Korumalı alan** modunu seçin.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Yapılandır":::

6. **Kaydet**’i seçin.

### <a name="option-2-use-token-based-authentication"></a>Seçenek 2: belirteç tabanlı kimlik doğrulaması kullanma

1. **Belirteç** seçin.

2. Daha önce aldığınız aşağıdaki değerleri girin:

   - **Anahtar KIMLIĞI**
   - **Paket Kimliği**
   - **Takım KIMLIĞI**
   - **Belirteç**

3. **Korumalı alan** seçin

4. **Kaydet**’i seçin.

Artık Bildirim Hub 'ınızı APNS ile yapılandırdınız. Ayrıca, uygulamanızı kaydettirmek ve anında iletme bildirimleri göndermek için gereken bağlantı dizelerine sahip olursunuz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure 'da bir Bildirim Hub 'ı oluşturup yapılandırdığınıza ve bu uygulamayı Apple Anında İletilen Bildirim Servisi (APNS) aracılığıyla uygulamanıza gönderilmesine izin verecek şekilde yapılandırmış olursunuz. Ardından, örnek bir iOS uygulaması oluşturacağız ve Azure Notifications hub SDK 'sını, Azure portal aracılığıyla gönderilen anında iletme bildirimleri alabilmesi için tümleştireceğiz. Tercih ettiğiniz dile göre aşağıdaki öğreticiye ilerleyin:

- [Öğretici: Azure Notification Hubs kullanarak iOS uygulamalarına anında iletme bildirimleri gönderme](ios-sdk-current.md)
