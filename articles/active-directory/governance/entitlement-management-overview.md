---
title: Azure AD yetkilendirme yönetimi nedir? -Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimine genel bakış ve bu bilgileri, iç ve dış kullanıcılar için gruplara, uygulamalara ve SharePoint Online sitelerine erişimi yönetmek üzere nasıl kullanabileceğinizi öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd72bba6e8c01644adebaa333c8fe588c35b8c7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927310"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetimi nedir?

Azure Active Directory (Azure AD) yetkilendirme yönetimi, kuruluşların kimlik ve erişim yaşam döngüsünü ölçekteki yönetmesine olanak tanıyan bir [kimlik idare](identity-governance-overview.md) özelliğidir. böylece, erişim isteği iş akışlarına, erişim atamalarına, incelemelerine ve süre sonuna erişin.

Kuruluşlardaki çalışanların işlerini gerçekleştirmek için çeşitli gruplar, uygulamalar ve sitelere erişmesi gerekir. Gereksinimler değiştikçe bu erişimin yönetilmesi zorlayıcı bir şekilde, yeni uygulamalar eklendikçe veya kullanıcıların ek erişim haklarına ihtiyacı vardır.  Bu senaryo, dış kuruluşlar ile işbirliği yaptığınızda daha karmaşıktır. diğer kuruluşun kuruluşunuzun kaynaklarına erişmesi gerektiğini bilmiyor olabilirsiniz ve kuruluşunuzun hangi uygulamaları, grupları veya siteleri kullandığını bilmez.

Azure AD Yetkilendirme Yönetimi, iç kullanıcılar için gruplara, uygulamalara ve SharePoint Online sitelerine erişimi daha verimli bir şekilde yönetmenize ve kuruluşunuzun dışındaki kullanıcılar için bu kaynaklara erişmesi gerekir.

## <a name="why-use-entitlement-management"></a>Yetkilendirme Yönetimi neden kullanılmalıdır?

Kurumsal kuruluşlar, çalışanların şu gibi kaynaklara erişimini yönetirken genellikle güçlüklere sahiptir:

- Kullanıcılar sahip olmaları gereken erişimi bilmiyor olabilir ve olsalar bile, erişimleri onaylamak için doğru bireyleri bulmaya zorluk gösterebilir
- Kullanıcılar bir kaynağa erişim bulup aldıktan sonra, iş amaçları için gerekenden daha uzun bir süre erişim için açık kalabilir

Bu sorunlar, tedarik zinciri kuruluşlarından veya diğer iş ortaklarından gelen harici kullanıcılar gibi başka bir kuruluştan erişmesi gereken kullanıcılar için bir bileşim oluşturur. Örneğin:

- Başka bir kişi, diğer kuruluşun dizinlerindeki tüm kişileri davet edemeyecek şekilde hiç kimse bilmez
- Bu kullanıcılar davet edebilse bile, bu kuruluştan hiç kimse kullanıcının tüm erişimini tutarlı bir şekilde yönetmeyi anımsayabilir

Azure AD Yetkilendirme Yönetimi, bu zorlukları ele almanıza yardımcı olabilir.  Müşterilerin Azure AD Yetkilendirme Yönetimi 'ni nasıl kullandığı hakkında daha fazla bilgi edinmek için, [Avanade örnek olay incelemesi](https://aka.ms/AvanadeELMCase) ve [Centrica örnek olay incelemesi](https://aka.ms/CentricaELMCase)okuyabilirsiniz.  Bu videoda, yetkilendirme yönetimine genel bakış ve bunun değeri verilmiştir:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Yetkilendirme yönetimiyle ne yapabilirim?

Yetkilendirme yönetiminin bazı özellikleri şunlardır:

- Yönetici olmayanların erişim paketleri oluşturma yeteneğini temsil edin. Bu erişim paketleri kullanıcıların isteyebileceği kaynakları içerir ve temsilci erişim paketi yöneticileri, kullanıcıların isteyebileceği, erişimleri onaylaması gereken ve erişimin ne zaman sona ermeyeceği kurallarla ilke tanımlayabilir.
- Kullanıcıları erişim isteğinde bulunan bağlı kuruluşları seçin.  Dizininizde henüz olmayan bir Kullanıcı erişim isteğinde bulunduğunda ve onaylanırsa, otomatik olarak dizininize davet edilir ve erişim atanır.  Erişiminin süresi dolmuşsa, başka bir erişim paketi ataması yoksa, dizininizdeki B2B hesabı otomatik olarak kaldırılabilir.

[İlk erişim paketinizi oluşturmak için öğreticimize](entitlement-management-access-package-first.md)başlamanızı sağlayabilirsiniz. Ayrıca, [Genel senaryoları](entitlement-management-scenarios.md)okuyabilir veya aşağıdakiler dahil olmak üzere videoları izleyebilirsiniz.

- [Kuruluşunuzda Azure AD yetkilendirme yönetimi nasıl dağıtılır](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Azure AD Yetkilendirme Yönetimi kullanımını izleme ve ölçeklendirme](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Yetkilendirme yönetiminde temsilci seçme](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Erişim paketleri nedir ve bunlarla hangi kaynakları yönetebilirim?

Yetkilendirme Yönetimi, Azure AD 'ye bir *erişim paketi*kavramını tanıtır. Erişim paketi, kullanıcının bir proje üzerinde çalışması veya görevlerini gerçekleştirmesi için gereken erişime sahip tüm kaynakların bir paketidir. Erişim paketleri, dahili çalışanlarınızın ve ayrıca kuruluşunuzun dışındaki kullanıcıların erişimini yönetmek için kullanılır.

 Kullanıcıların yetkilendirme yönetimiyle erişimini yönetebileceğiniz kaynak türleri şunlardır:

- Azure AD güvenlik gruplarının üyeliği
- Office 365 gruplarının ve takımların üyeliği
- SaaS uygulamaları ve Federasyon/çoklu oturum açma ve/veya sağlamayı destekleyen özel tümleşik uygulamalar dahil olmak üzere Azure AD kurumsal uygulamalarına atama
- SharePoint Online sitelerinin üyeliği

Ayrıca, Azure AD güvenlik gruplarına veya Office 365 gruplarına bağlı diğer kaynaklara erişimi de denetleyebilirsiniz.  Örneğin:

- Erişim paketindeki bir Azure AD güvenlik grubunu kullanarak ve bu grup için [grup tabanlı lisanslamayı](../users-groups-roles/licensing-groups-assign.md) yapılandırarak, Microsoft Office 365 için kullanıcılara lisans verebilirsiniz
- Erişim paketindeki bir Azure AD güvenlik grubunu kullanarak ve bu grup için bir [Azure rol ataması](../../role-based-access-control/role-assignments-portal.md) oluşturarak kullanıcılara Azure kaynaklarını yönetmeye yönelik erişim izni verebilirsiniz.

## <a name="how-do-i-control-who-gets-access"></a>Kimin erişimi olan Nasıl yaparım? denetimi?

Bir erişim paketiyle yönetici veya temsilci erişimi paketi Yöneticisi, kaynakları (gruplar, uygulamalar ve siteler) ve kullanıcıların bu kaynaklar için ihtiyaç duyduğu rolleri listeler.

Erişim paketleri bir veya daha fazla *ilke*de içerir. Bir ilke, erişim paketine atanmak üzere kuralları veya guardrayları tanımlar. Her ilke, yalnızca uygun kullanıcıların erişim isteyebilmesini, istekleri için onaylayan olduğunu ve bu kaynaklara erişiminin zaman sınırlı olduğunu ve yenilenmediğinde süresinin dolacağını sağlamak için kullanılabilir.

![Paket ve ilkelere erişim](./media/entitlement-management-overview/elm-overview-access-package.png)

Her ilke içinde, bir yönetici veya erişim paketi Yöneticisi şunları tanımlar

- Zaten var olan kullanıcılar (genellikle çalışanlar veya zaten davet edilen konuklar) ya da dış kullanıcıların iş ortağı kuruluşları, erişim isteği için uygun
- Onaylama işlemi ve erişimi onaylayabilen veya reddedebilen kullanıcılar
- Atamanın süresi dolmadan önce, bir kullanıcının erişim atamasının süresi onaylandığında

Aşağıdaki diyagramda, yetkilendirme yönetiminde farklı öğelerin bir örneği gösterilmektedir. İki örnek erişim paketi olan bir katalog gösterir.

- **Erişim paketi 1** , kaynak olarak tek bir grup içerir. Erişim, dizinde erişim isteğinde bulunan bir kullanıcı kümesini sağlayan bir ilkeyle tanımlanır.
- **Erişim paketi 2** , kaynak olarak bir grup, uygulama ve SharePoint Online sitesi içerir. Erişim iki farklı ilke ile tanımlanır. İlk ilke, dizinde erişim istemek için dizindeki bir kullanıcı kümesini sağlar. İkinci ilke, bir dış dizindeki kullanıcıların erişim istemesine olanak sağlar.

![Yetkilendirme yönetimine genel bakış](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Erişim paketlerini ne zaman kullanmalıyım?

Erişim paketleri, erişim atama için diğer mekanizmaların yerini alır.  Bunlar, aşağıdakiler gibi durumlarda en uygun durumlardır:

- Çalışanların belirli bir görev için zamana sınırlı erişim gerekir.  Örneğin, tüm çalışanların Exchange Online posta kutusuna sahip olduğundan emin olmak için grup tabanlı lisanslama ve dinamik bir grup kullanabilir ve sonra çalışanların başka erişim ihtiyacı olan durumlara yönelik olarak, diğer bir deyişle Departman kaynaklarını okumak için erişim paketleri kullanabilirsiniz bölüme.
- Erişimin bir çalışanın Yöneticisi veya diğer belirlenen bireyler tarafından onaylanması gerekir.
- Departmanlar, kaynakları katılımsız olarak kendi erişim ilkelerini yönetmek ister.  
- İki veya daha fazla kuruluş bir proje üzerinde işbirliği sağlar ve sonuç olarak, bir kuruluştan birden çok kullanıcının başka bir kuruluşun kaynaklarına erişmek için Azure AD B2B aracılığıyla getirilmesi gerekir.

## <a name="how-do-i-delegate-access"></a>Temsilci erişimi mi Nasıl yaparım??

 Erişim paketleri, *kataloglar*olarak adlandırılan kapsayıcılar içinde tanımlanır.  Tüm erişim paketleriniz için tek bir kataloğunuz olabilir ya da bireyleri kendi kataloglarını oluşturmak ve kendilerine sahip olacak şekilde tanımlayabilirsiniz. Bir yönetici herhangi bir kataloğa kaynak ekleyebilir, ancak yönetici olmayan bir kataloğa yalnızca sahip oldukları kaynakları ekleyebilir. Katalog sahibi, diğer kullanıcıları Katalog ortak sahipleri veya erişim paketi yöneticileri olarak ekleyebilir.  Bu senaryolar, [Azure AD Yetkilendirme Yönetimi 'nde daha fazla destek ve rol](entitlement-management-delegate.md)makalesinde açıklanmaktadır.

## <a name="summary-of-terminology"></a>Terminolojinin Özeti

Yetkilendirme yönetimini ve belgelerini daha iyi anlamak için, aşağıdaki terim listesine geri başvurabilirsiniz.

| Sözleşme Dönemi | Açıklama |
| --- | --- |
| erişim paketi | Bir ekibin veya projenin ihtiyaç duyacağı ve ilkelerle ilişkilendirilen kaynak demeti. Bir erişim paketi her zaman bir katalogda bulunur. Kullanıcıların erişim istemesi gereken bir senaryo için yeni bir erişim paketi oluşturacaksınız.  |
| erişim isteği | Erişim paketindeki kaynaklara erişim isteği. İstek genellikle bir onay iş akışından geçer.  Onaylanırsa, isteyen Kullanıcı bir erişim paketi ataması alır. |
| atanmış | Bir erişim paketinin bir kullanıcıya atanması, kullanıcının bu erişim paketinin tüm kaynak rollerine sahip olmasını sağlar.  Erişim paketi atamalarının genellikle süresi dolmadan önce bir zaman sınırı vardır. |
| kataloglarını | İlgili kaynakların ve erişim paketlerinin kapsayıcısı.  Kataloglar, yönetici olmayan kullanıcılar kendi erişim paketlerini oluşturabilmesi için, yetkilendirme için kullanılır. Katalog sahipleri, kendilerine ait oldukları kaynakları bir kataloğa ekleyebilir. |
| Katalog Oluşturucu | Yeni kataloglar oluşturma yetkisine sahip kullanıcılar koleksiyonu.  Bir katalog Oluşturucu olarak yetkilendirilmiş yönetici olmayan bir Kullanıcı yeni bir katalog oluşturduğunda, otomatik olarak o kataloğun sahibi olur. |
| bağlı kuruluş | İle ilişkiniz olan bir dış Azure AD dizini veya etki alanı. Bağlı bir kuruluştaki kullanıcılar, erişim istemesine izin verilen bir ilkede belirtilebilir. |
| ilke | Kullanıcıların erişim alma, ne kadar süreceğine ve kullanıcıların bir atamaya göre ne kadar süreceğine ilişkin erişim yaşam döngüsünü tanımlayan bir kurallar kümesi. Bir ilke bir erişim paketine bağlıdır. Örneğin, bir erişim paketinde iki ilke olabilir. çalışanların erişim istemesi ve dış kullanıcıların erişim istemesi için ikinci bir saniye. |
| Kaynak | Bir kullanıcının izin verilebir rolü olan Office grubu, güvenlik grubu, uygulama veya SharePoint Online sitesi gibi bir varlık. |
| Kaynak dizini | Paylaşılacak bir veya daha fazla kaynağı olan bir dizin. |
| Kaynak rolü | İle ilişkili ve bir kaynak tarafından tanımlanan izinler koleksiyonu. Grubun iki rolü vardır-üye ve sahip. SharePoint sitelerinde genellikle 3 rol vardır ancak ek özel roller olabilir. Uygulamalar özel rollere sahip olabilir. |


## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Azure Kamu, Azure Almanya ve Azure Çin 21Vianet gibi özel bulutlar Şu anda kullanım için kullanılamaz.

### <a name="which-users-must-have-licenses"></a>Hangi kullanıcıların Lisansı olmalıdır?

Yetkilendirme yönetiminde etkin üye kullanıcıları olduğundan kiracınız en az sayıda Azure AD Premium P2 lisansa sahip olmalıdır. Yetkilendirme yönetiminde etkin üye kullanıcıları şunları içerir:

- Bir erişim paketi için isteği başlatan veya onaylayan bir kullanıcı.
- Erişim paketi atanan bir kullanıcı.
- Erişim paketlerini yöneten bir kullanıcı.

Üye kullanıcılara yönelik lisansların bir parçası olarak, bir dizi Konuk kullanıcının yetkilendirme yönetimiyle etkileşime girmesine de izin verebilirsiniz. Dahil edebilirsiniz konuk kullanıcıların sayısını hesaplama hakkında daha fazla bilgi için bkz. [Azure ACTIVE DIRECTORY B2B işbirliği Lisanslama Kılavuzu](../b2b/licensing-guidance.md).

Kullanıcılarınıza lisansları atama hakkında daha fazla bilgi için, bkz. [Azure Active Directory portalını kullanarak lisans atama veya kaldırma](../fundamentals/license-users-groups.md). Yetkilendirme yönetiminin Şu anda kullanıcılar için lisans atamasını zorlayamadığını unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: ilk erişim paketinizi oluşturma](entitlement-management-access-package-first.md)
- [Yaygın senaryolar](entitlement-management-scenarios.md)
