---
title: .NET kullanarak Azure Storage örnekleri | Microsoft Docs
description: Azure depolama için örnek kod ve uygulamaları görüntüleyin, indirin ve çalıştırın. .NET depolama istemci kitaplıklarını kullanarak blob, kuyruk, tablo ve dosya kullanmaya başlama örneklerini bulun.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 9d6e441adeb7025e09b8d73764d2defab439dd90
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350911"
---
# <a name="azure-storage-samples-using-net"></a>.NET kullanan Azure depolama örnekleri

Aşağıdaki tabloda, örnek havuzumuza ve her örnekte ele alınan senaryolara bir genel bakış sunulmaktadır. GitHub 'da karşılık gelen örnek kodu görüntülemek için bağlantılara tıklayın.

## <a name="blob-samples"></a>Blob örnekleri

| **Senaryo** | **Örnek kod** |
|--------------|-----------------|
| Ekleme Blobu | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| Blok Blobu | [Azure Blob depolama Fotoğraf Galerisi Web uygulaması](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| İstemci Tarafında Şifreleme | [Blob şifreleme örnekleri](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Kopya blob'u | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Kapsayıcı oluşturma | [Azure Blob depolama Fotoğraf Galerisi Web uygulaması](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| BLOB silme | [Azure Blob depolama Fotoğraf Galerisi Web uygulaması](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Kapsayıcıyı Sil | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Blob meta verileri/özellikleri/Istatistikleri | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Kapsayıcı ACL/meta veriler/Özellikler | [Azure Blob depolama Fotoğraf Galerisi Web uygulaması](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Sayfa aralıklarını al | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Kira blobu/kapsayıcı | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Blob/kapsayıcı Listele | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| Sayfa Blobu | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| 'LARININ | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Hizmet Özellikleri | [Bloblarla çalışmaya başlama](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Anlık görüntü blobu | [Artımlı anlık görüntülerle Azure sanal makine disklerini yedekleme](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples"></a>Dosya örnekleri

| **Senaryo** | **Örnek kod** |
|--------------|-----------------|
| Paylaşımlar/dizinler/dosyalar oluşturma | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Paylaşımları/dizinleri/dosyaları sil | [.NET ' te Azure dosya hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| Dizin özellikleri/meta veriler | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Dosyaları indir | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Dosya özellikleri/meta veriler/ölçümler | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Dosya hizmeti özellikleri | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Dizinleri ve dosyaları listeleme | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| Paylaşımları Listele | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| Özellikleri/meta verileri/Istatistikleri paylaşma | [Azure Storage .NET dosya depolama örneği](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>Kuyruk örnekleri

| **Senaryo** | **Örnek kod** |
|--------------|-----------------|
| Ileti Ekle | [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| İstemci Tarafında Şifreleme | [Azure Storage .NET kuyruğu Istemci tarafı şifrelemesi](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| Kuyruklar Oluşturma | [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Iletiyi/kuyruğu Sil | [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Iletiye Gözat | [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| Kuyruk ACL/Metadata/stats | [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Kuyruk hizmeti özellikleri | [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| Güncelleştirme Iletisi | [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples"></a>Tablo örnekleri

| **Senaryo** | **Örnek kod** |
|--------------|-----------------|
| Tablo Oluştur | [Azure depolama kullanarak eşzamanlılık yönetme-örnek uygulama](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Varlığı/tabloyu Sil | [.NET’te Azure Tablo Depolama Kullanmaya Başlama](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Varlığı Ekle/Birleştir/Değiştir | [Azure depolama kullanarak eşzamanlılık yönetme-örnek uygulama](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| Sorgu varlıkları | [.NET’te Azure Tablo Depolama Kullanmaya Başlama](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Sorgu tabloları | [.NET’te Azure Tablo Depolama Kullanmaya Başlama](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| Tablo ACL 'SI/özellikleri | [.NET’te Azure Tablo Depolama Kullanmaya Başlama](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| Varlığı Güncelleştir | [Azure depolama kullanarak eşzamanlılık yönetme-örnek uygulama](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Azure kod örnekleri kitaplığı

Tüm örnek kitaplığı görüntülemek için, yerel olarak indirebileceğiniz ve çalıştırabileceğiniz Azure Storage örneklerini içeren [Azure kod örnekleri](https://azure.microsoft.com/resources/samples/?service=storage) kitaplığına gidin. Kod örneği kitaplığı,. zip biçiminde örnek kod sağlar. Alternatif olarak, her bir örnek için GitHub deposuna gözatıp kopyalayabilirsiniz.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Başlarken kılavuzlarını

Azure Storage Istemci kitaplıklarını yüklemek ve kullanmaya başlamak için yönergeler arıyorsanız aşağıdaki kılavuzlara göz atın.

* [.NET ' te Azure Blob hizmetini kullanmaya başlama](../blobs/storage-dotnet-how-to-use-blobs.md)
* [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](../storage-dotnet-how-to-use-queues.md)
* [.NET ' te Azure Tablo hizmeti ile çalışmaya başlama](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET ' te Azure dosya hizmeti ile çalışmaya başlama](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Sonraki adımlar

Diğer dillere yönelik örnekler hakkında bilgi için:

* Java: [Java kullanan Azure Depolama örnekleri](storage-samples-java.md)
* JavaScript/Node. js: [JavaScript kullanan Azure depolama örnekleri](storage-samples-javascript.md)
* Python: [Python kullanarak Azure Storage örnekleri](storage-samples-python.md)
* Diğer tüm diller: [Azure depolama örnekleri](../storage-samples.md)
