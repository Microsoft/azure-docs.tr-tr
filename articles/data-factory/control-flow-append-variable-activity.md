---
title: Azure Data Factory değişken etkinliğini sona Ekle
description: Bir Data Factory işlem hattında tanımlanmış var olan bir dizi değişkenine değer eklemek için değişken ekleme etkinliğinin nasıl ayarlanacağını öğrenin
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 1ca58fc208bb02d137b977e0b18857e8c87a5440
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783855"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory değişken etkinliğini sona Ekle
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Bir Data Factory işlem hattında tanımlanmış var olan bir dizi değişkenine değer eklemek için değişken ekleme etkinliğini kullanın.

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | Gerekli
-------- | ----------- | --------
name | İşlem hattının etkinlik adı | Yes
açıklama | Etkinliğin ne yaptığını açıklayan metin | hayır
tür | Etkinlik türü AppendVariable | evet
değer | Belirtilen değişkene eklemek için kullanılan dize sabiti veya ifade nesnesi değeri | evet
variableName | Etkinliğe göre değiştirilecek değişkenin adı, değişkenin ' Array ' türünde olması gerekir | evet

## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen ilgili bir denetim akışı etkinliği hakkında bilgi edinin: 

- [Değişken Etkinlik Ayarlama](control-flow-set-variable-activity.md)
