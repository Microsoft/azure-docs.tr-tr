---
title: "Hızlı başlangıç: BLOB depolama 'da depolanan konuşmayı tanıma-konuşma hizmeti"
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503979"
---
Bu hızlı başlangıçta, bir [SAS blobu](https://aka.ms/ignite2019/speech/placeholder)içinde depolanan konuşmayı tanımak Için [toplu iş dökümü REST API](../../../batch-transcription.md) kullanacaksınız. Birkaç önkoşulu karşıladıktan sonra, REST API kullanarak konuşmayı tanıma yalnızca birkaç adım sürer:
> [!div class="checklist"]
> * Konuşma konuşmaya başlamak için JSON isteğini konuşma hizmetine gönderin.
> * Döküm durumunu denetleyin.
> * Tamamlandığında, döküm sonuçlarını indirin.