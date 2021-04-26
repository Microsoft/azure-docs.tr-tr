---
title: Çeviri özelleştirmesi-çevirmen
titleSuffix: Azure Cognitive Services
description: Tercih ettiğiniz terminolojiyi ve stili kullanarak kendi makine çevirisi sisteminizi oluşturmak için Microsoft Translator hub 'ını kullanın.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898045"
---
# <a name="customize-your-text-translations"></a>Metin çevirilerinizi özelleştirin

Özel çevirici, çevirmen hizmetinin, kullanıcıların, çeviriciyi (yalnızca sürüm 3) kullanarak metin çevirirken Microsoft Translator 'ın gelişmiş sinir makine çevirisini özelleştirmesini sağlayan, Translator hizmetinin bir özelliğidir.

Özelliği, bilişsel [Hizmetler konuşmayla](../speech-service/index.yml)kullanıldığında konuşma çevirisini özelleştirmek için de kullanılabilir.

## <a name="custom-translator"></a>Özel Çevirmen

Özel çevirmenle, kendi iş ve sektöründe kullanılan terminolojiyi anlayan sinir çeviri sistemleri oluşturabilirsiniz. Özelleştirilmiş çeviri sistemi daha sonra mevcut uygulamalar, iş akışları ve Web siteleri ile tümleşir.

### <a name="how-does-it-work"></a>Nasıl çalışır?

Daha önce çevrilmiş belgelerinizi (sızıntı, Web sayfaları, belgeler vb.) kullanarak, bir standart çeviri sisteminden daha iyi bir şekilde, etki alanına özgü terminolojiyi ve stilinizi yansıtan bir çeviri sistemi oluşturun. Kullanıcılar TMX, XLıFF, TXT, DOCX ve XLSX belgelerini karşıya yükleyebilir.  

Sistem ayrıca belge düzeyinde paralel olan ancak tümce düzeyinde henüz hizalanmayan verileri de kabul eder. Kullanıcılar birden çok dilde aynı içeriğin sürümlerine erişebiliyorsa, ancak ayrı belgeler özel çeviricisinde, belgelerde otomatik olarak tümceler eşleştirebilir.  Sistem Ayrıca, çevirileri geliştirmek üzere paralel eğitim verilerini tamamlamak için her iki dilde de monolingual verileri kullanabilir.

Özelleştirilmiş sistem daha sonra Kategori parametresi kullanılarak bir çevirmene çağrı yoluyla kullanılabilir.

Uygun tür ve eğitim verisi miktarı verildiğinde, 5 ile 10 arasında kazanç beklenmez veya özel çevirici kullanarak çeviri kalitesine daha fazla BLEU noktası gelir.

Kullanılabilir verilere göre çeşitli özelleştirme düzeyleri hakkında daha fazla ayrıntı için [özel çevirmen kullanıcı kılavuzunda](./custom-translator/overview.md)bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özelleştirilmiş bir dil sistemini özel çevirici kullanarak ayarlama](./custom-translator/overview.md)
