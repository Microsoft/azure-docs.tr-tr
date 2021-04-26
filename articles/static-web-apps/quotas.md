---
title: Azure statik Web Apps önizlemede kotalar
description: Azure statik Web Apps önizlemesi ile ilişkili kotalar hakkında bilgi edinin
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095178"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemede kotalar

Azure statik Web Apps önizlemesi için aşağıdaki kotalar mevcuttur.

> [!IMPORTANT]
> Azure statik Web Apps genel önizlemede ve üretim kullanımı için tasarlanmamıştır.

| Özellik                     | Ücretsiz plan        |
|-----------------------------|------------------|
| Dahil edilen bant genişliği          | ayda 100 GB |
| Fazla kullanım bant genişliği           | Kullanılamaz      |
| Azure aboneliği başına uygulamalar | 10               |
| Uygulama boyutu                    | 250 MB           |
| Üretim öncesi ortamları | 3                |
| Özel etki alanları              | 1                |
| Yetkilendirme (özel roller ve yönlendirme kuralları ile) | Özel rollere ait olabilecek en fazla 25 Son Kullanıcı |
| Azure İşlevleri             | Kullanılabilir        |
| SLA                         | Yok             |

## <a name="github-storage"></a>GitHub depolama alanı

GitHub eylemleri ve paketleri, kendi kotalar kümesine sahip GitHub Storage kullanır. Azure statik Web Apps sitesi oluşturduğunuzda GitHub, dağıtımdan sonra bile sitenizin yapıtları depolar.

Daha ayrıntılı bilgi için aşağıdaki kaynaklara bakın:

- [Eylemler depolama alanını yönetme](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [GitHub eylemleri için faturalandırma hakkında](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [GitHub eylemleri için harcama limitinizi yönetme](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Sonraki adımlar

- [Genel Bakış](overview.md)
