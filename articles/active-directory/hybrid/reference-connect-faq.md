---
title: Azure Active Directory Connect SSS-| Microsoft Docs
description: Bu makale Azure AD Connect hakkında sık sorulan soruları yanıtlar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dbc7f8068ed84f42ec41ebd969e0aa91ffbb264
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473317"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect SSS

## <a name="general-installation"></a>Genel yükleme

**S: güvenlik saldırısı yüzeyini azaltmak için Azure AD Connect sunucudan nasıl HARG olabilirim?**

Microsoft, BT ortamınızın bu kritik bileşeninin güvenlik saldırısı yüzeyini azaltmak için Azure AD Connect sunucunuzu sağlamlaştırmanızı önerir.  Aşağıdaki önerilerin ardından kuruluşunuza yönelik güvenlik riskleri azalır.

* Etki alanına katılmış bir sunucuda Azure AD Connect dağıtma ve etki alanı yöneticilerine veya daha sıkı denetlenen güvenlik gruplarına yönetici erişimini kısıtlama

Daha fazla bilgi için bkz: 

* [Yöneticiler gruplarının güvenliğini sağlama](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Yerleşik yönetici hesaplarının güvenliğini sağlama](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Saldırı yüzeylerini azaltarak güvenlik iyileştirmesi ve sürdürülebilirlik](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Active Directory saldırı yüzeyini azaltma](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**S: Azure Active Directory (Azure AD) genel yöneticisinin iki öğeli kimlik doğrulaması (2FA) etkinse, yükleme işi yapılacak.**  
Şubat 2016 Derlemeleriyle, bu senaryo desteklenir.

**S: Azure AD Connect katılımsız olarak yüklemek için bir yol var mı?**  
Azure AD Connect yükleme yalnızca yükleme sihirbazını kullandığınızda desteklenir. Katılımsız, sessiz yükleme desteklenmez.

**S: bir etki alanına başvurmayan bir ormanım var. Nasıl yaparım? Azure AD Connect yüklensin mi?**  
Şubat 2016 Derlemeleriyle, bu senaryo desteklenir.

**S: Azure Active Directory Domain Services (Azure AD DS) sistem durumu Aracısı sunucu çekirdeği üzerinde çalışıyor mu?**  
Evet. Aracıyı yükledikten sonra, aşağıdaki PowerShell cmdlet 'ini kullanarak kayıt işlemini tamamlayabilirsiniz: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**S: iki etki alanından bir Azure AD ile eşitleme Azure AD Connect destekler mi?**  
Evet, bu senaryo desteklenir. [Birden çok etki alanına](how-to-connect-install-multiple-domains.md)bakın.
 
**S: Azure AD Connect aynı Active Directory etki alanı için birden çok bağlayıcı olabilir mi?**  
Hayır, aynı AD etki alanı için birden fazla bağlayıcı desteklenmez. 

**S: Azure AD Connect veritabanını yerel veritabanından uzak SQL Server örneğine taşıyabilir miyim?**    
Evet, aşağıdaki adımlar bunu nasıl yapabileceğine ilişkin genel yönergeler sağlar. Şu anda daha ayrıntılı bir belge üzerinde çalışıyoruz.
1. LocalDB ADSync veritabanını yedekleyin.
Bunu yapmanın en kolay yolu, Azure AD Connect aynı makinede yüklü SQL Server Management Studio kullanmaktır. *(LocalDB) .\ADSync*öğesine bağlanın ve ardından ADSync veritabanını yedekleyin.

2. ADSync veritabanını uzak SQL Server örneğine geri yükleyin.

3. Azure AD Connect mevcut [uzak SQL veritabanına](how-to-connect-install-existing-database.md)karşı yükler.
   Makalesinde yerel bir SQL veritabanı kullanılarak nasıl geçiş yapılacağı gösterilir. ' I uzak bir SQL veritabanı kullanarak geçiriyorsanız, işlemin 5. adımında, Windows eşitleme hizmeti 'nin çalışacağı mevcut bir hizmet hesabını da girmeniz gerekir. Bu eşitleme altyapısı hizmet hesabı aşağıda açıklanmıştır:
   
      **Mevcut bir hizmet hesabını kullan**: varsayılan olarak, Azure AD Connect eşitleme hizmetlerinin kullanması için bir sanal hizmet hesabı kullanır. Uzak bir SQL Server örneği kullanıyorsanız veya kimlik doğrulaması gerektiren bir proxy kullanıyorsanız, bir yönetilen hizmet hesabı veya etki alanında bir hizmet hesabı kullanın ve parolayı öğrenin. Bu gibi durumlarda kullanılacak olan hesabı girin. Yüklemeyi çalıştıran kullanıcıların, hizmet hesabı için oturum açma kimlik bilgilerinin oluşturulabilmesi için SQL 'de sistem yöneticileri olduğundan emin olun. Daha fazla bilgi için bkz. [Azure AD Connect hesapları ve izinleri](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      En son sürümle, veritabanını sağlama, artık SQL yöneticisi tarafından bant dışında gerçekleştirilebilir ve ardından veritabanı sahibi haklarıyla Azure AD Connect yöneticisi tarafından yüklenebilir. Daha fazla bilgi için bkz. [SQL Temsilcili yönetici izinleri kullanarak Azure AD Connect yüklemesi](how-to-connect-install-sql-delegation.md).

Şeyleri basit tutmak için, Azure AD Connect yükleyen kullanıcıların SQL 'de sistem yöneticileri olması önerilir. Ancak, son derlemeler sayesinde, [SQL yönetici temsilcisi izinlerini kullanarak Azure AD Connect yüklemesi](how-to-connect-install-sql-delegation.md)bölümünde açıklandığı gibi, artık atanmış SQL yöneticileri kullanabilirsiniz.

**S: alandan en iyi uygulamalardan bazıları nelerdir?**  

Aşağıda, mühendislik, destek ve danışmanlarımızın yıllarca geliştirildiği en iyi uygulamalardan bazılarını sunan bilgilendirici bir belgedir.  Bu, hızlı bir şekilde başvurulabilen bir madde işareti listesinde sunulmaktadır.  Bu liste kapsamlı bir hale getirmeye çalışsa da, listede henüz yapılmamış olabilecek ek en iyi uygulamalar olabilir.

- Tam SQL kullanılıyorsa, yerel ve uzak kalacak
    - Daha az sıçrama
    - Sorun gidermek daha kolay
    - Daha az karmaşıklık
    - Kaynakları SQL 'e atamanız ve Azure AD Connect ve işletim sistemi için ek yüke izin vermeniz gerekir
- Tüm mümkünse proxy 'yi atla, proxy 'yi atlayadıysanız, zaman aşımı değerinin 5 dakikadan fazla olduğundan emin olmanız gerekir.
- Proxy gerekliyse, proxy 'yi Machine. config dosyasına eklemeniz gerekir
- Yerel SQL işlerinin ve bakımın yanı sıra Azure AD Connect-özellikle yeniden dizin oluşturma işlemlerini nasıl etkileyeceğini göz önünde bulundurun
- DNS 'in dışarıdan çözümleyebileceğinden emin olun
- Fiziksel veya sanal sunucuları kullanıp kullanmayacağınızı [sunucu belirtimlerinin](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) öneri başına olduğundan emin olun
- Gerekli kaynakların ayrılmış olduğu bir sanal sunucu kullanıyorsanız emin olun
- Disk ve disk yapılandırmasının SQL Server için En Iyi uygulamaları karşıladığından emin olun
- İzleme için Azure AD Connect Health yükleyip yapılandırın
- Azure AD Connect yerleşik olarak bulunan silme eşiğini kullanın.
- Tüm değişiklikler ve eklenebilir yeni öznitelikler için hazırlanmaya yönelik sürüm güncelleştirmelerini dikkatle gözden geçirin
- Her şeyi yedekle
    - Yedekleme anahtarları
    - Yedekleme eşitleme kuralları
    - Yedekleme sunucusu yapılandırması
    - SQL veritabanını yedekle
- SQL VSS Yazıcı olmadan SQL 'i yedekleyen 3. taraf yedekleme Aracısı olmadığından emin olun (3. taraf anlık görüntülerle sanal sunucularda ortak)
- Karmaşıklık ekleme için kullanılan özel eşitleme kuralları miktarını sınırlayın
- Azure AD Connect sunucularını katman 0 sunucuları olarak değerlendir
- Bulut eşitleme kurallarının etkisini ve doğru iş sürücülerini önemli ölçüde anlayabilmeksizin değiştirme
- Azure AD Connect ve Azure AD Connect Health desteği için doğru URL 'nin ve güvenlik duvarı bağlantı noktalarının açık olduğundan emin olun
- Hayalet nesneleri sorunlarını gidermek ve engellemek için, bulut filtrelenmiş özniteliğiyle yararlanın
- Hazırlama sunucusu ile sunucular arasında tutarlılık için Azure AD Connect yapılandırma Belgeno ' nı kullandığınızdan emin olun
- Hazırlama sunucuları ayrı veri merkezlerinde (fiziksel konumlar) olmalıdır
- Hazırlama sunucularının yüksek kullanılabilirlik çözümü olması amaçlıyordu, ancak birden fazla hazırlama sunucunuz olabilir
- "Gecikme" hazırlama sunucularına giriş, hata durumunda bazı olası kapalı kalma sürelerini azaltabileceği için
- Önce hazırlama sunucusundaki tüm yükseltmeleri test edin ve doğrulayın
- Hazırlama sunucusun üzerinde geçiş yapmadan önce her zaman dışarı aktarmaları doğrula, tam Içeri aktarmalar için hazırlama sunucusundan yararlanın ve iş etkisini azaltmak için tam eşitlemeler
- Azure AD Connect sunucular arasında sürüm tutarlılığını mümkün olduğunca koru 

**S: Azure AD Connect çalışma grubu makinesinde Azure AD bağlayıcı hesabı oluşturmasına izin verebilir miyim?**
Hayır.  Azure AD Connect Azure AD Bağlayıcısı hesabını otomatik olarak oluşturmasına izin vermek için, makinenin etki alanına katılmış olması gerekir.  

## <a name="network"></a>Ağ
**S: bir güvenlik duvarım, ağ cihazım veya bağlantının ağımdaki açık kalabileceği süreyi sınırlayan başka bir şeydir. Azure AD Connect kullandığım zaman istemci tarafı zaman aşımı eşiğim ne olur?**  
Tüm ağ yazılımı, fiziksel cihazlar veya bağlantıların açık kalabileceği en uzun süreyi sınırlayan herhangi bir şey, Azure AD Connect istemcisinin yüklendiği sunucu arasındaki bağlantı için en az beş dakika (300 saniye) eşiğini kullanmalıdır ve Azure Active Directory. Bu öneri, daha önce yayınlanan tüm Microsoft Kimlik eşitlemesi araçları için de geçerlidir.

**S: tek etiketli etki alanları (SLDs) destekleniyor mu?**  
Tek düzeyli etki alanı için ağ yapılandırması doğru şekilde çalıştığı sürece, bu ağ yapılandırmasına karşı ([bkz. makaleye bakın](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), tek etiketli bir etki alanıyla Azure AD Connect eşitleme kullanılması desteklenir.

**S: kopuk AD etki alanları olan ormanlar destekleniyor mu?**  
Hayır, Azure AD Connect ayrık ad alanları içeren şirket içi ormanları desteklemez.

**S: "noktalı" NetBIOS adları destekleniyor mu?**  
Hayır, Azure AD Connect NetBIOS adının bir nokta (.) içerdiği şirket içi ormanları veya etki alanlarını desteklemez.

**S: saf IPv6 ortamı destekleniyor mu?**  
Hayır, Azure AD Connect saf IPv6 ortamını desteklemez.

**Q:ı Çok ormanlı bir ortama ve iki orman arasındaki ağa NAT (ağ adresi çevirisi) kullanılıyor. Bu iki ormanla desteklenen Azure AD Connect kullanıyor mu?**</br>
Hayır, NAT üzerinden Azure AD Connect kullanılması desteklenmez. 

## <a name="federation"></a>Federasyon
**S: Office 365 sertifikamı yenilediğimi soran bir e-posta alırsanız ne yapmalıyım?**  
Sertifikayı yenileme hakkında rehberlik için bkz. [sertifikaları yenileme](how-to-connect-fed-o365-certs.md).

**S: Office 365 bağlı olan taraf için "bağlı olan taraf otomatik olarak güncelleştir" kümesini aldım. Belirteç imzalama sertifikamın üzerine otomatik olarak kayıt yapması durumunda herhangi bir işlem yapmam gerekir mi?**  
[Sertifikaları yenileme](how-to-connect-fed-o365-certs.md)makalesinde özetlenen kılavuzu kullanın.

## <a name="environment"></a>Ortam
**S: Azure AD Connect yüklendikten sonra sunucunun yeniden adlandırılması destekleniyor mu?**  
Hayır. Sunucu adının değiştirilmesi, eşitleme altyapısının SQL veritabanı örneğine bağlanamamasıyla bağlantı kuramıyor ve hizmet başlatılamıyor.

**S: bir sonraki nesil şifreleme (NGC) eşitleme kuralları FIPS özellikli bir makinede destekleniyor mu?**  
Hayır.  Bunlar desteklenmez.

**S. Azure portal eşitlenmiş bir cihazı (örneğin: HASıFATı) devre dışı bırakırsanız, neden yeniden etkinleştirilmiştir?**<br>
Eşitlenmiş cihazlar şirket içinde yazılmış veya ana kopyalı olabilir. Eşitlenmiş bir cihaz şirket içinde etkinleştirilirse, daha önce bir yönetici tarafından devre dışı bırakılmış olsa bile Azure portal yeniden etkinleştirilebilir. Eşitlenmiş bir cihazı devre dışı bırakmak için, bilgisayar hesabını devre dışı bırakmak üzere şirket içi Active Directory kullanın.

**Soru-cevap kullanıcılar için Office 365 veya Azure AD portalında Kullanıcı oturum açmayı engellediğimde, yeniden oturum açmak için neden engellenmemiş?**<br>
Eşitlenen kullanıcılar şirket içinde yazılabilir veya ana kopyalı olabilir. Hesap şirket içinde etkinleştirilirse, yönetici tarafından yerleştirilmiş oturum açma bloğunun engellemesini kaldırabilir.

## <a name="identity-data"></a>Kimlik verileri
**S: Azure AD 'deki userPrincipalName (UPN) özniteliği şirket içi UPN ile neden eşleşmiyor?**  
Daha fazla bilgi için şu makalelere bakın:

* [Office 365, Azure veya Intune 'daki Kullanıcı adları şirket içi UPN veya alternatif oturum açma KIMLIĞIYLE eşleşmez](https://support.microsoft.com/kb/2523192)
* [Kullanıcı hesabının UPN 'sini farklı bir Federasyon etki alanı kullanacak şekilde değiştirdikten sonra değişiklikler Azure Active Directory eşitleme aracı tarafından eşitlenmedi](https://support.microsoft.com/kb/2669550)

Azure AD 'yi, eşitleme altyapısının [Azure AD Connect eşitleme hizmeti özellikleri](how-to-connect-syncservice-features.md)bölümünde açıklandığı gıbı, UPN 'yi güncelleştirmesine izin verecek şekilde de yapılandırabilirsiniz.

**S: şirket içi bir Azure AD grubu veya var olan bir Azure AD grubu ya da ilgili kişi nesnesiyle yazılım ile eşleştirmek için destekleniyor mu?**  
Evet, bu yumuşak eşleşme proxyAddress tabanlıdır. Posta etkin olmayan gruplar için geçici eşleştirme desteklenmez.

**S: var olan bir Azure AD grubunda ImmutableID özniteliğini el ile ayarlamak veya bir şirket içi Azure AD grubu veya ilgili kişi nesnesiyle sabit eşleşmesi için iletişim nesnesi mi istiyorsunuz?**  
Hayır, var olan bir Azure AD grubunda veya ilgili kişi nesnesinde ImmutableID özniteliğini elle ayarlama işlemi şu anda desteklenmiyor.

## <a name="custom-configuration"></a>Özel yapılandırma
**S: Azure AD Connect için PowerShell cmdlet 'leri nerede belgelenmiştir?**  
Bu sitede belgelenen cmdlet 'ler dışında, Azure AD Connect ' de bulunan diğer PowerShell cmdlet 'leri müşteri kullanımı için desteklenmez.

**S: yapılandırmayı sunucular arasında taşımak için Synchronization Service Manager bulunan "sunucu dışarı aktarma/sunucu içeri aktarma" seçeneğini kullanabilir miyim?**  
Hayır. Bu seçenek, tüm yapılandırma ayarlarını almaz ve kullanılmamalıdır. Bunun yerine, ikinci sunucuda temel yapılandırmayı oluşturmak için Sihirbazı kullanın ve sunucular arasında özel bir kural taşımak üzere PowerShell betikleri oluşturmak için eşitleme kuralı düzenleyicisini kullanın. Daha fazla bilgi için bkz. [esnek geçiş](how-to-upgrade-previous-version.md#swing-migration).

**S: Azure oturum açma sayfası için parolalar önbelleğe alınabilir ve bu önbelleğe alma, *AutoComplete = "false"* özniteliğine sahip bir parola girişi öğesi içerdiği için önlenebilir mi?**  
Şu anda, otomatik tamamlama etiketi de dahil olmak üzere **parola** alanının HTML özniteliklerini değiştirme desteklenmez. Şu anda özel JavaScript 'e izin veren bir özellik üzerinde çalışıyoruz. Bu, **parola** alanına herhangi bir öznitelik eklemenize olanak tanır.

**S: Azure oturum açma sayfası, daha önce başarıyla oturum açmış kullanıcıların kullanıcı adlarını görüntüler. Bu davranış kapalı olabilir mi?**  
Şu anda, otomatik tamamlama etiketi de dahil olmak üzere **parola** GIRIŞI alanının HTML özniteliklerini değiştirme desteklenmez. Şu anda özel JavaScript 'e izin veren bir özellik üzerinde çalışıyoruz. Bu, **parola** alanına herhangi bir öznitelik eklemenize olanak tanır.

**S: eşzamanlı oturumları önlemenin bir yolu var mı?**  
Hayır.

## <a name="auto-upgrade"></a>Otomatik yükseltme

**S: otomatik yükseltmeyi kullanmanın avantajları ve sonuçları nelerdir?**  
Azure AD Connect yüklemeleri için otomatik yükseltmeyi etkinleştirmek üzere tüm müşterileri inceleyeceğiz. Avantajı, Azure AD Connect bulunan güvenlik açıklarına yönelik güvenlik güncelleştirmeleri de dahil olmak üzere her zaman en son düzeltme eklerini almanızı öneririz. Yükseltme işlemi çok daha azdır ve yeni bir sürüm kullanılabilir duruma geldiğinde otomatik olarak gerçekleşir. Birçok binlerce Azure AD Connect müşterisi, her yeni sürümde otomatik yükseltmeyi kullanır.

Otomatik yükseltme işlemi her zaman önce bir yüklemenin otomatik yükseltme için uygun olup olmadığını belirler. Uygun değilse, yükseltme gerçekleştirilir ve test edilir. İşlem ayrıca kurallara ve belirli ortam faktörlerine özel değişiklikler için arama de içerir. Testler bir yükseltmenin başarısız olduğunu göstermediğinde, önceki sürüm otomatik olarak geri yüklenir.

Ortamın boyutuna bağlı olarak, işlem birkaç saat sürebilir. Yükseltme devam ederken, Windows Server Active Directory ile Azure AD arasında eşitleme gerçekleşmez.

**S: otomatik yükseltmem artık çalışmadığını söyleyen bir e-posta aldım ve yeni bir sürüm yüklemem gerekiyor. Neden bunu yapmam gerekir?**  
Son yıl, belirli koşullar altında, sunucunuzdaki otomatik yükseltme özelliğini devre dışı bırakmış olabilecek bir Azure AD Connect sürümü yayımladık. Azure AD Connect Version 1.1.750.0 'daki sorunu düzelttik. Sorundan etkileniyorsanız, bunu onarmak için bir PowerShell betiği çalıştırarak veya Azure AD Connect en son sürümüne el ile yükselterek sorunu azaltabilirsiniz. 

PowerShell betiğini çalıştırmak için [betiği indirip](https://aka.ms/repairaadconnect) Azure AD Connect sunucunuzda bir yönetim PowerShell penceresinde çalıştırın. Betiği çalıştırmayı öğrenmek için [Bu kısa videoyu görüntüleyin](https://aka.ms/repairaadcau).

El ile yükseltmek için AADConnect. msi dosyasının en son sürümünü indirmeniz ve çalıştırmanız gerekir.
 
-  Geçerli sürümünüz 1.1.750.0 ' den eskiyse, [en son sürüme indirip yükseltin](https://www.microsoft.com/download/details.aspx?id=47594).
- Azure AD Connect sürümünüz 1.1.750.0 veya üzeri ise başka bir eylem gerekmez. Otomatik yükseltme düzeltmesini içeren sürümü zaten kullanıyorsunuz. 

**S: otomatik yükseltmeyi yeniden etkinleştirmek için en son sürüme yükseltmeme bildiren bir e-posta aldım. 1.1.654.0 sürümünü kullanıyorum. Yükseltmem gerekiyor mu?**  
Evet, otomatik yükseltmeyi yeniden etkinleştirmek için sürüm 1.1.750.0 veya sonraki bir sürüme yükseltmeniz gerekir. [' İ indirin ve en son sürüme yükseltin](https://www.microsoft.com/download/details.aspx?id=47594).

**S: otomatik yükseltmeyi yeniden etkinleştirmek için en son sürüme yükseltmeme bildiren bir e-posta aldım. Otomatik yükseltmeyi etkinleştirmek için PowerShell 'i kullandım, hala en son sürümü yüklemem gerekir mi?**  
Evet, hala sürüm 1.1.750.0 veya sonraki sürüme yükseltmeniz gerekir. PowerShell ile otomatik yükseltme hizmetini etkinleştirmek, 1.1.750.0 'ten önceki sürümlerde bulunan otomatik yükseltme sorununu azaltmaz.

**S: daha yeni bir sürüme yükseltmek istiyorum, ancak Azure AD Connect kim yükledim ve Kullanıcı adı ve parola yok. Bunun için gerekli midir?**
Azure AD Connect yükseltmek için başlangıçta kullanılan Kullanıcı adını ve parolayı bilmeniz gerekmez. Genel yönetici rolüne sahip herhangi bir Azure AD hesabını kullanın.

**S: hangi Azure AD Connect sürümünü kullandığım hakkında nasıl öğrenebilirim?**  
Sunucunuza hangi Azure AD Connect sürümünün yüklendiğini doğrulamak için, Denetim Masası ' na gidin ve aşağıda gösterildiği gibi **programlar** > **Programlar ve Özellikler**' i seçerek Microsoft Azure AD Connect ' in yüklü sürümünü bulun:

![Denetim Masası 'nda Azure AD Connect sürümü](./media/reference-connect-faq/faq1.png)

**S: Nasıl yaparım? Azure AD Connect en son sürümüne yükseltme yapılsın mı?**  
En son sürüme yükseltme hakkında bilgi edinmek için bkz. [Azure AD Connect: önceki bir sürümden en son sürüme yükseltme](how-to-upgrade-previous-version.md). 

**S: son yılın Azure AD Connect en son sürümüne zaten yükseltiyoruz. Yeniden yükseltmeniz gerekiyor mu?**  
Azure AD Connect ekibi, hizmette sık sık güncelleştirme yapar. Hata düzeltmelerinin ve güvenlik güncelleştirmelerinin yanı sıra yeni özelliklerden yararlanmak için, en son sürümle sunucunuzu güncel tutmak önemlidir. Otomatik yükseltmeyi etkinleştirirseniz, yazılım sürümünüz otomatik olarak güncelleştirilir. Azure AD Connect sürüm sürümü geçmişini bulmak için, bkz. [Azure AD Connect: sürüm yayınlama geçmişi](reference-connect-version-history.md).

**S: yükseltmeyi gerçekleştirmek için ne kadar sürer ve Kullanıcılarım üzerinde ne etkisi vardır?**  
Yükseltme için gereken süre, kiracı boyutınıza bağlıdır. Daha büyük kuruluşlar için, yükseltmenin akşam veya hafta sonu içinde yapılması en iyi yöntem olabilir. Yükseltme sırasında, hiçbir eşitleme etkinliği gerçekleşmez.

**S: Azure AD Connect yükseltilip yükseltildiğimde, ancak Office portalı hala DirSync 'e değindim. Bunun nedeni nedir?**  
Office ekibi, Office portalı güncelleştirmelerini almak için geçerli ürün adını yansıtacak şekilde çalışmaktadır. Hangi eşitleme aracının kullandığınızı yansıtmaz.

**S: otomatik yükseltme durumum "askıya alındı" diyor. Neden askıya alındı? Bunu etkinleştirmem gerekir mi?**  
Bir hata, belirli koşullarda, otomatik yükseltme durumunun "askıya alındı" olarak ayarlanmış olduğu önceki bir sürümde tanıtılmıştır. BT 'nin el ile etkinleştirilmesi teknik açıdan olasıdır, ancak birkaç karmaşık adım gerektirir. Yapabileceğiniz en iyi şey Azure AD Connect en son sürümünü yükler.

**S: şirketim kesin değişiklik yönetimi gereksinimlerine sahiptir ve ne zaman dışarı itiltiğini denetlemek istiyorum. Otomatik yükseltmenin ne zaman başlatılabildiğini denetleyebilir miyim?**  
Hayır, bugün böyle bir özellik yok. Özellik gelecek bir sürüm için değerlendiriliyor.

**S: otomatik yükseltme başarısız olursa bir e-posta alacak mıyım? Başarılı olduğunu nasıl anlarım?**  
Yükseltmenin sonucu hakkında bilgilendirilmeyecektir. Özellik gelecek bir sürüm için değerlendiriliyor.

**S: Otomatik yükseltmeleri göndermeyi planlarken bir zaman çizelgesi yayımladığınızı mı istiyorsunuz?**  
Otomatik yükseltme, daha yeni bir sürümün yayın işlemindeki ilk adımdır. Yeni bir sürüm olduğunda, yükseltmeler otomatik olarak gönderilir. Azure AD Connect daha yeni sürümleri, [Azure AD yol haritasında](../fundamentals/whats-new.md)önceden duyurulmuştur.

**S: otomatik yükseltme Azure AD Connect Health de Yükselt mi?**  
Evet, otomatik yükseltme Azure AD Connect Health de yükseltir.

**S: Ayrıca, hazırlama modunda Azure AD Connect sunucularını otomatik olarak yükseltirsiniz misiniz?**  
Evet, hazırlama modundaki bir Azure AD Connect sunucusunu otomatik olarak yükseltebilirsiniz.

**S: otomatik yükseltme başarısız olursa ve Azure AD Connect sunucum başlamazsa, ne yapmam gerekir?**  
Nadir durumlarda Azure AD Connect hizmeti, yükseltmeyi gerçekleştirdikten sonra başlatılmaz. Bu durumlarda, sunucuyu yeniden başlatmak genellikle sorunu düzeltir. Azure AD Connect hizmeti hala başlamazsa, bir destek bileti açın. Daha fazla bilgi için bkz. [Office 365 desteğiyle iletişim kurmak için hizmet Isteği oluşturma](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**S: Azure AD Connect daha yeni bir sürüme yükseltdiğimde risklerden emin değilim. Yükseltmeye yardım etmek için beni çağırabilir miyim?**  
Azure AD Connect daha yeni bir sürüme yükseltme için yardıma ihtiyacınız varsa, [hizmet oluşturma isteği sırasında Office 365 desteği 'ne başvurmak için](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)bir destek bileti açın.

## <a name="troubleshooting"></a>Sorun giderme
**S: Azure AD Connect nasıl yardım alabilirim?**

[Microsoft Bilgi Bankası 'Nda (KB) arama](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Azure AD Connect desteğiyle ilgili sık karşılaşılan sorunları gidermek için KB 'de teknik çözümler arayın.

[Azure Active Directory forumları](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* [Azure AD Community](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)'ye giderek Teknik sorular ve yanıtlar arayın veya kendi sorularınızı sorun.

[Azure AD için destek alın](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**S: eşitleme adımı hatalarından sonra neden 6311 ve 6401 olaylarını görüyorum?**

Olaylar 6311- **sunucu, geri çağırma işlemi gerçekleştirirken beklenmeyen bir hatayla karşılaştı** ve 6401- **Yönetim aracısı denetleyicisi beklenmeyen bir hatayla karşılaştı** -eşitleme adımı hatasından sonra her zaman günlüğe kaydedilir. Bu hataları gidermek için, eşitleme adımı hatalarını temizlemeniz gerekir.  Daha fazla bilgi için bkz. [eşitleme sırasında hata giderme](tshoot-connect-sync-errors.md) ve [Azure AD Connect eşitleme Ile nesne eşitleme sorunlarını giderme](tshoot-connect-objectsync.md)
