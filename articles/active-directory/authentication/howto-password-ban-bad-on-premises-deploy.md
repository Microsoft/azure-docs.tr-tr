---
title: Azure AD parola korumasını dağıtma-Azure Active Directory
description: Şirket içi hatalı parolaları açmak için Azure AD parola korumasını dağıtın
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695bd461ae7e979c0a803cd2d6cb450003a6bcee
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602991"
---
# <a name="deploy-azure-ad-password-protection"></a>Azure AD parola korumasını dağıtma

[Windows Server Active Directory Için Azure AD parola korumasını nasıl zorlayabileceğinizi](concept-password-ban-bad-on-premises.md)anladığınıza göre, bir sonraki adım dağıtımınızı planlayıp yürütmektir.

## <a name="deployment-strategy"></a>Dağıtım stratejisi

Dağıtımları denetim modunda başlatmanız önerilir. Denetim modu, parolaların ayarlanmasına devam edebilecekleri varsayılan başlangıç ayarıdır. Engellenen parolalar olay günlüğüne kaydedilir. Ara sunucuları ve DC aracılarını denetim modunda dağıttıktan sonra, ilke zorlandığında parola ilkesinin kullanıcılara ve ortama sahip olacağı etkiyi izlemeniz gerekir.

Denetim aşamasında birçok kuruluş şunları bulur:

* Daha güvenli parolalar kullanmak için mevcut işletimsel işlemler geliştirmeleri gerekir.
* Kullanıcılar genellikle güvenli olmayan parolalar kullanır.
* Kullanıcılara, güvenlik zorlamada yaklaşan değişikliği, bunlara ilişkin olası etkileri ve daha güvenli parolalar seçme hakkında bilgilendirmeleri gerekir.

Daha güçlü parola doğrulamanın mevcut Active Directory etki alanı denetleyicisi dağıtım otomasyonunu etkilemesini de mümkündür. Bu tür sorunların önceden açığa geçebilmesi için Denetim dönemi değerlendirmesi sırasında en az bir DC promosyonu ve bir DC indirgemenin gerçekleşmesini öneririz.  Daha fazla bilgi için bkz.

* [Ntdsutil. exe zayıf Dizin Hizmetleri onarım modu parolasını ayarlayamadı](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Etki alanı denetleyicisi çoğaltma yükseltmesi zayıf bir dizin hizmetleri onarım modu parolası nedeniyle başarısız oluyor](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Etki alanı denetleyicisi indirgeme zayıf bir yerel yönetici parolası nedeniyle başarısız oluyor](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Özellik, makul bir süre için denetim modunda çalıştıktan sonra, daha güvenli parolalar *istemek için yapılandırmayı* *Denetim* modundan geçirebilirsiniz. Bu süre boyunca odaklanmış izleme iyi bir fikirdir.

## <a name="deployment-requirements"></a>Dağıtım gereksinimleri

* Azure AD parola koruması için lisans gereksinimleri, [kuruluşunuzdaki hatalı parolaları kaldırma](concept-password-ban-bad.md#license-requirements)makalesinde bulunabilir.
* Azure AD parola koruması DC Aracısı yazılımının yükleneceği tüm makineler Windows Server 2012 veya sonraki bir sürümünü çalıştırmalıdır. Bu gereksinim, Active Directory etki alanı veya ormanın Windows Server 2012 etki alanı veya orman işlev düzeyinde de olması gerektiğini göstermez. [Tasarım ilkeleri](concept-password-ban-bad-on-premises.md#design-principles)bölümünde belirtildiği gıbı, DC Aracısı veya proxy yazılımının çalışması için gereken en az DFL veya FFL yoktur.
* DC Aracısı hizmetini yükleyen tüm makinelerin .NET 4,5 yüklü olması gerekir.
* Azure AD parola koruma proxy hizmeti 'nin yükleneceği tüm makineler Windows Server 2012 R2 veya sonraki bir sürümünü çalıştırmalıdır.
   > [!NOTE]
   > Proxy hizmeti dağıtımı, etki alanı denetleyicisinin giden doğrudan internet bağlantısına sahip olmasına rağmen Azure AD parola koruması 'nı dağıtmaya yönelik zorunlu bir gereksinimdir. 
   >
* Azure AD parola koruma proxy hizmeti 'nin yükleneceği tüm makinelerin .NET 4,7 yüklü olması gerekir.
  .NET 4,7, tam olarak güncelleştirilmiş bir Windows Server 'a zaten yüklenmiş olmalıdır. Gerekirse, [Windows için .NET Framework 4,7 çevrimdışı yükleyicisinde](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)bulunan yükleyiciyi indirip çalıştırın.
* Azure AD parola koruma bileşenlerine sahip etki alanı denetleyicileri dahil tüm makinelerde evrensel C çalışma zamanı yüklü olmalıdır. Windows Update tüm güncelleştirmelere sahip olduğunuzdan emin olarak çalışma zamanını alabilirsiniz. Alternatif olarak, bir işletim sistemine özgü güncelleştirme paketi de edinebilirsiniz. Daha fazla bilgi için bkz. [Windows 'Da evrensel C çalışma zamanı güncelleştirmesi](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Ağ bağlantısı, her etki alanındaki en az bir etki alanı denetleyicisi ve parola koruması için proxy hizmetini barındıran en az bir sunucu arasında bulunmalıdır. Bu bağlantı, etki alanı denetleyicisinin RPC uç nokta Eşleyici bağlantı noktası 135 ve proxy hizmetindeki RPC sunucusu bağlantı noktasına erişmesine izin vermelidir. Varsayılan olarak, RPC sunucu bağlantı noktası dinamik bir RPC bağlantı noktasıdır, ancak [statik bir bağlantı noktası kullanacak](#static)şekilde yapılandırılabilir.
* Azure AD parola koruma Proxy hizmetinin yükleneceği tüm makinelerin aşağıdaki uç noktalara ağ erişimi olması gerekir:

    |**Bkz**|**Amaç**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Kimlik doğrulama istekleri|
    |`https://enterpriseregistration.windows.net`|Azure AD parola koruma işlevi|
 
* Microsoft Azure AD aracı Güncelleştirici önkoşullarını bağlama

  Microsoft Azure AD Connect Agent Güncelleştirici hizmeti, Azure AD parola koruma proxy hizmeti ile yan yana yüklenir. Microsoft Azure AD Connect Agent Güncelleştirici hizmetinin işlev yapabilmesi için ek yapılandırma gerekir:

  Ortamınız bir http proxy sunucusu kullanıyorsa, [mevcut şirket içi ara sunucularla birlikte çalışırken](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)belirtilen yönergeleri izlemeniz gerekir.

  Microsoft Azure AD Connect Agent Güncelleştirici hizmeti ayrıca [TLS gereksinimlerinde](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)belirtilen TLS 1,2 adımlarını da gerektirir.

  [Uygulama proxy 'si ortamı kurulum yordamları](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)'nda belirtilen bağlantı noktaları ve URL 'ler kümesi için ağ erişimi etkinleştirilmelidir.

  > [!WARNING]
  > Azure AD parola koruma proxy 'Si ve uygulama proxy 'si, Microsoft Azure AD Connect Agent Güncelleştirici hizmetinin farklı sürümlerini yükler, bu nedenle yönergelerin uygulama proxy 'Si içeriğine başvurmasının nedeni budur. Bu farklı sürümler yan yana yüklendiğinde uyumlu değildir, bu nedenle Azure AD parola koruma proxy 'Si ve uygulama proxy 'Sinin aynı makinede yan yana yüklenmesi önerilmez.

* Parola koruması için proxy hizmetini barındıran tüm makineler, etki alanı denetleyicilerinin proxy hizmetinde oturum açabilme izni verecek şekilde yapılandırılmalıdır. Bu özellik "Bu bilgisayara ağ üzerinden eriş" ayrıcalık ataması aracılığıyla denetlenir.
* Parola koruması için proxy hizmetini barındıran tüm makinelerin giden TLS 1,2 HTTP trafiğine izin verecek şekilde yapılandırılması gerekir.
* Azure AD ile parola koruması ve ormana yönelik ara sunucu hizmetini kaydetmek için bir genel yönetici hesabı.
* Windows Server Active Directory ormanını Azure AD 'ye kaydetmek için orman kök etki alanında Active Directory etki alanı yöneticisi ayrıcalıklarına sahip bir hesap.
* DC Aracısı hizmet yazılımını çalıştıran tüm Active Directory etki alanı, SYSVOL çoğaltması için Dağıtılmış Dosya Sistemi Çoğaltma (DFSR) kullanmalıdır.

  Etki alanınız zaten DFSR kullanıyorsa, Azure AD parola korumasını yüklemeden önce bu uygulamayı DFSR 'yi kullanacak şekilde geçirmeniz gerekır. Daha fazla bilgi için şu bağlantıya bakın:

  [SYSVOL çoğaltma geçiş kılavuzu: FRS DFS Çoğaltma](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Azure AD parola koruması DC Aracısı yazılımı, SYSVOL çoğaltması için hala FRS (DFSR 'nin öncül teknolojisi) kullanan etki alanlarında bulunan etki alanı denetleyicilerine yüklenir, ancak yazılım bu ortamda düzgün çalışmaz. Diğer negatif yan etkiler, çoğaltılamayan tek dosyaları ve SYSVOL geri yükleme yordamlarını başarılı olarak, ancak tüm dosyaları çoğaltamaz. Yalnızca DFSR 'nin kendi avantajları ve ayrıca Azure AD parola koruması dağıtımının engellemesini kaldırmak için etki alanınızı en kısa sürede DFSR 'yi kullanacak şekilde geçirmeniz gerekir. Yazılımın gelecekteki sürümleri, hala FRS kullanan bir etki alanında çalışırken otomatik olarak devre dışı bırakılacaktır.

* Anahtar Dağıtım Hizmeti, Windows Server 2012 çalıştıran etki alanındaki tüm etki alanı denetleyicilerinde etkinleştirilmelidir. Varsayılan olarak, bu hizmet el ile tetikleyici başlangıcı aracılığıyla etkinleştirilir.

## <a name="single-forest-deployment"></a>Tek ormanlı dağıtım

Aşağıdaki diyagramda Azure AD parola korumasının temel bileşenlerinin, şirket içi Active Directory ortamında nasıl birlikte çalıştığı gösterilmektedir.

![Azure AD parola koruma bileşenlerinin birlikte çalışması](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Dağıtmadan önce yazılımın nasıl çalıştığını gözden geçirmek iyi bir fikirdir. Bkz. [Azure AD parola korumasına kavramsal genel bakış](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Yazılımı indirin

Azure AD parola koruması için gereken iki yükleyici vardır. Bunlar [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57071)' nden kullanılabilir.

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Parola koruması için proxy hizmetini yükleyip yapılandırın

1. Parola koruması için proxy hizmetini barındırmak üzere bir veya daha fazla sunucu seçin.
   * Bu tür bir hizmet yalnızca tek bir orman için parola ilkeleri sağlayabilir. Konak makinenin o ormandaki bir etki alanına katılması gerekir. Kök ve alt etki alanlarının ikisi de desteklenir. Ormanın her etki alanında ve parola koruma makinesinde en az bir DC arasında ağ bağlantısı gerekir.
   * Test için bir etki alanı denetleyicisinde proxy hizmetini çalıştırabilirsiniz. Ancak bu etki alanı denetleyicisi, bir güvenlik sorunu olabilecek internet bağlantısı gerektirir. Bu yapılandırmayı yalnızca test için öneririz.
   * Artıklık için en az iki ara sunucu kullanmanızı öneririz. Bkz. [yüksek kullanılabilirlik](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. `AzureADPasswordProtectionProxySetup.exe` yazılım yükleyicisini kullanarak Azure AD parola koruma proxy hizmetini yükleme.
   * Yazılım yüklemesinin yeniden başlatılması gerekmez. Yazılım yüklemesi standart MSI yordamları kullanılarak otomatikleştirilebilir, örneğin:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Yükleme hatasından kaçınmak için AzureADPasswordProtectionProxySetup. msi paketini yüklemeden önce Windows Güvenlik Duvarı hizmeti 'nin çalışıyor olması gerekir. Windows Güvenlik Duvarı çalıştırılmayan bir şekilde yapılandırıldıysa, geçici çözüm yükleme sırasında güvenlik duvarı hizmetini geçici olarak etkinleştirip çalıştırmaya yönelik bir çözümdür. Proxy yazılımının yüklemeden sonra Windows Güvenlik Duvarı 'nda belirli bir bağımlılığı yoktur. Üçüncü taraf bir güvenlik duvarı kullanıyorsanız, hala dağıtım gereksinimlerini karşılayacak şekilde yapılandırılmış olmalıdır. Bunlar, 135 numaralı bağlantı noktasına ve proxy RPC sunucu bağlantı noktasına gelen erişime izin vermeyi içerir. Bkz. [dağıtım gereksinimleri](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Yönetici olarak bir PowerShell penceresi açın.
   * Parola koruma proxy 'si yazılımı, *Azureadpasswordprotection*yeni bir PowerShell modülü içerir. Aşağıdaki adımlarda bu PowerShell modülünden çeşitli cmdlet 'ler çalıştırılır. Yeni modülü aşağıdaki gibi içeri aktarın:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Hizmetin çalışıp çalışmadığını denetlemek için aşağıdaki PowerShell komutunu kullanın:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Sonuç, "çalışıyor" **durumunun** gösterilmesi gerekir.

1. Proxy 'yi kaydedin.
   * 3\. adım tamamlandıktan sonra, proxy hizmeti makinede çalışır. Ancak hizmet henüz Azure AD ile iletişim kurmak için gerekli kimlik bilgilerine sahip değil. Azure AD 'ye kaydolma gereklidir:

     `Register-AzureADPasswordProtectionProxy`

     Bu cmdlet Azure kiracınız için genel yönetici kimlik bilgilerini gerektirir. Ayrıca, orman kök etki alanında Şirket içi Active Directory etki alanı yöneticisi ayrıcalıklarına sahip olmanız gerekir. Bu komut bir proxy hizmeti için bir kez başarılı olduktan sonra, bu, ek olarak çağırmaları başarılı olur ancak gereksizdir.

      `Register-AzureADPasswordProtectionProxy` cmdlet 'i aşağıdaki üç kimlik doğrulama modunu destekler. İlk iki mod Azure Multi-Factor Authentication destekler, ancak üçüncü mod desteklemez. Daha fazla ayrıntı için lütfen aşağıdaki açıklamalara bakın.

     * Etkileşimli kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Bu mod sunucu çekirdeği işletim sistemlerinde çalışmaz. Bunun yerine, aşağıdaki kimlik doğrulama modlarından birini kullanın. Ayrıca, Internet Explorer Artırılmış Güvenlik Yapılandırması etkinse bu mod başarısız olabilir. Geçici çözüm, bu yapılandırmayı devre dışı bırakmak, proxy 'yi kaydettirmek ve sonra yeniden etkinleştirmektir.

     * Cihaz kodu kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Ardından, farklı bir cihazdaki görüntülenmiş yönergeleri izleyerek kimlik doğrulamasını tamamlayabilirsiniz.

     * Sessiz (parola tabanlı) kimlik doğrulama modu:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Hesabınız için Azure Multi-Factor Authentication gerekliyse bu mod başarısız olur. Bu durumda, önceki iki kimlik doğrulama modundan birini kullanın veya bunun yerine MFA gerektirmeyen farklı bir hesap kullanın.
        >
        > Ayrıca, Azure cihaz kaydı (Azure AD parola koruması 'nın altında kullanılan), MFA 'yı genel olarak gerektirecek şekilde yapılandırıldıysa, MFA 'nın gerekli olduğunu da görebilirsiniz. Bu sorunu geçici olarak kullanabilmek için, önceki iki kimlik doğrulama modundan biriyle MFA 'yı destekleyen farklı bir hesap kullanabilirsiniz ya da Azure cihaz kaydı MFA gereksinimini geçici olarak rahat hale gelebilir. Bunu yapmak için Azure Yönetim Portalı ' na gidin ve ardından Azure Active Directory, cihazlar ve cihaz ayarları ' na gidin, ardından "cihazlara katılması gereken çok faktörlü kimlik doğrulamasını gerektir" seçeneğini Hayır olarak ayarlayın. Kayıt tamamlandıktan sonra bu ayarı Evet olarak yeniden yapılandırdığınızdan emin olun.
        >
        > MFA gereksinimlerinin yalnızca test amacıyla atlanmasına önerilir.

       Şu anda, gelecekteki işlevler için ayrılmış olan *-forestcredential* parametresini belirtmeniz gerekmez.

   Parola koruması için Proxy hizmetinin kaydı, hizmetin kullanım ömrü içinde yalnızca bir kere gereklidir. Bundan sonra proxy hizmeti, diğer tüm gerekli bakımı otomatik olarak gerçekleştirir.

   > [!TIP]
   > Bu cmdlet belirli bir Azure kiracısı için ilk kez çalıştırıldığında tamamlanmadan önce dikkat çekici bir gecikme olabilir. Bir hata raporlanmadığı takdirde bu gecikmeden endişelenmeyin.

1. Ormanı kaydedin.
   * `Register-AzureADPasswordProtectionForest` PowerShell cmdlet 'ini kullanarak Azure ile iletişim kurmak için şirket içi Active Directory ormanını gerekli kimlik bilgileriyle başlatmalısınız. Cmdlet 'i Azure kiracınız için genel yönetici kimlik bilgileri gerektirir. Ayrıca şirket içi Active Directory Kurumsal Yönetici ayrıcalıkları gerektirir. Bu adım, her orman için bir kez çalıştırılır.

      `Register-AzureADPasswordProtectionForest` cmdlet 'i aşağıdaki üç kimlik doğrulama modunu destekler. İlk iki mod Azure Multi-Factor Authentication destekler, ancak üçüncü mod desteklemez. Daha fazla ayrıntı için lütfen aşağıdaki açıklamalara bakın.

     * Etkileşimli kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Bu mod sunucu çekirdeği işletim sistemlerinde çalışmaz. Bunun yerine, aşağıdaki iki kimlik doğrulama modundan birini kullanın. Ayrıca, Internet Explorer Artırılmış Güvenlik Yapılandırması etkinse bu mod başarısız olabilir. Geçici çözüm, bu yapılandırmayı devre dışı bırakmak, ormanı kaydettirmek ve sonra yeniden etkinleştirmek olur.  

     * Cihaz kodu kimlik doğrulama modu:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Ardından, farklı bir cihazdaki görüntülenmiş yönergeleri izleyerek kimlik doğrulamasını tamamlayabilirsiniz.

     * Sessiz (parola tabanlı) kimlik doğrulama modu:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Hesabınız için Azure Multi-Factor Authentication gerekliyse bu mod başarısız olur. Bu durumda, önceki iki kimlik doğrulama modundan birini kullanın veya bunun yerine MFA gerektirmeyen farklı bir hesap kullanın.
        >
        > Ayrıca, Azure cihaz kaydı (Azure AD parola koruması 'nın altında kullanılan), MFA 'yı genel olarak gerektirecek şekilde yapılandırıldıysa, MFA 'nın gerekli olduğunu da görebilirsiniz. Bu sorunu geçici olarak kullanabilmek için, önceki iki kimlik doğrulama modundan biriyle MFA 'yı destekleyen farklı bir hesap kullanabilirsiniz ya da Azure cihaz kaydı MFA gereksinimini geçici olarak rahat hale gelebilir. Bunu yapmak için Azure Yönetim Portalı ' na gidin ve ardından Azure Active Directory, cihazlar ve cihaz ayarları ' na gidin, ardından "cihazlara katılması gereken çok faktörlü kimlik doğrulamasını gerektir" seçeneğini Hayır olarak ayarlayın. Kayıt tamamlandıktan sonra bu ayarı Evet olarak yeniden yapılandırdığınızdan emin olun.
        >
        > MFA gereksinimlerinin yalnızca test amacıyla atlanmasına önerilir.

       Bu örnekler yalnızca, oturum açmış olan kullanıcının kök etki alanı için bir Active Directory etki alanı yöneticisi olması durumunda başarılı olur. Bu durumda, *-forestcredential* parametresi aracılığıyla alternatif etki alanı kimlik bilgileri sağlayabilirsiniz.

   > [!NOTE]
   > Ortamınızda birden fazla ara sunucu yüklüyse, bu, ormanı kaydetmek için kullandığınız ara sunucuyu değil.
   >
   > [!TIP]
   > Bu cmdlet belirli bir Azure kiracısı için ilk kez çalıştırıldığında tamamlanmadan önce dikkat çekici bir gecikme olabilir. Bir hata raporlanmadığı takdirde bu gecikmeden endişelenmeyin.

   Active Directory ormanın kaydı, ormanın kullanım ömrü içinde yalnızca bir kere gereklidir. Bundan sonra, ormandaki etki alanı denetleyicisi aracıları, diğer gerekli bakımı otomatik olarak gerçekleştirir. Bir orman için `Register-AzureADPasswordProtectionForest` başarıyla çalıştıktan sonra cmdlet 'in ek çağırmaları başarılı olur ancak gereksizdir.

   `Register-AzureADPasswordProtectionForest` başarılı olması için, proxy sunucusu etki alanında Windows Server 2012 veya üzeri çalıştıran en az bir etki alanı denetleyicisinin kullanılabilir olması gerekir. Bu adımdan önce DC Aracısı yazılımının herhangi bir etki alanı denetleyicisine yüklenmesi gerekmez.

1. Bir HTTP proxy 'si üzerinden iletişim kurmak üzere parola koruması için proxy hizmetini yapılandırın.

   Ortamınız Azure ile iletişim kurmak için belirli bir HTTP proxy 'sinin kullanılmasını gerektiriyorsa, şu yöntemi kullanın:%ProgramFiles%\Azure AD parola koruma Proxy\Service klasöründe bir *Azureadpasswordprotectionproxy. exe. config* dosyası oluşturun. Aşağıdaki içeriği ekleyin:

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

   Her iki durumda da `http://yourhttpproxy.com:8080` yerine, belirli HTTP proxy sunucunuzun adresini ve bağlantı noktasını değiştirin.

   HTTP proxy 'niz bir yetkilendirme ilkesi kullanacak şekilde yapılandırıldıysa, parola koruması için proxy hizmetini barındıran makinenin Active Directory bilgisayar hesabına erişim vermeniz gerekir.

   *Azureadpasswordprotectionproxy. exe. config* dosyasını oluşturduktan veya güncelleştirdikten sonra proxy hizmetini durdurmanız ve yeniden başlatmanız önerilir.

   Proxy hizmeti bir HTTP proxy 'sine bağlanmak için belirli kimlik bilgilerinin kullanılmasını desteklememektedir.

1. İsteğe bağlı: belirli bir bağlantı noktasını dinlemek üzere parola koruması için ara sunucu hizmetini yapılandırın.
   * Etki alanı denetleyicilerinde parola koruması için DC Aracısı yazılımı, proxy hizmetiyle iletişim kurmak için TCP üzerinden RPC kullanır. Varsayılan olarak, proxy hizmeti kullanılabilir herhangi bir Dinamik RPC uç noktasını dinler. Ancak ortamınızda ağ topolojisi veya güvenlik duvarı gereksinimleri nedeniyle bu gerekliyse, belirli bir TCP bağlantı noktasını dinlemek için hizmeti yapılandırabilirsiniz.
      * <a id="static" /></a>hizmeti statik bir bağlantı noktası altında çalışacak şekilde yapılandırmak Için `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 'ini kullanın.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Bu değişikliklerin etkili olması için hizmeti durdurup yeniden başlatmanız gerekir.

      * Hizmeti dinamik bir bağlantı noktası altında çalışacak şekilde yapılandırmak için aynı yordamı kullanın, ancak *Staticport* öğesini sıfır olarak ayarlayın:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Bu değişikliklerin etkili olması için hizmeti durdurup yeniden başlatmanız gerekir.

   > [!NOTE]
   > Parola koruması için proxy hizmeti, bağlantı noktası yapılandırmasındaki herhangi bir değişiklikten sonra el ile yeniden başlatma gerektirir. Ancak, bu yapılandırma değişikliklerini yaptıktan sonra etki alanı denetleyicilerinde DC Aracısı hizmet yazılımını yeniden başlatmanız gerekmez.

   * Hizmetin geçerli yapılandırmasını sorgulamak için `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 'ini kullanın:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>DC Aracısı hizmetini yükler

   `AzureADPasswordProtectionDCAgentSetup.msi` paketini kullanarak parola koruması için DC Aracısı hizmetini yükler.

   Yazılım yüklemesi veya kaldırılması için yeniden başlatma gerekir. Bu gereksinim, parola filtresi dll 'Lerinin yalnızca bir yeniden başlatma ile yüklenmesi veya yüklemesi nedeniyle kaldırılmalıdır.

   DC Aracısı hizmetini henüz bir etki alanı denetleyicisi olmayan bir makineye yükleyebilirsiniz. Bu durumda, hizmet başlatılır ve çalışır, ancak makine bir etki alanı denetleyicisi olacak şekilde yükseltilene kadar devre dışı bırakılır.

   Yazılım yüklemesini standart MSI yordamlarını kullanarak otomatikleştirebilirsiniz. Örneğin:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Yükleyicinin makineyi otomatik olarak yeniden başlatması tercih ediyorsanız `/norestart` bayrağını atlayabilirsiniz.

Bir etki alanı denetleyicisine DC Aracısı yazılımı yüklendikten sonra yükleme tamamlanır ve bu bilgisayar yeniden başlatılır. Başka yapılandırma gerekmez veya mümkün değildir.

## <a name="upgrading-the-proxy-agent"></a>Proxy aracısını yükseltme

Azure AD parola koruma proxy yazılımının daha yeni bir sürümü kullanılabilir olduğunda, yükseltme `AzureADPasswordProtectionProxySetup.exe` yazılım yükleyicisinin en son sürümü çalıştırılarak gerçekleştirilir. Yazılımın en son sürümü [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57071)' nde bulunabilir.

Proxy yazılımının geçerli sürümünü kaldırmak gerekli değildir-yükleyici yerinde bir yükseltme gerçekleştirir. Proxy yazılımı yükseltilirken yeniden başlatma gerekmez. Yazılım yükseltme standart MSI yordamları kullanılarak otomatikleştirilebilir, örneğin: `AzureADPasswordProtectionProxySetup.exe /quiet`.

Proxy aracısı otomatik yükseltmeyi destekler. Otomatik yükseltme, proxy hizmeti ile yan yana yüklenen Microsoft Azure AD Connect Agent Güncelleştirici hizmetini kullanır. Otomatik yükseltme varsayılan olarak etkindir ve `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 'i kullanılarak etkinleştirilebilir veya devre dışı bırakılabilir. Geçerli ayar `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 'i kullanılarak sorgulanabilir. Microsoft otomatik yükseltme ayarının her zaman etkinleştirilmesini önerir.

`Get-AzureADPasswordProtectionProxy` cmdlet 'i, bir ormandaki yüklü olan tüm proxy aracılarının yazılım sürümünü sorgulamak için kullanılabilir.

## <a name="upgrading-the-dc-agent"></a>DC aracısını yükseltme

Azure AD parola koruması DC Aracısı yazılımının daha yeni bir sürümü kullanılabilir olduğunda, `AzureADPasswordProtectionDCAgentSetup.msi` yazılım paketinin en son sürümü çalıştırılarak yükseltme gerçekleştirilir. Yazılımın en son sürümü [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=57071)' nde bulunabilir.

DC Aracısı yazılımının geçerli sürümünü kaldırmak gerekli değildir-yükleyicinin yerinde bir yükseltme gerçekleştirmesi gerekir. DC Aracısı yazılımı yükseltilirken her zaman bir yeniden başlatma gereklidir-bu gereksinim, çekirdek Windows davranışından kaynaklanır. 

Yazılım yükseltme standart MSI yordamları kullanılarak otomatikleştirilebilir, örneğin: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Yükleyicinin makineyi otomatik olarak yeniden başlatması tercih ediyorsanız `/norestart` bayrağını atlayabilirsiniz.

`Get-AzureADPasswordProtectionDCAgent` cmdlet 'i, bir ormandaki yüklü olan tüm DC aracılarının yazılım sürümünü sorgulamak için kullanılabilir.

## <a name="multiple-forest-deployments"></a>Birden çok orman dağıtımı

Azure AD parola korumasını birden çok ormanda dağıtmak için ek gereksinim yoktur. Her orman, "Tek ormanlı dağıtım" bölümünde açıklandığı gibi bağımsız olarak yapılandırılır. Her parola koruma proxy 'si, etki alanı denetleyicilerini yalnızca katıldığı ormandan destekleyebilir. Herhangi bir ormandaki parola koruma yazılımı, Active Directory güven yapılandırmalarına bakılmaksızın diğer ormanlarda dağıtılan parola koruma yazılımlarıdır.

## <a name="read-only-domain-controllers"></a>Salt okuma etki alanı denetleyicileri

Parola değişiklikleri/kümeler, salt okuma etki alanı denetleyicilerinde (RODC) işlenmez ve kalıcı hale getirilir. Bunlar yazılabilir etki alanı denetleyicilerine iletilir. Bu nedenle, DC Aracısı yazılımını RODC 'Lere yüklemek zorunda değilsiniz.

## <a name="high-availability"></a>Yüksek kullanılabilirlik

Parola koruması için başlıca kullanılabilirlik sorunu, bir ormandaki etki alanı denetleyicileri yeni ilkeleri veya Azure 'dan başka verileri indirmeye çalıştığında proxy sunucularının kullanılabilirliğine yöneliktir. Her DC Aracısı, hangi ara sunucunun çağrılacağını saptarken basit bir hepsini bir kez deneme stili algoritması kullanır. Aracı yanıt vermeyen proxy sunucularını atlar. Hem dizin hem de SYSVOL klasörü durumunun iyi şekilde çoğaltılmasını sağlayan çoğu tam bağlı Active Directory dağıtımı için, kullanılabilirlik sağlamak üzere iki proxy sunucu yeterlidir. Bu, yeni ilkelerin ve diğer verilerin zamanında indirilmesine neden olur. Ancak ek proxy sunucuları dağıtabilirsiniz.

DC Aracısı yazılımının tasarımı, yüksek kullanılabilirliğe ilişkin olağan sorunları azaltır. DC Aracısı en son indirilen parola ilkesinin yerel bir önbelleğini tutar. Tüm kayıtlı proxy sunucuları kullanılamaz hale gelirse, DC aracıları önbelleğe alınmış parola ilkelerini zorlamaya devam eder. Büyük bir dağıtımda parola ilkeleri için makul bir güncelleştirme sıklığı genellikle günler, saat veya daha az değildir. Bu nedenle, proxy sunucularının kısa kesintileri Azure AD parola korumasını önemli ölçüde etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD parola koruması için ihtiyaç duyduğunuz hizmetleri şirket içi sunucularınızda yüklemişseniz, [yükleme sonrası yapılandırma gerçekleştirin ve dağıtımınızı tamamlamak için raporlama bilgilerini toplayın](howto-password-ban-bad-on-premises-operations.md) .

[Azure AD parola korumasına kavramsal genel bakış](concept-password-ban-bad-on-premises.md)
