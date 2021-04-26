---
title: Hizmet sağlayıcısı etkinliğini görüntüleme
description: Müşteriler, Azure tarafından atanan kaynak yönetimi aracılığıyla hizmet sağlayıcıları tarafından gerçekleştirilen eylemleri görmek için günlüğe kaydedilen etkinliği görüntüleyebilir.
ms.date: 12/11/2020
ms.topic: how-to
ms.openlocfilehash: 40deca310eea2fc9618cfc83d34caadcf2b2b14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571750"
---
# <a name="view-service-provider-activity"></a>Hizmet sağlayıcısı etkinliğini görüntüleme

Azure açık bir [kullanım](../overview.md) için abonelikleri temsilci seçen müşteriler, gerçekleştirilen tüm eylemleri görmek Için [Azure etkinlik günlüğü verilerini görüntüleyebilir](../../azure-monitor/essentials/platform-logs-overview.md) . Bu, müşterilere hizmet sağlayıcılarının [Azure tarafından atanan kaynak yönetimi](../concepts/azure-delegated-resource-management.md)aracılığıyla yaptığı ve müşterinin kendi Azure Active Directory (Azure AD) kiracısındaki kullanıcılar tarafından gerçekleştirilen işlemlerle birlikte tam görünürlük sağlar.

> [!TIP]
> Ayrıca, [temsilciliğini yönetmek](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) ve [kapsamları yöneten bir kiracıya Yetkilendirmeyi denetlemek](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json)için Azure ilkesi yerleşik ilke tanımları sunuyoruz. Daha fazla bilgi için bkz. [ortamınızdaki denetim temsilcileri](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Etkinlik günlüğü verilerini görüntüleme

[Etkinlik günlüğünü](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) Azure Portal **Monitor** menüsünden görüntüleyebilirsiniz. Sonuçları belirli bir abonelikle sınırlamak için, belirli bir abonelik seçmek üzere filtreleri kullanın. Ayrıca, [etkinlik günlüğü olaylarını programlı bir şekilde görüntüleyebilir ve alabilirsiniz](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) .

> [!NOTE]
> Bir hizmet sağlayıcısının kiracısındaki kullanıcılar, bu abonelik Azure Kathouse 'a eklendi olduğunda, [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü (veya okuyucu erişimi içeren başka bir yerleşik rol) verildiğinde, bir müşteri kiracısında bir temsilci abonelik için etkinlik günlüğü sonuçlarını görüntüleyebilir.

Etkinlik günlüğünde işlemin adını ve durumunu, gerçekleştirildiği tarih ve saat ile birlikte görürsünüz. Sütunu **tarafından başlatılan olay** , hizmetin Azure Mathouse veya müşterinin kendi kiracısındaki bir kullanıcı tarafından kullanılan hizmet sağlayıcının kiracısında bir kullanıcı olup olmadığını gösterir. Kullanıcı adının, kiracı veya kullanıcının bu abonelik için atandığı rol yerine gösterildiğini unutmayın.

Günlüğe kaydedilen etkinlik, son 90 gün için Azure portal kullanılabilir. Bu verileri 90 günden daha uzun bir süre için nasıl depolayacağınızı öğrenmek için bkz. [Log Analytics çalışma alanında Azure etkinlik günlüklerini toplayın ve çözümleyin](../../azure-monitor/essentials/activity-log.md).

> [!NOTE]
> Hizmet sağlayıcısından kullanıcılar etkinlik günlüğünde görünür, ancak bu kullanıcılar ve rol atamaları **Access Control (IAM)** veya API 'ler aracılığıyla rol atama bilgileri alınırken gösterilmez.

## <a name="set-alerts-for-critical-operations"></a>Kritik işlemler için uyarı ayarlama

Hizmet sağlayıcılarının (veya kendi kiracınızdaki kullanıcıların) gerçekleştirdiği kritik işlemlerden haberdar olmak için, [etkinlik günlüğü uyarıları](../../azure-monitor/alerts/activity-log-alerts.md)oluşturmanızı öneririz. Örneğin, bir abonelik için tüm yönetim eylemlerini izlemek veya belirli bir kaynak grubundaki herhangi bir sanal makine silindiğinde bildirim almak isteyebilirsiniz. Uyarılar oluşturduğunuzda, kullanıcılar tarafından, kullanıcıların kendi kiracısındaki ve tüm kiracıların yönetiminde gerçekleştirilen eylemler dahil edilir.

Daha fazla bilgi için bkz. [etkinlik günlüğü uyarıları oluşturma ve yönetme](../../azure-monitor/alerts/alerts-activity-log.md).

## <a name="create-log-queries"></a>Günlük sorguları oluştur

Günlüğe kaydedilen etkinliğinizi çözümlemek veya belirli öğelere odaklanmak için sorgular oluşturabilirsiniz. Örneğin, bir denetim, bir abonelikte gerçekleştirilen tüm yönetim düzeyi eylemleri raporlamak için gerekli olabilir. Yalnızca bu eylemleri filtrelemek ve sonuçları Kullanıcı, tarih veya başka bir değere göre sıralamak için bir sorgu oluşturabilirsiniz.

Daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../../azure-monitor/logs/log-query-overview.md).

## <a name="view-user-activity-across-domains"></a>Etki alanları genelindeki Kullanıcı etkinliğini görüntüleme

Etki alanı örnek çalışma kitabına [göre etkinlik günlüklerini](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) kullanarak, birden çok etki alanında bulunan bireysel kullanıcılardan etkinliği görüntüleyebilirsiniz.

Sonuçlar, etki alanı adına göre filtrelenebilir. Kategori, düzey veya kaynak grubu gibi ek filtreler de uygulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici](../../azure-monitor/index.yml)hakkında daha fazla bilgi edinin.
- Azure portal [hizmet sağlayıcısı tekliflerini görüntülemeyi ve yönetmeyi](view-manage-service-providers.md) öğrenin.
