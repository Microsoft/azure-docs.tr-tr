---
title: Azure veri merkezlerinin fiziksel güvenliği-Microsoft Azure | Microsoft Docs
description: Makalede, Microsoft 'un fiziksel altyapı, güvenlik ve uyumluluk teklifleri dahil olmak üzere Azure veri merkezlerini güvenli hale getirmek için ne olduğu açıklanır.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 634f89b3123902d981ad07dd1404315387104322
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726736"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure tesisler, şirket içi ve fiziksel güvenlik
Bu makalede, Microsoft 'un Azure altyapısını güvenli hale getirmek için ne olduğu açıklanır.

## <a name="datacenter-infrastructure"></a>Veri merkezi altyapısı
Azure, binlerce çevrimiçi hizmetler desteklemeye ve dünya çapındaki 100 ' den fazla güvenli tesisden fazlasını kapsayan [küresel olarak dağıtılmış bir veri merkezi altyapısından](https://azure.microsoft.com/global-infrastructure/)oluşur.

Altyapı, uygulamaları dünyanın dört bir yanındaki kullanıcılara yaklaştırmak, veri uygunluğunu korumak ve müşteriler için kapsamlı uyumluluk ve dayanıklılık seçenekleri sunmak üzere tasarlanmıştır. Azure, dünya çapındaki 52 bölgeye sahiptir ve 140 ülkede/bölgede kullanılabilir.

Bölge, büyük ve dayanıklı bir ağ aracılığıyla birbirine bağlanmış bir veri merkezleri kümesidir. Ağ, varsayılan olarak içerik dağıtımı, Yük Dengeleme, artıklık ve şifreleme içerir. Diğer tüm bulut sağlayıcılarından daha fazla genel bölge ile Azure, size ihtiyacınız olan uygulamaları dağıtma esnekliği sunar.

Azure bölgeleri coğrafyalar halinde düzenlenir. Her Azure coğrafyası, veri yerleşimi, bağımsızlık, uyumluluk ve dayanıklılık gereksinimlerinin ilgili coğrafi bölge içinde karşılanmasını sağlar.

Coğrafyalar, özel veri yerleşikliği ve uyumluluk gereksinimleri olan müşterilerin verilerini ve uygulamalarını yakında tutmasına imkan tanır. Geographiler, adanmış ve yüksek kapasiteli ağ altyapısına olan bağlantılar aracılığıyla, tam bölge hatası ile hataya dayanıklıdır.

Kullanılabilirlik alanları, bir Azure bölgesi içinde fiziksel olarak ayrı konumlardır. Her kullanılabilirlik alanı, bağımsız güç, soğutma ve ağ ile donatılmış bir veya daha fazla veri merkezinden oluşur. Kullanılabilirlik alanları, yüksek kullanılabilirlik ve düşük gecikmeli çoğaltma ile görev açısından kritik uygulamalar çalıştırmanıza olanak tanır.

Aşağıdaki şekilde, aynı veriler içindeki Azure genel altyapı çiftleri bölgesinin ve kullanılabilirlik bölgelerinin yüksek kullanılabilirlik, olağanüstü durum kurtarma ve yedekleme için nasıl bulunduğu gösterilmektedir.

![Veri fazlalığını gösteren diyagram](./media/physical-security/data-residency-boundary.png)

Coğrafi olarak dağıtılmış veri merkezleri, ağ gecikmesini azaltmak ve coğrafi olarak yedekli yedekleme ve yük devretme için izin vermek üzere Microsoft 'un müşterilerine kapatılmasını sağlar.

## <a name="physical-security"></a>Fiziksel güvenlik
Microsoft, verilerinizin depolandığı alanlara fiziksel erişimi kesinlikle kontrol eden bir şekilde veri merkezlerini tasarlar, oluşturur ve çalışır. Microsoft verilerinizi korumanın önemini anlamıştır ve verilerinizi içeren veri merkezlerinin güvenliğinin sağlanmasına yardımcı olmaya kararlıdır. Microsoft 'un, Azure 'u destekleyen fiziksel tesislerin tasarlanmasıyla, oluşturulmasına ve bu olanakları desteklemeye yönelik olarak tamamına yönelik bir bölüm sunuyoruz. Bu ekip, son derece fiziksel güvenliği korumak için yatırım yapmış.

Microsoft, yetkisiz kullanıcıların verilere ve veri merkezi kaynaklarına fiziksel erişim riskini azaltmak için fiziksel güvenliğe yönelik katmanlı bir yaklaşım alır. Microsoft tarafından yönetilen veri merkezlerinin kapsamlı koruma katmanları vardır: tesisin çevre sürümünde, Bina içinde ve veri merkezi tabanında erişim onayı. Fiziksel güvenlik katmanları şunlardır:

- **Erişim isteği ve onayı.** Veri merkezine ulaşan önce erişim istemeniz gerekir. Ziyaretiniz için uyumluluk veya denetim amaçları gibi geçerli bir iş doğrulaması sağlamanız gerekir. Tüm istekler, Microsoft çalışanları tarafından erişim gereksinimi temelinde onaylanır. Erişim gereksinimi gereği, veri merkezlerinde bir görevi tam en düşük düzeyde tutmak için gereken kişilerin sayısını tutmaya yardımcı olur. Microsoft izin verdikten sonra, yalnızca bir bireyin, onaylanan iş gerekçe temelinde gerekli olan veri merkezinin ayrı alanına erişimi vardır. İzinler belirli bir süre ile sınırlıdır ve sonra süresi sona erer.

- **Tesis çevresi.** Bir veri merkezine geldiğinizde, iyi tanımlanmış bir erişim noktasından gitmeniz gerekir. Genellikle, her bir kuşın her bir saniyede çelik ve somut bir şekilde oluşan uzun bir ölçü. Bir güvenlik ekibinin videolarını her zaman izlerken, veri merkezlerinin etrafında kameralar vardır.

- **Giriş oluşturma.** Veri Merkezi giriş, eğitilebilir eğitim ve arka plan denetimleri olan profesyonel güvenlik ofisleriyle birlikte çalışıyor. Bu güvenlik ofisleri ayrıca veri merkezini düzenli olarak denetler ve her zaman veri merkezinde bulunan kameraların videolarını izler.

- **Yapı içinde.** Oluşturma 'yı girdikten sonra, veri merkezinde taşımaya devam etmek için biyometri ile iki öğeli kimlik doğrulaması geçirmeniz gerekir. Kimliğiniz doğrulandıktan sonra, yalnızca erişimini onayladığınız veri merkezinin bölümünü girebilirsiniz. Yalnızca onaylanan sürenin süresi boyunca kalabilirler.

- **Veri merkezi tabanı.** Yalnızca girmeye onayladığınız tabanda izin verilir. Tam gövde metal algılama filtrelemeden geçiş yapmanız gerekir. Yetkisiz veriler hakkında bilgi sahibi olmadan veri merkezini girme veya ayrılma riskini azaltmak için, yalnızca onaylanan cihazlar veri merkezi katında kendi yolunu yapabilir. Ayrıca, video kameraları her sunucu rafın ön ve geri 'yi izler. Veri merkezi tabanından çıktığınızda, yeniden tam gövde metal algılama filtrelemeden geçiş yapmanız gerekir. Veri merkezini bırakmak için, ek bir güvenlik taramasından geçiş yapmanız gerekir.

Microsoft, ziyaretçilerin herhangi bir Microsoft tesisinden ayrılınca rozetleri işlemesini gerektirir.

## <a name="physical-security-reviews"></a>Fiziksel güvenlik incelemeleri
Düzenli olarak, veri merkezlerinin Azure Güvenlik gereksinimlerini doğru şekilde ele aldığından emin olmak için tesislerin fiziksel güvenlik incelemelerini sunuyoruz. Veri merkezi barındırma sağlayıcısı personeli, Azure hizmet yönetimi sağlamaz. Personel Azure sistemlerinde oturum açamaz ve Azure birlikte bulunan yere ve cages 'e fiziksel erişime sahip olamaz.

## <a name="data-bearing-devices"></a>Cihazların verileri alınıyor
Microsoft en iyi uygulama yordamlarını ve [nıst 800-88 uyumlu](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01)bir wpıng çözümünü kullanır. Silinebilen sabit sürücüler için, bunu yok eden bir yok etme işlemi kullanıyoruz ve bilgilerin kurtarılmasını mümkün değildir. Bu yok etme işlemi, tümleştirilebilir, shred, pulnize veya ıneylemsizlik olabilir. Aktiften çıkarma araçlarını varlık türüne göre belirliyoruz. Yok etme kayıtlarının kayıtları bekletiyoruz.  

## <a name="equipment-disposal"></a>Ekipman elden çıkarma
Bir sistemin yaşam süresi boyunca Microsoft operasyonel personeli, verilerinizi içeren donanımın güvenilmeyen tarafların kullanımına açık olmamasını güvence altına almak için kapsamlı veri işleme ve donanım çıkarma yordamlarını izler. Bunu destekleyen sabit sürücüler için güvenli bir silme yaklaşımı kullanıyoruz. Silinebilen sabit sürücüler için, sürücüyü yok eden ve bilgilerin kurtarmasını işleyen bir yok etme işlemi kullanırız. Bu yok etme işlemi, tümleştirilebilir, shred, pulnize veya ıneylemsizlik olabilir. Aktiften çıkarma araçlarını varlık türüne göre belirliyoruz. Yok etme kayıtlarının kayıtları bekletiyoruz. Tüm Azure hizmetleri onaylı medya depolama ve aktiften çıkarma Yönetim Hizmetleri kullanır.

## <a name="compliance"></a>Uyumluluk
ISO 27001, HIPAA, Fedrampa, SOC 1 ve SOC 2 gibi çok sayıda uluslararası ve sektöre özgü uyumluluk standardını karşılamak için Azure altyapısını tasarlıyoruz ve yönettik. Avustralya ıRAP, UK G-Cloud ve Singapur MTCS dahil olmak üzere ülkeye veya bölgeye özgü standartları da karşıladık. Ingiliz Standartları Enstitüsü 'Nün yaptığı gibi zorlu üçüncü taraf denetimleri, bu standartlar ve bu standartları denetler.

Azure 'un bağlı olduğu uyumluluk standartlarının tam listesi için bkz. [Uyumluluk teklifleri](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yardımcı olma hakkında daha fazla bilgi edinmek için bkz.:

- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)


