---
title: Bilişsel hizmetler kapsayıcı görüntüsü etiketleri
titleSuffix: Azure Cognitive Services
description: Tüm bilişsel hizmet kapsayıcısı resim etiketlerinin kapsamlı bir listesi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/18/2019
ms.author: dapine
ms.openlocfilehash: 0d8c7a36582c30975f3a408a2ea6e95d39e560ef
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173747"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure bilişsel hizmetler kapsayıcı görüntüsü etiketleri

Azure bilişsel hizmetler birçok kapsayıcı görüntüsü sunar. Kapsayıcı kayıt defterleri ve ilgili depolar kapsayıcı görüntüleri arasında farklılık gösterir. Her kapsayıcı görüntüsü adı birden çok etiket sunar. Kapsayıcı görüntüsü etiketi, kapsayıcı görüntüsünü sürüm oluşturma mekanizmasından oluşur. Bu makale, tüm bilişsel hizmetler kapsayıcı görüntülerini ve bunların kullanılabilir etiketlerini listelemek için kapsamlı bir başvuru olarak kullanılmak üzere tasarlanmıştır.

> [!TIP]
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)kullanırken, **büyük/küçük harfe duyarlı**olduğu için kapsayıcı kayıt defteri, depo, kapsayıcı görüntüsü adı ve karşılık gelen etiket büyük küçük harfe yakın bir ilgi ödeyin.

## <a name="anomaly-detector"></a>Anomali Algılayıcısı

[Anomali algılayıcı][ad-containers] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-anomaly-detector`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008500001-amd64-preview` | |

## <a name="computer-vision"></a>Görüntü İşleme

[Görüntü işleme][cv-containers] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-read`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.009920003-amd64-preview` | |
| `1.1.009910003-amd64-preview` | |

## <a name="face"></a>Yüz

[Yüz][fa-containers] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-face`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |
| `1.1.006490002-amd64-preview` | |
| `1.0.005940002-amd64-preview` | |
| `1.0.005550001-amd64-preview` | |

## <a name="form-recognizer"></a>Form Tanıma

[Form tanıyıcı][fr-containers] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-form-recognizer`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008640001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |

## <a name="language-understanding-luis"></a>Dil Anlama (LUIS)

[Lusıs][lu-containers] kapsayıcı görüntüsü `mcr.microsoft.com` kapsayıcısı kayıt defteri genel konumunda bulunabilir. `azure-cognitive-services` deposu içinde bulunur ve `luis`olarak adlandırılır. Tam kapsayıcı görüntü adı, `mcr.microsoft.com/azure-cognitive-services/luis`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.010330004-amd64-preview` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.008010002-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.007020001-amd64-preview` | |

## <a name="custom-speech-to-text"></a>Özel Konuşma Tanıma metin

[Özel konuşma tanıma metin][sp-cstt] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-custom-speech-to-text`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `2.0.0-amd64-preview` | |

## <a name="custom-text-to-speech"></a>Özel metin okuma

[Özel metin okuma][sp-ctts] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-custom-text-to-speech`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.3.0-amd64-preview` | |

## <a name="speech-to-text"></a>Konuşmayı metne dönüştürme

[Konuşmadan metne][sp-stt] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-speech-to-text`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-ar-eg-preview` | `ar-EG` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-ca-es-preview` | `ca-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-da-dk-preview` | `da-DK` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-de-de-preview` | `de-DE` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-en-au-preview` | `en-AU` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-en-ca-preview` | `en-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-en-gb-preview` | `en-GB` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-en-in-preview` | `en-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-en-nz-preview` | `en-NZ` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-en-us-preview` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-es-es-preview` | `es-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-es-mx-preview` | `es-MX` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-fi-fi-preview` | `fi-FI` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-fr-ca-preview` | `fr-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-fr-fr-preview` | `fr-FR` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-hi-in-preview` | `hi-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-it-it-preview` | `it-IT` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-ja-jp-preview` | `ja-JP` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-ko-kr-preview` | `ko-KR` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-nb-no-preview` | `nb-NO` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-nl-nl-preview` | `nl-NL` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-pl-pl-preview` | `pl-PL` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-pt-br-preview` | `pt-BR` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-pt-pt-preview` | `pt-PT` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-ru-ru-preview` | `ru-RU` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-sv-se-preview` | `sv-SE` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-th-th-preview` | `th-TH` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-tr-tr-preview` | `tr-TR` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-zh-cn-preview` | `zh-CN` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-zh-hk-preview` | `zh-HK` yerel ayarıyla kapsayıcı görüntüsü. |
| `2.0.0-amd64-zh-tw-preview` | `zh-TW` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-de-de-preview` | `de-DE` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-au-preview` | `en-AU` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-ca-preview` | `en-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-gb-preview` | `en-GB` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-in-preview` | `en-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-us-preview` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-es-es-preview` | `es-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-es-mx-preview` | `es-MX` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-fr-ca-preview` | `fr-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-fr-fr-preview` | `fr-FR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-it-it-preview` | `it-IT` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-ja-jp-preview` | `ja-JP` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-pt-br-preview` | `pt-BR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.2.0-amd64-zh-cn-preview` | `zh-CN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-de-de-preview` | `de-DE` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-en-au-preview` | `en-AU` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-en-ca-preview` | `en-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-en-gb-preview` | `en-GB` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-en-in-preview` | `en-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-en-us-preview` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-es-es-preview` | `es-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-es-mx-preview` | `es-MX` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-fr-ca-preview` | `fr-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-fr-fr-preview` | `fr-FR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-it-it-preview` | `it-IT` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-ja-jp-preview` | `ja-JP` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-pt-br-preview` | `pt-BR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.3-amd64-zh-cn-preview` | `zh-CN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-de-de-preview` | `de-DE` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-en-au-preview` | `en-AU` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-en-ca-preview` | `en-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-en-gb-preview` | `en-GB` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-en-in-preview` | `en-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-en-us-preview` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-es-es-preview` | `es-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-es-mx-preview` | `es-MX` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-fr-ca-preview` | `fr-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-fr-fr-preview` | `fr-FR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-it-it-preview` | `it-IT` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-ja-jp-preview` | `ja-JP` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-pt-br-preview` | `pt-BR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.2-amd64-zh-cn-preview` | `zh-CN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-de-de-preview` | `de-DE` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-en-au-preview` | `en-AU` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-en-ca-preview` | `en-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-en-gb-preview` | `en-GB` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-en-in-preview` | `en-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-en-us-preview` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-es-es-preview` | `es-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-es-mx-preview` | `es-MX` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-fr-ca-preview` | `fr-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-fr-fr-preview` | `fr-FR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-it-it-preview` | `it-IT` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-ja-jp-preview` | `ja-JP` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-pt-br-preview` | `pt-BR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.1-amd64-zh-cn-preview` | `zh-CN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-de-de-preview` | `de-DE` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-au-preview` | `en-AU` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-ca-preview` | `en-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-gb-preview` | `en-GB` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-in-preview` | `en-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-us-preview` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-es-es-preview` | `es-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-es-mx-preview` | `es-MX` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-fr-ca-preview` | `fr-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-fr-fr-preview` | `fr-FR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-it-it-preview` | `it-IT` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-ja-jp-preview` | `ja-JP` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-pt-br-preview` | `pt-BR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.1.0-amd64-zh-cn-preview` | `zh-CN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-de-de-preview` | `de-DE` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-au-preview` | `en-AU` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-ca-preview` | `en-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-gb-preview` | `en-GB` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-in-preview` | `en-IN` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-us-preview` | `en-US` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-es-es-preview` | `es-ES` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-es-mx-preview` | `es-MX` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-fr-ca-preview` | `fr-CA` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-fr-fr-preview` | `fr-FR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-it-it-preview` | `it-IT` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-ja-jp-preview` | `ja-JP` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-pt-br-preview` | `pt-BR` yerel ayarıyla kapsayıcı görüntüsü. |
| `1.0.0-amd64-zh-cn-preview` | `zh-CN` yerel ayarıyla kapsayıcı görüntüsü. |

## <a name="text-to-speech"></a>Metin okuma

[Metin okuma][sp-tts] kapsayıcı görüntüsü `containerpreview.azurecr.io` kapsayıcı kayıt defterinde bulunabilir. `microsoft` deposu içinde bulunur ve `cognitive-services-text-to-speech`olarak adlandırılır. Tam kapsayıcı görüntü adı, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | `en-US` yerel ayarı ve `en-US-JessaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ar-eg-hoda-preview` | `ar-EG` yerel ayarı ve `ar-EG-Hoda` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ar-sa-naayf-preview` | `ar-SA` yerel ayarı ve `ar-SA-Naayf` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-bg-bg-ivan-preview` | `bg-BG` yerel ayarı ve `bg-BG-Ivan` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ca-es-herenarus-preview` | `ca-ES` yerel ayarı ve `ca-ES-HerenaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-cs-cz-jakub-preview` | `cs-CZ` yerel ayarı ve `cs-CZ-Jakub` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-da-dk-hellerus-preview` | `da-DK` yerel ayarı ve `da-DK-HelleRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-de-at-michael-preview` | `de-AT` yerel ayarı ve `de-AT-Michael` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-de-ch-karsten-preview` | `de-CH` yerel ayarı ve `de-CH-Karsten` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-de-de-hedda-preview` | `de-DE` yerel ayarı ve `de-DE-Hedda` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-de-de-heddarus-preview` | `de-DE` yerel ayarı ve `de-DE-Hedda` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-de-de-heddarus-preview` | `de-DE` yerel ayarı ve `de-DE-HeddaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | `de-DE` yerel ayarı ve `de-DE-Stefan-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-el-gr-stefanos-preview` | `el-GR` yerel ayarı ve `el-GR-Stefanos` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-au-catherine-preview` | `en-AU` yerel ayarı ve `en-AU-Catherine` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-au-hayleyrus-preview` | `en-AU` yerel ayarı ve `en-AU-HayleyRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-ca-heatherrus-preview` | `en-CA` yerel ayarı ve `en-CA-HeatherRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-ca-linda-preview` | `en-CA` yerel ayarı ve `en-CA-Linda` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-gb-george-apollo-preview` | `en-GB` yerel ayarı ve `en-GB-George-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-gb-hazelrus-preview` | `en-GB` yerel ayarı ve `en-GB-HazelRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | `en-GB` yerel ayarı ve `en-GB-Susan-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-ie-sean-preview` | `en-IE` yerel ayarı ve `en-IE-Sean` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-in-heera-apollo-preview` | `en-IN` yerel ayarı ve `en-IN-Heera-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-in-priyarus-preview` | `en-IN` yerel ayarı ve `en-IN-PriyaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | `en-IN` yerel ayarı ve `en-IN-Ravi-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-us-benjaminrus-preview` | `en-US` yerel ayarı ve `en-US-BenjaminRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-us-guy24krus-preview` | `en-US` yerel ayarı ve `en-US-Guy24kRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-us-jessa24krus-preview` | `en-US` yerel ayarı ve `en-US-Jessa24kRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-us-jessarus-preview` | `en-US` yerel ayarı ve `en-US-JessaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-en-us-zirarus-preview` | `en-US` yerel ayarı ve `en-US-ZiraRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-es-es-helenarus-preview` | `es-ES` yerel ayarı ve `es-ES-HelenaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-es-es-laura-apollo-preview` | `es-ES` yerel ayarı ve `es-ES-Laura-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | `es-ES` yerel ayarı ve `es-ES-Pablo-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-es-mx-hildarus-preview` | `es-MX` yerel ayarı ve `es-MX-HildaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | `es-MX` yerel ayarı ve `es-MX-Raul-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-fi-fi-heidirus-preview` | `fi-FI` yerel ayarı ve `fi-FI-HeidiRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-fr-ca-caroline-preview` | `fr-CA` yerel ayarı ve `fr-CA-Caroline` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | `fr-CA` yerel ayarı ve `fr-CA-HarmonieRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-fr-ch-guillaume-preview` | `fr-CH` yerel ayarı ve `fr-CH-Guillaume` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | `fr-FR` yerel ayarı ve `fr-FR-HortenseRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | `fr-FR` yerel ayarı ve `fr-FR-Julie-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | `fr-FR` yerel ayarı ve `fr-FR-Paul-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-he-il-asaf-preview` | `he-IL` yerel ayarı ve `he-IL-Asaf` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-hi-in-hemant-preview` | `hi-IN` yerel ayarı ve `hi-IN-Hemant` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | `hi-IN` yerel ayarı ve `hi-IN-Kalpana-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | `hi-IN` yerel ayarı ve `hi-IN-Kalpana` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-hi-in-kalpana-preview` | `hi-IN` yerel ayarı ve `hi-IN-Kalpana` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-hr-hr-matej-preview` | `hr-HR` yerel ayarı ve `hr-HR-Matej` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | `hu-HU` yerel ayarı ve `hu-HU-Szabolcs` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-id-id-andika-preview` | `id-ID` yerel ayarı ve `id-ID-Andika` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | `it-IT` yerel ayarı ve `it-IT-Cosimo-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-it-it-luciarus-preview` | `it-IT` yerel ayarı ve `it-IT-LuciaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | `ja-JP` yerel ayarı ve `ja-JP-Ayumi-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ja-jp-harukarus-preview` | `ja-JP` yerel ayarı ve `ja-JP-HarukaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | `ja-JP` yerel ayarı ve `ja-JP-Ichiro-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ko-kr-heamirus-preview` | `ko-KR` yerel ayarı ve `ko-KR-HeamiRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ms-my-rizwan-preview` | `ms-MY` yerel ayarı ve `ms-MY-Rizwan` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-nb-no-huldarus-preview` | `nb-NO` yerel ayarı ve `nb-NO-HuldaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-nl-nl-hannarus-preview` | `nl-NL` yerel ayarı ve `nl-NL-HannaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | `pl-PL` yerel ayarı ve `pl-PL-PaulinaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | `pt-BR` yerel ayarı ve `pt-BR-Daniel-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-pt-br-heloisarus-preview` | `pt-BR` yerel ayarı ve `pt-BR-HeloisaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-pt-pt-heliarus-preview` | `pt-PT` yerel ayarı ve `pt-PT-HeliaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ro-ro-andrei-preview` | `ro-RO` yerel ayarı ve `ro-RO-Andrei` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | `ru-RU` yerel ayarı ve `ru-RU-EkaterinaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | `ru-RU` yerel ayarı ve `ru-RU-Irina-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | `ru-RU` yerel ayarı ve `ru-RU-Pavel-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-sk-sk-filip-preview` | `sk-SK` yerel ayarı ve `sk-SK-Filip` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-sl-si-lado-preview` | `sl-SI` yerel ayarı ve `sl-SI-Lado` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | `sv-SE` yerel ayarı ve `sv-SE-HedvigRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-ta-in-valluvar-preview` | `ta-IN` yerel ayarı ve `ta-IN-Valluvar` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-te-in-chitra-preview` | `te-IN` yerel ayarı ve `te-IN-Chitra` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-th-th-pattara-preview` | `th-TH` yerel ayarı ve `th-TH-Pattara` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-tr-tr-sedarus-preview` | `tr-TR` yerel ayarı ve `tr-TR-SedaRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-vi-vn-an-preview` | `vi-VN` yerel ayarı ve `vi-VN-An` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | `zh-CN` yerel ayarı ve `zh-CN-HuihuiRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Kangkang-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Yaoyao-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | `zh-HK` yerel ayarı ve `zh-HK-Danny-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | `zh-HK` yerel ayarı ve `zh-HK-Tracy-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | `zh-HK` yerel ayarı ve `zh-HK-TracyRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | `zh-TW` yerel ayarı ve `zh-TW-HanHanRUS` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | `zh-TW` yerel ayarı ve `zh-TW-Yating-Apollo` sesli kapsayıcı görüntüsü. |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | `zh-TW` yerel ayarı ve `zh-TW-Zhiwei-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-de-de-heddarus-preview` | `de-DE` yerel ayarı ve `de-DE-Hedda` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-de-de-heddarus-preview` | `de-DE` yerel ayarı ve `de-DE-HeddaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | `de-DE` yerel ayarı ve `de-DE-Stefan-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-au-catherine-preview` | `en-AU` yerel ayarı ve `en-AU-Catherine` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-au-hayleyrus-preview` | `en-AU` yerel ayarı ve `en-AU-HayleyRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-gb-george-apollo-preview` | `en-GB` yerel ayarı ve `en-GB-George-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-gb-hazelrus-preview` | `en-GB` yerel ayarı ve `en-GB-HazelRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | `en-GB` yerel ayarı ve `en-GB-Susan-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-in-heera-apollo-preview` | `en-IN` yerel ayarı ve `en-IN-Heera-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-in-priyarus-preview` | `en-IN` yerel ayarı ve `en-IN-PriyaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | `en-IN` yerel ayarı ve `en-IN-Ravi-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-us-benjaminrus-preview` | `en-US` yerel ayarı ve `en-US-BenjaminRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-us-guy24krus-preview` | `en-US` yerel ayarı ve `en-US-Guy24kRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-us-jessa24krus-preview` | `en-US` yerel ayarı ve `en-US-Jessa24kRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-us-jessarus-preview` | `en-US` yerel ayarı ve `en-US-JessaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-en-us-zirarus-preview` | `en-US` yerel ayarı ve `en-US-ZiraRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-es-es-helenarus-preview` | `es-ES` yerel ayarı ve `es-ES-HelenaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-es-es-laura-apollo-preview` | `es-ES` yerel ayarı ve `es-ES-Laura-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | `es-ES` yerel ayarı ve `es-ES-Pablo-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-es-mx-hildarus-preview` | `es-MX` yerel ayarı ve `es-MX-HildaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | `es-MX` yerel ayarı ve `es-MX-Raul-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-fr-ca-caroline-preview` | `fr-CA` yerel ayarı ve `fr-CA-Caroline` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | `fr-CA` yerel ayarı ve `fr-CA-HarmonieRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | `fr-FR` yerel ayarı ve `fr-FR-HortenseRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | `fr-FR` yerel ayarı ve `fr-FR-Julie-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | `fr-FR` yerel ayarı ve `fr-FR-Paul-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | `it-IT` yerel ayarı ve `it-IT-Cosimo-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-it-it-luciarus-preview` | `it-IT` yerel ayarı ve `it-IT-LuciaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | `ja-JP` yerel ayarı ve `ja-JP-Ayumi-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-ja-jp-harukarus-preview` | `ja-JP` yerel ayarı ve `ja-JP-HarukaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | `ja-JP` yerel ayarı ve `ja-JP-Ichiro-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-ko-kr-heamirus-preview` | `ko-KR` yerel ayarı ve `ko-KR-HeamiRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | `pt-BR` yerel ayarı ve `pt-BR-Daniel-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-pt-br-heloisarus-preview` | `pt-BR` yerel ayarı ve `pt-BR-HeloisaRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | `zh-CN` yerel ayarı ve `zh-CN-HuihuiRUS` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Kangkang-Apollo` sesli kapsayıcı görüntüsü. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Yaoyao-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-de-de-hedda-preview` | `de-DE` yerel ayarı ve `de-DE-Hedda` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-de-de-heddarus-preview` | `de-DE` yerel ayarı ve `de-DE-Hedda` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-de-de-heddarus-preview` | `de-DE` yerel ayarı ve `de-DE-HeddaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | `de-DE` yerel ayarı ve `de-DE-Stefan-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-au-catherine-preview` | `en-AU` yerel ayarı ve `en-AU-Catherine` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-au-hayleyrus-preview` | `en-AU` yerel ayarı ve `en-AU-HayleyRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-gb-george-apollo-preview` | `en-GB` yerel ayarı ve `en-GB-George-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-gb-hazelrus-preview` | `en-GB` yerel ayarı ve `en-GB-HazelRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | `en-GB` yerel ayarı ve `en-GB-Susan-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-in-heera-apollo-preview` | `en-IN` yerel ayarı ve `en-IN-Heera-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-in-priyarus-preview` | `en-IN` yerel ayarı ve `en-IN-PriyaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | `en-IN` yerel ayarı ve `en-IN-Ravi-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-us-benjaminrus-preview` | `en-US` yerel ayarı ve `en-US-BenjaminRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-us-guy24krus-preview` | `en-US` yerel ayarı ve `en-US-Guy24kRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-us-jessa24krus-preview` | `en-US` yerel ayarı ve `en-US-Jessa24kRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-us-jessarus-preview` | `en-US` yerel ayarı ve `en-US-JessaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-en-us-zirarus-preview` | `en-US` yerel ayarı ve `en-US-ZiraRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-es-es-helenarus-preview` | `es-ES` yerel ayarı ve `es-ES-HelenaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-es-es-laura-apollo-preview` | `es-ES` yerel ayarı ve `es-ES-Laura-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | `es-ES` yerel ayarı ve `es-ES-Pablo-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-es-mx-hildarus-preview` | `es-MX` yerel ayarı ve `es-MX-HildaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | `es-MX` yerel ayarı ve `es-MX-Raul-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-fr-ca-caroline-preview` | `fr-CA` yerel ayarı ve `fr-CA-Caroline` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | `fr-CA` yerel ayarı ve `fr-CA-HarmonieRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | `fr-FR` yerel ayarı ve `fr-FR-HortenseRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | `fr-FR` yerel ayarı ve `fr-FR-Julie-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | `fr-FR` yerel ayarı ve `fr-FR-Paul-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | `it-IT` yerel ayarı ve `it-IT-Cosimo-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-it-it-luciarus-preview` | `it-IT` yerel ayarı ve `it-IT-LuciaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | `ja-JP` yerel ayarı ve `ja-JP-Ayumi-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-ja-jp-harukarus-preview` | `ja-JP` yerel ayarı ve `ja-JP-HarukaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | `ja-JP` yerel ayarı ve `ja-JP-Ichiro-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-ko-kr-heamirus-preview` | `ko-KR` yerel ayarı ve `ko-KR-HeamiRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | `pt-BR` yerel ayarı ve `pt-BR-Daniel-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-pt-br-heloisarus-preview` | `pt-BR` yerel ayarı ve `pt-BR-HeloisaRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | `zh-CN` yerel ayarı ve `zh-CN-HuihuiRUS` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Kangkang-Apollo` sesli kapsayıcı görüntüsü. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Yaoyao-Apollo` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-us-benjaminrus-preview` | `en-US` yerel ayarı ve `en-US-BenjaminRUS` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-us-guy24krus-preview` | `en-US` yerel ayarı ve `en-US-Guy24kRUS` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-us-jessa24krus-preview` | `en-US` yerel ayarı ve `en-US-Jessa24kRUS` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-us-jessarus-preview` | `en-US` yerel ayarı ve `en-US-JessaRUS` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-en-us-zirarus-preview` | `en-US` yerel ayarı ve `en-US-ZiraRUS` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | `zh-CN` yerel ayarı ve `zh-CN-HuihuiRUS` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Kangkang-Apollo` sesli kapsayıcı görüntüsü. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | `zh-CN` yerel ayarı ve `zh-CN-Yaoyao-Apollo` sesli kapsayıcı görüntüsü. |

## <a name="key-phrase-extraction"></a>Anahtar İfade Ayıklama

[Anahtar ifade ayıklama][ta-kp] kapsayıcı görüntüsü `mcr.microsoft.com` kapsayıcı kayıt defteri genel konumunda bulunabilir. `azure-cognitive-services` deposu içinde bulunur ve `keyphrase`olarak adlandırılır. Tam kapsayıcı görüntü adı, `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="language-detection"></a>Dil Algılama

[Dil algılama][ta-la] kapsayıcı görüntüsü `mcr.microsoft.com` kapsayıcı kayıt defteri genel konumunda bulunabilir. `azure-cognitive-services` deposu içinde bulunur ve `language`olarak adlandırılır. Tam kapsayıcı görüntü adı, `mcr.microsoft.com/azure-cognitive-services/language`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="sentiment-analysis"></a>Duygu Analizi

[Yaklaşım Analizi][ta-se] kapsayıcı görüntüsü `mcr.microsoft.com` kapsayıcı kayıt defteri genel konumunda bulunabilir. `azure-cognitive-services` deposu içinde bulunur ve `sentiment`olarak adlandırılır. Tam kapsayıcı görüntü adı, `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Bu kapsayıcı görüntüsünün aşağıdaki etiketleri mevcuttur:

| Resim etiketleri | Notlar |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
