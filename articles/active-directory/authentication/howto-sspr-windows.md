---
title: Windows için Azure AD self servis parola sıfırlama-Azure Active Directory
description: Windows oturum açma ekranında parolayı unuttum kullanarak Self servis parola sıfırlama özelliğini etkinleştirme
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 519993be873e7864dab4de4f66919c56aebfc379
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171856"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Nasıl yapılır: Windows oturum açma ekranından parola sıfırlamayı etkinleştirme

Windows 7, 8, 8,1 ve 10 çalıştıran makineler için, Windows oturum açma ekranında kullanıcıların parolalarını sıfırlamalarını sağlayabilirsiniz. Kullanıcıların artık [SSPR portalına](https://aka.ms/sspr)erişmek için bir Web tarayıcısına sahip bir cihaz bulması gerekmez.

![Örnek Windows 7 ve SSPR bağlantısı ile 10 oturum açma ekranı gösteriliyor](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Genel sınırlamalar

- Parola sıfırlama, uzak bir masaüstünden veya Hyper-V gelişmiş oturumlarından Şu anda desteklenmiyor.
- Bu özellik, 802.1 x ağ kimlik doğrulaması dağıtılan ağlarda ve "Kullanıcı oturum açmadan hemen önce gerçekleştir" seçeneği için çalışmaz. 802.1 x ağ kimlik doğrulaması dağıtılan ağlarda, bu özelliği etkinleştirmek için makine kimlik doğrulamasının kullanılması önerilir.
- Hibrit Azure AD 'ye katılmış makineler, yeni parolayı kullanmak ve önbelleğe alınmış kimlik bilgilerini güncelleştirmek için bir etki alanı denetleyicisine görüşün ağ bağlantısı hattına sahip olmalıdır.
- Bir görüntü kullanıyorsanız, Sysprep 'i çalıştırmadan önce, CopyProfile adımını gerçekleştirmeden önce yerleşik yönetici için Web önbelleğinin temizlendiğinden emin olun. Bu adım hakkında daha fazla bilgi, [özel varsayılan kullanıcı profili kullanılırken destek makalesinde performans düşüklede](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)bulunabilir.
- Aşağıdaki ayarların Windows 10 cihazlarında parola kullanma ve sıfırlama yeteneğinin kesintiye uğratabileceği bilinmektedir
    - V1809 öncesi Windows 10 sürümlerindeki ilke için Ctrl + Alt + Del gerekliyse, **parola sıfırlama** işlemi çalışmaz.
    - Kilit ekranı bildirimleri kapalıysa, **parola sıfırlama** çalışmaz.
    - Hidefastuseranahtarlama etkin veya 1 olarak ayarlandı
    - DontDisplayLastUserName, etkin veya 1 olarak ayarlandı
    - NoLockScreen, etkin veya 1 olarak ayarlandı
    - EnableLostMode, cihazda ayarlandı
    - Explorer. exe, özel bir kabuğa göre değiştirilmiştir
- Aşağıdaki belirli üç ayarların birleşimi bu özelliğin çalışmamasına neden olabilir.
    - Etkileşimli oturum açma: CTRL + ALT + DEL = devre dışı isteme
    - DisableLockScreenAppNotifications = 1 veya etkin
    - Icontentdeliverypolicyenzorlanmış = 1 veya doğru

## <a name="windows-10-password-reset"></a>Windows 10 parola sıfırlama

### <a name="windows-10-prerequisites"></a>Windows 10 önkoşulları

- Bir yöneticinin Azure portal Azure AD self servis parola sıfırlaması etkinleştirmesi gerekir.
- **Bu özelliği kullanmadan önce kullanıcıların SSPR 'ye kaydolması gerekir**
- Ağ proxy 'si gereksinimleri
   - Windows 10 cihazları 
       - `passwordreset.microsoftonline.com` ve `ajax.aspnetcdn.com` bağlantı noktası 443
       - Windows 10 cihazları yalnızca makine düzeyinde ara sunucu yapılandırmasını destekler
- En az Windows 10, sürüm 2018 Güncelleştirmesi (v1803) çalıştırın ve cihazların şunlardan biri olması gerekir:
    - Azure AD 'ye katılmış
    - Karma Azure AD 'ye katılmış

### <a name="enable-for-windows-10-using-intune"></a>Intune kullanarak Windows 10 için etkinleştirme

Oturum açma ekranından parola sıfırlama yapılmasını sağlayan yapılandırma değişikliğinin Intune'dan dağıtılması en esnek yöntemdir. Intune, yapılandırma değişikliğini tanımladığınız belirli bir makine grubuna dağıtmanızı sağlar. Bu yöntem, cihazın Intune kaydını gerektirir.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune'da cihaz yapılandırma ilkesi oluşturma

1. [Azure Portal](https://portal.azure.com)'da oturum açın ve **Intune**'a tıklayın.
1. **Cihaz yapılandırması** > **Profiller** > **Profil Oluştur**'a giderek yeni bir cihaz yapılandırma profili oluşturun
   - Profil için anlamlı bir ad girin
   - İsteğe bağlı olarak, profil için anlamlı bir açıklama girin
   - Platform **Windows 10 ve üstü**
   - Profil türü **Özel**
1. **Ayarlar**'ı yapılandırın
   - Parolayı sıfırla bağlantısını etkinleştirmek için şu OMA-URI Ayarını **ekleyin**
      - Ayarın ne yaptığını açıklayan anlamlı bir ad girin
      - İsteğe bağlı olarak, ayar için anlamlı bir açıklama girin
      - **OMA-URI** olarak `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` ayarlayın
      - **Veri türü** olarak **Tamsayı** ayarlayın
      - **Değer** olarak **1** ayarlayın
      - **Tamam**’a tıklayın.
   - **Tamam**’a tıklayın.
1. **Oluştur**'a tıklayın
1. Bu ilke belirli kullanıcılara, cihazlara veya gruplara atanabilir. [Microsoft Intune, Kullanıcı ve cihaz profilleri atama](https://docs.microsoft.com/intune/device-profile-assign)makalesinde daha fazla bilgi bulunabilir.

### <a name="enable-for-windows-10-using-the-registry"></a>Kayıt defterini kullanarak Windows 10 için etkinleştirme

1. Yönetici kimlik bilgilerini kullanarak Windows BILGISAYARıNDA oturum açın
1. Yönetici olarak **regedit** komutunu çalıştırın
1. Aşağıdaki kayıt defteri anahtarını ayarlayın
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 parola sıfırlama sorunlarını giderme

Azure AD denetim günlüğü parola sıfırlamanın oluştuğu yerin IP adresi ve ClientType'ı hakkında bilgi içerir.

![Örnek Windows 7 parola sıfırlama Azure AD denetim günlüğünde](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Kullanıcılar, Windows 10 cihazının oturum açma ekranından parolalarını sıfırlarında `defaultuser1` adlı düşük ayrıcalıklı geçici bir hesap oluşturulur. Bu hesap, parola sıfırlama işleminin güvenli kalmasını sağlamak için kullanılır. Hesabın kendisi rastgele oluşturulmuş bir parolaya sahiptir, cihaz oturum açma için gösterilmez ve Kullanıcı parolasını sıfırladıktan sonra otomatik olarak kaldırılacaktır. Birden çok `defaultuser` profili var olabilir, ancak güvenle yoksayılabilir.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 ve 8,1 parola sıfırlama

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 ve 8,1 önkoşulları

- Bir yöneticinin Azure portal Azure AD self servis parola sıfırlaması etkinleştirmesi gerekir.
- **Bu özelliği kullanmadan önce kullanıcıların SSPR 'ye kaydolması gerekir**
- Ağ proxy 'si gereksinimleri
   - Windows 7, 8 ve 8,1 cihazları
       - 443 numaralı bağlantı noktası `passwordreset.microsoftonline.com`
- Düzeltme eki uygulanan Windows 7 veya Windows 8.1 Işletim sistemi.
- TLS 1,2, [Aktarım Katmanı Güvenliği (TLS) kayıt defteri ayarları](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)'nda bulunan kılavuzluk kullanılarak etkinleştirildi.
- Makinenizde birden fazla 3. taraf kimlik bilgisi sağlayıcısı etkinse, kullanıcılar oturum açma ekranında birden fazla Kullanıcı profili görür.

> [!WARNING]
> TLS 1,2 etkinleştirilmeli, yalnızca otomatik anlaşma olarak ayarlanmamalıdır

### <a name="install"></a>Yükleme

1. Etkinleştirmek istediğiniz Windows sürümü için uygun yükleyiciyi indirin.
   - Yazılım, Microsoft İndirme Merkezi 'nde [https://aka.ms/sspraddin](https://aka.ms/sspraddin) adresinden edinilebilir
1. Yüklemek istediğiniz makinede oturum açın ve yükleyiciyi çalıştırın.
1. Yükleme sonrasında, bir yeniden başlatma önemle önerilir.
1. Yeniden başlatmanın ardından, oturum açma ekranında bir kullanıcı seçin ve "parolayı unuttum mı?" düğmesine tıklayın. parola sıfırlama iş akışını başlatmak için.
1. Parolanızı sıfırlamak için ekrandaki adımları izleyerek iş akışını doldurun.

![Örnek Windows 7 "parolayı unuttum mı?" tıklandı SSPR akışı](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Sessiz yükleme

- Sessiz yüklemesi için "msiexec/i SsprWindowsLogon. PROD. msi/QN" komutunu kullanın
- Sessiz kaldırma için, "msiexec/x SsprWindowsLogon. PROD. msi/QN" komutunu kullanın

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 ve 8,1 parola sıfırlama sorunlarını giderme

Olaylar hem makinede hem de Azure AD 'de günlüğe kaydedilir. Azure AD olayları, parola sıfırlamasının gerçekleştiği IP adresi ve ClientType hakkında bilgi içerir.

![Örnek Windows 7 parola sıfırlama Azure AD denetim günlüğünde](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Ek günlüğe kaydetme gerekliyse, ayrıntılı günlük kaydını etkinleştirmek için makinedeki bir kayıt defteri anahtarı değiştirilebilir. Yalnızca sorun giderme amacıyla ayrıntılı günlüğe yazmayı etkinleştirin.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Ayrıntılı günlüğü etkinleştirmek için bir `REG_DWORD: “EnableLogging”`oluşturun ve bunu 1 olarak ayarlayın.
- Ayrıntılı günlük kaydını devre dışı bırakmak için `REG_DWORD: “EnableLogging”` 0 olarak değiştirin.

## <a name="what-do-users-see"></a>Kullanıcıların ne görecek

Windows cihazlarınız için parola sıfırlamayı yapılandırdığınıza göre, Kullanıcı için ne değişiklikler var? Oturum açma ekranında parolalarını sıfırlayabileceklerini nasıl anlayacaklar?

![Örnek Windows 7 ve SSPR bağlantısı ile 10 oturum açma ekranı gösteriliyor](./media/howto-sspr-windows/windows-reset-password.png)

Kullanıcılar oturum açmaya çalıştığında, oturum açma ekranında self servis parola sıfırlama deneyimini açan parolayı **Sıfırla** veya **parolayı unutma** bağlantısını görür. Bu işlev kullanıcıların web tarayıcısına erişmek için başka bir cihaz kullanmalarına gerek kalmadan parolalarını sıfırlamalarına olanak tanır.

Kullanıcılarınız bu özelliği kullanma yönergelerini [İş veya okul parolanızı sıfırlama](../user-help/active-directory-passwords-update-your-own-password.md) konusunda bulabilirler

## <a name="next-steps"></a>Sonraki adımlar

[İzin vermek için kimlik doğrulama yöntemlerini planlayın](concept-authentication-methods.md)

[Windows 10 yapılandırma](https://docs.microsoft.com/windows/configuration/)
