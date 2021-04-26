---
title: Şema dağıtımının aşamaları
description: Azure şemaları hizmetlerinin bir şema ataması oluştururken üzerinden gittiği güvenlik ve yapıt ile ilgili adımları öğrenin.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 473f8c7957994401ea6000ecc0d8023a89f8c349
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560171"
---
# <a name="stages-of-a-blueprint-deployment"></a>Şema dağıtımının aşamaları

Bir şema dağıtıldığında, şema içinde tanımlanan kaynakları dağıtmak için Azure plan hizmeti tarafından bir dizi eylem alınır. Bu makalede, her adımın neler olduğu hakkında ayrıntılı bilgi sağlanır.

Şema dağıtımı, bir aboneliğe bir şema atanarak veya [var olan bir atamayı güncelleştirerek](../how-to/update-existing-assignments.md)tetiklenir. Dağıtım sırasında Azure şemaları aşağıdaki üst düzey adımları gerçekleştirir:

> [!div class="checklist"]
> - Azure şemaları sahip hakları verildi
> - Şema atama nesnesi oluşturuldu
> - İsteğe bağlı-Azure şemaları, **sistem tarafından atanan** yönetilen kimlik oluşturur
> - Yönetilen kimlik, şema yapıtları dağıtır
> - Azure şemaları hizmeti ve **sistem tarafından atanan** yönetilen kimlik hakları iptal edilir

## <a name="azure-blueprints-granted-owner-rights"></a>Azure şemaları sahip hakları verildi

Azure planları hizmet sorumlusu, atanan abonelik veya aboneliklerde, [sistem tarafından atanan yönetilen kimlik](../../../active-directory/managed-identities-azure-resources/overview.md) yönetimli bir kimlik kullanıldığında sahip hakları verilir. Verilen rol, Azure şemaları 'nın **sistem tarafından atanan** yönetilen kimliği oluşturmasına ve daha sonra iptal etmesine olanak tanır. **Kullanıcı tarafından atanan** yönetilen kimlik kullanılıyorsa Azure planları hizmet sorumlusu, abonelik üzerinde sahip haklarına sahip değildir ve bu haklara gerek kalmaz.

Atama Portal üzerinden yapıldığında haklar otomatik olarak verilir. Ancak, atama REST API aracılığıyla yapılabiliyorsanız, hakların ayrı bir API çağrısıyla yapılması gerekir. Azure şemaları uygulama kimliği `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` , ancak hizmet sorumlusu kiracıya göre farklılık gösterir. Hizmet sorumlusunu almak için [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) ve REST uç nokta [servicesorumlularını](/graph/api/resources/serviceprincipal) kullanın. Daha sonra, Azure 'a [Portal](../../../role-based-access-control/role-assignments-portal.md), [azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md)veya [Azure Resource Manager şablonu](../../../role-based-access-control/role-assignments-template.md)aracılığıyla _sahip_ rolünü verin.

Azure şemaları hizmeti, kaynakları doğrudan dağıtmaz.

## <a name="the-blueprint-assignment-object-is-created"></a>Şema atama nesnesi oluşturuldu

Bir Kullanıcı, Grup veya hizmet sorumlusu bir aboneliğe şeması atar. Atama nesnesi, şema 'in atandığı abonelik düzeyinde bulunur. Dağıtım tarafından oluşturulan kaynaklar, dağıtım varlığının bağlamında yapılmaz.

Şema atamasını oluştururken, [yönetilen kimliğin](../../../active-directory/managed-identities-azure-resources/overview.md) türü seçilidir. Varsayılan ayar, **sistem tarafından atanan** yönetilen bir kimliktir. **Kullanıcı tarafından atanan** yönetilen kimlik seçilebilir. **Kullanıcı tarafından atanan** bir yönetilen kimlik kullanılırken, şema atama oluşturulmadan önce tanımlanmalı ve izinler verilmelidir. Hem [Owner](../../../role-based-access-control/built-in-roles.md#owner) hem de [Blueprint işlecinin](../../../role-based-access-control/built-in-roles.md#blueprint-operator) yerleşik rollerinin, `blueprintAssignment/write` **Kullanıcı tarafından atanan** yönetilen kimlik kullanan bir atama oluşturmak için gerekli izni vardır.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>İsteğe bağlı-Azure şemaları, sistem tarafından atanan yönetilen kimlik oluşturur

Atama sırasında [sistem tarafından atanan yönetilen kimlik](../../../active-directory/managed-identities-azure-resources/overview.md) seçildiğinde, Azure şemaları kimliği oluşturur ve yönetilen kimliğe [sahip](../../../role-based-access-control/built-in-roles.md#owner) rolü verir. Mevcut bir [atama yükseltildiyse](../how-to/update-existing-assignments.md), Azure şemaları önceden oluşturulmuş yönetilen kimliği kullanır.

Şema atamasıyla ilgili yönetilen kimlik, şema içinde tanımlanan kaynakları dağıtmak veya yeniden dağıtmak için kullanılır. Bu tasarım, atamaları yanlışlıkla kesintiye uğramasını önler.
Bu tasarım Ayrıca, Blueprint 'ten dağıtılan her bir kaynağın güvenliğini denetleyerek [kaynak kilitleme](./resource-locking.md) özelliğini destekler.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Yönetilen kimlik, şema yapıtları dağıtır

Yönetilen kimlik daha sonra, şema içindeki yapıların Kaynak Yöneticisi dağıtımlarını tanımlanan [sıralama düzeninde](./sequencing-order.md)tetikler. Diğer yapıtlara bağımlı yapıtlar doğru sırada dağıtıldığından emin olmak için sıra ayarlanabilir.

Bir dağıtım tarafından erişim hatası genellikle yönetilen kimliğe verilen erişim düzeyinin sonucudur. Azure planları Hizmeti, **sistem tarafından atanan** yönetilen kimliğin güvenlik yaşam döngüsünü yönetir. Bununla birlikte, Kullanıcı **tarafından atanan** yönetilen kimliğin haklarının ve yaşam döngüsünün yönetilmesi Kullanıcı tarafından sorumludur.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Blueprint hizmeti ve sistem tarafından atanan yönetilen kimlik hakları iptal edilir

Dağıtımlar tamamlandığında, Azure şemaları, abonelikle **sistem tarafından atanan** yönetilen kimliğin haklarını iptal eder. Ardından, Azure planlar hizmeti aboneliğin haklarını iptal eder. Haklar kaldırma, Azure şemaları 'nın bir abonelikte kalıcı bir sahip olmasını önler.

## <a name="next-steps"></a>Sonraki adımlar

- [Statik ve dinamik parametrelerin](./parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](./sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](./resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.