---
title: Self servis parola sıfırlama hakkında SSS-Azure Active Directory
description: Azure AD self servis parola sıfırlama hakkında sık sorulan sorular
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0874f6aeb74b028e92e10d972757cc5971735e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744321"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Self servis parola sıfırlama sık sorulan sorular

Aşağıda, self servis parola sıfırlama ile ilgili tüm şeyler için sık sorulan bazı sorular (SSS) verilmiştir.

Burada yanıtlanmayan Azure Active Directory (Azure AD) ve self servis parola sıfırlama (SSPR) hakkında genel bir sorunuz varsa, [Azure Active Directory Için Microsoft Q&soru sayfası](/answers/topics/azure-active-directory.html)hakkında yardım almak için topluluğa soru sorabilirsiniz. Topluluk üyeleri; mühendisler, ürün yöneticileri, MVP 'ler ve BT uzmanlarına yönelik BT profesyonellerini içerir.

Bu SSS aşağıdaki bölümlere ayrılır:

* [Parola sıfırlama kaydı hakkında sorular](#password-reset-registration)
* [Parola sıfırlama ile ilgili sorular](#password-reset)
* [Parola değiştirme hakkında sorular](#password-change)
* [Parola yönetimi raporları hakkında sorular](#password-management-reports)
* [Parola geri yazma ile ilgili sorular](#password-writeback)

## <a name="password-reset-registration"></a>Parola sıfırlama kaydı

* **S: Kullanıcılarım kendi parola sıfırlama verilerini kaydedebilirler mi?**

  > **Y:** Evet. Parola sıfırlama etkin olduğu ve lisansladığı sürece, kullanıcılar, https://aka.ms/ssprsetup) kimlik doğrulama bilgilerini kaydetmek için parola sıfırlama kayıt portalına gidebilir. Kullanıcılar ayrıca erişim paneli ( https://myapps.microsoft.com) . Erişim paneli 'ne kaydolmak için profil resmini seçmesini, **profil**' i ve ardından **parola sıfırlama için kaydol** seçeneğini belirlemeniz gerekir.
  >
  > [Birleşik kaydı](concept-registration-mfa-sspr-combined.md)etkinleştirirseniz kullanıcılar her IKI SSPR ve Azure AD Multi-Factor Authentication için de aynı anda kayıt yapabilir.
* **S: bir grup için parola sıfırlamayı etkinleştirdiğimde ve Kullanıcılarım için izin verme izni varsa, Kullanıcılarım için yeniden kayıt gerekli midir?**

  > Y **:** Eşleşen. Kimlik doğrulama verilerinin doldurulduğu kullanıcıların yeniden kaydedilmesi gerekmez.
  >
  >
* **S: Kullanıcılarım adına parola sıfırlama verilerini tanımlayabilir miyim?**

  > Y **:** Evet, bunu Azure AD Connect, PowerShell, [Azure Portal](https://portal.azure.com)veya [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com)ile yapabilirsiniz. Daha fazla bilgi için bkz. [Azure AD self servis parola sıfırlama tarafından kullanılan veriler](howto-sspr-authenticationdata.md).
  >
  >
* **S: şirket içi güvenlik soruları için verileri eşitleyebilir miyim?**

  > Y **:** Hayır, bu, bugün mümkün değildir.
  >
  >
* **S: Kullanıcılarım, verileri diğer kullanıcıların göremez bu şekilde kaydedesin mi?**

  > **Y:** Evet. Kullanıcılar, parola sıfırlama kayıt portalını kullanarak veri kaydettirebilir, veriler yalnızca genel Yöneticiler ve Kullanıcı için görünür olan özel kimlik doğrulama alanlarına kaydedilir.
  >
  >
* **S: Kullanıcılarınızın parola sıfırlamayı kullanabilmesi için kaydolmaları gerekir mi?**

  > Y **:** Eşleşen. Adına yeterli kimlik doğrulama bilgileri tanımlarsanız, kullanıcıların kaydolması gerekmez. Parola sıfırlama, dizinde uygun alanlarda depolanan verileri doğru şekilde biçimlendirmiş olduğunuz sürece işe yarar.
  >
  >
* **S: kimlik doğrulama telefonunu, kimlik doğrulama e-postasını veya diğer kimlik doğrulama telefon alanlarını Kullanıcılarım adına eşitleyebilir veya ayarlayabilir miyim?**

  > Y **:** Genel yönetici tarafından ayarlanamayacak alanlar [SSPR veri gereksinimleri](howto-sspr-authenticationdata.md)makalesinde tanımlanmıştır.
  >
  >
* **S: kayıt portalı, Kullanıcılarımı hangi seçenekleri gösterecek şekilde nasıl belirlenir?**

  > Y **:** Parola sıfırlama kayıt portalı, yalnızca kullanıcılarınız için etkinleştirdiğiniz seçenekleri gösterir. Bu seçenekler, dizininizin **Yapılandır** sekmesinin **Kullanıcı parolası sıfırlama ilkesi** bölümünde bulunur. Örneğin, güvenlik sorularını etkinleştirmezseniz kullanıcılar bu seçeneğe kaydolayamaz.
  >
  >
* **S: Kullanıcı ne zaman kayıtlı olarak kabul edilir?**

  > Y **:** Bir Kullanıcı, [Azure Portal](https://portal.azure.com)ayarlamış olduğunuz bir parolayı **sıfırlamak Için gereken yöntem sayısını** en az kaydettiklerinde SSPR için kayıtlı olarak kabul edilir.
  >
  >

## <a name="password-reset"></a>Parola sıfırlama

* **S: kullanıcıların bir parolayı kısa bir süre içinde birden çok denemeden sıfırlamayı önleyin mi?**

  > Y **:** Evet, parola sıfırlamanın kötüye kullanılmasına karşı korunması için yerleşik güvenlik özellikleri vardır. 
  >
  > Kullanıcılar 24 saat boyunca kilitlenmeden önce, 24 saat içinde yalnızca beş parola sıfırlama denemesi deneyebilir. 
  >
  > Kullanıcılar bir telefon numarasını doğrulamaya, SMS gönderebilecek veya güvenlik sorularını ve yanıtlarını 24 saat boyunca kilitlerinden önce bir saat içinde yalnızca beş kez doğrulamaya çalışabilir. 
  >
  > Kullanıcılar, 24 saat boyunca kilitlenmeden önce 10 dakikalık bir süre içinde en fazla 10 kez e-posta gönderebilir.
  >
  > Kullanıcı parolasını sıfırladığında sayaçlar sıfırlanır.
  >
  >
* **S: parola sıfırlamalarından e-posta, SMS veya telefon araması almak için ne kadar beklemeliyim?**

  > Y **:** E-postaların, SMS iletilerinin ve telefon aramalarının bir dakika içinde gelmesi gerekir. Normal durum 5 ila 20 saniyedir.
  > Bu zaman çerçevesinde bildirim almazsanız:
  > * Önemsiz klasörünüzü denetleyin.
  > * İletişim kurulan numaranın veya e-postanın beklediğinizi doğrulayın.
  > * Dizindeki kimlik doğrulama verilerinin doğru biçimlendirildiğinden emin olun, örneğin + 1 4255551234 veya *kullanıcı \@ contoso.com*. 
* **S: parola sıfırlama hangi dilleri destekler?**

  > Y **:** Parola sıfırlama kullanıcı arabirimi, SMS iletileri ve sesli çağrılar, Microsoft 365 desteklenen dillerdeki aynı dillerde yerelleştirilir.
  >
  >
* **S: dizinimin Yapılandır sekmesindeki kurumsal marka öğelerini ayarladıktan sonra parola sıfırlama deneyiminin hangi bölümlerinin markalı olduğunu al?**

  > Y **:** Parola sıfırlama portalı, kuruluşunuzun logosunu gösterir ve özel bir e-posta veya URL 'yi işaret etmek için "yöneticinize başvurun" bağlantısını yapılandırmanıza olanak tanır. Parola sıfırlama tarafından gönderilen herhangi bir e-posta, kuruluşunuzun logosu, renkleri ve adını e-posta gövdesinde içerir ve bu belirli bir ada ait ayarlardan özelleştirilir.
  >
  >
* **S: kullanıcılarınızın parolalarını sıfırlamamak üzere nereden gidebileceği hakkında nasıl eğitirim?**

  > Y **:** [SSPR dağıtım](howto-sspr-deployment.md#plan-communications) makalemizden bazı önerilerden bazılarını deneyin.
  >
  >
* **S: Bu sayfayı bir mobil cihazdan kullanabilir miyim?**

  > Y **:** Evet, Bu sayfa mobil cihazlarda çalışmaktadır.
  >
  >
* **S: kullanıcılar parolalarını sıfırlarladıktan sonra yerel Active Directory hesaplarının kilidinin açılmasını destekliyor mu?**

  > **Y:** Evet. Kullanıcı parolasını sıfırladığında, parola geri yazma işlemi Azure AD Connect aracılığıyla dağıtılmışsa, bu kullanıcının hesabı, parolasını sıfırladığında otomatik olarak açılır.
  >
  >
* **S: parola sıfırlamayı doğrudan kullanıcının masaüstü oturum açma deneyimimde nasıl tümleştirebilirim?**

  > Y **:** Azure AD Premium müşteriyseniz, ek ücret ödemeden Microsoft Identity Manager yükleyebilir ve şirket içi parola sıfırlama çözümünü dağıtabilirsiniz.
  >
  >
* **S: farklı yerel ayarlar için farklı güvenlik soruları ayarlayabilir miyim?**

  > Y **:** Hayır, bu, bugün mümkün değildir.
  >
  >
* **S: güvenlik soruları kimlik doğrulama seçeneği için kaç soru yapılandırabilirim?**

  > Y **:** [Azure Portal](https://portal.azure.com)en fazla 20 özel güvenlik sorusu yapılandırabilirsiniz.
  >
  >
* **S: güvenlik sorularını ne kadar süreyle alabilir?**

  > Y **:** Güvenlik soruları 3 ile 200 karakter uzunluğunda olabilir.
  >
  >
* **S: güvenlik soruları için ne kadar yanıt olabilir?**

  > Y **:** Yanıtlar 3 ile 40 karakter uzunluğunda olabilir.
  >
  >
* **S: güvenlik soruları için yinelenen yanıtlar reddedildi mi?**

  > Y **:** Evet, güvenlik sorularına yinelenen yanıtları reddeyoruz.
  >
  >
* **S: Kullanıcı aynı güvenlik sorusunu birden çok kez kaydedebilir mi?**

  > Y **:** Eşleşen. Bir Kullanıcı belirli bir soruyu kaydettikten sonra, bu soruya ikinci kez kaydolamazlar.
  >
  >
* **S: kayıt ve sıfırlama için en düşük güvenlik soruları sınırını ayarlamak mümkün mü?**

  > Y **:** Evet, bir sınır kayıt için, diğeri de sıfırlama için ayarlanabilir. Kayıt için üç ila beş güvenlik sorusu gerekli olabilir ve sıfırlama için üç ila beş soru gerekebilir.
  >
  >
* **S: ilkeden, kullanıcıların sıfırlamaya yönelik güvenlik sorularını kullanmasını zorunlu kılmak için sitemi yapılandırdım, ancak Azure yöneticileri farklı şekilde yapılandırılmış gibi görünüyor.**

  > Y **:** Bu beklenen davranıştır. Microsoft, tüm Azure yöneticisi rolleri için varsayılan olarak güçlü ve iki aşamalı parola sıfırlama ilkesi uygular. Bu, yöneticilerin güvenlik sorularını kullanmasını önler. Bu ilkeyle ilgili daha fazla bilgiyi, [Azure Active Directory makalesinde parola ilkeleri ve kısıtlamalarında](concept-sspr-policy.md) bulabilirsiniz.
  >
  >
* **S: bir Kullanıcı sıfırlamak için gereken en fazla soru sayısından daha fazla kaydolmışsa, sıfırlama sırasında güvenlik soruları nasıl seçilidir?**

  > **A:** *n* güvenlik sorusu sayısı, bir kullanıcının kaydolduğu toplam soru sayısından (burada *n* , sıfırlama seçeneği için **gereken soruların sayısı** için ayarlanan miktar) rastgele seçilir. Örneğin, bir Kullanıcı beş güvenlik sorusu kaydettirirse, ancak parolayı sıfırlamak için yalnızca üçü gerekliyse, beş sorudan üçü rastgele seçilir ve sıfırlama sırasında sunulur. Kullanıcı, soru-cevap işlemini engellemek için seçim işleminin yeniden başladığı yanlış soruların yanıtlarını alırsa.
  >
  >
* **S: e-posta ve SMS tek seferlik geçiş kodlarını ne kadar süreyle geçerlidir?**

  > Y **:** Parola sıfırlama için oturum ömrü 15 dakikadır. Parola sıfırlama işleminin başından, kullanıcının parolasını sıfırlaması 15 dakika sürer. Parola sıfırlama oturumu sırasında e-posta ve SMS One-saat geçiş kodu 5 dakika için geçerlidir.
  >
  >
* **S: kullanıcıların parolalarını sıfırlamalarını engelleyebilir miyim?**

  > Y **:** Evet, SSPR 'yi etkinleştirmek için bir grup kullanıyorsanız, kullanıcıların parolalarını sıfırlamasına olanak tanıyan tek bir kullanıcıyı gruptan çıkarabilirsiniz. Kullanıcı genel yöneticisidir, parolasını sıfırlama özelliğini korur ve bu devre dışı bırakılamaz.
  >
  >

## <a name="password-change"></a>Parola değiştirme

* **S: Kullanıcılarım parolalarını değiştirmek için nereye gitmelidir?**

  > Y **:** Kullanıcılar, [Office 365](https://portal.office.com) portalının veya [erişim paneli](https://myapps.microsoft.com) deneyimlerinin sağ üst köşesinde olduğu gibi, kendi parolalarını kendi profil resmini veya simgesini görürler her yerde değiştirebilir. Kullanıcılar, [erişim paneli profili sayfasından](https://account.activedirectory.windowsazure.com/r#/profile)parolalarını değiştirebilir. Kullanıcıların parolalarının geçerliliği dolmuşsa Azure AD oturum açma sayfasında parolalarını otomatik olarak değiştirmesi istenebilir. Son olarak, kullanıcılar parolalarını değiştirmek istediklerinde [Azure AD parola değiştirme portalına](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) doğrudan gözatabilirler.
  >
  >
* **S: şirket içi parolasının süresi dolarsa Kullanıcılarım Office portalında bilgilendirilir mi?**

  > Y **:** Evet, bu, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanıyorsanız bugün mümkündür. AD FS kullanıyorsanız, [parola ilkesi taleplerini AD FS Ile gönderme](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) makalesindeki yönergeleri izleyin. Parola karması eşitlemesi kullanıyorsanız, bu, bugün mümkün değildir. Şirket içi dizinlerden parola ilkelerini eşitliyoruz, bu nedenle bulut deneyimlerine süre sonu bildirimleri göndermemiz mümkün değildir. Her iki durumda da, [parolalarının, PowerShell aracılığıyla kullanım süresini dolacak olan kullanıcılara bildirimde bulunur](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **S: kullanıcıların parolalarını değiştirmelerini engelleyebilir miyim?**

  > Y **:** Yalnızca bulutta bulunan kullanıcılar için parola değişiklikleri engellenemez. Şirket içi kullanıcılar için, **Kullanıcı parolayı değiştiremez** seçeneğini belirleyebilirsiniz. Seçilen kullanıcılar parolalarını değiştiremezler.
  >
  >

## <a name="password-management-reports"></a>Parola yönetimi raporları

* **S: parola yönetim raporlarında verilerin gösterilmesi ne kadar sürer?**

  > Y **:** Veriler, parola yönetimi raporlarında 5 ila 10 dakika içinde görünmelidir. Bazı örneklerde, görünmesi bir saat kadar sürebilir.
  >
  >
* **S: parola yönetimi raporlarını nasıl filtreleyebilirim?**

  > Y **:** Parola yönetimi raporlarını filtrelemek için, raporun üst kısmına yakın bir şekilde sütun etiketlerinin Extreme 'e kadar küçük Büyüteç Camı ' nı seçin. Daha zengin filtreleme yapmak istiyorsanız, raporu Excel 'e indirebilir ve Pivot tablosu oluşturabilirsiniz.
  >
  >
* **S: parola yönetimi raporlarında depolanan en fazla olay sayısı nedir?**

  > Y **:** En çok 75.000 parola sıfırlama veya parola sıfırlama kayıt olayları, parola yönetimi raporlarında, 30 güne kadar geri yayılmaktadır. Bu numarayı daha fazla olay içerecek şekilde genişletmek için çalışıyoruz.
  >
  >
* **S: parola yönetimi raporları ne kadar geri getirilir?**

  > Y **:** Parola yönetimi raporları, son 30 gün içinde gerçekleşen işlemleri gösterir. Şimdilik, bu verileri arşivlemek gerekirse, raporları düzenli aralıklarla indirebilir ve ayrı bir konuma kaydedebilirsiniz.
  >
  >
* **S: parola yönetimi raporlarında görünebilen en fazla satır sayısı var mı?**

  > **Y:** Evet. Parola Yönetim raporlarında, Kullanıcı arabiriminde gösterildiklerinde veya indirilip indirilmediğinden, en fazla 75.000 satır görünebilir.
  >
  >
* **S: parola sıfırlama veya kayıt raporlama verilerine erişmek için bir API var mı?**

  > **Y:** Evet. Parola sıfırlama raporlama verilerine nasıl erişebileceğinizi öğrenmek için bkz. [Azure Log Analytics REST API başvurusu](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Parola geri yazma

* **S: parola geri yazma, arka planda nasıl çalışır?**

  > Y **:** Parola geri yazma özelliğini etkinleştirdiğinizde ve verilerin sistem aracılığıyla şirket içi ortamınıza geri nasıl akacağını öğrenmek için [parola geri yazma özelliğinin nasıl çalıştığını](./tutorial-enable-sspr-writeback.md) öğrenin.
  >
  >
* **S: parola geri yazma ne kadar sürer? Parola karması eşitlemesine benzer bir eşitleme gecikmesi var mı?**

  > Y **:** Parola geri yazma işlemi anında. Bu, Parola karması eşitlemesine göre temelde farklı şekilde çalışır olan zaman uyumlu bir ardışık düzen. Parola geri yazma, kullanıcıların parola sıfırlama veya değiştirme işleminin başarısı hakkında gerçek zamanlı geri bildirim almasına izin verir. Parolanın başarılı bir şekilde geri yazma işlemi için Ortalama süre 500 MS altındadır.
  >
  >
* **S: şirket içi Hesabım devre dışıysa, bulut hesabım ve erişim nasıl etkilenir?**

  > Y **:** Şirket içi KIMLIĞINIZ devre dışıysa, bulut KIMLIĞINIZ ve erişim, Azure AD Connect aracılığıyla bir sonraki eşitleme aralığında da devre dışı bırakılır. Varsayılan olarak, bu eşitleme 30 dakikada bir olur.
  >
  >
* **S: şirket içi Hesabım, şirket içi Active Directory parola ilkesi tarafından kısıtlanmamışsa, parolamı değiştirdiğimde Bu ilkeyi SSPR 'ye uyar mi?**

  > Y **:** Evet, SSPR şirket içi Active Directory parola ilkesiyle ilgili ve bunları kullanır. Bu ilke, bir kullanıcıya hedeflenmiş, tanımlı ve hassas parola ilkelerinin yanı sıra tipik Active Directory etki alanı parola ilkesini de içerir.
  >
  >
* **S: parola geri yazma ne tür hesaplar çalışır?**

  > Y **:** Parola geri yazma, şirket içi Active Directory, Federasyon, Parola karması eşitlenmiş ve Pass-Through kimlik doğrulama kullanıcıları dahil olmak üzere Azure AD 'ye eşitlenen Kullanıcı hesapları için geçerlidir.
  >
  >
* **S: parola geri yazma, etki alanım 'ın parola ilkeleriyle zorlanır mi?**

  > **Y:** Evet. Parola geri yazma, parola yaşı, geçmiş, karmaşıklık, filtreler ve yerel etki alanındaki parolalara göre yerine koyabileceğiniz diğer tüm kısıtlamaları uygular.
  >
  >
* **S: parola geri yazma güvenli mi?  Korsanın izinsiz sürmemesi için nasıl emin olabilirim?**

  > Y **:** Evet, parola geri yazma güvenli. Parola geri yazma hizmeti tarafından uygulanan birden çok güvenlik katmanı hakkında daha fazla bilgi edinmek için [parola geri yazma genel bakış](./tutorial-enable-sspr-writeback.md) makalesindeki [parola geri yazma güvenliği](concept-sspr-writeback.md#password-writeback-security) bölümüne bakın.
  >
  >

## <a name="next-steps"></a>Sonraki adımlar

* [SSPR’yi başarılı bir şekilde nasıl piyasaya çıkarabilirim?](howto-sspr-deployment.md)
* [Parolanızı sıfırlama veya değiştirme](../user-help/active-directory-passwords-update-your-own-password.md)
* [Self servis parola sıfırlama için kaydolma](../user-help/active-directory-passwords-reset-register.md)
* [Lisansla ilgili bir sorunuz mu var?](concept-sspr-licensing.md)
* [SSPR hangi verileri kullanır ve kullanıcılarınız için hangi verileri doldurmanız gerekir?](howto-sspr-authenticationdata.md)
* [Kullanıcılar hangi kimlik doğrulama yöntemlerini kullanabilir?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR ile kullanılabilen ilke seçenekleri nelerdir?](concept-sspr-policy.md)
* [Parola geri yazma nedir ve neden önemlidir?](./tutorial-enable-sspr-writeback.md)
* [SSPR’de etkinliği nasıl bildirebilirim?](howto-sspr-reporting.md)
* [SSPR’deki tüm seçenekler nelerdir ve ne anlama gelir?](concept-sspr-howitworks.md)
* [Bir şeyin bozuk olduğunu düşünüyorum. SSPR sorunlarını gidermek Nasıl yaparım??](./troubleshoot-sspr.md)