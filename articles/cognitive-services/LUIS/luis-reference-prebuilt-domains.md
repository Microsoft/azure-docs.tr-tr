---
title: Önceden oluşturulmuş etki alanı başvurusu-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Services (LUSıS) ' den önceden oluşturulmuş amaç ve varlık koleksiyonları olan önceden oluşturulmuş etki alanları için başvuru.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 17d72ad51974bb0fb741ae19ebb19f313e646c62
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677646"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUSıS uygulamanız için önceden oluşturulmuş etki alanı başvurusu
Bu başvuru [önceden oluşturulan etki alanları](luis-how-to-use-prebuilt-domains.md)hakkında bilgi sağlar. Bu, önceden oluşturulmuş amaç ve diğer varlıkların sunduğu varlıklardır.

[Özel etki alanları](luis-how-to-start-new-app.md), aksine, amaç ve modeller olmadan başlar. Önceden oluşturulmuş herhangi bir etki alanı amacını ve varlıklarını özel bir modele ekleyebilirsiniz.

## <a name="custom-domains-per-language"></a>Dil başına özel etki alanları

Aşağıdaki tabloda, şu anda desteklenen etki alanları özetlenmektedir. Ingilizce desteği genellikle diğerlerinden daha tamamdır. 

| Varlık türü       | EN-US      | ZH-CN   | DE    | GS     | ES    | BT      | PT-BR |  JP  |      DILI |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Takvim  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|İletişim  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-posta     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Notlar     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Yerler   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Restoran Antreservatıon  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Altyapı Hizmetleri      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Gü        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Önceden oluşturulmuş etki alanları ' de **desteklenmez** :

* Kanada Fransızcası
* Hintçe
* İspanyolca Meksika

## <a name="next-steps"></a>Sonraki adımlar

[Basit varlığı](reference-entity-simple.md)öğrenin. 