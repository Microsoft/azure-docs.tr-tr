---
title: Azure Stack Edge Pro limitleri | Microsoft Docs
description: Hizmet sınırları, cihaz limitleri ve depolama sınırları dahil olmak üzere Azure Stack Edge Pro 'Yu dağıtırken ve işletirken sınırlar ve önerilen Boyutlar hakkında bilgi edinin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992756"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro sınırları

Microsoft Azure Stack Edge Pro çözümünüzü dağıtırken ve işletirken bu limitleri göz önünde bulundurun. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge hizmeti limitleri

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge cihaz limitleri

Aşağıdaki tablo Azure Stack Edge Pro cihazının sınırlarını açıklamaktadır. 

Aşağıdaki tablo Azure Stack Edge cihazının sınırlarını açıklamaktadır.

| Description | Değer |
|---|---|
|Hayır. cihaz başına dosya |100.000.000 |
|Hayır. kapsayıcı başına paylaşım sayısı |1 |
|Maksimum No. her cihaz için uç nokta ve REST uç noktalarını paylaşma| 24 |
|Maksimum No. cihaz başına katmanlı depolama hesapları| 24|
|Bir paylaşıma yazılan en büyük dosya boyutu| 5 TB |
|Cihaz başına en fazla kaynak grubu sayısı| 800 |

## <a name="azure-storage-limits"></a>Azure depolama sınırları

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Veri yükleme uyarıları

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure depolama hesabı boyutu ve nesne boyutu sınırları

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure nesne boyutu sınırları

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'Yu dağıtmaya hazırlanma](azure-stack-edge-deploy-prep.md)
