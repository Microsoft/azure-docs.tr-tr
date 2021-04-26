---
title: Azure Data Lake Storage 2. 'de bulunan BLOB depolama özellikleri | Microsoft Docs
description: Azure Data Lake Storage 2. ile kullanabileceğiniz BLOB depolama özellikleri hakkında bilgi edinin
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 5c9eb1aa2e88019609f5ab26c2e995e4e43063ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640819"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. Nesil'de kullanılabilen Blob depolama özellikleri

[Tanılama günlüğü](../common/storage-analytics-logging.md), [erişim katmanları](storage-blob-storage-tiers.md)ve [BLOB depolama yaşam döngüsü yönetim ilkeleri](storage-lifecycle-management-concepts.md) gibi BLOB depolama özellikleri artık hiyerarşik bir ad alanı olan hesaplarla çalışır. Bu nedenle, bu özelliklere erişimi kaybetmeksizin BLOB depolama hesaplarınızda hiyerarşik ad alanlarını etkinleştirebilirsiniz.

Bu tablo, Azure Data Lake Storage 2. ile kullanabileceğiniz BLOB depolama özelliklerini listeler. Destek genişlemeye devam ettiğinden, Bu tablolarda görüntülenen öğeler zaman içinde değişir. Bir özelliğin önizleme durumuyla ilişkili belirli sorunlar hakkında daha fazla bilgi edinmek için, [bilinen sorunlar](data-lake-storage-known-issues.md) makalesine bakın.

## <a name="supported-blob-storage-features"></a>Desteklenen Blob depolama özellikleri

Aşağıdaki tabloda, her bir BLOB depolama özelliğinin Data Lake Storage 2. ile nasıl desteklendiği gösterilmektedir. Standart ve [Premium performans](premium-tier-for-data-lake-storage.md) katmanları için bir sütun vardır. 

|BLOB depolama özelliği |Standart |Premium |İlgili makaleler: |
|---------------|-------------------|---|
|Sık erişim katmanı|Genel kullanıma sunuldu|Desteklenmez|[Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md)|
|Seyrek erişim katmanı|Genel kullanıma sunuldu|Desteklenmez|[Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md)|
|Ekinlikler|Genel kullanıma sunuldu|Genel kullanıma sunuldu|[Blob depolama olaylarına yanıt verme](storage-blob-event-overview.md)|
|Ölçümler (klasik)|Genel kullanıma sunuldu|Genel kullanıma sunuldu|[Azure Storage Analytics ölçümleri (klasik)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure İzleyicisi'nde ölçümler|Genel kullanıma sunuldu|Önizleme|[Azure İzleyici’de Azure Depolama ölçümleri](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|BLOB depolama PowerShell komutları|Genel kullanıma sunuldu|Genel kullanıma sunuldu|[Hızlı başlangıç: PowerShell ile Blobları karşıya yükleme, indirme ve listeleme](storage-quickstart-blobs-powershell.md)|
|BLOB depolama Azure CLı komutları|Genel kullanıma sunuldu|Genel kullanıma sunuldu|[Hızlı başlangıç: Azure CLı ile Bloblar oluşturma, indirme ve listeleme](storage-quickstart-blobs-cli.md)|
|BLOB depolama API 'Leri|Genel kullanıma sunuldu|Genel kullanıma sunuldu|[Hızlı Başlangıç: .NET için Azure Blob depolama istemci kitaplığı v12](storage-quickstart-blobs-dotnet.md)<br>[Hızlı başlangıç: Java V12 SDK ile Blobları yönetme](storage-quickstart-blobs-java.md)<br>[Hızlı başlangıç: Python V12 SDK ile Blobları yönetme](storage-quickstart-blobs-python.md)<br>[Hızlı başlangıç: Node.jsiçindeki JavaScript V12 SDK ile Blobları yönetme ](storage-quickstart-blobs-nodejs.md)|
|Tanılama günlükleri|Genel kullanıma sunuldu|Önizleme |[Azure Depolama analizini günlüğe kaydetme](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Arşiv erişim katmanı|Genel kullanıma sunuldu|Desteklenmez|[Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md)|
|Yaşam döngüsü yönetimi ilkeleri (katmanlama)|Genel kullanıma sunuldu|Henüz desteklenmiyor|[Azure Blob depolama yaşam döngüsünü yönetme](storage-lifecycle-management-concepts.md)|
|Yaşam döngüsü yönetim ilkeleri (blobu Sil)|Genel kullanıma sunuldu|Genel kullanıma sunuldu|[Azure Blob depolama yaşam döngüsünü yönetme](storage-lifecycle-management-concepts.md)|
|Azure Izleyici 'de günlüğe kaydetme|Önizleme |Önizleme|[Azure Storage 'ı izleme](./monitor-blob-storage.md)|
|Anlık Görüntüler|Önizleme<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Önizleme<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Blob anlık görüntüleri](snapshots-overview.md)|
|Statik web siteleri|Önizleme<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Önizleme<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Azure Depolama'da statik web sitesi barındırma](storage-blob-static-website.md)|
|Sabit depolama|Önizleme<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Önizleme<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[İş açısından kritik blob verilerini sabit depolama alanıyla depolama](storage-blob-immutable-storage.md)|
|Kapsayıcı geçici silme|Önizleme|Önizleme|[Kapsayıcılar için geçici silme (Önizleme)](soft-delete-container-overview.md)|
|Azure depolama envanteri|Önizleme|Önizleme|[Blob verilerini yönetmek için Azure depolama envanterini kullanma (Önizleme)](blob-inventory.md)|
|Özel etki alanları|Önizleme<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|Önizleme<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[Özel bir etki alanını Azure Blob depolama uç noktasıyla eşleme](storage-custom-domain-name.md)|
|Blob geçici silme|Henüz desteklenmiyor|Henüz desteklenmiyor|[Bloblar için geçici silme](./soft-delete-blob-overview.md)|
|Blobsigortası|Genel kullanıma sunuldu|Genel kullanıma sunuldu|[Blob depolamayı blobsigortası ile dosya sistemi olarak bağlama](storage-how-to-mount-container-linux.md)|
|Anonim genel erişim |Genel kullanıma sunuldu|Genel kullanıma sunuldu| Bkz. [kapsayıcılar ve Bloblar için anonim genel okuma erişimini yapılandırma](anonymous-read-access-configure.md).|
|Müşteri tarafından yönetilen hesap yük devretmesi|Henüz desteklenmiyor|Henüz desteklenmiyor|[Olağanüstü durum kurtarma ve hesap yük devretme](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Müşteri tarafından sunulan anahtarlar|Henüz desteklenmiyor|Henüz desteklenmiyor|[BLOB depolama için istekte bir şifreleme anahtarı sağlayın](encryption-customer-provided-keys.md)|
|Şifreleme kapsamları|Henüz desteklenmiyor|Henüz desteklenmiyor|[Şifreleme kapsamları oluşturma ve yönetme](encryption-scope-manage.md)|
|Akışı değiştirme|Henüz desteklenmiyor|Henüz desteklenmiyor|[Azure Blob depolamada akış desteğini değiştirme](storage-blob-change-feed.md)|
|Nesne çoğaltma|Henüz desteklenmiyor|Henüz desteklenmiyor|[Blok Blobları için nesne çoğaltmasını yapılandırma](object-replication-configure.md)|
|Blob sürümü oluşturma|Henüz desteklenmiyor|Henüz desteklenmiyor|[Blob sürüm oluşturmayı etkinleştirme ve yönetme](versioning-enable.md)|

<div id="preview-form"><sup>1</sup> Data Lake Storage 2. ile anlık görüntüleri, sabit depolamayı veya statik Web sitelerini kullanmak için bu <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formu</a>tamamlayarak önizlemeye kaydetmeniz gerekir.  </div>
<div id="preview-form-2"><sup>2</sup> Özel bir etki alanı adı yalnızca blob hizmeti veya statik Web sitesi uç noktası ile eşlenir. Data Lake depolama uç noktası desteklenmiyor. </a> .  </div>

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Data Lake Storage 2. ile ilgili bilinen sorunlar](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage 2. destekleyen Azure hizmetleri](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage 2. destekleyen açık kaynaklı platformlar](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage çoklu protokol erişimi](data-lake-storage-multi-protocol-access.md)