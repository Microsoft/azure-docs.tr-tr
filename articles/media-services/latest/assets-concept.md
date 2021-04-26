---
title: Azure Media Services varlıklar
description: Varlıkların ne olduğunu ve Azure Media Services tarafından nasıl kullanıldığını öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5159432107e60f6c21bcf70e0bbc9a9e2123a728
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897705"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure Media Services v3 içindeki varlıklar

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, bir [varlık](/rest/api/media/assets) temel kavramdır. Medyayı (örneğin, karşıya yükleme veya canlı alma aracılığıyla), çıkış medyasını (bir iş çıktısından) ve medya yayımlama (akış için). 

Bir varlık, [Azure depolama hesabındaki](storage-account-concept.md) bir blob kapsayıcısına eşlenir ve varlık içindeki dosyalar bu kapsayıcıda blok Bloblar olarak depolanır. Varlıklar, Azure depolama 'da depolanan dijital dosyalarla ilgili bilgiler içerir (video, ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı altyazı dosyaları dahil).

Media Services, hesap genel amaçlı v2 (GPv2) depolaması kullandığında blob katmanlarını destekler. GPv2 ile dosyaları seyrek erişimli [veya arşiv depolamaya](../../storage/blobs/storage-blob-storage-tiers.md)taşıyabilirsiniz. **Arşiv** depolama, artık gerekli olmadığında (örneğin, kodlandıktan sonra) kaynak dosyalarını arşivlemek için uygundur.

**Arşiv** depolama katmanı yalnızca önceden kodlanmış çok büyük kaynak dosyaları için önerilir ve kodlama iş çıktısı bir çıkış blobu kapsayıcısına konur. Bir varlıkla ilişkilendirmek istediğiniz çıkış kapsayıcısındaki Bloblar, **sık** erişimli veya seyrek erişimli **bir depolama katmanında** bulunmalıdır.

## <a name="naming"></a>Adlandırma 

### <a name="assets"></a>Varlıklar

Varlığın adları benzersiz olmalıdır. Media Services v3 kaynak adları (örneğin, varlıklar, Işler, dönüşümler) Azure Resource Manager adlandırma kısıtlamalarına tabidir. Daha fazla bilgi için bkz. [adlandırma kuralları](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Bloblar

Bir varlık içindeki dosyaların/Blobların adları, hem [BLOB adı gereksinimlerini](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) hem de [NTFS ad gereksinimlerini](/windows/win32/fileio/naming-a-file)izlemelidir. Bu gereksinimlerin nedeni, dosyaların işlenmek üzere blob depolamadan yerel bir NTFS diskine kopyalanmasını sağlayabilir.

## <a name="next-steps"></a>Sonraki adımlar

[Media Services genel bakış](media-services-overview.md)

## <a name="see-also"></a>Ayrıca bkz.

[Media Services V2 ve v3 arasındaki farklar](migrate-v-2-v-3-migration-introduction.md)
