---
title: Azure Information System bileşenleri ve sınırları
description: Bu makalede Microsoft Azure mimarisine ve yönetimine ilişkin genel bir açıklama sunulmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727208"
---
# <a name="azure-information-system-components-and-boundaries"></a>Azure Information System bileşenleri ve sınırları
Bu makalede, Azure mimarisine ve yönetimine ilişkin genel bir açıklama sunulmaktadır. Azure sistem ortamı aşağıdaki ağlardan oluşur:

- Microsoft Azure üretim ağı (Azure ağı)
- Microsoft kurumsal ağ (Corpnet)

Ayrı BT ekipleri, bu ağların işlemlerinden ve bakımının sorumluluğundadır.

## <a name="azure-architecture"></a>Azure mimarisi
Azure, bir veri merkezi ağı aracılığıyla uygulama ve hizmet oluşturmaya, dağıtmaya ve yönetmeye yönelik bir bulut bilgi işlem platformudur ve altyapısıdır. Microsoft bu veri merkezlerini yönetir. Azure, belirttiğiniz kaynak sayısına bağlı olarak, kaynak gereksinimini temel alan sanal makineler (VM 'Ler) oluşturur. Bu VM 'Ler, bulutta kullanılmak üzere tasarlanan ve herkese açık olmayan bir Azure Hiper Yöneticisi üzerinde çalışır.

Her Azure fiziksel sunucu düğümünde, doğrudan donanım üzerinde çalışan bir hiper yönetici vardır. Hiper yönetici, bir düğümü değişken sayıda konuk VM 'ye böler. Her düğümün aynı zamanda ana bilgisayar işletim sistemini çalıştıran bir kök VM 'si vardır. Her VM 'de Windows Güvenlik Duvarı etkindir. Hizmet tanımı dosyasını yapılandırarak hangi bağlantı noktalarının adreslenebilir olduğunu tanımlarsınız. Bu bağlantı noktaları açık ve adreslenebilir, dahili veya harici tek alanlardır. Tüm trafik ve diske ve ağa erişim hiper yönetici ve kök işletim sistemi tarafından dağıtılır.

Azure VM 'Leri, ana bilgisayar katmanında en son Windows Server 'ın özelleştirilmiş ve sağlamlaştırılmış bir sürümünü çalıştırır. Azure, yalnızca VM 'Leri barındırmak için gereken bileşenleri içeren bir Windows Server sürümü kullanır. Bu, performansı artırır ve saldırı yüzeyini azaltır. Makine sınırları, işletim sistemi güvenliğine bağlı olmayan hiper yönetici tarafından zorlanır.

### <a name="azure-management-by-fabric-controllers"></a>Doku denetleyicileri tarafından Azure yönetimi

Azure 'da, fiziksel sunucularda çalışan VM 'Ler (dikey pencereler/düğümler) 1000 hakkında kümeler halinde gruplandırılır. VM 'Ler, yapı denetleyicisi (FC) adlı genişleme ve yedekli platform yazılım bileşeni tarafından bağımsız olarak yönetilir.

Her FC, kümesinde çalışan uygulamaların yaşam döngüsünü yönetir ve denetimi altındaki donanımın durumunu sağlar ve izler. Sunucu başarısız olduğunu belirlediğinde, sağlıklı sunuculardaki sanal makine örneklerini yeniden eğim etme gibi Autonomic işlemleri çalıştırır. FC, uygulamaları dağıtma, güncelleştirme ve ölçeklendirme gibi uygulama yönetimi işlemlerini de gerçekleştirir.

Veri merkezi kümelere bölünmüştür. Kümeler, hataları FC düzeyinde yalıtır ve belirli hata sınıflarının, meydana geldikleri kümenin ötesinde sunucuları etkilemesini önler. Belirli bir Azure kümesine hizmeti sunan FCs, bir FC kümesi halinde gruplandırılır.

### <a name="hardware-inventory"></a>Donanım envanteri

FC, önyükleme yapılandırma işlemi sırasında Azure donanım ve ağ cihazlarının envanterini hazırlar. Azure üretim ortamının girildiği yeni donanım ve ağ bileşenlerinin önyükleme yapılandırma işlemini izlemesi gerekir. FC, Datacenter. xml yapılandırma dosyasında listelenen tüm envanteri yönetmekten sorumludur.

### <a name="fc-managed-operating-system-images"></a>FC ile yönetilen işletim sistemi görüntüleri

İşletim sistemi ekibi, Azure üretim ortamındaki tüm ana bilgisayarlarda ve konuk VM 'lerde dağıtılan sanal sabit diskler biçiminde görüntüler sağlar. Takım, bu temel görüntüleri otomatik bir çevrimdışı derleme işlemi aracılığıyla oluşturur. Temel görüntü, çekirdek ve diğer çekirdek bileşenlerinin değiştirildiği ve Azure ortamını destekleyecek şekilde iyileştirdiği işletim sisteminin bir sürümüdür.

Üç tür doku ile yönetilen işletim sistemi görüntüsü vardır:

- Konağının Konak VM 'lerde çalışan özelleştirilmiş bir işletim sistemi.
- Yerel Kiracılar üzerinde çalışan yerel bir işletim sistemi (örneğin, Azure depolama). Bu işletim sisteminde herhangi bir hiper yönetici yok.
- N Konuk VM 'lerde çalışan bir konuk işletim sistemi.

Konak ve yerel FC ile yönetilen işletim sistemleri, bulutta kullanılmak üzere tasarlanmıştır ve herkese açık bir şekilde erişilebilir değildir.

#### <a name="host-and-native-operating-systems"></a>Konak ve yerel işletim sistemleri

Konak ve yerel, yapı aracılarını barındıran ve bir işlem düğümünde (düğümde ilk VM olarak çalışır) ve depolama düğümlerinde çalışan sağlamlaştırılmış işletim sistemi görüntüleridir. Konak ve yerel için iyileştirilmiş temel görüntüleri kullanmanın avantajı, API 'Ler veya kullanılmayan bileşenler tarafından sunulan yüzey alanını azaltmasıdır. Bunlar, yüksek güvenlik riskleri sunabilir ve işletim sisteminin parmak izini artırabilir. Azaltılan işletim sistemleri yalnızca Azure 'a gereken bileşenleri içerir.

#### <a name="guest-operating-system"></a>Konuk işletim sistemi

Konuk işletim sistemi VM 'lerinde çalışan Azure iç bileşenlerinin Uzak Masaüstü Protokolü çalıştırma olanağı yoktur. Taban çizgisi yapılandırma ayarlarındaki tüm değişiklikler değişiklik ve sürüm yönetimi sürecini kullanmalıdır.

## <a name="azure-datacenters"></a>Azure veri merkezleri
Microsoft Bulut altyapı ve Işlemler (MCıO) ekibi, tüm Microsoft çevrimiçi hizmetler için fiziksel altyapıyı ve veri merkezi olanaklarını yönetir. MCIO öncelikle veri merkezlerinde fiziksel ve çevresel denetimleri yönetmekten ve dış çevre ağ cihazlarını (uç yönlendiriciler ve veri merkezi yönlendiricileri gibi) yönetmeye ve desteklemeye yöneliktir. MCıO, veri merkezindeki raflarda en düşük sunucu donanımını ayarlamaktan de sorumludur. Müşterilerin Azure ile doğrudan etkileşimi yoktur.

## <a name="service-management-and-service-teams"></a>Hizmet yönetimi ve hizmet takımları
Hizmet takımları olarak bilinen çeşitli mühendislik grupları, Azure hizmeti desteğini yönetir. Her hizmet ekibi, Azure desteği olan bir alandan sorumludur. Her hizmet ekibinin, hizmette oluşan sorunları araştırmak ve çözmek için bir mühendis 7/24 kullanılabilir hale gelmelidir. Hizmet ekipleri, varsayılan olarak Azure 'da donanıma fiziksel erişime sahip değildir.

Hizmet takımları şunlardır:

- Uygulama Platformu
- Azure Active Directory
- Azure Compute
- Azure ağı
- Bulut Mühendisliği Hizmetleri
- ISSD: Güvenlik
- Çok Faktörlü Kimlik Doğrulama
- SQL Veritabanı
- Depolama

## <a name="types-of-users"></a>Kullanıcı türleri
Microsoft 'un çalışanları (veya yükleniciler), iç Kullanıcı olarak kabul edilir. Diğer tüm kullanıcılar dış Kullanıcı olarak kabul edilir. Tüm Azure iç kullanıcılarının çalışan durumu, müşteri verilerine erişimini tanımlayan bir duyarlılık düzeyiyle kategorize edilir (erişim veya erişim yok). Azure 'a yönelik kullanıcı ayrıcalıkları (kimlik doğrulama gerçekleştikten sonra yetkilendirme izni) aşağıdaki tabloda açıklanmıştır:

| Role | İç veya dış | Duyarlılık düzeyi | Gerçekleştirilen yetkili ayrıcalıklar ve işlevler | Erişim türü
| --- | --- | --- | --- | --- |
| Azure veri merkezi mühendisi | İç | Müşteri verilerine erişim yok | Şirket için fiziksel güvenliği yönetin. Veri merkezinde yer alan ve giden patrols gerçekleştirin ve tüm giriş noktalarını izleyin. Genel Hizmetler (örneğin, dinleme veya temizleme gibi) sağlayan ve veri merkezinde çalışan belirli bir temizlenmiş personeli içeri ve dışarı aktarmak. Ağ donanımının rutin izleme ve bakımını gerçekleştirin. Çeşitli araçlar kullanarak olay yönetimi ve onarım işleri gerçekleştirin. Veri merkezlerinde fiziksel donanımın rutin izlenmesini ve bakımını gerçekleştirin. Özellik sahiplerinden isteğe bağlı ortama erişim. Adli araştırmalar gerçekleştirebilir, olay raporlarını günlüğe kaydeder ve zorunlu güvenlik eğitimi ve ilke gereksinimleri gerektirir. Tarayıcılar ve günlük toplama gibi kritik güvenlik araçlarının işletimsel sahipliği ve bakımı. | Ortama kalıcı erişim. |
| Azure olay önceliklendirme (hızlı yanıt mühendisleri) | İç | Müşteri verilerine erişim | MCIO, destek ve mühendislik takımları arasındaki iletişimleri yönetin. Platform olayları, dağıtım sorunları ve hizmet istekleri önceliklendirme. | Müşteriye ait olmayan sistemlere sınırlı kalıcı erişimle, ortama tam zamanında erişim. |
| Azure dağıtım mühendisleri | İç | Müşteri verilerine erişim | Azure desteğiyle platform bileşenlerini, yazılımlarını ve zamanlanan yapılandırma değişikliklerini dağıtın ve yükseltin. | Müşteriye ait olmayan sistemlere sınırlı kalıcı erişimle, ortama tam zamanında erişim. |
| Azure müşteri kesintisi desteği (kiracı) | İç | Müşteri verilerine erişim | Bireysel işlem kiracılarının ve Azure hesaplarının hata ayıklama ve tanılama sorunlarını giderme. Hataları analiz edin. Platform veya müşteri için kritik düzeltmeleri sürüyor ve destek genelinde teknik iyileştirmeleri sürüyor. | Müşteriye ait olmayan sistemlere sınırlı kalıcı erişimle, ortama tam zamanında erişim. |
| Azure Live site mühendisleri (izleme mühendisleri) ve olay | İç | Müşteri verilerine erişim | Tanılama araçlarını kullanarak platform sistem durumunu tanılayın ve azaltabilirsiniz. Birim sürücüleri için düzeltme, kesintiden kaynaklanan öğeleri onarır ve geri yükleme eylemlerine yardımcı olur. | Müşteriye ait olmayan sistemlere sınırlı kalıcı erişimle, ortama tam zamanında erişim. |
|Azure müşterileri | Dış | Yok | Yok | Yok |

Azure, kurumsal kullanıcıların ve müşterilerin (veya kuruluş kullanıcıları adına işlem gören işlemlerin) kimliğini doğrulamak için benzersiz tanımlayıcılar kullanır. Bu, Azure ortamının bir parçası olan tüm varlıklar ve cihazlar için geçerlidir.

### <a name="azure-internal-authentication"></a>Azure iç kimlik doğrulaması

Azure iç bileşenleri arasındaki iletişimler TLS şifrelemesi ile korunur. Çoğu durumda, X. 509.440 sertifikaları kendinden imzalanır. Azure ağı dışından erişilebilen bağlantılara sahip sertifikalara, FCs için sertifikalar gibi bir özel durum söz konusu olabilir. FCs, güvenilen bir kök CA tarafından desteklenen bir Microsoft yetkilisi sertifikası (CA) tarafından verilmiş sertifikalara sahiptir. Bu, FC ortak anahtarlarının kolayca alınmasına izin verir. Ayrıca, Microsoft Geliştirici Araçları FC ortak anahtarlarını kullanır. Geliştiriciler yeni uygulama görüntülerini gönderdiğinde, gömülü gizli dizileri korumak için görüntüler bir FC ortak anahtarıyla şifrelenir.

### <a name="azure-hardware-device-authentication"></a>Azure donanım cihaz kimlik doğrulaması

FC, kendi denetimi altındaki çeşitli donanım cihazlarına kimliğini doğrulamak için kullanılan bir dizi kimlik bilgisi (anahtarlar ve/veya parolalar) sağlar. Microsoft bu kimlik bilgilerine erişimi engellemek için bir sistem kullanır. Özellikle, bu kimlik bilgilerinin taşınması, kalıcılığı ve kullanılması, Azure geliştiricilerinin, yöneticilerin ve yedekleme hizmetlerinin ve personelin gizli, gizli veya özel bilgilere erişmesini engelleyecek şekilde tasarlanmıştır.

Microsoft, FC 'nin ana kimlik ortak anahtarı temel alınarak şifrelemeyi kullanır. Bu, ağ donanımı cihazlarına erişmek için kullanılan kimlik bilgilerini aktarmak üzere FC kurulumu ve FC yeniden yapılandırma saatlerinde oluşur. FC kimlik bilgilerine ihtiyaç duyduğunda, FC bunları alır ve şifresini çözer.

### <a name="network-devices"></a>Ağ cihazları

Azure ağ ekibi, bir Azure istemcisinin Ağ cihazlarına (yönlendiriciler, anahtarlar ve yük dengeleyiciler) kimlik doğrulaması yapmasını sağlamak üzere ağ hizmeti hesaplarını yapılandırır.

## <a name="secure-service-administration"></a>Güvenli hizmet yönetimi
Güvenli yönetici iş istasyonlarını (SAWs) kullanmak için Azure operasyon personeli gereklidir. Müşteriler, ayrıcalıklı erişim iş istasyonlarını kullanarak benzer denetimleri uygulayabilir. SAWs ile yönetim personeli, kullanıcının standart kullanıcı hesabından ayrı olarak atanmış ayrı bir yönetim hesabı kullanır. Bu, bu hassas hesaplar için güvenilir bir iş istasyonu sağlayarak bu hesabın ayrım uygulaması üzerinde oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yardımcı olma hakkında daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)
