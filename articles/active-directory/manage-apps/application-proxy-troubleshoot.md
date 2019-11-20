---
title: Uygulama Ara sunucusu sorunlarını giderme | Microsoft Docs
description: Azure AD uygulama proxy'sinde hataları gidermek nasıl etkinleştireceğinizi de açıklar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7be9a17bed2a39d16f813332c2d6effc03393264
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812719"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Uygulama proxy'si sorunlarını ve hata iletileri sorunlarını giderme

Uygulama proxy 'Si sorunlarını giderirken, uygulama proxy bağlayıcısının doğru yapılandırılıp yapılandırılmadığını anlamak için sorun giderme akışını gözden geçirmeye, [uygulama proxy Bağlayıcısı sorunlarını ayıklamanıza](application-proxy-debug-connectors.md)başlamanız önerilir. Uygulamaya bağlanırken sorun yaşamaya devam ediyorsanız, [uygulama proxy 'si uygulama sorunlarını ayıklama](application-proxy-debug-apps.md)içindeki sorun giderme akışını izleyin.

Yayımlanan bir uygulamaya erişen veya yayımlama uygulamalarda hata oluşursa, Microsoft Azure AD uygulama ara sunucusu doğru şekilde çalışıp çalışmadığını görmek için aşağıdaki seçeneklerden denetleyin:

* Windows Hizmetleri konsolunu açın. **MICROSOFT AAD uygulama proxy Bağlayıcısı** hizmetinin etkinleştirildiğini ve çalıştığını doğrulayın. Ayrıca uygulama ara sunucusu hizmet özellikleri sayfasında bakın aşağıdaki görüntüde gösterildiği gibi istediğiniz:  
  ![Microsoft AAD Application Proxy Connector özellikleri penceresi ekran görüntüsü](./media/application-proxy-troubleshoot/connectorproperties.png)
* Olay Görüntüleyicisi'ni açın ve uygulama ara sunucusu Bağlayıcısı olayları arayın **uygulama ve hizmet günlükleri** > **Microsoft** > **AadApplicationProxy**  >  **Bağlayıcı** > **yönetici**.
* Gerekli olursa, daha ayrıntılı günlüklere tarafından [uygulama Proxy Bağlayıcısı oturumu açtığında kapatma](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>Sayfa doğru işlenmez
İşleme veya belirli hata iletileri almadan hatalı işlev uygulamanız ile ilgili sorunlar olabilir. Bu makalede yolu yayımlanan ancak uygulama dışında yolun var olduğundan içerik gerektiriyor ortaya çıkabilir.

Örneğin, yol yayımlarsanız `https://yourapp/app` ancak uygulama görüntüleri çağrıları `https://yourapp/media`, bunlar işlenmez. Tüm ilgili içerik dahil etmek için gereken en yüksek düzey yolu kullanarak uygulama yayımlama emin olun. Bu örnekte, bu olacaktır `http://yourapp/`.

Başvurulan içerik, ancak daha ayrıntılı bir bağlantıyı yolunda yerleşmesi kullanıcılar yine yolunuzu değiştirirseniz, blog gönderisine bakın [panelinde ve Office 365 uygulama başlatıcısındaAzureADuygulamaproxy'siuygulamalarıerişmekiçindoğrubağlantıayarını](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Bağlayıcı hataları

Bağlayıcı Sihirbazı'nı yükleme sırasında kayıt başarısız olursa, başarısızlığın nedenini görüntülemek için iki yolu vardır. Ya da konum altında olay günlüğüne **uygulamaları ve Hizmetleri Logs\Microsoft\AadApplicationProxy\Connector\Admin**, veya aşağıdaki Windows PowerShell komutunu çalıştırın:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Olay günlüğünden Bağlayıcısı hata bulduğunuzda, sorunu çözmek için bu tablo yaygın hataların kullanın:

| Hata | Önerilen adımlar |
| ----- | ----------------- |
| Bağlayıcı kaydı başarısız oldu: Azure Yönetim Portalı uygulama proxy 'Sini etkinleştirdiğinizden ve Active Directory Kullanıcı adınızı ve parolanızı doğru girdiğinizden emin olun. Hata: ' Bir veya daha fazla hata oluştu. ' | Azure AD'ye açmadan kayıt pencereyi kapattıysanız, bağlayıcı Sihirbazı'nı yeniden çalıştırın ve bağlayıcıyı kaydedin. <br><br> Kayıt penceresi açılıp daha sonra oturum açmaya izin vermeden hemen kapatırsa bu hatayı alırsınız. Sisteminizde bir ağ hatası olduğunda bu hata oluşur. Tarayıcıdan ortak bir Web sitesine bağlanmak ve bağlantı noktalarının [uygulama proxy önkoşulları](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)'nda belirtilen şekilde açık olduğundan emin olun. |
| NET hata kayıt penceresinde görüntülenir. Devam edilemiyor | Bu hatayı ve ardından pencereyi kapatır, yanlış kullanıcı adı veya parola girildi. Yeniden deneyin. |
| Bağlayıcı kaydı başarısız oldu: Azure Yönetim Portalı uygulama proxy 'Sini etkinleştirdiğinizden ve Active Directory Kullanıcı adınızı ve parolanızı doğru girdiğinizden emin olun. Hata: 'AADSTS50059: İstekte veya belirtilen kimlik bilgileri tarafından ima edilen ya da hizmet sorumlusu URI 'sine göre arama başarısız olan hiçbir kiracı tanımlama bilgisi bulunamadı. | Erişmeye çalıştığınız dizinin kuruluş KIMLIĞININ bir parçası olan bir etki alanı değil, bir Microsoft hesabı kullanarak oturum açmaya çalışıyorsunuz. Yönetici Kiracı etki alanı olarak aynı etki alanı adının bir parçasıdır, örneğin, Azure AD etki alanı contoso.com ise yönetici olmalıdır emin admin@contoso.com. |
| PowerShell komut dosyaları çalıştırmak için geçerli yürütme İlkesi alınamadı. | Bağlayıcı yüklemesi başarısız olursa, PowerShell yürütme ilkesinin devre dışı olmadığından emin olmak için denetleyin. <br><br>1. Grup İlkesi Düzenleyicisi'ni açın.<br>2. Git **Bilgisayar Yapılandırması** > **Yönetim Şablonları** > **Windows bileşenleri**  >   **Windows PowerShell** ve çift **betik yürütmeyi açma**.<br>3. Yürütme ilkesini olarak ayarlanabilir **yapılandırılmadı** veya **etkin**. Varsa kümesine **etkin**seçenekleri altında olduğundan emin olun, yürütme İlkesi ayarlandığından **yerel ve uzak imzalanmış komut dosyalarını izin** veya **tüm betiklere izin vermek**. |
| Bağlayıcı yapılandırması yüklenemedi. | Kimlik doğrulaması için kullanılır, bağlayıcının istemci sertifikasının süresi doldu. Bir proxy'nin arkasında yüklü bağlayıcı varsa bu da oluşabilir. Bu durumda, bağlayıcı Internet'e erişemez ve uzak kullanıcılar uygulamalara sağlamak mümkün olmayacaktır. Güven kullanarak el ile yenileme `Register-AppProxyConnector` Windows PowerShell cmdlet'i. Bağlayıcınızı bir proxy'nin arkasındayken, Internet erişimi Bağlayıcısı hesaplarına "Ağ Hizmetleri" ve "yerel sistem." vermek gerekli değildir Bu, bunları ara sunucuya erişim verme veya bunları proxy atlama ayarlayarak gerçekleştirilebilir. |
| Bağlayıcı kaydı başarısız oldu: Bağlayıcıyı kaydettirmek için Active Directory bir uygulama yöneticisi olduğunuzdan emin olun. Hata: ' Kayıt isteği reddedildi. ' | Bir yönetici bu etki alanında oturum açmada çalıştığınız diğer ad değil. Bağlayıcınızı kullanıcının etki alanı sahibi olan dizin için her zaman yüklenir. Oturum açmaya çalıştığınız yönetici hesabının Azure AD kiracısı için en az uygulama Yöneticisi izinlerine sahip olduğundan emin olun. |
| Bağlayıcı, ağ sorunları nedeniyle hizmete bağlanamadı. Bağlayıcı aşağıdaki URL 'ye erişmeyi denedi. | Bağlayıcı, uygulama proxy 'Si bulut hizmetine bağlanamıyor. Bağlantıyı engelleyen bir güvenlik duvarı kuralınız varsa bu durum oluşabilir. [Uygulama proxy 'si önkoşulları](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)'nda listelenen doğru bağlantı noktalarına ve URL 'lere erişim izni verildiğinden emin olun. |

## <a name="kerberos-errors"></a>Kerberos hataları

Bu tablo, Kerberos Kurulum ve yapılandırma gelen daha yaygın hataları kapsar ve çözümleme için öneriler içerir.

| Hata | Önerilen adımlar |
| ----- | ----------------- |
| PowerShell komut dosyaları çalıştırmak için geçerli yürütme İlkesi alınamadı. | Bağlayıcısı yüklemesi başarısız olursa, PowerShell yürütme ilkesini devre dışı olduğunu emin olmak için kontrol edin.<br><br>1. Grup İlkesi Düzenleyicisi'ni açın.<br>2. Git **Bilgisayar Yapılandırması** > **Yönetim Şablonları** > **Windows bileşenleri**  >   **Windows PowerShell** ve çift **betik yürütmeyi açma**.<br>3. Yürütme ilkesini olarak ayarlanabilir **yapılandırılmadı** veya **etkin**. Varsa kümesine **etkin**seçenekleri altında olduğundan emin olun, yürütme İlkesi ayarlandığından **yerel ve uzak imzalanmış komut dosyalarını izin** veya **tüm betiklere izin vermek**. |
| 12008 - azure AD arka uç sunucusu izin verilen Kerberos kimlik doğrulaması girişimi sınırını aştı. | Bu hata Azure AD arasında yapılandırması yanlış gösterebilir ve arka uç uygulama sunucusu veya her iki makinede saat ve tarih yapılandırmasında bir sorun. Arka uç sunucusu, Azure AD tarafından oluşturulan Kerberos biletini reddetti. Doğrulayın, Azure AD ve arka uç uygulama sunucusu doğru şekilde yapılandırılır. Emin olun Azure AD saat ve tarih yapılandırmasına ve arka uç uygulama sunucusu eşitlenir. |
| 13016 - sınır belirteci veya erişim tanımlama bilgisi UPN olduğundan azure AD kullanıcı adına bir Kerberos anahtarı alınamıyor. | STS yapılandırmasında bir sorun yoktur. UPN talep yapılandırması içinde STS düzeltin. |
| 13019 - azure AD, aşağıdaki genel API hatası nedeniyle kullanıcı adına bir Kerberos anahtarı alınamıyor. | Bu olay, Azure AD arasında yapılandırması yanlış gösterebilir ve etki alanı denetleyicisi sunucusuna veya her iki makinede saat ve tarih yapılandırmasında bir sorun. Etki alanı denetleyicisi, Azure AD tarafından oluşturulan Kerberos biletini reddetti. Doğrulayın, Azure AD ve arka uç uygulama sunucusu özellikle SPN yapılandırma doğru yapılandırılmış. Azure AD etki alanı denetleyicisi güven Azure AD ile başlatmasını sağlamak etki alanı denetleyicisi aynı etki alanına katılmış olduğundan emin olun. Emin olun Azure AD saat ve tarih yapılandırmasına ve etki alanı denetleyicisi eşitlenir. |
| 13020 - arka uç sunucu SPN'si tanımlanmadığından azure AD kullanıcı adına bir Kerberos anahtarı alınamıyor. | Bu olay, Azure AD arasında yapılandırması yanlış gösterebilir ve etki alanı denetleyicisi sunucusuna veya her iki makinede saat ve tarih yapılandırmasında bir sorun. Etki alanı denetleyicisi, Azure AD tarafından oluşturulan Kerberos biletini reddetti. Doğrulayın, Azure AD ve arka uç uygulama sunucusu özellikle SPN yapılandırma doğru yapılandırılmış. Azure AD etki alanı denetleyicisi güven Azure AD ile başlatmasını sağlamak etki alanı denetleyicisi aynı etki alanına katılmış olduğundan emin olun. Emin olun Azure AD saat ve tarih yapılandırmasına ve etki alanı denetleyicisi eşitlenir. |
| 13022 - Kerberos kimlik doğrulaması denemeleri ile bir HTTP 401 hata için arka uç sunucu yanıt verir çünkü azure AD kullanıcı kimlik doğrulaması yapamaz. | Bu olay, Azure AD arasında yapılandırması yanlış gösterebilir ve arka uç uygulama sunucusu veya her iki makinede saat ve tarih yapılandırmasında bir sorun. Arka uç sunucusu, Azure AD tarafından oluşturulan Kerberos biletini reddetti. Doğrulayın, Azure AD ve arka uç uygulama sunucusu doğru şekilde yapılandırılır. Emin olun Azure AD saat ve tarih yapılandırmasına ve arka uç uygulama sunucusu eşitlenir. Daha fazla bilgi için [sorun giderme Kerberos Kısıtlı temsilci yapılandırmaları için uygulama proxy'si](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Son kullanıcı hataları

Bu liste, uygulamaya erişmek ve başarısız çalıştıklarında son kullanıcılarınızın karşılaşabileceği hatalar kapsar. 

| Hata | Önerilen adımlar |
| ----- | ----------------- |
| Web sitesi, sayfa görüntülenemiyor. | Kullanıcı, uygulama bir IWA uygulamasıysa, yayımladığınız uygulamayı erişmeye çalışırken bu hatayı alabilirsiniz. Bu uygulama için tanımlanmış SPN yanlış olabilir. IWA uygulamalar için bu uygulama için yapılandırılan SPN doğru olduğundan emin olun. |
| Web sitesi, sayfa görüntülenemiyor. | Kullanıcı, uygulama bir OWA uygulama ise, yayımladığınız uygulamayı erişmeye çalışırken bu hatayı alabilirsiniz. Bunun nedeni aşağıdakilerden biri olabilir:<br><li>Bu uygulama için tanımlanmış SPN doğru değil. Bu uygulama için yapılandırılan SPN doğru olduğundan emin olun.</li><li>Uygulamaya erişmeyi denedi kullanıcının oturum açmak için kullanıyor uygun şirket hesabı yerine bir Microsoft hesabı veya Konuk kullanıcı kullanıcıdır. Yayımlanan uygulama etki alanını eşleşen kullanıcıların Kurumsal hesap kullanarak oturum açtığında emin olun. Microsoft Account kullanıcıları ve Konuk IWA uygulamaları erişemez.</li><li>Uygulamaya erişmeye çalışan Kullanıcı, şirket içi tarafında bu uygulama için düzgün tanımlanmamıştır. Bu kullanıcının şirket içi makinede bu arka uç uygulaması için tanımlanan uygun izinlere sahip olduğundan emin olun. |
| Bu Kurumsal uygulama erişilemez. Bu uygulamaya erişmek için yetkiniz yok. Yetkilendirme başarısız oldu. Bu uygulamaya erişimi olan kullanıcı atadığınızdan emin olun. | Kullanıcılarınız, şirket hesabı yerine Microsoft hesapları kullanıyorsa yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. Konuk kullanıcılar da bu hatayı alabilirsiniz. Microsoft Account kullanıcılar ve konuklar IWA uygulamaları erişemez. Yayımlanan uygulama etki alanını eşleşen kullanıcıların Kurumsal hesap kullanarak oturum açtığında emin olun.<br><br>Bu uygulama için kullanıcı atanmamış. Git **uygulama** sekmesinde altında **kullanıcılar ve gruplar**, bu uygulama için bu kullanıcı veya kullanıcı grubuna atayın. |
| Şirket bu uygulama şu anda erişilemiyor. Lütfen daha sonra yeniden deneyin... Bağlayıcı zaman aşımına uğradı. | Kullanıcılarınız şirket içi tarafında bu uygulama için doğru tanımlanmamışsa, yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. Kullanıcılarınızın şirket içi makinede bu arka uç uygulaması için tanımlanan uygun izinlere sahip olduğundan emin olun. |
| Bu Kurumsal uygulama erişilemez. Bu uygulamaya erişmek için yetkiniz yok. Yetkilendirme başarısız oldu. Kullanıcının Azure Active Directory Premium için bir lisansa sahip olduğundan emin olun. | Kullanıcılarınız, abone yöneticisinin bir Premium lisansıyla açıkça atanmamışsa, yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. Abonenin Active Directory **Lisansları** sekmesine gidin ve bu kullanıcı veya kullanıcı grubuna bir Premium Lisansı atandığından emin olun. |
| Belirtilen ana bilgisayar adına sahip bir sunucu bulunamadı. | Bu hata, uygulamanın özel etki alanı doğru yapılandırılmamışsa yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. [Azure 'da özel etki alanları Ile çalışma](application-proxy-configure-custom-domain.md) bölümündeki adımları izleyerek etki alanı için bir sertifika YÜKLEDIĞINIZDEN ve DNS kaydını doğru şekilde yapılandırdığınızdan emin olun ad uygulama ara sunucusu |

## <a name="my-error-wasnt-listed-here"></a>My hata burada listelenen değildi

Bir hata veya sorun listede bu sorun giderme Kılavuzu'nda Azure AD uygulama proxy'si ile karşılaşırsanız, bu konuda fikrinizi öğrenmek isteriz. Bir e-posta Gönder bizim [geri bildirim ekibi](mailto:aadapfeedback@microsoft.com) karşılaştığınız hata ayrıntılarını içeren.

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Active Directory Uygulama Ara sunucusunu etkinleştirme](application-proxy-add-on-premises-application.md)
* [Uygulama Ara sunucusu ile uygulama yayımlama](application-proxy-add-on-premises-application.md)
* [Çoklu oturum açmayı etkinleştirin](application-proxy-configure-single-sign-on-with-kcd.md)
* [Koşullu erişimi etkinleştir](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
