---
title: Azure Data Box Edge sınırları | Microsoft Docs
description: Azure Data Box Edge için sistem sınırlarını ve önerilen boyutları açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 9adfc1ef355c31f0bfeeb6c3b194d62426b7b9b3
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244308"
---
# <a name="azure-data-box-edge-limits"></a>Azure Data Box Edge sınırları

Microsoft Azure Data Box Edge çözümünüzü dağıtırken ve işletirken bu limitleri göz önünde bulundurun.

## <a name="data-box-edge-service-limits"></a>Hizmet sınırlarını Data Box Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Cihaz sınırlarını Data Box Edge

Aşağıdaki tabloda Data Box Edge cihazının sınırları açıklanmaktadır.

| Açıklama | Değer |
|---|---|
|Hayır. cihaz başına dosya |100.000.000 |
|Hayır. cihaz başına paylaşım sayısı |24 |
|Hayır. kapsayıcı başına paylaşım sayısı |1 |
|Bir paylaşıma yazılan en büyük dosya boyutu| 5 TB |

## <a name="azure-storage-limits"></a>Azure depolama sınırları

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Karşıya veri yükleme uyarıları

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure depolama hesabı boyutu ve nesne boyutu sınırları

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure nesne boyutu sınırları

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Box Edge’i dağıtmaya hazırlanma](data-box-edge-deploy-prep.md)
