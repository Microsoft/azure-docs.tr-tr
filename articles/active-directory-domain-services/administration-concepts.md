---
title: Azure AD Domain Services için yönetim kavramları | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanını ve Kullanıcı hesaplarının ve parolaların davranışını yönetme hakkında bilgi edinin
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: justinha
ms.openlocfilehash: 5473ef46751d64fdbbf1d52f39c66f49d707e615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631396"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 'de Kullanıcı hesapları, parolalar ve yönetim için yönetim kavramları

Azure Active Directory Domain Services (AD DS) yönetilen bir etki alanı oluşturup çalıştırdığınızda, bazı durumlarda geleneksel bir şirket içi AD DS ortamıyla karşılaştırıldığında bazı farklılıklar vardır. Azure AD DS 'de otomatik olarak yönetilen bir etki alanı olarak aynı yönetim araçlarını kullanırsınız, ancak etki alanı denetleyicilerine (DC) doğrudan erişemezsiniz. Ayrıca, Kullanıcı hesabı oluşturma kaynağına bağlı olarak parola ilkeleri ve parola karmalarının davranışında bazı farklılıklar da vardır.

Bu kavramsal makalede, yönetilen bir etki alanını ve Kullanıcı hesaplarının oluşturuldukları yönteme bağlı olarak farklı davranışları yönetme ayrıntıları yer alır.

## <a name="domain-management"></a>Etki alanı yönetimi

Yönetilen etki alanı, DNS ad alanıdır ve eşleşen dizindir. Yönetilen bir etki alanında kullanıcılar ve gruplar, kimlik bilgileri ve ilkeler gibi tüm kaynakları içeren etki alanı denetleyicileri (DC 'Ler), yönetilen hizmetin bir parçasıdır. Artıklık için, yönetilen bir etki alanının parçası olarak iki DC oluşturulur. Yönetim görevlerini gerçekleştirmek için bu DC 'lerde oturum açamazsınız. Bunun yerine, yönetilen etki alanına katılmış bir yönetim sanal makinesi oluşturun ve ardından normal AD DS yönetim araçlarınızı yükleyebilirsiniz. Örneğin, DNS veya grup ilkesi nesneleri gibi Active Directory Yönetim Merkezi veya Microsoft Yönetim Konsolu (MMC) ek bileşenlerini kullanabilirsiniz.

## <a name="user-account-creation"></a>Kullanıcı hesabı oluşturma

Kullanıcı hesapları, yönetilen bir etki alanında birden çok şekilde oluşturulabilir. Çoğu kullanıcı hesabı Azure AD 'den eşitlenir ve bu da şirket içi AD DS ortamından eşitlenen Kullanıcı hesabını da içerebilir. Ayrıca, hesapları doğrudan yönetilen etki alanında el ile de oluşturabilirsiniz. İlk parola eşitleme veya parola ilkesi gibi bazı özellikler, Kullanıcı hesaplarının nasıl ve nerede oluşturulduğuna bağlı olarak farklı davranır.

* Kullanıcı hesabı Azure AD 'den eşitlenebilir. Bu, doğrudan Azure AD 'de oluşturulan bulut Kullanıcı hesaplarını ve Azure AD Connect kullanarak şirket içi AD DS ortamından eşitlenen karma Kullanıcı hesaplarını içerir.
    * Yönetilen bir etki alanındaki Kullanıcı hesaplarının çoğunluğu Azure AD 'den eşitleme işlemi aracılığıyla oluşturulur.
* Kullanıcı hesabı, yönetilen bir etki alanında el ile oluşturulabilir ve Azure AD 'de mevcut değildir.
    * Yalnızca yönetilen etki alanında çalışan uygulamalar için hizmet hesapları oluşturmanız gerekiyorsa, bunları yönetilen etki alanında el ile oluşturabilirsiniz. Eşitleme, Azure AD 'den bir yol olduğu için, yönetilen etki alanında oluşturulan kullanıcı hesapları Azure AD 'ye geri eşitlenmez.

## <a name="password-policy"></a>Parola ilkesi

Azure AD DS, hesap kilitleme, en fazla parola yaşı ve parola karmaşıklığı gibi şeyler için ayarları tanımlayan bir varsayılan parola ilkesi içerir. Hesap kilitleme ilkesi gibi ayarlar, kullanıcının önceki bölümde özetlendiği şekilde oluşturulma şeklinden bağımsız olarak, yönetilen bir etki alanındaki tüm kullanıcılara uygulanır. Minimum parola uzunluğu ve parola karmaşıklığı gibi birkaç ayar yalnızca, doğrudan yönetilen bir etki alanında oluşturulan kullanıcılara uygulanır.

Yönetilen bir etki alanında varsayılan ilkeyi geçersiz kılmak için kendi özel parola ilkelerinizi oluşturabilirsiniz. Bu özel ilkeler, gerektiğinde belirli kullanıcı gruplarına uygulanabilir.

Parola ilkelerinin Kullanıcı oluşturma kaynağına göre uygulanma farkları hakkında daha fazla bilgi için, bkz. [yönetilen etki alanlarında parola ve hesap kilitleme ilkeleri][password-policy].

## <a name="password-hashes"></a>Parola karmaları

Yönetilen etki alanındaki kullanıcıların kimliğini doğrulamak için Azure AD DS 'nin, NT LAN Manager (NTLM) ve Kerberos kimlik doğrulaması için uygun bir biçimde parola karmaları olması gerekir. Azure AD, kiracınız için Azure AD DS etkinleştirene kadar NTLM veya Kerberos kimlik doğrulaması için gereken biçimde parola karmaları oluşturmaz veya depolamaz. Azure AD, güvenlik nedenleriyle şifresiz metin biçiminde hiçbir parola kimlik bilgisi depolamaz. Bu nedenle, Azure AD kullanıcıların mevcut kimlik bilgilerini temel alarak bu NTLM veya Kerberos parola karmalarını otomatik olarak üretemiyor.

Yalnızca bulutta yer alan Kullanıcı hesapları için, kullanıcıların yönetilen etki alanını kullanabilmesi için parolalarını değiştirmesi gerekir. Bu parola değiştirme işlemi, Kerberos ve NTLM kimlik doğrulamasının parola karmalarının Azure AD 'de oluşturulmasına ve depolanmasına neden olur. Hesap, parola değiştirilene kadar Azure AD 'den Azure AD DS eşitlenmemiş.

Azure AD Connect kullanarak şirket içi AD DS ortamından eşitlenen kullanıcılar için [parola karmalarının eşitlenmesini etkinleştirin][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect yalnızca Azure AD kiracınız için Azure AD DS etkinleştirdiğinizde eski parola karmalarını eşitler. Yalnızca Azure AD ile şirket içi AD DS ortamını eşleştirmek için Azure AD Connect kullanıyorsanız eski parola karmaları kullanılmaz.
>
> Eski uygulamalarınız NTLM kimlik doğrulaması veya LDAP basit bağlamalar kullanmıyorsanız, Azure AD DS için NTLM parola karma eşitlemesini devre dışı bırakmanızı öneririz. Daha fazla bilgi için bkz. [zayıf şifre paketlerini ve NTLM kimlik bilgisi karma eşitlemesini devre dışı bırakma][secure-domain].

Uygun şekilde yapılandırıldıktan sonra, kullanılabilir parola karmaları yönetilen etki alanında depolanır. Yönetilen etki alanını silerseniz, o noktada depolanan herhangi bir parola karması de silinir. Azure AD 'deki eşitlenmiş kimlik bilgileri, daha sonra başka bir yönetilen etki alanı oluşturursanız yeniden kullanılamaz. parola karmalarını yeniden depolamak için Parola karması eşitlemesini yeniden yapılandırmanız gerekir. Daha önce etki alanına katılmış VM 'Ler veya kullanıcılar, parola karmalarını yeni yönetilen etki alanında oluşturmak ve depolamak için Azure AD ihtiyaçlarına hemen kimlik doğrulaması yapamaz. Daha fazla bilgi için bkz. [Azure AD DS Için Parola karması eşitleme işlemi ve Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect yalnızca şirket içi AD DS ortamları ile eşitleme için yüklenmeli ve yapılandırılmalıdır. Nesneleri Azure AD 'ye geri eşitlemeniz için yönetilen bir etki alanında Azure AD Connect yüklemek desteklenmez.

## <a name="forests-and-trusts"></a>Ormanlar ve güvenler

*Orman* , bir veya daha fazla *etki alanını* gruplamak için Active Directory Domain Services (AD DS) tarafından kullanılan mantıksal bir yapıdır. Etki alanları daha sonra Kullanıcı veya grup için nesneleri depolar ve kimlik doğrulama hizmetleri sağlar.

Azure AD DS, orman yalnızca bir etki alanı içerir. Şirket içi AD DS ormanları genellikle birçok etki alanı içerir. Büyük kuruluşlarda, özellikle Birleşmeler ve alımlar sonrasında, her biri birden çok etki alanı içeren birden çok şirket içi ormana sahip olabilirsiniz.

Varsayılan olarak, yönetilen bir etki alanı bir *Kullanıcı* Ormanı olarak oluşturulur. Bu tür bir orman, şirket içi AD DS ortamında oluşturulan kullanıcı hesapları da dahil olmak üzere Azure AD 'deki tüm nesneleri eşitler. Kullanıcı hesapları, etki alanına katılmış bir VM 'de oturum açmak gibi, yönetilen etki alanında doğrudan kimlik doğrulaması yapabilir. Parola karmaları eşitlenecekse ve kullanıcılar akıllı kart kimlik doğrulaması gibi özel oturum açma yöntemlerini kullanmadığı zaman bir Kullanıcı ormanı işe yarar.

Azure AD DS *kaynak* ormanında, kullanıcılar şirket içi AD DS tek yönlü bir orman *güveni* üzerinden kimlik doğrular. Bu yaklaşımda, Kullanıcı nesneleri ve parola karmaları Azure AD DS ile eşitlenmez. Kullanıcı nesneleri ve kimlik bilgileri yalnızca şirket içi AD DS bulunur. Bu yaklaşım, kuruluşların Azure 'da, LDAPS, Kerberos veya NTLM gibi klasik kimlik doğrulamasına bağlı olan kaynakları ve uygulama platformlarını barındırmasına, ancak tüm kimlik doğrulama sorunları veya kaygıları kaldırılmasına olanak sağlar.

Azure AD DS orman türleri hakkında daha fazla bilgi için bkz. [kaynak ormanları nelerdir?][concepts-forest] ve [orman güvenleri Azure AD DS nasıl çalışır?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU 'Ları

Azure AD DS 'de, kullanılabilir performans ve Özellikler SKU 'YU temel alır. Yönetilen etki alanını oluştururken bir SKU seçersiniz ve yönetilen etki alanı dağıtıldıktan sonra iş gereksinimleriniz değiştikçe SKU 'Ları değiştirebilirsiniz. Aşağıdaki tabloda kullanılabilir SKU 'Lar ve aralarındaki farklar özetlenmektedir:

| SKU adı   | En fazla nesne sayısı | Yedekleme sıklığı | En fazla giden orman güveni sayısı |
|------------|----------------------|------------------|----|
| Standart   | Sınırsız            | 5 günde bir     | 0  |
| Kurumsal | Sınırsız            | 3 günde bir     | 5  |
| Premium    | Sınırsız            | Günlük            | 10 |

Bu Azure AD DS SKU 'larından önce, yönetilen etki alanındaki nesne sayısına (Kullanıcı ve bilgisayar hesapları) göre bir faturalandırma modeli kullanılmıştır. Yönetilen etki alanındaki nesne sayısına bağlı olarak artık değişken fiyatlandırma yoktur.

Daha fazla bilgi için bkz. [Azure AD DS fiyatlandırma sayfası][pricing].

### <a name="managed-domain-performance"></a>Yönetilen etki alanı performansı

Etki alanı performansı, bir uygulama için kimlik doğrulamanın nasıl uygulandığına göre farklılık gösterir. Ek işlem kaynakları sorgu yanıt süresinin artırılmasına ve eşitleme işlemlerinde harcanan süreyi azaltmanıza yardımcı olabilir. SKU düzeyi arttıkça, yönetilen etki alanı için kullanılabilir işlem kaynakları artar. Uygulamalarınızın performansını izleyin ve gerekli kaynakları planlayın.

İşiniz veya uygulamanız için değişiklik yaptıysanız ve yönetilen etki alanınız için ek işlem gücü gerekiyorsa, farklı bir SKU 'ya geçiş yapabilirsiniz.

### <a name="backup-frequency"></a>Yedekleme sıklığı

Yedekleme sıklığı, yönetilen etki alanının bir anlık görüntüsünün ne sıklıkta alınacağını belirler. Yedeklemeler, Azure platformu tarafından yönetilen otomatikleştirilmiş bir işlemdir. Yönetilen etki alanı ile ilgili bir sorun olması durumunda Azure desteği, yedekten geri yükleme konusunda size yardımcı olabilir. Eşitleme yalnızca Azure AD *'den* bir şekilde gerçekleşiyorsa, yönetilen bir etki alanındaki tüm sorunlar Azure AD 'yi veya şirket içi AD DS ortamları ve işlevleri etkilemez.

SKU düzeyi arttıkça, bu yedekleme anlık görüntülerinin sıklığı artar. Yönetilen etki alanınız için gereken yedekleme sıklığını öğrenmek üzere iş gereksinimlerinizi ve kurtarma noktası hedefini (RPO) gözden geçirin. İşiniz veya uygulama gereksinimleriniz değiştikçe ve daha sık yedeklemeler gerekiyorsa, farklı bir SKU 'ya geçiş yapabilirsiniz.

### <a name="outbound-forest-trusts"></a>Giden orman güvenleri

Önceki bölümde, tek yönlü giden orman, yönetilen bir etki alanından şirket içi AD DS ortamına güvenir. SKU, yönetilen bir etki alanı için oluşturabileceğiniz en fazla orman güveni sayısını belirler. İhtiyacınız olan güvenin sayısını öğrenmek için iş ve uygulama gereksinimlerinizi gözden geçirin ve uygun Azure AD DS SKU 'sunu seçin. Yine, iş gereksinimleriniz değiştiğinde ve ek orman güvenleri oluşturmanız gerekiyorsa, farklı bir SKU 'ya geçiş yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için [Azure AD DS yönetilen bir etki alanı oluşturun][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
