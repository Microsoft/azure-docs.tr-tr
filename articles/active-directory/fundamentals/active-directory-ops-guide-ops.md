---
title: Azure Active Directory genel işlemler Kılavuzu başvurusu
description: Bu işlemler başvuru kılavuzu, genel işlemleri güvenli hale getirmek için gerçekleştirmeniz gereken denetimleri ve eylemleri açıklar
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836844"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory genel işlemler Kılavuzu başvurusu

[Azure AD işlemler başvuru kılavuzu](active-directory-ops-guide-intro.md) 'nun bu bölümünde, Azure Active Directory (Azure AD) genel işlemlerini iyileştirmek için gerçekleştirmeniz gereken denetimler ve eylemler açıklanır.

> [!NOTE]
> Bu öneriler, yayımlama tarihi itibariyle geçerli olmakla kalmaz, zaman içinde değişebilir. Kuruluşlar, Microsoft ürün ve Hizmetleri zaman içinde geliştikçe, işletimsel uygulamalarını sürekli olarak değerlendirmelidir.

## <a name="key-operational-processes"></a>Anahtar işletimsel işlemler

### <a name="assign-owners-to-key-tasks"></a>Anahtar görevlere sahipler atama

Azure Active Directory Yönetimi, önemli işlem görevlerinin ve işlemlerin sürekli yürütülmesini gerektirir ve bu, bir dağıtım projesinin parçası olmayabilir. Ortamınızı iyileştirmek için bu görevleri ayarlamanız de önemlidir. Önemli görevler ve bunların önerilen sahipleri şunlardır:

| Görev | Sahip |
| :- | :- |
| Kimlik güvenli puanı 'nda sürücü geliştirmeleri | InfoSec Işlemler ekibi |
| Azure AD Connect sunucularının bakımını yapma | IAM Işlemler ekibi |
| Düzenli olarak, ıddüzeltmesini raporlarını yürütme ve önceliklendirme | IAM Işlemler ekibi |
| Azure AD Connect Health Uyarıları eşitleme ve AD FS için önceliklendirme | IAM Işlemler ekibi |
| Azure AD Connect Health kullanmıyorsanız, müşterinin özel altyapıyı izlemeye yönelik eşdeğer işlem ve araçları vardır | IAM Işlemler ekibi |
| AD FS kullanmıyorsanız, müşterinin özel altyapıyı izlemeye yönelik eşdeğer işlem ve araçları vardır | IAM Işlemler ekibi |
| Karma günlükleri izleme: Azure AD Uygulaması Proxy bağlayıcıları | IAM Işlemler ekibi |
| Karma günlükleri izleme: geçiş kimlik doğrulama aracıları | IAM Işlemler ekibi |
| Karma günlükleri izleme: parola geri yazma hizmeti | IAM Işlemler ekibi |
| Karma günlükleri izleme: şirket içi parola koruma ağ geçidi | IAM Işlemler ekibi |
| Karma günlükleri izleme: Azure AD MFA NPS uzantısı (varsa) | IAM Işlemler ekibi |

Listenizi gözden geçirdikten sonra, sahibi eksik olan görevler için bir sahip atamanız veya yukarıdaki önerilere göre hizalanmamış olan sahiplerin sahipliğini ayarlamanız gerekebilir.

#### <a name="owners-recommended-reading"></a>Okumakta önerilen sahipler

- [Azure Active Directory’de yönetici rolü atama](../roles/permissions-reference.md)
- [Azure’da idare](../../governance/index.yml)

## <a name="hybrid-management"></a>Karma yönetim

### <a name="recent-versions-of-on-premises-components"></a>Şirket içi bileşenlerin son sürümleri

Şirket içi bileşenlerin en güncel sürümlerine sahip olmak, müşterinin tüm en son güvenlik güncelleştirmelerini, performans geliştirmelerini ve ortamı daha da kolaylaştırmaya yardımcı olabilecek işlevleri sağlar. Çoğu bileşenin otomatik yükseltme ayarı vardır ve bu, yükseltme işlemini otomatik hale getirir.

Bu bileşenler şunlardır:

- Azure AD Connect
- Azure AD Uygulama Ara Sunucusu bağlayıcıları
- Azure AD geçişli kimlik doğrulama aracıları
- Azure AD Connect Health aracıları

Biri kurulmadığı sürece, bu bileşenleri yükseltmek ve mümkün olduğunda otomatik yükseltme özelliğini bilmeniz için bir işlem tanımlamanız gerekir. Altı veya daha fazla ay içinde olan bileşenleri bulursanız, en kısa sürede yükseltmeniz gerekir.

#### <a name="hybrid-management-recommended-reading"></a>Karma yönetiminin kullanılması önerilir

- [Azure AD Connect: Otomatik yükseltme](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Azure AD Uygulama Ara Sunucusu bağlayıcıları anlama | Otomatik Güncelleştirmeler](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health uyarı temeli

Kuruluşlar, Azure AD Connect ve AD FS izleme ve raporlama için [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) dağıtmalıdır. Azure AD Connect ve AD FS, yaşam döngüsü yönetimi ve kimlik doğrulamasını kesintiye uğratır ve bu nedenle kesintilere yol açabilecek kritik bileşenlerdir Azure AD Connect Health, ortamınızın güvenilirliğini sağlamak için şirket içi kimlik altyapınızı izlemenize ve bu altyapıyı elde etmenize yardımcı olur.

![Azure AD Connect sistem mimarisi](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Ortamınızın sistem durumunu izlerken, yüksek önem derecesine sahip uyarıları hemen ele almanız ve ardından düşük önem derecesine sahip olmanız gerekir.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health okumanız önerilir

- [Aracı yüklemesini Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Şirket içi aracılar günlükleri

Bazı kimlik ve erişim yönetimi hizmetleri, karma senaryoları etkinleştirmek için şirket içi aracılar gerektirir. Parola sıfırlama, geçişli kimlik doğrulaması (PTA), Azure AD Uygulama Ara Sunucusu ve Azure AD MFA NPS uzantısı örnekleri bulunur. System Center Operations Manager veya SıEM gibi çözümler kullanarak bileşen Aracısı günlüklerini arşivleyerek ve çözümleyerek, işlem ekibinin temel aldığı bir anahtardır ve bu bileşenlerin sistem durumunu izlersiniz. Bilgi Işlem ekibiniz veya yardım masasının hata desenleriyle ilgili sorunları nasıl giderebileceğinizi anlayabilmesi de aynı şekilde önemlidir.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Şirket içi aracılar günlüklerinin okunması önerilir

- [Uygulama Ara Sunucusu ile ilgili sorunları giderme](../manage-apps/application-proxy-troubleshoot.md)
- [Self servis parola sıfırlama sorunlarını giderme-Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: geçişli kimlik doğrulaması sorunlarını giderme](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Azure AD MFA NPS uzantısı için hata kodları sorunlarını giderme](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Şirket içi aracılar yönetimi

En iyi uygulamaları benimseme, şirket içi aracıların en iyi operasyonunun sağlanmasına yardımcı olabilir. Aşağıdaki en iyi yöntemleri göz önünde bulundurun:

- Bağlayıcı grubu başına birden çok Azure AD uygulama proxy Bağlayıcısı, proxy uygulamalarına erişirken tek hata noktalarından kaçınarak sorunsuz yük dengelemesi ve yüksek kullanılabilirlik sağlamak için önerilir. Şu anda üretimde uygulamaları işleyen bir bağlayıcı grubunda yalnızca bir bağlayıcınız varsa, artıklık için en az iki bağlayıcı dağıtmanız gerekir.
- Hata ayıklama amacıyla uygulama proxy Bağlayıcısı grubu oluşturma ve kullanma, sorun giderme senaryolarında ve yeni şirket içi uygulamalar eklenirken yararlı olabilir. Ayrıca, bağlayıcı makinelerinde Ileti çözümleyici ve Fiddler gibi ağ araçları yüklemenizi öneririz.
- Kimlik doğrulama akışı sırasında tek hata noktasını önleyerek sorunsuz yük dengelemesi ve yüksek kullanılabilirlik sağlamak için birden çok geçişli kimlik doğrulama Aracısı önerilir. Artıklık için en az iki geçişli kimlik doğrulama Aracısı dağıttığınızdan emin olun.

#### <a name="on-premises-agents-management-recommended-reading"></a>Şirket içi aracılar yönetiminin kullanılması önerilir

- [Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](../manage-apps/application-proxy-connectors.md)
- [Azure AD geçişli kimlik doğrulaması-hızlı başlangıç](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Ölçekte yönetim

### <a name="identity-secure-score"></a>Kimlik secure score’u

[Kimlik güvenli puanı](./identity-secure-score.md) , kuruluşunuzun güvenlik duruşunu ölçülebilir bir ölçüm sağlar. Raporlanan bulguları sürekli gözden geçirmek ve adreslendirmenin yanı sıra mümkün olan en yüksek puanı elde etmek için bir anahtardır. Puanın yardımıyla:

- Kimlik güvenliği duruşunuzu nesnel olarak ölçebilirsiniz
- Kimlik güvenliği geliştirmelerini planlayabilirsiniz
- Geliştirmelerinizin başarısını gözden geçirebilirsiniz

![Güvenlik puanı](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Kuruluşunuzda, kimlik güvenli puandaki değişiklikleri izlemek için şu anda bir program yoksa, bir plan uygulamanız ve geliştirme eylemlerini izlemek ve sağlamak için sahipler atamanız önerilir. Kuruluşlar, bir puan etkisi olan geliştirme eylemlerini en kısa sürede 30 ' dan daha sonra düzeltmelidir.

### <a name="notifications"></a>Bildirimler

Microsoft, hizmette çeşitli değişiklikleri, gereken yapılandırma güncelleştirmelerini ve yönetici müdahalesini gerektiren hataları bilgilendirmek için yöneticilere e-posta iletişimleri gönderir. Müşterilerin bildirim e-posta adreslerini, bildirimlerin tüm bildirimleri kabul edebilen ve üzerinde işlem yapacak uygun takım üyelerine gönderilmesi önemlidir. [Ileti merkezine](/office365/admin/manage/message-center) birden çok alıcı eklemenizi ve bu bildirimlerin (Azure AD Connect Health bildirimleri dahil) bir dağıtım listesine veya paylaşılan posta kutusuna gönderilmesini istemeniz önerilir. Yalnızca bir e-posta adresi olan bir genel yönetici hesabınız varsa, en az iki e-posta özellikli hesabı yapılandırmayı unutmayın.

Azure AD tarafından kullanılan iki "Kimden" adresi vardır: <o365mc@email2.microsoft.com> Ileti merkezi bildirimleri gönderen ve ile <azure-noreply@microsoft.com> ilgili bildirimleri gönderen:

- [Azure AD erişim Incelemeleri](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Kimlik Koruması](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Kurumsal uygulamanın süresi dolan sertifika bildirimleri](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Kurumsal uygulama sağlama hizmeti bildirimleri

Gönderilen bildirimlerin türünü ve bunların nerede kontrol edildiği hakkında bilgi edinmek için aşağıdaki tabloya bakın:

| Bildirim kaynağı | Ne gönderilir | Denetlenecek yer |
|:-|:-|:-|
| Teknik iletişim | Eşitleme hataları | Azure portal-Özellikler dikey penceresi |
| İleti Merkezi | Kimlik hizmetleri ve arka uç Hizmetleri Microsoft 365 olay ve düşme bildirimleri | Office portalı |
| Kimlik koruması Haftalık Özet | Kimlik koruması Özeti | Azure AD Kimlik Koruması dikey pencere |
| Azure AD Connect Health | Uyarı bildirimleri | Azure portal-Azure AD Connect Health dikey penceresi |
| Kurumsal uygulama bildirimleri | Sertifikaların süre sonu ve sağlama hataları olduğunda bildirimler | Azure portal-kurumsal uygulama dikey penceresi (her uygulamanın kendi e-posta adresi ayarı vardır) |

#### <a name="notifications-recommended-reading"></a>Okuma için önerilen bildirimler

- [Kuruluşunuzun adresini, teknik ilgili kişisini ve daha fazlasını değiştirin](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>İşletimsel yüzey alanı

### <a name="ad-fs-lockdown"></a>AD FS kilidi

Uygulamaları [Azure AD akıllı kilitleme](../authentication/concept-sspr-howitworks.md)'den Azure AD avantajına doğrudan kimlik doğrulaması yapacak şekilde yapılandıran kuruluşlar. Windows Server 2012 R2 'de AD FS kullanıyorsanız, AD FS [Extranet kilitleme koruması](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)uygulayın. Windows Server 2016 veya sonraki sürümlerde AD FS kullanıyorsanız, [extranet akıllı kilitleme](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)uygulayın. En azından, şirket içi Active Directory karşı deneme yanılma saldırısı riskini içermesi için extranet kilitlemeyi etkinleştirmenizi öneririz. Ancak, Windows 2016 veya üzeri sürümlerde AD FS varsa, [parola spreyi](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) saldırılarını azaltmaya yardımcı olacak extranet akıllı kilitleme özelliğini de etkinleştirmeniz gerekir.

AD FS yalnızca Azure AD Federasyonu için kullanılıyorsa, saldırı yüzeyi alanını en aza indirmek için kapatılabilir bazı uç noktalar vardır. Örneğin, AD FS yalnızca Azure AD için kullanılıyorsa, **usernamemixed** ve **windowstransport** için etkinleştirilen uç noktaların dışında WS-Trust uç noktaları devre dışı bırakmanız gerekir.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Şirket içi kimlik bileşenleriyle makinelere erişim

Kuruluşlar şirket içi karma bileşenlere sahip makinelere erişimi şirket içi etki alanınız ile aynı şekilde kilitlemelidir. Örneğin, bir yedekleme işletmeni veya Hyper-V Yöneticisi, kuralları değiştirmek için Azure AD Connect sunucuda oturum açabilmelidir.

Active Directory Yönetim Katmanı modeli, ortamın tam denetimi (Katman 0) ve saldırganlar tarafından çok daha fazla tehlikeye neden olan yüksek riskli iş istasyonu varlıkları arasında bir dizi arabellek bölgesi kullanarak kimlik sistemlerini korumak için tasarlanmıştır. ![Katman modelinin üç katmanını gösteren diyagram](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[Katman modeli](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) üç düzeyden oluşur ve standart Kullanıcı hesapları yerine yalnızca yönetim hesapları içerir.

- **Katman 0**: Ortamdaki kuruluş kimliklerinin Doğrudan Denetimi. Katman 0 Active Directory ormanının, etki alanlarının veya etki alanı denetleyicilerinin ve içindeki tüm varlıkların doğrudan ya da dolaylı yönetimsel denetimine sahip hesapları, grupları ve diğer varlıkları içerir. Tüm Katman 0 varlıkları birbirlerini etkin bir şekilde denetlediği için güvenlik açısından hassasiyetleri denktir.
- **Katman 1**: Kuruluş sunucuları ve uygulamalarının denetimi. Katman 1 varlıklarına sunucu işletim sistemleri, bulut hizmetleri ve kuruluş uygulamaları dahildir. Katman 1 yönetim hesapları, bu varlıklar üzerinde barındırılan önemli miktarda iş değeri üzerinde yönetimsel denetime sahiptir. Yaygın olarak kullanılan bir rol örneği, bu işletim sistemlerinin bakımını yapan ve tüm kuruluş hizmetlerini etkileme imkanına sahip olan sunucu yöneticileridir.
- **Katman 2**: Kullanıcı iş istasyonları ve cihazlarının denetimi. Katman 2 yönetim hesapları, kullanıcı iş istasyonları ve cihazları üzerinde barındırılan önemli miktarda iş değeri üzerinde yönetimsel denetime sahiptir. Örnek olarak neredeyse tüm kullanıcı verilerinin bütünlüğünü etkileyebilecek Yardım Masası ve bilgisayar destek yöneticileri verilebilir.

Azure AD Connect, AD FS ve SQL Hizmetleri gibi şirket içi kimlik bileşenlerine erişimi, etki alanı denetleyicilerle aynı şekilde kilitleyin.

## <a name="summary"></a>Özet

Güvenli bir kimlik altyapısının yedi yönü vardır. Bu liste, Azure Active Directory (Azure AD) için işlemleri iyileştirmek üzere gerçekleştirmeniz gereken eylemleri bulmanıza yardımcı olur.

- Anahtar görevlere sahip atayın.
- Şirket içi karma bileşenler için yükseltme işlemini otomatikleştirin.
- Azure AD Connect ve AD FS izleme ve raporlama için Azure AD Connect Health dağıtın.
- System Center Operations Manager veya SıEM çözümünü kullanarak bileşen Aracısı günlüklerini arşivleyerek ve çözümleyerek şirket içi karma bileşenlerin sistem durumunu izleyin.
- Kimlik güvenli puanı ile güvenlik durunuzu ölçerek güvenlik iyileştirmeleri uygulayın.
- AD FS kilitle.
- Şirket içi kimlik bileşenleriyle makinelere erişimi kilitle.

## <a name="next-steps"></a>Sonraki adımlar

Dağıtmadığınız tüm yetenekler hakkında uygulama ayrıntıları için [Azure AD Dağıtım planlarına](active-directory-deployment-plans.md) bakın.