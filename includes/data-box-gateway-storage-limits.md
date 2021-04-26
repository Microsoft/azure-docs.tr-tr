---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 624df1185f86004b48bcca921e76d55bfb14c48d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901248"
---
Bu bölümde, Azure Stack Edge/Data Box Gateway hizmetine uygun olarak Azure Storage hizmeti için sınırlamalar ve Azure dosyaları, Azure blok Blobları ve Azure sayfa Blobları için gerekli adlandırma kuralları açıklanmaktadır. Depolama sınırlarını dikkatlice gözden geçirin ve tüm önerileri izleyin.

Azure depolama hizmeti sınırları ve adlandırma paylaşımları, kapsayıcılar ve dosyaları için en iyi uygulamalar hakkında en son bilgiler için şuraya gidin:

- [Kapsayıcıları adlandırma ve başvuru](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Paylaşımları adlandırma ve onlara başvurma](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blok Blobları ve Sayfa Blobu kuralları](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure Depolama hizmetinin sınırlarını aşan ya da Azure Dosyalar/Blob adlandırma kurallarına uymayan dosyalar veya dizinler varsa, bu dosyalar veya dizinler Azure Stack Edge / Data Box Gateway hizmeti yoluyla Azure Depolama'ya alınmaz.