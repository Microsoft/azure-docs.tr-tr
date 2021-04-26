---
title: Azure Active Directory Uygulama Ara Sunucusu uzak masaüstü 'Nü yayımlama
description: Uzak Masaüstü Hizmetleri (RDS) ile uygulama proxy 'Sinin nasıl yapılandırılacağını ele alır
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 5e5d5370057449d1877c31b249d3fe47fd60bf2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687677"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Azure Active Directory Uygulama Ara Sunucusu ile Uzak Masaüstünü yayımlama

Uzak Masaüstü hizmeti ve Azure AD Uygulama Ara Sunucusu, kurumsal ağdan uzakta çalışan çalışanların üretkenliğini artırmak için birlikte çalışır. 

Bu makale için amaçlanan hedef kitle şunlardır:
- Uzak Masaüstü Hizmetleri aracılığıyla şirket içi uygulamaları yayımlayarak son kullanıcılarına daha fazla uygulama sunmak isteyen geçerli uygulama proxy 'Si müşterileri.
- Azure AD Uygulama Ara Sunucusu kullanarak dağıtımının saldırı yüzeyini azaltmak isteyen geçerli Uzak Masaüstü Hizmetleri müşteriler. Bu senaryo, RDS 'ye bir dizi iki adımlı doğrulama ve koşullu erişim denetimi sağlar.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Uygulama proxy 'Sinin standart RDS dağıtımına uygun olması

Standart bir RDS dağıtımı, Windows Server üzerinde çalışan çeşitli uzak masaüstü rolü hizmetlerini içerir. [Uzak Masaüstü Hizmetleri mimarisine](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture)bakarak birden çok dağıtım seçeneği vardır. Diğer RDS dağıtım seçeneklerinden farklı olarak, [Azure AD uygulama ara sunucusu](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) (Aşağıdaki diyagramda gösterilmiştir) RDS dağıtımı, bağlayıcı hizmetini çalıştıran sunucudan kalıcı bir giden bağlantıya sahiptir. Diğer dağıtımlar, Açık gelen bağlantıları bir yük dengeleyici aracılığıyla bırakır.

![Uygulama proxy 'Si, RDS VM ile genel İnternet arasında bulunur](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Bir RDS dağıtımında, RD Web rolü ve RD Ağ Geçidi rolü Internet 'e yönelik makinelerde çalışır. Bu uç noktalar aşağıdaki nedenlerle sunulur:
- RD Web, kullanıcıya oturum açmak için genel bir uç nokta sağlar ve erişebileceği çeşitli şirket içi uygulamaları ve masaüstlerini görüntüler. Bir kaynak seçtikten sonra, işletim sistemindeki yerel uygulama kullanılarak bir RDP bağlantısı oluşturulur.
- RD Ağ Geçidi, Kullanıcı RDP bağlantısını başlattığında resme gelir. RD Ağ Geçidi, internet üzerinden gelen şifrelenmiş RDP trafiğini işler ve bunu kullanıcının bağlandığı şirket içi sunucuya dönüştürür. Bu senaryoda RD Ağ Geçidi aldığı trafik Azure AD Uygulama Ara Sunucusu gelir.

>[!TIP]
>Daha önce RDS dağıtmadıysanız ya da başlamadan önce daha fazla bilgi isterseniz, [rds Azure Resource Manager ve Azure Marketi ile sorunsuz](/windows-server/remote/remote-desktop-services/rds-in-azure)bir şekilde dağıtmayı öğrenin.

## <a name="requirements"></a>Gereksinimler

- Hem RD Web hem de RD Ağ Geçidi uç noktaları aynı makinede ve ortak bir köke yerleştirilmelidir. RD Web ve RD Ağ Geçidi, uygulama proxy 'Si ile tek bir uygulama olarak yayımlanır, böylece iki uygulama arasında çoklu oturum açma deneyimine sahip olabilirsiniz.
- Zaten [RDS 'yi dağıtmış](/windows-server/remote/remote-desktop-services/rds-in-azure)ve [uygulama proxy 'si etkinleştirmiş](application-proxy-add-on-premises-application.md)olmanız gerekir. Bağlayıcı yükleme, gerekli bağlantı noktalarını ve URL 'LERI açma ve sunucuda TLS 1,2 'yi etkinleştirme gibi uygulama ara sunucusunu etkinleştirmek için önkoşulları karşılatığınızdan emin olun.
- Son kullanıcılarınızın RD Web 'e veya RD Web istemcisine bağlanmak için uyumlu bir tarayıcı kullanması gerekir. Daha fazla ayrıntı için bkz. [istemci yapılandırmalarına yönelik destek](#support-for-other-client-configurations).
- RD Web 'i yayımlarken, aynı iç ve dış FQDN 'nin kullanılması önerilir. İç ve dış FQDN 'Ler farklıysa, istemcinin geçersiz bağlantılar almasını önlemek için Istek üst bilgisi çevirisini devre dışı bırakmanız gerekir.
- Internet Explorer 'da RD Web kullanıyorsanız, RDS ActiveX eklentisini etkinleştirmeniz gerekir.
- RD Web istemcisini kullanıyorsanız, uygulama proxy [Bağlayıcısı Sürüm 1.5.1975 veya üstünü](./application-proxy-release-version-history.md)kullanmanız gerekir.
- Azure AD ön kimlik doğrulama akışı için, kullanıcılar yalnızca **RemoteApp ve masaüstleri** bölmesinde yayımlanmış kaynaklara bağlanabilir. Kullanıcılar, **uzak bilgisayara bağlan** bölmesine kullanarak bir masaüstüne bağlanamaz.
- Windows Server 2019 kullanıyorsanız, HTTP2 protokolünü devre dışı bırakmanız gerekebilir. Daha fazla bilgi için bkz. [öğretici: Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md).

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Birleşik RDS ve uygulama proxy 'Si senaryosunu dağıtma

Ortamınız için RDS ve Azure AD Uygulama Ara Sunucusu ayarladıktan sonra, iki çözümü birleştirmek için adımları izleyin. Bu adımlar, Web 'e yönelik iki RDS uç noktasını (RD Web ve RD Ağ Geçidi) uygulama olarak yayımlamayı ve sonra RDS 'deki trafiği uygulama proxy 'Si üzerinden çalışmaya yönlendirmenize yol gösterir.

### <a name="publish-the-rd-host-endpoint"></a>RD konak uç noktasını yayımlama

1. Aşağıdaki değerlerle [Yeni bir uygulama proxy uygulaması yayımlayın](application-proxy-add-on-premises-application.md) :
   - İç URL: `https://<rdhost>.com/` , burada `<rdhost>` RD Web ve RD Ağ Geçidi paylaşımının ortak köküdür.
   - Dış URL: Bu alan, uygulamanın adına göre otomatik olarak doldurulur, ancak bunu değiştirebilirsiniz. Kullanıcılarınız, RDS 'ye erişirken bu URL 'ye gider.
   - Ön kimlik doğrulama yöntemi: Azure Active Directory
   - URL üst bilgilerini çevir: Hayır
2. Yayınlanan RD uygulamasına kullanıcı atama. Bunların tümünün RDS 'ye erişimi olduğundan emin olun.
3. **Azure AD çoklu oturum açma devre dışı** olduğundan uygulama için çoklu oturum açma yöntemini bırakın.

   >[!Note]
   >Kullanıcılarınızın Azure AD 'de bir kez kimlik doğrulaması yapması ve RD Web 'e bir kez doğrulanması istenir, ancak RD Ağ Geçidi için çoklu oturum açma vardır.

4. **Azure Active Directory** ve ardından **uygulama kayıtları**' nı seçin. Listeden uygulamanızı seçin.
5. **Yönet** altında **marka**' i seçin.
6. **Ana sayfa URL 'si** alanını RD Web uç noktanıza işaret etmek için (gibi `https://<rdhost>.com/RDWeb` ) güncelleştirin.

### <a name="direct-rds-traffic-to-application-proxy"></a>RDS trafiğini uygulama proxy 'sine doğrudan

RDS dağıtımına yönetici olarak bağlanın ve dağıtım için RD Ağ Geçidi sunucusu adını değiştirin. Bu yapılandırma, bağlantıların Azure AD Uygulama Ara Sunucusu hizmetinden gitmesini sağlar.

1. RD Bağlantı Aracısı rolünü çalıştıran RDS sunucusuna bağlanın.
2. **Sunucu Yöneticisi** başlatın.
3. Soldaki bölmeden **Uzak Masaüstü Hizmetleri** seçin.
4. **Genel bakış**'ı seçin.
5. Dağıtıma genel bakış bölümünde, açılan menüyü seçin ve **dağıtım özelliklerini Düzenle**' yi seçin.
6. RD Ağ Geçidi sekmesinde **sunucu adı** alanını, uygulama proxy 'sindeki RD ana bilgisayar uç noktası Için ayarladığınız dış URL ile değiştirin.
7. **Oturum açma yöntemi** alanını **parola kimlik doğrulaması** olarak değiştirin.

   ![RDS 'de dağıtım özellikleri ekranı](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Her koleksiyon için bu komutu çalıştırın. *\<yourcollectionname\>* Ve *\<proxyfrontendurl\>* bilgilerini kendi bilgileriniz ile değiştirin. Bu komut RD Web ile RD Ağ Geçidi arasında çoklu oturum açmayı mümkün hale getirir ve performansı iyileştirir:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Örnek:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Yukarıdaki komut, "' ngerektir" içinde bir geri onay kullanır.

9. Özel RDP özelliklerinin değiştirilmesini ve bu koleksiyon için RDWeb 'den indirilecek RDP dosyası içeriklerini görüntülemeyi doğrulamak için şu komutu çalıştırın:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Uzak Masaüstü 'Nü yapılandırdığınıza göre, Azure AD Uygulama Ara Sunucusu, internet 'e yönelik bir bileşen olarak kullanıma sunulmuştur. RD Web ve RD Ağ Geçidi makinelerinizdeki diğer herkese açık internet 'e yönelik uç noktaları kaldırabilirsiniz.

### <a name="enable-the-rd-web-client"></a>RD Web Istemcisini etkinleştirme
Ayrıca, kullanıcıların RD Web Istemcisini kullanmasını istiyorsanız, [kullanıcılarınız Için Uzak Masaüstü Web Istemcisini ayarlama](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin) adımlarını izleyin.

Uzak Masaüstü Web istemcisi, kullanıcıların Microsoft Edge, Internet Explorer 11, Google Chrome, Safari veya Mozilla Firefox (v 55.0 ve üzeri) gibi HTML5 uyumlu bir Web tarayıcısı aracılığıyla kuruluşunuzun uzak masaüstü altyapısına erişmesini sağlar.

## <a name="test-the-scenario"></a>Senaryoyu test etme

Bir Windows 7 veya 10 bilgisayarda Internet Explorer ile senaryoyu test edin.

1. Ayarladığınız dış URL 'ye gidin veya uygulamanızı [Uygulamaps panelinde](https://myapps.microsoft.com)bulun.
2. Azure Active Directory için kimlik doğrulamanız istenir. Uygulamaya atadığınız bir hesabı kullanın.
3. RD Web 'de kimlik doğrulamanız istenir.
4. RDS kimlik doğrulaması başarılı olduktan sonra istediğiniz masaüstü veya uygulamayı seçip çalışmaya başlayabilirsiniz.

## <a name="support-for-other-client-configurations"></a>Diğer istemci yapılandırmalarına yönelik destek

Bu makalede özetlenen yapılandırma RD Web veya RD Web Istemcisi aracılığıyla RDS 'ye erişim için kullanılır. Bununla birlikte, diğer işletim sistemlerini veya tarayıcılarını de destekleyebilirsiniz. Fark, kullandığınız kimlik doğrulama yöntemidir.

| Kimlik doğrulama yöntemi | Desteklenen istemci yapılandırması |
| --------------------- | ------------------------------ |
| Ön kimlik doğrulama    | Internet Explorer * veya [Edge BERKUM IE modu](/deployedge/edge-ie-mode) + RDS ActiveX EKLENTISI kullanılarak RD Web-Windows 7/10 |
| Ön kimlik doğrulama    | Microsoft Edge, Internet Explorer 11, Google Chrome, Safari veya Mozilla Firefox gibi RD Web Istemcisi-HTML5 uyumlu Web tarayıcısı (v 55.0 ve üzeri) |
| Doğrudan geçiş | Microsoft Uzak Masaüstü uygulamasını destekleyen diğer tüm işletim sistemleri |

* My Apps Portal, uzak masaüstü uygulamasına erişmek için kullanıldığında Edge Bermıum IE modu gereklidir.  

Ön kimlik doğrulama akışı, geçiş akışından daha fazla güvenlik avantajı sunar. Ön kimlik doğrulaması sayesinde, çoklu oturum açma, koşullu erişim ve şirket içi kaynaklarınız için iki aşamalı doğrulama gibi Azure AD kimlik doğrulama özelliklerini kullanabilirsiniz. Ayrıca, yalnızca kimliği doğrulanmış trafiğin ağınıza ulaşmasını de güvence altına alırsınız.

Geçiş kimlik doğrulamasını kullanmak için, bu makalede listelenen adımlarda yalnızca iki değişiklik vardır:
1. [RD ana bilgisayar uç noktasını Yayımla](#publish-the-rd-host-endpoint) adım 1 ' de, ön kimlik doğrulama yöntemini **PASSTHROUGH** olarak ayarlayın.
2. [Uygulama proxy 'Sine doğrudan RDS trafiği](#direct-rds-traffic-to-application-proxy)içinde, 8. adımı tamamen atlayın.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD Uygulama Ara Sunucusu ile SharePoint’e uzaktan erişimi etkinleştirme](application-proxy-integrate-with-sharepoint-server.md)
- [Azure AD Uygulama Ara Sunucusu kullanarak uygulamalara uzaktan erişmek için güvenlik konuları](application-proxy-security.md)
- [Birden çok uygulama sunucusuna yük dengeleme için en iyi uygulamalar](application-proxy-high-availability-load-balancing.md#best-practices-for-load-balancing-among-multiple-app-servers)
