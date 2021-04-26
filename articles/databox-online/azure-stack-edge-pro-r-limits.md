---
title: Azure Stack Edge Pro R sınırları | Microsoft Docs
description: Azure Stack Edge Pro R için sistem sınırlarını ve önerilen boyutları açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96468018"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack Edge Pro R limitleri

Azure Stack Edge Pro R çözümünüzü dağıtırken ve işletirken bu limitleri göz önünde bulundurun.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Azure Stack Edge Pro R hizmet limitleri

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Azure Stack Edge Pro R cihaz limitleri

Aşağıdaki tabloda Azure Stack Edge Pro R cihazının sınırları açıklanmaktadır.

| Description | Değer |
|---|---|
|Hayır. cihaz başına dosya |100.000.000 |
|Hayır. kapsayıcı başına paylaşım sayısı |1 |
|Maksimum No. her cihaz için uç nokta ve REST uç noktalarını paylaşma| 24 |
|Maksimum No. cihaz başına katmanlı depolama hesapları| 24|
|Bir paylaşıma yazılan en büyük dosya boyutu| 5 TB |
|Cihaz başına en fazla kaynak grubu sayısı| 800 |

## <a name="azure-storage-limits"></a>Azure depolama sınırları

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Veri yükleme uyarıları

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure depolama hesabı boyutu ve nesne boyutu sınırları

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure nesne boyutu sınırları

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro R 'yi dağıtmaya hazırlanma](azure-stack-edge-pro-r-deploy-prep.md)
