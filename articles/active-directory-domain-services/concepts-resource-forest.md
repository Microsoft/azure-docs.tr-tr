---
title: Azure AD Domain Services için kaynak ormanı kavramları | Microsoft Docs
description: Kaynak ormanın ne Azure Active Directory Domain Services olduğunu ve kuruluşunuzun Karma ortamda, sınırlı Kullanıcı kimlik doğrulama seçenekleriyle veya güvenlik kaygılarıyla nasıl yararlandıkları hakkında bilgi edinin.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 79cf408bcb9060c247b97e6a81204c5a5517d384
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620010"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services için kaynak ormanı kavramları ve özellikleri

Azure Active Directory Domain Services (Azure AD DS), eski, şirket içi ve iş kolu uygulamaları için bir oturum açma deneyimi sağlar. Şirket içi ve bulut kullanıcılarının kullanıcıları, grupları ve parola karmaları Azure AD DS yönetilen etki alanıyla eşitlenir. Bu eşitlenmiş parola karmaları, kullanıcılara şirket içi AD DS, Microsoft 365 ve Azure Active Directory için kullanabilecekleri tek bir kimlik bilgileri kümesi sağlar.

Güvenli ve ek güvenlik avantajları sunmakla birlikte, bazı kuruluşlar bu kullanıcı parolası karmalarını Azure AD veya Azure AD DS ile eşitleyemez. Kuruluştaki kullanıcılar, yalnızca akıllı kart kimlik doğrulamasını kullandıkları için parolasını bilmiyor olabilir. Bu sınırlamalar, bazı kuruluşların şirket içi klasik uygulamaları Azure 'a taşımak ve bunlara geçiş yapmak için Azure AD DS kullanmasını engeller.

Bu ihtiyaçları ve kısıtlamaları ele almak için, kaynak ormanı kullanan bir yönetilen etki alanı oluşturabilirsiniz. Bu kavramsal makalede, ormanların ne olduğu ve güvenli bir kimlik doğrulama yöntemi sağlamak üzere diğer kaynaklara nasıl güvendikleri açıklanmaktadır.

## <a name="what-are-forests"></a>Ormanlar nelerdir?

*Orman* , bir veya daha fazla *etki alanını* gruplamak için Active Directory Domain Services (AD DS) tarafından kullanılan mantıksal bir yapıdır. Etki alanları daha sonra Kullanıcı veya grup için nesneleri depolar ve kimlik doğrulama hizmetleri sağlar.

Azure AD DS yönetilen bir etki alanında, orman yalnızca bir etki alanı içerir. Şirket içi AD DS ormanları genellikle birçok etki alanı içerir. Büyük kuruluşlarda, özellikle Birleşmeler ve alımlar sonrasında, her biri birden çok etki alanı içeren birden çok şirket içi ormana sahip olabilirsiniz.

Varsayılan olarak, yönetilen bir etki alanı bir *Kullanıcı* Ormanı olarak oluşturulur. Bu tür bir orman, şirket içi AD DS ortamında oluşturulan kullanıcı hesapları da dahil olmak üzere Azure AD 'deki tüm nesneleri eşitler. Kullanıcı hesapları, etki alanına katılmış bir VM 'de oturum açmak gibi, yönetilen etki alanında doğrudan kimlik doğrulaması yapabilir. Parola karmaları eşitlenecekse ve kullanıcılar akıllı kart kimlik doğrulaması gibi özel oturum açma yöntemlerini kullanmadığı zaman bir Kullanıcı ormanı işe yarar.

Yönetilen bir etki alanı *kaynak* ormanında, kullanıcılar şirket içi AD DS tek yönlü bir orman *güveni* üzerinden kimlik doğrular. Bu yaklaşımda, Kullanıcı nesneleri ve parola karmaları yönetilen etki alanı ile eşitlenmez. Kullanıcı nesneleri ve kimlik bilgileri yalnızca şirket içi AD DS bulunur. Bu yaklaşım, kuruluşların Azure 'da, LDAPS, Kerberos veya NTLM gibi klasik kimlik doğrulamasına bağlı olan kaynakları ve uygulama platformlarını barındırmasına, ancak tüm kimlik doğrulama sorunları veya kaygıları kaldırılmasına olanak sağlar.

Kaynak ormanları Ayrıca uygulamalarınızı tek seferde bir bileşen üzerinde kaldırma ve kaydırma özelliği de sağlar. Birçok eski şirket içi uygulama, genellikle bir Web sunucusu veya ön uç ve veritabanı ile ilgili birçok bileşeni kullanan çok katmanlı. Bu katmanlar, tek bir adımda uygulamanın tamamını buluta taşımak ve kaydırmak için çok daha fazlasını yapar. Kaynak ormanlarıyla uygulamanızı aşamalı yaklaşımda kaldırabilirsiniz. Bu, uygulamanızı Azure 'a taşımayı kolaylaştırır.

## <a name="what-are-trusts"></a>Güvenler nelerdir?

Birden fazla etki alanı olan kuruluşların genellikle kullanıcıların farklı bir etki alanında paylaşılan kaynaklara erişmesi gerekir. Bu paylaşılan kaynaklara erişim, bir etki alanındaki kullanıcıların başka bir etki alanında kimlik doğrulamasını gerektirir. Farklı etki alanlarındaki istemciler ve sunucular arasında bu kimlik doğrulama ve yetkilendirme özellikleri sağlamak için iki etki alanı arasında bir *güven* olmalıdır.

Etki alanı güvenleri ile, her etki alanı için kimlik doğrulama mekanizmaları, diğer etki alanından gelen kimlik doğrulamaları için güvenir. Güvenler, gelen kimlik doğrulama isteklerinin güvenilen bir yetkilinizden ( *Güvenilen* etki alanı) geldiği doğrulayarak, kaynak etki alanındaki ( *güvenen* etki alanındaki) paylaşılan kaynaklara denetimli erişim sağlamaya yardımcı olur. Güvenler yalnızca, kimlik alanları arasında dolaşmak için doğrulanan kimlik doğrulama isteklerinin izin veren köprü olarak davranır.

Güven, kimlik doğrulama isteklerini nasıl geçireceğini nasıl yapılandırdığına bağlıdır. Güvenler aşağıdaki yollarla yapılandırılabilir:

* **Tek yönlü** -güvenen etki alanındaki kaynaklara güvenilir etki alanından erişim sağlar.
* **İki yönlü** -diğer etki alanındaki kaynaklara her etki alanından erişim sağlar.

Güvenler Ayrıca aşağıdaki yollarla ek güven ilişkilerini işleyecek şekilde yapılandırılır:

* **Geçişsiz** -güven yalnızca iki güven ortağı etki alanı arasında bulunur.
* **Geçişli** güven, iş ortaklarının güvendiği diğer etki alanlarına otomatik olarak genişletilir.

Bazı durumlarda, etki alanları oluşturulduğunda güven ilişkileri otomatik olarak oluşturulur. Diğer zamanlarda, bir güven türü seçmeniz ve uygun ilişkileri açıkça oluşturmanız gerekir. Kullanılan belirli güven türleri ve bu güven ilişkilerinin yapısı, AD DS dizininin nasıl düzenlendiğini ve ağda farklı Windows sürümlerinin aynı olup olmadığını gösterir.

## <a name="trusts-between-two-forests"></a>İki orman arasındaki güvenler

Tek yönlü veya iki yönlü bir orman güveni el ile oluşturarak, tek bir ormandaki etki alanı güvenlerini başka bir ormana genişletebilirsiniz. Orman güveni, yalnızca bir orman kök etki alanı ile ikinci bir orman kök etki alanı arasında bulunan geçişli bir güvendir.

* Tek yönlü bir orman güveni, bir ormandaki tüm kullanıcıların diğer ormandaki tüm etki alanlarına güvenmesini sağlar.
* İki yönlü bir orman güveni, her iki ormandaki her etki alanı arasında geçişli bir güven ilişkisi oluşturur.

Orman güvenlerini geçişliliği, iki orman iş ortaklarıyla sınırlıdır. Orman güveni, iş ortaklarının her biri tarafından güvenilen ek ormana genişlemez.

![Azure AD DS şirket içi AD DS orman güveninin diyagramı](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Kuruluşun AD DS yapısına bağlı olarak, farklı etki alanı ve orman güveni yapılandırması oluşturabilirsiniz. Azure AD DS yalnızca tek yönlü bir orman güvenini destekler. Bu yapılandırmada, yönetilen etki alanındaki kaynaklar şirket içi bir ormandaki tüm etki alanlarına güvenebilir.

## <a name="supporting-technology-for-trusts"></a>Güvenler için destekleme teknolojisi

Güvenler, ortaklık ormanlarda etki alanı denetleyicilerini bulmak için DNS gibi çeşitli hizmetler ve Özellikler kullanır. Güvenler Ayrıca, AD DS etki alanları ve ormanlar arasında güvenli bir iletişim altyapısı sağlamaya yardımcı olmak için NTLM ve Kerberos kimlik doğrulama protokollerine ve Windows tabanlı yetkilendirme ve erişim denetimi mekanizmalarına de bağlıdır. Aşağıdaki hizmetler ve Özellikler başarılı güven ilişkilerini desteklemeye yardımcı olur.

### <a name="dns"></a>DNS

AD DS, etki alanı denetleyicisi (DC) konumu ve adlandırması için DNS gerektirir. AD DS başarıyla çalışması için DNS 'den aşağıdaki destek sağlanır:

* Ağ konaklarının ve hizmetlerinin DC 'leri bulmasını sağlayan bir ad çözümleme hizmeti.
* Bir kuruluşun, kendi dizin hizmeti etki alanlarının adlarında kuruluş yapısını yansıtmasını sağlayan bir adlandırma yapısı.

Genellikle AD DS etki alanı ad alanını yansıtan bir DNS etki alanı ad alanı dağıtılır. AD DS dağıtımından önce mevcut bir DNS ad alanı varsa, DNS ad alanı genellikle AD DS için bölümlenir ve AD DS orman köküne yönelik bir DNS alt etki alanı ve temsili oluşturulur. Daha sonra her bir AD DS alt etki alanı için ek DNS etki alanı adları eklenir.

DNS, AD DS DC 'lerin konumunu desteklemek için de kullanılır. DNS bölgeleri, ağ ana bilgisayarlarının ve hizmetlerinin AD DS DC 'leri bulmasını sağlayan DNS kaynak kayıtlarıyla doldurulur.

### <a name="applications-and-net-logon"></a>Uygulamalar ve net oturum açma

Hem uygulamalar hem de net oturum açma hizmeti, Windows dağıtılmış güvenlik kanal modelinin bileşenleridir. Windows Server ve AD DS ile tümleştirilmiş uygulamalar, ağ oturum açma hizmetiyle iletişim kurmak için kimlik doğrulama protokollerini kullanır. böylece, kimlik doğrulamanın gerçekleşebileceği güvenli bir yol oluşturulabilir.

### <a name="authentication-protocols"></a>Kimlik Doğrulama Protokolleri

AD DS DC 'Ler, aşağıdaki protokollerden birini kullanarak kullanıcıların ve uygulamaların kimliğini doğrular:

* **Kerberos sürüm 5 kimlik doğrulama protokolü**
    * Kerberos sürüm 5 Protokolü, Windows çalıştıran ve üçüncü taraf işletim sistemlerini destekleyen şirket içi bilgisayarlar tarafından kullanılan varsayılan kimlik doğrulama protokolüdür. Bu protokol, RFC 1510 ' de belirtilmiştir ve AD DS, sunucu ileti bloğu (SMB), HTTP ve uzak yordam çağrısı (RPC) ile ve bu protokolleri kullanan istemci ve sunucu uygulamaları ile tamamen tümleşiktir.
    * Kerberos protokolü kullanıldığında, sunucunun DC ile iletişim kurmak zorunda değildir. Bunun yerine, istemci sunucu hesabı etki alanındaki DC 'den bir sunucu isteyerek bir bilet alır. Sunucu daha sonra başka bir yetkiliye danışmadan bileti doğrular.
    * Bir işlemde yer alan herhangi bir bilgisayar Kerberos sürüm 5 protokolünü desteklemiyorsa, NTLM protokolü kullanılır.

* **NTLM kimlik doğrulama protokolü**
    * NTLM protokolü, eski işletim sistemleri tarafından kullanılan klasik bir ağ kimlik doğrulama protokolüdür. Uyumluluk nedenleriyle, daha önceki Windows tabanlı istemciler ve sunucular ve üçüncü taraf işletim sistemleri için tasarlanan uygulamalardan gelen ağ kimlik doğrulama isteklerini işlemek için AD DS etki alanları tarafından kullanılır.
    * Bir istemci ve sunucu arasında NTLM protokolü kullanıldığında, sunucunun istemci kimlik bilgilerini doğrulamak için bir DC 'de bir etki alanı kimlik doğrulama hizmetiyle iletişim kurabilmesi gerekir. Sunucu istemci kimlik bilgilerini istemci hesabı etki alanındaki bir DC 'ye ileterek istemcinin kimliğini doğrular.
    * İki AD DS etki alanı veya orman bir güven ile bağlandığında, bu protokolleri kullanarak yapılan kimlik doğrulama istekleri her iki ormandaki kaynaklara erişim sağlamak için yönlendirilebilir.

## <a name="authorization-and-access-control"></a>Yetkilendirme ve erişim denetimi

Yetkilendirme ve güven teknolojileri, AD DS etki alanları veya ormanlar arasında güvenli bir iletişim altyapısı sağlamak için birlikte çalışır. Yetkilendirme, bir kullanıcının bir etki alanındaki kaynaklara hangi erişim düzeyinin olduğunu belirler. Güvenler, diğer etki alanlarındaki kullanıcıların kimliğini doğrulamak için bir yol sağlayarak kullanıcıların etki alanları arası yetkilendirmesini kolaylaştırır ve bu sayede bu etki alanındaki paylaşılan kaynaklara yönelik isteklere yetki verebilir.

Güvenen bir etki alanında yapılan bir kimlik doğrulama isteği, güvenilen etki alanı tarafından doğrulandığında, hedef kaynağa geçirilir. Hedef kaynak daha sonra, erişim denetimi yapılandırmasına göre güvenilen etki alanındaki Kullanıcı, hizmet veya bilgisayar tarafından yapılan belirli bir isteğin yetkilendirilip yetkilendirilmeyeceğini belirler.

Güvenler, güvenen bir etki alanına geçirilen kimlik doğrulama isteklerini doğrulamak için bu mekanizmayı sağlar. Kaynak bilgisayardaki erişim denetimi mekanizmaları, güvenilen etki alanında istek sahibine verilen son erişim düzeyini tespit ediyor.

## <a name="next-steps"></a>Sonraki adımlar

Güvenler hakkında daha fazla bilgi edinmek için bkz. [Azure AD DS orman güvenleri nasıl çalışır?][concepts-trust]

Kaynak ormanı ile yönetilen bir etki alanı oluşturmaya başlamak için bkz. [Azure AD DS yönetilen etki alanı oluşturma ve yapılandırma][tutorial-create-advanced]. Daha sonra şirket [içi etki alanına giden bir orman güveni oluşturabilirsiniz][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
