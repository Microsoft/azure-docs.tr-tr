---
title: Kimlik yönetimine yardımcı olan Azure Güvenlik özellikleri | Microsoft Docs
description: Kimlik yönetimine yardımcı olan çekirdek Azure Güvenlik özellikleri hakkında bilgi edinin. Çoklu oturum açma ve ters proxy gibi konular hakkında bilgi için bkz..
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2021
ms.author: terrylan
ms.openlocfilehash: 1bb4f22c6ef114a224597ede2b053d076aba5997
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065846"
---
# <a name="azure-identity-management-security-overview"></a>Azure'da kimlik yönetim güvenliğine genel bakış

 Kimlik yönetimi, [güvenlik sorumlularının](/windows/security/identity-protection/access-control/security-principals)kimlik doğrulama ve yetkilendirme işlemidir. Ayrıca, bu sorumlular (kimlikler) hakkındaki bilgilerin denetlenmesini da içerir. Güvenlik sorumluları (kimlikler), hizmetler, uygulamalar, kullanıcılar, gruplar vb. içerebilir. Microsoft kimlik ve erişim yönetimi çözümleri, şirket veri merkezinde ve buluta uygulamalara ve kaynaklara erişimi korumaya yardımcı olur. Bu koruma, Multi-Factor Authentication ve koşullu erişim ilkeleri gibi ek doğrulama düzeyleri sunar. Gelişmiş güvenlik raporlaması, denetim ve uyarı aracılığıyla kuşkulu etkinlikleri izlemek, olası güvenlik sorunlarını azaltmaya yardımcı olur. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) , binlerce bulut yazılım hizmeti (SaaS) uygulaması ve şirket içinde çalıştırdığınız Web uygulamalarına erişim için çoklu oturum açma (SSO) sağlar.
 
Azure Active Directory (Azure AD) güvenlik avantajlarından yararlanarak şunları yapabilirsiniz:

* Karma kuruluşunuzdaki her kullanıcı için tek bir kimlik oluşturup yönetebilir ve kullanıcıları, grupları ve cihazları eşitleyebilirsiniz. 
* Önceden tümleştirilmiş binlerce SaaS uygulaması da dahil olmak üzere uygulamalarınıza SSO erişimi sağlayın.
* Hem şirket içi hem de bulut uygulamaları için Multi-Factor Authentication tabanlı kurallar kullanarak uygulama erişimi güvenliğini sağlayabilirsiniz.
* Azure AD Uygulama Ara Sunucusu aracılığıyla şirket içi Web uygulamalarına güvenli uzaktan erişim sağlayın.

Bu makalenin amacı, kimlik yönetimine yardımcı olan çekirdek Azure Güvenlik özelliklerine genel bir bakış sağlamaktır. Ayrıca, daha fazla bilgi edinmek için her bir özelliğin ayrıntılarını veren makalelerin bağlantılarını sunuyoruz.  

Makale Aşağıdaki temel Azure kimlik yönetimi özelliklerine odaklanmaktadır:

* Çoklu oturum açma
* Ters proxy
* Multi-Factor Authentication
* Azure rol tabanlı erişim denetimi (Azure RBAC)
* Güvenlik izleme, uyarılar ve makine öğrenimi tabanlı raporlar
* Tüketici kimliği ve erişim yönetimi
* Cihaz kaydı
* Privileged Identity Management
* Kimlik koruması
* Karma kimlik yönetimi/Azure AD Connect
* Azure AD erişim gözden geçirmeleri

## <a name="single-sign-on"></a>Çoklu oturum açma

SSO, tek bir kullanıcı hesabı kullanarak yalnızca bir kez oturum açarak, iş yapmak için ihtiyacınız olan tüm uygulamalara ve kaynaklara erişebiliyor demektir. Oturum açtıktan sonra, ihtiyacınız olan tüm uygulamalara (örneğin, bir parola yazmanız) ikinci bir kez erişebilirsiniz.

Birçok kuruluş, kullanıcı üretkenliği için Microsoft 365, Box ve Salesforce gibi SaaS uygulamalarına bağımlıdır. Tarihsel olarak, BT personeli her bir SaaS uygulamasında kullanıcı hesaplarını tek tek oluşturmak ve güncelleştirmek için gereklidir ve kullanıcıların her bir SaaS uygulaması için bir parolayı hatırlamaları gerekiyordu.

Azure AD, şirket içi Active Directory ortamlarını buluta genişleterek, kullanıcıların yalnızca etki alanına katılmış cihazlara ve şirket kaynaklarına değil, aynı zamanda kendi işleri için ihtiyaç duydukları tüm Web ve SaaS uygulamalarına oturum açmasını sağlamak üzere birincil kurumsal hesaplarını kullanmalarına olanak sağlar.

Yalnızca birden çok Kullanıcı adı grubunu ve parolayı yönetmek zorunda olmayan kullanıcılar, kuruluş gruplarına ve bunların çalışan durumlarına göre uygulama erişimini otomatik olarak sağlayabilir veya devre dışı bırakabilirsiniz. Azure AD, Kullanıcı erişimini SaaS uygulamaları genelinde merkezi olarak yönetebileceğiniz güvenlik ve erişim idare denetimleri sunmaktadır.

Daha fazla bilgi edinin:

* [SSO 'ya genel bakış](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Kimlik doğrulama temelleri videosu](https://www.youtube.com/watch?v=fbSVgC8nGz4&feature=emb_title)
* [Uygulama yönetiminde hızlı başlangıç serisi](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>Ters proxy

Azure AD Uygulama Ara Sunucusu, [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) siteleri, [Outlook Web App](/Exchange/clients/outlook-on-the-web/outlook-on-the-web)ve [IIS](https://www.iis.net/)tabanlı uygulamalar gibi şirket içi uygulamaları özel ağınızda yayımlamanıza olanak sağlar ve ağınız dışındaki kullanıcılara güvenli erişim sağlar. Uygulama proxy 'Si, Azure AD 'nin desteklediği binlerce SaaS uygulaması ile birçok şirket içi Web uygulaması türü için uzaktan erişim ve SSO sağlar. Çalışanlar kendi cihazlarındaki uygulamalarınızda oturum açabilir ve bu bulut tabanlı proxy aracılığıyla kimlik doğrulaması yapabilir.

Daha fazla bilgi edinin:

* [Azure AD Uygulama Ara Sunucusu etkinleştiriliyor](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Azure AD Uygulama Ara Sunucusu ile uygulama yayımlama](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Uygulama Ara Sunucusu ile çoklu oturum açma](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Koşullu erişimle çalışma](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication, birden fazla doğrulama yönteminin kullanılmasını gerektiren bir kimlik doğrulama yöntemidir ve Kullanıcı oturum açma işlemlerine ve işlemlerine kritik ikinci bir güvenlik katmanı ekler. Multi-Factor Authentication, kullanıcıların basit bir oturum açma işlemi taleplerini karşılarken veri ve uygulamalara erişimi korumaya yardımcı olur. Bir dizi doğrulama seçeneği aracılığıyla güçlü kimlik doğrulaması sağlar: telefon görüşmeleri, SMS iletileri veya mobil uygulama bildirimleri veya doğrulama kodları ve üçüncü taraf OAuth belirteçleri.

Daha fazla bilgi edinin:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure AD Multi-Factor Authentication nedir?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Azure AD Multi-Factor Authentication nasıl çalışır?](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>Azure RBAC

Azure RBAC, Azure 'da kaynakların ayrıntılı erişim yönetimi sağlayan Azure Resource Manager yerleşik bir yetkilendirme sistemidir. Azure RBAC, kullanıcıların sahip olduğu erişim düzeyini, ne kadar fazla kontrol etmenizi sağlar. Örneğin, bir kullanıcıyı bir kaynak grubundaki tüm kaynakları yönetmek için yalnızca sanal ağları ve başka bir kullanıcıyı yönetecek şekilde sınırlayabilirsiniz. Azure'da kullanabileceğiniz birçok yerleşik rol vardır. Dört temel yerleşik rol aşağıda listelenmiştir. İlk üçü tüm kaynak türleri için geçerlidir.

- [Sahip](../../role-based-access-control/built-in-roles.md#owner): Erişimi başkalarına verme hakkı dahil olmak üzere tüm kaynaklar üzerinde tam erişime sahiptir. 
- [Katkıda Bulunan](../../role-based-access-control/built-in-roles.md#contributor): Her türlü Azure kaynağını oluşturup yönetebilir, ancak başkalarına erişim izni veremez.
- [Okuyucu](../../role-based-access-control/built-in-roles.md#reader) -mevcut Azure kaynaklarını görüntüleyebilir.
- [Kullanıcı Erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator): Azure kaynaklarına kullanıcı erişimini yönetmenizi sağlar.

Daha fazla bilgi edinin:

* [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md)
* [Yerleşik Azure rolleri](../../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Güvenlik izleme, uyarılar ve makine öğrenimi tabanlı raporlar

Tutarsız erişim düzenlerini tanımlayan güvenlik izleme, uyarılar ve makine öğrenimi tabanlı raporlar işletmenizi korumanıza yardımcı olabilir. Azure AD erişim ve kullanım raporlarını, kuruluşunuzun dizininin bütünlüğü ve güvenliğine ilişkin görünürlük elde etmek için kullanabilirsiniz. Bu bilgilerle bir dizin Yöneticisi, olası güvenlik risklerini daha iyi bir şekilde tespit edebilir. böylece, bu riskleri hafifletmek için yeterli planlayabilirler.

Azure portal, raporlar aşağıdaki kategorilere ayrılır:

* **Anomali raporları**: anormal olmak üzere bulduğumuz oturum açma olaylarını içerir. Amacınız, bu etkinlikleri haberdar etmek ve bir olayın şüpheli olup olmadığını belirlemenizi sağlamaktır.
* **Tümleşik uygulama raporları**: bulut uygulamalarının kuruluşunuzda nasıl kullanıldığına ilişkin öngörüler sağlar. Azure AD, binlerce bulut uygulaması ile tümleştirme sunar.
* **Hata raporları**: dış uygulamalara hesap sağladığınızda oluşabilecek hataları belirtin.
* **Kullanıcıya özel raporlar**: belirli bir kullanıcı için cihaz oturum açma etkinlik verilerini görüntüler.
* **Etkinlik günlükleri**: son 24 saat, son 7 gün veya son 30 gün içinde denetlenen tüm olayların bir kaydını içerir ve etkinlik değişikliklerini ve parola sıfırlama ve kayıt etkinliğini gruplandırın.

Daha fazla bilgi edinin:

* [Erişim ve kullanım raporlarınızı görüntüleme](../../active-directory/reports-monitoring/overview-reports.md)
* [Azure Active Directory raporlama ile çalışmaya başlama](../../active-directory/reports-monitoring/overview-reports.md)
* [Azure Active Directory Raporlama Kılavuzu](../../active-directory/reports-monitoring/overview-reports.md)

## <a name="consumer-identity-and-access-management"></a>Tüketici kimliği ve erişim yönetimi

Azure AD B2C, yüz milyonlarca kimliği ölçeklendirirken tüketiciye yönelik uygulamalar için yüksek düzeyde kullanılabilir, küresel bir kimlik yönetimi hizmetidir. Bu hizmet mobil platformlar ve web platformlarıyla tümleştirilebilir. Tüketicileriniz, var olan sosyal hesaplarını kullanarak veya yeni kimlik bilgileri oluşturarak özelleştirilebilen deneyimler aracılığıyla tüm uygulamalarınızda oturum açabilir.

Geçmişte, müşterileri kaydetmek ve uygulamalarına abone olmak isteyen uygulama geliştiricileri kendi kodlarını yazırdı. Ayrıca, kullanıcı adları ile parolaları depolamak için şirket içi veritabanlarını veya sistemleri kullanırlardı. Azure AD B2C, kuruluşunuzun tüketici kimlik yönetimini, güvenli, standartlara dayalı bir platformun ve büyük bir Genişletilebilir ilke kümesinin yardımıyla uygulamalarla tümleştirmenin daha iyi bir yolunu sunar.

Azure AD B2C kullandığınızda, Tüketicileriniz mevcut sosyal hesaplarını (Facebook, Google, Amazon, LinkedIn) kullanarak veya yeni kimlik bilgileri (e-posta adresi ve parola veya Kullanıcı adı ve parola) oluşturarak uygulamalarınıza kaydolabilir.

Daha fazla bilgi edinin:

* [Azure Active Directory B2C nedir?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C önizlemesi: Tüketicilerinizin uygulamanıza kaydolmalarını ve oturum açmalarını sağlama](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C önizlemesi: uygulama türleri](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Cihaz kaydı

Azure AD cihaz kaydı, cihaz tabanlı [koşullu erişim](../../active-directory/devices/device-management-azure-portal.md) senaryolarının temelini oluşturacak. Bir cihaz kaydedildiğinde Azure AD cihaz kaydı, bir Kullanıcı oturum açtığında cihazın kimliğini doğrulamak için kullandığı bir kimlikle cihaza sağlar. Daha sonra bulutta ve şirket içinde barındırılan uygulamalar için koşullu erişim ilkelerini zorlamak üzere, kimliği doğrulanmış cihaz ve cihazın öznitelikleri kullanılabilir.

Intune gibi bir mobil cihaz yönetimi çözümüyle birlikte kullanıldığında, Azure AD 'deki cihaz öznitelikleri cihaz hakkındaki ek bilgilerle güncelleştirilir. Daha sonra, güvenlik ve uyumluluk standartlarınızı karşılamak üzere cihazlardan erişimi zorlayan koşullu erişim kuralları oluşturabilirsiniz.

Daha fazla bilgi edinin:

* [Azure AD cihaz kaydı ile çalışmaya başlama](../../active-directory/devices/device-management-azure-portal.md)
* [Windows etki alanına katılmış cihazlar için Azure AD ile otomatik cihaz kaydı](../../active-directory/devices/hybrid-azuread-join-plan.md)
* [Windows etki alanına katılmış cihazların Azure AD ile otomatik kaydını ayarlama](../../active-directory/devices/hybrid-azuread-join-plan.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Azure AD Privileged Identity Management ile, ayrıcalıklı kimliklerinizi yönetebilir, denetleyebilir ve izleyebilir, Azure AD 'deki kaynaklara ve Microsoft 365 ve Microsoft Intune gibi diğer Microsoft çevrimiçi hizmetler erişebilirsiniz.

Kullanıcıların bazen Azure 'da veya Microsoft 365 kaynaklarında veya diğer SaaS uygulamalarında ayrıcalıklı işlemler gerçekleştirmesi gerekir. Bu gerek genellikle kuruluşların Azure AD 'de kullanıcılara kalıcı ayrıcalıklı erişim vermesi gerektiği anlamına gelir. Kuruluşlar, kullanıcıların yönetici ayrıcalıklarıyla ne yaptığını yeterince izleyemediği için bulutta barındırılan kaynaklarla ilgili büyümekte olan bir güvenlik riskidir. Ayrıca, ayrıcalıklı erişimi olan bir kullanıcı hesabı tehlikeye atılırsa, bir ihlal, kuruluşun genel bulut güvenliğini etkileyebilir. Azure AD Privileged Identity Management, bu riskin azaltılmasına yardımcı olur.

Azure AD Privileged Identity Management, şunları yapabilirsiniz:

* Hangi kullanıcıların Azure AD yöneticileri olduğunu görün.
* İsteğe bağlı, tam zamanında (JıT) Microsoft 365 ve Intune gibi Microsoft hizmetlerine yönelik yönetici erişimini etkinleştirin.
* Yönetici erişim geçmişi ve yönetici atamalarındaki değişiklikler hakkında rapor alın.
* Ayrıcalıklı bir role erişim hakkında uyarı alın.

Daha fazla bilgi edinin:

* [Azure AD Privileged Identity Management nedir?](../../active-directory/privileged-identity-management/pim-configure.md)
* [PıM 'de Azure AD dizin rolleri atama](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Kimlik koruması

Azure AD Kimlik Koruması, risk algılamaları ve kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarına birleştirilmiş bir görünüm sağlayan bir güvenlik hizmetidir. Kimlik koruması, Azure AD anomalous etkinlik raporları aracılığıyla kullanılabilen mevcut Azure AD anomali algılama yetilerinden yararlanır. Kimlik koruması, anormallikleri gerçek zamanlı olarak tespit eden yeni risk algılama türleri de sunar.

Daha fazla bilgi edinin:

* [Azure AD Kimlik Koruması](../../active-directory/identity-protection/overview-identity-protection.md)
* [Channel 9: Azure AD ve kimlik Show: kimlik koruması önizlemesi](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Karma kimlik yönetimi/Azure AD Connect

Microsoft’un kimlik çözümleri şirket içi ve bulut tabanlı çözümleri birleştirerek konumdan bağımsız olarak tüm kaynaklara kimlik doğrulaması ve yetkilendirme sağlamak üzere tek bir kullanıcı kimliği oluşturur. Buna hibrit kimlik denir. Azure AD Connect, Microsoft'un karma kimlik hedeflerinizi karşılamak ve gerçekleştirmek için tasarladığı araçtır. Bu sayede kullanıcılarınıza Azure AD ile tümleşik Microsoft 365, Azure ve SaaS uygulamaları için ortak bir kimlik sağlayabilirsiniz. Aşağıdaki özellikleri sağlar:

* Eşitleme
* AD FS ve Federasyon tümleştirmesi
* Doğrudan kimlik doğrulama
* Sistem Durumunu İzleme

Daha fazla bilgi edinin:

* [Karma kimlik teknik incelemesi](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD ekibi blogu](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleri

Azure Active Directory (Azure AD) erişim gözden geçirmeleri, kuruluşların grup üyeliklerini, kurumsal uygulamalara erişimi ve ayrıcalıklı rol atamalarını verimli şekilde yönetmesine olanak sağlar.

Daha fazla bilgi edinin:

* [Azure AD erişim gözden geçirmeleri](../../active-directory/governance/access-reviews-overview.md)
* [Azure AD erişim gözden geçirmeleriyle kullanıcı erişimini yönetme](../../active-directory/governance/access-reviews-overview.md)