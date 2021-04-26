---
title: Güvenler Azure AD Domain Services için nasıl çalışır | Microsoft Docs
description: Orman güveninin nasıl çalıştığı hakkında daha fazla bilgi edinin Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 5c72ab7d085de558ee95f3c602ccc6be6160b322
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620214"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Güven ilişkilerinin Azure Active Directory Domain Services içindeki kaynak ormanları için nasıl çalıştığı

Active Directory Domain Services (AD DS), etki alanı ve orman güveni ilişkileri aracılığıyla birden çok etki alanı veya orman arasında güvenlik sağlar. Güvenlerde kimlik doğrulamanın gerçekleşebilmesi için önce Windows 'un, bir Kullanıcı, bilgisayar veya hizmet tarafından istenen etki alanının, istekte bulunan hesabın etki alanı ile bir güven ilişkisine sahip olup olmadığını denetlemesi gerekir.

Bu güven ilişkisini denetlemek için Windows güvenlik sistemi, isteği alan sunucunun etki alanı denetleyicisi (DC) ve istekte bulunan hesabın etki alanındaki bir DC arasında bir güven yolu hesaplar.

AD DS tarafından sunulan erişim denetim mekanizmaları ve Windows dağıtılmış güvenlik modeli, etki alanı ve orman güvenleri 'nin çalışması için bir ortam sağlar. Bu güvenin düzgün çalışması için, her kaynak veya bilgisayarın, bulunduğu etki alanındaki bir DC 'ye yönelik doğrudan güven yoluna sahip olması gerekir.

Güven yolu, güvenilen etki alanı yetkilisine kimliği doğrulanmış bir uzak yordam çağrısı (RPC) bağlantısı kullanılarak net oturum açma hizmeti tarafından uygulanır. Güvenli kanal, etki alanları arası güven ilişkileri aracılığıyla diğer AD DS etki alanlarına da genişletilir. Bu güvenli kanal, kullanıcılar ve gruplar için güvenlik tanımlayıcıları (SID 'Ler) dahil olmak üzere güvenlik bilgilerini almak ve doğrulamak için kullanılır.

Güvenin Azure AD DS için nasıl uygulandıkları hakkında genel bir bakış için bkz. [kaynak ormanı kavramları ve özellikleri][create-forest-trust].

Azure AD DS 'de güvenleri kullanmaya başlamak için, [orman güvenlerini kullanan bir yönetilen etki alanı oluşturun][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Güven ilişkisi akışları

Güven üzerinden güvenli iletişimin akışı, bir güvenin esneklik düzeyini belirler. Güven oluşturma veya yapılandırma, iletişimin ne kadar orman içinde veya ormanlar arasında genişletiğini belirler.

Güven üzerinden iletişimin akışı güvenin yönlerine göre belirlenir. Güvenler tek yönlü veya iki yönlü olabilir ve geçişli ya da geçişli olabilir.

Aşağıdaki diyagramda, *ağaç 1* ve *ağaç 2* ' deki tüm etki alanlarının varsayılan olarak geçişli güven ilişkilerine sahip olduğu gösterilmektedir. Sonuç olarak, *ağaç 1* ' deki kullanıcılar, *ağaç 2* ' deki etki alanlarındaki kaynaklara erişebilir ve kaynak üzerinde uygun izinler atandığında ağaç *1* ' deki kullanıcılar, *ağaç 2*' deki kaynaklara erişebilir.

![İki orman arasındaki güven ilişkilerinin diyagramı](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Tek yönlü ve çift yönlü güvenler

Güven ilişkileri, kaynaklara erişimin tek yönlü veya iki yönlü olmasını sağlar.

Tek yönlü güven, iki etki alanı arasında oluşturulan tek yönlü kimlik doğrulama yoludur. Etki alanı *a* ve *etki alanı b* arasındaki tek yönlü güvende, *a etki* alanındaki kullanıcılar *etki alanı b*'deki kaynaklara erişebilir. Ancak, *etki alanı B* 'Deki kullanıcılar *A etki alanındaki* kaynaklara erişemez.

Bazı tek yönlü güvenler, oluşturulmakta olan güvenin türüne bağlı olarak geçişli ya da geçişli olabilir.

İki yönlü güvende *etki alanı bir* güven *etki alanı b* ve etki alanı *b* etki *alanı a*'ya güvenir. Bu yapılandırma, her iki yönde de iki etki alanı arasında kimlik doğrulama isteklerinin geçirilebileceği anlamına gelir. Bazı iki yönlü ilişkiler, oluşturulmakta olan güvenin türüne bağlı olarak geçişli veya geçişli olabilir.

Bir AD DS ormanındaki tüm etki alanı güvenleri, iki yönlü, geçişli güvenlerdir. Yeni bir alt etki alanı oluşturulduğunda, yeni alt etki alanı ve üst etki alanı arasında iki yönlü, geçişli bir güven otomatik olarak oluşturulur.

### <a name="transitive-and-non-transitive-trusts"></a>Geçişli ve geçişli olmayan güvenler

Geçişlilik, bir güvenin oluşturulduğu iki etki alanının dışında uzatılıp uzatılamayabileceğini belirler.

* Karşılıklı güven, diğer etki alanlarıyla güven ilişkilerini genişletmek için kullanılabilir.
* Geçişli olmayan bir güven, diğer etki alanlarıyla güven ilişkilerini reddetmek için kullanılabilir.

Bir ormanda yeni bir etki alanı oluşturduğunuzda, yeni etki alanı ve onun üst etki alanı arasında iki yönlü, geçişli bir güven ilişkisi otomatik olarak oluşturulur. Yeni etki alanına alt etki alanları eklenirse, güven yolu, yeni etki alanı ve onun üst etki alanı arasında oluşturulan ilk güven yolunu genişleterek etki alanı hiyerarşisinde yukarı doğru akar. Geçişli güven ilişkileri, etki alanı ağacındaki tüm etki alanları arasında geçişli güvenler oluşturarak, bir etki alanı ağacı aracılığıyla yukarı doğru akar.

Kimlik doğrulama istekleri bu güven yollarını izler, bu nedenle ormandaki herhangi bir etki alanının hesaplarının kimliği, ormandaki başka bir etki alanı tarafından doğrulanabilir. Çoklu oturum açma işlemiyle, uygun izinlere sahip hesaplar ormandaki herhangi bir etki alanındaki kaynaklara erişebilir.

## <a name="forest-trusts"></a>Ormanlar güvenleri

Orman güvenleri, bölünmüş bir AD DS altyapıları yönetmenize ve birden çok orman genelinde kaynaklara ve diğer nesnelere erişimi desteklemeye yardımcı olur. Orman güvenleri, hizmet sağlayıcıları, birleşmeler veya alımlar, işbirliğine dayalı iş extranet 'ler ve yönetim bağımsız çalışma sınırı için bir çözüm arayan şirketler için faydalıdır.

Orman güvenlerini kullanarak, tek yönlü veya iki yönlü geçişli güven ilişkisi oluşturmak için iki farklı ormanı bağlayabilirsiniz. Orman güveni, yöneticilerin ormanlar genelinde sorunsuz bir kimlik doğrulama ve yetkilendirme deneyimi sağlaması için tek bir güven ilişkisine sahip iki AD DS ormanını bağlanmasına olanak sağlar.

Orman güveni, yalnızca bir ormandaki bir orman kök etki alanı ve başka bir ormandaki bir orman kök etki alanı arasında oluşturulabilir. Orman güvenleri yalnızca iki orman arasında oluşturulabilir ve örtülü olarak üçüncü bir ormana genişletilemez. Bu davranış, orman *1* ile *orman 2* arasında bir orman güveni oluşturulduysa ve *orman 2* ile *orman 3* arasında başka bir orman güveni oluşturulduysa, *Orman 1* ' in *orman 3* ile kapalı bir güveni yoktur anlamına gelir.

Aşağıdaki diyagramda tek bir kuruluşta üç AD DS ormanı arasında iki ayrı orman güven ilişkisi gösterilmektedir.

![Tek bir kuruluş içindeki orman güvenleri ilişkilerinin diyagramı](./media/concepts-forest-trust/forest-trusts-diagram.png)

Bu örnek yapılandırma aşağıdaki erişimi sağlar:

* *Orman 2* ' deki kullanıcılar, her *Orman 1* veya *orman 3* ' teki herhangi bir etki alanındaki kaynaklara erişebilir
* *Orman 3* ' teki kullanıcılar, *orman 2* ' deki herhangi bir etki alanındaki kaynaklara erişebilir
* *Orman 1* ' deki kullanıcılar, *orman 2* ' deki herhangi bir etki alanındaki kaynaklara erişebilir

Bu yapılandırma, *Orman 1* ' deki kullanıcıların, *orman 3* ' teki kaynaklara erişmesine izin vermez veya tam tersi olur. Hem *Orman 1* hem de *orman 3* ' teki kullanıcıların kaynakları paylaşmasına izin vermek için iki orman arasında iki yönlü geçişli güven oluşturulmalıdır.

İki orman arasında tek yönlü bir orman güveni oluşturulursa, güvenilen ormanın üyeleri güvenen ormanda bulunan kaynakları kullanabilir. Ancak, güven yalnızca bir yönde çalışır.

Örneğin, *Orman 1* (güvenilen orman) ve *orman 2* (güvenen orman) arasında tek yönlü bir orman güveni oluşturulduğunda:

* *Orman 1* üyeleri, *orman 2*' de bulunan kaynaklara erişebilir.
* *Orman 2* üyeleri aynı güveni kullanarak *Orman 1* ' de bulunan kaynaklara erişemez.

> [!IMPORTANT]
> Azure AD Domain Services kaynak ormanı yalnızca şirket içi Active Directory tek yönlü bir orman güvenini destekler.

### <a name="forest-trust-requirements"></a>Orman güveni gereksinimleri

Bir orman güveni oluşturabilmeniz için önce doğru etki alanı adı sistemi (DNS) altyapısının bulunduğunu doğrulamanız gerekir. Orman güvenleri yalnızca aşağıdaki DNS yapılandırmalarından biri kullanılabilir olduğunda oluşturulabilir:

* Tek bir kök DNS sunucusu, her iki orman DNS ad alanı için de kök DNS sunucusudur. kök bölge, DNS ad alanlarının her biri için temsilciler içerir ve tüm DNS sunucularının kök ipuçları kök DNS sunucusunu içerir.
* Paylaşılan bir kök DNS sunucusu olmadığında ve her orman DNS ad alanındaki kök DNS sunucuları, diğer ad alanındaki adlara yönelik sorguları yönlendirmek üzere her DNS ad alanı için DNS koşullu ileticileri kullanır.

    > [!IMPORTANT]
    > Azure AD Domain Services kaynak ormanı bu DNS yapılandırmasını kullanmalıdır. Kaynak ormanı DNS ad alanı dışında bir DNS ad alanı barındırması Azure AD Domain Services bir özellik değildir. Koşullu ileticiler doğru yapılandırmadır.

* Paylaşılan bir kök DNS sunucusu yoksa ve her orman DNS ad alanındaki kök DNS sunucuları, diğer ad alanındaki adlara yönelik sorguları yönlendirmek için her bir DNS ad alanında yapılandırılmış DNS ikincil bölgeleri yapılandırılır.

Orman güveni oluşturmak için, Active Directory etki alanı yöneticileri grubunun (orman kök etki alanında) veya Enterprise Admins grubunun bir üyesi olmanız gerekir. Her güvene, her iki ormandaki yöneticilerin bilmeleri gereken bir parola atanır. Her iki ormanda da Enterprise Admins üyeleri her iki ormanda da güvenleri oluşturabilir ve bu senaryoda, her iki orman için de şifreli olarak rastgele bir parola oluşturulur ve yazılır.

Azure AD Domain Services giden orman güveni Azure portal oluşturulur. Güveni, yönetilen etki alanının kendisiyle el ile oluşturmazsınız. Gelen orman güveninin, daha önce şirket içi Active Directory belirtilen ayrıcalıklara sahip bir kullanıcı tarafından yapılandırılması gerekir.

## <a name="trust-processes-and-interactions"></a>Güvenli süreçler ve etkileşimler

Birçok etki alanı ve ormanlar arası işlem, çeşitli görevleri tamamlamaya yönelik etki alanı veya orman güvenlerini temel alır. Bu bölümde, güvenler arasında kaynaklara erişildiği ve kimlik doğrulama başvurularına değerlendirilen süreçler ve etkileşimler açıklanmaktadır.

### <a name="overview-of-authentication-referral-processing"></a>Kimlik doğrulama başvurusu işlemeye genel bakış

Bir etki alanı kimlik doğrulaması için bir istek olduğunda, bu etki alanındaki etki alanı denetleyicisinin, isteğin geldiği etki alanı ile bir güven ilişkisinin olup olmadığını belirlemesi gerekir. Güvenin yönü ve güvenin geçişli ya da geçişsiz olması, etki alanındaki kaynaklara erişmek için kullanıcının kimlik doğrulamasından geçmeden önce de belirlenmesi gerekir. Güvenilen etki alanları arasında gerçekleşen kimlik doğrulama işlemi, kullanımdaki kimlik doğrulama protokolüne göre değişir. Kerberos V5 ve NTLM protokolleri, kimlik doğrulama için başvuruları farklı bir etki alanına göre işler

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5 başvuru işleme

Kerberos V5 kimlik doğrulama protokolü, istemci kimlik doğrulaması ve yetkilendirme bilgileri için etki alanı denetleyicilerindeki net oturum açma hizmetine bağımlıdır. Kerberos protokolü, oturum biletleri için bir çevrimiçi Anahtar Dağıtım Merkezi (KDC) ve Active Directory hesap deposuna bağlanır.

Kerberos protokolü Ayrıca, siteler arası Bilet sağlama hizmetleri (TGS) için güvenler kullanır ve güvenli bir kanalda ayrıcalık özniteliği sertifikalarını (PACs) doğrular. Kerberos protokolü, yalnızca bir MıT Kerberos bölgesi gibi Windows markalı olmayan işletim sistemi Kerberos alanları ile çapraz bölge kimlik doğrulaması gerçekleştirir ve Net Logon hizmetiyle etkileşime gerek kalmaz.

İstemci kimlik doğrulaması için Kerberos V5 kullanıyorsa, hedef etki alanındaki sunucuya, hesap etki alanındaki bir etki alanı denetleyicisinden bir bilet ister. Kerberos KDC, istemci ve sunucu arasında güvenilir bir aracı görevi görür ve iki tarafın birbirlerinin kimliğini doğrulamasını sağlayan bir oturum anahtarı sağlar. Hedef etki alanı geçerli etki alanından farklıysa, KDC bir kimlik doğrulama isteğinin başvuramayacağını anlamak için bir mantıksal işlem izler:

1. Geçerli etki alanı, istenen sunucunun etki alanı tarafından doğrudan güvenilir mi?
    * Yanıt Evet ise, istemciye istenen etki alanına bir başvuru gönderin.
    * Hayır ise, bir sonraki adıma gidin.

2. Geçerli etki alanı ile güven yolundaki bir sonraki etki alanı arasında geçişli bir güven ilişkisi var mı?
    * Yanıt Evet ise, istemciye güven yolundaki sonraki etki alanına bir başvuru gönderir.
    * Hayır ise, istemciye bir oturum açma reddedildi iletisi gönderin.

### <a name="ntlm-referral-processing"></a>NTLM başvuru işleme

NTLM kimlik doğrulama protokolü, istemci kimlik doğrulaması ve yetkilendirme bilgileri için etki alanı denetleyicilerindeki net oturum açma hizmetine bağımlıdır. Bu protokol, Kerberos kimlik doğrulaması kullanmayan istemcilerin kimliğini doğrular. NTLM, etki alanları arasında kimlik doğrulama isteklerini geçirmek için güvenleri kullanır.

İstemci kimlik doğrulaması için NTLM kullanıyorsa, kimlik doğrulaması için başlangıç isteği doğrudan istemciden hedef etki alanındaki kaynak sunucusuna gider. Bu sunucu, istemcinin yanıt verdiği bir zorluk oluşturur. Sunucu daha sonra kullanıcının bilgisayar hesabı etki alanındaki bir etki alanı denetleyicisine yanıtını gönderir. Bu etki alanı denetleyicisi, Kullanıcı hesabını güvenlik hesapları veritabanına karşı denetler.

Hesap veritabanında yoksa, etki alanı denetleyicisi doğrudan kimlik doğrulaması yapılıp yapılmayacağını, isteği iletmenizi veya aşağıdaki mantığı kullanarak isteği reddetmeyi belirler:

1. Geçerli etki alanı, kullanıcının etki alanıyla doğrudan güven ilişkisine sahip mi?
    * Yanıt Evet ise, etki alanı denetleyicisi istemcinin kimlik bilgilerini doğrudan kimlik doğrulama için kullanıcının etki alanındaki etki alanı denetleyicisine gönderir.
    * Hayır ise, bir sonraki adıma gidin.

2. Geçerli etki alanı, kullanıcının etki alanıyla geçişli bir güven ilişkisine sahip mi?
    * Yanıt Evet ise, kimlik doğrulama isteğini güven yolundaki bir sonraki etki alanına geçirin. Bu etki alanı denetleyicisi, kullanıcının kimlik bilgilerini kendi güvenlik hesapları veritabanına göre denetleyerek işlemi yineler.
    * Hayır ise, istemciyi bir oturum açma reddedildi iletisi gönderin.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Orman güvenleri üzerinden kimlik doğrulama isteklerinin Kerberos tabanlı işlenmesi

İki orman bir orman güveni tarafından bağlandığında, her iki ormandaki kaynaklara erişim sağlamak için, Kerberos V5 veya NTLM protokolleri kullanılarak yapılan kimlik doğrulama istekleri, ormanlar arasında yönlendirilebilir.

Bir orman güveni ilk kez oluşturulduğunda, her orman ortak ormanındaki tüm güvenilir ad alanlarını toplar ve bilgileri [Güvenilen bir etki alanı nesnesine](#trusted-domain-object)depolar. Güvenilen ad alanları, diğer ormanda kullanılan etki alanı ağacı adlarını, Kullanıcı asıl adı (UPN) soneklerini, hizmet asıl adı (SPN) soneklerini ve güvenlik KIMLIĞI (SID) ad alanlarını içerir. TDO nesneleri genel kataloğa çoğaltılır.

Kimlik doğrulama protokollerinin orman güven yolunu izleyebilmesi için, kaynak bilgisayarın hizmet asıl adı (SPN) diğer ormandaki bir konuma çözümlenmelidir. SPN aşağıdaki adlardan biri olabilir:

* Bir konağın DNS adı.
* Bir etki alanının DNS adı.
* Bir hizmet bağlantı noktası nesnesinin ayırt edici adı.

Bir ormandaki bir iş istasyonu, başka bir ormandaki bir kaynak bilgisayardaki verilere erişmeye çalıştığında, Kerberos kimlik doğrulama işlemi, kaynak bilgisayarın SPN 'sine bir hizmet bileti için etki alanı denetleyicisiyle iletişim kurar. Etki alanı denetleyicisi genel kataloğu sorgular ve SPN 'nin etki alanı denetleyicisiyle aynı ormanda olmadığını belirlerse, etki alanı denetleyicisi, ana etki alanı için iş istasyonuna geri bir başvuru gönderir. Bu noktada, iş istasyonu, hizmet bileti için üst etki alanını sorgular ve kaynağın bulunduğu etki alanına ulaşıncaya kadar referans zincirini izlemeye devam eder.

Aşağıdaki diyagram ve adımlar, Windows çalıştıran bilgisayarlar başka bir ormanda bulunan bir bilgisayardan kaynaklara erişmeyi denediklerinde kullanılan Kerberos kimlik doğrulama işleminin ayrıntılı bir açıklamasını sağlar.

![Bir orman güveni üzerinden Kerberos işleminin diyagramı](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. *Kullanıcı1* , *Europe.tailspintoys.com* etki alanındaki kimlik bilgilerini kullanarak *işistasyona* oturum açar. Kullanıcı daha sonra *USA.wingtiptoys.com* ormanında bulunan *FileServer1* üzerinde paylaşılan bir kaynağa erişmeyi dener.

2. *Işistasyonu* , etki alanındaki bir etki alanı DENETLEYICISINDEKI Kerberos KDC ile iletişim kurar, *ChildDC1* ve *FileServer1* SPN için bir hizmet bileti ister.

3. *ChildDC1* , SPN 'yi etki alanı veritabanında bulamaz ve *tailspintoys.com* ormanındaki herhangi BIR etki alanının bu SPN 'yi içerdiğini görmek için genel kataloğu sorgular. Genel Katalog kendi ormanıyla sınırlı olduğundan SPN bulunamadı.

    Genel Katalog daha sonra, ormanı ile oluşturulan tüm orman güvenleri hakkında bilgi için veritabanını denetler. Bulunursa, bir eşleşme bulmak için orman güveni güvenilen etki alanı nesnesinde (TDO) listelenen ad soneklerini hedef SPN 'nin sonekine karşılaştırır. Bir eşleşme bulunduğunda genel katalog, *ChildDC1* için bir yönlendirme ipucu sağlar.

    Yönlendirme ipuçları, kimlik doğrulama isteklerini hedef ormana yönlendirmek için yardım sağlar. İpuçları yalnızca yerel etki alanı denetleyicisi ve genel katalog gibi geleneksel kimlik doğrulama kanalları bir SPN 'yi bulamazsa kullanılır.

4. *ChildDC1* , üst etki alanı için bir başvuruyu *işistasyona* geri gönderir.

5. *Işistasyonubu* , *wingtiptoys.com* ormanının orman kök etki alanındaki bir etki alanı denetleyicisine (*ForestRootDC2*) bir başvuru için *ForestRootDC1* (üst etki alanı) içindeki bir etki alanı denetleyicisiyle iletişim kurar.

6. *Işistasyonuo* , istenen hizmete hizmet bileti için *wingtiptoys.com* ormanında *ForestRootDC2* ile iletişim kurar.

7. *FORESTROOTDC2* SPN 'yi bulmak için genel kataloğu ile iletişim kurar ve genel katalog SPN için bir eşleşme bulur ve bunu *ForestRootDC2*'e geri gönderir.

8. *ForestRootDC2* daha sonra *USA.wingtiptoys.com* 'e başvurusunu *işistasyona* geri gönderir.

9. *Işistasyonubu* , *ChildDC2* üzerindeki KDC Ile iletişim kurar ve *FileServer1*'ye erişim kazanmak için *kullanıcı1* biletini belirler.

10. *Işistasyonubu* bir hizmet bileti olduktan sonra, *kullanıcı1*'in güvenlik kimlik bilgilerini okuyan ve buna uygun bir erişim belirteci oluşturan *FileServer1*'e hizmet bileti gönderir.

## <a name="trusted-domain-object"></a>Güvenilen etki alanı nesnesi

Bir kuruluştaki her etki alanı veya orman güveni, etki alanı içindeki *sistem* kapsayıcısında depolanan bir güvenilen etki alanı NESNESI (TDO) ile temsil edilir.

### <a name="tdo-contents"></a>TDO içeriği

TDO 'da yer alan bilgiler, bir TDO 'nun bir etki alanı güveni veya bir orman güveni tarafından oluşturulup oluşturulmadığına bağlı olarak değişir.

Bir etki alanı güveni oluşturulduğunda, DNS etki alanı adı, etki alanı SID, güven türü, güven geçişliliği ve karşılıklı etki alanı adı gibi öznitelikler TDO 'da temsil edilir. Orman güveni TDOs, ortak ormandaki tüm güvenilir ad alanlarını tanımlamak için ek öznitelikler depolar. Bu öznitelikler etki alanı ağacı adlarını, Kullanıcı asıl adı (UPN) soneklerini, hizmet asıl adı (SPN) soneklerini ve güvenlik KIMLIĞI (SID) ad alanlarını içerir.

Güvenler as TDOs olarak Active Directory depolandığından, ormandaki tüm etki alanları, orman genelinde yerinde olan güven ilişkilerinin bilgisine sahiptir. Benzer şekilde, iki veya daha fazla orman, orman güvenleri üzerinden birlikte eklendiğinde, her ormandaki orman kök etki alanları, güvenilen ormanlardaki tüm etki alanları genelinde yer alan güven ilişkileri hakkında bilgi sahibi olmalıdır.

### <a name="tdo-password-changes"></a>TDO parola değişiklikleri

Güven ilişkisindeki her iki etki alanı da Active Directory içindeki TDO nesnesinde depolanan bir parolayı paylaşır. Hesap bakım işleminin bir parçası olarak, güvenen etki alanı denetleyicisinde her 30 günde bir, TDO 'da depolanan parolayı değiştirir. Her iki yönlü güven gerçekten 2 1 yönlü güven olduğundan, iki yönlü güvenler için işlem iki kez gerçekleşir.

Güven, güvenen ve güvenilen bir tarafına sahiptir. Güvenilen tarafta, işlem için tüm yazılabilir etki alanı denetleyicileri kullanılabilir. Güvenen tarafta, PDC öykünücüsü parola değişikliğini gerçekleştirir.

Bir parolayı değiştirmek için etki alanı denetleyicileri aşağıdaki işlemi tamamlar:

1. Güvenen etki alanındaki birincil etki alanı denetleyicisi (PDC) öykünücüsü yeni bir parola oluşturur. Güvenilen etki alanındaki bir etki alanı denetleyicisi, parola değişikliğini hiçbir şekilde başlatmayacaktır. Güvenilen etki alanı PDC öykünücüsü tarafından her zaman başlatılır.

2. Güvenen etki alanındaki PDC öykünücüsü, TDO nesnesinin *oldPassword* alanını geçerli *YeniParola* alanına ayarlar.

3. Güvenen etki alanındaki PDC öykünücüsü, TDO nesnesinin *YeniParola* alanını yeni parolaya ayarlar. Önceki parolanın bir kopyasının tutulması, güvenilen etki alanındaki etki alanı denetleyicisi değişikliği alamadığında veya değişiklik, yeni güven parolasını kullanan bir istek yapılmadan önce çoğaltılmamışsa eski parolaya geri dönmek mümkün hale gelir.

4. Güvenen etki alanındaki PDC öykünücüsü, güvenilen etki alanındaki bir etki alanı denetleyicisine uzaktan bir çağrı yapar ve bu kullanıcıdan güven hesabındaki parolayı yeni parolaya ayarlamanızı ister.

5. Güvenilen etki alanındaki etki alanı denetleyicisi, güven parolasını yeni parolayla değiştirir.

6. Güvenin her tarafında güncelleştirmeler, etki alanındaki diğer etki alanı denetleyicilerine çoğaltılır. Güvenen etki alanında değişiklik, güvenilen etki alanı nesnesinin acil bir çoğaltmasını tetikler.

Parola, her iki etki alanı denetleyicisinde de değişmiştir. Normal çoğaltma, TDO nesnelerini etki alanındaki diğer etki alanı denetleyicilerine dağıtır. Ancak, güvenen etki alanındaki etki alanı denetleyicisinin, güvenilen etki alanındaki bir etki alanı denetleyicisini başarıyla güncelleştirmeden parolayı değiştirmesi mümkündür. Bu senaryo, parola değişikliğini işlemek için gerekli olan güvenli bir kanal kurulamadığından meydana gelebilir. Ayrıca, güvenilen etki alanındaki etki alanı denetleyicisinin işlem sırasında bir noktada kullanılamaması ve güncelleştirilmiş parolayı almamanız mümkündür.

Parola değişikliğinin başarılı bir şekilde iletimediği durumlarla başa çıkmak için, güvenen etki alanındaki etki alanı denetleyicisi, yeni parolayı kullanarak kimlik doğrulamasından (güvenli bir kanal ayarlamaya), yeni parolayı hiçbir şekilde değiştirmez. Bu davranış, güvenilen etki alanının TDO nesnesinde hem eski hem de yeni parolaların tutulmasından neden olur.

Parola başarıyla kullanıldığında kimlik doğrulaması başarılı olana kadar parola değişikliği sonlandırılmadı. Güvenilen etki alanındaki etki alanı denetleyicisi yeni parolayı alıncaya kadar, bu nedenle kesintisiz hizmeti etkinleştirerek, eski, depolanan parola güvenli kanal üzerinden kullanılabilir.

Parola geçersiz olduğundan, yeni parolayı kullanan kimlik doğrulaması başarısız olursa, güvenen etki alanı denetleyicisi eski parolayı kullanarak kimlik doğrulamaya çalışır. Eski parolayla başarıyla kimlik doğrulaması gerçekleştiriyorsa, 15 dakika içinde parola değiştirme işlemine devam eder.

Güven parola güncelleştirmelerinin, her iki tarafının da 30 gün içinde etki alanı denetleyicilerine çoğaltılması gerekir. Güven parolası 30 gün sonra değiştirilirse ve bir etki alanı denetleyicisinin yalnızca N-2 parolası varsa, güvenen taraftan güveni kullanamaz ve güvenilir tarafta güvenli bir kanal oluşturamaz.

## <a name="network-ports-used-by-trusts"></a>Güvenler tarafından kullanılan ağ bağlantı noktaları

Güvenin çeşitli ağ sınırları genelinde dağıtılması gerektiğinden, bir veya daha fazla güvenlik duvarının yayılması gerekebilir. Bu durumda, trafiğin geçmesine izin vermek için güvenlik duvarı genelinde güven trafiğini tüneledebilir veya güvenlik duvarında belirli bağlantı noktalarını açabilirsiniz.

> [!IMPORTANT]
> Active Directory Domain Services, Active Directory RPC trafiğinin belirli bağlantı noktalarına kısıtlanması desteklenmez.

Bir orman güveni için gereken bağlantı noktaları hakkında bilgi edinmek için [Active Directory etki alanları ve Güvenleri için bir güvenlik duvarı yapılandırma](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) Microsoft desteği makalesinin **Windows Server 2008 ve sonraki sürümler** bölümünü okuyun.

## <a name="supporting-services-and-tools"></a>Destekleyici hizmetler ve araçlar

Güvenleri ve kimlik doğrulamasını desteklemek için bazı ek özellikler ve yönetim araçları kullanılır.

### <a name="net-logon"></a>NET oturum açma

NET oturum açma hizmeti, Windows tabanlı bir bilgisayardan DC 'ye güvenli bir kanal sağlar. Ayrıca, aşağıdaki güvenle ilgili işlemlerde de kullanılır:

* Güven kurulumu ve yönetimi-net oturum açma, güven parolalarının korunmasını, güven bilgilerini toplamanıza ve LSA işlemiyle ve TDO ile etkileşime girerek güvenleri doğrular.

    Orman güvenleri için güven bilgileri, güvenilen bir ormanın yönetilmesi için gereken ad alanları kümesini içeren orman güven bilgileri (*Ftınfo*) kaydını içerir. Bu, her talebe güvenen orman tarafından güvenilip güvenilmediğini gösteren bir alanla birlikte açıklanmış olur.

* Kimlik doğrulaması – bir etki alanı denetleyicisine güvenli bir kanal üzerinden Kullanıcı kimlik bilgilerini sağlar ve Kullanıcı için etki alanı SID 'Lerini ve Kullanıcı haklarını döndürür.

* Etki alanı denetleyicisi konumu – bir etki alanında veya etki alanlarında etki alanı denetleyicileri bulmaya veya bulmaya yardımcı olur.

* Doğrudan doğrulama – diğer etki alanlarındaki kullanıcıların kimlik bilgileri net oturum açma tarafından işlenir. Güvenen bir etki alanının bir kullanıcının kimliğini doğrulaması gerektiğinde, doğrulama için kullanıcının kimlik bilgilerini güvenilir etki alanına ağ oturumu aracılığıyla geçirir.

* Ayrıcalık öznitelik sertifikası (PAC) doğrulaması – kimlik doğrulaması için Kerberos protokolünü kullanan bir sunucunun bir hizmet bileti içindeki PAC 'yi doğrulaması gerektiğinde, kimlik doğrulama için PAC 'yi güvenli kanal üzerinden etki alanı denetleyicisine gönderir.

### <a name="local-security-authority"></a>Yerel Güvenlik Yetkilisi

Yerel güvenlik yetkilisi (LSA), sistemdeki yerel güvenlik özelliklerinin tüm yönleri hakkında bilgi tutan bir korumalı alt sistemdir. Her topluca yerel güvenlik ilkesi olarak bilinen LSA, adlar ve tanımlayıcılar arasında çeviri için çeşitli hizmetler sağlar.

LSA güvenlik alt sistemi, nesnelere erişimi doğrulamak, kullanıcı ayrıcalıklarını denetlemek ve denetim iletileri oluşturmak için hem çekirdek modunda hem de Kullanıcı modunda hizmetler sağlar. LSA, güvenilen veya güvenilmeyen etki alanlarında hizmetler tarafından sunulan tüm oturum biletlerinin geçerliliğini denetmaktan sorumludur.

### <a name="management-tools"></a>Yönetim araçları

Yöneticiler, güvenleri göstermek, oluşturmak, kaldırmak veya değiştirmek için *Active Directory etki alanları ve Güvenleri*, *netdom* ve *nltest* kullanabilirler.

* *Active Directory etki alanları ve güvenler* , etki alanı güvenlerini, etki alanı ve orman işlev düzeylerini ve Kullanıcı asıl adı soneklerini yönetmek Için kullanılan Microsoft Yönetim Konsolu 'NA (MMC) sahiptir.
* *Netdom* ve *nltest* komut satırı araçları, güvenleri bulmak, göstermek, oluşturmak ve yönetmek için kullanılabilir. Bu araçlar, bir etki alanı denetleyicisindeki LSA yetkilisi ile doğrudan iletişim kurar.

## <a name="next-steps"></a>Sonraki adımlar

Kaynak ormanları hakkında daha fazla bilgi edinmek için bkz. [Azure AD DS orman güvenleri nasıl çalışır?][concepts-trust]

Kaynak ormanı ile yönetilen bir etki alanı oluşturmaya başlamak için bkz. [Azure AD DS yönetilen etki alanı oluşturma ve yapılandırma][tutorial-create-advanced]. Daha sonra şirket [içi etki alanına giden bir orman güveni oluşturabilirsiniz][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
