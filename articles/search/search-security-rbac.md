---
title: Azure yönetim erişimi için RBAC rollerini ayarlama
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama Yönetimi için yönetim görevlerini denetleme ve bunların yetkisini aktarmaya yönelik Azure portal rol tabanlı yönetim denetimi (RBAC).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112565"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Azure Bilişsel Arama yönetim erişimi için RBAC rolleri ayarlama

Azure, portal veya Kaynak Yöneticisi API 'Leri aracılığıyla yönetilen tüm hizmetler için [Genel rol tabanlı bir yetkilendirme modeli](../role-based-access-control/role-assignments-portal.md) sağlar. Sahip, katkıda bulunan ve okuyucu rolleri, her role atanan Active Directory Kullanıcılar, gruplar ve güvenlik sorumluları için *hizmet yönetiminin* düzeyini belirlemektir. 

> [!Note]
> Bir dizinin veya bir belge alt kümesinin bölümlerinin güvenliğini sağlamak için rol tabanlı erişim denetimi yoktur. Arama sonuçları üzerinden kimlik tabanlı erişim için, sonuçları kimliğe göre kırpmak, istek sahibinin erişimi olmaması gereken belgeleri kaldırmak için güvenlik filtreleri oluşturabilirsiniz. Daha fazla bilgi için bkz. [Güvenlik filtreleri](search-security-trimming-for-azure-search.md) ve [Active Directory Ile güvenli hale getirme](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Role göre yönetim görevleri

Azure Bilişsel Arama için roller, aşağıdaki yönetim görevlerini destekleyen izin düzeyleriyle ilişkilendirilir:

| Rol | Görev |
| --- | --- |
| Sahip |API anahtarları, dizinler, Dizin oluşturucular, Dizin Oluşturucu veri kaynakları ve Dizin Oluşturucu zamanlamaları dahil hizmet veya hizmette herhangi bir nesne oluşturun veya silin.<p>Sayılar ve depolama boyutu dahil olmak üzere hizmet durumunu görüntüleyin.<p>Rol üyeliği ekleme veya silme (yalnızca sahip, rol üyeliğini yönetebilir).<p>Abonelik yöneticileri ve hizmet sahipleri, sahipler rolünde otomatik üyeliğe sahiptir. |
| Katılımcı |Sahibe göre aynı erişim düzeyi, eksi RBAC rol yönetimi. Örneğin, katkıda bulunan, nesne oluşturabilir veya silebilir ya da [API anahtarlarını](search-security-api-keys.md)görüntüleyebilir ve yeniden oluşturabilir, ancak rol üyeliklerini değiştiremezler. |
| [Arama Hizmeti katkıda bulunan yerleşik rolü](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Katkıda bulunan rolüne eşdeğerdir. |
| Okuyucu |Hizmet temellerini ve ölçümlerini görüntüleyin. Bu rolün üyeleri dizin, Dizin Oluşturucu, veri kaynağı veya anahtar bilgilerini görüntüleyemez.  |

Roller, hizmet uç noktasına erişim hakkı vermez. Dizin Yönetimi, dizin oluşturma ve arama verilerinde sorgular gibi arama hizmeti işlemleri, rol değil API anahtarları aracılığıyla denetlenir. Daha fazla bilgi için bkz. [API anahtarlarını yönetme](search-security-api-keys.md).

## <a name="see-also"></a>Ayrıca bkz.

+ [PowerShell’i kullanarak yönetme](search-manage-powershell.md) 
+ [Azure Bilişsel Arama performans ve iyileştirme](search-performance-optimization.md)
+ [Azure Portal rol tabanlı Access Control kullanmaya](../role-based-access-control/overview.md)başlayın.