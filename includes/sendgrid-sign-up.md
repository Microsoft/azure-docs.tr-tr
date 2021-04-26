---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99214640"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>SendGrid hesabı açmak için
1. [Azure portalında][Azure portal] oturum açın.
2. Azure portal menüsünde veya giriş sayfasında, **kaynak oluştur**' u seçin.

    ![Kaynak oluştur seçeneği işaretli Azure portal menüsünün ekran görüntüsü.][command-bar-new]
3. **SendGrid**'i arayın ve seçin.

    ![Arama sonuçlarında "SendGr" ' i ve arama sonuçlarında seçilen SendGrid ' i gösteren Azure portal marketi ekranının ekran görüntüsü.][sendgrid-store]
4. Kayıt formunu doldurun ve **Oluştur**'u seçin.

    ![Ad, parola, abonelik ve kaynak grubu alanları doldurulmuş yeni bir SendGrid hesabı oluştur iletişim kutusunun ekran görüntüsü.][sendgrid-create]
5. SendGrid hizmetinizi Azure ayarlarınızda tanımlamak için bir **Ad** girin. Adın 1-100 karakter arasında olması ve yalnızca alfasayısal karakterler, çizgi, nokta ve alt çizgi içermesi gerekir. Adın abone olunan Azure Mağazası Öğeleri arasında benzersiz olması gerekir.
6. **Parolanızı** girin ve onaylayın.
7. **Aboneliğinizi** seçin.
8. Yeni bir **Kaynak grubu** oluşturun veya var olanlardan birini kullanın.
9. **Fiyatlandırma katmanı** bölümünde kaydolmak istediğiniz SendGrid planını seçin.

    ![Fiyatlandırma katmanınızı seçme bölümünün açık olduğu yeni bir SendGrid hesabı oluştur iletişim kutusunun ekran görüntüsü][sendgrid-pricing]
10. Varsa **Promosyon Kodu** girin.
11. **Iletişim bilgilerinizi** girin.
12. **Yasal koşulları** gözden geçirin ve kabul edin.
13. Satın alımınızın onaylandıktan sonra **dağıtım başarılı** bir açılır pencere görürsünüz ve hesabınızı listede görürsünüz.

    ![Yeni ContosoSendGrid hesabını gösteren SendGrid hesapları sayfasının ekran görüntüsü.][all-resources]

    Satın alma işlemini tamamladıktan ve e-posta doğrulama işlemini başlatmak için **Yönet** düğmesine tıkladıktan sonra SendGrid'den hesabınızı doğrulamanızı isteyen bir e-posta alacaksınız. Bu e-postayı almazsanız veya hesabınızı doğrulamakta sorun yaşıyorsanız lütfen SSS sitemizi inceleyin.

    ![Yönet düğmesinin vurgulandığı ContosoSendGrid hesabı sayfasının ekran görüntüsü.][manage]

    **Hesabınızı doğrulayana kadar günde en fazla 100 e-posta gönderebilirsiniz.**

    Abonelik planınızı değiştirmek veya SendGrid iletişim ayarlarını görüntülemek için SendGrid hizmetinizin adına tıklayarak SendGrid Marketplace panosunu açın.

    ![ContosoSendGrid hesabının Ayarlar sayfasının ContosoSendGrid hesabı sayfasından tüm ayarlar seçilerek açıldığını gösteren ekran görüntüsü.][settings]

    SendGrid kullanarak bir e-posta göndermek için API Anahtarınızı girmeniz gerekir.

### <a name="to-find-your-sendgrid-api-key"></a>SendGrid API Anahtarınızı bulmak için
1. **Yönet**'e tıklayın.

    ![Yönet düğmesinin vurgulandığı ContosoSendGrid hesabı sayfasının ekran görüntüsü.][manage]
2. SendGrid panonuzda **Ayarlar**'ı ve ardından sol taraftaki menüden **API Anahtarları**'nı seçin.

    ![Açık ayarlar açılan ve API anahtarları seçili olan SendGrid panosunun ekran görüntüsü.][api-keys]

3. **API anahtarı oluştur**' a tıklayın.

    ![API anahtarı oluştur düğmesi seçili olan API anahtarları ekranının ekran görüntüsü.][general-api-key]
4. En azından **Bu anahtarın adı** alanını doldurun, **Posta Gönderimi** için tam erişim verin ve **Kaydet**'i seçin.

    ![E-posta gönderme kümesi ile tam erişime sahip yeni genel API anahtarı ekranının ekran görüntüsü, zamanlanmış göndermeleri, erişim yok olarak ayarlanır ve Kaydet düğmesi vurgulanır.][access]
5. API anahtarınız bu adımda bir kez görüntülenir. Güvenli bir yerde saklamayı unutmayın.

### <a name="to-find-your-sendgrid-credentials"></a>SendGrid kimlik bilgilerinizi bulmak için
1. **Kullanıcı adınızı** bulmak için anahtar simgesine tıklayın.

    ![Anahtar simgesi vurgulanmış şekilde ContosoSendGrid hesabı sayfasının ekran görüntüsü.][key]
2. Parola, kurulum sırasında seçtiğiniz paroladır. **Parolayı değiştir** veya **Parolayı sıfırla**'yı seçerek değişiklik yapabilirsiniz.

E-posta teslim ayarlarınızı yönetmek için **Yönet** düğmesine tıklayın. Bu, SendGrid panonuza yönlendirecektir.

![Yönet düğmesinin vurgulandığı ContosoSendGrid hesabı sayfasının ekran görüntüsü.][manage]

SendGrid aracılığıyla e-posta gönderme hakkında daha fazla bilgi için [e-posta API 'Sine genel bakış ' a][Email API Overview]

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
