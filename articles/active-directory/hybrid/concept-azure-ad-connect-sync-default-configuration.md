---
title: 'Azure AD Connect eşitleme: Varsayılan yapılandırmayı anlama | Microsoft Docs'
description: Bu makalede Azure AD Connect eşitlemede varsayılan yapılandırma açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfaf3cc9b113ff10766f7a17bd7bf09ffa619a8e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227428"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect eşitleme: Varsayılan yapılandırmayı anlama
Bu makalede, kullanıma hazır yapılandırma kuralları açıklanmaktadır. Kuralları ve bu kuralların yapılandırmayı nasıl etkilediğini belgelemektedir. Ayrıca, Azure AD Connect eşitleme 'nin varsayılan yapılandırması boyunca size yol gösterir. Amaç, okuyucunun bildirim temelli sağlama adlı yapılandırma modelinin gerçek dünyada bir örnekte nasıl çalıştığını anlamaktır. Bu makalede, Yükleme Sihirbazı 'nı kullanarak Azure AD Connect eşitleme 'yi zaten yüklemiş ve yapılandırdığınız varsayılmaktadır.

Yapılandırma modelinin ayrıntılarını anlamak için [bildirim temelli sağlamayı anlama](concept-azure-ad-connect-sync-declarative-provisioning.md)konusunu okuyun.

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Şirket içinden Azure AD 'ye yerleşik kurallar
Aşağıdaki ifadeler, hazır olmayan yapılandırmada bulunabilir.

### <a name="user-out-of-box-rules"></a>Kullanıcı hazır kuralları
Bu kurallar InetOrgPerson nesne türü için de geçerlidir.

Bir Kullanıcı nesnesinin eşitlenmesi için aşağıdakileri karşılaması gerekir:

* Bir Sourcetutturucu olmalıdır.
* Azure AD 'de nesne oluşturulduktan sonra Sourcetutturucu değiştirilemez. Değer Şirket içinde değiştirilirse, kaynak Bağlayıcısı önceki değerine geri değiştirilene kadar nesne eşitlemeyi sonlandırır.
* AccountEnabled (userAccountControl) özniteliği doldurulmuş olmalıdır. Şirket içi Active Directory, bu öznitelik her zaman vardır ve doldurulur.

Aşağıdaki kullanıcı nesneleri Azure AD **ile eşitlenmez:**

* `IsPresent([isCriticalSystemObject])`. Yerleşik yönetici hesabı gibi Active Directory çok sayıda hazır nesnenin bulunduğundan emin olun.
* `IsPresent([sAMAccountName]) = False`. SAMAccountName özniteliği olmayan kullanıcı nesnelerinin eşitlenmediğinden emin olun. Bu durum yalnızca, NT4 'den yükseltilen bir etki alanında yalnızca pratikte gerçekleşir.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Azure AD Connect Sync ve önceki sürümleri tarafından kullanılan hizmet hesabını eşitlememe.
* Exchange Online 'da çalışmayan Exchange hesaplarını eşitlememe.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Exchange Online 'da çalışmayan nesneleri eşitlememe.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Bu bit maskesi (& H21C07000) aşağıdaki nesneleri filtreleyecek:
  * Posta etkin ortak klasör (sürüm 1.1.524.0 itibariyle önizlemede)
  * Sistem Görevlisi posta kutusu
  * Posta kutusu veritabanı posta kutusu (sistem posta kutusu)
  * Evrensel güvenlik grubu (bir kullanıcı için kullanılamaz, ancak eski nedenlerle vardır)
  * Evrensel olmayan Grup (Kullanıcı için kullanılamaz, ancak eski nedenlerle vardır)
  * Posta kutusu planı
  * Keşif posta kutusu
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Herhangi bir çoğaltma kurbanı nesnesini eşitlememe.

Aşağıdaki öznitelik kuralları geçerlidir:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Sourcetutturucu özniteliği bağlı bir posta kutusundan katkıda bulunmuyor. Bağlı bir posta kutusu bulunursa gerçek hesabın daha sonra katıldığını kabul edilir.
* Exchange ile ilgili öznitelikler yalnızca, **Mailrumonun** değeri varsa eşitlenir.
* Birden çok orman olduğunda, öznitelikler aşağıdaki sırada kullanılır:
  1. Oturum açma ile ilgili öznitelikler (örneğin, userPrincipalName), ormandan etkin bir hesapla katkıda bulunur.
  2. Bir Exchange GAL (genel adres listesi) içinde bulunan öznitelikler, bir Exchange posta kutusuyla ormandan katkıda bulunur.
  3. Posta kutusu bulunamazsa, bu öznitelikler herhangi bir ormandan gelebilir.
  4. Exchange ile ilgili öznitelikler (GAL 'te görünmeyen teknik öznitelikler), ormanda yer aldığı `mailNickname ISNOTNULL`ormandan katkıda bulunur.
  5. Bu kurallardan birine uyan birden fazla orman varsa, hangi ormanın özniteliklere katkıda bulunduğunu belirlemede kullanılan bağlayıcılar (ormanlar) oluşturma sırası (Tarih/saat) kullanılır. Bağlanan ilk orman, eşitlenecek ilk orman olacak. 

### <a name="contact-out-of-box-rules"></a>Hazır olmayan kurallara ulaşın
Bir kişi nesnesinin eşitlenmesi için aşağıdakileri karşılaması gerekir:

* Kişinin posta etkin olması gerekir. Aşağıdaki kurallarla doğrulanır:
  * `IsPresent([proxyAddresses]) = True)`. ProxyAddresses özniteliği doldurulmalıdır.
  * Birincil e-posta adresi, proxyAddresses özniteliğinde veya mail özniteliğinde bulunabilir. Öğesinin \@ varlığı, içeriğin bir e-posta adresi olduğunu doğrulamak için kullanılır. Bu iki kuralın biri doğru olarak değerlendirilmelidir.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. "SMTP:" ile bir giriş var ve \@ varsa dizede bulunabilir mi?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Posta özniteliği doldurulmuş \@ mi, yoksa dizede mi bulunabilir?

Aşağıdaki iletişim nesneleri Azure AD **ile eşitlenmez:**

* `IsPresent([isCriticalSystemObject])`. Kritik olarak işaretlenen hiçbir kişi nesnesinin eşitlendiğinden emin olun. Varsayılan yapılandırma ile herhangi bir olmamalıdır.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Bu nesneler Exchange Online 'da çalışmaz.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Herhangi bir çoğaltma kurbanı nesnesini eşitlememe.

### <a name="group-out-of-box-rules"></a>Box dışı kuralları Gruplandır
Bir grup nesnesinin eşitlenmesi için aşağıdakileri karşılaması gerekir:

* 50.000 'den az üye olmalıdır. Bu sayı, şirket içi gruptaki üyelerin sayısıdır.
  * Eşitleme ilk kez başlatılmadan daha fazla üyeye sahipse, Grup eşitlenmez.
  * Üye sayısı başlangıçta oluşturulduğunda büyürken, üyelik sayısı 50.000 ' den daha az olana kadar, 50.000 üyeye ulaştığında eşitleme işlemini sonlandırır.
  * Not: 50.000 üyelik sayısı da Azure AD tarafından zorlanır. Bu kuralı değiştirseniz veya kaldırsanız bile grupları daha fazla üye ile eşitleyemezsiniz.
* Grup bir **dağıtım grubu**ise, ayrıca posta etkin olmalıdır. Bu kural için [kişi hazır iletişim kuralları](#contact-out-of-box-rules) bölümüne bakın.

Aşağıdaki Grup nesneleri Azure AD **ile eşitlenmez:**

* `IsPresent([isCriticalSystemObject])`. Yerleşik Yöneticiler grubu gibi Active Directory çok sayıda kullanıma hazır nesne bulunduğundan emin olun.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. DirSync tarafından kullanılan eski grup.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Rol grubu.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Herhangi bir çoğaltma kurbanı nesnesini eşitlememe.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal dışı kurallar
FSPS 'ler meta veri deposundaki "Any"\*() nesnesine birleştirilir. Gerçekte, bu JOIN yalnızca kullanıcılar ve güvenlik grupları için gerçekleşir. Bu yapılandırma, Azure AD 'de ormanlar arası üyeliklerin çözümlenmesini ve doğru şekilde temsil edilmesini sağlar.

### <a name="computer-out-of-box-rules"></a>Bilgisayar kutudan çıkar kuralları
Bir bilgisayar nesnesinin eşitlenmesi için aşağıdakileri karşılaması gerekir:

* `userCertificate ISNOTNULL`. Bu özniteliği yalnızca Windows 10 bilgisayarları doldurur. Bu öznitelikte bir değere sahip tüm bilgisayar nesneleri eşitlenir.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Hazır kurallar senaryosunu anlama
Bu örnekte, bir hesap ormanı (A), bir kaynak ormanı (R) ve bir Azure AD dizini ile bir dağıtım kullanıyoruz.

![Senaryo açıklamasıyla resim](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Bu yapılandırmada, hesap ormanında etkin bir hesap ve bağlı bir posta kutusuyla kaynak ormanında devre dışı bırakılmış bir hesap olduğu varsayılır.

Varsayılan yapılandırmayla ilgili hedefiniz:

* Oturum açma ile ilgili öznitelikler, etkin hesapla ormandan eşitlenir.
* GAL (genel adres listesi) içinde bulunan öznitelikler, posta kutusuyla ormandan eşitlenir. Posta kutusu bulunamazsa, başka bir orman kullanılır.
* Bağlı bir posta kutusu bulunursa, nesnenin Azure AD 'ye aktarılması için bağlantılı etkinleştirilmiş hesabın bulunması gerekir.

### <a name="synchronization-rule-editor"></a>Eşitleme kuralı Düzenleyicisi
Yapılandırma, araç eşitleme kuralları Düzenleyicisi (SRE) ile görüntülenebilir ve değiştirilebilir ve başlangıç menüsünde bir kısayol bulunabilir.

![Eşitleme kuralları Düzenleyicisi simgesi](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE bir kaynak seti aracıdır ve Azure AD Connect eşitleme ile yüklenir. Başlayabilmek için ADSyncAdmins grubunun üyesi olmanız gerekir. Başladığında şuna benzer bir şey görürsünüz:

![Gelen eşitleme kuralları](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Bu bölmede, yapılandırmanız için oluşturulan tüm eşitleme kurallarını görürsünüz. Tablodaki her satır bir eşitleme kuralıdır. Kural türlerinin sol tarafında, iki farklı tür listelenir: Gelen ve giden. Gelen ve giden, meta veri deposu görünümünden oluşur. Bu genel bakışta genellikle gelen kurallara odaklanırsınız. Eşitleme kurallarının gerçek listesi AD 'de algılanan şemaya bağlıdır. Yukarıdaki resimde, hesap ormanı (fabrikamonline.com) Exchange ve Lync gibi herhangi bir hizmete sahip değildir ve bu hizmetler için hiçbir eşitleme kuralı oluşturulmaz. Bununla birlikte, kaynak ormanında (res.fabrikamonline.com), bu hizmetler için eşitleme kuralları ' nı bulabilirsiniz. Kuralların içeriği, algılanan sürüme bağlı olarak farklılık açmış. Örneğin, Exchange 2013 ile bir dağıtımda, Exchange 2010/2007 ' den daha fazla öznitelik akışı yapılandırılabilir.

### <a name="synchronization-rule"></a>Eşitleme kuralı
Eşitleme kuralı, bir koşul karşılandığında bir dizi öznitelik içeren bir yapılandırma nesnesidir. Ayrıca, bir bağlayıcı alanındaki bir nesnenin, meta veri deposundaki bir nesneyle ilgili olduğunu, **JOIN** veya **Match**olarak bilinir. Eşitleme kuralları, birbirleriyle ilişkilerini gösteren bir öncelik değeri sağlar. Daha düşük bir sayısal değere sahip bir eşitleme kuralının önceliği daha yüksektir ve bir öznitelik akışı çakışmasında, daha yüksek öncelikli çakışma çözümü kazanır.

Örnek olarak, **ad – Kullanıcı AccountEnabled Içindeki**eşitleme kuralına bakın. Bu satırı SRE 'de işaretleyin ve **Düzenle**' yi seçin.

Bu kural hazır olmayan bir kural olduğundan, kuralı açtığınızda bir uyarı alırsınız. Hazır olmayan kurallara herhangi bir [değişiklik](how-to-connect-sync-best-practices-changing-default-configuration.md)yapmamalıdır, bu nedenle, amaclarınızın ne olduğunu sordunuz. Bu durumda, yalnızca kuralı görüntülemek istersiniz. **Hayır**' ı seçin.

![Eşitleme kuralları uyarısı](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Eşitleme kuralında dört yapılandırma bölümü vardır: Açıklama, kapsam filtresi, JOIN kuralları ve dönüşümler.

#### <a name="description"></a>Açıklama
İlk bölüm, ad ve açıklama gibi temel bilgileri sağlar.

![Eşitleme kuralı Düzenleyicisi 'ndeki Açıklama sekmesi](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Ayrıca, bu kuralın hangi bağlı sistem ile ilgili olduğunu, hangi bağlı sistemdeki hangi nesne türünü ve meta veri deposu nesne türünü de bulabilirsiniz. Meta veri deposu nesne türü, kaynak nesne türü bir Kullanıcı, iNetOrgPerson veya ilgili kişi olduğunda her zaman kişidir. Meta veri deposu nesne türü hiçbir şekilde değiştirilmemelidir, bu nedenle genel bir tür olarak oluşturulur. Bağlantı türü, JOIN, StickyJoin veya provision olarak ayarlanabilir. Bu ayar, ekleme kuralları bölümü ile birlikte çalışarak daha sonra ele alınmıştır.

Ayrıca, bu eşitleme kuralının parola eşitleme için kullanıldığını da görebilirsiniz. Bir Kullanıcı bu eşitleme kuralına yönelik kapsamdadır, parola Şirket içinden buluta eşitlenir (parola eşitleme özelliğini etkinleştirdiğiniz varsayılarak).

#### <a name="scoping-filter"></a>Kapsam filtresi
Kapsam Filtresi bölümü, bir eşitleme kuralının ne zaman uygulanacağını yapılandırmak için kullanılır. Aradığınız eşitleme kuralının adı, yalnızca etkin kullanıcılar için uygulanması gerektiğini gösterdiği için, kapsam yapılandırılır, bu yüzden AD özniteliğinin **UserAccountControl** bit 2 kümesine sahip olmamalıdır. Eşitleme altyapısı AD 'de bir Kullanıcı bulduğunda, **UserAccountControl** ondalık değer 512 (normal Kullanıcı etkin) olarak ayarlandığında bu eşitleme kuralını uygular. Kullanıcı **userAccountControl** 514 olarak ayarlandığında kuralı uygulamaz (normal kullanıcı devre dışı bırakılır).

![Eşitleme kuralı düzenleyicisinde kapsam sekmesi](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Kapsam filtresi, iç içe olabilecek gruplara ve yan tümcelerde sahiptir. Bir eşitleme kuralının uygulanması için bir grup içindeki tüm yan tümceleri karşılamalıdır. Birden çok grup tanımlandığında kuralın uygulanabilmesi için en az bir grup karşılanması gerekir. Diğer bir deyişle, bir mantıksal veya gruplar arasında değerlendirilir ve bir grup içinde değerlendirilir. Bu yapılandırmaya bir örnek, **AAD-Group JOIN 'e**giden eşitleme kuralında bulunabilir. Örneğin, güvenlik grupları (`securityEnabled EQUAL True`) ve diğeri dağıtım grupları (`securityEnabled EQUAL False`) için bir tane olmak üzere birkaç eşitleme filtresi grubu vardır.

![Eşitleme kuralı düzenleyicisinde kapsam sekmesi](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Bu kural, Azure AD 'ye hangi grupların sağlanması gerektiğini tanımlamak için kullanılır. Dağıtım grupları, Azure AD ile eşitlenecek posta özelliğinin etkin olması gerekir, ancak güvenlik grupları için bir e-posta gerekli değildir.

#### <a name="join-rules"></a>Ekleme kuralları
Üçüncü bölüm, bağlayıcı alanındaki nesnelerin meta veri deposundaki nesnelerle ilişkilerini yapılandırmak için kullanılır. Daha önce bakmış olduğunuz kural, JOIN kuralları için herhangi bir yapılandırmaya sahip değildir, bu nedenle **ad – Kullanıcı birleşiminden**göz atacağız.

![Eşitleme kuralı Düzenleyicisi 'nde kuralları Birleştir sekmesi](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

JOIN kuralının içeriği, Yükleme sihirbazında seçilen eşleşen seçeneğe bağlıdır. Bir gelen kuralı için değerlendirme, kaynak bağlayıcı alanındaki bir nesneyle başlar ve JOIN kurallarındaki her grup sırayla değerlendirilir. Bir kaynak nesne, JOIN kurallarından birini kullanarak metadizesinde tam olarak bir nesneyle eşleşecek şekilde değerlendiriliyorsa, nesneler birleştirilir. Tüm kurallar değerlendiriliyorsa ve eşleşme yoksa, açıklama sayfasındaki bağlantı türü kullanılır. Bu yapılandırma **sağlama**olarak ayarlandıysa, hedefte yeni bir nesne oluşturulur, meta veri deposu. Meta veri deposuna yeni bir nesne sağlamak için, meta veri deposuna bir nesne **Proje** olarak da bilinir.

JOIN kuralları yalnızca bir kez değerlendirilir. Bir bağlayıcı alanı nesnesi ve bir meta veri deposu nesnesi katıldığında, eşitleme kuralının kapsamı hala karşılanmadığı sürece bu nesneler birleştirilmiş olarak kalır.

Eşitleme kuralları değerlendirilirken, yalnızca tanımlanmış JOIN kuralları olan bir eşitleme kuralı kapsamda olmalıdır. Bir nesne için JOIN kurallarına sahip birden çok eşitleme kuralı bulunursa bir hata oluşur. Bu nedenle en iyi yöntem, bir nesne için kapsamda birden çok eşitleme kuralı olduğunda, JOIN ile yalnızca bir eşitleme kuralına sahip olmalıdır. Azure AD Connect eşitleme için hazır yapılandırma için, bu kurallar ada bakarak ve adın sonundaki sözcük **katılımı** bulacak şekilde bulunabilir. Herhangi bir katılım kuralı olmadan eşitleme kuralı, başka bir eşitleme kuralı nesnelere birlikte katıldığında veya hedefte yeni bir nesne sağladığında öznitelik akışlarını uygular.

Yukarıdaki resme bakarsanız, kuralın, hesap-kaynak orman topolojisinde Beklendiğimiz, **msExchMasterAccountSid** (Exchange) ve **msRTCSIP-originatorsid** (Lync) ile **objectSID** 'ye katılmayı denediğini görebilirsiniz. Aynı kuralı tüm ormanlarda bulabilirsiniz. Varsayımı, her ormanın bir hesap veya kaynak ormanı olabilir. Bu yapılandırma, tek bir ormanda bulunan ve katılması gereken hesaplarınız varsa da kullanılır.

#### <a name="transformations"></a>Dönüşümler
Dönüştürme bölümü, nesneler katıldığında ve kapsam filtresi karşılandığında hedef nesneye uygulanan tüm öznitelik akışlarını tanımlar. **Ad – Kullanıcı AccountEnabled** eşitleme kuralı ' na geri dönerek aşağıdaki dönüşümleri bulabilirsiniz:

![Eşitleme kuralı Düzenleyicisi 'ndeki dönüşümler sekmesi](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Bu yapılandırmayı bağlam içine koymak için bir hesap-kaynak orman dağıtımında, hesap ormanında etkin bir hesap ve Exchange ve Lync ayarları ile kaynak ormanında devre dışı bırakılmış bir hesap bulunması beklenir. Aradığınız eşitleme kuralı, oturum açma için gereken öznitelikleri içerir ve bu özniteliklerin etkin bir hesabın olduğu ormandan akışı gerekir. Tüm bu öznitelik akışları tek bir eşitleme kuralında birlikte konur.

Bir dönüştürme farklı türlere sahip olabilir: Sabit, doğrudan ve Ifade.

* Sabit bir akış her zaman sürekli kodlanmış bir değer akar. Yukarıdaki durumda, her zaman **Accountenabled**adlı meta veri deposu özniteliğinde **true** değerini ayarlar.
* Doğrudan akış, kaynak içindeki özniteliğin değerini her zaman olduğu gibi hedef özniteliğe akar.
* Üçüncü akış türü Ifadedir ve daha gelişmiş yapılandırmalara izin verir.

İfade dili VBA (Visual Basic for Applications), bu nedenle Microsoft Office veya VBScript deneyimi olan kişiler biçimi tanır. Öznitelikler köşeli ayraç, [attributeName] içine alınmıştır. Öznitelik adları ve işlev adları büyük/küçük harfe duyarlıdır, ancak eşitleme kuralları Düzenleyicisi ifadeleri değerlendirir ve ifade geçerli değilse bir uyarı verir. Tüm ifadeler, iç içe yerleştirilmiş işlevlerle tek bir satırda ifade edilir. Yapılandırma dilinin gücünü göstermek için pwdLastSet için akış, ancak ek açıklamalar eklenmiştir:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Öznitelik akışları için ifade dili hakkında daha fazla bilgi için bkz. [bildirim temelli sağlama Ifadelerini anlama](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Öncellik
Artık bazı bireysel eşitleme kurallarına baktınız, ancak kurallar yapılandırmada birlikte çalışıyor. Bazı durumlarda, bir öznitelik değeri birden çok eşitleme kurallarından aynı hedef özniteliğe katkıda bulunur. Bu durumda, hangi özniteliğin WINS 'e yönelik olduğunu belirleyen öznitelik önceliği kullanılır. Örnek olarak, Sourcetutturucu özniteliğine bakın. Bu öznitelik, Azure AD 'de oturum açabilmeniz için önemli bir özniteliktir. Bu öznitelik için bir öznitelik akışını **ad – Kullanıcı AccountEnabled** ve **from ad – Kullanıcı ortak**olan iki farklı eşitleme kuralına göre bulabilirsiniz. Eşitleme kuralı önceliği nedeniyle, meta veri deposu nesnesine katılmış birkaç nesne olduğunda, Sourcetutturucu özniteliği, önce etkin bir hesapla ormandan katkıda bulunur. Etkin hesap yoksa, eşitleme altyapısı **ad – Kullanıcı ortak olan ' daki**catch-all eşitleme kuralını kullanır. Bu yapılandırma, devre dışı bırakılan hesaplar için de bir Sourcetutturucu olduğundan emin olur.

![Gelen eşitleme kuralları](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Eşitleme kuralları için öncelik, Yükleme Sihirbazı tarafından gruplar halinde ayarlanır. Bir gruptaki tüm kuralların aynı adı vardır, ancak bunlar farklı bağlı dizinlere bağlanır. Yükleme Sihirbazı, kuralı **ad – Kullanıcı birleşimi** en yüksek önceliğe verir ve tüm bağlı ad dizinleri üzerinde yinelenir. Daha sonra önceden tanımlanmış bir sırada sonraki kural grupları ile devam eder. Bir grup içinde kurallar, bağlayıcı eklendiği sırada eklenir. Sihirbaz aracılığıyla başka bir bağlayıcı eklenirse, eşitleme kuralları yeniden sıralanmaz ve yeni bağlayıcının kuralları her gruba son eklenir.

### <a name="putting-it-all-together"></a>Hepsini bir araya getirme
Artık yapılandırmanın farklı eşitleme kurallarıyla nasıl çalıştığını anlayabilmek için eşitleme kuralları hakkında yeterli bilgi veriyoruz. Meta veri deposuna katkıda bulunulan bir kullanıcıya ve özniteliklere bakarsanız, kurallar aşağıdaki sırayla uygulanır:

| Ad | Yorum |
|:--- |:--- |
| AD 'den içinde – kullanıcı katılımı |Bağlayıcı alanı nesnelerini metaverse ile birleştirme kuralı. |
| AD 'den-kullanıcıhesabı etkin |Azure AD ve Office 365 için oturum açma için gereken öznitelikler. Etkin hesaptan bu özniteliklerin olmasını istiyoruz. |
| AD 'den, Exchange 'den ortak Kullanıcı |Genel adres listesinde bulunan öznitelikler. Veri kalitesinin, kullanıcının posta kutusunu bulduğu ormanda en iyi şekilde olduğunu varsayıyoruz. |
| AD 'den içinde – Kullanıcı ortak |Genel adres listesinde bulunan öznitelikler. Bir posta kutusu bulmadığımızda, diğer tüm birleştirilmiş nesneler öznitelik değerine katkıda bulunabilir. |
| AD 'den içinde – Kullanıcı değişimi |Yalnızca Exchange algılanırsa mevcuttur. Tüm altyapı değişim özniteliklerini akar. |
| AD 'den içinde – kullanıcı Lync |Yalnızca Lync algılanırsa vardır. Tüm altyapı Lync özniteliklerini akar. |

## <a name="next-steps"></a>Sonraki adımlar
* [Bildirim temelli sağlamayı anlamak](concept-azure-ad-connect-sync-declarative-provisioning.md)için yapılandırma modeli hakkında daha fazla bilgi edinin.
* [Bildirim temelli sağlama Ifadelerini anlama](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)bölümünde ifade dili hakkında daha fazla bilgi edinin.
* Kullanıma hazır yapılandırmanın [Kullanıcı ve kişileri anlama](concept-azure-ad-connect-sync-user-and-contacts.md) bölümünde nasıl çalıştığını okumaya devam edin
* [Varsayılan yapılandırmada değişiklik yapma konusunda](how-to-connect-sync-change-the-configuration.md)bildirim temelli sağlama kullanarak pratik bir değişiklik yapma konusuna bakın.

**Genel Bakış konuları**

* [Azure AD Connect eşitlemesi: Eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)

