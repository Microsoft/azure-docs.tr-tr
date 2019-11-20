---
title: Conversation Learner varsayılan yapılandırma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Varsayılan Conversation Learner yapılandırması hakkında bilgi edinin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: aaef6f5498e5a8da65d1c829feae8b3e85dba0fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705292"
---
# <a name="default-values-and-boundaries"></a>Varsayılan değerler ve sınırlar

Bu belgede, Conversation Learner varsayılan yapılandırması ve anahtar hizmeti sınırları açıklanmaktadır.

## <a name="default-configuration"></a>Varsayılan yapılandırma

Parametre | Varsayılan değer
--- | --- 
Varsayılan oturum zaman aşımı | 30 dakika

## <a name="boundaries"></a>Sınırlar

Parametre | Sınır
--- | --- 
Yazma API 'SI, aylık HTTP çağrısı sayısı üst sınırı | 5 DK
Yazma API 'SI, saniye başına en fazla HTTP çağrısı | 25
Oturum API 'SI, aylık HTTP çağrısı sayısı üst sınırı | 500K
Oturum API 'SI, saniye başına en fazla HTTP çağrısı | 10
Her model için en fazla özel (programlı olmayan) varlık sayısı | Bkz. [lusıs sınırları belgesi](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); uygulamada, gerçek sayı biraz daha küçük olabilir
Model başına en fazla önceden oluşturulmuş varlık sayısı | Bkz. [lusıs sınırları belgesi](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Model başına en fazla varlık sayısı (Toplam) | 100
Model başına en fazla eylem sayısı | 32
Model başına maksimum tren iletişim kutusu sayısı | 1000
Eğitim başına en fazla kullanıcı sayısı iletişim kutusu | 100
Model başına en fazla günlük iletişim kutusu sayısı | Ön ayarlı sınır yoktur, ancak günlük iletişimleri atılmadan önce yalnızca sabit bir süre için tutulur.  Ayrıca, Conversation Learner Kullanıcı arabirimi aynı anda 100 günlük iletişim kutusunu gösterecektir. 
Kullanıcı başına en fazla model sayısı | Ön ayarlı sınır yok
Ardışık bekleme olmayan en fazla eylem sayısı | 5 (*)

(*) 5 ardışık bekleme olmayan eylemlerden sonra, tüm bekleme olmayan eylemler maskelenir ve Conversation Learner kullanılabilir bekleme işlemleri arasından seçim yapılır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Conversation Learner kullanmaya başlayın](./quickstart.md)
