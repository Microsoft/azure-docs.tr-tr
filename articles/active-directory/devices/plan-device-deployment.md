---
title: Azure Active Directory cihaz dağıtımınızı planlayın
description: Kurumsal ihtiyaçlarınızı karşılayan Azure AD cihaz tümleştirme stratejilerini seçin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa18b619c785ad23e1da037621597684b1c5c63
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551790"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Azure Active Directory cihaz dağıtımınızı planlayın

Bu makale, cihazınızı Azure AD ile tümleştirme yöntemlerini değerlendirmenize yardımcı olur, uygulama planını seçebilir ve desteklenen cihaz yönetim araçlarına yönelik anahtar bağlantıları sağlar.

Kullanıcılarınızın oturum açmasını sağlayan cihazlar genişletiliyor. Kuruluşlar Masaüstü, dizüstü bilgisayar, telefon, tablet ve diğer cihazlar sağlayabilir. Kullanıcılarınız kendi cihaz dizisini getirebilir ve değişen konumlardan bilgilere erişebilir. Bu ortamda, bir yönetici olarak işiniz, kurumsal kaynaklarınızı tüm cihazlarda güvenli tutmalar.

Azure Active Directory (Azure AD), kuruluşunuzun bu hedefleri cihaz kimlik yönetimiyle karşılamasına olanak sağlar. Artık cihazlarınızı Azure AD 'de alabilir ve [Azure Portal](https://portal.azure.com/)merkezi bir konumdan kontrol edebilirsiniz. Bu, size Birleşik bir deneyim ve gelişmiş güvenlik sağlar ve yeni bir cihaz yapılandırmak için gereken süreyi azaltır.

Cihazlarınızı Azure AD ile tümleştirmeye yönelik birden çok yöntem vardır:

* Cihazları Azure AD 'ye [kaydedebilirsiniz](concept-azure-ad-register.md)

* [Cihazları](concept-azure-ad-join.md) Azure AD 'ye ekleme (yalnızca bulut) veya

* Şirket içi Active Directory ve Azure AD 'de cihazlar arasında [karma bir Azure AD katılımı oluşturun](concept-azure-ad-join-hybrid.md) . 

## <a name="learn"></a>Learn

Başlamadan önce, [cihaz kimlik yönetimine genel bakış](overview.md)hakkında bilgi sahibi olduğunuzdan emin olun.

### <a name="benefits"></a>Avantajlar

Cihazlarınıza bir Azure AD kimliği verme hakkında önemli avantajlar:

* Üretkenliği artırın – Azure AD Ile kullanıcılarınız şirket içi ve bulut kaynaklarınız için [sorunsuz oturum açma (SSO)](./azuread-join-sso.md) yapabilir ve bu sayede her yerde üretken olmalarını sağlar.

* Güvenliği artırma – Azure AD cihazları, cihaz veya kullanıcı kimliğine göre kaynaklara [koşullu erişim ilkeleri](../conditional-access/require-managed-devices.md) uygulamanızı sağlar. Koşullu erişim ilkeleri, [Azure AD kimlik koruması](../identity-protection/overview-identity-protection.md)kullanarak ek koruma sunabilir. Bir cihazın Azure AD 'ye katılması, güvenli bir şekilde [Passwordbir kimlik doğrulama](../authentication/concept-authentication-passwordless.md) stratejisi ile Güvenliğinizi artırmaya yönelik bir önkoşuldur.

* Kullanıcı deneyimini geliştirme: Azure AD 'de cihaz kimlikleri sayesinde kullanıcılarınıza kuruluşunuzun bulut tabanlı kaynaklarına hem kişisel hem de şirket cihazlarından kolay erişim sağlayabilirsiniz. Yöneticiler, tüm Windows cihazlarında birleştirilmiş bir deneyim için [Enterprise State Roaming](enterprise-state-roaming-overview.md) etkinleştirebilir.

* Dağıtım ve yönetimi basitleştirme – cihaz kimlik yönetimi, [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot), [toplu sağlama](/mem/intune/enrollment/windows-bulk-enroll)ve [self servis: kullanıma hazır deneyim (OOBE)](../user-help/user-help-join-device-on-network.md)ile cihazları Azure AD 'ye getirme sürecini basitleştirir. Bu cihazları, [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)gibi mobil cihaz YÖNETIMI (MDM) araçlarıyla ve [Azure Portal](https://portal.azure.com/)kimlikleri ile yönetebilirsiniz.

### <a name="training-resources"></a>Eğitim kaynakları

Video:  [cihaz denetimleriyle koşullu erişim](https://youtu.be/NcONUf-jeS4)

SSS: [Azure AD cihaz YÖNETIMI SSS](faq.yml)  ve [Ayarlar ve VERI dolaşımı hakkında SSS](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Dağıtım projesini planlayın

Ortamınızda bu dağıtımın stratejisini belirlerken kurumsal ihtiyaçlarınızı göz önünde bulundurun.

### <a name="engage-the-right-stakeholders"></a>Doğru paydaşlara katılın

Teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri nedeniyle bu, genellikle bu şekilde yapılır. Bu sınırları önlemek için, [doğru paydaşları](../fundamentals/active-directory-deployment-plans.md) ve projedeki paydaş rollerinin iyi anlaşıldığından emin olun. 

Bu plan için aşağıdaki paydaşları listenize ekleyin:

| Rol| Açıklama |
| - | - |
| Cihaz Yöneticisi| Cihaz ekibinin, planın kuruluşunuzun cihaz gereksinimlerini karşıladığını doğrulayabilmeleri için bir temsilcisidir. |
| Ağ Yöneticisi| Ağ takımınızdan, ağ gereksinimlerini karşıladığınızdan emin olmak için bir temsilci. |
| Cihaz yönetimi ekibi| Cihazların envanterini yöneten takım. |
| İşletim sistemine özgü yönetici takımları| Belirli işletim sistemi sürümlerini destekleyen ve yöneten takımlar. Örneğin, bir Mac veya iOS odaklı ekibi olabilir. |

### <a name="plan-communications"></a>İletişimi planlama

İletişim, her yeni hizmetin başarısı için önemlidir. Deneyimlerinizin nasıl değiştirileceği, ne zaman değiştirileceği ve sorunlarla karşılaştıkları durumlarda nasıl destek kazanabilecekleri hakkında daha etkin bir şekilde iletişim kurun.

### <a name="plan-a-pilot"></a>Pilot planı planlayın

Tümleştirme yönteminizin ilk yapılandırmasının bir test ortamında veya küçük bir test cihazı grubuyla olması önerilir. [Pilot Için en iyi uygulamalar](../fundamentals/active-directory-deployment-plans.md)bölümüne bakın.

Karma Azure AD JOIN dağıtımı basittir ve bu, son kullanıcı eylemi gerekmeden bir yöneticinin görevi için %100 ' dır. [Karma Azure AD JOIN 'in](hybrid-azuread-join-control.md) tüm kuruluş genelinde tek seferde etkinleştirmeden önce denetimli bir doğrulaması yapmak isteyebilirsiniz.

## <a name="choose-your-integration-methods"></a>Tümleştirme yöntemlerinizi seçin

Kuruluşunuz, tek bir Azure AD kiracısında birden çok cihaz tümleştirme yöntemi kullanabilir. Amaç, cihazlarınızı Azure AD 'de güvenli bir şekilde yönetilmek için uygun olan yöntemi (ler) seçin. Bu kararı sahiplik, cihaz türleri, birincil kitleci ve kuruluşunuzun altyapısı gibi birçok parametre vardır.

Aşağıdaki bilgiler hangi tümleştirme yöntemlerinin kullanılacağına karar vermenize yardımcı olabilir.

### <a name="decision-tree-for-devices-integration"></a>Cihaz tümleştirme için karar ağacı

Kuruluşa ait cihazların seçeneklerini öğrenmek için bu ağacı kullanın. 

> [!NOTE]
> Kişisel veya kendi cihazını getir (BYOD) senaryoları bu diyagramda gösterilmez. Bunlar her zaman Azure AD kaydıyla sonuçlanır.

 ![Karar ağacı](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Karşılaştırma matrisi

iOS ve Android cihazları yalnızca Azure AD kayıtlı olabilir. Aşağıdaki tabloda Windows istemci cihazları için üst düzey noktalar sunulmaktadır. Genel bakış olarak kullanın, ardından farklı tümleştirme yöntemlerini ayrıntılı olarak inceleyin.

| Değerlendirme | Azure AD kayıtlı| Azure AD'ye katılım| Hibrit Azure AD'ye katılım |
| - | - | - | - |
| **İstemci işletim sistemleri**| | |  |
| Windows 10 cihazları| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| Windows alt düzey cihazlar (Windows 8.1 veya Windows 7)| | | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
|**Oturum açma seçenekleri**| | |  |
| Son Kullanıcı yerel kimlik bilgileri| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| |  |
| Parola| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| Cihaz PIN'i| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| |  |
| İş İçin Windows Hello| | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| FıDO 2,0 güvenlik anahtarları| | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator uygulaması (passwordless)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
|**Temel işlevler**| | |  |
| Bulut kaynaklarına SSO| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| Şirket içi kaynaklarda çoklu oturum açma| | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| Koşullu Erişim <br> (Cihazların uyumlu olarak işaretlenmesini gerektir) <br> (MDM tarafından yönetiliyor olması gerekir)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)|![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
Koşullu Erişim <br>(Karma Azure AD 'ye katılmış cihazlar gerektir)| | | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)
| Windows oturum açma ekranından self servis parola sıfırlama| | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| Windows Hello PIN sıfırlama| | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |
| Cihazlar arasında kurumsal durum dolaşımı| | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD kaydı 

Kayıtlı cihazlar genellikle [Microsoft Intune](/mem/intune/enrollment/device-enrollment)ile yönetilir. Cihazlar, işletim sistemine bağlı olarak çeşitli yollarla Intune 'A kaydedilir. 

Azure AD kayıtlı cihazlar, kendi cihazlarını getir (KCG) ve şirkete ait cihazlar için bulut kaynaklarına SSO 'ya yönelik destek sağlar. Kaynaklara erişim, cihaza ve kullanıcıya uygulanan Azure AD [koşullu erişim ilkelerini](../conditional-access/require-managed-devices.md) temel alır.

### <a name="registering-devices"></a>Cihazları kaydetme

Kayıtlı cihazlar genellikle [Microsoft Intune](/mem/intune/enrollment/device-enrollment)ile yönetilir. Cihazlar, işletim sistemine bağlı olarak çeşitli yollarla Intune 'A kaydedilir. 

KCG ve şirkete ait mobil cihaz, Şirket Portalı uygulamasını yükleyen kullanıcılar tarafından kaydedilir.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Cihazlarınızı kaydettirmek, kuruluşunuz için en iyi seçenektir. aşağıdaki kaynaklara bakın:

* [Azure AD kayıtlı cihazlarına](concept-azure-ad-register.md)bu genel bakış.

* Bu son kullanıcı belgesi, [Kişisel cihazınızı kuruluşunuzun ağına kaydeder](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Azure AD'ye katılım

Azure AD JOIN, Windows ile bulut öncelikli bir modele doğru geçiş yapmanızı sağlar. Cihaz yönetiyorsanız modernleştirin planlıyorsanız ve cihazla ilgili BT maliyetlerini azaltmak için harika bir temel sağlar. Azure AD katılımı yalnızca Windows 10 cihazlarıyla birlikte kullanılabilir. Yeni cihazlar için ilk seçenek olarak bunu göz önünde bulundurun.

Ancak, [Azure AD 'ye katılmış cihazlar](azuread-join-sso.md) , kuruluşun ağı üzerinde olduklarında, şirket ıçı kaynaklarda SSO yapabilir, dosya, yazdırma ve diğer uygulamalar gibi şirket içi sunucularda kimlik doğrulaması yapabilir.

Kuruluşunuz için en iyi seçenektir, aşağıdaki kaynaklara bakın:

* Bu [Azure AD 'ye katılmış cihazlara](concept-azure-ad-join.md)genel bakış.

* [Azure AD JOIN uygulama planı](azureadjoin-plan.md)hakkında bilgi edinin.

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Cihazlarınıza Azure AD katılımı sağlama

Azure AD JOIN sağlamak için aşağıdaki yaklaşımlara sahip olursunuz:

* Self Servis: [Windows 10 ilk çalıştırma deneyimi](azuread-joined-devices-frx.md)

Bir cihazda Windows 10 Professional veya Windows 10 Enterprise yüklüyse deneyim, varsayılan olarak şirkete ait cihazlar için kurulum işlemine ayarlanır.

* [Windows, kutudan çıkar deneyimi (OOBE) veya Windows ayarlarından](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Toplu kayıt](/mem/intune/enrollment/windows-bulk-enroll)

[Bu yaklaşımların dikkatli karşılaştırmasının](azureadjoin-plan.md)ardından dağıtım yordamınızdan birini seçin.

Azure AD JOIN 'in bir cihaz için en iyi çözüm olduğunu ve bu cihazın farklı durumlarda zaten olabileceğini belirleyebilirsiniz. Yükseltme konuları aşağıda verilmiştir.

| Geçerli cihaz durumu| İstenen cihaz durumu| Nasıl yapılır |
| - | - | - |
| Şirket içi etki alanına katılmış| Azure AD Join| Azure AD 'ye katılmadan önce cihazın şirket içi etki alanından katılmasını kaldır |
| Karma Azure AD katılımı| Azure AD Join| Azure AD 'ye katılmadan önce cihazın şirket içi etki alanından ve Azure AD 'den katılmasını kaldır |
| Azure AD kayıtlı| Azure AD Join| Azure AD 'ye katılmadan önce cihazın kaydını kaldırın |


## <a name="hybrid-azure-ad-join"></a>Hibrit Azure AD'ye katılım

Şirket içi Active Directory ortamınız varsa ve Active Directory etki alanına katılmış bilgisayarlarınızı Azure AD 'ye katmak istiyorsanız bunu karma Azure AD JOIN ile gerçekleştirebilirsiniz. Windows geçerli ve Windows alt düzey cihazları dahil olmak üzere [çok çeşitli Windows cihazlarını](hybrid-azuread-join-plan.md)destekler.

Çoğu kuruluş, etki alanına katılmış cihazlara sahiptir ve grup ilkesi veya System Center Configuration Manager (SCCM) aracılığıyla bunları yönetebilir. Bu durumda, hibrit Azure AD JOIN 'in, mevcut yatırımdan yararlanarak avantajlara başlamak için yapılandırılmasını öneririz.

Karma Azure AD katılımı, kuruluşunuz için en iyi seçenektir. aşağıdaki kaynaklara bakın:

* Bu [karma Azure AD 'ye katılmış cihazlara](concept-azure-ad-join-hybrid.md)genel bakış.

* [Hibrit Azure AD JOIN uygulama](hybrid-azuread-join-plan.md) planı hakkında bilgi edinin.

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Cihazlarınıza karma Azure AD katılımı sağlama

[Kimlik altyapınızı gözden geçirin](hybrid-azuread-join-plan.md). Azure AD Connect, için karma Azure AD birleştirmesini Yapılandırma Sihirbazı sağlar:

* [Federasyon etki alanları](hybrid-azuread-join-federated-domains.md)

* [Yönetilen etki alanları](hybrid-azuread-join-managed-domains.md)

Gerekli Azure AD Connect sürümünü yüklemek sizin için bir seçenek değilse, [karma Azure AD katılımı el ile yapılandırma](hybrid-azuread-join-manual.md)konusuna bakın. 

> [!NOTE] 
> Şirket içi etki alanına katılmış Windows 10 cihazı, karma Azure AD 'ye varsayılan olarak katılmış hale gelecek şekilde Azure AD 'ye otomatik katılma girişiminde bulunur. Bu, yalnızca doğru ortamı ayarladığınızda başarılı olur. 

Hibrit Azure AD JOIN 'in bir cihaz için en iyi çözüm olduğunu ve bu cihazın zaten farklı bir durumda olabileceğini belirleyebilirsiniz. Yükseltme konuları aşağıda verilmiştir.

| Geçerli cihaz durumu| İstenen cihaz durumu| Nasıl yapılır |
| - | - | - |
| Şirket içi etki alanına ekleme| Karma Azure AD katılımı| Azure 'a katmak için Azure AD Connect veya AD FS kullanma |
| Şirket içi çalışma grubu katılmış veya yeni| Karma Azure AD katılımı| [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)ile desteklenir. Aksi takdirde cihazın, karma Azure AD JOIN 'ten önce şirket içi etki alanına katılmış olması gerekir |
| Azure AD'ye katılanlar| Karma Azure AD katılımı| Azure AD 'den katılmayı, şirket içi çalışma grubuna veya yeni duruma getirir. |
| Azure AD kayıt defteri| Karma Azure AD katılımı| Windows sürümüne bağlıdır. [Bu noktalara göz atın](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Cihazlarınızı yönetme

Cihazlarınızı Azure AD 'ye kaydettikten veya katıldıktan sonra, cihaz kimliklerinizi yönetmek için [Azure Portal](https://portal.azure.com/) merkezi bir yer olarak kullanın. Azure Active Directory cihazlar sayfası şunları yapmanızı sağlar:

* [Cihaz ayarlarınızı yapılandırın](device-management-azure-portal.md#configure-device-settings)
* Windows cihazlarını yönetmek için yerel yönetici olmanız gerekir. [Azure AD, Azure AD 'ye katılmış cihazlar için bu üyeliği](assign-local-admin.md), cihaz yöneticisi rolüne sahip olanları otomatik olarak tüm katılmış cihazlara ekleyerek güncelleştirir.

[Eski cihazları yöneterek](manage-stale-devices.md)ortamı temizlediğinizden emin olun ve mevcut cihazları yönetmek için kaynaklarınızı odaklayın.

* [Cihazla ilgili denetim günlüklerini gözden geçirme](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Desteklenen cihaz yönetimi araçları

Yöneticiler, bu kayıtlı ve birleştirilmiş cihazları ek cihaz yönetim araçları kullanarak güvenli hale getirebilirsiniz. Bu araçlar, depolamanın şifreli, parola karmaşıklığı, yazılım yüklemeleri ve yazılım güncelleştirmeleri gibi kuruluş gerektiren yapılandırmaların zorlanmasını sağlamak için bir yol sağlar. 

Tümleşik Aygıtlar için desteklenen ve desteklenmeyen platformları gözden geçirin:

| Cihaz yönetimi araçları| Azure AD kayıtlı| Azure AD'ye katılım| Hibrit Azure AD'ye katılım|
| - | - | - | - |
| [Mobil cihaz yönetimi (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Örnek: Microsoft Intune| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| 
| [Microsoft Intune ve Microsoft uç noktası ile ortak yönetim Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 ve üzeri)| | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| 
| [Grup İlkesi](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Yalnızca Windows)| | | ![Bu değerler için onay işareti.](./media/plan-device-deployment/check.png)| 



 [Mobil uygulama yönetimi 'ni (MAM)](/mem/intune/apps/app-management) kayıtlı IOS veya Android cihazlar için cihaz yönetimiyle veya bunlarla birlikte Microsoft Intune düşünmeniz önerilir.

 Yöneticiler ayrıca, yönetimi kolaylaştırmak ve kaynakların konsolidasyonu ve merkezileşmeyi aracılığıyla maliyetleri azaltmak için kuruluşlarında Windows işletim sistemlerini barındıran [sanal masaüstü altyapısı (VDI) platformlarını dağıtabilir](howto-device-identity-virtual-desktop-infrastructure.md) . 

### <a name="troubleshoot-device-identities"></a>Cihaz kimliği sorunlarını giderme

* [Dsregcmd komutunu kullanarak cihazların sorunlarını giderme](troubleshoot-device-dsregcmd.md)

* [Azure Active Directory Enterprise State Roaming ayarları sorunlarını giderme](enterprise-state-roaming-troubleshooting.md)

Etki alanına katılmış Windows cihazları için karma Azure AD JOIN 'i tamamlamada sorunlarla karşılaşırsanız, bkz.:

* [Windows geçerli cihazlarda karma Azure AD katılımı sorunlarını giderme](troubleshoot-hybrid-join-windows-current.md)

* [Windows alt düzey cihazlarda karma Azure AD katılımı sorunlarını giderme](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD JOIN Uygulamanızı planlayın](azureadjoin-plan.md)
* [Hibrit Azure AD JOIN Uygulamanızı planlayın](hybrid-azuread-join-plan.md)
* [Cihaz kimliklerini yönetme](device-management-azure-portal.md)
