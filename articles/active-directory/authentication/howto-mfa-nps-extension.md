---
title: Azure AD Multi-Factor Authentication 'YI NPS ile kullanma-Azure Active Directory
description: Mevcut ağ Ilkesi sunucusu (NPS) kimlik doğrulama altyapınızın Azure AD Multi-Factor Authentication yeteneklerini nasıl kullanacağınızı öğrenin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 484dd8313710332660bb20d55f3dac2aa21bbc61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98232531"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Mevcut ağ Ilkesi sunucusu (NPS) altyapınızı Azure AD Multi-Factor Authentication tümleştirme

Azure AD Multi-Factor Authentication için ağ Ilkesi sunucusu (NPS) uzantısı, mevcut sunucularınızı kullanarak kimlik doğrulama altyapınıza bulut tabanlı MFA özellikleri ekler. NPS uzantısıyla, yeni sunucuları yüklemek, yapılandırmak ve bakımını yapmak zorunda kalmadan mevcut kimlik doğrulama akışınıza telefon araması, SMS mesajı veya telefon uygulaması doğrulama ekleyebilirsiniz.

NPS uzantısı, federal veya eşitlenmiş kullanıcılar için ikinci bir kimlik doğrulama faktörü sağlamak üzere RADIUS ve bulut tabanlı Azure AD Multi-Factor Authentication arasında bir bağdaştırıcı görevi görür.

## <a name="how-the-nps-extension-works"></a>NPS uzantısının çalışması

Azure AD Multi-Factor Authentication için NPS uzantısını kullandığınızda, kimlik doğrulama akışı aşağıdaki bileşenleri içerir:

1. **NAS/VPN sunucusu** , VPN istemcilerinden gelen istekleri alır ve NPS sunucularına RADIUS isteklerine dönüştürür.
2. **NPS sunucusu** , RADIUS istekleri için birincil kimlik doğrulamasını gerçekleştirmek üzere Active Directory Domain Services (AD DS) bağlanır ve başarılı olduğunda, isteği yüklü herhangi bir uzantıya geçirir.  
3. **NPS uzantısı** , ikincil kimlik doğrulaması Için BIR Azure AD Multi-Factor Authentication isteği tetikler. Uzantı yanıtı aldıktan sonra ve MFA sınaması başarılı olursa, Azure STS tarafından verilen bir MFA talebi içeren güvenlik belirteçlerine sahip NPS sunucusunu sağlayarak kimlik doğrulama isteğini tamamlar.
4. **Azure AD MFA** , Kullanıcı ayrıntılarını almak için Azure Active Directory (Azure AD) ile iletişim kurar ve kullanıcıya yapılandırılmış bir doğrulama yöntemi kullanarak ikincil kimlik doğrulamasını gerçekleştirir.

Aşağıdaki diyagramda bu üst düzey kimlik doğrulama isteği akışı gösterilmektedir:

![Bir VPN sunucusu üzerinden NPS sunucusuna ve Azure AD Multi-Factor Authentication NPS uzantısına kimlik doğrulaması yapan kullanıcı için kimlik doğrulama akışı diyagramı](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>RADIUS protokol davranışı ve NPS uzantısı

RADIUS bir UDP protokolü olduğundan, Gönderen paket kaybını varsayar ve yanıt bekler. Bir süre sonra bağlantı zaman aşımına uğrar. Bu durumda, gönderen paketin hedefe ulaşamadığını varsaydığı için paket yeniden gönderilir. Bu makaledeki kimlik doğrulama senaryosunda, VPN sunucuları isteği gönderir ve bir yanıt bekler. Bağlantı zaman aşımına uğrarsa, VPN sunucusu isteği yeniden gönderir.

![NPS sunucusundan yanıt zaman aşımından sonra RADIUS UDP paket akışı ve isteklerinin diyagramı](./media/howto-mfa-nps-extension/radius-flow.png)

NPS sunucusu, MFA isteği hala işlenebildiği için bağlantı zaman aşımına uğramadan önce VPN sunucusunun özgün isteğine yanıt vermeyebilir. Kullanıcı MFA istemine başarıyla yanıt vermeyebilir, bu nedenle Azure AD Multi-Factor Authentication NPS uzantısı bu olayın tamamlanmasını bekliyor. Bu durumda, NPS sunucusu, ek VPN sunucusu isteklerini yinelenen bir istek olarak tanımlar. NPS sunucusu, bu yinelenen VPN sunucusu isteklerini atar.

![RADIUS sunucusundan yinelenen istekleri iptal eden NPS sunucusu diyagramı](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

NPS sunucusu günlüklerine bakarsanız, bu ek isteklerin atıldığını görebilirsiniz. Bu davranış, son kullanıcının tek bir kimlik doğrulama girişimine yönelik birden çok istek almayı korumak için tasarımdır. NPS sunucusu olay günlüğündeki atılan istekler, NPS sunucusu veya Azure AD Multi-Factor Authentication NPS uzantısıyla ilgili bir sorun olduğunu göstermez.

Atılan istekleri en aza indirmek için VPN sunucularının en az 60 saniyelik bir zaman aşımı ile yapılandırılmasını öneririz. Gerekirse, ya da olay günlüklerindeki atılan istekleri azaltmak için, VPN sunucusu zaman aşımı değerini 90 veya 120 saniye olarak artırabilirsiniz.

Bu UDP protokol davranışı nedeniyle, NPS sunucusu yinelenen bir istek alabilir ve Kullanıcı ilk isteği zaten yanıtladıktan sonra bile başka bir MFA istemi gönderebilir. Bu zamanlama koşulunu önlemek için Azure AD Multi-Factor Authentication NPS uzantısı, VPN sunucusuna başarılı bir yanıt gönderildikten sonra yinelenen istekleri 10 saniyeye kadar filtrelemeye ve atmaya devam eder.

![Başarılı bir yanıt döndürülbaşladıktan sonra on saniye boyunca VPN sunucusundan yinelenen istekleri atmayı sürdürerek NPS sunucusu diyagramı](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Ayrıca, Azure AD Multi-Factor Authentication istemi başarılı olduğunda bile, NPS sunucusu olay günlüklerinde atılan istekleri görebilirsiniz. Bu beklenen davranıştır ve NPS sunucusu ya da Azure AD Multi-Factor Authentication NPS uzantısıyla ilgili bir sorun olmadığını göstermez.

## <a name="plan-your-deployment"></a>Dağıtımınızı planlama

NPS uzantısı, artıklığı otomatik olarak işler, bu nedenle özel bir yapılandırmaya gerek kalmaz.

İhtiyaç duyduğunuz kadar çok sayıda Azure AD Multi-Factor Authentication etkin NPS sunucusu oluşturabilirsiniz. Birden çok sunucu yüklüyorsanız, bunların her biri için fark istemci sertifikası kullanmanız gerekir. Her sunucu için bir sertifika oluşturmak, her sertifikayı tek tek güncelleştirebilmeniz ve tüm sunucularınızda kapalı kalma süresi konusunda endişelenmenize gerek kalmaz.

VPN sunucuları, kimlik doğrulama isteklerini yönlendirdiklerinden, yeni Azure AD Multi-Factor Authentication özellikli NPS sunucularının farkında olmaları gerekir.

## <a name="prerequisites"></a>Önkoşullar

NPS uzantısı, Mevcut altyapınızla birlikte çalışmak üzere tasarlanmıştır. Başlamadan önce aşağıdaki önkoşullara sahip olduğunuzdan emin olun.

### <a name="licenses"></a>Lisanslar

Azure AD Multi-Factor Authentication için NPS uzantısı, [Azure ad Multi-Factor Authentication lisanslarına](./concept-mfa-howitworks.md)sahip müşteriler tarafından kullanılabilir. Azure AD Multi-Factor Authentication için Kullanıcı başına veya kimlik doğrulama lisansı başına tüketim tabanlı lisanslar, NPS uzantısıyla uyumlu değildir.

### <a name="software"></a>Yazılım

Windows Server 2012 veya üzeri.

### <a name="libraries"></a>Kitaplıklar

Aşağıdaki kitaplığı el ile yüklemeniz gerekir:

- [Visual C++ Redistributable for Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

Aşağıdaki kitaplıklar, uzantısıyla otomatik olarak yüklenir.

- [Visual Studio 2013 için yeniden dağıtılabilir paketler Visual C++ (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Windows PowerShell için Microsoft Azure Active Directory Modülü Version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Windows PowerShell için Microsoft Azure Active Directory Modülü, zaten mevcut değilse kurulum sürecinin bir parçası olarak çalıştırdığınız bir yapılandırma betiği aracılığıyla da yüklenir. Daha önce yüklenmemişse bu modülü yüklemeniz gerekmez.

### <a name="azure-active-directory"></a>Azure Active Directory

NPS uzantısını kullanan herkesin Azure AD Connect kullanılarak Azure AD ile eşitlenmesi ve MFA için kayıtlı olması gerekir.

Uzantıyı yüklediğinizde, Azure AD kiracınızın *KIRACı kimliği* ve yönetici kimlik bilgilerine sahip olmanız gerekir. Kiracı KIMLIĞINI almak için aşağıdaki adımları izleyin:

1. Azure kiracının genel yöneticisi olarak [Azure Portal](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin.
1. **Genel bakış** sayfasında, *kiracı bilgileri* gösterilir. *KIRACı kimliği*' nin yanında, aşağıdaki örnekte gösterildiği gibi **Kopyala** simgesini seçin:

   ![Azure portal Kiracı KIMLIĞINI alma](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Ağ gereksinimleri

NPS sunucusu, 80 ve 443 bağlantı noktaları üzerinden aşağıdaki URL 'lerle iletişim kurabilmelidir:

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /Credentials.Azure.com*

Ayrıca, [belirtilen PowerShell betiği kullanılarak bağdaştırıcının kurulumunu](#run-the-powershell-script)tamamlaması Için aşağıdaki URL 'lerle bağlantı gereklidir:

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.net*
* *https: \/ /www.powershellgallery.com*
* *https: \/ /aadcdn.msftauthimages.net*

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

NPS uzantısını yüklemeden önce, kimlik doğrulama trafiğini işleyecek ortamı hazırlayın.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Etki alanına katılmış sunucuda NPS rolünü etkinleştirme

NPS sunucusu Azure AD 'ye bağlanır ve MFA isteklerinin kimliğini doğrular. Bu rol için bir sunucu seçin. NPS uzantısı, RADIUS olmayan istekler için hata oluşturduğundan, diğer hizmetlerden gelen istekleri işleyemeyen bir sunucu seçmeyi öneririz. NPS sunucusunun ortamınız için birincil ve ikincil kimlik doğrulama sunucusu olarak ayarlanması gerekir. RADIUS isteklerini başka bir sunucuya ara.

1. Sunucunuzda **Sunucu Yöneticisi** açın. *Hızlı başlangıç* menüsünde **rol ve Özellik Ekleme Sihirbazı '** nı seçin.
2. Yükleme türü için **rol tabanlı veya özellik tabanlı yükleme**' yi seçin.
3. **Ağ İlkesi ve erişim Hizmetleri** sunucu rolünü seçin. Bu rolü çalıştırmak için gereken ek özellikleri bildirmek üzere bir pencere açılır.
4. *Onay* sayfasına kadar sihirbaza devam edin. Hazırlandığınızda, **Install**' ı seçin.

NPS sunucu rolünün yüklenmesi birkaç dakika sürebilir. İşiniz bittiğinde, bu sunucuyu VPN çözümünden gelen RADIUS isteklerini işleyecek şekilde yapılandırmak için aşağıdaki bölümlerle devam edin.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>VPN çözümünüzü NPS sunucusuyla iletişim kuracak şekilde yapılandırma

Kullandığınız VPN çözümüne bağlı olarak, RADIUS kimlik doğrulama ilkenizi yapılandırma adımları değişir. VPN ilkenizi, RADIUS NPS sunucunuza işaret etmek üzere yapılandırın.

### <a name="sync-domain-users-to-the-cloud"></a>Etki alanı kullanıcılarını buluta eşitleme

Bu adım kiracınızda zaten tamamlanmış olabilir, ancak Azure AD Connect veritabanlarınızı yakın zamanda eşitler.

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory**  >  **Azure AD Connect** seçin
3. Eşitleme durumunuzu **etkinleştirildiğini** ve son eşitlemenin bir saatten daha önce olduğunu doğrulayın.

Eşitlemenin yeni bir hepsini açmanız gerekiyorsa, bkz. [Azure AD Connect eşitleme: Zamanlayıcı](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Kullanıcılarınızın hangi kimlik doğrulama yöntemlerini kullanabileceğini belirleme

NPS uzantısı dağıtımıyla hangi kimlik doğrulama yöntemlerinin kullanılabilir olduğunu etkileyen iki etken vardır:

* RADIUS istemcisi (VPN, NetScaler sunucusu veya diğer) ile NPS sunucuları arasında kullanılan parola şifreleme algoritması.
   - **Pap** , BULUTTA Azure AD Multi-Factor Authentication 'ın tüm kimlik doğrulama yöntemlerini destekler: telefon araması, tek yönlü SMS mesajı, mobil uygulama BILDIRIMI, oath Donanım belirteçleri ve mobil uygulama doğrulama kodu.
   - **CHAPv2** ve **EAP** desteği telefon araması ve mobil uygulama bildirimi.

    > [!NOTE]
    > NPS uzantısını dağıtırken, kullanıcılarınız için hangi yöntemlerin kullanılabildiğini değerlendirmek üzere bu faktörleri kullanın. RADIUS istemciniz PAP 'yi destekliyorsa, ancak istemci UX ' in doğrulama kodu için giriş alanı yoksa, telefon araması ve mobil uygulama bildirimi desteklenen iki seçenekten oluşan bir uygulamadır.
    >
    > Ayrıca, kullanılan kimlik doğrulama protokolüne (PAP, CHAP veya EAP) bakılmaksızın, MFA yönteminiz metin tabanlı (SMS, mobil uygulama doğrulama kodu veya OATH donanım belirteci) ise ve kullanıcının VPN istemcisi kullanıcı arabirimi giriş alanına bir kod veya metin girmesini gerektiriyorsa, kimlik doğrulaması başarılı olabilir. *Ancak* ağ erişim ilkesinde yapılandırılmış HERHANGI bir RADIUS ÖZNITELIĞI, RADIUS CIO 'ya (VPN ağ geçidi gibi ağ erişim cihazı *) iletilmez.* Sonuç olarak, VPN istemcisinin erişimi veya daha az erişimi olmasını istediğiniz daha fazla erişimi olabilir.

* İstemci uygulamasının (VPN, NetScaler sunucusu veya diğer) işleyebileceği giriş yöntemleri. Örneğin, VPN istemcisinin, kullanıcının bir metin veya mobil uygulamadan bir doğrulama kodu yazmasıdır.

[Desteklenmeyen kimlik doğrulama yöntemlerini Azure 'da devre dışı](howto-mfa-mfasettings.md#verification-methods) bırakabilirsiniz.

### <a name="register-users-for-mfa"></a>MFA için kullanıcıları kaydetme

NPS uzantısını dağıtmadan ve kullanmadan önce, Azure AD Multi-Factor Authentication gerçekleştirmek için gerekli olan kullanıcıların MFA için kayıtlı olması gerekir. Uzantıyı dağıtırken Test etmek için Azure AD Multi-Factor Authentication için tam olarak kayıtlı olan en az bir test hesabınız olması gerekir.

Bir sınama hesabı oluşturmanız ve yapılandırmanız gerekirse, aşağıdaki adımları kullanın:

1. [https://aka.ms/mfasetup](https://aka.ms/mfasetup)Bir test hesabıyla oturum açın.
2. Bir doğrulama yöntemi ayarlamak için istemleri izleyin.
3. Yönetici Kullanıcı olarak Azure portal, test hesabı için çok faktörlü kimlik doğrulaması gerektiren [bir koşullu erişim ilkesi oluşturun](howto-mfa-getstarted.md#create-conditional-access-policy) .

> [!IMPORTANT]
>
> Kullanıcıların Azure AD Multi-Factor Authentication için başarıyla kaydolduğunuzdan emin olun. Kullanıcılar daha önce yalnızca self servis parola sıfırlama (SSPR) için kaydedilmişse, hesapları için *Strongauthenticationmethods* etkinleştirilir. Kullanıcı yalnızca SSPR için kayıtlı olsa bile, *Strongauthenticationmethods* YAPıLANDıRıLDıĞıNDA Azure AD Multi-Factor Authentication zorlanır.
>
> Aynı anda SSPR ve Azure AD Multi-Factor Authentication yapılandıran Birleşik güvenlik kaydı etkinleştirilebilir. Daha fazla bilgi için bkz. [Azure Active Directory Birleşik güvenlik bilgileri kaydını etkinleştirme](howto-registration-mfa-sspr-combined.md).
>
> Ayrıca, daha önce yalnızca SSPR 'yi etkinleştirmişse, [Kullanıcıları kimlik doğrulama yöntemlerini yeniden kaydetmeye zorlayabilirsiniz](howto-mfa-userdevicesettings.md#manage-user-authentication-options) .

## <a name="install-the-nps-extension"></a>NPS uzantısını yükler

> [!IMPORTANT]
> NPS uzantısını VPN erişim noktasına göre farklı bir sunucuya yükler.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Azure AD MFA için NPS uzantısını indirme ve yükleme

NPS uzantısını indirip yüklemek için aşağıdaki adımları izleyin:

1. [NPS uzantısını](https://aka.ms/npsmfa) Microsoft İndirme Merkezi ' nden indirin.
1. İkiliyi, yapılandırmak istediğiniz ağ Ilkesi sunucusuna kopyalayın.
1. *setup.exe* çalıştırın ve yükleme yönergelerini izleyin. Hatalarla karşılaşırsanız, [Önkoşul bölümündeki kitaplıkların](#libraries) başarılı bir şekilde yüklendiğinden emin olun.

#### <a name="upgrade-the-nps-extension"></a>NPS uzantısını yükseltme

Daha sonra var olan bir NPS uzantısı yüklemesini yükseltirseniz, temel sunucunun yeniden başlatılmasını önlemek için aşağıdaki adımları izleyin:

1. Mevcut sürümü kaldırın.
1. Yeni yükleyiciyi çalıştırın.
1. *Ağ Ilkesi sunucusu (IAS)* hizmetini yeniden başlatın.

### <a name="run-the-powershell-script"></a>PowerShell betiğini çalıştırma

Yükleyici konumunda bir PowerShell betiği `C:\Program Files\Microsoft\AzureMfa\Config` ( `C:\` yükleme sürücünüz) oluşturur. Bu PowerShell betiği, her çalıştırılışında aşağıdaki eylemleri gerçekleştirir:

* Kendinden imzalı bir sertifika oluşturur.
* Sertifikanın ortak anahtarını Azure AD 'deki hizmet sorumlusu ile ilişkilendirir.
* Sertifikayı yerel makine sertifika deposunda depolar.
* Sertifikanın özel anahtarına ağ kullanıcısı için erişim izni verir.
* NPS hizmetini yeniden başlatır.

Kendi sertifikalarınızı kullanmak istemiyorsanız (PowerShell betiğinin oluşturduğu otomatik olarak imzalanan sertifikalar yerine), NPS uzantısı yüklemesini gerçekleştirmek için PowerShell betiğini çalıştırın. Uzantıyı birden çok sunucuya yüklerseniz, her sunucunun kendi sertifikası olmalıdır.

Yük Dengeleme Özellikleri veya artıklık sağlamak için, aşağıdaki adımları ek NPS sunucularında yineleyin:

1. Yönetici olarak bir Windows PowerShell istemi açın.
1. Dizinleri yükleyicinin PowerShell betiğini oluşturulduğu yere değiştirin:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Yükleyici tarafından oluşturulan PowerShell betiğini çalıştırın.

   PowerShell 'in paketleri düzgün şekilde bağlanabilmesi ve indirebilmesi için öncelikle TLS 1,2 ' i etkinleştirmeniz gerekebilir:
   
   `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

   > [!IMPORTANT]
   > Azure Kamu veya Azure Çin 21Vianet bulutlarını kullanan müşteriler için öncelikle `Connect-MsolService` *AzureMfaNpsExtnConfigSetup.ps1* betikteki cmdlet 'leri düzenleyerek gerekli bulutun *AzureEnvironment* parametrelerini ekleyin. Örneğin, *-AzureEnvironment Uskamu* veya *-AzureEnvironment AzureChinaCloud* belirtin.
   >
   > Daha fazla bilgi için bkz. [Connect-MsolService parametre başvurusu](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. İstendiğinde, Azure AD 'de yönetici olarak oturum açın.
1. Kiracı KIMLIĞINIZ için PowerShell istemleri. Önkoşullar bölümündeki Azure portal kopyaladığınız *KIRACı kimliği* GUID 'sini kullanın.
1. Komut dosyası tamamlandığında bir başarı iletisi gösterilir.  

Önceki bilgisayar sertifikanızın süresi dolmuşsa ve yeni bir sertifika oluşturulduysa, süresi geçmiş tüm sertifikaları silmelisiniz. Süre dolma sertifikaları olması, NPS uzantısıyla başlayarak soruna neden olabilir.

> [!NOTE]
> PowerShell betiği ile sertifika oluşturmak yerine kendi sertifikalarınızı kullanırsanız, NPS adlandırma kuralına göre hizalandıklarından emin olun. Konu adı **CN = \<TenantID\> , OU = Microsoft NPS uzantısı** olmalıdır.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure Kamu veya Azure Çin 21Vianet ek adımlar

Azure Kamu veya Azure Çin 21Vianet bulutlarını kullanan müşteriler için, her NPS sunucusunda aşağıdaki ek yapılandırma adımları gereklidir.

> [!IMPORTANT]
> Yalnızca bir Azure Kamu veya Azure Çin 21Vianet müşterisiyseniz bu kayıt defteri ayarlarını yapılandırın.

1. Azure Kamu veya Azure Çin 21Vianet müşterisiyseniz, NPS sunucusunda **kayıt defteri Düzenleyicisi** 'ni açın.
1. `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa` sayfasına gidin.
1. Azure Kamu müşterileri için aşağıdaki anahtar değerlerini ayarlayın.:

    | Kayıt defteri anahtarı       | Değer |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Azure Çin 21Vianet müşterileri için aşağıdaki anahtar değerlerini ayarlayın:

    | Kayıt defteri anahtarı       | Değer |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Her NPS sunucusu için kayıt defteri anahtarı değerlerini ayarlamak için önceki iki adımı tekrarlayın.
1. NPS hizmetini her NPS sunucusu için yeniden başlatın.

    En az etki için, her NPS sunucusunu tek seferde bir tane tek tek götürün ve tüm bağlantıların boşlanmasını bekleyin.

### <a name="certificate-rollover"></a>Sertifika geçişi

NPS uzantısının Release *1.0.1.32* ile birden çok sertifika okumak artık desteklenmektedir. Bu yetenek, sertifika güncelleştirmelerinin kullanım süresinin dolması için kolaylaştırılmasına yardımcı olur. Kuruluşunuz NPS uzantısının önceki bir sürümünü çalıştırıyorsa, sürüm *1.0.1.32* veya sonraki bir sürüme yükseltin.

Betik tarafından oluşturulan sertifikalar `AzureMfaNpsExtnConfigSetup.ps1` 2 yıl için geçerlidir. Sertifikaları süre sonu için izleyin. NPS uzantısı için sertifikalar, *Kişisel* ' ın altındaki *Yerel bilgisayar* sertifika deposuna yerleştirilir ve yükleme BETIĞINE verilen kiracı kimliğine *verilir* .

Bir sertifika sona erme tarihine yaklaşıyorsa, değiştirmek için yeni bir sertifika oluşturulmalıdır.  Bu işlem, `AzureMfaNpsExtnConfigSetup.ps1` yeniden çalıştırılarak ve istendiğinde aynı KIRACı kimliği korunarak gerçekleştirilir. Bu işlem, ortamınızdaki her NPS sunucusunda tekrarlanmış olmalıdır.

## <a name="configure-your-nps-extension"></a>NPS uzantınızı yapılandırma

Ortamınız hazırlanmışsa ve NPS uzantısı artık gerekli sunuculara yüklenmişse, uzantıyı yapılandırabilirsiniz.

Bu bölüm, başarılı bir NPS uzantısı dağıtımı için tasarım konuları ve öneriler içerir.

### <a name="configuration-limitations"></a>Yapılandırma sınırlamaları

- Azure AD Multi-Factor Authentication için NPS uzantısı, kullanıcıları ve ayarları MFA sunucusundan buluta geçirmeye yönelik araçlar içermez. Bu nedenle, mevcut dağıtım yerine yeni dağıtımlar için uzantıyı kullanmanızı öneririz. Uzantıyı var olan bir dağıtımda kullanıyorsanız, kullanıcılarınızın MFA ayrıntılarını bulutta doldurmak için yeniden prova yapması gerekir.  
- NPS uzantısı, Ikincil kimlik doğrulamasını gerçekleştirmek için Azure AD Multi-Factor Authentication üzerinde kullanıcıyı tanımlamak üzere şirket içi AD DS ortamından UPN kullanır. Uzantı, alternatif oturum açma KIMLIĞI gibi farklı bir tanıtıcıyı veya UPN dışında özel AD DS alanı kullanacak şekilde yapılandırılabilir. Daha fazla bilgi için, [Multi-Factor Authentication IÇIN NPS uzantısının gelişmiş yapılandırma seçenekleri](howto-mfa-nps-extension-advanced.md)makalesine bakın.
- Tüm şifreleme protokolleri tüm doğrulama yöntemlerini desteklemez.
   - **Pap** telefon aramasını, tek yönlü SMS iletisini, mobil uygulama bildirimini ve mobil uygulama doğrulama kodunu destekler
   - **CHAPv2** ve **EAP** desteği telefon araması ve mobil uygulama bildirimi

### <a name="control-radius-clients-that-require-mfa"></a>MFA gerektiren RADIUS istemcilerini denetleme

NPS uzantısını kullanarak bir RADIUS istemcisi için MFA 'yı etkinleştirdikten sonra, bu istemciye yönelik tüm kimlik doğrulamaları MFA gerçekleştirmek için gereklidir. Bazı RADIUS istemcileri için MFA 'yı etkinleştirmek istiyorsanız, diğer bir deyişle, iki NPS sunucusunu yapılandırabilir ve uzantıyı yalnızca birine yükleyebilirsiniz.

MFA ile yapılandırılan NPS sunucusuna istek göndermesini ve diğer RADIUS istemcilerini uzantı ile yapılandırılmamış NPS sunucusuna göndermesini gerektirmek istediğiniz RADIUS istemcilerini yapılandırın.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA için kayıtlı olmayan kullanıcılara hazırlanma

MFA için kayıtlı olmayan kullanıcılarınız varsa, kimlik doğrulamaya çalıştıklarında ne olacağını belirleyebilirsiniz. Bu davranışı denetlemek için, *HKLM\Software\Microsoft\AzureMFA* kayıt defteri yolundaki *REQUIRE_USER_MATCH* ayarını kullanın. Bu ayarın tek bir yapılandırma seçeneği vardır:

| Anahtar | Değer | Varsayılan |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | DOĞRU/YANLıŞ | Ayarlanmadı (TRUE değerine denktir) |

Bu ayar, bir Kullanıcı MFA için kayıtlı olmadığında ne yapılacağını belirler. Anahtar mevcut olmadığında, ayarlanmamışsa veya *true* olarak ayarlandığında ve Kullanıcı kayıtlı değilse, uzantı MFA sınamasını başarısız olur.

Anahtar *yanlış* olarak ayarlandığında ve Kullanıcı kayıtlı değilse, KIMLIK doğrulaması MFA yapılmadan devam eder. Bir Kullanıcı MFA 'ya kaydedildiyse, *REQUIRE_USER_MATCH* *false* olarak ayarlanmış olsa bile MFA ile kimlik doğrulaması yapılmalıdır.

Kullanıcılarınız eklenirken bu anahtarı oluşturmayı ve *yanlış* olarak ayarlamayı seçebilirsiniz ve Azure AD Multi-Factor Authentication henüz hiç kaydedilmeyebilir. Bununla birlikte, anahtarı ayarlamak MFA 'ya kayıtlı olmayan kullanıcıların oturum açmasına izin verdiğinden, üretime geçmeden önce bu anahtarı kaldırmanız gerekir.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="nps-extension-health-check-script"></a>NPS uzantısı durum denetimi betiği

NPS uzantısı sorunlarını giderirken temel sistem durumu denetimi adımlarını gerçekleştirmek için aşağıdaki komut dosyası kullanılabilir.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Nasıl yaparım? istemci sertifikası 'nın beklendiği şekilde yüklendiğini doğrulayın.

Sertifika deposunda yükleyici tarafından oluşturulan kendinden imzalı sertifikayı bulun ve özel anahtarın Kullanıcı *ağ hizmeti*'ne verilmiş izinlere sahip olup olmadığını denetleyin. Sertifika, **CN \<tenantid\> , OU = Microsoft NPS uzantısının** konu adına sahiptir

Betiği tarafından oluşturulan otomatik olarak imzalanan sertifikaların `AzureMfaNpsExtnConfigSetup.ps1` , iki yıllık geçerlilik ömrü vardır. Sertifikanın yüklendiği doğrulanırken, sertifikanın süresinin dolmadığını da denetlemeniz gerekir.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>İstemci sertifikamın Azure AD 'deki kiracım ile ilişkili olduğunu nasıl doğrulayabilirim?

PowerShell komut istemi ' ni açın ve aşağıdaki komutları çalıştırın:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Bu komutlar, kiracınızı, PowerShell oturumunuzda NPS uzantısı örneğinizle ilişkilendiren tüm sertifikaları yazdırır. İstemci sertifikanızı özel anahtar olmadan *Base-64 kodlamalı bir X. 509.440 (. cer)* dosyası olarak dışarı aktararak ve PowerShell 'deki listeyle karşılaştırarak sertifikanızın olup olmadığına bakın.

Aşağıdaki komut, *C:* sürücünüzün biçimindeki *. cer* biçiminde olan *npscercertificate* adlı bir dosya oluşturur.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Bu komutu çalıştırdıktan sonra *C:* sürücünüzün köküne gidin, dosyayı bulun ve üzerine çift tıklayın. Ayrıntılar ' a gidin ve "parmak izi" ' na kaydırın. Sunucuda yüklü sertifikanın parmak izini bu bir ile karşılaştırın. Sertifika parmak izleri eşleşmelidir.

*Geçerli-* ve *geçerli-Until* zaman damgalarına, bir komut birden fazla sertifika döndürürse açık yanlış sığacak şekilde filtre uygulamak için kullanılabilir.

### <a name="why-cannot-i-sign-in"></a>Neden oturum açamıyorum?

Parolanızın süresinin dolmadığından emin olun. NPS uzantısı, oturum açma iş akışının bir parçası olarak parolaların değiştirilmesini desteklemez. Daha fazla yardım için kuruluşunuzun BT ekibine başvurun.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>İsteklerim neden ADAL belirteci hatasıyla başarısız oluyor?

Bu hata, birkaç nedenden biri nedeniyle olabilir. Sorunlarını gidermek için aşağıdaki adımları kullanın:

1. NPS sunucunuzu yeniden başlatın.
2. İstemci sertifikası 'nın beklendiği gibi yüklendiğini doğrulayın.
3. Sertifikanın Azure AD 'deki kiracınızla ilişkili olduğunu doğrulayın.
4. Uzantıyı çalıştıran sunucudan `https://login.microsoftonline.com/` adresine erişilebildiğini doğrulayın.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Kimlik doğrulaması, kullanıcının bulunamadığını bildiren HTTP günlüklerinde hata vererek başarısız olur mu?

AD Connect 'in çalıştığını ve kullanıcının hem şirket içi AD DS ortamında hem de Azure AD 'de bulunduğundan emin olun.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Tüm kimlik doğrulamalarımı olan günlüklerde neden HTTP Connect hataları görüyorum?

NPS uzantısını çalıştıran sunucudan https://adnotifications.windowsazure.com adresine ulaşılabildiğini doğrulayın.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Geçerli bir sertifika mevcut olmasına rağmen neden kimlik doğrulaması çalışmıyor?

Önceki bilgisayar sertifikanızın süresi dolmuşsa ve yeni bir sertifika oluşturulduysa, süresi geçmiş tüm sertifikaları silin. Süre dolmuþ sertifikalar, NPS uzantısıyla başlayarak soruna neden olabilir.

Geçerli bir sertifikanız olup olmadığını denetlemek için, MMC kullanarak yerel *bilgisayar hesabının sertifika depolama alanını* denetleyin ve sertifikanın süre sonu tarihini geçirmemiş olduğundan emin olun. Yeni geçerli bir sertifika oluşturmak için [PowerShell yükleyici betiğini çalıştırma](#run-the-powershell-script)adımlarını yeniden çalıştırın.

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Neden atılan istekleri NPS sunucu günlüklerinde görüyorum?

Bir VPN sunucusu, zaman aşımı değeri çok düşükse NPS sunucusuna yinelenen istekler gönderebilir. NPS sunucusu bu yinelenen istekleri algılar ve bunları atar. Bu davranış tasarım ve NPS sunucusu ya da Azure AD Multi-Factor Authentication NPS uzantısıyla ilgili bir sorun olduğunu göstermez.

NPS sunucu günlüklerinde atılan paketleri neden görmenizin nedeni hakkında daha fazla bilgi için, bu makalenin başlangıcında [RADIUS protokol davranışı ve NPS uzantısı](#radius-protocol-behavior-and-the-nps-extension) bölümüne bakın.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL Protokollerini ve Şifre Paketlerini yönetme

Kuruluşunuz tarafından gerektirmedikleri takdirde eski ve daha zayıf şifre paketlerinin devre dışı bırakılması veya kaldırılması önerilir. Bu görevin nasıl tamamlanacağı hakkında bilgi, [AD FS IÇIN SSL/TLS protokollerini ve şifre paketlerini yönetme](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) makalesinde bulunabilir

### <a name="additional-troubleshooting"></a>Ek sorun giderme işlemleri

Ek sorun giderme kılavuzu ve olası çözümler, makalede [Azure AD Multi-Factor Authentication IÇIN NPS uzantısından hata Iletilerini çözümle](howto-mfa-nps-extension-errors.md)bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Windows Server 'da ağ Ilkesi sunucusu 'na genel bakış ve yapılandırma](/windows-server/networking/technologies/nps/nps-top)

- Oturum açmak için alternatif kimlikleri yapılandırın veya [Multi-Factor Authentication IÇIN NPS uzantısının gelişmiş yapılandırma seçeneklerinde](howto-mfa-nps-extension-advanced.md) iki adımlı doğrulama gerçekleştirmemelidir.

- NPS uzantısını kullanarak [Uzak Masaüstü Ağ Geçidi](howto-mfa-nps-extension-rdg.md) ve [VPN sunucularını](howto-mfa-nps-extension-vpn.md) tümleştirmeyi öğrenin

- [Azure AD Multi-Factor Authentication için NPS uzantısından hata iletilerini çözümleyin](howto-mfa-nps-extension-errors.md)
