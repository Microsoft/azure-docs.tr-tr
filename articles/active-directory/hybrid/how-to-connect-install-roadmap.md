---
title: Azure AD Connect ve Azure AD Connect Health yükleme yol haritası. | Microsoft Belgeleri
description: Bu belgede, yükleme seçeneklerinin ve Azure AD Connect ve Connect Health yüklemesi için kullanılabilen yolların genel bakışı sağlanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cddf2ab36b2372ce93ae8a2a6230ccb02ae30117
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721019"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Azure AD Connect ve Azure AD Connect Health yükleme yol haritası

## <a name="install-azure-ad-connect"></a>Azure AD Connect Yükleme

> [!IMPORTANT]
> Microsoft, resmi olarak belgelenen eylemler dışında Azure AD Connect eşitlemesinin değiştirilmesini veya çalıştırılmasını desteklemez. Bu eylemlerden herhangi biri tutarsız veya Azure AD Connect eşitlemeye neden olabilecek bir durum oluşabilir. Sonuç olarak, Microsoft bu tür dağıtımlar için teknik destek sağlayamaz.

Azure AD Connect'i [Microsoft İndirme Merkezi](https://go.microsoft.com/fwlink/?LinkId=615771)'nden indirebilirsiniz.

| Çözüm | Senaryo |
| --- | --- |
| Başlamadan önce - [Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) |<li>Azure AD Connect'i yüklemeye başlamadan önce tamamlamanız gereken adımlar.</li> |
| [Hızlı ayarlar](how-to-connect-install-express.md) |<li>Tek bir AD ormanınız varsa bu seçeneği kullanmanız önerilir.</li> <li>Parola eşitleme özelliğini kullanarak aynı parola ile kullanıcı oturumu açma.</li> |
| [Özelleştirilmiş ayarlar](how-to-connect-install-custom.md) |<li>Birden çok ormanınız olduğunda kullanılır. Birçok şirket içi [topolojiyi](plan-connect-topologies.md) destekler.</li> <li>Oturum açma seçeneğinizi özelleştirin (örneğin, doğrudan kimlik doğrulaması veya federasyon için ADFS) veya 3. taraf bir kimlik sağlayıcısı kullanın.</li> <li>Eşitleme özelliklerini özelleştirin (örneğin, filtreleme ve geri yazma).</li> |
| [DirSync’ten yükseltme](how-to-dirsync-upgrade-get-started.md) |<li>Zaten çalışmakta olan bir DirSync sunucunuz varsa kullanılır.</li> |
| [Azure AD Eşitleme veya Azure AD Connect’ten yükseltme](how-to-upgrade-previous-version.md) |<li>Tercihinize bağlı olarak birkaç farklı yöntem vardır.</li> |

[Yüklemeden sonra](how-to-connect-post-installation.md), programın beklendiği gibi çalıştığını doğrulamanız ve kullanıcılara lisans atamanız gerekir.

### <a name="next-steps-to-install-azure-ad-connect"></a>Azure AD Connect'i Yüklemek için sonraki adımlar
|Konu |Bağlantı|  
| --- | --- |
|Azure AD Connect'i indirme | [Azure AD Connect’i indirme](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Hızlı ayarları kullanarak yükleme | [Azure AD Connect’i hızlı yükleme](./how-to-connect-install-express.md)|
|Özelleştirilmiş ayarları kullanarak yükleme | [Azure AD Connect özel yüklemesi](./how-to-connect-install-custom.md)|
|DirSync'ten yükseltme | [Azure AD eşitleme aracından (DirSync) yükseltme](./how-to-dirsync-upgrade-get-started.md)|
|Yükleme işleminden sonra | [Yüklemeyi doğrulama ve lisansları atama](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Azure AD Connect'i yükleme hakkında daha fazla bilgi edinin
[İşletimsel](how-to-connect-sync-operations.md) sorunlara karşı hazırlıksız olmak istemezsiniz. [Olağanüstü bir durumla](how-to-connect-sync-staging-server.md#disaster-recovery) karşılaştığınızda kolayca üstesinden gelebilmek için yedekte bir sunucu bulundurmak isteyebilirsiniz. Sık sık yapılandırma değişiklikleri yapmayı düşünüyorsanız [hazırlama modu](how-to-connect-sync-staging-server.md) sunucusunu göz önünde bulundurmanız gerekir.

|Konu |Bağlantı|  
| --- | --- |
|Desteklenen topolojiler | [Azure AD Connect için topolojiler](plan-connect-topologies.md)|
|Tasarım kavramları | [Azure AD Connect tasarım kavramları](plan-connect-design-concepts.md)|
|Yükleme için kullanılan hesaplar | [Azure AD Connect kimlik bilgileri ve izinleri hakkında daha fazla bilgi](reference-connect-accounts-permissions.md)|
|İşletimsel planlama | [Azure AD Connect Eşitleme: İşletimsel görevler ve önemli noktalar](how-to-connect-sync-operations.md)|
|Kullanıcı oturumu açma seçenekleri | [Azure AD Connect kullanıcı oturumu açma seçenekleri](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Eşitleme özelliklerini yapılandırma
Azure AD Connect, isteğe bağlı olarak açabileceğiniz veya varsayılan olarak etkin olan çeşitli özellikler sunar. Bazı özellikler için bazen belirli senaryo ve topolojilerde daha fazla yapılandırma gerekebilir.

[Filtreleme](how-to-connect-sync-configure-filtering.md), hangi nesnelerin Azure AD ile eşitleneceğine yönelik bir sınırlama getirmek istediğinizde kullanılır. Varsayılan olarak tüm kullanıcılar, kişiler, gruplar ve Windows 10 yüklü bilgisayarlar eşitlenir. Etki alanlarına, kuruluş birimlerine veya özniteliklere göre filtrelemeyi değiştirebilirsiniz.

[Parola karması eşitleme](how-to-connect-password-hash-synchronization.md), Active Directory'deki parola karmasını Azure AD ile eşitler. Son kullanıcı, şirket içinde ve bulutta aynı parolayı kullanabilir ancak parolayı yalnızca tek bir konumda yönetebilir. Yetkili olarak şirket içi Active Directory'nizi kullandığından, kendi parola ilkenizi de kullanabilirsiniz.

[Parola geri yazma](../authentication/quickstart-sspr.md) özelliği, kullanıcılarınızın buluttaki parolalarını değiştirmelerine ve sıfırlamalarına olanak sağlamanın yanı sıra şirket içi parola ilkenizi uygular.

[Cihaz geri yazma](how-to-connect-device-writeback.md) özelliği, Azure AD 'de kayıtlı bir cihazın, koşullu erişim için kullanılabilmesi amacıyla şirket içi Active Directory geri yazılmasına izin verir.

[Yanlışlıkla silmeleri engelle](how-to-connect-sync-feature-prevent-accidental-deletes.md) özelliği, varsayılan olarak açıktır ve bulut dizininizi aynı anda gerçekleştirilen çoklu silme işlemlerine karşı korur. Varsayılan olarak, her çalıştırma sırasında 500 silme işlemine izin verir. Kuruluşunuzun büyüklüğüne bağlı olarak bu ayarı değiştirebilirsiniz.

[Otomatik yükseltme](how-to-connect-install-automatic-upgrade.md), hızlı ayar yüklemeleri için varsayılan olarak etkindir ve her zaman Azure AD Connect'in güncel olan en son sürümüne sahip olmanızı sağlar.

### <a name="next-steps-to-configure-sync-features"></a>Eşitleme özelliklerini yapılandırmak için sonraki adımlar
|Konu |Bağlantı|  
| --- | --- |
|Filtrelemeyi yapılandırma | [Azure AD Connect eşitleme: Filtrelemeyi yapılandırma](how-to-connect-sync-configure-filtering.md)|
|Parola karması eşitleme | [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md)|
|Doğrudan Kimlik Doğrulama | [Doğrudan kimlik doğrulama](how-to-connect-pta.md)
|Parola geri yazma | [Parola yönetimine başlarken](../authentication/quickstart-sspr.md)|
|Cihaz geri yazma | [Azure AD Connect’te cihaz geri yazma özelliğini etkinleştirme](how-to-connect-device-writeback.md)|
|Yanlışlıkla silmeleri engelleme | [Azure AD Connect eşitleme: Yanlışlıkla Silmeleri Engelleme](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|Otomatik yükseltme | [Azure AD Connect: Otomatik yükseltme](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Azure AD Connect Eşitleme'yi özelleştirme
Azure AD Connect Eşitleme, çoğu müşteri tarafından birçok topoloji ile kullanılmak üzere tasarlanmış varsayılan bir yapılandırmaya sahiptir. Ancak her zaman varsayılan yapılandırmanın işe yaramadığı ve ayarlanması gereken durumlarla karşılaşırsınız. Bu bölümde ve bağlantılı konu başlıklarında açıklandığı üzere, bu gibi değişiklikler desteklenmektedir.

Daha önce bir eşitleme topolojisi kullanmadıysanız [teknik kavramlarda](how-to-connect-sync-technical-concepts.md) açıklanan terimleri ve temel kavramları öğrenmeniz gerekir. Azure AD Connect, MIIS2003, ILM2007 ve FIM2010'un gelişmiş sürümüdür. Bazı özellikleri aynı olsa da bir çok şey değişti.

[Varsayılan yapılandırma](concept-azure-ad-connect-sync-default-configuration.md), yapılandırmada birden çok orman olabileceğini varsayar. Bu topolojilerde bir kullanıcı nesnesi, başka ormandaki bir kişi olarak gösterilebilir. Ayrıca kullanıcının, başka bir kaynak ormanında bağlı bir posta kutusu da olabilir. Varsayılan yapılandırma davranışı, [kullanıcılar ve kişiler](concept-azure-ad-connect-sync-user-and-contacts.md) bölümünde açıklanmaktadır.

Eşitlemedeki yapılandırma modeli, [bildirim temelli hazırlama](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) olarak adlandırılır. Gelişmiş öznitelik akışları, öznitelik dönüşümlerini ifade etmek için [işlevleri](reference-connect-sync-functions-reference.md) kullanır. Azure AD Connect ile birlikte sunulan araçları kullanarak tüm yapılandırmayı inceleyebilirsiniz. Yapılandırma değişiklikleri yapmanız gerekirse [en iyi yöntemleri](how-to-connect-sync-best-practices-changing-default-configuration.md) uyguladığınızdan emin olun , böylece yeni sürümleri daha kolay şekilde benimseyebilirsiniz.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Azure AD Connect Eşitleme'yi özelleştirmek için sonraki adımlar
|Konu |Bağlantı|  
| --- | --- |
|Tüm Azure AD Connect Eşitleme makaleleri | [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md)|
|Teknik kavramlar | [Azure AD Connect eşitleme: Teknik Kavramlar](how-to-connect-sync-technical-concepts.md)|
|Varsayılan yapılandırmayı anlama | [Azure AD Connect eşitleme: Varsayılan yapılandırmayı anlama](concept-azure-ad-connect-sync-default-configuration.md)|
|Kullanıcıları ve kişileri anlama | [Azure AD Connect eşitleme: Kullanıcıları ve Kişileri Anlama](concept-azure-ad-connect-sync-user-and-contacts.md)|
|Bildirim temelli hazırlama | [Azure AD Connect eşitleme: Bildirim Temelli Sağlama İfadelerini Anlama](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Varsayılan yapılandırmayı değiştirme | [Varsayılan yapılandırmanın değiştirilmesine ilişkin önerilen yöntemler](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Federasyon özelliklerini yapılandırma

Azure AD Connect, AD FS kullanarak Azure AD federasyonu yapılandırmayı ve federasyon güveninizi yönetmeyi kolaylaştıran birçok özellik sunar. Azure AD Connect, Windows Server 2012R2 veya üzeri için AD FS desteği sunar.

Azure AD Connect'i federasyon güveninizi yönetmek için kullanmıyor olsanız dahi [AD FS grubunun SSL sertifikasını güncelleştirin](how-to-connect-fed-ssl-update.md).

Grubu gereken şekilde genişletmek için grubunuza [AD FS sunucusu](how-to-connect-fed-management.md#addadfsserver) ekleyin.

Birkaç tıklamayla Azure AD ile [güveni onarın](how-to-connect-fed-management.md#repairthetrust).

ADFS, [birden çok etki alanını](how-to-connect-install-multiple-domains.md) destekleyecek şekilde yapılandırılabilir. Örneğin, federasyon için kullanmanız gereken birden çok üst etki alanınız olabilir.

ADFS sunucunuz sertifikaları Azure AD'den otomatik olarak güncelleştirecek şekilde yapılandırılmadıysa veya ADFS dışında bir çözüm kullanıyorsanız [sertifikaları güncelleştirmeniz](how-to-connect-fed-o365-certs.md) gerektiğinde size bildirilir.

### <a name="next-steps-to-configure-federation-features"></a>Federasyon özelliklerini yapılandırmak için sonraki adımlar
|Konu |Bağlantı|  
| --- | --- |
|Tüm AD FS makaleleri | [Azure AD Connect ve federasyon](how-to-connect-fed-whatis.md)|
|Alt etki alanları bulunan ADFS'yi yapılandırma | [Azure AD ile Federasyon için Çoklu Etki Alanı Desteği](how-to-connect-install-multiple-domains.md)|
|AD FS grubunu yönetme | [Azure AD Connect ile AD FS yönetimi ve özelleştirmesi](how-to-connect-fed-management.md)|
|Federasyon sertifikalarını el ile güncelleştirme | [Office 365 ve Azure AD için Federasyon Sertifikalarını Yenileme](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Azure AD Connect Health ile çalışmaya başlama
Azure AD Connect Health ile çalışmaya başlamak için aşağıdaki adımları kullanın:

1. [Azure AD Premium alın](../fundamentals/active-directory-get-started-premium.md) veya [deneme sürümünü başlatın](https://azure.microsoft.com/trial/get-started-active-directory/).
2. Kimlik sunucularınıza [Azure AD Connect Health Aracılarını indirin ve yükleyin](#download-and-install-azure-ad-connect-health-agent).
3. [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) adresindeki Azure AD Connect Health Panosunu görüntüleyin.

> [!NOTE]
> Azure AD Connect Health panonuzda veri görmeden önce hedeflenen sunucularınıza Azure Connect Health Aracıları yüklemeniz gerektiğini unutmayın.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Azure AD Connect Health Aracısını indirme ve yükleme
* Azure AD Connect Health [gereksinimlerini yerine getirdiğinizden](how-to-connect-health-agent-install.md#requirements) emin olun.
* AD FS için Azure AD Connect Health kullanmaya başlama
    * [AD FS için Azure AD Connect Health Aracısını indirin.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Yükleme talimatlarına bakın](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Eşitleme için Azure AD Connect Health kullanmaya başlama
    * [Azure AD Connect'in en son sürümünü indirip yükleyin](https://go.microsoft.com/fwlink/?linkid=615771). Eşitleme için Durum Aracısı, Azure AD Connect yüklemesinin bir parçası olarak yüklenir (sürüm 1.0.9125.0 veya daha yeni bir sürüm).
* AD DS için Azure AD Connect Health kullanmaya başlama
    * [AD DS için Azure AD Connect Health Aracısını indirin](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Yükleme talimatlarına bakın](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health portalı
Azure AD Connect Health portalı; uyarıları, performans izlemeyi ve kullanım analizini görüntüler. https://aka.ms/aadconnecthealth URL'si sizi Azure AD Connect Health'in ana dikey penceresine götürür. Dikey pencereyi bir pencere olarak düşünebilirsiniz. Ana dikey pencerede **Hızlı Başlangıç**'ı, Azure AD Connect Health'teki hizmetleri ve ek yapılandırma seçeneklerini görebilirsiniz. Aşağıdaki ekran görüntüsünü ve altındaki kısa açıklamaları inceleyin. Aracıları dağıttıktan sonra sistem durumu hizmeti Azure AD Connect Health tarafından izlenen hizmetleri otomatik olarak tanımlar.

> [!NOTE]
> Lisans bilgileri için bkz. [Azure AD Connect Health SSS](reference-connect-health-faq.md) veya [Azure AD Fiyatlandırma sayfası](https://aka.ms/aadpricing).
    
![Azure AD Connect Health Portalı](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Hızlı Başlangıç**: Bu seçeneği belirlediğinizde **Hızlı Başlangıç** dikey penceresi açılır. **Araçları Edinme**'yi seçerek Azure AD Connect Health Aracısı'nı indirebilirsiniz. Ayrıca, belgelere erişebilir ve geri bildirim gönderebilirsiniz.
* **Azure Active Directory Connect (eşitleme)** : Bu seçenek, Azure AD Connect Health'in o anda izlediği Azure AD Connect sunucularınızı gösterir. **Eşitleme hataları** girişi, uygulamaya alınan ilk eşitleme hizmetinizin temel eşitleme hatalarını kategorilere ayrılmış şekilde gösterir. **Eşitleme hizmetleri** girişini seçtiğinizde açılan dikey pencerede Azure AD Connect sunucularınız hakkında bilgiler gösterilir. Özellikler hakkında daha fazla bilgi için bkz. [Eşitleme için Azure AD Connect Health'i kullanma](how-to-connect-health-sync.md).
* **Active Directory Federasyon Hizmetleri**: Bu seçenek, Azure AD Connect Health'in o anda izlediği tüm AD FS hizmetlerini gösterir. Örneklerden birini seçtiğinizde açılan dikey pencerede seçtiğiniz hizmet örneğiyle ilgili bilgiler gösterilir. Bu bilgiler; genel bakışı, özellikleri, uyarıları, izlemeyi ve kullanım analizlerini içerir. Özellikler hakkında daha fazla bilgi için bkz. [Azure AD Connect Health'i AD FS ile kullanma](how-to-connect-health-adfs.md).
* **Active Directory Domain Services**: Bu seçenek, Azure AD Connect Health'in o anda izlediği tüm AD DS ormanlarını gösterir. Ormanlardan birini seçtiğinizde açılan dikey pencerede seçtiğiniz ormanla ilgili bilgiler gösterilir. Bu bilgiler; temel bilgiler, Etki Alanı Denetleyicileri panosu, Çoğaltma Durumu panosu, uyarılar ve izlemeye genel bakış içerir. Özellikler hakkında daha fazla bilgi için bkz. [Azure AD Connect Health'i AD DS ile kullanma](how-to-connect-health-adds.md).
* **Yapılandırma**: Bu bölümde aşağıdakileri açma veya kapatma seçenekleri bulunur:

   - Azure AD Connect Health aracısının en son sürüme **otomatik olarak güncelleştirilmesi** : yeni sürümler kullanılabilir olduğunda Azure AD Connect Health Aracısı otomatik olarak güncelleştirilir. Bu seçenek varsayılan olarak etkindir.
   - Microsoft tarafından yalnızca sorun giderme amacıyla Azure AD dizini bütünlüğünden **verilere erişim** : Bu seçenek etkinleştirilirse, Microsoft Kullanıcı tarafından görüntülenen verilere erişebilir. Bu bilgiler, sorun giderme ve gerekli yardımı sağlamak için yararlı olabilir. Bu seçenek varsayılan olarak devre dışıdır
* **Rol tabanlı erişim denetimi (IAM)** , Connect Health verilerine erişimi rol tabanlı olarak yönetebileceğiniz bölümdür. 

## <a name="next-steps"></a>Sonraki Adımlar

- [Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) 
- [Hızlı ayarlar](how-to-connect-install-express.md)
- [Özelleştirilmiş ayarlar](how-to-connect-install-custom.md)
- [Parola karması eşitlemesi](how-to-connect-password-hash-synchronization.md)|
- [Doğrudan kimlik doğrulama](how-to-connect-pta.md)
- [Azure AD Connect ve federasyon](how-to-connect-fed-whatis.md)
- [Azure AD Connect Health aracılarını yükleme](how-to-connect-health-agent-install.md) 
- [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md)
