---
title: Metin Analizi API'si için Docker Kapsayıcıları yükleyip çalıştırın
titleSuffix: Azure Cognitive Services
description: Şirket içinde yaklaşım analizi gibi doğal dil işlemlerini gerçekleştirmek için Metin Analizi API'si için Docker kapsayıcılarını kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
keywords: Şirket içi, Docker, kapsayıcı, yaklaşım analizi, doğal dil işleme
ms.openlocfilehash: 012e725e31097af5af634a1aba7693048c4c6b3e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277479"
---
# <a name="install-and-run-text-analytics-containers"></a>Metin Analizi kapsayıcılarını yükleme ve çalıştırma

> [!NOTE]
> * Yaklaşım Analizi ve dil algılama için kapsayıcı genel kullanıma sunulmuştur. Anahtar tümceciği ayıklama kapsayıcısı, geçişli olmayan genel önizleme olarak kullanılabilir.
> * Varlık bağlama ve NER Şu anda bir kapsayıcı olarak kullanılamıyor.
> * Sistem durumu kapsayıcısı için Metin Analizi erişilmesi bir [istek formu](https://aka.ms/csgate)gerektirir. Şu anda kullanım için faturalandırılmaz.
> * Kapsayıcı görüntüsü konumları yakın zamanda değişmiş olabilir. Bu kapsayıcının güncelleştirilmiş konumunu görmek için bu makaleyi okuyun.

Kapsayıcılar Metin Analizi API'lerini kendi ortamınızda çalıştırabilmenize olanak tanır. Bunlar belirli güvenlik ve veri idaresi gereksinimleriniz için çok kullanışlıdır. Metin Analizi kapsayıcıları ham metin üzerinde gelişmiş doğal dil işleme sağlar ve üç ana işlev içerir: yaklaşım analizi, anahtar ifade ayıklama ve dil algılama. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

> [!IMPORTANT]
> Ücretsiz hesap, ayda 5.000 işlem ile sınırlıdır ve kapsayıcılar için yalnızca **ücretsiz** ve **Standart** <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">fiyatlandırma katmanları</a> geçerlidir. İşlem isteği ücretleri hakkında daha fazla bilgi için bkz. [veri limitleri](../concepts/data-limits.md).

## <a name="prerequisites"></a>Önkoşullar

Metin Analizi kapsayıcılarından herhangi birini çalıştırmak için konak bilgisayar ve kapsayıcı ortamları olmalıdır.

## <a name="preparation"></a>Hazırlık

Metin Analizi kapsayıcıları kullanmadan önce aşağıdaki önkoşulları karşılamanız gerekir:

|Gerekli|Amaç|
|--|--|
|Docker altyapısı| Bir [ana bilgisayarda](#the-host-computer)Docker altyapısının yüklü olması gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) üzerinde Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temel bilgileri ile ilgili giriş yapmak için [Docker’a genel bakış](https://docs.docker.com/engine/docker-overview/) bölümüne bakın.<br><br> Kapsayıcıların Azure 'a bağlanıp faturalandırma verilerini göndermesini sağlamak için Docker yapılandırılmalıdır. <br><br> **Windows 'da** Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br>|
|Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarının yanı sıra temel komutlar hakkında bilgi sahibi olmanız gerekir `docker` .| 
|Metin Analizi kaynağı |Kapsayıcısını kullanabilmeniz için şunları yapmanız gerekir:<br><br>Ücretsiz (F0) veya standart (ler) [fiyatlandırma katmanına](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)sahip bir Azure [metin analizi kaynağı](../../cognitive-services-apis-create-account.md) . Azure portal kaynağın **anahtar ve uç** nokta sayfasına gıderek ilişkili API anahtarını ve uç nokta URI 'sini almanız gerekecektir. <br><br>**{API_KEY}**: kullanılabilir iki kaynak anahtardan biri. <br><br>**{ENDPOINT_URI}**: kaynağınız için uç nokta. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

Sistem durumu kapsayıcısı için Metin Analizi kullanıyorsanız [sorumlu AI](https://docs.microsoft.com/legal/cognitive-services/text-analytics/transparency-note-health)  (rampa) bildirimi de değeri ile birlikte bulunmalıdır `accept` .

## <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

Aşağıdaki tabloda Metin Analizi kapsayıcıları için en düşük ve önerilen özellikler açıklanmaktadır. En az 2 gigabayt (GB) bellek gereklidir ve her CPU çekirdeği en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır. Bölüm başına izin verilen Işlem (TPS) de listelenir.

|  | En düşük ana bilgisayar özellikleri | Önerilen konak özellikleri | En az TPS | Maksimum TPS|
|---|---------|-------------|--|--|
| **Dil algılama, anahtar tümceciği ayıklama**   | 1 çekirdek, 2GB bellek | 1 çekirdek, 4GB bellek |15 | 30|
| **Yaklaşım Analizi**   | 1 çekirdek, 2GB bellek | 4 çekirdek, 8GB bellek |15 | 30|
| **Sağlık-1 belgesi/isteği için Metin Analizi**   |  4 çekirdek, 10 GB bellek | 6 çekirdek, 12GB bellek |15 | 30|
| **Sistem durumu için Metin Analizi-10 belge/istek**   |  6 çekirdek, 16GB bellek | 8 çekirdek, 20GB bellek |15 | 30|

CPU çekirdeği ve belleği, `--cpus` `--memory` komutunun bir parçası olarak kullanılan ve ayarlarına karşılık gelir `docker run` .

## <a name="get-the-container-image-with-docker-pull"></a>Kapsayıcı görüntüsünü al `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Metin Analizi için kapsayıcı görüntüleri Microsoft Container Registry kullanılabilir.

# <a name="sentiment-analysis"></a>[Yaklaşım Analizi ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Anahtar İfade Ayıklama (Önizleme)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Dil Algılama](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Sistem durumu için Metin Analizi (Önizleme)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayardan](#the-host-computer)olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

1. [Kapsayıcıyı](#run-the-container-with-docker-run)gerekli faturalandırma ayarlarıyla çalıştırın.
1. [Kapsayıcının tahmin uç noktasını sorgulayın](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kapsayıcıyı ile çalıştırma `docker run`

Kapsayıcıları çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. Kapsayıcı siz durduruncaya kadar çalışmaya devam edecektir.

> [!IMPORTANT]
> * Aşağıdaki bölümlerdeki Docker komutları, `\` satır devamlılık karakteri olarak ters eğik çizgi kullanır. Bunu, ana bilgisayar işletim sisteminizin gereksinimlerine göre değiştirin veya kaldırın. 
> * `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz.  Daha fazla bilgi için bkz. [faturalandırma](#billing).
> * Yaklaşım Analizi ve dil algılama kapsayıcıları genel kullanıma sunulmuştur. Anahtar tümceciği ayıklama kapsayıcısı API 'nin v2 'sini kullanır ve önizlemededir.

# <a name="sentiment-analysis"></a>[Yaklaşım Analizi](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Anahtar İfade Ayıklama (Önizleme)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Dil Algılama](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Sistem durumu için Metin Analizi (Önizleme)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

Kapsayıcı REST tabanlı sorgu tahmin uç noktası API’lerini sağlar.

Kapsayıcı API’leri için `http://localhost:5000` konağını kullanın.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Kapsayıcıyı durdurma

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı bir çıkış [bağlaması](../text-analytics-resource-container-config.md#mount-settings) ve günlüğü etkin olarak çalıştırırsanız kapsayıcı, kapsayıcıyı başlatırken veya çalıştırırken oluşan sorunları gidermek için yararlı olan günlük dosyaları oluşturur.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

Metin Analizi kapsayıcıları Azure hesabınızdaki bir _metin analizi_ kaynak kullanarak faturalandırma bilgilerini Azure 'a gönderir. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Özet

Bu makalede, Metin Analizi kapsayıcıları indirmek, yüklemek ve çalıştırmak için kavramları ve iş akışını öğrendiniz. Özet:

* Metin Analizi, çeşitli özellikleri kapsüllemek üzere Docker için üç Linux kapsayıcısı sağlar:
   * *Yaklaşım Analizi*
   * *Anahtar İfade Ayıklama (Önizleme)* 
   * *Dil Algılama*
   * *Sistem durumu için Metin Analizi (Önizleme)*
* Kapsayıcı görüntüleri Microsoft Container Registry (MCR) veya önizleme kapsayıcı deposundan indirilir.
* Kapsayıcı görüntüleri Docker 'da çalışır.
* Kapsayıcının ana bilgisayar URI 'sini belirterek Metin Analizi kapsayıcılarındaki işlemleri çağırmak için REST API veya SDK kullanabilirsiniz.
* Bir kapsayıcıyı örnekledikten sonra faturalandırma bilgilerini belirtmeniz gerekir.

> [!IMPORTANT]
> Bilişsel hizmetler kapsayıcıları, ölçüm için Azure 'a bağlı kalmadan çalıştırılmak üzere lisanslanmaz. Müşterilerin, ödeme bilgilerini her zaman ölçüm hizmetiyle iletişimine olanak tanımak için kapsayıcıların etkinleştirilmesi gerekir. Bilişsel hizmetler kapsayıcıları, müşteri verilerini (örn. çözümlenmekte olan metin) Microsoft 'a göndermez.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için [kapsayıcıları](../text-analytics-resource-container-config.md) yapılandırmayı gözden geçir
* İşlevlerle ilgili sorunları çözmek için [sık sorulan sorular (SSS)](../text-analytics-resource-faq.md) bölümüne bakın.
