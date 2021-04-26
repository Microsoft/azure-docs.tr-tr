---
title: GitHub'a Bağlan
description: Ortak veri modeli varlık başvurularınızı belirtmek için GitHub kullanın
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: weetok
ms.openlocfilehash: 7461a0332a36509c7bb6dfdd6db5948b056b35a6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222121"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Ortak veri modeli varlık başvurularını okumak için GitHub kullanın

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 'daki GitHub Bağlayıcısı, yalnızca eşleme veri akışı 'nda [ortak veri modeli](format-common-data-model.md) için varlık başvurusu şemasını almak için kullanılır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

GitHub bağlantılı hizmeti için aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **GitHub** olarak ayarlanmalıdır. | evet
| userName | GitHub Kullanıcı adı | evet |
| password | GitHub parolası | evet |

## <a name="next-steps"></a>Sonraki Adımlar

Eşleme veri akışında bir [kaynak veri kümesi](data-flow-source.md) oluşturun.