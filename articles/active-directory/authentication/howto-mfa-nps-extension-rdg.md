---
title: RDG 'YI Azure AD MFA NPS uzantısıyla tümleştirme-Azure Active Directory
description: Microsoft Azure için ağ Ilkesi sunucusu uzantısını kullanarak Uzak Masaüstü Ağ Geçidi altyapınızı Azure AD MFA ile tümleştirin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f50792ec45570f7e90893a97150ea26b63ebf9c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829845"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Ağ Ilkesi sunucusu (NPS) uzantısını ve Azure AD 'yi kullanarak Uzak Masaüstü Ağ Geçidi altyapınızı tümleştirin

Bu makalede, Microsoft Azure için ağ Ilkesi sunucusu (NPS) uzantısını kullanarak Uzak Masaüstü Ağ Geçidi altyapınızı Azure AD Multi-Factor Authentication (MFA) ile tümleştirmeyle ilgili ayrıntılar sağlanmaktadır.

Azure için ağ Ilkesi sunucusu (NPS) uzantısı, müşterilerin Azure 'un bulut tabanlı [Multi-Factor Authentication (MFA)](./concept-mfa-howitworks.md)kullanarak arayan kullanıcının uzaktan KIMLIĞINI doğrulama HIZMETI (RADIUS) istemci kimlik doğrulamasını korumanıza olanak tanır. Bu çözüm, Kullanıcı oturum açma ve işlemlerine ikinci bir güvenlik katmanı eklemek için iki aşamalı doğrulama sağlar.

Bu makalede, Azure için NPS uzantısı kullanılarak Azure AD MFA ile NPS altyapısını tümleştirmek için adım adım yönergeler sağlanmaktadır. Bu, bir Uzak Masaüstü Ağ Geçidi oturum açmaya çalışan kullanıcılar için güvenli doğrulama sağlar.

> [!NOTE]
> Bu makale MFA sunucu dağıtımlarıyla kullanılmamalıdır ve yalnızca Azure AD MFA (bulut tabanlı) dağıtımlarıyla birlikte kullanılmalıdır.

Ağ Ilkesi ve erişim Hizmetleri (NPS), kuruluşlara aşağıdakileri yapma olanağı sağlar:

* Kimlerin bağlanabileceklerini, hangi gün bağlantılarına izin verileceğini, bağlantı süresini ve istemcilerin bağlanmak için kullanması gereken güvenlik düzeyini belirterek ağ isteklerinin yönetimi ve denetimi için Merkezi konumlar tanımlayın. Her VPN veya uzak masaüstü (RD) Ağ Geçidi sunucusunda bu ilkeleri belirtmek yerine, bu ilkeler merkezi bir konumda bir kez belirtilebilir. RADIUS Protokolü merkezi kimlik doğrulaması, yetkilendirme ve hesaplama (AAA) sağlar.
* Cihazların ağ kaynaklarına kısıtlanmamış veya kısıtlı erişim verilip verilmediğini belirlemek için ağ erişim koruması (NAP) istemci sistem durumu ilkeleri oluşturun ve uygulayın.
* 802.1 x özellikli kablosuz erişim noktalarına ve Ethernet anahtarlarına erişim için kimlik doğrulama ve yetkilendirmeyi zorlamak için bir yol belirtin.

Genellikle kuruluşlar VPN ilkelerinin yönetimini basitleştirmek ve merkezileştirmek için NPS (RADIUS) kullanır. Ancak birçok kuruluş, RD Masaüstü bağlantısı yetkilendirme Ilkelerinin (RD CAP 'ler) yönetimini basitleştirmek ve merkezileştirmek için NPS 'YI de kullanır.

Kuruluşlar, güvenliği artırmak ve yüksek düzeyde uyumluluk sağlamak için Azure AD MFA ile NPS 'yi tümleştirebilir. Bu, kullanıcıların Uzak Masaüstü Ağ Geçidi oturum açmak için iki aşamalı doğrulama kurmasını sağlamaya yardımcı olur. Kullanıcılara erişim izni verilmesi için, kullanıcının denetiminde sahip olduğu bilgilerle birlikte Kullanıcı adı/parola birleşimini sağlamaları gerekir. Bu bilgiler, bir cep telefonu numarası, yer çizgisi numarası, bir mobil cihazda uygulama vb. gibi güvenilir bir şekilde çoğaltılmamalıdır ve kolayca yinelenmemelidir. RDG Şu anda 2FA için Microsoft Authenticator uygulama yöntemlerinden telefon aramasını ve anında iletme bildirimlerini desteklemektedir. Desteklenen kimlik doğrulama yöntemleri hakkında daha fazla bilgi için [kullanıcılarınızın hangi kimlik doğrulama yöntemlerini kullanabileceğini belirleme](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)bölümüne bakın.

Azure için NPS uzantısının kullanılabilirliğine başlamadan önce, tümleşik NPS ve Azure AD MFA ortamları için iki aşamalı doğrulama uygulamayı kullanan müşterilerin, [Uzak Masaüstü Ağ Geçidi ve azure MULTI-Factor AUTHENTICATION sunucusu RADIUS kullanarak,](howto-mfaserver-nps-rdg.md)şirket içi ortamda ayrı bir MFA sunucusunu yapılandırması ve koruması gerekiyordu.

Azure için NPS uzantısının kullanılabilirliği artık kuruluşlara, güvenli RADIUS istemci kimlik doğrulamasına yönelik bir şirket içi tabanlı MFA çözümü ya da bulut tabanlı MFA çözümü dağıtma seçeneği sunar.

## <a name="authentication-flow"></a>Kimlik Doğrulama Akışı

Kullanıcılara bir Uzak Masaüstü Ağ Geçidi aracılığıyla ağ kaynaklarına erişim izni verilmesi için, tek bir RD Bağlantı Yetkilendirme Ilkesinde (RD CAP) ve tek bir RD kaynak yetkilendirme Ilkesinde (RD RAP) belirtilen koşulları karşılaması gerekir. RD CAP 'leri, RD ağ geçitlerine bağlanma yetkisi olan kişileri belirtin. RD RAP 'Ler, uzak masaüstleri veya uzak uygulamalar gibi, kullanıcının RD Ağ Geçidi üzerinden bağlanmasına izin verilen ağ kaynaklarını belirtir.

Bir RD Ağ Geçidi, RD CAP 'leri için merkezi bir ilke deposu kullanacak şekilde yapılandırılabilir. RD Ağ Geçidi işlendiği için RD RAP 'Ler merkezi bir ilke kullanamaz. RD CAP 'ler için merkezi bir ilke deposu kullanmak üzere yapılandırılmış bir RD Ağ Geçidi örneği, Merkezi ilke deposu olarak hizmet veren başka bir NPS sunucusuna RADIUS istemcörnektir.

Azure için NPS Uzantısı NPS ve Uzak Masaüstü Ağ Geçidi tümleştirildiğinde, başarılı kimlik doğrulama akışı şu şekildedir:

1. Uzak Masaüstü Ağ Geçidi sunucusu Uzak Masaüstü kullanıcıdan uzak masaüstü oturumu gibi bir kaynağa bağlanmak için bir kimlik doğrulama isteği alır. Bir RADIUS istemcisi olarak davranan Uzak Masaüstü Ağ Geçidi sunucusu, isteği bir RADIUS Access-Request iletisine dönüştürür ve iletiyi NPS uzantısının yüklendiği RADIUS (NPS) sunucusuna gönderir.
1. Kullanıcı adı ve parola birleşimi Active Directory doğrulanır ve kullanıcının kimliği doğrulanır.
1. NPS bağlantı Isteğinde belirtilen tüm koşullar ve ağ Ilkeleri karşılanıyorsa (örneğin, günün saati veya grup üyeliği kısıtlamaları), NPS uzantısı, Azure AD MFA ile ikincil kimlik doğrulama isteğini tetikler.
1. Azure AD MFA, Azure AD ile iletişim kurar, kullanıcının ayrıntılarını alır ve desteklenen yöntemleri kullanarak ikincil kimlik doğrulamasını gerçekleştirir.
1. MFA Challenge başarısından sonra Azure AD MFA, sonucu NPS Uzantısı ile iletişim kurar.
1. Uzantının yüklendiği NPS sunucusu, Uzak Masaüstü Ağ Geçidi sunucusuna RD CAP ilkesi için bir RADIUS Access-Accept iletisi gönderir.
1. Kullanıcıya, RD Ağ Geçidi aracılığıyla istenen ağ kaynağına erişim verilir.

## <a name="prerequisites"></a>Önkoşullar

Bu bölümde Azure AD MFA 'yı Uzak Masaüstü Ağ Geçidi ile tümleştirmadan önce gerekli Önkoşullar ayrıntılı olarak yer Başlamadan önce, aşağıdaki önkoşulların yerine gelmelidir.  

* Uzak Masaüstü Hizmetleri (RDS) altyapısı
* Azure AD MFA lisansı
* Windows Server yazılımı
* Ağ Ilkesi ve erişim Hizmetleri (NPS) rolü
* Azure Active Directory Şirket içi Active Directory eşitleniyor
* Azure Active Directory GUID KIMLIĞI

### <a name="remote-desktop-services-rds-infrastructure"></a>Uzak Masaüstü Hizmetleri (RDS) altyapısı

Yerinde çalışan bir Uzak Masaüstü Hizmetleri (RDS) altyapısına sahip olmanız gerekir. Bunu yapmazsanız, aşağıdaki hızlı başlangıç şablonunu kullanarak bu altyapıyı Azure 'da hızlıca oluşturabilirsiniz: [Uzak Masaüstü oturumu koleksiyonu dağıtımı oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Sınama amacıyla el ile şirket içi bir RDS altyapısını hızlı bir şekilde oluşturmak isterseniz, bir tane dağıtmak için adımları izleyin.
**Daha fazla bilgi edinin**: [RDS 'yi Azure hızlı başlangıç](/windows-server/remote/remote-desktop-services/rds-in-azure) ve [temel RDS altyapı dağıtımı](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)ile dağıtın.

### <a name="azure-ad-mfa-license"></a>Azure AD MFA lisansı

Gerekli olan Azure AD MFA lisanslayabileceği, Azure AD Premium veya dahil olan diğer paketlerde kullanılabilir. Azure AD MFA için Kullanıcı başına veya kimlik doğrulama lisansı başına tüketim tabanlı lisanslar, NPS uzantısıyla uyumlu değildir. Daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication alma](concept-mfa-licensing.md). Sınama amacıyla bir deneme aboneliği kullanabilirsiniz.

### <a name="windows-server-software"></a>Windows Server yazılımı

NPS uzantısı, NPS rol hizmetinin yüklü olduğu Windows Server 2008 R2 SP1 veya sonraki bir sürümünü gerektirir. Bu bölümdeki adımların tümü Windows Server 2016 kullanılarak gerçekleştirildi.

### <a name="network-policy-and-access-services-nps-role"></a>Ağ Ilkesi ve erişim Hizmetleri (NPS) rolü

NPS rol hizmeti, RADIUS sunucusu ve istemci işlevlerinin yanı sıra ağ erişim Ilkesi sistem durumu hizmeti sağlar. Bu rol, altyapınızdaki en az iki bilgisayara yüklenmelidir: Uzak Masaüstü Ağ Geçidi ve başka bir üye sunucu veya etki alanı denetleyicisi. Varsayılan olarak, rol Uzak Masaüstü Ağ Geçidi olarak yapılandırılmış bilgisayarda zaten mevcuttur.  NPS rolünü, etki alanı denetleyicisi veya üye sunucu gibi başka bir bilgisayara en azından de yüklemelisiniz.

Windows Server 2012 veya daha eski bir NPS rol hizmetini yükleme hakkında bilgi için bkz. [NAP sistem durumu Ilkesi sunucusu yükleme](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). NPS 'nin bir etki alanı denetleyicisine yüklenmesi önerisi dahil olmak üzere en iyi NPS yöntemlerinin açıklaması için bkz. [NPS Için En Iyi uygulamalar](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory Şirket içi Active Directory eşitleniyor

NPS uzantısını kullanmak için şirket içi kullanıcıların Azure AD ile eşitlenmesi ve MFA için etkinleştirilmesi gerekir. Bu bölümde, şirket içi kullanıcıların AD Connect kullanılarak Azure AD ile eşitleniyor olduğu varsayılır. Azure AD Connect hakkında daha fazla bilgi için bkz. Şirket [içi dizinlerinizi Azure Active Directory tümleştirme](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID KIMLIĞI

NPS uzantısını yüklemek için Azure AD 'nin GUID 'sini bilmeniz gerekir. Azure AD 'nin GUID 'sini bulmaya yönelik yönergeler aşağıda verilmiştir.

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication Yapılandır

Bu bölümde Azure AD MFA 'yı Uzak Masaüstü Ağ Geçidi ile tümleştirmek için yönergeler sağlanmaktadır. Yönetici olarak, kullanıcıların Multi-Factor cihazlarını veya uygulamalarını kendi kendine kaydedebilmeleri için önce Azure AD MFA hizmetini yapılandırmanız gerekir.

Azure AD kullanıcılarınız için MFA 'yı etkinleştirmek üzere [Bulutta Azure ad Multi-Factor Authentication kullanmaya](howto-mfa-getstarted.md) başlama adımlarını izleyin.

### <a name="configure-accounts-for-two-step-verification"></a>İki adımlı doğrulama için hesapları yapılandırma

Bir hesap MFA için etkinleştirildikten sonra, ikinci kimlik doğrulama faktörü için kullanılmak üzere bir güvenilen cihaz başarıyla yapılandırılana ve iki adımlı doğrulama kullanarak kimlik doğrulamasından çıkana kadar MFA ilkesi tarafından yönetilen kaynaklarda oturum açılamıyor.

[Azure AD Multi-Factor Authentication benim Için ne anlama geldiğini](../user-help/multi-factor-authentication-end-user-first-time.md) öğrenmek için bu adımları izleyin.

> [!IMPORTANT]
> Uzak Masaüstü Ağ Geçidi için oturum açma davranışı, Azure AD Multi-Factor Authentication bir doğrulama kodu girme seçeneği sağlamaz. Telefon doğrulaması için bir kullanıcı hesabı veya anında iletme bildirimleri olan Microsoft Authenticator uygulama için yapılandırılmış olmalıdır.
>
> Bir kullanıcı için telefon doğrulaması veya anında iletme bildirimleri olan Microsoft Authenticator uygulama yapılandırılmamışsa, Kullanıcı Azure AD Multi-Factor Authentication sınamasını tamamlayıp Uzak Masaüstü Ağ Geçidi oturum açamaz.
>
> SMS metin yöntemi, bir doğrulama kodu girme seçeneğini sağlamadığından Uzak Masaüstü Ağ Geçidi birlikte çalışmıyor.

## <a name="install-and-configure-nps-extension"></a>NPS uzantısını yükleyip yapılandırma

Bu bölümde, Uzak Masaüstü Ağ Geçidi ile istemci kimlik doğrulaması için Azure AD MFA 'yı kullanmak üzere RDS altyapısını yapılandırma yönergeleri sağlanmaktadır.

### <a name="acquire-azure-active-directory-tenant-id"></a>Azure Active Directory kiracı KIMLIĞI al

NPS uzantısının yapılandırmasının bir parçası olarak, Azure AD kiracınız için yönetici kimlik bilgilerini ve Azure AD KIMLIĞINI sağlamanız gerekir. Kiracı KIMLIĞINI almak için aşağıdaki adımları izleyin:

1. Azure kiracının genel yöneticisi olarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Azure portal menüsünde **Azure Active Directory**' i seçin veya herhangi bir sayfadan **Azure Active Directory** arayın ve seçin.
1. **Genel bakış** sayfasında, *kiracı bilgileri* gösterilir. *KIRACı kimliği*' nin yanında, aşağıdaki örnekte gösterildiği gibi **Kopyala** simgesini seçin:

   ![Azure portal Kiracı KIMLIĞINI alma](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>NPS uzantısını yükler

NPS uzantısını, ağ Ilkesi ve erişim Hizmetleri (NPS) rolünün yüklü olduğu bir sunucuya yükler. Bu, tasarımınızın RADIUS sunucusu olarak işlev görür.

> [!IMPORTANT]
> NPS uzantısını Uzak Masaüstü Ağ Geçidi (RDG) sunucunuza yüklemeyin. RDG sunucusu RADIUS protokolünü istemcisiyle kullanmaz, bu nedenle uzantı MFA 'yı yorumlayamaz ve gerçekleştiremez.
>
> RDG sunucusu ve NPS Uzantısı ile NPS sunucusu farklı sunuculardır, RDG diğer NPS sunucularıyla iletişim kurmak için NPS 'YI kullanır ve doğru iletişim kurmak için RADIUS kullanır.

1. [NPS uzantısını](https://aka.ms/npsmfa)indirin.
1. Kurulum yürütülebilir dosyasını (NpsExtnForAzureMfaInstaller.exe) NPS sunucusuna kopyalayın.
1. NPS sunucusunda **NpsExtnForAzureMfaInstaller.exe**' ye çift tıklayın. İstenirse, **Çalıştır**' a tıklayın.
1. Azure AD MFA kurulumu Için NPS uzantısı iletişim kutusunda, yazılım lisans koşullarını gözden geçirin, **Lisans hüküm ve koşullarını kabul** ediyorum ' u Işaretleyin ve **yükleme**' ye tıklayın.
1. Azure AD MFA kurulumu Için NPS uzantısı iletişim kutusunda **Kapat**' a tıklayın.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>NPS Uzantısı ile kullanılacak sertifikaları bir PowerShell betiği kullanarak yapılandırma

Daha sonra, güvenli iletişim ve güvence sağlamak için NPS uzantısı tarafından kullanılmak üzere sertifikaları yapılandırmanız gerekir. NPS bileşenleri, NPS ile kullanılmak üzere otomatik olarak imzalanan bir sertifika yapılandıran bir Windows PowerShell betiği içerir.

Betik aşağıdaki eylemleri gerçekleştirir:

* Kendinden imzalı bir sertifika oluşturur
* Azure AD 'de sertifikanın ortak anahtarını hizmet sorumlusu ile ilişkilendirir
* Sertifikayı yerel makine deposunda depolar
* Sertifikanın özel anahtarına ağ kullanıcısı için erişim izni verir
* Ağ Ilkesi sunucusu hizmetini yeniden başlatır

Kendi sertifikalarınızı kullanmak istiyorsanız, sertifikanızın ortak anahtarını Azure AD 'deki hizmet sorumlusu ile ilişkilendirmeniz ve bu şekilde devam etmeniz gerekir.

Betiği kullanmak için, uzantıyı Azure AD yönetici kimlik bilgilerinizle ve daha önce kopyaladığınız Azure AD kiracı KIMLIĞIYLE birlikte belirtin. Betiği, NPS uzantısını yüklediğiniz her NPS sunucusunda çalıştırın. Ardından şunları yapın:

1. Bir yönetim Windows PowerShell istemi açın.
1. PowerShell komut isteminde yazın `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` ve **ENTER** tuşuna basın.
1. Yazın `.\AzureMfaNpsExtnConfigSetup.ps1` ve **ENTER** tuşuna basın. Betik, Azure Active Directory PowerShell modülünün yüklü olup olmadığını denetler. Yüklü değilse, komut dosyası modülü sizin için yüklerse.

   ![Azure AD PowerShell 'de AzureMfaNpsExtnConfigSetup.ps1 çalıştırma](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Betik, PowerShell modülünün yüklemesini doğruladıktan sonra, Azure Active Directory PowerShell modülü iletişim kutusunu görüntüler. İletişim kutusunda Azure AD yönetici kimlik bilgilerinizi ve parolanızı girip **oturum aç**' a tıklayın.

   ![PowerShell 'de Azure AD kimlik doğrulaması](./media/howto-mfa-nps-extension-rdg/image5.png)

1. İstendiğinde, pano 'ya kopyaladığınız *KIRACı kimliğini* daha önce yapıştırın ve **ENTER** tuşuna basın.

   ![Kiracı KIMLIĞINI PowerShell 'e yerleştirme](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Betik otomatik olarak imzalanan bir sertifika oluşturur ve diğer yapılandırma değişikliklerini gerçekleştirir. Çıktı aşağıda gösterilen görüntü gibi olmalıdır.

   ![Otomatik olarak imzalanan sertifikayı gösteren PowerShell çıkışı](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Uzak Masaüstü Ağ Geçidi üzerinde NPS bileşenlerini yapılandırma

Bu bölümde, Uzak Masaüstü Ağ Geçidi bağlantı yetkilendirme ilkelerini ve diğer RADIUS ayarlarını yapılandırırsınız.

Kimlik doğrulama akışı, Uzak Masaüstü Ağ Geçidi ile NPS uzantısının yüklendiği NPS sunucusu arasında RADIUS iletilerinin değiştirilmesini gerektirir. Bu, hem Uzak Masaüstü Ağ Geçidi hem de NPS uzantısının yüklendiği NPS sunucusunda RADIUS istemci ayarlarını yapılandırmanız gerektiği anlamına gelir.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Merkezi depoyu kullanmak için Uzak Masaüstü Ağ Geçidi bağlantı yetkilendirme ilkelerini yapılandırma

Uzak Masaüstü bağlantı yetkilendirme ilkeleri (RD CAP 'ler) bir Uzak Masaüstü Ağ Geçidi sunucusuna bağlanmak için gereken gereksinimleri belirtir. RD CAP 'ler yerel olarak depolanabilir (varsayılan) veya NPS çalıştıran merkezi bir RD CAP deposunda depolanabilir. Azure AD MFA 'yı RDS ile tümleştirmeyi yapılandırmak için merkezi bir deponun kullanımını belirtmeniz gerekir.

1. RD Ağ Geçidi sunucusunda, **Sunucu Yöneticisi** açın.
1. Menüsünde **Araçlar**' a tıklayın, **Uzak Masaüstü Hizmetleri**' nin üzerine gelin ve ardından **Uzak Masaüstü Ağ Geçidi Yöneticisi**' e tıklayın.
1. RD Ağ Geçidi Yöneticisi, **\[ sunucu adı \] (yerel)**' e sağ tıklayın ve **Özellikler**' e tıklayın.
1. Özellikler iletişim kutusunda **RD CAP deposu** sekmesini seçin.
1. RD CAP Deposu sekmesinde **NPS çalıştıran merkezi sunucu**' yı seçin. 
1. **NPS çalıştıran sunucu için bir ad veya IP adresi girin** alanına NPS uzantısını YÜKLEDIĞINIZ sunucunun IP adresini veya sunucu adını yazın.

   ![NPS sunucunuzun adını veya IP adresini girin](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. **Ekle**'ye tıklayın.
1. **Paylaşılan gizlilik** iletişim kutusunda paylaşılan bir gizlilik girin ve ardından **Tamam**' a tıklayın. Bu paylaşılan gizliliği kaydedip kaydı güvenli bir şekilde depoladığınızdan emin olun.

   >[!NOTE]
   >Paylaşılan gizlilik, RADIUS sunucuları ve istemciler arasında güven oluşturmak için kullanılır. Uzun ve karmaşık bir parola oluşturun.
   >

   ![Güven sağlamak için paylaşılan gizlilik oluşturma](./media/howto-mfa-nps-extension-rdg/image11.png)

1. **Tamam**’a tıklayarak iletişim kutusunu kapatın.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>NPS Uzak Masaüstü Ağ Geçidi RADIUS zaman aşımı değerini yapılandırma

Kullanıcıların kimlik bilgilerini doğrulama, iki aşamalı doğrulama gerçekleştirme, yanıtları alma ve RADIUS iletilerine yanıt verme zamanlarından emin olmak için, RADIUS zaman aşımı değerini ayarlamak gereklidir.

1. RD Ağ Geçidi sunucusunda, Sunucu Yöneticisi açın. Menüsünde **Araçlar**' a ve ardından **ağ ilkesi sunucusu**' na tıklayın.
1. **NPS (yerel)** konsolunda, **RADIUS istemcileri ve sunucuları**' nı genişletin ve **uzak RADIUS sunucusu**' nu seçin.

   ![Uzak RADIUS sunucusunu gösteren ağ Ilkesi sunucusu yönetim konsolu](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Ayrıntılar bölmesinde **TS Ağ GEÇIDI sunucu grubu**' na çift tıklayın.

   >[!NOTE]
   >Bu RADIUS sunucu grubu, NPS ilkeleri için merkezi sunucuyu yapılandırdığınızda oluşturulmuştur. RD Ağ Geçidi, Grup içinde birden fazla varsa, RADIUS iletilerini bu sunucuya veya sunucu grubuna iletir.
   >

1. **TS Ağ GEÇIDI sunucu grubu özellikleri** iletişim kutusunda, RD CAP 'leri depolamak IÇIN yapılandırdığınız NPS sunucusunun IP adresini veya adını seçin ve ardından **Düzenle**' ye tıklayın.

   ![Daha önce yapılandırılan NPS sunucusunun IP veya adını seçin](./media/howto-mfa-nps-extension-rdg/image13.png)

1. **RADIUS sunucusunu Düzenle** Iletişim kutusunda **Yük Dengeleme** sekmesini seçin.
1. **Yük Dengeleme** sekmesinde, **istek bırakılmış olarak kabul edilmeden önce yanıt olmadan saniye sayısı** alanına, varsayılan değeri 3 ' ten 30 ile 60 saniye arasında bir değere değiştirin.
1. **Sunucu kullanılamaz olarak tanımlandığında istekler arasındaki saniye sayısı** alanında, 30 saniyelik varsayılan değeri, önceki adımda belirttiğiniz değere eşit veya ondan daha büyük bir değere değiştirin.

   ![Yük Dengeleme sekmesinde RADIUS sunucusu zaman aşımı ayarlarını düzenleyin](./media/howto-mfa-nps-extension-rdg/image14.png)

1. İletişim kutularını kapatmak için iki kez **Tamam** ' a tıklayın.

### <a name="verify-connection-request-policies"></a>Bağlantı Isteği Ilkelerini doğrulama

Varsayılan olarak, RD Ağ Geçidi bağlantı yetkilendirme ilkeleri için merkezi bir ilke deposu kullanacak şekilde yapılandırdığınızda, RD Ağ Geçidi CAP isteklerini NPS sunucusuna iletecek şekilde yapılandırılır. Azure AD MFA uzantısı yüklü NPS sunucusu, RADIUS erişim isteğini işler. Aşağıdaki adımlarda, varsayılan bağlantı isteği ilkesini nasıl doğrulayabildiğinizi gösterilmektedir.  

1. RD Ağ Geçidi, NPS (yerel) konsolunda, **ilkeler**' i genişletin ve **bağlantı isteği ilkeleri**' ni seçin.
1. **TS Ağ GEÇIDI YETKILENDIRME ilkesi**' ne çift tıklayın.
1. **TS Ağ GEÇIDI YETKILENDIRME ilkesi özellikleri** Iletişim kutusunda **Ayarlar** sekmesine tıklayın.
1. **Ayarlar** sekmesinde, bağlantı isteği Iletme altında **kimlik doğrulama**' ya tıklayın. RADIUS istemcisi kimlik doğrulama isteklerini iletecek şekilde yapılandırılmıştır.

   ![Sunucu grubunu belirten kimlik doğrulama ayarlarını yapılandırın](./media/howto-mfa-nps-extension-rdg/image15.png)

1. **Iptal 'e** tıklayın.

>[!NOTE]
> Bağlantı isteği ilkesi oluşturma hakkında daha fazla bilgi için, [bağlantı isteği ilkelerini](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) aynı şekilde yapılandırma makalesini inceleyin. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS uzantısının yüklendiği sunucuda NPS 'YI yapılandırma

NPS uzantısının yüklendiği NPS sunucusunun, Uzak Masaüstü Ağ Geçidi NPS sunucusuyla RADIUS iletileri alışverişi yapabilmesi gerekir. Bu ileti değişimini etkinleştirmek için NPS uzantısı hizmetinin yüklü olduğu sunucuda NPS bileşenlerini yapılandırmanız gerekir.

### <a name="register-server-in-active-directory"></a>Sunucuyu Active Directory Kaydet

Bu senaryoda düzgün çalışması için, NPS sunucusunun Active Directory kayıtlı olması gerekir.

1. NPS sunucusunda **Sunucu Yöneticisi** açın.
1. Sunucu Yöneticisi ' de **Araçlar**' a ve ardından **ağ ilkesi sunucusu**' na tıklayın.
1. Ağ Ilkesi sunucu konsolunda **NPS (yerel)** öğesine sağ tıklayın ve ardından **Active Directory sunucuyu kaydet**' e tıklayın.
1. **Tamam** ' ı iki kez tıklatın.

   ![NPS sunucusunu Active Directory kaydetme](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Sonraki yordam için konsolunu açık bırakın.

### <a name="create-and-configure-radius-client"></a>RADIUS istemcisi oluşturma ve yapılandırma

Uzak Masaüstü Ağ Geçidi, NPS sunucusu için bir RADIUS istemcisi olarak yapılandırılmalıdır.

1. NPS uzantısının yüklendiği NPS sunucusunda, **NPS (yerel)** konsolunda, **RADIUS istemcileri** ' ne sağ tıklayın ve **Yeni**' ye tıklayın.

   ![NPS konsolunda yeni bir RADIUS Istemcisi oluşturma](./media/howto-mfa-nps-extension-rdg/image17.png)

1. **Yenı RADIUS istemcisi** iletişim kutusunda, _ağ geçidi_ gibi kolay bir ad ve Uzak Masaüstü Ağ Geçidi sunucusunun IP adresini veya DNS adını belirtin.
1. **Paylaşılan gizlilik** ve **paylaşılan gizli anahtarı Onayla** alanlarında, daha önce kullandığınız gizli anahtarı girin.

   ![Kolay bir ad ve IP veya DNS adresi yapılandırın](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Yeni RADIUS Istemcisi iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

### <a name="configure-network-policy"></a>Ağ Ilkesini yapılandırma

Azure AD MFA uzantısına sahip NPS sunucusunun, bağlantı yetkilendirme Ilkesi (CAP) için belirtilen Merkezi ilke deposu olduğunu unutmayın. Bu nedenle, geçerli bağlantı isteklerini yetkilendirmek için NPS sunucusunda bir CAP uygulamanız gerekir.  

1. NPS sunucusunda NPS (yerel) konsolunu açın, **ilkeler**' i genişletin ve **ağ ilkeleri**' ne tıklayın.
1. **Diğer erişim sunucularının bağlantıları**' na sağ tıklayın ve **yinelenen ilke**' ye tıklayın.

   ![Diğer erişim sunucuları ilkesiyle bağlantıyı çoğaltın](./media/howto-mfa-nps-extension-rdg/image19.png)

1. **Diğer erişim sunucularındaki bağlantıların kopyasını** sağ tıklatın ve **Özellikler**' e tıklayın.
1. **Diğer erişim sunucularına bağlantı kopyalama** iletişim kutusunda, **ilke adı**' nda, _RDG_CAP_ gibi uygun bir ad girin. **İlkeyi etkin** olarak işaretleyin ve **erişim ver**' i seçin. İsteğe bağlı olarak, **ağ erişim sunucusu türü**' nde **Uzak Masaüstü Ağ Geçidi**' yi seçin veya **belirtilmemiş** olarak bırakabilirsiniz.

   ![İlkeyi adlandırın, etkinleştirin ve erişim izni verin](./media/howto-mfa-nps-extension-rdg/image21.png)

1. **Kısıtlamalar** sekmesine tıklayın ve **istemcilerin kimlik doğrulama yöntemi üzerinde anlaşmadan bağlanmasına izin ver**' i işaretleyin.

   ![İstemcilerin bağlanmasına izin vermek için kimlik doğrulama yöntemlerini değiştirme](./media/howto-mfa-nps-extension-rdg/image22.png)

1. İsteğe bağlı olarak, **koşullar** sekmesine tıklayın ve bağlantının yetkilendirildiği, örneğin belirli bir Windows grubuna üyelik için karşılanması gereken koşulları ekleyin.

   ![İsteğe bağlı olarak bağlantı koşullarını belirtin](./media/howto-mfa-nps-extension-rdg/image23.png)

1. **Tamam**'a tıklayın. İlgili yardım konusunu görüntülemeniz istendiğinde **Hayır**' a tıklayın.
1. Yeni ilkenizin listenin en üstünde olduğundan, ilkenin etkin olduğundan ve erişim izni verdiğinden emin olun.

   ![İlkenizi listenin en üstüne taşıyın](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Yapılandırmayı Doğrula

Yapılandırmayı doğrulamak için, uygun bir RDP istemcisiyle Uzak Masaüstü Ağ Geçidi oturum açmanız gerekir. Bağlantı yetkilendirme Ilkeleriniz tarafından izin verilen ve Azure AD MFA için etkinleştirilen bir hesabı kullandığınızdan emin olun.

Aşağıdaki görüntüde gösterildiği gibi, **uzak masaüstü web erişimi** sayfasını kullanabilirsiniz.

![Uzak Masaüstü Web Erişimi test etme](./media/howto-mfa-nps-extension-rdg/image25.png)

Birincil kimlik doğrulaması için kimlik bilgilerinizi başarıyla girdikten sonra, Uzak Masaüstü bağlantısı iletişim kutusu aşağıda gösterildiği gibi uzak bağlantı başlatma durumunu gösterir. 

Daha önce Azure AD MFA 'da yapılandırdığınız ikincil kimlik doğrulama yöntemiyle kimlik doğrulaması yaptıysanız, kaynağa bağlanırsınız. Ancak, ikincil kimlik doğrulaması başarılı olmazsa kaynağa erişiminizi reddettiniz. 

![Uzak bağlantı başlatma Uzak Masaüstü Bağlantısı](./media/howto-mfa-nps-extension-rdg/image26.png)

Aşağıdaki örnekte, bir Windows Phone 'da kimlik doğrulayıcı uygulaması, ikincil kimlik doğrulaması sağlamak için kullanılır.

![Doğrulamayı gösteren Windows Phone Authenticator uygulaması örneği](./media/howto-mfa-nps-extension-rdg/image27.png)

İkincil kimlik doğrulama yöntemini kullanarak kimlik doğrulamasından sonra, Uzak Masaüstü Ağ Geçidi normal olarak oturum açarsınız. Ancak, güvenilir bir cihazda bir mobil uygulama kullanarak ikincil bir kimlik doğrulama yöntemi kullanmanız gerektiğinden oturum açma işlemi, aksi takdirde daha güvenlidir.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Başarılı oturum açma olayları için Olay Görüntüleyicisi günlüklerini görüntüleme

Windows Olay Görüntüleyicisi günlüklerinde başarılı oturum açma olaylarını görüntülemek için, Windows Terminal Hizmetleri ve Windows güvenlik günlüklerini sorgulamak üzere aşağıdaki Windows PowerShell komutunu verebilirsiniz.

Ağ Geçidi işlem günlüklerinde _(olay Viewer\uygulamalar ve hizmetler Logs\microsoft\windows\terminalservices-gateway\işletimsel)_ başarılı oturum açma olaylarını sorgulamak Için aşağıdaki PowerShell komutlarını kullanın:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Bu komut, kullanıcının kaynak yetkilendirme ilkesi gereksinimlerini (RD RAP) gösteren ve erişim izni verilen Windows olaylarını görüntüler.

![PowerShell kullanarak olayları görüntüleme](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Bu komut, kullanıcının bağlantı yetkilendirme ilkesi gereksinimlerini karşıladığı zaman gösterilecek olayları görüntüler.

![PowerShell kullanarak bağlantı yetkilendirme ilkesini görüntüleme](./media/howto-mfa-nps-extension-rdg/image29.png)

Ayrıca bu günlüğü görüntüleyebilir ve olay kimlikleri, 300 ve 200 ' de filtre uygulayabilirsiniz. Güvenlik Olay Görüntüleyici günlüklerinde başarılı oturum açma olaylarını sorgulamak için aşağıdaki komutu kullanın:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Bu komut, merkezi NPS veya RD Ağ Geçidi sunucusunda çalıştırılabilir.

![Başarılı oturum açma olayları örneği](./media/howto-mfa-nps-extension-rdg/image30.png)

Aşağıda gösterildiği gibi güvenlik günlüğünü veya ağ Ilkesi ve erişim Hizmetleri özel görünümünü de görüntüleyebilirsiniz:

![Ağ Ilkesi ve erişim Hizmetleri Olay Görüntüleyicisi](./media/howto-mfa-nps-extension-rdg/image31.png)

Azure AD MFA için NPS uzantısını yüklediğiniz sunucuda, _uygulama ve hizmetler Logs\Microsoft\AzureMfa_' de uzantıya özgü Olay Görüntüleyicisi uygulama günlüklerini bulabilirsiniz.

![Olay Görüntüleyicisi AuthZ uygulama günlükleri](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Sorun giderme kılavuzu

Yapılandırma beklenildiği gibi çalışmıyorsa, sorun gidermeye başlamak için ilk yer, kullanıcının Azure AD MFA kullanacak şekilde yapılandırıldığını doğrulamadır. Kullanıcının [Azure Portal](https://portal.azure.com)bağlanmasını sağlar. Kullanıcılardan ikincil doğrulama istenir ve kimlik doğrulaması başarılı olursa, hatalı bir Azure AD MFA yapılandırması ortadan kaldırabilirsiniz.

Azure AD MFA, kullanıcılar için çalışıyorsa ilgili olay günlüklerini gözden geçirmeniz gerekir. Bunlar, önceki bölümde ele alınan güvenlik olayı, ağ geçidi işlem ve Azure AD MFA günlüklerini içerir.

Aşağıda, başarısız bir oturum açma olayının (olay KIMLIĞI 6273) gösterildiği güvenlik günlüğünün bir çıkışı verilmiştir.

![Başarısız oturum açma olayının örneği](./media/howto-mfa-nps-extension-rdg/image33.png)

AzureMFA günlüklerinden ilgili bir olay aşağıda verilmiştir:

![Örnek Azure AD MFA günlüğü Olay Görüntüleyicisi](./media/howto-mfa-nps-extension-rdg/image34.png)

Gelişmiş sorun giderme seçeneklerini gerçekleştirmek için, NPS hizmetinin yüklü olduğu NPS veritabanı biçim günlük dosyalarına başvurun. Bu günlük dosyaları, _%systemroot%\System32\Logs_ klasöründe virgülle ayrılmış metin dosyaları olarak oluşturulur.

Bu günlük dosyalarının açıklaması için bkz. [NPS veritabanı biçimi günlük dosyalarını yorumlama](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)). Bu günlük dosyalarındaki girişlerin bir elektronik tabloya veya veritabanına aktarılmadan yorumlanması zor olabilir. Günlük dosyalarını yorumlarken size yardımcı olmak için birkaç IAS ayrıştırıcılarını çevrimiçi bulabilirsiniz.

Aşağıdaki görüntüde, bu tür indirilebilir bir [paylaşılan yazılım uygulamasının](https://www.deepsoftware.com/iasviewer)çıkışı gösterilmektedir.

![Örnek paylaşılan yazılım uygulaması IAS ayrıştırıcısı](./media/howto-mfa-nps-extension-rdg/image35.png)

Son olarak, ek sorun giderme seçenekleri için, [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)gibi bir protokol Çözümleyicisi kullanabilirsiniz.

Microsoft Message Analyzer ' dan aşağıdaki görüntüde, **contoso alicec** Kullanıcı adını içeren RADIUS protokolünde filtrelenmiş ağ trafiği gösterilmektedir.

![Filtrelenmiş trafiği gösteren Microsoft Ileti Çözümleyicisi](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD Multi-Factor Authentication alma](concept-mfa-licensing.md)

[RADIUS kullanan Uzak Masaüstü Ağ Geçidi ve Azure Multi-Factor Authentication Sunucusu](howto-mfaserver-nps-rdg.md)

[Şirket içi dizinlerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md)
