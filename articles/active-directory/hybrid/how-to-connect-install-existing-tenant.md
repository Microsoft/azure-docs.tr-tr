---
title: "Azure AD Connect: Zaten Azure AD 'ye sahipsiniz | Microsoft Docs"
description: Bu konuda, mevcut bir Azure AD kiracınız olduğunda Connect 'in nasıl kullanılacağı açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3636b88b14cf7e76e4fb023434316e7ee31ded04
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336815"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Mevcut bir kiracınız olduğunda
Azure AD Connect kullanımı ile ilgili konuların çoğu yeni bir Azure AD kiracısıyla başladığınız ve orada hiç Kullanıcı veya başka nesne olmadığı varsayılır. Ancak, bir Azure AD kiracısı ile başladıysanız, bunu kullanıcılar ve diğer nesnelerle doldurduktan sonra da Bağlan ' ı kullanmak istiyorsanız, bu konu sizin için önemlidir.

## <a name="the-basics"></a>Temel bilgiler
Azure AD 'deki bir nesne, bulutta (Azure AD) veya şirket içinde ana kopyalı olabilir. Tek bir nesne için, bazı öznitelikleri şirket içi ve Azure AD içindeki bazı öznitelikleri yönetemezsiniz. Her nesne, nesnenin nerede yönetildiğini belirten bir bayrak içerir.

Şirket içinde ve diğer kullanıcıları bulutta yönetebilirsiniz. Bu yapılandırma için yaygın bir senaryo, hesap çalışanları ve satış çalışanları karışımını içeren bir kuruluştur. Muhasebe çalışanlarının şirket içi bir AD hesabı vardır, ancak satış çalışanları Azure AD 'de bir hesaba sahip olurlar. Şirket içi ve bazı kullanıcıları Azure AD 'de yönetirsiniz.

Azure AD 'de bulunan ve şirket içi AD 'de bulunan kullanıcıları yönetmeye başladıysanız ve daha sonra Connect kullanmak istiyorsanız, dikkate almanız gereken bazı ek sorunlar vardır.

## <a name="sync-with-existing-users-in-azure-ad"></a>Azure AD 'de mevcut kullanıcılarla eşitleme
Azure AD Connect yüklediğinizde ve eşitlemeye başladığınızda, Azure AD eşitleme hizmeti (Azure AD 'de) her yeni nesne üzerinde bir denetim yapar ve eşleştirilecek varolan bir nesneyi bulmaya çalışır. Bu işlem için kullanılan üç öznitelik vardır: **userPrincipalName**, **proxyAddresses**ve **Sourcetutturucu**/**ImmutableID**. **UserPrincipalName** ve **proxyAddresses** ile bir eşleşme, **yumuşak eşleşme**olarak bilinir. **Sourcetutturucu** üzerinde bir eşleşme, **sabit eşleşme**olarak bilinir. **ProxyAddresses** özniteliği Için yalnızca **SMTP:** , birincil e-posta adresi olan değer değerlendirme için kullanılır.

Eşleşme yalnızca, Connect 'ten gelen yeni nesneler için değerlendirilir. Varolan bir nesneyi bu özniteliklerin herhangi biriyle eşleşmesine olanak verecek şekilde değiştirirseniz, bunun yerine bir hata görürsünüz.

Azure AD, bağlanmasından gelen bir nesne için öznitelik değerlerinin aynı olduğu ve Azure AD 'de zaten bulunduğu bir nesne bulursa, Azure AD 'deki nesne, Connect tarafından üzerinden alınır. Daha önce bulut tarafından yönetilen nesne, şirket içi yönetilen olarak işaretlenir. Şirket içi AD 'deki bir değere sahip Azure AD 'deki tüm özniteliklerin şirket içi değer ile üzerine yazılır. Özel durum, bir özniteliğin şirket içinde **null** değere sahip olduğu durumdur. Bu durumda, Azure AD 'deki değer kalır, ancak yine de şirket içinde yalnızca başka bir şeye değiştirebilirsiniz.

> [!WARNING]
> Azure AD 'deki tüm özniteliklerin şirket içi değer tarafından üzerine yazılacağı için şirket içinde iyi veri bulunduğundan emin olun. Örneğin, yalnızca Office 365 ' de yönetilen e-posta adresiniz varsa ve şirket içi AD DS güncel tutulmazsa, AD DS Azure AD/Office 365 ' deki tüm değerleri kaybedersiniz.

> [!IMPORTANT]
> Her zaman hızlı ayarlar tarafından kullanılan parola eşitleme ' yi kullanırsanız, Azure AD 'deki parolanın şirket içi AD 'deki parolayla üzerine yazılır. Kullanıcılarınız farklı parolaları yönetmek için kullanılıyorsa, Connect ' i yükledikten sonra şirket içi parolayı kullanmaları gerektiğini bilgilendirmeniz gerekir.

Önceki bölüm ve uyarı, planlamada dikkate alınmalıdır. Azure AD 'de, şirket içi AD DS yansıtılmamış çok sayıda değişiklik yaptıysanız, nesnelerinizi Azure AD Connect eşitlemeden önce AD DS güncelleştirilmiş değerlerle doldurmayı planlamanız gerekir.

Nesnelerinizi yumuşak eşleşme ile eşleştirdiniz, daha sonra sabit bir eşleşme kullanılabilmesi için Azure AD 'deki nesnesine **Sourcetutturucu** eklenir.

>[!IMPORTANT]
> Microsoft, şirket içi hesapların Azure Active Directory önceden var olan yönetim hesaplarıyla eşitlenmesini kesinlikle tavsiye eder.

### <a name="hard-match-vs-soft-match"></a>Sabit eşleşme vs ile yumuşak eşleşme
Yeni bir Connect yüklemesi için, yumuşak ve sabit eşleşme arasında pratik bir fark yoktur. Fark, bir olağanüstü durum kurtarma durumudur. Sunucunuzu Azure AD Connect kaybettiyseniz, verileri kaybetmeden yeni bir örneği yeniden yükleyebilirsiniz. İlk yüklemesi sırasında bağlanmak için Sourcetutturucuya sahip bir nesne gönderilir. Eşleşme daha sonra, Azure AD 'de aynı yapmaktan daha hızlı bir şekilde istemci (Azure AD Connect) tarafından değerlendirilir. Bir sabit eşleşme hem Connect hem de Azure AD tarafından değerlendirilir. Yumuşak eşleşme yalnızca Azure AD tarafından değerlendirilir.

### <a name="other-objects-than-users"></a>Kullanıcılardan diğer nesneler
Posta etkin gruplar ve kişiler için proxyAddresses temelinde geçici eşleştirme yapabilirsiniz. Yalnızca kullanıcılar için Sourcebağlayıcısını/ImmutableID 'ı (PowerShell kullanarak) güncelleştirebileceğinizden, sabit eşleşme geçerli değildir. Posta etkin olmayan gruplar için, şu anda, geçici eşleşme veya sabit eşleşme desteği yoktur.

### <a name="admin-role-considerations"></a>Yönetici rolü konuları
Güvenilmeyen şirket içi kullanıcıların, yönetici rolüne sahip bir bulut kullanıcıyla eşleşmesini engellemek için Azure AD Connect, yönetici rolüne sahip nesneler ile şirket içi Kullanıcı nesneleriyle eşleşmeyecektir. Bu varsayılan olarak olur. Bu davranışa geçici bir çözüm için şunları yapabilirsiniz:

1.  Dizin rollerini yalnızca bulutta bulunan kullanıcı nesnesinden kaldırın.
2.  Başarısız bir Kullanıcı eşitleme denemesi varsa, bulutta karantinaya alınan nesneyi kalıcı olarak silin.
3.  Bir eşitleme tetikleyin.
4.  Eşleme gerçekleştikten sonra isteğe bağlı olarak, Dizin rollerini buluttaki kullanıcı nesnesine geri ekleyin.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Azure AD 'de verilerden yeni bir şirket içi Active Directory oluşturma
Bazı müşteriler Azure AD ile yalnızca bulutta yer alan bir çözümle başlar ve şirket içi bir AD içermez. Daha sonra, şirket içi kaynakları kullanmak ve Azure AD verilerine dayalı olarak şirket içi AD oluşturmak ister. Azure AD Connect, bu senaryo için size yardımcı olamaz. Şirket içinde Kullanıcı oluşturmaz ve şirket içi parolayı Azure AD ile aynı olarak ayarlama yeteneği yoktur.

Şirket içi AD eklemeyi planladığınız tek neden, LOBs 'yi (Iş kolu uygulamaları) destekliyoruz, ancak bunun yerine [Azure AD etki alanı Hizmetleri](../../active-directory-domain-services/index.yml) 'ni kullanmayı göz önünde bulundurmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
