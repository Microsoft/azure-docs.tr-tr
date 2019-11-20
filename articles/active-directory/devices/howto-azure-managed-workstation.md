---
title: Azure tarafından yönetilen iş istasyonlarını dağıtma-Azure Active Directory
description: Yanlış yapılandırma veya uzlaşmadan dolayı ihlal riskini azaltmak için güvenli, Azure tarafından yönetilen iş istasyonları dağıtmayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: be9e6374d92fbb7bb1c4b5a2a9e154119c5baf87
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377497"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Güvenli, Azure tarafından yönetilen bir iş istasyonu dağıtma

Artık [güvenli iş istasyonlarını anladığınıza](concept-azure-managed-workstation.md)göre, dağıtım işleminin başlaması zaman atalım. Bu kılavuzda, başlangıçtan daha güvenli bir iş istasyonu oluşturmak için tanımlı profilleri kullanın.

![Güvenli bir iş istasyonunun dağıtımı](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Çözümü dağıtabilmeniz için önce bir profil seçmelisiniz. Birden çok profili dağıtımda eşzamanlı olarak kullanabilir ve Etiketler ya da gruplarla atayabilirsiniz.
> [!NOTE]
> Gereksinimlerinizin gerektirdiği her türlü profili uygulayın. Intune 'a atayarak başka bir profile geçebilirsiniz.

| Profil | Düşük | Gelişmiş | Yüksek | Özelleştirilmiş | Güvenli | Yalıtılmış |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD 'de Kullanıcı | Evet | Evet | Evet | Evet | Evet | Evet |
| Intune tarafından yönetilen | Evet | Evet | Evet | Evet | Evet | Evet |
| Cihaz-Azure AD kayıtlı | Evet |  |  |  |  | |   |
| Cihaz-Azure AD 'ye katılmış |   | Evet | Evet | Evet | Evet | Evet |
| Intune güvenlik temeli uygulandı |   | Evet <br> Leştirilmiş | Evet <br> (HighSecurity) | Evet <br> (NCSC) | Evet <br> Korunmalıdır |  NA |
| Donanım, güvenli Windows 10 standartlarına karşılıyor |   | Evet | Evet | Evet | Evet | Evet |
| Microsoft Defender ATP etkin |   | Evet  | Evet | Evet | Evet | Evet |
| Yönetici haklarının kaldırılması |   |   | Evet  | Evet | Evet | Evet |
| Microsoft Autopilot kullanarak dağıtım |   |   | Evet  | Evet | Evet | Evet |
| Yalnızca Intune tarafından yüklenen uygulamalar |   |   |   | Evet | Evet |Evet |
| Onaylanan listeyle kısıtlanan URL 'Ler |   |   |   | Evet | Evet |Evet |
| Internet engellendi (gelen/giden) |   |   |   |  |  |Evet |

## <a name="license-requirements"></a>Lisans gereksinimleri

Bu kılavuzda ele alınan kavramlar, Microsoft 365 Kurumsal E5 veya denk bir SKU olduğunu varsayar. Bu kılavuzdaki önerilerden bazıları, daha düşük SKU 'Lar ile uygulanabilir. Daha fazla bilgi için bkz. [Microsoft 365 Kurumsal lisanslama](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Lisans sağlamayı otomatik hale getirmek için kullanıcılarınız için [grup tabanlı lisanslamayı](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) göz önünde bulundurun.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory yapılandırması

Azure Active Directory (Azure AD), yönetici iş istasyonlarınız için kullanıcıları, grupları ve aygıtları yönetir. Kimlik hizmetlerini ve özellikleri bir [yönetici hesabıyla](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)etkinleştirmeniz gerekir.

Güvenli iş istasyonu yönetici hesabını oluşturduğunuzda, hesabı geçerli iş istasyonunuzda kullanıma sunun. Bu ilk yapılandırmayı ve tüm genel yapılandırmayı yapmak için bilinen bir güvenli cihaz kullandığınızdan emin olun. İlk seferlik deneyimin saldırıya maruz kalma olasılığını azaltmak için, [kötü amaçlı yazılımdan bulaşmaları önleme kılavuzunu](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)takip etmeyi göz önünde bulundurun.

Ayrıca, en azından yöneticileriniz için çok faktörlü kimlik doğrulaması gerekir. Bkz. Uygulama Kılavuzu için [bulut tabanlı MFA dağıtma](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) .

### <a name="azure-ad-users-and-groups"></a>Azure AD kullanıcıları ve grupları

1. Azure Portal,**kullanıcılara** >  Yeni > Kullanıcı Azure Active Directory gidin.
1. [Kullanıcı oluşturma öğreticisindeki](https://docs.microsoft.com/Intune/quickstart-create-user)adımları izleyerek cihaz yöneticinizi oluşturun.
1. Girmesini
   * **Ad** -güvenli Iş istasyonu Yöneticisi
   * **Kullanıcı adı** - `secure-ws-admin@identityitpro.com`
   * **Dizin rolü** - **sınırlı yönetici** ve **Intune yönetici** rolünü seçin.
1. **Oluştur**’u seçin.

Sonra iki grup oluşturursunuz: iş istasyonu kullanıcıları ve iş istasyonu cihazları.

Azure Portal **Azure Active Directory** > gruplarYeni > **Grup**' a gidin.

1. İş istasyonu kullanıcıları grubu için, kullanıcılara lisansların sağlanması otomatik hale getirmek üzere [grup tabanlı lisanslama](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) yapılandırmak isteyebilirsiniz.
1. İş istasyonu kullanıcıları grubu için şunu girin:
   * **Grup türü** -güvenlik
   * **Grup adı** -güvenli Iş istasyonu kullanıcıları
   * **Üyelik türü** -atandı
1. Güvenli iş istasyonu yönetici kullanıcısını ekleyin:`secure-ws-admin@identityitpro.com`
1. Güvenli iş istasyonlarını yönetmek için diğer kullanıcıları ekleyebilirsiniz.
1. **Oluştur**’u seçin.

1. İş istasyonu cihazları grubu için şunu girin:
   * **Grup türü** -güvenlik
   * **Grup adı** -güvenli iş istasyonları
   * **Üyelik türü** -atandı
1. **Oluştur**’u seçin.

### <a name="azure-ad-device-configuration"></a>Azure AD cihaz yapılandırması

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Cihazları Azure AD 'ye kimlerin katılabileceği belirtin

Yönetim güvenlik grubunuzun cihazları etki alanınıza katılmasına izin vermek için Active Directory cihaz ayarınızı yapılandırın. Bu ayarı Azure portal yapılandırmak için:

1. **Azure Active Directory** > cihazlarcihaz > **ayarları**' na gidin.
1. **Kullanıcılar cihazları Azure AD 'ye katabilir**ve ardından "güvenli Iş istasyonu kullanıcıları" grubunu seçerek **Seçili** ' ı seçin.

#### <a name="removal-of-local-admin-rights"></a>Yerel yönetici haklarının kaldırılması

Bu yöntem VIP, DevOps ve güvenli düzey iş istasyonlarının kullanıcılarının makinelerinde yönetici haklarına sahip olmasını gerektirir. Bu ayarı Azure portal yapılandırmak için:

1. **Azure Active Directory** > cihazlarcihaz > **ayarları**' na gidin.
1. **Azure AD 'ye katılmış cihazlarda ek yerel Yöneticiler**altında **hiçbiri** ' ni seçin.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Cihazlara katılması için çok faktörlü kimlik doğrulaması gerektir

Cihazları Azure AD 'ye katma işlemini daha da güçlendirin:

1. **Azure Active Directory** > cihazlarcihaz > **ayarları**' na gidin.
1. **Cihazlara katmak Için Multi-Factor auth iste**altında **Evet** ' i seçin.
1. **Kaydet**’i seçin.

#### <a name="configure-mdm"></a>MDM 'yi yapılandırma

Azure portal:

1.  >  **Azure Active Directory** > **Mobility (MDM ve MAM)** **Microsoft Intune**gidin.
1. **MDM Kullanıcı kapsamı** ayarını **Tümü**olarak değiştirin.
1. **Kaydet**’i seçin.

Bu adımlar, Intune ile herhangi bir cihazı yönetmenizi sağlar. Daha fazla bilgi için bkz [. Intune hızlı başlangıç: Windows 10 cihazları](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)için otomatik kayıt ayarlama. Intune yapılandırma ve uyumluluk ilkelerini gelecekteki bir adımda oluşturursunuz.

#### <a name="azure-ad-conditional-access"></a>Azure AD Koşullu Erişim

Azure AD koşullu erişimi, ayrıcalıklı yönetim görevlerinin uyumlu cihazlarla kısıtlanmasını sağlamaya yardımcı olabilir. **Güvenli Iş Istasyonu kullanıcıları** grubunun önceden tanımlanmış üyeleri, bulut uygulamalarında oturum açarken Multi-Factor Authentication gerçekleştirmek için gereklidir. En iyi uygulama, acil durum erişim hesaplarını ilkeden dışlayamazsınız. Daha fazla bilgi için bkz. [Azure AD 'de acil durum erişim hesaplarını yönetme](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Azure portal koşullu erişimi yapılandırmak için:

1.  > **Koşullu**erişimYeni > **ilke**Azure Active Directory gidin.
1. Girmesini
   * **Ad** -güvenli cihaz gerekli ilkesi
   * Atamalar
     * **Kullanıcılar ve gruplar**
       * Ekle- **Kullanıcılar ve gruplar** -daha önce oluşturulan **güvenli iş istasyonu Kullanıcı** grubunu seçin.
       * Dışlama- **Kullanıcılar ve gruplar** -kuruluşunuzun acil erişim hesapları ' nı seçin.
     * **Bulut uygulamaları** - **tüm bulut uygulamalarını**dahil edin.
    * Erişim denetimleri
      * **Grant** - **erişim ver** radyo düğmesini seçin.
        * **Multi-Factor Authentication gerektir**.
        * **Cihazın uyumlu olarak Işaretlenmesini gerektir**.
        * Birden çok denetim için- **Seçili tüm denetimleri gerektir**.
    * İlkeyi **etkinleştirin.**

Kullanıcıların şirket kaynaklarına erişebilecekleri ülkeleri engelleyen ilkeler oluşturma seçeneğiniz vardır. IP konum tabanlı koşullu erişim ilkeleri hakkında daha fazla bilgi için [Azure Active Directory Koşullu erişim içindeki konum koşuluna](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)bakın.

## <a name="intune-configuration"></a>Intune yapılandırması

### <a name="configure-enrollment-status"></a>Kayıt durumunu yapılandırma

Güvenli iş istasyonunuzun güvenilir bir temiz cihaz olduğundan emin olmak önemlidir. Yeni cihazlar satın alırken, Microsoft 'un, sağlama zinciri yönetimi sırasında güvenlik açıklarına maruz kalma oranını sınırlayan [Windows 10 Pro 'Nun Windows 10 Pro](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)'ya ayarlı olduğunu insist sağlayabilirsiniz. Tedarikçinizden bir cihaz aldıktan sonra, Autopilot kullanarak bunları S modundan değiştirebilirsiniz. Aşağıdaki kılavuz, dönüşüm işlemini uygulama hakkında ayrıntılar sağlar.

Cihazların kullanılmadan önce tam olarak yapılandırıldığından emin olmak için Intune, **tüm uygulamalar ve profiller yüklenene kadar cihaz kullanımını engellemek**için bir yol sağlar.

**Azure Portal**:
1. **Microsoft Intune**cihazkaydı > **Windows kayıt** **kayıt durumu sayfası**varsayılanAyarları > ' na gidin. >  >  > 
1. **Uygulama profili yükleme Ilerlemesini göster** ' i **Evet**olarak ayarlayın.
1. **Tüm uygulamalar ve profiller Evet 'e yüklenene kadar cihaz kullanımını engelle** ' ye ayarlayın.

### <a name="create-an-autopilot-deployment-profile"></a>Bir Autopilot dağıtım profili oluşturma

Bir cihaz grubu oluşturduktan sonra, Autopilot cihazlarını yapılandırmak için bir dağıtım profili oluşturmanız gerekir.

Azure portal Intune 'da:

1. **Cihaz kaydı** > **Windows kayıt** > **dağıtım**profilleriprofil > **Oluştur**' u seçin.
1. Girmesini
   * Ad- **güvenli iş istasyonu dağıtım profili**.
   * Açıklama- **güvenli iş Istasyonlarının dağıtımı**.
   * **Tüm hedeflenen cihazları Autopilot olarak Dönüştür** ' ü **Evet**olarak ayarlayın. Bu ayar, listedeki tüm cihazların Autopilot dağıtım hizmetine kaydedildiğinden emin olur. Kaydın işlenmesi için 48 saat kadar bekleyin.
1. **İleri**’yi seçin.
   * **Dağıtım modu**Için, **kendi kendine dağıtma (Önizleme)** öğesini seçin. Bu profile sahip cihazlar, cihazı kaydeden kullanıcıyla ilişkilendirilir. Cihazı kaydetmek için kullanıcı kimlik bilgileri gerekir. Bir cihazı **kendi kendine** dağıtma modunda dağıtmak, paylaşılan bir modele dizüstü bilgisayar dağıtmanıza imkan tanımak için gereklidir. Cihaz bir kullanıcıya ilk kez atanana kadar hiçbir Kullanıcı Ataması gerçekleşmeyecektir. Sonuç olarak, bir kullanıcı ataması tamamlanana kadar BitLocker gibi tüm kullanıcı ilkeleri etkinleştirilmez. Güvenli bir cihazda oturum açma hakkında daha fazla ayrıntı için bkz. [Seçili profiller](https://docs.microsoft.com/intune/device-profile-assign).
   * **Azure AD 'ye katılım** kutusu, **Azure AD 'ye katılmış** ve gri renkte olmalıdır.
   * Langugage (bölge), Kullanıcı hesabı türü **standardını**seçin. 
1. **İleri**’yi seçin.
   * Önceden yapılandırdıysanız bir kapsam etiketi seçin.
1. **İleri**’yi seçin.
1. **Seçili gruplara** **atanan**  > atamalar'ı > seçin. **Dahil edilecek grupları seçin**' de, **güvenli iş istasyonu kullanıcıları**' nı seçin.
1. **İleri**’yi seçin.
1. Profili oluşturmak için **Oluştur**’u seçin. Autopilot dağıtım profili artık cihazlara atanmak üzere hazırdır.

Autopilot ' deki cihaz kaydı, cihaz türü ve rolüne göre farklı bir kullanıcı deneyimi sağlar. Dağıtım örneğimizde, güvenli cihazların toplu olarak dağıtıldığı ve paylaşılabileceği, ancak ilk kez kullanıldığı, cihazın bir kullanıcıya atandığı bir model gösterilmektedir. Daha fazla bilgi için bkz. [Intune Autopilot cihaz kaydı](https://docs.microsoft.com/intune/device-enrollment).

### <a name="configure-windows-update"></a>Windows Update Yapılandır

Windows 10 ' un güncel tutulması, yapabileceğiniz en önemli şeylerden biridir. Windows 'u güvenli bir durumda tutmak için güncelleştirmelerin iş istasyonlarına uygulanma hızını yönetmek üzere bir güncelleştirme halkası dağıtırsınız. 

Bu kılavuzda, yeni bir güncelleştirme halkası oluşturmanız ve aşağıdaki varsayılan ayarları değiştirmeniz önerilir:

Azure portalında:

1. **Microsoft Intune** > **yazılım**güncelleştirmeleriWindows > **10 güncelleştirme halkaları**' na gidin.
1. Girmesini
   * Ad- **Azure yönetilen iş istasyonu güncelleştirmeleri**
   * Bakım kanalı- **Windows Insider-Fast**
   * Kalite güncelleştirmesi erteleme (gün)- **3**
   * Özellik Güncelleştirmesi erteleme süresi (gün)- **3**
   * Otomatik Güncelleştirme davranışı- **Son Kullanıcı denetimi olmadan otomatik yükleme ve yeniden başlatma**
   * Kullanıcının Windows güncelleştirmelerini duraklatmasını Engelle- **Engelle**
   * İş saatleri dışında yeniden başlatma için kullanıcının onayını iste- **gerekli**
   * Kullanıcının yeniden başlatılmasına izin ver (bağlı yeniden başlatma)- **gerekli**
   * Otomatik yeniden başlatmadan sonra kullanıcıların kullanıcı tarafından yeniden başlatılmasını sağlamak için geçiş (gün)- **3**
   * Yeniden başlatma için yeniden başlatma anımsatıcısı (gün)- **3**
   * Bekleyen yeniden başlatmalar için son tarihi ayarla (gün)- **3**

1. **Oluştur**’u seçin.
1. **Atamalar** sekmesinde, **güvenli iş istasyonları** grubunu ekleyin.

Windows Update ilkeleri hakkında daha fazla bilgi için bkz. [Ilke CSP-güncelleştirme](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune tümleştirmesi

Windows Defender ATP ve Microsoft Intune, güvenlik ihlallerini önlemeye yardımcı olmak için birlikte çalışır. Ayrıca, ihlallerinin etkisini de sınırlayabilir. ATP özellikleri, gerçek zamanlı tehdit algılama ve uç nokta cihazlarının kapsamlı şekilde denetlenmesini ve günlüğe kaydedilmesini olanaklı kılmaktadır.

Windows Defender ATP ve Intune tümleştirmesini yapılandırmak için Azure portal gidin.

1.  > **Cihaz**uyumluluğuWindows > **Defender ATP**Microsoft Intune gidin.
1. Windows Defender ATP 'yi **yapılandırma**altındaki adım 1 ' de, Windows Defender **güvenlik merkezi 'Nde Microsoft Intune IÇIN Windows Defender ATP 'yi bağla**' yı seçin.
1. Windows Defender Güvenlik Merkezi 'nde:
   1. **Ayarlar** > **Gelişmiş Özellikler**' i seçin.
   1. **Microsoft Intune bağlantı**için **Açık**' ı seçin.
   1. **Tercihleri kaydet**' i seçin.
1. Bir bağlantı kurulduktan sonra Intune 'a dönün ve en üstte **Yenile** ' yi seçin.
1. Windows **cihazları sürüm 10.0.15063 ve üstünü Windows Defender ATP 'ye bağlama** ' yı **Açık**olarak ayarlayın.
1. **Kaydet**’i seçin.

Daha fazla bilgi için bkz. [Windows Defender Gelişmiş tehdit koruması](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>İş istasyonu profili sağlamlaştırma

Çözümün sağlamlaştırılmasını başarıyla tamamlaması için uygun betiği indirip yürütün. İstediğiniz **profil düzeyiniz**için indirme bağlantılarını bulun:

| Profil | İndirme konumu | Dosya adı |
| --- | --- | --- |
| Düşük güvenlik | Yok |  Yok |
| Geliştirilmiş Güvenlik | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809). ps1 |
| Yüksek güvenlik  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809). ps1 |
| Özelleştirilmiş | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Özelleştirilmiş uyumluluk * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Güvenli | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline. ps1 |

\*Özelleştirilmiş uyumluluk, NCSC Windows10 SecurityBaseline içinde sunulan özelleştirilmiş yapılandırmayı zorlayan bir betiktir.

Betik başarıyla yürütüldükten sonra, Intune 'da profiller ve ilkeler için güncelleştirmeler yapabilirsiniz. Gelişmiş ve güvenli profiller için betikler, sizin için ilkeler ve profiller oluşturur, ancak ilkeyi **güvenli Iş istasyonları** grubunuza atamanız gerekir.

* Betikler tarafından oluşturulan Intune cihaz yapılandırma profillerini burada bulabilirsiniz: **Azure Portal** **Microsoft Intune cihaz**yapılandırmaprofilleri > . >  > 
* Betikler tarafından oluşturulan Intune cihaz uyumluluk ilkelerini burada bulabilirsiniz: **Azure Portal** **Microsoft Intune cihaz**uyumlulukilkeleri > . >  > 

Betikler tarafından yapılan değişiklikleri gözden geçirmek için profilleri dışarı aktarabilirsiniz. Bu şekilde, [Seccon belgelerinde](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)özetlendiği gibi gerekebilecek ek sağlamlaştırma belirleyebilirsiniz.

Geçerli tüm Intune profillerini dışarı aktarmak `DeviceConfiguration_Export.ps1` için [deviceconfiguration giuhub deposundan](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) Intune veri dışa aktarma betiğini çalıştırın.

## <a name="additional-configurations-and-hardening-to-consider"></a>Göz önünde bulundurulması gereken ek yapılandırma ve sağlamlaştırma

Buradaki kılavuzu izleyerek güvenli bir iş istasyonu dağıttınız. Bununla birlikte, ek denetimler de göz önünde bulundurmanız gerekir. Örneğin:

* farklı tarayıcılara erişimi sınırlandırma
* giden HTTP 'ye izin ver
* select Web sitelerini engelle
* Özel PowerShell betikleri çalıştırmak için izinleri ayarla

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Güvenlik Duvarı yapılandırma hizmeti sağlayıcısında (CSP) kuralları ayarlama

İzin verilen ve engellenen uç noktalarınız için gerektiğinde hem gelen hem de giden kuralların yönetiminde ek değişiklikler yapabilirsiniz. Güvenli iş istasyonuna güvenmeye devam ederken tüm gelen ve giden trafiği reddeden kısıtlamayı gevşeyin. İzin verilen giden sitelerin ortak ve güvenilen Web sitelerini içermesini sağlayabilirsiniz. Daha fazla bilgi için bkz. [güvenlik duvarı yapılandırma hizmeti](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Varsayılan kısıtlı öneriler şunlardır:

* Tüm gelen trafiği reddet
* Tüm giden trafiği reddet

Spamhaus projesi [, etki alanı engelleme listesini (DBL)](https://www.spamhaus.org/dbl/)korur: siteleri engellemek için bir başlangıç noktası olarak kullanılacak iyi bir kaynaktır.

### <a name="manage-local-applications"></a>Yerel uygulamaları yönetme

Üretkenlik uygulamaları da dahil olmak üzere, yerel uygulamalar kaldırıldığında güvenli iş istasyonu gerçekten sağlamlaştırılmış duruma getirilir. Burada, Chrome 'u varsayılan tarayıcı olarak ekler ve Intune 'U kullanarak kullanıcının tarayıcıyı ve eklentilerini değiştirme yeteneğini sınırlayabilirsiniz.

#### <a name="deploy-applications-using-intune"></a>Intune kullanarak uygulama dağıtma

Bazı durumlarda, Google Chrome tarayıcısı gibi uygulamalar, güvenli iş istasyonunda gereklidir. Aşağıdaki örnek, güvenlik grubu **güvenli Iş istasyonlarındaki**cihazlara Chrome yüklemek için yönergeler sağlar.

1. [Windows 64-bit için çevrimdışı yükleyici Chrome paketi](https://cloud.google.com/chrome-enterprise/browser/download/)' ni indirin.
1. Dosyaları ayıklayın ve `GoogleChromeStandaloneEnterprise64.msi` dosyanın konumunu aklınızda olun.
1. **Azure Portal** **Microsoft Intune** **Client Apps** **uygulamaları**Ekle ' ye gidin. >  >  > 
1. **Uygulama türü**' nün altında **iş kolu**' yi seçin.
1. **Uygulama paketi dosyası**altında ayıklanan konumdan `GoogleChromeStandaloneEnterprise64.msi` dosyayı seçin ve **Tamam**' ı seçin.
1. **Uygulama bilgileri**altında bir açıklama ve yayımcı sağlayın. **Tamam**’ı seçin.
1. **Add (Ekle)** seçeneğini belirleyin.
1. **Atamalar** sekmesinde, **atama türü**altında **Kayıtlı cihazlar için kullanılabilir** ' ı seçin.
1. **Dahil edilen gruplar**' ın altında, **güvenli iş istasyonları** grubunu ekleyin.
1. **Tamam**' ı ve ardından **Kaydet**' i seçin.

Chrome ayarlarını yapılandırma hakkında daha fazla bilgi için bkz. [Microsoft Intune Chrome tarayıcısını yönetme](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Özel uygulamalar için şirket portalını yapılandırma

Güvenli modda uygulama yüklemesi, Intune şirket portalı ile kısıtlıdır. Ancak, portalın yüklenmesi Microsoft Store için erişim gerektirir. Güvenli çözümünüzde, şirket portalını çevrimdışı bir mod aracılığıyla tüm cihazlarda kullanılabilir hale getirebilirsiniz.

[Şirket portalı](https://docs.microsoft.com/Intune/store-apps-company-portal-app) Intune tarafından yönetilen bir kopyası, güvenli iş istasyonları kullanıcılarına gönderebilmeniz için kullanabileceğiniz ek araçlara isteğe bağlı erişim sağlar.

Dağıtımı özel hazırlıklar gerektiren Windows 32-bit uygulamaları veya diğer uygulamaları yüklemeniz gerekebilir. Bu gibi durumlarda, [Microsoft Win32 içerik hazırlığı aracı](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) , yükleme için kullanıma hazırlamış `.intunewin` bir biçim dosyası sağlayabilir.

### <a name="use-powershell-to-create-custom-settings"></a>Özel ayarlar oluşturmak için PowerShell 'i kullanma

Ayrıca, PowerShell kullanarak konak yönetimi özelliklerini genişletebilirsiniz. Bu örnek betik, konakta varsayılan bir arka plan ayarlar. Bu, Azure portal ve profiller aracılığıyla da kullanılabilir bir özelliktir. Örnek betik yalnızca PowerShell işlevlerini göstermek için hizmet verir.

Güvenli iş istasyonlarınızda bazı özel denetimleri ve ayarları ayarlamanız gerekebilir. Bu örnek, PowerShell 'in cihazı kullanıma hazırlama ve güvenli bir iş istasyonu olarak kolayca belirlemek için PowerShell 'in özelliğini kullanarak iş istasyonunun arka planını değiştirir.

Microsoft betik merkezi 'ndeki [Setdesktopbackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) betiği Windows 'un başlangıçta bu [ücretsiz, genel arka plan görüntüsünü](https://i.imgur.com/OAJ28zO.png) yüklemesine olanak tanır.

1. Betiği yerel bir cihaza indirin.
1. CustomerXXXX ve arka plan görüntüsünün karşıdan yükleme konumunu güncelleştirin. Örneğimizde, customerXXXX ' i arka plana değiştirirsiniz.  
1. **Azure Portal**Microsoft Intune cihaz yapılandırmasıPowerShellbetikleri > Ekle ' ye gidin. >  >  > 
1. Betik için bir **ad** girin ve **betik konumunu**belirtin.
1. **Yapılandır**' ı seçin.
   1. **Bu betiği, oturum açmış kimlik bilgilerini Evet olarak kullanarak Çalıştır '** a ayarlayın.
   1. **Tamam**’ı seçin.
1. **Oluştur**’u seçin.
1. **Atamaları** > seçin**grupları**seçin.
   1. Güvenlik grubu **güvenli Iş istasyonlarını**ekleyin.
   1. **Kaydet**’i seçin.

## <a name="enroll-and-validate-your-first-device"></a>İlk cihazınızı kaydetme ve doğrulama

1. Cihazınızı kaydetmek için aşağıdaki bilgilere ihtiyacınız vardır:
   * **Seri numarası** -cihaz kasada bulunabilir.
   * **Windows ürün kimliği** -Windows ayarları menüsünden **sistem** > **hakkında** bölümünde bulunur.
   * Cihaz kaydı için gerekli tüm bilgileri içeren bir CSV karma dosyası almak için [Get-Windowsautopilotınfo](https://aka.ms/Autopilotshell) ' yı çalıştırabilirsiniz.
   
     Bilgileri `Get-WindowsAutoPilotInfo – outputfile device1.csv` Intune 'a aktarabileceğiniz CSV dosyası olarak çıktısını almak için çalıştırın.

     > [!NOTE]
     > Betik için yükseltilmiş haklar gerekir. Uzaktan imzalanmış olarak çalışır. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` Komut betiğin doğru çalışmasına izin verir.

   * Bu bilgileri, Windows 10 sürüm 1809 veya daha yüksek bir cihazda oturum açarak toplayabilirsiniz. Ayrıca, donanım satıcınız bu bilgileri de sağlayabilir.
1. **Azure Portal** **Microsoft Intune** **cihaz kaydı Windows kayıt**cihazları >  **-Windows Autopilot cihazlarını Yönet**' e gidin. >  > 
1. **Içeri aktar** ' ı SEÇIN ve CSV dosyanızı seçin.
1. Cihazı **güvenli Iş istasyonları** güvenlik grubuna ekleyin.
1. Yapılandırmak istediğiniz Windows 10 cihazında, **Windows ayarları** > **güncelleştirme & güvenlik** > **Kurtarma**' ya gidin.
   1. **Bu bilgisayarı sıfırla**bölümünden **başlayın** ' ı seçin.
   1. Cihazı sıfırlamak ve yapılandırılmış profil ve uyumluluk ilkeleriyle yeniden yapılandırmak için istemleri izleyin.

Cihazı yapılandırdıktan sonra bir gözden geçirmeyi tamamlayıp yapılandırmayı denetleyin. Dağıtıma devam etmeden önce ilk cihazın doğru şekilde yapılandırıldığını onaylayın.

## <a name="assign-and-monitor"></a>Atama ve izleme

Cihazları ve kullanıcıları atamak için, [Seçili profilleri](https://docs.microsoft.com/intune/device-profile-assign) güvenlik grubunuza eşlemeniz gerekir. Hizmet izinleri gerektiren tüm yeni kullanıcılar da güvenlik grubuna eklenmelidir.

Profilleri [Intune profil izleme](https://docs.microsoft.com/intune/device-profile-monitor)ile izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft Intune](https://docs.microsoft.com/intune/index)hakkında daha fazla bilgi edinin.
* [Azure AD](https://docs.microsoft.com/azure/active-directory/index)'yi anlayın.
