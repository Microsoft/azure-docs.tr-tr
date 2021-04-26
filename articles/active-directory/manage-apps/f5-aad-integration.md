---
title: F5 ile Azure AD güvenli karma erişimi | Microsoft Docs
description: F5 BIG-IP Access Policy Manager ve güvenli karma erişim için Azure Active Directory tümleştirme
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38b484bd67f3354132675f343dcc06bd7f9d48a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499823"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Policy Manager ve güvenli karma erişim için Azure Active Directory tümleştirme

Mobilite ve gelişen tehdit yatağı, kaynak erişimi ve idare üzerine ek bir yönetim, tüm modernleştirme programlarının [sıfır güveni](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) ve merkezini yerleştiriyor.
Microsoft ve F5 ' te bu dijital dönüşümün genellikle her işletme için çok yıllık bir yolculukta olduğunu fark ediyoruz ve bu da modernleştirene kadar kritik kaynakları açığa çıkardık. Genesin, F5 büyük IP 'nin arkasında ve Azure Active Directory güvenli karma erişim (SHA) amaçlar, yalnızca şirket içi uygulamalara uzaktan erişimi geliştirmekle kalmaz, aynı zamanda bu güvenlik açığı bulunan hizmetlerin genel güvenlik duruşunu güçlendirerek sağlar.

Bağlam için, araştırma tahminleri, şirket içi uygulamaların% 60-80 ' ün doğası içinde eski olduğunu veya diğer bir deyişle Azure Active Directory (AD) ile doğrudan tümleştirilebilme yeteneğine sahiptir. Aynı araştırma Ayrıca, bu sistemlerin büyük bir oranının, SAP, Oracle,, ve kritik hizmetler sağlayan diğer iyi bilinen iş yüklerinin alt düzey sürümlerinde çalıştırıldığını de gösterdi.

SHA, kuruluşların üstün ağ ve uygulama teslimi için F5 yatırımlarını kullanmaya devam etmesine olanak tanıyarak bu gizli noktayı adresleyen. Azure AD ile birlikte, modern kimlik denetim düzlemi ile heterojen uygulamayı yatay olarak köprüler.

Azure AD 'nin, büyük IP yayımlanmış hizmetlere erişim izni vermek birçok avantaj sağlar:

- [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](../user-help/user-help-auth-app-download-install.md), [hızlı kimlik çevrimiçi (Fido) anahtarları](../authentication/howto-authentication-passwordless-security-key.md)ve [sertifika tabanlı kimlik doğrulaması](../authentication/active-directory-certificate-based-authentication-get-started.md) aracılığıyla parola-daha az kimlik doğrulama

- Preemptive [koşullu erişim](../conditional-access/overview.md) ve [çok faktörlü KIMLIK doğrulaması (MFA)](../authentication/concept-mfa-howitworks.md)

- [Kimlik koruması](../identity-protection/overview-identity-protection.md) -Kullanıcı ve oturum risk profili oluşturma aracılığıyla uyarlamalı denetim


- [Sızdırılan kimlik bilgisi algılama](../identity-protection/concept-identity-protection-risks.md)

- [Self servis parola sıfırlama (SSPR)](../authentication/tutorial-enable-sspr.md)

- [Ortak işbirliği](../governance/entitlement-management-external-users.md) -yönetilen konuk erişimi için Yetkilendirme Yönetimi

- [Cloud App Security (KASB)](/cloud-app-security/what-is-cloud-app-security) -tam uygulama bulma ve denetim için

- Tehdit izleme-Gelişmiş tehdit analizi için [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)

- [Azure AD portalı](https://azure.microsoft.com/features/azure-portal/) -kimlik ve erişim yönetmek için tek bir denetim düzlemi

## <a name="scenario-description"></a>Senaryo açıklaması

Bir uygulama teslim denetleyicisi (ADC) ve SSL-VPN olarak, büyük bir IP sistemi, aşağıdakiler dahil olmak üzere tüm hizmet türlerine yerel ve uzaktan erişim sağlar:

- Modern ve eski Web uygulamaları

- Web tabanlı olmayan uygulamalar

- REST ve SOAP Web API hizmetleri

Yerel Traffic Manager (LTM), ters proxy işlevselliği aracılığıyla hizmetlerin güvenli bir şekilde yayımlanmasına olanak tanır. Aynı zamanda, gelişmiş bir erişim Ilkesi Yöneticisi (APM) büyük IP 'yi daha zengin bir özellik kümesiyle genişleterek Federasyon ve çoklu oturum açmayı (SSO) etkinleştirir.

Tümleştirme, APM ile Azure AD arasındaki standart bir Federasyon güvenine dayalıdır. Bu, [SSL-VPN senaryosunu](f5-aad-password-less-vpn.md)IÇEREN çoğu SHA kullanım durumu için ortaktır. Security Assertion Markup Language (SAML), OAuth ve açık KIMLIK Connect (OıDC) kaynakları, uzaktan erişim için güvenliği sağlanamayacak şekilde hiçbir özel durum değildir. Ayrıca, bir büyük IP 'nin, SaaS uygulamaları dahil olmak üzere tüm hizmetlere sıfır güven erişimi için bir sıkıştırma noktası haline geldiği senaryolar da vardır.

BÜYÜK IP 'nin Azure AD ile tümleştirme özelliği, eski veya Azure dışı AD ile tümleşik hizmetlerin, [parola kullanmayan kimlik doğrulama](https://www.microsoft.com/security/business/identity/passwordless) ve [koşullu erişim](../conditional-access/overview.md)gibi modern denetimlerle güvenli hale getirmek için gerekli olan protokol geçişini sağlar. Bu senaryoda, büyük bir IP rolünü ters bir ara sunucu olarak yerine getirmek için, Azure AD 'ye ön kimlik doğrulama ve yetkilendirme işlemlerini bir hizmet temelinde yapmaya devam etmektedir.

Diyagramdaki Adım 1-4, bir Kullanıcı, büyük IP ve Azure AD arasında bir hizmet sağlayıcısı tarafından başlatılan akışta ön uç ön kimlik doğrulama değişimini gösterir. Adımlar 5-6, sonraki APM oturumu zenginleştirme ve SSO 'yu ayrı arka uç hizmetlerine gösterir.

![Görüntüde yüksek düzey mimari gösteriliyor](./media/f5-aad-integration/integration-flow-diagram.png)

| Adım | Description |
|:------|:-----------|
| 1. | Kullanıcı portalda bir uygulama simgesi seçer, URL 'YI SAML SP 'ye çözümlüyor (BIG-IP) |
| 2. | BÜYÜK IP, kullanıcıyı ön kimlik doğrulama için SAML ıDP 'ye (Azure AD) yönlendirir|
| 3. | Azure AD, yetkilendirme için koşullu erişim ilkelerini ve [oturum denetimlerini](../conditional-access/concept-conditional-access-session.md) işler|
| 4. | Kullanıcı, Azure AD tarafından verilen SAML taleplerini sunan büyük IP 'ye yeniden yönlendirir |
| 5. | BÜYÜK-IP yayımlanan hizmete [SSO](../hybrid/how-to-connect-sso.md) ve [rol tabanlı ERIŞIM denetimi (RBAC)](../../role-based-access-control/overview.md) dahil etmek için ek oturum bilgileri ister |
| 6. | BÜYÜK IP, istemci isteğini arka uç hizmetine iletir

## <a name="user-experience"></a>Kullanıcı deneyimi

Doğrudan çalışan, bağlı veya tüketici, çoğu kullanıcının Office 365 oturum açma deneyimiyle zaten tanışıp kaynaklanmadığı için, SHA aracılığıyla büyük IP hizmetlerine erişmek büyük ölçüde tanıdık kalır.

Kullanıcılar artık, cihaz veya konum türü ne olduğuna bakılmaksızın,  [uygulamalılar](../user-help/my-apps-portal-end-user-access.md) veya [O365 başlatma DEFTERLERINDE](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) birleştirilmiş büyük IP yayınlanmış Hizmetleri, daha geniş bir hizmet kümesiyle birlikte bulabilirsiniz. Kullanıcılar, tercih edilen BIG-IPs özel WebTop portalı aracılığıyla doğrudan yayımlanmış hizmetlere erişmeye devam edebilir. Oturum kapatma sırasında SHA, büyük IP ve Azure AD olmak üzere bir kullanıcının oturumunun her iki uçta da sonlanmasını sağlar. böylece hizmetler yetkisiz erişimden tamamen korunmuş kalır.  

Belirtilen ekran görüntüleri, kullanıcıların, büyük IP yayınlanan hizmetlerini bulmak ve hesap özelliklerini yönetmek için güvenli şekilde erişebileceği Azure AD uygulama portalından yöneliktir.  

![Ekran görüntüsünde Woodgrove uygps Galerisi gösterilmektedir](media/f5-aad-integration/woodgrove-app-gallery.png)

![Ekran görüntüsü Woodgrove myaccounts self servis sayfasını gösterir](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Öngörüler ve analiz

BÜYÜK IP 'nin rolü her işletme için kritik öneme sahip olduğundan, yayımlanan hizmetlerin yüksek oranda kullanılabilir olduğundan ve hem SHA düzeyinde hem de çalışır durumda olduğundan emin olmak için dağıtılan büyük IP örneklerinin izlenmesi gerekir.

Olayları yerel olarak veya bir güvenlik bilgileri ve olay yönetimi (SıEM) çözümü ile uzaktan günlüğe kaydetmek için çeşitli seçenekler vardır. Bu, devre dışı depolamayı ve telemetri işlemesini etkinleştirir. Azure AD ve SHA 'ya özgü etkinlikleri izlemeye yönelik yüksek düzeyde etkili bir çözüm olan Azure [izleyici](../../azure-monitor/overview.md) ve [Azure Sentinel](../../sentinel/overview.md), birlikte sunma:

- BÜYÜK IP altyapısı dahil olmak üzere kuruluşunuza, potansiyel olarak birden çok bulutta ve şirket içi konumlara ilişkin ayrıntılı genel bakış

- Tüm sinyallerin birleştirilmiş görünümünü sağlayan tek denetim düzlemi, karmaşık ve farklı araçlara karşı rahatdan kaçınma

![Görüntü, izleme akışını gösterir](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Önkoşullar

SHA için Azure AD ile F5 BIG-IP tümleştirme aşağıdaki önkoşulların yerine getirilmesini ister:

- Aşağıdaki platformlardan biri üzerinde çalışan bir F5 BIG-IP örneği:

  - Fiziksel Gereç

  - Microsoft Hyper-V, VMware ESXi, Linux KVM ve Citrix Hiper Yöneticisi gibi hiper yönetici sanal sürümü

  - Azure, VMware, KVM, topluluk Xen, MS Hyper-V, AWS, OpenStack ve Google Cloud gibi bulut sanal sürümü

    BÜYÜK bir IP örneğinin gerçek konumu, şirket içinde veya Azure gibi desteklenen herhangi bir bulut platformunda, Internet bağlantısı, yayımlanmakta olan kaynaklar ve Active Directory gibi diğer gerekli hizmetler olabilir.  

- Aşağıdaki seçeneklerden birini kullanarak etkin bir F5 BIG-IP APM lisansı:

   - F5 BIG-IP® En Iyi demeti (veya)

   - F5 BIG-IP Access Policy Manager™ tek başına lisans

   - F5 BIG-IP Access Policy Manager™ (APM) eklenti lisansı var olan bir büyük IP F5 BIG-IP® yerel Traffic Manager™ (LTM)

   - 90 günlük büyük IP erişim Ilkesi Yöneticisi™ (APM) [deneme lisansı](https://www.f5.com/trial/big-ip-trial.php)

- Aşağıdaki seçeneklerden biriyle Azure AD Lisanslama:

   - Azure AD [ücretsiz aboneliği](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) , salt parola-daha az KIMLIK doğrulamasıyla SHA uygulamak için en düşük çekirdek gereksinimleri sağlar

   - [Premium abonelik](https://azure.microsoft.com/pricing/details/active-directory/) , [koşullu erişim](../conditional-access/overview.md), [MFA](../authentication/concept-mfa-howitworks.md)ve [kimlik koruması](../identity-protection/overview-identity-protection.md) dahil olmak üzere ön yüzde özetlenen tüm ek değer eklemeleri sağlar

SHA uygulamak için önceki bir deneyim veya F5 BIG-IP bilgisi gerekmez, ancak F5 büyük-IP terminolojisini alıştırarak kendiniz öneririz. F5's zengin [Bilgi Bankası](https://www.f5.com/services/resources/glossary) , büyük IP bilgisi oluşturmaya başlamak için de iyi bir yerdir.

## <a name="deployment-scenarios"></a>Dağıtım senaryoları

Aşağıdaki öğreticiler, büyük IP ve Azure AD SHA için daha yaygın olarak kullanılan bazı düzenleri uygulamaya yönelik ayrıntılı yönergeler sağlar:

- [F5 BIG-IP for Azure Deployment izlenecek yol](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM ve Azure AD SSO, Kerberos uygulamalarına](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM ve Azure AD SSO, üst bilgi tabanlı uygulamalara](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Azure AD SHA ile F5 BIG-IP SSL-VPN güvenliğini sağlama](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Ek kaynaklar

- [Parolaların sonu, parolasız go](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory güvenli karma erişim](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Uzak çalışmayı etkinleştirmek için Microsoft sıfır güven çerçevesi](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure Sentinel ile çalışmaya başlama](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Sonraki adımlar

Mevcut büyük IP altyapınızı kullanarak veya bir deneme örneği dağıtarak bir SHA kavram kanıtı (POC) çalıştırmayı göz önünde bulundurun. [Büyük IP sanal sürüm (ve) VM 'Leri Azure 'A dağıtmak](f5-bigip-deployment-guide.md) yaklaşık 30 dakika sürer ve bu noktada şunları yapabilirsiniz:

- SHA kanıtı kavramını modelleyebilir tam olarak güvenli bir platform

- Yeni büyük IP sistem güncelleştirmelerini ve düzeltmeleri test etmek için kullanılan, tam olarak güvenli bir platform olan ön üretim örneği

Aynı zamanda, büyük IP aracılığıyla yayımlama için hedeflenebilir bir veya iki uygulamayı ve SHA ile korumayı belirlemeniz gerekir.  

Bizim önerimiz, henüz büyük IP ile yayımlanmamış bir uygulamayla başlayıp, üretim hizmetleri için olası kesintiden kaçınmak için. Bu makalede bahsedilen yönergeler, çeşitli büyük IP yapılandırma nesneleri oluşturmak ve SHA ayarlamak için genel yordam ile tanışmanız için size yardımcı olacaktır. Tamamlandıktan sonra, diğer yeni hizmetlerle aynı şekilde yapabilmeniz gerekir ve ayrıca, mevcut büyük IP Yayınlanan hizmetleri en az çaba ile SHA 'ya dönüştürmek için yeterli bilgiye sahip olabilirsiniz.

Aşağıdaki etkileşimli kılavuz, SHA uygulamak ve son kullanıcı deneyimini görmek için üst düzey prosedürü adım adım göstermektedir.

[![Görüntüde etkileşimli kılavuz kapağı gösterilmektedir](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
