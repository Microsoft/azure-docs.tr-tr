---
title: Azure Güvenlik Merkezi araştırmada bulunan Kullanıcı verilerini yönetme
description: " Azure Güvenlik Merkezi 'nin araştırma özelliğinde bulunan Kullanıcı verilerini yönetmeyi öğrenin. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 059ca2a26e50128d6bc4313dad9f995e97c06378
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686395"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Azure Güvenlik Merkezi araştırmada bulunan Kullanıcı verilerini yönetme
Bu makalede, Azure Güvenlik Merkezi 'nin araştırma özelliğinde bulunan Kullanıcı verilerinin nasıl yönetileceği hakkında bilgi verilmektedir. Araştırma verileri [Azure izleyici günlüklerinde](../log-analytics/log-analytics-overview.md) depolanır ve Güvenlik Merkezi 'nde gösterilir. Kullanıcı verilerini yönetmek, verileri silme veya dışa aktarma olanağını içerir.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Kişisel verileri arama ve tanımlama
Azure portal, güvenlik merkezi 'nin [Araştırma özelliğini](../security-center/security-center-investigation.md) kullanarak kişisel verileri arayabilirsiniz. Araştırma özelliği **güvenlik uyarıları**altında bulunabilir.

Araştırma özelliği, **varlıklar** sekmesinde tüm varlıkları, Kullanıcı bilgilerini ve verileri gösterir.

## <a name="securing-and-controlling-access-to-personal-information"></a>Kişisel bilgilere erişimi güvenli hale getirme ve denetleme
Okuyucu, sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına araç içindeki müşteri verilerine erişim sağlayabilir.

Okuyucu, sahip ve katkıda bulunan rolleri hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi Için yerleşik roller](../role-based-access-control/built-in-roles.md) . Hesap Yöneticisi rolü hakkında daha fazla bilgi edinmek için bkz. [Azure abonelik yöneticileri](../billing/billing-add-change-azure-subscription-administrator.md) .

## <a name="deleting-personal-data"></a>Kişisel verileri silme
Sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına araştırma bilgileri silinebilir.

Bir araştırmayı silmek için Azure Resource Manager REST API `DELETE` isteği gönderebilirsiniz:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`incidentName` girişi, tüm olaylar `GET` isteği kullanılarak listelenerek bulunabilir:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Kişisel verileri dışarı aktarma
Sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına araştırma bilgilerini dışarı aktarabilirsiniz. Araştırma bilgilerini dışarı aktarmak için **varlıklar** sekmesine giderek ilgili bilgileri kopyalayıp yapıştırın.

## <a name="next-steps"></a>Sonraki adımlar
Kullanıcı verilerini yönetme hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde Kullanıcı verilerini yönetme](security-center-privacy.md).
Azure Izleyici günlüklerinde özel verileri silme hakkında daha fazla bilgi edinmek için bkz. [özel verileri dışarı aktarma ve silme](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
