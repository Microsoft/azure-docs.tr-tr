---
title: Conversation Learner-Microsoft bilişsel hizmetler ile öğretme | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner öğretme hakkında bilgi edinin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: a18d4c31da4ffeefebd4bda9aa441fdfec062be9
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705260"
---
# <a name="how-to-teach-with-conversation-learner"></a>Konuşma Öğrenici ile öğretme 

Bu belgede Conversation Learner hangi sinyallerin farkında olduğu açıklanmakta ve nasıl öğrendiği açıklanmaktadır.  

Öğretme iki ayrı adım halinde parçalanamaz: varlık ayıklama ve eylem seçimi.

## <a name="entity-extraction"></a>Varlık ayıklama

Kapsıyorsa Conversation Learner, varlık ayıklama için [Luo](https://www.luis.ai) 'yı kullanır.  LUO hakkında bilginiz varsa, bu deneyim Conversation Learner varlık ayıklama için geçerlidir.

Varlık ayıklama modelleri, bir Kullanıcı içindeki *içerik* ve *bağlamdan* haberdar edilir.  Örneğin, "Seattle" sözcüğünün "Seattle 'daki Hava durumu nedir" gibi tek bir şehirde şehir olarak etiketlenmesi halinde, varlık ayıklama aynı içeriği ("Seattle"), "Seattle 'ın popülasyon" gibi başka bir şekilde (örneğin, söyleyceler çok farklıdır.  Buna karşılık "FRANCIS", "Francı 'lar ile Toplantı zamanla" adında bir ad olarak tanınmışsa, daha önce görülmeyen yeni bir ad, "bir kez deneme ile toplantı ayarlama" gibi benzer bir bağlamda tanınabilmesi.  Makine öğrenimi, eğitim örneklerine bağlı olarak içeriğe, içeriğe veya her ikisine de katılmayacalarını algılar.

Bu noktada, varlık ayıklama yalnızca geçerli utterance 'in içeriğini biliyor.  Eylem seçiminden farklı olarak (aşağıda), önceki sistem, önceki Kullanıcı veya daha önce tanınan varlıklar gibi iletişim kutusu geçmişinden haberdar değildir.  Sonuç olarak, varlık ayıklama davranışı tüm sonuçlar genelinde "paylaşılır".  Örneğin, Kullanıcı, "Apple" ın "meyve" varlık türü olarak etiketlenmiş "Apple" varsa, varlık ayıklama modeli bu söylenişi 'in ("Apple") her zaman "meyve" olarak etiketlenmiş "Apple" öğesine sahip olmasını bekler.

Varlık ayıklama beklendiği gibi davranmıyorsa, olası düzeltmeler aşağıda verilmiştir:

- Deneyebileceğiniz ilk şey, daha fazla eğitim örneği eklemektir; özellikle de tipik varlık bağlamını (çevreleyen sözcükler) veya özel durumları açığa çıkarmayan örnekleri
- Uygunsa bir eyleme "beklenen varlık" özelliği eklemeyi düşünün.  Daha fazla ayrıntı için beklenen varlıklar hakkında öğreticiye bakın.
- Kodu kullanarak varlıkları ayıklamak `EntityExtractionCallback` için el ile işleme eklemek mümkün olsa da, bu en az önerilen yaklaşımdır çünkü sistem geliştirmeleriniz olarak makine öğrenimi geliştirmelerinden faydalanır.

## <a name="action-selection"></a>Eylem seçimi

Eylem seçimi, tüm konuşma geçmişini giriş olarak geçen bir yinelenen sinir ağı kullanır.  Bu nedenle, eylem seçimi önceki Kullanıcı aradıkları, varlık değerleri ve sistem dıkları hakkında farkında olan durum bilgisi olmayan bir işlemdir.  

Bazı sinyaller, öğrenme süreci tarafından doğal olarak tercih edilir.  Diğer bir deyişle, Conversation Learner "daha tercih edilen" sinyalleri kullanarak bir eylem seçimi kararı Açıklayabiliyorsanız, bu, olur; Bu işlem, "daha az tercih edilen" sinyalleri kullanacaktır.

İşte Conversation Learner tüm sinyalleri gösteren ve bunların eylem seçimi tarafından kullanıldığı bir tablo.  Kullanıcı utsları 'ndaki sözcük sırasının yok sayıldığını unutmayın.

Sinyalinin | Tercih (1 = tercih edilen) | Notlar
--- | --- | --- 
Önceki sırayla sistem eylemi | 1\. | 
Geçerli sırayla bulunan varlıklar | 1\. | 
Bunun ilk kez olup olmadığı | 1 |
Geçerli Kullanıcı boyutlandırmasındaki kelimelerin tam eşleşmesi | 2 | 
Benzer-geçerli kullanıcı aramasındaki anlamlı sözcükler | 3 | 
Önceki sırayla sistem eylemleri | 4 |
' De mevcut varlıklar, geçerli dönüşten önce sırasıyla | 4 | 
Geçerli kılmadan önce Kullanıcı utsliği | 5 | 

> [!NOTE]
> Eylem seçimi, sistem eylemlerinin içeriğini almaz--metin, kart içeriği veya API adı ya da davranış--yalnızca sistem eyleminin kimliği.  Sonuç olarak, bir eylemin içeriğinin değiştirilmesi, eylem seçim modelinin davranışını değiştirmez.
>
> Ayrıca, varlıkların İçindekiler/değerleri kullanılmaz--yalnızca kendi varlığı/yokluğunda değildir.

Eylem seçimi beklendiği gibi davranmıyorsa, olası düzeltmeler aşağıda verilmiştir:

- Daha fazla eğitme iletişim kutusu ekleyin, özellikle de eylem seçimine dikkat edilmesi gereken sinyalleri gösteren iletişim kutuları.  Örneğin, eylem seçimi bir veya başka bir sinyal tercih etmelidir, tercih edilen sinyalin aynı durumda olduğunu ve diğer sinyalleri benzer şekilde gösteren örneklere sahip olmanız gerekir.  Bazı diziler, öğrenmek için bir eğitim iletişim kutusu alabilir.
- Eylem tanımlarına "gerekli" ve "ayırt edici" varlıkları ekleyin.  Bu, eylemler kullanılabilir olduğunda kısıtlar ve iş kuralları ve bazı yaygın algılama desenleri için yararlı olabilir. 

## <a name="updates-to-models"></a>Modellerdeki güncelleştirmeler

Kullanıcı arabiriminde bir varlık, eylem veya tren iletişim kutusu eklediğinizde veya düzenlediğinizde, bu, hem varlık ayıklama modelini hem de eylem seçim modelini yeniden eğitme isteği oluşturur.  Bu istek bir kuyruğa konur ve yeniden eğitim zaman uyumsuz olarak yapılır.  Yeni bir model kullanılabilir olduğunda, varlık ayıklama ve eylem seçimi için bu noktadan itibaren kullanılır.  Bu yeniden eğitim süreci genellikle 5 saniye boyunca sürer, ancak model karmaşıksa veya eğitim hizmetindeki yükün yüksek olması durumunda daha uzun bir süre olabilir.

Eğitim zaman uyumsuz olarak yapıldığından, yaptığınız düzenlemeler hemen yansıtılmıyor olabilir.  Varlık ayıklama veya eylem seçimi, son 5-10 saniye içinde yaptığınız değişikliklere göre beklendiği gibi davranmıyorsa, bu durum olabilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Varsayılan değerler ve sınırlar](./cl-values-and-boundaries.md)
