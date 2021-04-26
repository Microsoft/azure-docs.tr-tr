---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 18f64defbc4222b46d858cb9cfd3e9b56866a4d0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513105"
---
Azure, Azure AD ve OAuth kullanarak blob ve kuyruk verilerine erişim yetkisi veren aşağıdaki Azure yerleşik rollerini sağlar:

- [Depolama Blob Verileri Sahibi](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake Storage 2. Nesil için sahipliği ayarlamak ve POSIX erişim denetimini yönetmek için kullanın. Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Nesil'de erişim denetimi](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Depolama Blob Verileri Katkıda Bulunanı](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob depolama kaynaklarına okuma/yazma/silme izinleri vermek için kullanın.
- [Depolama Blob Verileri Okuyucusu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob depolama kaynaklarına salt okuma izinleri vermek için kullanın.
- [Depolama Blob Temsilcisi](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Kapsayıcı veya blob için Azure AD kimlik bilgileriyle imzalanan bir paylaşılan erişim imzası oluştururken kullanmak üzere bir kullanıcı temsilci anahtarı alın.
- [Depolama Kuyruğu Verileri Katkıda Bulunanı](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Azure kuyruklarına okuma/yazma/silme izinleri vermek için kullanın.
- [Depolama Kuyruğu Verileri Okuyucusu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Azure kuyruklarına salt okuma izinleri vermek için kullanın.
- [Depolama Kuyruğu Verileri İleti İşleyicisi](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Azure Depolama kuyruklarındaki iletiler üzerinde göz atma, alma ve silme izinleri vermek için kullanın.
- [Depolama Kuyruğu Verileri İleti Göndereni](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Azure Depolama kuyruklarındaki iletiler üzerinde ekleme izinleri vermek için kullanın.

Yalnızca veri erişimi için açıkça tanımlanmış roller, bir güvenlik sorumlusunun blob veya kuyruk verilerine erişmesine izin verir. **Sahip**, **katkıda** bulunan ve **depolama hesabı katılımcısı** gibi yerleşik roller, bir güvenlik sorumlusunun bir depolama hesabını yönetmesine izin verir, ancak bu hesap IÇINDEKI blob veya kuyruk verilerine Azure AD aracılığıyla erişim sağlamaz. Ancak, bir rol **Microsoft. Storage/storageAccounts/listKeys/Action** içeriyorsa, bu rolün atandığı bir Kullanıcı, hesap erişim anahtarlarıyla paylaşılan anahtar yetkilendirmesi aracılığıyla depolama hesabındaki verilere erişebilir. Daha fazla bilgi için bkz. [BLOB veya kuyruk verilerine erişmek için Azure Portal kullanma](../articles/storage/blobs/authorize-data-operations-portal.md).

Hem veri hizmetleri hem de yönetim hizmeti için Azure depolama için Azure yerleşik rolleri hakkında ayrıntılı bilgi için Azure [RBAC Için Azure yerleşik rollerinin](../articles/role-based-access-control/built-in-roles.md#storage) **depolama** bölümüne bakın. Ayrıca, Azure 'da izinler sağlayan farklı rol türleri hakkında bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Azure rol atamalarının yayılması 30 dakika kadar sürebilir.
