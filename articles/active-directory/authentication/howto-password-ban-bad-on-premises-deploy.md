---
title: Şirket içi Azure AD parola korumasını dağıtma
description: Şirket içi Active Directory Domain Services ortamda Azure AD parola korumasını nasıl planlayıp dağıtacağınızı öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5134c0eeaf3ebd1467b35cf825aa64bd1b445bd5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010588"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Şirket içi Azure Active Directory parola korumasını planlayın ve dağıtın

Kullanıcılar genellikle okul, spor ekibi veya çok büyük kişi gibi yaygın yerel sözcükleri kullanan parolalar oluşturur. Bu parolalar kolayca tahmin edilir ve sözlük tabanlı saldırılara karşı zayıftır. Kuruluşunuzda güçlü parolalar zorlamak için Azure Active Directory (Azure AD) parola koruması, genel ve özel yasaklanmış parola listesi sağlar. Bu yasaklanmış parola listesinde bir eşleşme varsa parola değiştirme isteği başarısız olur.

Şirket içi Active Directory Domain Services (AD DS) ortamınızı korumak için Azure AD parola koruması 'nı şirket içi DC 'niz ile çalışacak şekilde yükleyip yapılandırabilirsiniz. Bu makalede Azure AD parola koruma proxy hizmeti ve Azure AD parola koruma DC aracısının şirket içi ortamınızda nasıl yükleneceği ve kaydedileceği gösterilmektedir.

Azure AD parola korumasının şirket içi bir ortamda nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Windows Server Için Azure AD parola korumasını zorlama Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Dağıtım stratejisi

Aşağıdaki diyagramda Azure AD parola korumasının temel bileşenlerinin, şirket içi Active Directory ortamında nasıl birlikte çalıştığı gösterilmektedir:

![Azure AD parola koruma bileşenlerinin birlikte çalışması](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Dağıtmadan önce yazılımın nasıl çalıştığını gözden geçirmek iyi bir fikirdir. Daha fazla bilgi için bkz. [Azure AD parola korumasına kavramsal genel bakış](concept-password-ban-bad-on-premises.md).

Dağıtımları *Denetim* modunda başlatmanız önerilir. Denetim modu, parolaların ayarlanmasına devam edebilecekleri varsayılan başlangıç ayarıdır. Engellenen parolalar olay günlüğüne kaydedilir. Ara sunucuları ve DC aracılarını denetim modunda dağıttıktan sonra, ilke zorlandığında parola ilkesinin kullanıcılar için sahip olacağı etkiyi izleyin.

Denetim aşamasında, birçok kuruluş aşağıdaki durumların uygulanacağını bulur:

* Daha güvenli parolalar kullanmak için mevcut işletimsel işlemler geliştirmeleri gerekir.
* Kullanıcılar genellikle güvenli olmayan parolalar kullanır.
* Kullanıcılara, güvenlik zorlamada yaklaşan değişikliği, bunlara ilişkin olası etkileri ve daha güvenli parolalar seçme hakkında bilgilendirmeleri gerekir.

Daha güçlü parola doğrulamanın, mevcut Active Directory etki alanı denetleyicisi dağıtım otomasyonunu etkilemesini de mümkün hale gelir. Bu tür sorunları açığa çıkarmak için Denetim dönemi değerlendirmesi sırasında en az bir DC yükseltmesinin ve bir DC indirgemenin gerçekleşmesini öneririz. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* [Ntdsutil.exe zayıf Dizin Hizmetleri onarım modu parolasını ayarlayamadı](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Etki alanı denetleyicisi çoğaltma yükseltmesi zayıf bir dizin hizmetleri onarım modu parolası nedeniyle başarısız oluyor](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Etki alanı denetleyicisi indirgeme zayıf bir yerel yönetici parolası nedeniyle başarısız oluyor](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Özellik, makul bir süre için denetim modunda çalıştıktan sonra, daha güvenli parolalar *istemek için yapılandırmayı* *Denetim* modundan geçirebilirsiniz. Bu süre boyunca ek izleme iyi bir fikirdir.

Azure AD parola korumasının parola değiştirme veya ayarlama işlemleri sırasında yalnızca parolaları doğrulayabileceğini unutmayın. Azure AD parola koruması dağıtımı öncesinde Active Directory kabul edilen ve depolanan parolalar hiçbir şekilde doğrulanmaz ve olduğu gibi çalışmaya devam edecektir. Zaman içinde, tüm kullanıcılar ve hesaplar sonunda mevcut parolalarının süresi dolduğunda Azure AD parola koruması ile doğrulanan parolaları kullanmaya başlar. "Parola süresi dolmasın" ile yapılandırılan hesaplar bundan muaf tutulur.

### <a name="multiple-forest-considerations"></a>Birden çok orman konuları

Azure AD parola korumasını birden çok ormanda dağıtmak için ek gereksinim yoktur.

Her orman, [Şirket Içi Azure AD parola korumasını dağıtmak](#download-required-software)için aşağıdaki bölümde açıklandığı gibi bağımsız olarak yapılandırılır. Her Azure AD parola koruma proxy 'si, etki alanı denetleyicilerini yalnızca katıldığı ormandan destekleyebilir.

Herhangi bir ormandaki Azure AD parola koruma yazılımı, Active Directory güven yapılandırmalarına bakılmaksızın diğer ormanlarda dağıtılan parola koruma yazılımlarıdır.

### <a name="read-only-domain-controller-considerations"></a>Salt okuma etki alanı denetleyicisi konuları

Parola değiştirme veya ayarlama olayları, salt okuma etki alanı denetleyicilerinde (RODC) işlenmez ve kalıcı hale getirilir. Bunun yerine, bunlar yazılabilir etki alanı denetleyicilerine iletilir. Azure AD parola koruması DC Aracısı yazılımını RODC 'Lere yüklemek zorunda değilsiniz.

Ayrıca, Azure AD parola koruma proxy hizmetini salt bir etki alanı denetleyicisinde çalıştırmak desteklenmez.

### <a name="high-availability-considerations"></a>Yüksek kullanılabilirlik konuları

Parola korumasının ana sorunu, bir ormandaki DC 'Ler yeni ilkeleri veya Azure 'dan başka verileri indirmeye çalıştığında Azure AD parola koruma proxy sunucularının kullanılabilirliğinden oluşur. Her Azure AD parola koruma DC Aracısı, hangi ara sunucunun çağrılacağını saptarken basit bir hepsini bir kez deneme stili algoritması kullanır. Aracı yanıt vermeyen proxy sunucularını atlar.

Hem dizin hem de SYSVOL klasörü durumunun iyi bir şekilde çoğaltılmasını sağlayan çoğu tam bağlı Active Directory dağıtımı için, kullanılabilirlik sağlamak için iki Azure AD parola koruma proxy sunucusu yeterlidir. Bu yapılandırma, yeni ilkelerin ve diğer verilerin zamanında indirilmesine neden olur. İsterseniz ek Azure AD parola koruma proxy sunucuları dağıtabilirsiniz.

Azure AD parola koruması DC Aracısı yazılımının tasarımı, yüksek kullanılabilirliğe ilişkin olağan sorunları azaltır. Azure AD parola koruması DC Aracısı, en son indirilen parola ilkesinin yerel bir önbelleğini tutar. Kayıtlı tüm proxy sunucuları kullanılamaz hale gelirse, Azure AD parola koruma DC aracıları, önbelleğe alınmış parola ilkelerini zorlamaya devam eder.

Büyük bir dağıtımda parola ilkeleri için makul bir güncelleştirme sıklığı genellikle günler, saat veya daha az değildir. Bu nedenle, proxy sunucularının kısa kesintileri Azure AD parola korumasını önemli ölçüde etkilemez.

## <a name="deployment-requirements"></a>Dağıtım gereksinimleri

Lisanslama hakkında daha fazla bilgi için bkz. [Azure AD parola koruması lisanslama gereksinimleri](concept-password-ban-bad.md#license-requirements).

Aşağıdaki temel gereksinimler geçerlidir:

* Azure AD parola koruma bileşenlerine sahip etki alanı denetleyicileri dahil tüm makinelerde evrensel C çalışma zamanı yüklü olmalıdır.
    * Windows Update tüm güncelleştirmelere sahip olduğunuzdan emin olarak çalışma zamanını alabilirsiniz. Alternatif olarak, bir işletim sistemine özgü güncelleştirme paketi de edinebilirsiniz. Daha fazla bilgi için bkz. [Windows 'Da evrensel C çalışma zamanı güncelleştirmesi](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Windows Server Active Directory ormanını Azure AD 'ye kaydetmek için, orman kök etki alanında Active Directory etki alanı yöneticisi ayrıcalıklarına sahip bir hesap gerekir.
* Anahtar Dağıtım Hizmeti, Windows Server 2012 ve sonraki sürümlerini çalıştıran etki alanındaki tüm etki alanı denetleyicilerinde etkinleştirilmelidir. Varsayılan olarak, bu hizmet el ile tetikleyici başlangıcı aracılığıyla etkinleştirilir.

* Ağ bağlantısı, her etki alanındaki en az bir etki alanı denetleyicisi ve Azure AD parola koruması için proxy hizmetini barındıran en az bir sunucu arasında bulunmalıdır. Bu bağlantı, etki alanı denetleyicisinin RPC uç nokta Eşleyici bağlantı noktası 135 ve proxy hizmetindeki RPC sunucusu bağlantı noktasına erişmesine izin vermelidir.
    * Varsayılan olarak, RPC sunucu bağlantı noktası aralıktan (49152-65535) dinamik bir RPC bağlantı noktasıdır, ancak [statik bir bağlantı noktası kullanacak](#static)şekilde yapılandırılabilir.
* Azure AD parola koruma Proxy hizmetinin yükleneceği tüm makinelerin aşağıdaki uç noktalara ağ erişimi olması gerekir:

    |**Uç Nokta**|**Amaç**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Kimlik doğrulama istekleri|
    |`https://enterpriseregistration.windows.net`|Azure AD parola koruma işlevi|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD parola koruması DC Aracısı

Azure AD parola koruması DC Aracısı için aşağıdaki gereksinimler geçerlidir:

* Azure AD parola koruması DC Aracısı yazılımının yükleneceği tüm makineler Windows Server Core sürümleri dahil olmak üzere Windows Server 2012 veya üstünü çalıştırmalıdır.
    * Active Directory etki alanı veya ormanın Windows Server 2012 etki alanı işlev düzeyinde (DFL) veya orman işlev düzeyinde (FFL) olması gerekmez. [Tasarım ilkeleri](concept-password-ban-bad-on-premises.md#design-principles)bölümünde belirtildiği gıbı, DC Aracısı veya proxy yazılımının çalışması için gereken en az DFL veya FFL yoktur.
* Azure AD parola koruma proxy hizmeti 'nin yükleneceği tüm makinelerin .NET 4.7.2 yüklü olması gerekir.
    * .NET 4.7.2 henüz yüklenmemişse, [Windows için .NET Framework 4.7.2 çevrimdışı yükleyicisi](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)' nde bulunan yükleyiciyi indirip çalıştırın.
* Azure AD parola koruması DC Aracısı hizmetini çalıştıran tüm Active Directory etki alanı, SYSVOL çoğaltması için Dağıtılmış Dosya Sistemi Çoğaltma (DFSR) kullanmalıdır.
   * Etki alanınız zaten DFSR kullanıyorsa, Azure AD parola korumasını yüklemeden önce geçişi yapmanız gerekir. Daha fazla bilgi için bkz [. SYSVOL çoğaltma geçiş kılavuzu: FRS DFS Çoğaltma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD parola koruması DC Aracısı yazılımı, SYSVOL çoğaltması için hala FRS (DFSR 'nin öncül teknolojisi) kullanan etki alanlarında bulunan etki alanı denetleyicilerine yüklenir, ancak yazılım bu ortamda düzgün çalışmaz.
    >
    > Diğer negatif yan etkiler, çoğaltılamayan tek dosyaları ve SYSVOL geri yükleme yordamlarını başarılı olarak, ancak tüm dosyaları çoğaltamaz.
    >
    > Hem DFSR 'nin ilgili avantajları hem de Azure AD parola koruması dağıtımının engelini kaldırmak için etki alanınızı, en kısa sürede DFSR 'yi kullanacak şekilde geçirin. Yazılımın gelecekteki sürümleri, hala FRS kullanan bir etki alanında çalışırken otomatik olarak devre dışı bırakılır.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD parola koruma proxy hizmeti

Azure AD parola koruması proxy hizmeti için aşağıdaki gereksinimler geçerlidir:

* Azure AD parola koruma proxy hizmeti 'nin yükleneceği tüm makineler Windows Server Core sürümleri dahil olmak üzere Windows Server 2012 R2 veya sonraki bir sürümünü çalıştırmalıdır.

    > [!NOTE]
    > Azure AD parola koruma proxy hizmeti dağıtımı, etki alanı denetleyicisinin giden doğrudan internet bağlantısına sahip olmasına rağmen Azure AD parola koruması 'nı dağıtmaya yönelik zorunlu bir gereksinimdir.

* Azure AD parola koruma proxy hizmeti 'nin yükleneceği tüm makinelerin .NET 4.7.2 yüklü olması gerekir.
    * .NET 4.7.2 henüz yüklenmemişse, [Windows için .NET Framework 4.7.2 çevrimdışı yükleyicisi](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)' nde bulunan yükleyiciyi indirip çalıştırın.
* Azure AD parola koruması ara sunucusu hizmetini barındıran tüm makineler, etki alanı denetleyicilerinin proxy hizmetinde oturum açabilme izni verecek şekilde yapılandırılmalıdır. Bu özellik "Bu bilgisayara ağ üzerinden eriş" ayrıcalık ataması aracılığıyla denetlenir.
* Azure AD parola koruması ara sunucusu hizmetini barındıran tüm makineler, giden TLS 1,2 HTTP trafiğine izin verecek şekilde yapılandırılmalıdır.
* Azure AD parola koruma proxy hizmetini ve ormanını Azure AD 'ye kaydetmek için bir *genel yönetici* veya *Güvenlik Yöneticisi* hesabı.
* [Uygulama proxy 'si ortamı kurulum yordamları](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)'nda belirtilen bağlantı noktaları ve URL 'ler kümesi için ağ erişimi etkinleştirilmelidir.

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD aracı Güncelleştirici önkoşullarını bağlama

Microsoft Azure AD Connect Agent Güncelleştirici hizmeti, Azure AD parola koruma proxy hizmeti ile yan yana yüklenir. Microsoft Azure AD Connect Agent Güncelleştirici hizmetinin işlev yapabilmesi için ek yapılandırma gerekir:

* Ortamınız bir HTTP proxy sunucusu kullanıyorsa, [mevcut şirket içi proxy sunucularıyla çalışma](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)bölümünde belirtilen yönergeleri izleyin.
* Microsoft Azure AD Connect Agent Güncelleştirici hizmeti ayrıca [TLS gereksinimlerinde](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements)belirtilen TLS 1,2 adımlarını da gerektirir.

> [!WARNING]
> Azure AD parola koruma proxy 'si ve Azure AD Uygulama Ara Sunucusu, yönergelerin uygulama proxy 'Si içeriğine başvurmasının neden olduğu Microsoft Azure AD Connect aracı Güncelleştirici hizmetinin farklı sürümlerini yükler. Bu farklı sürümler yan yana yüklendiğinde uyumsuzdur ve bunu yaptığınızda, aracı Güncelleştirici hizmetinin yazılım güncelleştirmeleri için Azure 'a bağlantı kurmasını önleyecek, bu nedenle Azure AD parola koruma proxy 'sini ve uygulama ara sunucusunu hiçbir zaman aynı makineye yüklememelisiniz.

## <a name="download-required-software"></a>Gerekli yazılımı indir

Şirket içi Azure AD parola koruma dağıtımı için gereken iki yükleyici vardır:

* Azure AD parola koruma DC Aracısı (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD parola koruma proxy 'si (*AzureADPasswordProtectionProxySetup.exe*)

Her iki yükleyiciyi de [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57071)' nden indirin.

## <a name="install-and-configure-the-proxy-service"></a>Proxy hizmetini yükleyip yapılandırma

Azure AD parola koruma proxy hizmeti, genellikle şirket içi AD DS ortamınızdaki bir üye sunucuda bulunur. Yüklendikten sonra Azure AD parola koruma proxy hizmeti, Azure AD kiracınız için genel ve müşteri yasaklanmış parola listelerinin bir kopyasını sürdürmek üzere Azure AD ile iletişim kurar.

Sonraki bölümde, Azure AD parola koruma DC aracılarını şirket içi AD DS ortamınızdaki etki alanı denetleyicilerine yüklersiniz. Bu DC aracıları, etki alanındaki parola değiştirme olaylarını işlerken kullanılmak üzere en son yasaklanmış parola listelerini almak için proxy hizmeti ile iletişim kurar.

Azure AD parola koruma proxy hizmetini barındırmak için bir veya daha fazla sunucu seçin. Sunucu (ler) için aşağıdaki noktalar geçerlidir:

* Bu tür bir hizmet yalnızca tek bir orman için parola ilkeleri sağlayabilir. Konak makinenin bu ormandaki herhangi bir etki alanına katılması gerekir.
* Proxy hizmetini kök veya alt etki alanına ya da bunların bir birleşimini yükleyebilirsiniz.
* Ormanın her etki alanında en az bir DC ve bir parola koruma proxy sunucusu arasında ağ bağlantısı gerekir.
* Test için bir etki alanı denetleyicisinde Azure AD parola koruma proxy hizmetini çalıştırabilirsiniz, ancak bu etki alanı denetleyicisi daha sonra internet bağlantısı gerektirir. Bu bağlantı bir güvenlik sorunu olabilir. Bu yapılandırmayı yalnızca test için öneririz.
* [Yüksek kullanılabilirlik konuları](#high-availability-considerations)hakkında önceki bölümde belirtildiği gibi, her orman için ormanlar için en az ıkı Azure AD parola koruma proxy sunucusu önerilir.
* Azure AD parola koruma proxy hizmetini salt bir etki alanı denetleyicisinde çalıştırmak desteklenmez.

Azure AD parola koruma proxy hizmeti 'ni yüklemek için aşağıdaki adımları izleyin:

1. Azure AD parola koruma proxy hizmeti 'ni yüklemek için `AzureADPasswordProtectionProxySetup.exe` yazılım yükleyicisini çalıştırın.

    Yazılım yüklemesi için yeniden başlatma gerekmez ve aşağıdaki örnekte olduğu gibi standart MSI yordamları kullanılarak otomatik olabilir:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Yükleme hatasından kaçınmak için paketi yüklemeden önce Windows Güvenlik Duvarı hizmeti 'nin çalışıyor olması gerekir `AzureADPasswordProtectionProxySetup.exe` .
    >
    > Windows Güvenlik Duvarı çalıştırılmayan bir şekilde yapılandırıldıysa, geçici çözüm yükleme sırasında güvenlik duvarı hizmetini geçici olarak etkinleştirip çalıştırmaya yönelik bir çözümdür. Proxy yazılımının yüklemeden sonra Windows Güvenlik Duvarı 'nda belirli bir bağımlılığı yoktur.
    >
    > Üçüncü taraf bir güvenlik duvarı kullanıyorsanız, hala dağıtım gereksinimlerini karşılayacak şekilde yapılandırılmış olmalıdır. Bunlar, 135 numaralı bağlantı noktasına ve proxy RPC sunucu bağlantı noktasına gelen erişime izin vermeyi içerir. Daha fazla bilgi için [dağıtım gereksinimleri](#deployment-requirements)konusunun önceki bölümüne bakın.

1. Azure AD parola koruma proxy yazılımı, yeni bir PowerShell modülü içerir `AzureADPasswordProtection` . Aşağıdaki adımlarda bu PowerShell modülünden çeşitli cmdlet 'ler çalıştırılır.

    Bu modülü kullanmak için yönetici olarak bir PowerShell penceresi açın ve yeni modülü aşağıdaki gibi içeri aktarın:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Azure AD parola koruma proxy hizmeti 'nin çalışıp çalışmadığını denetlemek için aşağıdaki PowerShell komutunu kullanın:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Sonuç, *çalışıyor* **durumunun** gösterilmesi gerekir.

1. Proxy hizmeti makinede çalışıyor, ancak Azure AD ile iletişim kurmak için kimlik bilgileri yok. Cmdlet 'ini kullanarak Azure AD parola koruma proxy sunucusunu Azure AD 'ye kaydedin `Register-AzureADPasswordProtectionProxy` .

    Bu cmdlet Azure kiracınız için *genel yönetici* veya *Güvenlik Yöneticisi* kimlik bilgileri gerektirir. Bu cmdlet 'in Ayrıca yerel yönetici ayrıcalıklarına sahip bir hesap kullanılarak çalıştırılması gerekir.

    Bu komut bir kez başarılı olduktan sonra, ek çağırmaları da başarılı olur ancak gereksizdir.

    `Register-AzureADPasswordProtectionProxy`Cmdlet 'i aşağıdaki üç kimlik doğrulama modunu destekler. İlk iki mod Azure AD Multi-Factor Authentication destekler, ancak üçüncü mod değildir.

    > [!TIP]
    > Bu cmdlet belirli bir Azure kiracısı için ilk kez çalıştırıldığında tamamlanmadan önce dikkat çekici bir gecikme olabilir. Bir hata raporlanmadığı takdirde bu gecikmeden endişelenmeyin.

     * Etkileşimli kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Bu mod sunucu çekirdeği işletim sistemlerinde çalışmaz. Bunun yerine, aşağıdaki kimlik doğrulama modlarından birini kullanın. Ayrıca, Internet Explorer Artırılmış Güvenlik Yapılandırması etkinse bu mod başarısız olabilir. Geçici çözüm, bu yapılandırmayı devre dışı bırakmak, proxy 'yi kaydettirmek ve sonra yeniden etkinleştirmektir.

     * Cihaz kodu kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        İstendiğinde, bir Web tarayıcısı açmak ve kimlik doğrulama kodunu girmek için bağlantıyı takip edin.

     * Sessiz (parola tabanlı) kimlik doğrulama modu:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Hesabınız için Azure AD Multi-Factor Authentication gerekliyse bu mod başarısız olur. Bu durumda, önceki iki kimlik doğrulama modundan birini kullanın veya bunun yerine MFA gerektirmeyen farklı bir hesap kullanın.
        >
        > Ayrıca, Azure cihaz kaydı (Azure AD parola koruması 'nın altında kullanılan), MFA 'yı genel olarak gerektirecek şekilde yapılandırıldıysa, MFA 'nın gerekli olduğunu da görebilirsiniz. Bu gereksinime geçici bir çözüm olarak, önceki iki kimlik doğrulama modundan biriyle MFA 'yı destekleyen farklı bir hesap kullanabilir veya Azure cihaz kaydı MFA gereksinimini geçici olarak da yapabilirsiniz.
        >
        > Bu değişikliği yapmak için, Azure portal **Azure Active Directory** arayıp seçin, sonra **cihaz ayarları > cihazlar**' ı seçin. Cihazların *Hayır*'a **katılması Için Multi-Factor auth gerektir** ' i ayarlayın. Kayıt tamamlandıktan sonra bu ayarı *Evet* olarak yeniden yapılandırdığınızdan emin olun.
        >
        > MFA gereksinimlerinin yalnızca test amacıyla atlanmasına önerilir.

    Şu anda, gelecekteki işlevler için ayrılmış olan *-forestcredential* parametresini belirtmeniz gerekmez.

    Azure AD parola koruma proxy hizmeti 'nin kaydı, hizmetin kullanım ömrü içinde yalnızca bir kere gereklidir. Bundan sonra Azure AD parola koruma proxy hizmeti, diğer tüm gerekli bakımı otomatik olarak gerçekleştirir.

1. Şimdi, PowerShell cmdlet 'ini kullanarak Azure ile iletişim kurmak için şirket içi Active Directory ormanını gerekli kimlik bilgileriyle kaydedin `Register-AzureADPasswordProtectionForest` .

    > [!NOTE]
    > Ortamınızda birden çok Azure AD parola koruma proxy sunucusu yüklüyse, bu, ormanı kaydetmek için kullandığınız proxy sunucusunu değildir.

    Cmdlet 'i Azure kiracınız için *genel yönetici* veya *Güvenlik Yöneticisi* kimlik bilgileri gerektirir. Ayrıca şirket içi Active Directory Kurumsal Yönetici ayrıcalıkları gerektirir. Ayrıca, yerel yönetici ayrıcalıklarına sahip bir hesap kullanarak bu cmdlet 'i çalıştırmalısınız. Ormanı kaydettirmek için kullanılan Azure hesabı, şirket içi Active Directory hesabından farklı olabilir.
    
    Bu adım, her orman için bir kez çalıştırılır.

    `Register-AzureADPasswordProtectionForest`Cmdlet 'i aşağıdaki üç kimlik doğrulama modunu destekler. İlk iki mod Azure AD Multi-Factor Authentication destekler, ancak üçüncü mod değildir.

    > [!TIP]
    > Bu cmdlet belirli bir Azure kiracısı için ilk kez çalıştırıldığında tamamlanmadan önce dikkat çekici bir gecikme olabilir. Bir hata raporlanmadığı takdirde bu gecikmeden endişelenmeyin.

     * Etkileşimli kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Bu mod sunucu çekirdeği işletim sistemlerinde çalışmaz. Bunun yerine, aşağıdaki iki kimlik doğrulama modundan birini kullanın. Ayrıca, Internet Explorer Artırılmış Güvenlik Yapılandırması etkinse bu mod başarısız olabilir. Geçici çözüm, bu yapılandırmayı devre dışı bırakmak, ormanı kaydettirmek ve sonra yeniden etkinleştirmek olur.  

     * Cihaz kodu kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        İstendiğinde, bir Web tarayıcısı açmak ve kimlik doğrulama kodunu girmek için bağlantıyı takip edin.

     * Sessiz (parola tabanlı) kimlik doğrulama modu:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Hesabınız için Azure AD Multi-Factor Authentication gerekliyse bu mod başarısız olur. Bu durumda, önceki iki kimlik doğrulama modundan birini kullanın veya bunun yerine MFA gerektirmeyen farklı bir hesap kullanın.
        >
        > Ayrıca, Azure cihaz kaydı (Azure AD parola koruması 'nın altında kullanılan), MFA 'yı genel olarak gerektirecek şekilde yapılandırıldıysa, MFA 'nın gerekli olduğunu da görebilirsiniz. Bu gereksinime geçici bir çözüm olarak, önceki iki kimlik doğrulama modundan biriyle MFA 'yı destekleyen farklı bir hesap kullanabilir veya Azure cihaz kaydı MFA gereksinimini geçici olarak da yapabilirsiniz.
        >
        > Bu değişikliği yapmak için, Azure portal **Azure Active Directory** arayıp seçin, sonra **cihaz ayarları > cihazlar**' ı seçin. Cihazların *Hayır*'a **katılması Için Multi-Factor auth gerektir** ' i ayarlayın. Kayıt tamamlandıktan sonra bu ayarı *Evet* olarak yeniden yapılandırdığınızdan emin olun.
        >
        > MFA gereksinimlerinin yalnızca test amacıyla atlanmasına önerilir.

       Bu örnekler yalnızca, oturum açmış olan kullanıcının kök etki alanı için bir Active Directory etki alanı yöneticisi olması durumunda başarılı olur. Bu durumda, *-forestcredential* parametresi aracılığıyla alternatif etki alanı kimlik bilgileri sağlayabilirsiniz.

    Active Directory ormanın kaydı, ormanın kullanım ömrü içinde yalnızca bir kere gereklidir. Bundan sonra, ormandaki Azure AD parola koruma DC aracıları, herhangi bir diğer gerekli bakımı otomatik olarak gerçekleştirir. `Register-AzureADPasswordProtectionForest`Bir orman için başarılı bir şekilde çalıştıktan sonra cmdlet 'in ek etkinleştirmeleri başarılı olur, ancak gereksizdir.
    
    Başarılı olması için `Register-AzureADPasswordProtectionForest` , Azure AD parola koruma proxy sunucusunun etki alanında Windows Server 2012 veya üzeri çalıştıran en az BIR DC kullanılabilir olmalıdır. Bu adımdan önce Azure AD parola koruma DC Aracısı yazılımının herhangi bir etki alanı denetleyicisine yüklenmesi gerekmez.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Proxy hizmetini bir HTTP proxy üzerinden iletişim kuracak şekilde yapılandırma

Ortamınız Azure ile iletişim kurmak için belirli bir HTTP proxy kullanılmasını gerektiriyorsa, Azure AD parola koruma hizmetini yapılandırmak için aşağıdaki adımları kullanın.

Klasörde bir *AzureADPasswordProtectionProxy.exe.config* dosyası oluşturun `%ProgramFiles%\Azure AD Password Protection Proxy\Service` . Aşağıdaki içeriği ekleyin:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

HTTP proxy 'niz kimlik doğrulaması gerektiriyorsa, *UseDefaultCredentials* etiketini ekleyin:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Her iki durumda da, `http://yourhttpproxy.com:8080` özel http proxy sunucunuzun adresi ve bağlantı noktasıyla değiştirin.

HTTP proxy 'niz bir yetkilendirme ilkesi kullanacak şekilde yapılandırıldıysa, parola koruması için proxy hizmetini barındıran makinenin Active Directory bilgisayar hesabına erişim vermeniz gerekir.

*AzureADPasswordProtectionProxy.exe.config* dosyasını oluşturduktan veya güncelleştirdikten sonra Azure AD parola koruma proxy hizmetini durdurup yeniden başlatmanızı öneririz.

Proxy hizmeti bir HTTP proxy 'sine bağlanmak için belirli kimlik bilgilerinin kullanılmasını desteklememektedir.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Proxy hizmetini belirli bir bağlantı noktasını dinlemek üzere yapılandırma

Azure AD parola koruması DC Aracısı yazılımı, proxy hizmeti ile iletişim kurmak için TCP üzerinden RPC kullanır. Varsayılan olarak, Azure AD parola koruma proxy hizmeti, kullanılabilir olan herhangi bir Dinamik RPC uç noktasını dinler. Ortamınızdaki ağ topolojisi veya güvenlik duvarı gereksinimleri nedeniyle gerekirse, belirli bir TCP bağlantı noktasını dinlemek için hizmeti yapılandırabilirsiniz.

<a id="static" /></a>Hizmeti statik bir bağlantı noktası altında çalışacak şekilde yapılandırmak için `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 'ini aşağıdaki gibi kullanın:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Bu değişikliklerin etkili olması için Azure AD parola koruma proxy hizmetini durdurup yeniden başlatmanız gerekir.

Hizmeti dinamik bir bağlantı noktası altında çalışacak şekilde yapılandırmak için aynı yordamı kullanın, ancak *Staticport* öğesini sıfır olarak ayarlayın:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Bu değişikliklerin etkili olması için Azure AD parola koruma proxy hizmetini durdurup yeniden başlatmanız gerekir.

Azure AD parola koruma proxy hizmeti, bağlantı noktası yapılandırmasındaki herhangi bir değişiklikten sonra el ile yeniden başlatma gerektirir. Bu yapılandırma değişikliklerini yaptıktan sonra, etki alanı denetleyicilerinde Azure AD parola koruması DC Aracısı hizmetini yeniden başlatmanız gerekmez.

Hizmetin geçerli yapılandırmasını sorgulamak için `Get-AzureADPasswordProtectionProxyConfiguration` Aşağıdaki örnekte gösterildiği gibi cmdlet 'ini kullanın

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Aşağıdaki örnek çıktı, Azure AD parola koruma proxy hizmeti 'nin dinamik bir bağlantı noktası kullandığını göstermektedir:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>DC Aracısı hizmetini yükler

Azure AD parola koruması DC Aracısı hizmetini yüklemek için `AzureADPasswordProtectionDCAgentSetup.msi` paketini çalıştırın.

Aşağıdaki örnekte gösterildiği gibi, standart MSI yordamlarını kullanarak yazılım yükleme işlemini otomatik hale getirebilirsiniz:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

`/norestart`Yükleyicinin makineyi otomatik olarak yeniden başlatması tercih ediyorsanız bayrak atlanabilir.

Yazılım yüklemesi veya kaldırılması için yeniden başlatma gerekir. Bu gereksinim, parola filtresi dll 'Lerinin yalnızca bir yeniden başlatma ile yüklenmesi veya yüklemesi nedeniyle kaldırılmalıdır.

Şirket içi Azure AD parola koruması yüklemesi, DC Aracısı yazılımı bir etki alanı denetleyicisine yüklendikten ve bilgisayar yeniden başlatıldıktan sonra tamamlanır. Başka yapılandırma gerekmez veya mümkün değildir. Şirket içi DC 'Lerde parola değiştirme olayları, Azure AD 'den yapılandırılmış yasaklanmış parola listelerini kullanır.

Azure portal şirket içi Azure AD parola korumasını etkinleştirmek veya özel yasaklanmış parolaları yapılandırmak için bkz. [Şirket Içi Azure AD parola korumasını etkinleştirme](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Azure AD parola koruması DC aracısını henüz bir etki alanı denetleyicisi olmayan bir makineye yükleyebilirsiniz. Bu durumda, hizmet başlatılır ve çalışır, ancak makine bir etki alanı denetleyicisi olacak şekilde yükseltilene kadar devre dışı kalır.

## <a name="upgrading-the-proxy-service"></a>Proxy hizmeti yükseltiliyor

Azure AD parola koruma proxy hizmeti, otomatik yükseltmeyi destekler. Otomatik yükseltme, proxy hizmeti ile yan yana yüklenen Microsoft Azure AD Connect Agent Güncelleştirici hizmetini kullanır. Otomatik yükseltme varsayılan olarak açık olur ve cmdlet 'i kullanılarak etkinleştirilebilir veya devre dışı bırakılabilir `Set-AzureADPasswordProtectionProxyConfiguration` .

Geçerli ayar `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 'i kullanılarak sorgulanabilir. Otomatik yükseltme ayarının her zaman etkinleştirilmesini öneririz.

`Get-AzureADPasswordProtectionProxy`Cmdlet 'i, bir ormandaki yüklü olan tüm Azure AD parola koruma proxy sunucularının yazılım sürümünü sorgulamak için kullanılabilir.

### <a name="manual-upgrade-process"></a>El ile yükseltme işlemi

Yazılım yükleyicisinin en son sürümü çalıştırılarak el ile yükseltme gerçekleştirilir `AzureADPasswordProtectionProxySetup.exe` . Yazılımın en son sürümü [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57071)' nde bulunabilir.

Azure AD parola koruma proxy hizmeti 'nin geçerli sürümünü kaldırmak gerekli değildir-yükleyici yerinde yükseltme gerçekleştirir. Proxy hizmeti yükseltilirken yeniden başlatma gerekmez. Yazılım yükseltme, gibi standart MSI yordamları kullanılarak otomatikleştirilebilir `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>DC aracısını yükseltme

Azure AD parola koruması DC Aracısı yazılımının daha yeni bir sürümü kullanılabilir olduğunda, yazılım paketinin en son sürümü çalıştırılarak yükseltme gerçekleştirilir `AzureADPasswordProtectionDCAgentSetup.msi` . Yazılımın en son sürümü [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57071)' nde bulunabilir.

DC Aracısı yazılımının geçerli sürümünü kaldırmak gerekli değildir-yükleyici yerinde yükseltme gerçekleştirir. DC Aracısı yazılımı yükseltilirken her zaman bir yeniden başlatma gereklidir-bu gereksinim, çekirdek Windows davranışından kaynaklanır.

Yazılım yükseltme, gibi standart MSI yordamları kullanılarak otomatikleştirilebilir `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

`/norestart`Yükleyicinin makineyi otomatik olarak yeniden başlatması tercih ediyorsanız bayrak atlayabilirsiniz.

`Get-AzureADPasswordProtectionDCAgent`Cmdlet 'i, bir ormandaki şu anda yüklü olan tüm Azure AD parola koruma DC aracılarının yazılım sürümünü sorgulamak için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD parola koruması için ihtiyaç duyduğunuz hizmetleri şirket içi sunucularınızda yüklemişseniz, dağıtımınızı tamamlaması için [Azure Portal şirket Içi Azure AD parola korumasını etkinleştirin](howto-password-ban-bad-on-premises-operations.md) .
