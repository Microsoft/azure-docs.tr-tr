---
title: Self servis parola sıfırlama sorunlarını giderme-Azure Active Directory
description: Azure Active Directory içinde self servis parola sıfırlaması için sık karşılaşılan sorunları ve çözüm adımlarını nasıl giderebileceğinizi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dee21182349108c44f9d498417d3b760ac4913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103600871"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Azure Active Directory içinde self servis parola sıfırlama sorunlarını giderme

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR), kullanıcıların buluttaki parolalarını sıfırlamasına olanak sağlar.

SSPR ile ilgili sorunlar yaşıyorsanız, aşağıdaki sorun giderme adımları ve sık karşılaşılan hatalar yardımcı olabilir. Bu kısa videoyu, [en yaygın altı SSPR Son Kullanıcı hata Iletisini nasıl çözebileceği](https://www.youtube.com/watch?v=9RPrNVLzT8I&list=PL3ZTgFEc7LyuS8615yo39LtXR7j1GCerW&index=1)hakkında da izleyebilirsiniz.

Sorununuz için yanıt bulamazsanız, destek ekiplerimiz size daha fazla yardımcı olmak için [her zaman kullanılabilir](#contact-microsoft-support) .

## <a name="sspr-configuration-in-the-azure-portal"></a>Azure portal SSPR yapılandırması

Azure portal SSPR seçeneklerini görmeye veya yapılandırmaya yönelik sorunlar yaşıyorsanız, aşağıdaki sorun giderme adımlarını gözden geçirin:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Azure portal Azure AD altında **parola sıfırlama** bölümünü görmüyorum.

Bu işlemi gerçekleştirmek için bir Azure AD lisansınız yoksa, **parola sıfırlama** menü seçeneğini göremezsiniz.

Söz konusu yönetici hesabına bir lisans atamak için, [lisanslarla ilgili sorunları atama, doğrulama ve çözme](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)adımlarını izleyin.

### <a name="i-dont-see-a-particular-configuration-option"></a>Belirli bir yapılandırma seçeneğini görmüyorum.

Kullanıcı arabirimindeki birçok öğe, gerekene kadar gizlenir. Belirli yapılandırma seçeneklerine bakmadan önce seçeneğinin etkinleştirildiğinden emin olun.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>**Şirket içi tümleştirme** sekmesini görmüyorum.

Şirket içi parola geri yazma yalnızca Azure AD Connect indirdiyseniz ve özelliği yapılandırdıysanız görünür.

Daha fazla bilgi için bkz. [Azure AD Connect kullanmaya](../hybrid/how-to-connect-install-express.md)başlama.

## <a name="sspr-reporting"></a>SSPR raporlama

Azure portal SSPR raporlama ile ilgili sorunlar yaşıyorsanız, aşağıdaki sorun giderme adımlarını gözden geçirin:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>**Self servis parola yönetimi** denetim olayı kategorisinde herhangi bir parola yönetimi etkinlik türü görmüyorum.

Bu, işlemi gerçekleştiren yöneticiye atanmış bir Azure AD lisansınız yoksa oluşabilir.

Söz konusu yönetici hesabına bir lisans atamak için, [lisanslarla ilgili sorunları atama, doğrulama ve çözme](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)adımlarını izleyin.

### <a name="user-registrations-show-multiple-times"></a>Kullanıcı kayıtları birden çok kez gösterir.

Bir Kullanıcı kaydettiğinde, ayrı bir olay olarak kaydedilen her bir veri parçasını Şu anda günlüğe kaydettik.

Bu verileri toplamak ve nasıl görüntüleyebileceğiniz konusunda daha fazla esneklik sağlamak istiyorsanız, raporu indirebilir ve verileri Excel 'de Özet Tablo olarak açabilirsiniz.

## <a name="sspr-registration-portal"></a>SSPR kayıt portalı

Kullanıcılarınızın SSPR için kayıt sorunları varsa, aşağıdaki sorun giderme adımlarını gözden geçirin:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Parola sıfırlama için Dizin etkinleştirilmemiş. Kullanıcı rapor veren bir hata görebilir, "yöneticiniz bu özelliği kullanmak için etkinleştirmedi."

SSPR 'yi tüm kullanıcılar, Kullanıcı veya seçili kullanıcı grupları için etkinleştirebilirsiniz. Şu anda yalnızca bir Azure AD grubu Azure portal kullanılarak SSPR için etkinleştirilebilir. SSPR 'nin daha geniş dağıtımının bir parçası olarak, iç içe gruplar desteklenir. Seçtiğiniz gruptaki (ler) kullanıcılara uygun lisansların atandığından emin olun.

Azure portal, **self servis parola sıfırlama etkin** yapılandırmasını *Seçili* veya *Tümü* olarak değiştirin ve ardından **Kaydet**' i seçin.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Kullanıcıya atanmış bir Azure AD lisansı yok. Kullanıcı rapor veren bir hata görebilir, "yöneticiniz bu özelliği kullanmak için etkinleştirmedi."

Şu anda yalnızca bir Azure AD grubu Azure portal kullanılarak SSPR için etkinleştirilebilir. SSPR 'nin daha geniş dağıtımının bir parçası olarak, iç içe gruplar desteklenir. Seçtiğiniz gruptaki (ler) kullanıcılara uygun lisansların atandığından emin olun. Bir önceki sorun giderme adımını inceleyerek SSPR 'yi gerektiği şekilde etkinleştirin.

Ayrıca, yapılandırma seçeneklerini gerçekleştiren yöneticinin atanmış bir lisansı olduğundan emin olmak için sorun giderme adımlarını gözden geçirin. Söz konusu yönetici hesabına bir lisans atamak için, [lisanslarla ilgili sorunları atama, doğrulama ve çözme](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)adımlarını izleyin.

### <a name="theres-an-error-processing-the-request"></a>İstek işlenirken bir hata oluştu.

Genel SSPR kayıt hatalarına birçok sorun neden olabilir, ancak genellikle bu hata bir hizmet kesintisi veya yapılandırma sorunu nedeniyle oluşur. SSPR kayıt işlemini yeniden denediğinizde bu genel hatayı görmeye devam ederseniz, ek yardım için [Microsoft desteği 'ne başvurun](#contact-microsoft-support) .

## <a name="sspr-usage"></a>SSPR kullanımı

Siz veya kullanıcılarınız SSPR kullanarak sorun yaşıyorsanız, aşağıdaki sorun giderme senaryolarını ve çözüm adımlarını gözden geçirin:

| Hata | Çözüm |
| --- | --- |
| Parola sıfırlama için Dizin etkinleştirilmemiş. | Azure portal, **self servis parola sıfırlama etkin** yapılandırmasını *Seçili* veya *Tümü* olarak değiştirin ve ardından **Kaydet**' i seçin. |
| Kullanıcıya atanmış bir Azure AD lisansı yok. | İstenen kullanıcıya atanmış bir Azure AD lisansınız yoksa bu durum oluşabilir. Söz konusu yönetici hesabına bir lisans atamak için, [lisanslarla ilgili sorunları atama, doğrulama ve çözme](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)adımlarını izleyin. |
| Dizin, parola sıfırlama için etkinleştirildi, ancak Kullanıcı eksik veya hatalı biçimlendirilmiş kimlik doğrulama bilgilerine sahip. | Kullanıcının dizindeki dosya üzerinde doğru şekilde oluşturulmuş iletişim verilerine sahip olduğundan emin olun. Daha fazla bilgi için bkz. [Azure AD self servis parola sıfırlama tarafından kullanılan veriler](howto-sspr-authenticationdata.md). |
| Dizin, parola sıfırlama için etkinleştirilir, ancak ilke iki doğrulama yöntemi gerektirecek şekilde ayarlandığında, kullanıcının dosya üzerinde yalnızca bir kişi veri parçası vardır. | Kullanıcının en az iki doğru yapılandırılmış iletişim yöntemine sahip olduğundan emin olun. Bir örnek, hem cep telefonu numarası *hem* de ofis telefon numarası vardır. |
| Dizin, parola sıfırlama için etkinleştirilir ve Kullanıcı düzgün şekilde yapılandırılır, ancak kullanıcıya ulaşılamıyor. | Bu, geçici bir hizmet hatasının sonucu veya düzgün algılayamayacağı yanlış iletişim verileri olabilir. <br> <br> Kullanıcı 10 saniye bekliyorsa, "yeniden dene" ve "yöneticinize başvurun" şeklinde bir bağlantı görüntülenir. Kullanıcı "yeniden dene" seçeneğini seçerse çağrıyı yeniden dener. Kullanıcı "yöneticinize başvurun" seçeneğini seçerse, bu kullanıcı hesabı için bir parola sıfırlama isteğinde bulunan yöneticilere bir form e-postası gönderir. |
| Kullanıcı hiçbir şekilde parola sıfırlama SMS veya telefon çağrısını almaz. | Bu, dizinde hatalı biçimlendirilmiş telefon numarasının sonucu olabilir. Telefon numarasının "+ 1 4251234567" biçiminde olduğundan emin olun. <br> <br>Parola sıfırlama, dizinde bir tane belirtseniz bile uzantıları desteklemez. Bu uzantılar, çağrı yapılmadan önce çıkarılır. Uzantısı olmayan bir sayı kullanın veya uzantıyı özel şube alışverişi (PBX) içindeki telefon numarasıyla tümleştirin. |
| Kullanıcı hiçbir şekilde parola sıfırlama e-postasını almaz. | Bu sorunun en yaygın nedeni, iletinin bir istenmeyen posta Filtresi tarafından reddedildiği bir sorundur. E-posta için istenmeyen posta, istenmeyen veya silinmiş öğeler klasörünüzü denetleyin. <br> <br> Ayrıca, kullanıcının SSPR ile kayıtlı olarak doğru e-posta hesabını denetlediğinizden emin olun. |
| Bir parola sıfırlama ilkesi ayarlandım, ancak bir yönetici hesabı parola sıfırlama kullandığında, bu ilke uygulanmaz. | Microsoft, en yüksek düzeyde güvenlik sağlamak için yönetici parola sıfırlama ilkesini yönetir ve denetler. |
| Kullanıcının bir günde çok fazla kez parola sıfırlamayı denemesi engellenir. | Bir otomatik daraltma mekanizması, kullanıcıların parolalarını kısa bir süre içinde çok fazla kez sıfırlamasına engel olmak için kullanılır. Kısıtlama aşağıdaki senaryolar meydana gelir: <br><ul><li>Kullanıcı bir saatte beş kez bir telefon numarasını doğrulamaya çalışır.</li><li>Kullanıcı, bir saatte beş kez güvenlik soruları geçidini kullanmaya çalışır.</li><li>Kullanıcı aynı kullanıcı hesabı için bir parolayı bir saatte beş kez sıfırlamaya çalışır.</li></ul>Bir Kullanıcı bu sorunla karşılaşırsa, son denemeden sonra 24 saat beklemeniz gerekir. Kullanıcı daha sonra parolalarını sıfırlayabilir. |
| Kullanıcı telefon numaralarını doğrularken bir hata görür. | Bu hata, girilen telefon numarası dosyadaki telefon numarasıyla eşleşmediği zaman oluşur. Kullanıcının, parola sıfırlama için telefon tabanlı bir yöntem kullanmaya çalıştıklarında, alan ve ülke kodu dahil olmak üzere tam telefon numarasını girdiğinden emin olun. |
| İstek işlenirken bir hata oluştu. | Genel SSPR kayıt hatalarına birçok sorun neden olabilir, ancak genellikle bu hata bir hizmet kesintisi veya yapılandırma sorunu nedeniyle oluşur. SSPR kayıt işlemini yeniden denediğinizde bu genel hatayı görmeye devam ederseniz, ek yardım için [Microsoft desteği 'ne başvurun](#contact-microsoft-support) . |
| Şirket içi ilke ihlali | Parola, şirket içi Active Directory parola ilkesini karşılamıyor. Kullanıcı karmaşıklık veya güç gereksinimlerini karşılayan bir parola tanımlamalıdır. |
| Parola, benzer ilkeyle uyumlu değil | Kullanılan parola [yasaklanmış parola listesinde](./concept-password-ban-bad.md#how-are-passwords-evaluated) görünür ve kullanılamaz. Kullanıcı yasaklanmış parola listesi ilkesini karşılayan veya aşan bir parola tanımlamalıdır. |

## <a name="sspr-errors-that-a-user-might-see"></a>Kullanıcının görebileceği SSPR hataları

Aşağıdaki hatalar ve teknik ayrıntılar, bir kullanıcıya SSPR sürecinin bir parçası olarak gösterilmeyebilir. Genellikle, SSPR özelliğinin hesabı için etkinleştirilmesi, yapılandırılması veya kaydettirilmesi gerektiğinden, hata kendilerini çözebilecekleri bir şey değildir.

Sorunu ve Azure AD kiracısında veya bireysel kullanıcı hesabında düzeltilmesi gereken sorunları anlamak için aşağıdaki bilgileri kullanın.

| Hata | Ayrıntılar | Teknik ayrıntılar |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Maalesef yöneticiniz kuruluşunuz için parola sıfırlamayı devre dışı bırakmış olduğundan, şu anda parolanızı sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve bu özelliği etkinleştirmesini isteyin.<br /><br />Daha fazla bilgi edinmek için bkz. [Yardım, Azure AD Parolamı unuttum](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: parola sıfırlamanın yöneticiniz tarafından etkinleştirilmemiş olduğunu algıladık. Lütfen yöneticinizle iletişime geçin ve kuruluşunuzun parola sıfırlamasını etkinleştirmesini isteyin. |
| WritebackNotEnabled = 10 |Ne yazık ki yöneticiniz kuruluşunuz için gerekli bir hizmeti etkinleştirmediği için parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve kuruluşunuzun yapılandırmasını denetlemesini isteyin.<br /><br />Bu gerekli hizmet hakkında daha fazla bilgi edinmek için bkz. [parola geri yazmayı yapılandırma](./tutorial-enable-sspr-writeback.md). | SSPR_0010: parola geri yazma özelliğinin etkinleştirilmemiş olduğunu algıladık. Lütfen yöneticinize başvurun ve parola geri yazma özelliğini etkinleştirmesini isteyin. |
| Ssprnotenabledınuserpolicy = 11 | Ne yazık ki yöneticiniz kuruluşunuz için parola sıfırlama yapılandırmadığı için parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Yöneticinizle iletişime geçerek parola sıfırlamayı yapılandırmasını isteyin.<br /><br />Parola sıfırlama yapılandırması hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: Azure AD self servis parola sıfırlama](./tutorial-enable-sspr.md). | SSPR_0011: Kuruluşunuz bir parola sıfırlama ilkesi tanımlayamıyor. Lütfen yöneticinize başvurun ve bir parola sıfırlama ilkesi tanımlamasını isteyin. |
| Usernotlisanslanmış = 12 | Ne yazık ki, gerekli lisanslar kuruluşunuzda olmadığından parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinize başvurarak lisans atamasını denetlemesini isteyin.<br /><br />Lisanslama hakkında daha fazla bilgi edinmek için bkz. [Azure AD self servis parola sıfırlama Için lisans gereksinimleri](./concept-sspr-licensing.md). | SSPR_0012: kuruluşunuzda, parola sıfırlama işlemini gerçekleştirmek için gerekli lisanslar yok. Lütfen yöneticinize başvurun ve lisans atamalarını gözden geçirmesini isteyin. |
| UserNotMemberOfScopedAccessGroup = 13 | Maalesef yöneticiniz hesabınızı parola sıfırlamayı kullanacak şekilde yapılandırmadığından, şu anda parolanızı sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçerek parolanızı parola sıfırlama için yapılandırmasını isteyin.<br /><br />Parola sıfırlama için hesap yapılandırması hakkında daha fazla bilgi için bkz. [kullanıcılar için parola sıfırlamayı kullanıma alma](./howto-sspr-deployment.md). | SSPR_0013: parola sıfırlama için etkinleştirilen bir grubun üyesi değilsiniz. Yöneticinizle iletişime geçin ve gruba ekleme isteğinizi yapın. |
| UserNotProperlyConfigured = 14 | Ne yazık ki hesabınızda gerekli bilgiler eksik olduğu için parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve parolanızı sıfırlamasını isteyin. Hesabınıza yeniden erişduktan sonra gerekli bilgileri kaydetmeniz gerekir.<br /><br />Bilgileri kaydetmek için, [self servis parola sıfırlama Için kaydolun](../user-help/active-directory-passwords-reset-register.md) makalesindeki adımları izleyin. | SSPR_0014: parolanızı sıfırlamak için ek güvenlik bilgileri gerekir. Devam etmek için yöneticinize başvurun ve parolanızı sıfırlamasını isteyin. Hesabınıza erişeceğinizden sonra ek güvenlik bilgilerini adresinden kaydedebilirsiniz https://aka.ms/ssprsetup . Yöneticiniz, [parola sıfırlama için kimlik doğrulama verilerini ayarlama ve okuma](howto-sspr-authenticationdata.md)bölümündeki adımları izleyerek hesabınıza ek güvenlik bilgileri ekleyebilir. |
| OnPremisesAdminActionRequired = 29 | Ne yazık ki kuruluşunuzun parola sıfırlama yapılandırmasıyla ilgili bir sorun nedeniyle parolanızı Şu anda sıfırlayamıyoruz. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve araştırmasını isteyin. <br /><br />Veya<br /><br />Kuruluşunuzun parola sıfırlama yapılandırmasıyla ilgili bir sorun olduğundan şu anda parolanızı sıfırlayamıyoruz. Bu sorunu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve araştırmasını isteyin.<br /><br />Olası sorun hakkında daha fazla bilgi edinmek için bkz. [parola geri yazma sorunlarını giderme](troubleshoot-sspr-writeback.md). | SSPR_0029: şirket içi yapılandırmanızda bir hata nedeniyle parolanızı sıfırlayamıyoruz. Lütfen yöneticinizle iletişime geçin ve araştırmasını isteyin. |
| OnPremisesConnectivityError = 30 | Ne yazık ki, kuruluşunuzda bağlantı sorunları nedeniyle parolanızı Şu anda sıfırlayamıyoruz. Şu anda yapılacak bir eylem yok, ancak daha sonra yeniden denerseniz sorun çözülebilir. Sorun devam ederse lütfen yöneticinizle iletişime geçin ve araştırmasını isteyin.<br /><br />Bağlantı sorunları hakkında daha fazla bilgi edinmek için bkz. [parola geri yazma bağlantısı sorunlarını giderme](troubleshoot-sspr-writeback.md). | SSPR_0030: şirket içi ortamınıza kötü bir bağlantı nedeniyle parolanızı sıfırlayamıyoruz. Yöneticinizle iletişime geçin ve araştırmasını isteyin.|

## <a name="azure-ad-forums"></a>Azure AD forumları

Azure AD ve self servis parola sıfırlama hakkında genel sorularınız varsa, [Azure Active Directory Için Microsoft Q&soru sayfasında](/answers/topics/azure-active-directory.html)topluluğa yardım isteyebilirsiniz. Topluluk üyeleri; mühendisler, ürün yöneticileri, MVP 'ler ve BT uzmanlarına yönelik BT profesyonellerini içerir.

## <a name="contact-microsoft-support"></a>Microsoft desteğine başvurma

Bir sorunun yanıtını bulamazsanız, destek ekiplerimiz size daha fazla yardımcı olmak için her zaman kullanılabilir.

Size uygun bir şekilde yardımcı olması için, bir servis talebi açarken mümkün olduğunca fazla ayrıntı sağlamanızı isteyeceğiz. Bu ayrıntılar şunları içerir:

* **Hatanın genel açıklaması**: hata nedir? Fark edilen davranış nedir? Hatayı nasıl yeniden oluşturuyoruz? Mümkün olduğunca çok ayrıntı sağlayın.
* **Sayfa**: hatayı fark ettiğinizde hangi sayfayı kullanıyorsunuz? Mümkün olduğunda URL 'YI ve sayfanın ekran görüntüsünü ekleyin.
* **Destek kodu**: Kullanıcı hatayı gördüğünüzde oluşturulan destek kodu nedir?
   * Bu kodu bulmak için, hatayı yeniden oluşturun ve ardından ekranın alt kısmındaki **destek kodu** bağlantısını seçin ve destek mühendisine sonuçları veren GUID 'yi gönderin.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Destek kodu, Web tarayıcı penceresinin sağ alt kısmında bulunur.":::

  * En altta destek kodu olmayan bir sayfadan karşılaşırsanız, F12 ' yi seçin ve SID ve CıD için arama yapın ve bu iki sonucu destek mühendisine gönderin.
* **Tarih, saat ve saat dilimi**: Hatanın gerçekleştiği *saat dilimiyle* kesin tarih ve saati dahil edin.
* **Kullanıcı kimliği**: hatayı seçen kullanıcı kim? *Kullanıcı \@ contoso.com* bir örnektir.
   * Bu bir Federasyon kullanıcısı mı?
   * Bu bir geçişli kimlik doğrulama kullanıcısı mı?
   * Bu, Parola karması ile eşitlenen bir Kullanıcı mı?
   * Bu yalnızca bulut kullanıcısı mı?
* **Lisanslama**: kullanıcıya BIR Azure AD lisansı atandı mi?
* **Uygulama olay günlüğü**: parola geri yazma özelliğini kullanıyorsanız ve hata şirket içi altyapınızda ise, Azure AD Connect sunucusundan uygulama olay günlüğlerinizin daraltılmış bir kopyasını ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

SSPR hakkında daha fazla bilgi edinmek için bkz [. nasıl çalışır: Azure AD self servis parola sıfırlama](concept-sspr-howitworks.md) veya [self servis parola sıfırlama geri yazma özelliği Azure AD 'de nasıl çalışır?](concept-sspr-writeback.md).
