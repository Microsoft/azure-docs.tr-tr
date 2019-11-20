---
title: Azure MFA özellikleri sağlamak için mevcut NPS sunucularını kullanın-Azure Active Directory
description: Mevcut kimlik doğrulama altyapınıza bulut tabanlı iki adımlı doğrulama özelliği ekleyin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8606ad9afb6642fa29cc3cae523c31e129c7ebd
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73061478"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Mevcut NPS altyapınızı Azure Multi-Factor Authentication tümleştirin

Azure MFA için ağ Ilkesi sunucusu (NPS) uzantısı, mevcut sunucularınızı kullanarak kimlik doğrulama altyapınıza bulut tabanlı MFA özellikleri ekler. NPS uzantısıyla, yeni sunucuları yüklemek, yapılandırmak ve bakımını yapmak zorunda kalmadan mevcut kimlik doğrulama akışınıza telefon araması, SMS mesajı veya telefon uygulaması doğrulama ekleyebilirsiniz. 

Bu uzantı, Azure MFA sunucusunu dağıtmadan VPN bağlantılarını korumak isteyen kuruluşlar için oluşturulmuştur. NPS uzantısı, federal veya eşitlenmiş kullanıcılar için ikinci bir kimlik doğrulama faktörü sağlamak üzere RADIUS ve bulut tabanlı Azure MFA arasında bir bağdaştırıcı görevi görür.

Azure MFA için NPS uzantısını kullanırken, kimlik doğrulama akışı aşağıdaki bileşenleri içerir: 

1. **NAS/VPN sunucusu** , VPN istemcilerinden gelen istekleri alır ve NPS sunucularına RADIUS isteklerine dönüştürür. 
2. **NPS sunucusu** , RADIUS istekleri için birincil kimlik doğrulamasını gerçekleştirmek üzere Active Directory bağlanır ve başarılı olduğunda, isteği yüklü herhangi bir uzantıya geçirir.  
3. **NPS uzantısı** , ikincil kimlik doğrulaması IÇIN Azure MFA 'ya bir istek tetikler. Uzantı yanıtı aldıktan sonra ve MFA sınaması başarılı olursa, Azure STS tarafından verilen bir MFA talebi içeren güvenlik belirteçlerine sahip NPS sunucusunu sağlayarak kimlik doğrulama isteğini tamamlar.  
4. **Azure MFA** , kullanıcının ayrıntılarını almak için Azure Active Directory ile iletişim kurar ve kullanıcıya yapılandırılmış bir doğrulama yöntemi kullanarak ikincil kimlik doğrulamasını gerçekleştirir.

Aşağıdaki diyagramda bu üst düzey kimlik doğrulama isteği akışı gösterilmektedir: 

![Kimlik doğrulama akış diyagramı](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Dağıtımınızı planlayın

NPS uzantısı, artıklığı otomatik olarak işler, bu nedenle özel bir yapılandırmaya gerek kalmaz.

İhtiyaç duyduğunuz kadar çok sayıda Azure MFA özellikli NPS sunucusu oluşturabilirsiniz. Birden çok sunucu yüklüyorsanız, bunların her biri için fark istemci sertifikası kullanmanız gerekir. Her sunucu için bir sertifika oluşturmak, her sertifikayı tek tek güncelleştirebilmeniz ve tüm sunucularınızda kapalı kalma süresi konusunda endişelenmenize gerek kalmaz.

VPN sunucuları, kimlik doğrulama isteklerini yönlendirdiklerinden, yeni Azure MFA özellikli NPS sunucularının farkında olmaları gerekir.

## <a name="prerequisites"></a>Önkoşullar

NPS uzantısı, Mevcut altyapınızla birlikte çalışmak üzere tasarlanmıştır. Başlamadan önce aşağıdaki önkoşullara sahip olduğunuzdan emin olun.

### <a name="licenses"></a>Lisanslarının

Azure MFA için NPS uzantısı, [azure Multi-Factor Authentication lisanslarına](multi-factor-authentication.md) (Azure AD PREMIUM, EMS veya MFA tek başına lisansa dahildir) sahip müşteriler tarafından kullanılabilir. Azure MFA için Kullanıcı başına veya kimlik doğrulama lisansı başına tüketim tabanlı lisanslar, NPS uzantısıyla uyumlu değildir. 

### <a name="software"></a>Yazılım

Windows Server 2008 R2 SP1 veya üzeri.

### <a name="libraries"></a>Kitaplıklar

Bu kitaplıklar, uzantısıyla otomatik olarak yüklenir.

- [Visual Studio 2013 C++ için görsel yeniden dağıtılabilir paketler (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Windows PowerShell için Microsoft Azure Active Directory Modülü Version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Windows PowerShell için Microsoft Azure Active Directory Modülü, zaten mevcut değilse, kurulum sürecinin bir parçası olarak çalıştırdığınız bir yapılandırma betiği aracılığıyla yüklenir. Daha önce yüklenmemişse bu modülü yüklemeye gerek yoktur.

### <a name="azure-active-directory"></a>Azure Active Directory

NPS uzantısını kullanan herkesin Azure AD Connect kullanılarak Azure Active Directory eşitlenmesi ve MFA için kayıtlı olması gerekir.

Uzantıyı yüklediğinizde, Azure AD kiracınız için dizin KIMLIĞI ve yönetici kimlik bilgilerine sahip olmanız gerekir. Dizin KIMLIĞINIZI [Azure Portal](https://portal.azure.com)bulabilirsiniz. Yönetici olarak oturum açın, sol taraftaki **Azure Active Directory** simgesini seçin, sonra **Özellikler**' i seçin. **DIZIN kimliği** kutusuna GUID 'yi kopyalayın ve kaydedin. NPS uzantısını yüklerken bu GUID 'yi kiracı KIMLIĞI olarak kullanırsınız.

![Azure Active Directory özellikleri altında Dizin KIMLIĞINIZI bulun](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>Ağ gereksinimleri

NPS sunucusunun, 80 ve 443 bağlantı noktaları üzerinden aşağıdaki URL 'lerle iletişim kurabilmesi gerekir.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Ayrıca, [belirtilen PowerShell betiği kullanılarak bağdaştırıcının kurulumunu](#run-the-powershell-script) tamamlaması Için aşağıdaki URL 'lere bağlantı gerekir

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

NPS uzantısını yüklemeden önce, kimlik doğrulama trafiğini işleyecek ortamı hazırlamak istersiniz.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Etki alanına katılmış sunucuda NPS rolünü etkinleştirme

NPS sunucusu Azure Active Directory bağlanır ve MFA isteklerinin kimliğini doğrular. Bu rol için bir sunucu seçin. NPS uzantısı, RADIUS olmayan istekler için hata oluşturduğundan, diğer hizmetlerden gelen istekleri işleyemeyen bir sunucu seçmeyi öneririz. NPS sunucusu, ortamınız için birincil ve ikincil kimlik doğrulama sunucusu olarak ayarlanmalıdır; RADIUS isteklerini başka bir sunucuya ara sunucu olarak kullanamaz.

1. Sunucunuzda Sunucu Yöneticisi hızlı başlangıç menüsünde **rol ve Özellik Ekleme Sihirbazı** ' nı açın.
2. Yükleme türü için **rol tabanlı veya özellik tabanlı yükleme** ' yi seçin.
3. **Ağ İlkesi ve erişim Hizmetleri** sunucu rolünü seçin. Bu rolü çalıştırmak için gerekli özellikleri bilgilendirebilen bir pencere açılır.
4. Onay sayfasına kadar sihirbaza devam edin. **Yükle**’yi seçin.

Artık NPS için tasarlanmış bir sunucunuz olduğuna göre, bu sunucuyu VPN çözümünün gelen RADIUS isteklerini işleyecek şekilde de yapılandırmanız gerekir.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>VPN çözümünüzü NPS sunucusuyla iletişim kuracak şekilde yapılandırma

Kullandığınız VPN çözümüne bağlı olarak, RADIUS kimlik doğrulama ilkenizi yapılandırma adımları değişir. Bu ilkeyi, RADIUS NPS sunucunuza işaret etmek üzere yapılandırın.

### <a name="sync-domain-users-to-the-cloud"></a>Etki alanı kullanıcılarını buluta eşitleme

Bu adım kiracınızda zaten tamamlanmış olabilir, ancak Azure AD Connect veritabanlarınızı yakın zamanda eşitler.

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Azure Active Directory** > seçin **Azure AD Connect**
3. Eşitleme durumunuzu **etkinleştirildiğini** ve son eşitlemenin bir saatten daha önce olduğunu doğrulayın.

Eşitlemenin yeni bir hepsini açmanız gerekiyorsa [Azure AD Connect eşitleme: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)' daki yönergeler bizimle yapılır.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Kullanıcılarınızın hangi kimlik doğrulama yöntemlerini kullanabileceğini belirleme

NPS uzantısı dağıtımıyla hangi kimlik doğrulama yöntemlerinin kullanılabilir olduğunu etkileyen iki etken vardır:

1. RADIUS istemcisi (VPN, NetScaler sunucusu veya diğer) ile NPS sunucuları arasında kullanılan parola şifreleme algoritması.
   - **Pap** , BULUTTA Azure MFA 'nın tüm kimlik doğrulama yöntemlerini destekler: telefon araması, tek yönlü SMS mesajı, mobil uygulama BILDIRIMI, oath Donanım belirteçleri ve mobil uygulama doğrulama kodu.
   - **CHAPv2** ve **EAP** desteği telefon araması ve mobil uygulama bildirimi.

      > [!NOTE]
      > NPS uzantısını dağıtırken, kullanıcılarınız için hangi yöntemlerin kullanılabildiğini değerlendirmek üzere bu faktörleri kullanın. RADIUS istemciniz PAP 'yi destekliyorsa, ancak istemci UX ' in doğrulama kodu için giriş alanı yoksa, telefon araması ve mobil uygulama bildirimi desteklenen iki seçenekten oluşan bir uygulamadır.
      >
      > Ayrıca, VPN istemci UX 'niz giriş alanını destekliyorsa ve ağ erişim Ilkesini yapılandırdıysanız, kimlik doğrulaması başarılı olabilir, ancak ağ Ilkesinde yapılandırılan RADIUS özniteliklerinin hiçbiri ağ erişim cihazına uygulanmaz. RRAS sunucusu ya da VPN istemcisi gibi. Sonuç olarak, VPN istemcisinin, erişim olmadan istenen veya daha az erişimi olabilir.
      >

2. İstemci uygulamasının (VPN, NetScaler sunucusu veya diğer) işleyebileceği giriş yöntemleri. Örneğin, VPN istemcisinin, kullanıcının bir metin veya mobil uygulamadan bir doğrulama kodu yazmasıdır.

[Desteklenmeyen kimlik doğrulama yöntemlerini Azure 'da devre dışı](howto-mfa-mfasettings.md#verification-methods) bırakabilirsiniz.

### <a name="register-users-for-mfa"></a>MFA için kullanıcıları kaydetme

NPS uzantısını dağıtmadan ve kullanmadan önce, iki adımlı doğrulama gerçekleştirmek için gereken kullanıcıların MFA için kayıtlı olması gerekir. Daha sonra, uzantıyı dağıtırken Test etmek için, Multi-Factor Authentication için tam olarak kayıtlı olan en az bir test hesabınız olması gerekir.

Bir sınama hesabını kullanmaya başlamak için bu adımları kullanın:

1. Bir test hesabıyla [https://aka.ms/mfasetup](https://aka.ms/mfasetup) için oturum açın.
2. Bir doğrulama yöntemi ayarlamak için istemleri izleyin.
3. Test hesabı için çok faktörlü kimlik doğrulaması gerektiren [bir koşullu erişim Ilkesi oluşturun](howto-mfa-getstarted.md#create-conditional-access-policy) .

## <a name="install-the-nps-extension"></a>NPS uzantısını yükler

> [!IMPORTANT]
> NPS uzantısını VPN erişim noktasına göre farklı bir sunucuya yükler.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Azure MFA için NPS uzantısını indirme ve yükleme

1. [NPS uzantısını](https://aka.ms/npsmfa) Microsoft İndirme Merkezi ' nden indirin.
2. İkiliyi, yapılandırmak istediğiniz ağ Ilkesi sunucusuna kopyalayın.
3. *Setup. exe* ' yi çalıştırın ve yükleme yönergelerini izleyin. Hatalarla karşılaşırsanız, önkoşul bölümündeki iki kitaplığı başarıyla yüklediğinden emin olun.

#### <a name="upgrade-the-nps-extension"></a>NPS uzantısını yükseltme

Mevcut bir NPS uzantısı yüklemesini yükseltirken, temeldeki sunucunun yeniden başlatılmasını önlemek için aşağıdaki adımları izleyin:

1. Mevcut sürümü kaldır
1. Yeni yükleyiciyi Çalıştır
1. Ağ Ilkesi sunucusu (IAS) hizmetini yeniden başlatın

### <a name="run-the-powershell-script"></a>PowerShell betiğini çalıştırma

Yükleyici şu konumda bir PowerShell betiği oluşturuyor: `C:\Program Files\Microsoft\AzureMfa\Config` (burada C:\ yükleme sürücünüz). Bu PowerShell betiği her çalıştırılışında aşağıdaki eylemleri gerçekleştirir:

- Kendinden imzalı bir sertifika oluşturun.
- Sertifikanın ortak anahtarını Azure AD 'de hizmet sorumlusu ile ilişkilendirin.
- Sertifikayı yerel makine sertifika deposunda depolayın.
- Sertifikanın özel anahtarına ağ kullanıcısı için erişim izni verin.
- NPS 'YI yeniden başlatın.

Kendi sertifikalarınızı kullanmak istemiyorsanız (PowerShell betiğinin oluşturduğu otomatik olarak imzalanan sertifikalar yerine), yüklemeyi gerçekleştirmek için PowerShell betiğini çalıştırın. Uzantıyı birden çok sunucuya yüklerseniz, her birinin kendi sertifikası olmalıdır.

1. Windows PowerShell 'i yönetici olarak çalıştırın.
2. Dizinleri değiştirin.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Yükleyici tarafından oluşturulan PowerShell betiğini çalıştırın.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Azure AD 'de yönetici olarak oturum açın.
5. Kiracı KIMLIĞINIZ için PowerShell istemleri. Önkoşullar bölümündeki Azure portal kopyaladığınız dizin KIMLIĞI GUID 'sini kullanın.
6. Betik tamamlandığında PowerShell bir başarı iletisi gösterir.  

Yük Dengeleme için ayarlamak istediğiniz tüm ek NPS sunucuları üzerinde bu adımları yineleyin.

Önceki bilgisayar sertifikanızın süresi dolmuşsa ve yeni bir sertifika oluşturulduysa, süresi geçmiş tüm sertifikaları silmelisiniz. Süre dolma sertifikaları olması, NPS uzantısıyla başlayarak soruna neden olabilir.

> [!NOTE]
> PowerShell betiği ile sertifika oluşturmak yerine kendi sertifikalarınızı kullanırsanız, NPS adlandırma kuralına göre hizalandıklarından emin olun. Konu adı **CN =\<Tenantıd\>, OU = MICROSOFT NPS uzantısı**olmalıdır. 

### <a name="certificate-rollover"></a>Sertifika geçişi

NPS uzantısının Release 1.0.1.32 ile birden çok sertifika okumak artık desteklenmektedir. Bu yetenek, sertifika güncelleştirmelerinin süresi dolmadan önce işlenmesine yardımcı olur. Kuruluşunuz NPS uzantısının önceki bir sürümünü çalıştırıyorsa, sürüm 1.0.1.32 veya üstünü yükseltmeniz gerekir.

`AzureMfaNpsExtnConfigSetup.ps1` betiği tarafından oluşturulan sertifikalar 2 yıl için geçerlidir. BT kuruluşları, sertifikaları süre sonu için izlemelidir. NPS uzantısı için sertifikalar, kişisel ' in altındaki yerel bilgisayar sertifika deposuna yerleştirilir ve betiğe verilen kiracı KIMLIĞINE verilir.

Bir sertifika sona erme tarihine yaklaşıyorsa, değiştirmek için yeni bir sertifika oluşturulmalıdır.  Bu işlem, `AzureMfaNpsExtnConfigSetup.ps1` yeniden çalıştırılarak ve istendiğinde aynı kiracı KIMLIĞINI koruyarak gerçekleştirilir. Bu işlem, ortamınızdaki her NPS sunucusunda tekrarlanmış olmalıdır.

## <a name="configure-your-nps-extension"></a>NPS uzantınızı yapılandırma

Bu bölüm, başarılı bir NPS uzantısı dağıtımı için tasarım konuları ve öneriler içerir.

### <a name="configuration-limitations"></a>Yapılandırma sınırlamaları

- Azure MFA için NPS uzantısı, kullanıcıları ve ayarları MFA sunucusundan buluta geçirmeye yönelik araçlar içermez. Bu nedenle, mevcut dağıtım yerine yeni dağıtımlar için uzantıyı kullanmanızı öneririz. Uzantıyı var olan bir dağıtımda kullanıyorsanız, kullanıcılarınızın MFA ayrıntılarını bulutta doldurmak için yeniden prova yapması gerekir.  
- NPS uzantısı, Azure MFA 'da Ikincil kimlik doğrulamasını gerçekleştirmek için kullanıcıyı tanımlamak üzere şirket içi Active Directory 'den UPN 'yi kullanır. Uzantı, alternatif oturum açma KIMLIĞI gibi farklı bir tanıtıcıyı veya UPN dışında özel Active Directory alanı kullanacak şekilde yapılandırılabilir. Daha fazla bilgi için, [Multi-Factor Authentication IÇIN NPS uzantısının gelişmiş yapılandırma seçenekleri](howto-mfa-nps-extension-advanced.md)makalesine bakın.
- Tüm şifreleme protokolleri tüm doğrulama yöntemlerini desteklemez.
   - **Pap** telefon aramasını, tek yönlü SMS iletisini, mobil uygulama bildirimini ve mobil uygulama doğrulama kodunu destekler
   - **CHAPv2** ve **EAP** desteği telefon araması ve mobil uygulama bildirimi

### <a name="control-radius-clients-that-require-mfa"></a>MFA gerektiren RADIUS istemcilerini denetleme

NPS uzantısını kullanarak bir RADIUS istemcisi için MFA 'yı etkinleştirdikten sonra, bu istemciye yönelik tüm kimlik doğrulamaları MFA gerçekleştirmek için gereklidir. Bazı RADIUS istemcileri için MFA 'yı etkinleştirmek istiyorsanız, diğer bir deyişle, iki NPS sunucusunu yapılandırabilir ve uzantıyı yalnızca birine yükleyebilirsiniz. MFA ile yapılandırılan NPS sunucusuna istek göndermesini ve diğer RADIUS istemcilerini uzantı ile yapılandırılmamış NPS sunucusuna göndermesini gerektirmek istediğiniz RADIUS istemcilerini yapılandırın.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA için kayıtlı olmayan kullanıcılara hazırlanma

MFA için kayıtlı olmayan kullanıcılarınız varsa, kimlik doğrulamaya çalıştıklarında ne olacağını belirleyebilirsiniz. Özellik davranışını denetlemek için kayıt defteri yolu *HKLM\Software\Microsoft\AzureMFA* içindeki *REQUIRE_USER_MATCH* kayıt defteri ayarını kullanın. Bu ayarın tek bir yapılandırma seçeneği vardır:

| Anahtar | Değer | Varsayılan |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | DOĞRU/YANLıŞ | Ayarlanmadı (TRUE değerine denktir) |

Bu ayarın amacı, bir Kullanıcı MFA 'ya kaydolmadıysa ne yapılacağını belirlemektir. Anahtar mevcut olmadığında, ayarlanmamışsa veya TRUE olarak ayarlandığında ve Kullanıcı kayıtlı değilse, uzantı MFA sınamasını başarısız olur. Anahtar yanlış olarak ayarlandığında ve Kullanıcı kayıtlı değilse, kimlik doğrulaması MFA yapılmadan devam eder. Bir Kullanıcı MFA 'ya kaydedildiyse, REQUIRE_USER_MATCH yanlış olarak ayarlanmış olsa bile MFA ile kimlik doğrulaması yapılmalıdır.

Kullanıcılarınız eklenirken bu anahtarı oluşturmayı ve yanlış olarak ayarlamayı tercih edebilirsiniz; ancak bunların hepsi Azure MFA için henüz kaydedilmeyebilir. Bununla birlikte, anahtarı ayarlamak MFA 'ya kayıtlı olmayan kullanıcıların oturum açmasına izin verdiğinden, üretime geçmeden önce bu anahtarı kaldırmanız gerekir.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="nps-extension-health-check-script"></a>NPS uzantısı durum denetimi betiği

NPS uzantısında sorun giderirken temel sistem durumu denetimi adımlarını gerçekleştirmek üzere TechNet galerisinde aşağıdaki komut dosyası kullanılabilir.

[MFA_NPS_Troubleshooter. ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Nasıl yaparım? istemci sertifikası 'nın beklendiği şekilde yüklendiğini doğrulayın.

Sertifika deposunda yükleyici tarafından oluşturulan kendinden imzalı sertifikayı bulun ve özel anahtarın Kullanıcı **ağ hizmeti**'ne verilmiş izinlere sahip olup olmadığını denetleyin. Sertifika, **CN \<tenantıd\>, OU = MICROSOFT NPS uzantısı** olan bir konu adına sahiptir

*AzureMfaNpsExtnConfigSetup. ps1* betiği tarafından oluşturulan otomatik olarak imzalanan sertifikaların Ayrıca iki yıla ait geçerlilik ömrü de vardır. Sertifikanın yüklendiği doğrulanırken, sertifikanın sona ermemiş olduğunu da denetlemeniz gerekir.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>İstemci serimin Azure Active Directory kiracım ile ilişkili olduğunu nasıl doğrulayabilirim?

PowerShell komut istemi ' ni açın ve aşağıdaki komutları çalıştırın:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Bu komutlar, kiracınızı, PowerShell oturumunuzda NPS uzantısı örneğinizle ilişkilendiren tüm sertifikaları yazdırır. İstemci sertifikanızı özel anahtar olmadan bir "Base-64 kodlamalı X. 509.440 (. cer)" dosyası olarak dışarı aktararak ve PowerShell 'deki listeyle karşılaştırarak sertifikanızın olup olmadığına bakın.

Aşağıdaki komut, "C:" sürücünüzde. cer biçiminde "npscercertificate" adlı bir dosya oluşturur.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Bu komutu çalıştırdığınızda C sürücünüze gidin, dosyayı bulun ve üzerine çift tıklayın. Ayrıntılar ' a gidin ve "parmak izi" ' ne gidin, sunucuda yüklü sertifikanın parmak izini bu bir ile karşılaştırın. Sertifika parmak izleri eşleşmelidir.

Geçerli-ve geçerli-Until zaman damgalarına, bir komut birden fazla sertifika döndürürse açık yanlış sığacak şekilde filtre uygulamak için kullanılabilir.

---

### <a name="why-cant-i-sign-in"></a>Neden oturum açamıyorum?

Parolanızın süresinin dolmadığından emin olun. NPS uzantısı, oturum açma iş akışının bir parçası olarak parolaların değiştirilmesini desteklemez. Daha fazla yardım için kuruluşunuzun BT ekibine başvurun.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>İsteklerim neden ADAL belirteci hatasıyla başarısız oluyor?

Bu hata, birkaç nedenden biri nedeniyle olabilir. Sorun gidermeye yardımcı olması için aşağıdaki adımları kullanın:

1. NPS sunucunuzu yeniden başlatın.
2. İstemci sertifikası 'nın beklendiği gibi yüklendiğini doğrulayın.
3. Sertifikanın Azure AD 'deki kiracınızla ilişkili olduğunu doğrulayın.
4. Uzantıyı çalıştıran sunucudan https://login.microsoftonline.com/ adresine erişilebildiğini doğrulayın.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Kimlik doğrulaması, kullanıcının bulunamadığını bildiren HTTP günlüklerinde hata vererek başarısız olur mu?

AD Connect 'in çalıştığını ve kullanıcının hem Windows Active Directory hem de Azure Active Directory bulunduğundan emin olun.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Tüm kimlik doğrulamalarımı olan günlüklerde neden HTTP Connect hataları görüyorum?

NPS uzantısını çalıştıran sunucudan https://adnotifications.windowsazure.com adresine ulaşılabildiğini doğrulayın.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Geçerli bir sertifika mevcut olmasına rağmen neden kimlik doğrulaması çalışmıyor?

Önceki bilgisayar sertifikanızın süresi dolmuşsa ve yeni bir sertifika oluşturulduysa, süresi geçmiş tüm sertifikaları silmelisiniz. Süre dolma sertifikaları olması, NPS uzantısıyla başlayarak soruna neden olabilir.

Geçerli bir sertifikanız olup olmadığını denetlemek için, MMC kullanarak yerel bilgisayar hesabının sertifika depolama alanını denetleyin ve sertifikanın süre sonu tarihini geçirmediğinden emin olun. Yeni geçerli bir sertifika oluşturmak için, "[PowerShell betiğini Çalıştır](#run-the-powershell-script)" bölümünde açıklanan adımları yeniden çalıştırın.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL Protokollerini ve Şifre Paketlerini yönetme

Kuruluşunuz tarafından gerektirmedikleri takdirde, daha eski ve daha zayıf şifre paketlerinin devre dışı bırakılması veya kaldırılması önerilir. Bu görevin nasıl gerçekleştirileceği hakkında bilgiler [AD FS için SSL/TLS Protokollerini ve Şifre Paketlerini Yönetme](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) makalesine bakın

### <a name="additional-troubleshooting"></a>Ek sorun giderme

Ek sorun giderme kılavuzu ve olası çözümler, [Azure MULTI-Factor AUTHENTICATION NPS uzantısından hata Iletilerini çözümleme](howto-mfa-nps-extension-errors.md)makalesinde bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Oturum açmak için alternatif kimlikleri yapılandırın veya [Multi-Factor Authentication IÇIN NPS uzantısının gelişmiş yapılandırma seçeneklerinde](howto-mfa-nps-extension-advanced.md) iki adımlı doğrulama gerçekleştirmemelidir.

- NPS uzantısını kullanarak [Uzak Masaüstü Ağ Geçidi](howto-mfa-nps-extension-rdg.md) ve [VPN sunucularını](howto-mfa-nps-extension-vpn.md) tümleştirmeyi öğrenin

- [Azure Multi-Factor Authentication için NPS uzantısından alınan hata iletilerini çözme](howto-mfa-nps-extension-errors.md)
