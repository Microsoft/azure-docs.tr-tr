---
title: Azure Active Directory 'de şirket içi uygulama uygulama proxy 'Si ekleme | Microsoft Docs
description: Azure Active Directory (Azure AD), kullanıcıların Azure AD hesabıyla oturum açarak şirket içi uygulamalara erişmesini sağlayan bir uygulama proxy hizmeti içerir. Bu öğreticide, ortamınızı uygulama proxy 'Si ile kullanım için nasıl hazırlayacağınız gösterilmektedir. Daha sonra, Azure AD kiracınıza şirket içi bir uygulama eklemek için Azure portal kullanır.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0399f084e663ab891d59384af263a7faac2f42e
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73943825"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Öğretici: Azure Active Directory içindeki uygulama proxy 'Si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme

Azure Active Directory (Azure AD), kullanıcıların Azure AD hesabıyla oturum açarak şirket içi uygulamalara erişmesini sağlayan bir uygulama proxy hizmeti içerir. Bu öğretici, ortamınızı uygulama proxy 'Si ile kullanılmak üzere hazırlar. Ortamınız hazırlandıktan sonra, Azure AD kiracınıza şirket içi bir uygulama eklemek için Azure portal kullanırsınız.

Bu öğreticide:

> [!div class="checklist"]
> * Giden trafik için bağlantı noktaları açar ve belirli URL 'lere erişim sağlar
> * Bağlayıcıyı Windows sunucunuza yükleyip uygulama proxy 'Sine kaydeder
> * Yüklü olan bağlayıcıyı doğrular ve doğru şekilde kaydedilir
> * Azure AD kiracınıza şirket içi bir uygulama ekler
> * Bir sınama kullanıcısının Azure AD hesabı kullanarak uygulamada oturum açmasını doğrular

## <a name="before-you-begin"></a>Başlamadan önce

Azure AD 'ye şirket içi bir uygulama eklemek için şunlar gerekir:

* [Microsoft Azure AD Premium aboneliği](https://azure.microsoft.com/pricing/details/active-directory)
* Bir uygulama yöneticisi hesabı
* Kullanıcı kimlikleri, şirket içi bir dizinden eşitlenmeli veya doğrudan Azure AD kiracılarınız içinde oluşturulmuş olmalıdır. Kimlik eşitlemesi, Azure AD 'nin uygulama proxy 'Si yayımlanmış uygulamalarına erişim vermeden önce kullanıcıların kimliğini doğrulamasına ve çoklu oturum açma (SSO) gerçekleştirmesi için gerekli Kullanıcı tanımlayıcı bilgilerine sahip olmasına olanak sağlar.

### <a name="windows-server"></a>Windows server

Uygulama proxy 'Sini kullanmak için, Windows Server 2012 R2 veya üstünü çalıştıran bir Windows Server gerekir. Uygulama proxy bağlayıcısını sunucuya yüklersiniz. Bu bağlayıcı sunucusunun Azure 'daki uygulama proxy hizmetlerine ve yayımlamayı planladığınız şirket içi uygulamalara bağlanması gerekir.

Üretim ortamınızda yüksek kullanılabilirlik için birden fazla Windows Server olması önerilir. Bu öğretici için, bir Windows Server yeterlidir.

> [!IMPORTANT]
> Bağlayıcıyı Windows Server 2019 ' ye yüklüyorsanız HTTP2 sınırlaması vardır. Bağlayıcıyı bu sürümde kullanma geçici çözümü aşağıdaki kayıt defteri anahtarını ekleyip sunucuyu yeniden başlatmayı sağlar. Bu bir makine kayıt defteri geniş anahtarıdır. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Bağlayıcı sunucusu için öneriler

1. Bağlayıcı ile uygulama arasındaki performansı iyileştirmek için bağlayıcı sunucusunu uygulama sunucularına yakın fiziksel olarak bulun. Daha fazla bilgi için bkz. [ağ topolojisi konuları](application-proxy-network-topology.md).
1. Bağlayıcı sunucusu ve Web uygulamaları sunucuları aynı Active Directory etki alanına ait olmalıdır veya güvenen etki alanlarını kapsamalıdır. Sunucuların aynı etki alanında veya güvenen etki alanlarında olması, tümleşik Windows kimlik doğrulaması (ıWA) ve Kerberos kısıtlanmış temsili (KCD) ile çoklu oturum açma (SSO) kullanma gereksinimidir. Bağlayıcı sunucusu ve Web uygulaması sunucuları farklı Active Directory etki alanlarındaysa, çoklu oturum açma için kaynak tabanlı temsilciyi kullanmanız gerekir. Daha fazla bilgi için bkz. [uygulama proxy 'si ile çoklu oturum açma Için KCD](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Azure AD parola koruma proxy 'Si dağıttıysanız, Azure AD Uygulama Ara Sunucusu ve Azure AD parola koruma proxy 'yi aynı makinede yüklemeyin. Azure AD Uygulama Ara Sunucusu ve Azure AD parola koruma proxy, Azure AD Connect Aracısı Güncelleştirici hizmeti 'nin farklı sürümlerini yükler. Aynı makinede birlikte yüklendiğinde bu farklı sürümler uyumlu değildir.

#### <a name="tls-requirements"></a>TLS gereksinimleri

Uygulama proxy bağlayıcısını yüklemeden önce Windows Bağlayıcısı sunucusunda TLS 1,2 özelliğinin etkinleştirilmiş olması gerekir.

TLS 1.2 etkinleştirmek için:

1. Aşağıdaki kayıt defteri anahtarlarını ayarlayın:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Sunucuyu yeniden başlatın.

> [!IMPORTANT]
> Müşterilerimize en iyi sınıf şifrelemeyi sağlamak için, uygulama proxy hizmeti yalnızca TLS 1,2 protokollerine erişimi sınırlandırır. Bu değişiklikler 31 Ağustos 2019 tarihinden itibaren kademeli olarak alındı ve geçerli. Tüm istemci sunucu ve tarayıcı-sunucu birleşimlerinizin, uygulama proxy hizmeti ile bağlantı sağlamak için TLS 1,2 kullanacak şekilde güncelleştirildiğinden emin olun. Bunlar, kullanıcılarınızın uygulama proxy 'Si aracılığıyla yayımlanan uygulamalara erişmek için kullandığı istemcileri içerir. Faydalı başvurular ve kaynaklar için bkz. [Office 365 ' de TLS 1,2](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) için hazırlanma.

## <a name="prepare-your-on-premises-environment"></a>Şirket içi ortamınızı hazırlama

Azure AD Uygulama Ara Sunucusu için ortamınızı hazırlamak üzere Azure veri merkezlerine iletişimi etkinleştirerek başlayın. Yolda bir güvenlik duvarı varsa, açık olduğundan emin olun. Açık bir güvenlik duvarı, bağlayıcının uygulama proxy 'sine HTTPS (TCP) istekleri yapmasına izin verir.

### <a name="open-ports"></a>Açık bağlantı noktaları

**Giden** trafiğe aşağıdaki bağlantı noktalarını açın.

   | Bağlantı noktası numarası | Nasıl kullanılır? |
   | --- | --- |
   | 80 | SSL sertifikası doğrulanırken sertifika iptal listelerinin (CRL 'Ler) indirilmesi |
   | 443 | Uygulama proxy 'Si hizmeti ile giden tüm iletişim |

Güvenlik duvarınız kaynak kullanıcılara göre trafiği zorlarsa, ağ hizmeti olarak çalışan Windows hizmetlerinden gelen trafik için 80 ve 443 bağlantı noktalarını da açın.

### <a name="allow-access-to-urls"></a>URL 'lere erişime izin ver

Aşağıdaki URL 'Lere erişime izin ver:

| URL'si | Nasıl kullanılır? |
| --- | --- |
| \*. msappproxy.net<br>\*. servicebus.windows.net | Bağlayıcı ile uygulama proxy 'Si bulut hizmeti arasındaki iletişim |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure, sertifikaları doğrulamak için bu URL 'Leri kullanır. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*. microsoftonline.com<br>\*. microsoftonline-p.com<br>\*. msauth.net<br>\*. msauthimages.net<br>\*. msecnd.net<br>\*. msftauth.net<br>\*. msftauthimages.net<br>\*. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | Bağlayıcı, kayıt işlemi sırasında bu URL 'Leri kullanır. |

Güvenlik duvarınız veya proxy 'niz DNS izin verilenler listelerine olanak tanımak için \*. msappproxy.net ve \*. servicebus.windows.net bağlantılarına izin verebilirsiniz. Aksi takdirde, [Azure IP aralıklarına ve hizmet etiketlerine genel buluta](https://www.microsoft.com/download/details.aspx?id=56519)erişime izin vermeniz gerekir. IP aralıkları her hafta güncellenir.

## <a name="install-and-register-a-connector"></a>Bağlayıcı yükleyip kaydetme

Uygulama proxy 'Sini kullanmak için, uygulama proxy 'Si hizmeti ile kullandığınız her Windows sunucusuna bir bağlayıcı yüklersiniz. Bağlayıcı, şirket içi uygulama sunucularından Azure AD 'deki uygulama proxy 'sine giden bağlantıyı yöneten bir aracıdır. Ayrıca, Azure AD Connect gibi diğer kimlik doğrulama aracılarının de yüklü olduğu sunuculara bir bağlayıcı yükleyebilirsiniz.

Bağlayıcıyı yüklemek için:

1. Uygulama proxy 'Si kullanan dizinin uygulama Yöneticisi olarak [Azure Portal](https://portal.azure.com/) oturum açın. Örneğin, kiracı etki alanı contoso.com ise yönetici bu etki alanında admin@contoso.com veya başka bir yönetici diğer adı olmalıdır.
1. Sağ üst köşedeki Kullanıcı adınızı seçin. Uygulama proxy 'Si kullanan bir dizine oturum açtığınızdan emin olun. Dizinleri değiştirmeniz gerekiyorsa, **dizini** Değiştir ' i seçin ve uygulama proxy 'si kullanan bir dizin seçin.
1. Sol gezinti panelinde **Azure Active Directory**' yi seçin.
1. **Yönet**altında **uygulama proxy 'si**' ni seçin.
1. **Bağlayıcı hizmetini indir**' i seçin.

    ![Hizmet koşullarını görmek için bağlayıcı hizmetini indirin](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Hizmet koşullarını okuyun. Hazırsanız **& koşulları kabul et**' i seçin.
1. Pencerenin alt kısmında, bağlayıcıyı yüklemek için **Çalıştır** ' ı seçin. Bir Install Sihirbazı açılır.
1. Hizmeti yüklemek için sihirbazdaki yönergeleri izleyin. Bağlayıcıyı Azure AD kiracınızın uygulama proxy 'Si ile kaydetmeniz istendiğinde, uygulama yöneticinizin kimlik bilgilerini sağlayın.
    - Internet Explorer (IE) için, **IE artırılmış güvenlik yapılandırması** **Açık**olarak ayarlandıysa, kayıt ekranını göremeyebilirsiniz. Erişim sağlamak için hata iletisindeki yönergeleri izleyin. **Internet Explorer Artırılmış Güvenlik Yapılandırması** 'nın **kapalı**olduğundan emin olun.

### <a name="general-remarks"></a>Genel açıklamalar

Daha önce bir bağlayıcı yüklediyseniz, en son sürümü almak için yeniden yükleyin. Önceden yayınlanan sürümler ve içerdikleri değişiklikler hakkında bilgi için bkz. [uygulama proxy 'si: sürüm yayınlama geçmişi](application-proxy-release-version-history.md).

Şirket içi uygulamalarınız için birden fazla Windows Server 'ı seçerseniz, bağlayıcıyı her bir sunucuya yükleyip kaydetmeniz gerekir. Bağlayıcıları bağlayıcı grupları halinde düzenleyebilirsiniz. Daha fazla bilgi için bkz. [bağlayıcı grupları](application-proxy-connector-groups.md).

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
1. Aşağıdaki iki hizmetin durumunun **çalışır**durumda olup olmadığını denetleyin.
   - **MICROSOFT AAD uygulama proxy Bağlayıcısı** bağlantı imkanı sunar.
   - **Microsoft AAD uygulama ara sunucusu bağlayıcı güncelleştiricisi** , otomatik bir güncelleştirme hizmetidir. Güncelleştirici, bağlayıcının yeni sürümlerini denetler ve bağlayıcıyı gerektiği şekilde güncelleştirir.

     ![Uygulama Ara Sunucusu Bağlayıcısı hizmetleri - ekran görüntüsü](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Hizmetlerin durumu **çalışmıyorsa**, her bir hizmeti seçmek için sağ tıklayın ve **Başlat**' ı seçin.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Azure AD 'ye şirket içi uygulama ekleme

Ortamınızı hazırladığınıza ve bir bağlayıcı yükleolduğunuza göre, Azure AD 'ye şirket içi uygulamalar eklemeye hazırsınız.  

1. [Azure Portal](https://portal.azure.com/)yönetici olarak oturum açın.
2. Sol gezinti panelinde **Azure Active Directory**' yi seçin.
3. **Kurumsal uygulamalar**' ı seçin ve ardından **Yeni uygulama**' yı seçin.
4. Şirket **içi uygulamalar** bölümünde Şirket **içi uygulama ekle**' yi seçin.
5. **Kendi şirket içi uygulamanızı ekleyin** bölümünde, uygulamanız hakkında aşağıdaki bilgileri sağlayın:

    | Alan | Açıklama |
    | :---- | :---------- |
    | **Adı** | Erişim panelinde ve Azure portal görünecek uygulamanın adı. |
    | **İç URL** | Özel ağınızın içinden uygulamaya erişim URL 'SI. Arka uç sunucusundaki belirli bir yolun yayımlanmasını sağlayabilirsiniz. Sunucunun geri kalanı yayımlanmaz. Bu şekilde, farklı siteleri farklı uygulamalarla aynı sunucuda yayımlayabilir ve her birine kendi ad ve erişim kurallarına sahip olabilirsiniz.<br><br>Bir yol yayımlarsanız uygulamanıza ilişkin tüm gerekli görüntüleri, betikleri ve stil sayfalarını içerdiğinden emin olun. Örneğin, uygulamanız https:\//yourapp/App ise ve https:\//yourapp/Media konumunda bulunan görüntüleri kullanıyorsa, https:\//yourapp/yolunu yol olarak yayımlamanız gerekir. Bu iç URL 'nin kullanıcılarınızın göreceği giriş sayfası olması gerekmez. Daha fazla bilgi için bkz. [yayımlanan uygulamalar için özel bir giriş sayfası ayarlama](application-proxy-configure-custom-home-page.md). |
    | **Dış URL** | Kullanıcıların uygulamaya ağınızın dışından erişebileceği adres. Varsayılan uygulama proxy 'Si etki alanını kullanmak istemiyorsanız, [Azure AD uygulama ara sunucusu özel etki alanları](application-proxy-configure-custom-domain.md)hakkında bilgi edinin.|
    | **Ön kimlik doğrulama** | Uygulama proxy 'Si, uygulamanıza erişim vermeden önce kullanıcıları nasıl doğrular.<br><br>**Azure Active Directory** -uygulama proxy 'si, kullanıcıların dizin ve uygulama için izinlerinin kimliğini doğrulayan Azure AD ile oturum açmasını yeniden yönlendirir. Koşullu erişim ve Multi-Factor Authentication gibi Azure AD güvenlik özelliklerinin avantajlarından yararlanabilmek için bu seçeneği varsayılan olarak tutmanız önerilir. Uygulamayı Microsoft Bulut uygulama güvenliği ile izlemek için **Azure Active Directory** gereklidir.<br><br>**Geçiş** -kullanıcıların uygulamaya erişmek IÇIN Azure AD 'de kimlik doğrulaması yapması gerekmez. Arka uçta kimlik doğrulama gereksinimlerini ayarlamaya devam edebilirsiniz. |
    | **Bağlayıcı grubu** | Bağlayıcılar uygulamanıza uzaktan erişimi işler ve bağlayıcı grupları, bağlayıcıları ve uygulamaları bölgeye, ağa veya amaca göre düzenlemenize yardımcı olur. Henüz oluşturulmuş bağlayıcı grubunuz yoksa, uygulamanız **varsayılan**olarak atanır.<br><br>Uygulamanız bağlanmak için WebSockets kullanıyorsa, gruptaki tüm bağlayıcılar sürüm 1.5.612.0 veya üzeri olmalıdır.|

6. Gerekirse, **ek ayarları**yapılandırın. Çoğu uygulama için, bu ayarları varsayılan durumlarında tutmanız gerekir. 

    | Alan | Açıklama |
    | :---- | :---------- |
    | **Arka uç uygulama zaman aşımı** | Bu değeri yalnızca uygulamanızın kimlik doğrulaması ve bağlanma yavaş olması durumunda **uzun** olarak ayarlayın. Varsayılan olarak, arka uç uygulama zaman aşımı 85 saniye uzunluğuna sahiptir. Long olarak ayarlandığında, arka uç zaman aşımı 180 saniyeye yükseltilir. |
    | **Yalnızca HTTP tanımlama bilgisini kullan** | Uygulama proxy 'Si tanımlama bilgilerinin HTTP yanıt üstbilgisinde HTTPOnly bayrağını içermesi için bu değeri **Evet** olarak ayarlayın. Uzak Masaüstü Hizmetleri kullanıyorsanız, bu değeri **Hayır**olarak ayarlayın.|
    | **Güvenli tanımlama bilgisi kullan**| Şifrelenmiş HTTPS isteği gibi güvenli bir kanal üzerinden tanımlama bilgilerini iletmek için bu değeri **Evet** olarak ayarlayın.
    | **Kalıcı tanımlama bilgisi kullan**| Bu değeri **Hayır**olarak ayarlayın. Bu ayarı yalnızca süreçler arasında tanımlama bilgilerini paylaşabilen uygulamalar için kullanın. Tanımlama bilgisi ayarları hakkında daha fazla bilgi için bkz. [Azure Active Directory ' de şirket içi uygulamalara erişmek Için tanımlama bilgisi ayarları](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Üst bilgilerdeki URL 'Leri çevir** | Uygulamanız kimlik doğrulaması isteğindeki orijinal ana bilgisayar üst bilgisini gerektirmediğiniz sürece bu değeri **Evet** olarak tutun. |
    | **Uygulama gövdesinde URL 'Leri çevir** | Diğer şirket içi uygulamalara yönelik olarak kodlanmış HTML bağlantıları yoksa ve özel etki alanları kullanmadıkça bu değeri **Hayır** olarak tutun. Daha fazla bilgi için bkz. [uygulama proxy 'si Ile bağlantı çevirisi](application-proxy-configure-hard-coded-link-translation.md).<br><br>Bu uygulamayı Microsoft Cloud App Security (MCAS) ile izlemeyi planlıyorsanız, bu değeri **Evet** olarak ayarlayın. Daha fazla bilgi için bkz. [Microsoft Cloud App Security ve Azure Active Directory ile gerçek zamanlı uygulama erişimi Izlemeyi yapılandırma](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. **Add (Ekle)** seçeneğini belirleyin.

## <a name="test-the-application"></a>Uygulamayı test etme

Uygulamanın doğru şekilde ekleneceğini test etmeye hazırsınız. Aşağıdaki adımlarda, uygulamaya bir kullanıcı hesabı ekleyecek ve oturum açmayı denemeniz gerekir.

### <a name="add-a-user-for-testing"></a>Test için Kullanıcı ekleme

Uygulamaya Kullanıcı eklemeden önce, Kullanıcı hesabının şirket ağının içinden uygulamaya erişim izni olduğunu doğrulayın.

Bir test kullanıcısı eklemek için:

1. **Kurumsal uygulamalar**' ı seçin ve ardından test etmek istediğiniz uygulamayı seçin.
2. **Başlarken**' i seçin ve ardından **test için Kullanıcı ata**' yı seçin.
3. **Kullanıcılar ve gruplar**altında **Kullanıcı Ekle**' yi seçin.
4. **Atama Ekle**altında **Kullanıcılar ve gruplar**' ı seçin. **Kullanıcı ve gruplar** bölümü görüntülenir.
5. Eklemek istediğiniz hesabı seçin.
6. **Seç**' i seçin ve ardından **ata**' yı seçin.

### <a name="test-the-sign-on"></a>Oturum açmayı test etme

Uygulamanın oturum açma sınamasını test etmek için:

1. Tarayıcınızda, yayımlama adımı sırasında yapılandırdığınız dış URL 'ye gidin. Başlangıç ekranını görmeniz gerekir.
1. Önceki bölümde oluşturduğunuz Kullanıcı olarak oturum açın.

Sorun giderme için bkz. [uygulama proxy 'si sorunlarını ve hata Iletilerini sorun giderme](application-proxy-troubleshoot.md).

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
> [Çoklu oturum açmayı yapılandırma](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
