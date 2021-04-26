---
title: Şirket Içi Iş yükleri için Azure AD bulutu yönetilen yönetimi-Azure
description: Bu konuda, şirket içi iş yükleri için bulut yönetilen yönetimi açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b4d1041b9d330227fadf31f6afc1804174ea2ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96340858"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD, şirket Içi Iş yükleri için bulut tarafından yönetilen yönetim sağlar

Azure Active Directory (Azure AD), kimlik, erişim yönetimi ve güvenlik özelliklerinin tüm yönlerini kapsayan milyonlarca kuruluş tarafından kullanılan kapsamlı bir hizmet olarak kimlik (IDaaS) çözümüdür. Azure AD, milyardan fazla Kullanıcı kimliği tutar ve kullanıcıların oturum açıp güvenli şekilde erişmelerine yardımcı olur:

* Microsoft 365, Azure portal ve diğer hizmet olarak yazılım (SaaS) uygulamaları gibi dış kaynaklar.
* Kuruluşun kurumsal ağındaki ve intranetinde bulunan uygulamalar gibi iç kaynaklar, bu kuruluş tarafından geliştirilen tüm bulut uygulamalarıyla birlikte.

Kuruluşlar, şirket içi iş yüklerine sahip olmaları durumunda ' saf bulut ' veya ' karma ' dağıtım olarak Azure AD 'yi kullanabilir. Azure AD 'nin karma dağıtımı, bir kuruluşun BT varlıklarını buluta geçirebilmek ya da mevcut şirket içi altyapıyı yeni bulut Hizmetleri ile tümleştirmeye devam etmek için bir stratejinin parçası olabilir.

Tarihsel olarak, ' hibrit ' kuruluşları Azure AD 'yi mevcut şirket içi altyapısının bir uzantısı olarak gördük. Bu dağıtımlarda, şirket içi kimlik idare yönetimi, Windows Server Active Directory veya diğer şirket içi dizin sistemleri, denetim noktalarıdır ve kullanıcılar ve gruplar bu sistemlerden Azure AD gibi bir bulut dizinine eşitlenir. Bu kimlikler bulutta olduktan sonra, Microsoft 365, Azure ve diğer uygulamalar için kullanılabilir hale getirilebilir.

![Kimlik yaşam döngüsü](media/cloud-governed-management-for-on-premises//image1.png)

Kuruluşlar, BT altyapılarını ve uygulamalarına yönelik uygulamalarını buluta taşıdığından, çoğu kimlik yönetimi için gelişmiş güvenlik ve basitleştirilmiş yönetim özelliklerine bakıyor. Azure AD 'deki buluta teslim edilen IDaaS özellikleri, kuruluşların kendi kimlik yönetimini geleneksel şirket içi sistemlerden Azure AD 'ye hızla benimseme ve taşımasına imkan tanıyan çözümler ve yetenekler sunarak bulut yönetilen yönetimine geçişi hızlandırır. böylece, yeni uygulamaları desteklemeye devam edebilirsiniz.

Bu yazıda, Microsoft 'un karma IDaaS stratejisi özetlenmektedir ve kuruluşların mevcut uygulamaları için Azure AD 'yi nasıl kullanabileceği açıklanmaktadır.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Bulut tarafından yönetilen kimlik yönetimi için Azure AD yaklaşımı

Kuruluşlar buluta geçiş yaparken, tam ortamları üzerinde denetimlere sahip oldukları, daha fazla güvenlik ve etkinliklere daha fazla görünürlük, Otomasyon tarafından desteklenen ve proaktif içgörüler hakkında daha fazla bilgi sahibi olmaları gerekir. "**Bulut yönetilen yönetimi**", kuruluşların kullanıcılarını, uygulamalarını, gruplarını ve cihazlarını buluttan nasıl yönetip yöneteceğini açıklar.

Bu modern dünyada, SaaS uygulamalarının uzamasını ve işbirliği ve dış kimliklerin artan rolünü nedeniyle kuruluşların ölçeği etkin bir şekilde yönetebilmeleri gerekir. Bulutun yeni risk dünyası, bir kuruluşun daha fazla yanıt vermesi gerektiği anlamına gelir. bir bulut kullanıcısını kapatan kötü niyetli bir aktör, bulutu ve şirket içi uygulamaları etkileyebilir.

Özellikle, karma kuruluşların, el ile yaptığımız görevleri devredebilir ve otomatikleştirebilmeleri gerekir. Görevleri otomatikleştirmek için, kimlik ile ilgili farklı kaynakların (kullanıcılar, gruplar, uygulamalar, cihazlar) yaşam döngüsünü düzenleyen API 'Ler ve süreçler olması gerekir. bu sayede, bu kaynakların gündelik yönetimini, çekirdek BT personelinin dışında daha fazla kişiye atayabilirler. Azure AD, şirket içi kimlik altyapısına gerek duymadan Kullanıcı hesabı yönetimi ve kullanıcılar için yerel kimlik doğrulama aracılığıyla bu gereksinimleri ele alır. Şirket içi altyapı oluşturma, şirket içi dizinlerinde olmayan, ancak erişim yönetimi iş sonuçlarını elde etmek için kritik olan iş ortakları gibi yeni kullanıcı topluluklarına sahip kuruluşlara faydalanabilir.

Ayrıca, yönetimi, idare---olmadan tamamlanmaz ve bu yeni dünyada idare, kimlik yerine bir eklenti değil, kimlik sisteminin tümleşik bir parçasıdır. Kimlik yönetimi, kuruluşlara çalışanlar, iş ortakları ve satıcılar, hizmetler ve uygulamalar genelinde kimlik ve erişim yaşam döngüsünü yönetme olanağı sunar.

Kimlik yönetimi eklemek, kuruluşun bulut yönetilen yönetimine geçiş yapmasına olanak tanır, bu sayede, konukların ölçeklendirilmesine, yeni güçlüklere yönelik daha fazla bilgi sağlar ve şirket içi altyapıyla müşterilerin sahip olduğu daha derin Öngörüler ve otomasyon sağlar. Bu yeni dünyada idare, bir kuruluşun kuruluştaki kaynaklara erişimi üzerinde saydamlığın, görünürlüğün ve uygun denetimlerin olduğu anlamına gelir. Azure AD ile, güvenlik işlemleri ve denetim ekipleri,---sahip oldukları ve kuruluştaki kaynaklara (hangi cihazlarda), bu kullanıcılarla ne yaptığını ve kuruluşun veya mevzuata ilkelerine uygun olarak erişimi kaldırmak veya kısıtlamak için uygun denetimleri kullanıp kullanmadığını gösteren görünürlüğe sahiptir.

Yeni yönetim modeli, kuruluşların bu uygulamalara erişimi daha kolay yönetebilmeleri ve güvenli hale getirmek için hem SaaS hem de iş kolu (LOB) uygulamalarıyla faydalanır. Uygulamaları Azure AD ile tümleştirerek, kuruluşlar hem bulutta hem de şirket içinde kaynaklı kimliklerde erişimi sürekli olarak kullanabilir ve yönetebilir. Uygulama yaşam döngüsü yönetimi daha otomatikleştirilir ve Azure AD, şirket içi kimlik yönetiminde kolayca ulaşılabilir olmayan uygulama kullanımı hakkında zengin öngörüler sağlar. Kuruluşlar, Azure AD, Microsoft 365 grupları ve takımlar self servis özellikleri aracılığıyla, erişim yönetimi ve işbirliği için grupları kolayca oluşturabilir ve işbirliği ve erişim yönetimi gereksinimlerini etkinleştirmek için bulutta kullanıcı ekleyebilir veya kaldırabilir.

Bulut yönetilen yönetimi için doğru Azure AD özelliklerini seçmek, kullanılacak uygulamalara ve bu uygulamaların Azure AD ile nasıl tümleştirileğinize bağlıdır. Aşağıdaki bölümlerde, AD ile tümleşik uygulamalar ve Federasyon protokolleri kullanan uygulamalar (örneğin, SAML, OAuth veya OpenID Connect) için gerçekleştirilecek yaklaşımlar ana hatlarıyla verilmiştir.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD ile tümleşik uygulamalar için bulut yönetilen yönetimi

Azure AD, kuruluşun şirket içi Active Directory tümleşik uygulamalarının yönetimini, güvenli uzaktan erişim ve bu uygulamalara koşullu erişim yoluyla geliştirir. Ayrıca, Azure AD, kullanıcının mevcut AD hesapları için hesap yaşam döngüsü yönetimi ve kimlik bilgisi yönetimi de sağlar; örneğin:

* **Şirket içi uygulamalar için güvenli uzaktan erişim ve koşullu erişim**

Birçok kuruluşta, şirket içi AD ile tümleşik Web ve uzak masaüstü tabanlı uygulamalar için buluttan erişimi yönetmenin ilk adımı, güvenli uzaktan erişim sağlamak için bu uygulamaların önüne [uygulama proxy 'si](../manage-apps/application-proxy.md) dağıtmaktır.

Azure AD 'de çoklu oturum açma işleminden sonra, kullanıcılar harici bir URL veya dahili uygulama portalı aracılığıyla hem buluta hem de şirket içi uygulamalara erişebilir. Örneğin, uygulama proxy 'Si Uzak Masaüstü, SharePoint ve Tableau ve Qlik ve iş kolu (LOB) uygulamaları gibi uygulamalar için uzaktan erişim ve çoklu oturum açma sağlar. Ayrıca koşullu erişim ilkeleri, [kullanım koşullarını](../conditional-access/terms-of-use.md) görüntülemeyi ve kullanıcının bir uygulamaya erişmeden önce [bunları kabul etmiş olmasını sağlar](../conditional-access/require-tou.md) .

![Uygulama proxy 'Si mimarisi](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory hesapları için otomatik yaşam döngüsü yönetimi**

Kimlik yönetimi, kuruluşların *üretkenlik* ---, kuruluşa ne zaman sahip oldukları kaynaklara ne kadar hızlı bir şekilde erişim sağlayabileceğini bir denge elde etmesine yardımcı olur. ---ve *güvenlik* ---, bu kişinin iş durumu ne zaman değişir? Kimlik yaşam döngüsü yönetimi, kimlik yönetimi için temel ve ölçekteki etkili idare, uygulamalar için kimlik yaşam döngüsü yönetim altyapısını modernleştirmeyi gerektirir.

Birçok kuruluş için, çalışanlar için kimlik yaşam döngüsü, insan büyük yönetim (HCM) sisteminde söz konusu kullanıcının gösterimine bağlıdır. Azure AD, iş Workday 'yi HCM sistemi olarak kullanan kuruluşlar için, AD 'deki Kullanıcı hesaplarının iş [günü içindeki çalışanlar için otomatik olarak sağlanması ve sağlanması](../saas-apps/workday-inbound-tutorial.md)garanti altına alabilir. Bunun yapılması, bir Kullanıcı rolleri değiştirdiğinde veya kuruluştan ayrıldığında uygulama erişiminin otomatik olarak güncelleştirilmesini sağlamak için, ilk olarak, Doğum dışı hesapların Otomasyonu aracılığıyla geliştirilmiş Kullanıcı üretkenliğini sağlar ve riski yönetir. Workday tabanlı Kullanıcı sağlama [dağıtım planı](https://aka.ms/WorkdayDeploymentPlan) , kuruluşların beş adımlı bir Işlemde Kullanıcı sağlama çözümüne Active Directory için en iyi yöntemler uygulaması aracılığıyla kuruluşlara yol gösteren adım adım bir kılavuzdur.

Azure AD Premium ayrıca, SAP, Oracle eBusiness ve Oracle PeopleSoft dahil diğer şirket içi HCM sistemlerinden kayıtları içeri aktarabilen Microsoft Identity Manager de içerir.

İşletmeden işletmeye işbirliği, kuruluşunuzun dışındaki kişilere erişim verilmesini gerektirir. [Azure AD B2B](/azure/active-directory/b2b/) işbirliği, kuruluşların kendi şirket verileri üzerinde denetim sağlarken Konuk kullanıcılar ve dış iş ortaklarıyla uygulamalarını ve hizmetlerini güvenli bir şekilde paylaşmasına olanak sağlar.

Azure AD, [Konuk kullanıcılar IÇIN ad 'de otomatik olarak hesap oluşturabilir](../external-identities/hybrid-cloud-to-on-premises.md) ve iş konuklarının diğer parolaya gerek kalmadan ŞIRKET içi ad ile tümleşik uygulamalara erişmesini sağlar. Kuruluşlar, [Konuk Kullanıcı için Multi-Factor Authentication (MFA) ilkeleri](../external-identities/conditional-access.md)ayarlayarak uygulama proxy kimlik doğrulaması sırasında MFA denetimleri gerçekleştirilir. Ayrıca, bulut B2B kullanıcıları üzerinde gerçekleştirilen tüm [erişim İncelemeleri](../governance/manage-guest-access-with-access-reviews.md) şirket içi kullanıcılara uygulanır. Örneğin, bulut kullanıcısı yaşam döngüsü yönetim ilkeleriyle silinirse şirket içi kullanıcı da silinir.

**Active Directory hesapları Için kimlik bilgisi yönetimi** Azure AD 'nin self servis parola sıfırlaması, parolalarının unuttutığı kullanıcıların, Şirket [içi Active Directory yazılan](../authentication/concept-sspr-writeback.md)parola ile parolalarını yeniden doğrulaması ve sıfırlamalarını sağlar. Parola sıfırlama işlemi, şirket içi Active Directory parola ilkelerini de kullanabilir: bir Kullanıcı parolasını sıfırladığında, bu dizine işlemeden önce şirket içi Active Directory ilkesini karşıladığından emin olmak denetlenir. Self servis parola sıfırlama [dağıtım planı](../authentication/howto-sspr-deployment.md) , Web ve Windows ile tümleşik deneyimler aracılığıyla kullanıcılara self servis parola sıfırlamasını sağlamak için en iyi uygulamaları özetler.

![Azure AD SSPR mimarisi](media/cloud-governed-management-for-on-premises/image3.png)

Son olarak, kullanıcıların AD 'de parolalarını değiştirmesine izin veren kuruluşlarda, AD, kuruluşun Azure AD [parola koruması özelliği](../authentication/concept-password-ban-bad-on-premises.md)aracılığıyla Azure AD 'de kullanıldığı şekilde aynı parola ilkesini kullanacak şekilde yapılandırılabilir, ancak şu anda genel önizlemede.

Bir kuruluş, uygulamayı barındıran işletim sistemini Azure 'a taşıyarak bir AD ile tümleşik uygulamayı buluta taşımaya hazırsanız [Azure AD Domain Services](../../active-directory-domain-services/overview.md) , ad ile uyumlu etki alanı Hizmetleri (etki alanına katılması, Grup ILKESI, LDAP ve Kerberos/NTLM kimlik doğrulaması gibi) sağlar. Azure AD Domain Services, kuruluşun mevcut Azure AD kiracısı ile tümleşir ve kullanıcıların kurumsal kimlik bilgilerini kullanarak oturum açmasını olanaklı kılar. Ayrıca, mevcut gruplar ve Kullanıcı hesapları, kaynaklara erişimi güvenli hale getirmek için kullanılabilir ve şirket içi kaynakların Azure altyapı hizmetlerine daha yumuşak bir şekilde ' açık ve aşağı kaydırmasını sağlar.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Şirket içi Federasyon tabanlı uygulamalar için bulut yönetilen yönetimi

Şirket içi kimlik sağlayıcısı kullanan bir kuruluş için, uygulamaları Azure AD 'ye taşımak daha güvenli erişim ve Federasyon yönetimi için daha kolay bir yönetim deneyimi sağlar. Azure AD, Azure AD Multi-Factor Authentication dahil olmak üzere ayrıntılı uygulama başına erişim denetimlerini Azure AD koşullu erişim kullanarak yapılandırmaya izin vermez. Azure AD, uygulamaya özel belirteç imzalama sertifikaları ve yapılandırılabilir sertifika sona erme tarihleri dahil olmak üzere daha fazla özelliği destekler. Bu yetenekler, Araçlar ve yönergeler kuruluşların şirket içi kimlik sağlayıcılarını devre dışı bırakamalarına olanak sağlar. Microsoft 'un tek bir örneği olan 17.987 uygulamalarını Microsoft 'un Dahili Active Directory Federasyon Hizmetleri (AD FS) (AD FS) Azure AD 'ye taşımıştır.

![Azure AD evrimi](media/cloud-governed-management-for-on-premises/image5.png)

Federasyon uygulamalarını kimlik sağlayıcısı olarak Azure AD 'ye geçirmeye başlamak için şu https://aka.ms/migrateapps bağlantıları içeren öğesine başvurun:

* Geçiş avantajlarının yanı sıra, açıkça Seviyelendirilmiş dört aşamada geçişin nasıl planlanacağını gösteren [Azure Active Directory, uygulamalarınızı geçirme](https://aka.ms/migrateapps/whitepaper)teknik incelemesi: bulma, sınıflandırma, geçiş ve devam eden yönetim. İşlemin nasıl düşündüğünü ve projenizi kullanımı kolay parçalara nasıl başlayacağınızı öğreneceksiniz. Belge genelinde, bu şekilde size yardımcı olacak önemli kaynakların bağlantıları bulunur.

* Çözüm Kılavuzu, uygulama [kimlik doğrulamasını Active Directory Federasyon Hizmetleri (AD FS) ' den Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) bir uygulama geçişi projesi planlama ve yürütme ile aynı dört aşamayı daha ayrıntılı bir şekilde ele geçirmektedir. Bu kılavuzda, bir uygulamayı Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ' dan Azure AD 'ye taşımaya yönelik belirli bir hedefe bu aşamaların nasıl uygulanacağını öğreneceksiniz.

* [Active Directory Federasyon Hizmetleri (AD FS) geçişi hazırlık betiği](https://aka.ms/migrateapps/adfstools) mevcut şirket içi Active Directory Federasyon Hizmetleri (AD FS) (AD FS) sunucularında çalıştırılabilir ve bu da Azure AD 'ye geçiş için uygulamaların hazır olduğunu tespit edebilir.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Bulut ve şirket içi uygulamalar arasında devam eden erişim yönetimi

Kuruluşların ölçeklenebilir erişimi yönetmesi için bir işlem gerekir. Kullanıcılar, erişim haklarını birikmeye devam eder ve başlangıçta bunlar için sağlananların ötesinde daha fazla. Ayrıca, kurumsal kuruluşların, erişim ilkesini ve denetimleri sürekli olarak geliştirmeye ve zorlayabilmeleri için verimli bir şekilde ölçeklendirilebilecek olması gerekir.

Genellikle, iş karar mekanizmalarının onay kararlarını devreder. Ayrıca, kullanıcıların kendilerini de içerebilir. Örneğin, Avrupa 'daki bir şirketin pazarlama uygulamasındaki gizli müşteri verilerine erişen kullanıcıların şirket ilkelerini bilmeleri gerekir. Konuk kullanıcılar ayrıca, davet edildikleri bir kuruluştaki veriler için işleme gereksinimlerinin farkında olabilir.

Kuruluşlar [,](../enterprise-users/groups-dynamic-membership.md) [SaaS uygulamalarına](../saas-apps/tutorial-list.md)Kullanıcı hazırlama veya [etki alanları arası kimlik yönetimi (SCIM) standardı kullanılarak tümleştirilmiş uygulamalar](../app-provisioning/use-scim-to-provision-users-and-groups.md)gibi teknolojiler aracılığıyla erişim yaşam döngüsü sürecini otomatik hale getirebilir. Kuruluşlar, hangi [Konuk kullanıcıların şirket içi uygulamalara erişebileceğini](../external-identities/hybrid-cloud-to-on-premises.md)de denetleyebilir. Bu erişim hakları daha sonra yinelenen [Azure AD erişim İncelemeleri](../governance/access-reviews-overview.md)kullanılarak düzenli olarak gözden geçirilebilir.

## <a name="future-directions"></a>Gelecekteki yönergeler

Karma ortamlarda, Microsoft 'un stratejisi, **bulutun kimlik için denetim düzlemi olduğu**, şirket içi dizinlerin ve Active Directory ve diğer şirket içi uygulamalar gibi diğer kimlik sistemlerinin erişimi olan kullanıcıları sağlamaya yönelik hedef olan dağıtımları etkinleştirmektir. Bu strateji, bu uygulama ve iş yüklerindeki haklara, kimliklere ve bunlara dayalı erişim sağlamaya devam edecektir. Bu bitiş durumunda kuruluşlar, son kullanıcı üretkenliğini tamamen buluttan çalıştırabilecektir.

![Azure AD mimarisi](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu yolculuğa nasıl başlacağınız hakkında daha fazla bilgi için, adresinde bulunan Azure AD Dağıtım planlarına bakın <https://aka.ms/deploymentplans> . Azure Active Directory (Azure AD) yeteneklerini dağıtma hakkında uçtan uca yönergeler sağlar. Her plan, yaygın Azure AD yeteneklerini başarıyla almak için gereken iş değerini, planlama konularını, tasarımı ve işletimsel yordamları açıklar. Microsoft, Azure AD ile buluttan yönetmeye yönelik yeni yetenekler eklediğimiz zaman, müşteri dağıtımlarından ve diğer geri bildirimlerden öğrenilen en iyi uygulamalarla dağıtım planlarını sürekli olarak güncelleştirir.