---
title: Yapay sesli teknolojinin sorumlu dağıtımına ilişkin yönergeler
titleSuffix: Azure Cognitive Services
description: Microsoft 'un yapay ses teknolojisini kullanmaya yönelik genel tasarım yönergeleri. Bunlar, Microsoft 'un sesli tatatsyon, müşteriler ve konuşma ile ilgili yapay bir sesin sorumlu geliştirilmesine kılavuzluk eden kişiler sayesinde geliştirildiği çalışmalar üzerinde geliştirilmiştir.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836777"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Yapay sesli teknolojinin sorumlu dağıtımına ilişkin yönergeler
Yapay ses teknolojisini kullanmaya yönelik Microsoft 'un genel tasarım yönergeleri aşağıda verilmiştir. Bunlar, Microsoft 'un sesli tatathı, tüketicilerle ve yapay sesten sorumlu geliştirmeye kılavuzluk etmek için konuşma içermeyen kişilerin geliştirdiği çalışmalar üzerinde geliştirilmiştir.

## <a name="general-considerations"></a>Genel hususlar
Yapay konuşma teknolojisinin dağıtımı için, çoğu senaryo genelinde aşağıdaki yönergeler geçerlidir.

### <a name="disclose-when-the-voice-is-synthetic"></a>Ses yapay olduğunda açıklayadır
Bir sesin oluşturulduğu bir sesin, yalnızca zararlı sonuçlar riskini en aza indirir, ancak aynı zamanda sesi teslim eden kuruluştaki güveni de artırır. [Nasıl açıklayacağınızı](concepts-disclosure-guidelines.md)öğrenin.

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Senaryonuz için uygun ses türlerini seçin
Kullanım bağlamını ve yapay seslendirme ile ilişkili potansiyel güvenliğini aşmanızı 'yi dikkatle değerlendirin. Örneğin, yüksek kaliteli yapay sesler, kişisel mesajlaşma, mali işlemler veya insan uyumluluk veya emplik gerektiren karmaşık durumlar gibi yüksek riskli senaryolar için uygun olmayabilir. Kullanıcılar ses türleri için farklı beklentiler de içerebilir. Örneğin, hassas haberleri yapay bir ses tarafından okunmayı dinlerken, bazı kullanıcılar haberleri daha empathetic ve insan benzeri bir şekilde okumayı tercih ederken, diğerleri daha çok monoton, taraflı olmayan sesi tercih eder. Kullanıcı tercihlerini daha iyi anlamak için uygulamanızı test etmeyi düşünün.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Yetenekler ve sınırlamalar hakkında saydam olun
Kullanıcılar, yüksek uygunlukta yapay sesli aracılarla etkileşim kurarken daha yüksek beklentileri daha yüksektir. Sonuç olarak, sistem özellikleri bu beklentileri karşılamıyorsa, güven zarar görebilir ve hatta zararlı deneyimler oluşmasına neden olabilir.

### <a name="provide-optional-human-support"></a>İsteğe bağlı insan desteği sağlama
Belirsiz, işlem senaryolarında (örneğin, bir çağrı Destek Merkezi), kullanıcılar isteklerine uygun bir şekilde yanıt vermek için her zaman bir bilgisayar aracısına güvenmiyor. İnsan desteği, sistemin ses veya yeteneğinin gerçekçi kaliteden bağımsız olarak bu durumlarda gerekli olabilir.

## <a name="considerations-for-voice-talent"></a>Ses taçödünç alma konuları
Ses aktörleri gibi Sesli aktörler ile çalışırken, yapay sesler oluşturmak için aşağıdaki kılavuz geçerlidir.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Sesli tatatdan anlamlı onay alın
Ses tatatzı, ses yazı tipi (nasıl ve nerede kullanılır) üzerinde denetim sahibi olmak için kullanılır ve her zaman dengelenebilir. Bu nedenle, sistem sahipleri, sesli tastasyondan açık yazılı bir izin almalıdır ve kullanım örnekleri, kullanım süresi, kullanım süresi ve benzerlerini açık şekilde sözleşmeli belirtimlere sahip olmalıdır. Bazı ses tastası, teknolojinin olası kötü amaçlı kullanımlarıyla uyumlu değildir ve teknolojinin özellikleri hakkında sistem sahipleri tarafından eğitilmeli. Ses taçanız ve onayı hakkında daha fazla bilgi edinmek için [sesli tatatyi](https://aka.ms/disclosure-voice-talent)okuyun.


## <a name="considerations-for-those-with-speech-disorders"></a>Konuşma yemekleriyle ilgili konular
Konuşma olmayan kişilerle çalışırken yapay sesli teknoloji oluşturmak veya dağıtmak için aşağıdaki yönergeler geçerlidir.

### <a name="provide-guidelines-to-establish-contracts"></a>Sözleşmeleri oluşturmak için yönergeler sağlama
Konuşma yardımı için yapay bir ses kullanan kişilerle sözleşme oluşturmaya yönelik yönergeler sağlar. Sözleşme, sese sahip olan tarafları, kullanım süresini, sahiplik aktarma ölçütlerini, ses yazı tipini silmeye yönelik yordamları ve yetkisiz erişimi engelleme yapmayı düşünmelidir. Ayrıca, bu kişinin izin vermiş olması halinde aile üyelerine bir daha fazla ölülinden sonra, ses yazı tipi sahipliğinin sözleşme aktarımını etkinleştirin.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Konuşma desenlerinde tutarsızlıklar için hesap
Kendi ses yazı tiplerini kaydeden konuşma olmayan kişiler için, konuşma düzenlerinde (slurhalka veya belirli sözcükleri pronounce Me) tutarsızlıklar, kayıt işlemini karmaşıklaştırabilir. Bu durumlarda yapay sesli teknoloji ve kayıt oturumları bunlara uyum sağlamalıdır (yani, kesmeler ve ek sayıda kayıt oturumu sağlar).

### <a name="allow-modification-over-time"></a>Zaman içinde değişikliğe izin ver
Konuşmaya sahip kişiler, eskime seslerini yansıtmak için yapay seslerinde güncelleştirmeler yapmayı ister (örneğin, puberty 'e ulaşan bir çocuk). Bireyler, zaman içinde değişen stil tercihleri de içerebilir ve değiştirme, aksan veya diğer ses özelliklerinde değişiklik yapmak isteyebilir.


## <a name="reference-docs"></a>Başvuru belgeleri

* [Sesli Taçanın açıklanması](https://aka.ms/disclosure-voice-talent)
* [Geçişi genel bakış](concepts-gating-overview.md)
* [Nasıl açığa çıkarmaz](concepts-disclosure-guidelines.md)
* [Açıklama tasarım desenleri](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Sesli Taçanın açıklanması](https://aka.ms/disclosure-voice-talent)
* [Nasıl açığa çıkarmaz](concepts-disclosure-guidelines.md)
* [Açıklama tasarım desenleri](concepts-disclosure-patterns.md)
