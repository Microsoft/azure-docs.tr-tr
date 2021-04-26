---
title: Azure Active Directory 'de parola tabanlı çoklu oturum açma sorunlarını giderme
description: Parola tabanlı çoklu oturum açma için yapılandırılmış bir Azure AD uygulamasıyla ilgili sorunları giderin.
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 2d9bcbdcb292f33d4b1f9dd1d0f6779d8d6cdd49
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376486"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Azure AD’de parola tabanlı çoklu oturum açma sorunlarını giderme

Uygulamamda parola tabanlı çoklu oturum açma (SSO) kullanmak için tarayıcı uzantısının yüklenmesi gerekir. Parola tabanlı SSO için yapılandırılmış bir uygulama seçtiğinizde uzantı otomatik olarak indirilir. Son Kullanıcı perspektifinden uygulamalarımı kullanma hakkında bilgi edinmek için bkz. [Apps Portal yardımı](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Uygulamalarım tarayıcı uzantısı yüklü değil
Tarayıcı uzantısının yüklü olduğundan emin olun. Daha fazla bilgi edinmek için bkz. [Azure Active Directory Apps dağıtımıyla plan oluşturma](my-apps-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Çoklu oturum açma yapılandırılmadı
Parola tabanlı çoklu oturum açma 'nın yapılandırıldığından emin olun. Daha fazla bilgi için bkz. [parola tabanlı çoklu oturum açmayı yapılandırma](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Atanmayan kullanıcılar
Kullanıcının uygulamaya atandığından emin olun. Daha fazla bilgi için bkz. bir [uygulamaya Kullanıcı veya Grup atama](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Kimlik bilgileri dolduruluyor ama uzantı bunları göndermiyor

Bu sorun genellikle uygulama satıcısı oturum açma sayfasını son zamanlarda bir alan eklemek, Kullanıcı adı ve parola alanlarını algılamak için kullanılan tanımlayıcıyı değiştirmek veya oturum açma deneyiminin uygulama için nasıl çalıştığını değiştirmek üzere değiştirdiyse meydana gelir. Neyse ki, birçok örnekte Microsoft bu sorunları hızlı bir şekilde çözmek için uygulama satıcılarıyla birlikte çalışabilir.

Microsoft, tümleştirmelerin ne zaman otomatik olarak algılanması için teknolojiler sağlarken, sorunları hemen bulmak mümkün olmayabilir veya sorunların düzeltilmesi biraz zaman alabilir. Bu tümleştirmelerin bir örneği doğru şekilde çalışmazsa, mümkün olduğunca çabuk düzeltibilmeleri için bir destek talebi açın.

**Bu uygulamanın satıcısıyla iletişim kursunsam, Microsoft 'un** kendi uygulamasını Azure Active Directory ile yerel olarak tümleştirmeleri için onlarla birlikte çalışmasına yol açabilir. Satıcıyı başlamak için [Azure Active Directory Uygulama galerisinde uygulamanızı listeye](../develop/v2-howto-app-gallery-listing.md) gönderebilirsiniz.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Kimlik bilgileri dolduruluyor ve gönderiliyor ama sayfa kimlik bilgilerinin yanlış olduğunu gösteriyor

Bu sorunu çözmek için öncelikle şunları deneyin:

- Kullanıcının **uygulama Web sitesinde, doğrudan** bunlar için depolanan kimlik bilgileriyle oturum açmaya çalışın.

  * Oturum açma çalışır durumda olduğunda, kullanıcının [uygulamalarımın](https://myapps.microsoft.com/) **uygulamalar** bölümündeki **uygulama kutucuğunda** bulunan **kimlik bilgilerini güncelleştir** düğmesine tıklamasını, bilinen son çalışan Kullanıcı adı ve parolasıyla güncelleştirmek için tıklatın.

  * Ya da bu kullanıcı için kimlik bilgilerini başka bir yönetici atadıysanız, uygulamanın **kullanıcılar & gruplar** sekmesine giderek, atamayı seçerek ve **kimlik bilgilerini güncelleştir** düğmesine tıklayarak Kullanıcı veya grubun uygulama atamasını bulun.

- Kullanıcı kendi kimlik bilgilerini atamışsa, kullanıcının, **parolasının uygulamanın süresi dolmadığından emin** olup olmadığını denetleyin ve bu durumda, uygulamada doğrudan oturum açarak, **süresi doldu parolasını güncelleştirin** .

  * Parola uygulamada güncelleştirildikten sonra, Kullanıcı, en son bilinen çalışma Kullanıcı adı ve parolasıyla güncelleştirmek için [uygulamalarımın](https://myapps.microsoft.com/) **uygulamalar** bölümündeki **uygulama kutucuğunda** bulunan **kimlik bilgilerini güncelleştir** düğmesine tıklamasını isteyin.

  * Ya da bu kullanıcı için kimlik bilgilerini başka bir yönetici atadıysanız, uygulamanın **kullanıcılar & gruplar** sekmesine giderek, atamayı seçerek ve **kimlik bilgilerini güncelleştir** düğmesine tıklayarak Kullanıcı veya grubun uygulama atamasını bulun.

- My Apps Browser uzantısının, kullanıcının tarayıcısında çalıştığından ve etkinleştirildiğinden emin olun.

- Kullanıcılarınızın uygulamalarımın uygulamada oturum açmaya çalışmalarından emin olun, ancak bu, **InPrivate veya özel moddadır**. Uygulamalarım uzantısı bu modlarda desteklenmez.

Önceki önerilerin çalışmamasına karşı, uygulamanın Azure AD ile tümleştirmesini geçici olarak bozan uygulama tarafında bir değişikliğin oluşması durumunda olabilir. Örneğin, bu durum, uygulama satıcısı kendi sayfasında bir betiği ortaya çıkardığı zaman gerçekleşebilir ve bu, kendi otomatik entegrasyonumuz gibi otomatik tümleştirmeye neden olur. Neyse ki, birçok örnekte Microsoft bu sorunları hızlı bir şekilde çözmek için uygulama satıcılarıyla birlikte çalışabilir.

Microsoft, uygulama tümleştirmeleri sırasında otomatik olarak algılamaya yönelik teknolojiler sağlarken, sorunları hemen bulmak mümkün olmayabilir veya sorunların düzeltilmesi biraz zaman alabilir. Bir tümleştirme düzgün çalışmazsa, mümkün olduğunca hızlı bir şekilde düzeltilmesi için bir destek talebi açabilirsiniz. 

Buna ek olarak, **Bu uygulamanın satıcısıyla iletişim** kursunuz, uygulamaları Azure Active Directory ile yerel olarak tümleştirmek üzere bunlarla çalışabilmemiz için sizinle birlikte çalışmamız **gerekir.** Satıcıyı başlamak için [Azure Active Directory Uygulama galerisinde uygulamanızı listeye](../develop/v2-howto-app-gallery-listing.md) gönderebilirsiniz.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Uygulamanın oturum açma sayfasının yakın zamanda değişip değişmediğini ve ek bir alan gerektirip gerektirmediğini denetleyin

Uygulamanın oturum açma sayfası büyük ölçüde değiştirilmişse, bazı durumlarda tümleştirmelerimiz kesintiye neden olur. Bunun bir örneği, bir uygulama satıcısının deneyimler için bir oturum açma alanı, CAPTCHA veya Multi-Factor Authentication eklediğinde oluşur. Neyse ki, birçok örnekte Microsoft bu sorunları hızlı bir şekilde çözmek için uygulama satıcılarıyla birlikte çalışabilir.

Microsoft, uygulama tümleştirmeleri sırasında otomatik olarak algılamaya yönelik teknolojiler sağlarken, sorunları hemen bulmak mümkün olmayabilir veya sorunların düzeltilmesi biraz zaman alabilir. Bir tümleştirme düzgün çalışmazsa, mümkün olduğunca hızlı bir şekilde düzeltilmesi için bir destek talebi açabilirsiniz. 

Buna ek olarak, **Bu uygulamanın satıcısıyla iletişim** kursunuz, uygulamaları Azure Active Directory ile yerel olarak tümleştirmek üzere bunlarla çalışabilmemiz için sizinle birlikte çalışmamız **gerekir.** Satıcıyı başlamak için [Azure Active Directory Uygulama galerisinde uygulamanızı listeye](../develop/v2-howto-app-gallery-listing.md) gönderebilirsiniz.

## <a name="capture-sign-in-fields-for-an-app"></a>Uygulama için oturum açma alanlarını yakalama

Oturum açma alanı yakalama yalnızca HTML özellikli oturum açma sayfaları için desteklenir. Standart olmayan oturum açma sayfalarında, Adobe Flash veya HTML olmayan diğer teknolojileri kullananlar gibi desteklenmez.

Özel uygulamalarınız için oturum açma alanlarını yakalamanın iki yolu vardır:

- **Otomatik oturum açma alanı yakalama** , Kullanıcı adı ve parola alanları için *Iyi bilinen DIV kimliklerini kullanıyorsa* , en fazla HTML etkin oturum açma sayfası ile iyi çalışacaktır. Sayfadaki HTML, belirli ölçütlerle eşleşen DIV kimliklerini bulmak için korlanır. Bu meta veriler, daha sonra uygulamaya yeniden oynatılabilecek şekilde kaydedilir.

- Uygulama satıcısı *oturum açma giriş alanlarını etiketleyip*, **el ile oturum açma alanı yakalama** kullanılır. Satıcı *otomatik olarak algılanamayan birden çok alan işliyorsa* el ile yakalama da kullanılır. Azure Active Directory (Azure AD), oturum açma sayfasında, bu alanların sayfada olduğu yerde olduğunu söylemiş kadar çok sayıda alan için veri depolayabilirler.

Genel olarak, otomatik oturum açma alanı yakalama işe yaramazsa, el ile seçeneğini deneyin.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Bir uygulama için oturum açma alanlarını otomatik olarak yakala

Parola tabanlı SSO 'yu otomatik oturum açma alanı yakalamayı kullanarak yapılandırmak için şu adımları izleyin:
1. [Azure portalını](https://portal.azure.com/) açın. Genel yönetici veya ortak yönetici olarak oturum açın.
2. Sol taraftaki Gezinti bölmesinde, **tüm hizmetler** ' i seçerek Azure ad uzantısını açın.
3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.
4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.
5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.
   > [!NOTE]
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar** listesinin en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.
6. SSO için yapılandırmak istediğiniz uygulamayı seçin.
7. Uygulama yüklendikten sonra, sol taraftaki Gezinti bölmesinde **Çoklu oturum açma** seçeneğini belirleyin.
8. **Parola tabanlı oturum açma** modunu seçin.
9. Kullanıcıların oturum açmak için Kullanıcı adını ve parolasını girmesi gereken sayfanın URL 'SI olan **oturum açma URL**'sini girin. *SAĞLADıĞıNıZ URL 'nin sayfada oturum açma alanlarının göründüğünden emin olun*.
10. **Kaydet**’i seçin.
    Sayfa, Kullanıcı adı ve parola giriş kutuları için otomatik olarak indirgendi. Artık Apps tarayıcı uzantısını kullanarak bu uygulamaya parolaları güvenli bir şekilde aktarmak için Azure AD 'yi kullanabilirsiniz.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Bir uygulama için oturum açma alanlarını el ile yakalama

Oturum açma alanlarını el ile yakalamak için uygulamalarım tarayıcı uzantınızın yüklü olması gerekir. Ayrıca, tarayıcınız *InPrivate*, *ınbilito* veya *özel* modda çalıştırılabilir.

El ile oturum açma alanı yakalamayı kullanarak bir uygulama için parola tabanlı SSO yapılandırmak için aşağıdaki adımları izleyin:
1. [Azure portalını](https://portal.azure.com/) açın. Genel yönetici veya ortak yönetici olarak oturum açın.
2. Sol taraftaki Gezinti bölmesinde, **tüm hizmetler** ' i seçerek Azure ad uzantısını açın.
3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.
4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.
5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.
   > [!NOTE] 
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar** listesinin en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.
6. SSO için yapılandırmak istediğiniz uygulamayı seçin.
7. Uygulama yüklendikten sonra, sol taraftaki Gezinti bölmesinde **Çoklu oturum açma** seçeneğini belirleyin.
8. **Parola tabanlı oturum açma** modunu seçin.
9. Kullanıcıların oturum açmak için Kullanıcı adını ve parolasını girmesi gereken sayfa olan **oturum açma URL 'sini** girin. *SAĞLADıĞıNıZ URL 'nin sayfada oturum açma alanlarının göründüğünden emin olun*.
10. ***&lt; Appname &gt;* parolasını tek oturum açma ayarlarını yapılandır '** ı seçin.
11. **Oturum açma alanlarını el ile Algıla**' yı seçin.
14. **Tamam**'ı seçin.
15. **Kaydet**’i seçin.
16. Uygulamalarımı kullanmak için yönergeleri izleyin.


## <a name="troubleshoot-problems"></a>Sorunları giderme

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>"Bu URL 'de oturum açma alanları bulamadık" hatası alıyorum

Bu hata iletisini, oturum açma alanlarının otomatik algılanması başarısız olduğunda alırsınız. Sorunu çözmek için el ile oturum açma alanı algılamayı deneyin. Bu makalenin [bir uygulaması için, oturum açma alanlarını el ile yakala](#manually-capture-sign-in-fields-for-an-app) bölümüne bakın.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"Çoklu oturum açma yapılandırması kaydedilemedi" hatası alıyorum

Nadiren, SSO yapılandırmasının güncelleştirilmesi başarısız olur. Bu sorunu çözmek için yapılandırmayı tekrar kaydetmeyi deneyin.

Hatayı almaya devam ederseniz bir destek talebi açın. Bu makalenin yardım bölümüne ulaşmak için [Portal bildirim ayrıntılarına bakın](#view-portal-notification-details) ve [bildirim ayrıntılarını bir destek mühendisine gönderin](#send-notification-details-to-a-support-engineer-to-get-help) .

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Uygulamam için oturum açma alanlarını el ile algılayamıyorum

El ile algılama çalışmadığı zaman aşağıdaki davranışları gözlemleyebilirsiniz:
- El ile yakalama işlemi çalışıyor görünüyor, ancak yakalanan alanlar doğru değil.
- Yakalama işlemi çalıştırıldığında doğru alanlar vurgulanmaz.
- Yakalama işlemi sizi uygulamanın oturum açma sayfasına beklenen şekilde götürür, ancak hiçbir şey olmaz.
- El ile yakalama çalışır, ancak kullanıcılar uygulamalarım ' dan uygulamaya gitiyorsa SSO gerçekleşmez.

Bu sorunlardan herhangi biriyle karşılaşırsanız şunları yapın:
- Uygulamalarım tarayıcı uzantısının en son sürümünün *yüklü ve etkin* olduğundan emin olun.
- Yakalama işlemi sırasında tarayıcınızın, *InPrivate* veya *özel* *modda olmadığından emin* olun. Uygulamalarım uzantısı bu modlarda desteklenmez.
- Kullanıcılarınızın uygulamalarımın uygulamada oturum açmaya çalışmalarından *,* *InPrivate* veya *özel moddayken* emin olun.
- El ile yakalama işlemini yeniden deneyin. Kırmızı işaretçilerin doğru alanların üzerinde olduğundan emin olun.
- El ile yakalama işlemi yanıt vermeyi durdurmuş gibi görünüyorsa veya oturum açma sayfası yanıt vermezse, el ile yakalama işlemini yeniden deneyin. Ancak, bu kez işlemi tamamladıktan sonra gözatıcının geliştirici konsolunu açmak için F12 tuşuna basın. **Konsol** sekmesini seçin. **Window. Location = "yazın ve *&lt; &gt; Uygulamayı yapılandırırken belirttiğiniz oturum açma URL 'si*"** yazın ve ENTER tuşuna basın. Bu, yakalama işlemini sonlandıran ve yakalanan alanları depolayan bir sayfa yeniden yönlendirmeyi zorlar.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Parola tabanlı SSO uygulamama başka bir Kullanıcı ekleyemiyorum

Parola tabanlı SSO uygulamasının 48 Kullanıcı sınırlaması vardır. Bu nedenle, uygulama başına Kullanıcı adı/parola çiftleri için 48 anahtar sınırlaması vardır.
Ek kullanıcılar eklemek istiyorsanız şunlardan birini yapabilirsiniz:
-   Uygulamanın başka bir örneğini ekleyin
-   Önce uygulamayı artık kullanmayan kullanıcıları kaldırın

## <a name="request-support"></a>Destek iste 
SSO 'yu ayarlarken ve kullanıcıları atarken bir hata mesajı alırsanız, bir destek bileti açın. Mümkün olduğunca aşağıdaki bilgilerin çoğunu ekleyin:

-   Bağıntı hata KIMLIĞI
-   UPN (Kullanıcı e-posta adresi)
-   Değerine
-   Tarayıcı türü
-   Hatanın gerçekleştiği saat dilimi ve saat/saat dilimi
-   Fiddler izlemeleri

### <a name="view-portal-notification-details"></a>Portal bildirim ayrıntılarını görüntüleme

Herhangi bir portal bildiriminin ayrıntılarını görmek için şu adımları izleyin:
1. Azure portal sağ üst köşesindeki **Bildirimler** simgesini (zil) seçin.
2. Bir *hata* durumu gösteren herhangi bir bildirim seçin. (Kırmızı bir "!" vardır.)
   > [!NOTE]
   > *Başarılı* veya *sürüyor* durumunda olan bildirimleri seçemezsiniz.
3. **Bildirim ayrıntıları** bölmesi açılır. Sorun hakkında bilgi edinmek için bilgileri okuyun.
5. Hala yardıma ihtiyacınız varsa, bilgileri bir destek mühendisiyle veya ürün grubuyla paylaşabilirsiniz. Bildirim ayrıntılarını paylaşmak üzere kopyalamak için **kopyalama hatası** kutusunun sağ tarafındaki **Kopyala** simgesini seçin.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Yardım almak için bir destek mühendisine bildirim ayrıntıları gönderin

Bu bölümde listelenen *Tüm* ayrıntıları, hızlı bir şekilde yardımcı olmaları için destek ile paylaşmanız önemlidir. Kaydetmek için, bir ekran görüntüsü alabilir veya **kopyalama hatası**' nı seçebilirsiniz.

Aşağıdaki bilgiler, her bildirim öğesinin ne anlama geldiğini açıklar ve örnekler sağlar.

#### <a name="essential-notification-items"></a>Önemli bildirim öğeleri

- **Başlık**: bildirimin açıklayıcı başlığı.

   Örnek: *uygulama proxy 'si ayarları*

- **Açıklama**: işlemin sonucu olarak ne oldu.

   Örnek: *girilen Iç URL zaten başka bir uygulama tarafından kullanılıyor.*

- **BILDIRIM kimliği**: BILDIRIMIN benzersiz kimliği.

    Örnek: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Istemci Istek kimliği**: tarayıcınızın yaptığı özel istek kimliği.

    Örnek: *302fd775-3329-4670-a9f3-kir, 37004f0bc*

- **Zaman DAMGASı UTC**: bildirimin gerçekleştiği zaman DAMGASı (UTC).

    Örnek: *2017-03-23T19:50:43.7583681 z*

- **Iç Işlem kimliği**: sistemlerimizde hatayı aramak için kullanılan iç kimlik.

    Örnek: **71a2f329-ca29-402f-AA72-bc00a7aca603**

- **UPN**: işlemi çalıştıran kullanıcı.

    Örnek: *tperkins \@ f128.info*

- **KIRACı kimliği**: işlemi çalıştıran kullanıcının üyesi olduğu kiracının benzersiz kimliği.

    Örnek: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Kullanıcı nesne kimliği**: işlemi çalıştıran kullanıcının benzersiz kimliği.

    Örnek: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Ayrıntılı bildirim öğeleri

- **Görünen ad**: (boş olabilir) hata için daha ayrıntılı bir görünen ad.

    Örnek: *uygulama proxy 'si ayarları*

- **Durum**: bildirimin özel durumu.

    Örnek: *başarısız oldu*

- **Nesne kimliği**: (boş olabilir) işlemin ÇALıŞTıRıLDıĞı nesne kimliği.

   Örnek: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Ayrıntılar**: işlemin sonucu olarak neyin ne olduğuna ilişkin ayrıntılı açıklama.

    Örnek: *' ' iç URL 'si <https://bing.com/> zaten kullanımda olduğundan geçersizdir.*

- **Kopyalama hatası**: destek konusunda yardım almak üzere bildirim ayrıntılarını kopyalamak için **kopyalama hatası** metin kutusunun sağ tarafındaki **Kopyala simgesini** seçmenizi sağlar.

    Örneğinde   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Sonraki adımlar
* [Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)
* [Uygulamalarım dağıtımı planlama](my-apps-deployment-plan.md)
