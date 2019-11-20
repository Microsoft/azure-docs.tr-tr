---
title: Azure Kullanılabilirlik Alanları nedir? | Microsoft Belgeleri
description: Azure 'da yüksek oranda kullanılabilir ve dayanıklı uygulamalar oluşturmak için, kaynaklarınızı çalıştırmak için kullanabileceğiniz fiziksel olarak ayrı konumlar sağlayın Kullanılabilirlik Alanları.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1733493c88129f465782af8ac5e6c4914fd213ca
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73957940"
---
# <a name="what-are-availability-zones-in-azure"></a>Azure 'da Kullanılabilirlik Alanları nedir?
Kullanılabilirlik Alanları, uygulamalarınızı ve verilerinizi veri merkezi hatalarından koruyan yüksek kullanılabilirliğe sahip bir tekliftir. Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır. Bir bölgedeki Kullanılabilirlik Alanları fiziksel ayrımı, uygulamaları ve verileri veri merkezi hatalarından korur. Bölgesel olarak yedekli hizmetler, uygulamalarınızı ve verilerinizi Kullanılabilirlik Alanları arasında çoğaltarak hata noktalarından koruyun. Azure, Kullanılabilirlik Alanları sayesinde sektörün en iyi% 99,99 VM çalışma süresi SLA 'sını sunmaktadır. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

Bir Azure bölgesindeki kullanılabilirlik bölgesi bir hata etki alanının ve bir güncelleştirme etki alanının birleşimidir. Örneğin, bir Azure bölgesindeki üç bölgede üç veya daha fazla VM oluşturursanız, VM 'niz üç hata etki alanına ve üç güncelleştirme etki alanına etkili bir şekilde dağıtılır. Azure platformu, farklı bölgelerdeki VM 'Lerin aynı anda güncelleştirildiğinden emin olmak için bu dağıtımı güncelleştirme etki alanları genelinde tanır.

Bir bölgedeki işlem, depolama, ağ ve veri kaynaklarınızı birlikte bularak ve diğer bölgelerde çoğaltarak uygulama mimarinizde yüksek kullanılabilirlik oluşturun. Kullanılabilirlik Alanları destekleyen Azure hizmetleri iki kategoride yer almalıdır:

- **Zonal Hizmetleri** : kaynağı belirli bir bölgeye (örneğin, sanal makineler, yönetilen diskler, standart IP adresleri) sabitleyebilir veya
- Bölgesel olarak **yedekli hizmetler** – platform bölgeler arasında otomatik olarak çoğaltılır (örneğin, bölgesel olarak yedekli depolama, SQL veritabanı).

Azure 'da kapsamlı iş sürekliliği elde etmek için Azure bölge çiftleriyle Kullanılabilirlik Alanları birleşimini kullanarak uygulama mimarinizi geliştirin. Uygulamalarınızı ve verilerinizi Azure bölgesindeki Kullanılabilirlik Alanları kullanarak yüksek kullanılabilirlik ve olağanüstü durum kurtarma koruması için Azure bölgelerinde zaman uyumsuz olarak çoğaltma yapabilirsiniz.
 
![bölgede aşağı doğru bir bölgenin kavramsal görünümü](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Kullanılabilirlik alanı tanımlayıcıları (yukarıdaki resimde 1, 2 ve 3 sayıları), her aboneliğin bağımsız olarak gerçek fiziksel bölgelere mantıksal olarak eşlenir. Bu, belirli bir abonelikteki kullanılabilirlik Bölge 1 farklı bir abonelikte kullanılabilirlik Bölge 1 farklı bir fiziksel bölgeye başvurabileceği anlamına gelir. Sonuç olarak, sanal makine yerleştirmesi için farklı aboneliklerde kullanılabilirlik bölgesi kimliklerinin olmaması önerilir.

## <a name="services-support-by-region"></a>Bölgeye göre hizmet desteği

Kullanılabilirlik Alanları destekleyen Azure hizmetleri ve bölgelerinin birleşimleri şunlardır:


|                                 |Kuzey ve Güney Amerika |              |           |           | Avrupa |              |          |              | Asya Pasifik |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |Orta ABD|Doğu ABD|Doğu ABD 2|Batı ABD 2|Fransa Orta|Kuzey Avrupa|Birleşik Krallık Güney|Batı Avrupa|Japonya Doğu|Güneydoğu Asya|
| **İşlem**                         |            |              |           |           |                |              |          |             |            |                |
| Linux Sanal Makineleri          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Windows Sanal Makineleri        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Sanal Makine Ölçek Kümeleri      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Depolama**   |            |              |           |           |                |              |          |             |            |                |
| Yönetilen Diskler                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Bölgesel olarak yedekli depolama          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Ağ**                     |            |              |           |           |                |              |          |             |            |                |
| Standart IP adresi        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standart Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute ağ geçidi   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Application Gateway (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Güvenlik Duvarı           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Veritabanları**                     |            |              |           |           |                |              |          |             |            |                |
| Azure Veri Gezgini                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL Veritabanı                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;Önizle      | &#10003;       |
| Redis için Azure Önbelleği           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analiz**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Tümleştirme**                     |            |              |           |           |                |              |          |             |            |                |
| Service Bus (yalnızca Premium katman) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Kimlik**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Hizmet dayanıklılığı
Tüm Azure Yönetim Hizmetleri bölge düzeyindeki hatalardan dayanıklı olacak şekilde tasarlanmıştır. Başarısızlık kapsamında, bir bölgedeki bir veya daha fazla kullanılabilirlik bölgesi hatası, tüm bölge arızasına kıyasla daha küçük bir hata Radius içermelidir. Azure, bölgedeki veya başka bir Azure bölgesindeki yönetim hizmetlerinden oluşan bölge düzeyindeki bir hatadan kurtuya ait olabilir. Azure, bir bölgedeki Kullanılabilirlik Alanları arasında dağıtılan müşteri kaynaklarını etkileyen tüm sorunları engellemek için bir bölge içinde tek seferde kritik bakım gerçekleştirir.

## <a name="pricing"></a>Fiyatlandırma
Bir kullanılabilirlik bölgesinde dağıtılan sanal makineler için ek ücret yoktur. % 99,99 VM çalışma süresi SLA 'Sı, iki veya daha fazla VM bir Azure bölgesi içinde iki veya daha fazla Kullanılabilirlik Alanları dağıtıldığında sunulur. Ek kullanılabilirlik alanı VM 'leri-VM veri aktarımı ücretleri olacaktır. Daha fazla bilgi için [bant genişliği fiyatlandırma](https://azure.microsoft.com/pricing/details/bandwidth/) sayfasını gözden geçirin.


## <a name="get-started-with-availability-zones"></a>Kullanılabilirlik Alanları kullanmaya başlayın
- [Sanal makine oluşturun](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell kullanarak yönetilen disk ekleme](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Bölge yedekli sanal makine ölçek kümesi oluşturma](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bölgesel olarak yedekli bir ön uç ile bir Standart Load Balancer kullanarak VM 'lerde Yük Dengeleme](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Bir bölge içindeki VM 'leri, bölgesel ön ucu ile standart Load Balancer kullanarak yük dengeleme](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Alanlar arası yedekli depolama](../storage/common/storage-redundancy-zrs.md)
- [SQL Veritabanı](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Event Hubs coğrafi olağanüstü durum kurtarma](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus coğrafi olağanüstü durum kurtarma](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Alanlar arası yedekli sanal ağ geçidi oluşturma](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB için bölge yedekli bölgesi ekleme](../cosmos-db/high-availability.md#availability-zone-support)
- [Redsıs için Azure önbelleği Başlarken Kullanılabilirlik Alanları](https://aka.ms/redis/az/getstarted)
- [Azure Active Directory Domain Services örneği oluşturma](../active-directory-domain-services/tutorial-create-instance.md)

## <a name="next-steps"></a>Sonraki adımlar
- [Hızlı Başlangıç şablonları](https://aka.ms/azqs)
