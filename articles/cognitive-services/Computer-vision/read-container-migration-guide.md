---
title: Read v3. x kapsayıcılarına geçme
titleSuffix: Azure Cognitive Services
description: V3 okuma OCR kapsayıcılarına geçiş yapmayı öğrenin
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/29/2021
ms.author: aahi
ms.openlocfilehash: 1cc17306265e6e8ba2e7fb3f570d0017b006b84f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284694"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Read v3. x OCR kapsayıcılarına geçiş

Görüntü İşleme okuma OCR kapsayıcısının 2. sürümünü kullanıyorsanız, uygulamanızı kapsayıcının 3. x sürümünü kullanacak şekilde yükseltme hakkında bilgi edinmek için bu makaleyi kullanın. 


## <a name="configuration-changes"></a>Yapılandırma değişiklikleri

* `ReadEngineConfig:ResultExpirationPeriod` artık desteklenmiyor. Okuma OCR kapsayıcısının, 48 saat sonra bir istekle ilişkili sonuçları ve meta verileri kaldıran yerleşik bir cron işi vardır.
* `Cache:Redis:Configuration` artık desteklenmiyor. Önbellek v3. x kapsayıcılarında kullanılmaz, bu yüzden ayarlamanız gerekmez.

## <a name="api-changes"></a>API değişiklikleri

Read v 3.2 kapsayıcısı Görüntü İşleme API'si sürüm 3 ' ü kullanır ve aşağıdaki uç noktalara sahiptir:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Uygulamalarınızı bulut tabanlı okuma API 'sinin sürüm 3 ' ü kullanacak şekilde güncelleştirme hakkında ayrıntılı bilgi için bkz. [v3 REST API geçiş kılavuzu görüntü işleme](./upgrade-api-versions.md) . Bu bilgiler kapsayıcı için de geçerlidir. Eşitleme işlemlerinin yalnızca kapsayıcılar için desteklendiğini unutmayın.

## <a name="memory-requirements"></a>Bellek gereksinimleri

Gereksinimler ve öneriler, 29 satır ve toplam 803 karakter içeren taranmış bir iş harfinin 8 MB 'lik bir görüntüsünü kullanarak saniyede tek bir istek ile kıyaslamayı temel alır. Aşağıdaki tabloda her bir okuma OCR kapsayıcısı için kaynakların en düşük ve önerilen ayırması açıklanmaktadır.

|Kapsayıcı  |Minimum | Önerilen  |
|---------|---------|------|
|Okuma 3,2-Önizleme | 8 çekirdek, 16 GB bellek         | 8 çekirdek, 24 GB bellek |

Her çekirdek en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır.

Çekirdek ve bellek, `--cpus` `--memory` Docker Run komutunun bir parçası olarak kullanılan ve ayarlarına karşılık gelir.

## <a name="storage-implementations"></a>Depolama uygulamaları

>[!NOTE]
> MongoDB, kapsayıcının 3. x sürümünde artık desteklenmiyor. Bunun yerine kapsayıcılar, Azure depolama ve çevrimdışı dosya sistemlerini destekler.

| Uygulama |    Gerekli çalışma zamanı bağımsız değişkenleri |
|---------|---------|
|Dosya düzeyi (varsayılan)    | Çalışma zamanı bağımsız değişkeni gerekmez. `/share` Dizin kullanılacak. |
|Azure Blob    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Kuyruk uygulamaları

Kapsayıcının v3. x dosyasında, Kbbitmq Şu anda desteklenmiyor. Desteklenen destek uygulamaları şunlardır:

| Uygulama | Çalışma zamanı bağımsız değişkenleri | Amaçlanan kullanım |
|---------|---------|-------|
| Bellekte (varsayılan) | Çalışma zamanı bağımsız değişkeni gerekmez. | Geliştirme ve test |
| Azure Kuyrukları | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Üretim |
| RabbitMQ    | Kullanılamaz | Üretim |

Eklenen artıklık için Read v3. x kapsayıcısı, çok kapsayıcılı bir planda çalışırken isteklerin kilitlenme durumunda başarılı bir şekilde işlenebilmesi için görünürlük zamanlayıcısını kullanır. 

Zamanlayıcıyı, `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` başka bir çalışan tarafından işlendiği zaman bir iletinin görünmez olmasını ayarlayan ile birlikte ayarlayın. Sayfaların Redundantly işlenmesini önlemek için, zaman aşımı süresini 120 saniyeye ayarlamayı öneririz. Varsayılan değer 30 saniyedir.

| Varsayılan değer | Önerilen değer |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için [kapsayıcıları](computer-vision-resource-container-config.md) yapılandırmayı gözden geçir
* [OCR genel bakışını](overview-ocr.md) inceleyerek yazdırılmış ve el yazısı metin tanıma hakkında daha fazla bilgi edinin
* Kapsayıcı tarafından desteklenen yöntemler hakkındaki ayrıntılar için [okuma API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 'sine bakın.
* Görüntü İşleme işlevlerle ilgili sorunları çözmek için [sık sorulan sorular (SSS)](FAQ.md) bölümüne bakın.
* Daha fazla bilişsel [Hizmetler kapsayıcısı](../cognitive-services-container-support.md) kullanın