---
title: Yapay zeka (AI)
description: LUSıS, tanımladığınız şemaya göre verilerinize yönelik dil anlama sağlamak için yapay zeka (AI) kullanır.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021380"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Language Understanding (LUSıS) içinde yapay zeka

LUSıS, tanımladığınız şemaya göre verilerinize doğal dil anlama (NLU) sağlamak için yapay zeka (AI) kullanır.

## <a name="natural-language-processing-nlp"></a>Doğal dil işleme (NLP)

Doğal Language Understanding (NLU), doğal dil Işleme (NLP) için belirli bir alt konudur.

Doğal dil işleme, metin verilerinin her türlü işlemeyi gerçekleştiren daha geniş bir kavramdır; bu, şöyle şeyler içerir:

* Simgeleştirme
* Konuşma (POS) etiketleme bölümü
* Kesimleme
* Morphological Analizi
* Anlamsal benzerlik
* Konuşma
* Çeviri

## <a name="natural-language-processing-in-luis"></a>LUSıS 'de doğal dil işleme

Doğal dil işleme, LUSıS uygulamanız için aşağıdaki yollarla kullanılabilir:
* [Doğal dil anlama](#natural-language-processing-nlp) (lusıs)
* LUŞU 'da yapılandırılabilir NLP yönleri:
    * [Simgeleştirme](luis-language-support.md#tokenization)
    * Morphology-Aksanlar, noktalama işaretleri ve Word Forms [API ayarları](luis-reference-application-settings.md)
* Şu gibi diğer bilişsel [Hizmetler](../what-are-cognitive-services.md) tarafından sunulan sorgu utterliğini ön veya sonrası işleme:
    * [Çeviri](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>Doğal dil anlama (NLU)

NLU bir dil bildirimini, kullanıcılarınızı doğal olarak anlamanıza olanak tanıyan bir gösterimle _dönüştürebilme_ yeteneğidir. Doğal dil anlama çok zorlayıcı bir sorun olduğunu ve bir _AI-Hard_ sorunu olarak tanımlanır.

LUSıS, amaç ve ayıklama için odaklanmak üzere tasarlanmıştır, bu da şunları belirleyebilmelidir:
* Kullanıcının istediği
* Ne konuşuyor?

LUSıS, örneklerde açık tanımlama olmadan semantik benzerlik gibi daha geniş _NLP_ yönlerini çok az veya hiç bilgisine sahip değil. Örneğin, aşağıdaki belirteçler (kelimeler), girilen örneklerde benzer bağlamlarda kullanılana kadar üç farklı nesnelerdir:

* satıcıdan
* almanın
* satın

## <a name="next-steps"></a>Sonraki adımlar

* Bir LUO uygulamasının [geliştirme yaşam döngüsü](luis-concept-app-iteration.md) hakkında bilgi edinin