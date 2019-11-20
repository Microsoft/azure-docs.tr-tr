---
title: Özellik değerlendirmesi-kişiselleştirici
titleSuffix: Azure Cognitive Services
description: Kişiselleştirici kaynağında Azure portal bir değerlendirme çalıştırdığınızda, kişiselleştirmede bağlam ve eylemlerin hangi özellikleri modeli bir şekilde etkileyen hakkında bilgi sağlanır.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242425"
---
# <a name="feature-evaluation"></a>Özellik değerlendirmesi

Kişiselleştirici kaynağında [Azure Portal](https://portal.azure.com)bir değerlendirme çalıştırdığınızda, kişiselleştirmede bağlam ve eylemlerin hangi özellikleri modeli bir şekilde etkileyen hakkında bilgi sağlanır. 

Bu, aşağıdakileri yapmak için yararlıdır:

* Modelde daha önemli olan özelliklerden yararlanmak için kullanabileceğiniz ek özellikleri düşünün.
* Hangi özelliklerin önemli olmadığını görün ve büyük olasılıkla bunları kaldırabilir veya kullanımı neyin etkilediğini daha fazla analiz edebilirsiniz.
* Kataloğa göre yeni içerik veya ürünler hakkında düzenleme veya seçkin takımlar hakkında rehberlik sağlar.
* Kişiselleştiriciye Özellikler gönderilirken gerçekleşen yaygın sorunları ve hataları giderin.

Daha önemli özelliklerin modelde daha güçlü ağırlıkları vardır. Bu özellikler daha güçlü bir ağırlığa sahip olduğundan, kişiselleştirici daha yüksek bir şekilde elde edildiğinde mevcut olmaya eğilimlidir.

## <a name="getting-feature-importance-evaluation"></a>Özellik önem değerlendirmesi alınıyor

Özellik önem sonuçlarını görmek için bir değerlendirme çalıştırmanız gerekir. Değerlendirme, değerlendirme süresi boyunca gözlemlenen özellik adlarına dayanarak, insanlarca okunabilir Özellik etiketleri oluşturur.

Özellik önemi hakkındaki sonuç bilgileri, geçerli kişiselleştirici çevrimiçi modelini temsil eder. Değerlendirme, geçerli çevrimiçi öğrenme ilkesiyle değerlendirme sırasında yapılan tüm eğitime başladıktan sonra değerlendirme döneminin bitiş tarihinde kaydedilen modelin Özellik önemini analiz eder. 

Özellik önem sonuçları, değerlendirme sırasında sınanan veya oluşturulan diğer ilkeleri ve modelleri temsil etmez.  Değerlendirme, değerlendirme döneminin sonundan sonra Kişiselleştiriciye gönderilen özellikleri içermez.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Özellik önem değerlendirmesi nasıl yorumlanır

Kişiselleştirici, benzer öneme sahip özelliklerin "grupları" oluşturarak özellikleri değerlendirir. Tek bir grup diğerlerine kıyasla daha güçlü bir önem derecesine sahip olabilir, ancak grup içinde özellik sıralaması alfabetik olarak belirlenir.

Her özellik hakkındaki bilgiler şunları içerir:

* Özelliğin bağlam veya eylemlerden mi geldiği.
* Özellik anahtarı ve değeri.

Örneğin, bir dondurma mağaza siparişi uygulaması, çok önemli bir özellik olarak "Context. Hava durumu: Hot" i görebilir.

Kişiselleştirmede, birlikte çalışırken, daha yüksek bir reşler üreten özelliklerin bağıntıları görüntülenir.

Örneğin, "Context. Hava durumu: Hot *with* Action. MenuItem: Icecream" ve "Context. Hava durumu: soğuk *with* Action. MenuItem: warmtea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Özellik değerlendirmesini temel alarak gerçekleştirebileceğiniz eylemler

### <a name="imagine-additional-features-you-could-use"></a>Kullanabileceğiniz ek özellikleri düşünün

Modeldeki daha önemli özelliklerden yararlanın. Örneğin, bir video mobil uygulamasında "Context. Mobilepili: Low" görürseniz, bağlantı türünün müşterilerin bir video klibini diğeri üzerinde görmeyi seçebildiğini düşünebilir, ardından uygulamanıza bağlantı türü ve bant genişliği hakkında özellikler ekleyebilirsiniz.

### <a name="see-what-features-are-not-important"></a>Hangi özelliklerin önemli olmadığını görün

Büyük olasılıkla önemli olmayan özellikleri kaldırabilir veya kullanımı neyin etkileyebileceğini daha fazla analiz edebilirsiniz. Özellikler birçok nedenden dolayı düşük olabilir. Bunlardan biri, özelliğin kullanıcı davranışını etkilememesini sağlayabilir. Ancak, özelliğin kullanıcıya açık olmadığı anlamına da gelebilir. 

Örneğin, bir video sitesi "Action. VideoResolution = 4k" ' ın düşük öneme sahip bir özellik olduğunu görebilir ve Kullanıcı araştırmasını çelişiyor. Bunun nedeni, uygulamanın video çözümlemesine bile dahil değildir veya göstermez, böylece kullanıcılar bu davranışı temel alan davranışlarını değiştirmez.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Düzenleme veya seçkin takımlara rehberlik sağlama

Kataloğa getirilmesi gereken yeni içerik veya ürünler hakkında rehberlik sağlar. Kişiselleştirici, insan öngörülerini ve ekiplerini genişlettiğini sağlayan bir araç olacak şekilde tasarlanmıştır. Bunu yapmanın bir yolu, davranış, makale veya davranışın hangi içerikleriyle ilgili olarak BT gruplarını düzenleme grupları hakkında bilgi sağlamaktır. Örneğin, video uygulama senaryosu, "Action. VideoEntities. cat: true" adlı önemli bir özellik olduğunu ve düzenleme ekibinin daha fazla Cat videosunu göstermesini isteyip istemediğini gösteriyor olabilir.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Sık karşılaşılan sorunları ve hataları giderme

Ortak sorunlar ve hatalar, uygulama kodunuzun değiştirilerek düzeltilebilir, böylece kişiselleştirici için uygunsuz veya hatalı biçimlendirilmiş Özellikler gönderilemez. 

Özellikleri gönderirken sık karşılaşılan hatalar şunlardır:

* Kişisel olarak tanımlanabilen bilgiler (PII) gönderiliyor. Bir kişiye özgü PII (ad, telefon numarası, kredi kartı numaraları, IP adresleri gibi), kişiselleştirici ile kullanılmamalıdır. Uygulamanızın kullanıcıları izlemesi gerekiyorsa, tanımlayıcı olmayan bir UUID veya başka bir kullanıcı kimliği numarası kullanın. Çoğu senaryoda bu da sorunlu olur.
* Çok sayıda kullanıcıyla, her kullanıcının etkileşiminin tüm popülasyondan daha fazla etkileşime sahip olması çok düşüktür, bu nedenle kullanıcı kimliklerinin gönderilmesi (PII olmayan) büyük olasılıkla modele değerden daha fazla gürültü ekleyecektir.
* Tarih-Saat alanlarını, doğru zaman değerleri yerine kesin bir zaman damgası olarak gönderme. Context. TimeStamp. Day = Pazartesi veya "Context. TimeStamp. Hour" = "13" gibi özelliklere sahip olmak daha yararlıdır. Her biri için en fazla 7 veya 24 Özellik değeri olacaktır. Ancak "Context. TimeStamp": "1985-04-12T23:20:50.52 Z", bu, hiçbir zaman yeniden gerçekleşmeyeceğinden, bundan öğrenmenin bir yolu olmadığını kesin hale gelir.

## <a name="next-steps"></a>Sonraki adımlar

Kişiselleştirici ile [ölçeklenebilirliği ve performansı](concepts-scalability-performance.md) anlayın.

