---
title: Azure depolama hesabındaki bir Blobun erişim katmanını yönetme
description: GPv2 veya blob depolama hesabındaki bir Blobun katmanını değiştirmeyi öğrenin
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: cd46f5b5a8847150bd56c1daeaac470f9afd2d19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278550"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Azure depolama hesabındaki bir Blobun erişim katmanını yönetme

Her Blobun, sık erişimli, seyrek erişimli veya arşiv olmak üzere bir varsayılan erişim katmanına sahiptir. Blob, Azure depolama hesabının oluşturulduğu varsayılan erişim katmanını alır. BLOB depolama ve GPv2 hesapları, **erişim katmanı** özniteliğini hesap düzeyinde kullanıma sunar. Bu öznitelik, nesne düzeyinde açıkça ayarlanmamış olan herhangi bir blob için varsayılan erişim katmanını belirtir. Katman, nesne düzeyinde ayarlanan nesneler için, hesap katmanı uygulanmaz. Arşiv katmanı yalnızca nesne düzeyinde uygulanabilir. Aşağıdaki adımları izleyerek istediğiniz zaman erişim katmanları arasında geçiş yapabilirsiniz.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>GPv2 veya blob depolama hesabındaki bir Blobun katmanını değiştirme

Aşağıdaki senaryolar Azure portal veya PowerShell kullanır:

# <a name="portal"></a>[Portal](#tab/portal)

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal, **tüm kaynakları** arayıp seçin.

1. Depolama hesabınızı seçin.

1. Kapsayıcınızı seçin ve ardından blobu seçin.

1. **BLOB özellikleri**' nde **Katmanı Değiştir**' i seçin.

1. **Sık** **erişimli, seyrek** erişimli veya **Arşiv** erişim katmanını seçin. Blobun Şu anda arşivde varsa ve çevrimiçi bir katmana yeniden dönmek istiyorsanız, **Standart** veya **yüksek** bir yeniden doldurma önceliği de seçebilirsiniz.

1. Alt kısımdaki **Kaydet** ' i seçin.

![Azure portal blob katmanını değiştirme](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Blob katmanını değiştirmek için aşağıdaki PowerShell betiği kullanılabilir. `$rgName`Değişken, kaynak grubu adınızla başlatılmalıdır. `$accountName`Değişken, depolama hesabı adınızla başlatılmalıdır. `$containerName`Değişken, kapsayıcı adınızla başlatılmalıdır. `$blobName`Değişken, blob adınızla başlatılmalıdır.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Bir Azure depolama hesabının varsayılan hesap erişim katmanını yönetme](../common/manage-account-default-access-tier.md)
- [Blob verilerini arşiv katmanından yeniden doldurma hakkında bilgi edinin](storage-blob-rehydration.md)
- [BLOB depolama ve GPv2 hesaplarında bölgeye göre sık erişimli, seyrek erişimli ve arşiv fiyatlandırmasını denetleme](https://azure.microsoft.com/pricing/details/storage/)
