---
title: Azure Active Directory Apps yapılandırması 'nı planla
description: Kuruluşunuzdaki uygulamalarımı etkin bir şekilde kullanmak için Planlama Kılavuzu.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: baselden
ms.openlocfilehash: 777daecc119a158f11d865489e4eb497c3bc7899
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376605"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Azure Active Directory Apps yapılandırması 'nı planla

> [!NOTE]
> Bu makale, kuruluşunuzun uygulama portalı 'nın yapılandırmasını planlamak isteyen BT uzmanları için tasarlanmıştır. 
>
> **Son Kullanıcı belgeleri için bkz. [uygulamalarım portalından oturum açma ve uygulamaları başlatma](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (Azure AD) Uygulamalarım, uygulamaları başlatmak ve yönetmek için Web tabanlı bir portaldır. Uygulamalarım sayfası, kullanıcılara işlerini başlatmak ve erişimleri olan tüm uygulamaları bulmak için tek bir yer sağlar. Kullanıcılar uygulamalarıma ' de erişir [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Uygulamalarımı neden yapılandırmanız

Uygulamalarım portalı varsayılan olarak kullanıcılar tarafından kullanılabilir ve kapatılamaz. Mümkün olan en iyi deneyimle karşılaşabilmeleri ve Portal yararlı kalacak şekilde yapılandırmak önemlidir. 

Azure Active Directory Kurumsal uygulamalar listesindeki herhangi bir uygulama, aşağıdaki koşullardan her ikisi karşılandığında görünür:

* Uygulamanın görünürlük özelliği true olarak ayarlanır. 

* Uygulama herhangi bir kullanıcıya veya gruba atanır. Atanmış kullanıcılar için görüntülenir.

Portalın yapılandırılması, doğru kişilerin doğru uygulamaları kolayca bulabilmesini sağlar.

 
### <a name="how-is-the-my-apps-portal-used"></a>Uygulamalarım portalı nasıl kullanılır?

Kullanıcılar uygulamalarım portalına şu şekilde erişir:

* Tüm kuruluşlarının, erişimleri olan Azure AD 'ye bağlı uygulamalarının tümünü bulun ve bunlara erişin.

   * Kullanıcılara en iyi deneyimi sağlamak için, uygulamaların çoklu oturum açma (SSO) için yapılandırılmasını sağlamak en iyisidir.

* Self servis için yapılandırılmış yeni uygulamalara erişim isteyin.

* Kişisel uygulama koleksiyonları oluşturun.

* Uygulamaya erişim izni vermek için kullanılan Grup sahibi veya denetim temsilcisi atanan bir rol olduğunda diğerleri için uygulamalara erişimi yönetin.

Yöneticiler şunları yapılandırabilir:

* Hizmet koşulları dahil [onay deneyimleri](../manage-apps/configure-user-consent.md) .

* [Self servis uygulama bulma ve erişim istekleri](../manage-apps/access-panel-manage-self-service-access.md).

* [Uygulama koleksiyonları](../manage-apps/access-panel-collections.md).

* Uygulamalara simgeler atama

* Uygulamalar için Kullanıcı dostu adlar

* Uygulamamda gösterilen şirket markası

 

## <a name="plan-consent-configuration"></a>Onay yapılandırmasını planla

### <a name="user-consent-for-applications"></a>Uygulamalar için Kullanıcı onayı 

Bir kullanıcının bir uygulamada oturum açması ve uygulamanın kuruluşunuzun verilerine erişebilmesi için, bir kullanıcının veya yöneticinin uygulama izinlerini vermesi gerekir. Kullanıcı onaylamasına izin verilip verilmeyeceğini ve bu koşulların altına göre yapılandırma yapabilirsiniz. **Microsoft, yalnızca uygulamaların doğrulanmış yayımcılar için Kullanıcı onaylamasına izin vermenizi önerir.**

Daha fazla bilgi için bkz. [son kullanıcıların uygulamalara nasıl izin vermesini yapılandırma](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Verilere erişen uygulamalar için Grup sahibi onayı

Grup ve takım sahipleri, üçüncü taraf satıcılar tarafından yayımlanan uygulamalar gibi, kuruluşunuzun bir grupla ilişkili verilerinize erişebilmesi için uygulamalara yetki verebilir. Daha fazla bilgi için bkz. [Microsoft ekiplerde kaynağa özel izin](https://docs.microsoft.com/microsoftteams/resource-specific-consent) . 

Bu özelliği izin vermek veya devre dışı bırakmak isteyip istemediğinizi yapılandırabilirsiniz.

Daha fazla bilgi için bkz. [Grup onay Izinlerini yapılandırma](../manage-apps/configure-user-consent-groups.md).

### <a name="plan-communications"></a>İletişimi planlama

İletişim, her yeni hizmetin başarısı için önemlidir. Kullanıcılarınıza deneyiminizin nasıl ve ne zaman değiştirileceğini ve gerektiğinde nasıl destek kazanabileceğini önceden bilgilendirirsiniz.

Uygulamalarım genellikle kullanıcı sorunları oluşturmasa da, hazırlanması önemlidir. Başlamadan önce destek personelinize yönelik tüm kaynakların kılavuzlarını ve bir listesini oluşturun.

#### <a name="communications-templates"></a>İletişim şablonları

Microsoft, [e-postalar için özelleştirilebilen şablonlar ve](https://aka.ms/APTemplates) Uygulamalarım için diğer iletişimler sağlar. Bu varlıkları, kurumsal külgereksinimlerinize uygun şekilde diğer iletişim kanallarında kullanmak üzere uyarlayabilirsiniz.

 

## <a name="plan-your-sso-configuration"></a>SSO yapılandırmanızı planlayın

Kullanıcıların kimlik bilgilerini girmeye gerek kalmadan sorunsuz bir deneyim sahibi olması için, My Apps Portalındaki tüm uygulamalar için SSO etkinse bu en iyisidir.

Azure AD birden çok SSO seçeneğini destekler. 

* Daha fazla bilgi edinmek için bkz. [Azure AD 'de çoklu oturum açma seçenekleri](sso-options.md).

* Azure AD 'yi bir uygulama için kimlik sağlayıcısı olarak kullanma hakkında daha fazla bilgi edinmek için bkz. [uygulama yönetiminde hızlı başlangıç serisi](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Mümkünse Federal SSO kullan

Uygulamalarım sayfasında en iyi kullanıcı deneyimi için, Federasyon SSO (OpenID Connect veya SAML) için kullanılabilen bulut uygulamalarının tümleştirmesiyle başlayın. Federasyon SSO, kullanıcıların, uygulama başlatma ve yapılandırma denetiminde daha sağlam olma eğilimi üzerinde tutarlı bir tıklama deneyimine sahip olmasını sağlar.

SSO için hizmet olarak yazılım (SaaS) uygulamaları yapılandırma hakkında daha fazla bilgi için bkz. [SaaS SSO dağıtım planı].. /Desktop/plan-SSO-Deployment.exe).

### <a name="considerations-for-special-sso-circumstances"></a>Özel SSO koşullara yönelik hususlar

> [!TIP]
> Daha iyi bir kullanıcı deneyimi için, parola tabanlı SSO ve ADFS yerine bir uygulama destekliyorsa Azure AD ile Federasyon SSO (OpenID Connect/SAML) kullanın.

Parola tabanlı SSO uygulamalarında veya Azure AD Uygulama Ara Sunucusu tarafından erişilen uygulamalarda oturum açmak için, kullanıcıların uygulamalarım güvenli oturum açma uzantısını yüklemesi ve kullanması gerekir. Kullanıcılardan, parola tabanlı SSO veya uygulama proxy 'Si uygulamasını ilk kez başlattığında uzantıyı yüklemeleri istenir. 

![Ekran görüntüsü](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Uzantı hakkında ayrıntılı bilgi için bkz. [uygulamalarımı yükleme tarayıcı uzantısı](../user-help/my-apps-portal-end-user-access.md).

Bu uygulamaları tümleştirmeniz gerekiyorsa, uzantıyı [desteklenen tarayıcılarla](../user-help/my-apps-portal-end-user-access.md)bir ölçekte dağıtmak için bir mekanizma tanımlamanız gerekir. Seçeneklere şunlar dahildir:

* [Chrome, Firefox, Microsoft Edge veya IE için Kullanıcı odaklı indirme ve yapılandırma](../user-help/my-apps-portal-end-user-access.md)

* [Internet Explorer için Configuration Manager](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Uzantı, kullanıcıların arama çubuğundan herhangi bir uygulamayı başlatabilir, son kullanılan uygulamalara erişimi bulmasını ve uygulamalarım sayfasına yönelik bir bağlantıya sahip olmasını sağlar.

![Uygulama Uzantısı aramamın ekran görüntüsü](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Mobil erişim planlaması

Parola tabanlı SSO kullanan veya [Microsoft Azure AD uygulama proxy 'si](../manage-apps/application-proxy.md)kullanarak erişilen uygulamalar Için Microsoft Edge Mobile 'ı kullanmanız gerekir. Diğer uygulamalar için, herhangi bir mobil tarayıcı kullanılabilir. 

### <a name="linked-sso"></a>Bağlı SSO

Uygulamalar, bağlantılı SSO seçeneği kullanılarak eklenebilir. Mevcut Web uygulamanızın URL 'sine bağlanan bir uygulama kutucuğu yapılandırabilirsiniz. Bağlantılı SSO, tüm uygulamaları Azure AD SSO 'ya geçirmeden kullanıcıları uygulama portalına yönlendirmeye başlayabilmeniz için izin verir. Kullanıcıların deneyimini kesintiye uğratmadan, Azure AD SSO ile yapılandırılmış uygulamalara giderek geçebilirsiniz.

## <a name="plan-the-user-experience"></a>Kullanıcı deneyimini planlayın

Varsayılan olarak, kullanıcının erişimi olan tüm uygulamalar ve self servis bulma için yapılandırılmış tüm uygulamalar, kullanıcının uygulamalar panelinde görünür. Birçok kuruluş için bu, çok büyük bir liste olabilir ve bu, düzenlenmemişse buraşkın bir liste olabilir

### <a name="plan-my-apps-collections"></a>Uygulama koleksiyonlarımı planla

Bir kullanıcının erişimi olan her Azure AD uygulaması, uygulamamda tüm uygulamalar koleksiyonunda görünür. İlgili uygulamaları gruplamak ve bunları ayrı bir sekmede sunmak, daha kolay bulunmalarını sağlamak için koleksiyonları kullanın. Örneğin, koleksiyonları, belirli iş rolleri, görevler, projeler vb. için mantıksal uygulama gruplandırmaları oluşturmak üzere kullanabilirsiniz. 

Son kullanıcılar ayrıca, deneyimlerini şu şekilde özelleştirebilir

* Kendi uygulama koleksiyonları oluşturma.

* [Uygulama koleksiyonlarını gizleme ve yeniden sıralama](access-panel-collections.md).

![Self Servis yapılandırması ekran görüntüsü](./media/my-apps-deployment-plan/collections.png)

Uygulamaları uygulamalarım portalından gizlemek için, Microsoft 365 Portalı gibi diğer konumlardan erişime izin verirken bu bir seçenek vardır. Daha fazla bilgi: [bir uygulamayı kullanıcının Azure Active Directory deneyiminden gizleyin](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Yalnızca bir kullanıcıya erişimi olan 950 uygulama, Uygulamalarım aracılığıyla erişilebilir. Bu, Kullanıcı ya da yönetici tarafından gizlenen uygulamaları içerir. 

### <a name="plan-self-service-group-management-membership"></a>Self Servis Grup Yönetimi üyeliğini planlayın

Kullanıcıların Azure AD 'de kendi güvenlik gruplarını veya Microsoft 365 gruplarını oluşturmasını ve yönetmesini sağlayabilirsiniz. Grubun sahibi, üyelik isteklerini onaylayabilir veya reddedebilir ve grup üyeliği denetimini devredebilir. Self Servis Grup yönetimi özellikleri posta etkin güvenlik grupları veya dağıtım listeleri için kullanılamaz.

Self Servis Grup üyeliğini planlamak için, kuruluşunuzdaki tüm kullanıcıların grup oluşturma ve yönetme izni mi yoksa yalnızca bir kullanıcı alt kümesi mi olacağını saptayın. Kullanıcıların bir alt kümesine izin verirseniz, bu kişilerin eklendiği bir grup ayarlamanız gerekir. 

Bu senaryoları etkinleştirme hakkında daha fazla bilgi için, bkz. [Azure Active Directory self servis grup yönetimini ayarlama](../enterprise-users/groups-self-service-management.md) .

### <a name="plan-self-service-application-access"></a>Self Servis uygulama erişimini planlayın

Kullanıcıların uygulamalar panelinden uygulamalara erişimi bulmasını ve erişimini isteme olanağı sağlayabilirsiniz. Bunu yapmak için öncelikle 

* Self Servis Grup yönetimini etkinleştir

* SSO için uygulamayı etkinleştir

* uygulama erişimi için bir grup oluşturma

![Uygulamalarım self servis yapılandırması ekran görüntüsü](./media/my-apps-deployment-plan/my-apps-self-service.png)

Kullanıcılar erişim isteğinde bulunduğunda, temel alınan gruba erişim istenir ve Grup sahipleri, Grup üyeliğini yönetmek için temsilci izni ve bu nedenle uygulama erişimi olabilir. Onay iş akışları, uygulamalara erişim için açık onay için kullanılabilir. Onaylayanlar olan kullanıcılar, uygulamaya erişim için bekleyen istek olduğunda uygulamalar portalı içinde bildirim alır.

## <a name="plan-reporting-and-auditing"></a>Raporlama ve denetim planlaması

Azure AD, [teknik ve iş öngörüleri sunan raporlar] sağlar. /Reports-Monitoring/Overview-Reports.exe). Bu raporların sahipliğini kabul etmek ve bunları düzenli olarak tüketmek için iş ve teknik uygulama sahipleriyle çalışın. Aşağıdaki tabloda tipik raporlama senaryolarının bazı örnekleri verilmiştir.

| Örnek| Riski yönetin| Üretkenliği artırın| İdare ve uyum |
| - | - | - | -|
| Rapor türleri| Uygulama izinleri ve kullanımı| Hesap sağlama etkinliği| Uygulamalara kimlerin eriştiğini gözden geçirme |
| Olası eylemler| Denetim erişimi; izinleri iptal et| Sağlama hatalarını düzelt| Erişimi iptal et |


Azure AD, en çok denetim verilerini 30 gün boyunca tutar. Veriler, çözümleme sistemlerinize indirmeniz için Azure yönetim portalı veya API aracılığıyla kullanılabilir.

#### <a name="auditing"></a>Denetim

Uygulama erişimi için denetim günlükleri 30 gün boyunca kullanılabilir. Kuruluşunuz daha uzun bekletme gerektiriyorsa, günlükleri splunk veya Arctıma gibi bir güvenlik bilgileri olay ve yönetim (SıEM) aracına dışarı aktarın.

Denetim, raporlama ve olağanüstü durum kurtarma yedeklemeleri için gereken indirme sıklığını, hedef sistemin ne olduğunu ve her yedeklemeyi yönetmekten sorumlu olanları belgeleyin. Ayrı denetim ve raporlama yedeklemeleri gerekmez. Olağanüstü durum kurtarma yedeklemeniz ayrı bir varlık olmalıdır.

## <a name="validate-your-deployment"></a>Dağıtımınızı doğrulama

Uygulamalarımın dağıtımının tamamen sınanmış olduğundan ve geri alma planının yapıldığından emin olun.

Şirkete ait cihazlar ve kişisel cihazlarla aşağıdaki testleri gerçekleştirin. Bu test çalışmaları, iş kullanım örneklerini de yansıtmalıdır. Tipik teknik senaryolara göre birkaç durum aşağıda verilmiştir. Gereksinimlerinize özgü diğerlerini ekleyin.

#### <a name="application-sso-access-test-case-examples"></a>Uygulama SSO 'SU erişim test çalışması örnekleri:


| İş örneği| Beklenen sonuç |
| - | - |
| Kullanıcı My Apps portalında oturum açar| Kullanıcı oturum açabilir ve uygulamalarını görebilir |
| Kullanıcı bir Federasyon SSO uygulaması başlatıyor| Kullanıcı otomatik olarak uygulamada oturum açtı |
| Kullanıcı ilk kez parola SSO uygulaması başlatıyor| Kullanıcının uygulama Uzantımı yüklemesi gerekiyor |
| Kullanıcı bir parola SSO uygulamasını sonraki bir zamanda başlatır| Kullanıcı otomatik olarak uygulamada oturum açtı |
| Kullanıcı Microsoft 365 portalından bir uygulama başlatıyor| Kullanıcı otomatik olarak uygulamada oturum açtı |
| Kullanıcı Managed Browser bir uygulamayı başlatır| Kullanıcı otomatik olarak uygulamada oturum açtı |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Uygulama self servis yetenekleri test çalışması örnekleri


| İş örneği| Beklenen sonuç |
| - | - |
| Kullanıcı, uygulama üyeliğini yönetebilir| Kullanıcı, uygulamaya erişimi olan üyeleri ekleyebilir/kaldırabilir |
| Kullanıcı uygulamayı düzenleyebilir| Kullanıcı, parola SSO uygulamaları için uygulamanın açıklamasını ve kimlik bilgilerini düzenleyebilir |


### <a name="rollback-steps"></a>Geri alma adımları

Dağıtımınız planlanmazsa ne yapılacağını planlamanız önemlidir. Dağıtım sırasında SSO yapılandırması başarısız olursa, [SSO sorunlarını nasıl giderebileceğinizi](../hybrid/tshoot-connect-sso.md) ve kullanıcılarınıza etkisini nasıl azaltacağınızı anlamanız gerekir. Olağanüstü durumlarda, [SSO 'yu geri almanız](plan-sso-deployment.md)gerekebilir.

## <a name="manage-your-implementation"></a>Uygulamanızı yönetme

Azure Active Directory içinde gerekli bir görevi gerçekleştirmek için en az ayrıcalıklı rolü kullanın. [Kullanılabilir farklı rolleri gözden geçirin](../roles/permissions-reference.md) ve bu uygulamaya yönelik her kişi için ihtiyaçlarınızı çözümlemek üzere doğru olanı seçin. Dağıtım tamamlandıktan sonra bazı rollerin geçici olarak uygulanması ve kaldırılması gerekebilir.

| Kişilikler| Roller| Azure AD rolü |
| - | - | - |
| Yardım Masası Yöneticisi| Katman 1 desteği| Yok |
| Kimlik Yöneticisi| Sorunları Azure AD ' i etkileyen yapılandırma ve hata ayıklama| Genel yönetici |
| Uygulama Yöneticisi| Uygulamada Kullanıcı kanıtlaması, izinleri olan kullanıcılar üzerinde yapılandırma| Yok |
| Altyapı Yöneticileri| Sertifika aktarma sahibi| Genel yönetici |
| İşletme sahibi/paydaş| Uygulamada Kullanıcı kanıtlaması, izinleri olan kullanıcılar üzerinde yapılandırma| Yok |


Dizin izinleri olan kullanıcılar için ek denetim, denetim ve erişim incelemesi sağlamak üzere rollerinizi yönetmek için [Privileged Identity Management](../privileged-identity-management/pim-configure.md) kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD Multi-Factor Authentication dağıtımını planlayın](../authentication/howto-mfa-getstarted.md)

[Uygulama Ara Sunucusu dağıtımını planlama](application-proxy-deployment-plan.md)

