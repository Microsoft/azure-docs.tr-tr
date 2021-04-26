---
title: Passwordless güvenlik anahtarı oturum açma pencereleri-Azure Active Directory
description: FIDO2 güvenlik anahtarlarını kullanarak Azure Active Directory için passwordless güvenlik anahtarı oturumu açmayı nasıl etkinleştirebileceğinizi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190e9c857f1ec9d19eb89493dc4b4a9fb68fac87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653516"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory"></a>Azure Active Directory ile Windows 10 cihazlarında passwordless güvenlik anahtarı oturumunu etkinleştirme 

Bu belge, Windows 10 cihazlarıyla, FIDO2 güvenlik anahtarı tabanlı passwordless kimlik doğrulamasını etkinleştirmeye odaklanır. Bu makalenin sonunda, bir FIDO2 güvenlik anahtarı kullanarak Azure AD ve hibrit Azure AD 'ye katılmış Windows 10 cihazlarında Azure AD hesabınızla oturum açabilirsiniz.

## <a name="requirements"></a>Gereksinimler

| Cihaz Türü | Azure AD'ye katılanlar | Hibrit Azure AD'ye katılmış |
| --- | --- | --- |
| [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Birleşik güvenlik bilgileri kaydı](concept-registration-mfa-sspr-combined.md) | X | X |
| Uyumlu [FIDO2 güvenlik anahtarları](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN, Windows 10 sürüm 1903 veya üstünü gerektirir | X | X |
| [Azure AD 'ye katılmış cihazlar](../devices/concept-azure-ad-join.md) için Windows 10 sürüm 1909 veya üzeri gerekir | X |   |
| [Karma Azure AD 'ye katılmış cihazlar](../devices/concept-azure-ad-join-hybrid.md) için Windows 10 sürüm 2004 veya üzeri gerekir |   | X |
| Tam düzeltme eki uygulanan Windows Server 2016/2019 etki alanı denetleyicileri. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) sürüm 1.4.32.0 veya üzeri |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (isteğe bağlı) | X | X |
| Sağlama paketi (Isteğe bağlı) | X | X |
| Grup ilkesi (Isteğe bağlı) |   | X |

### <a name="unsupported-scenarios"></a>Desteklenmeyen senaryolar

Aşağıdaki senaryolar desteklenmez:

- Windows Server Active Directory Domain Services (AD DS) etki alanına katılmış (Şirket içi cihazlar) dağıtımı.
- Güvenlik anahtarı kullanan RDP, VDı ve Citrix senaryoları.
- Bir güvenlik anahtarı kullanan S/MIME.
- Güvenlik anahtarı kullanarak "farklı çalıştır".
- Bir güvenlik anahtarı kullanarak bir sunucuda oturum açın.
- Çevrimiçi ortamda cihazınızda oturum açmak için güvenlik anahtarınızı kullanmadıysanız, çevrimdışı oturum açmak veya oturumu açmak için kullanamazsınız.
- Birden çok Azure AD hesabı içeren bir güvenlik anahtarıyla Windows 10 cihazının oturum açması veya kilidini açma. Bu senaryo, güvenlik anahtarına eklenen son hesabı kullanır. WebAuthN, kullanıcıların kullanmak istedikleri hesabı seçmesine olanak sağlar.
- Windows 10 sürüm 1809 çalıştıran bir cihazın kilidini açın. En iyi deneyim için Windows 10 sürüm 1903 veya üstünü kullanın.

## <a name="prepare-devices"></a>Cihazları hazırlama

Azure AD 'ye katılmış cihazların Windows 10 sürüm 1909 veya üstünü çalıştırması gerekir.

Karma Azure AD 'ye katılmış cihazların Windows 10 sürüm 2004 veya daha yeni bir sürümü çalıştırması gerekir.

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows oturum açma için güvenlik anahtarlarını etkinleştir

Kuruluşlar, kuruluşunuzun gereksinimlerine bağlı olarak Windows oturum açma için güvenlik anahtarlarının kullanımını etkinleştirmek üzere aşağıdaki yöntemlerden birini veya daha fazlasını kullanmayı seçebilir:

- [Intune ile etkinleştirme](#enable-with-intune)
- [Hedeflenen Intune dağıtımı](#targeted-intune-deployment)
- [Sağlama paketiyle etkinleştir](#enable-with-a-provisioning-package)
- [Grup ilkesi ile etkinleştir (yalnızca karma Azure AD 'ye katılmış cihazlar)](#enable-with-group-policy)

> [!IMPORTANT]
> **Karma Azure AD 'ye katılmış cihazlara** sahip kuruluşlar, WINDOWS 10 FIDO2 güvenlik anahtarı kimlik doğrulamasının çalışması [için önce ŞIRKET içi kaynaklarda FIDO2 kimlik doğrulamasını etkinleştirme](howto-authentication-passwordless-security-key-on-premises.md) makalesindeki adımları **da** tamamlamalıdır.
>
> **Azure AD 'ye katılmış cihazlara** sahip kuruluşlar, cihazları FIDO2 güvenlik anahtarlarıyla şirket içi kaynaklarda kimlik doğrulaması yapabilmek için bunu yapması gerekir.

### <a name="enable-with-intune"></a>Intune ile etkinleştirme

Intune kullanarak güvenlik anahtarlarının kullanımını etkinleştirmek için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1.   >  **Cihaz kaydı**  >  **Windows kaydı**  >  **iş için Windows Hello**  >  **özelliklerini** Microsoft Intune gidin.
1. **Ayarlar**' ın altında, **oturum açma Için güvenlik anahtarlarını kullan** ' ı **etkin** olarak ayarlayın.

Oturum açma için güvenlik anahtarlarının yapılandırılması, Iş için Windows Hello 'Yu yapılandırmaya bağlı değildir.

### <a name="targeted-intune-deployment"></a>Hedeflenen Intune dağıtımı

Kimlik bilgisi sağlayıcısını etkinleştirmek üzere belirli cihaz gruplarını hedeflemek için, Intune aracılığıyla aşağıdaki özel ayarları kullanın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Microsoft Intune**  >  **cihaz yapılandırma**  >  **profilleri**  >  **Profil oluştur**' a gidin.
1. Yeni profili aşağıdaki ayarlarla yapılandırın:
   - Ad: Windows Sign-In için güvenlik anahtarları
   - Açıklama: Windows oturum açma sırasında FIDO güvenlik anahtarlarının kullanılmasını sağlar
   - Platform: Windows 10 ve üzeri
   - Profil türü: özel
   - Özel OMA-URI ayarları:
      - Ad: Windows Sign-In için FIDO güvenlik anahtarlarını açın
      - OMA-URI:./Device/Vendor/MSFT/passportforwork/SecurityKey/usesecuritykeyforsignın
      - Veri türü: tamsayı
      - Değer: 1
1. Bu ilke belirli kullanıcılara, cihazlara veya gruplara atanabilir. Daha fazla bilgi için bkz. [Microsoft Intune kullanıcı ve cihaz profilleri atama](/intune/device-profile-assign).

![Intune özel cihaz yapılandırma ilkesi oluşturma](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Sağlama paketiyle etkinleştir

Intune tarafından yönetilmeyen cihazlar için, işlevselliği etkinleştirmek üzere bir sağlama paketi yükleyebilirsiniz. Windows yapılandırma Tasarımcısı uygulaması [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)yüklenebilir. Bir sağlama paketi oluşturmak için aşağıdaki adımları izleyin:

1. Windows yapılandırma Tasarımcısı 'nı başlatın.
1. **Dosya**  >  **Yeni proje**' yi seçin.
1. Projenize bir ad verin ve projenin oluşturulduğu yolu bir yere göz atın ve ardından **İleri**' yi seçin.
1. **Seçili proje iş akışı** olarak *sağlama paketini* seçili bırakın ve **İleri ' yi** seçin.
1. Görüntülenecek **ve yapılandırılacak ayarları seçin** altında *tüm Windows masaüstü sürümleri* ' ni seçin ve ardından **İleri**' yi seçin.
1. **Son**'u seçin.
1. Yeni oluşturduğunuz projenizde, **çalışma zamanı ayarları**  >  **windowshelloforbusiness**  >  **SecurityKeys**  >  **usesecuritykeyforsign'** e gidin.
1. **Usesecuritykeyforsignın** öğesini *etkin* olarak ayarlayın.
1.   >  **Sağlama paketini** dışarı aktar 'ı seçin
1. **Sağlama paketini açıkla** altında **derleme** penceresinde varsayılan değerleri bırakın ve ardından **İleri**' yi seçin.
1. **Sağlama paketinin güvenlik ayrıntılarını Seç** altında **derleme** penceresinde varsayılan değerleri bırakın ve **İleri ' yi** seçin.
1. **Sağlama paketinin kaydedileceği yeri seçin** ' ın altında **derleme** pencereleri ' ne göz atın veya yolu değiştirin ve **İleri ' yi** seçin.
1. **Sağlama paketini oluşturma** sayfasında **Oluştur** ' u seçin.
1. Oluşturulan iki dosyayı (*ppkg* ve *Cat*), daha sonra makinelere uygulayabileceğiniz bir konuma kaydedin.
1. Oluşturduğunuz sağlama paketini uygulamak için bkz. [sağlama paketi uygulama](/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Windows 10 sürüm 1903 çalıştıran cihazların Ayrıca paylaşılan bılgısayar modunu (*Enablesharedpcmode*) etkinleştirmesi gerekir. Bu işlevi etkinleştirme hakkında daha fazla bilgi için bkz. [Windows 10 ile paylaşılan veya konuk bilgisayar ayarlama](/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>grup ilkesi ile etkinleştir

Kuruluşlar, **karma Azure AD 'ye katılmış cihazlarda**, Fido güvenlik anahtarı oturum açma özelliğini etkinleştirmek için aşağıdaki Grup İlkesi ayarını yapılandırabilir. Bu ayar **bilgisayar yapılandırması** altında bulunabilir  >  **Yönetim Şablonları**  >  **sistem**  >  **oturumu açma**  >  **güvenlik anahtarı oturum aç**:

- Bu ilkenin **etkin** olarak ayarlanması, kullanıcıların güvenlik anahtarlarıyla oturum açmasına olanak tanır.
- Bu ilkenin **devre dışı** veya **yapılandırılmamış** olarak ayarlanması, kullanıcıların güvenlik anahtarlarıyla oturum açmasını engeller.

Bu grup ilkesi ayarı grup ilkesi şablonunun güncelleştirilmiş bir sürümünü gerektirir `CredentialProviders.admx` . Bu yeni şablon, Windows Server 'ın bir sonraki sürümüyle ve Windows 10 20 H1 ile kullanılabilir. Bu ayar, Windows 'un bu yeni sürümlerinden birini çalıştıran bir cihazla veya destek konusundaki kılavuzu izleyerek, [Windows 'ta grup ilkesi Yönetim Şablonları Için merkezi mağaza oluşturma ve yönetme](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)ile yönetilebilir.

## <a name="sign-in-with-fido2-security-key"></a>FIDO2 güvenlik anahtarıyla oturum açın

Aşağıdaki örnekte, Bala Sandhu adlı bir Kullanıcı önceki makaledeki adımları kullanarak FIDO2 güvenlik anahtarını zaten sağladı, [passwordless güvenlik anahtarı oturum açma özelliğini etkinleştirin](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Hibrit Azure AD 'ye katılmış cihazlar için, Şirket [içi kaynaklarda parolasız güvenlik anahtarı oturum açma](howto-authentication-passwordless-security-key-on-premises.md)özelliğinin de etkinleştirildiğinden emin olun. Bala, Windows 10 kilit ekranından güvenlik anahtarı kimlik bilgileri sağlayıcısını seçebilir ve Windows 'da oturum açmak için güvenlik anahtarını ekleyebilir.

![Windows 10 kilit ekranında güvenlik anahtarı oturumu açma](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Güvenlik anahtarını biyometrik, PIN veya sıfırlama güvenlik anahtarını yönetme

* Windows 10 sürüm 1903 veya üzeri
   * Kullanıcılar, cihaz > **hesapları** güvenlik anahtarı 'nda **Windows ayarlarını** açabilir  >  
   * Kullanıcılar PIN kodlarını değiştirebilir, Biyometri güncelleştirebilir veya güvenlik anahtarını sıfırlayabilir

## <a name="troubleshooting-and-feedback"></a>Sorun giderme ve geri bildirim

Geri bildirim paylaşmak veya bu özellikle ilgili sorunlarla karşılaşırsanız, aşağıdaki adımları kullanarak Windows geri bildirim Merkezi uygulaması aracılığıyla paylaşabilirsiniz:

1. **Geri Bildirim Hub 'ını** başlatın ve oturum açtığınızdan emin olun.
1. Aşağıdaki kategoriye göre geri bildirim gönderin:
   - Kategori: güvenlik ve Gizlilik
   - Alt Kategori: FıDO
1. Günlükleri yakalamak için, **sorunu yeniden oluşturmak** için seçeneğini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD ve karma Azure AD 'ye katılmış cihazlar için şirket içi kaynaklara erişimi etkinleştirme](howto-authentication-passwordless-security-key-on-premises.md)

[Cihaz kaydı hakkında daha fazla bilgi edinin](../devices/overview.md)

[Azure AD Multi-Factor Authentication hakkında daha fazla bilgi](../authentication/howto-mfa-getstarted.md)