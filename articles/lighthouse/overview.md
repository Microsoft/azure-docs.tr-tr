---
title: Azure Lighthouse nedir?
description: Azure Athouse, hizmet sağlayıcılarının daha yüksek otomasyon ve verimlilik özelliklerine sahip müşterileri için yönetilen hizmetler sunmasına olanak tanır.
ms.date: 12/16/2020
ms.topic: overview
ms.openlocfilehash: b9215f42e3ad3b7517d14fdaee710a31680ce453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97617162"
---
# <a name="what-is-azure-lighthouse"></a>Azure Lighthouse nedir?

Azure Lighthouse, kiracılar arası ve çok kiracılı yönetime olanak tanır. Bu sayede kaynaklar ve kiracılar daha yüksek otomasyon, ölçeklenebilirlik ve gelişmiş idare elde edilir.

Azure açık, hizmet sağlayıcıları, Azure platformunda yerleşik kapsamlı ve güçlü yönetim araçlarını kullanarak yönetilen Hizmetleri sunabilir. Müşteriler, kiracılarına kimlerin erişebileceklerini, erişebilecek kaynakları ve hangi eylemlerin alınacağını denetler. Bu teklif, birden fazla kiracıda kaynakları yöneten [Kurumsal BT kuruluşları](concepts/enterprise.md) için de avantaj sağlayabilir.

![Azure açık kullanım için genel bakış Diyagramı](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Avantajlar

Azure Kathouse, hizmet sağlayıcılarının yönetilen Hizmetleri verimli bir şekilde oluşturmasına ve sunmaya yardımcı olur. Faydaları şunlardır:

- **Ölçekteki yönetim**: müşteri kaynaklarını yönetmeye yönelik müşteri katılımı ve yaşam döngüsü işlemleri daha kolay ve ölçeklenebilir. Mevcut API 'Ler, yönetim araçları ve iş akışları, bulundukları bölgeler ne olursa olsun, Azure dışında barındırılan makineler dahil olmak üzere Temsilcili kaynaklarla birlikte kullanılabilir.
- **Müşteriler Için daha fazla görünürlük ve denetim**: müşteriler, yönetim için temsilcdikleri kapsamlar ve izin verilen izinler üzerinde tam denetime sahiptir. Hizmet sağlayıcı eylemlerini denetleyebilir ve isterseniz erişimi tamamen kaldırabilir.
- **Kapsamlı ve Birleşik platform araçları**: alet oluşturma DENEYIMIMIZ, EA, CSP ve kullandıkça öde gibi birden fazla lisanslama modeli de dahil olmak üzere temel hizmet sağlayıcısı senaryolarına yöneliktir. Azure ışıklı kullanımı, mevcut araçlar ve API 'Ler, lisanslama modelleri, [Azure yönetilen uygulamalar](concepts/managed-applications.md)ve [bulut çözümü sağlayıcısı programı (CSP)](/partner-center/csp-overview)gibi iş ortağı programlarıyla birlikte kullanılabilir. Azure açık Thouse 'ı mevcut iş akışlarınızla ve uygulamalarınızda tümleştirebilir ve [iş ortağı kimliğinizi bağlayarak](./how-to/partner-earned-credit.md)müşteri görevlendirmelerinizi takip edebilirsiniz.

Azure kaynaklarını yönetmek için Azure açık Thouse kullanımıyla ilişkili ek maliyet yoktur. Tüm Azure müşterileri veya iş ortakları, Azure Use 'ı kullanabilir.

## <a name="capabilities"></a>Özellikler

Azure ışıklı kullanımı, katılım ve yönetimin kolaylaştırılmasına yardımcı olmak için birden çok yol içerir:

- **Azure Temsilcili kaynak yönetimi**: içerik ve denetim düzlemleri arasında geçiş yapmak zorunda kalmadan [müşterilerinizin Azure kaynaklarını kendi kiracınızın içinden güvenli bir şekilde yönetin](concepts/azure-delegated-resource-management.md). Müşteri abonelikleri ve kaynak grupları, yönetim kiracısındaki belirtilen kullanıcılar ve roller için, gerektiğinde erişimi kaldırabilme özelliği ile atanabilir.
- **Yeni Azure Portal deneyimler**: Azure Portal içindeki [ **müşteriler** sayfasında](how-to/view-manage-customers.md) , platformlar arası bilgileri görüntüleyin. Karşılık gelen bir [ **hizmet** sağlayıcı sayfası](how-to/view-manage-service-providers.md) , müşterilerin hizmet sağlayıcı erişimini görüntülemesine ve yönetmesine olanak tanır.
- **Azure Resource Manager şablonları**: [temsilci atanmış müşteri kaynakları](how-to/onboard-customer.md) eklemek ve [platformlar arası yönetim görevlerini gerçekleştirmek](samples/index.md)için ARM şablonlarını kullanın.
- **Azure Marketi 'Nde yönetilen hizmet teklifleri**: [hizmetlerinizi müşterilere](concepts/managed-services-offers.md) özel veya genel tekliflerle sunun ve bunları otomatik olarak Azure açık thouse 'a ekleyin.

> [!TIP]
> Benzer bir teklif olan [Microsoft 365 ışıklı](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181)bir sunum, BT iş ortaklarının Microsoft 365 müşterilerinin ölçeklendirilmesine ekleme, izleme ve yönetme konusunda yardımcı olur. Açık Microsoft 365 kullanımı Şu anda özel önizlemededir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure tarafından atanan temsilcinin kaynak yönetimi](concepts/azure-delegated-resource-management.md) hakkında bilgi edinin.
- [Çapraz kiracı yönetim deneyimlerini](concepts/cross-tenant-management-experience.md)gezin.
- Bkz. [Azure açık thouse 'ı bir kuruluş içinde kullanma](concepts/enterprise.md).
- Azure ınthouse için [kullanılabilirlik](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) ve [Fedrampa ve DOD CC SRG denetim kapsamı](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) ayrıntılarını görüntüleyin.
