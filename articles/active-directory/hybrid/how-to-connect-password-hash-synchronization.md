---
title: Azure AD Connect Sync ile parola karması eşitlemesini uygulama | Microsoft Docs
description: Parola karma eşitlemesinin nasıl çalıştığı ve nasıl ayarlanacağı hakkında bilgi sağlar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee22ba3816e667bc58247fa81142e54587124fd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865306"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect eşitlemesi ile parola karması eşitlemeyi uygulama
Bu makale, Kullanıcı parolalarınızı şirket içi Active Directory örneğinden bulut tabanlı Azure Active Directory (Azure AD) örneğine eşitlemeniz için gereken bilgileri sağlar.

## <a name="how-password-hash-synchronization-works"></a>Parola karması eşitleme nasıl çalışır?
Active Directory etki alanı hizmeti, parolaları asıl Kullanıcı parolasının karma değer temsili biçiminde depolar. Karma değeri, tek yönlü matematik işlevinin ( *karma algoritma*) bir sonucudur. Tek yönlü işlevin sonucunu, parolanın düz metin sürümüne döndürmek mümkün değildir. 

Parolanızı eşitlemek için Azure AD Connect eşitleme, parola karmalarınızı şirket içi Active Directory örneğinden ayıklar. Ek güvenlik işlemesi, Azure Active Directory kimlik doğrulama hizmetiyle eşitlenmeden önce parola karmasından uygulanır. Parolalar kullanıcı bazında ve kronolojik sırada eşitlenir.

Parola karması eşitleme işleminin gerçek veri akışı, Kullanıcı verilerinin eşitlenmesine benzerdir. Ancak, parolalar diğer özniteliklerin standart dizin eşitleme penceresinden daha sık eşitlenir. Parola karması eşitleme işlemi her 2 dakikada bir çalışır. Bu işlemin sıklığını değiştiremezsiniz. Bir parolayı eşitlediğinizde, mevcut bulut parolasının üzerine yazar.

Parola karması eşitleme özelliğini ilk kez etkinleştirdiğinizde, tüm kapsamdaki kullanıcıların parolalarının ilk eşitlemesini gerçekleştirir. Eşitlenmesi istediğiniz kullanıcı parolalarının bir alt kümesini açıkça tanımlayamazsınız. Ancak, birden çok bağlayıcı varsa, bazı bağlayıcılar için parola karma eşitlemesini devre dışı bırakmak mümkündür ancak [set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) cmdlet 'ini kullanarak diğerlerine uygulanmaz.

Şirket içi bir parolayı değiştirdiğinizde, güncelleştirilmiş parola genellikle birkaç dakika içinde eşitlenir.
Parola karması eşitleme özelliği başarısız eşitleme girişimlerini otomatik olarak yeniden dener. Parolayı eşitlemeye çalışırken bir hata oluşursa, olay görüntüleyiciniz bir hata günlüğe kaydedilir.

Bir parolanın eşitlenmesi, şu anda oturum açmış olan kullanıcıyı etkilemez.
Geçerli bulut hizmeti oturumunuz, bir bulut hizmetinde oturum açtığınızda oluşan eşitlenmiş bir parola değişikliğinden hemen etkilenmemiştir. Ancak, bulut hizmeti yeniden kimlik doğrulaması yapmanızı gerektirdiğinde, yeni parolanızı sağlamanız gerekir.

Şirket ağında oturum açmış olup olmadıklarına bakılmaksızın kullanıcının Azure AD kimlik doğrulaması için şirket kimlik bilgilerini ikinci kez girmesi gerekir. Bu model, oturum açma sırasında Kullanıcı oturum açanlar (KMSı) onay kutusunu seçerse, simge durumuna küçültülmüş olabilir. Bu seçim, 180 gün için kimlik doğrulamasını atlayan bir oturum tanımlama bilgisi ayarlar. KMSı davranışı, Azure AD yöneticisi tarafından etkinleştirilebilir veya devre dışı bırakılabilir. Ayrıca, şirket ağınıza bağlı şirket cihazlarındaki kullanıcıları otomatik olarak imzalayan [sorunsuz SSO](how-to-connect-sso.md)'yu etkinleştirerek parola istemlerini azaltabilirsiniz.

> [!NOTE]
> Parola eşitleme yalnızca Active Directory nesne türü kullanıcı için desteklenir. InetOrgPerson nesne türü için desteklenmez.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Parola karması eşitlemesinin nasıl çalıştığına ilişkin ayrıntılı açıklama

Aşağıdaki bölümde, parola karma eşitlemesinin Active Directory ile Azure AD arasında nasıl çalıştığı ayrıntılı bir şekilde açıklanmaktadır.

![Ayrıntılı parola akışı](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Her iki dakikada bir AD Connect sunucusundaki Parola karması eşitleme Aracısı, bir DC 'den depolanan parola karmalarını (unicodePwd özniteliği) ister.  Bu istek, DC 'Ler arasında verileri eşzamanlı hale getirmek için kullanılan standart [MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) çoğaltma protokolü aracılığıyla yapılır. Hizmet hesabı, parola karmalarını almak için Dizin değişikliklerini çoğaltma ve Dizin değişikliklerini çoğaltma (yüklemede varsayılan olarak verilir) olmalıdır.
2. Göndermeden önce, DC, RPC oturum anahtarının [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) karması ve bir güvenlik alanı olan bir anahtar kullanarak MD4 parola karmasını şifreler. Ardından, sonucu RPC üzerinden parola karması eşitleme aracısına gönderir. DC, DC çoğaltma protokolünü kullanarak da güvenlik alanı 'nı eşitleme aracısına geçirir, bu nedenle, aracı zarfın şifresini çözebilecektir.
3. Parola karması eşitleme aracısının şifreli zarfı olduktan sonra, alınan verilerin şifresinin çözülmesi için bir anahtar oluşturmak üzere [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) kullanır ve bu ANAHTARıN özgün MD4 biçimine geri dönmesi için anahtarı oluşturur. Parola karması eşitleme aracısının hiçbir zaman şifresiz metin parolasına erişimi yoktur. Parola karması eşitleme aracısının MD5 kullanımı, DC ile çoğaltma protokolü uyumluluğu için kesinlikle ve yalnızca DC ile parola karması eşitleme Aracısı arasında kullanılır.
4. Parola karması eşitleme Aracısı, önce karmayı 32 baytlık bir onaltılı dizeye dönüştürerek 16 baytlık ikili parola karmasını 64 bayta genişletir ve ardından bu dizeyi UTF-16 kodlaması ile tekrar ikiliye dönüştürür.
5. Parola karması eşitleme Aracısı, özgün karmayı daha fazla korumak için 10 baytlık uzunluğa sahip bir 2. ve 64 baytlık ikiliye sahip Kullanıcı başına bir anahtar ekler.
6. Parola karması eşitleme Aracısı daha sonra MD4 karmasını ve Kullanıcı başına anahtar olarak birleştirir ve [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) işlevine giriş yapın. [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256) anahtarlı karma algoritmasının 1000 yinelemesi kullanılır. 
7. Parola karması eşitleme Aracısı elde edilen 32 baytlık karma değeri alır, hem Kullanıcı başına güvenlik düzeyini hem de SHA256 yineleme sayısını (Azure AD tarafından kullanılmak üzere) birleştirir, ardından Azure AD Connect dizeyi TLS üzerinden Azure AD 'ye iletir.</br> 
8. Bir Kullanıcı Azure AD 'de oturum açmaya çalıştığında ve parolasını girerse, parola aynı MD4 + anahtar + PBKDF2 + HMAC-SHA256 işlemi üzerinden çalıştırılır. Elde edilen karma, Azure AD 'de depolanan karma ile eşleşiyorsa, Kullanıcı doğru parolayı girdikten sonra kimliği doğrulanır.

> [!NOTE]
> Özgün MD4 karması Azure AD 'ye aktarılmaz. Bunun yerine, özgün MD4 karmasının SHA256 karması iletilir. Sonuç olarak, Azure AD 'de depolanan karma değer elde edilmişse, şirket içi bir karma geçişi saldırısında kullanılamaz.

### <a name="security-considerations"></a>Güvenlik konuları

Parolaları eşitlerken, parolanızın düz metin sürümü Parola karması eşitleme özelliğine, Azure AD 'ye veya ilişkili hizmetlerden herhangi birine gösterilmez.

Kullanıcı kimlik doğrulaması, kuruluşun kendi Active Directory örneğine göre değil, Azure AD 'ye karşı gerçekleşir. Azure AD 'de depolanan SHA256 parola verileri--özgün MD4 karmasının karma değeri, Active Directory depolanmadan daha güvenlidir. Ayrıca, bu SHA256 karmasının şifresi çözülemediğinden, kuruluşun Active Directory ortamına geri getirilemiyor ve karma geçişi saldırısında geçerli bir Kullanıcı parolası olarak sunulabilir.

### <a name="password-policy-considerations"></a>Parola ilkesi değerlendirmeleri

Parola karması eşitlemesini etkinleştirerek etkilenen iki tür parola ilkesi vardır:

* Parola karmaşıklığı ilkesi
* Parola süre sonu ilkesi

#### <a name="password-complexity-policy"></a>Parola karmaşıklığı ilkesi

Parola karması eşitleme etkinleştirildiğinde, şirket içi Active Directory örneğindeki parola karmaşıklığı ilkeleri eşitlenmiş kullanıcılar için buluttaki karmaşıklık ilkelerini geçersiz kılar. Azure AD hizmetlerine erişmek için şirket içi Active Directory örneğinizin tüm geçerli parolalarını kullanabilirsiniz.

> [!NOTE]
> Bulutta doğrudan oluşturulan kullanıcılar için parolalar hala bulutta tanımlanan parola ilkelerine tabidir.

#### <a name="password-expiration-policy"></a>Parola süre sonu ilkesi

Bir kullanıcı parola karması eşitleme kapsamınsa, varsayılan olarak, bulut hesabı parolası *hiç kullanım* dışı olarak ayarlanır.

Şirket içi ortamınızda kullanım dışı olan eşitlenmiş bir parolayı kullanarak bulut hizmetlerinize oturum açmaya devam edebilirsiniz. Şirket içi ortamda parolayı bir sonraki değiştirişinizde bulut parolanız güncelleştirilir.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>Enforcechoparlör Passwordpolicyforpasswordsyncedusers

Yalnızca Azure AD ile tümleşik hizmetler ile etkileşime geçen eşitlenmiş kullanıcılar varsa ve bir parola süre sonu ilkesiyle uyumlu olması gerekiyorsa, *Enforcechoparlör Passwordpolicyforpasswordsyncedusers* özelliğini etkinleştirerek BUNLARı Azure AD parola süre sonu ilkenize uygun hale getirebilirsiniz.

*Enforcecıpasswordpolicyforpasswordsyncedusers* devre dışı bırakıldığında (varsayılan ayar), Azure AD Connect eşitlenen kullanıcıların passwordpolicies özniteliğini "Disablepasswordexpidıma" olarak ayarlar. Bu, bir kullanıcının parolasının her eşitlenilişinde yapılır ve Azure AD 'yi bu kullanıcı için bulut parolası süre sonu ilkesini yoksayacak şekilde yönlendirir. Aşağıdaki komutla Azure AD PowerShell modülünü kullanarak özniteliğin değerini kontrol edebilirsiniz:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Enforcechoparlör Passwordpolicyforpasswordsyncedusers özelliğini etkinleştirmek için aşağıda gösterildiği gibi MSOnline PowerShell modülünü kullanarak aşağıdaki komutu çalıştırın. Enable parametresi için aşağıda gösterildiği gibi Yes yazmanız gerekir:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Etkinleştirildikten sonra Azure AD, PasswordPolicies özniteliğinden değeri kaldırmak için eşitlenmiş her kullanıcıya gitmez `DisablePasswordExpiration` . Bunun yerine, bu `DisablePasswordExpiration` değer, her kullanıcı için sonraki parola karması eşitlemesi sırasında, şirket ıçı ad 'de sonraki parola değişikliğinden sonra PasswordPolicies 'tan kaldırılır.

Parola karma eşitlemesi etkinleştirilmeden önce Enforcechoparlör Passwordpolicyforpasswordsyncedusers ' ı etkinleştirmeniz önerilir, böylece parola karmalarının ilk eşitlenmesi, `DisablePasswordExpiration` Kullanıcılar Için PasswordPolicies özniteliğine değeri eklemez.

Varsayılan Azure AD parola ilkesi, kullanıcıların parolalarının her 90 günde bir değiştirilmesini gerektirir. AD 'deki ilkeniz da 90 gün ise, iki ilke eşleşmelidir. Ancak, AD ilkesi 90 gün değilse, Azure AD parola ilkesini Set-MsolPasswordPolicy PowerShell komutunu kullanarak eşleşecek şekilde güncelleştirebilirsiniz.

Azure AD, kayıtlı etki alanı başına ayrı bir parola süre sonu ilkesini destekler.

Desteklenmediği uyarısıyla: Azure AD 'de süresi dolmayan parolalara sahip olması gereken eşitlenmiş hesaplar varsa, `DisablePasswordExpiration` değeri, Azure AD 'de Kullanıcı nesnesinin PasswordPolicies özniteliğine açıkça eklemeniz gerekir.  Bunu, aşağıdaki komutu çalıştırarak yapabilirsiniz.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Set-MsolPasswordPolicy PowerShell komutu, Federasyon etki alanlarında çalışmayacaktır. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Geçici parolalar eşitleniyor ve "bir sonraki oturumda parola değişikliğini zorla"

Bir kullanıcıyı ilk oturum açma sırasında, özellikle de yönetici parolası sıfırlama oluştuktan sonra parolalarını değiştirmeye zorlamak normaldir.  Genellikle "geçici" bir parola ayarı olarak bilinir ve Active Directory (AD) içindeki bir kullanıcı nesnesi üzerinde "Kullanıcı bir sonraki oturum açışında parolayı değiştirmeli" bayrağıyla tamamlanır.
  
Geçici parola işlevi, kimlik bilgisinin sahipliğinin bu kimlik bilgisi ile ilgili bilgi sahibi olduğu süreyi en aza indirmek için ilk kullanımda, kimlik bilgisinin sahipliğinin aktarılışında emin olmaya yardımcı olur.

Azure AD 'de eşitlenen kullanıcılar için geçici parolaları desteklemek üzere Azure AD Connect sunucunuzda aşağıdaki komutu çalıştırarak *Forcepasswordchangeonlogon* özelliğini etkinleştirebilirsiniz:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Bir kullanıcının bir sonraki oturum açışında parolasını değiştirmesine zorlamak için aynı anda bir parola değişikliği yapılması gerekir.  Azure AD Connect, parolayı zorla değiştirme bayrağını kendisi seçmeyecektir; Parola karması eşitleme sırasında oluşan algılanan parola değişikliğine ek olarak belirlenir.

> [!CAUTION]
> Bu özelliği yalnızca, kiracı üzerinde SSPR ve parola geri yazma etkinleştirildiğinde kullanmanız gerekir.  Bu, bir Kullanıcı parolasını SSPR ile değiştirirse, Active Directory eşitlenecek.

#### <a name="account-expiration"></a>Hesap süre sonu

Kuruluşunuz, Kullanıcı hesabı yönetiminin bir parçası olarak accountExpires özniteliğini kullanıyorsa, bu öznitelik Azure AD ile eşitlenmez. Sonuç olarak, Parola karması eşitlemesi için yapılandırılmış bir ortamdaki zaman aşımına uğradı Active Directory hesabı Azure AD 'de etkin olmaya devam edecektir. Hesabın kullanım zamanı dolmuşsa, bir iş akışı eyleminin kullanıcının Azure AD hesabını devre dışı bırakan bir PowerShell betiğini tetiklemesi gerektiğini öneririz ( [set-AzureADUser](/powershell/module/azuread/set-azureaduser) cmdlet 'ini kullanın). Buna karşılık, hesap açık olduğunda, Azure AD örneği açık olmalıdır.

### <a name="overwrite-synchronized-passwords"></a>Eşitlenen parolaların üzerine yaz

Yönetici, Windows PowerShell kullanarak parolanızı el ile sıfırlayabilir.

Bu durumda, yeni parola eşitlenen parolanızı geçersiz kılar ve bulutta tanımlanan tüm parola ilkeleri yeni parolaya uygulanır.

Şirket içi parolanızı yeniden değiştirirseniz, yeni parola buluta eşitlenir ve el ile güncellenen parolayı geçersiz kılar.

Bir parolanın eşitlenmesi, oturum açan Azure kullanıcısına hiçbir etkiye sahip değildir. Geçerli bulut hizmeti oturumunuz, bir bulut hizmetinde oturum açtığınızda oluşan eşitlenmiş bir parola değişikliğinden hemen etkilenmemiştir. KMSı bu farkın süresini uzatır. Bulut hizmeti yeniden kimlik doğrulaması yapmanızı gerektirdiğinde, yeni parolanızı sağlamanız gerekir.

### <a name="additional-advantages"></a>Ek avantajlar

- Genellikle, Parola karması eşitleme, bir Federasyon hizmetinden daha kolay bir şekilde uygulanır. Ek sunucu gerektirmez ve kullanıcıların kimliğini doğrulamak için yüksek kullanılabilirliğe sahip bir Federasyon hizmetindeki bağımlılığını ortadan kaldırır.
- Parola karması eşitleme, Federasyona ek olarak da etkinleştirilebilir. Federasyon Hizmetiniz bir kesinti yaşıyorsa, geri dönüş olarak kullanılabilir.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Azure AD Domain Services için Parola karması eşitleme işlemi

Kerberos, LDAP veya NTLM kullanması gereken uygulamalar ve hizmetler için eski kimlik doğrulama sağlamak üzere Azure AD Domain Services kullanırsanız, bazı ek süreçler Parola karması eşitleme akışının bir parçasıdır. Azure AD Connect, Azure AD Domain Services kullanım için parola karmalarını Azure AD ile eşitlemeye yönelik aşağıdaki ek işlemleri kullanır:

> [!IMPORTANT]
> Azure AD Connect yalnızca şirket içi AD DS ortamları ile eşitleme için yüklenmeli ve yapılandırılmalıdır. Nesneleri Azure AD 'ye geri eşitlemeniz için Azure AD DS tarafından yönetilen bir etki alanına Azure AD Connect yüklemek desteklenmez.
>
> Azure AD Connect yalnızca Azure AD kiracınız için Azure AD DS etkinleştirdiğinizde eski parola karmalarını eşitler. Yalnızca Azure AD ile şirket içi AD DS ortamı eşitlemesini Azure AD Connect kullanıyorsanız aşağıdaki adımlar kullanılmaz.
>
> Eski uygulamalarınız NTLM kimlik doğrulaması veya LDAP basit bağlamalar kullanmıyorsanız, Azure AD DS için NTLM parola karma eşitlemesini devre dışı bırakmanızı öneririz. Daha fazla bilgi için bkz. [zayıf şifre paketlerini ve NTLM kimlik bilgisi karma eşitlemesini devre dışı bırakma](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect kiracının Azure AD Domain Services örneğinin ortak anahtarını alır.
1. Kullanıcı parolasını değiştirdiğinde, şirket içi etki alanı denetleyicisi parola değişikliğinin (karma) sonucunu iki özniteliğe depolar:
    * NTLM parola karması için *unicodePwd* .
    * , Kerberos Parola karması için *kimlik bilgileri* .
1. Azure AD Connect, Dizin çoğaltma kanalı aracılığıyla parola değişikliklerini algılar (öznitelik değişiklikleri diğer etki alanı denetleyicilerine çoğaltılmaya gerek yoktur).
1. Parolası değişmiş olan her kullanıcı için, Azure AD Connect aşağıdaki adımları gerçekleştirir:
    * Rastgele bir AES 256 bit simetrik anahtar üretir.
    * Şifrelemenin ilk yuvarlaklaştırmak için gereken rastgele bir başlatma vektörü üretir.
    * , *Mentalcredentials* özniteliklerinden Kerberos parola karmalarını ayıklar.
    * Azure AD Domain Services Güvenlik Yapılandırması *Syncntlmpasswords* ayarını denetler.
        * Bu ayar devre dışı bırakılırsa, rastgele, yüksek entropi bir NTLM karması (kullanıcının parolasından farklı) oluşturur. Bu karma daha sonra, exacted Kerberos parola karmalarıyla birlikte, *Mentalcrendetials* özniteliğinden bir veri yapısına birleştirilir.
        * Etkinleştirilirse, *unicodePwd* özniteliğinin değerini, *dtalcredentials* özniteliğinden ayıklanan Kerberos parola karmalarıyla tek bir veri yapısına birleştirir.
    * AES Simetrik anahtarını kullanarak tek veri yapısını şifreler.
    * Kiracının Azure AD Domain Services ortak anahtarını kullanarak AES Simetrik anahtarını şifreler.
1. Azure AD Connect şifreli AES Simetrik anahtarını, parola karmalarını içeren şifreli veri yapısını ve Azure AD 'ye başlatma vektörünü iletir.
1. Azure AD şifreli AES Simetrik anahtarını, şifreli veri yapısını ve Kullanıcı için başlatma vektörünü depolar.
1. Azure AD, şifreli AES Simetrik anahtarını, şifrelenmiş veri yapısını ve Azure AD Domain Services için şifrelenmiş bir HTTP oturumunda iç eşitleme mekanizması kullanarak başlatma vektörünü gönderir.
1. Azure AD Domain Services kiracının Azure Anahtar Kasası 'na ait örneğinin özel anahtarını alır.
1. Her şifreli veri kümesi (tek bir kullanıcının parola değişikliğini temsil eden) için Azure AD Domain Services aşağıdaki adımları gerçekleştirir:
    * , AES Simetrik anahtarının şifresini çözmek için özel anahtarını kullanır.
    * Parola karmalarını içeren şifreli veri yapısının şifresini çözmek için başlatma vektörü ile AES Simetrik anahtarını kullanır.
    * Aldığı Kerberos parola karmalarını Azure AD Domain Services etki alanı denetleyicisine yazar. Karmalar, Azure AD Domain Services etki alanı denetleyicisinin ortak anahtarına şifrelenmiş Kullanıcı nesnesinin *Mentalcredentials* özniteliğine kaydedilir.
    * Azure AD Domain Services, Azure AD Domain Services etki alanı denetleyicisine aldığı NTLM parola karmasını yazar. Karma, Azure AD Domain Services etki alanı denetleyicisinin ortak anahtarına şifrelenmiş Kullanıcı nesnesinin *unicodePwd* özniteliğine kaydedilir.

## <a name="enable-password-hash-synchronization"></a>Parola karması eşitlemeyi etkinleştirme

>[!IMPORTANT]
>AD FS (veya başka bir Federasyon teknolojileri) ile parola karması eşitlemeye geçiş yapıyorsanız, [burada](https://aka.ms/adfstophsdpdownload)yayımlanan ayrıntılı dağıtım kılavuzumuzu izlemenizi kesinlikle öneririz.

Azure AD Connect **Express ayarları** seçeneğini kullanarak yüklediğinizde, Parola karması eşitleme otomatik olarak etkinleştirilir. Daha fazla bilgi için bkz. [Express ayarlarını kullanarak Azure AD Connect](how-to-connect-install-express.md)kullanmaya başlama.

Azure AD Connect yüklerken özel ayarları kullanıyorsanız, Kullanıcı oturum açma sayfasında parola karması eşitlemesi kullanılabilir. Daha fazla bilgi için bkz. [özel Azure AD Connect yüklemesi](how-to-connect-install-custom.md).

![Parola karmasını eşitlemeyi etkinleştirme](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Parola karması eşitleme ve FIPS
Sunucunuz Federal bilgi Işleme standardı (FIPS) uyarınca kilitliyse, MD5 devre dışı bırakılır.

**Parola karması eşitlemesi için MD5 etkinleştirmek üzere aşağıdaki adımları uygulayın:**

1. %Programfiles%\Microsoft Azure AD Sync\Bin. 'e gidin
2. miiserver.exe.config açın.
3. Dosyanın sonundaki yapılandırma/çalışma zamanı düğümüne gidin.
4. Aşağıdaki düğümü ekleyin: `<enforceFIPSPolicy enabled="false"/>`
5. Yaptığınız değişiklikleri kaydedin.

Başvuru için, bu kod parçacığı şöyle görünmelidir:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Güvenlik ve FIPS hakkında daha fazla bilgi için bkz. [Azure AD Parola karması eşitleme, şifreleme ve FIPS uyumluluğu](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Parola karması eşitleme sorunlarını giderme
Parola karması eşitlemeyle ilgili sorun yaşıyorsanız, bkz. [Parola karması eşitleme sorunlarını giderme](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure AD Connect eşitleme: eşitleme seçeneklerini özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
* [ADFS 'den Parola karması eşitlemesine geçiş için adım adım bir dağıtım planı alın](https://aka.ms/authenticationDeploymentPlan)
