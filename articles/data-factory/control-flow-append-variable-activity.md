---
title: Azure Data Factory değişken etkinliğini sona Ekle
description: Bir Data Factory işlem hattında tanımlanmış var olan bir dizi değişkenine değer eklemek için değişken ekleme etkinliğinin nasıl ayarlanacağını öğrenin
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679987"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory değişken etkinliğini sona Ekle

Bir Data Factory işlem hattında tanımlanmış var olan bir dizi değişkenine değer eklemek için değişken ekleme etkinliğini kullanın.

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | Gerekli
-------- | ----------- | --------
ad | İşlem hattının etkinlik adı | Evet
açıklama | Etkinliğin ne yaptığını açıklayan metin | hayır
type | Etkinlik türü AppendVariable | evet
değer | Belirtilen değişkene eklemek için kullanılan dize sabiti veya ifade nesnesi değeri | evet
variableName | Etkinliğe göre değiştirilecek değişkenin adı, değişkenin ' Array ' türünde olması gerekir | evet

## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen ilgili bir denetim akışı etkinliği hakkında bilgi edinin: 

- [Değişken etkinliğini ayarla](control-flow-set-variable-activity.md)
