---
title: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373768"
---
Bir güvenlik sorumlusuna Azure RBAC rolü atamadan önce, güvenlik sorumlusunun sahip olması gereken erişimin kapsamını saptayın. En iyi uygulamalar, yalnızca en dar olası kapsamı sağlamak için her zaman en iyi seçenektir. Daha geniş bir kapsamda tanımlanan Azure RBAC rolleri, bunların altındaki kaynaklar tarafından devralınır.

Aşağıdaki listede, en dar kapsamdan başlayarak Azure Blob ve kuyruk kaynaklarına erişimi kapsamındaki düzeyler açıklanmaktadır:

- **Tek bir kapsayıcı.** Bu kapsamda bir rol ataması, kapsayıcıdaki tüm Blobların yanı sıra kapsayıcı özellikleri ve meta veriler için de geçerlidir.
- **Tek bir kuyruk.** Bu kapsamda, bir rol atamasının kuyruktaki iletiler, Ayrıca kuyruk özellikleri ve meta veriler için geçerli olduğunu.
- **Depolama hesabı.** Bu kapsamda, bir rol ataması tüm kapsayıcılar ve Blobları için ya da tüm kuyruklar ve bunların iletileri için geçerlidir.
- **Kaynak grubu.** Bu kapsamda, bir rol ataması, kaynak grubundaki tüm depolama hesaplarında bulunan tüm kapsayıcılar veya kuyruklar için geçerlidir.
- **Abonelik.** Bu kapsamda, bir rol ataması, abonelikteki tüm kaynak gruplarındaki tüm depolama hesaplarında tüm kapsayıcılar veya kuyruklar için geçerlidir.
- **Bir yönetim grubu.** Bu kapsamda, bir rol ataması, yönetim grubundaki tüm aboneliklerdeki tüm kaynak gruplarındaki tüm depolama hesaplarında tüm kapsayıcılar veya kuyruklar için geçerlidir.

Azure rol atamaları ve kapsamı hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../articles/role-based-access-control/overview.md).
