---
title: Azure Data Lake depolama Gen2 ile'ilgili bilinen sorunlar | Microsoft Docs
description: Azure Data Lake depolama Gen2 ile'ilgili bilinen sorunlar ve sınırlamalar hakkında bilgi edinin
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 78693dceaac119279b1c1d06a6c3a18cc4fdb485
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033933"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake depolama Gen2 ile'ilgili bilinen sorunlar

Bu makalede henüz desteklenmeyen veya hiyerarşik ad alanı (Azure Data Lake Storage 2.) olan depolama hesaplarıyla kısmen desteklenen özellikler ve araçlar listelenmektedir.

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Blob API 'Leri kullanma ile ilgili sorunlar ve sınırlamalar

Blob API 'Leri ve Data Lake Storage 2. API 'Leri aynı verilerde çalışabilir.

Bu bölümde, aynı verilerde çalışacak blob API 'Leri ve Data Lake Storage 2. API 'Leri kullanımıyla ilgili sorunlar ve sınırlamalar açıklanmaktadır.

* Aynı dosyanın aynı örneğine yazmak için hem blob API 'Leri hem de Data Lake Storage API 'Leri kullanamazsınız. Data Lake Storage 2. API 'Leri kullanarak bir dosyaya yazarsanız, bu dosyanın blokları [Get Block list](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API 'sine yapılan çağrılara görünmez. Data Lake Storage 2. API 'leri ya da blob API 'Lerini kullanarak bir dosyanın üzerine yazabilirsiniz. Bu dosya özelliklerini etkilemez.

* Bir sınırlayıcı belirtmeden [Blobları Listele](https://docs.microsoft.com/rest/api/storageservices/list-blobs) işlemini kullandığınızda, sonuçlar hem dizinleri hem de Blobları içerecektir. Bir sınırlayıcı kullanmayı seçerseniz yalnızca eğik çizgi kullanın (`/`). Bu, desteklenen tek sınırlayıcıdır.

* Bir dizini silmek için [blobu silme](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 'sini kullanırsanız, bu dizin yalnızca boşsa silinir. Bu, blob API 'SI silme dizinlerini yinelemeli olarak kullanamayacağı anlamına gelir.

Bu blob REST API 'Leri desteklenmez:

* [Blobu Yerleştir (sayfa)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Yerleştirme sayfası](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Sayfa aralıklarını al](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Artımlı kopya blobu](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [URL 'den sayfa koy](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Blobu Yerleştir (Ekle)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Ekleme bloğu](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [URL 'den ekleme bloğu](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Yönetilmeyen VM diskleri hiyerarşik bir ad alanına sahip hesaplarda desteklenmez. Bir depolama hesabında hiyerarşik bir ad alanı etkinleştirmek istiyorsanız, yönetilmeyen VM disklerini hiyerarşik ad alanı özelliği etkin olmayan bir depolama hesabına yerleştirin.

## <a name="support-for-other-blob-storage-features"></a>Diğer BLOB depolama özellikleri için destek

Aşağıdaki tabloda henüz desteklenmeyen veya hiyerarşik ad alanı (Azure Data Lake Storage 2.) olan depolama hesaplarıyla kısmen desteklenen tüm diğer özellikler ve araçlar listelenmektedir.

| Özellik/Araç    | Daha fazla bilgi    |
|--------|-----------|
| **Data Lake Storage 2. API 'Leri** | Kısmen destekleniyor <br><br>Geçerli sürümde, dizinler ile etkileşim kurmak ve erişim denetim listeleri (ACL 'Ler) ayarlamak için Data Lake Storage 2. **rest** API 'leri kullanabilirsiniz, ancak bu görevleri gerçekleştirmek Için başka SDK 'lar (örneğin, .net, Java veya Python) yoktur. Dosya yükleme ve indirme gibi diğer görevleri gerçekleştirmek için blob SDK 'larını kullanabilirsiniz.  |
| **AzCopy** | Sürüme özgü destek <br><br>AzCopy 'in yalnızca en son sürümünü kullanın ([AzCopy ile v10 arasındaki](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). AzCopy v 8.1 gibi önceki AzCopy sürümleri desteklenmez.|
| **Azure Blob depolama yaşam döngüsü yönetim ilkeleri** | Tüm erişim katmanları desteklenir. Arşiv erişim katmanı Şu anda önizleme aşamasındadır. Blob anlık görüntülerini silme henüz desteklenmiyor. |
| **Azure Content Delivery Network (CDN)** | Henüz desteklenmiyor|
| **Azure Arama** |Desteklenir (Önizleme)|
| **Azure Depolama Gezgini** | Sürüme özgü destek <br><br>Yalnızca `1.10.0``1.6.0` sürümleri kullanın. <br> Sürüm `1.10.0` [ücretsiz bir indirme](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)olarak sunulmaktadır. Sürüm `1.11.0` henüz desteklenmiyor.|
| **Blob kapsayıcı ACL 'Leri** |Henüz desteklenmiyor|
| **Blobsigortası** |Henüz desteklenmiyor|
| **Özel etki alanları** |Henüz desteklenmiyor|
| **Azure portal Depolama Gezgini** | Sınırlı destek. ACL 'Ler henüz desteklenmiyor. |
| **Tanılama günlüğüne kaydetme** |Tanılama günlükleri desteklenir (Önizleme).<br><br>Azure portal günlüklerin etkinleştirilmesi Şu anda desteklenmiyor. Günlükleri PowerShell kullanarak nasıl etkinleştireceğinizi gösteren bir örnek aşağıda verilmiştir. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Bu örnekte gösterildiği gibi, `-ServiceType` parametresinin değeri olarak `Blob` belirttiğinizden emin olun. <br><br>Şu anda Azure Depolama Gezgini tanılama günlüklerini görüntülemek için kullanılamaz. Günlükleri görüntülemek için lütfen AzCopy veya SDK 'Ları kullanın.
| **Sabit depolama** |Henüz desteklenmiyor <br><br>Sabit depolama, verileri bir solucan içinde depolamanıza olanak tanır [(bir kez yaz, çok oku)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) durumu sağlar.|
| **Nesne düzeyi katmanları** |Seyrek erişimli ve arşiv katmanları desteklenir. Arşiv katmanı önizlemededir. Diğer tüm erişim katmanları henüz desteklenmiyor.|
| **PowerShell ve CLı desteği** | Sınırlı işlevsellik <br><br>Blob işlemleri desteklenir. Dizinlerle çalışma ve erişim denetim listelerini (ACL 'Ler) ayarlama henüz desteklenmiyor. |
| **Statik Web siteleri** |Henüz desteklenmiyor <br><br>Özellikle, dosyaları [statik Web sitelerine](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)sunma özelliği.|
| **Üçüncü taraf uygulamalar** | Sınırlı destek <br><br>REST API 'Leri kullanan üçüncü taraf uygulamalar, Data Lake Storage 2. ile birlikte kullandığınızda çalışmaya devam edecektir. <br>Blob API 'Leri çağıran uygulamalar muhtemelen işe sunulacaktır.|
|**Geçici silme** |Henüz desteklenmiyor|
| **Sürüm oluşturma özellikleri** |Henüz desteklenmiyor <br><br>Bu, [geçici silme](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)ve [anlık görüntüler](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)gibi diğer sürüm oluşturma özelliklerini içerir.|


