---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: d12a5042d0c2d79989d82e86e7265d030912f5ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901256"
---
Bu bölümde, Azure Stack Edge hizmetine uygun olarak Azure Storage hizmeti için sınırlamalar ve Azure dosyaları, Azure blok Blobları ve Azure sayfa Blobları için gerekli adlandırma kuralları açıklanmaktadır. Depolama sınırlarını dikkatlice gözden geçirin ve tüm önerileri izleyin.

Azure depolama hizmeti sınırları ve adlandırma paylaşımları, kapsayıcılar ve dosyaları için en iyi uygulamalar hakkında en son bilgiler için şuraya gidin:

- [Kapsayıcıları adlandırma ve başvuru](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Paylaşımları adlandırma ve onlara başvurma](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blok Blobları ve Sayfa Blobu kuralları](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure depolama hizmeti sınırlarını aşan veya Azure dosyaları/blob adlandırma kurallarına uymayan herhangi bir dosya veya dizin varsa, bu dosya veya dizinlerin Azure Stack Edge hizmeti aracılığıyla Azure depolama içine alınmaz.