---
title: Öğretici-Azure Active Directory ' de şirket içi uygulama uygulama proxy 'Si ekleme
description: Azure Active Directory (Azure AD), kullanıcıların Azure AD hesabıyla oturum açarak şirket içi uygulamalara erişmesini sağlayan bir uygulama proxy hizmeti içerir. Bu öğreticide, ortamınızı uygulama proxy 'Si ile kullanım için nasıl hazırlayacağınız gösterilmektedir. Daha sonra, Azure AD kiracınıza şirket içi bir uygulama eklemek için Azure portal kullanır.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 6a7f50268a09ae451b1e9dda2ca354ded31efb68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200745"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Öğretici: Azure Active Directory içindeki uygulama proxy 'Si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme

Azure Active Directory (Azure AD), kullanıcıların Azure AD hesabıyla oturum açarak şirket içi uygulamalara erişmesini sağlayan bir uygulama proxy hizmeti içerir. Uygulama proxy 'si hakkında daha fazla bilgi edinmek için bkz. [uygulama proxy nedir?](what-is-application-proxy.md). Bu öğretici, ortamınızı uygulama proxy 'Si ile kullanılmak üzere hazırlar. Ortamınız hazırlandıktan sonra, Azure AD kiracınıza şirket içi bir uygulama eklemek için Azure portal kullanırsınız. 

:::image type="content" source="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png" alt-text="Uygulama proxy 'Si genel bakış Diyagramı" lightbox="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png":::

Başlamadan önce, uygulama yönetimi ve **tek Sign-On (SSO)** kavramlarıyla ilgili bilgi edindiğinizden emin olun. Aşağıdaki bağlantılara göz atın:
- [Azure AD 'de uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)
- [Tek Sign-On (SSO) nedir?](what-is-single-sign-on.md)

Bağlayıcılar, uygulama proxy 'sinin önemli bir parçasıdır. Bağlayıcılar hakkında daha fazla bilgi edinmek için bkz. [Azure AD uygulama ara sunucusu bağlayıcıları anlama](application-proxy-connectors.md).

Bu öğreticide:

> [!div class="checklist"]
> * Giden trafik için bağlantı noktaları açar ve belirli URL 'lere erişim sağlar
> * Bağlayıcıyı Windows sunucunuza yükleyip uygulama proxy 'Sine kaydeder
> * Yüklü olan bağlayıcıyı doğrular ve doğru şekilde kaydedilir
> * Azure AD kiracınıza şirket içi bir uygulama ekler
> * Bir sınama kullanıcısının Azure AD hesabı kullanarak uygulamada oturum açmasını doğrular

## <a name="prerequisites"></a>Önkoşullar

Azure AD 'ye şirket içi bir uygulama eklemek için şunlar gerekir:

* [Microsoft Azure AD Premium aboneliği](https://azure.microsoft.com/pricing/details/active-directory)
* Bir uygulama yöneticisi hesabı
* Kullanıcı kimlikleri, şirket içi bir dizinden eşitlenmeli veya doğrudan Azure AD kiracılarınız içinde oluşturulmuş olmalıdır. Kimlik eşitlemesi, Azure AD 'nin uygulama proxy 'Si yayımlanmış uygulamalarına erişim vermeden önce kullanıcıların kimliğini doğrulamasına ve çoklu oturum açma (SSO) gerçekleştirmesi için gerekli Kullanıcı tanımlayıcı bilgilerine sahip olmasına olanak sağlar.

### <a name="windows-server"></a>Windows sunucusu

Uygulama proxy 'Sini kullanmak için, Windows Server 2012 R2 veya üstünü çalıştıran bir Windows Server gerekir. Uygulama proxy bağlayıcısını sunucuya yüklersiniz. Bu bağlayıcı sunucusunun Azure 'daki uygulama proxy hizmetlerine ve yayımlamayı planladığınız şirket içi uygulamalara bağlanması gerekir.

Üretim ortamınızda yüksek kullanılabilirlik için birden fazla Windows Server olması önerilir. Bu öğretici için, bir Windows Server yeterlidir.

> [!IMPORTANT]
> Bağlayıcıyı Windows Server 2019 ' ye yüklüyorsanız, düzgün çalışması için Kerberos kısıtlanmış temsili için WinHttp bileşeninde HTTP2 protokol desteğini devre dışı bırakmanız gerekir. Bu, desteklenen işletim sistemlerinin önceki sürümlerinde varsayılan olarak devre dışıdır. Aşağıdaki kayıt defteri anahtarını eklemek ve sunucuyu yeniden başlatmak Windows Server 2019 ' de devre dışı bırakır. Bunun makine genelinde bir kayıt defteri anahtarı olduğunu unutmayın.
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp]
> "EnableDefaultHTTP2"=dword:00000000
> ```
>
> Anahtar PowerShell ile aşağıdaki komutla ayarlanabilir.
> ```
> Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\' -Name EnableDefaultHTTP2 -Value 0
> ```

#### <a name="recommendations-for-the-connector-server"></a>Bağlayıcı sunucusu için öneriler

1. Bağlayıcı ile uygulama arasındaki performansı iyileştirmek için bağlayıcı sunucusunu uygulama sunucularına yakın fiziksel olarak bulun. Daha fazla bilgi için bkz. [trafik akışını en iyileştirme Azure Active Directory uygulama ara sunucusu](application-proxy-network-topology.md).
1. Bağlayıcı sunucusu ve Web uygulamaları sunucuları aynı Active Directory etki alanına ait olmalıdır veya güvenen etki alanlarını kapsamalıdır. Sunucuların aynı etki alanında veya güvenen etki alanlarında olması, tümleşik Windows kimlik doğrulaması (ıWA) ve Kerberos kısıtlanmış temsili (KCD) ile çoklu oturum açma (SSO) kullanma gereksinimidir. Bağlayıcı sunucusu ve Web uygulaması sunucuları farklı Active Directory etki alanlarındaysa, çoklu oturum açma için kaynak tabanlı temsilciyi kullanmanız gerekir. Daha fazla bilgi için bkz. [uygulama proxy 'si ile çoklu oturum açma Için KCD](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Azure AD parola koruma proxy 'Si dağıttıysanız, Azure AD Uygulama Ara Sunucusu ve Azure AD parola koruma proxy 'yi aynı makinede yüklemeyin. Azure AD Uygulama Ara Sunucusu ve Azure AD parola koruma proxy, Azure AD Connect Aracısı Güncelleştirici hizmeti 'nin farklı sürümlerini yükler. Aynı makinede birlikte yüklendiğinde bu farklı sürümler uyumlu değildir.

#### <a name="tls-requirements"></a>TLS gereksinimleri

Uygulama proxy bağlayıcısını yüklemeden önce Windows Bağlayıcısı sunucusunda TLS 1,2 özelliğinin etkinleştirilmiş olması gerekir.

TLS 1,2 ' i etkinleştirmek için:

1. Aşağıdaki kayıt defteri anahtarlarını ayarlayın:

   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Sunucuyu yeniden başlatın.

> [!Note]
> Microsoft, Azure hizmetlerini farklı bir kök sertifika yetkilisi (CA) kümesinden TLS sertifikaları kullanacak şekilde güncelleştiriyor. Geçerli CA sertifikaları CA/tarayıcı Forum temel gereksinimlerinden biriyle uyumlu olmadığından bu değişiklik yapılıyor. Daha fazla bilgi için bkz. [Azure TLS sertifikası değişiklikleri](../../security/fundamentals/tls-certificate-changes.md) .

## <a name="prepare-your-on-premises-environment"></a>Şirket içi ortamınızı hazırlama

Azure AD Uygulama Ara Sunucusu için ortamınızı hazırlamak üzere Azure veri merkezlerine iletişimi etkinleştirerek başlayın. Yolda bir güvenlik duvarı varsa, açık olduğundan emin olun. Açık bir güvenlik duvarı, bağlayıcının uygulama proxy 'sine HTTPS (TCP) istekleri yapmasına izin verir.

> [!IMPORTANT]
> Bağlayıcıyı Azure Kamu Bulutu için yüklüyorsanız, [ön](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) koşullar ve [yükleme adımlarını](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)izleyin. Bu, yüklemeyi çalıştırmak için farklı bir URL kümesine ve ek parametreye erişim etkinleştirilmesini gerektirir.

### <a name="open-ports"></a>Bağlantı noktalarını açma

**Giden** trafiğe aşağıdaki bağlantı noktalarını açın.

| Bağlantı noktası numarası | Nasıl kullanılır? |
| ----------- | ------------------------------------------------------------ |
| 80          | TLS/SSL sertifikası doğrulanırken sertifika iptal listelerini (CRL 'Ler) indirme |
| 443         | Uygulama proxy 'Si hizmeti ile giden tüm iletişim |

Güvenlik duvarınız kaynak kullanıcılara göre trafiği zorlarsa, ağ hizmeti olarak çalışan Windows hizmetlerinden gelen trafik için 80 ve 443 bağlantı noktalarını da açın.

### <a name="allow-access-to-urls"></a>URL 'lere erişime izin ver

Aşağıdaki URL 'Lere erişime izin ver:

| URL | Bağlantı noktası | Nasıl kullanılır? |
| ------------------------------------------------------------ | --------- | ------------------------------------------------------------ |
| &ast;. msappproxy.net<br>&ast;. servicebus.windows.net         | 443/HTTPS | Bağlayıcı ile uygulama proxy 'Si bulut hizmeti arasındaki iletişim |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP   | Bağlayıcı, sertifikaları doğrulamak için bu URL 'Leri kullanır.        |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;. microsoftonline-p.com<br>&ast;. msauth.net<br>&ast;. msauthimages.net<br>&ast;. msecnd.net<br>&ast;. msftauth.net<br>&ast;. msftauthimages.net<br>&ast;. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com<br>www.microsoft.com/pkiops | 443/HTTPS | Bağlayıcı, kayıt işlemi sırasında bu URL 'Leri kullanır. |
| ctldl.windowsupdate.com                                      | 80/HTTP   | Bağlayıcı, kayıt işlemi sırasında bu URL 'YI kullanır. |

&ast; &ast; Güvenlik duvarınız veya ara sunucunuz, etki alanı sonekleri temelinde erişim kurallarını yapılandırmanıza izin ver, yukarıdaki. msappproxy.net,. ServiceBus.Windows.net ve diğer URL 'lere bağlantılara izin verebilirsiniz. Aksi takdirde, [Azure IP aralıklarına ve hizmet etiketlerine genel buluta](https://www.microsoft.com/download/details.aspx?id=56519)erişime izin vermeniz gerekir. IP aralıkları her hafta güncellenir.

### <a name="dns-name-resolution-for-azure-ad-application-proxy-endpoints"></a>Azure AD Uygulama Ara Sunucusu uç noktaları için DNS ad çözümlemesi

Azure AD Uygulama Ara Sunucusu uç noktaları için genel DNS kayıtları, bir kayda işaret eden CNAME kayıtlardır. Bu, hataya dayanıklılık ve esneklik sağlar. Azure AD Uygulama Ara Sunucusu bağlayıcısının, her zaman, _*. msappproxy.net_ veya _*. ServiceBus.Windows.net_ etki alanı sonekleri ile ana bilgisayar adlarıyla eriştiği garanti edilir. Ancak, ad çözümlemesi sırasında CNAME kayıtları farklı ana bilgisayar adları ve sonekleri içeren DNS kayıtları içerebilir.  Bu nedenle, cihazın (Kurulum-bağlayıcı sunucunuza, güvenlik duvarı, giden ara sunucuya bağlı olarak) zincirdeki tüm kayıtları çözümleyebildiğinden ve çözümlenen IP adresleriyle bağlantıya izin verdiğinden emin olmanız gerekir. Zincirdeki DNS kayıtları zaman zaman olarak değiştirilebileceğinizden, hiçbir liste DNS kaydı sunamaz.

## <a name="install-and-register-a-connector"></a>Bağlayıcı yükleyip kaydetme

Uygulama proxy 'Sini kullanmak için, uygulama proxy 'Si hizmeti ile kullandığınız her Windows sunucusuna bir bağlayıcı yüklersiniz. Bağlayıcı, şirket içi uygulama sunucularından Azure AD 'deki uygulama proxy 'sine giden bağlantıyı yöneten bir aracıdır. Ayrıca, Azure AD Connect gibi diğer kimlik doğrulama aracılarının de yüklü olduğu sunuculara bir bağlayıcı yükleyebilirsiniz.


Bağlayıcıyı yüklemek için:

1. Uygulama proxy 'Si kullanan dizinin uygulama Yöneticisi olarak [Azure Portal](https://portal.azure.com/) oturum açın. Örneğin, kiracı etki alanı contoso.com ise yönetici admin@contoso.com ya da bu etki alanında başka bir yönetici diğer adı olmalıdır.
1. Sağ üst köşedeki Kullanıcı adınızı seçin. Uygulama proxy 'Si kullanan bir dizine oturum açtığınızdan emin olun. Dizinleri değiştirmeniz gerekiyorsa, **dizini** Değiştir ' i seçin ve uygulama proxy 'si kullanan bir dizin seçin.
1. Sol gezinti panelinde **Azure Active Directory**' yi seçin.
1. **Yönet** altında **uygulama proxy 'si**' ni seçin.
1. **Bağlayıcı hizmetini indir**' i seçin.

    ![Hizmet koşullarını görmek için bağlayıcı hizmetini indirin](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Hizmet koşullarını okuyun. Hazırsanız **& koşulları kabul et**' i seçin.
1. Pencerenin alt kısmında, bağlayıcıyı yüklemek için **Çalıştır** ' ı seçin. Bir Install Sihirbazı açılır.
1. Hizmeti yüklemek için sihirbazdaki yönergeleri izleyin. Bağlayıcıyı Azure AD kiracınızın uygulama proxy 'Si ile kaydetmeniz istendiğinde, uygulama yöneticinizin kimlik bilgilerini sağlayın.
   
    - Internet Explorer (IE) için, **IE artırılmış güvenlik yapılandırması** **Açık** olarak ayarlandıysa, kayıt ekranını göremeyebilirsiniz. Erişim sağlamak için hata iletisindeki yönergeleri izleyin. **Internet Explorer Artırılmış Güvenlik Yapılandırması** 'nın **kapalı** olduğundan emin olun.

### <a name="general-remarks"></a>Genel açıklamalar

Daha önce bir bağlayıcı yüklediyseniz, en son sürümü almak için yeniden yükleyin. Önceden yayınlanan sürümler ve içerdikleri değişiklikler hakkında bilgi için bkz. [uygulama proxy 'si: sürüm yayınlama geçmişi](application-proxy-release-version-history.md).

Şirket içi uygulamalarınız için birden fazla Windows Server 'ı seçerseniz, bağlayıcıyı her bir sunucuya yükleyip kaydetmeniz gerekir. Bağlayıcıları bağlayıcı grupları halinde düzenleyebilirsiniz. Daha fazla bilgi için bkz. [bağlayıcı grupları](application-proxy-connector-groups.md).

Farklı bölgelerde bağlayıcılar yüklediyseniz, her bağlayıcı grubuyla kullanılacak en yakın uygulama proxy 'Si bulut hizmeti bölgesini seçerek trafiği iyileştirebilirsiniz, bkz. [trafik akışını en iyileştirme Azure Active Directory uygulama ara sunucusu](application-proxy-network-topology.md)

Kuruluşunuz internet 'e bağlanmak için proxy sunucuları kullanıyorsa, bunları uygulama proxy 'Si için yapılandırmanız gerekir.  Daha fazla bilgi için bkz. [mevcut şirket içi proxy sunucularıyla çalışma](application-proxy-configure-connectors-with-proxy-servers.md). 

Bağlayıcılar, kapasite planlaması ve bunların güncel kalması hakkında bilgi için bkz. [Azure AD uygulama ara sunucusu bağlayıcıları anlama](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Bağlayıcının yüklendiğini ve doğru şekilde kaydedildiğini doğrulama

Yeni bir bağlayıcının doğru bir şekilde yüklendiğini doğrulamak için Azure portal veya Windows Server 'ı kullanabilirsiniz.

### <a name="verify-the-installation-through-azure-portal"></a>Azure portal aracılığıyla yüklemeyi doğrulama

Bağlayıcının yüklendiğini ve doğru şekilde kaydedildiğini doğrulamak için:

1. [Azure Portal](https://portal.azure.com)kiracı dizininizde oturum açın.
1. Sol gezinti panelinde **Azure Active Directory**' yi seçin ve ardından **Yönet** bölümünün altındaki **uygulama proxy 'si** ' ni seçin. Tüm bağlayıcılar ve bağlayıcı gruplarınız bu sayfada görüntülenir.
1. Ayrıntılarını doğrulamak için bir bağlayıcı görüntüleyin. Bağlayıcılar varsayılan olarak genişletilmelidir. Görüntülemek istediğiniz bağlayıcı genişletilmemişse, ayrıntıları görüntülemek için bağlayıcıyı genişletin. Etkin yeşil etiket, bağlayıcının hizmete bağlanamadığını gösterir. Ancak, etiket yeşil olsa da bir ağ sorunu bağlayıcının ileti almasını engelliyor olabilir.

    ![Azure AD Uygulama Ara Sunucusu bağlayıcıları](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Bağlayıcının yüklenmesiyle ilgili daha fazla yardım için bkz. [uygulama proxy bağlayıcısını yükleme sorunu](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Windows sunucunuz aracılığıyla yüklemeyi doğrulama

Bağlayıcının yüklendiğini ve doğru şekilde kaydedildiğini doğrulamak için:

1. **Windows anahtarı '** nı ve *Services. msc*' yi girerek Windows Hizmetleri Yöneticisi 'ni açın.
1. Aşağıdaki iki hizmetin durumunun **çalışır** durumda olup olmadığını denetleyin.
   - **MICROSOFT AAD uygulama proxy Bağlayıcısı** bağlantı imkanı sunar.
   - **Microsoft AAD uygulama ara sunucusu bağlayıcı güncelleştiricisi** , otomatik bir güncelleştirme hizmetidir. Güncelleştirici, bağlayıcının yeni sürümlerini denetler ve bağlayıcıyı gerektiği şekilde güncelleştirir.

     ![Uygulama Ara Sunucusu Bağlayıcısı hizmetleri - ekran görüntüsü](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Hizmetlerin durumu **çalışmıyorsa**, her bir hizmeti seçmek için sağ tıklayın ve **Başlat**' ı seçin.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Azure AD 'ye şirket içi uygulama ekleme

Ortamınızı hazırladığınıza ve bir bağlayıcı yükleolduğunuza göre, Azure AD 'ye şirket içi uygulamalar eklemeye hazırsınız.  

1. [Azure Portal](https://portal.azure.com/)yönetici olarak oturum açın.
2. Sol gezinti panelinde **Azure Active Directory**' yi seçin.
3. **Kurumsal uygulamalar**' ı seçin ve ardından **Yeni uygulama**' yı seçin.
4. Şirket içi **uygulamalar** bölümünde sayfanın alt yarısında görüntülenen **bir şirket içi uygulama ekle** düğmesini seçin. Alternatif olarak, sayfanın üst kısmında **kendi uygulamanızı oluştur** ' u seçip Şirket **içi uygulamaya güvenli uzaktan erişim Için uygulama ara sunucusunu yapılandır**' ı seçebilirsiniz.
5. **Kendi şirket içi uygulamanızı ekleyin** bölümünde, uygulamanız hakkında aşağıdaki bilgileri sağlayın:

    | Alan  | Açıklama |
    | :--------------------- | :----------------------------------------------------------- |
    | **Ad** | Uygulamamda ve Azure portal görünecek uygulamanın adı. |
    | **İç URL** | Özel ağınızın içinden uygulamaya erişim URL 'SI. Arka uç sunucusundaki belirli bir yolun yayımlanmasını sağlayabilirsiniz. Sunucunun geri kalanı yayımlanmaz. Bu şekilde, farklı siteleri farklı uygulamalarla aynı sunucuda yayımlayabilir ve her birine kendi ad ve erişim kurallarına sahip olabilirsiniz.<br><br>Bir yol yayımlarsanız uygulamanıza ilişkin tüm gerekli görüntüleri, betikleri ve stil sayfalarını içerdiğinden emin olun. Örneğin, uygulamanız https: \/ /yourapp/App ise ve https: \/ /yourapp/Media konumunda bulunan görüntüleri kullanıyorsa, https: \/ /yourapp/yolunu olarak yayımlamanız gerekir. Bu iç URL 'nin kullanıcılarınızın göreceği giriş sayfası olması gerekmez. Daha fazla bilgi için bkz. [yayımlanan uygulamalar için özel bir giriş sayfası ayarlama](application-proxy-configure-custom-home-page.md). |
    | **Dış URL** | Kullanıcıların uygulamaya ağınızın dışından erişebileceği adres. Varsayılan uygulama proxy 'Si etki alanını kullanmak istemiyorsanız, [Azure AD uygulama ara sunucusu özel etki alanları](application-proxy-configure-custom-domain.md)hakkında bilgi edinin. |
    | **Ön kimlik doğrulama** | Uygulama proxy 'Si, uygulamanıza erişim vermeden önce kullanıcıları nasıl doğrular.<br><br>**Azure Active Directory** -uygulama proxy 'si, kullanıcıların dizin ve uygulama için izinlerinin kimliğini doğrulayan Azure AD ile oturum açmasını yeniden yönlendirir. Koşullu erişim ve Multi-Factor Authentication gibi Azure AD güvenlik özelliklerinin avantajlarından yararlanabilmek için bu seçeneği varsayılan olarak tutmanız önerilir. Uygulamayı Microsoft Bulut uygulama güvenliği ile izlemek için **Azure Active Directory** gereklidir.<br><br>**Geçiş** -kullanıcıların uygulamaya erişmek IÇIN Azure AD 'de kimlik doğrulaması yapması gerekmez. Arka uçta kimlik doğrulama gereksinimlerini ayarlamaya devam edebilirsiniz. |
    | **Bağlayıcı grubu** | Bağlayıcılar uygulamanıza uzaktan erişimi işler ve bağlayıcı grupları, bağlayıcıları ve uygulamaları bölgeye, ağa veya amaca göre düzenlemenize yardımcı olur. Henüz oluşturulmuş bağlayıcı grubunuz yoksa, uygulamanız **varsayılan** olarak atanır.<br><br>Uygulamanız bağlanmak için WebSockets kullanıyorsa, gruptaki tüm bağlayıcılar sürüm 1.5.612.0 veya üzeri olmalıdır. |

6. Gerekirse, **ek ayarları** yapılandırın. Çoğu uygulama için, bu ayarları varsayılan durumlarında tutmanız gerekir. 

    | Alan | Açıklama |
    | :------------------------------ | :----------------------------------------------------------- |
    | **Arka uç uygulama zaman aşımı** | Bu değeri yalnızca uygulamanızın kimlik doğrulaması ve bağlanma yavaş olması durumunda **uzun** olarak ayarlayın. Varsayılan olarak, arka uç uygulama zaman aşımı 85 saniye uzunluğuna sahiptir. Long olarak ayarlandığında, arka uç zaman aşımı 180 saniyeye yükseltilir. |
    | **HTTP-Only tanımlama bilgisi kullan** | Uygulama proxy 'Si tanımlama bilgilerinin HTTP yanıt üstbilgisinde HTTPOnly bayrağını içermesi için bu değeri **Evet** olarak ayarlayın. Uzak Masaüstü Hizmetleri kullanıyorsanız, bu değeri **Hayır** olarak ayarlayın. |
    | **Güvenli tanımlama bilgisi kullan**| Şifrelenmiş HTTPS isteği gibi güvenli bir kanal üzerinden tanımlama bilgilerini iletmek için bu değeri **Evet** olarak ayarlayın.
    | **Kalıcı tanımlama bilgisi kullan**| Bu değeri **Hayır** olarak ayarlayın. Bu ayarı yalnızca süreçler arasında tanımlama bilgilerini paylaşabilen uygulamalar için kullanın. Tanımlama bilgisi ayarları hakkında daha fazla bilgi için bkz. [Azure Active Directory ' de şirket içi uygulamalara erişmek Için tanımlama bilgisi ayarları](./application-proxy-configure-cookie-settings.md).
    | **Üst bilgilerdeki URL 'Leri çevir** | Uygulamanız kimlik doğrulaması isteğindeki orijinal ana bilgisayar üst bilgisini gerektirmediğiniz sürece bu değeri **Evet** olarak tutun. |
    | **Uygulama gövdesinde URL 'Leri çevir** | Diğer şirket içi uygulamalara yönelik olarak kodlanmış HTML bağlantıları yoksa ve özel etki alanları kullanmadıkça bu değeri **Hayır** olarak tutun. Daha fazla bilgi için bkz. [uygulama proxy 'si Ile bağlantı çevirisi](application-proxy-configure-hard-coded-link-translation.md).<br><br>Bu uygulamayı Microsoft Cloud App Security (MCAS) ile izlemeyi planlıyorsanız, bu değeri **Evet** olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft Cloud App Security ve Azure Active Directory ile gerçek zamanlı uygulama erişimi Izlemeyi yapılandırma](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. **Add (Ekle)** seçeneğini belirleyin.

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulamanın doğru şekilde ekleneceğini test etmeye hazırsınız. Aşağıdaki adımlarda, uygulamaya bir kullanıcı hesabı ekleyecek ve oturum açmayı denemeniz gerekir.

### <a name="add-a-user-for-testing"></a>Test için Kullanıcı ekleme

Uygulamaya Kullanıcı eklemeden önce, Kullanıcı hesabının şirket ağının içinden uygulamaya erişim izni olduğunu doğrulayın.

Bir test kullanıcısı eklemek için:

1. **Kurumsal uygulamalar**' ı seçin ve ardından test etmek istediğiniz uygulamayı seçin.
2. **Başlarken**' i seçin ve ardından **test için Kullanıcı ata**' yı seçin.
3. **Kullanıcılar ve gruplar** altında **Kullanıcı Ekle**' yi seçin.
4. **Atama Ekle** altında **Kullanıcılar ve gruplar**' ı seçin. **Kullanıcı ve gruplar** bölümü görüntülenir.
5. Eklemek istediğiniz hesabı seçin.
6. **Seç**' i seçin ve ardından **ata**' yı seçin.

### <a name="test-the-sign-on"></a>Oturum açmayı test etme

Uygulamanın oturum açma sınamasını test etmek için:

1. Test etmek istediğiniz uygulamadan **uygulama proxy 'si**' ni seçin.
2. Sayfanın üst kısmında, uygulamayı **Test** et ' i seçerek uygulama üzerinde bir test çalıştırın ve herhangi bir yapılandırma sorunu olup olmadığını denetleyin.
3. Uygulamanın oturum açmasını sınamak için öncelikle uygulamayı çalıştırdığınızdan emin olun, ardından algılanan sorunların çözüm kılavuzunu gözden geçirmek için tanılama raporunu indirin.

Sorun giderme için bkz. [uygulama proxy 'si sorunlarını ve hata Iletilerini sorun giderme](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu öğreticide oluşturduğunuz kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şirket içi ortamınızı uygulama proxy 'Si ile çalışacak şekilde hazırladınız ve ardından uygulama proxy bağlayıcısını yükleyip kaydettiniz. Daha sonra, Azure AD kiracınıza bir uygulama eklediniz. Bir kullanıcının Azure AD hesabı kullanarak uygulamada oturum açmasını doğruladınız.

Şu işlemleri yaptınız:
> [!div class="checklist"]
> * Giden trafik için açılan bağlantı noktaları ve belirli URL 'lere erişim izni verildi
> * Bağlayıcı Windows sunucunuza yüklendi ve uygulama proxy 'Si ile kaydedildi
> * Bağlayıcı yüklendi ve doğru şekilde kaydedildi
> * Azure AD kiracınıza şirket içi uygulama eklendi
> * Bir test kullanıcısının Azure AD hesabı kullanarak uygulamada oturum açmasını doğrulayan

Uygulamayı çoklu oturum açma için yapılandırmaya hazırsınız. Tek bir oturum açma yöntemi seçmek ve çoklu oturum açma öğreticilerini bulmak için aşağıdaki bağlantıyı kullanın.

> [!div class="nextstepaction"]
> [Çoklu oturum açmayı yapılandırma](sso-options.md#choosing-a-single-sign-on-method)
