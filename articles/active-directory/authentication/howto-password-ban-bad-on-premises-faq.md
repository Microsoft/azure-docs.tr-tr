---
title: Şirket içi Azure AD parola koruması hakkında SSS-Azure Active Directory
description: Şirket içi Azure AD parola koruması hakkında SSS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 473fe43bb4cf18c61f30d9b7e057da888dc6da62
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167911"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Şirket içi Azure AD parola koruması-sık sorulan sorular

Bu bölümde, Azure AD parola koruması hakkında sık sorulan birçok soruya yanıtlar verilmektedir.

## <a name="general-questions"></a>Genel sorular

**S: güvenli parola seçme konusunda kullanıcılara ne tür yönergeler verilmelidir?**

Microsoft 'un bu konuyla ilgili geçerli Kılavuzu aşağıdaki bağlantıda bulunabilir:

[Microsoft parola Kılavuzu](https://www.microsoft.com/research/publication/password-guidance)

**S: şirket içi Azure AD parola koruması, genel olmayan bulutlarda destekleniyor mu?**

Şirket içi Azure AD parola koruması yalnızca genel bulutta desteklenir. Genel olmayan bulut kullanılabilirliği için hiçbir tarih duyurulmamıştır.

Azure AD portalı, genel olmayan bulutlarda bile olsa, şirket içi "Windows Server Active Directory parola koruması" yapılandırması için değişiklik yapılmasına izin verir; Bu değişiklikler kalıcı hale getirilir, aksi takdirde hiçbir zaman etkili olmayacaktır. Genel olmayan bulut kimlik bilgileri kullanıldığında şirket içi proxy aracılarının veya ormanlarının kaydı desteklenmez ve bu tür kayıt denemeleri her zaman başarısız olur.

**S: Azure AD parola koruma avantajlarını şirket içi kullanıcılarınızın bir alt kümesine nasıl uygulayabilirim?**

Desteklenmiyor. Dağıtım ve etkinleştirildikten sonra Azure AD parola koruması, tüm kullanıcılar eşit güvenlik avantajları elde etmez.

**S: parola değiştirme ve parola ayarlama (veya sıfırlama) arasındaki fark nedir?**

Parola değişikliği, bir Kullanıcı eski parola hakkında bilgi sahibi olduktan sonra yeni bir parola seçtiğinde. Örneğin, bir Kullanıcı Windows 'da oturum açtığında ve ardından yeni bir parola seçmesi istendiğinde parola değişikliği olur.

Bir parola kümesi (bazen parola sıfırlama olarak adlandırılır), bir yönetici bir hesaptaki parolayı yeni bir parolayla Değiştir, örneğin Active Directory Kullanıcılar ve bilgisayarlar Yönetim Aracı ' nı kullanmaktır. Bu işlem, yüksek düzeyde ayrıcalık (genellikle etki alanı Yöneticisi) gerektirir ve işlemi gerçekleştiren kişi genellikle eski parola hakkında bilgi sahibi değildir. Yardım Masası senaryoları genellikle parola kümeleri gerçekleştirir, örneğin, parolasını unutmuş bir kullanıcıyı öğreniyor. Ayrıca, yeni bir kullanıcı hesabı bir parola ile ilk kez oluşturulduğunda parola ayarlama olaylarını da görürsünüz.

Parola doğrulama ilkesi, parola değiştirme veya ayarlama işleminin yapılıp yapılmadığına bakılmaksızın aynı şekilde davranır. Azure AD parola koruması DC Aracısı hizmeti, parola değiştirme veya ayarlama işleminin yapılıp yapılmadığını bildirmek için farklı olayları günlüğe kaydeder.  Bkz. [Azure AD parola koruması izleme ve günlüğe kaydetme](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**S: Active Directory Kullanıcıları ve bilgisayarları Yönetimi ek bileşenini kullanarak zayıf bir parola ayarlamaya çalışırken neden yinelenen parola reddetme olayları günlüğe kaydedilir?**

Active Directory Kullanıcıları ve Bilgisayarları yönetim ek bileşeni, önce Kerberos protokolünü kullanarak yeni parolayı ayarlamaya çalışır. Hata sonrasında, ek bileşen eski (SAM RPC) protokolünü kullanarak parolayı ayarlamaya yönelik ikinci bir deneme yapar (kullanılan protokoller önemli değildir). Yeni parola Azure AD parola koruması tarafından zayıf kabul edildiğinde, bu ek bileşen davranışı iki parola sıfırlama reddetme olayı kümesine yol açar.

**S: Azure AD parola koruma parolası doğrulama olayları neden boş bir kullanıcı adıyla günlüğe kaydediliyor?**

Active Directory, örneğin [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API 'sini kullanarak etki alanının geçerli parola karmaşıklığı gereksinimlerini geçirmesinin başarılı olup olmadığını görmek için bir parolayı test etme özelliğini destekler. Bu şekilde bir parola doğrulandığında, test, Azure AD parola koruması gibi parola filtresi DLL tabanlı ürünlerin doğrulanmasını de içerir, ancak belirli bir parola filtresi dll 'sine geçirilen kullanıcı adları boş olur. Bu senaryoda, Azure AD parola koruması Şu anda etkin olan parola ilkesini kullanarak parolayı doğrular ve sonucu yakalamak için bir olay günlüğü iletisi verir, ancak olay günlüğü iletisinde boş Kullanıcı adı alanları vardır.

**S: Azure AD parola koruması 'Nın diğer parola filtresi tabanlı ürünlerle yan yana yüklenmesi destekleniyor mu?**

Evet. Birden çok kayıtlı parola filtresi dll 'leri için destek, Azure AD parola korumasına özgü olmayan bir temel Windows özelliğidir. Bir parola kabul edilmeden önce tüm kayıtlı parola filtresi dll 'leri kabul etmelidir.

**S: Azure AD parola korumasını Azure kullanmadan Active Directory ortammda nasıl dağıtırım ve yapılandırabilirim?**

Desteklenmiyor. Azure AD parola koruması, şirket içi Active Directory ortamına genişletilmesini destekleyen bir Azure özelliğidir.

**S: Active Directory düzeyinde ilkenin içeriğini nasıl değiştirebilirim?**

Desteklenmiyor. İlke yalnızca Azure AD Portalı kullanılarak yönetilebilir. Ayrıca önceki soruya bakın.

**S: SYSVOL çoğaltması için DFSR neden gereklidir?**

FRS (DFSR 'nin öncül teknolojisi) birçok bilinen soruna sahiptir ve Windows Server Active Directory 'in daha yeni sürümlerinde tümüyle desteklenmez. Azure AD parola korumasının sıfır testi, FRS tarafından yapılandırılan etki alanlarında yapılır.

Daha fazla bilgi için lütfen aşağıdaki makalelere bakın:

[SYSVOL çoğaltmasını DFSR 'ye geçirme durumu](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Son, FRS için Nigh](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Etki alanınız zaten DFSR kullanıyorsa, Azure AD parola korumasını yüklemeden önce bu uygulamayı DFSR 'yi kullanacak şekilde geçirmeniz gerekır. Daha fazla bilgi için şu bağlantıya bakın:

[SYSVOL çoğaltma geçiş kılavuzu: FRS DFS Çoğaltma](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

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

Ne yazık ki, Azure AD parola koruma proxy 'Si yazılımı tarafından yüklenen Microsoft Azure AD Connect Agent Güncelleştirici hizmeti sürümü ile [Azure Active Directory uygulama ara sunucusu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) yazılımı tarafından yüklenen hizmetin sürümü arasında bir uyumsuzluk bulundu. Bu uyumsuzluk, aracı güncelleştiricisi hizmetinin yazılım güncelleştirmeleri için Azure ile bağlantı kurabileceğinden kaynaklanabilir. Aynı makinede Azure AD parola koruma proxy ve Azure Active Directory Uygulama Ara Sunucusu yüklenmesi önerilmez.

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

## <a name="additional-content"></a>Ek içerik

Aşağıdaki bağlantılar çekirdek Azure AD parola koruma belgelerinin bir parçası değildir, ancak bu özellik hakkında yararlı bir ek bilgi kaynağı olabilir.

[Azure AD parola koruması genel kullanıma sunuldu!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-posta sızdırma koruma Kılavuzu – 15. Bölüm: Microsoft Azure AD parola koruma hizmetini uygulama (Şirket Içi için çok!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD parola koruması ve akıllı kilitleme artık genel önizlemede!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified destek eğitimi kullanılabilir

Azure AD parola koruması hakkında daha fazla bilgi edinmek ve ortamınıza dağıtmak istiyorsanız, Premier veya Birleşik destek sözleşimiyle bu müşterilere sunulan Microsoft proaktif bir hizmetten yararlanabilirsiniz. Hizmet Azure Active Directory: parola koruması olarak adlandırılır. Daha fazla bilgi için teknik hesap yöneticinize başvurun.

## <a name="next-steps"></a>Sonraki adımlar

Burada yanıtlanmayan bir şirket içi Azure AD parola koruma sorunuz varsa, bir geri bildirim öğesi gönderin; teşekkürler!

[Azure AD parola korumasını dağıtma](howto-password-ban-bad-on-premises-deploy.md)
