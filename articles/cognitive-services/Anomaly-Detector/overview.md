---
title: Anomali Algılayıcısı API'si nedir?
titleSuffix: Azure Cognitive Services
description: Zaman serisi verilerinizde bozukluklar belirlemek için anomali algılayıcı API 'sinin gelişmiş algoritmalarını kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 706bd9c141b191632acee1c04745d64baaf50ee5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012052"
---
# <a name="what-is-the-anomaly-detector-api"></a>Anomali Algılayıcısı API'si nedir?

Anomali algılayıcı API 'SI, makine öğrenimi ile zaman serisi verilerinizde bulunan normalleştirikleri izlemenize ve algılamanıza olanak sağlar. Anomali algılayıcı API 'SI, sektör, senaryo veya veri hacminin ne olursa olsun, verilerinize en uygun modelleri otomatik olarak tanımlayarak ve uygulayarak uyum sağlar. API, zaman serisi verilerinizi kullanarak anomali algılama, beklenen değerler ve hangi veri noktalarının güvenlik sınırlarını belirler.

![Hizmet isteklerindeki model değişikliklerini Algıla](./media/anomaly_detection2.png)

Anomali algılayıcısının kullanılması, Machine Learning 'de önceki bir deneyim gerektirmez ve yeniden işleme API 'SI, hizmeti uygulamalarınızın ve süreçlerinizle kolayca tümleştirmenize olanak sağlar.

## <a name="features"></a>Özellikler

Anomali algılayıcısı sayesinde, zaman serisi verileriniz genelinde veya gerçek zamanlı olarak oluşan anormallikleri otomatik olarak tespit edebilirsiniz. 

|Özellik  |Açıklama  |
|---------|---------|
|Gerçek zamanlı olarak gerçekleştikleri gibi bozukluklar olup olmadığı tespit edin. | En son birinin bir anomali olup olmadığını belirlemek için daha önce görülmüş veri noktalarını kullanarak akış verilerinizde bozukluklar algılayın. Bu işlem, göndereceğiniz veri noktalarını kullanarak bir model oluşturur ve hedef noktanın bir anomali olup olmadığını belirler. Oluşturduğunuz her yeni veri noktasıyla API çağırarak, verileri oluşturulduğu gibi izleyebilirsiniz. |
|Veri kümesinin tamamında bir toplu iş olarak, anormallikleri tespit edin. | Verilerinizin tamamında mevcut olabilecek tüm anormallikleri algılamak için zaman serinizi kullanın. Bu işlem, her bir noktanın aynı modelle çözümlenme işleminde, tüm zaman serisi verilerinizi kullanarak bir model oluşturur.         |
| Verileriniz hakkında daha fazla bilgi alın. | Verileriniz ve beklenen değerler, anomali sınırları ve pozisyonları dahil tüm gözlemlenen bozukluklar hakkında yararlı Ayrıntılar alın. |
| Anomali algılama sınırlarını ayarlayın. | Anomali algılayıcı API 'SI, anomali algılama için sınırları otomatik olarak oluşturur. Bu sınırları, API 'nin veri bozuklularındaki duyarlılığını artırmak veya azaltmak için ayarlayın ve verilerinize daha iyi uyum sağlar. |

## <a name="demo"></a>Tanıtım

Anomali algılayıcısının nasıl çalıştığını anlamak için bu [etkileşimli tanıtımı](https://aka.ms/adDemo) inceleyin.
Tanıtımı çalıştırmak için bir anomali algılayıcısı kaynağı oluşturmanız ve API anahtarını ve uç noktasını almanız gerekir.

## <a name="notebook"></a>Not Defteri

Anomali algılayıcı API 'sini çağırmayı öğrenmek için bu [Azure Not defterini](https://aka.ms/adNotebook)deneyin. Bu Web 'de barındırılan Jupyter Notebook, bir API isteğinin nasıl gönderileceğini ve sonucu görselleştirmeyi gösterir.

Not defterini çalıştırmak için aşağıdaki adımları gerçekleştirin:

1. Geçerli bir anomali algılayıcı API 'SI abonelik anahtarı ve bir API uç noktası alın. Aşağıdaki bölümde kaydolma talimatları bulunur.
1. Oturum açın ve sağ üst köşedeki Kopyala ' ya tıklayın.
1. Kopyalama işlemini tamamlamadan önce iletişim kutusundaki "genel" seçeneğinin işaretini kaldırın, aksi takdirde tüm abonelik anahtarları dahil Not defteriniz herkese açık olur.
1. **Ücretsiz işlem sırasında Çalıştır 'a** tıklayın
1. Not defterlerinden birini seçin.
1. Geçerli anomali algılayıcısı API abonelik anahtarınızı `subscription_key` değişkenine ekleyin. 
1. `endpoint` değişkenini uç noktanızla değiştirin. Örneğin, `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Üstteki menü çubuğunda **hücre**' ya ve ardından **Tümünü Çalıştır**' ı tıklatın.

## <a name="workflow"></a>İş akışı

Anomali algılayıcı API 'SI, bir daha fazla Web hizmeti olduğundan, HTTP istekleri yapıp JSON 'u ayrıştırabilen herhangi bir programlama dilinden çağrı yapmayı kolaylaştırır.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Kaydolduktan sonra:

1. Zaman serisi verilerinizi alın ve geçerli bir JSON biçimine dönüştürün. En iyi sonuçları elde etmek için verilerinizi hazırlarken [en iyi uygulamaları](concepts/anomaly-detection-best-practices.md) kullanın.
1. Anomali algılayıcısı API 'sine verilerinize bir istek gönderin.
1. Döndürülen JSON iletisini ayrıştırarak API yanıtını işleyin.

## <a name="algorithms"></a>Algoritmalar

* Lütfen [Azure anomali algılayıcısı API 'sine](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) başvuran teknik bloglara ve [Azure anomali ALGıLAYıCıSı ' nda on 'un](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798) altındaki algoritmalarla ilgili genel bakışlara bakın.
* Microsoft tarafından geliştirilen son teknoloji tabanlı SR-CNN algoritmaları için lütfen [Microsoft 'ta bu kağıt zaman serisi anomali algılama hizmeti](https://arxiv.org/abs/1906.03821) (kdd 2019 tarafından kabul edilir) bölümüne bakın.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Anomali Algılayıcısı topluluğuna katılın

* [Microsoft ekiplerinde anomali algılayıcı danışmanları grubuna](https://aka.ms/AdAdvisorsJoin) katılarak
* Seçilen [Kullanıcı tarafından oluşturulan içeriğe](user-generated-content.md) bakın

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: anomali algılayıcısının kullanıldığı zaman serisi verilerinizde bozukluklar Algıla REST API](quickstarts/detect-data-anomalies-csharp.md)
* Anomali algılayıcı API 'SI [çevrimiçi tanıtımı](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Anomali algılayıcısı [REST API başvurusu](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
