---
title: Uygulamanızı planlayın-Soru-Cevap Oluşturma
description: Soru-Cevap Oluşturma uygulamanızı nasıl planlayacağınızı öğrenin. Soru-Cevap Oluşturma nasıl çalıştığını ve diğer Azure hizmetleriyle ve bazı Bilgi Bankası kavramlarıyla nasıl etkileşime gireceğini anlayın.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e20679c3999f7ece1f6d3ed47a241cfd9dab9236
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102214754"
---
# <a name="plan-your-qna-maker-app"></a>Soru-Cevap Oluşturma Uygulamanızı planlayın

Soru-Cevap Oluşturma uygulamanızı planlamak için, Soru-Cevap Oluşturma nasıl çalıştığını ve diğer Azure hizmetleriyle nasıl etkileşime gireceğini anlamanız gerekir. Ayrıca, düz bir Bilgi Bankası kavramınız olmalıdır.

## <a name="azure-resources"></a>Azure kaynakları

Soru-Cevap Oluşturma ile oluşturulan her [Azure kaynağının](azure-resources.md#resource-purposes) belirli bir amacı vardır. Her kaynağın kendi amacı, limitleri ve [fiyatlandırma katmanı](azure-resources.md#pricing-tier-considerations)vardır. Bu bilgileri planlama sürecinizde kullanabilmeniz için bu kaynakların işlevini anlamanız önemlidir.

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

| Kaynak | Amaç |
|--|--|
| [Soru-cevap oluşturma](azure-resources.md#qna-maker-resource) kaynağı | Yazma ve sorgu tahmini |
| [Bilişsel arama](azure-resources.md#cognitive-search-resource) kaynağı | Veri depolama ve arama |
| [Kaynak ve uygulama planı hizmet kaynağı App Service](azure-resources.md#app-service-and-app-service-plan) | Sorgu tahmin uç noktası |
| [Application Insights](azure-resources.md#application-insights) kaynağı | Sorgu tahmin telemetrisi |

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

| Kaynak | Amaç |
|--|--|
| [Soru-cevap oluşturma](azure-resources.md#qna-maker-resource) kaynağı | Yazma, sorgu tahmini uç noktası ve telemetri|
| [Bilişsel arama](azure-resources.md#cognitive-search-resource) kaynağı | Veri depolama ve arama |

---
### <a name="resource-planning"></a>Kaynak planlama

`F0`Her bir kaynağın ücretsiz katmanı işe yarar ve hem yazma hem de sorgu tahmin deneyimini sağlayabilir. Bu katmanı, yazma ve sorgu tahminini öğrenmek için kullanabilirsiniz. Bir üretime veya canlı senaryoya geçtiğinizde, kaynak seçiminizi yeniden değerlendirmeniz gerekir.

#### <a name="qna-maker-resource"></a>Soru-Cevap Oluşturma kaynağı

Tek bir Soru-Cevap Oluşturma kaynağı, birden fazla bilgi tabanını barındırabilir. Bilgi tabanı sayısı, Bilişsel Arama fiyatlandırma katmanının desteklenen dizin miktarına göre belirlenir. [Bilgi temellerine dizinlerin ilişkisi](azure-resources.md#index-usage)hakkında daha fazla bilgi edinin.

#### <a name="knowledge-base-size-and-throughput"></a>Bilgi Bankası boyutu ve aktarım hızı

Gerçek bir uygulama oluşturduğunuzda, bilgi Bankalarınızın boyutuna ve beklenen sorgu tahmin istekleriniz için yeterli kaynakları planlayın.

Bilgi Bankası boyutu tarafından denetlenir:
* [Bilişsel arama kaynak](../../../search/search-limits-quotas-capacity.md) fiyatlandırma katmanı limitleri
* [Soru-Cevap Oluşturma sınırları](../limits.md)

Bilgi Bankası sorgu tahmini isteği, Web uygulaması planı ve Web uygulaması tarafından denetlenir. Fiyatlandırma katmanınızı planlamak için [önerilen ayarlar](azure-resources.md#recommended-settings) ' a bakın.

### <a name="resource-sharing"></a>Kaynak paylaşma

Bu kaynaklardan bazıları zaten kullanımda ise, kaynakları paylaşmayı düşünebilirsiniz. Kaynak paylaşımının gelişmiş bir senaryo olduğunu anlamak için hangi kaynakların [paylaşılabileceğini](azure-resources.md#share-services-with-qna-maker) görün.

Aynı Soru-Cevap Oluşturma kaynakta oluşturulan tüm bilgi tabanları aynı **Test** sorgu tahmin uç noktasını paylaşır.

### <a name="understand-the-impact-of-resource-selection"></a>Kaynak seçiminin etkisini anlayın

Doğru kaynak seçimi, bilgi Bankalarınızın sorgu tahminleri başarıyla yanıtladığı anlamına gelir.

Bilgi tabanınız düzgün çalışmıyorsa, genellikle hatalı Kaynak yönetiminin bir sorunu olur.

Hatalı kaynak seçimi, hangi [kaynağın değişmesi](azure-resources.md#when-to-change-a-pricing-tier)gerektiğini belirlemede araştırma gerektiriyor.

## <a name="knowledge-bases"></a>Bilgi Bankası tabanları

Bilgi Bankası, Soru-Cevap Oluşturma kaynağına doğrudan bağlıdır. Sorgu tahmin isteklerini yanıtlamak için kullanılan soru ve yanıt (QnA) çiftlerini barındırır.

### <a name="language-considerations"></a>Dil konuları

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Soru-Cevap Oluşturma kaynağınız üzerinde oluşturulan ilk bilgi tabanı, kaynağın dilini ayarlar. Bir Soru-Cevap Oluşturma kaynağı için yalnızca bir dile sahip olabilirsiniz.

Soru-Cevap Oluşturma kaynaklarınızı dile göre oluşturabilir veya sorguyu sorgu tahmin uç noktasına göndermeden önce, başka bir dildeki sorguyu bilgi bankasındaki dile dönüştürmek için [çeviriciyi](../../translator/translator-info-overview.md) kullanabilirsiniz.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Artık aynı Soru-Cevap Oluşturma kaynağı dahilinde farklı dillerde bilgi esaslarını sağlayabilirsiniz. İlk Bilgi Bankası 'nı oluştururken, kaynak bilgilerini tek bir dilde veya birden çok dilde kullanmak isteyip istemediğinizi seçebilirsiniz.

![Soru-Cevap Oluşturma yönetilen (Önizleme) çok dilli Bilgi Bankası seçimi](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

> [!NOTE]
> Her Bilgi Bankası için dil ayarlarını etkinleştirirseniz Soru-Cevap Oluşturma kaynağınız için pek çok bilgi temeli oluşturamazsınız. [Dil ayarları sınırlamaları hakkında daha fazla bilgi](./azure-resources.md)için.

---

### <a name="ingest-data-sources"></a>Alma veri kaynakları

Bilgi Bankası oluşturmak için aşağıdaki [veri kaynaklarından](../Concepts/data-sources-and-content.md) birini kullanabilirsiniz:

* Genel URL
* Özel SharePoint URL 'SI
* Dosya

Alma işlemi, [desteklenen içerik türlerini](../reference-document-format-guidelines.md) markaşağı dönüştürür. *Yanıtın* daha fazla düzenlemesi markın ile yapılır. Bilgi Bankası oluşturduktan sonra, Soru-Cevap Oluşturma portalında [QNA çiftlerini](question-answer-set.md) [zengin metin yazma](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)ile düzenleyebilirsiniz.

### <a name="data-format-considerations"></a>Veri biçimi konuları

Bir QnA çiftinin son biçimi markın olduğundan, [markı desteğinin](../reference-markdown-format.md)anlaşılması önemlidir.

Bağlı görüntülerin, Soru-Cevap Oluşturma portalının test bölmesinde veya bir istemci uygulamasında görüntülenebilmesi için genel bir URL 'den erişilebilir olması gerekir. Soru-Cevap Oluşturma görüntüler dahil içerik için kimlik doğrulaması sağlamaz.

### <a name="bot-personality"></a>Bot kişiliği

[Kiıt-chat](../how-to/chit-chat-knowledge-base.md)ile bilgi tabanınız için bir bot kişilik ekleyin. Bu kişilik, *profesyonel* ve *kolay* gibi belirli bir konuşma tonlarında verilen yanıtlarla birlikte gelir. Bu CHIT-chat, ekleme, düzenleme ve kaldırma için toplam denetime sahip olduğunuz bir konuşma kümesi olarak sunulmaktadır.

Bot 'niz bilgi tabanınızı bağladığında bir bot kişiliği önerilir. Diğer hizmetlere da bağlansanız bile, bilgi tabanınızda CHIT-chat kullanmayı seçebilirsiniz, ancak bu, sizin için doğru mimari tasarımı olup olmadığını öğrenmek için bot hizmetinin nasıl etkileşime gireceğini gözden geçirmeniz gerekir.

### <a name="conversation-flow-with-a-knowledge-base"></a>Bilgi Bankası ile görüşme akışı

Konuşma akışı genellikle, veya gibi bir kullanıcıdan selamlama ile başlar `Hi` `Hello` . Bilgi tabanınız gibi genel bir Yanıt ile yanıt verebilir `Hi, how can I help you` ve ayrıca konuşmaya devam etmek için bir izleme istemleri seçimi sağlayabilir.

Bir kullanıcının botunuzu nasıl kullanacağınızı bilmesi ve konuşmada bot tarafından terk olmaması için konuşma akışınızı göz önünde bir döngüyle tasarlamanız gerekir. [Takip eden istemler](../how-to/multiturn-conversation.md) , konuşma akışına Izin veren QNA çiftleri arasında bağlama sağlar.

### <a name="authoring-with-collaborators"></a>Ortak çalışanlarla yazma

Ortak çalışanlar, Bilgi Bankası uygulamasının tam geliştirme yığınını paylaşan diğer geliştiriciler olabilir veya yalnızca Bilgi Bankası yazma ile sınırlı olabilir.

Bilgi Bankası yazma, ortak çalışan becerileri kapsamını sınırlandırmak için Azure portal uyguladığınız çeşitli rol tabanlı erişim izinlerini destekler.

## <a name="integration-with-client-applications"></a>İstemci uygulamalarıyla tümleştirme

İstemci uygulamalarıyla tümleştirme, tahmin çalışma zamanı uç noktasına bir sorgu gönderilerek gerçekleştirilir. Bir sorgu, Soru-Cevap Oluşturma Web uygulaması uç noktasına SDK veya REST tabanlı bir istek ile özel bilgi tabanıza gönderilir.

İstemci isteğinin doğru şekilde doğrulanması için, istemci uygulamanın doğru kimlik bilgilerini ve Bilgi Bankası KIMLIĞINI gönderebilmesi gerekir. Bir Azure bot hizmeti kullanıyorsanız, bu ayarları Azure portal bot yapılandırmasının bir parçası olarak yapılandırın.

### <a name="conversation-flow-in-a-client-application"></a>İstemci uygulamasında konuşma akışı

Azure bot gibi bir istemci uygulamasındaki konuşma akışı, Bilgi Bankası ile etkileşime girmeden önce ve sonra işlevselliği gerektirebilir.

İstemci uygulamanız konuşma akışını, izleme istemlerini işlemek için alternatif bir yol sunarak veya Chit-CHIT dahil olmak üzere destekliyor mu? Bu durumda, bunları erken tasarlayın ve istemci uygulama sorgusunun başka bir hizmet tarafından veya bilgi tabanınıza gönderildiğinde doğru şekilde işlendiğinden emin olun.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Soru-Cevap Oluşturma ve Language Understanding arasında dağıtım (LUSıS)

Bir istemci uygulaması, yalnızca biri bir Bilgi Bankası tarafından yanıtlanarak çeşitli özellikler sağlayabilir. Diğer özelliklerin yine de konuşma metnini anlaması ve bundan anlamı ayıklamanız gerekir.

Ortak bir istemci uygulama mimarisi hem Soru-Cevap Oluşturma hem de [Language Understanding (LUIS)](../../LUIS/what-is-luis.md) birlikte kullanılır. LUO, diğer hizmetlere dahil olmak üzere herhangi bir sorgu için metin sınıflandırması ve ayıklama sağlar. Soru-Cevap Oluşturma bilgi tabanınızdan yanıt verir.

Bu tür bir [paylaşılan mimari](../choose-natural-language-processing-service.md) senaryosunda, iki hizmet arasındaki gönderim, bot çerçevesinin [dağıtım](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) aracı tarafından gerçekleştirilir.

### <a name="active-learning-from-a-client-application"></a>İstemci uygulamasından etkin öğrenme

Soru-Cevap Oluşturma, yanıt için alternatif sorular önererek bilgi bankasını geliştirmek üzere _etkin öğrenimi_ kullanır. Bu [etkin öğrenimi](../How-To/use-active-learning.md)'nin bir parçası olan istemci uygulaması sorumludur. Konuşma istemleriyle, istemci uygulaması bilgi bankasından Kullanıcı için faydalı olmayan bir yanıt döndürmeyeceğini ve daha iyi bir yanıt belirleyebileceğini belirleyebilir. Tahmin kalitesini artırmak için istemci uygulamanın bu bilgileri bilgi bankasına geri gönderebilmesi gerekir.

### <a name="providing-a-default-answer"></a>Varsayılan bir yanıt sağlama

Bilgi tabanınız bir yanıt bulamazsa, _varsayılan yanıtı_ döndürür. Bu yanıt, Soru-Cevap Oluşturma portalındaki veya [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)'lerde **Ayarlar** sayfasında yapılandırılabilir.

Bu varsayılan yanıt, Azure bot varsayılan cevabı 'ndan farklıdır. Azure portal Azure bot için varsayılan yanıtı yapılandırma ayarlarının bir parçası olarak yapılandırırsınız. Puan eşiğine uyulmazsa döndürülür.

## <a name="prediction"></a>Tahmin

Tahmin, bilgi tabanınızdan alınan yanıttır ve yalnızca yanıta göre daha fazla bilgi içerir. Sorgu tahmin yanıtı almak için [Generateanswer API](query-knowledge-base.md)'sini kullanın.

### <a name="prediction-score-fluctuations"></a>Tahmin puanı dalgalanmaları

Puan, çeşitli faktörlere göre değişebilir:

* Özelliği ile [Generateanswer](query-knowledge-base.md) 'a yanıt olarak istediğiniz yanıt sayısı `top`
* Çeşitli kullanılabilir alternatif sorular
* Meta veriler için filtreleme
* Sorgu `test` veya `production` Bilgi Bankası 'na gönderilen sorgu

[İki aşamalı bir yanıt derecelendirmesi](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)vardır:
- Bilişsel Arama-ilk derece. En iyi yanıtların Bilişsel Arama tarafından döndürüldüğünden ve sonra Soru-Cevap Oluşturma Ranker içine geçirildiğinden, _izin verilen yanıtların_ sayısını yeterince yüksek olarak ayarlayın.
- Soru-Cevap Oluşturma saniyelik derece. En iyi yanıtı öğrenmek için özelliği ve makine öğrenimini uygulayın.

### <a name="service-updates"></a>Hizmet güncelleştirmeleri

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Hizmet güncelleştirmelerini otomatik olarak yönetmek için [en son çalışma zamanı güncelleştirmelerini](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) uygulayın.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Soru-Cevap Oluşturma yönetilen (Önizleme) içinde, çalışma zamanı Soru-Cevap Oluşturma hizmetin kendisi tarafından yönetilir. Bu nedenle, hizmet güncelleştirmeleri geçerli değildir.

---

### <a name="scaling-throughput-and-resiliency"></a>Ölçeklendirme, verimlilik ve dayanıklılık

Ölçeklendirme, verimlilik ve dayanıklılık, [Azure kaynakları](../how-to/set-up-qnamaker-service-azure.md), fiyatlandırma katmanları ve [Traffic Manager](../how-to/configure-QnA-Maker-resources.md#business-continuity-with-traffic-manager)gibi herhangi bir çevreleyen mimari tarafından belirlenir.

### <a name="analytics-with-application-insights"></a>Application Insights ile analiz

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Bilgi bankalarınıza yönelik tüm sorgular Application Insights depolanır. Ölçümlerinizi anlamak için [en iyi sorguları](../how-to/get-analytics-knowledge-base.md) kullanın.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Yönetilen dağıtımda, telemetri [Azure İzleyici hizmeti](../../../azure-monitor/index.yml)aracılığıyla sunulur. Ölçümlerinizi anlamak için [en iyi sorguları](../how-to/get-analytics-knowledge-base.md) kullanın.


---

## <a name="development-lifecycle"></a>Geliştirme yaşam döngüsü

Bilgi bankasındaki [geliştirme yaşam döngüsü](development-lifecycle-knowledge-base.md) devam etmektedir: bilgi tabanınızı Düzenle, test etme ve yayımlama.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Soru-Cevap Oluşturma çiftleri hakkında bilgi Bankası geliştirme

QnA Pairs, istemci uygulaması kullanımınıza göre tasarlanıp geliştirilebilmelidir.

Her bir çift şunları içerebilir:
* Meta veri-filtreleyerek, QnA çiftlerinizi verilerinize kaynak, içerik, biçim ve amaç hakkında ek bilgilerle etiketleyebilmesini sağlayacak şekilde sorgulama yapılır.
* İzleme istemleri-Kullanıcı doğru yanıta ulaşabilmesi için bilgi tabanınız aracılığıyla bir yol belirlemesine yardımcı olur.
* Diğer sorular-aramanın farklı formlardaki yanıtınızı eşleştirmeye izin vermek için önemlidir. [Etkin öğrenme önerileri](../How-To/use-active-learning.md) , alternatif sorulara sahiptir.

### <a name="devops-development"></a>DevOps geliştirme

Bir DevOps ardışık düzenine eklemek için Bilgi Bankası geliştirme işlemi, bilgi bankasını toplu test sırasında yalıtıyor olması gerekir.

Bilgi Bankası, Bilişsel Arama dizinini Soru-Cevap Oluşturma kaynaktaki diğer tüm bilgi bankalarıyla paylaşır. Bilgi Bankası, Bölüm tarafından yalıtılırken, Dizin paylaşımı, yayımlanan bilgi tabanı ile karşılaştırıldığında puanta farklılık oluşmasına neden olabilir.

Ve bilgi bankalarında _aynı puanı_ elde etmek için `test` `production` bir soru-cevap oluşturma kaynağını tek bir Bilgi Bankası ile yalıtın. Bu mimaride, kaynağın yalnızca yalıtılmış toplu iş testi süresi kadar canlı olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure kaynakları](../how-to/set-up-qnamaker-service-azure.md)
* [Soru ve yanıt çiftleri](question-answer-set.md)
