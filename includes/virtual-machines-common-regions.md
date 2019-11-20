---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4b2dcf8f156f231041d7636d103eea59816ec65f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008433"
---
Sanal makinelerinizin (VM’ler) Azure’da nasıl ve hangi konumda çalıştığının yanı sıra performans, kullanılabilirlik ve yedekliliği artırmak için kullanabileceğiniz seçeneklerin de anlaşılması önemlidir. Bu makalede, Azure’un kullanılabilirlik ve yedeklilik özelliklerine genel bakış sunulmaktadır.


## <a name="what-are-azure-regions"></a>Azure bölgeleri nelerdir?
Azure, dünyanın dört bir yanındaki birden fazla veri merkezinde çalışmaktadır. Bu veri merkezleri, coğrafi bölgeler halinde gruplandırılarak uygulamalarınızı oluşturacağınız yeri seçme esnekliği tanır. 

Azure kaynaklarını ' Batı ABD ', ' Kuzey Avrupa ' veya ' Güneydoğu Asya ' gibi tanımlı coğrafi bölgelerde oluşturursunuz. [Bölgeler ve konumlarının listesini](https://azure.microsoft.com/regions/) gözden geçirebilirsiniz. Her bölge içinde, yedeklilik ve kullanılabilirlik sağlayan birden fazla veri merkezi mevcuttur. Bu yaklaşım, kullanıcılarınıza en yakın VM 'Ler oluşturmak ve herhangi bir yasal, uyumluluk veya vergi amacını karşılamak üzere uygulama tasarlarken esneklik sağlar.

## <a name="special-azure-regions"></a>Özel Azure bölgeleri
Azure 'da, uyumluluk veya yasal amaçlar için uygulamalarınızı oluştururken kullanmak isteyebileceğiniz bazı özel bölgeler vardır. Bu özel bölgeleri şunlardır:

* **US Gov Virginia** ve **US Gov Iowa**
  * ABD kamu kuruluşları ve iş ortaklarına yönelik olarak ABD’de bulunan ve denetlenen kişilerce çalıştırılan fiziksel ve mantıksal ağdan yalıtılmış Azure örneği. [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) ve [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA) gibi ek uyumluluk sertifikaları içerir. [Azure Kamu](https://azure.microsoft.com/features/gov/) hakkında daha fazla bilgi alın.
* **Çin Doğu** ve **Çin Kuzey**
  * Bu bölgeler, Microsoft ile 21Vianet arasında, Microsoft’un veri merkezlerini doğrudan yönetmediği benzersiz ortaklık ile kullanıma sunulmaktadır. [Azure Çin 21Vianet](http://www.windowsazure.cn/)hakkında daha fazla bilgi için bkz.
* **Almanya Orta** ve **Almanya Kuzeydoğu**
  * Bu bölgeler, müşteri verilerinin, Almanya veri emaneti olarak davranan bir Deutsche Telekok şirketi olan T-Systems denetimi kapsamında kaldığı bir veri güvenliği modeli aracılığıyla kullanılabilir.

## <a name="region-pairs"></a>Bölge çiftleri
Her Azure bölgesi aynı coğrafyadaki (ABD, Avrupa veya Asya) başka bir bölgeyle eşleştirilir. Bu yaklaşım her iki bölgeyi de aynı anda etkileyen bir doğal felaket, toplumsal karmaşa, güç kesintisi veya fiziksel ağ kesintisi olasılığını azaltması gereken bir coğrafyada VM depolama gibi kaynak çoğaltma işlemlerine olanak tanır. Bölge çiftlerinin diğer avantajları şunlardır:

* Daha geniş bir Azure kesintisi durumunda, uygulamalar için geri yükleme süresini azaltmak üzere her çift içinden bir bölgeye öncelik verilir. 
* Kapalı kalma süresini ve uygulama kesintisi riskini azaltmak amacıyla, planlı Azure güncelleştirmeleri, bölge çiftlerine tek tek uygulanır.
* Veriler, vergi ve yasa uygulama yetkisi bakımından çiftiyle aynı coğrafyada (Brezilya Güney hariç) bulunmaya devam eder.

Bölge çiftlerinin örnekleri şunlardır:

| Birincil | İkincil |
|:--- |:--- |
| Batı ABD |Doğu ABD |
| Kuzey Avrupa |Batı Avrupa |
| Güneydoğu Asya |Doğu Asya |

[Bölgesel çiftlerin tam listesini burada](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions) görebilirsiniz.

## <a name="feature-availability"></a>Özellik kullanılabilirliği
Belirli VM boyutları ya da depolama türleri gibi bazı hizmetler veya VM özellikleri yalnızca belirli bölgelerde kullanılabilir. Ayrıca, belirli bir bölge seçmenizi gerektirmeyen [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) veya [Azure DNS](../articles/dns/dns-overview.md) gibi bazı genel Azure hizmetleri de vardır. Uygulama ortamınızı tasarlamanıza yardımcı olmak üzere [her bölgedeki Azure hizmetleri kullanılabilirliğini](https://azure.microsoft.com/regions/#services) denetleyebilirsiniz. Ayrıca [, her bölgedeki desteklenen VM boyutlarını ve kısıtlamalarını programlı](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md)bir şekilde sorgulayabilirsiniz.

## <a name="storage-availability"></a>Depolama kullanılabilirliği
Kullanılabilir çoğaltma seçenekleri düşünüldüğünde Azure bölge ve coğrafyalarının anlaşılması önemlidir. Depolama türüne bağlı olarak farklı çoğaltma seçenekleriniz vardır.

**Azure Yönetilen Diskler**
* Yerel olarak yedekli depolama (LRS)
  * Depolama hesabınızı oluşturduğunuz bölge içinde verilerinizi üç kez çoğaltır.

**Depolama hesabı temelli diskler**
* Yerel olarak yedekli depolama (LRS)
  * Depolama hesabınızı oluşturduğunuz bölge içinde verilerinizi üç kez çoğaltır.
* Alanlar arası yedekli depolama (ZRS)
  * Tek bir bölge ya da iki bölgedeki iki veya üç tesiste verilerinizi üç kez çoğaltır.
* Coğrafi olarak yedekli depolama (GRS)
  * Verilerinizi birincil bölgeden yüzlerce kilometre uzaktaki bir ikincil bölgeye çoğaltır.
* Okuma erişimli coğrafi olarak yedekli depolama (RA-GRS)
  * GRS ile olduğu gibi verilerinizi ikincil bölgeye çoğaltır, ancak daha sonra ikincil konumdaki verilere salt okunur erişim sağlar.

Aşağıdaki tabloda, depolama çoğaltma türleri arasındaki farkları hızlı bir genel bakış sunulmaktadır:

| Çoğaltma stratejisi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Veriler birden çok tesis arasında çoğaltılır. |Hayır |Yes |Yes |Yes |
| Veriler ikincil konumdan ve birincil konumdan okunabilir. |Hayır |Hayır |Hayır |Yes |
| Ayrı düğümlerde tutulan veri kopyası sayısı. |3 |3 |6 |6 |

[Azure Depolama çoğaltma seçenekleri hakkında buradan](../articles/storage/common/storage-redundancy.md) daha fazla bilgi alabilirsiniz. Yönetilen diskler hakkında daha fazla bilgi için bkz. [Azure Yönetilen Disklere genel bakış](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Depolama maliyetleri
Fiyatlar seçtiğiniz depolama türüne ve kullanılabilirliğe bağlı olarak değişir.

**Azure Yönetilen Diskler**
* Premium yönetilen diskler, katı hal sürücüleri (SSD 'Ler) tarafından desteklenir ve standart yönetilen diskler düzenli olarak dönen diskler tarafından desteklenir. Hem Premium hem de Standart Yönetilen Diskler, diskin sağlanan kapasitesine göre ücretlendirilir.

**Yönetilmeyen diskler**
* Premium Depolama katı hal sürücüleri (SSD 'Ler) tarafından desteklenir ve diskin kapasitesine göre ücretlendirilir.
* Standart depolama, normal dönen disklerle desteklenir ve kullanımdaki kapasiteye ve istenen depolama kullanılabilirliğine göre ücretlendirilir.
  * RA-GRS için, verileri başka bir Azure bölgesine çoğaltmak için gereken bant genişliğine yönelik ek bir Coğrafi Çoğaltma Veri Aktarımı ücreti vardır.

Farklı depolama türleri ve kullanılabilirlik seçenekleri hakkında fiyatlandırma bilgileri için bkz. [Azure Depolama Fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

