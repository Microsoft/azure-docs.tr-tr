---
title: Azure Percept AI modelleri
description: Prototipleme ve dağıtım için kullanılabilen AI modelleri hakkında daha fazla bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: f10a330de52d40f728cd628a1d7cb83b54ad1ff6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557369"
---
# <a name="azure-percept-ai-models"></a>Azure Percept AI modelleri

Azure Percept, Azure [Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)'Dan doğrudan [Azure PERCEPT dk](./overview-azure-percept-dk.md) 'ye AI modelleri geliştirmenize ve dağıtmanıza olanak sağlar. Model dağıtımı [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) ve [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)kullanır.

## <a name="sample-ai-models"></a>Örnek AI modelleri

Azure Percept Studio, aşağıdaki uygulamalar için örnek modeller içerir:

- insanların algılanması
- araç algılama
- Genel nesne algılama
- ürün-raf üzerinde algılama

Önceden eğitilen modeller sayesinde, kodlama veya eğitim veri koleksiyonu gerekmez. İstediğiniz modeli portalda Azure Percept DK 'ye [dağıtmanız](./how-to-deploy-model.md) yeterlidir ve devkit 'in [video akışını](./how-to-view-video-stream.md) açarak model ınkıt 'in işlem içinde olduğunu görürsünüz. [Model ınmetriyle sınırlama telemetrisine](./how-to-view-telemetry.md) [Azure IoT gezgin](https://github.com/Azure/azure-iot-explorer/releases) aracı üzerinden de erişilebilir.

## <a name="reference-solutions"></a>Başvuru çözümleri

Bir [kişi sayma başvuru çözümü](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) de mevcuttur. Bu başvuru çözümü, Kullanıcı tanımlı bölge giriş/çıkış olayları ile, Edge tabanlı kişiler sağlayan açık kaynaklı bir AI uygulamasıdır. Şirket içi sınır cihazından alınan video ve AI çıkışı, Azure Web sitesi olarak çalışan kullanıcı arabirimiyle [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/). AI ınsıya özellikleri, insanlar algılaması için açık kaynaklı bir AI modeliyle sağlanır.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Uzamsal analiz önceden oluşturulmuş çözüm GIF 'i.":::

## <a name="custom-no-code-solutions"></a>Özel kod içermeyen çözümler

Azure Percept Studio sayesinde, hiçbir kodlama [gerekmeden özel görüntü](./tutorial-nocode-vision.md) ve [konuşma](./tutorial-no-code-speech.md) çözümleri geliştirebilirsiniz.

Özel görme çözümleri için, hem nesne algılama hem de sınıflandırma AI modelleri kullanılabilir. Yalnızca eğitim görüntülerinizi karşıya yükleyip etiketleyerek Azure Percept DK 'nin isteniyorsa Azure Percept Vision SoM ile doğrudan alınabilir. Model eğitimi ve değerlendirmesi, Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/#overview)'in bir parçası olan [özel görüntü işleme](https://www.customvision.ai/)kolayca gerçekleştirilir.

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

Özel konuşma çözümleri için şu anda aşağıdaki uygulamalar için ses Yardımcısı şablonları kullanılabilir:

- Hosititesi: ses denetimli akıllı cihazlarla birlikte bulunan otel odası.
- Sağlık Hizmetleri: Bu özellik, sesli denetimli akıllı cihazlarla donatılmıştır.
- Envanter: ses denetimli akıllı cihazlarla birlikte bulunan envanter hub 'ı.
- Oto: ses denetimli akıllı cihazlarla donatılmış bir şekilde, oto.

Önceden oluşturulmuş sesli yardım anahtar sözcükleri ve komutları Portal üzerinden doğrudan kullanılabilir. Özel anahtar sözcükler ve komutlar, Azure bilişsel hizmetler 'in de bir parçası olan [konuşma Studio](https://speech.microsoft.com/)'da oluşturulabilir ve eğitilmiş olabilir.

## <a name="advanced-development"></a>Gelişmiş geliştirme

Lütfen güncel rehberlik, öğreticiler ve örnekler için bkz. [Azure PERCEPT dk gelişmiş geliştirme GitHub](https://github.com/microsoft/azure-percept-advanced-development) , şunun gibi şeyler:

- Azure Percept DK 'ye özel bir AI modeli dağıtma
- Desteklenen bir modeli aktarma öğrenimiyle güncelleştirme
- Ve daha fazlası
