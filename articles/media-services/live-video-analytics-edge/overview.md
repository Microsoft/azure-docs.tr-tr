---
title: IoT Edge Azure 'da canlı video analizi nedir?
description: Bu konu, IoT Edge üzerindeki canlı video analizlerine genel bakış sağlar. Platform, IoT çözümlerinizi geliştirmek için kullanabileceğiniz özelliği sunar. Örneğin, canlı videoyu yakalayın, kaydedin, çözümleyin, Azure hizmetlerine sonuçları (video ve/veya video analizi) yayımlayın.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: b9b8726c5e8ad6850e05aeee48fccabee703080e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90904346"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>IoT Edge canlı video analizi nedir? (önizleme)

IoT Edge’de Canlı Video Analizi hem uca hem de buluta yayılan akıllı video uygulamaları oluşturmak için bir platform sağlar. Platform canlı video yakalama, kaydetme ve analiz özelliklerinin yanı sıra sonuçları (video ve/veya video analizi) Azure hizmetlerine (bulutta ve/veya uçta) yayımlama olanağını da sunar. Platform, video analiziyle IoT çözümlerini geliştirmek için kullanılabilir. IoT Edge işlevselliğindeki canlı video analizi, IoT Edge üzerinde Stream Analytics ve IoT Edge, Olay Hub 'ı, bilişsel hizmetler gibi bulutta bulunan Azure hizmetlerinin yanı sıra güçlü karma (örneğin, Edge + bulut) uygulamaları oluşturmak için kullanabileceğiniz diğer Azure IoT Edge modülleriyle birleştirilebilir.

IoT Edge üzerindeki canlı video analizi, genişletilebilir bir platform olacak şekilde tasarlanmıştır. bu sayede, farklı video analizi kenar modüllerini (örneğin, açık kaynaklı makine öğrenimi modelleri veya kendi verilerinize göre tasarlanmış özel modeller) bağlayarak canlı videoyu analiz etmek ve canlı video işlem hattı oluşturma ve çalıştırma karmaşıklığı hakkında endişelenmeden bunları kullanabilirsiniz.

## <a name="accelerate-iot-solutions-development"></a>IoT çözümlerini geliştirmeyi hızlandırma 

Video analizini diğer IoT algılayıcılarının sinyalleriyle ve/veya iş verileriyle birleştiren IoT çözümleri iş kararlarını otomatik veya yarı otomatik hale getirmenize yardımcı olarak sonuçta üretkenlik geliştirmeleri sağlar. IoT Edge’de Canlı Video Analizi bu tür çözümleri daha hızlı oluşturmanıza olanak tanır. İşlerinize özgü video analizi modülleri ve mantığı oluşturmaya, platformun video işlem hattını yönetmenin ve çalıştırmanın karmaşıklıklarını gizlemesini sağlamaya odaklanabilirsiniz.

IoT Edge üzerinde canlı video analizi sayesinde, mevcut [video yönetim sistemlerinizle (VM 'ler)](https://en.wikipedia.org/wiki/Video_management_system) [CCTV kameralarınızı](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) kullanmaya devam edebilir ve video analizi uygulamalarını bağımsız olarak oluşturabilirsiniz. IoT Edge’de Canlı Video Analizi görüntü işleme SDK’ları ve araç setleriyle birlikte kullanılarak son derece gelişmiş IoT çözümleri oluşturulabilir. Aşağıdaki diyagramda bunu çizimi yer alır.

![IoT Edge 'da canlı video analiziyle IoT çözümleri geliştirin](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Desteklenen ortamlar

Linux x86-64 ve ARM64 ortamları desteklenir.
> [!NOTE]
> ARM64 cihazlarına yönelik destek, derlemelerde `1.0.4` ve daha yeni sürümlerde kullanılabilir.
> ARM64 cihazlarında Azure IoT Edge Runtime çalıştırmaya yönelik destek [genel önizlemededir](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-started"></a>başlarken

Aşağıdaki kavram makalelerini okuyun ve ardından canlı bir video akışında hareket algılamayı çalıştırmak için hızlı başlangıç yapmayı deneyin.

### <a name="concepts"></a>Kavramlar

* [Medya grafiği](media-graph-concept.md)
* [Video kaydı](video-recording-concept.md)
* [Video kayıttan yürütme](video-playback-concept.md)
* [Sürekli video kaydı](continuous-video-recording-concept.md)
* [Olay tabanlı video kaydı](event-based-video-recording-concept.md)
* [Video kaydı olmadan Canlı Video Analizi](analyze-live-video-concept.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: canlı video analizinizi kendi modelinizle çalıştırın](use-your-model-quickstart.md) ve canlı video akışında hareket algılamayı nasıl çalıştıracağınızı öğrenin.
* [Terminolojiyi](terminology.md) gözden geçir
* [Canlı video analizi açık kaynak malzemesini](https://github.com/Azure/live-video-analytics) keşfet

