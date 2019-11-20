---
title: Azure Data Lake Storage çoklu protokol erişimi | Microsoft Docs
description: Blob API 'Leri ve Azure Data Lake Storage 2. blob API 'Leri kullanan uygulamaları kullanın.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3470e27f64a672db0165b0132996db91a1447c08
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033975"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage çoklu protokol erişimi

Blob API 'Leri artık hiyerarşik bir ad alanı olan hesaplarla çalışır. Bu, araçların, uygulamaların ve hizmetlerin ekosisteminin yanı sıra hiyerarşik bir ad alanına sahip hesaplara birçok BLOB depolama özelliği de sunar.

Yakın zamanda, nesne depolama ve analiz depolaması için ayrı depolama çözümlerini korumanız gerekiyordu. Çünkü Azure Data Lake Storage 2., ekosistem desteğinin sınırlı olması gerekiyordu. Ayrıca, tanılama günlüğü gibi BLOB hizmeti özelliklerine sınırlı erişimi vardır. Çeşitli senaryolar gerçekleştirmek için verileri hesaplar arasında taşımanız gerektiğinden parçalanmış bir depolama çözümü devam etmek zordur. Artık bunu yapmanız gerekmez.

Data Lake Storage çoklu protokol erişimi sayesinde, Araçlar, uygulamalar ve hizmetler ekosistemini kullanarak verilerinize çalışabilirsiniz. Bu ayrıca üçüncü taraf araçları ve uygulamaları da içerir. Bunları değiştirmek zorunda kalmadan hiyerarşik bir ad alanına sahip hesaplara işaret edebilirsiniz. Blob API 'Leri artık hiyerarşik bir ad alanına sahip hesaplarda veri üzerinde çalışabildiklerinden, bu uygulamalar blob API 'Lerini çağırsalar *bile çalışır* .

[Tanılama günlüğü](../common/storage-analytics-logging.md), [erişim katmanları](storage-blob-storage-tiers.md)ve [BLOB depolama yaşam döngüsü yönetim ilkeleri](storage-lifecycle-management-concepts.md) gibi BLOB depolama özellikleri artık hiyerarşik bir ad alanı olan hesaplarla çalışır. Bu nedenle, bu önemli özelliklere erişimi kaybetmeksizin BLOB depolama hesaplarınızda hiyerarşik ad alanlarını etkinleştirebilirsiniz. 

> [!NOTE]
> Data Lake Storage çoklu protokol erişimi genel kullanıma sunulmuştur ve tüm bölgelerde kullanılabilir. Çoklu protokol erişimi tarafından etkinleştirilen bazı Azure hizmetleri veya blob depolama özellikleri önizlemede kalır. Daha fazla bilgi için bu makalenin her bölümündeki tablolara bakın. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data Lake Storage 'da çoklu protokol erişimi nasıl kullanılır?

Blob API 'Leri ve Data Lake Storage 2. API 'Leri, hiyerarşik bir ad alanına sahip depolama hesaplarında aynı verilerde çalışabilir. Data Lake Storage 2., birinci sınıf dizin işlemlerinin ve POSIX ile uyumlu erişim denetim listelerinin (ACL 'Ler) avantajlarından yararlanmak için, blob API 'Lerini hiyerarşik ad alanı aracılığıyla yönlendirir. 

![Kavramsal Data Lake Storage çoklu protokol erişimi](./media/data-lake-storage-interop/interop-concept.png) 

Blob API kullanan mevcut araçlar ve uygulamalar bu avantajları otomatik olarak elde edebilir. Geliştiricilerin onları değiştirmesi gerekmez. Data Lake Storage 2., Araçlar ve uygulamaların verilere erişmek için kullandığı Protokolden bağımsız olarak dizin ve dosya düzeyi ACL 'Leri tutarlı bir şekilde uygular. 

## <a name="blob-storage-feature-support"></a>BLOB depolama özelliği desteği

Data Lake Storage çoklu protokol erişimi, Data Lake Storage daha fazla BLOB depolama özelliği kullanmanıza olanak sağlar. Bu tabloda, Data Lake Storage üzerinde çoklu protokol erişimi tarafından etkinleştirilen özellikler listelenmiştir. 

BLOB depolama özellikleri için destek genişlemeye devam ettiğinden, bu tabloda görünen öğeler zamanla değişecektir. 

> [!NOTE]
> Data Lake Storage çoklu protokol erişimi genel kullanıma sunuldu olsa da, bu özelliklerden bazıları için destek önizlemede kalır. 

|BLOB depolama özelliği | Destek düzeyi |
|---|---|
|[Seyrek Erişimli erişim katmanı](storage-blob-storage-tiers.md)|Genel kullanıma sunuldu|
|Blob SDK 'Ları |Genel kullanıma sunuldu|
|Blob REST API 'Leri|Genel olarak kullanılabilir|
|[PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Genel kullanıma sunuldu|
|[CLI](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Genel kullanıma sunuldu|
|[Tanılama günlükleri](../common/storage-analytics-logging.md)| Önizleme|
|[Yaşam döngüsü yönetim ilkeleri](storage-lifecycle-management-concepts.md)| Önizleme|
|[Azure Event Grid aracılığıyla bildirimler](data-lake-storage-events.md)|Önizleme|
|[Arşiv erişim katmanı](storage-blob-storage-tiers.md)| Önizleme|
|[blobsigortası](storage-how-to-mount-container-linux.md)|Henüz desteklenmiyor|
|[Sabit depolama](storage-blob-immutable-storage.md)|Henüz desteklenmiyor|
|[Görüntüsünü](storage-blob-snapshots.md)|Henüz desteklenmiyor|
|[Geçici silme](storage-blob-soft-delete.md)|Henüz desteklenmiyor|
|[Statik Web siteleri](storage-blob-static-website.md)|Henüz desteklenmiyor|

Azure Data Lake Storage 2. ile ilgili genel bilinen sorunlar ve sınırlamalar hakkında daha fazla bilgi için bkz. [bilinen sorunlar](data-lake-storage-known-issues.md).

## <a name="azure-ecosystem-support"></a>Azure ekosistemi desteği

Data Lake Storage çoklu protokol erişimi ayrıca, Data Lake Storage daha fazla Azure hizmetine bağlanmanızı sağlar. Bu tabloda, Data Lake Storage üzerinde çoklu protokol erişimi tarafından etkinleştirilen hizmetler listelenir. 

Desteklenen BLOB depolama özelliklerinin listesi gibi, bu tabloda görünen öğeler, Azure hizmetleri için destek genişlemeye devam ettiği için zaman içinde değişir. 

> [!NOTE]
> Data Lake Storage çoklu protokol erişimi genel kullanıma açık olsa da, bu hizmetlerden bazılarının desteği önizlemede kalır. 

|Azure hizmeti | Destek düzeyi |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Genel kullanıma sunuldu|
|[Azure Event Hubs yakalama](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Genel kullanıma sunuldu|
|[Azure Akış Analizi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Genel kullanıma sunuldu|
|[IoT Hub’ı](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Genel kullanıma sunuldu|
|[Logic Apps](https://azure.microsoft.com/services/logic-apps/)|Genel kullanıma sunuldu|
|[Azure Bilişsel Arama](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|Önizleme|

Data Lake Storage 2. için Azure ekosistem desteğinin tüm listesi için bkz. [Azure hizmetleriyle Azure Data Lake Storage tümleştirme](data-lake-storage-integrate-with-azure-services.md).

Azure Data Lake Storage 2. ile ilgili genel bilinen sorunlar ve sınırlamalar hakkında daha fazla bilgi için bkz. [bilinen sorunlar](data-lake-storage-known-issues.md).

## <a name="next-steps"></a>Sonraki adımlar

[Bilinen sorunlara](data-lake-storage-known-issues.md) bakın




