---
title: Azure Lighthouse nedir?
description: Azure Athouse, hizmet sağlayıcılarının daha yüksek otomasyon ve verimlilik özelliklerine sahip müşterileri için yönetilen hizmetler sunmasına olanak tanır.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0551bbcbc9609809b0b28eb56a0a3b848debcaae
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132201"
---
# <a name="what-is-azure-lighthouse"></a>Azure Lighthouse nedir?

Azure Athouse hizmeti sağlayıcıları, daha yüksek otomasyon, ölçek ve gelişmiş idare özelliklerine sahip tüm müşterilerine Azure 'u görüntülemek ve yönetmek için tek bir denetim düzlemi sunmaktadır. Azure açık, hizmet sağlayıcıları, Azure platformunda yerleşik kapsamlı ve güçlü yönetim araçlarını kullanarak yönetilen Hizmetleri sunabilir. Bu teklif, birden fazla kiracıda kaynakları yöneten kurumsal BT kuruluşları için de avantaj sağlayabilir.

![Azure açık kullanım için genel bakış Diyagramı](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Yararları

Azure Kathouse, müşterileriniz için yönetilen Hizmetleri öngörülebilir ve verimli bir şekilde oluşturup sunmanıza yardımcı olur. Avantajları şunlardır:

- **Ölçekteki yönetim**: müşteri kaynaklarını yönetmeye yönelik müşteri katılımı ve yaşam döngüsü işlemleri daha kolay ve ölçeklenebilir.
- **Müşteriler Için daha fazla görünürlük ve duyarlık**: yönettiğiniz kaynakları, eylemleriniz üzerinde daha fazla görünürlük ve yönetim için temsilcdikleri kapsam üzerinde tam denetim sahibi olacak şekilde IP 'niz korunurken
- **Kapsamlı ve Birleşik platform araçları**: alet oluşturma DENEYIMIMIZ, EA, CSP ve kullandıkça öde gibi birden fazla lisanslama modeli de dahil olmak üzere temel hizmet sağlayıcısı senaryolarına yöneliktir. Yeni yetenekler, mevcut araçlar ve API 'Ler, lisanslama modelleri ve [bulut çözümü sağlayıcısı programı (CSP)](https://docs.microsoft.com/partner-center/csp-overview)gibi iş ortağı programlarıyla çalışır. Seçtiğiniz Azure hafif kullanım seçenekleri mevcut iş akışlarınızla ve uygulamalarınızla tümleştirilebilir ve [iş ortağı kimliğinizi bağlayarak](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started)müşteri görevlendirmelerinizi izleyebilirsiniz.

Müşterilerinizin Azure kaynaklarını yönetmek için Azure açık Thouse kullanımıyla ilişkili ek maliyet yoktur.

## <a name="capabilities"></a>Özellikler

Azure ışıklı kullanımı, müşteri katılımı ve yönetimini kolaylaştırmaya yardımcı olmak için birden çok yol içerir:

- **Azure Temsilcili kaynak yönetimi**: içerik ve denetim düzlemleri arasında geçiş yapmak zorunda kalmadan müşterilerinizin Azure kaynaklarını kendi kiracınızın içinden güvenli bir şekilde yönetin. Daha fazla bilgi için bkz. [Azure temsilcisi kaynak yönetimi](./concepts/azure-delegated-resource-management.md).
- **Yeni Azure Portal deneyimler**: [Azure Portal](https://portal.azure.com)içindeki yeni **müşterilerimiz** sayfasında çapraz kiracı bilgilerini görüntüleyin. Karşılık gelen bir **hizmet** sağlayıcı dikey penceresi, müşterilerinizin hizmet sağlayıcı erişimini görüntülemesine ve yönetmesine olanak tanır. Daha fazla bilgi için bkz. [müşterileri görüntüleme ve yönetme](./how-to/view-manage-customers.md) ve [hizmet sağlayıcılarını görüntüleme ve yönetme](./how-to/view-manage-service-providers.md).
- **Azure Resource Manager şablonlar**: Azure tarafından atanan kaynak yönetimine yönelik ekleme müşterileri dahil olmak üzere yönetim görevlerini daha kolay bir şekilde gerçekleştirin. Daha fazla bilgi için bkz. [örnek](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) depomız ve [bir müşteriyi Azure tarafından atanan kaynak yönetimine ekleme](how-to/onboard-customer.md).
- **Azure Market 'Te yönetilen hizmetler**teklifi: özel veya genel tekliflerle hizmetlerinizi müşterilere sunun ve Azure Resource Manager şablonlarını kullanarak hazırlama için alternatif olarak Azure tarafından atanan kaynak yönetimine otomatik olarak eklendi. Daha fazla bilgi için bkz. [Azure Marketi 'Nde yönetilen hizmetler teklifleri](./concepts/managed-services-offers.md).
- **Azure yönetilen uygulamalar**: müşterilerinizin kendi aboneliklerinde dağıtımı ve kullanması kolay olan uygulamaları paketleyin ve sunun. Uygulama, kiracınızdan erişebileceğiniz bir kaynak grubuna dağıtılır ve bu, hizmeti genel Azure Athouse deneyiminin bir parçası olarak yönetmenize olanak tanır. Daha fazla bilgi için bkz. [Azure yönetilen uygulamalara genel bakış](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure tarafından atanan temsilcinin kaynak yönetimi](concepts/azure-delegated-resource-management.md) hakkında bilgi edinin.
- [Çapraz kiracı yönetim deneyimleri](concepts/cross-tenant-management-experience.md)hakkında bilgi edinin.
