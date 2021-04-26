---
title: Import-Soru-Cevap Oluşturma için desteklenen URL türleri
description: URL türlerinin QnA çiftlerini içeri ve dışarı aktarmak için nasıl kullanılacağını anlayın.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776706"
---
# <a name="urls-supported-for-importing-documents"></a>Belgeleri içeri aktarmak için desteklenen URL 'Ler

URL türlerinin QnA çiftlerini içeri ve dışarı aktarmak için nasıl kullanılacağını anlayın.

## <a name="faq-urls"></a>SSS URL 'Leri

Soru-Cevap Oluşturma, 3 farklı formda SSS Web sayfalarını destekleyebilir:

* Düz SSS sayfaları
* Bağlantılarla ilgili SSS sayfaları
* Konular giriş sayfası ile SSS sayfaları

### <a name="plain-faq-pages"></a>Düz SSS sayfaları

Bu en yaygın SSS sayfası türüdür ve burada, yanıtlar aynı sayfada soruları hemen izler.

Aşağıda, düz bir SSS sayfasına örnek verilmiştir:

![Bilgi Bankası için düz SSS sayfası örneği](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Bağlantılarla ilgili SSS sayfaları

Bu tür bir SSS sayfasında, sorular birlikte toplanır ve aynı sayfanın farklı bölümlerinde ya da farklı sayfalarda bulunan yanıtlara bağlanır.

Aşağıda, aynı sayfada bulunan bölümlerde bulunan bağlantılarla ilgili bir SSS sayfası örneği verilmiştir:

 ![Bilgi Bankası için bölüm bağlantısı SSS sayfası örneği](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Üst konular sayfası alt yanıt sayfalarına bağlantılar

Bu tür bir SSS, her konunun farklı bir sayfada ilgili soru ve yanıt kümesiyle bağlantılı olduğu konular sayfasına sahiptir. Soru-Cevap Oluşturma, karşılık gelen soruların & yanıtları ayıklamak için tüm bağlantılı sayfalara gezinir.

Aşağıda, farklı sayfalardaki SSS bölümlerinin bağlantılarıyla ilgili konular sayfasına bir örnek verilmiştir.

 ![Ayrıntılı bağlantı SSS sayfası bir Bilgi Bankası için örnek](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Destek URL 'Leri

Soru-Cevap Oluşturma, belirli bir görevin nasıl gerçekleştirileceğini, belirli bir sorunun nasıl tanılanacağı ve çözümleneceğini ve belirli bir işlem için en iyi uygulamaların hangileri olduğunu betimleyen web makaleleri gibi yarı yapılandırılmış destek Web sayfalarını işleyebilir. Ayıklama, hiyerarşik başlıkları olan açık bir yapıya sahip içerikte en iyi şekilde kullanılır.

> [!NOTE]
> Destek makaleleri için ayıklama yeni bir özelliktir ve erken aşamalarındayken. İyi yapılandırılmış olan ve karmaşık üstbilgiler/altbilgiler içermeyen basit sayfalar için en iyi şekilde kullanılır.

![Soru-Cevap Oluşturma, açık bir yapının hiyerarşik başlıklarla sunulduğu yarı yapılandırılmış Web sayfalarından ayıklanmasını destekler](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)