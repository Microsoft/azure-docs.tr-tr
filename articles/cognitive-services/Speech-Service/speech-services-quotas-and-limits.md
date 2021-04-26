---
title: Konuşma hizmeti kotaları ve limitleri
titleSuffix: Azure Cognitive Services
description: Azure bilişsel konuşma hizmeti kotaları ve limitleri için hızlı başvuru, ayrıntılı açıklama ve en iyi uygulamalar
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alexeyo
ms.openlocfilehash: f851d7999b063a2b1334564902d81343e3789439
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011182"
---
# <a name="speech-service-quotas-and-limits"></a>Konuşma hizmeti kotaları ve limitleri

Bu makalede, Azure bilişsel konuşma hizmeti kotaları ve tüm [fiyatlandırma katmanlarında](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)limitlerin **ayrıntılı açıklaması** ve bir hızlı başvuru yer almaktadır. Ayrıca, istek azaltmasını önlemek için bazı en iyi yöntemleri içerir. 

## <a name="quotas-and-limits-quick-reference"></a>Kotalar ve sınırlar hızlı başvuru
[Metin okuma kotaları ve sınırlarına](#text-to-speech-quotas-and-limits-per-speech-resource) atlayın
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Konuşma kaynağı başına Konuşmayı Metne Dönüştürme Kotaları ve Sınırları
Aşağıdaki tablolarda, "ayarlanamaz" satırı olmayan parametreler, tüm fiyat katmanları **için ayarlanamaz.**

#### <a name="online-transcription"></a>Çevrimiçi döküm
[Konuşma SDK](speech-sdk.md) ile kullanım ve/veya [kısa ses için konuşmaya metin REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Kota | Ücretsiz (F0)<sup>1</sup> | Standart (S0) |
|--|--|--|
| **Eşzamanlı Istek sınırı-temel model** | 1 | 100 (varsayılan değer) |
| Ayarlanabilir | <sup>2</sup> yok | Evet<sup>2</sup> |
| **Eşzamanlı Istek sınırı-özel model** | 1 | 20 (varsayılan değer) |
| Ayarlanabilir | <sup>2</sup> yok | Evet<sup>2</sup> |

#### <a name="batch-transcription"></a>Toplu iş dökümü
| Kota | Ücretsiz (F0)<sup>1</sup> | Standart (S0) |
|--|--|--|
| [Konuşmadan metne REST API v 2.0 ve v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) sınırı | Toplu iş dökümü F0 için kullanılamaz | dakikada 300 istek |
| Maksimum ses girişi dosya boyutu | Yok | 1 GB |
| Maksimum giriş blobu boyutu (örneğin, bir ZIP arşivinde birden fazla dosya içerebilir; yukarıdaki dosya boyutu sınırını aklınızda bulundurtığınızdan emin olun) | Yok | 2,5 GB |
| En büyük blob kapsayıcısı boyutu | Yok | 5 GB |
| Kapsayıcı başına en fazla BLOB sayısı | Yok | 10000 |
| İstek başına en fazla dosya sayısı (giriş olarak birden çok içerik URL 'Si kullanılırken) | Yok | 1000  |
| Eşzamanlı çalışan en fazla iş sayısı | Yok | 2000  |

#### <a name="model-customization"></a>Model özelleştirmesi
| Kota | Ücretsiz (F0)<sup>1</sup> | Standart (S0) |
|--|--|--|
| REST API sınırı | dakikada 300 istek | dakikada 300 istek |
| En fazla konuşma veri kümesi sayısı | 2 | 500 |
| Veri Içeri aktarma için maksimum akustik veri kümesi dosya boyutu | 2 GB | 2 GB |
| Veri Içeri aktarma için en büyük dil veri kümesi dosya boyutu | 200 MB | 1,5 GB |
| Veri Içeri aktarma için maksimum Söyleniş veri kümesi dosya boyutu | 1 KB | 1 MB |
| `text` [Model oluşturma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API 'si isteğinde parametre kullanılırken maksimum metin boyutu | 200 KB | 500 KB |

<sup>1</sup> **ücretsiz (F0)** fiyatlandırma katmanı için bkz. [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)Ayrıca aylık allowsuyum.<br/>
<sup>2</sup> [ek açıklamalar](#detailed-description-quota-adjustment-and-best-practices), [en iyi uygulamalar](#general-best-practices-to-mitigate-throttling-during-autoscaling)ve [ayarlama yönergelerine](#speech-to-text-increasing-online-transcription-concurrent-request-limit)bakın.<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Konuşma kaynağı başına metinden konuşmaya kotalar ve sınırlar
"Ayarlanamaz" satırı olmayan parametrelerin altındaki tabloda tüm fiyat katmanları **için ayarlanamaz.**

| Kota                                                                          | Ücretsiz (F0)<sup>3</sup>  | Standart (S0)   |
|--------------------------------------------------------------------------------|------------------------|-----------------|
| **Standart ve sinir sesler için saniye başına en fazla Işlem sayısı (TPS)** | 200<sup>4</sup>        | 200<sup>4</sup> |
| **Özel ses için eşzamanlı Istek sınırı**                                  |                        |                 |
| Varsayılan değer                                                                  | 10                     | 10              |
| Ayarlanabilir                                                                     | <sup>5</sup> yok         | Evet<sup>5</sup> |
| **HTTP 'e özgü kotalar**                                                       |                        |                 |
| İstek başına en fazla üretilen ses uzunluğu                                          | 10 dakika                 | 10 dakika          |
| `<voice>`SSML 'de ayrı etiket sayısı üst sınırı                                  | 50                     | 50              |
| **WebSocket 'e özgü kotalar**                                                  |                        |                 |
| Her açma için üretilen maksimum ses uzunluğu                                             | 10 dakika                 | 10 dakika          |
| Her açma için en fazla SSML Ileti boyutu                                                 | 64 KB                  | 64 KB           |
| **REST API sınırı**                                                             | dakikada 20 istek | dakikada 300 istek |


<sup>3</sup> **ücretsiz (F0)** fiyatlandırma katmanı için bkz. [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)Ayrıca aylık allowsuyum.<br/>
<sup>4</sup> [ek açıklamaları](#detailed-description-quota-adjustment-and-best-practices) ve [en iyi yöntemleri](#general-best-practices-to-mitigate-throttling-during-autoscaling)görüntüleyin.<br/>
<sup>5</sup> [ek açıklamalar](#detailed-description-quota-adjustment-and-best-practices), [en iyi uygulamalar](#general-best-practices-to-mitigate-throttling-during-autoscaling)ve [ayarlama yönergelerine](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)bakın.<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Ayrıntılı açıklama, kota ayarlaması ve en iyi yöntemler
Kota artışı (uygun olduğunda) istenmeden önce, gerekli olduğundan emin olun. Konuşma hizmeti, gerekli hesaplama kaynaklarını "isteğe bağlı" modda ve aynı zamanda, çok fazla sayıda donanım kapasitesini sürdürmeden müşteri maliyetlerini düşük tutmak için otomatik ölçeklendirme teknolojileri kullanıyor. İş yükünüz tanımlı sınırlardayken ("çok fazla istek") bir yanıt 429 kodu ("çok fazla istek") aldığında, en olası açıklama hizmetin talebe göre [ölçeklendirilmesi ve](#quotas-and-limits-quick-reference)gerekli ölçeğe ulaşamamasının, bu nedenle isteği karşılamak için hemen yeterli kaynağa sahip olmamasından kaynaklanıyor olabilir. Bu durum genellikle geçicidir ve son kez kullanılmamalıdır.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Otomatik ölçeklendirme sırasında azaltmayı azaltmak için genel en iyi yöntemler
Daraltma ile ilgili sorunları en aza indirmek için (yanıt kodu 429), aşağıdaki teknikleri kullanmanızı öneririz:
- Uygulamanızda yeniden deneme mantığını uygulama
- İş yükünde keskin değişikliklerden kaçının. İş yükünü kademeli olarak artırın <br/>
*Örnek.* Uygulamanız metin okuma kullanıyor ve geçerli iş yükünüz 5 TPS (saniye başına işlem). Sonraki saniye, 20 TPS 'ye yükü (dört kat daha fazla) artırırsınız. Hizmet, yeni yükü karşılamak için hemen ölçeklendirmeyi başlatır, ancak büyük olasılıkla bunu bir saniye içinde yapamayacak, bazı istekler yanıt kodu 429 alır.   
- Farklı yük artışı desenlerini test etme
  - Bkz. [konuşmayı metne dönüştürme örneği](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Aynı veya farklı bölgelerde daha fazla konuşma kaynağı oluşturun ve iş yükünü "hepsini bir kez deneme" tekniğinin ardından aralarında dağıtın. Bu özellikle, konuşma kaynağı başına 200 olarak ayarlanan ve ayarlanmayacak olan **metinden konuşmaya TPS (saniye başına işlem)** parametresi için önemlidir  

Sonraki bölümlerde, kotaları ayarlama hakkında belirli durumlar açıklanır.<br/>
[Metin okuma 'ya atlayın. Özel ses için eşzamanlı Istek sınırını artırma](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Konuşmayı metne dönüştürme: çevrimiçi döküm eşzamanlı istek sınırını artırma
Varsayılan olarak, eş zamanlı istek sayısı, konuşma kaynağı başına 100 (temel model) ve özel uç nokta başına 20 (özel model) ile sınırlıdır. Standart fiyatlandırma katmanı için bu miktar artırılabilir. İsteği göndermeden önce, [Bu bölümün](#detailed-description-quota-adjustment-and-best-practices) malzemesini öğrendiğinizden ve bu [en iyi uygulamalardan](#general-best-practices-to-mitigate-throttling-during-autoscaling)haberdar olduğunuzdan emin olun.

>[!NOTE]
> Özel modeller kullanıyorsanız, bir konuşma kaynağının birçok özel model dağıtımını barındıran çok sayıda özel uç nokta ile ilişkilendirilebilen lütfen unutmayın. Her özel uç nokta, oluşturma tarafından ayarlanan varsayılan eşzamanlı istek sınırı (20) sayısına sahiptir. Bu ayarı değiştirmeniz gerekiyorsa, her özel uç noktanın ayarlamasını **ayrı ayrı** yapmanız gerekir. Ayrıca, bir konuşma kaynağının temel modeli için eşzamanlı istek sınırı sayısının bu kaynakla ilişkili özel uç noktalar üzerinde **hiçbir** etkisi olmadığını unutmayın.


Eşzamanlı Istek limitini **artırmak, maliyetlerinizi doğrudan etkilemez** . Konuşma hizmeti "yalnızca kullandığınız kadar ödeyin" modeli kullanır. Sınır, hizmetin isteklerinizi kısıtlama başlamadan önce ne kadar yüksek ölçeklenebileceğini tanımlar.

**Taban** ve **özel** modellerin eşzamanlı istek sınırları **ayrı ayrı** ayarlanması gerekir.

Eşzamanlı Istek sınırı parametresinin mevcut değeri Azure portal, Command-Line araçları veya API istekleri aracılığıyla görünür **değil** . Mevcut değeri doğrulamak için bir Azure destek Isteği oluşturun.

>[!NOTE]
>Kapsayıcılar yalnızca barındırıldığı donanımın CPU 'Ları ile sınırlandırıldığından, [konuşma kapsayıcıları](speech-container-howto.md) eşzamanlı istek sınırı artışı gerektirmez. Ancak, konuşma kapsayıcılarının hesaba alınması gereken kendi kapasite sınırlamaları vardır. *"Şirket Içi konuşmadan metin kapsayıcıları için kapasite planlaması ve maliyet tahmini konusunda yardımcı olabilir* " sorusuna bakın. [konuşma kapsayıcıları hakkında SSS bölümüne bakın](./speech-container-howto.md).

#### <a name="have-the-required-information-ready"></a>Gerekli bilgileri hazırlayın:
- **Taban model** için:
  - Konuşma kaynak KIMLIĞI
  - Bölge
- **Özel model** için: 
  - Bölge
  - Özel uç nokta KIMLIĞI

- **Bilgi alma (temel model)**:  
  - [Azure Portal](https://portal.azure.com/) git
  - Eşzamanlılık Istek sınırını artırmak istediğiniz konuşma kaynağını seçin
  - *Özellikleri* seçin (*kaynak yönetim* grubu) 
  - Aşağıdaki alanların değerlerini kopyalayın ve kaydedin:
    - **Kaynak KIMLIĞI**
    - **Konum** (uç nokta bölgeniz)

- **Bilgi alma (özel model)**:
  - [Konuşma Studio](https://speech.microsoft.com/) portalına git
  - Gerekirse oturum açın
  - Özel Konuşma Tanıma Git
  - Projenizi seçin
  - *Dağıtıma* git
  - Gerekli uç noktayı seçin
  - Aşağıdaki alanların değerlerini kopyalayın ve kaydedin:
    - **Hizmet bölgesi** (uç nokta bölgeniz)
    - **Uç nokta KIMLIĞI**
  
#### <a name="create-and-submit-support-request"></a>Destek isteği oluştur ve Gönder
Kaynağınız için eşzamanlı Istek sınırının artışını başlatın veya gerekirse destek Isteği göndererek bugünün sınırını denetleyin:

- [Gerekli bilgilere](#have-the-required-information-ready) sahip olduğunuzdan emin olun
- [Azure Portal](https://portal.azure.com/) git
- Eşzamanlılık Isteği sınırını artırmak (veya denetlemek için) istediğiniz konuşma kaynağını seçin
- *Yeni destek isteği* seçin (*destek + sorun giderme* grubu) 
- Azure aboneliğiniz ve Azure kaynağınız hakkında otomatik doldurulmuş bilgilerle yeni bir pencere görüntülenir
- *Özet* girin ("STT eşzamanlılık Istek sınırını Artır" gibi)
- *Sorun türü* "kota veya abonelik sorunları" seçeneğini belirleyin
- Görünen *sorun alt türü* şunu seçin:
  - "Kota veya eşzamanlı istekler artar"-bir artış isteği için
  - Var olan limiti denetlemek için kota veya kullanım doğrulaması "
- Ileri ' ye tıklayın *: çözümler*
- İstek oluşturma ile daha fazla ilerleyin
- *Ayrıntılar* sekmesinde *Açıklama* alanına yazın:
  - isteğin **konuşmaya metin** kotası hakkında olduğuna ilişkin bir Note
  - **Taban** veya **özel** model
  - Daha [önce topladığınız](#have-the-required-information-ready) Azure Kaynak bilgileri 
  - Gerekli bilgileri girmeyi ve *gözden geçir + oluştur* sekmesinde *Oluştur* düğmesine tıklayın
  - Azure portal bildirimlerde destek istek numarasını aklınızda edin. Daha fazla işlem için kısa bir süre önce sizinle iletişim kurulacaksınız

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Konuşmayı metne dönüştürme: iş yükü deseninin en iyi uygulaması örneği
Bu örnek, [Otomatik ölçeklendirme devam ettiğinden olası istek azaltmasını](#detailed-description-quota-adjustment-and-best-practices)azaltmak için aşağıdaki önerdiğimiz yaklaşımı gösterir. Bu bir "tam tarif" değildir, ancak yalnızca takip etmeyi ve gereken şekilde ayarlamayı davet ederiz.

Bir konuşma kaynağının eşzamanlı Istek sınırının 300 olarak ayarlandığını varsayalım. 20 eşzamanlı bağlantıyla iş yükünü başlatın ve günde her 1,5-2 dakikada 20 eşzamanlı bağlantıyı artırın. Hizmet yanıtlarını denetleyin ve çok fazla yanıt kodu 429 alırsanız geri kalan mantığı uygulayın (yükü azaltır). Ardından 1-2-4-4 dakikalık bir düzende yeniden deneyin. (Bu, yük artışını 1 dakika içinde yeniden dener, hala işe yaramazsa 2 dak, vb.)

Genel olarak, üretime geçmeden önce iş yükünün ve iş yükü desenlerinin test yapılması önemle önerilir.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Metinden konuşmaya: özel ses için eşzamanlı istek sınırını artırma
Varsayılan olarak, bir özel ses bitiş noktası için eşzamanlı istek sayısı 10 ile sınırlıdır. Standart fiyatlandırma katmanı için bu miktar artırılabilir. İsteği göndermeden önce, [Bu bölümün](#detailed-description-quota-adjustment-and-best-practices) malzemesini öğrendiğinizden ve bu [en iyi uygulamalardan](#general-best-practices-to-mitigate-throttling-during-autoscaling)haberdar olduğunuzdan emin olun.

Eşzamanlı Istek limitini **artırmak, maliyetlerinizi doğrudan etkilemez** . Konuşma hizmeti "yalnızca kullandığınız kadar ödeyin" modeli kullanır. Sınır, hizmetin isteklerinizi kısıtlama başlamadan önce ne kadar yüksek ölçeklenebileceğini tanımlar.

Eşzamanlı Istek sınırı parametresinin mevcut değeri Azure portal, Command-Line araçları veya API istekleri aracılığıyla görünür **değil** . Mevcut değeri doğrulamak için bir Azure destek Isteği oluşturun.

>[!NOTE]
>Kapsayıcılar yalnızca barındırıldığı donanımın CPU 'Ları ile sınırlandırıldığından, [konuşma kapsayıcıları](speech-container-howto.md) eşzamanlı istek sınırı artışı gerektirmez.

#### <a name="prepare-the-required-information"></a>Gerekli bilgileri hazırlayın:
Bir artış isteği oluşturmak için dağıtım bölgenizi ve özel uç nokta KIMLIĞINI sağlamanız gerekir. Bunu almak için aşağıdaki eylemleri gerçekleştirin: 

- [Konuşma Studio](https://speech.microsoft.com/) portalına git
- Gerekirse oturum açın
- *Özel sese* git
- Projenizi seçin
- *Dağıtıma* git
- Gerekli uç noktayı seçin
- Aşağıdaki alanların değerlerini kopyalayın ve kaydedin:
    - **Hizmet bölgesi** (uç nokta bölgeniz)
    - **Uç nokta KIMLIĞI**
  
#### <a name="create-and-submit-support-request"></a>Destek isteği oluştur ve Gönder
Kaynağınız için eşzamanlı Istek sınırının artışını başlatın veya gerekirse destek Isteği göndererek bugünün sınırını denetleyin:

- [Gerekli bilgilere](#prepare-the-required-information) sahip olduğunuzdan emin olun
- [Azure Portal](https://portal.azure.com/) git
- Eşzamanlılık Isteği sınırını artırmak (veya denetlemek için) istediğiniz konuşma kaynağını seçin
- *Yeni destek isteği* seçin (*destek + sorun giderme* grubu) 
- Azure aboneliğiniz ve Azure kaynağınız hakkında otomatik doldurulmuş bilgilerle yeni bir pencere görüntülenir
- *Özet* girin ("TTS özel uç nokta eşzamanlılık Istek sınırını Artır" gibi)
- *Sorun türü* "kota veya abonelik sorunları" seçeneğini belirleyin
- Görünen *sorun alt türü* şunu seçin:
  - "Kota veya eşzamanlı istekler artar"-bir artış isteği için
  - Var olan limiti denetlemek için kota veya kullanım doğrulaması "
- Ileri ' ye tıklayın *: çözümler*
- İstek oluşturma ile daha fazla ilerleyin
- *Ayrıntılar* sekmesinde *Açıklama* alanına yazın:
  - isteğin **metin okuma** kotasıyla ilgili olduğunu belirten bir Note
  - Daha [önce topladığınız](#prepare-the-required-information) Azure Kaynak bilgileri 
  - Gerekli bilgileri girmeyi ve *gözden geçir + oluştur* sekmesinde *Oluştur* düğmesine tıklayın
  - Azure portal bildirimlerde destek istek numarasını aklınızda edin. Daha fazla işlem için kısa bir süre önce sizinle iletişim kurulacaksınız