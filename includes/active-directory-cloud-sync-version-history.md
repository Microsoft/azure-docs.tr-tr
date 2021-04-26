---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901246"
---
Bu makalede, yayımlanan Azure Active Directory Connect sağlama aracısının sürümleri ve özellikleri listelenmektedir. Azure AD ekibi, sağlama aracısını yeni özellikler ve işlevlerle düzenli olarak güncelleştirir. Yeni bir sürüm yayınlandığında sağlama Aracısı otomatik olarak güncelleştirilir. 

Microsoft, en son aracı sürümü ve bir sürümü için doğrudan destek sağlar.

## <a name="113540"></a>1.1.354.0

20 Ocak 2021: indirilmek üzere yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler
- Önceden özel oluşturulmuş GMSA hesabı desteği dahil, GMSA deneyimine iyileştirme
- GMSA kurulumu için [PowerShell cmdlet 'leri](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) desteği
- Aracı yüklemesi için [CLI desteği](../articles/active-directory/cloud-sync/how-to-install-pshell.md) (sessiz yükleme)
- Aracı kaynak karantina sorunları için ek Tanılamalar
- OU kapsam filtrelerinin bellek kullanımını azaltmayı içeren hata düzeltmeleri, yalnızca kapsamdaki kullanıcılar için PHS, OU kapsamı kullanılırken OU 'da iç içe nesnelerin işlenmesi. 


### <a name="fixed-issues"></a>Düzeltilen sorunlar
-    Kapsam grubu kapsam dışı olduğunda karantinaya almayı önle
-   kapsam filtreleri yapılandırıldığında-PHS işi şimdi yalnızca kapsamdaki kullanıcılar için çalışır
-   Aracı yükseltme sırasında bir süre askıda kalıyor
-   OU kapsamı kullanılırken iç içe yerleştirilmiş nesneler için ilk eşitleme
-   Repair-AADCloudSyncToolsAccount daha sağlam hale getirme
-   OU kapsam filtrelerinin büyük bellek kullanımını azaltma
-   Rol üyeleri bir güvenlik grubu içeriyorsa yönetici rolü denetimi başarısız olur
-   Aracı sertifikası yenilemeyi engelleyen GMSA klasörü izin sorununu düzeltir







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Yayın durumu

23 Kasım 2020: indirme için yayımlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* [GMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts) desteği
* Artımlı veya Delta eşitleme çevrimi sırasında 1500 ' den az üyeye kadar olan gruplar için destek. Bu, Grup kapsamı filtresi kullanılırken geçerlidir
* En fazla 15K 'ye üye boyutu olan büyük gruplar için destek
* İlk eşitleme geliştirmeleri
* Gelişmiş ayrıntılı günlük kaydı
* [Aadcloudsynctools PowerShell modülünün](../articles/active-directory/cloud-sync/reference-powershell.md) tanıtımı
* Ingilizce olmayan sunucuda aracının yüklenmesine izin veren bir kısıtlama düzeltildi
* Yalnızca kapsamdaki nesneler için PHS filtreleme desteği (başlangıçta tüm nesneler için parola karmaları eşitliyoruz)
* Aracıdaki bellek sızıntısı sorunu düzeltildi
* Geliştirilmiş sağlama günlükleri
* [LDAP bağlantısı zaman aşımını](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) yapılandırma desteği 
* [Başvuru](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) oluşturma yapılandırması için destek 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Yayın durumu

4 Aralık 2019: indirme için yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* Kullanıcıyı eşitlemek, şirket içi Active Directory Azure AD 'ye iletişim kurmak ve verileri gruplamak için [Azure AD Connect bulut eşitlemesi](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) desteğini içerir


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Yayın durumu

9 Eylül 2019: otomatik güncelleştirme için yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* Hazırlama Aracısı sorunlarını gidermek için ek izleme ve günlüğe kaydetme yapılandırma olanağı
* Yalnızca eşleme performansını geliştirmek için eşlemede yapılandırılmış Azure AD özniteliklerini getirme olanağı

### <a name="fixed-issues"></a>Düzeltilen sorunlar

* Azure AD bağlantı hatalarıyla ilgili sorunlar oluşursa, aracıdaki bir hata düzeltilmedi durumuna geçti
* İkili veriler Azure Active Directory okunmadığında soruna neden olan bir hata düzeltildi
* Aracıdaki bir hata düzeltildiğinde bulut hibrit kimlik hizmeti ile güveni yenileyemedi

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Yayın durumu

23 Ocak 2019: indirilmek üzere yayınlandı

### <a name="new-features-and-improvements"></a>Yeni özellikler ve geliştirmeler

* Daha iyi performans, kararlılık ve güvenilirlik için sağlama Aracısı ve bağlayıcı mimarisi kullanıma alınıyor 
* UI tabanlı yükleme sihirbazını kullanarak sağlama Aracısı yapılandırması basitleşme 
* Otomatik aracı güncelleştirmeleri için destek eklendi