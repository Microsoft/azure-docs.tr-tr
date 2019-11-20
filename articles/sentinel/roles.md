---
title: Azure Sentinel izinleri | Microsoft Docs
description: Bu makalede, Azure Sentinel 'in kullanıcılara izin atamak ve her bir rol için izin verilen eylemleri tanımlamak için rol tabanlı erişim denetimi nasıl kullandığı açıklanmaktadır.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0bf95b499a7366dad1e7b78fa4298aa6a42bb5fe
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316795"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel izinleri

Azure Sentinel, Azure 'daki kullanıcılara, gruplara ve hizmetlere atanabilen [yerleşik roller](../role-based-access-control/built-in-roles.md) sağlamak için [rol tabanlı Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)kullanır.

RBAC kullanarak, Azure Sentinel 'e uygun erişim sağlamak için güvenlik işlemleri ekibiniz dahilinde roller kullanabilir ve oluşturabilirsiniz. Rollere bağlı olarak, Azure Sentinel 'e erişimi olan kullanıcıların neleri görebileceklerini ayrıntılı olarak denetleyebilirsiniz. RBAC rollerini doğrudan Azure Sentinel çalışma alanında veya çalışma alanının ait olduğu bir aboneliğe veya kaynak grubuna atayabilirsiniz.

Üç özel yerleşik Azure Sentinel rolü vardır.  
**Tüm Azure Sentinel yerleşik rolleri, Azure Sentinel çalışma alanınızdaki verilere yönelik okuma erişimi verir.**
- **Azure Sentinel okuyucusu**: Bu rolle atanan bir kullanıcının Azure Sentinel 'e yönelik hakları görüntüleme hakkı vardır. Kullanıcı olayları ve verileri görüntüleyebilir, ancak değişiklik yapamaz.
- **Azure Sentinel Yanıtlayıcısı**: Bu rolle atanan bir kullanıcı atama ve önem derecesi değişiklikleri gibi olaylar için okuma ve eylemleri gerçekleştirebilir.
- **Azure Sentinel katılımcısı**: Bu rolle atanan bir Kullanıcı, olaylar üzerinde eylemler okuyup gerçekleştirebilir ve analitik kuralları oluşturabilir ve silebilir.

Azure Sentinel adanmış RBAC rollerinin yanı sıra Azure ve Log Analytics RBAC rolleri, Azure Sentinel çalışma alanınıza ve diğer kaynaklara erişimi de içeren daha geniş bir izin kümesine izin verebilir:

- **Azure rolleri:** [Sahip](../role-based-access-control/built-in-roles.md#owner), [katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor)ve [okuyucu](../role-based-access-control/built-in-roles.md#reader). Azure rolleri, Log Analytics çalışma alanları ve Azure Sentinel kaynakları dahil olmak üzere tüm Azure kaynaklarınız genelinde erişim izni verir.

-   **Log Analytics roller:** [Log Analytics katkıda bulunan](../role-based-access-control/built-in-roles.md#log-analytics-contributor) [Log Analytics okuyucu](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics roller tüm Log Analytics çalışma alanlarınız genelinde erişim izni verir. 

> [!NOTE]
> Log Analytics roller, tüm Azure kaynaklarında okuma erişimi de verir, ancak yalnızca Log Analytics kaynaklarına yazma izinleri atar.


Örneğin, **Azure Sentinel okuyucu** ve **Azure katılımcısı** ( **Azure Sentinel katılımcısı**değil) rolleri Ile atanan bir Kullanıcı, yalnızca **Sentinel okuyucu** izinleri olsa da verileri Azure Sentinel 'de düzenleyebilecektir. Bu nedenle, yalnızca Azure Sentinel 'de izin vermek istiyorsanız, başka bir kaynak için gerekli izin rollerini bozmayın, bu kullanıcının önceki izinlerini dikkatle kaldırmanız gerekir.

> [!NOTE]
>- Azure Sentinel otomatik tehdit yanıtı için PlayBook 'ları kullanır. Playbooks Azure Logic Apps yararlanır ve ayrı bir Azure kaynağıdır. Güvenlik düzenleme, otomasyon ve yanıt (SOAR) işlemleri için Logic Apps kullanma seçeneğiyle güvenlik işlemleri ekibinizin belirli üyelerini atamak isteyebilirsiniz. PlayBook 'ları kullanmaya açık izin atamak için [Logic App katılımcısı](../role-based-access-control/built-in-roles.md#logic-app-contributor) rolünü veya [mantıksal uygulama işletmeni](../role-based-access-control/built-in-roles.md#logic-app-operator) rolünü kullanabilirsiniz.
>- Veri bağlayıcıları eklemek için her bağlayıcı için gerekli roller bağlayıcı türü başına ve ilgili bağlayıcı sayfasında listelenir. Ayrıca, herhangi bir veri kaynağına bağlanmak için Azure Sentinel çalışma alanında yazma izninizin olması gerekir.



## <a name="roles-and-allowed-actions"></a>Roller ve izin verilen eylemler

Aşağıdaki tabloda, Azure Sentinel 'de roller ve izin verilen eylemler görüntülenmektedir. X, bu rol için eyleme izin verildiğini gösterir.

| Role | PlayBook 'lar oluşturma ve çalıştırma| Panolar, analitik kurallar ve diğer Azure Sentinel kaynakları oluşturma ve düzenleme | Olayları yönetme (Kapat, ata, vb.) | Verileri, olayları, panoları ve diğer Azure Sentinel kaynaklarını görüntüleyin |
|--- |---|---|---|---|
| Azure Sentinel okuyucusu | -- | -- | -- | X |
| Azure Sentinel Yanıtlayıcısı|--|--| X | X |
| Azure Sentinel Katılımcısı | -- | X | X | X |
| Azure Sentinel katkıda bulunan + Logic App Katılımcısı | X | X | X | X |


> [!NOTE]
> - Kullanıcılara, görevlerini tamamlamak için gereken rolleri en alt seviyede esneklik sunacak şekilde atamanızı öneririz. Örneğin, Azure Sentinel katkıda bulunan rolünü yalnızca kurallar veya panolar oluşturması gereken kullanıcılara atayın.
> - Kullanıcının aynı kaynak grubundaki tüm Azure Sentinel çalışma alanlarına erişebilmesi için, kaynak grubu kapsamında Azure Sentinel için izinleri ayarlamanızı öneririz.
>
## <a name="building-custom-rbac-roles"></a>Özel RBAC rolleri oluşturma

Ya da yerine, yerleşik RBAC rollerini kullanarak Azure Sentinel için özel RBAC rolleri oluşturabilirsiniz. Azure Sentinel için özel RBAC rolleri, Azure Sentinel kaynaklarına özel izinler temel alınarak diğer [özel Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) rollerini oluşturduğunuz şekilde oluşturulmuştur.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Azure Sentinel 'de depoladığınız veriler üzerinde gelişmiş RBAC
  
Log Analytics gelişmiş rol tabanlı erişim denetimini Azure Sentinel çalışma alanınızdaki veriler arasında kullanabilirsiniz. Bu, veri türü ve kaynak merkezli rol tabanlı erişim denetimi başına rol tabanlı erişim denetimi içerir. Log Analytics rolleri hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük verilerini ve çalışma alanlarını yönetme](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Sentinel kullanıcıları için rollerle çalışmayı ve her rolün kullanıcılara ne yaptığını öğrendiniz.

* [Azure Güvenlik Blogu](https://blogs.msdn.com/b/azuresecurity/). Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulun.
