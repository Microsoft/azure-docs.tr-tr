---
title: Azure Stack Edge Mini R sınırları | Microsoft Docs
description: Azure Stack Edge Mini R için sistem sınırlarını ve önerilen boyutları açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96468058"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack Edge Mini R sınırları


Azure Stack Edge Mini R çözümünüzü dağıtırken ve işletirken bu limitleri göz önünde bulundurun.

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge hizmeti limitleri

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Azure Stack Edge Mini R cihaz limitleri

Aşağıdaki tabloda Azure Stack Edge Mini R cihazının sınırları açıklanmaktadır.

| Description | Sınır|
|---|---:|
|Hayır. cihaz başına dosya | 100.000.000 <!--check with devs-->|
|Hayır. kapsayıcı başına paylaşım sayısı | 1|
|Maksimum No. her cihaz için uç nokta ve REST uç noktalarını paylaşma| 24 |
|Maksimum No. cihaz başına katmanlı depolama hesapları| 24|
|Bir paylaşıma yazılan en büyük dosya boyutu| 500 GB|
|Cihaz başına en fazla kaynak grubu sayısı| 800|

## <a name="azure-storage-limits"></a>Azure depolama sınırları

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Veri yükleme uyarıları

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure depolama hesabı boyutu ve nesne boyutu sınırları

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure nesne boyutu sınırları

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge dağıtmaya hazırlanma](azure-stack-edge-gpu-deploy-prep.md)
