---
title: 'Azure AD Connect: sürüm sürümü geçmişi | Microsoft Docs'
description: Bu makalede Azure AD Connect ve Azure AD Eşitleme tüm sürümleri listelenir
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/7/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 893b617a965b0823b8d630e036d5d5f923647f8f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73944219"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: sürüm sürümü geçmişi
Azure Active Directory (Azure AD) ekibi, yeni özellikler ve işlevlerle Azure AD Connect düzenli olarak güncelleştirir. Tüm eklemeler tüm izleyiciler için geçerli değildir.


Bu makale, yayımlanan sürümleri izlemenize ve değişikliklerin en son sürümde ne olduğunu anlamanıza yardımcı olmak için tasarlanmıştır.

Bu tablo, ilgili konuların listesidir:

Konu |  Ayrıntılar
--------- | --------- |
Azure AD Connect yükseltme adımları | [Önceki bir sürümden en son](how-to-upgrade-previous-version.md) Azure AD Connect sürüme yükseltmek için farklı yöntemler.
Gerekli izinler | Bir güncelleştirmeyi uygulamak için gereken izinler için bkz. [hesaplar ve izinler](reference-connect-accounts-permissions.md#upgrade).
İndir| [Azure AD Connect indirin](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Azure AD Connect yeni bir sürümünü serbest bırakmak, hizmetin işlem işlevlerini sağlamak için çeşitli kalite denetimi adımı gerektiren bir işlemdir ve bu işlemden sonra yeni bir yayının sürüm numarası ve yayın durumu güncelleştirilir en son durumu yansıtacak şekilde.
Bu işlemden sonra, yayının sürüm numarası, "1.3. X. 0" içinde olduğu gibi küçük Yayın numarası konumunda bir "X" ile gösterilir. bu belgedeki sürüm notlarının, "1,3." ile başlayan tüm sürümler için geçerli olduğunu gösterir. Yayın işlemini sonuçlandırdıktan hemen sonra yayın sürümü numarası en son yayınlanan sürüme güncelleştirilir ve yayın durumu "indirme ve otomatik yükseltme için yayımlandı" olarak güncelleştirilir.
Azure AD Connect sürümlerinin hepsi otomatik yükseltme için kullanılabilir hale getirilmeyecektir. Sürüm durumu, bir yayının otomatik yükseltme veya yalnızca indirme için kullanılabilir duruma getirilmeyeceğini gösterir. Azure AD Connect sunucunuzda otomatik yükseltme etkinleştirildiyse, bu sunucu otomatik olarak yükseltme için yayınlanan en son Azure AD Connect sürümüne yükseltilir. Azure AD Connect yapılandırmalarının tümünün otomatik yükseltme için uygun olmadığına unutmayın. [Otomatik yükseltme](how-to-connect-install-automatic-upgrade.md) hakkında daha fazla bilgi için lütfen bu bağlantıyı izleyin

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Yayın durumu
11/08/2019: indirilmek üzere yayınlandı. Otomatik yükseltme için kullanılamaz

>[!IMPORTANT]
>Bu Azure AD Connect sürümünde bir iç şema değişikliği nedeniyle, MSOnline PowerShell kullanarak ADFS güven ilişkisi yapılandırma ayarlarını yönetiyorsanız, MSOnline PowerShell modülünüzü 1.1.183.57 veya üzeri sürümüne güncelleştirmeniz gerekir
### <a name="fixed-issues"></a>Düzeltilen sorunlar

Bu sürüm, mevcut karma Azure AD 'ye katılmış cihazlarda bir sorunu düzeltir. Bu sürüm, bu sorunu gideren yeni bir cihaz eşitleme kuralı içerir.
Bu kural değişikliğinin, kullanılmayan cihazların Azure AD 'den silinmesine neden olabileceğini unutmayın. Bu cihaz nesneleri, koşullu erişim yetkilendirmesi sırasında Azure AD tarafından kullanılmadığından bu sorun için bir neden değildir. Bazı müşteriler için, bu kural değişikliği üzerinden silinecek cihaz sayısı, silme eşiğini aşabilir. Azure AD 'de cihaz nesneleri silme işlemini dışarı aktarma silme eşiğini aştıktan sonra, silme işlemlerinin devam eden bir şekilde tamamlanmasına izin verilmesi önerilir. [Silme eşiğini aştıklarında akışa silme izni verme](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Yayın durumu
9/28/2019: kiracı seçmek için otomatik yükseltme için yayınlandı. İndirileceği için kullanılamaz.

Bu sürüm, önceki sürümden 1.4.18.0 'e otomatik olarak yükseltilen bazı sunucuların, self servis parola sıfırlama (SSPR) ve parola geri yazma ile ilgili sorun yaşadığı bir hatayı düzeltir.

### <a name="fixed-issues"></a>Düzeltilen sorunlar

Belirli koşullarda, 1.4.18.0 sürümüne otomatik olarak yükseltilen sunucular, yükseltme tamamlandıktan sonra self servis parola sıfırlama ve parola geri yazma özelliğini yeniden etkinleştirmemiştir. Bu otomatik yükseltme sürümü, self servis parola sıfırlama ve parola geri yazma sorununu gidermeye ve yeniden etkinleştirmesine izin veren düzeltmeleri.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Bu Azure AD Connect sürümüne yükselttikten sonra bazı müşterilerin mevcut karma Azure AD 'ye katılmış cihazlarda bir sorunla karşılaşmakta olduğu bir olay araştırıyoruz. Bu sorunların temel nedeni tam olarak anlaşılana ve hafiflene kadar, karma Azure AD JOIN 'i dağıtan müşterilere bu sürüme yükseltmeyi erteleyin. Daha fazla bilgi mümkün olan en kısa sürede sunulacaktır.

>[!IMPORTANT]
>Bu Azure AD Connect, bazı müşteriler Windows cihazlarının bazı veya tümünün Azure AD 'den kaybolabileceğini görebilir. Bu cihaz kimlikleri, koşullu erişim yetkilendirmesi sırasında Azure AD tarafından kullanılmadığından bu sorun için bir neden değildir. Daha fazla bilgi için bkz [. Azure AD Connect 1.4. xx. x Device disappearnce 'ı anlama](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Yayın durumu
9/25/2019: olay araştırması tamamlanana kadar el ile indirme işleminden kaldırılmıştır.

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
- Yeni sorun giderme araçları, "kullanıcının eşitlemesine yönelik", "Grup eşitlenmiyor" veya "grup üyesi eşitlenmiyor" senaryolarındaki sorunları gidermeye yardımcı olur.
- AAD Connect sorun giderme betikteki Ulusal bulutlar için destek ekleme 
- Müşteriler MIIS_Service için kullanımdan kaldırılan WMI bitiş noktaları kaldırılmıştır. Tüm WMI işlemleri artık PS cmdlet 'leri aracılığıyla yapılmalıdır.
- AZUREADSSOACC nesnesinde kısıtlanmış temsilciyi sıfırlayarak güvenlik iyileştirmesi
- Bir eşitleme kuralı eklerken/düzenlenirken, kuralda bağlayıcı şemasında bulunan ancak bağlayıcıya eklenmemiş öznitelikler varsa, bu öznitelikler otomatik olarak bağlayıcıya eklenir. Kuralın etkilediği nesne türü için de aynı değer geçerlidir. Bağlayıcıya herhangi bir şey eklenirse, bağlayıcı sonraki eşitleme döngüsüne tam içeri aktarma işlemi için işaretlenir.
- Bağlayıcı hesabı olarak bir kuruluş veya etki alanı yöneticisi kullanmak artık yeni AAD Connect dağıtımlarında desteklenmez. Bağlayıcı hesabı olarak bir kuruluş veya etki alanı Yöneticisi kullanan geçerli AAD Connect dağıtımları Bu sürümden etkilenmeyecektir.
- Eşitleme yöneticisinde, kural oluşturma/düzenleme/silme üzerinde tam eşitleme çalıştırılır. Tam içeri aktarma veya tam eşitleme çalışmaya devam ederseniz, kullanıcıya bildiren bir açılan pencere, herhangi bir kural değişikliği görünür.
- Parola hatalarının "Bağlayıcılar > Özellikler > bağlantı" sayfasına yönelik risk azaltma adımları eklendi
- Bağlayıcı Özellikleri sayfasında Sync Service Manager için kullanımdan kaldırma uyarısı eklendi. Bu uyarı, kullanıcıya, değişikliklerin AADC Sihirbazı aracılığıyla yapılması gerektiğini bildirir.
- Kullanıcının parola ilkesiyle ilgili sorunlar için yeni hata eklendi.
- Etki alanı ve OU filtrelerine göre grup filtrelemesinin yanlış yapılandırılmasını önleyin. Girilen grubun etki alanı/OU 'su zaten filtrelenirse ve sorun çözümlenene kadar kullanıcının ileri doğru hareket etmesini önlemek için Grup filtreleme bir hata gösterir.
- Kullanıcılar artık Synchronization Service Manager Kullanıcı arabirimindeki Active Directory Domain Services veya Windows Azure Active Directory için bağlayıcı oluşturamaz.
- Synchronization Service Manager özel UI denetimleri için sabit erişilebilirlik.
- Azure AD Connect 'de tüm oturum açma yöntemleri için altı Federasyon yönetim görevi etkinleştirildi.  (Daha önce, tüm oturum açma işlemleri için yalnızca "güncelleştirme AD FS SSL sertifikası" görevi mevcuttur.)
- Tüm Azure AD etki alanlarının ve kullanıcılarının yönetilen kimlik doğrulamasına dönüştürüleceği, Federasyon 'dan PHS 'ye veya PTA 'ya oturum açma yöntemi değiştirilirken bir uyarı eklendi.
- "Azure AD ve AD FS güveni sıfırlama" görevinden belirteç imzalama sertifikaları kaldırıldı ve bu sertifikaları güncelleştirmek için ayrı bir alt görev eklendi.
- AD FS grubuna yönelik SSL veya belirteç imzalama sertifikalarını güncelleştirmek için alt görevler içeren "sertifikaları Yönet" adlı yeni bir Federasyon yönetim görevi eklendi.
- Yöneticilerin AD FS grubu için yeni bir birincil sunucu belirtmesini sağlayan "birincil sunucu belirtin" adlı yeni bir Federasyon yönetimi alt görevi eklendi.
- Bir AD FS sunucusu dağıtmak, bir Web uygulaması ara sunucusu dağıtmak ve birincil sunucuyu belirtmek için alt görevler içeren "sunucuları Yönet" adlı yeni bir Federasyon yönetim görevi eklendi.
- Geçerli AD FS ayarlarını görüntüleyen "Federasyon yapılandırmasını görüntüle" adlı yeni bir Federasyon yönetim görevi eklendi.  (Bu ek olarak, AD FS ayarları "çözümünüzü gözden geçirin" sayfasından kaldırılmıştır.)

### <a name="fixed-issues"></a>Düzeltilen sorunlar
- Kendisine karşılık gelen kişi nesnesini alan bir Kullanıcı nesnesinin kendi kendine başvurmasının (ör. Kullanıcı kendi Yöneticisi olduğu) senaryoya yönelik eşitleme hatası sorunu çözüldü.
- Yardım açılanları şimdi klavye odağında gösteriliyor.
- Otomatik yükseltme için, 6 saatten daha fazla çakışan uygulama çalışıyorsa, sonlandırın ve yükseltmeye devam edin.
- Dizin uzantıları seçerken bir müşterinin nesne başına 100 ' i seçmesi için öznitelik sayısını sınırlayın. Bu, Azure 'un nesne başına en fazla 100 uzantı özniteliklerine sahip olduğu için hata verme sırasında oluşmasını engeller.
- AD bağlantı betiğini daha sağlam hale getirmek için bir hata düzeltildi
- Mevcut bir adlandırılmış yöneltme WCF hizmetini daha sağlam bir makineye yüklemek için bir hata düzeltildi.
- İlk yüklendiğinde ADSync hizmetine izin verilmeyen Grup ilkeleri etrafında geliştirilmiş Tanılamalar ve sorun giderme.
- Bir Windows bilgisayarı için görünen adın yanlış yazıldığı bir hata düzeltildi.
- Bir Windows bilgisayarı için işletim sistemi türünün yanlış yazıldığı bir hatayı düzeltir.
- Windows 10 olmayan bilgisayarların beklenmedik şekilde eşitlendiğinden oluşan hata düzeltildi. Bu değişikliğin etkisinin, daha önce eşitlenen Windows-10 olmayan bilgisayarların artık silineceğini unutmayın. Bu, Windows bilgisayarlarının eşitlenmesi yalnızca Windows-10 cihazlarında kullanılan karma Azure AD etki alanına katılması için kullanıldığından, hiçbir özelliği etkilemez.
- ADSync PowerShell modülüne birkaç yeni (iç) cmdlet eklendi.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Daha önceki bir sürümden Azure AD Connect yükseltirken, Azure AD Connect başarıyla yükseltilse de O365 portalının güncelleştirilmiş sürümü yansıtmadığından, bu bilinen bir sorun vardır.
>
> Bu sorunu gidermek için **ADSync** modülünü içeri aktarmanız ve sonra Azure AD Connect sunucusunda`Set-ADSyncDirSyncConfiguration` PowerShell cmdlet 'ini çalıştırmanız gerekir.  Aşağıdaki adımları kullanabilirsiniz:
>
>1. PowerShell 'i erişiminizin engellenmediğinden modunda açma
>2. `Import-Module "ADSync"` öğesini çalıştırın
>3. `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` öğesini çalıştırın
 
### <a name="release-status"></a>Yayın durumu 

05/14/2019: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar 

- Microsoft Azure Active Directory Connect Build 1.3.20.0 'ta bulunan ayrıcalık yükselmesi güvenlik açığı düzeltildi.  Bu güvenlik açığı belirli koşullar altında, bir saldırganın ayrıcalıklı bir hesap bağlamında iki PowerShell cmdlet 'i yürütmesine ve ayrıcalıklı eylemler gerçekleştirmesine izin verebilir.  Bu güvenlik güncelleştirmesi, bu cmdlet 'leri devre dışı bırakarak sorunu giderir. Daha fazla bilgi için bkz. [güvenlik güncelleştirmesi](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Yayın durumu 

04/24/2019: indirilmek üzere yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler 

- Etki alanı yenileme desteği ekle 
- Exchange posta ortak klasörleri özelliği GA 'ye gidiyor 
- Hizmet hataları için sihirbaz hata işlemeyi geliştirme 
- Bağlayıcı Özellikleri sayfasında Synchronization Service Manager Kullanıcı arabirimine uyarı bağlantısı eklendi. 
- Birleşik gruplar geri yazma özelliği artık GA 'dir 
- DC 'de bir LDAP denetimi eksik olduğunda, geliştirilmiş SSPR hata iletisi 
- Yüklemesi sırasında DCOM kayıt defteri hataları için tanılama eklendi  
- PHS RPC hatalarının artırılması izleniyor 
- Bir alt etki alanından EA kimlik bilgileri 'ne izin ver 
- Yüklemesi sırasında veritabanı adının girilebileceği izin ver (varsayılan ad ADSync)
- Bir WS-Trust düzeltmesini almak ve yeni Azure örnekleri için destek eklemek üzere ADAL 3.19.8 sürümüne yükseltin 
- Grup eşitleme kurallarını, talepler için gerekli olan samAccountName, Domainnetbıos ve DomainFQDN 'leri buluta akıtma için değiştirin 
- Değiştirilen varsayılan eşitleme kuralı Işleme [– daha fazla bilgi edinin](how-to-connect-fix-default-rules.md).
- Windows hizmeti olarak çalışan yeni bir aracı eklendi. "Yönetici Aracısı" adlı bu aracı, bir destek talebi açtığınızda Microsoft mühendislerinin sorun gidermeye yardımcı olmak için Azure AD Connect sunucusunun daha derin uzak tanılamasını sağlar. Bu aracı yüklenmez ve varsayılan olarak etkin değildir.  Aracının nasıl yükleneceği ve etkinleştirileceği hakkında daha fazla bilgi için bkz. [Azure AD Connect yönetici Aracısı nedir?](whatis-aadc-admin-agent.md). 
- Son Kullanıcı Lisans Sözleşmesi (EULA) güncelleştirildi 
- AD FS kullanan dağıtımlar için oturum açma türü olarak otomatik yükseltme desteği eklendi.  Bu Ayrıca, yükseltme işleminin bir parçası olarak AD FS Azure AD bağlı olan taraf güvenini güncelleştirme gereksinimini ortadan kaldırdık. 
- İki seçenek sağlayan bir Azure AD güven yönetimi görevi eklendi: çözümle/güncelleştirme güveni ve güveni sıfırlama. 
- AD FS Azure AD bağlı olan taraf güveni davranışı, her zaman-SupportMultipleDomain anahtarını (güven ve Azure AD etki alanı güncelleştirmelerini içerir) kullanacak şekilde değiştirildi. 
- Önceden yüklenmiş bir sertifika kullanma seçeneğini kaldırarak. pfx sertifikası gerektirdiğinden, yeni AD FS grubu yükleme davranışı değiştirildi.
- Yeni AD FS grubu iş akışı, yalnızca 1 AD FS ve 1 WAP sunucusu dağıtmaya izin verecek şekilde güncelleştirildi.  Tüm ek sunucular, ilk yüklemeden sonra yapılır. 

### <a name="fixed-issues"></a>Düzeltilen sorunlar 

- ADSync hizmeti için SQL yeniden bağlama mantığını çözme 
- Boş bir SQL AOA DB kullanarak temiz yüklemeye izin vermek için onarma 
- GWB izinlerini iyileştirmek için PS Izinleri betiğini düzeltir 
- LocalDB ile VSS hatalarını çözme  
- Nesne türü kapsamda olmadığında yanıltıcı hata iletisini düzeltir 
- Bir sunucuya Azure AD PowerShell yüklemesinin Azure AD Connect bir derleme çakışmasına neden olabileceği bir sorun düzeltildi. 
- Synchronization Service Manager Kullanıcı arabiriminde bağlayıcı kimlik bilgileri güncelleştirilirken hazırlama sunucusunda duran PHS hatası. 
- Bazı bellek sızıntıları düzeltildi 
- Çeşitli oto yükseltme düzeltmeleri 
- Dışarı aktarma ve onaylanmamış Içeri aktarma Işlemlerinin çeşitli düzeltmeleri 
- Etki alanı ve OU filtrelemesinde ters eğik çizgi işleme ile ilgili hata düzeltildi 
- ADSync hizmetinin durdurulması 2 dakikadan uzun sürmesine neden olan ve yükseltme zamanında bir soruna neden olan bir sorun düzeltildi. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Yayın durumu

12/18/2018: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar

Bu derleme, Azure AD Connect ile gönderilen standart olmayan bağlayıcıları (örneğin, genel LDAP Bağlayıcısı ve genel SQL Bağlayıcısı) güncelleştirir. Geçerli bağlayıcılar hakkında daha fazla bilgi için bkz. sürüm 1.1.911.0 in [bağlayıcı sürümü yayın geçmişi](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Yayın durumu
12/11/2018: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar
Bu düzeltme derlemesi, kullanıcının cihaz geri yazma özelliğini etkinleştirirken RegisteredDevices kapsayıcısı için belirtilen ormanda bir hedef etki alanı seçmesine olanak sağlar.  Yeni cihaz seçenekleri işlevselliğini (1.1.819.0 – 1.2.68.0) içeren önceki sürümlerde, RegisteredDevices kapsayıcı konumu orman köküyle sınırlandırıldı ve alt etki alanlarına izin vermedi.  Bu sınırlama yalnızca Yeni dağıtımlar üzerinde, yerinde yükseltmeler etkilenmemiştir.  

Güncelleştirilmiş cihaz seçenekleri işlevlerini içeren herhangi bir derleme yeni bir sunucuya dağıtılmışsa ve cihaz geri yazma özelliği etkinleştirildiyse, bu durumda, orman kökünde istemiyorsanız kapsayıcının konumunu el ile belirtmeniz gerekir.  Bunu yapmak için cihaz geri yazmayı devre dışı bırakmanız ve yeniden etkinleştirmeniz gerekir. Bu, "geri yazma Ormanı" sayfasında kapsayıcı konumunu belirtmenize olanak tanır.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Yayın durumu 

11/30/2018: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar

Bu düzeltme derlemesi, eşitleme sunucusunda MSOnline PowerShell Galerisi modülünün bağımsız olması nedeniyle bir kimlik doğrulama hatasının gerçekleşebileceği bir çakışmayı düzeltir.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Yayın durumu 

11/19/2018: indirilmek üzere yayınlandı

### <a name="fixed-issues"></a>Düzeltilen sorunlar

Bu düzeltme derlemesi, Windows Server 2008/R2 'de bir etki alanı denetleyicisi kullanırken parola geri yazma işlemi başarısız olduğunda önceki derlemede bir gerileme düzeltir.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Yayın durumu 

10/25/2018: indirilmek üzere yayınlandı

 
### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler 


- Barındırılan sesli postanın beklenen şekilde çalıştığından emin olmak için özniteliğin geri yazma işlevselliği değiştirilmiştir.  Bazı senaryolarda, Azure AD, null değer ile geri yazma sırasında msExchUcVoicemailSettings özniteliğinin üzerine yazılıyor.  Artık, bulut değeri ayarlanmamışsa Azure AD bu özniteliğin şirket içi değerini artık temizlemez.
- Azure AD Connect sihirbazında tanılama, Azure AD 'ye yönelik bağlantı sorunlarını araştırmak ve tanımlamak için eklenmiştir. Aynı Tanılamalar, test-AdSyncAzureServiceConnectivity cmdlet 'ı kullanılarak doğrudan PowerShell aracılığıyla da çalıştırılabilir. 
- AD 'ye yönelik bağlantı sorunlarını araştırmak ve tanımlamak için Azure AD Connect sihirbazında tanılama eklendi. Aynı Tanılamalar, ADConnectivityTools PowerShell modülündeki start-ConnectivityValidation işlevi kullanılarak doğrudan PowerShell aracılığıyla da çalıştırılabilir.  Daha fazla bilgi için bkz. [ADConnectivityTool PowerShell modülü nedir?](how-to-connect-adconnectivitytools.md)
- Karma Azure Active Directory katılması ve cihaz geri yazma için bir AD şeması sürümü ön denetimi eklendi 
- Dizin uzantısı sayfa özniteliği araması, büyük/küçük harfe duyarlı olmayan şekilde değiştirildi.
-   TLS 1,2 için tam destek eklendi. Bu sürüm devre dışı bırakılan tüm diğer protokolleri destekler ve Azure AD Connect yüklendiği makinede yalnızca TLS 1,2 etkinleştiriliyor.  Daha fazla bilgi için bkz. [Azure AD Connect Için TLS 1,2 zorlaması](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Düzeltilen sorunlar   

- SQL Always on kullanılıyorsa Azure AD Connect yükseltmenin başarısız olduğu bir hata düzeltildi. 
- Eğik çizgi içeren OU adlarını doğru bir şekilde ayrıştırmak için hata düzeltildi. 
- Hazırlama modunda temiz bir yüklemede geçiş kimlik doğrulamasının devre dışı bırakılabileceği bir sorun düzeltildi. 
- Sorun giderme araçları çalışırken PowerShell modülünün yüklenmesini önleyen bir hata düzeltildi 
- Müşterilerin ana bilgisayar adının ilk karakterinde sayısal değerler kullanmasını engelleyen bir hata düzeltildi. 
- Azure AD Connect, geçersiz bölümlere ve kapsayıcı seçimine izin verecek bir hata düzeltildi 
- Masaüstü SSO etkinken "geçersiz parola" hata iletisi düzeltildi. 
- AD FS güven yönetimi için çeşitli hata düzeltmeleri  
- Cihaz geri yazma yapılandırması sırasında-msDs-DeviceContainer nesne sınıfını (WS2012 R2 'de tanıtılan) aramak için şema denetimi düzeltildi

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: indirme için yayımlandı, otomatik yükseltme için yayınlanmayacak 

### <a name="fixed-issues"></a>Düzeltilen sorunlar  

ADSync DB için SQL Always on kullanılabilirliği yapılandırılırsa Azure AD Connect yükseltme başarısız olur. Bu düzeltme, bu sorunu gidermektedir ve yükseltmenin başarılı olmasına izin verir. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Yayın durumu

8/21/2018: indirme ve otomatik yükseltme için yayınlandı. 

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

- Azure AD Connect ' deki ping Federate tümleştirmesi artık genel kullanıma sunulmuştur. [Ping Federate ile Azure AD federasyon oluşturma hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Azure AD Connect, bir güncelleştirme her yapıldığında AD FS Azure AD güveninin yedeklemesini oluşturur ve gerektiğinde kolayca geri yüklemek için ayrı bir dosyada depolar. [Azure AD Connect ' de yeni işlevsellik ve Azure AD güven yönetimi hakkında daha fazla bilgi edinin](https://aka.ms/fedtrustinaadconnect).
- Yeni sorun giderme araçları, birincil e-posta adresini değiştirme ve hesabı genel adres listesinden gizleme sorunlarını gidermeye yardımcı olur
- Azure AD Connect, en son SQL Server 2012 yerel Istemcisini içerecek şekilde güncelleştirildi
- Parola karması eşitleme veya "Kullanıcı oturum açma 'yı Değiştir" görevinde Kullanıcı oturum açma 'yı değiştirdiğinizde, sorunsuz çoklu oturum açma onay kutusu varsayılan olarak etkinleştirilir.
- Windows Server Essentials 2019 için destek eklendi
- Azure AD Connect Health Aracısı en son sürüme güncelleştirildi 3.1.7.0
- Yükseltme sırasında, yükleyici varsayılan eşitleme kurallarında değişiklik algılarsa, değiştirilen kuralların üzerine yazmadan önce yöneticiye bir uyarı gönderilir. Bu, kullanıcının düzeltici eylemler yapmasına ve daha sonra sürdürmesine imkan sağlar. Eski davranış: değiştirilmiş bir hazır kural varsa, el ile yükseltme, kullanıcıya hiçbir uyarı vermeden bu kuralların üzerine yazılmıştı ve Kullanıcı bilgilendirmeden eşitleme zamanlayıcı devre dışı bırakıldı. Yeni davranış: kullanıcıya, değiştirilen kullanıma hazır eşitleme kurallarının üzerine yazmadan önce uyarı istenir. Kullanıcı, yükseltme işlemini durdurmak ve düzeltici eylem gerçekleştirdikten sonra daha sonra sürdürülmesi için seçim yapmış olacaktır.
- FIPS uyumlu bir ortamda MD5 karma oluşturmaya yönelik bir hata iletisi ve bu sorun için geçici bir çözüm sağlayan belgelere bir bağlantı sağlayan FIPS uyumluluk sorununa daha iyi bir işlem sağlar.
- Artık Federasyon için ayrı bir alt grup altında bulunan, sihirbazdaki Federasyon görevlerini geliştirmek için Kullanıcı arabirimi güncelleştirmesi. 
- Tüm Federasyon ek görevleri artık kullanım kolaylığı için tek bir alt menü altında gruplandırılır.
- Yeni ad izinleri işlevleri olan yeni bir yenilenmiş adsyncconfig Posh modülü (adsyncconfig. psm1), eski adsyncprep. psm1 öğesinden taşındı (kısa süre önce devre dışı bırakılmış olabilir)

### <a name="fixed-issues"></a>Düzeltilen sorunlar 

- AAD Connect sunucusunun .NET 4.7.2 'e yükselttikten sonra yüksek CPU kullanımı göstereceği bir hata düzeltildi
- Otomatik çözümlenen bir SQL kilitlenme sorunu için zaman zaman bir hata mesajı üreten hata düzeltildi
- Eşitleme kuralları Düzenleyicisi ve eşitleme Service Manager için çeşitli erişilebilirlik sorunları düzeltildi  
- Azure AD Connect kayıt defteri ayarı bilgilerini alınamayan bir hata düzeltildi
- Kullanıcı sihirbazda ileri/geri gittiğinde sorun oluşturan bir hata düzeltildi
- Sihirbazda yanlış çok iş parçacığı teslim nedeniyle hata oluşmasını engellemek için hata düzeltildi
- Grup eşitleme filtreleme sayfasında güvenlik grupları çözümlenirken bir LDAP hatasıyla karşılaştığında Azure AD Connect artık tam doğruluk olan özel durumu döndürür.  Başvuru özel durumunun kök nedeni hala bilinmiyor ve farklı bir hata tarafından giderilecektir.
-  STK ve NGC anahtarlarının izinlerinin (WHfB için Kullanıcı/cihaz nesnelerinde) doğru ayarlanmamasına neden olan bir hata düzeltildi.     
- ' Set-ADSyncRestrictedPermissions ' doğru çağrılmadığından hata düzeltildi
-  AADConnect 'in Yükleme sihirbazında grup geri yazma işleminde izin verme desteği ekleniyor
- Parola karması eşitleden AD FS oturum açma yöntemi değiştirilirken, Parola karması eşitlemesi devre dışı bırakılmadı.
- AD FS yapılandırmasındaki IPv6 adresleri için doğrulama eklendi
- Mevcut bir yapılandırmanın var olduğunu bildirmek için bildirim iletisi güncelleştirildi.
- Cihaz geri yazma işlemi, güvenilmeyen ormandaki kapsayıcıyı algılayamıyor. Bu, daha iyi bir hata iletisi ve ilgili belgelere bir bağlantı sağlayacak şekilde güncelleştirilmiştir
- Bir OU 'nun seçimini kaldırıp bu OU 'ya karşılık gelen eşitleme/geri yazma genel eşitleme hatası verir. Bu, daha anlaşılır bir hata iletisi oluşturacak şekilde değiştirilmiştir.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Yayın durumu

5/14/2018: otomatik yükseltme ve indirme için yayınlandı.

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

Yeni özellikler ve geliştirmeler

- Bu sürüm, Azure AD Connect 'de PingFederate 'in tümleştirmesinin genel önizlemesini içerir. Bu sürümde, müşteriler, Azure Active Directory ortamlarını Federasyon sağlayıcıları olarak PingFederate 'ten faydalanabilir şekilde kolayca ve güvenilir bir şekilde yapılandırabilir. Bu yeni özelliğin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için lütfen [çevrimiçi belgelerimizi](plan-connect-user-signin.md#federation-with-pingfederate)ziyaret edin. 
- Artık bağlı posta kutuları ve AD dinamik grupları gibi daha fazla hata senaryosunu çözümleyen Azure AD Connect Sihirbazı sorun giderme yardımcı programı güncelleştirildi. Sorun [giderme yardımcı programı](tshoot-connect-objectsync.md)hakkında daha fazla bilgi edinin.
- Cihaz geri yazma yapılandırması artık yalnızca Azure AD Connect Sihirbazı içinde yönetiliyor.
- SQL bağlantı sorunlarını ve çeşitli diğer sorun giderme yardımcı programlarını gidermek için kullanılabilen ADSyncTools. psm1 adlı yeni bir PowerShell modülü eklenmiştir. [Burada](tshoot-connect-tshoot-sql-connectivity.md)ADSyncTools modülü hakkında daha fazla bilgi edinin. 
- Yeni "cihaz seçeneklerini yapılandır" ek görevi eklendi. Aşağıdaki iki işlemi yapılandırmak için görevini kullanabilirsiniz: 
  - **Karma Azure AD katılımı**: ortamınızda şirket ıçı bir ad alanı varsa ve Azure Active Directory tarafından sunulan yetenekler için de avantaj istiyorsanız, karma Azure AD 'ye katılmış cihazları uygulayabilirsiniz. Bunlar hem şirket içi Active Directory alanınıza hem de Azure Active Directory alanınıza katılmış cihazlardır.
  - **Cihaz geri yazma**: cihazların AD FS (2012 R2 veya üzeri) korunan cihazlara göre koşullu erişimi etkinleştirmek için cihaz geri yazma kullanılır

    >[!NOTE] 
    > - Eşitleme seçeneklerini özelleştirmek için cihaz geri yazmayı etkinleştirme seçeneği de gri kalır. 
    > -  ADPrep için PowerShell modülü bu sürümde kullanımdan kaldırılmıştır.



### <a name="fixed-issues"></a>Düzeltilen sorunlar 

- Bu sürüm, SQL Server Express yüklemesini SQL Server 2012 SP4'E güncelleştirir, diğer bir deyişle, bazı güvenlik açıklarına yönelik düzeltmeler sağlar.  SQL Server 2012 SP4 hakkında daha fazla bilgi için lütfen [buraya](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) bakın.
- Eşitleme kuralı Işleme: üst eşitleme kuralı artık geçerli değilse, hiçbir JOIN koşuluna sahip olmayan giden JOIN eşitleme kuralları de uygulanmış olmalıdır
- Synchronization Service Manager Kullanıcı arabirimine ve eşitleme kuralları düzenleyicisine çeşitli erişilebilirlik düzeltmeleri uygulandı
- Azure AD Connect Sihirbazı: Azure AD Connect bir çalışma grubunda olduğunda AD bağlayıcı hesabı oluşturma hatası
- Azure AD Connect Sihirbazı: Azure AD oturum açma sayfasında, AD etki alanlarında ve Azure AD doğrulanan etki alanlarında herhangi bir uyuşmazlık olduğunda doğrulama onay kutusunu görüntüle
- Otomatik yükseltme denendikten sonra belirli durumlarda otomatik yükseltme durumunu doğru bir şekilde ayarlamak için PowerShell düzeltmesini otomatik olarak yükseltin.
- Azure AD Connect Sihirbazı: daha önce eksik bilgileri yakalamak için telemetri güncelleştirildi
- Azure AD Connect Sihirbazı: AD FS 'den doğrudan kimlik doğrulamaya geçiş yapmak için **Kullanıcı oturum açma görevini Değiştir '** i kullandığınızda aşağıdaki değişiklikler yapılmıştır:
    - Doğrudan kimlik doğrulama Aracısı Azure AD Connect sunucusuna yüklenir ve etki alanını Federasyon 'dan yönetilene dönüştürmeden önce geçişli kimlik doğrulaması özelliği etkinleştirilir.
    - Kullanıcılar artık federe 'dan yönetilene dönüştürülmez. Yalnızca etki alanı (ler) dönüştürülür.
- Azure AD Connect Sihirbazı: Kullanıcı UPN 'nin özel karakterleri desteklemesi için özel karakter Regex güncelleştirmesi olduğunda AD FS birden çok etki alanı Regex doğru değil
- Azure AD Connect Sihirbazı: değişiklik olmadığında spurmerak "kaynak bağlantı noktası özniteliğini yapılandırma" iletisini kaldır 
- Azure AD Connect Sihirbazı: çift Federasyon senaryosu için AD FS desteği
- Azure AD Connect Sihirbazı: yönetilen bir etki alanını Federasyon 'ya dönüştürürken AD FS talepler eklenen etki alanı için güncelleştirilmiyor
- Azure AD Connect Sihirbazı: yüklü paketlerin algılanması sırasında, eski DirSync/Azure AD Eşitleme/Azure AD Connect ilgili ürünleri bulduk. Şimdi eski ürünleri kaldırmayı deneyeceğiz.
- Azure AD Connect Sihirbazı: geçiş kimlik doğrulama aracısının yüklemesi başarısız olduğunda doğru hata Iletisi eşleme
- Azure AD Connect Sihirbazı: "yapılandırma" kapsayıcısını etki alanı OU filtreleme sayfasından kaldırdı
- Eşitleme altyapısı yüklemesi: zaman zaman eşitleme altyapısı 'ndan başarısız olan gereksiz eski mantığı kaldırın MSI Install msi
- Azure AD Connect Sihirbazı: Parola karması eşitleme için Isteğe bağlı özellikler sayfasında açılan pencere yardım metni
- Eşitleme altyapısı çalışma zamanı: bir CS nesnesinin içeri aktarılmış silme ve eşitleme kuralları, nesneyi yeniden sağlamayı deneyeceği senaryoyu düzeltir.
- Sync Engine çalışma zamanı: çevrimiçi bağlantı sorun giderme kılavuzu için yardım bağlantısı ekleme hata bir Içeri aktarma hatası için olay günlüğü
- Eşitleme altyapısı çalışma zamanı: bağlayıcılar numaralandırılırken daha düşük olan eşitleme Zamanlayıcısı bellek kullanımı
- Azure AD Connect Sihirbazı: AD okuma ayrıcalıklarına sahip olmayan bir özel eşitleme hizmeti hesabını çözümlemek için bir sorunu giderme
- Azure AD Connect Sihirbazı: etki alanı ve OU filtreleme seçimlerinin günlüğe kaydedilmesini geliştirme
- Azure AD Connect Sihirbazı: AD FS MFA senaryosu için oluşturulan Federasyon güvenine varsayılan talepler ekleme
- Azure AD Connect Sihirbazı: AD FS Deploy: sunucu ekleme yeni sertifika kullanamaz
- Azure AD Connect Sihirbazı: onPremCredentials bir etki alanı için başlatılmazsa DSSO özel durumu 
- Tercihe bağlı olarak, AD distinguishedName 'dir özniteliğini etkin kullanıcı nesnesinden akar.
- Yüzeysel hatası düzeltildi, ilk OOB eşitleme kuralının 100 yerine 99 olarak ayarlandığını belirleme Önceiydi



## <a name="117510"></a>1.1.751.0
Durum 4/12/2018: yalnızca indirme için yayınlandı

>[!NOTE]
>Bu sürüm Azure AD Connect için bir düzeltmedir

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Eşitleme
#### <a name="fixed-issues"></a>Düzeltilen sorunlar
Bir sorun düzeltildiğinde, Çin kiracılar için otomatik Azure örnek keşfi zaman zaman başarısız oldu.  

### <a name="ad-fs-management"></a>AD FS Yönetimi
#### <a name="fixed-issues"></a>Düzeltilen sorunlar

Yapılandırma yeniden deneme mantığındaki bir sorun oluştu. Bu, "aynı anahtara sahip bir öğe zaten eklenmiş" olduğunu belirten bir ArgumentException ile sonuçlanır.  Bu, tüm yeniden deneme işlemlerinin başarısız olmasına neden olur.

## <a name="117500"></a>1.1.750.0
Durum 3/22/2018: otomatik yükseltme ve indirme için yayınlandı.
>[!NOTE]
>Bu yeni sürüme yükseltme tamamlandığında, otomatik olarak Azure AD Bağlayıcısı için tam eşitleme ve tam içeri aktarma ve AD Bağlayıcısı için tam eşitleme tetikleyecektir. Azure AD Connect ortamınızın boyutuna bağlı olarak bu işlem biraz zaman alabilir, bunu desteklemek için gerekli adımları gerçekleştirdiğinizden emin olun ve bunu yapmak için uygun bir süre bulana kadar yükseltme işlemine devam edin.

>[!NOTE]
>"1.1.524.0 'dan daha sonra derleme dağıtan bazı kiracılar için, oto yükseltme işlevselliği yanlış şekilde devre dışı bırakıldı. Azure AD Connect örneğinizin hala tekrar yükseltme için uygun olduğundan emin olmak için şu PowerShell cmdlet 'ini çalıştırın: "set-ADSyncAutoUpgrade-oto Upgradestate Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Düzeltilen sorunlar

* Set-ADSyncAutoUpgrade cmdlet 'i daha önce otomatik yükseltme durumu askıya alındı olarak ayarlandıysa otomatik yükseltmeyi engeller. Bu işlevsellik artık sonraki derlemelerin yeniden Yükseltilmemesi için değiştirilmiştir.
* "Parola eşitleme" **Kullanıcı oturum açma** sayfası seçeneği "Parola karması eşitleme" olarak değiştirildi.  Azure AD Connect parolalar değil, parola karmalarını eşitler, bu nedenle aslında ortaya çıkan özellikler ile hizalanır.  Daha fazla bilgi için bkz. [Azure AD Connect eşitleme ile parola karması eşitlemeyi uygulama](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Durum: müşteriler seçmek için yayımlandı

>[!NOTE]
>Bu yeni sürüme yükseltme tamamlandığında, otomatik olarak Azure AD Bağlayıcısı için tam eşitleme ve tam içeri aktarma ve AD Bağlayıcısı için tam eşitleme tetikleyecektir. Azure AD Connect ortamınızın boyutuna bağlı olarak bu işlem biraz zaman alabilir. lütfen bunu desteklemek için gerekli adımları gerçekleştirdiğinizden emin olun veya bunu yapmak için uygun bir süre bulana kadar yükseltme adımlarını izleyin.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Düzeltilen sorunlar
* Sonraki sayfaya geçiş yaparken bölüm filtreleme sayfası için arka plan görevleri zamanlama penceresi düzeltmesi.

* Yapılandırmaveritabanı özel eylemi sırasında erişim ihlaline neden bir hata düzeltildi.

* SQL bağlantı zaman aşımından kurtarmak için bir hata düzeltildi.

* SAN joker karakterlerinden oluşan sertifikaların bir önkoşul denetiminde başarısız olduğu bir hata düzeltildi.

* Azure AD Bağlayıcısı dışarı aktarma sırasında mııver. exe ' nin kilitlenmesine neden olan bir hata düzeltildi.

* Yapılandırmayı değiştirmek için Azure AD Connect sihirbazını çalıştırırken DC 'de hatalı parola denemesine oturum açan bir hata düzeltildi.


#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* Genel Veri Koruma Yönetmeliği gizlilik ayarları ekleniyor (GDPR).  Daha fazla bilgi için [buradaki](reference-connect-user-privacy.md)makaleye bakın.

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* Uygulama telemetrisi-yönetici bu veri sınıfını açık/kapalı olarak değiştirebilir

* Azure AD sistem durumu verileri-yönetici, sistem durumu ayarlarını denetlemek için sistem durumu portalını ziyaret etmelidir.
   Hizmet İlkesi değiştirildi sonra aracılar okuyun ve onu zorla.

* Sayfa başlatma için cihaz geri yazma yapılandırma eylemleri ve ilerleme çubuğu eklendi

* Bir ZIP-Text/HTML raporundaki HTML raporu ve tam veri koleksiyonuyla geliştirilmiş genel Tanılamalar

* Otomatik yükseltmenin güvenilirliği geliştirildi ve sunucunun sistem durumunun belirlenebildiğinden emin olmak için ek telemetri eklendi

* AD bağlayıcı hesabında ayrıcalıklı hesaplar için kullanılabilir izinleri kısıtla

  * Yeni yüklemeler için, sihirbaz, MSOL hesabını oluşturduktan sonra ayrıcalıklı hesapların MSOL hesabında sahip olduğu izinleri kısıtlar.

Değişiklikler aşağıdaki şekilde ele alınacaktır:
1. Hızlı yüklemeler
2. Otomatik oluşturma hesabıyla özel yüklemeler
3. Yükleyici, Azure AD Connect temiz yüklemesi sırasında SA ayrıcalığı gerektirmediğinden bu nedenle değiştirildi

* Belirli bir nesne için eşitleme sorunlarını gidermeye yönelik yeni bir yardımcı program eklendi. Azure AD Connect sihirbazının ' nesne eşitleme sorunlarını giderme ' seçeneğinde ek görevde sorun giderin. Şu anda yardımcı program aşağıdakileri denetler:

  * Azure AD kiracısında kullanıcı hesabı ile eşitlenmiş kullanıcı nesnesi arasındaki UserPrincipalName uyuşmazlığı.
  * Nesne filtreleme etki alanı nedeniyle eşitleme filtrelediyseniz
  * Nesne eşitleme filtreleme kuruluş birimi (OU) nedeniyle filtrelediyseniz

* Belirli bir kullanıcı hesabı için şirket içi Active Directory depolanan geçerli parola karmasını eşitlemeye yönelik yeni bir yardımcı program eklendi.

Yardımcı programı, parola değişikliği gerektirmez. Azure AD Connect sihirbazın "parola karma eşitlemesi sorunlarını giderme" seçeneğinde ek görevde sorun giderin.






## <a name="116540"></a>1.1.654.0
Durum: 12 Aralık, 2017

>[!NOTE]
>Bu sürüm Azure AD Connect için güvenlikle ilgili bir düzeltmedir

### <a name="azure-ad-connect"></a>Azure AD Connect
Azure AD Connect AD DS hesabı oluşturduğunda [AD DS hesabına kilit kilitleme erişimi](#lock) bölümünde açıklanan önerilen izin değişikliklerinin otomatik olarak uygulandığından emin olmak için Azure AD Connect sürüm 1.1.654.0 ' ye (ve sonra) bir geliştirme eklenmiştir. 

- Azure AD Connect ayarlarken, yönetici, var olan bir AD DS hesabı sağlayabilir ya da hesabı otomatik olarak oluşturmalarına Azure AD Connect izin verebilir. İzin değişiklikleri, kurulum sırasında Azure AD Connect tarafından oluşturulan AD DS hesabına otomatik olarak uygulanır. Bunlar, yükleme yöneticisi tarafından sunulan mevcut AD DS hesabına uygulanmaz.
- Eski bir Azure AD Connect sürümden 1.1.654.0 (veya sonrası) sürümüne yükselten müşteriler için, izin değişiklikleri yükseltmeden önce oluşturulan mevcut AD DS hesaplara geriye dönük olarak uygulanmaz. Bunlar yalnızca yükseltmeden sonra oluşturulan yeni AD DS hesaplarına uygulanır. Bu, Azure AD 'ye eşitlenecek yeni AD ormanları eklerken oluşur.

>[!NOTE]
>Bu sürüm, yalnızca hizmet hesabının yükleme işlemi tarafından oluşturulduğu yeni Azure AD Connect yüklemelerine yönelik güvenlik açığını ortadan kaldırır. Mevcut yüklemeler veya hesabı kendiniz sağladığınız durumlarda, bu güvenlik açığının mevcut olmadığından emin olun.

#### <a name="lock"></a>AD DS hesabına erişimi kilitle
Şirket içi AD 'de aşağıdaki izin değişikliklerini uygulayarak AD DS hesabına erişimi kilitle:  

*   Belirtilen nesnede devralmayı devre dışı bırak
*   KENDISINE özgü Ace 'Ler hariç belirli bir nesnedeki tüm Ace 'Leri kaldırın. Kendi kendine geldiğinde varsayılan izinleri değiştirmeden tutmak istiyoruz.
*   Bu özel izinleri ata:

Tür     | Ad                          | Access               | Uygulama hedefi
---------|-------------------------------|----------------------|--------------|
İzin Ver    | SISTEMIN                        | Tam denetim         | Bu nesne  |
İzin Ver    | Kuruluş yöneticileri             | Tam denetim         | Bu nesne  |
İzin Ver    | Etki alanı yöneticileri                 | Tam denetim         | Bu nesne  |
İzin Ver    | Yöneticiler                | Tam denetim         | Bu nesne  |
İzin Ver    | Kurumsal etki alanı denetleyicileri | Liste Içerikleri        | Bu nesne  |
İzin Ver    | Kurumsal etki alanı denetleyicileri | Tüm özellikleri oku  | Bu nesne  |
İzin Ver    | Kurumsal etki alanı denetleyicileri | Okuma Izinleri     | Bu nesne  |
İzin Ver    | Kimliği doğrulanmış kullanıcılar           | Liste Içerikleri        | Bu nesne  |
İzin Ver    | Kimliği doğrulanmış kullanıcılar           | Tüm özellikleri oku  | Bu nesne  |
İzin Ver    | Kimliği doğrulanmış kullanıcılar           | Okuma Izinleri     | Bu nesne  |

AD DS hesabının ayarlarını sağlamkılabilmeniz için [Bu PowerShell betiğini](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978)çalıştırabilirsiniz. PowerShell betiği yukarıda belirtilen izinleri AD DS hesabına atayacaktır.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Önceden var olan bir hizmet hesabını sağlamtıran PowerShell betiği

PowerShell betiğini kullanmak için, bu ayarları önceden varolan bir AD DS hesabına (kuruluşunuz tarafından sağlanmış veya önceki bir Azure AD Connect yüklemesi tarafından oluşturulan) uygulamak için lütfen yukarıdaki belirtilen bağlantıdan betiği indirin.

##### <a name="usage"></a>Kullanım:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Konum 

**$ObjectDN** = izinlerinin daha sıkı bir şekilde getirilmesi gereken Active Directory hesabı.

**$Credential** = $ObjectDN hesabındaki izinleri kısıtlamak için gereken ayrıcalıklara sahip yönetici kimlik bilgileri. Bu ayrıcalıklar genellikle kuruluş veya etki alanı yöneticisi tarafından tutulur. Hesap arama hatalarından kaçınmak için yönetici hesabının tam etki alanı adını kullanın. Örnek: contoso. com\admin.

>[!NOTE] 
>$credential. Kullanıcı adı FQDN\username biçiminde olmalıdır. Örnek: contoso. com\admin 

##### <a name="example"></a>Örnek:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Bu güvenlik açığı yetkisiz erişim elde etmek için kullanılıyor mu?

Azure AD Connect yapılandırmanızı tehlikeye almak için bu güvenlik açığının kullanıldığını görmek için, hizmet hesabının son parola sıfırlama tarihini doğrulamanız gerekir.  Zaman damgası beklenmedik şekilde, bu parola sıfırlama olayı için olay günlüğü aracılığıyla daha fazla araştırma yapılması gerekir.

Daha fazla bilgi için bkz. [Microsoft Güvenlik Danışmanlığı 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Durum: Ekim 27 2017

>[!NOTE]
>Bu derleme, Azure AD Connect otomatik yükseltme özelliği aracılığıyla müşteriler tarafından kullanılamaz.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Sorun düzeltildi
* Azure AD Connect ve Azure AD Connect Health Aracısı arasında sürüm uyumluluğu sorunu düzeltildi (eşitleme için). Bu sorun, 1.1.647.0 sürümüne yerinde yükseltme Azure AD Connect, ancak şu anda sistem durumu Aracısı sürümü 3.0.127.0 olan müşterileri etkiler. Yükseltmeden sonra, sistem durumu Aracısı artık Azure AD Connect eşitleme hizmeti ile Azure AD Sistem Sağlığı Hizmeti arasında sistem durumu verileri gönderemez. Bu düzeltmeyle birlikte Azure AD Connect yerinde yükseltme sırasında durum Aracısı sürümü 3.0.129.0 yüklenir. Sistem Durumu Aracısı sürüm 3.0.129.0 Azure AD Connect sürüm 1.1.649.0 ile uyumluluk sorununa sahip değil.


## <a name="116470"></a>1.1.647.0
Durum: Ekim 19 2017

> [!IMPORTANT]
> Azure AD Connect sürüm 1.1.647.0 ve Azure AD Connect Health Aracısı (eşitleme için) arasında bilinen bir uyumluluk sorunu var 3.0.127.0. Bu sorun, sistem durumu aracısının Azure AD Connect eşitleme hizmeti (nesne eşitleme hataları ve çalışma geçmişi verileri dahil) ile Azure AD Sistem Sağlığı Hizmeti arasında sistem durumu verileri göndermesini engeller. Azure AD Connect dağıtımınızı 1.1.647.0 sürümüne el ile yükseltmeden önce lütfen Azure AD Connect sunucunuzda yüklü olan Azure AD Connect Health aracısının geçerli sürümünü doğrulayın. Bunu, *Denetim Masası → Ekle Program Ekle* ' ye giderek, uygulama *Microsoft Azure AD Connect Health Aracısı*' na bakın. Sürümü 3.0.127.0 ise, yükseltmeden önce bir sonraki Azure AD Connect sürümünün kullanılabilmesini beklemeniz önerilir. Sistem Durumu Aracısı sürümü 3.0.127.0 değilse, el ile, yerinde yükseltme işlemine devam etmek iyi olur. Bu sorunun, yeni Azure AD Connect yüklemesi gerçekleştiren esnek yükseltme veya müşterileri etkilemediğini unutmayın.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Düzeltilen sorunlar
* Azure AD Connect sihirbazında *Kullanıcı oturum açma görevini değiştirme* ile ilgili bir sorun düzeltildi:

  * Bu sorun, parola eşitlemesi **etkinken**mevcut bir Azure AD Connect dağıtımınız varsa ve Kullanıcı oturum açma yöntemini *doğrudan kimlik doğrulama*olarak ayarlamaya çalışıyorsanız oluşur. Değişiklik uygulanmadan önce, sihirbaz "*parola eşitlemesini devre dışı bırak*" isteminde yanlış bir şekilde gösteriliyor. Ancak, değişiklik uygulandıktan sonra parola eşitleme etkin kalır. Bu düzeltmeyle, sihirbaz artık istemi göstermez.

  * Tasarım yaparak sihirbaz, Kullanıcı *oturum açma yöntemini değiştirme* görevini kullanarak Kullanıcı oturum açma yöntemini güncelleştirdiğinizde parola eşitlemesini devre dışı bırakır. Bu, birincil kullanıcı oturum açma yöntemi olarak doğrudan kimlik doğrulamayı veya Federasyonu etkinleştirse bile, parola eşitlemesini tutmak isteyen müşterilerin kesintiye uğramasını önlemek için kullanılır.
  
  * Kullanıcı oturum açma yöntemini güncelleştirdikten sonra parola eşitlemesini devre dışı bırakmak istiyorsanız, sihirbazda *eşitleme yapılandırmasını Özelleştir* görevini yürütmelisiniz. *Isteğe bağlı özellikler* sayfasına gittiğinizde *parola eşitleme* seçeneğinin işaretini kaldırın.
  
  * Sorunsuz çoklu oturum açmayı etkinleştirmeyi/devre dışı bırakmayı denediğinizde de aynı sorunun oluştuğunu unutmayın. Özellikle, parola eşitleme etkin olan bir Azure AD Connect dağıtımınız vardır ve Kullanıcı oturum açma yöntemi zaten *geçişli kimlik doğrulama*olarak yapılandırılmıştır. Kullanıcı oturum açma görevini *Değiştir* ' i kullanarak, Kullanıcı oturum açma yöntemi "geçişli kimlik doğrulaması" olarak yapılandırıldıklarında, *sorunsuz çoklu oturum açma seçeneğini etkinleştirin* /işaretini kaldırın. Değişiklik uygulanmadan önce, sihirbaz "*parola eşitlemesini devre dışı bırak*" isteminde yanlış bir şekilde gösteriliyor. Ancak, değişiklik uygulandıktan sonra parola eşitleme etkin kalır. Bu düzeltmeyle, sihirbaz artık istemi göstermez.

* Azure AD Connect sihirbazında *Kullanıcı oturum açma görevini değiştirme* ile ilgili bir sorun düzeltildi:

  * Bu sorun, parola eşitlemesi **devre dışı bırakılmış**bir Azure AD Connect dağıtımınız varsa ve Kullanıcı oturum açma yöntemini *doğrudan kimlik doğrulama*olarak ayarlamaya çalışıyorsanız oluşur. Değişiklik uygulandığında, sihirbaz hem geçişli kimlik doğrulaması hem de parola eşitlemeyi mümkün olur. Bu düzeltmeyle, sihirbaz artık parola eşitlemeyi etkinleştirmezse.

  * Daha önce, parola eşitleme doğrudan kimlik doğrulamayı etkinleştirmek için bir önkoşul idi. Kullanıcı oturum açma yöntemini *geçişli kimlik doğrulaması*olarak ayarladığınızda, sihirbaz hem geçişli kimlik doğrulaması hem de parola eşitlemesini etkinleştirir. Son olarak parola eşitleme, önkoşul olarak kaldırılmıştır. Azure AD Connect sürüm 1.1.557.0 bir parçası olarak, Kullanıcı oturum açma yöntemini *doğrudan kimlik doğrulama*olarak ayarladığınızda parola eşitlemesini etkinleştirmemelidir Azure AD Connect bir değişiklik yapılmıştır. Ancak, değişiklik yalnızca Azure AD Connect yüklemesine uygulandı. Bu düzeltmeyle aynı değişiklik *Kullanıcı oturum açma görevini Değiştir* görevine de uygulanır.
  
  * Sorunsuz çoklu oturum açmayı etkinleştirmeyi/devre dışı bırakmayı denediğinizde de aynı sorunun oluştuğunu unutmayın. Özellikle, parola eşitleme devre dışı bırakılmış bir Azure AD Connect dağıtımınız vardır ve Kullanıcı oturum açma yöntemi zaten *geçişli kimlik doğrulama*olarak yapılandırılmıştır. Kullanıcı oturum açma görevini *Değiştir* ' i kullanarak, Kullanıcı oturum açma yöntemi "geçişli kimlik doğrulaması" olarak yapılandırıldıklarında, *sorunsuz çoklu oturum açma seçeneğini etkinleştirin* /işaretini kaldırın. Değişiklik uygulandığında sihirbaz parola eşitlemesini mümkün olur. Bu düzeltmeyle, sihirbaz artık parola eşitlemeyi etkinleştirmezse. 

* Azure AD Connect yükseltmenin başarısız olmasına neden olan bir sorun düzeltildi *, "eşitleme hizmeti yükseltilemiyor*" hatası oluştu. Ayrıca, eşitleme hizmeti artık olay hatasıyla başlayamaz "*veritabanının sürümü yüklü ikili dosyaların sürümünden daha yeni olduğundan hizmet başlatılamadı*". Bu sorun, yükseltmenin gerçekleştiği yöneticinin Azure AD Connect tarafından kullanılmakta olan SQL Server 'da sysadmin ayrıcalığı olmadığında oluşur. Bu düzeltmeyle Azure AD Connect, yalnızca yöneticinin yükseltme sırasında ADSync veritabanına db_owner ayrıcalığına sahip olmasını gerektirir.

* [Sorunsuz çoklu oturum açmayı](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)etkinleştirmiş etkilenen müşterilerin etkilediği Azure AD Connect bir yükseltme sorunu düzeltildi. Azure AD Connect yükseltildikten sonra, özellik etkin ve tamamen işlevsel olsa bile, sorunsuz çoklu oturum açma işlemi Azure AD Connect sihirbazında devre dışı olarak görünür. Bu düzeltmeyle, özellik artık sihirbazda etkin şekilde doğru şekilde görünür.

* Kaynak çıpası ile ilgili hiçbir değişiklik yapılmasa bile, *yapılandırmaya hazırlanma* sayfasında her zaman "*kaynak bağlayıcısını yapılandırma*" istemi göstermek için Azure AD Connect sihirbaza neden olan bir sorun düzeltildi.

* Azure AD Connect el ile yerinde yükseltmesini gerçekleştirirken, müşterinin ilgili Azure AD kiracısının genel yönetici kimlik bilgilerini sağlaması gerekir. Daha önce, genel yönetici kimlik bilgileri farklı bir Azure AD kiracısına ait olsa bile yükseltme devam edebiliyordu. Yükseltme başarıyla tamamlandı olarak göründüğünde, bazı konfigürasyonlar yükseltme ile düzgün bir şekilde kalıcı olmaz. Bu değişiklik ile, belirtilen kimlik bilgileri Azure AD kiracısı ile eşleşmezse, sihirbaz yükseltmenin devam etmesini engeller.

* El ile Yükseltmenin başlangıcında Azure AD Connect Health hizmeti 'ni gereksiz şekilde yeniden çalıştıran gereksiz mantık kaldırıldı.


#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* Microsoft Almanya bulutu ile Azure AD Connect ayarlamak için gereken adımları basitleştirmek için mantık eklendi. Daha önce, bu makalede açıklandığı gibi, Azure AD Connect sunucusundaki belirli kayıt defteri anahtarlarını Microsoft Almanya bulutuyla doğru çalışması için güncelleştirmeniz gerekir. Artık Azure AD Connect, kiracınızın Microsoft Almanya bulutu 'nda kurulum sırasında belirtilen genel yönetici kimlik bilgilerine göre otomatik olarak algılanmasını sağlayabilir.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Note: eşitleme hizmeti, kendi özel Scheduler uygulamanızı geliştirmenize olanak sağlayan bir WMI arabirimine sahiptir. Bu arabirim artık kullanım dışıdır ve 30 Haziran 2018 ' den sonra gelen Azure AD Connect gelecek sürümlerinden kaldırılacak. Eşitleme zamanlamasını özelleştirmek isteyen müşteriler [yerleşik zamanlayıcıyı](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler)kullanmalıdır.

#### <a name="fixed-issues"></a>Düzeltilen sorunlar
* Azure AD Connect Sihirbazı, değişiklikleri şirket içi Active Directory eşitlenmesi için gereken AD Bağlayıcısı hesabını oluşturduğunda, PublicFolder nesnelerini okumak için gereken izinleri hesaba doğru atamaz. Bu sorun hem hızlı yükleme hem de özel yüklemeyi etkiler. Bu değişiklik sorunu düzeltir.

* Azure AD Connect Sihirbazı sorun giderme sayfasının Windows Server 2016 ' den çalıştırılan Yöneticiler için düzgün işlenmemesine neden olan bir sorun düzeltildi.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* Azure AD Connect Sihirbazı sorun giderme sayfasını kullanarak parola eşitleme sorunlarını giderirken, sorun giderme sayfası artık etki alanına özgü durumu döndürür.

* Daha önce, parola karma eşitlemesini etkinleştirmeye çalıştıysanız Azure AD Connect, AD Bağlayıcısı hesabının şirket içi AD 'den parola karmalarını eşitlemek için gerekli izinlere sahip olup olmadığını doğrulamaz. Şimdi, AD Bağlayıcısı hesabının yeterli izni yoksa Azure AD Connect sihirbaz sizi doğrular ve sizi uyarır.

### <a name="ad-fs-management"></a>AD FS Yönetimi
#### <a name="fixed-issue"></a>Sorun düzeltildi
* [MS-DS-ımnbu GUID 'Nin kaynak bağlantısı olarak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) kullanımıyla ilgili bir sorun düzeltildi. Bu sorun, Kullanıcı oturum açma yöntemi olarak *AD FS Federasyonu* yapılandıran müşterileri etkiler. Sihirbazda *kaynak bağlantısını yapılandır* görevini yürüttüğünüzde, ' Azure AD Connect, ImmutableID için kaynak özniteliği olarak * ms-DS-ımıbu GUID ' i kullanarak geçiş yapar. Bu değişikliğin bir parçası olarak, Azure AD Connect AD FS ImmutableID talep kurallarını güncelleştirmeye çalışır. Ancak, Azure AD Connect AD FS yapılandırmak için gereken yönetici kimlik bilgilerine sahip olmadığı için bu adım başarısız oldu. Bu düzeltmeyle Azure AD Connect, şimdi *kaynak bağlantısını yapılandır* görevini yürüttüğünüzde AD FS için yönetici kimlik bilgilerini girmenizi ister.



## <a name="116140"></a>1.1.614.0
Durum: Eylül 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Bilinen sorunlar
* Azure AD Connect yükseltmenin "*eşitleme hizmeti yükseltilemiyor*" hatasıyla başarısız olmasına neden olan bilinen bir sorun var. Ayrıca, eşitleme hizmeti artık olay hatasıyla başlayamaz "*veritabanının sürümü yüklü ikili dosyaların sürümünden daha yeni olduğundan hizmet başlatılamadı*". Bu sorun, yükseltmenin gerçekleştiği yöneticinin Azure AD Connect tarafından kullanılmakta olan SQL Server 'da sysadmin ayrıcalığı olmadığında oluşur. Dbo izinleri yeterli değildir.

* [Kesintisiz çoklu oturum açma](how-to-connect-sso.md)özelliği sunan müşterileri etkileyen Azure AD Connect yükseltmesinde bilinen bir sorun vardır. Azure AD Connect yükseltildikten sonra, özellik etkin olmaya devam etse bile Özellik sihirbazda devre dışı olarak görünür. Bu soruna yönelik bir çözüm gelecek sürümde sunulacaktır. Bu görüntü sorunuyla ilgili olan müşteriler, sihirbazda sorunsuz çoklu oturum açmayı etkinleştirerek el ile düzeldebilir.

#### <a name="fixed-issues"></a>Düzeltilen sorunlar
* Azure AD Connect, şirket içi AD FS talep kurallarının güncelleştirilmesini engelleyen bir sorun düzeltildi ve bu sayede, [kaynak bağlantısı olarak ms-DS-](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) IBir GUID özelliği etkinleştiriliyor. Bu sorun, oturum açma yöntemi olarak yapılandırılmış AD FS sahip olan mevcut bir Azure AD Connect dağıtımı için özelliği etkinleştirmeye çalışırsanız oluşur. Bu sorun, sihirbaz AD FS ' deki talep kurallarını güncelleştirmeye çalışmadan önce ADFS kimlik bilgilerini istemez.
* Şirket içi AD ormanında NTLM devre dışı bırakılmışsa Azure AD Connect yüklemede başarısız olmasına neden olan bir sorun düzeltildi. Bu sorun, Kerberos kimlik doğrulaması için gerekli güvenlik bağlamlarını oluştururken Azure AD Connect Sihirbazı 'nın tam nitelikli kimlik bilgileri sağlamamasından kaynaklanır. Bu, Kerberos kimlik doğrulamasının başarısız olmasına ve Azure AD Connect sihirbazının NTLM kullanmaya geri dönmesine neden olur.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Düzeltilen sorunlar
* Etiket özniteliği doldurulmamışsa yeni eşitleme kuralının oluşturulabileceği bir sorun düzeltildi.
* Kerberos kullanılabilir olsa bile NTLM kullanarak parola eşitleme için şirket içi AD 'ye bağlanmasına Azure AD Connect neden olan bir sorun düzeltildi. Bu sorun, şirket içi AD topolojisinde bir yedeklemeden geri yüklenen bir veya daha fazla etki alanı denetleyicisi varsa meydana gelir.
* Yükseltmeden sonra tam eşitleme adımlarının gereksiz yere oluşmasına neden olan bir sorun düzeltildi. Genel olarak, kullanıma hazır eşitleme kurallarında değişiklikler varsa yükseltmeden sonra tam eşitleme adımlarının çalıştırılması gerekir. Bu sorun, yeni satır karakterleri ile eşitleme kuralı ifadesiyle karşılaşıldığında yanlış bir değişikliği algılayan değişiklik algılama mantığındaki bir hata nedeniyle oluştu. Okunabilirliğini geliştirmek için yeni satır karakterleri eşitleme kuralı ifadesine eklenir.
* Otomatik yükseltmeden sonra Azure AD Connect sunucusunun düzgün çalışmamasına neden olabilecek bir sorun düzeltildi. Bu sorun, sürüm 1.1.443.0 (veya önceki sürümler) olan Azure AD Connect sunucularını etkiler. Sorun hakkında daha fazla bilgi için, [Azure AD Connect otomatik bir yükseltmeden sonra düzgün şekilde çalışmıyor](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade)makalesine başvurun.
* Hatalarla karşılaşıldığında, otomatik yükseltmenin 5 dakikada bir yeniden denenmesine neden olabilecek bir sorun düzeltildi. Düzeltmeyle birlikte, hata ile karşılaşıldığında otomatik yükseltme üstel geri dönüş ile yeniden dener.
* Parola eşitleme olayının 611 hatalı olarak Windows uygulama olay günlüklerinde **hata**yerine **bilgilendirici** olarak gösterildiği bir sorun düzeltildi. Olay 611, parola eşitleme bir sorunla karşılaştığında oluşturulur. 
* Azure AD Connect sihirbazında, grup geri yazma için gerekli bir OU seçilmeksizin grup geri yazma özelliğinin etkinleştirilmesini sağlayan bir sorun düzeltildi.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* Ek görevler altında Azure AD Connect sihirbazına bir sorun giderme görevi eklendi. Müşteriler, parola eşitlemeyle ilgili sorunları gidermek ve genel tanılamayı toplamak için bu görevden faydalanabilir. Gelecekte, sorun giderme görevi, Dizin eşitlemeyle ilgili diğer sorunları içerecek şekilde genişletilir.
* Azure AD Connect artık **var olan veritabanını kullan**adlı yeni bir yükleme modunu desteklemektedir. Bu yükleme modu, müşterilerin var olan bir ADSync veritabanını belirten Azure AD Connect yüklemesine olanak tanır. Bu özellik hakkında daha fazla bilgi için [var olan bir veritabanını kullanma](how-to-connect-install-existing-database.md)makalesine bakın.
* Daha iyi güvenlik için Azure AD Connect, Dizin eşitleme için Azure AD 'ye bağlanmak üzere artık varsayılan olarak TLS 1.2 'yi kullanmaktır. Daha önce varsayılan olarak TLS 1.0 idi.
* Azure AD Connect parola eşitleme Aracısı başlatıldığında, parola eşitleme için Azure AD iyi bilinen uç noktaya bağlanmaya çalışır. Başarılı bağlantı kurulduğunda, bölgeye özgü bir uç noktaya yeniden yönlendirilir. Daha önce, parola eşitleme Aracısı yeniden başlatılana kadar bölgeye özgü uç noktayı önbelleğe alır. Artık, aracı, bölgeye özgü uç nokta ile bağlantı sorunuyla karşılaşırsa, önbelleği temizler ve iyi bilinen uç noktayla yeniden dener. Bu değişiklik, önbelleğe alınmış bölgeye özgü uç nokta artık kullanılabilir olmadığında parola eşitlemesinin farklı bölgeye özgü bir uç noktaya yük devredebilmesini sağlar.
* Şirket içi AD ormanında yapılan değişiklikleri eşitlemek için bir AD DS hesabı kullanmanız gerekir. (İ) AD DS hesabı oluşturup kendi kimlik bilgisini Azure AD Connect veya (ii) bir kurumsal yönetici kimlik bilgilerini sağlayıp sizin için AD DS hesabı oluşturmasına Azure AD Connect izin verebilirsiniz. Daha önce, (i) Azure AD Connect sihirbazında varsayılan seçenektir. Şimdi, (ii) varsayılan seçenektir.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* Microsoft Azure Kamu bulut ve Microsoft Bulut Almanya desteği eklendi.

### <a name="ad-fs-management"></a>AD FS Yönetimi
#### <a name="fixed-issues"></a>Düzeltilen sorunlar
* AD Prep PowerShell modülündeki Initialize-ADSyncNGCKeysWriteBack cmdlet 'i, ACL 'Leri cihaz kayıt kapsayıcısına yanlış bir şekilde uyguluyor ve bu nedenle yalnızca var olan izinleri devralacak.  Bu, eşitleme hizmeti hesabının doğru izinlere sahip olması için güncelleştirildi.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* AAD Connect Verify ADFS Login görevi, Microsoft Online 'da oturum açma bilgilerini doğrulamak ve yalnızca ADFS 'den belirteç alımı değil olarak güncelleştirildi.
* AAD Connect kullanarak yeni bir ADFS grubu ayarlarken, ADFS kimlik bilgilerini soran sayfa, kullanıcıdan ADFS ve WAP sunucuları sağlamaları istenmeden önce gerçekleşmesini sağlayacak şekilde taşınmıştır.  Bu, AAD Connect 'in belirtilen hesabın doğru izinlere sahip olduğunu denetlemesi için izin verir.
* AAD Connect yükseltmesi sırasında, ADFS AAD güveninin güncelleştirilmesi başarısız olursa artık yükseltme işlemi başarısız olmayacaktır.  Bu durumda, kullanıcıya uygun bir uyarı iletisi gösterilir ve AAD Connect ek görevi aracılığıyla güveni sıfırlamaya devam edilmelidir.

### <a name="seamless-single-sign-on"></a>Kesintisiz çoklu oturum açma
#### <a name="fixed-issues"></a>Düzeltilen sorunlar
* [Sorunsuz çoklu oturum açmayı](how-to-connect-sso.md)etkinleştirmeye çalıştığınızda Azure AD Connect sihirbazının hata döndürmesine neden olan bir sorun düzeltildi. Hata iletisi *"Microsoft Azure AD Connect Authentication Agent yapılandırması başarısız oldu."* Bu sorun, bu [makalede](how-to-connect-pta-upgrade-preview-authentication-agents.md)açıklanan adımlara göre [geçişli kimlik doğrulaması](how-to-connect-sso.md) için kimlik doğrulama aracılarının önizleme sürümünü el ile yükselten mevcut müşterileri etkiler.


## <a name="115610"></a>1.1.561.0
Durum: Temmuz 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Sorun düzeltildi

* Kullanıma hazır eşitleme kuralının "AD-Kullanıcı ImmutableID" olarak kaldırılmasına neden olan bir sorun düzeltildi:

  * Sorun Azure AD Connect yükseltildiğinde veya Azure AD Connect eşitleme yapılandırmasını güncelleştirmek için Azure AD Connect sihirbazında görev seçeneği *güncelleştirme eşitleme yapılandırması* kullanıldığında oluşur.
  
  * Bu eşitleme kuralı, [MS-DS-ımnbu GUID 'Yi kaynak bağlantısı olarak](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)etkinleştirmiş olan müşteriler için geçerlidir. Bu özellik 1.1.524.0 sürümünde ve sonrasında tanıtılmıştır. Eşitleme kuralı kaldırıldığında, Azure AD Connect, şirket içi AD ms-DS-ımced GUID özniteliğini Objectguıd öznitelik değeriyle doldurmaz. Yeni kullanıcıların Azure AD 'ye sağlanmasını engellemez.
  
  * Bu çözüm, özellik etkin olduğu sürece eşitleme kuralının yükseltme sırasında veya yapılandırma değişikliği sırasında kaldırılmamasını sağlar. Bu sorundan etkilenen mevcut müşteriler için, bu, bu Azure AD Connect sürümüne yükselttikten sonra eşitleme kuralının geri eklenmesini de sağlar.

* Hazır olmayan eşitleme kurallarına 100 'den daha az öncelik değeri olmasına neden olan bir sorun düzeltildi:

  * Genel olarak, 0-99 öncelik değerleri özel eşitleme kuralları için ayrılmıştır. Yükseltme sırasında, kullanıma hazır eşitleme kuralları için öncelik değerleri, eşitleme kuralı değişikliklerini karşılayacak şekilde güncelleştirilir. Bu sorundan dolayı, kullanıma hazır eşitleme kurallarına 100 'den daha az öncelik değeri atanabilir.
  
  * Bu sorun, yükseltmenin yükseltme sırasında oluşmasını önler. Ancak, sorundan etkilenen mevcut müşteriler için öncelik değerlerini geri yüklemez. Gelecekte geri yüklemeyle ilgili yardım için ayrı bir düzelme sunulacaktır.

* Azure AD Connect sihirbazında [etki alanı ve OU filtreleme ekranının](how-to-connect-install-custom.md#domain-and-ou-filtering) , OU tabanlı filtreleme etkin olsa bile *tüm etki alanlarını ve* OU seçeneklerini seçili olarak eşitlemesini gösterdiği bir sorun düzeltildi.

*   *Yenileme* düğmesine tıklanınca Synchronization Service Manager [dizin bölümlerini yapılandırma ekranının](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) bir hata döndürmesine neden olan bir sorun düzeltildi. Hata iletisi *"etki alanları yenilenirken bir hata oluştu: ' System. Collections. ArrayList ' türündeki nesne ' Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject ' türüne yayınlanamıyor.* Hata, mevcut bir AD ormanına yeni AD etki alanı eklendiğinde ve Yenile düğmesini kullanarak Azure AD Connect güncelleştirmeye çalıştığınızda oluşur.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* Aşağıdaki yapılandırmalara sahip müşterileri desteklemek için [otomatik yükseltme özelliği](how-to-connect-install-automatic-upgrade.md) genişletildi:
  * Cihaz geri yazma özelliğini etkinleştirdiniz.
  * Grup geri yazma özelliğini etkinleştirdiniz.
  * Yükleme bir hızlı ayarlar veya DirSync yükseltmesi değildir.
  * Meta veri deposunda 100.000 taneden fazla nesne var.
  * Birden fazla ormana bağlanıyorsunuz. Hızlı Kurulum yalnızca bir ormana bağlanır.
  * AD Bağlayıcısı hesabı artık varsayılan MSOL_ hesabı değil.
  * Sunucu, hazırlama modunda olacak şekilde ayarlanır.
  * Kullanıcı geri yazma özelliğini etkinleştirdiniz.
  
  >[!NOTE]
  >Otomatik yükseltme özelliğinin kapsam genişletmesi, müşterileri Azure AD Connect Build 1.1.105.0 ve After ile etkiler. Azure AD Connect sunucunuzun otomatik olarak yükseltilmesini istemiyorsanız, aşağıdaki cmdlet 'i Azure AD Connect sunucunuzda çalıştırmanız gerekir: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Otomatik yükseltmeyi etkinleştirme/devre dışı bırakma hakkında daha fazla bilgi için, [Azure AD Connect: otomatik yükseltme](how-to-connect-install-automatic-upgrade.md)makalesine bakın.

## <a name="115580"></a>1.1.558.0
Durum: yayınlanmayacak. Bu derlemedeki değişiklikler 1.1.561.0 sürümüne dahildir.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Sorun düzeltildi

* OU tabanlı filtreleme yapılandırması güncellendiğinde, "AD-Kullanıcı ImmutableID" olarak kullanıma hazır eşitleme kuralının kaldırılmasına neden olan bir sorun düzeltildi. Bu eşitleme kuralı, [MS-DS-ımnbu GUID, kaynak bağlayıcı özelliği olarak](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)gereklidir.

* Azure AD Connect sihirbazında [etki alanı ve OU filtreleme ekranının](how-to-connect-install-custom.md#domain-and-ou-filtering) , OU tabanlı filtreleme etkin olsa bile *tüm etki alanlarını ve* OU seçeneklerini seçili olarak eşitlemesini gösterdiği bir sorun düzeltildi.

*   *Yenileme* düğmesine tıklanınca Synchronization Service Manager [dizin bölümlerini yapılandırma ekranının](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) bir hata döndürmesine neden olan bir sorun düzeltildi. Hata iletisi *"etki alanları yenilenirken bir hata oluştu: ' System. Collections. ArrayList ' türündeki nesne ' Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject ' türüne yayınlanamıyor.* Hata, mevcut bir AD ormanına yeni AD etki alanı eklendiğinde ve Yenile düğmesini kullanarak Azure AD Connect güncelleştirmeye çalıştığınızda oluşur.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* Aşağıdaki yapılandırmalara sahip müşterileri desteklemek için [otomatik yükseltme özelliği](how-to-connect-install-automatic-upgrade.md) genişletildi:
  * Cihaz geri yazma özelliğini etkinleştirdiniz.
  * Grup geri yazma özelliğini etkinleştirdiniz.
  * Yükleme bir hızlı ayarlar veya DirSync yükseltmesi değildir.
  * Meta veri deposunda 100.000 taneden fazla nesne var.
  * Birden fazla ormana bağlanıyorsunuz. Hızlı Kurulum yalnızca bir ormana bağlanır.
  * AD Bağlayıcısı hesabı artık varsayılan MSOL_ hesabı değil.
  * Sunucu, hazırlama modunda olacak şekilde ayarlanır.
  * Kullanıcı geri yazma özelliğini etkinleştirdiniz.
  
  >[!NOTE]
  >Otomatik yükseltme özelliğinin kapsam genişletmesi, müşterileri Azure AD Connect Build 1.1.105.0 ve After ile etkiler. Azure AD Connect sunucunuzun otomatik olarak yükseltilmesini istemiyorsanız, aşağıdaki cmdlet 'i Azure AD Connect sunucunuzda çalıştırmanız gerekir: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Otomatik yükseltmeyi etkinleştirme/devre dışı bırakma hakkında daha fazla bilgi için, [Azure AD Connect: otomatik yükseltme](how-to-connect-install-automatic-upgrade.md)makalesine bakın.

## <a name="115570"></a>1.1.557.0
Durum: Temmuz 2017

>[!NOTE]
>Bu derleme, Azure AD Connect otomatik yükseltme özelliği aracılığıyla müşteriler tarafından kullanılamaz.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Sorun düzeltildi
* Var olan hizmet bağlantı noktası nesnesi üzerinde yapılandırılan doğrulanmış etki alanının hala geçerli bir etki alanı olsa bile değiştirilmesini sağlayan Initialize-ADSyncDomainJoinedComputerSync cmdlet 'inde bir sorun düzeltildi. Bu sorun, Azure AD kiracınızda hizmet bağlantı noktasını yapılandırmak için kullanılabilecek birden fazla doğrulanmış etki alanı olduğunda oluşur.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* Parola geri yazma özelliği artık Microsoft Azure Kamu bulut ve Microsoft Bulut Almanya ile önizlemede kullanılabilir. Farklı hizmet örnekleri için Azure AD Connect desteği hakkında daha fazla bilgi için, bkz. [Azure AD Connect: örneklere yönelik özel konular](reference-connect-instances.md).

* Initialize-ADSyncDomainJoinedComputerSync cmdlet 'inde artık AzureADDomain adlı yeni bir isteğe bağlı parametre var. Bu parametre, hizmet bağlantı noktasını yapılandırmak için kullanılacak doğrulanmış etki alanını belirtmenize olanak sağlar.

### <a name="pass-through-authentication"></a>Doğrudan Kimlik Doğrulama

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* Doğrudan kimlik doğrulaması için gereken aracının adı, *Microsoft Azure AD uygulama proxy Bağlayıcısı* 'Ndan *Microsoft Azure AD Connect kimlik doğrulama aracısına*değiştirilmiştir.

* Doğrudan kimlik doğrulamasını etkinleştirme, varsayılan olarak parola karması eşitlemesini artık etkinleştirmiyor.


## <a name="115530"></a>1.1.553.0
Durum: Haziran 2017

> [!IMPORTANT]
> Bu derlemede tanıtılan şema ve eşitleme kuralı değişiklikleri var. Azure AD Connect eşitleme hizmeti, yükseltmeden sonra tam Içeri aktarma ve tam eşitleme adımlarını tetikler. Değişikliklerin ayrıntıları aşağıda açıklanmıştır. Yükseltmeden sonra tam Içeri aktarma ve tam eşitleme adımlarını geçici olarak ertelemek için, [yükseltmeden sonra tam eşitlemeyi erteleme](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)makalesine başvurun.
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Bilinen sorun
* Azure AD Connect eşitleme ile [OU tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) kullanan müşterileri etkileyen bir sorun vardır. Azure AD Connect sihirbazında [etki alanı ve OU filtreleme sayfasına](how-to-connect-install-custom.md#domain-and-ou-filtering) gittiğinizde aşağıdaki davranış beklenmektedir:
  * OU tabanlı filtreleme etkinse, **Seçilen etki alanlarını Eşitle ve OU 'lar** seçeneği seçilidir.
  * Aksi takdirde, **tüm etki alanlarını ve OU 'Ları Eşitle** seçeneği seçilidir.

Bu sorun, Sihirbazı çalıştırdığınızda **tüm etki alanlarını ve OU 'Ları eşitleme seçeneğinin** her zaman seçili olduğunu ortaya çıkar.  Bu durum, OU tabanlı filtreleme daha önce yapılandırılmış olsa bile oluşur. Herhangi bir AAD Connect yapılandırma değişikliğini kaydetmeden önce, **Seçilen etki alanlarını ve OU 'Ları eşitle seçeneğinin** belirlendiğinden emin olun ve eşitlenmesi gereken tüm OU 'ların yeniden etkinleştirildiğini doğrulayın. Aksi takdirde, OU tabanlı filtreleme devre dışı bırakılır.

#### <a name="fixed-issues"></a>Düzeltilen sorunlar

* Bir Azure AD yöneticisinin şirket içi AD ayrıcalıklı kullanıcı hesabının parolasını sıfırlamasına izin veren parola geri yazma ile ilgili bir sorun düzeltildi. Bu sorun, Azure AD Connect ayrıcalıklı hesap için parola sıfırlama izni verildiğinde oluşur. Bu sorun, yönetici bu hesabın sahibi olmadığı takdirde bir Azure AD yöneticisinin rastgele bir şirket içi AD ayrıcalıklı kullanıcı hesabının parolasını sıfırlamasına izin vermeyerek Azure AD Connect bu sürümünde karşılanır. Daha fazla bilgi için [Güvenlik Danışmanlığı 4033453](https://technet.microsoft.com/library/security/4033453)' a bakın.

* Azure AD Connect, şirket içi AD ms-DS-ımced GUID özniteliğinde geri [yazarken, kaynak bağlayıcı özelliği olarak ms-DS-ımced GUID 'si](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) ile ilgili bir sorun düzeltildi. Sorun, Azure AD Connect eklenen birden çok şirket içi AD Ormanı olduğunda ve *birden çok dizinde bulunan Kullanıcı kimlikleri seçeneği* belirlendiğinde oluşur. Bu tür yapılandırma kullanıldığında, sonuç eşitleme kuralları meta veri deposundaki sourceAnchorBinary özniteliğini doldurmaz. SourceAnchorBinary özniteliği, ms-DS-ımıbu Guid özniteliği için kaynak özniteliği olarak kullanılır. Sonuç olarak, MS-Dsmda, Guid özniteliği için geri yazma gerçekleşmez. Sorunu düzeltemedi, meta veri deposundaki sourceAnchorBinary özniteliğinin her zaman doldurulduğundan emin olmak için aşağıdaki eşitleme kuralları güncelleştirilmiştir:
  * AD-InetOrgPerson AccountEnabled. xml ' de
  * AD-InetOrgPerson Common. xml ' den
  * AD 'den Kullanıcı AccountEnabled. xml ' de
  * AD 'den-User ortak. xml ' de
  * ' Deki AD-Kullanıcı birleşimi SOAInAAD. xml ' den

* Daha önce, [MS-DS-ımıbu GUID kaynak bağlayıcı](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) özelliği etkin olmasa bile, "ad-Kullanıcı ImmutableID" eşitleme kuralı hala Azure AD Connect eklenir. Bu efekt zararsız olur ve ms-DS-ımbu GUID özniteliğinin geri yazma işleminin oluşmasına neden olmaz. Karışıklığın önüne geçmek için, eşitleme kuralının yalnızca özellik etkinleştirildiğinde eklendiğinden emin olmak için mantık eklenmiştir.

* Parola karması eşitlemesinin hata olayı 611 ile başarısız olmasına neden olan bir sorun düzeltildi. Bu sorun, şirket içi AD 'den bir veya daha fazla etki alanı denetleyicisi kaldırıldıktan sonra oluşur. Her parola eşitleme döngüsünün sonunda, şirket içi AD tarafından verilen eşitleme tanımlama bilgisi, kaldırılan etki alanı denetleyicilerinin USN (güncelleştirme sıra numarası) değeri olan çağırma kimliklerini içerir. Parola Eşitleme Yöneticisi, 0 USN değerini içeren eşitleme tanımlama bilgisini sürdüremiyor ve hata olayı 611 ile başarısız oluyor. Bir sonraki eşitleme çevrimi sırasında, Parola Eşitleme Yöneticisi, USN değeri 0 içermeyen son kalıcı eşitleme tanımlama bilgisini yeniden kullanır. Bu, aynı parola değişikliklerinin yeniden eşitlenmesi oluşmasına neden olur. Bu düzeltmeyle, Parola Eşitleme Yöneticisi eşitleme tanımlama bilgisini doğru şekilde devam ettirir.

* Daha önce, set-ADSyncAutoUpgrade cmdlet 'i kullanılarak otomatik yükseltme devre dışı bırakılmış olsa bile otomatik yükseltme işlemi, yükseltmeyi düzenli aralıklarla denetlemeye devam eder ve karşıdan yüklenen yükleyiciyi, disablement 'e uyacak şekilde kullanır. Bu düzeltmeyle, otomatik yükseltme işlemi artık yükseltmeyi düzenli aralıklarla denetlemiyor. Bu Azure AD Connect sürümü için yükseltme yükleyicisi bir kez yürütüldüğünde bu çözüm otomatik olarak uygulanır.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* Daha önce, [MS-DS-mımnsource bağlantı noktası olarak GUID](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) özelliği yalnızca Yeni dağıtımlar için kullanılabilir. Şimdi mevcut dağıtımlar tarafından kullanılabilir. Daha ayrıntılı belirtmek gerekirse:
  * Özelliğe erişmek için Azure AD Connect Sihirbazı 'nı başlatın ve *kaynak bağlantısını Güncelleştir* seçeneğini belirleyin.
  * Bu seçenek yalnızca, Sourcetutturucu özniteliği olarak Objectguıd kullanan mevcut dağıtımlar için görülebilir.
  * Seçeneği yapılandırılırken, sihirbaz, şirket içi Active Directory ms-DS-ımlar, Guid özniteliğinin durumunu doğrular. Öznitelik, dizindeki herhangi bir Kullanıcı nesnesinde yapılandırılmamışsa, sihirbaz, Sourcetutturucu özniteliği olarak ms-DS-ımıbu GUID ' i kullanır. Öznitelik, dizindeki bir veya daha fazla kullanıcı nesnesi üzerinde yapılandırılmışsa, sihirbaz özniteliği diğer uygulamalar tarafından kullanılır ve Sourcetutturucu özniteliği olarak uygun değildir ve kaynak bağlantısı değişikliğinin devam etsin. Özniteliğin mevcut uygulamalar tarafından kullanılmadığından eminseniz, hatanın nasıl bastıralınacağını öğrenmek için desteğe başvurmanız gerekir.

* Cihaz nesnelerinde **userCertificate** özniteliğine özgü, Azure AD Connect artık, [etki alanına katılmış cihazları Windows 10 için Azure AD 'ye bağlamak](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) ve Azure AD 'ye eşitlemeden önce Rest 'i filtrelemekte gerekli sertifika değerlerini arar. Bu davranışı etkinleştirmek için, kullanıma hazır eşitleme kuralı "AAD-Device JOIN SOAInAD" olarak güncelleştirilmiştir.

* Azure AD Connect artık Exchange Online **CloudPublicDelegates** özniteliğinin ŞIRKET Içi ad **publicDelegates** özniteliğine geri yazmayı destekliyor. Bu, Exchange Online posta kutusu 'nun şirket içi Exchange posta kutusuyla kullanıcılara yönelik haklara izin verilebileceği senaryoya olanak sağlar. Bu özelliği desteklemek için yeni bir kullanıma hazır eşitleme kuralı "AD – Kullanıcı Exchange hibrit PublicDelegates geri yazma" eklenmiştir. Bu eşitleme kuralı yalnızca Exchange karma özelliği etkinleştirildiğinde Azure AD Connect eklenir.

* Azure AD Connect artık, Azure AD 'den **Altrecipient** özniteliğini eşitlemeyi desteklemektedir. Bu değişikliği desteklemek için, aşağıdaki kullanıma hazır eşitleme kuralları gerekli öznitelik akışını içerecek şekilde güncelleştirilmiştir:
  * AD 'den içinde – Kullanıcı değişimi
  * AAD 'ye kadar – Kullanıcı ExchangeOnline
  
* Meta veri deposundaki **Cloudsoaexchmailbox** özniteliği, belirli bir kullanıcının Exchange Online posta kutusuna sahip olup olmadığını gösterir. Tanımı, bu tür ekipman ve konferans odası posta kutuları olarak ek Exchange Online alıcının Displaytypes 'ı içerecek şekilde güncelleştirilmiştir. Bu değişikliği etkinleştirmek için cloudSOAExchMailbox özniteliğinin tanımı, "AAD 'den, Kullanıcı değişim karması" Içindeki "kullanıma hazır eşitleme kuralı altında bulunur, şu kaynaktan güncelleştirilmiştir:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... şunları yapın:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* UserCertificate özniteliğinde sertifika değerlerini işlemek üzere eşitleme kuralı ifadeleri oluşturmak için aşağıdaki X509Certificate2 uyumlu işlevler kümesi eklendi:

    ||||
    | --- | --- | --- |
    |CertSubject|Certısuer|Certkeyalgorithd|
    |CertSubjectNameDN|Certısueroıd|Certnameınfo|
    |Certsubjectnameoıd|Certısuerdn|IsCert|
    |CertFriendlyName|Certparmak Izi|Certbu Sionıds|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Seçim|
    |CertKeyAlgorithmParams|CertHashString|Konum|
    |||With|

* Müşterilerin, Grup nesneleri için sAMAccountName, Domainnetbıos ve domainFQDN 'leri ve Kullanıcı nesneleri için distinguishedName 'dir akışını sağlamak üzere özel eşitleme kuralları oluşturmalarına olanak tanımak için aşağıdaki şema değişiklikleri sunulmuştur:

  * Aşağıdaki öznitelikler, MV şemasına eklenmiştir:
    * Grup: AccountName
    * Grup: Domainnetbıos
    * Grup: domainFQDN
    * Kişi: distinguishedName 'dir

  * Aşağıdaki öznitelikler Azure AD bağlayıcı şemasına eklendi:
    * Grup: OnPremisesSamAccountName
    * Grup: NetBiosName
    * Grup: DN
    * Kullanıcı: OnPremisesDistinguishedName

* ADSyncDomainJoinedComputerSync cmdlet betiği artık AzureEnvironment adlı yeni bir isteğe bağlı parametreye sahiptir. Parametresi, karşılık gelen Azure Active Directory kiracının barındırıldığı bölgeyi belirtmek için kullanılır. Geçerli değerler şunlardır:
  * Azurecyüksek (varsayılan)
  * AzureChinaCloud
  * AzureGermanyCloud
  * Uskamu
 
* Eşitleme kuralı Düzenleyicisi, eşitleme kuralı oluşturma sırasında bağlantı türünün varsayılan değeri olarak JOIN (sağlama yerine) kullanacak şekilde güncelleştirildi.

### <a name="ad-fs-management"></a>AD FS Yönetimi

#### <a name="issues-fixed"></a>Düzeltilen sorunlar

* Aşağıdaki URL 'Ler, kimlik doğrulama kesintisine karşı dayanıklılığı artırmak için Azure AD tarafından sunulan yeni WS-Federation uç noktalardır ve şirket içi AD FS yanıtlama tarafı güveni yapılandırmasına eklenecektir:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* AD FS ıssuerıd için yanlış talep değeri oluşturulmasına neden olan bir sorun düzeltildi. Bu sorun, Azure AD kiracısında birden çok doğrulanmış etki alanı varsa ve ıssuerıd talebini oluşturmak için kullanılan userPrincipalName özniteliğinin etki alanı sonekinin en az 3 düzey derinlikte (örneğin, johndoe@us.contoso.com) oluşması durumunda oluşur. Bu sorun, talep kuralları tarafından kullanılan Regex güncelleştirilerek çözümlenir.

#### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
* Daha önce Azure AD Connect tarafından sunulan ADFS sertifika yönetimi özelliği, yalnızca Azure AD Connect ile yönetilen ADFS grupları ile kullanılabilir. Şimdi, özelliği Azure AD Connect kullanılarak yönetilmeyen ADFS grupları ile kullanabilirsiniz.

## <a name="115240"></a>1.1.524.0
Yayımlanma tarihi: Mayıs 2017

> [!IMPORTANT]
> Bu derlemede tanıtılan şema ve eşitleme kuralı değişiklikleri var. Azure AD Connect eşitleme hizmeti, yükseltmeden sonra tam Içeri aktarma ve tam eşitleme adımlarını tetikler. Değişikliklerin ayrıntıları aşağıda açıklanmıştır.
>
>

**Sorun düzeltildi:**

Azure AD Connect Eşitleme

* Müşteri, set-ADSyncAutoUpgrade cmdlet 'ini kullanarak özelliği devre dışı bıraksa bile Azure AD Connect sunucuda otomatik yükseltmenin oluşmasına neden olan bir sorun düzeltildi. Bu düzeltmeyle, sunucudaki otomatik yükseltme işlemi, düzenli aralıklarla yükseltmeyi denetler, ancak indirilen yükleyici otomatik yükseltme yapılandırmasını kabul eder.
* DirSync yerinde yükseltme sırasında Azure AD Connect, Azure AD Bağlayıcısı tarafından Azure AD ile eşitleme için kullanılacak bir Azure AD hizmet hesabı oluşturur. Hesap oluşturulduktan sonra, hesabı kullanarak Azure AD ile kimlik doğrulaması Azure AD Connect. Bazen, geçici sorunlar nedeniyle kimlik doğrulaması başarısız olur ve bu da DirSync yerinde yükseltmenin hata vererek başarısız olmasına neden olarak *"yapılandırma AAD eşitleme bir hata oluştu: AADSTS50034: Bu uygulamada oturum açmak için, hesabın xxx.onmicrosoft.com dizinine eklenmesi gerekir."* DirSync yükseltmesinin dayanıklılığını artırmak için Azure AD Connect şimdi kimlik doğrulama adımını yeniden dener.
* Derleme 443 ' de DirSync yerinde yükseltmenin başarılı olmasına neden olan bir sorun oluştu, ancak dizin eşitlemesi için gereken profillerin çalıştırılması oluşturulmaz. Bu Azure AD Connect derlemesinde düzeltme mantığı bulunur. Müşteri bu yapıya yükseltirken, Azure AD Connect eksik çalıştırma profillerini algılar ve bunları oluşturur.
* Parola eşitleme işleminin olay KIMLIĞI 6900 ile başlamamasına neden olan bir sorun düzeltildi ve *"aynı anahtara sahip bir öğe zaten eklenmiş"* hatası. Bu sorun, OU filtreleme yapılandırmasını AD yapılandırma bölümünü içerecek şekilde güncelleştirdiğinizde oluşur. Bu sorunu onarmak için, parola eşitleme işlemi artık yalnızca AD etki alanı bölümlerinden parola değişikliklerini eşitler. Yapılandırma bölümü gibi etki alanı olmayan bölümler atlanır.
* Hızlı yükleme sırasında Azure AD Connect, şirket içi AD ile iletişim kurmak için AD Bağlayıcısı tarafından kullanılacak şirket içi AD DS hesabı oluşturur. Daha önce, hesap, Kullanıcı hesabı denetimi özniteliğinde ayarlanan PASSWD_NOTREQD bayrağıyla oluşturulur ve hesapta rastgele bir parola ayarlanır. Artık Azure AD Connect, hesapta parolanın ayarlanmasının ardından PASSWD_NOTREQD bayrağını açıkça kaldırır.
* DirSync yükseltmesinin, şirket içi AD şemasında Mailrumuz özniteliği bulunduğunda ve AD Kullanıcı nesne sınıfıyla sınırlı olmadığında, *"bir uygulama kilidi almaya çalışan SQL Server 'da bir kilitlenme oluştu"* hatasıyla başarısız olmasına neden olan bir sorun düzeltildi.
* Yönetici Azure AD Connect Sihirbazı 'nı kullanarak Azure AD Connect eşitleme yapılandırmasını güncelleştirirken cihaz geri yazma özelliğinin otomatik olarak devre dışı bırakılmasına neden olan bir sorun düzeltildi. Bu sorun, sihirbazın şirket içi AD 'de mevcut cihaz geri yazma yapılandırması için önkoşul denetimi gerçekleştirmesinin nedeni ve Denetim başarısız olur. Bu çözüm, cihaz geri yazma daha önce zaten etkin olup olmadığını denetlemeyi atlar.
* OU filtrelemesini yapılandırmak için Azure AD Connect Sihirbazı 'nı veya Synchronization Service Manager kullanabilirsiniz. Daha önce, OU filtrelemesini yapılandırmak için Azure AD Connect sihirbazını kullanırsanız, daha sonra oluşturulan yeni OU 'Lar Dizin eşitlemeye dahil edilir. Yeni OU 'Ların dahil edilmesini istemiyorsanız, Synchronization Service Manager kullanarak OU filtrelemeyi yapılandırmanız gerekir. Artık Azure AD Connect Sihirbazı 'nı kullanarak aynı davranışı elde edebilirsiniz.
* Azure AD Connect tarafından gereken saklı yordamların dbo şeması yerine, yükleme yöneticisinin şeması altında oluşturulmasına neden olan bir sorun düzeltildi.
* AAD Connect sunucusu olay günlüklerinde Azure AD tarafından döndürülen Trackingıd özniteliğinin atlanmasına neden olan bir sorun düzeltildi. Azure AD Connect, Azure AD 'den bir yeniden yönlendirme iletisi alırsa ve Azure AD Connect belirtilen uç noktaya bağlanamıyorsa sorun oluşur. Trackingıd, sorun giderme sırasında hizmet tarafı günlükleriyle ilişkilendirmek için destek mühendisleri tarafından kullanılır.
* Azure AD Connect, Azure AD 'den LargeObject hatası aldığında Azure AD Connect EventID 6941 ve *"sağlanan nesne çok büyük" iletisini içeren bir olay oluşturur. Bu nesnedeki öznitelik değerlerinin sayısını kırpın. "* Aynı zamanda, Azure AD Connect EventID 6900 ve *"Microsoft. online. ıwıth. ProvisionRetryException: Windows Azure Active Directory hizmetiyle iletişim* kurulamıyor" iletisiyle yanıltıcı bir olay oluşturur. Karışıklığı en aza indirmek için, LargeObject hatası alındığında Azure AD Connect artık ikinci olayı oluşturmaz.
* Genel LDAP Bağlayıcısı yapılandırmasını güncelleştirmeye çalışırken Synchronization Service Manager yanıt vermemesine neden olan bir sorun düzeltildi.

**Yeni özellikler/geliştirmeler:**

Azure AD Connect Eşitleme
* Eşitleme kuralı değişiklikleri – aşağıdaki eşitleme kuralı değişiklikleri uygulandı:
  * Özniteliklerin 15 ' ten fazla değeri varsa, **userCertificate** ve **Usersmmecertificate** özniteliklerini dışarı aktarmayan varsayılan eşitleme kuralı güncelleştirildi.
  * AD öznitelikleri **EmployeeID** ve **Msexchbypassmoderationlink** artık varsayılan eşitleme kural kümesine dahil edilmiştir.
  * AD özniteliği **fotoğrafı** varsayılan eşitleme kuralı kümesinden kaldırılmıştır.
  * Meta veri deposu şemasına ve AAD bağlayıcı şemasına **Preferreddatalocation** eklendi. Azure AD 'de öznitelikleri güncelleştirmek isteyen müşteriler, özel eşitleme kuralları uygulayabilir. 
  * Metadize şemasına ve AAD bağlayıcı şemasına **UserType** eklendi. Azure AD 'de öznitelikleri güncelleştirmek isteyen müşteriler, özel eşitleme kuralları uygulayabilir.

* Azure AD Connect artık, şirket içi AD nesneleri için kaynak bağlayıcı özniteliği olarak artık '% nGUID ' özelliğinin kullanılmasına otomatik olarak izin verebilir. Ayrıca, Azure AD Connect, boş ise, ' de bir Objectguıd özniteliği değeri ile birlikte,. Bu özellik yalnızca yeni dağıtım için geçerlidir. Bu özellik hakkında daha fazla bilgi edinmek için, [Azure AD Connect: tasarım kavramları-ms-DS-ıımıbu GUID 'Yi Sourcetutturucu olarak kullanma](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)makalesine bakın.
* Parola karması eşitlemeyle ilgili sorunları tanılamaya yardımcı olmak için yeni sorun giderme cmdlet 'i çağırma-ADSyncDiagnostics eklenmiştir. Cmdlet 'i kullanma hakkında daha fazla bilgi için, [Azure AD Connect eşitleme ile parola karması eşitlemesine sorun giderme](tshoot-connect-password-hash-synchronization.md)makalesine bakın.
* Azure AD Connect artık posta etkin ortak klasör nesnelerini şirket içi AD 'den Azure AD 'ye eşitlemeyi desteklemektedir. Isteğe bağlı özellikler altında Azure AD Connect Sihirbazı 'nı kullanarak özelliği etkinleştirebilirsiniz. Bu özellik hakkında daha fazla bilgi edinmek için, bkz. [Office 365 dizin tabanlı uç engelleme desteği-şirket Içi posta etkin ortak klasörler](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect, şirket içi AD 'den AD DS bir hesabının eşitlenmesini gerektirir. Daha önce, Express modunu kullanarak Azure AD Connect yüklediyseniz, bir kurumsal yönetici hesabının kimlik bilgilerini sağlayabilir ve Azure AD Connect gereken AD DS hesabı oluşturabilir. Ancak, özel bir yükleme ve mevcut bir dağıtıma ormanlar ekleme için, bunun yerine AD DS hesabını sağlamanız gerekiyordu. Artık, özel bir yükleme sırasında Kurumsal Yönetici hesabının kimlik bilgilerini sağlama ve gerekli AD DS hesabı Azure AD Connect oluşturma seçeneğiniz de vardır.
* Azure AD Connect artık SQL AOA 'yi destekliyor. Azure AD Connect yüklemeden önce SQL AOA 'i etkinleştirmeniz gerekir. Yükleme sırasında, sağlanan SQL örneğinin SQL AOA için etkinleştirilip etkinleştirilmediğini algılar Azure AD Connect. SQL AOA etkinleştirilmişse, SQL AOA zaman uyumlu çoğaltma veya zaman uyumsuz çoğaltma kullanacak şekilde yapılandırıldıysa Azure AD Connect daha fazla şekil bulabilirsiniz. Kullanılabilirlik grubu dinleyicisini ayarlarken RegisterAllProvidersIP özelliğini 0 olarak ayarlamanız önerilir. Bu öneri, Azure AD Connect Şu anda SQL 'e bağlanmak için SQL Native Client kullandığından SQL Native Client MultiSubNetFailover özelliğinin kullanımını desteklemez.
* LocalDB 'yi Azure AD Connect sunucunuz için veritabanı olarak kullanıyorsanız ve 10 GB boyut sınırına ulaştıysa, eşitleme hizmeti artık başlatılmayacaktır. Daha önce, Eşitleme hizmetinin başlaması için yeterli VERITABANı alanını geri kazanmak üzere LocalDB üzerinde SHRINKDATABASE işlemi gerçekleştirmeniz gerekir. Bundan sonra, daha fazla VERITABANı alanı kazanmak için çalıştırma geçmişini silmek üzere Synchronization Service Manager kullanabilirsiniz. Şimdi, VERITABANı alanını geri kazanmak için LocalDB 'den çalıştırma geçmişi verilerini temizlemek için Start-ADSyncPurgeRunHistory cmdlet 'ini kullanabilirsiniz. Ayrıca, bu cmdlet, eşitleme hizmeti çalışmadığı zaman kullanılabilecek çevrimdışı modu (-offline parametresini belirterek) destekler. Not: çevrimdışı mod yalnızca eşitleme hizmeti çalışmıyorsa ve kullanılan veritabanı LocalDB ise kullanılabilir.
* Gerekli depolama alanı miktarını azaltmak için Azure AD Connect artık, eşitleme hata ayrıntılarını LocalDB/SQL veritabanlarında depolamadan önce sıkıştırır. Azure AD Connect eski bir sürümünden bu sürüme yükseltirken, Azure AD Connect var olan eşitleme hata ayrıntılarında bir kerelik sıkıştırma gerçekleştirir.
* Daha önce, OU filtreleme yapılandırmasını güncelleştirdikten sonra, mevcut nesnelerin Dizin eşitlemesine doğru bir şekilde dahil edilmesini veya dışlandığından emin olmak için tam içeri aktarmayı el ile çalıştırmanız gerekir. Şimdi, Azure AD Connect bir sonraki eşitleme çevrimi sırasında otomatik olarak tam içeri aktarmayı tetikler. Ayrıca, tam içeri aktarma yalnızca güncelleştirmeden etkilenen AD bağlayıcılarına uygulanır. Bu geliştirme, yalnızca Azure AD Connect Sihirbazı kullanılarak yapılan OU filtreleme güncelleştirmelerine uygulanabilir. Synchronization Service Manager kullanılarak yapılan OU filtreleme güncelleştirmesi için geçerli değildir.
* Daha önce grup tabanlı filtreleme yalnızca kullanıcıları, grupları ve Ilgili kişi nesnelerini destekler. Artık grup tabanlı filtreleme, bilgisayar nesnelerini de destekler.
* Daha önce, Azure AD Connect eşitleme zamanlayıcısını devre dışı bırakmadan bağlayıcı alanı verilerini silebilirsiniz. Artık Synchronization Service Manager, Scheduler 'ın etkinleştirildiğini algılarsa bağlayıcı alanı verilerinin silinmesini engeller. Ayrıca, bağlayıcı alanı verileri silinirse, müşterileri potansiyel veri kaybını hakkında bilgilendirmek için bir uyarı döndürülür.
* Daha önce, Azure AD Connect sihirbazının doğru çalışması için PowerShell dökümünü devre dışı bırakmanız gerekir. Bu sorun kısmen çözüldü. Eşitleme yapılandırmasını yönetmek için Azure AD Connect Sihirbazı kullanıyorsanız PowerShell dökümünü etkinleştirebilirsiniz. ADFS yapılandırmasını yönetmek için Azure AD Connect Sihirbazı kullanıyorsanız PowerShell dökümünü devre dışı bırakmanız gerekir.



## <a name="114860"></a>1.1.486.0
Yayımlanma tarihi: Nisan 2017

**Sorun düzeltildi:**
* Windows Server 'ın yerelleştirilmiş sürümünde Azure AD Connect başarıyla yüklenemeyecek sorun düzeltildi.

## <a name="114840"></a>1.1.484.0
Yayımlanma tarihi: Nisan 2017

**Bilinen sorunlar:**

* Aşağıdaki koşulların tümü doğruysa Azure AD Connect bu sürümü başarıyla yüklenmez:
   1. DirSync yerinde yükseltme veya Azure AD Connect yeni yüklemesi gerçekleştiriliyor.
   2. Windows Server 'ın, sunucuda yerleşik yönetici grubunun adı "Yöneticiler" olmadığı bir yerelleştirilmiş sürümü kullanıyorsunuz.
   3. SQL Server Express LocalDB 'nin kendi tam SQL 'nizi sağlamak yerine Azure AD Connect ile yüklendiği varsayılan 2012 kullanıyorsunuz.

**Sorun düzeltildi:**

Azure AD Connect Eşitleme
* Bir veya daha fazla bağlayıcıda bu eşitleme adımı için çalıştırma profili eksikse eşitleme çizelgeleyicinin tüm eşitleme adımını atmadığı bir sorun düzeltildi. Örneğin, Synchronization Service Manager kullanarak bir bağlayıcıyı bir Delta Içeri aktarma çalıştırma profili oluşturmadan el ile eklediniz. Bu çözüm, eşitleme çizelgeleyicinin diğer bağlayıcılar için Delta Içeri aktarma çalıştırmaya devam etmesini sağlar.
* Çalışma adımlarında bir sorunla karşılaştığında eşitleme hizmetinin bir çalıştırma profilini işlemeyi hemen durdurduğu bir sorun düzeltildi. Bu çözüm, Eşitleme hizmetinin bu çalıştırma adımını atlayıp Rest 'i işlemeye devam etmesini sağlar. Örneğin, AD Bağlayıcınız için birden fazla çalışma adımını (her şirket içi AD etki alanı için bir tane) içeren bir Delta Içeri aktarma çalışma profiliniz vardır. Eşitleme hizmeti, birinin ağ bağlantısı sorunlarına sahip olsa bile, Delta Içeri aktarmayı diğer AD etki alanlarıyla çalıştırır.
* Otomatik yükseltme sırasında Azure AD Bağlayıcısı güncelleştirmesinin atlanmasına neden olan bir sorun düzeltildi.
* Azure AD Connect, kurulum sırasında sunucunun bir etki alanı denetleyicisi olup olmadığını yanlışlıkla belirlemesine neden olan bir sorun düzeltildi, bu da DirSync yükseltmesinin başarısız olmasına neden olur.
* DirSync yerinde yükseltmesine, Azure AD Bağlayıcısı için hiçbir çalıştırma profili oluşturmamasını sağlayan bir sorun düzeltildi.
* Genel LDAP bağlayıcısını yapılandırmaya çalışırken Synchronization Service Manager Kullanıcı arabiriminin yanıt vermemesine neden olan bir sorun düzeltildi.

AD FS Yönetimi
* AD FS birincil düğümü başka bir sunucuya taşındığında Azure AD Connect sihirbazının başarısız olduğu bir sorun düzeltildi.

Masaüstü SSO 'SU
* Yeni yükleme sırasında oturum açma seçeneği olarak parola eşitleme seçeneğini belirlediyseniz, oturum açma ekranının Masaüstü SSO özelliğini etkinleştirmenize izin vermediğinden Azure AD Connect sihirbazında sorun düzeltildi.

**Yeni özellikler/geliştirmeler:**

Azure AD Connect Eşitleme
* Azure AD Connect eşitleme artık sanal hizmet hesabı, yönetilen hizmet hesabı ve grup tarafından yönetilen hizmet hesabı hizmet hesabı olarak kullanımını desteklemektedir. Bu yalnızca Azure AD Connect yeni yüklemesi için geçerlidir. Azure AD Connect yüklenirken:
    * Varsayılan olarak, Azure AD Connect sihirbaz bir sanal hizmet hesabı oluşturur ve hizmet hesabı olarak kullanır.
    * Bir etki alanı denetleyicisine yüklüyorsanız, Azure AD Connect etki alanı kullanıcı hesabı oluşturacağınız ve bunu bunun yerine hizmet hesabı olarak kullanan önceki davranışa geri döner.
    * Aşağıdakilerden birini sağlayarak varsayılan davranışı geçersiz kılabilirsiniz:
      * Grup tarafından yönetilen hizmet hesabı
      * Yönetilen bir hizmet hesabı
      * Bir etki alanı kullanıcı hesabı
      * Yerel bir kullanıcı hesabı
* Daha önce, bağlayıcılar veya eşitleme kuralı değişikliklerini içeren yeni bir Azure AD Connect yapısını yükseltirseniz Azure AD Connect tam eşitleme döngüsünü tetikler. Artık Azure AD Connect, yalnızca güncelleştirme içeren bağlayıcılar için tam Içeri aktarma adımını ve yalnızca eşitleme kuralı değişikliklerini içeren bağlayıcılar için tam eşitleme adımını tetikler.
* Daha önce, dışarı aktarma silme eşiği yalnızca eşitleme Zamanlayıcı aracılığıyla tetiklenen dışarı aktarmalar için geçerlidir. Artık özellik, Synchronization Service Manager kullanılarak müşteri tarafından el ile tetiklenen dışarı aktarmaları içerecek şekilde genişletilir.
* Azure AD kiracınızda, kiracınız için parola eşitleme özelliğinin etkinleştirilip etkinleştirilmediğini belirten bir hizmet yapılandırması vardır. Daha önce, etkin ve bir hazırlama sunucunuz olduğunda, hizmet yapılandırmasının Azure AD Connect tarafından hatalı şekilde yapılandırılması kolaydır. Artık Azure AD Connect, hizmet yapılandırmasını yalnızca etkin Azure AD Connect sunucusu ile tutarlı tutmaya çalışacaktır.
* Azure AD Connect Sihirbazı şimdi, şirket içi AD 'de AD geri dönüşüm kutusu etkin değilse bir uyarı algılar ve döndürür.
* Daha önce, toplu işteki nesnelerin Birleşik boyutu belirli eşiki aşarsa Azure AD 'ye dışarı aktarma zaman aşımına uğrar ve başarısız olur. Artık eşitleme hizmeti, sorun ile karşılaşılırsa, nesneleri ayrı, daha küçük toplu işlerle yeniden göndermek için yeniden yanıt vermeye çalışır.
* Eşitleme hizmeti anahtar yönetimi uygulaması Windows Başlat menüsünden kaldırılmıştır. Şifreleme anahtarının yönetimi, mııskmu. exe ' yi kullanan komut satırı arabirimi aracılığıyla desteklenmeye devam edecektir. Şifreleme anahtarını yönetme hakkında bilgi için, [Azure AD Connect eşitleme şifreleme anahtarını bırakma](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key)makalesine başvurun.
* Daha önce, Azure AD Connect eşitleme hizmeti hesabı parolasını değiştirirseniz, şifreleme anahtarını terk edinceye ve Azure AD Connect eşitleme hizmeti hesabı parolasını yeniden başlatana kadar eşitleme hizmeti düzgün şekilde başlayamaz. Artık bu işlem artık gerekli değildir.

Masaüstü SSO 'SU

* Azure AD Connect Sihirbazı artık doğrudan kimlik doğrulama ve Masaüstü SSO 'yu yapılandırırken ağ üzerinde 9090 bağlantı noktasını açmaya gerek yoktur. Yalnızca 443 numaralı bağlantı noktası gereklidir. 

## <a name="114430"></a>1.1.443.0
Yayımlanma tarihi: Mart 2017

**Sorun düzeltildi:**

Azure AD Connect Eşitleme
* Azure AD bağlayıcısının görünen adı Azure AD kiracısına atanan ilk onmicrosoft.com etki alanını içermiyorsa Azure AD Connect sihirbazının başarısız olmasına neden olan bir sorun düzeltildi.
* Eşitleme hizmeti hesabının parolası, kesme işareti, iki nokta ve boşluk gibi özel karakterler içerdiğinde SQL veritabanıyla bağlantı kurulurken Azure AD Connect sihirbazının başarısız olmasına neden olan bir sorun düzeltildi.
* Bir şirket içi AD nesnesinin eşitlenmesini ve eşitlenmesi için yeniden eklenmesini gerçekleştirdikten sonra, "görüntü resimden farklı bir bağlantıya sahip" görüntüsünün, hazırlama modundaki bir Azure AD Connect sunucuda olması nedeniyle oluşan bir sorun düzeltildi.
* Bir şirket içi AD nesnesinin eşitlemesini bir kez daha hariç tutduktan ve sonra eşitleme için yeniden dahil ettikten sonra, "DN tarafından bulunan nesne" bir Azure AD Connect sunucuda olması nedeniyle oluşan bir sorun düzeltildi.

AD FS Yönetimi
* Azure AD Connect sihirbazının AD FS yapılandırmasını güncelleştirmediğinden ve alternatif oturum açma KIMLIĞI yapılandırıldıktan sonra bağlı olan taraf güveninde doğru talepleri ayarlayamadığından bir sorun düzeltildi.
* Azure AD Connect sihirbazının, hizmet hesapları sAMAccountName biçimi yerine userPrincipalName biçimi kullanılarak yapılandırılan AD FS sunucuları doğru şekilde işleyememesi sorunu düzeltildi.

Doğrudan Kimlik Doğrulama
* Kimlik doğrulama geçişi seçilirse, ancak bağlayıcının kaydı başarısız olursa Azure AD Connect sihirbazının başarısız olmasına neden olan bir sorun düzeltildi.
* Masaüstü SSO özelliği etkinken Azure AD Connect sihirbazının, oturum açma yönteminde seçili olan doğrulama denetimlerini atlamasına neden olan bir sorun düzeltildi.

Parola Sıfırlama
* Bağlantı bir güvenlik duvarı veya proxy tarafından sonlandırdıysanız Azure AAD Connect sunucusunun yeniden bağlanmaya çalışmasına neden olabilecek bir sorun düzeltildi.

**Yeni özellikler/geliştirmeler:**

Azure AD Connect Eşitleme
* Get-ADSyncScheduler cmdlet 'i artık SyncCycleInProgress adlı yeni bir Boole özelliği döndürüyor. Döndürülen değer true ise, devam eden zamanlanmış bir eşitleme döngüsünün olduğu anlamına gelir.
* Azure AD Connect yükleme ve kurulum günlüklerinin depolanacağı hedef klasör, günlük dosyalarına erişilebilirliği geliştirmek için%localappdata%\AADConnect ' den%programdata%\AADConnect ' e taşındı.

AD FS Yönetimi
* AD FS grubu SSL sertifikasını güncelleştirmek için destek eklendi.
* AD FS 2016 yönetimine yönelik destek eklendi.
* Artık, AD FS yükleme sırasında var olan gMSA (grup yönetilen hizmet hesabı) belirtebilirsiniz.
* Artık SHA-256 ' i Azure AD bağlı olan taraf güveni için imza karması algoritması olarak yapılandırabilirsiniz.

Parola Sıfırlama
* Ürünün daha sıkı güvenlik duvarı kurallarına sahip ortamlarda çalışmasına izin veren geliştirmeler tanıtılmıştır.
* Azure Service Bus için iyileştirilmiş bağlantı güvenilirliği.

## <a name="113800"></a>1.1.380.0
Yayımlanma tarihi: Aralık 2016

**Sorun düzeltildi:**

* Active Directory Federasyon Hizmetleri (AD FS) (AD FS) için ıssuerıd talep kuralının Bu derlemede bulunmadığı sorun düzeltildi.

>[!NOTE]
>Bu derleme, Azure AD Connect otomatik yükseltme özelliği aracılığıyla müşteriler tarafından kullanılamaz.

## <a name="113710"></a>1.1.371.0
Yayımlanma tarihi: Aralık 2016

**Bilinen sorun:**

* Bu derlemede AD FS ıssuerıd talep kuralı yok. Birden çok etki alanını Azure Active Directory (Azure AD) ile federasyona eklemek için ıssuerıd talep kuralı gereklidir. Şirket içi AD FS dağıtımınızı yönetmek için Azure AD Connect kullanıyorsanız, bu yapıya yükseltme, mevcut ıssuerıd talep kuralını AD FS yapılandırmadan kaldırır. Yükleme/yükseltmeden sonra ıssuerıd talep kuralını ekleyerek soruna geçici bir çözüm bulabilirsiniz. Issuerıd talep kuralını ekleme hakkında daha fazla bilgi için [Azure AD ile Federasyon Için birden çok etki alanı desteğinin](how-to-connect-install-multiple-domains.md)bu makalesine başvurun.

**Sorun düzeltildi:**

* Giden bağlantı için bağlantı noktası 9090 açık değilse, Azure AD Connect yüklemesi veya yükseltmesi başarısız olur.

>[!NOTE]
>Bu derleme, Azure AD Connect otomatik yükseltme özelliği aracılığıyla müşteriler tarafından kullanılamaz.

## <a name="113700"></a>1.1.370.0
Yayımlanma tarihi: Aralık 2016

**Bilinen sorunlar:**

* Bu derlemede AD FS ıssuerıd talep kuralı yok. Azure AD ile birden çok etki alanını federasyona eklemek istiyorsanız ıssuerıd talep kuralı gereklidir. Şirket içi AD FS dağıtımınızı yönetmek için Azure AD Connect kullanıyorsanız, bu yapıya yükseltme, mevcut ıssuerıd talep kuralını AD FS yapılandırmadan kaldırır. Yükleme/yükseltme sonrasında ıssuerıd talep kuralını ekleyerek soruna geçici bir çözüm bulabilirsiniz. Issuerıd talep kuralı ekleme hakkında ayrıntılı bilgi için [Azure AD ile federasyona yönelik birden çok etki alanı desteği](how-to-connect-install-multiple-domains.md)makalesine başvurun.
* Yüklemenin tamamlanabilmesi için bağlantı noktası 9090 ' ın açık olması gerekir.

**Yeni özellikler:**

* Geçişli kimlik doğrulaması (Önizleme).

>[!NOTE]
>Bu derleme, Azure AD Connect otomatik yükseltme özelliği aracılığıyla müşteriler tarafından kullanılamaz.

## <a name="113430"></a>1.1.343.0
Yayımlanma tarihi: Kasım 2016

**Bilinen sorun:**

* Bu derlemede AD FS ıssuerıd talep kuralı yok. Azure AD ile birden çok etki alanını federasyona eklemek istiyorsanız ıssuerıd talep kuralı gereklidir. Şirket içi AD FS dağıtımınızı yönetmek için Azure AD Connect kullanıyorsanız, bu yapıya yükseltme, mevcut ıssuerıd talep kuralını AD FS yapılandırmadan kaldırır. Yükleme/yükseltme sonrasında ıssuerıd talep kuralını ekleyerek soruna geçici bir çözüm bulabilirsiniz. Issuerıd talep kuralı ekleme hakkında ayrıntılı bilgi için [Azure AD ile federasyona yönelik birden çok etki alanı desteği](how-to-connect-install-multiple-domains.md)makalesine başvurun.

**Sorun düzeltildi:**

* Bazen Azure AD Connect yüklemesi, parolası kuruluşun parola ilkesi tarafından belirtilen karmaşıklık düzeyini karşılayan bir yerel hizmet hesabı oluşturamadığından başarısız olur.
* Bağlayıcı alanındaki bir nesne, bir JOIN kuralı için aynı anda kapsam dışı hale geldiğinde ve diğeri için kapsam içinde olduğunda, JOIN kurallarının yeniden değerlendirilmediği bir sorun düzeltildi. Bu, JOIN koşulları birbirini dışlamalı iki veya daha fazla JOIN kuralına sahipseniz meydana gelebilir.
* Gelen eşitleme kurallarının (Azure AD 'den), JOIN kuralları içermeyen öncelik değerlerine sahip olmaları durumunda işlenmediği bir sorun düzeltildi.

**İyileştirmeler:**

* Windows Server 2016 Standard veya üzeri üzerinde Azure AD Connect yükleme desteği eklendi.
* Azure AD Connect için uzak veritabanı olarak SQL Server 2016 kullanma desteği eklendi.

## <a name="112810"></a>1.1.281.0
Yayımlanma tarihi: Ağustos 2016

**Sorun düzeltildi:**

* Eşitleme aralığında yapılan değişiklikler, bir sonraki eşitleme döngüsünün tamamlanmasından önce gerçekleşmez.
* Azure AD Connect sihirbaz, Kullanıcı adı alt çizgi (\_) ile başlayan bir Azure AD hesabını kabul etmez.
* Hesap parolası çok fazla özel karakter içeriyorsa Azure AD Connect sihirbaz Azure AD hesabının kimliğini doğrulayamazsa. Hata iletisi "kimlik bilgileri doğrulanamıyor. Beklenmeyen bir hata oluştu. " döndürülür.
* Hazırlama sunucusunu kaldırmak, Azure AD kiracısında parola eşitlemesini devre dışı bırakır ve parola eşitlemesinin etkin sunucu ile başarısız olmasına neden olur.
* Kullanıcı üzerinde depolanan Parola karması olmadığında sık olmayan durumlarda parola eşitleme başarısız olur.
* Azure AD Connect sunucusu hazırlama modu için etkinleştirildiğinde, parola geri yazma geçici olarak devre dışı bırakılır.
* Azure AD Connect sihirbaz, sunucu hazırlama modundayken gerçek parola eşitleme ve parola geri yazma yapılandırmasını göstermez. Her zaman devre dışı olarak gösterir.
* Sunucu hazırlama modundayken parola eşitleme ve parola geri yazma için yapılandırma değişiklikleri Azure AD Connect Sihirbazı tarafından kalıcı olmaz.

**İyileştirmeler:**

* Yeni bir eşitleme döngüsünü başarıyla başlatıp başlatamadığını göstermek için Start-ADSyncSyncCycle cmdlet 'i güncelleştirildi.
* Şu anda devam eden eşitleme döngüsünü ve işlemi sonlandırmak için Stop-ADSyncSyncCycle cmdlet 'i eklendi.
* Şu anda devam eden eşitleme döngüsünü ve işlemi sonlandırmak için Stop-ADSyncScheduler cmdlet 'i güncelleştirildi.
* Azure AD Connect sihirbazında [Dizin uzantıları](how-to-connect-sync-feature-directory-extensions.md) yapılandırılırken, "Teletex String" TÜRÜNDEKI Azure ad özniteliği artık seçilebilir.

## <a name="111890"></a>1.1.189.0
Yayımlanma tarihi: Haziran 2016

**Sorun ve geliştirmeler düzeltildi:**

* Azure AD Connect, artık FIPS ile uyumlu bir sunucuya yüklenebilir.
  * Parola eşitleme için bkz. [Parola karması eşitleme ve FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Bir NetBIOS adının Active Directory bağlayıcısında FQDN 'ye çözümlenemediği bir sorun düzeltildi.

## <a name="111800"></a>1.1.180.0
Yayımlanma tarihi: Mayıs 2016

**Yeni özellikler:**

* Azure AD Connect çalıştırmadan önce bunu yapmadıysanız, etki alanlarını doğrulamanızı sağlar ve yardımcı olur.
* [Almanya Microsoft bulut](reference-connect-instances.md#microsoft-cloud-germany)için destek eklendi.
* Yeni URL gereksinimleriyle birlikte en son [Microsoft Azure Kamu bulut](reference-connect-instances.md#microsoft-azure-government) altyapısı için destek eklendi.

**Sorun ve geliştirmeler düzeltildi:**

* Eşitleme kuralları bulmayı kolaylaştırmak için eşitleme kuralı düzenleyicisine filtre eklendi.
* Bir bağlayıcı alanı silinirken gelişmiş performans.
* Aynı nesne hem silinip hem de aynı çalıştırmaya eklenirken sorun düzeltildi (silme/ekleme adı verilir).
* Devre dışı bırakılmış bir eşitleme kuralı, yükseltme veya dizin şeması yenilemesinde eklenen nesneleri ve öznitelikleri artık yeniden etkinleştirmez.

## <a name="111300"></a>1.1.130.0
Yayımlanma tarihi: Nisan 2016

**Yeni özellikler:**

* [Dizin uzantılarına](how-to-connect-sync-feature-directory-extensions.md)çok değerli öznitelikler için destek eklendi.
* [Otomatik yükseltmenin](how-to-connect-install-automatic-upgrade.md) yükseltme için uygun kabul edilmesi için daha fazla yapılandırma çeşitlemeleri desteği eklendi.
* [Özel Zamanlayıcı](how-to-connect-sync-feature-scheduler.md#custom-scheduler)için bazı cmdlet 'ler eklendi.

## <a name="111190"></a>1.1.119.0
Yayımlanma tarihi: Mart 2016

**Sorun düzeltildi:**

* Bu işletim sisteminde parola eşitleme desteklenmediğinden, Express yüklemesinin Windows Server 2008 ' de (Pre-R2) kullanılmadığından emin olun.
* Özel filtre yapılandırması ile DirSync 'ten yükseltme beklendiği gibi çalışmadı.
* Daha yeni bir sürüme yükseltirken yapılandırma değişikliği olmadığında, tam içeri aktarma/eşitleme zamanlanmamalıdır.

## <a name="111100"></a>1.1.110.0
Yayımlanma tarihi: Şubat 2016

**Sorun düzeltildi:**

* Yükleme varsayılan C:\Program Files klasöründe değilse, önceki sürümlerden yükseltme işlemi çalışmaz.
* Yükleme sihirbazının sonunda **eşitleme Işlemini başlatır** ve temizlerseniz, Yükleme Sihirbazı ikinci kez çalıştırıldığında Zamanlayıcı etkinleştirilmeyecektir.
* Zamanlayıcı, ABD-ve tarih/saat biçiminin kullanıldığı sunucularda beklendiği gibi çalışmaz. Ayrıca, `Get-ADSyncScheduler` doğru süreleri döndürecek şekilde engeller.
* Azure AD Connect önceki bir sürümünü oturum açma seçeneği ve yükseltme olarak AD FS yüklediyseniz, Yükleme Sihirbazı 'nı yeniden çalıştıramazsınız.

## <a name="111050"></a>1.1.105.0
Yayımlanma tarihi: Şubat 2016

**Yeni özellikler:**

* Hızlı ayarlar müşterileri için [otomatik yükseltme](how-to-connect-install-automatic-upgrade.md) özelliği.
* Azure Multi-Factor Authentication kullanarak genel yönetici desteği ve Yükleme sihirbazında Privileged Identity Management.
  * Multi-Factor Authentication kullanırsanız, ara sunucunuzun https://secure.aadcdn.microsoftonline-p.com trafiğe izin vermeyi de sağlayabilirsiniz.
  * Multi-Factor Authentication düzgün çalışması için güvenilir siteler listenize https://secure.aadcdn.microsoftonline-p.com eklemeniz gerekir.
* İlk yüklemeden sonra kullanıcının oturum açma yöntemini değiştirmeye izin verin.
* Yükleme sihirbazında [etki alanı ve OU filtrelemesine](how-to-connect-install-custom.md#domain-and-ou-filtering) izin verin. Bu Ayrıca, tüm etki alanlarının kullanılamadığı ormanlarda bağlanmasına izin verir.
* [Zamanlayıcı](how-to-connect-sync-feature-scheduler.md) , eşitleme altyapısında yerleşiktir.

**Önizlemeden GA 'ye yükseltilen Özellikler:**

* [Cihaz geri yazma](how-to-connect-device-writeback.md).
* [Dizin uzantıları](how-to-connect-sync-feature-directory-extensions.md).

**Yeni Önizleme özellikleri:**

* Yeni varsayılan eşitleme süresi aralığı 30 dakikadır. Önceki tüm sürümler için üç saat olmak üzere kullanılır. [Zamanlayıcı](how-to-connect-sync-feature-scheduler.md) davranışını değiştirmek için destek ekler.

**Sorun düzeltildi:**

* DNS etki alanlarını doğrula sayfası her zaman etki alanlarını tanımıyor.
* AD FS yapılandırılırken etki alanı yönetici kimlik bilgilerini ister.
* Şirket içi AD hesapları, kök etki alanından farklı bir DNS ağacına sahip bir etki alanında bulunuyorsa Yükleme Sihirbazı tarafından tanınmaz.

## <a name="1091310"></a>1.0.9131.0
Yayımlanma tarihi: Aralık 2015

**Sorun düzeltildi:**

* Parola eşitleme, Active Directory Domain Services (AD DS) parolaları değiştirdiğinizde çalışmayabilir, ancak bir parola ayarladığınızda çalışır.
* Bir proxy sunucunuz olduğunda, Azure AD 'de kimlik doğrulaması yükleme sırasında başarısız olabilir veya yapılandırma sayfasında bir yükseltme iptal edilirse.
* Bir SQL Server Sistem Yöneticisi (SA) değilseniz, Azure AD Connect önceki bir sürümden tam SQL Server örneğiyle güncelleştirme başarısız olur.
* Uzak bir SQL Server Azure AD Connect önceki bir sürümünden güncelleştirme, "ADSync SQL veritabanına erişilemiyor" hatası gösterir.

## <a name="1091250"></a>1.0.9125.0
Yayımlanma tarihi: Kasım 2015

**Yeni özellikler:**

* AD FS Azure AD güvenine yeniden yapılandırabilir.
* Active Directory şemayı yenileyebilir ve eşitleme kurallarını yeniden oluşturabilir.
* Eşitleme kuralını devre dışı bırakabilir.
* , "AuthoritativeNull" öğesini eşitleme kuralında yeni bir sabit değer olarak tanımlayabilir.

**Yeni Önizleme özellikleri:**

* [Eşitleme için Azure AD Connect Health](how-to-connect-health-sync.md).
* [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) parola eşitleme desteği.

**Yeni desteklenen senaryo:**

* Birden çok şirket içi Exchange kuruluşu destekler. Daha fazla bilgi için bkz. [birden çok Active Directory ormanlı karma dağıtımlar](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Sorun düzeltildi:**

* Parola eşitleme sorunları:
  * Kapsam dışından, kapsamdaki bir nesnenin parolasının eşitlenmesi yok. Bu hem OU hem de öznitelik filtrelemeyi içerir.
  * Eşitlemeye dahil etmek için yeni bir OU seçilmesi tam parola eşitlemesi gerektirmez.
  * Devre dışı bırakılmış bir Kullanıcı etkinleştirildiğinde parola eşitlenmez.
  * Parola yeniden deneme kuyruğu sonsuzdur ve kullanımdan kaldırılacak 5.000 nesnelerinin önceki limiti kaldırılmıştır.
* Windows Server 2016 orman işlev düzeyiyle Active Directory bağlantı kurulamadı.
* İlk yüklemeden sonra Grup filtrelemesi için kullanılan grup değiştirilemiyor.
* , Parola geri yazma etkinken parola değişikliği yapan her kullanıcı için Azure AD Connect sunucuda artık yeni bir kullanıcı profili oluşturmayacaktır.
* Eşitleme kuralları kapsamlarında uzun tamsayı değerleri kullanılamıyor.
* Erişilemeyen etki alanı denetleyicileri varsa, "cihaz geri yazma" onay kutusu devre dışı kalır.

## <a name="1086670"></a>1.0.8667.0
Yayımlanma tarihi: Ağustos 2015

**Yeni özellikler:**

* Azure AD Connect Yükleme Sihirbazı artık tüm Windows Server dillerine yerelleştirilir.
* Azure AD parola yönetimi kullanılırken hesap kilit açma desteği eklendi.

**Sorun düzeltildi:**

* Yüklemeyi ilk başlatan kişi yerine başka bir kullanıcı yüklemeye devam ederse Azure AD Connect Yükleme Sihirbazı çöker.
* Azure AD Connect önceki bir yüklemesini kaldırma işlemi düzgün şekilde kaldırılamazsa Azure AD Connect, yeniden yüklemek mümkün değildir.
* Kullanıcı ormanın kök etki alanında değilse veya Ingilizce olmayan bir Active Directory sürümü kullanılırsa hızlı yükleme kullanılarak Azure AD Connect yüklenemez.
* Active Directory Kullanıcı hesabının FQDN 'SI çözümlenemiyorsa, "şema işlenemedi" hata iletisi gösterilir.
* Active Directory Bağlayıcısı 'nda kullanılan hesap sihirbazın dışında değiştiyse, sihirbaz sonraki çalışmalarda başarısız olur.
* Azure AD Connect bazen bir etki alanı denetleyicisine yüklenemez.
* Uzantı öznitelikleri eklendiyse "hazırlama modu" etkinleştirilemez ve devre dışı bırakılamaz.
* Active Directory bağlayıcısında hatalı bir parola nedeniyle bazı yapılandırmalarda parola geri yazma işlemi başarısız olur.
* Öznitelik filtrelemesinde bir ayırt edici ad (DN) kullanılıyorsa DirSync yükseltilemez.
* Parola sıfırlama kullanılırken aşırı CPU kullanımı.

**Önizleme özellikleri kaldırıldı:**

* Önizleme özelliği [Kullanıcı geri yazma](how-to-connect-preview.md#user-writeback) işlemi, önizleme müşterilerimizin geri bildirimlerine göre geçici olarak kaldırılmıştır. Daha sonra, belirtilen geri bildirimde bulunduktan sonra yeniden eklenecektir.

## <a name="1086410"></a>1.0.8641.0
Yayımlanma tarihi: Haziran 2015

**Azure AD Connect ilk sürümü.**

Azure AD Eşitleme adı Azure AD Connect olarak değiştirildi.

**Yeni özellikler:**

* [Hızlı ayarlar](how-to-connect-install-express.md) yüklemesi
* [AD FS yapılandırabilir](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* [DirSync 'ten yükseltebilir](how-to-dirsync-upgrade-get-started.md)
* [Yanlışlıkla silmeleri engelleme](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Tanıtılan [hazırlama modu](how-to-connect-sync-staging-server.md)

**Yeni Önizleme özellikleri:**

* [Kullanıcı geri yazma](how-to-connect-preview.md#user-writeback)
* [Grup geri yazma](how-to-connect-preview.md#group-writeback)
* [Cihaz geri yazma](how-to-connect-device-writeback.md)
* [Dizin genişletmeleri](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Yayımlanma tarihi: Mayıs 2015

**Yeni gereksinim:**

* Azure AD Eşitleme artık .NET Framework Version 4.5.1 'ın yüklenmesini gerektiriyor.

**Sorun düzeltildi:**

* Azure AD 'de parola geri yazma işlemi Azure Service Bus bağlantı hatasıyla başarısız oluyor.

## <a name="104910413"></a>1.0.491.0413
Yayımlanma tarihi: Nisan 2015

**Sorun ve geliştirmeler düzeltildi:**

* Active Directory Bağlayıcısı, geri dönüşüm kutusu etkinse ve ormanda birden çok etki alanı varsa, silme işlemini doğru işlemez.
* Azure Active Directory Bağlayıcısı için içeri aktarma işlemlerinin performansı geliştirilmiştir.
* Bir grup üyelik sınırını aştığında (varsayılan olarak, sınır 50.000 nesneler olarak ayarlanır), Grup Azure Active Directory silindi. Yeni davranışla, Grup silinmez, bir hata oluşur ve yeni üyelik değişiklikleri aktarılmaz.
* Bağlayıcı alanında aynı DN ile hazırlanmış bir silme zaten mevcutsa yeni bir nesne sağlanamıyor.
* Bazı nesneler, nesnede hazırlanmış olmasına rağmen bir Delta eşitlemesi sırasında eşitleme için işaretlenir.
* Parola eşitlemesini zorlamak, tercih edilen DC listesini de kaldırır.
* CSExportAnalyzer bazı nesne durumlarıyla ilgili sorunlar içeriyor.

**Yeni özellikler:**

* Bir JOIN artık MV 'daki "ANY" nesne türüne bağlanabilir.

## <a name="104850222"></a>1.0.485.0222
Yayımlanma tarihi: Şubat 2015

**İyileştirmeler:**

* Geliştirilmiş içeri aktarma performansı.

**Sorun düzeltildi:**

* Parola eşitleme, öznitelik filtrelemesi tarafından kullanılan Cloudfiltrelenmiş özniteliğini kullanır. Filtrelenmiş nesneler artık parola eşitleme kapsamında değildir.
* Topolojide çok sayıda etki alanı denetleyicisi olduğu nadir durumlarda parola eşitleme çalışmaz.
* Azure AD Bağlayıcısı 'nda cihaz yönetimi etkinleştirildikten sonra Azure AD bağlayıcısından içeri aktarırken "durdurulan-Server".
* Aynı ormandaki birden çok etki alanından yabancı güvenlik sorumlularını (FSPs 'ler) birleştirmek, belirsiz birleştirme hatasına neden olur.

## <a name="104751202"></a>1.0.475.1202
Yayımlanma tarihi: Aralık 2014

**Yeni özellikler:**

* Öznitelik tabanlı filtrelemeye sahip parola eşitleme artık desteklenmektedir. Daha fazla bilgi için bkz. [filtreleme Ile parola eşitleme](how-to-connect-sync-configure-filtering.md).
* Ms-DS-Externaldirectoryobjectıd özniteliği Active Directory 'e geri yazılır. Bu özellik Office 365 uygulamaları için destek ekler. Karma bir Exchange dağıtımında çevrimiçi ve şirket içi posta kutularına erişmek için OAuth2 kullanır.

**Düzeltilen yükseltme sorunları:**

* Sunucuda oturum açma Yardımcısı 'nın daha yeni bir sürümü var.
* Azure AD Eşitleme yüklemek için özel bir yükleme yolu kullanıldı.
* Geçersiz bir özel JOIN ölçütü yükseltmeyi engelliyor.

**Diğer düzeltmeler:**

* Office Pro Plus şablonları düzeltildi.
* Tire ile başlayan kullanıcı adlarından kaynaklanan, düzeltilen yükleme sorunları.
* Yükleme Sihirbazı ikinci kez çalıştırılırken Sourcetutturucu ayarı kaybedildiğinde düzeltildi.
* Parola eşitleme için sabit ETW izleme.

## <a name="104701023"></a>1.0.470.1023
Yayımlanma tarihi: Ekim 2014

**Yeni özellikler:**

* Birden çok şirket içi Active Directory Azure AD 'ye parola eşitleme.
* Tüm Windows Server dillerine yerelleştirilmiş yükleme kullanıcı arabirimi.

**AADSync 1,0 GA 'den yükseltme**

Zaten Azure AD Eşitleme yüklüyse, kullanıma hazır eşitleme kurallarından birini değiştirmiş olmanız durumunda yapmanız gereken ek bir adım vardır. 1\.0.470.1023 sürümüne yükselttikten sonra, değiştirdiğiniz eşitleme kuralları yinelenir. Değiştirilen her eşitleme kuralı için aşağıdakileri yapın:

1. Değiştirdiğiniz eşitleme kuralını bulun ve değişiklikleri bir yere göz atın.
1. Eşitleme kuralını silin.
1. Azure AD Eşitleme tarafından oluşturulan yeni eşitleme kuralını bulun ve ardından değişiklikleri yeniden uygulayın.

**Active Directory hesabı için izinler**

Active Directory parola karmalarını okuyabilmek için Active Directory hesaba ek izinler verilmelidir. Verilecek izinler "Dizin değişikliklerini çoğaltma" ve "Dizin değişikliklerini çoğaltma" olarak adlandırılır. Parola karmalarını okuyabilmek için her iki izin da gereklidir.

## <a name="104190911"></a>1.0.419.0911
Yayımlanma tarihi: Eylül 2014

**Azure AD Eşitleme ilk sürümü.**

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
