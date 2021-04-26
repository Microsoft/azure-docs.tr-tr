---
title: Anomali algılayıcısı çok değişen API nedir?
titleSuffix: Azure Cognitive Services
description: Yeni anomali algılayıcısı genel önizleme çok değişkenli API 'Lerine genel bakış.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomali algılama, makine öğrenimi, algoritmalar
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315668"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Çok değişen zaman serisi anomali algılama (Genel Önizleme)

Azure bilişsel hizmetler anomali algılayıcısının ilk sürümü, kullanımı kolay [Tek değişkenli zaman serisi anomali algılayıcı API 'lerini](overview.md)kullanarak ölçüm izleme çözümleri oluşturmanıza izin verildi. Her zaman serinin analizine izin vererek anomali algılayıcı bağımsız değişken kolaylık ve ölçeklenebilirlik sağlar.

Yeni **multivarianomali algılama** API 'leri, Machine Learning bilgisine veya etiketli verilere gerek kalmadan, gelişmiş AI 'nin ölçüm gruplarından aykırları tespit etmek üzere kolayca tümleştirilmesine olanak tanır. 300 kadar farklı sinyal arasındaki bağımlılıklar ve şirketler arası ilişkiler artık otomatik olarak anahtar faktörü olarak sayılır. Bu yeni özellik, yazılım uygulamaları, sunucular, fabrika makineleri, spacecrat, hatta işiniz gibi karmaşık sistemlerinizi hatalara karşı önceden korumanıza yardımcı olur.

Otomatik altyapıdan 20 algılayıcı, titreşim, sıcaklık, yakıt baskısı vb. gibi 20 farklı sinyal oluşturuyor. Bu sinyallerin ayrı ayrı kullanımları, sistem düzeyi sorunları hakkında çok daha fazla bilgi vermeyebilir, ancak bunlar altyapının sistem durumunu temsil edebilir. Bu sinyallerin etkileşimi, her zamanki aralığın dışında farklılık gösterir. çok varianomali algılama özelliği, deneyimli bir uzman gibi anomali 'i de anlamış olabilir. Temel alınan AI modelleri, işletmenizin benzersiz ihtiyaçlarını anlayacağı için verileriniz kullanılarak eğitilmiş ve özelleştirilmiş hale gelir. Anomali algılayıcısı 'ndaki yeni API 'Ler sayesinde, geliştiriciler artık çok büyük ölçekli zaman serisi anomali algılama yeteneklerini tahmine dayalı bakım çözümlerine, karmaşık kurumsal yazılımlara yönelik Aıops izleme çözümlerine veya iş zekası araçlarına kolayca tümleştirebilir.

## <a name="when-to-use-multivariate-versus-univariate"></a>**Çok varilıya** ve **tek tek** eşleme ne zaman kullanılır?

Amacınız, kendi geçmiş verilerine göre yalnızca her bir zaman serisinde normal bir düzende olan bozuklukları algılamadıysanız, univarianomali algılama API 'Lerini kullanın. Örnekler: gelir verilerinin kendisini temel alarak günlük gelir anormallarını algılamak veya CPU verilerini tamamen bir CPU 'ya göre saptamak istiyorsanız
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Tek bir değişkenin dalgalanmasına sahip, turuncu daireler tarafından tanımlanan anormallarla bir mavi çizgi ile yakalanan değerleri taşıyan zaman serisi çizgisi grafiği](./media/anomaly_detection2.png)

Amacınız, bir zaman serisi verileri grubundan sistem düzeyi anormallarını algılıyorsanız, aşağıdaki multivarianomali algılama API 'Lerini kullanın. Özellikle, herhangi bir zaman serisi size çok önemli değildir ve sistem düzeyi sorunu tespit etmek için tüm sinyallere (bir zaman serisi grubu) Holistik olarak bakmanız gerekir. Örnek: uçak, yağ tertibatı veya uydu gibi pahalı bir fiziksel varlığınız vardır. Bu varlıkların her birinde onlarca veya yüzlerce farklı algılayıcı türü vardır. Sistem düzeyi sorunu olup olmadığına karar vermek için bu sensörlerden gelen tüm zaman serisi sinyallerine bakmanız gerekir.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Değişkenler için birden çok zaman serisi çizgi grafiği: titreşim, sıcaklık, basınç, hız, anormalilerin turuncu ile vurgulanmış şekilde döndürme hızı](./media/multivariate-graph.png)

## <a name="region-support"></a>Bölge desteği

Anomali algılayıcı multivaritünün genel önizlemesi Şu anda üç bölgede mevcuttur: Batı ABD2, Doğu ABD2 ve Batı Avrupa.

## <a name="algorithms"></a>Algoritmalar

- [Grafik dikkat ağı aracılığıyla çok sayıda zaman serisi anomali algılama](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Anomali Algılayıcısı topluluğuna katılın

- [Microsoft ekiplerinde anomali algılayıcı danışmanları grubuna](https://aka.ms/AdAdvisorsJoin) katılarak

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç](./quickstarts/client-libraries-multivariate.md).
- [En Iyi uygulamalar](./concepts/best-practices-multivariate.md): Bu makale, çok değişkenli API 'ler ile kullanmak için önerilen desenlerle ilgilidir.
