---
title: Açıklama tasarım desenleri
titleSuffix: Azure Cognitive Services
description: Tasarım desenleri ve en iyi deneyimler.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: aec553b473e0fb2688224e8a8d027f7e9f3a6025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101716627"
---
# <a name="disclosure-design-patterns"></a>Tasarım desenlerini açıklama
Yapay sesli deneyiminize yönelik doğru [Açıklama düzeyini](concepts-disclosure-guidelines.md#disclosure-assessment)&#39;ve potansiyel tasarım düzenlerini araştırmak için uygun bir zaman&#39;.
## <a name="overview"></a>Genel Bakış
Yapay sesli deneyiminize uygulayabileceğiniz bir dizi açıklama tasarım deseni vardır. Açıklama değerlendirmeinizin sonucu ' yüksek açığa çıkması ' ise [**açık bir açıklama**](#explicit-disclosure)önerilir, bu da yapay ses çıkış sağlarının kaynaklarına iletişim kurmasıdır. [**Örtük açığa çıkması**](#implicit-disclosure) , gerekli bir açıklama düzeyi yüksek veya düşük olup olmadığına bakılmaksızın sesli deneyimlere yönelik ipuçları ve etkileşim desenleri içerir.
![Açıklama desenleri](media/responsible-ai/disclosure-patterns/affordances.png)






| Açık açıklama desenleri                                                                                                                                                                                    | Örtük açıklama desenleri                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Şeffaf giriş](#transparent-introduction)<br> [Çapraz şeffaf giriş](#verbal-transparent-introduction)<br>  [Açık satır](#explicit-byline)<br>  [Özelleştirme ve ayarlama](#customization-and-calibration)<br> [Ebeveyn açıklaması](#parental-disclosure)<br> [Sesin nasıl yapıldığı hakkında daha fazla bilgi edinmek için fırsat sağlama](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Yetenek açığa çıkması](#capability-disclosure)<br>[Örtük Ipuçları ve geri bildirim](#implicit-cues--feedback)<br> [Konuşma saydamlığı](#conversational-transparency) |



Yapay sesinize uygulanan desenlere doğrudan başvurmak için aşağıdaki grafiği kullanın. Bu grafikteki diğer koşullardan bazıları senaryonuz için de geçerli olabilir:<br/>



| Yapay ses deneyiminiz varsa... | Öneriler | Tasarım desenleri |
| --- | --- | --- |
| Yüksek açığa çıkması gerekir  | Kullanıcıların ilişkilendirme oluşturmasına yardımcı olmak için en az bir açık kalıp ve örtük ipuçları kullanın. |[Açık açıklama](#explicit-disclosure)<br>[Örtük açıklama](#implicit-disclosure)  |
| Düşük bir açıklama gerektirir | Açıklama en az veya gereksiz olabilir, ancak bazı örtük desenlerden yararlanabilir. | [Yetenek açığa çıkması](#capability-disclosure)<br>[Konuşma saydamlığı](#conversational-transparency)  |
| , Yüksek düzeyde bir görevlendirmeye sahiptir | Uzun süreli oluşturun ve Kullanıcı yolculuğu boyunca açıklanmek üzere birden çok giriş noktası sunun. Bir ekleme deneyiminizin olması önemle önerilir. | [Şeffaf giriş](#transparent-introduction)<br>[Özelleştirme ve ayarlama](#customization-and-calibration)<br>[Yetenek açığa çıkması](#capability-disclosure) |
| Birincil amaçlanan hedef kitle olarak alt öğeler içerir | Ana öğeleri birincil açıklama kitleleri olarak hedefleyin ve bu bilgilerin, çocuklarla iletişim kurabildiğinden emin olun.  | [Ebeveyn açıklaması](#parental-disclosure)<br>[Çapraz şeffaf giriş](#verbal-transparent-introduction)<br> [Örtük açıklama](#implicit-disclosure)<br> [Konuşma saydamlığı](#conversational-transparency)  |
| Birincil amaçlanan hedef kitle olarak görme engelli kullanıcıları veya kişileri görme  | Tüm kullanıcıların yanı sıra, herhangi bir görsel açıklama biçiminin diğer metin veya ses efektlerine sahip olduğundan emin olun. Kontrast oranı ve görüntüleme boyutu için erişilebilirlik standartlarına uyar. Açığa çıkabilecek iletişim kurmak için sessel ipuçları kullanın.  | [Çapraz şeffaf giriş](#verbal-transparent-introduction) <br>[Seste Ipuçları](#implicit-cues--feedback)<br>[Haptik Ipuçları](#implicit-cues--feedback)<br>[Konuşma saydamlığı](#conversational-transparency)<br>[Erişilebilirlik standartları](https://www.microsoft.com/accessibility) |
| Ekran-daha az, cihaz-daha az veya tek etkileşim modu olarak seslendirmeyi kullanır | Açığa çıkabilecek iletişim kurmak için sessel ipuçları kullanın. | [Çapraz şeffaf giriş](#verbal-transparent-introduction) <br> [Seste Ipuçları](#implicit-cues--feedback)  |
| Potansiyel olarak birden çok Kullanıcı/dinleyici içeriyor (örneğin, birden çok evdeki kişisel yardımcı)  | Çeşitli Kullanıcı bağlamlarının ve seviyelerinin öğrenmiş olması ve Kullanıcı yolculuğunda açıklanmak üzere birden çok fırsat sunmanın olması.  | [Saydam giriş (dönüş kullanıcısı)](#transparent-introduction)<br> [Sesin nasıl yapıldığı hakkında daha fazla bilgi edinmek için fırsat sağlama](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Konuşma saydamlığı](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Açık açıklama
Yapay sesli deneyiminizin yüksek bir açıklama gerektirmesi durumunda yapay doğası açıkça sağlamak için aşağıdaki açık desenlerden en az birini kullanmak en iyisidir.
### <a name="transparent-introduction"></a>Şeffaf giriş

Ses deneyimi başlamadan önce, sesin kaynakları ve özellikleri hakkında tamamen saydam olacak şekilde dijital yardımcı 'yı tanıtın. Bu düzenin en iyi şekilde kullanılması, yeni bir kullanıcı ekleme veya dönen bir kullanıcıya yeni özellikler tanıtma. Giriş sırasında örtük ipuçları uygulamak, kullanıcıların sayısal aracının yapay doğası hakkında bir model oluşturmasına yardımcı olur.

#### <a name="first-time-user-experience"></a>İlk kez kullanıcı deneyimi

![İlk çalıştırma deneyimi sırasında şeffaf giriş](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Yeni bir Kullanıcı eklenirken yapay ses ortaya çıkartılır.*

Öneriler
- Sesin yapay olduğunu (örn. &quot; dijital &quot; ) Açıklama
- Aracının yapabilme yeteneğine sahip olduğunu açıkla
- Sesli&#39;s kaynakları açıkça durumu
- Yapay ses hakkında daha fazla bilgi edinmek için bir giriş noktası sunun

#### <a name="returning-user-experience"></a>Kullanıcı deneyimi döndürülüyor

Kullanıcı ekleme deneyimini atrsa, Kullanıcı sesi ilk kez tetiklene kadar saydam giriş deneyimine giriş noktaları sunmaya devam edin.
<br/>

![Dönme Kullanıcı deneyimi sırasında şeffaf giriş](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Yapay sesli deneyimle tutarlı bir giriş noktası sağlayın. Kullanıcı yolculuğunda herhangi bir noktada sesi ilk kez tetiklerse, kullanıcının ekleme deneyimine dönmesini sağlar.*


### <a name="verbal-transparent-introduction"></a>Çapraz şeffaf giriş

Dijital yardımcının&#39;s Voice 'un açığa çıkması için yeterince açık olduğunu belirten konuşulan bir istem. Bu model, sesin kullanılabilir tek etkileşim modu olduğu yüksek açıklanmasına yönelik senaryolar için idealdir.
<br/>

![Saydam olarak konuşulan saydam giriş](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Kullanıcı deneyiminde, bir kişiyi&#39;s sesini zaten tanıtan veya özniteolabileceğiniz bir süre olduğunda saydam bir giriş kullanın.*


![İlk kişiye şeffaf bir giriş yapın](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Ek saydamlık için, ses aktör ilk kişinin yapay sesine ilişkin kaynakları açığa çıkarabilir.*

### <a name="explicit-byline"></a>Açık satır

Kullanıcı sesi tetiklemek için bir ses yürütücüsü veya etkileşimli bileşen ile etkileşim kurulacaktır bu kalıbı kullanın.


![Haber medya senaryosunda açık satır](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Açık bir byline, sesin nereden geldiği atıdır.*

Öneriler

- Birleştirilmiş ses hakkında daha fazla bilgi edinmek için teklif giriş noktası

### <a name="customization-and-calibration"></a>Özelleştirme ve ayarlama

Kullanıcılar, dijital yardımcının bunlara nasıl yanıt vereceğini (yani, ses seslerinin) üzerinde denetim sağlar.  Bir Kullanıcı kendi koşullarında bir sistemle ve belirli hedeflere göz önüne alarak etkileşime geçtiğinde, gerçek bir kişi değil&#39;s 'yi zaten anlamıştır.

#### <a name="user-control"></a>Kullanıcı denetimi

Yapay ses deneyiminde anlamlı ve belirgin bir etkiye sahip seçimler sunun.

![Kullanıcı tercihleri](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Kullanıcı tercihleri, kullanıcıların deneyimlerini özelleştirmesini ve iyileştirmelerini sağlar.*

Öneriler

- Kullanıcıların sesi özelleştirmesine izin ver (örneğin, dil ve ses türü seçin)
- Kullanıcılara, benzersiz sesinin (ör. ses ayarlaması, özel komutlar) yanıt vermesini öğretmek için bir yol sağlayın
- Kullanıcı tarafından oluşturulan veya bağlamsal etkileşimler için iyileştirin (ör. Anımsatıcılar)

#### <a name="persona-customization"></a>Kişi özelleştirmesi

Dijital yardımcı&#39;s sesini özelleştirmek için yollar sunar. Ses bir ünlüye veya çok daha tanınabilir bir kişiye dayanıyorsa, kullanıcılar sesi önizlediğinizde hem görsel hem de konuşulan tanıtımları kullanmayı göz önünde bulundurun.

![Ses özelleştirme](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Bir ses kümesi arasından seçim yapma özelliği sunan yapay doğası sağlamaya yardımcı olur.*

Öneriler
- Kullanıcıların her ses sesinin önizlemesine izin ver
- Her ses için gerçek bir giriş kullanın
- Birleştirilmiş ses hakkında daha fazla bilgi edinmek için giriş noktaları sunun

### <a name="parental-disclosure"></a>Ebeveyn açıklaması

COPPA düzenlemelerine uyanın yanı sıra, birincil amaçlanan hedef kitlesi küçük bir alt öğe ise ve pozlama düzeyiniz yüksek ise ebeveynler için açıklama sağlayın. Gizli kullanımlar için, bir yetişkin yapay sesinin kullanımını kabul edene kadar deneyimi kabul etmeyi göz önünde bulundurun. Ebeveynler, iletileri alt öğelerine iletmelerini teşvik edin.

![Ebeveynler için açıklama](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Ebeveynler için en iyi duruma getirilmiş şeffaf bir giriş, bir yetişkin 'nin bir alt ile etkileşime geçmeden önce sesin yapay doğası hakkında bilgi almasını sağlar.*

Öneriler

- Ana öğeleri birincil hedef kitle olarak açıklama olarak hedefleyin
- Üst öğeleri, alt öğelerine açıklama ile iletişim kurmaya teşvik edin
- Birleştirilmiş ses hakkında daha fazla bilgi edinmek için giriş noktaları sunun
- Ebeveynler &quot; &quot; , üst bilgilerin açığa çıkmasına neden olduğunu göstermek için ebeveynlerle basit bir güvenlik sorusu sorarak deneyim

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Sesin nasıl yapıldığı hakkında daha fazla bilgi edinmek için fırsat sağlama

Yapay ses teknolojisi hakkında daha fazla bilgi sağlayan bir sayfa, açılır pencere veya dış siteye bağlama duyarlı giriş noktaları sunun. Örneğin, ekleme sırasında veya Kullanıcı konuşma sırasında daha fazla bilgi isteminde bulunduğunda daha fazla bilgi edinmek için bir bağlantıyı yüzeyinizi seçebilirsiniz.

![Daha fazla bilgi için giriş noktası](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Sentezlenmiş ses hakkında daha fazla bilgi edinmek için fırsat sunan bir giriş noktası örneği.*

Bir Kullanıcı yapay sesle ilgili daha fazla bilgi istediğinde, birincil hedef yapay sesin kaynakları hakkında eğitmektir ve teknolojide saydam hale olmaktır.

![Yapay sesle kullanıcılara daha fazla bilgi sağlama](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Daha fazla bilgi, bir dış site yardım sitesinde sunulabilir.*

Öneriler

- Karmaşık kavramları basitleştirip, legalei ve teknik jargkullanmaktan kaçının
- Bu içeriği gizlilik ve kullanım koşulları deyimlerine eklemeyin
- İçeriği kısa tutun ve kullanılabilir olduğunda canlandırın kullanın

## <a name="implicit-disclosure"></a>Örtük açıklama

Tutarlılık, Kullanıcı yolculuğu boyunca örtülü olarak açıklanmaya yönelik anahtardır. Cihazlar ve etkileşim modları genelinde görsel ve sessel ipuçlarının tutarlı kullanımı örtük desenler ve açık açıklama arasında ilişkilendirme oluşturmaya yardımcı olabilir.

![Örtük ipuçları tutarlılığı](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Örtük Ipuçları & geri bildirim

Anthropomorphism, aracının gerçek görsel gösteriminden ses, ses, ışık desenleri, sıçrama şekilleri, hatta bir cihazın titreşimi gibi farklı yollarla bildirim alabilir. Kişiyi tanımlarken, çok insan benzeri bir avatar için amacı yerine örtük ipuçları ve geri bildirim desenlerinden yararlanın. Bu, daha belirgin bir açıklama gereksinimini en aza indirmek için bir yoldur.

![Görsel ipuçları ve geri bildirim](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Bu ipuçları, çok insan gibi bir aracı anthropomorphize yardımcı olur. Ayrıca, zaman içinde sürekli olarak kullanıldığında kendi kendine etkin bir açıklama mekanizmaları de olabilirler.*

Aşağıdaki ipucu türlerini eklerken deneyiminizin farklı şekillerini göz önünde bulundurun:

| Görsel Ipuçları                                                                                                                                                               | Seste Ipuçları                                                      | Haptik Ipuçları |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Yanıt veren gerçek zamanlı ipuçları (ör. animasyon)<br> Ekran olmayan ipuçları (örneğin, bir cihazdaki ışıklar ve desenler)<br>  | Sonıcon (örn., önemli bir ses, müzik notları dizisi) | Titreşimi   |

### <a name="capability-disclosure"></a>Yetenek açığa çıkması

Açıklama, dijital yardımcının özelliği için doğru beklentileri ayarlanarak örtülü olarak elde edilebilir. Kullanıcıların dijital yardımınızla etkileşim kurmayı öğrenmeleri ve bu deneyimin erken aşamaları sırasında yapay sesle ilgili daha fazla bilgi edinmek için bağlamsal yardım sunabilmesi için örnek komutlar sağlayın.

![Bir konuşmaya yönelik olarak kullanabileceğiniz varsayılan yanıtların örneği.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Konuşma saydamlığı

Konuşmalar beklenmeyen yollara denk geldiğinde, beklentileri sıfırlamasına, saydamlığa zorlamaya ve kullanıcıların başarılı yollara yaklaşmasına yardımcı olabilecek varsayılan yanıtları yeniden taslağı yapmayı düşünün. Görüşmede açık bir açıklama kullanmanın fırsatları da vardır.

![Beklenmeyen yolları işleme](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Kapalı görev veya &quot; &quot; aracıya yönelik kişisel sorular, kullanıcılara aracının yapay yapısını anımsatmak ve BT 'yi uygun bir kişiye doğru şekilde yönelmek ya da onları gerçek bir kişiye yönlendirmek için iyi bir zaman harcar.

![Görev için soruları işleme](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Ne zaman açığa çıkar

Kullanıcı yolculuğu boyunca açıklananın çok sayıda fırsat vardır. İlk kullanım için tasarım, ikinci kullanım, n...., Ayrıca, &quot; &quot; Saydamlık vurgulamamasının (sistem bir hata yaptığı veya kullanıcı aracı&#39;s özellikleri sınırlaması bulduğunda olduğu gibi).

![Kullanıcı yolculuğu boyunca açıklama fırsatları](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Standart dijital yardım kullanıcısı, çeşitli açıklama fırsatlarını vurgulayan bir örnektir.

### <a name="up-front"></a>Yukarı-ön

En iyi açıklama, bir kişi yapay sesle ilk kez etkileşime geçtiğinde.Kişisel bir ses Yardımcısı senaryosunda bu, ekleme işlemi sırasında veya Kullanıcı deneyimi ilk kez daha önce kaldırırdı. Diğer senaryolarda, yapay bir ses bir Web sitesindeki içeriği ilk kez okuduğunda veya Kullanıcı sanal karakterle ilk kez etkileşime geçtiğinde bu olabilir.

- [Şeffaf giriş](#transparent-introduction)
- [Yetenek açığa çıkması](#capability-disclosure)
- [Özelleştirme ve ayarlama](#customization-and-calibration)
- [Örtük Ipuçları](#implicit-cues--feedback)

### <a name="upon-request"></a>İstek üzerine

Kullanıcılar, istendiğinde Kullanıcı yolculuğu sırasında herhangi bir noktada ek bilgilere ve denetim tercihlerine kolayca erişebilmelidir.

- [Sesin nasıl yapıldığı hakkında daha fazla bilgi edinmek için fırsat sağlama](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Özelleştirme ve ayarlama](#customization-and-calibration)
- [Konuşma saydamlığı](#conversational-transparency)

### <a name="continuously"></a>Sürekli

Kullanıcı deneyimini sürekli geliştiren örtülü tasarım düzenlerini kullanın.

- [Yetenek açığa çıkması](#capability-disclosure)
- [Örtük Ipuçları](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Sistem başarısız olduğunda

Sorunsuz bir şekilde başarısız olan bir fırsat olarak açıklama kullanın.

- [Konuşma saydamlığı](#conversational-transparency)
- [Sesin nasıl yapıldığı hakkında daha fazla bilgi edinmek için fırsat sağlama](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [İnsana iletimi](#conversational-transparency)

## <a name="additional-resources"></a>Ek kaynaklar
- [Microsoft bot yönergeleri](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana tasarım yönergeleri](/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP konuşma tasarım yönergeleri](/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed Reality sesli komut yönergeleri](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="see-also"></a>Ayrıca bkz.

* [Sesli Taçanın açıklanması](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Yapay sesli teknolojinin sorumlu dağıtımına ilişkin yönergeler](concepts-guidelines-responsible-deployment-synthetic.md)
* [Nasıl açığa çıkarmaz](concepts-disclosure-guidelines.md)