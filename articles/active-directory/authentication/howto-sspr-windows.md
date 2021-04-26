---
title: Windows cihazları için self servis parola sıfırlama-Azure Active Directory
description: Windows oturum açma ekranında self servis parola sıfırlamayı Azure Active Directory etkinleştirmeyi öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491394"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Windows oturum açma ekranında self servis parola sıfırlamayı Azure Active Directory etkinleştirme

Self servis parola sıfırlama (SSPR), Azure Active Directory kullanıcılara (Azure AD), yönetici veya yardım masası katılımı olmadan parolasını değiştirme veya sıfırlama yeteneği verir. Genellikle, kullanıcılar [SSPR portalına](https://aka.ms/sspr)erişmek için başka bir cihazda bir Web tarayıcısı açar. Windows 7, 8, 8,1 ve 10 çalıştıran bilgisayarlarda deneyimi geliştirmek için, Windows oturum açma ekranında kullanıcıların parolalarını sıfırlamalarını sağlayabilirsiniz.

![Örnek Windows 7 ve SSPR bağlantısı ile 10 oturum açma ekranı gösteriliyor](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> Bu öğreticide, bir kuruluşta Windows cihazları için SSPR 'yi etkinleştirme Yöneticisi gösterilmektedir.
>
> BT ekibiniz Windows cihazınızdan SSPR kullanma özelliğini etkinleştirmediyseniz veya oturum açma sırasında sorun yaşıyorsanız, ek yardım için yardım masasına ulaşın.

## <a name="general-limitations"></a>Genel sınırlamalar

Windows oturum açma ekranından SSPR kullanımı için aşağıdaki sınırlamalar geçerlidir:

- Parola sıfırlama, uzak bir masaüstünden veya Hyper-V gelişmiş oturumlarından Şu anda desteklenmiyor.
- Bazı üçüncü taraf kimlik bilgisi sağlayıcılarının bu özellikle ilgili sorunlara neden olduğu bilinmektedir.
- [EnableLUA kayıt defteri anahtarı](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) DEĞIŞIKLIĞI aracılığıyla UAC 'nin devre dışı bırakılması, sorunlara yol açabilecek şekilde bilinmektedir.
- Bu özellik, 802.1 x ağ kimlik doğrulaması dağıtılan ağlarda ve "Kullanıcı oturum açmadan hemen önce gerçekleştir" seçeneği için çalışmaz. 802.1 x ağ kimlik doğrulaması dağıtılan ağlarda, bu özelliği etkinleştirmek için makine kimlik doğrulaması kullanılması önerilir.
- Hibrit Azure AD 'ye katılmış makineler, yeni parolayı kullanmak ve önbelleğe alınmış kimlik bilgilerini güncelleştirmek için bir etki alanı denetleyicisine görüşün ağ bağlantısı hattına sahip olmalıdır. Bu, cihazların kuruluşun iç ağı üzerinde veya şirket içi etki alanı denetleyicisine ağ erişimi olan bir VPN üzerinde olması gerektiği anlamına gelir.
- Bir görüntü kullanıyorsanız, Sysprep 'i çalıştırmadan önce, CopyProfile adımını gerçekleştirmeden önce yerleşik yönetici için Web önbelleğinin temizlendiğinden emin olun. Bu adım hakkında daha fazla bilgi, [özel varsayılan kullanıcı profili kullanılırken destek makalesinde performans düşüklede](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)bulunabilir.
- Aşağıdaki ayarlar Windows 10 cihazlarında parola kullanma ve sıfırlama özelliğini kesintiye uğratabilecek şekilde bilinmektedir:
    - Windows 10 ' daki ilke için Ctrl + Alt + Del gerekliyse **parola sıfırlama** işlemi çalışmaz.
    - Kilit ekranı bildirimleri kapalıysa, **parola sıfırlama** çalışmaz.
    - *Hidefastuseranahtarlama* etkin veya 1 olarak ayarlandı
    - *DontDisplayLastUserName* , etkin veya 1 olarak ayarlandı
    - *NoLockScreen* , etkin veya 1 olarak ayarlandı
    - *EnableLostMode* , cihazda ayarlandı
    - Explorer.exe bir özel kabuğa göre değiştirilmiştir
- Aşağıdaki belirli üç ayarların birleşimi bu özelliğin çalışmamasına neden olabilir.
    - Etkileşimli oturum açma: CTRL + ALT + DEL = devre dışı isteme
    - *Disablelockscreenappnotifications* = 1 veya etkin
    - Windows SKU 'SU Home veya Professional sürümü değil

> [!NOTE]
> Bu sınırlamalar Ayrıca cihaz kilit ekranından Iş için Windows Hello PIN sıfırlaması için de geçerlidir.
>

## <a name="windows-10-password-reset"></a>Windows 10 parola sıfırlama

Oturum açma ekranında SSPR için bir Windows 10 cihazı yapılandırmak için aşağıdaki önkoşulları ve yapılandırma adımlarını gözden geçirin.

### <a name="windows-10-prerequisites"></a>Windows 10 önkoşulları

- Bir yöneticinin [Azure Portal Azure AD self servis parola sıfırlaması etkinleştirmesi gerekir](tutorial-enable-sspr.md).
- Bu özelliği kullanmadan önce kullanıcıların SSPR 'ye kaydolması gerekir [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Windows oturum açma ekranından SSPR 'yi kullanmak için benzersiz değil, tüm kullanıcıların, parolasını sıfırlayabilmeleri için önce kimlik doğrulama iletişim bilgilerini sağlaması gerekir.
- Ağ proxy gereksinimleri:
    - 443 numaralı bağlantı noktası `passwordreset.microsoftonline.com` ve `ajax.aspnetcdn.com`
    - Windows 10 cihazları yalnızca makine düzeyinde ara sunucu yapılandırmasını destekler.
- En az Windows 10, sürüm 2018 Güncelleştirmesi (v1803) çalıştırın ve cihazların şunlardan biri olması gerekir:
    - Azure AD'ye katılanlar
    - Hibrit Azure AD'ye katılmış

### <a name="enable-for-windows-10-using-intune"></a>Intune kullanarak Windows 10 için etkinleştirme

Intune kullanarak oturum açma ekranından SSPR 'yi etkinleştirmek için yapılandırma değişikliğini dağıtmak en esnek yöntemdir. Intune, yapılandırma değişikliğini tanımladığınız belirli bir makine grubuna dağıtmanızı sağlar. Bu yöntem, cihazın Intune kaydını gerektirir.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune'da cihaz yapılandırma ilkesi oluşturma

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Intune**' u seçin.
1. **Cihaz yapılandırma**  >  **profillerine** giderek ve **+ Profil oluştur** ' u seçerek yeni bir cihaz yapılandırma profili oluşturun.
   - **Platform** için *Windows 10 ve üstünü* seçin
   - **Profil türü** için *özel* ' i seçin
1. **Oluştur**' u seçin ve profil için *Windows 10 oturum açma ekranı SSPR* gibi anlamlı bir ad sağlayın

    İsteğe bağlı olarak, profilin anlamlı bir açıklamasını girip **İleri**' yi seçin.
1. *Yapılandırma ayarları*' nın altında **Ekle** ' yi seçin ve Parolayı Sıfırla bağlantısını etkinleştirmek IÇIN aşağıdaki OMA-URI ayarını sağlayın:
      - Örneğin *SSPR bağlantısı ekleme* gibi ayarların ne yaptığını açıklayan anlamlı bir ad sağlayın.
      - İsteğe bağlı olarak, ayarın anlamlı bir açıklamasını sağlayın.
      - **OMA-URI** olarak `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` ayarlayın
      - **Veri türü** olarak **Tamsayı** ayarlayın
      - **Değer** olarak **1** ayarlayın

    **Ekle**' yi ve ardından **İleri**' yi seçin.
1. İlke belirli kullanıcılara, cihazlara veya gruplara atanabilir. Profili ortamınız için istediğiniz şekilde atayın ve öncelikle bir cihaz test grubu için, ardından **İleri**' yi seçin.

    Daha fazla bilgi için bkz. [Microsoft Intune kullanıcı ve cihaz profilleri atama](/mem/intune/configuration/device-profile-assign).

1. *İşletim sistemi sürümü Windows 10 Enterprise ise profil atama* gibi uygulanabilirlik kurallarını ortamınız için istediğiniz şekilde yapılandırın, ardından **İleri**' yi seçin.
1. Profilinizi gözden geçirin ve ardından **Oluştur**' u seçin.

### <a name="enable-for-windows-10-using-the-registry"></a>Kayıt defterini kullanarak Windows 10 için etkinleştirme

Kayıt defteri anahtarını kullanarak SSPR 'yi oturum açma ekranında etkinleştirmek için aşağıdaki adımları izleyin:

1. Yönetici kimlik bilgilerini kullanarak Windows BILGISAYARıNDA oturum açın.
1.   +  *Çalıştır* iletişim kutusunu açmak için Windows **R** tuşuna basın, ardından **Regedit** komutunu yönetici olarak çalıştırın
1. Aşağıdaki kayıt defteri anahtarını ayarlayın:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 parola sıfırlama sorunlarını giderme

Windows oturum açma ekranından SSPR 'yi kullanmayla ilgili sorunlar yaşıyorsanız, Azure AD denetim günlüğü, aşağıdaki örnek çıktıda gösterildiği gibi, IP adresi ve parola sıfırlamasının gerçekleştiği *ClientType* hakkında bilgi içerir:

![Örnek Windows 7 parola sıfırlama Azure AD denetim günlüğünde](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Kullanıcılar, Windows 10 cihazının oturum açma ekranından parolalarını sıfırlarında, adlı düşük ayrıcalıklı geçici bir hesap `defaultuser1` oluşturulur. Bu hesap, parola sıfırlama işleminin güvenli kalmasını sağlamak için kullanılır.

Hesabın kendisi rastgele oluşturulmuş bir parolaya sahiptir, cihaz oturum açma için gösterilmez ve Kullanıcı parolasını sıfırladıktan sonra otomatik olarak kaldırılır. Birden çok `defaultuser` profil var olabilir, ancak güvenle yoksayılabilir.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 ve 8,1 parola sıfırlama

Oturum açma ekranında SSPR için bir Windows 7, 8 veya 8,1 cihazı yapılandırmak üzere aşağıdaki önkoşulları ve yapılandırma adımlarını gözden geçirin.

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 ve 8,1 önkoşulları

- Bir yöneticinin [Azure Portal Azure AD self servis parola sıfırlaması etkinleştirmesi gerekir](tutorial-enable-sspr.md).
- Bu özelliği kullanmadan önce kullanıcıların SSPR 'ye kaydolması gerekir [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Windows oturum açma ekranından SSPR 'yi kullanmak için benzersiz değil, tüm kullanıcıların, parolasını sıfırlayabilmeleri için önce kimlik doğrulama iletişim bilgilerini sağlaması gerekir.
- Ağ proxy gereksinimleri:
    - 443 numaralı bağlantı noktası `passwordreset.microsoftonline.com`
- Düzeltme eki uygulanan Windows 7 veya Windows 8.1 Işletim sistemi.
- TLS 1,2, [Aktarım Katmanı Güvenliği (TLS) kayıt defteri ayarları](/windows-server/security/tls/tls-registry-settings#tls-12)'nda bulunan kılavuzluk kullanılarak etkinleştirildi.
- Makinenizde birden fazla 3. taraf kimlik bilgisi sağlayıcısı etkinse, kullanıcılar oturum açma ekranında birden fazla Kullanıcı profili görür.

> [!WARNING]
> TLS 1,2 etkinleştirilmeli, yalnızca otomatik anlaşma olarak ayarlanmamalıdır.

### <a name="install"></a>Yükleme

Windows 7, 8 ve 8,1 için, oturum açma ekranında SSPR 'yi etkinleştirmek üzere makinede küçük bir bileşen yüklü olmalıdır. Bu SSPR bileşenini yüklemek için aşağıdaki adımları izleyin:

1. Etkinleştirmek istediğiniz Windows sürümü için uygun yükleyiciyi indirin.

    Yazılım yükleyicisi, Microsoft İndirme Merkezi 'nden şu adresten edinilebilir: [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Yüklemek istediğiniz makinede oturum açın ve yükleyiciyi çalıştırın.
1. Yükleme sonrasında, bir yeniden başlatma önemle önerilir.
1. Yeniden başlatmanın ardından, oturum açma ekranında bir kullanıcı seçin ve "parolayı unuttum mı?" seçeneğini belirleyin. parola sıfırlama iş akışını başlatmak için.
1. Parolanızı sıfırlamak için ekrandaki adımları izleyerek iş akışını doldurun.

![Örnek Windows 7 "parolayı unuttum mı?" tıklandı SSPR akışı](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Sessiz yükleme

SSPR bileşeni, aşağıdaki komutları kullanarak sormadan yüklenebilir veya kaldırılabilir:

- Sessiz yüklemesi için "msiexec/i SsprWindowsLogon.PROD.msi/QN" komutunu kullanın
- Sessiz kaldırma için, "msiexec/x SsprWindowsLogon.PROD.msi/QN" komutunu kullanın

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 ve 8,1 parola sıfırlama sorunlarını giderme

Windows oturum açma ekranından SSPR 'yi kullanmayla ilgili sorunlar yaşıyorsanız, olaylar hem makinede hem de Azure AD 'de günlüğe kaydedilir. Azure AD olayları, aşağıdaki örnek çıktıda gösterildiği gibi, parola sıfırlamasının gerçekleştiği IP adresi ve ClientType hakkında bilgiler içerir:

![Örnek Windows 7 parola sıfırlama Azure AD denetim günlüğünde](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Ek günlüğe kaydetme gerekliyse, ayrıntılı günlük kaydını etkinleştirmek için makinedeki bir kayıt defteri anahtarı değiştirilebilir. Yalnızca aşağıdaki kayıt defteri anahtarı değerini kullanarak sorun giderme amacıyla ayrıntılı günlüğe kaydetmeyi etkinleştirin:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Ayrıntılı günlüğü etkinleştirmek için, oluşturun `REG_DWORD: "EnableLogging"` ve 1 olarak ayarlayın.
- Ayrıntılı günlük kaydını devre dışı bırakmak için öğesini `REG_DWORD: "EnableLogging"` 0 olarak değiştirin.

## <a name="what-do-users-see"></a>Kullanıcıların ne görecek

Windows cihazlarınız için bir SSPR yapılandırıldığında, Kullanıcı için ne değişiklikler yapılsın? Oturum açma ekranında parolalarını sıfırlayabileceklerini nasıl anlayacaklar? Aşağıdaki örnek ekran görüntüleri, bir kullanıcının SSPR kullanarak parolalarını sıfırlamasına yönelik ek seçenekleri gösterir:

![Örnek Windows 7 ve SSPR bağlantısı ile 10 oturum açma ekranı gösteriliyor](./media/howto-sspr-windows/windows-reset-password.png)

Kullanıcılar oturum açmaya çalıştığında, oturum açma ekranında self servis parola sıfırlama deneyimini açan **Parolayı Sıfırla** veya **parolayı unuttum** bağlantısını görürler. Bu işlev kullanıcıların web tarayıcısına erişmek için başka bir cihaz kullanmalarına gerek kalmadan parolalarını sıfırlamalarına olanak tanır.

Bu özelliği kullanan kullanıcılar hakkında daha fazla bilgi, [iş veya okul parolanızı sıfırlama](../user-help/active-directory-passwords-update-your-own-password.md) bölümünde bulunabilir

## <a name="next-steps"></a>Sonraki adımlar

Kullanıcı kayıt deneyimini basitleştirmek için, [SSPR için Kullanıcı kimlik doğrulaması iletişim bilgilerini önceden doldurabilirsiniz](howto-sspr-authenticationdata.md).
