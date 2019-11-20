---
title: Azure Stream Analytics özelliği karşılaştırması
description: Bu makalede Azure portal, Visual Studio ve Visual Studio Code Azure Stream Analytics bulut ve IoT Edge işleri için desteklenen özellikler karşılaştırılır.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4eb19a5b344cc5bda5ecad724daaddf9b0000d7e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580915"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics özelliği karşılaştırması

Azure Stream Analytics, bulutta ve IoT Edge [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)ve [Visual Studio Code](quick-create-vs-code.md)kullanarak akış çözümleri oluşturabilirsiniz. Bu makaledeki tablolarda her iki iş türü için her platform tarafından desteklenen özellikler gösterilir.

## <a name="cloud-job-features"></a>Bulut işi özellikleri


|Özellik  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Platformlar arası     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Betik yazma     |Evet         |Evet         |Evet         |
|Betik IntelliSense     |Söz dizimi vurgulama         |Söz dizimi vurgulama</br>Kod tamamlama</br>Hata işaretçisi         |Söz dizimi vurgulama</br>Kod tamamlama</br>Hata işaretçisi         |
|Girdileri, çıkışları ve iş yapılandırmasını tanımlama     |Evet         |Evet         |Evet         |
|Blob çıkış bölümlendirme     |Evet         |Evet         |Evet         |
|Çıkış olarak Power BI     |Evet         |Evet         |Hayır         |
|SQL veritabanı başvuru verileri     |Evet         |Evet         |Evet         |
|Özel ileti özellikleri     |Evet         |Hayır         |Hayır         |
|Birden çok sorgu arasında girişleri ve çıkışları paylaşma     |Hayır         |Evet         |Evet         |
|JavaScript UDF ve UDA     |Evet         |Evet         |Yalnızca Windows         |
|Machine Learning belirtme çizgileri     |Evet, ancak sorgu test edilemez        |Evet, ancak yerel olarak test edilemez         |Hayır         |
|Uyumluluk düzeyi     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Yerleşik ML tabanlı anomali algılama işlevleri     |Evet         |Evet         |Evet         |
|Yerleşik Jeo-uzamsal işlevler     |Evet         |Evet         |Evet         |
|Örnek dosya ile sorgu testi     |Evet         |Evet         |Evet         |
|Canlı veri yerel testi     |Hayır         |Evet         |Hayır         |
|İşleri listeleme ve iş varlıklarını görüntüleme     |Evet         |Evet         |Evet         |
|Yerel bir projeye bir işi dışarı aktarma     |Hayır         |Evet         |Evet         |
|İşleri gönderme, başlatma ve durdurma     |Evet         |Evet         |Evet         |
|Kaynak denetimi     |Hayır         |Evet         |Evet         |
|CI/CD desteği     |Kısmi         |Evet         |Evet         |
|İş ölçümlerini ve Diyagramı görüntüleme     |Evet         |Evet         |Portalda açma         |
|İş çalışma zamanı hatalarını görüntüleme     |Evet         |Evet         |Hayır         |
|Tanılama günlükleri     |Evet         |Hayır         |Hayır         |


## <a name="iot-edge-job-features"></a>IoT Edge iş özellikleri

|Özellik  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|İş yazma     |Evet         |Evet         |Hayır         |
|Kaynak denetimi     |Hayır         |Evet         |Hayır         |
|Yerel bir projeye bir işi dışarı aktarma     |Hayır         |Evet         |Hayır         |
|Örnek dosya ile sorgu testi     |Evet         |Evet         |Hayır         |
|Birden çok sorgu arasında girişleri ve çıkışları paylaşma     |Hayır         |Evet         |Hayır         |
|C#SÜRÜMÜNDE     |Hayır         |Evet         |Hayır         |
|İşleri gönder     |Evet         |Evet         |Hayır         |
|İşleri listeleme ve iş varlıklarını görüntüleme     |Evet         |Evet         |Hayır         |
|İş ölçümlerini ve Diyagramı görüntüleme     |Evet         |Kısmi         |Hayır         |
|İş çalışma zamanı hatalarını görüntüleme     |Evet         |Kısmi         |Hayır         |
|CI/CD desteği     |Hayır         |Hayır         |Hayır         |


## <a name="next-steps"></a>Sonraki adımlar

* [IoT Edge üzerinde Azure Stream Analytics](stream-analytics-edge.md)
* [Öğretici: Azure Stream Analytics IoT Edge C# işi için Kullanıcı tanımlı bir işlev yazma (Önizleme)](stream-analytics-edge-csharp-udf.md)
* [Visual Studio araçlarını kullanarak Stream Analytics IoT Edge işleri geliştirme](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Azure Stream Analytics işleri görüntülemek için Visual Studio 'Yu kullanma](stream-analytics-vs-tools.md)
* [Visual Studio Code ile Azure Stream Analytics araştırma (Önizleme)](vscode-explore-jobs.md)


