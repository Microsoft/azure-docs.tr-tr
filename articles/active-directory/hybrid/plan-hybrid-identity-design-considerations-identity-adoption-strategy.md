---
title: Karma kimlik tasarımı-benimseme stratejisi Azure | Microsoft Docs
description: Koşullu erişim denetimi ile, Azure AD, kullanıcının kimliğini doğrularken ve uygulamaya erişim izni vermeden önce seçtiğiniz belirli koşulları denetler.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836198"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Karma kimlik benimseme stratejisi tanımlama
Bu görevde, ' de açıklanan iş gereksinimlerini karşılamak üzere hibrit kimlik çözümü için karma kimlik benimseme stratejisini tanımlarsınız:

* [İş ihtiyaçlarını belirleme](plan-hybrid-identity-design-considerations-business-needs.md)
* [Dizin eşitleme gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Multi-Factor Authentication gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>İş ihtiyaçları stratejisini tanımlama
Kuruluşların iş ihtiyaçlarını belirleyen ilk görev adresi.  Bu çok geniş olabilir ve dikkatli değilseniz kapsam katlama meydana gelebilir.  Başlangıcında basit tutun, ancak gelecekte değişikliğe uyum sağlayacak ve bu şekilde değişiklik yapılmasını sağlayan bir tasarımı planlamaya her zaman hatırlıyorum.  Basit bir tasarım veya son derece karmaşık olmasına bakılmaksızın, Azure Active Directory Microsoft 365, Microsoft Online Services ve bulut kullanan uygulamaları destekleyen Microsoft Identity platform ' dır.

## <a name="define-an-integration-strategy"></a>Tümleştirme stratejisi tanımlama
Microsoft 'un bulut kimlikleri, eşitlenen kimlikler ve Federal kimlikler olan üç ana tümleştirme senaryosu vardır.  Bu tümleştirme stratejilerinden birini benimsemeye yönelik plan yapmanız gerekir.  Seçtiğiniz strateji farklılık gösterebilir ve bunlardan birini seçmek, ne tür bir kullanıcı deneyimi sağlamak istediğinizi, var olan bir altyapınızın olduğunu ve en uygun maliyetli olduğunu gösterir.  

![tümleştirme senaryoları](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Yukarıdaki şekilde tanımlanan senaryolar şunlardır:

* **Bulut kimlikleri**: bunlar yalnızca bulutta bulunan kimliklerdir.  Azure AD söz konusu olduğunda, özellikle Azure AD dizininizde yer alırlar.
* **Eşitlenmiş**: bunlar şirket içinde ve bulutta bulunan kimliklerdir.  Azure AD Connect kullanarak, bu kullanıcılar oluşturulmuş veya mevcut Azure AD hesaplarına katılmış durumda.  Kullanıcının parola karması, Parola karması olarak adlandırılan şirket içi ortamdan buluta eşitlenir.  Eşitlenmiş bir desteklenmediği uyarısıyla kullanıldığında, bir kullanıcı şirket içi ortamda devre dışı bırakılmışsa, bu hesap durumunun Azure AD 'de gösterilmesi üç saate kadar sürebilir.  Bunun nedeni eşitleme zaman aralığıdır.
* **Federasyon**: bu kimlikler hem şirket içinde hem de bulutta bulunur.  Azure AD Connect kullanarak, bu kullanıcılar oluşturulmuş veya mevcut Azure AD hesaplarına katılmış durumda.  

> [!NOTE]
> Eşitleme seçenekleri hakkında daha fazla bilgi için, Şirket [içi kimliklerinizi Azure Active Directory Ile tümleştirmeyi](whatis-hybrid-identity.md)okuyun.
> 
> 

Aşağıdaki tablo, aşağıdaki stratejilerin her birinin olumlu ve olumsuz yönlerini belirlemede yardımcı olur:

| Strateji | Avantajlar | Dezavantajlar |
| --- | --- | --- |
| **Bulut kimlikleri** |Küçük kuruluş için daha kolay yönetilebilir. <br> Şirket içinde yüklenecek bir şey yok. Ek donanım gerekmez<br>Kullanıcı şirketten ayrılırsa kolayca devre dışı bırakılır |Kullanıcıların buluttaki iş yüklerine erişirken oturum açması gerekir <br> Parolalar bulut ve şirket içi kimlikler için aynı olabilir veya olmayabilir |
| **Zaman** |Şirket içi parola, hem şirket içi hem de bulut dizinlerinin kimliğini doğrular <br>Küçük, orta veya büyük kuruluşlar için daha kolay yönetilebilir <br>Kullanıcılar bazı kaynaklar için çoklu oturum açma (SSO) içerebilir <br> Eşitleme için Microsoft tarafından tercih edilen yöntem <br> Daha kolay yönetilebilir |Bazı müşteriler, belirli bir şirketin ilkelerine bağlı olarak dizinlerini buluta eşitlebilmeyebilir |
| **Federe** |Kullanıcılar çoklu oturum açma (SSO) alabilir <br>Bir Kullanıcı sonlandırılırsa veya bırakılırsa, hesap hemen devre dışı bırakılabilir ve erişim iptal edilebilir.<br> Eşitlenmiş ile gerçekleştirilmeyebilir gelişmiş senaryoları destekler |Daha fazla ayarlama ve yapılandırma adımları <br> Daha yüksek bakım <br> STS altyapısı için ek donanım gerekebilir <br> Federasyon sunucusunu yüklemek için ek donanım gerekebilir. AD FS kullanılırsa ek yazılım gerekir <br> SSO için kapsamlı kurulum gerektir <br> Kritik başarısızlık noktası Federasyon sunucusu kapalıysa, kullanıcılar kimlik doğrulaması yapamaz |

### <a name="client-experience"></a>Müşteri deneyimi
Kullandığınız strateji, Kullanıcı oturum açma deneyimini dikte eder.  Aşağıdaki tablolar, kullanıcıların oturum açma deneyimini ne kadar beklemelerine ilişkin bilgiler sağlar.  Tüm federal kimlik sağlayıcıları tüm senaryolarda SSO 'yu desteklemez.

**Doman 'a katılmış ve özel ağ uygulamaları**:

| Uygulama | Eşitlenmiş kimlik | Federal Kimlik |
| --- | --- | --- |
| Web tarayıcıları |Form tabanlı kimlik doğrulaması |Çoklu oturum açma, bazen kuruluş KIMLIĞI sağlamak için gereklidir |
| Outlook |Kimlik bilgileri iste |Kimlik bilgileri iste |
| Skype Kurumsal (Lync) |Kimlik bilgileri iste |Lync için çoklu oturum açma, Exchange için istenen kimlik bilgileri |
| OneDrive İş |Kimlik bilgileri iste |Çoklu oturum açma |
| Office Pro Plus aboneliği |Kimlik bilgileri iste |Çoklu oturum açma |

**Dış veya güvenilmeyen kaynaklar**:

| Uygulama | Eşitlenmiş kimlik | Federal Kimlik |
| --- | --- | --- |
| Web tarayıcıları |Form tabanlı kimlik doğrulaması |Form tabanlı kimlik doğrulaması |
| Outlook, Skype Kurumsal (Lync), OneDrive Iş, Office aboneliği |Kimlik bilgileri iste |Kimlik bilgileri iste |
| Exchange ActiveSync |Kimlik bilgileri iste |Lync için çoklu oturum açma, Exchange için istenen kimlik bilgileri |
| Mobil uygulamalar |Kimlik bilgileri iste |Kimlik bilgileri iste |

Görev 1 ' den üçüncü taraf IDP olduğunu veya Azure AD ile Federasyon sağlamak için bir tane kullanacağınızı belirlerseniz, aşağıdaki desteklenen özellikleri bilmeniz gerekir:

* SP-Lite profili için uyumlu olan herhangi bir SAML 2,0 sağlayıcısı, Azure AD ve ilişkili uygulamalarla ilgili kimlik doğrulamasını destekleyebilir
* , OWA, SPO, vb. kimlik doğrulamasını kolaylaştıran pasif kimlik doğrulamasını destekler.
* Exchange Online istemcileri SAML 2,0 geliştirilmiş Istemci profili (ECP) aracılığıyla desteklenebilir

Ayrıca, kullanılabilecek özellikleri bilmeniz gerekir:

* WS-Trust/Federation desteği olmadan, tüm diğer etkin istemciler kesilir
  * Diğer bir deyişle, Skype istemcisi, OneDrive istemcisi, Office aboneliği, Office 2016 ' den önceki Office mobil
* Office 'in pasif kimlik doğrulamasına geçişi, saf SAML 2,0 IDPs 'yi desteklemeye izin verir, ancak destek yine de istemci istemci temelinde olmaya devam eder

> [!NOTE]
> En güncel liste için [Azure AD Federasyon uyumluluğu listesi](how-to-connect-fed-compatibility.md)makalesini okuyun.
> 
> 

## <a name="define-synchronization-strategy"></a>Eşitleme stratejisini tanımlama
Bu görevde, kuruluşun şirket içi verilerini buluta ve hangi topolojiyi kullanmanız gerektiğini eşitleyecek kullanılacak araçları tanımlayacaksınız.  Çoğu kuruluş Active Directory kullandığından, yukarıdaki sorulara yönelik Azure AD Connect kullanma hakkında bilgiler bazı ayrıntılarla sunulmaktadır.  Active Directory olmayan ortamlar için, bu stratejiyi planlamaya yardımcı olmak üzere FIM 2010 R2 veya MıM 2016 kullanmayla ilgili bilgiler vardır.  Ancak, Azure AD Connect gelecek sürümleri LDAP dizinlerini destekleyecektir, bu nedenle zaman çizelgenize bağlı olarak bu bilgiler size yardımcı olabilir.

### <a name="synchronization-tools"></a>Eşitleme araçları
Yıl boyunca birkaç eşitleme aracı vardı ve çeşitli senaryolar için kullanılır.  Şu anda Azure AD Connect tüm desteklenen senaryolar için seçim aracına git.  AAD Eşitleme ve DirSync de hala yerinde ve ortamınızda de mevcut olabilir. 

> [!NOTE]
> Her aracın desteklenen özellikleri ile ilgili en son bilgiler için, [Dizin tümleştirme araçları karşılaştırma](plan-hybrid-identity-design-considerations-tools-comparison.md) makalesini okuyun.  
> 
> 

### <a name="supported-topologies"></a>Desteklenen topolojiler
Bir eşitleme stratejisi tanımlarken, kullanılan topoloji belirtilmelidir. 2. adımda belirlenen bilgilere bağlı olarak hangi topolojinin kullanılması gerektiğini belirleyebilirsiniz. Tek orman, tek Azure AD topolojisi en yaygın ve tek bir Active Directory ormanı ve tek bir Azure AD örneğinden oluşur.  Bu, Çoğu senaryoda kullanılacaktır ve aşağıdaki şekilde gösterildiği gibi Azure AD Connect Express yüklemesi kullanılırken beklenen topolojidir.

![Desteklenen topolojiler ](./media/plan-hybrid-identity-design-considerations/single-forest.png) Tek orman senaryosu büyük ve hatta küçük kuruluşların, Şekil 5 ' te gösterildiği gibi birden çok ormanı olması yaygındır.

> [!NOTE]
> Azure AD Connect eşitleme ile farklı şirket içi ve Azure AD topolojileri hakkında daha fazla bilgi için [Azure AD Connect makale topolojilerini](plan-connect-topologies.md)okuyun.
> 
> 

![Çok ormanlı topoloji](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Çok ormanlı senaryo

Bu durumda, aşağıdaki öğeler doğru ise çok ormanlı tek Azure AD topolojisi göz önünde bulundurulmalıdır:

* Kullanıcıların tüm ormanlarda yalnızca 1 kimliği vardır: aşağıdaki kullanıcıları benzersiz olarak tanımlama bölümünde bu daha ayrıntılı bir şekilde açıklanmıştır.
* Kullanıcı kimliğinin bulunduğu ormanın kimliğini doğrular
* UPN ve kaynak bağlantısı (Sabit kimlik) bu ormandan geliyor
* Tüm ormanlarda Azure AD Connect tarafından erişilebilir. Bu, etki alanına katılmış olması gerekmediği ve bunu kolaylaştırmak için bir DMZ yerleştirilebileceği anlamına gelir.
* Kullanıcıların yalnızca bir posta kutusu vardır
* Kullanıcının posta kutusunu barındıran ormanın, Exchange genel adres listesinde (GAL) görünecek özniteliklerin en iyi veri kalitesi vardır
* Kullanıcı üzerinde bir posta kutusu yoksa, bu değerlere katkıda bulunmak için herhangi bir orman kullanılabilir
* Bağlı bir posta kutunuz varsa, farklı bir ormanda oturum açmak için kullanılan başka bir hesap de vardır.

> [!NOTE]
> Hem şirket içinde hem de bulutta bulunan nesneler, benzersiz bir tanımlayıcı aracılığıyla "bağlanır". Dizin eşitleme bağlamında, bu benzersiz tanımlayıcı Sourcetutturucu olarak adlandırılır. Çoklu oturum açma bağlamında, bu, ImmutableID olarak adlandırılır. Sourcebağlayıcının kullanımıyla ilgili daha fazla bilgi için, [Azure AD Connect için kavramlar tasarlayın](plan-connect-design-concepts.md#sourceanchor) .
> 
> 

Yukarıdaki doğru değilse ve birden fazla etkin hesabınız veya birden fazla posta kutusu varsa Azure AD Connect, birini seçer ve diğerini yoksayar.  Bağlı posta kutularınız varsa, ancak başka bir hesap yoksa, bu hesaplar Azure AD 'ye aktarılmaz ve bu kullanıcı herhangi bir grubun üyesi olmayacaktır.  Bu, daha önce DirSync ile olan ve bu çok ormanlı senaryolara daha iyi destek vermek için bilerek farklıdır. Aşağıdaki şekilde çok ormanlı bir senaryo gösterilmiştir.

![birden çok Azure AD kiracısından](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Çok ormanlı birden çok Azure AD senaryosu**

Bir kuruluş için Azure AD 'de yalnızca tek bir dizin olması önerilir, ancak bir Azure AD Connect eşitleme sunucusu ve bir Azure AD dizini arasında 1:1 ilişkisi tutulur.  Her Azure AD örneği için bir Azure AD Connect yüklemeniz gerekir.  Ayrıca, Azure AD, tasarıma göre yalıtılmıştır ve bir Azure AD örneğindeki kullanıcılar başka bir örnekteki kullanıcıları göremeyecektir.

Aşağıdaki şekilde gösterildiği gibi, bir şirket içi Active Directory örneğini birden çok Azure AD dizinine bağlamak mümkündür ve desteklenir:

![Tek orman filtreleme](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Tek ormanlı filtreleme senaryosu**

Bunu yapmak için, aşağıdakilerin doğru olması gerekir:

* Azure AD Connect eşitleme sunucularının, her birinin birbirini dışlayan bir nesne kümesine sahip olacak şekilde filtrelenebilmesi için yapılandırılması gerekir.  Bu, örneğin, her bir sunucunun belirli bir etki alanı veya OU ile kapsamı ile yapılır.
* DNS etki alanı yalnızca tek bir Azure AD dizinine kaydedilebilir, böylece şirket içi AD içindeki kullanıcıların UPN 'leri ayrı ad alanları kullanmalıdır
* Azure AD 'nin bir örneğindeki kullanıcılar yalnızca kendi örneklerinden kullanıcıları görebilirler.  Diğer örneklerde kullanıcıları göremeyecektir
* Azure AD dizinlerinden yalnızca biri şirket içi AD ile Exchange karma 'i etkinleştirebilir
* Karşılıklı denetim, geri yazma için de geçerlidir.  Bu, tek bir şirket içi yapılandırma varsaydığından bu topolojide bazı geri yazma özelliklerinin desteklenmesini sağlar.  Şunları içerir:
  * Varsayılan yapılandırma ile grup geri yazma
  * Cihaz geri yazma

Aşağıdakiler desteklenmez ve bir uygulama olarak seçilmemelidir:

* Birden çok Azure AD Connect eşitleme sunucusunun, birbirini dışlayan nesne kümesini eşitlemek üzere yapılandırılmış olsa bile aynı Azure AD dizinine bağlanması desteklenmez
* Aynı kullanıcının birden çok Azure AD dizini ile eşitlenmesi desteklenmez. 
* Ayrıca, bir Azure AD 'deki kullanıcıların başka bir Azure AD dizininde kişi olarak görünmesini sağlamak için bir yapılandırma değişikliği yapmak de desteklenmez. 
* Ayrıca, birden çok Azure AD dizinine bağlanmak üzere Azure AD Connect eşitlenecek şekilde değiştirmek de desteklenmez.
* Azure AD dizinleri tasarıma göre yalıtılmış. Dizinler arasında ortak ve Birleşik bir GAL oluşturma girişiminde, başka bir Azure AD dizininden veri okumak üzere Azure AD Connect eşitleme 'nin yapılandırılmasını değiştirmek desteklenmez. Ayrıca, Azure AD Connect eşitleme kullanarak kullanıcıları başka bir şirket içi AD 'ye dışarı aktarmak de desteklenmez.

> [!NOTE]
> Kuruluşunuz ağınızdaki bilgisayarları Internet 'e bağlanılmamasını kısıtlarsa Bu makalede, bilgisayarlarınızın Microsoft 365 başarıyla kullanabilmesi için, istemci bilgisayarların giden izin verilenler listelerine ve Internet Explorer güvenilen siteler bölgesine dahil etmeniz gereken uç noktalar (FQDN 'Ler, IPv4 ve IPv6 adres aralıkları) listelenir. Daha fazla bilgi için [Office 365 URL 'leri ve IP adresi aralıklarını](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)okuyun.
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Multi-Factor Authentication stratejisini tanımlama
Bu görevde, kullanılacak Multi-Factor Authentication stratejisini tanımlayacaksınız.  Azure AD Multi-Factor Authentication iki farklı sürümde sunulur.  Bunlardan biri, bulut tabanlıdır ve diğeri de Azure MFA sunucusu kullanılarak şirket içinde yer alır.  Yukarıdaki değerlendirmeye bağlı olarak, stratejiniz için doğru bir çözüm olduğunu belirleyebilirsiniz.  Şirketinizin güvenlik gereksinimini en iyi şekilde karşılayan tasarım seçeneğinin hangisi olduğunu öğrenmek için aşağıdaki tabloyu kullanın:

Multi-Factor tasarım seçenekleri:

| Güvenli hale getirmek için varlık | Bulutta MFA | Şirket içi MFA |
| --- | --- | --- |
| Microsoft uygulamaları |evet |evet |
| Uygulama galerisinde SaaS uygulamaları |evet |evet |
| Azure AD Uygulaması Proxy üzerinden yayımlanan IIS uygulamaları |evet |evet |
| Azure AD Uygulaması Proxy 'Si aracılığıyla yayımlanmayan IIS uygulamaları |hayır |evet |
| VPN olarak uzaktan erişim, RDG |hayır |evet |

Stratejiniz için bir çözümü kapatmış olsanız da, hala kullanıcılarınızın bulunduğu konumda değerlendirmeyi kullanmanız gerekir.  Bu, çözümün değişmesine neden olabilir.  Bunu belirlemenize yardımcı olması için aşağıdaki tabloyu kullanın:

| Kullanıcı konumu | Tercih edilen tasarım seçeneği |
| --- | --- |
| Azure Active Directory |Bulutta Multi-Factorayuthentication |
| AD FS ile federasyon kullanana Azure AD ve şirket içi AD |Her ikisi |
| Azure AD Connect parola eşitleme için Azure AD ve şirket içi AD kullanma |Her ikisi |
| Parola eşitleme ile Azure AD Connect kullanarak Azure AD ve şirket içi |Her ikisi |
| Şirket içi AD |Multi-Factor Authentication Sunucusu |

> [!NOTE]
> Ayrıca, seçtiğiniz Multi-Factor Authentication tasarım seçeneğinin tasarımınız için gereken özellikleri desteklediğinden emin olmanız gerekir.  Daha fazla bilgi edinmek için [Multi-Factor Security çözümünü sizin Için seçin](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor auth sağlayıcısı
Multi-Factor Authentication, Azure Active Directory kiracısına sahip genel Yöneticiler için varsayılan olarak kullanılabilir. Ancak, çok faktörlü kimlik doğrulamasını tüm kullanıcılarınıza uzatmak ve/veya genel yöneticileriniz için yönetim portalı, özel Tebrikler ve raporlar gibi özelliklerin avantajlarından yararlanmak istiyorsanız, Multi-Factor Authentication sağlayıcısı satın alıp yapılandırmanız gerekir.

> [!NOTE]
> Ayrıca, seçtiğiniz Multi-Factor Authentication tasarım seçeneğinin tasarımınız için gereken özellikleri desteklediğinden emin olmanız gerekir. 
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
[Veri koruma gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Ayrıca bkz.
[Tasarım konularına genel bakış](plan-hybrid-identity-design-considerations-overview.md)

