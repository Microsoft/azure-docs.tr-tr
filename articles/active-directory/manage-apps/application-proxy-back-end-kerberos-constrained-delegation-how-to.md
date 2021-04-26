---
title: Kerberos kısıtlamalı yetkilendirme sorunlarını giderme-uygulama proxy 'Si
description: Uygulama proxy 'Si için Kerberos kısıtlanmış temsil yapılandırmalarının sorunlarını giderme
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: b8562f3bdd82b5b0c2c1340f511f87ad90dfbe3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487959"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Uygulama Ara Sunucusu için Kerberos kısıtlanmış temsil yapılandırmalarının sorunlarını giderme 

Yayımlanmış uygulamalara SSO sağlamak için kullanılabilen yöntemler, bir uygulamadan diğerine farklılık gösterebilir. Azure Active Directory (Azure AD) uygulama proxy 'Sinin varsayılan olarak sunduğu bir seçenek, Kerberos kısıtlı temsili (KCD). Kullanıcılarınız için bir bağlayıcıyı, arka uç uygulamalarında kısıtlanmış Kerberos kimlik doğrulaması çalıştıracak şekilde yapılandırabilirsiniz.

KCD 'YI etkinleştirme yordamı basittir. Bu, SSO 'yu destekleyen çeşitli bileşenler ve kimlik doğrulama akışının genel olarak anlaşılmasına gerek yoktur. Ancak bazen, KCD SSO beklendiği gibi çalışmaz. Bu senaryoların sorunlarını gidermek için iyi bilgi kaynaklarına ihtiyacınız vardır.

Bu makalede, sorun gidermeye yardımcı olan ve en yaygın sorunlardan bazılarını kendi kendine düzeltebileceğiniz tek bir başvuru noktası sunulmaktadır. Ayrıca daha karmaşık uygulama sorunlarının tanılamasını da içerir.

Bu makalede aşağıdaki varsayımlar yapılır:

- [Uygulama proxy 'si ile çalışmaya başlama](application-proxy-add-on-premises-application.md) başına Azure AD uygulama ara sunucusu dağıtımı ve KCD olmayan uygulamalara genel erişim beklendiği gibi çalışır.
- Yayımlanan hedef uygulama Internet Information Services (IIS) ve Microsoft 'un Kerberos uygulamasını temel alır.
- Sunucu ve uygulama Konakları tek bir Azure Active Directory etki alanında bulunur. Etki alanları arası ve orman senaryolarıyla ilgili ayrıntılı bilgi için, bkz. [KCD teknik incelemesi](https://aka.ms/KCDPaper).
- Konu uygulaması, ön kimlik doğrulama etkin olan bir Azure kiracısında yayımlanır. Kullanıcıların, form tabanlı kimlik doğrulaması aracılığıyla Azure 'da kimlik doğrulaması yapması beklenir. Zengin istemci kimlik doğrulama senaryoları Bu makalede ele alınmıştır. Gelecekte bir noktada eklenebilirler.

## <a name="prerequisites"></a>Önkoşullar

Azure AD Uygulama Ara Sunucusu, birçok tür altyapıya da ortamda dağıtılabilir. Mimariler kuruluştan kuruluşa farklılık gösterir. KCD ile ilgili sorunların en yaygın nedenleri ortamlar değildir. Basit yanlış yapılandırma veya genel hatalar çoğu soruna neden olur.

Bu nedenle, sorun gidermeye başlamadan önce [uygulama proxy 'si Ile KCD SSO 'Yu kullanarak](application-proxy-configure-single-sign-on-with-kcd.md) tüm önkoşulları karşıladığınızdan emin olmak en iyisidir. 2012R2 üzerinde Kerberos kısıtlanmış temsilciyi yapılandırma bölümüne göz önünde edin. Bu işlem, önceki Windows sürümlerinde KCD 'yi yapılandırmaya yönelik farklı bir yaklaşım kullanır. Ayrıca, aşağıdaki noktalara dikkat edin:

- Bir etki alanı üyesi sunucusunun belirli bir etki alanı denetleyicisi (DC) ile güvenli bir kanal iletişim kutusu açması yaygın olmayan bir durumdur. Daha sonra sunucu, belirli bir zamanda başka bir iletişim kutusuna geçebilir. Bu nedenle, bağlayıcı Konakları yalnızca belirli yerel site DC 'Ler ile iletişim ile sınırlı değildir.
- Etki alanları arası senaryolar, bağlayıcı ana bilgisayarını yerel ağ çevre birimi dışında olabilecek DC 'lere yönlendirme başvurularına dayanır. Bu gibi durumlarda, trafiği diğer ilgili etki alanlarını temsil eden DC 'lere de göndermek aynı şekilde önemlidir. Aksi takdirde, yetkilendirme başarısız olur.
- Mümkün olduğunda, bağlayıcı konakları ve DC 'Ler arasında etkin IP 'ler veya KIMLIK cihazları yerleştirmekten kaçının. Bu cihazlar bazen ana RPC trafiğiyle çok daha zorlayıcı ve kesintiye uğratıyor.

Basit senaryolarda temsilciyi test edin. Daha fazla değişken, daha fazla bilgi sahibi olmanız gerekebilir. Zaman kazanmak için testinizi tek bir bağlayıcıya sınırlayın. Sorun çözümlendikten sonra ek bağlayıcılar ekleyin.

Bazı ortam faktörleri de bir sorunla katkıda bulunabilir. Bu faktörlerden kaçınmak için, test sırasında mimarinin mümkün olduğunca en aza indirin. Örneğin, yanlış yapılandırılmış iç güvenlik duvarı ACL 'Leri yaygındır. Mümkünse, bir bağlayıcıdan gelen tüm trafiği DC ve arka uç uygulamasına doğrudan gönderin.

Bağlayıcıların en iyi konumu, hedefleri için mümkün olduğunca yakın bir yerdir. Sınama, gereksiz karmaşıklık ekliyor ve araştırmalarınızı probmek için satır içi olarak bulunan bir güvenlik duvarı.

Bir KCD sorunu gösteriliyor? KCD SSO 'nun başarısız olduğu bazı yaygın göstergeler vardır. Bir sorunun ilk işareti tarayıcıda görüntülenir.

![Yanlış bir K C D yapılandırma hatasına bir örnek gösteren, "yanlış Kerberos kısıtlı temsilcisi..." hatasıyla bir örnek gösteren ekran görüntüsü Bunun.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Örnek: eksik izinler nedeniyle yetkilendirme başarısız oldu](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Bu görüntülerin her ikisi de aynı belirtiyi gösterir: SSO hatası. Uygulamaya Kullanıcı erişimi reddedildi.

## <a name="troubleshooting"></a>Sorun giderme

Sorunun nasıl giderileceği soruna ve gözlemlediğiniz belirtilere bağlıdır. Herhangi bir şekilde devam etmeden önce aşağıdaki makaleleri araştırın. Bunlar yararlı sorun giderme bilgileri sağlar:

- [Uygulama Proxy'si sorunlarını ve hata iletilerini giderme](application-proxy-troubleshoot.md)
- [Kerberos hataları ve belirtileri](application-proxy-troubleshoot.md#kerberos-errors)
- [Şirket içi ve bulut kimlikleri aynı olmadığında SSO ile çalışma](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Bu noktayı aldıysanız, ana sorununuz var demektir. Başlamak için akışı, sorunu giderebileceğiniz aşağıdaki üç aşamaya ayırın.

### <a name="client-pre-authentication"></a>İstemci ön kimlik doğrulaması

Azure 'da bir tarayıcı aracılığıyla kimlik doğrulaması yapan dış Kullanıcı. KCD SSO 'nun çalışması için Azure 'da önceden kimlik doğrulaması yapma özelliği gereklidir. Herhangi bir sorun varsa test edin ve bu özelliği çözün. Ön kimlik doğrulama aşaması, KCD veya yayımlanan uygulamayla ilgili değildir. Konu hesabının Azure 'da mevcut olup olmadığını kontrol ederek tüm tutarsızlıkları düzeltmek kolaydır. Ayrıca, devre dışı bırakılıp engellenmediğinden emin olun. Tarayıcıdaki hata yanıtı, sorunun açıklanabileceği açıklayıcı bir açıklama. Emin değilseniz, doğrulamak üzere diğer Microsoft sorun giderme makalelerini kontrol edin.

### <a name="delegation-service"></a>Temsili hizmeti

Kerberos Anahtar Dağıtım Merkezi (KCD) kullanıcıları için bir Kerberos hizmet bileti alan Azure proxy Bağlayıcısı.

İstemci ile Azure ön ucu arasındaki dış iletişimde KCD üzerinde hiçbir pul yoktur. Bu iletişimler yalnızca KCD 'nin çalıştığından emin olun. Azure proxy hizmeti, Kerberos bileti almak için kullanılan geçerli bir kullanıcı KIMLIĞI sağladı. Bu KIMLIK olmadığında KCD mümkün değildir ve başarısız olur.

Daha önce belirtildiği gibi, tarayıcı hata iletileri neden başarısız olduğu konusunda bazı yararlı ipuçları sağlar. Yanıtta etkinlik KIMLIĞI ve zaman damgasını girdiğinizden emin olun. Bu bilgiler, Azure proxy olay günlüğündeki gerçek olaylarla davranışı ilişkilendirmenize yardımcı olur.

![Örnek: yanlış KCD yapılandırma hatası](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Olay günlüğünde görülen karşılık gelen girişler, 13019 veya 12027 olaylarını gösterir. **Uygulama ve hizmet günlüklerinde** bağlayıcı olay günlüklerini bulma &gt; **Microsoft** &gt; **aadapplicationproxy** &gt; **bağlayıcı** &gt; **Yöneticisi**.

![Uygulama proxy 'Si olay günlüğünden olay 13019](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Uygulama proxy 'Si olay günlüğünden olay 12027](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. **CNAME** değil, uygulamanın adresi IÇIN iç DNS 'Niz içindeki **bir** kaydı kullanın.
1. Bağlayıcı ana bilgisayarına belirtilen hedef hesabın SPN 'sine temsilci atanmış hakkı verildiğini yeniden onaylayın. **Herhangi bir kimlik doğrulama protokolünü kullan** ' ın seçili olduğunu yeniden onaylayın. Daha fazla bilgi için bkz. [SSO yapılandırma makalesi](application-proxy-configure-single-sign-on-with-kcd.md).
1. Azure AD 'de mevcut olan SPN 'nin yalnızca bir örneğinin olduğunu doğrulayın. `setspn -x`Herhangi bir etki alanı üyesi konağında bir komut isteminden sorun.
1. [Verilen Kerberos belirteçlerinin maksimum boyutunu](/archive/blogs/askds/maxtokensize-and-windows-8-and-windows-server-2012)sınırlayan bir etki alanı ilkesinin zorlandığını denetleyin. Bu ilke, bağlayıcının aşırı olduğu bulunursa bir belirteç alınmasını engeller.

Bağlayıcı ana bilgisayarı ve etki alanı KDC arasındaki alışverişleri yakalayan bir ağ izlemesi, sorunlar hakkında daha düşük düzeyde ayrıntı elde etmek için bir sonraki en iyi adımdır. Daha fazla bilgi için bkz. [ayrıntılı sorun giderme](https://aka.ms/proxytshootpaper)sayfası.

Bilet iyi görünüyorsa, uygulama bir 401 döndürdüğünden, kimlik doğrulamasının başarısız olduğunu belirten günlüklerde bir olay görürsünüz. Bu olay hedef uygulamanın biletinizi reddettiğini belirtir. Sonraki aşamaya gidin.

### <a name="target-application"></a>Hedef uygulama

Bağlayıcı tarafından sağlanmış Kerberos biletinin tüketicisi. Bu aşamada, bağlayıcının arka uca bir Kerberos hizmeti bileti gönderilmesini bekler. Bu bilet, ilk uygulama isteğindeki bir üst bilgi.

1. Portalda tanımlanan iç URL 'yi kullanarak, uygulamanın bağlayıcı ana bilgisayarındaki tarayıcıdan doğrudan erişilebilir olduğunu doğrulayın. Ardından başarıyla oturum açabilirsiniz. Ayrıntılar bağlayıcı **sorun giderme** sayfasında bulunabilir.
1. Hala bağlayıcı konağında, tarayıcı ve uygulama arasındaki kimlik doğrulamasının Kerberos kullandığını doğrulayın. Aşağıdaki eylemlerden birini uygulayın:
1. Internet Explorer 'da DevTools (**F12**) öğesini çalıştırın veya bağlayıcı ana bilgisayardan [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) 'ı kullanın. İç URL 'YI kullanarak uygulamaya gidin. Negotiate veya Kerberos 'un mevcut olduğundan emin olmak için uygulamadan alınan yanıtta döndürülen, sunulan WWW yetkilendirme üst bilgilerini inceleyin.

   - Tarayıcıdan uygulamaya yapılan yanıtta döndürülen bir sonraki Kerberos blobu, **yıIı** ile başlar. Bu mektuplar, Kerberos 'un çalıştığını söyler. Diğer yandan Microsoft NT LAN Manager (NTLM), her zaman, Base64 'ten kodu çözülerek NTLM güvenlik desteği sağlayıcısı 'nı (NTLMSSP) okuyan **Tlrmtvntuaab** ile başlar. Blob başlangıcında **Tlrmtvntuaab** görürseniz, Kerberos kullanılabilir değildir. **Tlrmtvntuaab**' yi görmüyorsanız, Kerberos büyük olasılıkla kullanılabilir.

      > [!NOTE]
      > Fiddler kullanırsanız, bu yöntem uygulamanın IIS 'deki yapılandırmasında genişletilmiş korumayı geçici olarak devre dışı bırakmanızı gerektirir.

      ![Tarayıcı ağ İnceleme penceresi](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Bu şekildeki blob, **Timtvntuaab** ile başlamaz. Bu nedenle, Kerberos kullanılabilir ve Kerberos blobu **III** ile başlamaz.

1. NTLM 'yi IIS sitesindeki sağlayıcılar listesinden geçici olarak kaldırın. Uygulamaya doğrudan bağlayıcı ana bilgisayarındaki Internet Explorer 'dan erişin. NTLM artık sağlayıcılar listesinde değil. Uygulamaya yalnızca Kerberos kullanarak erişebilirsiniz. Erişim başarısız olursa, uygulamanın yapılandırmasıyla ilgili bir sorun olabilir. Kerberos kimlik doğrulaması çalışmıyor.

   - Kerberos kullanılamıyorsa, uygulamanın IIS 'de kimlik doğrulama ayarlarını kontrol edin. **Negotiate** 'in hemen altındaki NTLM ile en üstte listelendiğinden emin olun. **Negotiate**, **Kerberos veya Negotiate** ya da **PKU2U**' u görmüyorsanız, yalnızca Kerberos çalışır ise devam edin.

     ![Windows kimlik doğrulama sağlayıcıları](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Etkin Kerberos ve NTLM ile portalda uygulama için ön kimlik doğrulamasını geçici olarak devre dışı bırakın. Dış URL 'YI kullanarak İnternet 'ten erişmeyi deneyin. Kimlik doğrulamanız istenir. Bu şekilde, önceki adımda kullanılan aynı hesapla bunu yapabilirsiniz. Aksi takdirde, KCD değil arka uç uygulamasıyla ilgili bir sorun var.
   - Portalda ön kimlik doğrulamayı yeniden etkinleştirin. Uygulamaya dış URL aracılığıyla bağlanmayı deneyerek Azure üzerinden kimlik doğrulaması yapın. SSO başarısız olursa, tarayıcıda yasaklanmış bir hata iletisi ve günlükte olay 13022 ' u görürsünüz:

     *Arka uç sunucusu HTTP 401 hatası ile Kerberos kimlik doğrulaması denemesine yanıt verdiği için Microsoft AAD uygulama proxy bağlayıcısı kullanıcının kimliğini doğrulayamadı.*

      ![HTTTP 401 yasak hatasını gösterir](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - IIS uygulamasını kontrol edin. Yapılandırılmış uygulama havuzunun ve SPN 'nin Azure AD 'de aynı hesabı kullanacak şekilde yapılandırıldığından emin olun. Aşağıdaki çizimde gösterildiği gibi IIS 'de gezinin:

      ![IIS uygulama yapılandırma penceresi](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Kimliği öğrendikten sonra bu hesabın söz konusu SPN ile yapılandırıldığından emin olun. `setspn –q http/spn.wacketywack.com` bunun bir örneğidir. Komut istemine aşağıdaki metni girin:

      ![SetSPN komut penceresini gösterir](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Portaldaki uygulama ayarlarına karşı tanımlanan SPN 'YI denetleyin. Hedef Azure AD hesabında aynı SPN 'nin yapılandırıldığından, uygulamanın uygulama havuzu tarafından kullanıldığından emin olun.

      ![Azure portal SPN yapılandırması](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - IIS 'ye gidin ve uygulama için **yapılandırma Düzenleyicisi** seçeneğini belirleyin. **System. webserver/Security/Authentication/WindowsAuthentication** sayfasına gidin. **UseAppPoolCredentials** değerinin **doğru** olduğundan emin olun.

      ![IIS yapılandırma uygulama havuzları kimlik bilgileri seçeneği](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Bu değeri **true** olarak değiştirin. Aşağıdaki komutu çalıştırarak tüm önbelleğe alınmış Kerberos biletlerini arka uç sunucusundan kaldırın:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Daha fazla bilgi için bkz. [tüm oturumlar Için Kerberos istemci bileti önbelleğini temizleme](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Çekirdek modu etkin bırakırsanız, Kerberos işlemlerinin performansını geliştirir. Ancak, makine hesabı kullanılarak istenen hizmetin biletinin şifresinin çözülmesi de neden olur. Bu hesap yerel sistem olarak da adlandırılır. Uygulama bir gruptaki birden fazla sunucu üzerinde barındırıldığında KCD 'yi kesmek için bu değeri **true** olarak ayarlayın.

- Ek bir denetim olarak, **genişletilmiş** korumayı da devre dışı bırakın. Bazı senaryolarda, **genişletilmiş** koruma, belirli yapılandırmalarda etkinleştirildiğinde KCD 'yi brocağız. Bu durumlarda, bir uygulama varsayılan Web sitesinin bir alt klasörü olarak yayımlanmıştır. Bu uygulama yalnızca anonim kimlik doğrulaması için yapılandırılmış. Tüm iletişim kutuları gri renkte bulunur ve bu da alt nesnelerin etkin ayarları devralmasını önerir. Test etmenizi öneririz, ancak mümkün olduğunda bu değeri **etkin** olarak geri yüklemeyi unutmayın.

  Bu ek denetim, yayımlanan uygulamanızı kullanmak için sizi izlemenizi sağlar. Ayrıca, temsilci olarak yapılandırılmış ek bağlayıcılar çalıştırabilirsiniz. Daha fazla bilgi için daha ayrıntılı teknik inceleme ve [Azure AD uygulama ara sunucusu sorunlarını giderme](https://aka.ms/proxytshootpaper)makalesini okuyun.

Hala ilerleme yapadıysanız Microsoft desteği size yardımcı olabilir. Doğrudan Portal içinde bir destek bileti oluşturun. Bir mühendis sizinle iletişim kuracaktır.

## <a name="other-scenarios"></a>Diğer senaryolar

- Azure uygulama proxy 'Si, isteği bir uygulamaya göndermeden önce Kerberos bileti ister. Bazı üçüncü taraf uygulamalar bu kimlik doğrulama yöntemini beğenmez. Bu uygulamalar, daha geleneksel anlaşmaların gerçekleşmesini bekler. İlk istek anonimdir ve bu, uygulamanın bir 401 aracılığıyla desteklediği kimlik doğrulama türleriyle yanıt vermesini sağlar. Bu tür bir Kerberos anlaşması, bu belgede özetlenen adımlar kullanılarak etkinleştirilebilir: [Çoklu oturum açma Için Kerberos kısıtlanmış temsili](application-proxy-configure-single-sign-on-with-kcd.md).
- Çoklu atlama kimlik doğrulaması, genellikle bir uygulamanın katmanlı olduğu senaryolarda, her ikisi de SQL Server Reporting Services gibi kimlik doğrulaması gerektiren bir arka uç ve ön uç ile kullanılır. Daha ayrıntılı bilgi için bkz. [Web kaydı proxy sayfaları Için Kerberos kısıtlanmış temsil 'nı yapılandırma](/troubleshoot/windows-server/identity/configure-kerberos-constrained-delegation).

## <a name="next-steps"></a>Sonraki adımlar

[Yönetilen bir etki alanında KCD 'Yi yapılandırın](../../active-directory-domain-services/deploy-kcd.md).
