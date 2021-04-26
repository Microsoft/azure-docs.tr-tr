---
title: Sorular & Microsoft Authenticator uygulama hakkında yanıtlar-Azure AD
description: Microsoft kimlik doğrulama uygulaması ve iki öğeli doğrulama hakkında sık sorulan sorular ve yanıtlar (SSS).
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 8ddc02075d2cdeba4a4fa83a2475ec20c3f18d6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103418088"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulaması hakkında sık sorulan sorular (SSS)

Bu makalede Microsoft Authenticator uygulaması hakkında sık sorulan sorular yanıtlanmaktadır. Sorunuz için bir yanıt görmüyorsanız [Microsoft Authenticator uygulama forumuna](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)gidin.

Microsoft Authenticator uygulama Azure Authenticator uygulamasını değiştirdi ve Azure AD Multi-Factor Authentication kullandığınızda önerilen uygulamadır. Microsoft Authenticator uygulaması [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) ve [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)için kullanılabilir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="registering-a-device"></a>Cihaz kaydetme

**S**: şirket veya hizmet cihazımın erişimine izin vermek için bir cihaz kabul etmiş kaydediyor mu?

Y **: bir** cihazı kaydetmek, cihazınızın kuruluşunuzun hizmetlerine erişmesini sağlar ve kuruluşunuzun cihazınıza erişmesine izin vermez.

### <a name="error-adding-account"></a>Hesap eklenirken hata oluştu

**S**: hesabımı eklemeye çalıştığımda, "eklemeye çalıştığınız hesap şu anda geçerli değil ' i belirten bir hata mesajı alıyorum. Bu sorunu (benzersizlik doğrulaması) onarmak için yöneticinize başvurun. " Ne yapmalıyım?

Y: yöneticinizle iletişime geçin **ve bir benzersizlik** doğrulama sorunu nedeniyle hesabınızı kimlik doğrulayıcısına eklememenizi engellemiş olursunuz. Yöneticinizin kuruluşunuzda bakabilmesi için oturum açma kullanıcı adınızı sağlamanız gerekir.

### <a name="legacy-apns-support-deprecated"></a>Eski APNs desteği kullanım dışı

**S**: Apple Anında İletilen Bildirim hizmeti için eski ikili arabirim, Kasım 2020 ' de kullanımdan kaldırılmakta olduğundan, oturum açmak için Microsoft Authenticator/telefon faktörünü kullanmaya nasıl devam edebilirim?

**A**: Apple, telefon faktörü tarafından kullanılanlar gibi iOS cihazları için ikili arabirimini kullanan anında iletme bildirimlerinin [kullanımdan kalkmayı duyurmuştur](https://developer.apple.com/news/?id=11042019a) . Anında iletme bildirimleri almaya devam etmek için kullanıcıların Authenticator uygulamasını uygulamanın en son sürümüne güncelleştirmelerini öneririz. Bu sırada, doğrulayıcı uygulamasındaki bildirimleri el ile denetleyerek geçici çözüm bulabilirsiniz.

### <a name="app-lock-feature"></a>Uygulama kilidi özelliği

**S**: uygulama kilidi nedir ve daha güvende tutmaya yardımcı olması için nasıl kullanabilirim?

**A**: uygulama kilidi, tek seferlik doğrulama kodlarınızın, uygulama bilgilerinizin ve uygulama ayarlarınızın daha güvenli kalmasına yardımcı olur. Uygulama Kilidi etkinleştirildiğinde, kimlik doğrulayıcı 'yı her açışınızda cihaz PIN 'inizi veya biyometri kullanarak kimlik doğrulaması yapmanız istenir. Uygulama kilidi Ayrıca, PIN 'inizi veya bir oturum açma bildirimini onayladığınızı istediğiniz zaman, bildirimleri onaylayabilecek tek bir tane olduğundan emin olmanıza yardımcı olur. Doğrulayıcı ayarları sayfasında uygulama kilidi 'ni açıp kapatabilirsiniz. Varsayılan olarak, cihazınızda bir PIN veya biyometri ayarlarken uygulama kilidi açıktır.<br><br>Ne yazık ki, uygulama kilidinin kimlik doğrulayıcısına erişimi durdurabileceğini garanti etmez. Bunun nedeni, cihaz kaydının, Android hesap ayarları veya Şirket Portalı uygulamasında olduğu gibi, kimlik doğrulayıcı dışında diğer konumlarda gerçekleşebilmesine yönelik olabilir.

### <a name="windows-mobile-retired"></a>Windows Mobile kullanımdan kaldırıldı

**S**: Windows mobil cihazım var ve windows mobile Microsoft Authenticator kullanım dışı bırakıldı. Uygulamayı kullanarak kimlik doğrulamaya devam edebilir miyim?

Y **: Windows** Mobile Microsoft Authenticator kullanan tüm kimlik doğrulamaları 15 Temmuz 2020 ' den sonra kullanımdan kaldırılacaktır. Hesaplarınızı kilitlemeyi önlemek için alternatif bir kimlik doğrulama yöntemi kullanmanızı önemle tavsiye ederiz.<br>Kurumsal kullanıcılar için alternatif seçenekler şunlardır:<br><ul><li>[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) veya [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)için Microsoft Authenticator ayarlama.</li><li>[SMS](multi-factor-authentication-setup-phone-number.md) 'yi doğrulama kodları alacak şekilde ayarlama.</li><li>[Kimlik doğrulama için telefon görüşmesi](multi-factor-authentication-setup-office-phone.md)alacak telefon numarası ayarlama.</li></ul><br>Kişisel Microsoft hesabı kullanıcıları için alternatif seçenekler şunlardır:<br><ul><li>[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) veya [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)için Microsoft Authenticator ayarlama.</li><li>[Microsoft hesabı güvenlik sayfasından](https://account.microsoft.com/security/)güvenlik bilgilerinizi güncelleştirerek alternatif bir oturum açma YÖNTEMI (SMS veya e-posta) ayarlama.</li></ul>

### <a name="android-screenshots"></a>Android ekran görüntüleri

**S**: Android Authenticator üzerinde bir kerelik parola (OTP) kodlarından ekran görüntülerini alabilir miyim?

Y **: Authenticator** Android 'in 6.2003.1704 sürümünden başlayarak, varsayılan olarak tüm OTP kodları her zaman bir kimlik doğrulayıcısının ekran görüntüsü alınır. OTP kodlarınızı ekran görüntülerinde görmek veya diğer uygulamaların kimlik doğrulayıcı ekranını yakalamasını sağlamak istiyorsanız, kullanabilirsiniz. Kimlik Doğrulayıcı ' da **ekran yakalama** ayarını açıp uygulamayı yeniden başlatmanız yeterlidir.

### <a name="delete-stored-data"></a>Depolanan verileri Sil

**S**: Kimlik Doğrulayıcı hangi verileri kendi adınıza depolar ve nasıl silerim?

Y **: Authenticator** uygulaması üç tür bilgi toplar:

- Hesabınızı eklediğinizde sağladığınız hesap bilgileri. Bu veriler, hesabınız kaldırılarak kaldırılabilir.
- Microsoft 'a Günlükler göndermek için uygulamanın en üst menüsünde **geri bildirim gönderene** kadar yalnızca uygulamada kalan tanılama günlük verileri. Bu Günlükler e-posta adresleri, sunucu adresleri veya IP adresleri gibi kişisel verileri içerebilir. Ayrıca cihaz adı ve işletim sistemi sürümü gibi cihaz verilerini de içerebilir. Toplanan tüm kişisel veriler, uygulama sorunlarını gidermeye yardımcı olmak için gereken bilgiyle sınırlıdır. Toplanmakta olan bilgileri görmek için dilediğiniz zaman uygulamadaki bu günlük dosyalarına gözatabilmeniz gerekir. Günlük dosyalarınızı gönderirseniz, kimlik doğrulama uygulama mühendisleri onları yalnızca müşteri tarafından bildirilen sorunları gidermek için kullanır.
- Kişisel olarak tanımlanabilen kullanım verileri, "hesap akışı ekleme/başarıyla eklendi hesabı" veya "bildirim Onaylandı" gibi. Bu veriler, mühendislik kararlarımızın ayrılmaz bir parçasıdır. Kullanımınız, uygulamaları sizin için önemli şekillerde geliştirebileceğimizi belirlememize yardımcı olur. Uygulamayı ilk kez kullandığınızda bu veri koleksiyonunun bir bildirimini görürsünüz. Uygulamanın **Ayarlar** sayfasında kapatılamadığını size bildirir   . Dilediğiniz zaman bu ayarı açabilir veya kapatabilirsiniz.

### <a name="codes-in-the-app"></a>Uygulamadaki kodlar

**S**: uygulamadaki kodlar nelerdir?

Y **: kimlik** Doğrulayıcı 'yı açtığınızda, eklenen hesaplarınızı kutucuk olarak görürsünüz. İş veya okul hesaplarınız ve kişisel Microsoft hesaplarınızda, hesabın tam ekran görünümünde (hesap kutucuğuna dokunarak erişilen) altı veya sekiz basamaklı sayı görünür. Diğer hesaplar için, uygulamanın **hesaplar** sayfasında altı veya sekiz basamaklı bir sayı görürsünüz.<br>Size söylediğinizi doğrulamak için bu kodları tek kullanım parolası olarak kullanacaksınız. Kullanıcı adınız ve parolanızla oturum açtıktan sonra, bu hesapla ilişkili doğrulama kodunu yazmanız gerekir. Örneğin, contoso hesabınızda Katy oturum açıyorsanız, hesap kutucuğuna dokunduktan sonra 895823 doğrulama kodunu kullanmanız gerekir. Outlook hesabı için aynı adımları izlemeniz gerekir.<br>Contoso hesap kutucuğuna dokunun.<br>![Kimlik doğrulayıcı uygulamasındaki hesap kutucukları](media/user-help-auth-app-faq/katy-signin.png)<br>Contoso hesap kutucuğuna dokunduktan sonra doğrulama kodu tam ekranda görünür olur.<br>![Kimlik doğrulayıcısındaki hesap kutucuğunda doğrulama kodu](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Geri sayım süreölçeri

**S**: kodun yanındaki sayı neden devam etmez?

Y: etkin doğrulama kodunuzun yanında 30 **saniyelik bir Zamanlayıcı** olduğunu görebilirsiniz. Bu Zamanlayıcı, aynı kodu iki kez kullanarak hiçbir şekilde oturum açmanıza olanak sağlayacak. Bir parolanın aksine, bu sayıyı hatırlamanız istemiyorum. Fikir, yalnızca telefonunuza erişimi olan birisinin kodunuzun bilmesini sağlar.

### <a name="grayed-account-tile"></a>Gri hesap kutucuğu

**S**: Hesabım kutucuğu neden gridir?

Y **: bazı** kuruluşlar, kimlik doğrulayıcısının çoklu oturum açma ile çalışması ve kuruluş kaynaklarını koruması gerekir. Bu durumda, hesap iki adımlı doğrulama için kullanılmaz ve gri veya etkin değil olarak görüntülenir. Bu hesap türü genellikle "aracı" hesabı olarak adlandırılır.

### <a name="device-registration"></a>Cihaz kaydı

**S**: cihaz kaydı nedir?

Y **: Kuruluşunuz**, dosyalar ve uygulamalar gibi güvenli kaynaklara erişimi izlemek için cihazı kaydetmenizi gerektirebilir. Ayrıca, bu kaynaklara istenmeyen erişim riskini azaltmak için koşullu erişimi de açabilir. **Ayarlar** bölümünde cihazınızın kaydını silebilirsiniz, ancak Outlook 'taki e-postalara, OneDrive 'daki dosyalara erişiminizi kaybedebilirsiniz ve telefon oturum açma özelliğini kullanma imkanını kaybedersiniz.

### <a name="verification-codes-when-connected"></a>Bağlanıldığında doğrulama kodları

**S**: doğrulama kodlarını almak ve kullanmak için Internet 'e veya ağımdaki ağa bağlı olmam gerekiyor mu?

Y **: kodlar** Internet 'te veya verilere bağlı olmanızı gerektirmez, bu nedenle oturum açmak için telefon hizmetine gerek kalmaz. Ayrıca, uygulamayı kapattıktan hemen sonra çalışmayı durdurduğundan, pilinizi boşalmaz.

### <a name="no-notifications-when-app-is-closed"></a>Uygulama kapatıldığında bildirim yok

**S**: uygulama açık olduğunda neden yalnızca bildirim alıyorum? Uygulama kapatıldığında bildirim alamıyorum.

Y **: bildirim** alıyorsanız, ancak bir uyarı alamıyorsanız, uygulama ayarlarınızı denetlemeniz gerekir. Uygulamanın ses kullanımı veya bildirimler için Titret için açık olduğundan emin olun. Hiç bildirim alamazsanız, aşağıdaki koşulları denetlemeniz gerekir:<ul><li>Telefonunuz rahatsız değil veya sessiz modda mı? Bu modlar, uygulamaların bildirim göndermesini engelleyebilir.</li><li>Diğer uygulamalardan bildirimler alabilir misiniz? Aksi takdirde, telefonunuzdaki ağ bağlantılarıyla ilgili bir sorun olabilir veya Android veya Apple bildirimler kanalı olabilir. Telefon ayarlarınız aracılığıyla ağ bağlantılarınızı çözmeyi deneyebilirsiniz. Android veya Apple bildirimleri kanalında yardımcı olması için hizmet sağlayıcınıza konuşmanız gerekebilir.</li><li>Uygulamadaki bazı hesaplara ilişkin bildirimleri alabilir, ancak diğerlerini değil misiniz? Yanıt Evet ise, sorunlu hesabı uygulamanızdan kaldırın, bildirimleri yeniden ekleyin ve sorunu düzeltir.</li></ul>Bu adımların tümü denenseniz ve sorun yaşamaya devam ediyorsanız, Tanılama için günlük dosyalarınızı gönderilmesini öneririz. Uygulamayı açın, uygulamanın en üst düzey menüsüne gidin ve ardından **geri bildirim gönder**' i seçin. Bundan sonra [Microsoft Authenticator uygulama forumuna](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) gidin ve Microsoft 'un gördüğünüz sorunu ve denediğiniz adımları söyleyin.

### <a name="switch-to-push-notifications"></a>Anında iletme bildirimlerine geç

**S**: uygulamada doğrulama kodlarını kullanıyorum, ancak anında iletme bildirimlerine nasıl geçiş yapabilirim?

Y **: iş** veya okul hesabınız için ya da kişisel Microsoft hesabı için bildirimler ayarlayabilirsiniz. Bildirimler, Google veya Facebook gibi üçüncü taraf hesaplar için çalışmaz.<br>Kişisel hesabınızı bildirimleri üzerine geçirmek için, cihazınızı hesabıyla yeniden kaydetmeniz gerekir. **Hesap Ekle**' ye gidin, **Kişisel Microsoft hesabı**' nı seçin ve ardından Kullanıcı adınızı ve parolanızı kullanarak oturum açın.<br>Organizasyonunuz, iş veya okul hesabınız için tek tıklamayla bildirimlere izin verilip verilmeyeceğini belirler.

### <a name="notifications-for-other-accounts"></a>Diğer hesaplar için bildirimler

**S**: bildirimler Microsoft olmayan hesaplar için çalışıyor mu?

Y **: Hayır**, bildirimler yalnızca Microsoft hesaplarıyla ve Azure Active Directory hesaplarıyla çalışır. İş veya okul 'niz Azure AD hesapları kullanıyorsa, bu özelliği devre dışı bırakabilirsiniz.

### <a name="backup-and-recovery"></a>Yedekleme ve kurtarma

**S**: yeni bir cihaz aldım veya aygıtımı bir yedekten geri yükledi. Nasıl yaparım?, hesaplarımı yeniden kimlik doğrulayıcısında yeniden mi ayarla?

Y **: eski** cihazınızda **bulut yedeklemesini** açtıysanız, yeni iOS veya bir Android cihazında hesap kimlik bilgilerinizi kurtarmak için eski yedeklemenizi kullanabilirsiniz. Daha fazla bilgi için bkz. [kimlik doğrulayıcı Ile yedekleme ve kurtarma hesabı kimlik bilgileri](user-help-auth-app-backup-recovery.md) makalesi.

### <a name="lost-device"></a>Kayıp cihaz

**S**: cihazımı kaybettim veya yeni bir cihaza taşınıyor. Nasıl yaparım? bildirimlerin eski Cihazlarıma gitmeye devam etmesine izin veriyor musunuz?

Y **: yeni** cihazınıza kimlik doğrulayıcı eklemek, uygulamayı eski cihazınızdan otomatik olarak kaldırmaz. Uygulamanın eski cihazınızdan silinmesi yeterli değildir. Uygulamayı eski cihazınızdan silmeniz ve Microsoft 'un veya kuruluşunuzun eski cihazı unutmasını ve kaydını silmesini söylemeniz gerekir.<ul><li>**Uygulamayı kişisel Microsoft hesabı kullanarak bir cihazdan kaldırmak için.** [Hesap güvenliği](https://account.microsoft.com/security)sayfanızın iki adımlı doğrulama alanına gidin   ve eski cihazınız için doğrulamayı kapatmayı seçin.</li><li>**Uygulamayı bir iş veya okul Microsoft hesabı kullanarak bir cihazdan kaldırmak için.** Eski cihazınız için doğrulamayı devre dışı bırakmak üzere [Uygllarınızın](https://myapps.microsoft.com/) veya kuruluşunuzun özel portalının iki adımlı doğrulama alanına gidin.</li></ul>

### <a name="remove-account-from-app"></a>Hesabı uygulamadan kaldır

**S**: nasıl yaparım? bir hesabı uygulamadan kaldırmak istiyor musunuz?

**A: bir** hesap tam ekranını görüntülemek için uygulamadan kaldırmak istediğiniz hesabın hesap kutucuğuna dokunun. Hesabı uygulamadan kaldırmak için **Hesabı kaldır** ' a dokunun.<br>Kuruluşunuza kayıtlı bir cihazınız varsa, hesabınızı kaldırmak için ek bir adım gerekebilir. Bu cihazlarda, kimlik doğrulayıcı otomatik olarak bir cihaz yöneticisi olarak kaydedilir. Uygulamayı tamamen kaldırmak istiyorsanız uygulama ayarlarında uygulamanın kaydını kaldırmanız gerekir.

### <a name="too-many-permissions"></a>Çok fazla izin

**S**: uygulama neden çok sayıda izin istiyor?

**A**: burada, İstenen izinlerin tam listesi ve uygulama tarafından nasıl kullanıldıkları açıklanmaktadır. Gördüğünüz belirli izinler, sahip olduğunuz telefonun türüne bağlıdır.<ul><li>**Konum**. Bazen kuruluşunuz, belirli kaynaklara erişmenize izin vermeden önce konumunuzu bilmek istiyor. Uygulama bu izni yalnızca, kuruluşunuzun konum gerektiren bir ilke varsa ister.</li><li>**Biyometrik donanım kullanın.** Bazı iş ve okul hesapları, kimliğinizi her doğrulayışınızda ek bir PIN gerektirir. Uygulama, PIN girmek yerine Biyometri veya yüz tanıma 'yı kullanmak için izin vermenizi gerektirir.</li><li>**Fotoğraf.** Bir iş, okul veya Microsoft hesabı olmayan bir işlem eklediğinizde QR kodlarını taramak için kullanılır.</li><li>**Kişiler ve telefon.** Uygulama, telefonunuzda iş veya okul Microsoft hesaplarını aramak ve bunları sizin için uygulamaya eklemek için bu izni gerektirir.</li><li>**Kısa.** Kişisel Microsoft hesabı ilk kez oturum açtığınızda telefon numaranız kayıt numarası ile eşleştiğinden emin olmak için kullanılır. Bir 6-8 basamaklı doğrulama kodu içeren uygulamayı yüklediğiniz telefona bir kısa mesaj göndereceğiz. Bu kodu bulmanız ve kimlik doğrulayıcı tarafından metin iletisinde otomatik olarak bulduğu için girmeniz gerekmez.</li><li>**Diğer uygulamaların üzerine çizin.** Kimliğinizi doğrulayan aldığınız bildirim diğer çalışan uygulamalar üzerinde de görüntülenir.</li><li>**İnternet 'ten veri alın.** Bu izin, bildirim göndermek için gereklidir.</li><li>**Telefonun uyumasını önleyin.** Cihazınızı kuruluşunuza kaydettiğinizde, kuruluşunuz telefonunuzda bu ilkeyi değiştirebilir.</li><li>**Denetim titreşimi.** Kimliğinizi doğrulamak için bir bildirim aldığınızda bir titreşim isteyip istemediğinizi seçebilirsiniz.</li><li>**Parmak izi donanımını kullanın.** Bazı iş ve okul hesapları, kimliğinizi her doğrulayışınızda ek bir PIN gerektirir. İşlemi daha kolay hale getirmek için PIN 'inizi girmek yerine parmak izinizi kullanmanıza imkan sağlıyoruz.</li><li> **Ağ bağlantılarını görüntüleyin.** Microsoft hesabı eklediğinizde, uygulama ağ/Internet bağlantısı gerektirir.</li><li>**Depolamanın Içeriğini okuyun**. Bu izin yalnızca uygulama ayarları aracılığıyla teknik bir sorun raporaktardığınızda kullanılır. Depolamadaki bazı bilgiler, sorunu tanılamak için toplanır.</li><li>**Tam ağ erişimi.** Kimliğinizi doğrulamak için bildirim göndermek üzere bu izin gereklidir.</li><li>**Başlangıçta çalıştırın.** Telefonunuzu yeniden başlatırsanız, bu izin kimliğinizi doğrulamaya yönelik bildirimler almanızı sağlar.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Kilit açma olmadan istekleri Onayla

**S**: kimlik doğrulayıcı neden cihazın kilidini açmadan bir isteği onaylamanıza izin veriyor?

Y: herhangi **bir** telefonunuza sahip olduğunuzdan emin olmanız gerektiği için, doğrulama isteklerini onaylamak için cihazınızın kilidini açmanız gerekmez. İki aşamalı doğrulama, bilmeniz gereken bir şey ve sahip olduğunuz bir şey olmak üzere iki şeyi kanıtlama gerektirir. Bildiğiniz şey parolanız. Telefonunuza sahip olduğunuz şey (kimlik doğrulayıcı ile ayarlanır ve Multi-Factor Authentication sağlaması olarak kaydedilir.) Bu nedenle, telefonun ve isteğin onaylanmasından ikinci kimlik doğrulama faktörüyle ilgili ölçütlere uygun olması gerekir.

### <a name="activity-notifications"></a>Etkinlik bildirimleri

**S**: Hesabım etkinlikim hakkında neden bildirim alıyorum?

Y **: etkinlik** bildirimleri, kişisel Microsoft hesaplarınızda her değişiklik yapıldığında, daha güvenli tutmaya yardımcı olmak üzere anında kimlik doğrulayıcısına gönderilir. Daha önce bu bildirimleri yalnızca e-posta ve SMS aracılığıyla gönderdik. Bu etkinlik bildirimleri hakkında daha fazla bilgi için bkz. [hesabınızda olağan dışı oturum açma durumunda ne olur?](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in) Bildirimlerinizi nereden alacağınızı değiştirmek için hesabınızdaki [kritik olmayan hesap uyarıları ile sizinle iletişim kurduğumuz yerde](https://account.live.com/SecurityNotifications/Update) oturum açın.

### <a name="one-time-passcodes"></a>Bir kerelik geçiş kodları

**S**: tek seferlik geçiş kodlarım çalışmıyor. Ne yapmalıyım?

Y **: cihazınızdaki** tarih ve saatin doğru olduğundan ve otomatik olarak eşitlendiğinden emin olun. Tarih ve saat yanlış ya da eşitlenmemiş ise, kod çalışmaz.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**S**: Windows 10 Mobile işletim sistemi kullanım dışı bırakıldı Aralık 2019. Windows Mobile işletim sistemlerindeki Microsoft Authenticator de kullanım dışı bırakılacak mı?

Y **: tüm** Windows Mobile işletim sistemlerinde kimlik doğrulayıcı 28 Şubat 2020 ' den sonra desteklenmez. Kullanıcılar uygulamaya yeni güncelleştirmeler almak için uygun olmayacaktır. 28 Şubat 2020 ' den sonra, şu anda tüm Windows Mobile işletim sistemlerindeki Microsoft Authenticator kullanarak kimlik doğrulamaları destekledikleri Microsoft Hizmetleri, desteğini devre dışı bırakmaya başlayacak. Microsoft hizmetlerinde kimlik doğrulaması yapabilmek için, tüm kullanıcılarımızın bu tarihten önce alternatif bir kimlik doğrulama mekanizmasına geçiş yapmasını önemle öneririz.

### <a name="default-mail-app"></a>Varsayılan posta uygulaması

**S**: iOS ile gelen varsayılan posta uygulamasını kullanarak iş veya okul hesabınızda oturum açarken güvenlik doğrulama bilgilerim için yetkilendirmem istenir. Bu bilgileri girdikten ve posta uygulamasına geri döndüğünüzde bir hata alıyorum. Ne yapabilirim?

**A**: Bu en büyük olasılıkla, oturum açma ve posta uygulamanız iki farklı uygulama arasında gerçekleştiğinden, ilk arka planda oturum açma işleminin çalışmayı durdurmasına ve başarısız olmasına neden olur. Bu hatayı gidermeyi denemek için, posta uygulamanızda oturum açarken ekranın sağ alt tarafındaki **Safari** simgesini seçmenizi öneririz. Safari 'ye geçerek, tüm oturum açma süreci tek bir uygulamada gerçekleşir ve uygulamada başarıyla oturum açmanızı sağlar.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**S**: watchOS 7 üzerinde Apple Watch neden sorun yaşıyorum?

Y: watchOS 7 ' de bildirimleri onaylama ile **ilgili bir sorun** var ve bu sorunu düzeltti ve Apple ile çalışıyoruz. Bu sırada, Microsoft Authenticator watchOS uygulaması gerektiren tüm bildirimlerin, bunun yerine telefonunuzda onaylanması gerekir.

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch hesapları göstermiyor

**S**: Apple Watch, kimlik doğrulayıcı üzerinde kimlik doğrulamasını açtıklarında neden tüm hesaplarım gösterilmiyor?

Y **: Authenticator** yalnızca Apple Watch Yardımcısı uygulamasında anında iletme bildirimleri olan Microsoft kişisel veya okul veya iş hesaplarını destekler. Google veya Facebook gibi diğer hesaplarınız için doğrulama kodlarınızı görmek üzere telefonunuzdaki kimlik doğrulayıcı uygulamasını açmanız gerekir.

### <a name="apple-watch-notifications"></a>Apple Watch bildirimleri

**S**: neden Apple Watch bildirimleri onaylayamıyorum veya reddediyorum?

**A**: Ilk olarak iPhone 'Unuzdan Authenticator sürüm 6.0.0 veya üstünü yükseltdiğinizden emin olun. Bundan sonra, Apple Watch Microsoft Authenticator yardımcı uygulamasını açın ve altında **Kurulum** düğmesi olan herhangi bir hesabı bulun. Bu hesaplara ilişkin bildirimleri onaylamak için kurulum işlemini doldurun.

### <a name="apple-watch-communication-error"></a>İletişim Apple Watch hatası

**S**: Apple Watch ve telefonum arasında bir iletişim hatası alıyorum. Sorun gidermek için ne yapabilirim?

Y **: Bu** hata, izleme ekranınız telefonunuzla iletişim bitmeden önce uyku moduna geçtiğinde oluşur.<br><b>Kurulum sırasında hata oluşursa:</b><br>Kurulum 'u yeniden çalıştırmayı deneyin, böylece işlem tamamlanana kadar Izlemenize devam edin. Aynı zamanda, uygulamayı telefonunuzda açın ve görüntülenen istemlere yanıt verin.<br>Telefonunuz ve Watch 'ta hala iletişim kurulamaması durumunda aşağıdaki eylemleri deneyebilirsiniz:<ol><li>Microsoft Authenticator Phone uygulamasından çıkıp iPhone 'unuzdaki uygulamayı yeniden açın.</li><li>Apple Watch yardımcı uygulamadan çıkmaya zorlayın.<ol><li> Microsoft Authenticator yardımcı uygulamasını seyretmek üzere açın</li><li>**Kapat** ekranı görünene kadar kenar düğmesini basılı tutun.</li><li>Yan düğmeyi bırakın ve etkin uygulamadan çıkmaya zorlamak için Digital Crown basılı tutun.</li></ol></li><li>Hem telefonunuz hem de Izlemeniz için hem Bluetooth hem de Wi-Fi kapatın ve sonra yeniden açın.</li><li>İPhone 'nizi ve Watch 'u yeniden başlatın.</li></ol><b>Bir bildirimi onaylamaya çalışırken hata oluşursa:</b><br>Apple Watch bir bildirimi daha sonra onaylamaya çalıştığınızda, istek tamamlanana kadar ekranı açık tutun ve başarılı olduğunu belirten sesi dinleyin.

### <a name="apple-watch-companion-app-not-syncing"></a>Apple Watch yardımcı uygulama eşitlenmiyor

**S**: Microsoft Authenticator yardımcı uygulaması neden Apple Watch eşitleniyor veya Watch 'da gösterilmiyor.

Y **: uygulama**, Watch üzerinde görünmüyorsa aşağıdaki eylemleri deneyin: <ol><li>Watch 'un watchOS 4,0 veya üzeri bir sürümü çalıştırdığından emin olun.</li><li>Watch hesabınızı tekrar eşitleyin.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch yardımcı uygulama kilitlendi

**S**: Apple Watch Yardımcısı Uygulamam kilitlendi. Araştırma yapabilmeniz için kilitlenme Günlüklerimi gönderebilirim miyim?

**A**: öncelikle analizlerinizi bizimle paylaşmayı seçtiğinizden emin olmanız gerekir. Bir Testuçuş kullanıcısı kullanıyorsanız, zaten kaydolduysanız. Aksi takdirde, **ayarlar > gizlilik > Analytics** ' e gidebilir ve hem **iPhone & izleme analizi** hem de **uygulama geliştiricileri ile paylaşma** seçeneklerini belirleyebilirsiniz.<br>Kaydolduktan sonra, kilitlenme günlüklerinizin araştırma için otomatik olarak bize gönderilmesi için kilitlenmeyi yeniden oluşturmaya çalışırsınız. Ancak, çökmeyi yeniden oluşturamıyorsanız, günlük dosyalarınızı el ile kopyalayabilir ve bize gönderebilirsiniz.<ol><li>Telefonunuzda gözcü uygulamasını açın, **ayarlar > genel**' e gidin ve ardından **izleme analizini Kopyala**' ya tıklayın.</li><li>Ayarlar ' ın altında, **gizlilik > analytics > Analytics verileri**' ni > ve ardından metnin tamamını el ile kopyalayarak ilgili kilitlenmeyi bulun.</li><li>Telefonunuzdaki kimlik doğrulayıcı ' yı açın ve kopyalanmış olan  **metni, sorun** **yaşayan** konu başlığı altında bulunan  **sorunu açıkla** kutusuna yapıştırın. </li></ol>

## <a name="autofill-with-authenticator"></a>Doğrulayıcı ile otomatik doldurma

**S**: Authenticator Ile otomatik doldurma nedir?

Y **: Authenticator** uygulaması artık telefonunuzda ziyaret ettiğiniz uygulamalar ve web sitelerinde parolaları güvenli bir şekilde depolar ve yeniden doldurur. İOS ve Android cihazlarınızda parolalarınızı eşitlemek ve otomatik olarak açmak için Otomatik Doldur ' a de yararlanabilirsiniz. Kimlik doğrulayıcı uygulamasını telefonunuzda bir otomatik doldurma sağlayıcısı olarak ayarladıktan sonra, parolaları bir siteye veya bir uygulama oturum açma sayfasına girerken kaydetmenizi sağlar. Parolalar [kişisel Microsoft hesabı](https://account.microsoft.com/account) bir parçası olarak kaydedilir ve kişisel Microsoft hesabı Microsoft Edge 'de oturum açtığınızda da kullanılabilir.

**S**: hangi bilgileri bana otomatik doldurma Için kimlik doğrulayıcı yapabilir?

Y **: Authenticator**, telefonunuzdaki ziyaret ettiğiniz sitelerde ve uygulamalarda Kullanıcı adlarını ve parolaları otomatik olarak açabilir.

**S**: Telefonumdaki kimlik doğrulayıcısında parola Otomatik doldurmayı aç nasıl yaparım? misiniz?

Y **: şu adımları izleyin:**

1. Doğrulayıcı uygulamasını açın.
1. Doğrulayıcı 'daki **parolalar** sekmesinde **Microsoft hesabıyla oturum açın** seçin ve [Microsoft hesabı](https://account.microsoft.com/account)kullanarak oturum açın. Bu özellik şu anda yalnızca Microsoft hesaplarını destekliyor ve henüz iş veya okul hesaplarını desteklemiyor.

**S**: nasıl yaparım? Authenticator, Telefonumdaki varsayılan otomatik doldurma sağlayıcısına mi yapılsın?

Y **: şu adımları izleyin:**

1. Doğrulayıcı uygulamasını açın.
1. Uygulamanın içindeki **parolalar** sekmesinde **Microsoft hesabıyla oturum açın** ' yi seçin ve [Microsoft hesabı](https://account.microsoft.com/account)kullanarak oturum açın.
1. Aşağıdakilerden birini yapın:

   - İOS 'ta **Ayarlar** altında, varsayılan otomatik doldurma sağlayıcısı olarak kimlik doğrulamasını nasıl ayarlayacağınızı öğrenmek Için otomatik doldurma ayarları bölümünde **otomatik doldurma özelliğini açma** ' yı seçin.
   - Android 'de **Ayarlar** altında otomatik doldurma ayarları bölümünde **otomatik doldurma sağlayıcısı olarak ayarla** ' yı seçin.

**S**: ayarlarda **otomatik doldurma** kullanılamıyor mu?

Y: **bir** otomatik doldurma, kimlik doğrulayıcıda sizin için kullanılabilir değilse, bunun nedeni kuruluşunuz veya hesap türü için otomatik doldurmaya izin verilmiyor olabilir. Bu özelliği, iş veya okul hesabınızın eklendiği bir cihazda kullanabilirsiniz. Kuruluşunuz için otomatik doldurmaya izin verme hakkında daha fazla bilgi edinmek için bkz. [IT yöneticileri Için otomatik doldurma](#autofill-for-it-admins).

**S**: nasıl yaparım? parola eşitlemeyi durdur?

Y **: kimlik** Doğrulayıcı uygulamasında parolaları eşitlemeyi durdurmak için **Ayarlar**  >  **otomatik doldurma ayarları**  >  **eşitleme hesabı**' nı açın. Sonraki ekranda, **Eşitlemeyi Durdur ' u ve tüm otomatik doldurma verilerini kaldır**' ı seçebilirsiniz. Bu, parolaları ve diğer otomatik doldurma verilerini cihazdan kaldırır. Otomatik doldurma verilerinin kaldırılması Multi-Factor Authentication 'ı etkilemez.

**S**: parolalarım kimlik doğrulayıcı uygulaması tarafından nasıl korunur?

Y **: Authenticator** uygulaması zaten Multi-Factor Authentication ve hesap yönetimi için yüksek düzeyde güvenlik sağlar ve aynı yüksek güvenlik çubuğu da parolalarınızı yönetmeye yönelik olarak genişletilir.

- Kimlik doğrulayıcı **uygulaması Için güçlü kimlik doğrulaması gerekiyor: kimlik doğrulayıcıda** oturum açma işlemi ikinci bir faktör gerektirir. Bu, kimlik doğrulayıcı uygulamasının içindeki parolalarınızın, birisi Microsoft hesabı parolansa bile korunduğu anlamına gelir.
- **Verileri otomatik doldur Biyometri ve geçiş kodu**: bir uygulama veya sitede parolayı otomatik olarak doldurmaya başlamadan önce, Authenticator biyometrik veya cihaz geçiş kodu gerektirir. Bu, daha fazla güvenlik eklemeye yardımcı olur, böylece cihazınıza başka birinin erişimi olsa bile, Biyometri veya cihaz PIN girişi sağlamadıkları için parolanızı dolduramazlar veya göremez. Ayrıca, uygulama ayarlarında uygulama kilidi kapatmış olsalar bile, bir Kullanıcı, Biyometri veya PIN sağlamadıkça parolalar sayfasını açamaz.
- **Cihazdaki şifreli parolalar**: cihazdaki parolalar şifrelenir ve şifreleme/şifre çözme anahtarları hiçbir zaman depolanmaz ve gerektiğinde her zaman oluşturulur. Parolalar yalnızca Kullanıcı istediğinde, otomatik doldurma sırasında veya Kullanıcı, her ikisi de Biyometri veya PIN gerektiren parolayı görmek istediğinde çözülür.
- **Bulut ve ağ güvenliği**: buluttaki parolalarınız şifrelenir ve yalnızca cihazınıza ulaştığında şifresi çözülür. Parolalar SSL korumalı bir HTTPS bağlantısı üzerinden eşitlenir ve bu da bir saldırganın eşitleme sırasında hassas veriler üzerinde dinleme yapmasını önlemeye yardımcı olur. Ayrıca, şifreleme karma işlevleri (özellikle karma tabanlı ileti kimlik doğrulama kodu) kullanarak ağ üzerinden eşitlenen verilerin tasdikliğini denetliyoruz.

## <a name="autofill-for-it-admins"></a>BT yöneticileri için otomatik doldurma

**S**: çalışanlar veya öğrenciler kimlik doğrulayıcı uygulamasında parola otomatik doldurma özelliğini kullanacak mı?

Y: Evet, kimlik doğrulayıcı **uygulamasına bir iş** veya okul hesabı eklendiğinde bile, [Kişisel Microsoft hesaplarınız](https://go.microsoft.com/fwlink/?linkid=2144423) için otomatik doldurma artık kurumsal kullanıcılar için çalışır. Kuruluşunuz için otomatik doldurmaya izin vermek veya reddetmek ve [Doğrulayıcı ekibine göndermek](https://aka.ms/ConfigureAutofillInAuthenticator)için bir form doldurabilirsiniz. Otomatik doldurma, iş veya okul hesapları için şu anda kullanılamıyor.

**S**: kullanıcılarınızın iş veya okul hesabı parolası otomatik olarak eşitlenir mi?

Y **: Hayır**. Parola otomatik doldurma, kullanıcılarınız için iş veya okul hesabı parolasını eşitleyemez. Kullanıcılar bir siteyi veya uygulamayı ziyaret ettiğinde, kimlik doğrulayıcı bu site veya uygulamanın parolasını kaydetmenizi sağlar ve parola yalnızca Kullanıcı tarafından seçildiğinde kaydedilir.
  
**S**: yalnızca otomatik doldurma için Kuruluşumun yalnızca belirli kullanıcılarını listeleyebilir miyim?

Y **: Hayır**. Kuruluşlar, bu anda çalışanlarının tümü veya hiçbiri için otomatik doldurma sağlar.

**S**: çalışanım veya öğrencimin birden çok iş veya okul hesabı varsa ne olacak? Örneğin, çalışanım Microsoft Authenticator birden çok kurumdan veya okullardan hesaplar içeriyor.

Y **: kimlik** Doğrulayıcı uygulamasına eklenen tüm kuruluşlar veya okulların, uygulama sahibinin kullanabilmesi Için kimlik doğrulayıcıda otomatik doldurma için listelenmesi gerekir. Bu kısıtlamadaki tek istisna, çalışanınızın veya öğrencinin iş veya okul hesabını bir [dış veya üçüncü taraf hesabı](user-help-auth-app-add-non-ms-account.md)olarak Microsoft bulut tabanlı Multi-Factor Authentication 'a ekleme durumdur.

## <a name="next-steps"></a>Sonraki adımlar

- Kişisel Microsoft hesabı doğrulama kodunuzu alırken sorun yaşıyorsanız, [Microsoft hesabı güvenlik bilgileri & doğrulama kodları](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) makalesinin **sorun giderme doğrulama sorunları** bölümüne bakın.

- İki aşamalı doğrulama hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [hesabı iki adımlı doğrulama Için ayarlama](multi-factor-authentication-end-user-first-time.md)

- Güvenlik bilgileri hakkında daha fazla bilgi edinmek istiyorsanız bkz. [güvenlik bilgileri (Önizleme) genel bakış](./security-info-setup-signin.md)

- Sorunuz burada yanıtlanmamışsa, görüşlerinizi öğrenmek istiyoruz. Sorunuzu göndermek ve topluluktan yardım almak veya bu sayfada bir yorum bırakmak için [Microsoft Authenticator uygulama forumuna](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) gidin.
