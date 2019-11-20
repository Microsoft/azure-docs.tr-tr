---
title: Azure ve AppSource Market teklifleri
description: Azure ve AppSource marketlerine yönelik teklifler oluşturma ve yönetme
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: f1e561e84daed6c24ca0d9b7832af3d975082e25
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818768"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure ve AppSource Market teklifleri

Bu bölümün ilk bölümünde, Azure ve AppSource marketlerine yönelik teklifler oluşturmak ve bunları yönetmek için kullanılan genel işlemler tanıtılmaktadır.  Bu bölümde, belirli teklif türlerini yönetmek için anlamanız gereken arka plan ve tüm teklif türlerinde ortak olan teknik bilgiler yer almaktadır.  Bu bölümün çoğu, belirli teklif türlerini oluşturma ve yönetme hakkında ayrıntılı yönergeler içerir.  

Aşağıdaki videoda, Azure Market veya AppSource 'ta bulunan çeşitli yetenekler ve farklı teklif türleri tanıtılmaktadır.  Ayrıca, bu Pazar konumlarında bir uygulama veya hizmet yayımlamanın önemli teknik ve iş yönlerini de ele alır.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Azure Market ve AppSource için uygulama ve hizmet oluşturma-derleme 2018**

Bu pazar yerleri hakkında daha fazla bilgi için bkz. [Azure Marketi ve AppSource Yayımlama Kılavuzu](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Ortak teklif işlemleri

Yeni bir teklif oluşturma işlemi, örneğin bir [Azure Uygulama teklifi](./azure-applications/cpp-azure-app-offer.md) ve [danışmanlık hizmeti teklifi](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)arasında teklif türlerine göre büyük ölçüde farklılık gösterir.  Buna karşılık, [bulut iş ortağı portalı](https://cloudpartner.azure.com) bir teklif üzerinde gerçekleştirdiğiniz diğer işlemlerin çoğu teklif türleri arasında oldukça standartlaştırılmış.  Yayımla, durumu görüntüle, Güncelleştir ve Sil gibi yaygın işlemler, [teklifleri yönetme](./manage-offers/cpp-manage-offers.md) bölümünde ele alınmıştır.


## <a name="test-drive"></a>Test Sürüşü

*Test sürüşü* , müşterilerin her bir teklif için "tanıtım için deneme" gösterimi seçeneğini sunan bir market özelliğidir.  Test sürücüsü özelliği, teklif türlerinin aşağıdaki alt kümesiyle sınırlıdır: [Azure uygulamaları](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 iş merkezi](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [finans ve işlemler için Dynamics 365](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS uygulamalar](./saas-app/cpp-saas-offer.md)ve [sanal makineler](./virtual-machine/cpp-virtual-machine-offer.md).  Bu özellik, yayımcının teklifi için özelleştirilmiş bir test sürücü şablonu oluşturmasını gerektirir.  Daha fazla bilgi için, bkz. [test sürücüsü](./test-drive/what-is-test-drive.md).

Test sürüşü test [sürücüsü filtresi](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)uygulayarak test sürücüsü gösterimine sahip mevcut Market tekliflerine gidebilirsiniz. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketi ve AppSource teklif türleri

Aşağıdaki tabloda [bulut iş ortağı portalı](https://cloudpartner.azure.com)tarafından desteklenen geçerli teklif türleri listelenmektedir.  Her teklif türü için, teklifin listelenbileceği Market (ler) i ve teklif çözümü teknolojisinin genel bir açıklamasını listeler.

|                Teklif Türü                |  Market  |   Açıklama                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure uygulaması](./azure-applications/cpp-azure-app-offer.md) | Azure | Çözüm, bir Azure Resource Manager şablonuyla dağıtılan bir veya daha fazla sanal makineden (VM), isteğe bağlı özel Azure kodundan oluşur.  Dağıtım, bir çözüm şablonu aracılığıyla müşteri tarafından ya da yayımcı tarafından yönetilebilir. Bu tür, sağlanan sanal makine teklif türünden daha fazla esneklik sağlamak için kullanılır.  |
| [Danışmanlık hizmeti](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | İs | Microsoft-Qualified Consultants, etki alanına özgü hizmetlerini Azure Market veya AppSource 'ta listeleyebilir.  Uzmanlığı müşterilerin sorunlarını değerlendirmesine ve iş hedeflerini karşılamak üzere doğru çözümler oluşturup dağıtmalarına yardımcı olur.  |
| [Kapsayıcı](./containers/cpp-containers-offer.md)  | Azure | Çözüm, bir Kubernetes tabanlı hizmet ya da Azure Container Instances olarak sağlanan bir Docker kapsayıcı görüntüsüdür. |
| [Dynamics 365 Iş Merkezi](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Bu kurumsal kaynak planlama (ERP) ve iş yönetim sistemini genişleten bir paket. |
| [Müşteri katılımı için Dynamics 365](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Bu müşteri kaynak yönetimi (CRM) sistemini Sales, Service, Project Service ve Field Service modülleri aracılığıyla genişleten bir paket  |
| [Finans ve Işlemler için Dynamics 365](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Gelişmiş finans, işlemler, üretim ve tedarik zinciri yönetimini destekleyen bu kurumsal kaynak planlama (ERP) hizmetini genişleten bir paket |
| [IoT Edge modülü](./iot-edge-module/cpp-offer-process-parts.md) | Azure | IoT Edge cihazda çalışan Docker ile uyumlu bir kapsayıcı.  Özel kod, diğer Azure hizmetleri ve üçüncü taraf hizmetler birleşimini kullanan küçük hesaplama modüllerinden oluşur. |
| [Power BI uygulaması](./power-bi/cpp-power-bi-offer.md) | AppSource | Veri kümeleri, raporlar ve panolar gibi Özelleştirilebilir Power BI içeriği paketleyen Power BI uygulaması |
| [SaaS uygulaması](./saas-app/cpp-saas-offer.md) | Azure | Çözüm, yayımcı tarafından yönetilen, kullanıcıların Azure Active Directory kullanan özelleştirilmiş bir arabirim aracılığıyla oturum açmasını sağlayan bir hizmet olarak yazılım yazılımıdır. |
| [Sanal makine](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Çözüm, müşterinin aboneliğine dağıtılan tek bir sanal makine içinde bulunur.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Daha fazla bilgi için bkz. [teklif türüne göre kılavuz yayımlama](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Sonraki adımlar

Market tekliflerinde gerçekleştirebileceğiniz genel işlemleri ve bu kişilerin [tekliflerini yönetme](./manage-offers/cpp-manage-offers.md)makalesindeki ortak teknik özniteliklerini ve varlıklarını öğreneceksiniz.
