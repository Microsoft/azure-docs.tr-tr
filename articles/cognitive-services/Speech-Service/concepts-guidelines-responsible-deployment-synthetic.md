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
ms.openlocfilehash: 3a0b645acd7c21ff0416c748cdd2caf7041be508
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730822"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Yapay sesli teknolojinin sorumlu dağıtımına ilişkin yönergeler

Bu makalede, Microsoft 'un yapay ses teknolojisini kullanmaya yönelik genel tasarım yönergeleri hakkında bilgi edineceksiniz. Bu yönergeler, Microsoft 'un, yapay seslerin sorumlu geliştirilmesine kılavuzluk etmek için konuşma ile sesli tatatsyon, tüketici ve kişilerin geliştirdiği çalışmalar üzerinde geliştirilmiştir.

Yapay konuşma teknolojisinin dağıtımı için, çoğu senaryo genelinde aşağıdaki yönergeler geçerlidir.

### <a name="disclose-when-the-voice-is-synthetic"></a>Ses yapay olduğunda açıklayadır
Bir sesin oluşturulduğu bir sesin, yalnızca zararlı sonuçlar riskini en aza indirir, ancak aynı zamanda sesi teslim eden kuruluştaki güveni de artırır. [Nasıl açıklayacağınızı](concepts-disclosure-guidelines.md)öğrenin.

Microsoft, müşterilerinin kendi kullanıcılarına özel sinir Voice yapay yapısını açıklatabini gerektirir. 
* Özellikle de bilinen kişilerin sesini kullanırken, hedef kitlelere uygun bir açıklama sağladığınızdan emin olun, ancak BT consciously veya unconsciously yapıp yapmayacağı gibi, BT, BT 'yi teslim eden kişiye göre daha fazla bilgi elde edin.  Örneğin, açıklama oluşturma bir yayının başlangıcında ifade edilebilir. Daha fazla bilgi için [Açıklama düzenlerini](concepts-disclosure-patterns.md)ziyaret edin.   
* Sömürmeyi amaçlama ve alt öğeler için tasarlanan kullanım durumları olan ebeveynler veya diğer taraflara doğru bir şekilde göz önünde bulundurun. kullanım durumlarınız, sömürmeyi amaçlama veya alt öğeler için tasarlanıyorsa, ebeveynler veya yasal koruyucuların yapay medya kullanımı hakkındaki bilgileri anlayabilmesini ve bu deneyimin kullanılıp kullanılmayacağını en iyi şekilde kararlamasını sağlamak için gerekir. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Senaryonuz için uygun ses türlerini seçin
Kullanım bağlamını ve yapay seslendirme ile ilişkili potansiyel güvenliğini aşmanızı 'yi dikkatle değerlendirin. Örneğin, yüksek kaliteli yapay sesler, kişisel mesajlaşma, mali işlemler veya insan uyumluluk veya emplik gerektiren karmaşık durumlar gibi yüksek riskli senaryolar için uygun olmayabilir. 

Kullanıcılar ses türleri için farklı beklentiler de içerebilir. Örneğin, hassas haberleri yapay bir sesle dinlerken, bazı kullanıcılar daha empathetic ve insan benzeri bir ton tercih eder, diğerleri de taraflı olmayan bir ses tercih eder. Kullanıcı tercihlerini daha iyi anlamak için uygulamanızı test etmeyi düşünün.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Yetenekler ve sınırlamalar hakkında saydam olun
Kullanıcılar, yüksek uygunlukta yapay sesli aracılarla etkileşim kurarken daha yüksek beklentileri daha yüksektir. Sistem özellikleri bu beklentileri karşılamıyorsa, güven zarar görebilir ve hatta zararlı deneyimler olabilir.

### <a name="provide-optional-human-support"></a>İsteğe bağlı insan desteği sağlama
Belirsiz, işlem senaryolarında (örneğin, bir çağrı Destek Merkezi), kullanıcılar isteklerine uygun bir şekilde yanıt vermek için her zaman bir bilgisayar aracısına güvenmiyor. İnsan desteği, sistemin ses veya yeteneğinin gerçekçi kaliteden bağımsız olarak bu durumlarda gerekli olabilir.

## <a name="considerations-for-voice-talent"></a>Ses taçödünç alma konuları
Ses aktörleri gibi Sesli aktörler ile çalışırken, yapay sesler oluşturmak için aşağıdaki kılavuz geçerlidir.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Sesli tatatdan anlamlı onay alın
Sesli talenler, kendi ses modelleri üzerinde denetime sahip olmalıdır (nasıl ve nerede kullanılır) ve kullanımı için telafi edilir. Microsoft, özel sesli müşterilerin sesli tafets ile bir yapay ses ve bu süre boyunca sesli yeteneklerini Contemplate 'e sahip anlaşmasıyla birlikte açık yazılı izin almasını gerektirir.  İyi bilinen bir kişinin yapay bir sesini oluşturuyorsanız, sesin içeriğini düzenleme veya onaylama konusunda bir yol sağlamanız gerekir.

Bazı ses tatları, teknolojinin olası kötü amaçlı kullanımlarıyla uyumlu değildir ve teknolojinin özellikleri hakkında sistem sahipleri tarafından eğitilmeli. Microsoft, müşterilerin, Microsoft 'un sesli tatatsyon sayesinde doğrudan veya ses tatatası temsilcisinden, yapay seslerin nasıl geliştirildiğini ve konuşma Hizmetleri ile birlikte nasıl çalıştığını açıklayan yetkili temsilcisi aracılığıyla Microsoft 'un [açıklanmasını](/legal/cognitive-services/speech-service/disclosure-voice-talent) gerektirir.

## <a name="considerations-for-those-with-speech-disorders"></a>Konuşma yemekleriyle ilgili konular
Konuşma olmayan kişilerle çalışırken yapay sesli teknoloji oluşturmak veya dağıtmak için aşağıdaki yönergeler geçerlidir.

### <a name="provide-guidelines-to-establish-contracts"></a>Sözleşmeleri oluşturmak için yönergeler sağlama
Konuşma yardımı için yapay bir ses kullanan kişilerle sözleşme oluşturmaya yönelik yönergeler sağlar. Sözleşme, sese sahip olan tarafları, kullanım süresini, sahiplik aktarma ölçütlerini, ses yazı tipini silmeye yönelik yordamları ve yetkisiz erişimi engelleme yapmayı düşünmelidir. Ayrıca, izin verildiyse, Aile üyelerinden ölüm dışı olduktan sonra ses yazı tipi sahipliğinin aktarılmasını etkinleştirin.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Konuşma desenlerinde tutarsızlıklar için hesap
Kendi ses yazı tiplerini kaydeden konuşma olmayan kişiler için, konuşma düzenlerinde (slurhalka veya belirli sözcükleri pronounce Me) tutarsızlıklar, kayıt işlemini karmaşıklaştırabilir. Bu durumlarda yapay sesli teknoloji ve kayıt oturumları bunlara uyum sağlamalıdır (yani, kesmeler ve ek sayıda kayıt oturumu sağlar).

### <a name="allow-modification-over-time"></a>Zaman içinde değişikliğe izin ver
Konuşmaya sahip kişiler, eskime seslerini yansıtmak için yapay seslerinde güncelleştirmeler yapmayı ister (örneğin, puberty 'e ulaşan bir çocuk). Bireyler, zaman içinde değişen stil tercihleri de içerebilir ve değiştirme, aksan veya diğer ses özelliklerinde değişiklik yapmak isteyebilir.


## <a name="see-also"></a>Ayrıca bkz.

* [Sesli Taçanın açıklanması](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Nasıl açığa çıkarmaz](concepts-disclosure-guidelines.md)
* [Açıklama tasarım desenleri](concepts-disclosure-patterns.md)