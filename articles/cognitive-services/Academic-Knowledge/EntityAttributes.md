---
title: Akademik grafik varlık öznitelikleri-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Akademik Bilgi API 'sindeki akademik grafikle kullanabileceğiniz varlık öznitelikleri hakkında bilgi edinin.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705029"
---
# <a name="entity-attributes"></a>Varlık öznitelikleri

Akademik grafik 7 varlık türünden oluşur. Tüm varlıkların bir varlık KIMLIĞI ve bir varlık türü olacaktır.

## <a name="common-entity-attributes"></a>Ortak varlık öznitelikleri
Ad    |Açıklama                |Type       | İşlemler
------- | ------------------------- | --------- | ----------------------------
Id      |Varlık Kimliği                  |Int64      |Eşittir
Kalite      |Varlık türü                |Sabit listesi   |Eşittir

## <a name="entity-type-enum"></a>Varlık türü numaralandırması
Ad                                                            |value
----------------------------------------------------------------|-----
[Kağıt](PaperEntityAttributes.md)                               |0
[Yazar](AuthorEntityAttributes.md)                             |1\.
[Günlük](JournalEntityAttributes.md)                           |2
[Konferans Serisi](JournalEntityAttributes.md)                 |3
[Konferans örneği](ConferenceInstanceEntityAttributes.md)    |4
[İlişkilendirme](AffiliationEntityAttributes.md)                   |5
[Çalışma alanı](FieldsOfStudyEntityAttributes.md)                      |6

