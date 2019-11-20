---
title: Akademik Bilgi API 'sindeki ilişki varlığı öznitelikleri
titlesuffix: Azure Cognitive Services
description: Akademik Bilgi API 'sindeki Ilişki varlığı ile kullanabileceğiniz öznitelikleri öğrenin.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143927"
---
# <a name="affiliation-entity"></a>İlişki varlığı

> [!NOTE]
> Aşağıdaki öznitelikler ilişki varlığına özgüdür. (Ty = ' 5 ')

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
AfN | İlişki normalleştirilmiş adı |Dize |Eşittir
BILGISI | İlişki toplam alıntı sayısı |Int32        |yok  
Bafn | İlişki görünen adı |Dize |yok
E | Genişletilmiş meta veriler</br></br>**Önemli**: Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA) | [Genişletildiğinde](#extended) | None
ECC | Toplam tahmini alıntı sayısı |Int32 |yok
Kimlik | Varlık KIMLIĞI |Int64 |Eşittir
PC | Bu varlıkla ilişki halinde yazılan toplam yayın sayısı | Int32 | None

## <a name="extended"></a>Genişletildiğinde

> [!IMPORTANT]
> Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA)

> [!IMPORTANT]
> "E" kullanarak ayrı ayrı genişletilmiş öznitelikler isteme desteği. kapsam, yani "E. DN" kullanım dışıdır. Teknik olarak desteklenmeye devam ederken, "E" kullanarak ayrı ayrı genişletilmiş öznitelikler istiyor. kapsam, "E" nesnesinin ve en üst düzey özniteliğin bir parçası olarak JSON yanıtında iki yerde döndürülen öznitelik değerinin oluşmasına neden olur.

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
PC | Bu varlıkla ilişki halinde yazılan toplam yayın sayısı | Int32 | None
