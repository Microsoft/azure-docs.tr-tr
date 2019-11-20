---
title: Konferans örneği varlık öznitelikleri-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Akademik Bilgi API 'sindeki konferans örneği varlığıyla kullanabileceğiniz öznitelikleri öğrenin.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146569"
---
# <a name="conference-instance-entity"></a>Konferans örneği varlığı

> [!NOTE]
> Aşağıdaki öznitelikler, konferans örneği varlığına özgüdür. (Ty = ' 4 ')

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
BILGISI      |Konferans örneği toplam alıntı sayısı           |Int32      |None  
CD. TID    |Bir konferans örneği olayının tarihi    |Tarih       |Eşittir, ısbetween
CD. Şı    |Bir konferans örneği olayının başlığı   |Tarih       |Eşittir, ısbetween
CıARD   |Konferans örneğinin Özet kayıt bitiş tarihi  |Tarih       |Eşittir, ısbetween
CIED    |Konferans örneğinin bitiş tarihi    |Tarih       |Eşittir, ısbetween
CIFVD   |Konferans örneğinin son sürüm bitiş tarihi  |Tarih       |Eşittir, ısbetween
CIL     |Konferans örneğinin konumu    |Dize     |Eşittir, StartsWith
CIN     |Konferans örneği normalleştirilmiş adı |Dize        |Eşittir
CINDD   |Konferans örneğinin bildirim tarihi   |Tarih       |Eşittir, ısbetween
CıD    |Konferans örneğinin başlangıç tarihi  |Tarih       |Eşittir, ısbetween
CıDD   |Konferans örneğinin gönderim bitiş tarihi     |Tarih       |Eşittir, ısbetween
DCN     |Konferans örneği görünen adı  |Dize      |None
E | Genişletilmiş meta veriler</br></br>**Önemli**: Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA) | [Genişletildiğinde](#extended) | None
ECC     |Konferans örneği toplam tahmini alıntı sayısı |Int32      |None
FN | Konferans örneği tam adı | Dize | None
Kimlik      |Varlık KIMLIĞI                              |Int64      |Eşittir
PC | Konferans örneği toplam yayın sayısı | Int32 | None
Açıla. ,  |Örneğin üst konferans serisi adı |Dize  |Eşittir
Açıla. CID |Örneğin üst konferans serisi KIMLIĞI |Int64     |Eşittir

## <a name="extended"></a>Genişletildiğinde

> [!IMPORTANT]
> Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA)

> [!IMPORTANT]
> "E" kullanarak ayrı ayrı genişletilmiş öznitelikler isteme desteği. kapsam, yani "E. DN" kullanım dışıdır. Teknik olarak desteklenmeye devam ederken, "E" kullanarak ayrı ayrı genişletilmiş öznitelikler istiyor. kapsam, "E" nesnesinin ve en üst düzey özniteliğin bir parçası olarak JSON yanıtında iki yerde döndürülen öznitelik değerinin oluşmasına neden olur.

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
FN | Konferans örneği tam adı | Dize | None
PC | Konferans örneği toplam yayın sayısı | Int32 | None
