---
title: Şirket içinde Azure bilişsel hizmetler kapsayıcıları kullanma
titleSuffix: Azure Cognitive Services
description: Şirket içinde bilişsel hizmetler kullanmak için Docker kapsayıcılarını nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2021
ms.author: aahi
keywords: Şirket içi, Docker, kapsayıcı, Kubernetes
ms.openlocfilehash: c40e91d81df448021be74af768bc9d5952b263dd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588233"
---
# <a name="azure-cognitive-services-containers"></a>Azure Bilişsel Hizmetler kapsayıcıları

Azure bilişsel hizmetler, şirket içinde Azure 'da bulunan API 'Leri kullanmanıza olanak sağlayan çeşitli [Docker Kapsayıcıları](https://www.docker.com/what-container) sağlar. Bu kapsayıcıları kullanmak, bilişsel hizmetler 'i uyumluluk, güvenlik veya diğer işletimsel nedenlerle verilerinize daha yakın hale getirme esnekliği sunar. Kapsayıcı desteği şu anda Azure bilişsel hizmetler 'in bir alt kümesi için kullanılabilir.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kapsayıcılama, bağımlılık & yapılandırma dahil olmak üzere bir uygulama veya hizmetin bir kapsayıcı görüntüsü olarak birlikte paketlendiği yazılım dağıtımına yönelik bir yaklaşımdır. Çok az değişiklikle bir kapsayıcı ana bilgisayarına bir kapsayıcı görüntüsü dağıtılabilir. Kapsayıcılar, bir sanal makineden daha küçük bir parmak izine sahip ve temel alınan işletim sisteminden yalıtılmıştır. Kapsayıcılar, kısa süreli görevlere ait kapsayıcı görüntülerinden oluşturulabilir ve artık gerekli olmadığında kaldırılır.

## <a name="features-and-benefits"></a>Özellikler ve avantajlar

- **Sabit altyapı**: değişikliklere uyum sağlayabilirken, düzenli ve güvenilir bir bilinen sistem parametreleri kümesinden yararlanmak Için DevOps ekiplerini etkinleştirin. Kapsayıcılar, öngörülebilir bir ekosistem içinde Pivot esnekliği sağlar ve yapılandırma drmasını önler.
- **Veriler üzerinde denetim**: bilişsel hizmetler tarafından verilerinizin işlenme yeri seçin. Buluta veri gönderememek ancak Bilişsel Hizmetler API'si erişmeniz gerekiyorsa bu, bu gerekli olabilir. Karma ortamlarda, veriler, yönetim, kimlik ve güvenlik arasında tutarlılık desteği.
- **Model güncelleştirmeleri üzerinde denetim**: çözümlerinde dağıtılan modellerin sürümü oluşturma ve güncelleştirme esnekliği.
- **Taşınabilir mimari**: Azure 'da, şirket içinde ve kenarda dağıtılabilecek taşınabilir bir uygulama mimarisi oluşturulmasına izin vermez. Kapsayıcılar doğrudan [Azure Kubernetes hizmetine](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)veya [Azure Stack](/azure-stack/operator)dağıtılan bir [Kubernetes](https://kubernetes.io/) kümesine dağıtılabilir. Daha fazla bilgi için bkz. [Azure Stack Için Kubernetes dağıtma](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Yüksek aktarım hızı/düşük gecikme süresi**: müşterilerin uygulama mantığına ve verilerine fiziksel olarak yakın şekilde çalışmasını sağlayarak yüksek aktarım hızı ve düşük gecikme süreli gereksinimlere yönelik ölçeklendirme olanağı sağlar. Kapsayıcılar, saniye başına işlem (TPS) değildir ve gerekli donanım kaynaklarını sağladıysanız, her ikisi de ölçeği yukarı ve dışarı işlemek için kullanılabilir.
- **Ölçeklenebilirlik**: sürekli büyüyen ve Kubernetes gibi kapsayıcı düzenleme yazılımıyla birlikte artan popülerlik. ölçeklenebilirlik, Forefront of teknolojik gelişmelerden oluşur. Ölçeklenebilir bir küme kurucusu üzerinde derleme, uygulama geliştirme ve yüksek kullanılabilirlik.

## <a name="containers-in-azure-cognitive-services"></a>Azure bilişsel hizmetler 'deki kapsayıcılar

Azure bilişsel hizmetler kapsayıcıları, her biri Azure bilişsel hizmetler 'deki hizmetlerden bir işlev alt kümesini içeren aşağıdaki Docker Kapsayıcıları kümesini sağlar. Yönergeleri ve görüntü konumlarını aşağıdaki tablolarda bulabilirsiniz. [Kapsayıcı görüntülerinin](containers/container-image-tags.md) listesi de mevcuttur.

### <a name="decision-containers"></a>Karar kapsayıcıları

| Hizmet |  Kapsayıcı | Description | Kullanılabilirlik |
|--|--|--|--|
| [Anomali algılayıcısı][ad-containers] | **Anomali algılayıcısı** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Anomali algılayıcı API 'SI, makine öğrenimi ile zaman serisi verilerinizde bulunan normalleştirikleri izlemenize ve algılamanıza olanak sağlar. | Genel kullanıma sunuldu |

### <a name="language-containers"></a>Dil kapsayıcıları

| Hizmet |  Kapsayıcı | Description | Kullanılabilirlik |
|--|--|--|--|
| [LUIS][lu-containers] |  **Lusıs** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Bir Docker kapsayıcısı olarak da bilinen eğitilen veya yayınlanan Language Understanding modelini bir Docker kapsayıcısına yükler ve kapsayıcının API uç noktalarından sorgu tahminlerine erişim sağlar. Kapsayıcıda sorgu günlüklerini toplayabilir ve uygulamanın tahmin doğruluğunu artırmak için bunları [Luo portalına](https://www.luis.ai) geri yükleyebilirsiniz. | Genel kullanıma sunuldu |
| [Metin Analizi][ta-containers-keyphrase] | **Anahtar ifade ayıklama** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Ana noktaları belirlemek için anahtar tümceleri ayıklar. Örneğin, "The food was delicious and there were wonderful staff" (Yemekler lezzetliydi ve personel harikaydı) giriş metni olduğunda API, "food" (yemek) ve "wonderful staff" (personel harikaydı) ana konuşma noktalarını döndürür. | Önizleme |
| [Metin Analizi][ta-containers-language] |  **Metin dil algılama** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | 120 dilde dile kadar, giriş metninin hangi dilde yazıldığını algılar ve istekte gönderilen her belge için tek bir dil kodu bildirir. Dil kodu, puanın ağırlığını belirten bir puanla eşleştirilir. | Genel kullanıma sunuldu |
| [Metin Analizi][ta-containers-sentiment] | **Yaklaşım Analizi v3** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Olumlu veya negatif yaklaşım hakkında ipuçları için ham metni analiz eder. Bu yaklaşım analizinin bu sürümü, her belge ve tümce için yaklaşım etiketlerini (örneğin *pozitif* veya *negatif*) döndürür. |  Genel kullanıma sunuldu |
| [Metin Analizi][ta-containers-health] |  **Sistem durumu için Metin Analizi** | Yapılandırılmamış klinik metinden tıp bilgilerini ayıklayın. | Geçişli önizleme. [Erişim isteyin][request-access]. |

### <a name="speech-containers"></a>Konuşma kapsayıcıları

> [!NOTE]
> Konuşma kapsayıcılarını kullanmak için bir [çevrimiçi istek formu](https://aka.ms/csgate)doldurmanız gerekir.

| Hizmet |  Kapsayıcı | Description | Kullanılabilirlik |
|--|--|--|
| [Konuşma Hizmeti API’si][sp-containers-stt] |  **Konuşmayı metne** dönüştürme ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Sürekli, gerçek zamanlı konuşmaları metne dönüştürür. | Genel kullanıma sunuldu |
| [Konuşma Hizmeti API’si][sp-containers-cstt] | **Özel konuşma tanıma metin** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Özel bir model kullanarak sürekli gerçek zamanlı konuşmayı metne dönüştürme. | Genel kullanıma sunuldu |
| [Konuşma Hizmeti API’si][sp-containers-tts] | **Metinden konuşmaya** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Metni, doğal sesli konuşmaya dönüştürür. | Genel kullanıma sunuldu |
| [Konuşma Hizmeti API’si][sp-containers-ctts] | **Özel metin okuma** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Özel bir model kullanarak metni doğal-sounkonuşmaya dönüştürür. | Geçişli Önizleme |
| [Konuşma Hizmeti API’si][sp-containers-ntts] | **Sinir metin okuma** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Derin sinir ağ teknolojisini kullanarak metni doğal-sounding konuşmaya dönüştürür. Bu, doğal olarak birleştirilmiş konuşmaya olanak sağlar. | Genel kullanıma sunuldu |
| [Konuşma Hizmeti API’si][sp-containers-lid] | **Konuşma dili algılama** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Konuşulan ses dilini belirler. | Geçişli Önizleme |

### <a name="vision-containers"></a>Görüntü kapsayıcıları

> [!WARNING]
> 11 Haziran 2020’de Microsoft, insan haklarına dayalı etkili düzenlemeler yapılmadığı sürece ABD’deki polis departmanlarına yüz tanıma teknolojisi satmayacağını duyurmuştur. Bu nedenle, müşteriler yüz tanıma özelliklerini ya da yüz veya Video Indexer gibi Azure hizmetlerinde yer alan işlevselliği veya bir müşteri varsa ya da için Birleşik Devletler bir polis departmanı olan bu hizmetlerin kullanılmasına izin vermeyi kullanamaz.

| Hizmet |  Kapsayıcı | Description | Kullanılabilirlik |
|--|--|--|--|
| [Görüntü İşleme][cv-containers] | **OCR** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) oku | Okuma OCR kapsayıcısı, JPEG, PNG, BMP, PDF ve TIFF dosya biçimleri desteğiyle, görüntülerden ve belgelerden yazdırılmış ve el yazısı metinleri ayıklamanızı sağlar. Daha fazla bilgi için bkz. [okuma API 'si belgeleri](./computer-vision/overview-ocr.md). | Geçişli önizleme. [Erişim isteyin][request-access]. |
| [Uzamsal Analiz][spa-containers] | **Uzamsal analiz** ([görüntü](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Kişiler, taşınanlar ve fiziksel ortamlardaki nesnelerle etkileşimler arasındaki uzamsal ilişkileri anlamak için gerçek zamanlı akış videosunu analiz eder. | Geçişli önizleme. [Erişim isteyin][request-access]. |
| [Yüz Tanıma][fa-containers] | **Yüz Tanıma** | Görüntülerde insan yüzlerini algılar ve yüzleri (Nosi ve gözler gibi), cinsiyet, yaş ve diğer makine tarafından öngörülen yüz özellikleri dahil olmak üzere öznitelikleri tanımlar. Algılamanın yanı sıra, benzer bir veya aynı yüzün zaten mevcut olup olmadığını görmek için aynı görüntüde veya farklı görüntülerde iki yüzün aynı olup olmadığını denetleyebilir veya bir veritabanı ile yüzleri karşılaştırın. Ayrıca, paylaşılan görsel nitelikleri kullanarak benzer yüzeyleri gruplar halinde düzenleyebilir. | Kullanılamaz |
| [Form tanıyıcı][fr-containers] | **Form Tanıma** | Form, formlardan anahtar-değer çiftlerini ve tabloları tanımlamak ve ayıklamak için makine öğrenimi teknolojisini uygular. | Kullanılamaz | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ayrıca, bazı kapsayıcılar bilişsel Hizmetler [çoklu hizmet kaynak](cognitive-services-apis-create-account.md) sunumunda desteklenir. Tek bir bilişsel hizmetler oluşturup bir adet tek kaynak oluşturabilir ve aşağıdaki hizmetler için desteklenen hizmetler genelinde aynı faturalandırma anahtarını kullanabilirsiniz:

* Görüntü İşleme
* Yüz Tanıma
* LUIS
* Metin Analizi

## <a name="prerequisites"></a>Önkoşullar

Azure bilişsel hizmetler kapsayıcılarını kullanmadan önce aşağıdaki önkoşulları karşılamanız gerekir:

**Docker altyapısı**: Docker altyapısının yerel olarak yüklü olması gerekir. Docker, [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)ve [Windows](https://docs.docker.com/docker-for-windows/)üzerinde Docker ortamını yapılandıran paketler sağlar. Windows 'da, Docker 'ın Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir. Docker Kapsayıcıları ayrıca doğrudan [Azure Kubernetes hizmetine](../aks/index.yml) veya [Azure Container Instances](../container-instances/index.yml)dağıtılabilir.

Kapsayıcıların Azure 'a bağlanıp faturalandırma verilerini göndermesini sağlamak için Docker yapılandırılmalıdır.

**Microsoft Container Registry ve Docker Ile benzerlik**: kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Microsoft Container Registry ve Docker kavramlarının yanı sıra temel komutlar hakkında bilgi sahibi olmanız gerekir `docker` .

Docker ve kapsayıcı temel bilgileri ile ilgili giriş yapmak için [Docker’a genel bakış](https://docs.docker.com/engine/docker-overview/) bölümüne bakın.

Tek tek kapsayıcılar, sunucu ve bellek ayırma gereksinimleri dahil olmak üzere kendi gereksinimlerine sahip olabilir.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Geliştirici örnekleri

Geliştirici örnekleri, [GitHub deponuzda](https://github.com/Azure-Samples/cognitive-services-containers-samples)bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bilişsel hizmetler ile kullanabileceğiniz [kapsayıcı Tarifler](containers/container-reuse-recipe.md) hakkında bilgi edinin.

Azure bilişsel hizmetler 'de kapsayıcılar tarafından sunulan işlevleri yükleyip araştırın:

* [Anomali algılayıcı kapsayıcıları][ad-containers]
* [Görüntü İşleme kapsayıcılar][cv-containers]
* [Yüz kapsayıcıları][fa-containers]
* [Form tanıyıcı kapsayıcıları][fr-containers]
* [Language Understanding (LUSıS) kapsayıcıları][lu-containers]
* [Konuşma hizmeti API kapsayıcıları][sp-containers]
* [Metin Analizi kapsayıcılar][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://aka.ms/csgate
