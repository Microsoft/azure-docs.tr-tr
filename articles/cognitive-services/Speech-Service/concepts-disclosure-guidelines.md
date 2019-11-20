---
title: Açıklama tasarımı yönergeleri
titleSuffix: Azure Cognitive Services
description: Açıklama tasarımı yönergelerine ve açıklama düzeyini değerlendirmeye giriş.
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: angle
ms.openlocfilehash: 7dffa3d9f6e96adc6146a1059e7360c77926ece3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837388"
---
# <a name="disclosure-design-guidelines"></a>Açıklama tasarımı yönergeleri
Sesli deneyiminizin yapay doğası hakkında saydam olacak şekilde müşterilerle nasıl güven oluşturacağınızı ve bakımının nasıl yapılacağını öğrenin.

## <a name="what-is-disclosure"></a>Nelerin açıklanması gerekiyor?

Bunun anlamı, insanların bir veya daha fazla şekilde&#39;üretilen bir sesle etkileşime geçmesine veya dinlediğini bilmesini sağlayan bir araçtır.

## <a name="why-is-disclosure-necessary"></a>Neden açıklanması gerekir?

Bilgisayar tarafından oluşturulan bir sesin yapay kaynakları hakkında daha fazla yeni bir ses elde etmeniz gerekir. Geçmişte, bilgisayar tarafından oluşturulan seslerine, gerçek bir kişi için hiç kimse neden olmadığı açıktır. Ancak, yapay seslerin Reali artmaları artar ve insan seslerden daha fazla ayırt edilemez hale gelir.

## <a name="goals"></a>Hedefleri
Yapay sesli deneyimler tasarlarken göz önünde bulundurmanız gereken ilkeler şunlardır:

**Güveni zorla**
<br>Deneyimi düşürmeden, her geçen testi devretmek için tasarlayın. Kullanıcıların, deneyimle sorunsuz bir şekilde etkileşimde bulunmasına izin verirken yapay bir sesle etkileşime geçebilecekleri konusunda kullanıcılara izin verin.

**Kullanım bağlamına uyarlayın**
<br>Kullanıcılarınızın doğru zamanda doğru bir açıklama eklemek için yapay sesle ne zaman, nerede ve nasıl etkileşimde bulunduklarını anlayın.

**Açık beklentileri ayarla**
<br>Kullanıcıların aracının yeteneklerini kolayca bulmasına ve anlamasına izin verin. İstek üzerine yapay ses teknolojisi hakkında daha fazla bilgi edinmek için fırsatlar sunun.

**Emayracı hatası**
<br>Aracının yeteneklerini zorlamak için başarısızlık süreyi kullanın.

## <a name="how-to-use-this-guide"></a>Bu kılavuzu kullanma

Bu kılavuz, yapay ses deneyiminize en uygun açıklama desenleri belirlemenizi sağlar. Daha sonra bunları nasıl ve ne zaman kullanacağınızı örnekler sunuyoruz. Bu desenlerin her biri, yapay konuşma hakkında kullanıcılarla saydamlığı en üst düzeye çıkarmak için tasarlanmıştır ve insan merkezli tasarıma doğru kalmakta.

Tasarım kılavuzunun ses deneyimleriyle ilgili büyük bölümünü göz önünde bulundurarak, burada özellikle şunlara odaklanıyoruz:

1. [**Açıklama değerlendirmesi**](#disclosure-assessment): yapay sesli deneyiminizin önerdiği açıklama türünü belirleme işlemi

2. [**Nasıl açığa çıkabiliriz**](concepts-disclosure-patterns.md): yapay sesli deneyiminize uygulanabilecek açıklama desenleri örnekleri

3. [**Ne zaman açığa çıkarmayı**](concepts-disclosure-patterns.md#when-to-disclose): Kullanıcı yolculuğu boyunca açığa çıkarmakta en iyi süre

## <a name="disclosure-assessment"></a>Açıklama değerlendirmesi
Kullanıcılarınızın&#39; bir etkileşim ve sesle karşılaştıkları bağlam hakkında beklentilerini değerlendirin. Bağlam yapay bir sesin &quot;konuşmasını temizler,&quot; açıklanması en düşük, kopan veya hatta gereksiz olabilir. Açıklaması etkileyen ana bağlam türleri, kişi türü, senaryo türü ve pozlama düzeyi içerir. Ayrıca kimin dinlebileceğini göz önünde bulundurmanıza de yardımcı olur.

### <a name="understand-context"></a>Bağlam anlayın

Yapay ses deneyiminizin bağlamını öğrenmek için bu çalışma sayfasını kullanın. Bunu, açıklama düzeyinizi belirlediğiniz bir sonraki adımda uygularsınız.

|                                    | Kullanım bağlamı                                                                                                                                                                                                                                                                                                                                                       | Olası riskler & zorlukları                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. kişi türü**               | **Aşağıdakilerden biri uygulanıyorsa, kişi ' Insan benzeri kişi ' kategorisinin altına sığar:**<br><br><ul><li> Gerçek bir insan, hayali bir gösterim olup olmadığını belirtir. (örn., fotoğraf veya gerçek bir kişinin bilgisayar tarafından üretilmiş bir işlemesi)<br><br><li> Yapay ses, yaygın olarak tanınabilir bir gerçek kişinin sesine dayalıdır (ör., Ünlüler, siyatik şekil) | Daha fazla insan benzeri temsiller, kişilerinize verdiğiniz daha büyük olasılıkla Kullanıcı onu gerçek bir kişiyle ilişkilendirir ya da içeriğin bilgisayar tarafından üretilen gerçek bir kişi tarafından konuşulmasına neden olur. </ul>                                                                                                                                                                      |
| **2. senaryo türü**            | **Aşağıdakilerden biri uygunsa, ses deneyiminiz ' hassas ' kategorisine uyar:**<br><br><ul><li> Kullanıcının kişisel bilgilerini alır veya görüntüler <br><br> <li> Yayınlar zamana duyarlı Haberler/bilgiler (örneğin, acil durum uyarısı)<br><br><li> Gerçek kişilerin birbirleriyle iletişim kurmasına yardımcı olmaya yönelik aims (örn. kişisel e-postaları/metinleri okur)<br><br> <li> Tıp/Sağlık yardımı sağlar </ul>            | Yapay seslendirme kullanımı, önemli, kişisel veya acil konularda konular ile ilgili kişileri kullanan kişilere uygun veya güvenilir olmayabilir. Ayrıca, gerçek bir insan olarak aynı düzeyde empade ve bağlamsal tanıma da bekleyebilir. |
| **3. pozlama düzeyi** |**Şu durumlarda ses deneyiminizin büyük olasılıkla ' yüksek ' kategorisine sığar:** <br><br><ul><li>Kullanıcı, yapay sesle sık veya uzun bir süre boyunca iletişim kurar veya etkileşime geçerek </ul>                                                                                                                                                                             | Uzun vadeli ilişkiler kurulurken, saydamlığa ve kullanıcılarla güven oluşturmaya yönelik önem derecesi de daha yüksektir.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Açıklama düzeyini belirleme

Yapay sesli deneyiminizin kullanım bağlamına göre yüksek veya düşük bir açıklama gerektirip gerektirmediğini öğrenmek için aşağıdaki diyagramı kullanın.

  ![Açıklama değerlendirmesi diyagramı](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Başvuru belgeleri

* [Sesli Taçanın açıklanması](https://aka.ms/disclosure-voice-talent)
* [Yapay sesli teknolojinin sorumlu dağıtımına ilişkin yönergeler](concepts-guidelines-responsible-deployment-synthetic.md)
* [Geçişi genel bakış](concepts-gating-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Açıklama tasarım desenleri](concepts-disclosure-patterns.md)
