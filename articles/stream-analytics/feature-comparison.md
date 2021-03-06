---
title: Azure Stream Analytics özelliği karşılaştırması
description: Bu makalede Azure portal, Visual Studio ve Visual Studio Code Azure Stream Analytics bulut ve IoT Edge işleri için desteklenen özellikler karşılaştırılır.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 037bd8bc823cd8c77241d0ca25174e29d25149b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020545"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics özelliği karşılaştırması

Azure Stream Analytics, bulutta ve IoT Edge [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)ve [Visual Studio Code](quick-create-visual-studio-code.md)kullanarak akış çözümleri oluşturabilirsiniz. Bu makaledeki tablolarda her iki iş türü için her platform tarafından desteklenen özellikler gösterilir.

> [!NOTE]
> Visual Studio ve Visual Studio Code araçları, Çin Doğu, Çin Kuzey, Almanya Orta ve Almanya kuzeydoğu bölgelerinde bulunan işleri desteklemez.

## <a name="cloud-job-features"></a>Bulut işi özellikleri


|Özellik  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Platformlar arası     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Betik yazma     |Yes         |Yes         |Yes         |
|Betik IntelliSense     |Söz dizimi vurgulama         |Söz dizimi vurgulama</br>Kod tamamlama</br>Hata işaretçisi         |Söz dizimi vurgulama</br>Kod tamamlama</br>Hata işaretçisi         |
|Tüm giriş, çıkış ve iş yapılandırması türlerini tanımlayın     |Yes         |Yes         |Yes         |
|Kaynak denetimi     |Hayır         |Yes         |Yes         |
|CI/CD desteği     |Kısmi         |Yes         |Yes         |
|Birden çok sorgu arasında girişleri ve çıkışları paylaşma     |Hayır         |Yes         |Yes         |
|Örnek dosya ile sorgu testi     |Yes         |Yes        |Yes         |
|Canlı veri yerel testi     |Hayır         |Yes       |Yes      |
|İşleri listeleme ve iş varlıklarını görüntüleme     |Yes         |Yes        |Yes         |
|Yerel bir projeye bir işi dışarı aktarma     |Hayır         |Yes         |Yes         |
|İşleri gönderme, başlatma ve durdurma     |Yes         |Yes         |Yes         |
|İş ölçümlerini ve Diyagramı görüntüleme     |Yes         |Yes         |Portalda açma         |
|İş çalışma zamanı hatalarını görüntüleme     |Yes         |Yes         |Hayır         |
|Kaynak günlükleri     |Yes         |Hayır         |Hayır         |
|Özel ileti özellikleri     |Yes         |Yes         |Hayır       |
|C# özel kod işlevi ve seri hale getirici|Salt okunurdur modu|Yes|Hayır|
|JavaScript UDF ve UDA     |Yes         |Yes         |Yalnızca Windows         |
|Machine Learning Hizmeti     |Yes        |Yes         |Hayır         |
|Azure Machine Learning Studio (klasik)|Evet, ancak sorgu test edilemez        |Yes |Hayır         |
|Uyumluluk düzeyi     |1.0</br>1.1</br>1,2 (varsayılan)         |1.0</br>1.1</br>1,2 (varsayılan)           |1.0</br>1.1</br>1,2 (varsayılan)           |
|Yerleşik ML tabanlı anomali algılama işlevleri     |Yes         |Yes         |Yes         |
|Yerleşik Jeo-uzamsal işlevler     |Yes         |Yes         |Yes         |



## <a name="iot-edge-job-features"></a>IoT Edge iş özellikleri

|Özellik  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|İş yazma     |Yes         |Yes         |Hayır         |
|Kaynak denetimi     |Hayır         |Yes         |Hayır         |
|Yerel bir projeye bir işi dışarı aktarma     |Hayır         |Yes         |Hayır         |
|Örnek dosya ile sorgu testi     |Yes         |Yes         |Hayır         |
|Birden çok sorgu arasında girişleri ve çıkışları paylaşma     |Hayır         |Yes         |Hayır         |
|C# UDF     |Hayır         |Yes         |Hayır         |
|İşleri gönder     |Yes         |Yes         |Hayır         |
|İşleri listeleme ve iş varlıklarını görüntüleme     |Yes         |Yes         |Hayır         |
|İş ölçümlerini ve Diyagramı görüntüleme     |Yes         |Kısmi         |No         |
|İş çalışma zamanı hatalarını görüntüleme     |Yes         |Kısmi         |No         |
|CI/CD desteği     |Hayır         |Hayır         |Hayır         |


## <a name="next-steps"></a>Sonraki adımlar

* [IoT Edge üzerinde Azure Stream Analytics](stream-analytics-edge.md)
* [Öğretici: Azure Stream Analytics IoT Edge işi için C# Kullanıcı tanımlı bir işlev yazma (Önizleme)](stream-analytics-edge-csharp-udf.md)
* [Visual Studio araçlarını kullanarak Stream Analytics IoT Edge işleri geliştirme](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Azure Stream Analytics işleri görüntülemek için Visual Studio 'Yu kullanma](stream-analytics-vs-tools.md)
* [Visual Studio Code ile Azure Stream Analytics araştırma (Önizleme)](visual-studio-code-explore-jobs.md)


