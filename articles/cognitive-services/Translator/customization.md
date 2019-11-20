---
title: Çeviri özelleştirmesi-Translator Metin Çevirisi API'si
titleSuffix: Azure Cognitive Services
description: Tercih ettiğiniz terminolojiyi ve stili kullanarak kendi makine çevirisi sisteminizi oluşturmak için Microsoft Translator hub 'ını kullanın.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257615"
---
# <a name="customize-your-text-translations"></a>Metin çevirilerinizi özelleştirin

Microsoft özel çevirmeni, Microsoft Translator hizmetinin, kullanıcıların Translator Metin Çevirisi API'si (yalnızca sürüm 3) kullanarak metin çevirirken Microsoft Translator 'ın gelişmiş sinir makine çevirisini özelleştirmesini sağlayan bir özelliktir.

Özelliği, bilişsel [Hizmetler konuşmayla](https://docs.microsoft.com/azure/cognitive-services/speech-service/)kullanıldığında konuşma çevirisini özelleştirmek için de kullanılabilir.

## <a name="custom-translator"></a>Özel Çevirmen

Özel çevirmenle, kendi iş ve sektöründe kullanılan terminolojiyi anlayan sinir çeviri sistemleri oluşturabilirsiniz. Özelleştirilmiş çeviri sistemi daha sonra mevcut uygulamalar, iş akışları ve Web siteleri ile tümleşir.

### <a name="how-does-it-work"></a>Nasıl çalışır?

Daha önce çevrilmiş belgelerinizi (sızıntı, Web sayfaları, belgeler vb.) kullanarak, bir standart çeviri sisteminden daha iyi bir şekilde, etki alanına özgü terminolojiyi ve stilinizi yansıtan bir çeviri sistemi oluşturun. Kullanıcılar TMX, XLıFF, TXT, DOCX ve XLSX belgelerini karşıya yükleyebilir.  

Sistem ayrıca belge düzeyinde paralel olan ancak tümce düzeyinde henüz hizalanmayan verileri de kabul eder. Kullanıcılar birden çok dilde aynı içeriğin sürümlerine erişebiliyorsa, ancak ayrı belgeler özel çeviricisinde, belgelerde otomatik olarak tümceler eşleştirebilir.  Sistem Ayrıca, çevirileri geliştirmek üzere paralel eğitim verilerini tamamlamak için her iki dilde de monolingual verileri kullanabilir.

Özelleştirilmiş sistem daha sonra Kategori parametresi kullanılarak Microsoft Translator Metin Çevirisi API'si düzenli bir çağrısıyla kullanılabilir.

Uygun tür ve eğitim verisi miktarı verildiğinde, 5 ile 10 arasında kazanç beklenmez veya özel çevirici kullanarak çeviri kalitesine daha fazla BLEU noktası gelir.

Kullanılabilir verilere göre çeşitli özelleştirme düzeyleri hakkında daha fazla ayrıntı için [özel çevirmen kullanıcı kılavuzunda](https://aka.ms/CustomTranslatorDocs)bulabilirsiniz.


## <a name="microsoft-translator-hub"></a>Microsoft Translator hub 'ı

> [!NOTE]
> Eski Microsoft Translator hub 'ı 17 Mayıs 2019 tarihinde kullanımdan kaldırılacaktır. [Önemli geçiş bilgilerini ve tarihlerini görüntüleyin](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Özel Translator Hub karşılaştırması

|   | **Hub** | **Özel çevirici**|
|:-----|:----:|:----:|
|Özelleştirme özelliği durumu   | Genel Erişilebilirlik  | Genel Erişilebilirlik |
| Metin çevirisi API'si sürümü  | Yalnızca v2   | Yalnızca v3 |
| SMT özelleştirme | Evet   | Hayır |
| NMT özelleştirme | Hayır    | Evet |
| Yeni birleşik konuşma Hizmetleri özelleştirme | Hayır    | Evet |
| [Izleme yok](https://www.aka.ms/notrace) | Evet  | Evet |

## <a name="collaborative-translations-framework"></a>İşbirliğine dayalı Çeviriler çerçevesi

> [!NOTE]
> 1 Şubat 2018 itibariyle, AddTranslation () ve AddTranslationArray () artık Translator Metin Çevirisi API'si V 2.0 ile kullanılamaz. Bu yöntemler başarısız olur ve hiçbir şey yazılmaz. Translator Metin Çevirisi API'si V 3.0 bu yöntemleri desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özelleştirilmiş bir dil sistemini özel çevirici kullanarak ayarlama](https://aka.ms/CustomTranslatorDocs)
