---
title: Proje Kişilik Sohbeti nedir?
titlesuffix: Azure Cognitive Services
description: Bu makalede, bot’unuzun konuşma becerilerini geliştirmeye yönelik bulut tabanlı bir API olan Azure Projesi Personality Chat'e genel bir bakış sağlanır.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nitinme
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: 101f87230ee64e7f281f337189e18d09d93abed3
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257772"
---
# <a name="what-is-project-personality-chat"></a>Proje Kişilik Sohbeti nedir?

Proje Personality Chat ayrı, seçilmiş bir kişilikle birlikte havadan sudan konuşmaları işleyerek bot'unuzun konuşma becerilerini geliştirir. Personality Chat amaç sınıflandırıcılarını kullanarak yaygın havadan sudan sohbetlerin amaçlarını tanımlar ve kişilikle tutarlı yanıtlar oluşturur. Amaca ve güvenilirlik puanına dayanarak, bot düzenlenmiş editör tabanından en iyi yanıtı seçer veya derin sinir ağlarını (DNN) kullanarak gerçek zamanlı bir yanıt oluşturur. Varsayılan üç kişilikten birini seçebilirsiniz. Kişilik modeli seçilen kişiliğe en çok benzeyen yanıtları döndürür.

Özelleştirilebilir bir havadan sudan konuşma sorguları seti sağlanır. Bu, Microsoft Bot Framework SDK’sını kullanarak kolayca tümleştirilebilir. Bu SDK ile, bunları en baştan yazmaktan kurtularak hem zamandan, hem de paradan tasarruf edersiniz.

## <a name="getting-started-with-project-personality-chat"></a>Proje Personality Chat ile çalışmaya başlama

Proje Personality Chat laboratuvarları sayfasını ziyaret edebilir ve sağlanan tanıtımla sohbet edebilirsiniz. Hizmet kullanıma sunulduğunda da erken erişim istediğinde bulunabilirsiniz.
Bugün Microsoft Bot Framework SDK'sı aracılığıyla, özelleştirilebilir salt editör kitaplığını botlarınızla tümleştirebilirsiniz. <br>
[Lerinizi Bir bot 'ta Personality Chat tümleştirme](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Personality Chat kitaplığını deneme](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Sinir ağlarını kullanarak yanıt oluşturma

Personality Chat, genel konuşma desenlerini öğrenmek ve yanıt oluşturmak için derin öğrenme modellerini kullanır. Yanıt oluşturma modeli yinelenen bir sinir ağıdır (RNN). Bu model milyonlarca konuşmayla eğitilmiştir. İnsan etkileşimlerinin desenlerini anlar ve öğrenir. Öğrenilen tümce yapısı desenleriyle, yeni sorgular biçimlendirilir ve yanıtlar oluşturulur. Model bu yeni yanıtı oluştururken, sözcüklerin "yazma sözlüğünde" arama yapar ve yanıt olarak n. en iyi ilk sözcüğü seçer. Işın aramasını kullanarak, oluşturulan ilk sözcüklerin her biri için n. en iyi ikinci sözcükleri aramaya devam eder. Model "Cümle sonu" (EOS) sözcüğünü seçtiğinde yanıt tamamlanmış kabul edilir. Tüm yanıtlara sahip olunca, tüm yanıtın olasılık puanı temelinde n. en iyi yanıtları seçer.

Model doğru "yapıya" sahip, bağlamsal olarak uygun sıraları öğrenir. Örneğin, “Şimdi konuşmak ister misiniz?” sorusu makul bir yanıtın yapısıyla ilgili bir şeyler söyler: büyük olasılıkla “evet” veya “hayır” açıklamasıyla başlayacak ve birinci tekil şahıs kullanılacaktır. “Hayır” yanıtının kibar bir açıklama içerme olasılığı daha yüksektir. İşte bu şekilde devam eder.

Sistem, temel konuşma yapısı için bir dil modeli öğrenmeyi dener. Bu yapı sonuçların kısmen konu, kişilik gibi dış kısıtlamalardan etkilenmesine izin vermelidir.  Bu kısıtlamalar geniş desenlerden öğrenildiği için, Havadan sudan sohbet uygulamasına uyar (ama bunlarla sınırlı kalmaz).

![Yanıt oluşturma için sıralı model](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Kişilik modelleme

 Tüm veri odaklı konuşma modellerinde, sürekli olarak tutarlı bir “kişilik” verme güçlüğü yaşanır. KİŞİLİK, konuşma etkileşimleri boyunca deneyimlenen karakter olarak tanımlanır. Kişiliğin kimlik, dil davranışı ve etkileşim tarzı gibi öğelerden oluştuğu düşünülebilir. Personality Chat'in geçerli sürümünde, dil davranışına ve etkileşim tarzına odaklanılmıştır.

Bu model tek tek her konuşmacıyı bir vektör veya ekleme olarak gösterir. Yanıtlarının içeriğini ve tarzını etkileyen bilgileri konuşmacıdan kodlar. Ardından model, farklı konuşmacıların sağladığı konuşma içeriğini temel alarak konuşmacı gösterimlerini öğrenir. Benzer yanıtları veren konuşmacılar benzer eklemelere sahip olma, vektör alanında yakın konumlarda yer alma eğilimindedir. Bu şekilde, vektör alanında yakında bulunan konuşmacıların eğitim verileri konuşmacı modelinin genelleştirme becerisini artırmaya yardımcı olur. Model, vektör alanında benzer gösterimleri olan konuşmacıları etkili bir şekilde kümelendirerek, "kişilik kimliği" olan bir kişilik kümesi oluşturur.

Varsayılan kişilikler için, öznitelikler ve düzenlenmiş yanıtlar kullanılarak en yakın eşleşmeye sahip konuşmacı kümesi bulunur. Varsayılan kimliklerin her biri için Kişilik Kimliği olarak o küme seçilir. Her türlü kişilik için bir dizi bot/marka yanıtı alınarak sürekli özelleştirme yapılabilir. Bundan sonra, dilbilimsel yanıt davranışı ve diğer ana özellikler gibi söz konusu bireyin kimliğine doğru bir şekilde öykünen konuşmalar yapılır.

![Konuşmacı kümelerini kullanarak kişilik modelleme](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Havadan sudan konuşmanın amacını anlama

Personality Chat'in havadan sudan konuşmanın amaçlarına yanıt verebilmesi için amaç sınıflandırıcıları vardır. Bu sınıflandırıcılar görev veya bilgi sorgularını engellemez. Üst düzey bir sohbet sınıflandırıcısı, sorgunun havadan sudan konuşma mı yoksa çene çalma amaçlı mı olduğunu belirler. Sözcük ve anlam temelli sınıflandırıcılar kullanarak ve onların puanlarını birleştirerek karar verir. Bu amaçlar konuşma verileri (olumlu amaç örnekleri olarak) ve arama/görev sorguları (olumsuz amaç örnekleri olarak) kullanılarak eğitilir.

Havadan sudan konuşmanın alt sınıflarını tanımlayıp hizmetin yanıt verdiği havadan sudan konuşma türlerini (örneğin, karşılamalar, övgüler, görüşler, vb.) sınırlandırmak için başka alt amaç sınıflandırıcıları kullanılır. Bu derin öğrenme sınıflandırıcıları, Evrişimli Derin Yapı Semantik Modeli'ni (CDSSM) kullanarak tüm sorguları vektörlere dönüştürür. Bunlar, alt amaçlar için on binlerce düzenlenmiş sorgu kullanılarak eğitilir. Ardından sınıflandırıcı vektör alanında en yakın eşleştirmeyi bularak sorguyu mevcut, tanımlanmış amaç sınıflarıyla eşleştirir.

Zo gibi akıllı aracılardan gelen bilgi temelinde, istenmeyen yanıtların önlenmesine yardımcı olmak için bir dizi denetim ayarlanmıştır. Varsayılan olarak, Proje Personality Chat yalnızca tanınan kullanıcı amaçlarına yanıt vermek için ayarlanır. Proje Personality Chat'in sizin durumlarınıza uyup uymadığını test etmek isteyebilirsiniz. Daha fazla eğitilmesi gereken bir nokta görürseniz, geri bildiriminizi almak isteriz.
