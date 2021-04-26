---
title: Azure Active Directory Domain Services genel bakış | Microsoft Docs
description: Bu genel bakışta, bulutta uygulamalara ve hizmetlere kimlik hizmetleri sağlamak için Azure Active Directory Domain Services neler sağladığını ve kuruluşunuzda nasıl kullanacağınızı öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 194a5420f38e99d45f74241f67ae8cfbbaaee8f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661009"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services nedir?

Azure Active Directory Domain Services (AD DS), etki alanına katılması, Grup ilkesi, Hafif Dizin Erişim Protokolü (LDAP) ve Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Bu etki alanı hizmetlerini, bulutta etki alanı denetleyicileri (DC) dağıtmak, yönetmek ve bunlara yama yapmak zorunda kalmadan kullanırsınız.

Azure AD DS yönetilen bir etki alanı, bulutta modern kimlik doğrulama yöntemlerini kullanmayan eski uygulamaları çalıştırmanıza veya dizin aramalarının her zaman şirket içi AD DS ortamına geri dönmenize imkan tanır. Şirket içi ortamınızdan bu eski uygulamaları, bulutta AD DS ortamı yönetmeye gerek kalmadan, yönetilen bir etki alanına kaldırıp kaydıramazsınız.

Azure AD DS, mevcut Azure AD kiracınızla tümleşir. Bu tümleştirme, kullanıcıların, mevcut kimlik bilgilerini kullanarak yönetilen etki alanına bağlı hizmetler ve uygulamalar üzerinde oturum açmalarına olanak tanır. Kaynaklara erişimi güvenli hale getirmek için mevcut grupları ve Kullanıcı hesaplarını da kullanabilirsiniz. Bu özellikler, şirket içi kaynakların Azure 'a daha yumuşak bir şekilde daha iyi bir şekilde daha iyi şekilde kaymasını sağlar.

> [!div class="nextstepaction"]
> [Başlamak için Azure portal kullanarak Azure AD DS yönetilen bir etki alanı oluşturun][tutorial-create]

Azure AD DS hakkında daha fazla bilgi edinmek için kısa videolarımıza göz atın.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LblD]

## <a name="how-does-azure-ad-ds-work"></a>Azure AD DS nasıl çalışır?

Azure AD DS yönetilen bir etki alanı oluşturduğunuzda, benzersiz bir ad alanı tanımlarsınız. Bu ad alanı, *aaddscontoso.com* gibi etki alanı adıdır. İki Windows Server etki alanı denetleyicisi (DC), daha sonra seçtiğiniz Azure bölgenize dağıtılır. Bu DC dağıtımı bir çoğaltma kümesi olarak bilinir.

Bu DC 'leri yönetmeniz, yapılandırmanız veya güncelleştirmeniz gerekmez. Azure platformu, Azure disk şifrelemesi ile bekleyen yedeklemeler ve şifreleme dahil olmak üzere, DC 'leri yönetilen etki alanının bir parçası olarak işler.

Yönetilen bir etki alanı, merkezi bir grup kullanıcıya, gruba ve kimlik bilgilerine erişim sağlamak için Azure AD 'den tek yönlü bir eşitleme gerçekleştirecek şekilde yapılandırılmıştır. Kaynakları doğrudan yönetilen etki alanında oluşturabilirsiniz, ancak Azure AD 'ye geri eşitlenmez. Yönetilen etki alanına bağlanan Azure 'daki uygulamalar, hizmetler ve sanal makineler, etki alanına katılması, Grup ilkesi, LDAP ve Kerberos/NTLM kimlik doğrulaması gibi ortak AD DS özellikleri kullanabilir.

Şirket içi AD DS ortamına sahip bir karma ortamda, [Azure AD Connect][azure-ad-connect] kimlik BILGILERINI Azure AD ile eşitler ve bu daha sonra yönetilen etki alanıyla eşitlenir.

![AD Connect kullanarak Azure AD ve şirket içi AD DS Azure AD Domain Services eşitleme](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS, Azure AD 'den kimlik bilgilerini çoğalttığından, bu nedenle yalnızca bulutta bulunan veya şirket içi AD DS ortamıyla eşitlenmiş Azure AD kiracılarıyla birlikte çalışmaktadır. Her iki ortam için aynı Azure AD DS özellikleri kümesi vardır.

* Mevcut bir şirket içi AD DS ortamınız varsa, kullanıcılar için tutarlı bir kimlik sağlamak üzere Kullanıcı hesabı bilgilerini eşzamanlı hale getirebilirsiniz. Daha fazla bilgi edinmek için bkz. [nesneleri ve kimlik bilgilerini yönetilen bir etki alanında eşitleme][synchronization].
* Yalnızca bulut ortamlarında, Azure AD DS merkezi kimlik hizmetlerini kullanmak için geleneksel bir şirket içi AD DS ortamına ihtiyacınız yoktur.

Yönetilen bir etki alanını, Azure AD kiracısı başına birden fazla çoğaltma kümesine sahip olacak şekilde genişletebilirsiniz. Çoğaltma kümeleri, Azure AD DS 'yi destekleyen herhangi bir Azure bölgesindeki eşlenen herhangi bir sanal ağa eklenebilir. Farklı Azure bölgelerindeki ek çoğaltma kümeleri, bir Azure bölgesi çevrimdışı kalırsa eski uygulamalar için coğrafi olağanüstü durum kurtarma sağlar. Çoğaltma kümeleri Şu anda önizleme aşamasındadır. Daha fazla bilgi için bkz. [yönetilen etki alanları Için çoğaltma kümesi kavramları ve özellikleri][concepts-replica-sets].

Aşağıdaki videoda, Bulutta kimlik hizmetleri sağlamak için Azure AD DS uygulamalarınızın ve iş yüklerinizde nasıl tümleştirildiğini gösteren bir genel bakış sunulmaktadır:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Azure AD DS dağıtım senaryolarını eylemde görmek için aşağıdaki örnekleri inceleyebilirsiniz:

* [Hibrit kuruluşlar için Azure AD DS](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Yalnızca bulutta kuruluşlar için Azure AD DS](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS özellikleri ve avantajları

Bulutta uygulamalara ve sanal makinelere kimlik hizmetleri sağlamak için Azure AD DS, etki alanına katılması, Güvenli LDAP (LDAPS), grup ilkesi, DNS yönetimi ve LDAP bağlama ve okuma desteği gibi işlemler için geleneksel bir AD DS ortamıyla tamamen uyumludur. LDAP yazma desteği, yönetilen etki alanında oluşturulan nesneler için kullanılabilir, ancak Azure AD 'den eşitlenen kaynaklar için kullanılabilir.

Kimlik seçenekleriniz hakkında daha fazla bilgi edinmek için Azure [AD DS Azure AD ile karşılaştırın, Azure VM 'lerinde AD DS ve şirket içi AD DS][compare].

Azure AD DS dağıtım ve yönetim işlemlerini basitleştirecek aşağıdaki özellikler:

* **Basitleştirilmiş dağıtım deneyimi:** Azure AD DS, Azure portal tek bir sihirbaz kullanılarak Azure AD kiracınız için etkinleştirilmiştir.
* **Azure AD Ile tümleşik:** Kullanıcı hesapları, grup üyelikleri ve kimlik bilgileri Azure AD kiracınızdan otomatik olarak kullanılabilir. Azure AD kiracınızdan veya şirket içi AD DS ortamınızda bulunan yeni kullanıcılar, gruplar veya özniteliklerde yapılan değişiklikler otomatik olarak Azure AD DS eşitlenir.
    * Azure AD 'nize bağlı dış dizinlerdeki hesaplar Azure AD DS kullanılamıyor. Bu dış dizinler için kimlik bilgileri kullanılamaz, bu nedenle yönetilen bir etki alanına eşitlenemez.
* **Şirket kimlik bilgilerinizi/parolalarınızı kullanın:** Azure AD DS kullanıcılara yönelik parolalar Azure AD kiracınızdaki ile aynıdır. Kullanıcılar, Şirket kimlik bilgilerini etki alanına katmak, etkileşimli olarak veya uzak masaüstü üzerinden oturum açmak ve yönetilen etki alanında kimlik doğrulaması yapmak için kullanabilir.
* **NTLM ve Kerberos kimlik doğrulaması:** NTLM ve Kerberos kimlik doğrulaması desteğiyle, Windows ile tümleşik kimlik doğrulamasına dayanan uygulamalar dağıtabilirsiniz.
* **Yüksek kullanılabilirlik:** Azure AD DS, yönetilen etki alanınız için yüksek kullanılabilirlik sağlayan birden çok etki alanı denetleyicisi içerir. Bu yüksek kullanılabilirlik, hizmet çalışma süresini ve esnekliği hatalara karşı garanti eder.
    * [Azure kullanılabilirlik alanları][availability-zones]destekleyen bölgelerde, bu etki alanı denetleyicileri ek dayanıklılık için bölgelere da dağıtılır.
    * [Çoğaltma kümeleri][concepts-replica-sets] , bir Azure bölgesi çevrimdışı kalırsa eski uygulamalar için coğrafi olağanüstü durum kurtarması sağlamak üzere de kullanılabilir.

Yönetilen bir etki alanının bazı önemli yönleri şunları içerir:

* Yönetilen etki alanı tek başına bir etki alanıdır. Şirket içi bir etki alanının uzantısı değildir.
    * Gerekirse, Azure AD DS 'den şirket içi AD DS ortamına tek yönlü bir giden orman güveni oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure AD DS Için kaynak ormanı kavramları ve özellikleri][ forest-trusts].
* BT takımınızın bu yönetilen etki alanı için etki alanı denetleyicilerini yönetmesi, yaması veya izlemesi gerekmez.

Şirket içinde AD DS çalışan karma ortamlarda, AD çoğaltmasını yönetilen etki alanına yönetmeniz gerekmez. Şirket içi dizininizdeki Kullanıcı hesapları, grup üyelikleri ve kimlik bilgileri [Azure AD Connect][azure-ad-connect]aracılığıyla Azure AD ile eşitlenir. Bu Kullanıcı hesapları, grup üyelikleri ve kimlik bilgileri, yönetilen etki alanı içinde otomatik olarak kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS diğer kimlik çözümleriyle ve eşitlemenin nasıl çalıştığı hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure AD DS Azure AD ile karşılaştırın, Azure VM 'lerinde Active Directory Domain Services ve şirket içi Active Directory Domain Services][compare]
* [Azure AD Domain Services Azure AD dizininizle nasıl eşitleneceğini öğrenin][synchronization]
* Yönetilen bir etki alanı Yöneticisi hakkında bilgi edinmek için bkz. [Azure AD DS 'de Kullanıcı hesapları, parolalar ve yönetim için yönetim kavramları][administration-concepts].

Başlamak için [Azure Portal kullanarak yönetilen bir etki alanı oluşturun][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
