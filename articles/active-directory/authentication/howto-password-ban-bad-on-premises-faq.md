---
title: Şirket içi Azure AD parola koruması hakkında SSS
description: Şirket içi Active Directory Domain Services ortamda Azure AD parola koruması hakkında sık sorulan soruları gözden geçirin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625137"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD parola koruması şirket içi sık sorulan sorular

Bu bölümde, Azure AD parola koruması hakkında sık sorulan birçok soruya yanıtlar verilmektedir.

## <a name="general-questions"></a>Genel sorular

**S: güvenli parola seçme konusunda kullanıcılara ne tür yönergeler verilmelidir?**

Microsoft 'un bu konuyla ilgili geçerli Kılavuzu aşağıdaki bağlantıda bulunabilir:

[Microsoft parola Kılavuzu](https://www.microsoft.com/research/publication/password-guidance)

**S: şirket içi Azure AD parola koruması, genel olmayan bulutlarda destekleniyor mu?**

Şirket içi Azure AD parola koruması, genel bulutta ve Arlington bulutu 'nda desteklenir. Diğer bulutlarda kullanılabilirlik için hiçbir tarih duyurulmamıştır.

Azure AD portalı, desteklenmeyen bulutlarda bile olsa, şirket içi "Windows Server Active Directory parola koruması" yapılandırması için değişiklik yapılmasına izin verir; Bu değişiklikler kalıcı hale getirilir, aksi takdirde hiçbir zaman etkili olmayacaktır. Şirket içi proxy aracılarının veya ormanlarının kaydı, desteklenmeyen bulutlarda desteklenmez ve bu tür kayıt denemeleri her zaman başarısız olur.

**S: Azure AD parola koruma avantajlarını şirket içi kullanıcılarınızın bir alt kümesine nasıl uygulayabilirim?**

Desteklenmez. Dağıtım ve etkinleştirildikten sonra Azure AD parola koruması, tüm kullanıcılar eşit güvenlik avantajları elde etmez.

**S: parola değiştirme ve parola ayarlama (veya sıfırlama) arasındaki fark nedir?**

Parola değişikliği, bir Kullanıcı eski parola hakkında bilgi sahibi olduktan sonra yeni bir parola seçtiğinde. Örneğin, bir Kullanıcı Windows 'da oturum açtığında ve ardından yeni bir parola seçmesi istendiğinde parola değişikliği olur.

Bir parola kümesi (bazen parola sıfırlama olarak adlandırılır), bir yönetici bir hesaptaki parolayı yeni bir parolayla Değiştir, örneğin Active Directory Kullanıcılar ve bilgisayarlar Yönetim Aracı ' nı kullanmaktır. Bu işlem, yüksek düzeyde ayrıcalık (genellikle etki alanı Yöneticisi) gerektirir ve işlemi gerçekleştiren kişi genellikle eski parola hakkında bilgi sahibi değildir. Yardım Masası senaryoları genellikle parola kümeleri gerçekleştirir, örneğin, parolasını unutmuş bir kullanıcıyı öğreniyor. Ayrıca, yeni bir kullanıcı hesabı bir parola ile ilk kez oluşturulduğunda parola ayarlama olaylarını da görürsünüz.

Parola doğrulama ilkesi, parola değiştirme veya ayarlama işleminin yapılıp yapılmadığına bakılmaksızın aynı şekilde davranır. Azure AD parola koruması DC Aracısı hizmeti, parola değiştirme veya ayarlama işleminin yapılıp yapılmadığını bildirmek için farklı olayları günlüğe kaydeder.  Bkz. [Azure AD parola koruması izleme ve günlüğe kaydetme](./howto-password-ban-bad-on-premises-monitor.md).

**S: Azure AD parola koruması, mevcut parolaları yükledikten sonra doğrular mi?**

Hayır-Azure AD parola koruması, parola değiştirme veya ayarlama işlemi sırasında yalnızca şifresiz metin parolalarında parola ilkesini zorlayabilir. Bir parola Active Directory tarafından kabul edildiğinde, bu parolanın yalnızca kimlik doğrulaması protokolüne özgü karmaları kalıcı hale getirilir. Şifresiz metin parolası hiçbir şekilde kalıcı değildir, bu nedenle Azure AD parola koruması mevcut parolaları doğrulayamaz.

Azure AD parola koruması 'nın ilk dağıtımından sonra, tüm kullanıcılar ve hesaplar sonunda, var olan parolalarının zaman içinde normalde süresi dolduğunda Azure AD parola koruması ile doğrulanan parolayı kullanmaya başlar. İsterseniz, bu işlem, Kullanıcı hesabı parolalarının bir kerelik bir el ile süre sonu ile hızlandırılır.

"Parola süresi dolmasın" ile yapılandırılan hesapların, el ile sona erme tarihi yapılmadığı takdirde parolalarını değiştirme izni olmaz.

**S: Active Directory Kullanıcıları ve bilgisayarları Yönetimi ek bileşenini kullanarak zayıf bir parola ayarlamaya çalışırken neden yinelenen parola reddetme olayları günlüğe kaydedilir?**

Active Directory Kullanıcıları ve Bilgisayarları yönetim ek bileşeni, önce Kerberos protokolünü kullanarak yeni parolayı ayarlamaya çalışır. Hata sonrasında, ek bileşen eski (SAM RPC) protokolünü kullanarak parolayı ayarlamaya yönelik ikinci bir deneme yapar (kullanılan protokoller önemli değildir). Yeni parola Azure AD parola koruması tarafından zayıf kabul edildiğinde, bu ek bileşen davranışı iki parola sıfırlama reddetme olayı kümesine yol açar.

**S: Azure AD parola koruma parolası doğrulama olayları neden boş bir kullanıcı adıyla günlüğe kaydediliyor?**

Active Directory, örneğin [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API 'sini kullanarak etki alanının geçerli parola karmaşıklığı gereksinimlerini geçirmesinin başarılı olup olmadığını görmek için bir parolayı test etme özelliğini destekler. Bu şekilde bir parola doğrulandığında, test, Azure AD parola koruması gibi parola filtresi DLL tabanlı ürünlerin doğrulanmasını de içerir, ancak belirli bir parola filtresi dll 'sine geçirilen kullanıcı adları boş olur. Bu senaryoda, Azure AD parola koruması Şu anda etkin olan parola ilkesini kullanarak parolayı doğrular ve sonucu yakalamak için bir olay günlüğü iletisi verir, ancak olay günlüğü iletisinde boş Kullanıcı adı alanları vardır.

**S: Azure AD parola koruması 'Nın diğer parola filtresi tabanlı ürünlerle yan yana yüklenmesi destekleniyor mu?**

Evet. Birden çok kayıtlı parola filtresi dll 'leri için destek, Azure AD parola korumasına özgü olmayan bir temel Windows özelliğidir. Bir parola kabul edilmeden önce tüm kayıtlı parola filtresi dll 'leri kabul etmelidir.

**S: Azure AD parola korumasını Azure kullanmadan Active Directory ortammda nasıl dağıtırım ve yapılandırabilirim?**

Desteklenmez. Azure AD parola koruması, şirket içi Active Directory ortamına genişletilmesini destekleyen bir Azure özelliğidir.

**S: Active Directory düzeyinde ilkenin içeriğini nasıl değiştirebilirim?**

Desteklenmez. İlke yalnızca Azure AD Portalı kullanılarak yönetilebilir. Ayrıca önceki soruya bakın.

**S: SYSVOL çoğaltması için DFSR neden gereklidir?**

FRS (DFSR 'nin öncül teknolojisi) birçok bilinen soruna sahiptir ve Windows Server Active Directory 'in daha yeni sürümlerinde tümüyle desteklenmez. Azure AD parola korumasının sıfır testi, FRS tarafından yapılandırılan etki alanlarında yapılır.

Daha fazla bilgi için lütfen aşağıdaki makalelere bakın:

[SYSVOL çoğaltmasını DFSR 'ye geçirme durumu](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[Son, FRS için Nigh](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Etki alanınız zaten DFSR kullanıyorsa, Azure AD parola korumasını yüklemeden önce bu uygulamayı DFSR 'yi kullanacak şekilde geçirmeniz gerekır. Daha fazla bilgi için şu bağlantıya bakın:

[SYSVOL çoğaltma geçiş kılavuzu: FRS DFS Çoğaltma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD parola koruması DC Aracısı yazılımı, halen SYSVOL çoğaltması için FRS kullanan etki alanlarındaki etki alanı denetleyicilerine yüklenir, ancak yazılım bu ortamda düzgün çalışmaz. Diğer negatif yan etkiler, çoğaltılamayan tek dosyaları ve SYSVOL geri yükleme yordamlarını başarılı olarak, ancak tüm dosyaları çoğaltamaz. Yalnızca DFSR 'nin kendi avantajları ve ayrıca Azure AD parola koruması dağıtımının engellemesini kaldırmak için etki alanınızı en kısa sürede DFSR 'yi kullanacak şekilde geçirmeniz gerekir. Yazılımın gelecekteki sürümleri, hala FRS kullanan bir etki alanında çalışırken otomatik olarak devre dışı bırakılacaktır.

**S: özellik etki alanı SYSVOL paylaşımında ne kadar disk alanı gerektiriyor?**

Kesin alan kullanımı, Microsoft Global yasaklanmış listesindeki yasaklanmış belirteçlerin sayısı ve uzunluğu, kiracı başına özel liste ve şifreleme ek yükü gibi etkenlere bağlı olduğundan farklılık gösterir. Bu listelerin içeriği gelecekte büyümek için büyük olasılıkla. Göz önünde bulundurularak, özelliğin etki alanı SYSVOL paylaşımında en az beş (5) megabayt alana ihtiyacı olacak şekilde makul bir beklentidir.

**S: DC Aracısı yazılımını yüklemek veya yükseltmek için neden yeniden başlatma gerekiyor?**

Bu gereksinim, çekirdek Windows davranışının oluşmasına neden olur.

**S: bir DC aracısını belirli bir proxy sunucusunu kullanacak şekilde yapılandırmak için herhangi bir yol var mı?**

Hayır. Proxy sunucusu durum bilgisiz olduğundan, belirli bir proxy sunucusunun kullanıldığı önemli değildir.

**S: Azure AD parola koruma proxy hizmetini Azure AD Connect gibi diğer hizmetlerle yan yana dağıtmayı sorunsuz mı?**

Evet. Azure AD parola koruma proxy hizmeti ve Azure AD Connect hiçbir şekilde doğrudan çakışmalıdır.

Ne yazık ki, Azure AD parola koruma proxy 'Si yazılımı tarafından yüklenen Microsoft Azure AD Connect Agent Güncelleştirici hizmeti sürümü ile [Azure Active Directory uygulama ara sunucusu](../manage-apps/application-proxy.md) yazılımı tarafından yüklenen hizmetin sürümü arasında bir uyumsuzluk bulundu. Bu uyumsuzluk, aracı güncelleştiricisi hizmetinin yazılım güncelleştirmeleri için Azure ile bağlantı kurabileceğinden kaynaklanabilir. Aynı makinede Azure AD parola koruma proxy ve Azure Active Directory Uygulama Ara Sunucusu yüklenmesi önerilmez.

**S: DC aracıları ve proxy 'lerin hangi sırada yüklü ve kayıtlı olması gerekir?**

Proxy aracısı yüklemesi, DC Aracısı yüklemesi, orman kaydı ve proxy kaydı için herhangi bir sıralama desteklenir.

**S: etki alanı denetleyicilerimde bu özelliği dağıtmaktan performans isabetinden endişelenmelidir mi?**

Azure AD parola koruması DC Aracısı hizmeti, mevcut bir sağlıklı Active Directory dağıtımında etki alanı denetleyicisi performansını önemli ölçüde etkilememelidir.

Çoğu Active Directory dağıtım parolası değiştirme işlemleri, belirli bir etki alanı denetleyicisindeki genel iş yükünün küçük bir oransıdır. Örnek olarak, 10000 Kullanıcı hesabı ve bir Maxpasswordadge ilkesi içeren Active Directory bir etki alanını 30 gün olarak düşünün. Ortalama olarak, bu etki alanı, tek bir etki alanı denetleyicisi için çok az sayıda işlem olan her gün 10000/30 = ~ bir parola değiştirme işlemi görür. Olası bir en kötü durum senaryosunu göz önünde bulundurun: tek bir DC 'de ~ 333 parola değişikliklerinin tek bir saat içinde yapıldığını varsayalım. Örneğin, bu senaryo birçok çalışanın bir Pazartesi sabah günü üzerinde çalışması halinde gerçekleşebilir. Bu durumda bile, hala önemli bir yük olmayan ~ 333/60 dakika = altı parola değişikliğine bakıyoruz.

Ancak, geçerli etki alanı denetleyicileriniz zaten performans sınırlı düzeylerde çalışıyorsa (örneğin, CPU, disk alanı, disk g/ç vb. açısından), bu özellik dağıtılmadan önce ek etki alanı denetleyicileri eklemeniz veya kullanılabilir disk alanının genişlemesine önerilir. Yukarıdaki SYSVOL disk alanı kullanımı hakkında yukarıdaki soruya de bakın.

**S: Azure AD parola korumasını etki alanım 'da yalnızca birkaç DC 'de test etmek istiyorum. Kullanıcı parolası değişikliklerini söz konusu DC 'leri kullanacak şekilde zorlamak mümkün midir?**

Hayır. Windows istemci işletim sistemi, Kullanıcı parolasını değiştirdiğinde kullanılacak etki alanı denetleyicisini denetler. Etki alanı denetleyicisi, Active Directory site ve alt ağ atamaları, ortama özgü ağ yapılandırması vb. gibi etkenlere göre belirlenir. Azure AD parola koruması bu faktörleri denetlemez ve kullanıcının parolasını değiştirmek için hangi etki alanı denetleyicisinin seçili olduğunu etkilemez.

Bu hedefe kısmen ulaşmak için bir yol, Azure AD parola korumasını belirli bir Active Directory sitesindeki tüm etki alanı denetleyicilerine dağıtmaktır. Bu yaklaşım, bu siteye atanan Windows istemcileri için ve ayrıca bu istemcilere oturum açan ve parolalarını değiştiren kullanıcılar için makul bir kapsam sağlar.

**S: Azure AD parola koruması DC Aracısı hizmetini yalnızca birincil etki alanı denetleyicisine (PDC) yüklediğimde, etki alanındaki diğer tüm etki alanı denetleyicileri de korunacaktır mi?**

Hayır. Bir kullanıcının parolası, belirli bir PDC olmayan etki alanı denetleyicisinde değiştirildiğinde, şifresiz metin parolası hiçbir zaman PDC 'ye gönderilmez (Bu fikir, yaygın olarak karşılaşılan bir açıklıkdır). Belirli bir DC 'de yeni bir parola kabul edildikten sonra bu DC, söz konusu parolanın kimlik doğrulama protokolüne özgü karmalarını oluşturmak için bu parolayı kullanır ve sonra bu karmaların dizinde devam eder. Şifresiz metin parolası kalıcı değil. Güncelleştirilmiş karmalar daha sonra PDC 'ye çoğaltılır. Kullanıcı parolaları, bazı durumlarda ağ topolojisi ve Active Directory site tasarımı gibi çeşitli faktörlere bağlı olarak doğrudan PDC üzerinde değiştirilebilir. (Önceki soruya bakın.)

Özet olarak, etki alanı genelinde özelliğin %100 güvenlik kapsamına ulaşmak için PDC 'de Azure AD parola koruması DC Aracısı hizmetinin dağıtımı gerekir. Özelliği PDC 'ye dağıtmak, etki alanındaki diğer DC 'Ler için Azure AD parola koruması güvenlik avantajları sağlamaz.

**S: aracılar şirket içi Active Directory ortamımı yükledikten sonra bile özel akıllı kilitleme çalışmıyor mu?**

Özel akıllı kilitleme yalnızca Azure AD 'de desteklenir. Azure AD portalındaki özel akıllı kilitleme ayarlarında yapılan değişiklikler, aracıların yüklü olduğu hatta şirket içi Active Directory ortamı üzerinde hiçbir etkiye sahip değildir.

**S: Azure AD parola koruması için kullanılabilir bir System Center Operations Manager yönetim paketi var mı?**

Hayır.

**S: ilkeyi denetim modunda olacak şekilde yapılandırdığım halde Azure AD neden hala zayıf parolaları reddediyor?**

Denetim modu yalnızca şirket içi Active Directory ortamında desteklenir. Azure AD, parolaları değerlendirdiğinde örtülü olarak her zaman "zorla" modunda yapılır.

**S: Kullanıcılarım, Azure AD parola koruması tarafından bir parola reddedildiğinde geleneksel Windows hata iletisini görür. Bu hata iletisini, kullanıcıların gerçekten ne olduğunu bilmesi için özelleştirmek mümkün mü?**

Hayır. Bir etki alanı denetleyicisi tarafından bir parola reddedildiğinde, kullanıcılar tarafından görülen hata iletisi, etki alanı denetleyicisi tarafından değil, istemci makine tarafından denetlenir. Bu davranış, bir parolanın varsayılan Active Directory parola ilkeleri tarafından veya Azure AD parola koruması gibi parola filtresi tabanlı bir çözüm tarafından reddedilip reddedilmediğini ortaya çıkar.

## <a name="password-testing-procedures"></a>Parola sınama yordamları

Yazılımın düzgün çalışmasını doğrulamak ve [parola değerlendirme algoritması](concept-password-ban-bad.md#how-are-passwords-evaluated)hakkında daha iyi bir anlayışa ulaşmak için çeşitli parolaların temel testlerini yapmak isteyebilirsiniz. Bu bölümde, tekrarlanabilir sonuçlar oluşturmak için tasarlanan bu test için bir yöntem özetlenmektedir.

Bu tür adımları izlemek neden gereklidir? Şirket içi Active Directory ortamında parolaların denetimli, tekrarlanabilir sınamasını yapmayı zorlaştıran birkaç etken vardır:

* Parola ilkesi Azure 'da yapılandırılır ve kalıcı hale getirilir ve ilke kopyaları, bir yoklama mekanizması kullanılarak şirket içi DC aracıları tarafından düzenli aralıklarla eşitlenir. Bu yoklama döngüsünün içerdiği gecikme karışıklığına neden olabilir. Örneğin, ilkeyi Azure 'da yapılandırır ancak DC aracısıyla eşitlemeyi unutursanız, testleriniz beklenen sonuçları vermeyebilir. Yoklama aralığı şu anda saatte bir kez olacak şekilde kodlanmıştır, ancak ilke değişiklikleri arasındaki saatin beklenmesi etkileşimli bir test senaryosu için ideal değildir.
* Yeni bir parola ilkesi bir etki alanı denetleyicisine eşitlendikten sonra, diğer etki alanı denetleyicilerine çoğaltma yaparken daha fazla gecikme olur. Bu gecikmeler, ilkenin en son sürümünü henüz almamış bir etki alanı denetleyicisine karşı parola değişikliğini test ederseniz beklenmedik sonuçlara neden olabilir.
* Parola değişikliklerinin bir kullanıcı arabirimi aracılığıyla sınanması, sonuçlarınızda güvenle emin olmanızı zorlaştırır. Örneğin, çoğu parola Kullanıcı arabirimi Kullanıcı girişini gizleyecek (örneğin, Windows Ctrl-Alt-Delete-> parola değiştirme kullanıcı ARABIRIMI gibi), özellikle de Kullanıcı arabirimine geçersiz bir parola yazmak kolaydır.
* Etki alanına katılmış istemcilerden parola değişiklikleri test edilirken hangi etki alanı denetleyicisinin kullanılacağını tam olarak denetlemek mümkün değildir. Windows istemci işletim sistemi, Active Directory site ve alt ağ atamaları, ortama özgü ağ yapılandırması vb. gibi etkenlere bağlı olarak bir etki alanı denetleyicisi seçer.

Bu sorunlardan kaçınmak için aşağıdaki adımlar, bir etki alanı denetleyicisinde oturum açarken parola sıfırlamaları için komut satırı testini temel alır.

> [!WARNING]
> Bu yordamlar yalnızca bir test ortamında kullanılmalıdır, çünkü tüm gelen parola değişiklikleri ve sıfırlamaları, DC Aracısı hizmeti durdurulduğunda doğrulama yapılmadan kabul edilir ve ayrıca etki alanı denetleyicisinde oturum açan daha fazla riski ortadan kaldırmak için kullanılır.

Aşağıdaki adımlarda, DC aracısını en az bir etki alanı denetleyicisine yüklediğinizi, en az bir ara sunucu yüklediğinizi ve hem proxy hem de ormanı kaydettirdiğiniz varsayılır.

1. Etki alanı yönetici kimlik bilgilerini (veya test Kullanıcı hesapları oluşturmak için yeterli ayrıcalıklara sahip diğer kimlik bilgilerini) kullanarak etki alanı denetleyicisinde oturum açın, bu, DC aracı yazılımının yüklü olduğunu ve yeniden başlatılmasını sağlar.
1. Olay Görüntüleyicisi açın ve [DC Aracısı Yönetici olay günlüğüne](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log)gidin.
1. Yükseltilmiş bir komut istemi penceresi açın.
1. Parola testi yapmak için bir test hesabı oluşturun

   Bir kullanıcı hesabı oluşturmanın birçok yolu vardır ancak bir komut satırı seçeneği, yinelenen test döngüleri sırasında daha kolay hale getirmenin bir yolu olarak sunulur:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   Aşağıdaki tartışma amaçları doğrultusunda, "ContosoUser" adlı bir sınama hesabı oluşturduğumuz varsayın, örneğin:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Bir Web tarayıcısı açın (etki alanı denetleyiciniz yerine ayrı bir cihaz kullanmanız gerekebilir), [Azure Portal](https://portal.azure.com)oturum açın ve Azure Active Directory > güvenlik > kimlik doğrulama yöntemlerine > parola koruması ' na gidin.
1. Azure AD parola koruma ilkesini gerçekleştirmek istediğiniz test için gereken şekilde değiştirin.  Örneğin, zorlanan veya Denetim modunu yapılandırmaya karar verebilir ya da özel yasaklanmış parolalar listenizde yasaklanmış koşulların listesini değiştirmeye karar verebilirsiniz.
1. DC Aracısı hizmetini durdurup yeniden başlatarak yeni ilkeyi eşitler.

   Bu adım çeşitli yollarla gerçekleştirilebilir. Tek bir yol, Azure AD parola koruması DC Aracısı hizmetine sağ tıklayıp "yeniden Başlat" seçeneğini belirleyerek hizmet yönetimi Yönetim Konsolu 'nu kullanmaktır. Bunun gibi komut istemi penceresinde başka bir yol da gerçekleştirilebilir:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Yeni bir ilkenin indirildiğini doğrulamak için Olay Görüntüleyicisi denetleyin.

   DC Aracısı hizmetinin her durdurulduğu ve başlatıldığı her seferinde, art arda yakın 2 30006 olay görmeniz gerekir. İlk 30006 olay, SYSVOL paylaşımındaki diskte önbelleğe alınan ilkeyi yansıtır. İkinci 30006 olayda (varsa) güncelleştirilmiş bir kiracı ilkesi tarihi olmalıdır ve bu durumda Azure 'dan indirilen ilke yansıtılacaktır. Kiracı ilkesi tarih değeri şu anda, ilkenin Azure 'dan indirildiği yaklaşık zaman damgasını gösterecek şekilde kodlanmıştır.
   
   İkinci 30006 olayı görünmezse devam etmeden önce sorunu gidermeniz gerekir.
   
   30006 olayları şu örneğe benzer şekilde görünür:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Örneğin, Zorlanmış ve Denetim modu arasında değişiklik yapmak, AuditOnly bayrağının değiştirilmesine neden olur (yukarıdaki ilke, AuditOnly = 0 ile zorlanır); Özel yasaklanmış parola listesindeki değişiklikler yukarıdaki 30006 olayına doğrudan yansıtılmaz (ve güvenlik nedenleriyle herhangi bir yerde günlüğe kaydedilmez). Bu değişiklik sonrasında ilkeyi Azure 'dan başarıyla indirmek, değiştirilmiş özel yasaklanmış parola listesini de içerecektir.

1. Sınama Kullanıcı hesabındaki yeni bir parolayı sıfırlamaya çalışırken bir test çalıştırın.

   Bu adım, aşağıdaki gibi komut istemi penceresinden yapılabilir:

   ```text
   net.exe user ContosoUser <password>
   ```

   Komutunu çalıştırdıktan sonra olay görüntüleyicisine bakarak komutun sonucu hakkında daha fazla bilgi edinebilirsiniz. Parola doğrulama sonucu olayları, [DC Aracısı Yönetici olay günlüğü](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) konusunda belgelenmiştir; Bu tür olayları, net.exe komutlarının etkileşimli çıktısına ek olarak, testinizin sonucunu doğrulamak için kullanacaksınız.

   Bir örnek deneyin: Microsoft genel listesi tarafından yasaklanmış bir parolayı ayarlamaya çalışırken (listenin [açıklanmadığını](concept-password-ban-bad.md#global-banned-password-list) ancak bilinen yasaklanmış bir terime karşı burada test etmemiz gerektiğini unutmayın). Bu örnek, ilkeyi zorlanan modda olacak şekilde yapılandırdığınızı ve özel yasaklanmış parola listesine sıfır terim eklendiğini varsayar.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   Belgeler için, sınamamız bir parola sıfırlama işlemi olduğundan, ContosoUser kullanıcısı için 10017 ve 30005 bir olay görmeniz gerekir.

   10017 olayı şu örnekteki gibi görünmelidir:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30005 olayı şu örnekteki gibi görünmelidir:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   Bu eğlenceli bir örnek deneyin! Bu süre, ilke Denetleme modundayken özel yasaklanmış listesi tarafından yasaklanmış bir parolayı ayarlamaya çalışacaktır. Bu örnek, aşağıdaki adımları yaptığınızı varsayar: ilkeyi denetim modunda olacak şekilde yapılandırdıktan sonra, "lachrymose" terimini özel yasaklanmış parola listesine eklediniz ve yukarıda açıklandığı gibi DC Aracısı hizmetini geçirerek etki alanı denetleyicisi ile ilgili yeni ilkeyi eşitler.

   Tamam, yasaklanmış parolanın bir çeşidini ayarla:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   İlke denetim modunda olduğundan, bu süreyi unutmayın. ContosoUser kullanıcısı için bir 10025 ve 30007 olayı görmeniz gerekir.

   10025 olayı şu örnekteki gibi görünmelidir:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30007 olayı şu örnekteki gibi görünmelidir:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Seçtiğiniz çeşitli parolaları teste devam edin ve önceki adımlarda özetlenen yordamları kullanarak Olay görüntüleyicisindeki sonuçları kontrol edin. Azure portal ilkeyi değiştirmeniz gerekiyorsa, yeni ilkeyi daha önce açıklandığı gibi DC aracısına eşitlemeyi unutmayın.

Azure AD parola korumasının parola doğrulama davranışının denetimli test edilmesini sağlayan yordamlar ele alınmıştır. Kullanıcı parolalarının doğrudan bir etki alanı denetleyicisinde sıfırlanması, söz konusu testi yapmanın tek bir yolu olabilir, ancak daha önce belirtildiği gibi, tekrarlanabilir sonuçlar oluşturmak için tasarlanmıştır. Çeşitli parolaları test ederken, beklemediğinizi belirten sonuçları açıklamanıza yardımcı olabileceği için [parola değerlendirme algoritmasını](concept-password-ban-bad.md#how-are-passwords-evaluated) göz önünde bulundurun.

> [!WARNING]
> Tüm testler tamamlandığında, test amacıyla oluşturulan herhangi bir kullanıcı hesabını silmeyi unutmayın!

## <a name="additional-content"></a>Ek içerik

Aşağıdaki bağlantılar çekirdek Azure AD parola koruma belgelerinin bir parçası değildir, ancak bu özellik hakkında yararlı bir ek bilgi kaynağı olabilir.

[Azure AD parola koruması genel kullanıma sunuldu!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-posta sızdırma koruma Kılavuzu – 15. Bölüm: Microsoft Azure AD parola koruma hizmetini uygulama (Şirket Içi için çok!)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD parola koruması ve akıllı kilitleme artık genel önizlemede!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified destek eğitimi kullanılabilir

Azure AD parola koruması hakkında daha fazla bilgi edinmek ve ortamınıza dağıtmak istiyorsanız, Premier veya Birleşik destek sözleşimiyle bu müşterilere sunulan Microsoft proaktif bir hizmetten yararlanabilirsiniz. Hizmet Azure Active Directory: parola koruması olarak adlandırılır. Daha fazla bilgi için teknik hesap yöneticinize başvurun.

## <a name="next-steps"></a>Sonraki adımlar

Burada yanıtlanmayan bir şirket içi Azure AD parola koruma sorunuz varsa, bir geri bildirim öğesi gönderin; teşekkürler!

[Azure AD parola korumasını dağıtma](howto-password-ban-bad-on-premises-deploy.md)