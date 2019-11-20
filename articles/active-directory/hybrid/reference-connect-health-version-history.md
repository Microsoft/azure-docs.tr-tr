---
title: Azure AD Connect Health Sürüm Geçmişi
description: Bu belgede Azure AD Connect Health sürümleri ve bu sürümlere nelerin dahil olduğu açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27c0d3055512145396f204fdb17e9375f65a0db3
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562323"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Sürüm Yayınlama Geçmişi
Azure Active Directory ekibi, yeni özellikler ve işlevlerle Azure AD Connect Health düzenli olarak güncelleştirir. Bu makalede, yayımlanan sürümleri ve özellikler listelenir.  

> [!NOTE]
> Yeni sürüm yayınlandığında Connect Health Agents otomatik olarak güncelleştirilir. Lütfen otomatik yükseltme ayarlarının Azure portal etkinleştirildiğinden emin olun. 
>

Eşitleme için Azure AD Connect Health Azure AD Connect yüklemesiyle tümleşiktir. Özellik geri bildirimi [Azure AD Connect yayın geçmişi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) hakkında daha fazla bilgi edinin, [Connect Health Kullanıcı ses kanalı](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591) ' nı oylayın

## <a name="july-2019"></a>2019 Temmuz
**Aracı güncelleştirmesi**
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.59.0) 
   1. TestWindowsTransport 'da metin değişikliği
   2. AD FS RP karşıya yükleme için değişiklikler
   
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.56.0) 
   1. CheckOffice365Endpoints testinde TestWindowsTransport test ve Remove WsTrust endpoints denetimleri ekleme
   2. OS ve .NET bilgilerini günlüğe kaydet
   3. RP yapılandırma iletisini karşıya yükleme boyutunu 1 MB olarak artırın.
   4. Hata düzeltmeleri
   
* AD DS için Azure AD Connect Health Aracısı (sürüm 3.1.56.0) 
   1. OS ve .NET bilgilerini günlüğe kaydet 
   2. Hata düzeltmeleri

## <a name="may-2019"></a>Mayıs 2019
**Aracı güncelleştirmesi:** 
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.51.0) 
   1. Aynı istemci-istek kimliğini paylaşan birden çok oturum açma arasında ayrım yapmak için hata düzeltildi.
   2. Dil yerelleştirilmiş sunuculardaki Hatalı Kullanıcı adı/parola hatalarını ayrıştırmak için hata düzeltildi.   

## <a name="april-2019"></a>2019 Nisan
**Aracı güncelleştirmesi:** 
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.46.0) 
   1. Çözüm, ADFS için yinelenen SPN uyarı işlemini denetleyin

## <a name="march-2019"></a>Mart 2019
**Aracı güncelleştirmesi:** 
* AD DS için Azure AD Connect Health Aracısı (sürüm 3.1.41.0)  
   1. .NET sürüm koleksiyonu
   2. Belirli kategoriler eksik olduğunda performans sayacı toplama geliştirmesi
   3. Birden çok Izleme Aracısı örneğinin oluşturulmasını önlemek için hata düzeltildi

* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.41.0) 
   1. ADFSToolBox kullanarak AD FS test betikleri tümleştirme ve yükseltme
   2. .NET sürüm koleksiyonu Uygula
   3. Belirli kategoriler eksik olduğunda performans sayacı toplama geliştirmesi
   4. Birden çok Izleme Aracısı örneğinin oluşturulmasını önlemek için hata düzeltildi


## <a name="november-2018"></a>2018 Kasım
**Yeni GA özellikleri:** 
* Sync için Azure AD Connect Health-portaldan yinelenen öznitelik eşitleme hatalarını tanılayın ve düzeltin

**Aracı güncelleştirmesi:** 
* AD DS için Azure AD Connect Health Aracısı (sürüm 3.1.24.0) 
   1. Aktarım Katmanı Güvenliği (TLS) Protokolü sürüm 1,2 uyumluluğu ve zorlaması
   2. Genel Katalog uyarı gürültüsünü azaltma
   3. Sistem Durumu Aracısı kayıt hata düzeltmeleri

* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.24.0)  
   1. Aktarım Katmanı Güvenliği (TLS) Protokolü sürüm 1,2 uyumluluğu ve zorlaması
   2. Yerelleştirilmiş işletim sistemi için test-ADFSRequestToken desteği
   3. Çözülen tanılama Aracısı EventHandler kilitleme sorunu
   4. Sistem Durumu Aracısı kayıt hata düzeltmeleri

## <a name="august-2018"></a>Ağustos 2018 
*  Eşitleme için Azure AD Connect Health Aracısı (sürüm 3.1.7.0) Azure AD Connect sürümüyle yayınlandı 1.1.880.0    
   1. [.NET Framework KB 'lik yayınlar ile izleme aracısının yüksek CPU sorunu](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync) için düzeltme

## <a name="june-2018"></a>Haziran 2018 
**Yeni Önizleme özellikleri:** 
* Sync için Azure AD Connect Health-portaldan yinelenen öznitelik eşitleme hatalarını tanılayın ve düzeltin 

**Aracı güncelleştirmesi:** 
* AD DS için Azure AD Connect Health Aracısı (sürüm 3.1.7.0)    
  1. [.NET Framework KB 'lik yayınlar ile izleme aracısının yüksek CPU sorunu](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync) için düzeltme
   
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.7.0)  
  1. [.NET Framework KB 'lik yayınlar ile izleme aracısının yüksek CPU sorunu](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync) için düzeltme
  2. ADFS Server 2016 ikincil sunucu 'da test sonuçları düzeltmeleri
   
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.1.2.0)  
  1. Aracı bellek yönetimine yönelik düzeltme ve özellikle 3.0.244.0 sürümü için ilgili uyarılar


## <a name="may-2018"></a>Mayıs 2018
**Aracı güncelleştirmesi:**
* AD DS için Azure AD Connect Health Aracısı (sürüm 3.0.244.0)
  1. Aracı Gizlilik geliştirmesi  
  2. Hata düzeltmeleri ve genel iyileştirmeler

* AD FS için Azure AD Connect Health Aracısı (sürüm 3.0.244.0)
  1. Aracı tanılama hizmeti ve ilgili PowerShell modülü geliştirmeleri
  2. Aracı Gizlilik geliştirmesi  
  3. Hata düzeltmeleri ve genel iyileştirmeler

* Eşitleme için Azure AD Connect Health Aracısı (sürüm 3.0.164.0) Azure AD Connect sürümüyle yayınlandı 1.1.819.0 
  1. Aracı Gizlilik geliştirmesi  
  2. Hata düzeltmeleri ve genel iyileştirmeler


## <a name="march-2018"></a>Mart 2018
**Yeni Önizleme özellikleri:**
* AD FS riskli IP raporu ve uyarısı için Azure AD Connect Health.

**Aracı güncelleştirmesi:**

* AD DS için Azure AD Connect Health Aracısı (sürüm 3.0.176.0)
  1. Aracı kullanılabilirliği geliştirmeleri 
  2. Hata düzeltmeleri ve genel iyileştirmeler
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.0.176.0)
  1. Aracı kullanılabilirliği geliştirmeleri 
  2. Hata düzeltmeleri ve genel iyileştirmeler
* Eşitleme için Azure AD Connect Health Aracısı (sürüm 3.0.129.0) Azure AD Connect sürümüyle yayınlandı 1.1.750.0  
  1. Aracı kullanılabilirliği geliştirmeleri 
  2. Hata düzeltmeleri ve genel iyileştirmeler

## <a name="december-2017"></a>Aralık 2017
**Aracı güncelleştirmesi:**

* AD DS için Azure AD Connect Health Aracısı (sürüm 3.0.145.0)
  1. Aracı kullanılabilirliği geliştirmeleri 
  2. Yeni Aracı sorun giderme komutları eklendi
  3. Hata düzeltmeleri ve genel iyileştirmeler
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.0.145.0)
  1. Yeni Aracı sorun giderme komutları eklendi
  2. Aracı kullanılabilirliği geliştirmeleri 
  3. Hata düzeltmeleri ve genel iyileştirmeler
  
## <a name="october-2017"></a>Ekim 2017
**Aracı güncelleştirmesi:**

 * Eşitleme için Azure AD Connect Health Aracısı (sürüm 3.0.129.0) Azure AD Connect sürümüyle yayınlandı 1.1.649.0
<br></br> Eşitleme için Azure AD Connect ve Azure AD Connect Health Aracısı arasında bir sürüm uyumluluğu sorunu düzeltildi. Bu sorun, 1.1.647.0 sürümüne yerinde yükseltme Azure AD Connect, ancak şu anda sistem durumu Aracısı sürümü 3.0.127.0 olan müşterileri etkiler. Yükseltmeden sonra, sistem durumu Aracısı artık Azure AD Connect eşitleme hizmeti ile Azure AD Sistem Sağlığı Hizmeti arasında sistem durumu verileri gönderemez. Bu düzeltmeyle birlikte Azure AD Connect yerinde yükseltme sırasında durum Aracısı sürümü 3.0.129.0 yüklenir. Sistem Durumu Aracısı sürüm 3.0.129.0 Azure AD Connect sürüm 1.1.649.0 ile uyumluluk sorununa sahip değil.

## <a name="july-2017"></a>Temmuz 2017
**Aracı güncelleştirmesi:**

* AD DS için Azure AD Connect Health Aracısı (sürüm 3.0.68.0)
  1. Hata düzeltmeleri ve genel iyileştirmeler
  2. Sovereign bulut desteği
* AD FS için Azure AD Connect Health Aracısı (sürüm 3.0.68.0)
  1. Hata düzeltmeleri ve genel iyileştirmeler
  2. Sovereign bulut desteği
* Eşitleme için Azure AD Connect Health Aracısı (sürüm 3.0.68.0) Azure AD Connect sürümüyle yayınlandı 1.1.614.0
  1. Microsoft Azure Kamu bulut ve Microsoft Bulut Almanya desteği

## <a name="april-2017"></a>Nisan 2017      
**Aracı güncelleştirmesi:**

* AD FS için Azure AD Connect Health Aracısı (sürüm 3.0.12.0)
  1. Hata düzeltmeleri ve genel iyileştirmeler
* AD DS için Azure AD Connect Health Aracısı (sürüm 3.0.12.0)
  1. Performans sayaçları karşıya yükleme geliştirmeleri
  2. Hata düzeltmeleri ve genel iyileştirmeler

## <a name="october-2016"></a>2016 Ekim
**Aracı güncelleştirmesi:**

* AD FS için Azure AD Connect Health Aracısı (sürüm 2.6.408.0)
* Kimlik doğrulama isteklerindeki istemci IP adreslerini algılamayla geliştirmeler
* Uyarılarla ilgili hata düzeltmeleri
* AD DS için Azure AD Connect Health Aracısı (sürüm 2.6.408.0)
* Uyarılarla ilgili hata düzeltmeleri.
* Eşitleme için Azure AD Connect Health Aracısı (sürüm 2.6.353.0) Azure AD Connect sürümüyle yayınlandı 1.1.281.0
* Eşitleme hata raporları için gerekli verileri sağlayın
* Uyarılarla ilgili hata düzeltmeleri

**Yeni Önizleme özellikleri:**

* Azure AD Connect için eşitleme hata raporları

**Yeni özellikler:**

* AD FS-IP adresi alanı için Azure AD Connect Health, hatalı Kullanıcı adı/parola ile önde gelen 50 kullanıcı hakkında raporda bulunur.

## <a name="july-2016"></a>Temmuz 2016
**Yeni Önizleme özellikleri:**

* [AD DS için Azure AD Connect Health](how-to-connect-health-adds.md).

## <a name="january-2016"></a>2016 Ocak
**Aracı güncelleştirmesi:**

* AD FS için Azure AD Connect Health Aracısı (sürüm 2.6.91.1512)

**Yeni özellikler:**

* [Azure AD Connect Health aracıları için test bağlantısı aracı](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015 Kasım
**Yeni özellikler:**

* [Rol tabanlı Access Control](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) için destek

**Yeni Önizleme özellikleri:**

* [Eşitleme için Azure AD Connect Health](how-to-connect-health-sync.md).

**Sorun düzeltildi:**

* Aracı kayıtları sırasında görülen hatalara yönelik hata düzeltmeleri.

## <a name="september-2015"></a>Eylül 2015
**Yeni özellikler:**

* AD FS için yanlış Kullanıcı adı parolası raporu
* Kimliği doğrulanmamış HTTP proxy 'yi yapılandırma desteği
* Sunucu çekirdeğinde aracı yapılandırma desteği
* AD FS uyarılarında iyileştirmeler
* Azure AD Connect Health aracıdaki iyileştirmeler, bağlantı ve verilerin karşıya yüklenmesi için AD FS.

**Sorun düzeltildi:**

* AD FS hata türleri için kullanım öngörülerine yönelik hata düzeltmeleri.

## <a name="june-2015"></a>Haziran 2015
**AD FS ve AD FS proxy için Azure AD Connect Health ilk sürümü.**

**Yeni özellikler:**

* AD FS ve AD FS proxy sunucuları ile e-posta bildirimleri ile izleme uyarıları.
* AD FS performans Sayaçlarındaki AD FS topolojiye ve desenlere kolay erişim.
* Uygulamalar, kimlik doğrulama yöntemleri, Istek ağ konumu gibi AD FS sunuculardaki başarılı belirteç isteklerinin eğilimi.
* Uygulamalar, hata türleri vb. gruplanmış AD FS sunucularındaki başarısız istek eğilimleri.
* Azure AD Genel yönetici kimlik bilgilerini kullanarak daha basit aracı dağıtımı.  

## <a name="next-steps"></a>Sonraki adımlar
[Bulutta şirket içi kimlik altyapınızı ve eşitleme hizmetlerinizi izleme](whatis-hybrid-identity-health.md)hakkında daha fazla bilgi edinin.

