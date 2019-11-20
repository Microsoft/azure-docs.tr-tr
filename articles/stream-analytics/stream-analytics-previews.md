---
title: Azure Stream Analytics Önizleme özellikleri
description: Bu makalede, şu anda önizleme aşamasında olan Azure Stream Analytics özellikleri listelenmektedir.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: df3e8c1cd91c676c64d15c46c5acdc3d5bcfaa8e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161366"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics Önizleme özellikleri

Bu makale, şu anda Azure Stream Analytics için Önizlemedeki tüm özellikleri özetler. Bir üretim ortamında Önizleme özelliklerinin kullanılması önerilmez.

## <a name="public-previews"></a>Genel önizlemeler

Aşağıdaki özellikler genel önizlemede. Günümüzde bu özelliklerden faydalanabilirsiniz, ancak bunları üretim ortamınızda kullanmayın.

### <a name="online-scaling"></a>Çevrimiçi ölçeklendirme

Çevrimiçi ölçeklendirmeyle, SU ayırmayı değiştirmeniz gerekiyorsa işinizi durdurmanız gerekmez. Çalışan bir işin SU kapasitesini, durdurmak zorunda kalmadan artırabilir veya azaltabilirsiniz. Bu, bugün Stream Analytics sunduğu, uzun süreli görev açısından kritik işlem hatları için müşteri taahhüdünü oluşturur. Daha fazla bilgi için bkz. [Azure Stream Analytics akış birimlerini yapılandırma](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#Özel de serileştiriciler
Geliştiriciler, verileri prototip, XML veya özel bir biçimde işlemek için Azure Stream Analytics gücünden yararlanabilir. ' De C#, Azure Stream Analytics tarafından alınan olayların serileştirilmek için kullanılabilecek [özel de serileştiriciler](custom-deserializer-examples.md) uygulayabilirsiniz.

### <a name="extensibility-with-c-custom-code"></a>C# Özel kodla genişletilebilirlik

Bulutta veya IoT Edge Stream Analytics modülleri oluşturan geliştiriciler özel C# işlevleri yazabilir veya yeniden kullanabilir ve [Kullanıcı tanımlı işlevler](stream-analytics-edge-csharp-udf-methods.md)aracılığıyla doğrudan sorgu içinde çağırabilir.

### <a name="managed-identity-authentication-with-power-bi"></a>Power BI ile yönetilen kimlik kimlik doğrulaması

Azure Stream Analytics, dinamik bir çizgi oluşturma deneyimi için Power BI ile yönetilen kimlik tabanlı kimlik doğrulaması için tam destek sunar.

### <a name="anomaly-detection"></a>Anomali Algılama

Azure Stream Analytics ' makine öğrenimi modelleri, çift yönlü, yavaş pozitif ve yavaş negatif eğilimler algılamasına ek olarak *ani artış* ve *DIB 'ler* algılamayı destekler. Daha fazla bilgi için [Azure Stream Analytics 'de anomali algılamayı](stream-analytics-machine-learning-anomaly-detection.md)ziyaret edin.

### <a name="debug-query-steps-in-visual-studio"></a>Visual Studio 'da sorgu adımlarında hata ayıkla

Visual Studio için Azure Stream Analytics araçları 'nda yerel test yaparken, veri diyagramında ara satır kümesini kolayca önizleyebilirsiniz. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Visual Studio Code içindeki canlı verilerle yerel test

İşi Azure 'a göndermeden önce, sorgularınızı yerel makinenizde canlı verilere karşı test edebilirsiniz. Her test yinelemesi ortalama olarak iki ile üç saniyeden az sürer ve çok verimli bir geliştirme süreci elde edilir.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics için Visual Studio Code

Azure Stream Analytics işleri Visual Studio Code olarak yazılabilir. [Vs Code kullanmaya başlama Öğreticimizi](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)inceleyin.


### <a name="anomaly-detection"></a>Anomali Algılama

Azure Stream Analytics, çift yönlü, yavaş pozitif ve yavaş negatif eğilimler algılamasına ek olarak *ani artış* ve *DIB 'ler* algılama desteğiyle yeni makine öğrenimi modelleri sunmaktadır. Daha fazla bilgi için [Azure Stream Analytics 'de anomali algılamayı](stream-analytics-machine-learning-anomaly-detection.md)ziyaret edin.


### <a name="integration-with-azure-machine-learning"></a>Azure Machine Learning ile tümleştirme

Stream Analytics işlerini Machine Learning (ML) işlevleriyle ölçeklendirebilirsiniz. Stream Analytics işte ML işlevlerini nasıl kullanabileceğiniz hakkında daha fazla bilgi edinmek için [Stream Analytics işinizi Azure Machine Learning Işlevlerle ölçeklendirin](stream-analytics-scale-with-machine-learning-functions.md). [Azure Stream Analytics ve Azure Machine Learning kullanarak yaklaşım analizi gerçekleştirerek](stream-analytics-machine-learning-integration-tutorial.md)gerçek dünyada bir senaryoya göz atın.


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio 'da canlı veri testi

Azure Stream Analytics için Visual Studio Araçları, Olay Hub 'ı veya IoT Hub gibi bulut kaynaklarından canlı etkinlik akışlarına karşı sorguları test etmenizi sağlayan yerel test özelliğini geliştirir. [Visual Studio için Azure Stream Analytics araçları 'nı kullanarak canlı verileri yerel olarak test](stream-analytics-live-data-local-testing.md)etme hakkında bilgi edinin.


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge üzerinde .NET Kullanıcı tanımlı işlevler

.NET standardı Kullanıcı tanımlı işlevlerle, akış işlem hattının bir parçası olarak .NET Standard kodu çalıştırabilirsiniz. Basit C# sınıflar oluşturabilir veya tam proje ve kitaplıkları içeri aktarabilirsiniz. Visual Studio 'da tam yazma ve hata ayıklama deneyimi desteklenir. Daha fazla bilgi için [Azure Stream Analytics Edge işleri için Kullanıcı tanımlı .NET Standard geliştirme](stream-analytics-edge-csharp-udf-methods.md)makalesini ziyaret edin.

## <a name="other-previews"></a>Diğer önizlemeler

Ayrıca, istek üzerine Önizleme bölümünde aşağıdaki özellikler de mevcuttur.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning tarafından yönetilen özel ML modelleriyle gerçek zamanlı yüksek performans Puanlama

Azure Stream Analytics, Azure Machine Learning tarafından yönetilen ve Azure Kubernetes Service (AKS) veya Azure Container Instances (acı) ' de barındırılan, bir iş akışı kullanarak yüksek performanslı ve gerçek zamanlı Machine Learning Puanlama destekler Bu, kod yazmanızı gerektirmez. Önizleme için [kaydolun](https://aka.ms/asapreview1)

### <a name="support-for-azure-stack"></a>Azure Stack için destek
Azure IoT Edge çalışma zamanında etkinleştirilen bu özellik, Azure Stack çalışan yerel girişler ve çıktılar için yerel destek gibi özel Azure Stack özelliklerinden yararlanır (örneğin, Event Hubs, IoT Hub, BLOB depolama). Bu yeni tümleştirme, verilerinizi nerede oluşturulduğu yere çözümleyebilecekleri karma mimariler oluşturmanızı, gecikme süresini azaltmayı ve öngörüleri en üst düzeye çıkarmanızı sağlar.
Bu özellik, verilerinizin oluşturulduğu yere yakın, gecikme süresini düşürürken ve öngörüleri en üst düzeye çıkarmanızı sağlayan karma mimariler oluşturmanıza olanak sağlar. Bu önizlemeye [kaydolmanız](https://aka.ms/asapreview1) gerekir.
