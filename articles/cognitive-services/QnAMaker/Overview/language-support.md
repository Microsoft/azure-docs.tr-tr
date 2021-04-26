---
title: Dil desteği-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Bilgi tabanınız için Soru-Cevap Oluşturma tarafından desteklenen bir kültürün, doğal dillerin bir listesi. Aynı bilgi bankasındaki dilleri karıştırmayın.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97605068"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Soru-Cevap Oluşturma bir kaynak ve bilgi tabanı için dil desteği

Bu makalede, Soru-Cevap Oluşturma kaynakları ve bilgi tabanları için dil desteği seçenekleri açıklanmaktadır. 

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Kaynak için ilk Bilgi Bankası oluşturduğunuzda hizmetin dili seçilir. Kaynaktaki tüm ek bilgi tabanları aynı dilde olmalıdır. 

Dil, Kullanıcı sorgularına yanıt olarak Soru-Cevap Oluşturma sonuçların uygunluğunu belirler. Soru-Cevap Oluşturma kaynağı ve bu kaynaktaki tüm bilgi tabanları, tek bir dili destekler. Tek dil, bir sorgunun en iyi yanıt sonuçlarını sağlamak için gereklidir.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Yönetilen Soru-Cevap Oluşturma, tek tek Bilgi Bankası düzeyinde dil ayarları yapma seçeneğini görürsünüz. Bu ayar yalnızca hizmetin Bilgi Bankası ile etkinleştirilebilir. Ayar yapıldıktan sonra hizmet için dil ayarları değiştirilemez. 

Bilgi tabanına özel olarak dil ayarları ' nı seçerseniz, hizmetin kendisinde farklı dillerin bilgi tabanları oluşturma izni verilir. 

---

## <a name="single-language-per-resource"></a>Kaynak başına tek dil

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Aşağıdaki topluluklara bir göz atın:

* Bir Soru-Cevap Oluşturma Hizmeti ve tüm bilgi tabanları, yalnızca bir dili destekler.
* Dil, hizmetin ilk bilgi tabanı oluşturulduğunda açıkça ayarlanır.
* Dil, Bilgi Bankası oluşturulduğunda eklenen dosyalardan ve URL 'Lerden belirlenir.
* Hizmet, hizmetteki diğer bilgi tabanları için değiştirilemez.
* Dil, bir sorguya en iyi yanıtı oluşturmak için Bilişsel Arama hizmeti (Ranker #1) ve Soru-Cevap Oluşturma Hizmeti (Ranker #2) tarafından kullanılır.

# <a name="qnamaker-managed-preview"></a>[Yönetilen QnAMaker (Önizleme)](#tab/v2)
![Soru-Cevap Oluşturma yönetilen dil ayarı](../media/language-support/language-setting-managed.png)

**Bilgi Bankası başına dil ayarını etkinleştirmek için onay kutusunu işaretlemezseniz**, aşağıdakileri göz önünde bulundurun: 
* Bir Soru-Cevap Oluşturma Hizmeti ve tüm bilgi tabanları, yalnızca bir dili destekleyecektir.
* Hizmetin ilk bilgi tabanı oluşturulduğunda dil açıkça ayarlanır
* Dil, Bilgi Bankası oluşturulduğunda eklenen dosyalardan ve URL 'lerden belirlenir
* Hizmet, hizmette diğer bilgi tabanları için değiştirilemez
* Dil, bir sorguya en iyi yanıtı oluşturmak için Bilişsel Arama hizmeti (Ranker #1) ve Soru-Cevap Oluşturma Hizmeti (Ranker #2) tarafından kullanılır

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Tek bir Soru-Cevap Oluşturma kaynakta birden çok dili destekleme

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)
Bu işlev, geçerli genel kullanıma açık (GA) kararlı sürümümüzde desteklenmez. Bu işlevi sınamak için Soru-Cevap Oluşturma yönetilmek üzere kullanıma alma. 

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
* Hizmetinize ilk Bilgi Bankası oluştururken, Bilgi Bankası başına dil ayarını etkinleştirme seçeneğiniz vardır. Tek bir hizmette bulunan farklı dillere ait bilgi tabanları oluşturmak için onay kutusunu seçin.
* Dil ayarı seçeneği, ilk Bilgi Bankası oluşturulduktan sonra hizmet için değiştirilemez.
* Her bilgi tabanına özgü dil ayarlarını etkinleştirirseniz, hizmet için tek bir test dizinine sahip olmak yerine, her Bilgi Bankası için bir test dizinine sahip olursunuz. 

![Soru-Cevap Oluşturma yönetilen dil ayarı](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Tek bir bilgi tabanında birden çok dili destekleme

Birkaç dil içeren bir Bilgi Bankası sistemini desteklemeniz gerekiyorsa şunları yapabilirsiniz:

* Soruyu bilgi tabanınızı göndermeden önce bir soruyu tek bir dile çevirmek için [Translator hizmetini](../../translator/translator-info-overview.md) kullanın. Bu, tek bir dilin kalitesine ve diğer soruların ve yanıtların kalitesine odaklanabilmenizi sağlar.
* Her dil için bu kaynak içindeki bir Soru-Cevap Oluşturma kaynağı ve Bilgi Bankası oluşturun. Bu, ayrı alternatif soruları yönetmenize ve her bir dil için daha fazla nuılmış metin elde etmenizi sağlar. Bu, çok daha fazla esneklik sağlar ancak sorular veya yanıtlar tüm dillerde değiştiğinde çok daha yüksek bir bakım maliyeti gerektirir.


## <a name="languages-supported"></a>Desteklenen diller

Aşağıdaki liste, bir Soru-Cevap Oluşturma kaynağı için desteklenen dilleri içerir. 

| Dil |
|--|
| Arapça |
| Ermenice |
| Bangla |
| Baskça |
| Bulgarca |
| Katalanca |
| Chinese_Simplified |
| Chinese_Traditional |
| Hırvatça |
| Çekçe |
| Danca |
| Felemenkçe |
| İngilizce |
| Estonya Dili |
| Fince |
| Fransızca |
| Galiçya Dili |
| Almanca |
| Yunanca |
| Gucerat dili |
| İbranice |
| Hintçe |
| Macarca |
| İzlandaca |
| Endonezce |
| İrlandaca |
| İtalyanca |
| Japonca |
| Kannada dili |
| Korece |
| Letonca |
| Litvanca |
| Malayalam dili |
| Malayca |
| Norveççe |
| Lehçe |
| Portekizce |
| Pencap dili |
| Rumence |
| Rusça |
| Serbian_Cyrillic |
| Serbian_Latin |
| Slovakça |
| Slovence |
| İspanyolca |
| İsveççe |
| Tamil dili |
| Telugu dili |
| Tayca |
| Türkçe |
| Ukraynaca |
| Urduca |
| Vietnamca |

## <a name="query-matching-and-relevance"></a>Sorgu eşleştirme ve ilgisi
Soru-Cevap Oluşturma, sonuçları sağlamak için [Azure bilişsel arama dil Çözümleyicileri](/rest/api/searchservice/language-support) 'ne bağlıdır.

Azure bilişsel arama özellikleri desteklenen diller için eşit olsa da, soru-cevap oluşturma Azure Arama sonuçlarının üzerinde yer alan ek bir derecelendiricisini vardır. Bu derecelendiricisini modelinde, aşağıdaki dillerde bazı özel semantik ve Word tabanlı özellikleri kullanırız.

|Ek derecelendiricisini ile diller|
|--|
|Çince|
|Çekçe|
|Felemenkçe|
|İngilizce|
|Fransızca|
|Almanca|
|Macarca|
|İtalyanca|
|Japonca|
|Korece|
|Lehçe|
|Portekizce|
|İspanyolca|
|İsveççe|

Bu ek derecelendirme, Soru-Cevap Oluşturma Ranker 'ın iç çalışmadır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dil seçimi](../index.yml)