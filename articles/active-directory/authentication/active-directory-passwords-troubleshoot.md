---
title: Self servis parola sıfırlama sorunlarını giderme-Azure Active Directory
description: Azure AD self servis parola sıfırlama sorunlarını giderme
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 003ceb19fafade4972ebb0cf4e60ceda34dc1928
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893455"
---
# <a name="troubleshoot-self-service-password-reset"></a>Self servis parola sıfırlama sorunlarını giderme

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR) ile ilgili bir sorun mu yaşıyorsunuz? Aşağıdaki bilgiler, yeniden çalışan işlemleri almanıza yardımcı olabilir.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Kullanıcının görebileceği self servis parola sıfırlama hatalarıyla ilgili sorunları giderme

| Hata | Ayrıntılar | Teknik ayrıntılar |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Maalesef yöneticiniz kuruluşunuz için parola sıfırlamayı devre dışı bırakmış olduğundan, şu anda parolanızı sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve bu özelliği etkinleştirmesini isteyin. Daha fazla bilgi edinmek için bkz. [Yardım, Azure AD Parolamı unuttum](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: parola sıfırlamanın yöneticiniz tarafından etkinleştirilmemiş olduğunu algıladık. Lütfen yöneticinizle iletişime geçin ve kuruluşunuzun parola sıfırlamasını etkinleştirmesini isteyin. |
| WritebackNotEnabled = 10 |Ne yazık ki yöneticiniz kuruluşunuz için gerekli bir hizmeti etkinleştirmediği için parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve kuruluşunuzun yapılandırmasını denetlemesini isteyin. Bu gerekli hizmet hakkında daha fazla bilgi edinmek için bkz. [parola geri yazmayı yapılandırma](howto-sspr-writeback.md). | SSPR_0010: parola geri yazma özelliğinin etkinleştirilmemiş olduğunu algıladık. Lütfen yöneticinize başvurun ve parola geri yazma özelliğini etkinleştirmesini isteyin. |
| Ssprnotenabledınuserpolicy = 11 | Ne yazık ki yöneticiniz kuruluşunuz için parola sıfırlama yapılandırmadığı için parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Yöneticinizle iletişime geçerek parola sıfırlamayı yapılandırmasını isteyin. Parola sıfırlama yapılandırması hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: Azure AD self servis parola sıfırlama](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Kuruluşunuz bir parola sıfırlama ilkesi tanımmadı. Lütfen yöneticinize başvurun ve bir parola sıfırlama ilkesi tanımlamasını isteyin. |
| Usernotlisanslanmış = 12 | Ne yazık ki, gerekli lisanslar kuruluşunuzda olmadığından parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinize başvurarak lisans atamasını denetlemesini isteyin. Lisanslama hakkında daha fazla bilgi edinmek için bkz. [Azure AD self servis parola sıfırlama Için lisans gereksinimleri](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Kuruluşunuz, parola sıfırlama işlemini gerçekleştirmek için gerekli olan lisanslara sahip değil. Lütfen yöneticinize başvurun ve lisans atamalarını gözden geçirmesini isteyin. |
| UserNotMemberOfScopedAccessGroup = 13 | Maalesef yöneticiniz hesabınızı parola sıfırlamayı kullanacak şekilde yapılandırmadığından, şu anda parolanızı sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçerek parolanızı parola sıfırlama için yapılandırmasını isteyin. Parola sıfırlama için hesap yapılandırması hakkında daha fazla bilgi için bkz. [kullanıcılar için parola sıfırlamayı kullanıma alma](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: parola sıfırlama için etkinleştirilen bir grubun üyesi değilsiniz. Yöneticinizle iletişime geçin ve gruba ekleme isteğinizi yapın. |
| UserNotProperlyConfigured = 14 | Ne yazık ki hesabınızda gerekli bilgiler eksik olduğu için parolanızı Şu anda sıfırlayamazsınız. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve parolanızı sıfırlamasını isteyin. Hesabınıza yeniden erişduktan sonra gerekli bilgileri kaydetmeniz gerekir. Bilgileri kaydetmek için, [self servis parola sıfırlama Için kaydolun](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) makalesindeki adımları izleyin. | SSPR_0014: parolanızı sıfırlamak için ek güvenlik bilgileri gerekir. Devam etmek için yöneticinize başvurun ve parolanızı sıfırlamasını isteyin. Hesabınıza erişiminizden sonra, https://aka.ms/ssprsetup ' a ek güvenlik bilgileri kaydedebilirsiniz. Yöneticiniz, [parola sıfırlama için kimlik doğrulama verilerini ayarlama ve okuma](howto-sspr-authenticationdata.md)bölümündeki adımları izleyerek hesabınıza ek güvenlik bilgileri ekleyebilir. |
| OnPremisesAdminActionRequired = 29 | Ne yazık ki kuruluşunuzun parola sıfırlama yapılandırmasıyla ilgili bir sorun nedeniyle parolanızı Şu anda sıfırlayamıyoruz. Bu durumu çözmek için gerçekleştirebileceğiniz başka bir eylem yoktur. Lütfen yöneticinizle iletişime geçin ve araştırmasını isteyin. Olası sorun hakkında daha fazla bilgi edinmek için bkz. [parola geri yazma sorunlarını giderme](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: şirket içi yapılandırmanızda bir hata nedeniyle parolanızı sıfırlayamıyoruz. Lütfen yöneticinizle iletişime geçin ve araştırmasını isteyin. |
| OnPremisesConnectivityError = 30 | Ne yazık ki, kuruluşunuzda bağlantı sorunları nedeniyle parolanızı Şu anda sıfırlayamıyoruz. Şu anda yapılacak bir eylem yok, ancak daha sonra yeniden denerseniz sorun çözülebilir. Sorun devam ederse lütfen yöneticinizle iletişime geçin ve araştırmasını isteyin. Bağlantı sorunları hakkında daha fazla bilgi edinmek için bkz. [parola geri yazma bağlantısı sorunlarını giderme](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: şirket içi ortamınıza kötü bir bağlantı nedeniyle parolanızı sıfırlayamıyoruz. Yöneticinizle iletişime geçin ve araştırmasını isteyin.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Azure portal parola sıfırlama yapılandırmasında sorun giderme

| Hata | Çözüm |
| --- | --- |
| Azure portal Azure AD altında **parola sıfırlama** bölümünü görmüyorum. | Bu, işlemi gerçekleştiren yöneticiye atanmış bir Azure AD lisansınız yoksa oluşabilir. <br> <br> Söz konusu yönetici hesabına bir lisans atayın. [Lisanslar ile sorunları atama, doğrulama ve çözme](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) makalesindeki adımları izleyebilirsiniz.|
| Belirli bir yapılandırma seçeneğini görmüyorum. | Kullanıcı arabirimindeki birçok öğe, gerekene kadar gizlenir. Görmek istediğiniz tüm seçenekleri etkinleştirmeyi deneyin. |
| **Şirket içi tümleştirme** sekmesini görmüyorum. | Bu seçenek yalnızca Azure AD Connect indirdiyseniz ve parola geri yazma 'yı yapılandırdıktan sonra görünür hale gelir. Daha fazla bilgi için bkz. [hızlı ayarları kullanarak Azure AD Connect](../hybrid/how-to-connect-install-express.md)kullanmaya başlama. |

## <a name="troubleshoot-password-reset-reporting"></a>Parola sıfırlama raporlaması sorunlarını giderme

| Hata | Çözüm |
| --- | --- |
| **Self servis parola yönetimi** denetim olayı kategorisinde herhangi bir parola yönetimi etkinlik türü görmüyorum. | Bu, işlemi gerçekleştiren yöneticiye atanmış bir Azure AD lisansınız yoksa oluşabilir. <br> <br> Bu sorunu, söz konusu yönetici hesabına bir lisans atayarak çözebilirsiniz. [Lisanslar ile sorunları atama, doğrulama ve çözme](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) makalesindeki adımları izleyin. |
| Kullanıcı kayıtları birden çok kez gösterir. | Şu anda, bir Kullanıcı kaydettiğinde ayrı bir olay olarak kaydedilen her bir veri parçasını günlüğe kaydettik. <br> <br> Bu verileri toplamak ve nasıl görüntüleyebileceğiniz konusunda daha fazla esneklik sağlamak istiyorsanız, raporu indirebilir ve verileri Excel 'de Özet Tablo olarak açabilirsiniz.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Parola sıfırlama kayıt portalı sorunlarını giderme

| Hata | Çözüm |
| --- | --- |
| Dizin, parola sıfırlama için etkinleştirilmemiş. **Yöneticiniz bu özelliği kullanmayı etkinleştirmedi.** | **Self servis parola sıfırlama etkin** bayrağını **Seçili** veya **Tümü** olarak değiştirin ve ardından **Kaydet**' i seçin. |
| Kullanıcıya atanmış bir Azure AD lisansı yok. **Yöneticiniz bu özelliği kullanmayı etkinleştirmedi.** | Bu, işlemi gerçekleştiren yöneticiye atanmış bir Azure AD lisansınız yoksa oluşabilir. <br> <br> Bu sorunu, söz konusu yönetici hesabına bir lisans atayarak çözebilirsiniz. [Lisanslar ile sorunları atama, doğrulama ve çözme](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) makalesindeki adımları izleyin.|
| İstek işlenirken bir hata oluştu. | Bunun nedeni birçok sorun olabilir, ancak genellikle bu hata bir hizmet kesintisi ya da bir yapılandırma sorunudur. Bu hatayı görürseniz ve işinizi etkiliyorsa, ek yardım için Microsoft desteği 'ne başvurun. |

## <a name="troubleshoot-the-password-reset-portal"></a>Parola sıfırlama portalında sorun giderme

| Hata | Çözüm |
| --- | --- |
| Dizin, parola sıfırlama için etkinleştirilmemiş. | **Self servis parola sıfırlama etkin** bayrağını **Seçili** veya **Tümü** olarak değiştirin ve ardından **Kaydet**' i seçin. |
| Kullanıcıya atanmış bir Azure AD lisansı yok. | Bu, işlemi gerçekleştiren yöneticiye atanmış bir Azure AD lisansınız yoksa oluşabilir. <br> <br> Bu sorunu, söz konusu yönetici hesabına bir lisans atarsanız çözebilirsiniz. [Lisanslar ile sorunları atama, doğrulama ve çözme](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) makalesindeki adımları izleyin. |
| Dizin, parola sıfırlama için etkinleştirildi, ancak Kullanıcı eksik veya hatalı biçimlendirilmiş kimlik doğrulama bilgilerine sahip. | Devam etmeden önce, kullanıcının dizindeki dosya üzerinde doğru şekilde oluşturulmuş iletişim verileri olduğundan emin olun. Daha fazla bilgi için bkz. [Azure AD self servis parola sıfırlama tarafından kullanılan veriler](howto-sspr-authenticationdata.md). |
| Dizin, parola sıfırlama için etkinleştirilir, ancak ilke iki doğrulama yöntemi gerektirecek şekilde ayarlandığında, kullanıcının dosya üzerinde yalnızca bir kişi veri parçası vardır. | Devam etmeden önce, kullanıcının en az iki doğru yapılandırılmış iletişim yöntemine sahip olduğundan emin olun. Bir örnek, hem cep telefonu numarası *hem* de ofis telefon numarası vardır. |
| Dizin, parola sıfırlama için etkinleştirilir ve Kullanıcı düzgün şekilde yapılandırılır, ancak kullanıcıya ulaşılamıyor. | Bu, geçici bir hizmet hatasının sonucu veya düzgün algılayamayacağı yanlış iletişim verileri olması olabilir. <br> <br> Kullanıcı 10 saniye bekliyorsa, "yeniden dene" ve "yöneticinize başvurun" bağlantıları görüntülenir. Kullanıcı "yeniden dene" seçeneğini seçerse çağrıyı yeniden dener. Kullanıcı "yöneticinize başvurun" seçeneğini seçerse, yöneticilerine bu kullanıcı hesabı için parola sıfırlama isteğinde bulunan bir form e-postası gönderir. |
| Kullanıcı hiçbir şekilde parola sıfırlama SMS veya telefon çağrısını almaz. | Bu, dizinde hatalı biçimlendirilmiş telefon numarasının sonucu olabilir. Telefon numarasının "+ CCC Xxxyyyızzzzxeeee" biçiminde olduğundan emin olun. <br> <br> Parola sıfırlama, dizinde bir tane belirtseniz bile uzantıları desteklemez. Bu uzantılar, çağrı dağıtılmadan önce çıkarılır. Uzantısı olmayan bir sayı kullanın veya uzantıyı özel şube alışverişi (PBX) içindeki telefon numarası ile tümleştirin. |
| Kullanıcı hiçbir şekilde parola sıfırlama e-postasını almaz. | Bu sorunun en yaygın nedeni, iletinin bir istenmeyen posta Filtresi tarafından reddedildiği bir sorundur. E-posta için istenmeyen posta, istenmeyen veya silinmiş öğeler klasörünüzü denetleyin. <br> <br> Ayrıca, ileti için doğru e-posta hesabını denetdiğinizden emin olun. |
| Parola sıfırlama ilkesi ayarlandım, ancak bir yönetici hesabı parola sıfırlama kullandığında, bu ilke uygulanmaz. | Microsoft, en yüksek düzeyde güvenlik sağlamak için yönetici parola sıfırlama ilkesini yönetir ve denetler. |
| Kullanıcının bir günde çok fazla kez parola sıfırlamayı denemesi engellenir. | Kullanıcıların parolalarını kısa bir süre içinde çok fazla kez sıfırlamasına engel olmak için otomatik bir daraltma mekanizması uyguladık. Daraltma şu durumlarda oluşur: <br><ul><li>Kullanıcı bir saatte beş kez bir telefon numarasını doğrulamaya çalışır.</li><li>Kullanıcı, bir saatte beş kez güvenlik soruları geçidini kullanmaya çalışır.</li><li>Kullanıcı aynı kullanıcı hesabı için bir parolayı bir saatte beş kez sıfırlamaya çalışır.</li></ul>Bu sorunu gidermek için, kullanıcıdan son denemeden sonra 24 saat beklemesini isteyin. Kullanıcı daha sonra parolalarını sıfırlayabilir. |
| Kullanıcı telefon numaralarını doğrularken bir hata görür. | Girilen telefon numarası dosyadaki telefon numarasıyla eşleşmezse bu hata oluşur. Kullanıcının, parola sıfırlama için telefon tabanlı bir yöntem kullanmaya çalıştıklarında, alan ve ülke kodu dahil olmak üzere tam telefon numarasını girdiğinden emin olun. |
| İstek işlenirken bir hata oluştu. | Bunun nedeni birçok sorun olabilir, ancak genellikle bu hata bir hizmet kesintisi ya da bir yapılandırma sorunudur. Bu hatayı görürseniz ve işinizi etkiliyorsa, ek yardım için Microsoft desteği 'ne başvurun. |
| Şirket içi ilke ihlali | Parola, şirket içi Active Directory parola ilkesini karşılamıyor. |
| Parola, benzer ilkeye uymuyor | Kullanılan parola [yasaklanmış parola listesinde](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) görünür ve kullanılmıyor olabilir. |

## <a name="troubleshoot-password-writeback"></a>Parola geri yazma sorunlarını giderme

| Hata | Çözüm |
| --- | --- |
| Parola sıfırlama hizmeti şirket içinde başlamıyor. Hata 6800 Azure AD Connect makinenin uygulama olay günlüğünde görüntülenir. <br> <br> Ekleme, Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen kullanıcılar parolalarını sıfırlayamaz. | Parola geri yazma etkinleştirildiğinde, eşitleme altyapısı, bulut ekleme hizmetine iletişim kurarak yapılandırmayı (ekleme) gerçekleştirmek için geri yazma kitaplığını çağırır. Parola geri yazma sırasında veya Windows Communication Foundation (WCF) uç noktası başlatılırken karşılaşılan hatalar, Azure AD Connect makinenizde olay günlüğündeki hatalara neden olur. <br> <br> Azure AD Eşitleme (ADSync) hizmetinin yeniden başlatılması sırasında, geri yazma yapılandırıldıysa, WCF uç noktası başlatılır. Ancak, uç noktanın başlatılması başarısız olursa, olay 6800 ' i günlüğe kaydeder ve eşitleme hizmetinin başlamasını sağlar. Bu olayın varlığı, parola geri yazma uç noktasının başlamamasıdır. Bu olay 6800 için olay günlüğü ayrıntıları, PasswordResetService bileşeni tarafından oluşturulacak olay günlüğü girişleriyle birlikte, uç noktayı neden başlatamadığını gösterir. Bu olay günlüğü hatalarını gözden geçirin ve parola geri yazma hala çalışmıyorsa Azure AD Connect yeniden başlatmayı deneyin. Sorun devam ederse, parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.
| Kullanıcı parolayı sıfırlamayı veya parola geri yazma etkinken bir hesabın kilidini açmaya çalıştığında, işlem başarısız olur. <br> <br> Ayrıca, şunu içeren Azure AD Connect olay günlüğünde bir olay görürsünüz: "eşitleme altyapısı bir hata döndürdü HR = 800700CE, ileti = dosya adı veya uzantı çok uzun", kilit açma işlemi oluştuktan sonra. | Azure AD Connect için Active Directory hesabı bulun ve parolayı en fazla 256 karakter içerecek şekilde sıfırlayın. Ardından, **Başlat** menüsünden **eşitleme hizmetini** açın. **Bağlayıcılar** ' a gidin ve **Active Directory bağlayıcısını**bulun. Seçin ve ardından **Özellikler**' i seçin. **Kimlik bilgileri** sayfasına gidin ve yeni parolayı girin. Sayfayı kapatmak için **Tamam ' ı** seçin. |
| Azure AD Connect yükleme işleminin son adımında, parola geri yazma özelliğinin yapılandırılamadığını belirten bir hata görürsünüz. <br> <br> Azure AD Connect uygulama olay günlüğü, "Auth Token alma hatası" metnini içeren 32009 hatasını içerir. | Bu hata aşağıdaki iki durumda oluşur: <br><ul><li>Azure AD Connect yükleme işleminin başlangıcında belirtilen genel yönetici hesabı için yanlış bir parola belirttiniz.</li><li>Azure AD Connect yükleme işleminin başlangıcında belirtilen genel yönetici hesabı için bir Federasyon kullanıcısı kullanmaya çalıştınız.</li></ul> Bu sorunu gidermek için, yükleme işleminin başlangıcında belirttiğiniz genel yönetici için bir federal hesap kullandığınızdan emin olun. Ayrıca, belirtilen parolanın doğru olduğundan emin olun. |
| Azure AD Connect makine olay günlüğü, PasswordResetService çalıştırılarak oluşturulan 32002 hatasını içerir. <br> <br> Hata şunu okur: "ServiceBus 'a bağlanılırken hata oluştu. Belirteç sağlayıcısı bir güvenlik belirteci sağlayamadı. " | Şirket içi ortamınız, Bulutta Azure Service Bus uç noktasına bağlanamaz. Bu hata normalde, belirli bir bağlantı noktasına veya web adresine giden bağlantıyı engelleyen bir güvenlik duvarı kuralı nedeniyle oluşur. Daha fazla bilgi için bkz. [bağlantı önkoşulları](../hybrid/how-to-connect-install-prerequisites.md) . Bu kuralları güncelleştirdikten sonra, Azure AD Connect makineyi yeniden başlatın ve parola geri yazma işlemi yeniden çalışmaya başlaması gerekir. |
| Bir süredir çalıştıktan sonra, Federasyon, geçişli kimlik doğrulaması veya parola karması ile eşitlenen kullanıcılar parolalarını sıfırlayamaz. | Bazı ender durumlarda, parola geri yazma hizmeti Azure AD Connect yeniden başlatıldığında yeniden başlatılamaz. Bu durumlarda, ilk olarak parola geri yazmanın şirket içinde etkin görünüp başlatılmayacağını kontrol edin. Azure AD Connect Sihirbazı 'nı veya PowerShell 'i kullanarak kontrol edebilirsiniz (önceki HowTos bölümüne bakın). Özellik etkin görünüyorsa, özelliği kullanıcı arabirimi veya PowerShell aracılığıyla yeniden etkinleştirmeyi veya devre dışı bırakmayı deneyin. Bu işe yaramazsa, Azure AD Connect bir kaldırma ve yeniden yükleme işlemini deneyin. |
| Federal, geçişli kimlik doğrulaması veya parola karması eşitlenmiş kullanıcılar parolalarını sıfırlamaya çalışan bir hata görür. Hata, bir hizmet sorunu olduğunu gösterir. <br ><br> Bu soruna ek olarak, parola sıfırlama işlemleri sırasında, yönetim aracısının şirket içi olay günlüklerinizin erişimini reddettiğini belirten bir hata görebilirsiniz. | Olay günlüğünde bu hataları görürseniz, yapılandırma sırasında sihirbazda belirtilen Active Directory Yönetim Aracısı (ADMA) hesabının parola geri yazma için gerekli izinlere sahip olduğunu doğrulayın. <br> <br> Bu izin verildiğinde, etki alanı denetleyicisindeki (DC) `sdprop` arka plan görevi aracılığıyla bir süre aşağı doğru bir saat sürebilir. <br> <br> Parola sıfırlamanın çalışması için, bu iznin, parolası sıfırlanan Kullanıcı nesnesinin güvenlik tanımlayıcısına damgalı olması gerekir. Bu izin Kullanıcı nesnesinde gösterilene kadar, parola sıfırlama, erişim reddedildi iletisiyle başarısız olmaya devam eder. |
| Federal, geçişli kimlik doğrulaması veya parolalarını sıfırlamaya çalışan Parola karması ile eşitlenen kullanıcılar, parolasını gönderdikten sonra bir hata oluştu. Hata, bir hizmet sorunu olduğunu gösterir. <br> <br> Bu soruna ek olarak, parola sıfırlama işlemleri sırasında, bir "nesne bulunamadı" hatasını belirten Azure AD Connect hizmetinden Olay günlüklerinizi bir hata görebilirsiniz. | Bu hata genellikle eşitleme altyapısının Azure AD bağlayıcı alanında Kullanıcı nesnesini veya bağlantılı meta veri deposu (MV) ya da Azure AD bağlayıcı alanı nesnesini bulamadığını gösterir. <br> <br> Bu sorunu gidermek için, kullanıcının geçerli Azure AD Connect örneği aracılığıyla Şirket içinden Azure AD 'ye eşitlendiğinden emin olun ve bağlayıcı uzayları ve MV içindeki nesnelerin durumunu inceleyin. Active Directory Sertifika Hizmetleri (AD CS) nesnesinin, "Microsoft.InfromADUserAccountEnabled.xxx" kuralı aracılığıyla MV nesnesine bağlı olduğunu onaylayın.|
| Federal, geçişli kimlik doğrulaması veya parola karması ile eşitlenmiş kullanıcılar parolalarını gönderdikten sonra bir hata görür. Hata, bir hizmet sorunu olduğunu gösterir. <br> <br> Bu soruna ek olarak, parola sıfırlama işlemleri sırasında, Azure AD Connect hizmetinden bir "çoklu eşleşme bulundu" hatası olduğunu belirten Olay günlüklerinizi bir hata görebilirsiniz. | Bu, eşitleme altyapısının, "Microsoft.InfromADUserAccountEnabled.xxx" aracılığıyla birden fazla AD CS nesnesine bağlandığını algıladığını belirtir. Bu, kullanıcının birden fazla ormanda etkin bir hesabı olduğu anlamına gelir. Bu senaryo parola geri yazma için desteklenmez. |
| Parola işlemleri bir yapılandırma hatasıyla başarısız oluyor. Uygulama olay günlüğü, "0x8023061f (Bu yönetim aracısında parola eşitleme etkin olmadığından işlem başarısız oldu)" metniyle birlikte Azure AD Connect hatası 6329 içeriyor. | Parola geri yazma özelliği zaten etkinleştirildikten sonra, Azure AD Connect yapılandırması yeni bir Active Directory ormanı eklemek (veya mevcut bir ormanı kaldırmak ve yeniden eklemek) için değiştirilirse bu hata oluşur. Bu son eklenen ormanlardaki kullanıcılar için parola işlemleri başarısız olur. Sorunu gidermek için, orman yapılandırma değişiklikleri tamamlandıktan sonra parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirin. |

## <a name="password-writeback-event-log-error-codes"></a>Parola geri yazma olayı-günlük hata kodları

Parola geri yazma ile ilgili sorunları giderirken en iyi yöntem, Azure AD Connect makinenizde uygulama olay günlüğü 'nü incelemenizi kullanmaktır. Bu olay günlüğü, parola geri yazma için iki ilgi kaynaklı olay içerir. PasswordResetService kaynağı, parola geri yazma işlemiyle ilgili işlemleri ve sorunları açıklar. ADSync kaynağı, Active Directory ortamınızda parola ayarlamayla ilgili işlemleri ve sorunları açıklar.

### <a name="if-the-source-of-the-event-is-adsync"></a>Olayın kaynağı ADSync ise

| Kodlayın | Ad veya ileti | Açıklama |
| --- | --- | --- |
| 6329 | BAıL: MMS (4924) 0x80230619: "bir kısıtlama, parolanın belirtilen geçerli bir şekilde değiştirilmesini engelliyor." | Bu olay, parola geri yazma hizmeti yerel dizininizde, etki alanının parola yaşı, geçmişi, karmaşıklığı veya filtreleme gereksinimlerini karşılamayan bir parola ayarlamaya çalıştığında oluşur. <br> <br> En az bir parola yaşı varsa ve kısa süre önce bu zaman içindeki parolayı değiştirdiyseniz, etki alanındaki belirli bir yaşa ulaşıncaya kadar parolayı tekrar değiştiremezsiniz. Sınama amacıyla, minimum yaş 0 olarak ayarlanmalıdır. <br> <br> Parola geçmişi gereksinimleriniz etkinse, son *n* kez kullanılmamış bir parola seçmeniz gerekir, burada *n* parola geçmişi ayarıdır. Son *N* kez kullanılan bir parola seçerseniz, bu durumda bir hata görürsünüz. Sınama amacıyla parola geçmişi 0 olarak ayarlanmalıdır. <br> <br> Parola karmaşıklığı gereksinimleriniz varsa, Kullanıcı bir parolayı değiştirmeye veya sıfırlamaya çalıştığında bunların hepsi zorlanır. <br> <br> Parola Filtreleriniz etkinse ve Kullanıcı filtreleme ölçütlerine uymayan bir parola seçerse, sıfırlama veya değiştirme işlemi başarısız olur. |
| 6329 | MMS (3040): admaexport. cpp (2837): sunucu LDAP parola ilkesi denetimini içermez. | DC 'lerde LDAP_SERVER_POLICY_HINTS_OID Control (1.2.840.113556.1.4.2066) etkinleştirilmemişse bu sorun oluşur. Parola geri yazma özelliğini kullanmak için, denetimi etkinleştirmeniz gerekir. Bunu yapmak için, DC 'Ler Windows Server 2008R2 veya üzeri sürümlerde olmalıdır. |
| HR 8023042 | Eşitleme altyapısı bir hata döndürdü HR = 80230402, ileti = aynı bağlayıcı ile yinelenen girdiler bulunduğundan bir nesne alınamadı. | Birden çok etki alanında aynı kullanıcı KIMLIĞI etkinleştirildiğinde bu hata oluşur. Bu örnek, hesap ve kaynak ormanlarını eşitliyoruz ve aynı kullanıcı KIMLIĞININ her ormanda mevcut ve etkinleştirilmiş olduğunu bir örnektir. <br> <br> Bu hata, diğer ad veya UPN gibi benzersiz olmayan bir tutturucu özniteliği kullanırsanız ve iki kullanıcı aynı bağlayıcı özniteliğini paylaşıyorsa da oluşabilir. <br> <br> Bu sorunu çözmek için, etki alanlarınızda yinelenen bir kullanıcınız olmadığından ve her kullanıcı için benzersiz bir tutturucu özniteliği kullandığınızdan emin olun. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Olayın kaynağı PasswordResetService ise

| Kodlayın | Ad veya ileti | Açıklama |
| --- | --- | --- |
| 31001 | PasswordResetStart | Bu olay, şirket içi hizmetin, buluttan kaynaklanan bir Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen Kullanıcı için bir parola sıfırlama isteği algıladığını gösterir. Bu olay, her parola sıfırlama geri yazma işlemindeki ilk olaydır. |
| 31002 | PasswordResetSuccess | Bu olay, bir kullanıcının parola sıfırlama işlemi sırasında yeni bir parola seçtiği anlamına gelir. Bu parolanın, kurumsal parola gereksinimlerini karşıladığını belirledik. Parola, yerel Active Directory ortamına başarıyla geri yazıldı. |
| 31003 | PasswordResetFail | Bu olay, bir kullanıcının bir parolayı seçtiği ve parolanın şirket içi ortama başarıyla ulaştığını gösterir. Ancak, yerel Active Directory ortamında parolayı ayarlamaya çalışırken bir hata oluştu. Bu hata çeşitli nedenlerden kaynaklanabilir: <br><ul><li>Kullanıcının parolası, etki alanı için yaş, geçmiş, karmaşıklık veya filtre gereksinimlerini karşılamıyor. Bu sorunu çözmek için yeni bir parola oluşturun.</li><li>ADMA hizmet hesabı, söz konusu Kullanıcı hesabında yeni parolayı ayarlamak için uygun izinlere sahip değil.</li><li>Kullanıcının hesabı, parola ayarlama işlemlerine izin vermeyen etki alanı veya kuruluş Yöneticisi grubu gibi korumalı bir grupta bulunur.</li></ul>|
| 31004 | OnboardingEventStart | Bu olay, Azure AD Connect ile parola geri yazma özelliğini etkinleştirirseniz ve kuruluşunuzu parola geri yazma Web hizmetine eklemeyi başladığımızda oluşur. |
| 31005 | OnboardingEventSuccess | Bu olay, ekleme işleminin başarılı olduğunu ve parola geri yazma özelliğinin kullanıma hazırlandığını gösterir. |
| 31006 | ChangePasswordStart | Bu olay, şirket içi hizmetin, buluttan kaynaklanan bir Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen Kullanıcı için bir parola değişiklik isteği algıladığını gösterir. Bu olay, her parola değiştirme geri yazma işlemindeki ilk olaydır. |
| 31007 | ChangePasswordSuccess | Bu olay, bir kullanıcının parola değiştirme işlemi sırasında yeni bir parola seçtiği anlamına gelir; parolanın kurumsal parola gereksinimlerini karşıladığını ve parolanın yerel Active Directory ortamına başarıyla geri yazıldığını belirledik. |
| 31008 | ChangePasswordFail | Bu olay, bir kullanıcının bir parolayı seçtiği ve parolanın şirket içi ortama başarıyla ulaştığını gösterir, ancak yerel Active Directory ortamında parolayı ayarlamaya çalıştık bir hata oluştu. Bu hata çeşitli nedenlerden kaynaklanabilir: <br><ul><li>Kullanıcının parolası, etki alanı için yaş, geçmiş, karmaşıklık veya filtre gereksinimlerini karşılamıyor. Bu sorunu çözmek için yeni bir parola oluşturun.</li><li>ADMA hizmet hesabı, söz konusu Kullanıcı hesabında yeni parolayı ayarlamak için uygun izinlere sahip değil.</li><li>Kullanıcının hesabı, etki alanı veya kuruluş yöneticileri gibi, parola ayarlama işlemlerine izin vermeyen korumalı bir grupta bulunur.</li></ul> |
| 31009 | Resetuserpasswordbyadmınstart | Şirket içi hizmet, bir kullanıcı adına yöneticiden kaynaklanan bir Federasyon, geçişli kimlik doğrulama veya parola karması ile eşitlenen Kullanıcı için parola sıfırlama isteği algıladı. Bu olay, bir yönetici tarafından başlatılan her parola sıfırlama geri yazma işlemindeki ilk olaydır. |
| 31010 | ResetUserPasswordByAdminSuccess | Yönetici, bir yönetici tarafından başlatılan parola sıfırlama işlemi sırasında yeni bir parola seçti. Bu parolanın, kurumsal parola gereksinimlerini karşıladığını belirledik. Parola, yerel Active Directory ortamına başarıyla geri yazıldı. |
| 31011 | ResetUserPasswordByAdminFail | Yönetici Kullanıcı adına bir parola seçti. Parola, şirket içi ortama başarıyla ulaştı. Ancak, yerel Active Directory ortamında parolayı ayarlamaya çalışırken bir hata oluştu. Bu hata çeşitli nedenlerden kaynaklanabilir: <br><ul><li>Kullanıcının parolası, etki alanı için yaş, geçmiş, karmaşıklık veya filtre gereksinimlerini karşılamıyor. Bu sorunu çözmek için yeni bir parola deneyin.</li><li>ADMA hizmet hesabı, söz konusu Kullanıcı hesabında yeni parolayı ayarlamak için uygun izinlere sahip değil.</li><li>Kullanıcının hesabı, etki alanı veya kuruluş yöneticileri gibi, parola ayarlama işlemlerine izin vermeyen korumalı bir grupta bulunur.</li></ul>  |
| 31012 | OffboardingEventStart | Bu olay, Azure AD Connect ile parola geri yazma özelliğini devre dışı bıraktığınızda oluşur ve kuruluşunuzu parola geri yazma Web hizmetine boşaltdığımızda olduğunu gösterir. |
| 31013| OffboardingEventSuccess| Bu olay, çıkarma işleminin başarılı olduğunu ve parola geri yazma özelliğinin başarıyla devre dışı bırakıldığını gösterir. |
| 31014| OffboardingEventFail| Bu olay, çıkarma işleminin başarısız olduğunu gösterir. Bunun nedeni, yapılandırma sırasında belirtilen bulutta veya şirket içi yönetici hesabında bir izin hatası olabilir. Bu hata, parola geri yazma özelliğini devre dışı bırakırken bir Federasyon bulutu genel Yöneticisi kullanmaya çalışıyorsanız da oluşabilir. Bu sorunu gidermek için, yönetim izinlerinizi denetleyin ve parola geri yazma özelliğini yapılandırırken bir Federasyon hesabı kullanmadığınız emin olun.|
| 31015| WriteBackServiceStarted| Bu olay parola geri yazma hizmetinin başarıyla başlatıldığını gösterir. Buluttan parola yönetim isteklerini kabul etmeye hazırlanıyor.|
| 31016| Writebackservicestomış| Bu olay parola geri yazma hizmetinin durdurulduğunu gösterir. Buluttan gelen parola yönetim istekleri başarılı olmayacaktır.|
| 31017| AuthTokenSuccess| Bu olay, oluşturma veya ekleme işlemini başlatmak üzere Azure AD Connect kurulumu sırasında belirtilen genel yönetici için bir yetkilendirme belirtecini başarıyla aldığını gösterir.|
| 31018| KeyPairCreationSuccess| Bu olay parola şifreleme anahtarını başarıyla oluşturduğumuz anlamına gelir. Bu anahtar, şirket içi ortamınıza gönderilmek üzere buluttan parolaları şifrelemek için kullanılır.|
| 32000| UnknownError| Bu olay, parola yönetimi işlemi sırasında bilinmeyen bir hata oluştuğunu gösterir. Daha fazla ayrıntı için olaydaki özel durum metnine bakın. Sorun yaşıyorsanız, parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin. Bu yardım yoksa, etkinlik günlüğlerinizin bir kopyasını, Destek mühendisinize belirtilen izleme KIMLIĞIYLE birlikte ekleyin.|
| 32001| ServiceError| Bu olay, bulut parola sıfırlama hizmetine bağlanılırken bir hata olduğunu gösterir. Bu hata genellikle şirket içi hizmet, parola sıfırlama Web hizmetine bağlanamadığı zaman oluşur.|
| 32002| ServiceBusError| Bu olay, kiracının Service Bus örneğine bağlanılırken bir hata olduğunu gösterir. Şirket içi ortamınızda giden bağlantıları engelliyorsanız bu durum oluşabilir. TCP 443 üzerinden bağlantılara izin verildiğinden ve https://ssprdedicatedsbprodncu.servicebus.windows.net için güvenlik duvarınızı denetleyin ve sonra yeniden deneyin. Hala sorun yaşıyorsanız, parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32003| Inputvalidationerror| Bu olay, Web hizmeti API 'imize geçirilen girişin geçersiz olduğunu gösterir. İşlemi yeniden deneyin.|
| 32004| DecryptionError| Bu olay, buluttan gelen parolanın şifresini çözerken bir hata olduğunu gösterir. Bunun nedeni, bulut hizmeti ile şirket içi ortamınız arasında bir şifre çözme anahtarı uyumsuzluğu olabilir. Bu sorunu çözmek için, şirket içi ortamınızda parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirin.|
| 32005| ConfigurationError| Ekleme sırasında, kiracıya özgü bilgileri şirket içi ortamınızdaki bir yapılandırma dosyasına kaydedebiliyoruz. Bu olay, bu dosyayı kaydederken bir hata olduğunu veya hizmetin başlatıldığı sırada dosyayı okurken bir hata olduğunu gösterir. Bu sorunu gidermek için, yapılandırma dosyasının yeniden yazmayı zorlamak üzere parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32007| OnBoardingConfigUpdateError| Ekleme sırasında, buluttan verileri şirket içi parola sıfırlama hizmetine göndereceğiz. Daha sonra bu veriler, diskte güvenli bir şekilde depolanması için Eşitleme hizmetine gönderilmeden önce bellek içi bir dosyaya yazılır. Bu olay, bellekteki verileri yazma veya güncelleştirme ile ilgili bir sorun olduğunu gösterir. Bu sorunu gidermek için, bu yapılandırma dosyasının yeniden yazmayı zorlamak üzere parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32008| Doğrulama hatası| Bu olay, parola sıfırlama Web hizmetinden geçersiz bir yanıt aldığınızı gösterir. Bu sorunu gidermek için parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi deneyin.|
| 32009| AuthTokenError| Bu olay, Azure AD Connect kurulumu sırasında belirtilen genel yönetici hesabı için bir yetkilendirme belirteci alamadık. Bu hata, genel yönetici hesabı için belirtilen Hatalı Kullanıcı adı veya parola nedeniyle oluşabilir. Bu hata, belirtilen genel yönetici hesabı federe olduğunda da oluşabilir. Bu sorunu gidermek için yapılandırmayı doğru Kullanıcı adı ve parolayla yeniden çalıştırın ve yöneticinin yönetilen (salt bulut veya parola eşitlenmiş) hesap olduğundan emin olun.|
| 32010| CryptoError| Bu olay, parola şifreleme anahtarını oluştururken veya bulut hizmetinden gelen bir parolanın şifresini çözerken bir hata olduğunu gösterir. Bu hata büyük olasılıkla ortamınızla ilgili bir sorun olduğunu gösterir. Bu sorunu çözme hakkında daha fazla bilgi edinmek için olay günlüğlerinizin ayrıntılarına bakın. Ayrıca parola geri yazma hizmetini devre dışı bırakıp yeniden etkinleştirmeyi deneyebilirsiniz.|
| 32011| OnBoardingServiceError| Bu olay, şirket içi hizmetin ekleme işlemini başlatmak için parola sıfırlama Web hizmetiyle düzgün şekilde iletişim kuramadığını gösterir. Bu, bir güvenlik duvarı kuralı nedeniyle veya kiracınız için bir kimlik doğrulama belirteci alınırken bir sorun oluşursa oluşabilir. Bu sorunu gidermek için TCP 443 ve TCP 9350-9354 veya https://ssprdedicatedsbprodncu.servicebus.windows.net için giden bağlantıları engellemediğinizden emin olun. Ayrıca, eklemek için kullandığınız Azure AD yönetici hesabının Federasyonun olmadığından emin olun.|
| 32013| OffBoardingError| Bu olay, şirket içi hizmetin çıkarma işlemini başlatmak için parola sıfırlama Web hizmeti ile düzgün şekilde iletişim kuramadığını gösterir. Bu, bir güvenlik duvarı kuralı nedeniyle veya kiracınızın yetkilendirme belirtecini alırken bir sorun oluşursa oluşabilir. Bu sorunu gidermek için 443 veya https://ssprdedicatedsbprodncu.servicebus.windows.net üzerinden giden bağlantıları engellemediğinden ve kullandığınız Azure Active Directory yönetici hesabının federasyona sahip olmadığından emin olun.|
| 32014| ServiceBusWarning| Bu olay, kiracının Service Bus örneğine bağlanmayı yeniden denememiz gerektiğini gösterir. Normal koşullar altında bu bir sorun olmaması gerekir, ancak bu olayı birçok kez görürseniz, özellikle yüksek Gecikmeli veya düşük bant genişliğine sahip bir bağlantı olması halinde, ağ bağlantınızı Service Bus kontrol etmeyi göz önünde bulundurun.|
| 32015| ReportServiceHealthError| Parola geri yazma hizmetinizin sistem durumunu izlemek için, her beş dakikada bir parola sıfırlama Web hizmetinize sinyal verileri göndereceğiz. Bu olay, bu durum bilgilerini bulut Web hizmetine geri gönderirken bir hata olduğunu gösterir. Bu durum bilgileri, nesne tarafından tanımlanabilen bilgileri (OII) veya kişisel olarak tanımlanabilir bilgileri (PII) verilerini içermez ve bulutta hizmet durumu bilgileri sağlayabilmemiz için yalnızca bir sinyal ve temel hizmet istatistiklerdir.|
| 33001| ADUnKnownError| Bu olay Active Directory tarafından döndürülen bilinmeyen bir hata olduğunu gösterir. Daha fazla bilgi için, ADSync kaynağından olaylar için Azure AD Connect Server olay günlüğünü denetleyin.|
| 33002| Adusernotfoun| Bu olay, bir parolayı sıfırlamaya veya değiştirmeye çalışan kullanıcının şirket içi dizinde bulunamadığını gösterir. Bu hata, Kullanıcı şirket içinde silindiğinde ve bulutta olmadığında ortaya çıkabilir. Bu hata, eşitleme ile ilgili bir sorun varsa da oluşabilir. Daha fazla bilgi için eşitleme günlüklerinizi ve son birkaç eşitleme çalıştırması ayrıntılarını kontrol edin.|
| 33003| ADMutliMatchError| Bir parola sıfırlama veya değişiklik isteği buluttan kaynaklanıyorsa, bu isteğin şirket içi ortamınızdaki bir kullanıcıya nasıl bağlanacağını öğrenmek için Azure AD Connect kurulum işlemi sırasında belirtilen bulut bağlayıcısını kullanırız. Bu olay, şirket içi dizininizde aynı bulut bağlayıcısı özniteliğiyle iki Kullanıcı bulduğumuz anlamına gelir. Daha fazla bilgi için eşitleme günlüklerinizi ve son birkaç eşitleme çalıştırması ayrıntılarını kontrol edin.|
| 33004| ADPermissionsError| Bu olay, Active Directory Yönetim Aracısı (ADMA) hizmet hesabının yeni bir parola ayarlamak için söz konusu hesapta uygun izinlere sahip olmadığını gösterir. Kullanıcının ormanındaki ADMA hesabının ormandaki tüm nesnelerde sıfırlama ve parola değiştirme izinlerine sahip olduğundan emin olun. İzinlerin nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. 4. Adım: uygun Active Directory izinleri ayarlama. Bu hata, kullanıcının AdminCount özelliği 1 olarak ayarlandığında da oluşabilir.|
| 33005| ADUserAccountDisabled| Bu olay, şirket içinde devre dışı bırakılmış bir hesabın parolasını sıfırlama veya değiştirme girişiminde bulunulmadığını gösterir. Hesabı etkinleştirin ve işlemi yeniden deneyin.|
| 33006| ADUserAccountLockedOut| Bu olay, şirket içinde kilitlenen bir hesabın parolasını sıfırlama veya değiştirme girişiminde bulunulmadığını gösterir. Bir Kullanıcı kısa bir süre içinde çok fazla kez değişiklik veya sıfırlama işlemini denediğinde kilitleme işlemleri gerçekleşebilir. Hesabın kilidini açın ve işlemi yeniden deneyin.|
| 33007| ADUserIncorrectPassword| Bu olay, kullanıcının bir parola değiştirme işlemi gerçekleştirirken yanlış geçerli parolayı belirtdiğini gösterir. Doğru geçerli parolayı belirtip yeniden deneyin.|
| 33008| ADPasswordPolicyError| Bu olay, parola geri yazma hizmeti yerel dizininizde, etki alanının parola yaşı, geçmişi, karmaşıklığı veya filtreleme gereksinimlerini karşılamayan bir parola ayarlamaya çalıştığında oluşur. <br> <br> En az bir parola yaşı varsa ve kısa süre önce bu zaman içindeki parolayı değiştirdiyseniz, etki alanındaki belirli bir yaşa ulaşıncaya kadar parolayı tekrar değiştiremezsiniz. Sınama amacıyla, minimum yaş 0 olarak ayarlanmalıdır. <br> <br> Parola geçmişi gereksinimleriniz etkinse, son *n* kez kullanılmamış bir parola seçmeniz gerekir, burada *n* parola geçmişi ayarıdır. Son *N* kez kullanılan bir parola seçerseniz, bu durumda bir hata görürsünüz. Sınama amacıyla parola geçmişi 0 olarak ayarlanmalıdır. <br> <br> Parola karmaşıklığı gereksinimleriniz varsa, Kullanıcı bir parolayı değiştirmeye veya sıfırlamaya çalıştığında bunların hepsi zorlanır. <br> <br> Parola Filtreleriniz etkinse ve Kullanıcı filtreleme ölçütlerine uymayan bir parola seçerse, sıfırlama veya değiştirme işlemi başarısız olur.|
| 33009| ADConfigurationError| Bu olay, Active Directory bir yapılandırma sorunu nedeniyle Şirket içi dizininize geri parola yazılırken bir sorun olduğunu gösterir. Oluşan hata hakkında daha fazla bilgi için, ADSync hizmetinden iletiler için Azure AD Connect makinenin uygulama olay günlüğünü denetleyin.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Parola geri yazma bağlantısı sorunlarını giderme

Azure AD Connect parola geri yazma bileşeni ile hizmet kesintileri yaşıyorsanız, bu sorunu çözmek için uygulayabileceğiniz bazı hızlı adımlar aşağıda verilmiştir:

* [Ağ bağlantısını onayla](#confirm-network-connectivity)
* [Azure AD Connect eşitleme hizmetini yeniden başlatın](#restart-the-azure-ad-connect-sync-service)
* [Parola geri yazma özelliğini devre dışı bırakma ve yeniden etkinleştirme](#disable-and-re-enable-the-password-writeback-feature)
* [En son Azure AD Connect sürümü yüklensin](#install-the-latest-azure-ad-connect-release)
* [Parola geri yazma sorunlarını giderme](#troubleshoot-password-writeback)

Genel olarak, hizmetinizi en hızlı şekilde kurtarmak için, bu adımları daha önce açıklanan sırada yürütmenizi öneririz.

### <a name="confirm-network-connectivity"></a>Ağ bağlantısını onayla

En yaygın hata noktası, güvenlik duvarı ve veya proxy bağlantı noktaları ve boşta kalma zaman aşımlarının yanlış yapılandırılmalarından oluşur. 

Azure AD Connect Version 1.1.443.0 ve üzeri için aşağıdakiler için giden HTTPS erişiminizin olması gerekir:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Daha fazla ayrıntı elde etmek için, her Çarşamba güncelleştirilmiş [Microsoft Azure veri MERKEZI IP aralıklarının](https://www.microsoft.com/download/details.aspx?id=41653) güncelleştirilmiş listesine başvurun ve sonraki Pazartesi 'yi devreye koyar.

Daha fazla bilgi için [Azure AD Connect önkoşulları](../hybrid/how-to-connect-install-prerequisites.md) makalesindeki bağlantı önkoşullarını gözden geçirin.

> [!NOTE]
> "Parolanın süresi dolmasın" veya "Kullanıcı parolayı değiştiremez" ayarları şirket içi AD DS hesapta yapılandırılırsa SSPR de başarısız olabilir. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Azure AD Connect eşitleme hizmetini yeniden başlatın

Bağlantı sorunlarını veya hizmetle ilgili diğer geçici sorunları çözümlemek için Azure AD Connect eşitleme hizmetini yeniden başlatın:

1. Yönetici olarak, Azure AD Connect çalıştıran sunucuda **Başlat** ' ı seçin.
1. Arama alanına **Services. msc** yazın ve **ENTER**' u seçin.
1. **Microsoft Azure AD eşitleme** girişi ' ni arayın.
1. Hizmet girişine sağ tıklayın, **Yeniden Başlat**' ı seçin ve sonra işlemin bitmesini bekleyin.

   ![GUI 'yi kullanarak Azure AD Eşitleme hizmetini yeniden başlatın][Service restart]

Bu adımlar, bulut hizmetiyle bağlantınızı yeniden kurun ve yaşadığınız kesintileri çözün. ADSync hizmetini yeniden başlatmak sorununuzu gidermezse, parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirmeyi öneririz.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Parola geri yazma özelliğini devre dışı bırakma ve yeniden etkinleştirme

Bağlantı sorunlarını gidermek için parola geri yazma özelliğini devre dışı bırakıp yeniden etkinleştirin:

   1. Yönetici olarak, Azure AD Connect Yapılandırma Sihirbazı ' nı açın.
   1. **Azure AD 'ye Bağlan**' da Azure AD Genel yönetici kimlik bilgilerinizi girin.
   1. **AD DS Bağlan**' da, ad etki alanı Hizmetleri yönetici kimlik bilgilerinizi girin.
   1. **Kullanıcılarınızı benzersiz olarak tanımlamak**için **İleri** düğmesini seçin.
   1. **Isteğe bağlı özellikler**' de **parola geri yazma** onay kutusunu temizleyin.
   1. **Yapılandırmaya hazırlanma** sayfasına ulaşana kadar herhangi bir şeyi değiştirmeden kalan Iletişim sayfalarında **İleri ' yi** seçin.
   1. **Yapılandırmaya hazırlanma sayfasında** **parola geri yazma** seçeneğinin **devre dışı** olarak görüntülendiğinden emin olun ve ardından yaptığınız değişiklikleri kaydetmek için yeşil **Yapılandır** düğmesini seçin.
   1. **Tamamlandı** **seçeneğini temizleyin ve ardından** Sihirbazı kapatmak için **son** ' u seçin.
   1. Azure AD Connect Yapılandırma Sihirbazı 'nı yeniden açın.
   1. Hizmeti yeniden etkinleştirmek için **Isteğe bağlı özellikler** sayfasında **parola geri yazma** seçeneğini seçtiğinizden emin olun, 2-8 arasındaki adımları yineleyin.

Bu adımlar, bulut hizmetimiz ile bağlantınızı yeniden kurar ve yaşadığınız kesintileri çözmeyebilirsiniz.

Parola geri yazma özelliğinin devre dışı bırakılması ve yeniden etkinleştirilmesi sorununuzu gidermezse, Azure AD Connect yeniden yüklemenizi öneririz.

### <a name="install-the-latest-azure-ad-connect-release"></a>En son Azure AD Connect sürümü yüklensin

Azure AD Connect yeniden yüklemek, bulut hizmetlerimiz ve yerel Active Directory ortamınız arasındaki yapılandırma ve bağlantı sorunlarını çözebilir.

Bu adımı, daha önce açıklanan ilk iki adımı denemeden sonra gerçekleştirmenizi öneririz.

> [!WARNING]
> Kullanıma hazır eşitleme kurallarını özelleştirdiyseniz, *yükseltme işlemine devam etmeden önce bunları yedekleyin ve sonra işlemi tamamladıktan sonra el ile yeniden dağıtın.*

1. En son Azure AD Connect sürümünü [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?LinkId=615771)' nden indirin.
1. Azure AD Connect zaten yüklü olduğundan, Azure AD Connect yüklemenizi en son sürüme güncelleştirmek için yerinde yükseltme gerçekleştirmeniz gerekir.
1. İndirilen paketi yürütün ve Azure AD Connect makinenizi güncelleştirmek için ekrandaki yönergeleri izleyin.

Önceki adımlar, bulut hizmetimiz ile bağlantınızı yeniden oluşturmanız ve yaşadığınız kesintileri çözmelidir.

Azure AD Connect sunucusunun en son sürümünü yüklemek sorununuzu gidermezse, en son sürümü yükledikten sonra parola geri yazma özelliğini son adım olarak yeniden etkinleştirmeyi ve sonra yeniden etkinleştirmenizi öneririz.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Azure AD Connect parola geri yazma için gerekli izinlere sahip olduğunu doğrulayın

Azure AD Connect parola geri yazma işlemini gerçekleştirmek için **parola sıfırlama** izninin Active Directory gerekir. Azure AD Connect belirli bir şirket içi Active Directory Kullanıcı hesabı için gerekli izinlere sahip olup olmadığını öğrenmek için, Windows etkin Izin özelliğini kullanabilirsiniz:

1. Azure AD Connect sunucusunda oturum açın ve > **eşitleme hizmetini** **Başlat** ' a tıklayarak **Synchronization Service Manager** başlatın.
1. **Bağlayıcılar** sekmesinde şirket içi **Active Directory Domain Services** Bağlayıcısı ' nı seçin ve ardından **Özellikler**' i seçin.  
   özelliklerin nasıl düzenleneceğini gösteren ![Synchronization Service Manager](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. Açılır pencerede **Active Directory ormana Bağlan** ' ı seçin ve **Kullanıcı adı** özelliğini unutmayın. Bu özellik, dizin eşitlemesi gerçekleştirmek için Azure AD Connect tarafından kullanılan AD DS hesabıdır. Parola geri yazma işlemini gerçekleştirmek için Azure AD Connect AD DS hesabının parola sıfırlama iznine sahip olması gerekir.  

   ![Eşitleme hizmeti Active Directory Kullanıcı hesabı bulma](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Şirket içi etki alanı denetleyicisinde oturum açın ve **Active Directory Kullanıcıları ve bilgisayarları** uygulamasını başlatın.
1. **Görünüm** ' ü seçin ve **Gelişmiş Özellikler** seçeneğinin etkinleştirildiğinden emin olun.  

   ![Active Directory Kullanıcıları ve bilgisayarları gelişmiş özellikleri gösterir](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Doğrulamak istediğiniz Active Directory Kullanıcı hesabını bulun. Hesap adına sağ tıklayın ve **Özellikler**' i seçin.  
1. Açılır pencerede **güvenlik** sekmesine gidin ve **Gelişmiş**' i seçin.  
1. **Yönetici Için gelişmiş güvenlik ayarları** açılır penceresinde, **etkin erişim** sekmesine gidin.
1. **Kullanıcı Seç**' i seçin, Azure AD Connect tarafından kullanılan AD DS hesabı seçin (bkz. Adım 3) ve ardından **etkin erişimi görüntüle**' yi seçin.

   ![Eşitleme hesabını gösteren geçerli erişim sekmesi](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Aşağı kaydırın ve **sıfırlama parolasını**bulun. Girişte bir onay işareti varsa, AD DS hesabının seçili Active Directory Kullanıcı hesabının parolasını sıfırlama izni vardır.  

   ![Eşitleme hesabının parola sıfırlama iznine sahip olduğu doğrulanıyor](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD forumları

Azure AD ve self servis parola sıfırlama hakkında genel bir sorunuz varsa, [Azure AD forumları](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)hakkında yardım almak için topluluğa soru sorabilirsiniz. Topluluk üyeleri; mühendisler, ürün yöneticileri, MVP 'ler ve BT uzmanlarına yönelik BT profesyonellerini içerir.

## <a name="contact-microsoft-support"></a>Microsoft destek 'e başvurun

Bir sorunun yanıtını bulamazsanız, destek ekiplerimiz size daha fazla yardımcı olmak için her zaman kullanılabilir.

Size uygun bir şekilde yardımcı olması için, bir servis talebi açarken mümkün olduğunca fazla ayrıntı sağlamanızı isteyeceğiz. Bu ayrıntılar şunları içerir:

* **Hatanın genel açıklaması**: hata nedir? Fark edilen davranış nedir? Hatayı nasıl yeniden oluşturuyoruz? Mümkün olduğunca çok ayrıntı sağlayın.
* **Sayfa**: hatayı fark ettiğinizde hangi sayfayı kullanıyorsunuz? Mümkün olduğunda URL 'YI ve sayfanın ekran görüntüsünü ekleyin.
* **Destek kodu**: Kullanıcı hatayı gördüğünüzde oluşturulan destek kodu nedir?
   * Bu kodu bulmak için, hatayı yeniden oluşturun ve ardından ekranın alt kısmındaki **destek kodu** bağlantısını seçin ve destek mühendisine sonuçları veren GUID 'yi gönderin.

   ![Ekranın alt kısmındaki destek kodunu bulun][Support code]

  * En altta destek kodu olmayan bir sayfadan karşılaşırsanız, F12 ' yi seçin ve SID ve CıD için arama yapın ve bu iki sonucu destek mühendisine gönderin.
* **Tarih, saat ve saat dilimi**: Hatanın gerçekleştiği *saat dilimiyle* kesin tarih ve saati dahil edin.
* **Kullanıcı kimliği**: hatayı seçen kullanıcı kim? Örnek olarak *User\@contoso.com*.
   * Bu bir Federasyon kullanıcısı mı?
   * Bu bir geçişli kimlik doğrulama kullanıcısı mı?
   * Bu, Parola karması ile eşitlenen bir Kullanıcı mı?
   * Bu yalnızca bulut kullanıcısı mı?
* **Lisanslama**: kullanıcıya BIR Azure AD lisansı atandı mi?
* **Uygulama olay günlüğü**: parola geri yazma özelliğini kullanıyorsanız ve hata şirket içi altyapınızda ise, Azure AD Connect sunucusundan uygulama olay günlüğlerinizin daraltılmış bir kopyasını ekleyin.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Azure AD Eşitleme hizmetini yeniden başlatın"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Destek kodu pencerenin sağ alt kısmında bulunur"

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde, Azure AD ile parola sıfırlama hakkında ek bilgiler sağlanmaktadır:

* [SSPR’yi başarılı bir şekilde nasıl piyasaya çıkarabilirim?](howto-sspr-deployment.md)
* [Parolanızı sıfırlama veya değiştirme](../user-help/active-directory-passwords-update-your-own-password.md)
* [Self servis parola sıfırlama için kaydolma](../user-help/active-directory-passwords-reset-register.md)
* [Lisansla ilgili bir sorunuz mu var?](concept-sspr-licensing.md)
* [SSPR hangi verileri kullanır ve kullanıcılarınız için hangi verileri doldurmanız gerekir?](howto-sspr-authenticationdata.md)
* [Kullanıcılar hangi kimlik doğrulama yöntemlerini kullanabilir?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR ile kullanılabilen ilke seçenekleri nelerdir?](concept-sspr-policy.md)
* [Parola geri yazma nedir ve neden önemlidir?](howto-sspr-writeback.md)
* [SSPR’de etkinliği nasıl bildirebilirim?](howto-sspr-reporting.md)
* [SSPR’deki tüm seçenekler nelerdir ve ne anlama gelir?](concept-sspr-howitworks.md)
* [Başka bir yerde ele alınmayan bir sorum var](active-directory-passwords-faq.md)
