---
title: Azure MFA sunucusu ve Active Directory Azure Active Directory
description: Dizinleri eşitleyebilmek için Azure Multi-Factor Authentication Server ile Active Directory’yi tümleştirme.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b3778ea68edf1fbbb41efb899749e6f35b39bae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742298"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Azure MFA Sunucusu ile Active Directory arasında dizin tümleştirme

Active Directory veya başka bir LDAP dizini ile tümleştirmek için Azure MFA Sunucusu’nun Dizin Tümleştirme bölümünü kullanın. Öznitelikleri dizin şeması ile eşleşecek şekilde yapılandırabilir ve kullanıcıların otomatik eşitlemesini ayarlayabilirsiniz.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sağlamamaktadır. Oturum açma olayları sırasında çok faktörlü kimlik doğrulaması (MFA) gerektirmek isteyen yeni müşteriler, bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır.
>
> Bulut tabanlı MFA 'yı kullanmaya başlamak için bkz. [öğretici: Azure AD Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).
>
> MFA sunucusunu 1 Temmuz 2019 tarihinden önce etkinleştiren mevcut müşteriler, en son sürümü, gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

## <a name="settings"></a>Ayarlar

Varsayılan olarak, Azure Multi-Factor Authentication (MFA) Sunucusu kullanıcıları Active Directory'den içeri aktaracak ya da eşitleyecek şekilde yapılandırılır.  Dizin Tümleştirme sekmesi, varsayılan davranışın üzerine yazmanızı ve farklı bir LDAP dizini, ADAM dizini ya da belirli bir Active Directory etki alanı denetçisine bağlamanızı sağlar.  Ayrıca, LDAP Kimlik Doğrulaması için kullanmak üzere RADIUS hedefi olarak LDAP ya da LDAP Bağlama sunma, IIS Kimlik Doğrulaması için önceden kimlik doğrulaması ya da Kullanıcı Portalı için birincil kimlik doğrulaması sağlar.  Aşağıdaki tabloda tek tek ayarlar açıklanır.

![MFA sunucusu 'nda LDAP yapılandırmasını düzenle](./media/howto-mfaserver-dir-ad/dirint.png)

> [!NOTE]
> Dizin tümleştirmesinin Active Directory Domain Services dışındaki dizinlerde çalışması garanti edilmez.

| Özellik | Açıklama |
| --- | --- |
| Active Directory Kullan |İçeri aktarma ve eşitleme için Active Directory’yi kullanmak üzere Active Directory Kullan seçeneğini seçin.  Bu varsayılan ayardır. <br>Not: Active Directory tümleştirmesinin düzgün çalışması için bilgisayarı bir etki alanına katın ve etki alanı hesabıyla oturum açın. |
| Güvenilen etki alanlarını dahil et |Aracının geçerli etki alanı tarafından güvenilen etki alanlarına, ormandaki başka bir etki alanına ya da orman güvenine dahil olan etki alanlarına bağlanmaya çalışmasını sağlamak için **Güvenilen etki alanlarını dahil et**’i işaretleyin.  Kullanıcıları güvenilen etki alanlarından içeri aktarmaz ya da eşitlemezken, performansını iyileştirmek için onay kutusunun işaretini kaldırın.  Varsayılan olarak işaretlidir. |
| Özel LDAP yapılandırması kullan |İçeri aktarma ve eşitleme için belirtilen LDAP ayarlarını kullanmak üzere LDAP Kullan seçeneğini seçin. Not: LDAP Kullan seçildiğinde, kullanıcı arabirimi başvuruları Active Directory'den iken LDAP olarak değiştirir. |
| Düzenle düğmesi |Düzenle düğmesi geçerli LDAP yapılandırması ayarlarının değiştirilmesini sağlar. |
| Öznitelik kapsam sorgularını kullan |Öznitelik kapsam sorgularının kullanılıp kullanılmaması gerektiğini gösterir.  Öznitelik kapsam sorguları, kayıtları başka bir kaydın özniteliğindeki girişlere göre niteleyerek verimli dizin aramaları yapmayı sağlar.  Azure Multi-Factor Authentication Sunucusu bir güvenlik grubunun üyesi olan kullanıcıları verimli şekilde sorgulamak için öznitelik kapsam sorgularını kullanır.   <br>Not: Öznitelik kapsam sorgularının desteklendiği bazı durumlar vardır, ancak kullanılmamalıdır.  Örneğin, Active Directory bir güvenlik grubu birden fazla etki alanı üyesini içerdiğinde, öznitelik kapsam sorgularıyla sorun yaşayabilir. Bu durumda, onay kutusunun seçimini kaldırın. |

LDAP yapılandırması ayarları aşağıdaki tabloda açıklanmaktadır.

| Özellik | Açıklama |
| --- | --- |
| Sunucu |LDAP dizinini çalıştıran sunucunun ana bilgisayar adını veya IP adresini girin.  Noktalı virgülle ayrılarak bir yedek sunucu de belirtilebilir. <br>Note: bağlama türü SSL (TLS) olduğunda, tam olarak nitelendirilmiş bir konak adı gereklidir. |
| Temel DN |Tüm dizin sorgularının başlatıldığı temel dizin nesnesinin ayırt edici adını girin.  Örneğin, dc=abc,dc=com. |
| Bağlama türü - Sorgular |LDAP dizinini aramak için bağlanırken kullanmak üzere uygun bağlama türünü seçin.  Bu, içeri aktarımlar, eşitleme ve kullanıcı adı çözümleme için kullanılır. <br><br>  Anonim - Anonim bir bağlama gerçekleştirilir.  Bağlama DN’si ve Bağlama Parolası kullanılmaz.  Bu yalnızca, LDAP dizini anonim bağlamaya izin verirse ve izinler uygun kayıtların ve özniteliklerin sorgulanmasına izin verirse çalışır.  <br><br> Basit - Bağlama DN’si ve Bağlama Parolası LDAP dizinine bağlanmak için düz metin olarak geçirilir.  Bu metot, yalnızca sunucuya erişilebildiğini ve bağlama hesabının uygun erişime sahip olduğunu doğrulamayı hedefleyen testlere yöneliktir. Uygun sertifika yüklendikten sonra bunun yerine SSL’yi kullanın.  <br><br> SSL - Bağlama DN’si ve Bağlama Parolası LDAP dizinine bağlanmak için SSL kullanılarak şifrelenir.  Yerel olarak LDAP dizininin güvendiği bir sertifika yükleyin.  <br><br> Windows - Bir Active Directory etki alanı denetleyicisine veya ADAM dizinine güvenli bir şekilde bağlanmak için Bağlama Kullanıcı Adı ve Bağlama Parolası kullanılır.  Bağlama Kullanıcı Adı boş bırakılırsa, bağlama için oturum açmış kullanıcının hesabı kullanılır. |
| Bağlama türü - Kimlik doğrulamaları |LDAP bağlama kimlik doğrulaması gerçekleştirirken kullanmak üzere uygun bağlama türünü seçin.  Bağlama türü altındaki bağlama türü açıklamalarına bakın - Sorgular  Örneğin, bu SSL bağlama LDAP bağlama kimlik doğrulamaları için kullanılırken, sorgular için Anonim bağlama kullanılmasını sağlar. |
| Bağlama DN’si veya Bağlama kullanıcı adı |LDAP dizinine bağlanırken kullanmak üzere hesabın kullanıcı kaydı ayırt edici adını girin.<br><br>Bağlama ayırt edici adı yalnızca Bağlama Türü Basit ya da SSL olduğunda kullanılır.  <br><br>Bağlama Türü Windows olduğunda, LDAP dizinine bağlanırken kullanmak üzere Windows hesabı kullanıcı adını girin.  Boş bırakılırsa, bağlama için oturum açmış kullanıcının hesabı kullanılır. |
| Bağlama Parolası |LDAP dizinine bağlanmak için kullanılan Bind DN’si ya da kullanıcı adı için bağlama parolasını girin.  Multi-Factor Auth Sunucusu AdSync Hizmeti’nin parolasını yapılandırmak için eşitlemeyi etkinleştirin ve hizmetin yerel makinede çalışır durumda olduğundan emin olun.  Parola, Multi-Factor Auth Sunucusu AdSync Hizmeti’nin çalıştığı hesabın altında Windows’da Depolanan Kullanıcı Adları ve Parolalar’a kaydedilir.  Parola ayrıca, Multi-Factor Auth Sunucusu kullanıcı arabiriminin çalıştığı hesap altına ve Multi-Factor Auth Sunucusu Hizmeti’nin çalıştığı hesap altına da kaydedilir.  <br><br>Parola yalnızca yerel sunucunun Windows’da Depolanan Kullanıcı Adları ve Parolalar bölümünde depolandığından, parolaya erişmesi gereken her Multi-Factor Auth Sunucusu için bu adımı tekrarlayın. |
| Sorgu boyutu sınırı |Bir dizin araması tarafından döndürülen en fazla kullanıcı sayısı için boyut sınırını belirtin.  Bu sınır LDAP dizinindeki yapılandırmayla eşleşmelidir.  Disk belleğinin desteklenmediği büyük aramalar için, içeri aktarma ve eşitleme işlemleri kullanıcıları toplu olarak almayı dener.  Burada belirtilen boyut sınırı LDAP dizininde yapılandırılan sınırdan daha büyükse, bazı kullanıcılar eksik olabilir. |
| Test düğmesi |LDAP sunucusuna bağlamayı test etmek için **Test et** düğmesine tıklayın.  <br><br>Bağlamayı test etmek için **LDAP kullan** seçeneğini belirlemeniz gerekmez. Bu, LDAP yapılandırması kullanılmadan önce bağlamanın test edilmesini sağlar. |

## <a name="filters"></a>Filtreler

Filtreler, dizin araması yaparken kayıtları nitelemek üzere ölçüt belirlemenizi sağlar.  Filtreyi ayarlayarak eşitlemek istediğiniz nesnelerin kapsamını belirleyebilirsiniz.  

![MFA sunucusunda Dizin filtrelemeyi yapılandırma](./media/howto-mfaserver-dir-ad/dirint2.png)

Azure Multi-Factor Authentication aşağıdaki üç filtreleme seçeneğine sahiptir:

* **Kapsayıcı filtresi** - Dizin araması yaparken kapsayıcı kayıtlarını nitelemek için kullanılan filtre ölçütlerini belirtin.  Active Directory ve ADAM için yaygın olarak (|(objectClass=organizationalUnit)(objectClass=container)) kullanılır.  Diğer LDAP dizinleri için, dizin şemasına bağlı olarak her kapsayıcı nesnesi türünü niteleyen filtre ölçütlerini kullanın.  <br>Not: Boş bırakılırsa varsayılan olarak ((objectClass=organizationalUnit)(objectClass=container)) kullanılır.
* **Güvenlik grubu filtresi** - Dizin araması yaparken güvenlik grubu kayıtlarını nitelemek için kullanılan filtre ölçütlerini belirtin.  Active Directory ve ADAM için yaygın olarak (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) kullanılır.  Diğer LDAP dizinleri için, dizin şemasına bağlı olarak her güvenlik grubu nesnesi türünü niteleyen filtre ölçütlerini kullanın.  <br>Not: Boş bırakılırsa varsayılan olarak (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) kullanılır.
* **Kullanıcı filtresi** - Dizin araması yaparken kullanıcı kayıtlarını nitelemek için kullanılan filtre ölçütlerini belirtin.  Active Directory ve ADAM için yaygın olarak (&(objectClass=user)(objectCategory=person)) kullanılır.  Diğer LDAP dizinleri için, dizin şemasına bağlı olarak (objectClass=inetOrgPerson) ya da benzerini kullanın. <br>Not: Boş bırakılırsa varsayılan olarak (&(objectCategory=person)(objectClass=user)) kullanılır.

## <a name="attributes"></a>Öznitelikler

Belirli bir dizinin özniteliklerini gerektiği şekilde özelleştirebilirsiniz.  Bu, özel öznitelikler eklemenizi ve eşitlemeyi yalnızca size gereken öznitelikleri kapsayacak şekilde ayarlamanızı sağlar. Özniteliğin adını, her öznitelik alanının değeri için dizin şemasında tanımlanan şekilde kullanın. Aşağıdaki tabloda her özellikle ilgili ek bilgiler sağlanmıştır.

Öznitelikler el ile girilebilir ve öznitelik listesindeki bir öznitelikle eşleşmesi gerekmez.

![MFA sunucusu 'nda Dizin tümleştirme özniteliklerini özelleştirme](./media/howto-mfaserver-dir-ad/dirint3.png)

| Özellik | Açıklama |
| --- | --- |
| Benzersiz tanımlayıcı |Kapsayıcı, güvenlik grubu ve kullanıcı kayıtlarının benzersiz tanımlayıcısı olarak hizmet eden özniteliğin öznitelik adını girin.  Active Directory'de, bu genellikle objectGUID’dir. Diğer LDAP uygulamaları entryUUID veya benzerini kullanabilir.  ObjectGUID varsayılandır. |
| Benzersiz tanımlayıcı türü |Benzersiz tanımlayıcı özniteliği türünü seçin.  Active Directory'de objectGUID özniteliğinin türü GUID’dir. Diğer LDAP uygulamaları ASCII Bayt Dizisi ya da Dize türü kullanabilir.  GUID varsayılandır. <br><br>Eşitleme Öğeleri’ne Benzersiz Tanımlayıcısı ile başvurulduğundan, bu türü doğru ayarlamanız önemlidir. Nesnenin dizinde doğrudan bulunabilmesi için Benzersiz Tanımlayıcı Türü kullanılır.  Dizin değeri aslında ASCII bayt dizisi olarak depolanıyorsa bu türün Dize olarak ayarlanması eşitlemenin düzgün çalışmasını engeller. |
| Ayırt edici ad |Her bir kaydın ayırt edici adını içeren özniteliğin öznitelik adını girin.  Active Directory'de, bu genellikle distinguishedName’dir. Diğer LDAP uygulamaları entryDN veya benzerini kullanabilir.  distinguishedName varsayılandır. <br><br>Yalnızca ayırt edici adı içeren bir öznitelik yoksa, ads path özniteliği kullanılabilir.  Yolun "LDAP:// \<server\> /" kısmı, nesnenin yalnızca ayırt edici adı bırakılarak otomatik olarak çıkarılır. |
| Kapsayıcı adı |Kapsayıcı kaydındaki adı içeren özniteliğin öznitelik adını girin.  Bu özniteliğin değeri, Active Directory’den içeri aktarılırken ya da eşitleme öğeleri eklenirken Kapsayıcı Hiyerarşisi’nde görüntülenir.  Varsayılan addır. <br><br>Farklı kapsayıcılar kendi adları için farklı öznitelikler kullanıyorsa, birden çok kapsayıcı adı özniteliğini ayırmak için noktalı virgül kullanın.  Bir kapsayıcı nesnesinde bulunan ilk kapsayıcı adı özniteliği, nesnenin adını görüntülemek için kullanılır. |
| Güvenlik grubu adı |Güvenlik grubu kaydındaki adı içeren özniteliğin öznitelik adını girin.  Bu özniteliğin değeri, Active Directory’den içeri aktarılırken ya da eşitleme öğeleri eklenirken Güvenlik Grubu listesinde görüntülenir.  Varsayılan addır. |
| Kullanıcı adı |Kullanıcı kaydındaki kullanıcı adını içeren özniteliğin öznitelik adını girin.  Bu özniteliğin değeri Multi-Factor Auth Sunucusu kullanıcı adı olarak kullanılır.  Birinciye yedek olarak ikinci bir öznitelik belirtilebilir.  İkinci öznitelik, yalnızca ilk öznitelik kullanıcı için bir değer içermiyorsa kullanılır.  userPrincipalName ve sAMAccountName varsayılanlardır. |
| Ad |Kullanıcı kaydındaki adı içeren özniteliğin öznitelik adını girin.  givenName varsayılandır. |
| Soyadı |Kullanıcı kaydındaki soyadını içeren özniteliğin öznitelik adını girin.  sn varsayılandır. |
| E-posta adresi |Kullanıcı kaydındaki e-posta adresini içeren özniteliğin öznitelik adını girin.  E-posta adresi kullanıcıya karşılama ve güncelleştirme e-postaları göndermek için kullanılır.  mail varsayılandır. |
| Kullanıcı grubu |Kullanıcı kaydındaki kullanıcı grubunu içeren özniteliğin öznitelik adını girin.  Kullanıcı grubu, aracıda ve Multi-Factor Auth Sunucusu Yönetim Portalı’ndaki raporlarında bulunan kullanıcıları filtrelemek için kullanılabilir. |
| Description |Kullanıcı kaydındaki açıklamayı içeren özniteliğin öznitelik adını girin.  Açıklama yalnızca arama için kullanılır.  description varsayılandır. |
| Telefon araması dili |Kullanıcıya yönelik sesli aramalar için kullanılacak dilin kısa adını içeren özniteliğin öznitelik adını girin. |
| SMS dili |Kullanıcıya yönelik SMS iletileri için kullanılacak dilin kısa adını içeren özniteliğin öznitelik adını girin. |
| Mobil uygulama dili |Kullanıcıya yönelik telefon uygulama metin iletileri için kullanılacak dilin kısa adını içeren özniteliğin öznitelik adını girin. |
| OATH belirteci dili |Kullanıcıya yönelik OATH belirteci metin iletileri için kullanılacak dilin kısa adını içeren özniteliğin öznitelik adını girin. |
| İş telefonu |Kullanıcı kaydındaki iş telefonu numarasını içeren özniteliğin öznitelik adını girin.  telephoneNumber varsayılandır. |
| Ev telefonu |Kullanıcı kaydındaki ev telefonu numarasını içeren özniteliğin öznitelik adını girin.  homePhone varsayılandır. |
| Çağrı cihazı |Kullanıcı kaydındaki çağrı cihazı numarasını içeren özniteliğin öznitelik adını girin.  pager varsayılandır. |
| Cep telefonu |Kullanıcı kaydındaki cep telefonu numarasını içeren özniteliğin öznitelik adını girin.  mobile varsayılandır. |
| Faks |Kullanıcı kaydındaki faks numarasını içeren özniteliğin öznitelik adını girin.  facsimileTelephoneNumber varsayılandır. |
| IP telefonu |Kullanıcı kaydındaki IP telefonu numarasını içeren özniteliğin öznitelik adını girin.  ipPhone varsayılandır. |
| Özel |Kullanıcı kaydında özel bir telefon numarası içeren özniteliğin öznitelik adını girin.  Varsayılan boştur. |
| Dahili numara |Kullanıcı kaydındaki telefon numarası dahili numarasını içeren özniteliğin öznitelik adını girin.  Dahili numara alanının değeri yalnızca birincil telefon numarası için dahili numara olarak kullanılır.  Varsayılan boştur. <br><br>Dahili numara özniteliği belirtilmezse, dahili numaralar telefon özniteliğinin parçası olarak eklenebilir. Bu durumda, uzantının düzgün ayrıştırılabilmesi için önüne 'x' ekleyin.  Örneğin, 555-123-4567 x890, telefon numarası olarak 555-123-4567 ve dahili numara olarak 890’ı ifade eder. |
| Varsayılanları Geri Yükle düğmesi |Tüm öznitelikleri varsayılan değerlerine geri döndürmek için **Varsayılanları Geri Yükle**’ye tıklayın.  Varsayılanlar normal Active Directory ya da ADAM şemasıyla düzgün çalışmalıdır. |

Öznitelikleri düzenlemek için, öznitelikler sekmesinde **Düzenle** ' ye tıklayın.  Bu, öznitelikleri düzenleyebileceğiniz bir pencere getirir. Herhangi bir özniteliğin yanındaki **...** simgesini seçerek hangi özelliklerin görüntüleneceğini seçebileceğiniz pencereyi açın.

![MFA sunucusunda Dizin öznitelik eşlemesini Düzenle](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Eşitleme

Eşitleme, Azure MFA kullanıcı veritabanını Active Directory ya da başka bir Basit Dizin Erişimi Protokolü (LDAP) dizinindeki kullanıcılarla eşitlenmiş halde tutar. İşlem kullanıcıları el ile Active Directory'den içeri aktarmaya benzer, ancak işlemek üzere Active Directory kullanıcısı ve güvenlik grubu değişikliklerini düzenli aralıklarla yoklar.  Ayrıca, bir kapsayıcıdan, güvenlik grubundan ya da Active Directory’den kaldırılan kullanıcıları devre dışı bırakır veya kaldırır.

Multi-Factor Auth ADSync hizmeti, Active Directory’nin düzenli yoklamasını gerçekleştiren bir Windows hizmetidir.  Bu, Azure AD Sync ya da Azure AD Connect ile karıştırılmamalıdır.  Multi-Factor Auth ADSync, aynı kod tabanıyla oluşturulmakla birlikte, Azure Multi-Factor Authentication Sunucusu’na özeldir.  Bu, Durduruldu durumunda iken yüklenir ve çalışmak üzere yapılandırıldığında Multi-Factor Auth Sunucusu hizmeti tarafından başlatılır.  Çok sunuculu Multi-Factor Auth Sunucusu yapılandırmanız varsa, Multi-Factor Auth ADSync yalnızca tek bir sunucuda çalışabilir.

Multi-Factor Auth ADSync hizmeti, değişiklikleri verimli şekilde yoklamak üzere Microsoft tarafından sağlanan DirSync LDAP sunucu eklentisi kullanır.  Bu DirSync denetimi çağıranı, "directory get changes" hakkına ve DS-Replication-Get-Changes genişletilmiş denetim erişimi hakkına sahip olmalıdır.  Varsayılan olarak, bu haklar etki alanı denetleyicilerindeki Yönetici ve LocalSystem hesaplarına atanır.  Multi-Factor Auth AdSync hizmeti varsayılan olarak LocalSystem olarak çalışmak üzere yapılandırılmıştır.  Bu nedenle en basiti hizmeti bir etki alanı denetleyicisinde çalıştırmaktır.  Hizmeti her aman tam eşitleme gerçekleştirecek şekilde yapılandırırsanız hizmet daha düşük izinlere sahip bir hesap olarak çalışabilir.  Bu daha az verimlidir, ancak daha az hesap ayrıcalığı gerektirir.

LDAP dizini DirSync’i destekliyorsa ve DirSync kullanacak şekilde yapılandırılmışsa, kullanıcı ve güvenlik grubu değişiklikleri için yoklama işlemi Active Directory’dekiyle aynı şekilde işler.  LDAP dizini DirSync denetimini desteklemiyorsa, her döngü sırasında tam eşitleme gerçekleştirilir.

![Dizin nesnelerinin MFA sunucusuna eşitlenmesi](./media/howto-mfaserver-dir-ad/dirint5.png)

Aşağıdaki tablo, Eşitleme sekmesi ayarlarının her biriyle ilgili daha fazla bilgi içerir.

| Özellik | Açıklama |
| --- | --- |
| Active Directory ile eşitlemeyi etkinleştir |İşaretlenirse Multi-Factor Auth Sunucusu hizmeti düzenli olarak Active Directory değişikliklerini yoklar. <br><br>Not: Multi-Factor Auth Sunucusu hizmetinin değişiklikleri işlemeye başlaması için, en az bir eşitleme öğesinin eklenmesi ve Şimdi Eşitle işlemi gerçekleştirilmesi gerekir. |
| Şu aralıkta eşitle |Multi-Factor Auth Sunucusu hizmetinin değişiklikleri yoklama ile işleme arasında bekleyeceği zaman aralığı. <br><br> Not: Belirtilen aralık her döngünün başlangıcı arasındaki süredir.  Değişiklikleri işleme süresi aralığı aşarsa, hizmet hemen tekrar yoklama yapar. |
| Artık Active Directory'de olmayan kullanıcıları kaldır |İşaretlendiğinde, Multi-Factor Auth Sunucusu hizmeti Active Directory silinen kullanıcı küçük görüntülerini işler ve ilgili Multi-Factor Auth Sunucusu kullanıcısını kaldırır. |
| Her zaman tam eşitleme gerçekleştir |İşaretlendiğinde, Multi-Factor Auth Sunucusu hizmeti her zaman tam eşitleme gerçekleştirir.  İşaretlenmediğinde, Multi-Factor Auth Sunucusu hizmeti yalnızca değiştirilmiş kullanıcılar sorgulayarak artımlı eşitleme gerçekleştirir.  Varsayılan olarak işaretli değildir. <br><br>İşaretlenmezse Azure MFA tarafından yalnızca dizin DirSync denetimini destekliyorsa ve dizine yönelik hesap bağlaması DirSync artımlı sorgularını gerçekleştirme izinlerine sahipse artımlı eşitleme gerçekleştirilebilir.  Hesap uygun izinlere sahip değilse veya eşitlemeye birden çok etki alanı dahilse Azure MFA Sunucusu tam eşitleme gerçekleştirir. |
| X değerinden fazla kullanıcı devre dışı bırakıldığında ya da kaldırıldığında yönetici onayı iste |Eşitleme öğeleri, artık öğenin kapsayıcısı ya da güvenlik grubunun artık üyesi olmayan kullanıcıları devre dışı bırakacak veya kaldıracak şekilde yapılandırılabilir.  Önlem olarak, devre dışı bırakılacak veya silinecek kullanıcı sayısı eşiği aştığında, yönetici onayı gerekli olabilir.  İşaretlenirse belirtilen eşik için onay gerekir.  Varsayılan değer 5’tir ve aralık 1 - 999’dur. <br><br> Onay, ilk olarak yöneticilere bir e-posta bildirimi gönderilmesiyle sağlanır. E-posta bildirimi, kullanıcıları devre dışı bırakma ve kaldırmayı gözden geçirme ve onaylama için yönergeler sağlar.  Multi-Factor Auth Sunucusu kullanıcı arabirimi başlatıldığında, onay ister. |

**Şimdi Eşitle** düğmesi belirtilen eşitleme öğeleri için tam eşitleme çalıştırmanıza olanak sağlar.  Eşitleme öğeleri eklendiğinde, değiştirildiğinde, kaldırıldığında ve yeniden sıralandığında her seferde tam eşitleme gereklidir.  Ayrıca, artımlı değişiklikler için hizmetin yoklama yapacağı başlangıç noktasını ayarladığından, Multi-Factor Auth AdSync hizmeti çalışır duruma geçmeden önce de gereklidir.  Eşitleme öğelerinde değişiklik yapılmasına rağmen tam eşitleme gerçekleştirilmediyse Şimdi Eşitle seçeneği sunulur.

**Kaldır** düğmesi yöneticinin bir ya da daha fazla eşitleme öğesini Multi-Factor Auth Sunucusu eşitleme öğesi listesinden silmesini sağlar.

> [!WARNING]
> Eşitleme öğesi kaydı kaldırıldıktan sonra kurtarılamaz. Eşitleme öğesi kaydını yanlışlıkla sildiyseniz yeniden eklemeniz gerekir.

Eşitleme öğesi veya eşitleme öğeleri Multi-Factor Auth Sunucusu’ndan kaldırıldı.  Multi-Factor Auth Sunucusu artık eşitleme öğelerini işlemeyecek.

Yukarı Taşı ve Aşağı Taşı düğmeleri yöneticinin eşitleme öğelerinin sırasını değiştirmesini sağlar.  Aynı kullanıcı birden fazla eşitleme öğesinin üyesi olabileceğinden (örneğin, kapsayıcı ve güvenlik grubu), sıra önemlidir.  Kullanıcıya eşitleme sırasında uygulanan ayarlar, kullanıcının ilişkilendirildiği, listedeki ilk eşitleme öğesinden gelir.  Bu nedenle, eşitleme öğeleri öncelik sırasına koyulmalıdır.

> [!TIP]
> Eşitleme öğeleri kaldırıldıktan sonra bir tam eşitleme gerçekleştirilmelidir.  Eşitleme öğeleri sıralandıktan sonra bir tam eşitleme gerçekleştirilmelidir.  Tam eşitleme gerçekleştirmek için **Şimdi Eşitle**’ye tıklayın.

## <a name="multi-factor-authentication-servers"></a>Multi-Factor Authentication sunucuları

Ek Multi-Factor Authentication sunucular yedek RADIUS proxy, LDAP proxy veya IIS kimlik doğrulaması olarak kullanılmak üzere ayarlanabilir. Eşitleme yapılandırması tüm aracılar arasında paylaşılır. Ancak, bu aracılardan yalnızca birinde Multi-Factor Authentication sunucusu hizmeti çalışıyor olabilir. Bu sekme, eşitleme için etkinleştirilmesi gereken Multi-Factor Authentication sunucuyu seçmenizi sağlar.

![İlgili Multi-Factor Authentication sunucuları](./media/howto-mfaserver-dir-ad/dirint6.png)
