---
title: Kapsayıcı desteği
titleSuffix: Azure Cognitive Services
description: Docker kapsayıcıları yakın Bilişsel hizmetler verilerinize nasıl edinebildiğini öğrenin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: a35ceed4cefa47b903ceec915388b4831cd9e69b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173771"
---
# <a name="container-support-in-azure-cognitive-services"></a>Azure Bilişsel hizmetler kapsayıcı desteği

Azure Bilişsel hizmetler kapsayıcı desteği sayesinde geliştiriciler, Azure'da kullanılabilen zengin API'leri kullanmak için ve nerede dağıtmak ve gelir hizmetlerini barındırmak esneklik sağlar [Docker kapsayıcıları](https://www.docker.com/what-container). Kapsayıcı desteği şu anda Azure bilişsel hizmetler 'in şu parçalar dahil bir alt kümesi için kullanılabilir:

> [!div class="checklist"]
> * [Anomali algılayıcısı][ad-containers]
> * [Görüntü İşleme][cv-containers]
> * [Yüz tanıma][fa-containers]
> * [Form tanıyıcı][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Konuşma Hizmeti API’si][sp-containers]
> * [Metin Analizi][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kapsayıcı içinde bir uygulama veya onun bağımlılıklarını & yapılandırması gibi hizmet birlikte bir kapsayıcı görüntüsüne paketlenmiştir yazılım dağıtımı için bir yaklaşımdır. Çok az kayıpla veya hiç değişiklik yapmadan kapsayıcı görüntüsü, bir kapsayıcı konağı üzerinde dağıtılabilir. Birbirine ve bir sanal makine değerinden daha küçük bir kaplama alanı ile temel işletim sistemi, yalıtılmış kapsayıcılardır. Kapsayıcılar için kısa vadeli görevleri kapsayıcı görüntülerinden oluşturulan ve artık gerekli olmadığında kaldırıldı.

Bilişsel hizmetler kaynakları [Microsoft Azure](https://azure.microsoft.com)kullanılabilir. Oturum [Azure portalında](https://portal.azure.com/) oluşturma ve bu hizmetler için Azure kaynaklarını keşfedin.

## <a name="features-and-benefits"></a>Özellikler ve avantajlar

- **Sabit altyapı**: değişikliklere uyum sağlayabilirken, düzenli ve güvenilir bir bilinen sistem parametreleri kümesinden yararlanmak Için DevOps ekiplerini etkinleştirin. Kapsayıcılar, öngörülebilir bir ekosistem içinde Pivot esnekliği sağlar ve yapılandırma drmasını önler.
- **Veriler üzerinde denetim**: Burada bu Bilişsel hizmetler, veri işlem seçme özgürlüğü tanıyın. Bu, verileri buluta Gönder olamaz, ancak Bilişsel hizmetler teknoloji erişmesi gereken müşteriler için gereklidir. Karma ortamlarda, veriler, yönetim, kimlik ve güvenlik arasında tutarlılık desteği.
- **Model güncelleştirmelerini denetime**: müşteriler sürüm oluşturma ve kendi çözümlerinde dağıtılan modelleri güncelleştirme konusunda esneklik sağlar.
- **Taşınabilir mimarisi**: Azure'da, şirket içi ve uç dağıtılabilir bir taşınabilir uygulama mimarisi oluşturulmasını etkinleştir. Kapsayıcıları doğrudan dağıtılabilir [Azure Kubernetes hizmeti](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), veya bir [Kubernetes](https://kubernetes.io/) kümesi dağıtıldı için [Azure Yığın](/azure-stack/operator). Daha fazla bilgi için [Azure Stack dağıtma Kubernetes](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Yüksek performans / düşük gecikme süresi**: müşteriler kendi uygulama mantığı ve verileri yakın fiziksel olarak çalıştırmak, Bilişsel hizmetler sağlayarak yüksek aktarım hızına ve düşük gecikme süresi gereksinimleri için ölçekleme olanağı sunar. Kapsayıcılar, saniye başına işlem (TPS) cap değil ve gerekli donanım kaynakları sağlarsanız, isteğe bağlı işlemek için yukarı ve dışarı ölçeklendirme yapılabilir.
- **Ölçeklenebilirlik**: sürekli büyüyen ve Kubernetes gibi kapsayıcı düzenleme yazılımıyla birlikte artan popülerlik. ölçeklenebilirlik, Forefront of teknolojik gelişmelerden oluşur. Ölçeklenebilir bir küme kurucusu üzerinde derleme, uygulama geliştirme ve yüksek kullanılabilirlik.

## <a name="containers-in-azure-cognitive-services"></a>Azure Bilişsel hizmetler, kapsayıcılar

Azure Bilişsel hizmetler kapsayıcılar, Docker kapsayıcıları, aşağıdaki dizi her biri, Azure Bilişsel hizmetler hizmetlerden işlevlerinin bir alt kümesini içeren sağlar:

| Hizmet | Desteklenen Fiyatlandırma Katmanı | Kapsayıcı | Açıklama |
|---------|----------|----------|-------------|
|[Anomali Algılayıcısı ][ad-containers] |F0, S0|**Anomali-algılayıcı** |Anomali algılayıcı API 'SI, makine öğrenimi ile zaman serisi verilerinizde bulunan normalleştirikleri izlemenize ve algılamanıza olanak sağlar.<br>[Erişim isteği](https://aka.ms/adcontainer)|
|[Görüntü İşleme][cv-containers] |F0, S1|**Okuma** |Farklı yüzey ve arka planlar, giriş ve posterler kartvizitler gibi çeşitli nesne görüntülerdeki yazdırılan metin ayıklar. Okuma kapsayıcısı resimlerde *el yazısı metinleri* de ALGıLAR ve PDF/TIFF/çok sayfalı destek sağlar.<br/><br/>**Önemli:** Okuma kapsayıcısı Şu anda yalnızca Ingilizce ile çalışmaktadır.|
|[Yüz tanıma][fa-containers] |F0, S0|**Yüz tanıma** |Görüntülerdeki İnsan yüzlerini algılar ve yüz yer işareti (örneğin, noses ve gözler), cinsiyet, geçerlilik süresi ve diğer makine tahmin yüz özellikleri dahil olmak üzere, öznitelikleri tanımlar. Yüz algılama ek olarak, iki yüzün aynı görüntü ya da farklı görüntüleri bir güven puanı kullanarak aynı olduğundan veya bir benzeyen olmadığını görmek için bir veritabanında yüzleri karşılaştırın veya aynı yüz zaten kontrol edebilirsiniz. Bu gibi durumlarda, benzer yüzlerden de paylaşılan visual nitelikler kullanarak gruplar halinde düzenleyebilirsiniz.<br>[Erişim isteği](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Form tanıyıcı][fr-containers] |F0, S0|**Form tanıyıcı** |Form, formlardan anahtar-değer çiftlerini ve tabloları tanımlamak ve ayıklamak için makine öğrenimi teknolojisini uygular.<br>[Erişim isteği](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Bir eğitilen veya yayımlanmış dil anlama modeli olarak da bilinen bir LUIS uygulaması bir docker kapsayıcısına yükler ve kapsayıcının API uç noktalardan gelen sorgu tahminler elde etmek için erişim sağlar. Kapsayıcıdan sorgu günlüklerini toplamak ve bu geri yükleme [LUIS portalı](https://www.luis.ai) uygulamanın tahmin doğruluğunu artırmak için.|
|[Konuşma Hizmeti API’si][sp-containers-stt] |F0, S0|**Konuşmayı metne dönüştürme** |Sürekli, gerçek zamanlı konuşmaları metne dönüştürür.|
|[Konuşma Hizmeti API’si][sp-containers-cstt] |F0, S0|**Özel Konuşma Tanıma metin** |Özel bir model kullanarak sürekli gerçek zamanlı konuşmayı metne dönüştürme.|
|[Konuşma Hizmeti API’si][sp-containers-tts] |F0, S0|**Metin okuma** |Metni, doğal sesli konuşmaya dönüştürür.|
|[Konuşma Hizmeti API’si][sp-containers-ctts] |F0, S0|**Özel metin okuma** |Özel bir model kullanarak metni doğal-sounkonuşmaya dönüştürür.|
|[Metin Analizi][ta-containers-keyphrase] |F0, S|**Anahtar ifade ayıklama** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Ana noktaları belirleyin, anahtar ifadeleri ayıklar. Örneğin, "The food was delicious and there were wonderful staff" (Yemekler lezzetliydi ve personel harikaydı) giriş metni olduğunda API, "food" (yemek) ve "wonderful staff" (personel harikaydı) ana konuşma noktalarını döndürür. |
|[Metin Analizi][ta-containers-language]|F0, S|**Dil algılama** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |En fazla 120 dil için hangi dil giriş metni yazılır ve rapor istekte gönderilen her belge için bir tek dil kodu algılar. Dil kodu, puanın ağırlığını belirten bir puanla eşleştirilir. |
|[Metin Analizi][ta-containers-sentiment]|F0, S|**Yaklaşım analizi** ([görüntü](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Ham metin pozitif veya negatif yaklaşım hakkında ipuçları için analiz eder. API, her belge için 0 ile 1 arasında bir yaklaşım puanı döndürür ve 1 en pozitif değerdir. Analiz modelleri metin ve doğal dil Microsoft teknolojilerinin kapsamlı bir gövdesi kullanarak önceden eğitilir. API, [seçili dillerde](./text-analytics/language-support.md) sağladığınız ham metni analiz edip puanlayabilir ve sonuçları doğrudan çağrıyı yapan uygulamaya döndürebilir. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ayrıca, bazı kapsayıcılar bilişsel Hizmetler [**hepsi bir arada sunum**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) kaynak anahtarları içinde desteklenir. Tek bir bilişsel hizmetler oluşturup bir adet tek kaynak oluşturabilir ve aşağıdaki hizmetler için desteklenen hizmetler genelinde aynı faturalandırma anahtarını kullanabilirsiniz:

* Görüntü İşleme
* Yüz
* LUIS
* Metin Analizi

## <a name="container-availability-in-azure-cognitive-services"></a>Azure Bilişsel hizmetler kapsayıcı kullanılabilirlik

Azure aboneliğiniz üzerinden genel kullanıma açık Azure Bilişsel hizmetler kapsayıcıları ve Docker kapsayıcı görüntülerini Microsoft kapsayıcı kayıt defteri veya Docker Hub çekilebilir. Kullanabileceğiniz [docker isteği](https://docs.docker.com/engine/reference/commandline/pull/) uygun kayıt defterinden bir kapsayıcı görüntüsü indirilemedi komutu.

> [!IMPORTANT]
> Şu anda, aşağıdaki kapsayıcılara erişmek için bir kaydolma işlemini doldurmanız gerekir. burada, sizin, şirketiniz ve kapsayıcıları uygulamak istediğiniz kullanım durumu ile ilgili soruları içeren bir soru formunu doldurun ve gönderebilirsiniz. Erişim izni verildiğinde ve kimlik bilgilerini sağladıktan sonra, Azure Container Registry tarafından barındırılan özel bir kapsayıcı kayıt defterinden kapsayıcı görüntülerini çekebilirsiniz.
> * [Anomali Algılayıcısı ](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Yüz tanıma](Face/face-how-to-install-containers.md)
> * [Form tanıyıcı](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Okuma](computer-vision/computer-vision-how-to-install-containers.md)
> * [Konuşmadan metne ve metinden konuşmaya](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure Bilişsel hizmetler kapsayıcıları kullanmadan önce aşağıdaki önkoşulları karşılamanız gerekir:

**Docker altyapısı**: Docker altyapısının yerel olarak yüklü olması gerekir. Docker üzerinde Docker ortamını yapılandıran paketler sağlar [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), ve [Windows](https://docs.docker.com/docker-for-windows/). Windows Docker Linux kapsayıcıları destekleyecek şekilde yapılandırılması gerekir. Docker kapsayıcıları da dağıtılabilir doğrudan [Azure Kubernetes hizmeti](../aks/index.yml) veya [Azure Container Instances](../container-instances/index.yml).

Docker, kapsayıcılar ile bağlanma ve faturalama verileri Azure'a göndermek izin verecek şekilde yapılandırılmalıdır.

**Microsoft Container Registry ve Docker konusunda**: bir temel kavramlarını hem Microsoft Container Registry ve Docker kayıt defterleri, depoları, kapsayıcılar ve kapsayıcı görüntülerinin yanı sıra bilgisi gibi olmalıdır temel `docker` komutları.

Docker ve kapsayıcı temelleri hakkında bilgi için bkz: [Docker'a genel bakış](https://docs.docker.com/engine/docker-overview/).

Kapsayıcılara sunucu ve bellek ayırma gereksinimleri dahil olmak üzere kendi gereksinimleriyle de sahip olabilir.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bilişsel hizmetler ile kullanabileceğiniz [kapsayıcı Tarifler](containers/container-reuse-recipe.md) hakkında bilgi edinin.

Yükleme ve Azure Bilişsel hizmetler, kapsayıcılar tarafından sağlanan işlevselliği keşfedin:

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
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment