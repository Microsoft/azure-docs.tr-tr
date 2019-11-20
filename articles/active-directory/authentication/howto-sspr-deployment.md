---
title: Self servis parola sıfırlama dağıtım planı-Azure Active Directory
description: Azure AD self servis parola sıfırlama işleminin başarılı uygulanması stratejisi
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b648d6f914b5e3004ea3b62019bbec33e5a4871d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081531"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Azure AD self servis parola sıfırlamayı dağıtma

> [!NOTE]
> Bu kılavuzda, self servis parola sıfırlama ve nasıl dağıtılacağı açıklanmaktadır. Hesabınıza geri dönmek için self servis parola sıfırlama aracı 'nı arıyorsanız [https://aka.ms/sspr](https://aka.ms/sspr)gidin. 

Self servis parola sıfırlama (SSPR), çalışanların BT personeline başvurmanız gerekmeden parolalarını sıfırlamalarını sağlayan bir Azure Active Directory özelliğidir. Çalışanlar, hizmet kullanılmadan önce self servis parola sıfırlama için kaydolmalıdır veya kaydolmalıdır. Kayıt sırasında çalışan, kuruluşu tarafından etkinleştirilen bir veya daha fazla kimlik doğrulama yöntemi seçer.

SSPR, çalışanların engellerini hızlı bir şekilde engellemesini ve nerede nerede veya günün saati olmaksızın çalışmaya devam etmesine olanak sağlar. Kuruluşunuz, kullanıcıların kendi kendilerine engellemeyi kaldırmaya izin vererek, en yaygın parolayla ilgili sorunlara yönelik üretken olmayan süreyi ve yüksek destek maliyetlerini azaltabilir.

Kullanıcıların, kuruluşunuzda başka bir uygulama veya hizmetle birlikte SSPR 'yi dağıtarak hızla kaydolmasıyla yardım edin. Bu eylem, büyük bir oturum açma hacmi oluşturur ve kayıt işlemini yeniden dener.

SSPR 'yi dağıtmadan önce kuruluşlar, zaman içinde hangi parola sıfırlama ile ilgili yardım masası çağrılarının gerçekleştiği ve her çağrının ortalama maliyetinin sayısını tespit etmek isteyebilir. Bu veri dağıtımını, SSPR 'nin kuruluşunuza göre göstereceği değeri göstermek için kullanabilirler.  

## <a name="how-sspr-works"></a>SSPR 'nin çalışması

1. Bir Kullanıcı bir parolayı sıfırlamaya çalıştığında, kimliklerini kanıtlamak için önceden kaydedilmiş kimlik doğrulama yöntemini veya yöntemlerini doğrulamalıdır.
1. Ardından Kullanıcı yeni bir parola girer.
   1. Yalnızca bulutta bulunan kullanıcılar için yeni parola Azure Active Directory depolanır. Daha fazla bilgi için [SSPR 'Nin nasıl çalıştığı](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)makalesine bakın.
   1. Karma kullanıcılar için parola, Azure AD Connect hizmeti aracılığıyla şirket içi Active Directory geri yazılır. Daha fazla bilgi için [parola geri yazma nedir](concept-sspr-writeback.md#how-password-writeback-works)makalesine bakın.

## <a name="licensing-considerations"></a>Lisanslama konuları

Azure Active Directory, Kullanıcı başına lisanslanır, her kullanıcının kullandıkları özellikler için uygun bir lisansa sahip olması anlamına gelir.

Lisanslama hakkında daha fazla bilgiyi [Azure Active Directory fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/active-directory/) bulabilirsiniz

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>SSPR ve MFA için Birleşik kaydı etkinleştir

Microsoft, kuruluşların SSPR ve Multi-Factor Authentication için Birleşik kayıt deneyimini etkinleştirmesini önerir. Bu Birleşik kayıt deneyimini etkinleştirdiğinizde, kullanıcıların her iki özelliği de etkinleştirmek için kayıt bilgilerini yalnızca bir kez seçmesini gerekir.

![Birleşik güvenlik bilgileri kaydı](./media/howto-sspr-deployment/combined-security-info.png)

Birleşik kayıt deneyimi, kuruluşların hem SSPR hem de Azure Multi-Factor Authentication kullanımını etkinleştirmesini gerektirmez. Birleşik kayıt deneyimi, kuruluşların geleneksel ayrı bileşenlerle karşılaştırıldığında daha iyi bir kullanıcı deneyimi sağlar. Birleşik kayıt ve nasıl etkinleştirileceği hakkında daha fazla bilgi için, [Birleşik güvenlik bilgileri kaydı (Önizleme)](concept-registration-mfa-sspr-combined.md) makalesinde bulabilirsiniz

## <a name="plan-the-configuration"></a>Yapılandırmayı planlayın

SSPR 'yi önerilen değerlerle birlikte etkinleştirmek için aşağıdaki ayarlar gereklidir.

| Alan | Ayar | Değer |
| --- | --- | --- |
| **SSPR özellikleri** | Self servis parola sıfırlama etkin | Pilot için **Seçili** grup ve **üretim için** |
| **Kimlik doğrulama yöntemleri** | Kaydolmak için gereken kimlik doğrulama yöntemleri | Sıfırlama için her zaman 1 daha fazla |
|   | Sıfırlamak için gereken kimlik doğrulama yöntemleri | Bir veya iki |
| **Kayıt** | Kullanıcılardan oturum açarken kaydolmalarını iste | Yes |
|   | Kullanıcılardan kimlik doğrulama bilgilerini yeniden onaylamasını istemeden önce geçen gün sayısı | 90 – 180 gün |
| **Bildirimler** | Parola sıfırlamayı kullanıcılara bildir | Yes |
|   | Diğer yöneticiler parolalarını sıfırladığında tüm yöneticilere bildir | Yes |
| **Liş** | Yardım masası bağlantısını Özelleştir | Yes |
|   | Özel yardım masası e-postası veya URL 'SI | Destek sitesi veya e-posta adresi |
| **Şirket içi tümleştirme** | Parolaları şirket içi AD 'ye geri yazma | Yes |
|   | Kullanıcıların, parolayı sıfırlamadan hesabın kilidini açma izni ver | Yes |

### <a name="sspr-properties-recommendations"></a>SSPR özellikleri önerileri

Self servis parola sıfırlama etkinleştirildiğinde, pilot sırasında kullanılacak bir güvenlik grubu seçin.

Hizmeti daha geniş bir şekilde başlatmayı planlarken, kuruluştaki herkes için SSPR 'yi zorlamak üzere ALL seçeneğini kullanmanızı öneririz. Tümü olarak ayarlanamıyor, uygun Azure AD güvenlik grubunu veya Azure AD ile eşitlenen AD grubunu seçin.

### <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri

Sıfırlamak için gereken sayıdan en az bir daha fazlasına kaydolmak için gereken kimlik doğrulama yöntemlerini ayarlayın. Birden çok izin verilmesi, kullanıcıların sıfırlanması gerektiğinde esneklik sağlar.

Kuruluşunuza uygun bir düzeye **sıfırlamak için gereken yöntem sayısını** ayarlayın. Biri en az artışla ihtiyaç duyurken, ikisi de güvenlik duruşunuzu artırabilir.

SSPR, önceden tanımlanmış güvenlik soruları ve özelleştirilmiş güvenlik soruları oluşturma için hangi kimlik doğrulama yöntemlerinin kullanılabildiği hakkında ayrıntılı bilgi için bkz. [kimlik doğrulama yöntemleri nedir](concept-authentication-methods.md) .

### <a name="registration-settings"></a>Kayıt ayarları

Kullanıcıların **Evet** **'de oturum açarken kaydolmasını gerektir** ayarını belirleyin. Bu ayar, kullanıcıların oturum açarken kaydolması zorunlu olduğu, tüm kullanıcıların korunduğundan emin olmak anlamına gelir.

Kuruluşunuzun daha kısa bir zaman dilimine yönelik bir iş ihtiyacı yoksa, kullanıcılardan kimlik doğrulama bilgilerini **90** ile **180** gün arasında **yeniden onaylamasını istemeden önce geçen gün sayısını** ayarlayın.

### <a name="notifications-settings"></a>Bildirim ayarları

**Parola sıfırlamaları için kullanıcılara bildir** ' i ve **diğer yöneticiler parolasını Evet olarak sıfırladığında tüm yöneticilere bildir** 'i yapılandırın. Her ikisinde de **Evet** ' i seçmek, kullanıcıların parolası sıfırlandığında haberdar olmasını sağlayarak güvenliği artırır ve bir yönetici bir parolayı değiştirdiğinde tüm yöneticilerin farkında olur. Kullanıcılar veya Yöneticiler böyle bir bildirim alıyorsa ve değişikliği başlatmadığından, olası bir güvenlik ihlaline anında rapor verebilir.

### <a name="customization"></a>Özelleştirme

Sorun yaşayan kullanıcıların hızla yardım almasını sağlamak için Yardım **Masası e-postasını veya URL 'yi** özelleştirmek önemlidir. Bu seçeneği, kullanıcılarınızın tanıdık olduğu ortak bir yardım masası e-posta adresi veya Web sayfası olarak ayarlayın.

### <a name="on-premises-integration"></a>Şirket içi tümleştirme

Karma ortamınız varsa, Şirket **ıçı ad 'ye geri yazma parolalarının** **Evet**olarak ayarlandığından emin olun. Ayrıca, daha fazla esneklik sağladığı için kullanıcıların parolayı sıfırlamadan hesabı açmaya Izin ver seçeneğini Evet olarak ayarlayın.

### <a name="changingresetting-passwords-of-administrators"></a>Yöneticilerin parolalarını değiştirme/sıfırlama

Yönetici hesapları, yükseltilmiş izinlere sahip özel hesaplardır. Bunların güvenliğini sağlamak için aşağıdaki kısıtlamalar, yöneticilerin parolalarını değiştirmek için geçerlidir:

- Şirket içi kuruluş yöneticileri veya etki alanı yöneticileri, SSPR üzerinden parolalarını sıfırlayamaz. Yalnızca kendi şirket içi ortamlarında parolalarını değiştirebilirler. Bu nedenle, şirket içi AD yönetici hesaplarını Azure AD 'ye eşitetmememiz önerilir.
- Yönetici Parola sıfırlama yöntemi olarak yanıt & gizli soruları kullanamaz.

### <a name="environments-with-multiple-identity-management-systems"></a>Birden çok kimlik yönetimi sistemine sahip ortamlar

Bir ortamda, Oracle har, Sitedefteri veya diğer sistemler gibi şirket içi kimlik yöneticileri gibi birden çok kimlik yönetimi sistemi varsa, Active Directory yazılan parolaların, şunun gibi bir araç kullanılarak diğer sistemlerle eşitlenmesi gerekebilir Parola değiştirme bildirimi hizmeti (PCNS) Microsoft Identity Manager (MıM). Bu daha karmaşık senaryoda daha fazla bilgi bulmak için, [bir etki alanı denetleyicisinde MIM parola değiştirme bildirimi hizmetini dağıtma](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)makalesine bakın.

## <a name="plan-deployment-and-support-for-sspr"></a>SSPR için dağıtımı ve desteği planlayın

### <a name="engage-the-right-stakeholders"></a>Doğru paydaşlara katılın

Teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri nedeniyle bu, genellikle bu şekilde yapılır. Bu girişlerin oluşmasını önlemek için, doğru proje hissedarlarını belirttiğinizden emin olun ve projedeki paydaş rollerinin, paydaşlar ve proje giriş ve sorumluluklarını belgeleyerek iyi anlaşıldığından emin olun.

### <a name="communications-plan"></a>İletişim planı

İletişim, her yeni hizmetin başarısı için önemlidir. Hizmetin nasıl kullanılacağı ve beklenildiği gibi çalışmadıklarında yardım almak için neler yapabilecekleri hakkında daha etkin bir şekilde iletişim kurun. Son Kullanıcı iletişim stratejinizi nasıl planlayabileceğiniz hakkında fikir edinmek için [Microsoft İndirme Merkezi ' nde self servis parola sıfırlama dağıtım malzemelerini](https://www.microsoft.com/download/details.aspx?id=56768) gözden geçirin.

### <a name="testing-plan"></a>Test planı

Dağıtımınızın beklendiği gibi çalıştığından emin olmak için, uygulamayı doğrulamak üzere kullanacağınız bir test çalışmaları kümesi planlamanız gerekir. Aşağıdaki tabloda, ilkelerinize göre beklenen sonuçları belgelemek için kullanabileceğiniz bazı yararlı test senaryoları yer almaktadır.

| Kurum başarı hikayesi | Beklenen sonuç |
| --- | --- |
| SSPR portalına kurumsal ağ içinden erişilebilir | Kuruluşunuz tarafından belirlenir |
| SSPR portalına şirket ağı dışından erişilebilir | Kuruluşunuz tarafından belirlenir |
| Kullanıcı parola sıfırlama için etkin olmadığında Kullanıcı parolasını tarayıcıdan sıfırlayın | Kullanıcı parola sıfırlama akışına erişemiyor |
| Kullanıcı parola sıfırlama için kaydolmadıysanız Kullanıcı parolasını tarayıcıdan sıfırlayın | Kullanıcı parola sıfırlama akışına erişemiyor |
| Parola sıfırlama kaydı uygulandığında kullanıcı oturum açar | Kullanıcıdan güvenlik bilgilerini kaydetmesi istenir |
| Parola sıfırlama kaydı tamamlandığında Kullanıcı oturum açar | Kullanıcıdan güvenlik bilgilerini kaydetmesi istenmez |
| Kullanıcının lisansı olmadığında SSPR portalına erişilebilir | Erişilebilir |
| Kullanıcı kaydolduktan sonra Windows 10 ASıFATı veya H + ASıFATı cihaz kilidi ekranından Kullanıcı parolasını sıfırlama | Kullanıcı, parolayı sıfırlayabilir |
| SSPR kaydı ve kullanım verileri, neredeyse gerçek zamanlı olarak yöneticiler tarafından kullanılabilir | Denetim günlükleri aracılığıyla kullanılabilir |

### <a name="support-plan"></a>Destek planı

SSPR tipik olarak Kullanıcı sorunları oluşturmadığından, ortaya çıkabilecek sorunları ele almak için destek personelinin hazırlanabileceği önemlidir.

Yönetici Azure AD portalı aracılığıyla son kullanıcıların parolasını değiştirebilir veya sıfırlayabileceğinden, sorunu bir self servis destek süreci aracılığıyla çözmeye yardımcı olmak daha iyidir.

Bu belgenin işletimsel kılavuz bölümünde, destek durumlarının ve olası nedenlerinden oluşan bir liste oluşturun ve çözüm için bir kılavuz oluşturun.

### <a name="auditing-and-reporting"></a>Denetim ve raporlama

Dağıtımdan sonra, birçok kuruluş, kendi kendine parola sıfırlama (SSPR) hizmetinin nasıl veya ne sıklıkta kullanıldığını öğrenmek ister. Azure Active Directory (Azure AD) tarafından sağlanan raporlama özelliği, önceden oluşturulmuş raporları kullanarak soruları yanıtlamanıza yardımcı olur.

Kayıt ve parola sıfırlama için denetim günlükleri 30 gün boyunca kullanılabilir. Bu nedenle, bir kuruluş içindeki güvenlik denetimi daha uzun bekletme gerektiriyorsa, günlüklerin [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), splunk veya arctıma gıbı bır SIEM aracına aktarılması ve kullanılması gerekir.

Aşağıdaki gibi bir tabloda, yedekleme zamanlaması, sistem ve sorumlu taraflar belgeleyebilirsiniz. Ayrı denetim ve raporlama yedeklemeleri gerekmez, ancak bir sorundan kurtarabileceğiniz ayrı bir yedeğiniz olması gerekir.

|   | İndirme sıklığı | Hedef sistem | Sorumlu parti |
| --- | --- | --- | --- |
| Denetim yedeklemesi |   |   |   |
| Raporlama yedeklemesi |   |   |   |
| Olağanüstü durum kurtarma yedeklemesi |   |   |   |

## <a name="implementation"></a>Uygulama

Uygulama üç aşamada gerçekleşir:

- Kullanıcıları ve lisansları yapılandırma
- Kayıt ve self servis için Azure AD SSPR 'yi yapılandırma
- Parola geri yazma için Azure AD Connect yapılandırma

### <a name="communicate-the-change"></a>Değişikliği iletişim kurma

Planlama aşamasında geliştirdiğiniz iletişim planının uygulamasını başlatın.

### <a name="ensure-groups-are-created-and-populated"></a>Grupların oluşturulduğundan ve doldurulduğundan emin olun

Parola kimlik doğrulama yöntemlerini planlama bölümüne başvurun ve pilot veya üretim uygulaması için grupların kullanılabilir olduğundan ve tüm uygun kullanıcıların gruplara eklendiğinden emin olun.

### <a name="apply-licenses"></a>Lisansları Uygula

Uygulayacağınızı seçtiğiniz gruplara Azure AD Premium lisansı atanmış olmalıdır. Lisansları doğrudan gruba atayabilir veya PowerShell veya grup tabanlı lisanslama gibi mevcut lisans ilkelerini kullanabilirsiniz.

Kullanıcı gruplarına lisans atama hakkında bilgiler, [Azure Active Directory ' de grup üyeliğine göre kullanıcılara lisans atama](../users-groups-roles/licensing-groups-assign.md)hakkında bilgi bulabilirsiniz.

### <a name="configure-sspr"></a>SSPR 'yi yapılandırma

#### <a name="enable-groups-for-sspr"></a>SSPR için grupları etkinleştir

1. Azure portal bir yönetici hesabıyla erişin.
1. Tüm hizmetler ' i seçin ve filtre kutusunda Azure Active Directory yazın ve Azure Active Directory ' ı seçin.
1. Active Directory dikey penceresinde parola sıfırlama ' yı seçin.
1. Özellikler bölmesinde, seçili ' ı seçin. Tüm kullanıcıların etkinleştirilmesini istiyorsanız tüm ' ı seçin.
1. Varsayılan parola sıfırlama ilkesi dikey penceresinde, ilk grubun adını yazın, seçin ve ardından ekranın en altında bulunan Seç ' e tıklayın ve ekranın en üstünde bulunan Kaydet ' i seçin.
1. Her grup için bu işlemi tekrarlayın.

#### <a name="configure-the-authentication-methods"></a>Kimlik doğrulama yöntemlerini yapılandırma

Bu belgenin parola kimlik doğrulama yöntemlerinden planlama bölümündeki planlamaya başvurun.

1. Kayıt ' ı seçin, oturum açarken kullanıcının kaydolmasını ıste ' nin altında Evet ' i seçin ve ardından süre dolduktan önceki gün sayısını ayarlayın ve ardından Kaydet ' i seçin.
1. Bildirim ' ı seçin ve planınız başına yapılandırın ve ardından Kaydet ' i seçin.
1. Özelleştirmeyi seçin ve planınız başına yapılandırın ve ardından Kaydet ' i seçin.
1. Şirket içi tümleştirme ' i seçin ve planınız başına yapılandırın ve ardından Kaydet ' i seçin.

### <a name="enable-sspr-in-windows"></a>Windows 'da SSPR 'yi etkinleştirme

Azure AD 'ye katılmış veya hibrit Azure AD 'ye katılmış Windows 10 1803 cihazları, Windows oturum açma ekranında parolalarını sıfırlayabilirler. Bu özelliği yapılandırma bilgileri ve adımları, [oturum açma ekranından Azure AD parola sıfırlama](tutorial-sspr-windows.md) makalesinde bulunabilir.

### <a name="configure-password-writeback"></a>Parola geri yazmayı yapılandırma

Kuruluşunuz için parola geri yazma özelliğini yapılandırma adımları makalede [nasıl yapılır: parola geri yazma 'Yı yapılandırma](howto-sspr-writeback.md)makalesinde bulabilirsiniz.

## <a name="manage-sspr"></a>SSPR 'yi Yönet

Self servis parola sıfırlama ile ilişkili özellikleri yönetmek için gerekli roller.

| İş rolü/kişi | Azure AD rolü (gerekirse) |
| :---: | :---: |
| Düzey 1 yardım masası | Parola Yöneticisi |
| Düzey 2 yardım masası | Kullanıcı Yöneticisi |
| SSPR Yöneticisi | Genel yönetici |

### <a name="support-scenarios"></a>Destek senaryoları

Destek ekibi başarısını etkinleştirmek için, kullanıcılarınızın aldığınız sorulara dayalı bir SSS oluşturabilirsiniz. Aşağıdaki tablo, yaygın destek senaryolarını içerir.

| Senaryolar | Açıklama |
| --- | --- |
| Kullanıcının kayıtlı bir kimlik doğrulama yöntemi yok | Kullanıcı parolasını sıfırlamaya çalışıyor ancak kaydoldukları kimlik doğrulama yöntemlerinden hiçbirine sahip değil (örnek: kendi cep telefonlarını evde ve e-postaya erişemez) |
| Kullanıcı Office veya cep telefonunda bir metin veya çağrı almıyor | Kullanıcı, kimliğini metin veya çağrı yoluyla doğrulamaya çalışıyor ancak bir metin/çağrı almıyor. |
| Kullanıcı parola sıfırlama portalına erişemiyor | Kullanıcı parolasını sıfırlamak istiyor ancak parola sıfırlama için etkin değil, bu nedenle parolaları güncelleştirmek için sayfaya erişemez. |
| Kullanıcı yeni bir parola ayarlayamadı | Kullanıcı parola sıfırlama akışı sırasında doğrulamayı tamamlar, ancak yeni bir parola ayarlayamaz. |
| Kullanıcı bir Windows 10 cihazında parola sıfırlama bağlantısı görmez | Kullanıcı Windows 10 kilit ekranından parolayı sıfırlamaya çalışıyor, ancak cihaz Azure AD 'ye katılmadı ya da Intune cihaz ilkesi etkinleştirilmemiş |

Ek sorun giderme için aşağıdakiler gibi bilgileri de dahil etmek isteyebilirsiniz.

- SSPR için hangi gruplar etkinleştirilir.
- Hangi kimlik doğrulama yöntemlerinin yapılandırıldığı.
- Şirket ağı üzerinde veya üzerinde ilgili erişim ilkeleri.
- Yaygın senaryolar için sorun giderme adımları.

Ayrıca, en yaygın SSPR senaryolarıyla ilgili genel sorun giderme adımlarını anlamak için self servis parola sıfırlama sorunlarını gidermeye yönelik çevrimiçi belgelerimize da bakabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD parola korumasını uygulamayı deneyin](concept-password-ban-bad.md)

- [Azure AD akıllı kilitleme 'yi uygulamayı düşünün](howto-password-smart-lockout.md)
