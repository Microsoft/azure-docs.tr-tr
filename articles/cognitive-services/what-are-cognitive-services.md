---
title: Azure bilişsel hizmetler nedir?
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler, makine öğrenimi ve veri bilimi uzmanlığı gerekmeden AI her geliştirici için erişilebilir hale getirir. Uygulamalarınızdan bir API çağrısı yapmanız yeterlidir (Gelişmiş görüntü araması ve tanıması), duyduklarınız, konuşmanız, arama ve karar verme özelliklerini uygulamanıza ekleyebilirsiniz.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: bilişsel hizmetler, bilişsel zeka, bilişsel çözümler, AI Hizmetleri, bilişsel
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: c89131cc34d45ea94f3bb290ac11ec86f4b83be3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587621"
---
# <a name="what-are-azure-cognitive-services"></a>Azure bilişsel hizmetler nedir?

Azure bilişsel hizmetler, uygulamalarınızın bilişsel zekasını oluşturmanıza yardımcı olacak REST API 'Leri ve istemci kitaplığı SDK 'Ları bulunan bulut tabanlı hizmetlerdir. Yapay zeka (AI) veya veri bilimi becerileri olmadan uygulamalarınıza bilişsel özellikler ekleyebilirsiniz. Azure bilişsel hizmetler, kararları görebileceği, duymasına, konuşmanıza, anlamanıza ve hatta kararlar veren bilişsel çözümler oluşturmanıza olanak sağlayan çeşitli AI hizmetlerini içerir.

## <a name="categories-of-cognitive-services"></a>Bilişsel hizmetler kategorileri

Bilişsel hizmetler 'in bilişsel olarak anlaşılmasına yönelik Katalog, beş ana paragraf halinde kategorize edilir:

* Görsel
* Konuşma
* Dil
* Karar
* Arayın

Bu makalede yer alan aşağıdaki bölümlerde, bu beş sütun sayısının parçası olan hizmetlerin bir listesi yer almaktadır.

## <a name="vision-apis"></a>Vision API 'Leri

|Hizmet Adı|Hizmet Açıklaması|
|:-----------|:------------------|
|[Görüntü İşleme](./computer-vision/index.yml "Görüntü İşleme")|Görüntü İşleme Hizmeti, görüntüleri işlemeye ve bilgi döndürmeye yönelik gelişmiş bilişsel algoritmalara erişmenizi sağlar. Hizmeti kullanmaya başlamak için bkz. [görüntü işleme hızlı başlangıç](./computer-vision/quickstarts-sdk/client-library.md) .|
|[Özel Görüntü İşleme Hizmeti](./custom-vision-service/index.yml "Özel Görüntü İşleme Hizmeti")|Özel Görüntü İşleme Hizmeti kendi görüntü sınıflandırıcılarınızı oluşturmanıza, dağıtmanıza ve iyileştirmenize olanak tanır. Görüntü Sınıflandırıcısı, görsel özelliklerine göre resimlere etiket uygulayan bir AI hizmetidir. |
|[Yüz Tanıma](./face/index.yml "Yüz")| Yüz tanıma hizmeti, yüz öznitelik algılamayı ve tanımayı etkinleştirerek gelişmiş yüz algoritmalarına erişim sağlar. Hizmeti kullanmaya başlamak için bkz. [yüz hızlı başlangıç](./face/quickstarts/client-libraries.md) .|
|[Form Tanıma](./form-recognizer/index.yml "Form Tanıma")|Form tanıyıcı, form belgelerinden anahtar-değer çiftlerini ve tablo verilerini tanımlar ve ayıklar; ardından, özgün dosyadaki ilişkiler dahil olmak üzere yapılandırılmış verilerin çıktısını verir. Başlamak için [form tanıyıcı hızlı başlangıç](./form-recognizer/quickstarts/client-library.md) bölümüne bakın.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer, videonuzun öngörülerini ayıklamanızı sağlar. Başlamak için [video Indexer hızlı başlangıç](/azure/media-services/video-indexer/video-indexer-get-started) bölümüne bakın.|

## <a name="speech-apis"></a>Konuşma API 'Leri

|Hizmet Adı|Hizmet Açıklaması|
|:-----------|:------------------|
|[Konuşma hizmeti](./speech-service/index.yml "Konuşma hizmeti")|Konuşma hizmeti uygulamalara konuşma özellikli özellikler ekler. Konuşma hizmeti, konuşmadan metne, metinden konuşmaya, konuşma çevirisi ve çok daha fazlası gibi çeşitli yetenekler içerir.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Dil API 'Leri

|Hizmet Adı|Hizmet Açıklaması|
|:-----------|:------------------|
|[LUSıS Language Understanding](./luis/index.yml "Language Understanding")|Language Understanding (LUSıS), bir kullanıcının konuşma, genel anlamı tahmin etmek için doğal dil metnine özel makine öğrenimi zekası uygulayan ve ilgili ve ayrıntılı bilgileri kullanıma sunan bulut tabanlı bir konuşma hizmetidir. Hizmeti kullanmaya başlamak için [lusıs hızlı başlangıç bölümüne bakın](./luis/get-started-portal-build-app.md) .|
|[Soru-Cevap Oluşturma](./qnamaker/index.yml "Soru-Cevap Oluşturucu")|Soru-Cevap Oluşturma yarı yapılandırılmış içeriklerden bir soru ve yanıt hizmeti oluşturmanızı sağlar. Hizmeti kullanmaya başlamak için [bkz. soru-cevap oluşturma hızlı başlangıç](./qnamaker/quickstarts/create-publish-knowledge-base.md) .|
|[Metin Analizi](./text-analytics/index.yml "Metin Analizi")| Metin Analizi, yaklaşım analizi, anahtar ifade ayıklama ve dil algılama için ham metin üzerinde doğal dil işleme sağlar. Hizmeti kullanmaya başlamak için bkz. [metin analizi hızlı başlangıç](./text-analytics/quickstarts/client-libraries-rest-api.md) .|
|[Translator](./translator/index.yml "Çevirmen")|Translator, neredeyse gerçek zamanlı olarak makine tabanlı metin çevirisi sağlar.|
| [Tam Ekran Okuyucu](./immersive-reader/index.yml "Tam Ekran Okuyucu") | Modern okuyucu, uygulamalarınıza ekran okuma ve kavrama özellikleri ekler. Hizmeti kullanmaya başlamak için bkz. [modern okuyucu hızlı](./immersive-reader/quickstarts/client-libraries.md) başlangıcı. |

## <a name="decision-apis"></a>Karar API 'Leri

|Hizmet Adı|Hizmet Açıklaması|
|:-----------|:------------------|
|[Anomali Algılayıcısı](./anomaly-detector/index.yml "Anomali Algılayıcısı") |Anomali algılayıcısı, zaman serisi verilerinizde normalleştirimler izlemenize ve bunları algılamanıza olanak tanır. Hizmeti kullanmaya başlamak için bkz. [anomali algılayıcısı hızlı başlangıç](./anomaly-detector/quickstarts/client-libraries.md)|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator olası rahatsız edici, istenmeyen ve riskli içerik için izleme sağlar. Hizmeti kullanmaya başlamak için bkz. [Content moderator hızlı başlangıç](./content-moderator/client-libraries.md) .|
|[Ölçüm Danışmanı](./metrics-advisor/index.yml) (Önizleme) | Ölçüm Danışmanı, çok değişkenli bir zaman serisi verilerinde özelleştirilebilir anomali algılama ve hizmeti kullanmanıza yardımcı olacak tam özellikli bir Web portalı sağlar. Hizmeti kullanmaya başlamak için bkz. [ölçüm Danışmanı hızlı](./metrics-advisor/quickstarts/rest-api-and-client-library.md) başlangıcı. |
|[Kişiselleştirme](./personalizer/index.yml "Kişiselleştirme")|Kişiselleştirici, gerçek zamanlı davranışlarından öğrenerek kullanıcılarınıza göstermek için en iyi deneyimi seçmenize olanak sağlar. Hizmeti kullanmaya başlamak için bkz. [kişiselleştirici hızlı başlangıç](./personalizer/quickstart-personalizer-sdk.md) .|

## <a name="search-apis"></a>API Arama

> [!NOTE]
> [Azure bilişsel arama](../search/index.yml)mi arıyorsunuz? Bazı görevler için bilişsel hizmetler kullanmasına karşın, bu, diğer senaryoları destekleyen farklı bir arama teknolojisidir.

|Hizmet Adı|Hizmet Açıklaması|
|:-----------|:------------------|
|[Bing Haber Arama](/azure/cognitive-services/bing-news-search/ "Bing Haber Arama")|Bing Haber Arama, kullanıcının sorgusuyla ilgili olduğunu belirlenen haber makalelerinin bir listesini döndürür.|
|[Bing Video Arama](/azure/cognitive-services/Bing-Video-Search/ "Bing Video Arama")|Bing Video Arama, kullanıcının sorgusuyla ilgili olduğunu belirlenen videoların listesini döndürür.|
|[Bing Web Araması](./bing-web-search/index.yml "Bing Web Araması")|Bing Web Araması, kullanıcının sorgusuyla ilgili olduğunu belirlenen arama sonuçlarının listesini döndürür.|
|[Bing Otomatik Öneri](/azure/cognitive-services/Bing-Autosuggest "Bing Otomatik Öneri")|Bing Otomatik Öneri, Bing 'e kısmi arama sorgu terimi göndermenizi ve önerilen sorguların bir listesini geri almanızı sağlar.|
|[Bing Özel Arama](/azure/cognitive-services/bing-custom-search "Bing Özel Arama")|Bing Özel Arama, ilgilendiğiniz konularda özel arama deneyimleri oluşturmanıza olanak tanır.|
|[Bing Varlık Arama](/azure/cognitive-services/bing-entities-search/ "Bing Varlık Arama")|Bing Varlık Arama, bir kullanıcının sorgusuyla ilgili olarak Bing tarafından belirlediği varlıklar hakkında bilgi döndürür.|
|[Bing Resim Arama](/azure/cognitive-services/bing-image-search "Bing Resim Arama")|Bing Resim Arama, kullanıcının sorgusuyla ilgili olduğunu belirlenen görüntülerin bir görüntüsünü döndürür.|
|[Bing Görsel Arama](/azure/cognitive-services/bing-visual-search "Bing Görsel Arama")|Bing Görsel Arama, görsel açıdan benzer görüntüler, görüntüde bulunan ürünlere yönelik alışveriş kaynakları ve ilgili aramalar gibi bir görüntüyle ilgili öngörüler sağlar.|
|[Bing Yerel İşletme Arama](/azure/cognitive-services/bing-local-business-search/ "Bing Yerel İşletme Arama")| Bing yerel Iş Arama API 'SI, uygulamalarınızın arama sorguları temelinde yerel işletmeler hakkında iletişim ve konum bilgilerini bulmasını sağlar.|
|[Bing Yazım Denetimi](/azure/cognitive-services/bing-spell-check/ "Bing Yazım Denetimi")|Bing Yazım Denetimi bağlamsal dilbilgisi ve yazım denetimi gerçekleştirmenize olanak tanır.|

## <a name="get-started-with-cognitive-services"></a>Bilişsel Hizmetler’i kullanmaya başlama

Aşağıdaki yöntemleri kullanarak uygulamalı hızlı başlangıçlarla bilişsel hizmetler kaynağı oluşturarak başlayın:

* [Azure portalı](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK istemci kitaplıkları](cognitive-services-apis-create-account-cli.md?tabs=windows "bilişsel hizmetler-API 'ler-oluşturma-hesap-istemci-kitaplığı? özetleme = programlama-dil-CSharp")
* [Azure Resource Manager (ARM) şablonları](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager (ARM) şablonları")

## <a name="using-cognitive-services-in-different-development-environments"></a>Bilişsel hizmetler 'i farklı geliştirme ortamlarında kullanma

Azure ve bilişsel hizmetler ile çeşitli geliştirme seçeneklerine erişebilirsiniz, örneğin:

* Logic Apps ve güç otomatikleştirme gibi Otomasyon ve tümleştirme araçları.
* Azure Işlevleri ve App Service gibi dağıtım seçenekleri. 
* Güvenli erişim için bilişsel hizmetler Docker kapsayıcıları.
* Büyük veri senaryoları için Apache Spark, Azure Databricks, Azure SYNAPSE Analytics ve Azure Kubernetes hizmeti gibi araçlar. 

Daha fazla bilgi için bkz. bilişsel [Hizmetler geliştirme seçenekleri](./cognitive-services-development-options.md).

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Bilişsel hizmetler 'i güvenli kullanma

Azure bilişsel hizmetler, Azure Active Directory kimlik bilgileri, geçerli bir kaynak anahtarı ve [Azure sanal ağları](cognitive-services-virtual-networks.md "Azure Sanal Ağları")aracılığıyla [kimlik doğrulaması](authentication.md "kimlik doğrulaması") dahil katmanlı bir güvenlik modeli sağlar.

## <a name="containers-for-cognitive-services"></a>Bilişsel Hizmetler için Kapsayıcılar

 Azure bilişsel hizmetler, şirket içinde Azure 'da bulunan API 'Leri kullanmanıza olanak sağlayan çeşitli Docker kapsayıcıları sağlar. Bu kapsayıcıları kullanmak, bilişsel hizmetler 'i uyumluluk, güvenlik veya diğer işletimsel nedenlerle verilerinize daha yakın hale getirme esnekliği sunar. Bilişsel [Hizmetler kapsayıcıları](cognitive-services-container-support.md "Bilişsel Hizmetler Kapsayıcıları")hakkında daha fazla bilgi edinin.

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Bilişsel hizmetler 'deki API 'Ler, Microsoft tarafından yönetilen ve büyüyen bir veri merkezi ağında barındırılır. [Azure bölge listesindeki](https://azure.microsoft.com/regions "Azure bölge listesi")her bir API için bölgesel kullanılabilirliği bulabilirsiniz.

Henüz desteklemediğimiz bir bölge mi arıyorsunuz? [UserVoice forumumuza](https://cognitive.uservoice.com/ "UserVoice forumu")bir özellik isteği kaydederek bize bilgi verin.

## <a name="supported-cultural-languages"></a>Desteklenen kültürel dilleri

Bilişsel hizmetler, hizmet düzeyinde çok çeşitli kültürel dillerini destekler. Her API için dil kullanılabilirliğini [desteklenen diller listesinde](language-support.md "Desteklenen diller listesi")bulabilirsiniz.

## <a name="certifications-and-compliance"></a>Sertifikalamaları ve uyumluluk

Bilişsel hizmetler, CSA STAR sertifikası, Fedrata orta ve HIPAA BAA gibi sertifikalarıyla karşılaştı. Kendi denetim ve güvenlik incelemelerinizin sertifikalarını [indirebilirsiniz](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "indirin") .

Gizlilik ve veri yönetimini anlamak için [güven merkezine](https://servicetrust.microsoft.com/ "Güven Merkezi")gidin.

## <a name="support"></a>Destek

Bilişsel hizmetler, akıllı uygulamalar oluşturma ile ileriye doğru ileretmenize yardımcı olmak için çeşitli destek seçenekleri sunar. Bilişsel hizmetler Ayrıca, belirli sorularınıza yanıt veren güçlü bir geliştiricilerin topluluğuna sahiptir. Kullanabileceğiniz seçeneklerin tam listesi için bkz. bilişsel [Hizmetler desteği ve yardım seçenekleri](cognitive-services-support-options.md "Bilişsel Hizmetler desteği ve yardım seçenekleri").

## <a name="next-steps"></a>Sonraki adımlar

* [Bilişsel Hizmetler hesabı oluşturma](cognitive-services-apis-create-account.md "Bilişsel Hizmetler hesabı oluşturma")
* [Bilişsel hizmetler docs 'taki yenilikler](whats-new-docs.md "Bilişsel hizmetler docs 'taki yenilikler")
* [Bilişsel hizmetler için maliyetleri planlayın ve yönetin](plan-manage-costs.md)
