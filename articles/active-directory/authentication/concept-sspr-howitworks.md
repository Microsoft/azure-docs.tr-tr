---
title: Self servis parola sıfırlama derin bakış-Azure Active Directory
description: Self servis parola sıfırlama nasıl çalışır?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94077a1c6329aa1fecf9593f2df41fa77afc8a44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765926"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Nasıl çalışır? Azure AD self servis parola sıfırlama

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR), kullanıcılara yönetici veya yardım masası katılımı olmadan parolasını değiştirme veya sıfırlama yeteneği sağlar. Bir kullanıcının hesabı kilitliyse veya parolalarını unutduklarında, kendi kendilerini engellemeyi kaldırmak ve çalışmaya geri dönmek için istemleri izleyebilir. Bu özellik, bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltır.

> [!IMPORTANT]
> Bu kavramsal makalede, self servis parola sıfırlamanın nasıl çalıştığı bir yöneticiye açıklanmaktadır. Self servis parola sıfırlama için zaten kayıtlı bir son kullanıcı varsa ve hesabınıza geri dönmek istiyorsanız adresine gidin [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> BT ekibiniz kendi parolanızı sıfırlama özelliğini etkinleştirmediyseniz, ek yardım için yardım masasına ulaşın.

## <a name="how-does-the-password-reset-process-work"></a>Parola sıfırlama işlemi nasıl çalışır?

Kullanıcı [SSPR portalını](https://aka.ms/sspr)kullanarak parolalarını sıfırlayabilir veya değiştirebilir. Önce istedikleri kimlik doğrulama yöntemlerini kaydettirmelidir. Bir Kullanıcı SSPR portalına eriştiğinde, Azure platformu aşağıdaki faktörleri göz önünde bulundurur:

* Sayfa nasıl Yerelleştirilecek?
* Kullanıcı hesabı geçerli mi?
* Kullanıcı hangi kuruluşa ait?
* Kullanıcının parolası nerede yönetilir?
* Kullanıcı, özelliği kullanmak için lisanslı mı?

Bir Kullanıcı bir uygulama veya sayfadan **Hesap bağlantısına erişemeyeceğini** veya doğrudan öğesine gittiğinden, [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) SSPR portalında kullanılan dil aşağıdaki seçeneklere dayanır:

* Varsayılan olarak, tarayıcı yerel ayarı SSPR 'yi uygun dilde göstermek için kullanılır. Parola sıfırlama deneyimi [Microsoft 365 desteklediği](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)dillere yereldir.
* SSPR 'yi belirli bir yerelleştirilmiş dilde bağlamak istiyorsanız, `?mkt=` parola sıfırlama URL 'sinin sonuna gereken yerel ayarı ekleyin.
    * Örneğin, Ispanyolca *-US* yerel ayarını belirtmek için, kullanın `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

SSPR portalı gerekli dilde görüntülendikten sonra kullanıcıdan bir kullanıcı KIMLIĞI girmesi ve bir CAPTCHA geçirmesi istenir. Azure AD artık kullanıcının SSPR 'yi şu denetimleri yaparak kullanabildiğini doğrular:

* Kullanıcının SSPR özellikli olduğunu ve bir Azure AD lisansı atandığını denetler.
  * Kullanıcı SSPR için etkinleştirilmemişse veya atanmış bir lisansı yoksa, kullanıcıdan parolasını sıfırlaması için yöneticisiyle iletişim kurabilmesi istenir.
* Kullanıcının, yönetici ilkesine uygun olarak hesabında tanımlı doğru kimlik doğrulama yöntemlerine sahip olduğunu denetler.
  * İlke yalnızca bir yöntem gerektiriyorsa, kullanıcının yönetici ilkesi tarafından etkinleştirilen kimlik doğrulama yöntemlerinden en az biri için tanımlı uygun verilere sahip olup olmadığını denetleyin.
    * Kimlik doğrulama yöntemleri yapılandırılmamışsa, kullanıcıdan parolasını sıfırlaması için yönetici ile iletişim kurmanız önerilir.
  * İlke iki yöntem gerektiriyorsa, kullanıcının yönetici ilkesi tarafından etkinleştirilen kimlik doğrulama yöntemlerinin en az ikisi için tanımlı uygun verilere sahip olup olmadığını denetleyin.
    * Kimlik doğrulama yöntemleri yapılandırılmamışsa, kullanıcıdan parolasını sıfırlaması için yönetici ile iletişim kurmanız önerilir.
  * Kullanıcıya bir Azure yönetici rolü atanırsa, güçlü iki geçit parola ilkesi zorlanır. Daha fazla bilgi için bkz. [yönetici sıfırlama ilkesi farklılıkları](concept-sspr-policy.md#administrator-reset-policy-differences).
* Kullanıcının parolasının şirket içinde yönetilip yönetilmediğini (örneğin, Azure AD kiracısı federe, geçişli kimlik doğrulaması veya parola karması eşitlemesi mi kullanıyorsa) denetler:
  * SSPR geri yazma özelliği yapılandırılmışsa ve kullanıcının parolası şirket içinde yönetiliyorsa, kullanıcının kimlik doğrulamasına ve parolalarını sıfırlamasına izin verilir.
  * SSPR geri yazma özelliği dağıtılmazsa ve kullanıcının parolası şirket içinde yönetiliyorsa, kullanıcıdan parolasını sıfırlaması için yöneticisiyle iletişim kurabilmesi istenir.

Önceki denetimlerin tümü başarıyla tamamlanırsa, Kullanıcı, parolasını sıfırlama veya değiştirme sürecinde kılavuzluk eder.

> [!NOTE]
> SSPR kullanıcılara parola sıfırlama işleminin bir parçası olarak e-posta bildirimleri gönderebilir. Bu e-postalar, çeşitli bölgelerde etkin-etkin modda çalışan SMTP geçiş hizmeti kullanılarak gönderilir.
>
> SMTP geçiş hizmetleri e-posta gövdesini alıp işler, ancak depomayın. Kullanıcı tarafından girilen bilgileri içerebilen SSPR e-postası gövdesi SMTP geçiş hizmeti günlüklerinde depolanmaz. Günlükler yalnızca protokol meta verilerini içerir.

SSPR 'yi kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Self servis parola sıfırlamayı etkinleştirme (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Kullanıcıların oturum açtıklarında kaydolmaları iste

Azure AD 'yi kullanarak herhangi bir uygulamada oturum açmak için modern kimlik doğrulama veya Web tarayıcısı kullanıyorsa, kullanıcının SSPR kaydını tamamlamasını isteme seçeneğini etkinleştirebilirsiniz. Bu iş akışı aşağıdaki uygulamaları içerir:

* Microsoft 365
* Azure portalı
* Erişim Paneli
* Federasyon uygulamaları
* Azure AD kullanarak özel uygulamalar

Kayıt gerekmiyorsa, kullanıcılar oturum açma sırasında istenmez, ancak bunları el ile kaydedebilirler. Kullanıcılar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , erişim paneli 'Ndeki **profil** sekmesinde bulunan **parola sıfırlama** bağlantısını ziyaret edebilir veya seçebilir.

![Azure portal SSPR için kayıt seçenekleri][Registration]

> [!NOTE]
> Kullanıcılar, **iptal** ' i seçerek veya pencereyi kapatarak SSPR kayıt portalını kapatabilir. Ancak, kayıtlarını tamamlamada her oturum açtıklarında kaydolmaları istenir.
>
> SSPR 'ye kaydolmak için bu kesme, kullanıcının zaten oturum açmış olmaları durumunda bağlantısını kesmez.

## <a name="reconfirm-authentication-information"></a>Kimlik doğrulama bilgilerini yeniden Onayla

Kimlik doğrulama yöntemlerinin parolasını sıfırlama veya değiştirme gereksinimlerinde doğru olduğundan emin olmak için, belirli bir süre sonra kullanıcıların bilgileri kayıtlı bilgilerini onaylamasını zorunlu kılabilirsiniz. Bu seçenek yalnızca **kullanıcıların oturum açarken kaydolmasını gerektir** seçeneğini etkinleştirdiğinizde kullanılabilir.

Kullanıcının kayıtlı yöntemlerinin onaylamasını istemek için geçerli değerler *0* ile *730* gün arasında. Bu değerin *0* olarak ayarlanması, kullanıcıların kimlik doğrulama bilgilerini hiçbir şekilde onaylamasını istemeyeceği anlamına gelir. Birleşik kayıt deneyimini kullanırken, kullanıcıların bilgilerini yeniden onaylamadan önce kimliklerini onaylamalarını sağlamak gerekecektir.

## <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri

Bir Kullanıcı SSPR için etkinleştirildiğinde, en az bir kimlik doğrulama yöntemi kaydetmeleri gerekir. Kullanıcılarınızın ihtiyaç duydukları bir yönteme erişememesi durumunda daha fazla esneklik sağlamak için iki veya daha fazla kimlik doğrulama yöntemi seçmenizi önemle öneririz. Daha fazla bilgi için bkz. [kimlik doğrulama yöntemleri nedir?](concept-authentication-methods.md).

Aşağıdaki kimlik doğrulama yöntemleri SSPR için kullanılabilir:

* Mobil uygulama bildirimi
* Mobil uygulama kodu
* E-posta
* Cep telefonu
* Ofis telefonu
* Güvenlik soruları

Kullanıcılar, yalnızca yöneticinin etkinleştirildiği bir kimlik doğrulama yöntemi kaydolduklarında parolalarını sıfırlayabilirler.

> [!WARNING]
> Azure *yönetici* rolleri atanan hesaplara, [yönetici ilke farklılıklarını sıfırlama](concept-sspr-policy.md#administrator-reset-policy-differences)bölümünde tanımlandığı şekilde yöntemler kullanılması gerekir.

![Azure portal, kimlik doğrulama yöntemleri seçimi][Authentication]

### <a name="number-of-authentication-methods-required"></a>Gereken kimlik doğrulama yöntemi sayısı

Kullanıcının parolasını sıfırlamak veya kilidini açmak için sağlaması gereken kullanılabilir kimlik doğrulama yöntemlerinin sayısını yapılandırabilirsiniz. Bu değer, *bir* veya *iki* olarak ayarlanabilir.

Kullanıcılar, birden çok kimlik doğrulama yöntemini kaydedebilir ve bunları yapabilir. Ayrıca, kullanıcıların, ihtiyaç duydukları bir yönteme erişememesi durumunda daha fazla esneklik sağlamak için iki veya daha fazla kimlik doğrulama yöntemi kaydetmesi önemle önerilir.

Bir Kullanıcı SSPR kullanmaya çalıştıklarında kayıtlı en az sayıda gerekli yöntem yoksa, bunları yöneticinin parolasını sıfırlamasını isteyecek şekilde yönlendiren bir hata sayfası görürler. SSPR için kayıtlı olan kullanıcılarınız varsa ve bu özelliği kullanmadıysanız, bir ' dan iki ' a kadar gerekli yöntemlerin sayısını artırdıysanız dikkatli olabilirsiniz. Daha fazla bilgi için, [kimlik doğrulama yöntemlerini değiştirmek](#change-authentication-methods)için aşağıdaki bölüme bakın.

#### <a name="mobile-app-and-sspr"></a>Mobil uygulama ve SSPR

Microsoft Authenticator uygulaması gibi, parola sıfırlama yöntemi olarak bir mobil uygulama kullanırken aşağıdaki noktalar geçerlidir:

* Yöneticiler bir parolayı sıfırlamak için bir yöntem kullanılmasını gerektirdiğinde, doğrulama kodu kullanılabilir tek seçenektir.
* Yöneticiler bir parolayı sıfırlamak için iki yöntem gerektirdiğinde, kullanıcılar bildirim **veya** doğrulama kodunu diğer etkin yöntemlere ek olarak kullanabilir.

| Sıfırlamak için gereken Yöntem sayısı | Bir | İki |
| :---: | :---: | :---: |
| Mobil uygulama özellikleri kullanılabilir | Kod | Kod veya bildirim |

Kullanıcılar, self servis parola sıfırlama için kaydolurken mobil uygulamalarını kaydetme seçeneğine sahip değildir [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Kullanıcılar mobil uygulamalarını konumunda [https://aka.ms/mfasetup](https://aka.ms/mfasetup) veya Birleşik güvenlik bilgileri kaydına kaydedebilir [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Yalnızca bir yöntem gerekli olduğunda, Authenticator uygulaması tek kimlik doğrulama yöntemi olarak seçilemez. Benzer şekilde, iki yöntem gerektirmesi halinde Authenticator uygulaması ve yalnızca bir ek yöntem seçilemez.
>
> Bir yöntem olarak Authenticator uygulamasını içeren SSPR ilkelerini yapılandırırken, bir yöntem gerekli olduğunda en az bir ek yöntem seçilmelidir ve iki yöntemin yapılandırılması gerektiğinde en az iki ek yöntem seçilmelidir.
>
> Bu gereksinim, geçerli SSPR kayıt deneyiminin kimlik doğrulayıcı uygulamasını kaydetme seçeneğini içermediği için gereklidir. Kimlik doğrulayıcı uygulamasını kaydetme seçeneği, yeni [Birleşik kayıt deneyimine](./concept-registration-mfa-sspr-combined.md)dahildir.
>
> Yalnızca Authenticator uygulamasını (bir yöntem gerekli olduğunda) veya Doğrulayıcı uygulamasını ya da yalnızca bir ek yöntemi (iki yöntem gerektiğinde) kullanan ilkelere izin vermek, kullanıcıların, yeni Birleşik kayıt deneyimini kullanacak şekilde yapılandırılıncaya kadar SSPR için kaydolmaları engellenmiş olmasına neden olabilir.

### <a name="change-authentication-methods"></a>Kimlik doğrulama yöntemlerini Değiştir

Sıfırlama veya kilit açma için yalnızca bir gerekli kimlik doğrulama yöntemine sahip bir ilkeyle başlatırsanız ve bu, iki yönteme göre değişiklik yaparsanız, ne olur?

| Kayıtlı yöntemlerin sayısı | Gereken Yöntem sayısı | Sonuç |
| :---: | :---: | :---: |
| 1 veya daha fazla | 1 | **Sıfırlayabiliyor** veya kilit açıldı |
| 1 | 2 | Sıfırlanamıyor veya kilit **açılamıyor** |
| 2 veya daha fazla | 2 | **Sıfırlayabiliyor** veya kilit açıldı |

Kullanılabilir kimlik doğrulama yöntemlerinin değiştirilmesi, kullanıcılar için sorunlara da neden olabilir. Bir kullanıcının kullanabileceği kimlik doğrulama yöntemlerinin türlerini değiştirirseniz, kullanıcıların kullanılabilir en az miktarda veri yoksa SSPR 'yi kullanmalarını istemeden durdurabilirsiniz.

Aşağıdaki örnek senaryoyu göz önünde bulundurun:

1. Özgün ilke, gereken iki kimlik doğrulama yöntemi ile yapılandırılır. Yalnızca ofis telefon numarasını ve güvenlik sorularını kullanır.
1. Yönetici, ilkeyi artık güvenlik sorularını kullanmayacak şekilde değiştirir, ancak cep telefonu ve alternatif e-posta kullanımına izin verir.
1. Cep telefonu veya alternatif e-posta alanları olmayan kullanıcılar parolalarını sıfırlayamaz.

## <a name="notifications"></a>Bildirimler

SSPR, parola olaylarının farkında olması için hem kullanıcılar hem de kimlik yöneticileri için bildirimleri yapılandırmanıza olanak tanır.

### <a name="notify-users-on-password-resets"></a>Parola sıfırlamayı kullanıcılara bildir

Bu seçenek **Evet** olarak ayarlanırsa, parolasını sıfırlayan kullanıcılar, parolasının değiştiğini bildiren bir e-posta alır. E-posta, SSPR portalı aracılığıyla Azure AD 'de depolanan birincil ve alternatif e-posta adreslerine gönderilir. Sıfırlama olayının başka birine bildirim alınmaz.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Diğer yöneticiler parolalarını sıfırladıktan sonra tüm yöneticilere bildir

Bu seçenek **Evet** olarak ayarlanırsa, diğer tüm Azure yöneticileri, Azure AD 'de depolanan birincil e-posta adreslerine bir e-posta alır. E-posta, SSPR kullanarak başka bir yöneticinin parolalarını değiştirdiğinizi bildirir.

Aşağıdaki örnek senaryoyu göz önünde bulundurun:

* Bir ortamda dört yönetici vardır.
* Yönetici *A* , SSPR kullanarak parolalarını sıfırlar.
* Yöneticiler *B*, *C* ve *D* bir e-posta göndererek parola sıfırlamasının uyarı almasını ister.

## <a name="on-premises-integration"></a>Şirket içi tümleştirme

Karma ortamınız varsa, parola değiştirme olaylarını Azure AD 'den şirket içi bir dizine yazacak şekilde Azure AD Connect yapılandırabilirsiniz.

![Parola geri yazma özelliğinin etkin ve çalışır olduğu doğrulanıyor][Writeback]

Azure AD, geçerli karma bağlantınızı denetler ve Azure portal aşağıdaki iletilerden birini sağlar:

* Şirket içi geri yazma istemciniz çalışıyor ve çalışıyor.
* Azure AD çevrimiçi ve şirket içi geri yazma istemcinizi bağlamış. Ancak, Azure AD Connect yüklü sürümü güncel değil gibi görünüyor. En son bağlantı özelliklerine ve önemli hata düzeltmelerine sahip olduğunuzdan emin olmak için [Azure AD Connect yükseltmeyi](../hybrid/how-to-upgrade-previous-version.md) düşünün.
* Ne yazık ki, Azure AD Connect yüklü sürümü güncel olmadığından şirket içi geri yazma istemci durumunuzu denetleyemiyorum. Bağlantı durumunuzu denetlemek için [Azure AD Connect yükseltin](../hybrid/how-to-upgrade-previous-version.md) .
* Ne yazık ki, şu anda şirket içi geri yazma istemcimize bağlanamıyoruz. Bağlantıyı geri yüklemek için [Azure AD Connect sorunlarını giderin](./troubleshoot-sspr-writeback.md) .
* Ne yazık ki, parola geri yazma düzgün yapılandırılmadığından şirket içi geri yazma istemcinizi bağlayamıyoruz. Bağlantıyı geri yüklemek için [parola geri yazma 'Yı yapılandırın](./tutorial-enable-sspr-writeback.md) .
* Ne yazık ki, şu anda şirket içi geri yazma istemcimize bağlanamıyoruz. Bu, bizim sonunda geçici sorunlardan kaynaklanıyor olabilir. Sorun devam ederse, bağlantıyı geri yüklemek için [Azure AD Connect sorun giderin](./troubleshoot-sspr-writeback.md) .

SSPR geri yazma 'yı kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Self servis parola sıfırlama (SSPR) geri yazmayı etkinleştirme](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Parolaları şirket içi dizininize geri yazma

Azure portal kullanarak parola geri yazma özelliğini etkinleştirebilirsiniz. Ayrıca, Azure AD Connect yeniden yapılandırmak zorunda kalmadan parola geri yazma 'yı geçici olarak devre dışı bırakabilirsiniz.

* Seçenek **Evet** olarak ayarlanırsa, geri yazma etkinleştirilir. Federasyon, geçişli kimlik doğrulama veya parola karması eşitlenmiş kullanıcılar parolalarını sıfırlayabiliyor.
* Seçenek **Hayır** olarak ayarlanırsa, geri yazma devre dışı bırakılır. Federasyon, geçişli kimlik doğrulama veya parola karması eşitlenmiş kullanıcılar parolalarını sıfırlayamaz.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Kullanıcıların, parolasını sıfırlamadan hesapların kilidini açmalarına izin ver

Varsayılan olarak, Azure AD, parola sıfırlama gerçekleştirdiğinde hesapların kilidini açar. Esneklik sağlamak için kullanıcıların parolalarını sıfırlamasına gerek kalmadan şirket içi hesaplarının kilidini açmalarına izin vermeyi tercih edebilirsiniz. Bu iki işlemi ayırmak için bu ayarı kullanın.

* **Evet** olarak ayarlanırsa, kullanıcılara parolasını sıfırlama ve hesabın kilidini açma veya parolayı sıfırlamak zorunda kalmadan hesabının kilidini açma seçeneği verilir.
* **Hayır** olarak ayarlanırsa, kullanıcılar yalnızca bir birleştirilmiş parola sıfırlama ve hesap kilit açma işlemi gerçekleştirebilir.

### <a name="on-premises-active-directory-password-filters"></a>Şirket içi Active Directory parola filtreleri

SSPR Active Directory ' de yönetici tarafından başlatılan parola sıfırlamasının eşdeğerini uygular. Özel parola kurallarını zorlamak için bir üçüncü taraf parola filtresi kullanıyorsanız ve bu parola filtresinin Azure AD self servis parola sıfırlama sırasında denetlenmesi gerekiyorsa, üçüncü taraf parola filtresi çözümünün yönetici parola sıfırlama senaryosunda uygulanacak şekilde yapılandırıldığından emin olun. [Active Directory Domain Services Için Azure AD parola koruması](concept-password-ban-bad-on-premises.md) varsayılan olarak desteklenir.

## <a name="password-reset-for-b2b-users"></a>B2B kullanıcıları için parola sıfırlama

Parola sıfırlama ve değiştirme, tüm işletmeler arası (B2B) yapılandırmalarda tam olarak desteklenmektedir. B2B Kullanıcı parolası sıfırlama, aşağıdaki üç durumda desteklenir:

* **Mevcut bir Azure AD kiracısına sahip bir iş ortağı kuruluştan kullanıcılar**: ile iş ortağınız olan kuruluşun mevcut BIR Azure AD kiracısı varsa, bu kiracıda hangi parola sıfırlama ilkelerinin etkin olduğunu dikkate aldık. Parola sıfırlamanın çalışması için, iş ortağı kuruluşun yalnızca Azure AD SSPR 'nin etkinleştirildiğinden emin olması gerekir. Microsoft 365 müşterileri için ek ücret alınmaz.
* Self Servis kaydolma ile kaydolan **Kullanıcılar** : ile iş ortağınız olan kuruluş, bir kiracıya ulaşmak için [self servis kaydolma](../enterprise-users/directory-self-service-signup.md) özelliğini kullandıysanız, parolaları kayıtlı e-posta ile sıfırlayacağız.
* **B2B kullanıcıları**: yenı [Azure AD B2B özellikleri](../external-identities/what-is-b2b.md) KULLANıLARAK oluşturulan tüm yeni B2B kullanıcıları parolalarını, davet işlemi sırasında kayıtlı olan e-posta ile de sıfırlayabilir.

Bu senaryoyu test etmek için, https://passwordreset.microsoftonline.com Bu iş ortağı kullanıcılarından birine gidin. Alternatif bir e-posta veya kimlik doğrulama e-postası tanımlanmışsa, parola sıfırlama beklendiği gibi çalışmaktadır.

> [!NOTE]
> Hotmail.com, Outlook.com veya diğer kişisel e-posta adreslerinden gelen, Azure AD kiracınıza konuk erişimi verilen Microsoft hesapları Azure AD SSPR kullanamaz. Microsoft hesabı makalemize [oturum açabilmeniz](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) için içinde bulunan bilgileri kullanarak parolalarını sıfırlamaları gerekir.

## <a name="next-steps"></a>Sonraki adımlar

SSPR 'yi kullanmaya başlamak için aşağıdaki öğreticiyi izleyin:

> [!div class="nextstepaction"]
> [Öğretici: Self servis parola sıfırlamayı etkinleştirme (SSPR)](tutorial-enable-sspr.md)

Aşağıdaki makaleler, Azure AD aracılığıyla parola sıfırlama konusunda ek bilgiler sağlar:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Kullanılabilir Azure AD kimlik doğrulama yöntemleri ve gereken miktar"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Azure portal SSPR kayıt seçeneklerini yapılandırma"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Azure portal SSPR için şirket içi tümleştirme"
