---
title: Azure CLı betik örneği-dönüşüm oluşturma
description: Dönüşümler video veya ses dosyalarınızın işlenmesine yönelik görevlerden oluşan basit bir iş akışı tanımlar (genellikle "tarif" olarak adlandırılır). Bu makaledeki Azure CLI betiği, dönüşüm oluşturmayı gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 75b45067be49475ecd2e07aed9c4479b147fdd29
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490276"
---
# <a name="create-a-transform"></a>Dönüşüm oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makaledeki Azure CLI betiği, dönüşüm oluşturmayı gösterir. Dönüşümler video veya ses dosyalarınızın işlenmesine yönelik görevlerden oluşan basit bir iş akışı tanımlar (genellikle "tarif" olarak adlandırılır). Zaten istenen ada ve “tarife” sahip bir Dönüşümün olup olmadığını mutlaka kontrol etmelisiniz. Böyle bir tarif varsa bunu yeniden kullanmalısınız.

## <a name="prerequisites"></a>Önkoşullar

[Media Services hesabı oluşturun](./account-create-how-to.md).

## <a name="cli"></a>[CLI](#tab/cli/)

> [!NOTE]
> [Standardencoderönayar](/rest/api/media/transforms/createorupdate#standardencoderpreset)için yalnızca özel standart kodlayıcı önceden ayarlanmış JSON dosyası için bir yol belirtebilirsiniz, özel bir dönüşüm örneği [ile](transform-custom-preset-cli-how-to.md) kodlamaya bakın.
>
> [Builtınstandardencoderönayar](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)kullanılırken bir dosya adı geçirilemez.

## <a name="example-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
