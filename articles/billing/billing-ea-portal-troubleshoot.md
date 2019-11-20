---
title: Azure EA portalı erişim sorunlarını giderme
description: Bu makalede, Azure EA portalında Azure Kurumsal Anlaşma (EA) ile ilişkili olarak oluşabilen bazı sorunlar açıklanır.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/04/2019
ms.topic: troubleshooting
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1b0be54eea063399cc9ed2c3005808d5189e2663
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888347"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Azure EA portalı erişim sorunlarını giderme

Bu makalede Azure Kurumsal Anlaşma (EA) ile ilişkili olarak oluşabilen yaygın sorunlar açıklanır. Azure EA portalı, kurumsal anlaşma kullanıcıları ve maliyetlerini yönetmek için kullanılır. Azure EA portalı erişimini yapılandırırken veya güncelleştirirken bu sorunlarla karşılaşabilirsiniz.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Kayda yönetici ekleme sorunları

Kurumsal kayıtlar için farklı türde kimlik doğrulama düzeyleri vardır. Kimlik doğrulaması düzeyleri yanlış uygulandığında Azure EA portalında oturum açmaya çalıştığınızda sorunlarla karşılaşabilirsiniz.

Farklı kimlik doğrulaması düzeylerine sahip kullanıcılara erişim izni vermek için Azure EA portalını kullanırsınız. Kuruluş yöneticisi, kuruluşlarının güvenlik gereksinimlerini karşılamak için kimlik doğrulaması düzeyini güncelleştirebilir.

### <a name="authentication-level-types"></a>Kimlik doğrulaması düzeyi türleri

- Yalnızca Microsoft Hesabı - Microsoft hesapları aracılığıyla kullanıcıları kullanmak, oluşturmak ve yönetmek isteyen kuruluşlara yöneliktir.
- İş veya Okul Hesabı - Buluta Federasyon ile Active Directory ayarlamış olan ve tüm hesapları tek bir kiracıda bulunan kuruluşlara yöneliktir.
- Kiracılar Arasında İş veya Okul Hesabı - Buluta Federasyon ile Active Directory ayarlamış ve birden çok kiracıda hesapları bulunacak olan kuruluşlara yöneliktir.
- Karma Hesap - Microsoft Hesabı ve/veya İş ya da Okul Hesabıyla kullanıcı eklemenize olanak tanır.

Kayda eklenen ilk iş veya okul hesabı _varsayılan_ veya _ana_ etki alanını belirler. Başka bir kiracının iş veya okul hesabını eklemek için kaydın altındaki kimlik doğrulaması düzeyini kiracılar arası kimlik doğrulaması olarak değiştirmeniz gerekir.

Kimlik Doğrulaması Düzeyini güncelleştirmek için:

1. Azure EA portalında Kuruluş Yöneticisi olarak oturum açın.
2. Sol gezinti panelinde **Yönet**’e tıklayın.
3. **Kayıt** sekmesine tıklayın.
4. **Kayıt Ayrıntıları**’ndan **Kimlik Doğrulaması Düzeyi**’ni seçin.
5. Kalem simgesine tıklayın.
6. **Kaydet**’e tıklayın.

![Kimlik doğrulaması düzeylerini gösteren örnek ](./media/billing-ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft hesaplarının [https://signup.live.com](https://signup.live.com/) adresinde oluşturulmuş bir ilişkili kimliği olması gerekir.

İş veya okul hesapları, federasyonla Active Directory ayarlamış olan ve tüm hesapları tek bir kiracıda bulunan kuruluşlar tarafından kullanılabilir. Şirketin iç Active Directory hizmeti federasyon hizmetiyse kullanıcılar iş veya okul federasyon kullanıcısı kimlik doğrulamasıyla eklenebilir.

Kuruluşunuz Active Directory federasyonu kullanmıyorsa iş veya okul e-posta adresinizi kullanamazsınız. Bunun yerine yeni bir e-posta adresi kaydedin veya oluşturun ve bunu Microsoft hesabı olarak kaydedin.

## <a name="unable-to-access-the-azure-ea-portal"></a>Azure EA portalına erişilemiyor

Azure EA portalında oturum açmaya çalışırken hata iletisi alıyorsanız aşağıdaki sorun giderme adımlarını izleyin:

- Doğru Azure EA portalı URL’sini kullandığınızdan emin olun (https://ea.azure.com ).
- Azure EA portalına erişiminizin iş veya okul hesabı olarak mı yoksa Microsoft hesabı olarak mı eklendiğini belirleyin.
  - İş hesabınızı kullanıyorsanız, iş e-postanızı ve parolanızı girin. İş parolanız kuruluşunuz tarafından sağlanır. Parolayla ilgili sorun yaşıyorsanız sıfırlamak için BT bölümünüze danışabilirsiniz.
  - Microsoft hesabı kullanıyorsanız, Microsoft hesabı e-posta adresinizi ve parolanızı girin. Microsoft hesabı parolanızı unuttuysanız [https://account.live.com/password/reset](https://account.live.com/password/reset) adresinde parolayı sıfırlayabilirsiniz.
- Önceki veya mevcut oturumlardan kalan tanımlama bilgilerinin ya da önbelleğe alınmış bilgilerin saklanmaması için gizli veya özel tarayıcı oturumu kullanarak oturum açın. Tarayıcınızın önbelleğini temizleyin ve https://ea.azure.com adresini açmak için gizli veya özel bir pencere kullanın.
- Microsoft hesabını kullanırken _Geçersiz Kullanıcı_ hatası alıyorsanız, bunun nedeni birden çok Microsoft hesabınız olması olabilir. Oturum açmaya çalıştığınız e-posta adresi birincil e-posta adresi olmayabilir.
Öte yandan _Geçersiz Kullanıcı_ hatası almanızın nedeni, kullanıcı kayda eklenirken yanlış hesap türünün kullanılmış olması da olabilir. Örneğin, Microsoft hesabı yerine iş veya okul hesabı kullanılmıştır. Bu örnekte başka bir EA yöneticisi doğru hesabı ekler veya [desteğe](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c) başvurmanız gerekir.
  - Birincil diğer adı denetlemeniz gerekiyorsa [https://account.live.com](https://account.live.com) adresine gidin. Ardından **Bilgileriniz**'e ve **Microsoft'ta oturum açma şeklinizi yönetin**'e tıklayın. Alternatif bir e-posta adresini doğrulamak ve hassas bilgilere erişim kodunu almak için yönergeleri izleyin. Güvenlik kodunu girin. İki öğeli kimlik doğrulaması ayarlamak istemiyorsanız **Daha sonra ayarla**’yı seçin.
  - Hesap diğer adlarınızı görüntüleyebileceğiniz **Microsoft'ta oturum açma şeklinizi yönetin** sayfasını görürsünüz. Azure EA portalında oturum açmak için kullandığınız diğer adın birincil diğer ad olduğundan emin olun. Değilse, bunu birincil diğer adınız olarak ayarlayabilirsiniz. Öte yandan bunun yerine Azure EA portalı için olan birincil adı da kullanabilirsiniz.

## <a name="no-activation-email-received"></a>Etkinleştirme e-postası alınmadı

Azure EA portalından gelen etkinleştirme e-postası *waep@microsoft.com* adresinden gönderilir. Etkinleştirme e-postasını almadıysanız istenmeyen veya gereksiz posta klasörünüzü gözden geçirin. Bu e-posta _Microsoft Azure service_subject Görüntüleme/Yönetme Daveti_ ile gönderilir. Yeni eklenen tüm EA yöneticilerine gönderilir.

EA yöneticisi olarak ayarladığınızdan eminseniz, Azure EA portalında oturum açmak için etkinleştirme e-postasını beklemeniz gerekmez. Bunun yerine https://ea.azure.com adresine giderek e-posta adresinizle (iş, okul veya Microsoft hesabı) ve parolanızla oturum açabilirsiniz.

## <a name="azure-ea-activation-faq"></a>Azure EA Etkinleştirme hakkında SSS

Makalenin bu bölümünde Azure EA Etkinleştirme işlemleriyle ilgili yaygın sorunların çözümlerine yer verilmiştir.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Kaydıma yeni bir EA yöneticisi eklemek istiyorum

Yeni bir kuruluş yöneticisi ancak mevcut kuruluş yöneticileri tarafından eklenebilir. EA yöneticisiyseniz lütfen EA Portal'da oturum açın, **Yönet**'e ve ardından sağ üst köşedeki **+ Yönetici Ekle**'ye tıklayıp yeni EA yöneticisi ekleyin. Kullanıcıları eklemek için lütfen e-posta adreslerine ve iş veya okul hesabı ya da Microsoft Live ID gibi tercih edilen oturum açma bilgilerine sahip olduğunuzdan emin olun.

EA yöneticisi değilseniz lütfen şirketinizdeki EA yöneticilerine ulaşarak sizi kayda eklemelerini isteyin. Sizi kayda eklediklerinde bir etkinleştirme postası alacaksınız.

Ancak EA yöneticilerinin size yardımcı olamadığı durumlarda aşağıdaki bilgileri iletmeniz halinde sizi onların adına kayda ekleyebiliriz:
- Kayıt numarası.
- Eklenecek e-posta adresi ve kimlik doğrulama türü (iş/okul/MS).
- EA yöneticisinden gelen onay e-postası.

Gerekli tüm bilgilere sahip olduktan sonra lütfen [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) adresinden istek gönderin.

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Kayıttaki ilk EA yöneticisini güncelleştirmek istiyorum

İlk EA yöneticisini güncelleştirmek için toplu lisanslama hizmet merkezinde bildirim ilgili kişisini ve çevrimiçi yöneticiyi güncelleştirmeniz gerekir. Değişikliklerin EA portalına uygulanması yaklaşık 24 saat sürer. Güncelleştirme tamamlandıktan sonra yeni EA yöneticisine bir etkinleştirme e-postası gönderilir.

VLSC portalına erişiminiz yoksa veya ilk EA yöneticiniz artık kaydı yönetemiyorsa ve EA portalına erişimi yoksa güncelleştirme isteğinde bulunmak için lütfen [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) üzerinden bir istek gönderin ve şu bilgileri dahil edin:
- Kayıt numarası
- Eklenecek e-posta adresi ve kimlik doğrulama türü (iş/okul/MS)
- İlk EA yöneticisini değiştirme nedeni
- İlk EA yöneticisinden gelen onay e-postası

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>Geçerli EA yöneticim şirketten ayrıldı

Bir EA kaydında birden fazla EA yöneticisi olabilir ve başka bir EA yöneticisine ulaşarak yeni EA yöneticisi/hesap sahibi/bölüm yöneticisi eklemesini isteyebilirsiniz. Ancak şirketinizdeki EA yöneticisinin kim olduğunu bilmiyorsanız veya kayıtta başka bir EA yöneticisi yoksa lütfen aşağıdaki bilgilerle birlikte bize ulaşın:
- Kayıt numarası
- Eklenecek e-posta adresi ve kimlik doğrulama türü (iş/okul/MS)
- Geçerli EA yöneticisinin şirketten ayrıldığına dair destekleyici bilgiler

Kayıtta başka EA yöneticileri varsa kayıtta gerçekleştirilecek yönetim değişiklikleriyle ilgili olarak mevcut EA yöneticilerine ulaşacağımızı lütfen unutmayın.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>Kaydım bekleme durumunda görünüyor. Kaydımı nasıl etkinleştirebilirim?

İlk EA yöneticisi henüz oturum açmadıysa kayıt bekleme durumunda görünür. EA yöneticisi sizdeniz lütfen Azure EA Portal'da oturum açın. Tüm kayıt numaralarının yer aldığı giriş sayfasında bekleyen kaydınızı göremeyebilirsiniz. Lütfen EA Portal'ın sağ üst köşesinde yer alan "etkin" kutusunun işaretini kaldırın. Bunu yaptığınızda bekleme durumundaki kayıtlar da görüntülenir. Ayrıntılı bilgilere ulaşmak için lütfen kayda tıklayın. Kaydın "Yönet" sayfasına ulaştığınızda "Beklemede" olan durum, "Etkin" olarak güncelleştirilir.

### <a name="why-is-my-account-stuck-in-pending-status"></a>Hesabım neden bekleme durumunda takılı kaldı?

Kayda ilk kez eklenen yeni Hesap Sahiplerinin (AO) durumu her zaman "beklemede" olarak görünür. Hesap sahibi etkinleştirme karşılama e-postasını aldıktan sonra oturum açarak hesabını etkinleştirebilir. Oturum açıldığında "beklemede" olan hesap durumu "etkin" olarak değişir.

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Azure EA Portal'da oturum açarken bir hatayla karşılaştım

Oturum açma sırasında Azure EA Portal'da hatayla karşılaşmanızın birkaç nedeni olabilir. Lütfen şu sorun giderme adımlarını izleyin:

 1. Lütfen doğru EA Portal URL'si olan [https://ea.azure.com](https://ea.azure.com) adresini kullandığınızdan emin olun.
 1. Azure EA Portal'a erişiminizin iş veya okul hesabı olarak mı yoksa Microsoft Live ID olarak mı eklendiğini belirleyin. İş hesabınızı kullanıyorsanız lütfen iş e-postanızı ve parolanızı girin. Microsoft Live ID kullanıyorsanız lütfen Live ID e-postanızı ve Microsoft Live ID parolanızı girin. Microsoft Live ID parolanızı unuttuysanız lütfen [https://account.live.com/password/reset](https://account.live.com/password/reset) adresinden sıfırlayın.
 1. Önceki/mevcut oturumların tanımlama bilgilerinin veya önbellek verilerinin tutulmaması için gizli tarayıcı penceresinden oturum açmanız önerilir. Önbelleği temizleyin ve [https://ea.azure.com](https://ea.azure.com) adresini özel göz atma modundan/gizli pencereden açın.
 1. Microsoft hesabı kullanıyor ve Geçersiz Kullanıcı hatası alıyorsanız birden fazla Microsoft hesabınız olabilir ve oturum açmak için kullandığınız hesap, birincil diğer ad olmayabilir. Birincil diğer adı denetlemek için account.live.com adresine gidin:
    - "Bilgileriniz" > "Oturum açma e-posta adresinizi veya telefon numaranızı yönetin" yolunu izleyin.
    - Alternatif bir e-posta adresini doğrulamak ve hassas bilgilere erişim kodunu almak için ekrandaki yönergeleri izleyin.
    - Güvenlik kodunu girin.
    - İki adımlı kimlik doğrulamayı daha sonra ayarlamak istiyorsanız "Daha sonra ayarla"yı seçin.
    - Sahip olduğunuz hesap diğer adlarının yer aldığı "Hesap diğer adlarınızı yönetin" sayfası açılır. Azure EA Portal'da oturum açmak için kullandığınız diğer adın birincil diğer ad olduğundan emin olun. Değilse, bunu birincil diğer adınız yapabilir veya bunun yerine EA Portal'daki birincil diğer adı kullanabilirsiniz.

Yukarıdaki sorun giderme adımları başarısız olduysa lütfen [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) adresinden aşağıdaki bilgilerle birlikte bir istek gönderin:
- Kullanılan tarayıcılar ve sürüm.
- Hata iletisinin ekran görüntüsü.
- Hata veren sayfanın URL'si.  
- Hatanın gerçekleştiği tarih, saat ve saat dilimi.
- Ayrıca günlük dosyasına ulaşabilirseniz faydalı olacaktır. Aşağıdaki adımları izleyerek ağ izlemesi yakalayabilirsiniz:
  1. Internet Explorer'ı açın.
  1. F12 tuşuna basın; IE'nin en altında bir kutu açılır.
  1. **Ağ** sekmesini seçin.
  1. **Yakalamaya Başla**'ya tıklayın.
  1. Hataya neden olan eylemi gerçekleştirin.
  1. Hatayı aldıktan sonra **Yakalamayı Durdur**'a tıklayın.
  1. Dosyayı kaydedin ve destek isteğine gerekli bilgileri ekleyin.
  1. Destek isteğine kayıt numaranızı ve e-posta adresinizi yazmayı unutmayın.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>İş veya okul hesabı ile Microsoft hesabı arasındaki fark nedir?

**Microsoft hesabı:** [https://signup.live.com](https://signup.live.com) adresindeki Live ID ile ilişkilendirilmiş hesaplardır.

**İş veya okul hesabı:** Yalnızca Buluta Federasyon ile Active Directory ayarlamış olan ve tüm hesapları tek bir kiracıda bulunan şirketler tarafından kullanılabilir. Şirketin iç Active Directory hizmeti ile bulut arasında federasyon varsa kullanıcılar iş veya okul kimlik doğrulamasıyla eklenebilir.

  Microsoft, Eylül 2016'dan itibaren iş veya okul e-posta adreslerinin Microsoft hesabı olarak kaydedilmesine izin vermemektedir. Daha fazla ayrıntı için şu belgelere başvurun: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Kuruluşunuzla bulut arasında federasyon yoksa iş veya okul e-posta adresinizi kullanamazsınız. Bunun yerine lütfen yeni bir e-posta adresi kaydedin veya oluşturun ve bunu Microsoft hesabı olarak kaydedin.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Azure EA Portal parolamı unuttum

Microsoft Live ID parolanızı unuttuysanız lütfen [https://account.live.com/password/reset](https://account.live.com/password/reset) adresinden sıfırlayın.

Çalışma parolanızı unuttuysanız lütfen şirketinizin BT yöneticisine başvurun.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Geçerli bir iş veya okul hesabım var ancak EA Portal'a ekleyemiyorum

Farklı bir kiracıda iş veya okul hesabınız varsa lütfen kayıt ayrıntıları sayfasındaki yetkilendirme düzeyini "Kiracılar Arası İş veya Okul Hesabı" olarak değiştirin. Bu ayarı yaptıktan sonra hesabı ekleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Azure EA portalı yöneticilerinin yaygın yönetim görevleri hakkında bilgi edinmek için [Azure EA portalı yönetimi](billing-ea-portal-administration.md) makalesini okumaları gerekir.
