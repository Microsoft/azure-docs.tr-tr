---
title: Bakım Penceresi
description: Azure SQL veritabanı ve yönetilen örnek Bakımı penceresinin nasıl yapılandırılabileceğini anlayın.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/23/2021
ms.openlocfilehash: 9d7ab0498673ad7006087b66575eea9371b96d11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565915"
---
# <a name="maintenance-window-preview"></a>Bakım penceresi (Önizleme)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bakım penceresi özelliği, [Azure SQL veritabanı](sql-database-paas-overview.md) ve [Azure SQL yönetilen örnek](../managed-instance/sql-managed-instance-paas-overview.md) kaynakları için bakım zamanlamasını yapılandırmanıza olanak sağlar ve bu sayede iş yükünüz için öngörülü bakım olaylarını öngörülebilir ve daha az kesintiye uğratan hale getirirsiniz. 

> [!Note]
> Bakım penceresi özelliği, kısa bağlantı kesintileri oluşmasına neden olabilecek donanım arızaları gibi planlanmamış olaylardan korunmaz.

## <a name="overview"></a>Genel Bakış

Azure düzenli olarak SQL veritabanı ve SQL yönetilen örnek kaynakları için [Planlı bakım](planned-maintenance.md) gerçekleştirir. Azure SQL bakım olayı sırasında veritabanları tamamen mevcuttur, ancak [SQL veritabanı](https://azure.microsoft.com/support/legal/sla/sql-database) ve [SQL yönetilen örneği](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)Için ilgili kullanılabilirlik SLA 'ları içindeki kısa yeniden yapılandırmalara tabi olabilir.

Bakım penceresi, veritabanı veya örnek yeniden yapılandırmalarına dayanıklı olmayan üretim iş yükleri için tasarlanmıştır ve planlı bakım olaylarının neden olduğu kısa bağlantı kesintilerini artışlarını devralarak. Tercih ettiğiniz bir bakım penceresini seçerek, en yüksek iş saatlerinizin dışında gerçekleşmeyeceğinden planlı bakımın etkisini en aza indirirsiniz. Esnek iş yükleri ve üretim dışı iş yükleri, Azure SQL 'in varsayılan bakım ilkesini kullanabilir.

Bakım penceresi, oluşturma veya mevcut Azure SQL kaynakları için yapılandırılabilir. Azure portal, PowerShell, CLı veya Azure API 'SI kullanılarak yapılandırılabilir.

> [!Important]
> Bakım penceresini yapılandırmak, Azure SQL kaynağının hizmet katmanını değiştirmeye benzer şekilde, uzun süren bir zaman uyumsuz işlemdir. İşlem sırasında, işlemin sonunda gerçekleşen kısa bir yeniden yapılandırma haricinde ve genellikle uzun süreli işlem durumunda bile 8 saniyeye kadar süren kaynak kullanılabilir. Yeniden yapılandırma işleminin etkisini en aza indirmek için işlemi yoğun saatlerin dışında gerçekleştirmeniz gerekir.

### <a name="gain-more-predictability-with-maintenance-window"></a>Bakım penceresiyle daha öngörülebilirlik kazanın

Varsayılan olarak, Azure SQL bakım ilkesi, tipik yoğun iş saatlerinde oluşan kesintileri önlemek için **her gün saat 8:00-5 saat boyunca yerel saate** kadar olan güncelleştirmeleri engeller. Yerel saat, kaynağı barındıran [Azure bölgesinin](https://azure.microsoft.com/global-infrastructure/geographies/) konumu tarafından belirlenir ve yerel saat dilimi tanımına uygun olarak yaz saati kaydetme süresini gözlemleyebilirsiniz. 

İki ek bakım penceresi yuvası arasından seçim yaparak, bakım güncelleştirmelerini Azure SQL kaynaklarınıza uygun bir zamana göre daha fazla ayarlayabilirsiniz:
 
* Hafta içi pencere, 10:00-00 yerel saat Pazartesi-Perşembe
* Hafta sonu penceresi, 10:00-00 yerel saat Cuma-Pazar

Bakım penceresi seçimi yapıldıktan ve hizmet yapılandırması tamamlandıktan sonra, planlanan bakım yalnızca seçtiğiniz pencere sırasında gerçekleşir.   

> [!Important]
> Çok nadir koşullarda, kritik güvenlik düzeltme eki uygulama gibi önemli bir etkiye neden olabileceğinden, yapılandırılan bakım penceresi geçici olarak geçersiz kılınmış olabilir. 

### <a name="cost-and-eligibility"></a>Maliyet ve uygunluk

Bakım penceresini yapılandırmak ve kullanmak uygun olan tüm [teklif türleri](https://azure.microsoft.com/support/legal/offer-details/)için ücretsizdir: Kullandıkça öde, bulut çözümü sağlayıcısı (CSP), Microsoft kurumsal anlaşma veya Microsoft Müşteri Sözleşmesi.

> [!Note]
> Azure teklifi, sahip olduğunuz Azure aboneliğinin türüdür. Örneğin, [Kullandıkça Öde tarifesine](https://azure.microsoft.com/offers/ms-azr-0003p/)sahip bir abonelik, [Open ile Azure](https://azure.microsoft.com/offers/ms-azr-0111p/)ve [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) tüm Azure tekliflerdir. Her teklif veya planın farklı hüküm ve avantajları vardır. Teklifiniz veya planınız, aboneliğin Genel Bakış sayfasında gösterilir. Aboneliğinizi farklı bir teklifle değiştirme hakkında daha fazla bilgi için bkz. [Azure aboneliğinizi farklı bir teklifle değiştirme](../../cost-management-billing/manage/switch-azure-offer.md).

## <a name="advance-notifications"></a>Bildirimleri ilerlet

Bakım bildirimleri, bakım sırasında ve bakım tamamlandığında, Azure SQL veritabanınızın 24 saat ön planlı bakım olayları hakkında sizi uyaracak şekilde yapılandırılabilir. Daha fazla bilgi için bkz. [ön bildirimler](advance-notifications.md).

## <a name="availability"></a>Kullanılabilirlik

### <a name="supported-service-level-objectives"></a>Desteklenen hizmet düzeyi amaçları

Varsayılan dışında bir bakım penceresi seçmek, **aşağıdakiler hariç** tüm SLOS üzerinde kullanılabilir:
* Hiper Ölçek 
* Örnek havuzları
* Eski 4. nesil sanal çekirdek
* Temel, S0 ve S1 
* DC, Fsv2, d serisi

### <a name="azure-region-support"></a>Azure bölge desteği

Varsayılan dışında bir bakım penceresinin seçilmesi şu bölgelerde kullanılabilir:

- Doğu Avustralya
- Avustralya Güneydoğu
- Güney Brezilya
- Orta Kanada
- Central US
- Doğu ABD
- Doğu ABD 2
- Doğu Asya
- Doğu Japonya
- Kuzey Orta ABD
- Kuzey Avrupa
- Orta Güney ABD
- Güneydoğu Asya
- Güney Birleşik Krallık
- West Europe
- Batı ABD
- Batı ABD 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL veritabanı için ağ geçidi Bakımı

Bakım pencerelerinin en büyük avantajlarından yararlanabilmek için, istemci uygulamalarınızın yeniden yönlendirme bağlantı ilkesini kullandığınızdan emin olun. Yeniden yönlendirme, istemcilerin veritabanını barındıran düğüme doğrudan bağlantı kurmasını, azaltılmış gecikme süresine ve geliştirilmiş aktarım hızına kadar bir bağlantı kurmasını sağlar.  

* Azure SQL veritabanı 'nda, proxy bağlantı ilkesini kullanan tüm bağlantılar, seçilen bakım penceresi ve bir ağ geçidi düğümü bakım penceresinden etkilenebilir. Ancak, önerilen yeniden yönlendirme bağlantı ilkesini kullanan istemci bağlantıları, ağ geçidi düğümü Bakımı yeniden yapılandırması tarafından etkilenmez. 

* Azure SQL yönetilen örneği 'nde, ağ geçidi düğümleri [sanal küme içinde](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) barındırılır ve yönetilen örnekle aynı bakım penceresine sahiptir, ancak bakım olayı sırasında kesintiler sayısını en aza indirmek için yeniden yönlendirme bağlantı ilkesinin kullanılması yine de önerilir.

Azure SQL veritabanı 'nda istemci bağlantı ilkesi hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı bağlantı ilkesi](../database/connectivity-architecture.md#connection-policy). 

Azure SQL yönetilen örneği 'nde istemci bağlantı ilkesi hakkında daha fazla bilgi için bkz. [Azure SQL yönetilen örnek bağlantı türleri](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considerations-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için önemli noktalar

Azure SQL yönetilen örneği, müşterinin sanal ağ alt ağı içinde çalışan adanmış bir yalıtılmış sanal makine kümesinde barındırılan hizmet bileşenlerinden oluşur. Bu sanal makineler, birden çok yönetilen örneği barındırasağlayan [sanal kümeleri](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture) oluşturur. Bir alt ağın örneklerinde yapılandırılan bakım penceresi, alt ağdaki sanal kümelerin sayısını ve Sanal kümeler arasında örneklerin dağıtımını etkileyebilir. Bu, birkaç etkilerin bir dikkate alınması gerekebilir.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>Bakım penceresi yapılandırması uzun süren bir işlemdir 
Bir sanal kümede barındırılan tüm örnekler bakım penceresini paylaşır. Varsayılan olarak, tüm yönetilen örnekler varsayılan bakım penceresi ile sanal kümede barındırılır. Yönetilen örnek için oluşturma sırasında başka bir bakım penceresi belirtme veya daha sonra, sanal kümeye karşılık gelen bakım penceresiyle yerleştirilmesi gerektiği anlamına gelir. Alt ağda böyle bir sanal küme yoksa, örneğe uyum sağlamak için önce yeni bir tane oluşturulması gerekir. Var olan sanal kümede ek örnek konamak, küme yeniden boyutlandırma gerektirebilir. Her iki işlem de yönetilen bir örnek için bakım penceresi yapılandırma süresine katkıda bulunur.
Yönetilen örnekteki bakım penceresini yapılandırmanın beklenen süresi, [örnek yönetimi işlemlerinin tahmini süresi](../managed-instance/management-operations-overview.md#duration)kullanılarak hesaplanabilir.

> [!Important]
> Bakım işleminin sonunda kısa bir yeniden yapılandırma gerçekleşir ve genellikle uzun süreli işlem durumunda bile 8 saniyeye kadar sürer. Yeniden yapılandırma etkisini en aza indirmek için işlemi yoğun saatlerin dışında zamanlamanız gerekir.

### <a name="ip-address-space-requirements"></a>IP adresi alanı gereksinimleri
Alt ağdaki her yeni sanal küme, [sanal küme IP adresi ayırmaya](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size)göre ek IP adresleri gerektirir. Mevcut yönetilen örnek için bakım penceresini değiştirmek, karşılık gelen hizmet katmanı için sanal çekirdekler senaryosunu ölçeklendirirken [geçici ek IP kapasitesi](../managed-instance/vnet-subnet-determine-size.md#address-requirements-for-update-scenarios) de gerektirir.

### <a name="ip-address-change"></a>IP adresi değişikliği
Bakım penceresini yapılandırmak ve değiştirmek, alt ağın IP adresi aralığı içinde örneğin IP adresi değişikliğine neden olur.

> [!Important]
>  NSG ve Güvenlik Duvarı kurallarının, IP adresi değişikliğinden sonra veri trafiğini engellemediğinden emin olun. 

## <a name="next-steps"></a>Sonraki adımlar

* [Bildirimleri ilerlet](advance-notifications.md)
* [Bakım penceresini yapılandırma](maintenance-window-configure.md)

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Bakım penceresi hakkında SSS](maintenance-window-faq.yml)
* [Azure SQL Veritabanı](sql-database-paas-overview.md) 
* [SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde Azure bakım olaylarını planlayın](planned-maintenance.md)