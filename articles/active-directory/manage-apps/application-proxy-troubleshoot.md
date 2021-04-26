---
title: Uygulama ara sunucusu sorunlarını giderme | Microsoft Docs
description: Azure AD Uygulama Ara Sunucusu 'de hata giderme konularını ele alır.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cb8fb0e194b4c43b5e247f2ea5d1e38d924591db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257972"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Uygulama Proxy'si sorunlarını ve hata iletilerini giderme

Uygulama proxy 'Si sorunlarını giderirken, uygulama proxy bağlayıcısının doğru yapılandırılıp yapılandırılmadığını anlamak için sorun giderme akışını gözden geçirmeye, [uygulama proxy Bağlayıcısı sorunlarını ayıklamanıza](application-proxy-debug-connectors.md)başlamanız önerilir. Uygulamaya bağlanırken sorun yaşamaya devam ediyorsanız, [uygulama proxy 'si uygulama sorunlarını ayıklama](application-proxy-debug-apps.md)içindeki sorun giderme akışını izleyin.

Yayımlanan bir uygulamaya veya yayımlama uygulamalarına erişmede hata oluşursa, Microsoft Azure AD uygulama proxy 'Sinin düzgün çalışıp çalışmadığını görmek için aşağıdaki seçenekleri kontrol edin:

* Windows Hizmetleri konsolunu açın. **MICROSOFT AAD uygulama proxy Bağlayıcısı** hizmetinin etkinleştirildiğini ve çalıştığını doğrulayın. Aşağıdaki görüntüde gösterildiği gibi, uygulama proxy hizmeti özellikleri sayfasına da bakmak isteyebilirsiniz:  
  ![Microsoft AAD uygulama proxy Bağlayıcısı Özellikler penceresi ekran görüntüsü](./media/application-proxy-troubleshoot/connectorproperties.png)
* Olay Görüntüleyicisi açın ve **uygulamalar ve hizmetler günlüklerinde** uygulama proxy Bağlayıcısı olaylarını arayın  >  **Microsoft**  >  **aadapplicationproxy**  >  **bağlayıcı**  >  **Yöneticisi**.
* Gerekirse, [uygulama proxy Bağlayıcısı oturum günlüklerini etkinleştirerek](application-proxy-connectors.md#under-the-hood)daha ayrıntılı Günlükler kullanılabilir.

## <a name="the-page-is-not-rendered-correctly"></a>Sayfa doğru şekilde işlenmez
Belirli hata iletileri almadan, uygulamanız üzerinde işleme veya hatalı çalışmaya yönelik sorunlar yaşayabilirsiniz. Bu, makale yolunu yayımladıysanız oluşabilir, ancak uygulama o yolun dışında bulunan içeriği gerektiriyorsa.

Örneğin, yolu yayımlarsanız, `https://yourapp/app` ancak uygulama içindeki görüntüleri çağırıyorsa `https://yourapp/media` , işlenmez. Uygulamayı, tüm ilgili içeriği dahil etmek için gereken en yüksek düzey yolu kullanarak yayımladığınızdan emin olun. Bu örnekte, olacaktır `http://yourapp/` .

## <a name="connector-errors"></a>Bağlayıcı hataları

Bağlayıcı Sihirbazı yüklemesi sırasında kayıt başarısız olursa, hatanın nedenini görüntülemenin iki yolu vardır. **Uygulamalar ve hizmetler Logs\Microsoft\AadApplicationProxy\Connector\Admin** altında olay günlüğüne bakın veya aşağıdaki Windows PowerShell komutunu çalıştırın:

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

Olay günlüğünden bağlayıcı hatasını bulduktan sonra, sorunu gidermek için bu ortak hata tablosunu kullanın:

| Hata | Önerilen adımlar |
| ----- | ----------------- |
| Bağlayıcı kaydı başarısız oldu: Azure Yönetim Portalı uygulama proxy 'Sini etkinleştirdiğinizden ve Active Directory Kullanıcı adınızı ve parolanızı doğru girdiğinizden emin olun. Hata: ' bir veya daha fazla hata oluştu. ' | Azure AD 'de oturum açmadan kayıt penceresini kapattıysanız, bağlayıcı Sihirbazı 'nı yeniden çalıştırın ve bağlayıcıyı kaydedin. <br><br> Kayıt penceresi açılıp daha sonra oturum açmaya izin vermeden hemen kapatırsa bu hatayı alırsınız. Bu hata, sisteminizde bir ağ hatası olduğunda oluşur. Tarayıcıdan ortak bir Web sitesine bağlanmak ve bağlantı noktalarının [uygulama proxy önkoşulları](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)'nda belirtilen şekilde açık olduğundan emin olun. |
| Temizleme hatası, kayıt penceresinde gösterilir. Devam edilemiyor | Bu hatayı görürseniz ve pencere kapandığında yanlış Kullanıcı adı veya parola girdiniz. Yeniden deneyin. |
| Bağlayıcı kaydı başarısız oldu: Azure Yönetim Portalı uygulama proxy 'Sini etkinleştirdiğinizden ve Active Directory Kullanıcı adınızı ve parolanızı doğru girdiğinizden emin olun. Hata: ' AADSTS50059: istekte hiçbir kiracı tanımlama bilgisi bulunamadı veya belirtilen kimlik bilgileri tarafından kapsanıyor ve hizmet sorumlusu URI 'sine göre arama başarısız oldu. | Erişmeye çalıştığınız dizinin kuruluş KIMLIĞININ bir parçası olan bir etki alanı değil, bir Microsoft hesabı kullanarak oturum açmaya çalışıyorsunuz. Yöneticinin kiracı etki alanı ile aynı etki alanı adının bir parçası olduğundan emin olun. Örneğin, Azure AD etki alanı contoso.com ise, yöneticinin olması gerekir admin@contoso.com . |
| PowerShell betikleri çalıştırmak için geçerli yürütme ilkesi alınamadı. | Bağlayıcı yüklemesi başarısız olursa, PowerShell yürütme ilkesinin devre dışı olmadığından emin olmak için denetleyin. <br><br>1. grup ilkesi düzenleyicisini açın.<br>2.   >    >  **Windows bileşenleri**  >  **Windows PowerShell** Yönetim Şablonları bilgisayar yapılandırması ' na gidin ve **betik yürütmeyi aç**' a çift tıklayın.<br>3. yürütme ilkesi **yapılandırılmamış** ya da **etkin** olarak ayarlanabilir. **Etkin** olarak ayarlanırsa, Seçenekler ' in altında, yürütme ilkesinin **Yerel betikler ve uzaktan Imzalanmış betiklerine izin ver** ' e ayarlandığından emin olun veya **Tüm betiklerine izin verin**. |
| Bağlayıcı yapılandırmayı indiremedi. | Kimlik doğrulama için kullanılan bağlayıcının istemci sertifikası, zaman aşımına uğradı. Bu, bağlayıcının bir proxy 'nin arkasında yüklü olması halinde de oluşabilir. Bu durumda, bağlayıcı Internet 'e erişemez ve uzak kullanıcılara uygulama sağlayamayacak. `Register-AppProxyConnector`Windows PowerShell 'de cmdlet 'ini kullanarak güveni el ile yenileyin. Bağlayıcınız bir proxy 'nin arkasındaysa, "Ağ Hizmetleri" ve "yerel sistem" bağlayıcı hesaplarına Internet erişimi verilmesi gerekir. Bu, proxy 'ye erişim izni vererek ya da proxy 'yi atlayacak şekilde ayarlanarak yapılabilir. |
| Bağlayıcı kaydı başarısız oldu: bağlayıcıyı kaydettirmek için Active Directory bir uygulama yöneticisi olduğunuzdan emin olun. Hata: ' kayıt isteği reddedildi. ' | Oturum açmaya çalıştığınız diğer ad, bu etki alanında bir yönetici değil. Bağlayıcınız, kullanıcının etki alanına sahip olan dizin için her zaman yüklenir. Oturum açmaya çalıştığınız yönetici hesabının Azure AD kiracısı için en az uygulama Yöneticisi izinlerine sahip olduğundan emin olun. |
| Bağlayıcı, ağ sorunları nedeniyle hizmete bağlanamadı. Bağlayıcı aşağıdaki URL 'ye erişmeyi denedi. | Bağlayıcı, uygulama proxy 'Si bulut hizmetine bağlanamıyor. Bağlantıyı engelleyen bir güvenlik duvarı kuralınız varsa bu durum oluşabilir. [Uygulama proxy 'si önkoşulları](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)'nda listelenen doğru bağlantı noktalarına ve URL 'lere erişim izni verildiğinden emin olun. |

## <a name="kerberos-errors"></a>Kerberos hataları

Bu tablo, Kerberos kurulumu ve yapılandırmasından gelen daha yaygın hataları kapsar ve çözümleme önerilerini içerir.

| Hata | Önerilen adımlar |
| ----- | ----------------- |
| PowerShell betikleri çalıştırmak için geçerli yürütme ilkesi alınamadı. | Bağlayıcı yüklemesi başarısız olursa, PowerShell yürütme ilkesinin devre dışı bırakılmadığından emin olun.<br><br>1. grup ilkesi düzenleyicisini açın.<br>2.   >    >  **Windows bileşenleri**  >  **Windows PowerShell** Yönetim Şablonları bilgisayar yapılandırması ' na gidin ve **betik yürütmeyi aç**' a çift tıklayın.<br>3. yürütme ilkesi **yapılandırılmamış** ya da **etkin** olarak ayarlanabilir. **Etkin** olarak ayarlanırsa, Seçenekler ' in altında, yürütme ilkesinin **Yerel betikler ve uzaktan Imzalanmış betiklerine izin ver** ' e ayarlandığından emin olun veya **Tüm betiklerine izin verin**. |
| 12008-Azure AD, arka uç sunucusuna izin verilen Kerberos kimlik doğrulaması girişimlerinin en fazla sayısını aştı. | Bu hata, Azure AD ile arka uç uygulama sunucusu arasında yanlış yapılandırmayı ya da her iki makinede zaman ve Tarih yapılandırmasında bir sorun olduğunu gösteriyor olabilir. Arka uç sunucusu, Azure AD tarafından oluşturulan Kerberos anahtarını reddetti. Azure AD ve arka uç uygulama sunucusunun doğru şekilde yapılandırıldığını doğrulayın. Azure AD ve arka uç uygulama sunucusundaki saat ve Tarih yapılandırmasının eşitlendiğinden emin olun. |
| 13016-uç belirtecinde veya erişim tanımlama bilgisinde UPN olmadığından Azure AD Kullanıcı adına Kerberos bileti alamıyor. | STS yapılandırmasıyla ilgili bir sorun var. STS 'deki UPN talep yapılandırmasını düzeltir. |
| 13019-Azure AD, aşağıdaki genel API hatası nedeniyle kullanıcı adına Kerberos bileti alamıyor. | Bu olay, Azure AD ile etki alanı denetleyicisi sunucusu arasında yanlış yapılandırmayı veya her iki makinede zaman ve Tarih yapılandırmasında bir sorun olduğunu gösteriyor olabilir. Etki alanı denetleyicisi, Azure AD tarafından oluşturulan Kerberos biletini reddetti. Azure AD ve arka uç uygulama sunucusunun, özellikle SPN yapılandırması doğru şekilde yapılandırıldığını doğrulayın. Etki alanı denetleyicisinin Azure AD 'ye güvendiğinden emin olmak için, Azure AD 'nin etki alanı denetleyicisi ile aynı etki alanına katılmış olduğundan emin olun. Azure AD ve etki alanı denetleyicisindeki saat ve Tarih yapılandırmasının eşitlendiğinden emin olun. |
| 13020-arka uç sunucu SPN 'si tanımlanmadığı için Azure AD Kullanıcı adına Kerberos bileti alamıyor. | Bu olay, Azure AD ile etki alanı denetleyicisi sunucusu arasında yanlış yapılandırmayı veya her iki makinede zaman ve Tarih yapılandırmasında bir sorun olduğunu gösteriyor olabilir. Etki alanı denetleyicisi, Azure AD tarafından oluşturulan Kerberos biletini reddetti. Azure AD ve arka uç uygulama sunucusunun, özellikle SPN yapılandırması doğru şekilde yapılandırıldığını doğrulayın. Etki alanı denetleyicisinin Azure AD 'ye güvendiğinden emin olmak için, Azure AD 'nin etki alanı denetleyicisi ile aynı etki alanına katılmış olduğundan emin olun. Azure AD ve etki alanı denetleyicisindeki saat ve Tarih yapılandırmasının eşitlendiğinden emin olun. |
| 13022-arka uç sunucusu Kerberos kimlik doğrulaması girişimlerini HTTP 401 hatasıyla yanıtladığı için Azure AD kullanıcının kimliğini doğrulayamadı. | Bu olay, Azure AD ile arka uç uygulama sunucusu arasında yanlış yapılandırmayı veya her iki makinede de saat ve Tarih yapılandırmasında bir sorun olduğunu gösterebilir. Arka uç sunucusu, Azure AD tarafından oluşturulan Kerberos anahtarını reddetti. Azure AD ve arka uç uygulama sunucusunun doğru şekilde yapılandırıldığını doğrulayın. Azure AD ve arka uç uygulama sunucusundaki saat ve Tarih yapılandırmasının eşitlendiğinden emin olun. Daha fazla bilgi için bkz. [uygulama proxy 'si Için Kerberos kısıtlanmış temsil yapılandırmalarına sorun giderme](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Son Kullanıcı hataları

Bu liste, uygulamaya erişmeye ve başarısız olmasına çalıştıklarında son kullanıcılarınızın karşılaşabileceği hataları içerir. 

| Hata | Önerilen adımlar |
| ----- | ----------------- |
| Web sitesi sayfayı görüntüleyemiyor. | Uygulamanız bir ıWA uygulaması ise, yayımladığınız uygulamaya erişmeye çalışırken kullanıcılarınız bu hatayı alabilir. Bu uygulama için tanımlanan SPN yanlış olabilir. IWA uygulamaları için, bu uygulama için yapılandırılan SPN 'nin doğru olduğundan emin olun. |
| Web sitesi sayfayı görüntüleyemiyor. | Uygulamanız bir OWA uygulaması ise, yayımladığınız uygulamaya erişmeye çalışırken kullanıcılarınız bu hatayı alabilir. Bunun nedeni aşağıdakilerden biri olabilir:<br><li>Bu uygulama için tanımlanan SPN yanlış. Bu uygulama için yapılandırılan SPN 'nin doğru olduğundan emin olun.</li><li>Uygulamaya erişmeye çalışan Kullanıcı, oturum açmak için uygun şirket hesabı yerine bir Microsoft hesabı kullanıyor veya Kullanıcı bir Konuk Kullanıcı. Kullanıcının, yayımlanan uygulamanın etki alanıyla eşleşen kurumsal hesaplarını kullanarak oturum açtığından emin olun. Microsoft hesabı kullanıcıları ve konuıwa uygulamalarına erişemez.</li><li>Uygulamaya erişmeye çalışan Kullanıcı, şirket içi tarafında bu uygulama için düzgün tanımlanmamıştır. Bu kullanıcının şirket içi makinede bu arka uç uygulaması için tanımlanan uygun izinlere sahip olduğundan emin olun. |
| Bu şirket uygulamasına erişilemiyor. Bu uygulamaya erişim yetkiniz yok. Yetkilendirme başarısız oldu. Kullanıcıya bu uygulamaya erişimi atadığınızdan emin olun. | Kullanıcılarınız, şirket hesabı yerine Microsoft hesapları kullanıyorsa yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. Konuk kullanıcılar da bu hatayı alabilir. Microsoft hesabı kullanıcıları ve konukları ıWA uygulamalarına erişemez. Kullanıcının, yayımlanan uygulamanın etki alanıyla eşleşen kurumsal hesaplarını kullanarak oturum açtığından emin olun.<br><br>Bu uygulama için Kullanıcı atanmamış olabilir. **Uygulama** sekmesine gidin ve **Kullanıcılar ve gruplar**' ın altında bu kullanıcıya veya kullanıcı grubunu bu uygulamaya atayın. |
| Bu kurumsal uygulamaya şu anda erişilemiyor. Lütfen daha sonra tekrar deneyin... Bağlayıcı zaman aşımına uğradı. | Kullanıcılarınız şirket içi tarafında bu uygulama için doğru tanımlanmamışsa, yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. Kullanıcılarınızın şirket içi makinede bu arka uç uygulaması için tanımlanan uygun izinlere sahip olduğundan emin olun. |
| Bu şirket uygulamasına erişilemiyor. Bu uygulamaya erişim yetkiniz yok. Yetkilendirme başarısız oldu. Kullanıcının Azure Active Directory Premium için bir lisansa sahip olduğundan emin olun. | Kullanıcılarınız, abone yöneticisinin bir Premium lisansıyla açıkça atanmamışsa, yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. Abonenin Active Directory **Lisansları** sekmesine gidin ve bu kullanıcı veya kullanıcı grubuna bir Premium Lisansı atandığından emin olun. |
| Belirtilen ana bilgisayar adına sahip bir sunucu bulunamadı. | Bu hata, uygulamanın özel etki alanı doğru yapılandırılmamışsa yayımladığınız uygulamaya erişmeye çalışırken bu hatayı alabilir. [Azure 'da özel etki alanları Ile çalışma](application-proxy-configure-custom-domain.md) bölümündeki adımları izleyerek etki alanı için bir sertifika YÜKLEDIĞINIZDEN ve DNS kaydını doğru şekilde yapılandırdığınızdan emin olun ad uygulama ara sunucusu |
|Yasak: Bu şirket uygulamasına erişilemiyor veya Kullanıcı yetkilendirilemedi. Kullanıcının şirket içi AD 'niz içinde tanımlandığından ve kullanıcının şirket içi AD 'de uygulamaya erişimi olduğundan emin olun. | Bu, yetkilendirme bilgilerine erişimle ilgili bir sorun olabilir, bkz. [bazı uygulamalar ve API 'ler, hesap nesnelerinde yetkilendirme bilgilerine erişim gerektirir]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information). Bir Nutshell 'de, çözülecek uygulama proxy Bağlayıcısı makine hesabını "Windows yetkilendirme erişim grubu" adlı yerleşik etki alanı grubuna ekleyin. |

## <a name="my-error-wasnt-listed-here"></a>Hata listemde burada listelenmedi

Bu sorun giderme kılavuzunda listelenmeyen Azure AD Uygulama Ara Sunucusu bir hata veya sorun yaşarsanız bunun hakkında bilgi almak istiyoruz. Karşılaştığınız hatanın ayrıntıları ile [geri bildirim ekibimize](mailto:aadapfeedback@microsoft.com) bir e-posta gönderin.

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Active Directory için uygulama proxy 'Sini etkinleştir](application-proxy-add-on-premises-application.md)
* [Uygulama Ara Sunucusu ile uygulama yayımlama](application-proxy-add-on-premises-application.md)
* [Çoklu oturum açmayı etkinleştir](application-proxy-configure-single-sign-on-with-kcd.md)
* [Koşullu erişimi etkinleştir](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
