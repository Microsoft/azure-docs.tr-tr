---
title: Uygulama proxy 'Si için Kerberos kısıtlanmış temsil yapılandırmaları sorunlarını giderme | Microsoft Docs
description: Uygulama proxy'si için Kerberos kısıtlanmış temsil yapılandırmalarıyla ilgili sorunları giderme
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca50cfb8697fdbb8c71054c5a6b4d5e23792eb5
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381529"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Uygulama Ara sunucusu için kısıtlı Kerberos temsilcisi yapılandırmalarıyla ilgili sorunları giderme

Yayımlanan uygulamalara SSO elde etmek için kullanılabilen yöntemler, bir uygulamadan diğerine farklılık gösterebilir. Varsayılan olarak Azure Active Directory (Azure AD) uygulama proxy'si sunan bir Kerberos Kısıtlı temsilci (KCD) bir seçenektir. Kısıtlı Kerberos kimlik doğrulaması için arka uç uygulamaları çalıştırmak için kullanıcılarınız için bir bağlayıcı yapılandırabilirsiniz.

KCD etkinleştirme yordamını oldukça basittir. SSO destekleyen en fazla çeşitli bileşenler ve kimlik doğrulama akışı ilişkin genel bir yaklaşım gerektirir. Ancak bazen KCD SSO beklendiği gibi çalışmaz. Bu senaryolar sorunu gidermeye yönelik bilgi kaynakları iyi ihtiyacınız vardır.

Bu makalede tek bir sorun giderme ve en yaygın sorunlardan bazılarını kendi kendine düzeltme yardımcı olan bir başvuru noktası sağlar. Ayrıca, daha karmaşık uygulama sorunlarını tanılama da kapsar.

Bu makalede aşağıdaki varsayımların yapar:

- Azure AD uygulama proxy'si dağıtımını [uygulaması Ara sunucusu ile çalışmaya başlama](application-proxy-add-on-premises-application.md) ve genel olmayan KCD uygulamalara erişim beklendiği gibi çalışmayabilir.
- Yayımlanan hedef uygulama Internet Information Services (IIS) ve Kerberos Microsoft uyarlamasını dayanır.
- Sunucu ve uygulama konakları, tek bir Azure Active Directory etki alanında yer alır. Çapraz etki alanı ve orman senaryoları hakkında ayrıntılı bilgi için bkz. [KCD incelemeyi](https://aka.ms/KCDPaper).
- Konu olan uygulamanın yayımlanan bir Azure kiracısı ile ön kimlik doğrulama etkinleştirildi. Kullanıcıların Azure'a, form tabanlı kimlik doğrulaması kimlik doğrulaması beklenir. Zengin istemci kimlik doğrulama senaryoları, bu makalenin konusu değildir. Bunlar gelecekte bir noktada eklenebilir.

## <a name="prerequisites"></a>Önkoşullar

Azure AD uygulama proxy'si altyapıları veya ortamlar birçok tür olarak dağıtılabilir. Kuruluştan kuruluşa mimarileri değişir. KCD ile ilgili sorunları en yaygın nedenlerini ortamları değildir. Basit yanlış yapılandırmalarını veya genel hatalar çoğu sorunu neden olur.

Bu nedenle, tüm önkoşulların sağlandığından emin olmak en iyi [KCD SSO'su kullanarak uygulama proxy'si](application-proxy-configure-single-sign-on-with-kcd.md) sorunlarını gidermeye başlamadan önce. Kerberos kısıtlanmış temsil yapılandırması 2012R2 üzerinde bölümü unutmayın. Bu işlem, önceki Windows sürümlerinde KCD yapılandırma farklı bir yaklaşım kullanır. Ayrıca bu hususlara dikkatli olun:

- Genel olmayan bir özel etki alanı denetleyicisi (DC) bir güvenli kanal iletişim kutusunu açmak bir etki alanı üye sunucusu değil. Ardından sunucu başka bir iletişim kutusuna taşıyın ve bu da herhangi bir zamanda. Bu nedenle bağlayıcı konakları iletişimi yalnızca belirli yerel site DC'leri ile sınırlı değildir.
- Bir yerel ağ çevre dışında olabilecek DC'leri bağlayıcı konağa doğrudan başvuruların etki alanları arası senaryoları dayanır. Bu durumlarda, bu da ilgili diğer etki alanları temsil eden DC'ler için ileriye doğru trafiği göndermek aynı derecede önemlidir. Aksi durumda, temsilci başarısız olur.
- Mümkünse, tüm etkin IP'ler ya da kimlik cihazlar arasında bağlayıcı konakları ve DC'leri yerleştirme kaçının. Bu cihazlar bazen ana RPC trafiğiyle çok daha zorlayıcı ve kesintiye uğratıyor.

Temsilci seçme içinde basit senaryolar test edin. Size daha fazla değişken tanıtır, daha fazla ile azaltması gerekebilir. Zaman kazanmak için tek bir bağlayıcıyı test sınırlayın. Sorun çözüldükten sonra ek bağlayıcıları ekleyin.

Ayrıca bazı çevresel etmenler konuya katkıda bulunabilir. Bu etkenler önlemek için mümkün olduğunca test sırasında mimarisi en aza indirin. Örneğin, yanlış yapılandırılmış iç güvenlik duvarı ACL'leri ortak. Mümkünse, tüm trafiği bir bağlayıcı aracılığıyla düz DC'leri ve arka uç uygulaması için gönderin.

Bağlayıcılar konumlandırmak için en iyi yeri hedeflerine mümkün olduğunca yakın. Satır içi test ederken yer alan bir güvenlik duvarı gereksiz karmaşıklık ekler ve araştırmalarınıza uzatmak.

Hangi KCD ile ilgili bir sorun gösterir. KCD SSO başarısız olan birkaç yaygın göstergelerden vardır. Bir sorunun ilk açtığında tarayıcıda görüntülenir.

![Örnek: Yanlış KCD yapılandırma hatası](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Örnek: Yetkilendirme eksik izinler nedeniyle başarısız oldu](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Bu görüntülerin her ikisi de aynı belirtiyi göstermektedir: SSO hatası. Uygulama kullanıcı erişimi reddedilir.

## <a name="troubleshooting"></a>Sorun giderme

Sorunlarını nasıl sorun ve siz gözleyin belirtileri bağlıdır. Tüm daha da esnetmenize geçmeden önce aşağıdaki makaleleri keşfedin. Bunlar yararlı sorun giderme bilgileri sağlar:

- [Uygulama proxy'si sorunlarını ve hata iletileri sorunlarını giderme](application-proxy-troubleshoot.md)
- [Kerberos hataları ve belirtileri](application-proxy-troubleshoot.md#kerberos-errors)
- [SSO ile çalışma, şirket içi ve bulut kimlikleri aynı değil](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Bu noktada aldığınız, ardından ana sorunu var demektir. Başlamak için giderebileceğinizi aşağıdaki üç aşaması akışına ayırın.

### <a name="client-pre-authentication"></a>İstemcisi ön kimlik doğrulaması

Dış kullanıcı Azure için bir tarayıcı aracılığıyla kimlik doğrulama. Azure için önceden kimlik doğrulama becerisi sağlamak için işlev KCD SSO için gereklidir. Test ve herhangi bir sorun varsa bu özelliği çözün. Ön kimlik doğrulama aşama KCD veya yayımlanan uygulama için ilişkili değil. Azure'da konu hesabı var olan sağlamlık denetleyerek uyumsuzlukları düzeltmek kolay bir işlemdir. Ayrıca bu denetimi devre dışı veya engellendi. Tarayıcıdaki hata yanıtı nedenini açıklayan tanımlayıcı. Belirsiz doğrulamak için diğer Microsoft sorun giderme makaleleri kontrol edin.

### <a name="delegation-service"></a>Temsilci hizmeti

Bir Kerberos Hizmet biletinin kullanıcılar için bir Kerberos Anahtar Dağıtım Merkezi (KCD) öğesinden alır. Azure Ara sunucusu Bağlayıcısı.

Azure ön uç ile istemci arasında dış iletişimleri KCD üzerinde hiçbir seçtiğiniz vardır. Bu iletişimler yalnızca KCD çalıştığından emin olun. Azure Proxy Hizmeti, bir Kerberos anahtarı almak için kullanılan geçerli bir kullanıcı kimliği sağlanır. Bu Kodsuz KCD mümkün değildir ve başarısız olur.

Daha önce belirtildiği gibi tarayıcı hata iletileri hakkında bir şey neden başarısız iyi bazı ipuçları sağlar. Etkinlik Kimliği ve zaman damgası yanıt Not aldığınızdan emin olun. Bu bilgiler Azure proxy'si olay günlüğü gerçek olaylara davranışı bağıntısını yardımcı olur.

![Örnek: Yanlış KCD yapılandırma hatası](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Olay günlüğünde görülen karşılık gelen girişler 13019 veya 12027 olaylar olarak gösterir. Bağlayıcı olay günlüklerinde **uygulama ve hizmet günlükleri** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt;  **Bağlayıcı** &gt; **yönetici**.

![Uygulama proxy'si olay günlüğünden 13019 olay](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Uygulama proxy'si olay günlüğünden 12027 olay](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Kullanım bir **A** uygulamanın adresi için iç DNS kaydı değil bir **CName**.
1. Bağlayıcı ana bilgisayar belirtilen hedef hesabının SPN için temsilci seçme hakkı verilmiş onaylamasını. Onaylamasını **herhangi bir kimlik doğrulama protokolünü kullan** seçilir. Daha fazla bilgi için [SSO yapılandırma makale](application-proxy-configure-single-sign-on-with-kcd.md).
1. SPN varlığı Azure AD'de, yalnızca bir örneği olduğundan emin olun. Sorunu `setspn -x` herhangi bir etki alanı üyesi konakta bir komut isteminden.
1. Bir etki alanı ilkesi sınırlayan uygulandığını denetleyin [verilen Kerberos belirteçleri en büyük boyutunu](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Bu ilke, aşırı olmasını bulunursa, bir belirteç alma gelen bağlayıcı durdurur.

Bağlayıcı konakla KDC etki alanı arasındaki değişiklikleri yakalayan bir ağ izleme ilgili sorunlar hakkında daha fazla alt düzey ayrıntıları almak için sonraki en iyi adımdır. Daha fazla bilgi için [yakından sorun giderme kağıt](https://aka.ms/proxytshootpaper).

Bilet oluşturma iyi görünüyorsa, uygulama bir 401 döndürdüğünden, kimlik doğrulamanın başarısız belirten günlüklerinde olaya bakın. Bu olay, hedef uygulama'nın, biletini reddetti gösterir. Sonraki aşamaya geçin.

### <a name="target-application"></a>Hedef uygulama

Bağlayıcı tarafından sunulan Kerberos biletini tüketici. Bu aşamada, bağlayıcıyı bir Kerberos Hizmet biletinin arka uca gönderilen beklenir. İlk uygulama istek üstbilgisinde fırsattır.

1. Uygulamanın İç URL Portalı'nda tanımlanan kullanarak, uygulama bağlayıcısı konaktaki doğrudan tarayıcıdan erişilebilir olduğunu doğrulayın. Ardından, başarılı bir şekilde oturum açabilirsiniz. Bağlayıcı ayrıntıları bulunabilir **sorun giderme** sayfası.
1. Yine de bağlayıcı konakta, tarayıcı ve uygulama arasındaki kimlik doğrulaması için Kerberos kullandığını onaylayın. Aşağıdaki eylemlerden birini gerçekleştirin:
1. DevTools çalıştırın (**F12**) Internet Explorer veya [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) bağlayıcı konaktan. İç URL kullanarak uygulamaya gidin. Döndürülen sunulan WWW yetkilendirme üstbilgileri incelemek ya da uygulamadan emin olmak için yanıtta anlaşmasına veya Kerberos varsa.

   - Uygulamaya bir tarayıcıdan yanıtta döndürülen İleri Kerberos blob ile başlayan **YII**. Bu harfler Kerberos çalışıp çalışmadığını bildirir. Microsoft NT LAN Manager (NTLM), diğer yandan, her zaman ile başlayan **TlRMTVNTUAAB**, NTLM Güvenlik Desteği Sağlayıcısı (Base64 kodlaması çözülmüş olduğunda NTLMSSP) okur. Görürseniz **TlRMTVNTUAAB** blob başlangıcında, Kerberos kullanılabilir değil. Görmüyorsanız **TlRMTVNTUAAB**, Kerberos büyük olasılıkla kullanılabilir.

      > [!NOTE]
      > Fiddler'ı kullanırsanız, bu yöntem, Genişletilmiş Koruma'IIS uygulama yapılandırmasında geçici olarak devre dışı gerektirir.

      ![Ağ İnceleme tarayıcının](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Bu şekil blob, ile başlamaz **TIRMTVNTUAAB**. Bu örnekte, Kerberos kullanılabilir ve Kerberos blob, ile başlamaz **YII**.

1. Geçici olarak NTLM IIS sitesinde sağlayıcıları listesinden kaldırın. Uygulama bağlayıcısı ana bilgisayarda Internet Explorer'dan doğrudan erişim. NTLM, artık sağlayıcılar listesinde değil. Yalnızca Kerberos kullanarak uygulamaya erişebilir. Erişim başarısız olursa, uygulamanın yapılandırma ile ilgili bir sorun olabilir. Kerberos kimlik doğrulaması işlev değil.

   - Kerberos kullanılabilir değilse, IIS'de uygulamanın kimlik doğrulama ayarlarını kontrol edin. Emin **anlaş** hemen altındaki NTLM ile üst listelenir. Görürseniz **anlaşma**, **Kerberos veya anlaşma**, veya **PKU2U**, yalnızca Kerberos işlevsel olup olmadığını devam edin.

     ![Windows kimlik doğrulama sağlayıcıları](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Kerberos ve NTLM yerinde, ön kimlik doğrulama Portalı'nda uygulama için geçici olarak devre dışı bırakın. Dış URL'yi kullanarak internet'ten erişmeyi deneyin. Kimlik doğrulaması istenir. Önceki adımda kullanılan hesap ile bunu yapabilirsiniz. Aksi durumda, arka uç uygulaması, değil KCD ile ilgili bir sorun yoktur.
   - Ön kimlik doğrulama Portalı'nda yeniden etkinleştirin. Azure ile kimlik doğrulaması, dış URL aracılığıyla uygulamaya bağlanmak deneyerek. SSO başarısız olursa, tarayıcı ve 13022 olay günlüğünde Yasak hata iletisini görürsünüz:

     *Microsoft AAD Application Proxy Connector, Kerberos kimlik doğrulaması denemeleri ile bir HTTP 401 hata için arka uç sunucu yanıt verir çünkü kullanıcının kimliğini doğrulayamıyor.*

      ![HTTTP 401 yasak hatasını gösterir](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - IIS uygulama denetleyin. Azure AD'de aynı hesabı kullanmak için yapılandırılan uygulama havuzu ve SPN yapılandırılmış olduğundan emin olun. IIS'de aşağıdaki çizimde gösterildiği gibi gidin:

      ![IIS Uygulama Yapılandırması penceresi](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Kimlik bulduktan sonra bu hesap söz konusu SPN ile yapılandırıldığından emin olun. `setspn –q http/spn.wacketywack.com` bunun bir örneğidir. Komut isteminde aşağıdaki metni girin:

      ![SetSPN komut penceresini gösterir](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Uygulamanın ayarları portalda karşı tanımlanmış SPN denetleyin. Azure AD hesabı hedefe karşı yapılandırılmış aynı SPN uygulamanın uygulama havuzu tarafından kullanıldığından emin olun.

      ![Azure portalında SPN yapılandırma](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - IIS ve select biçimine **yapılandırma Düzenleyicisi** uygulama için seçeneği. Gidin **system.webServer/security/authentication/windowsAuthentication**. Değer emin **UseAppPoolCredentials** olduğu **True**.

      ![IIS yapılandırması uygulama havuzları kimlik bilgisi seçeneği](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Bu değeri değiştirmek **True**. Önbelleğe alınan tüm Kerberos anahtarlarını arka uç sunucusundan aşağıdaki komutu çalıştırarak kaldırın:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Daha fazla bilgi için [tüm oturumları için Kerberos istemci bilet önbelleğini temizlemek](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Çekirdek modu etkin değiştirmeden bırakırsanız, Kerberos işlemlerin performansını artırır. Ancak ayrıca makine hesabını kullanarak şifresinin çözülmesi istenen hizmet bileti neden olur. Bu hesap, yerel sistem olarak da adlandırılır. Bu değer kümesine **True** uygulama grubundaki birden fazla sunucu üzerinde barındırıldığında KCD ayırmak için.

- Ek bir denetim devre dışı **Genişletilmiş** koruma çok. Bazı senaryolarda **Genişletilmiş** koruma belirli yapılandırmalarında etkinleştirildiğinde KCD kesildi. Bu gibi durumlarda, uygulama varsayılan Web sitesinin bir alt klasörü olarak yayımlandı. Bu uygulama yalnızca Anonim kimlik doğrulama için yapılandırılmıştır. Tüm iletişim kutuları, alt nesneleri herhangi bir etkin ayarlarını devral mıydı kullandınız. gri görünür. Test, ancak bu değere geri unutmayın öneririz **etkin**, mümkün olduğunda.

  Bu ek onay yayımlanan uygulamanızı kanalında koyar. Temsilci olarak yapılandırılan ek bağlayıcılar yavaşlatabilir. Daha fazla bilgi için daha ayrıntılı teknik gözden geçirme okuma [Azure AD uygulama ara sunucusu sorunlarını giderme](https://aka.ms/proxytshootpaper).

Yine de ilerleme yapamıyorsanız, Microsoft desteği size yardımcı olabilir. Doğrudan portal içinde bir destek bileti oluşturun. Bir mühendisi sizinle iletişim kuracaktır.

## <a name="other-scenarios"></a>Diğer senaryolar

- Azure uygulama proxy'si, uygulamaya kendi isteği göndermeden önce bir Kerberos anahtarı ister. Bazı üçüncü taraf uygulamalar bu kimlik doğrulama yöntemini beğenmez. Bu uygulamalar, daha geleneksel anlaşmaları gerçekleşmesi için bekler. İlk istek uygulama bir 401 destekleyen kimlik doğrulama türleri ile yanıt veren, anonimdir.
- Çoklu atlamalı kimlik doğrulaması genellikle senaryolarda burada bir uygulama, bir arka plan ve ön uç ile katmanlı nerede hem de SQL Server Reporting Services gibi kimlik doğrulaması gerektiren kullanılır. Çoklu atlama senaryosunu yapılandırmak için, bkz. [Kerberos kısıtlanmış temsili Destek makalesi çoklu atlama senaryolarında protokol geçişi gerektirebilir](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Sonraki adımlar

[KCD, yönetilen bir etki alanı yapılandırma](../../active-directory-domain-services/deploy-kcd.md).
