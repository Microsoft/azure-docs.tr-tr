---
title: Varlık özniteliklerini yazar-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Akademik Bilgi API 'sindeki Author varlığıyla kullanabileceğiniz öznitelikleri öğrenin.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146499"
---
# <a name="author-entity"></a>Yazar varlığı

> [!NOTE]
> Aşağıdaki öznitelikler yazar varlığına özgüdür. (Ty = ' 1 ')

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
Kimlik      | Varlık KIMLIĞI                             |Int64      |Eşittir
AuN     | Yazar normalleştirilmiş adı                    |Dize     |Eşittir
BILGISI      | Yazar toplam alıntı sayısı           |Int32      |None  
Davun    | Yazar görünen adı                   |Dize     |None
E | Genişletilmiş meta veriler</br></br>**Önemli**: Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA) | [Genişletildiğinde](#extended) | None
ECC     | Yazar toplam tahmini alıntı sayısı |Int32      |None
LKA. AID | Yazar için bulunan son bilinen ilişki varlık KIMLIĞI | Int64 | None
LKA. AfN | Yazar için bulunan son bilinen ilişki normalleştirilmiş adı | Dize | None
PC | Yazar toplam yayın sayısı | Int32 | None

## <a name="extended"></a>Genişletildiğinde

> [!IMPORTANT]
> Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA)

> [!IMPORTANT]
> "E" kullanarak ayrı ayrı genişletilmiş öznitelikler isteme desteği. kapsam, yani "E. DN" kullanım dışıdır. Teknik olarak desteklenmeye devam ederken, "E" kullanarak ayrı ayrı genişletilmiş öznitelikler istiyor. kapsam, "E" nesnesinin ve en üst düzey özniteliğin bir parçası olarak JSON yanıtında iki yerde döndürülen öznitelik değerinin oluşmasına neden olur.

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
LKA. AID | Yazar için bulunan son bilinen ilişki varlık KIMLIĞI | Int64 | None
LKA. AfN | Yazar için bulunan son bilinen ilişki normalleştirilmiş adı | Dize | None
PC | Yazar toplam yayın sayısı | Int32 | None
