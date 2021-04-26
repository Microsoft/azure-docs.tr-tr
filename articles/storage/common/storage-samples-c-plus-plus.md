---
title: C++ kullanan Azure depolama örnekleri | Microsoft Docs
description: Azure depolama için örnek kod ve uygulamaları görüntüleyin, indirin ve çalıştırın. C++ depolama istemci kitaplıklarını kullanarak Bloblar, kuyruklar, tablolar ve dosyalar için çalışmaya başlama örneklerini bulun.
author: twooley
ms.author: twooley
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: d82b31cde0e8df5db2d073abcec8ea44f13bd0f0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276682"
---
# <a name="azure-storage-samples-using-v12-c-client-libraries"></a>V12 C++ istemci kitaplıklarını kullanan Azure depolama örnekleri

Aşağıdaki tabloda, örnek havuzumuza ve her örnekte ele alınan senaryolara bir genel bakış sunulmaktadır. GitHub 'da karşılık gelen örnek kodu görüntülemek için bağlantılara tıklayın.

> [!NOTE]
> Bu örnekler en son Azure Storage C++ V12 kitaplığını kullanır.

## <a name="blob-samples"></a>Blob örnekleri

:::row:::
   :::column:::
        [Bağlantı dizesi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L18)
   :::column-end:::
   :::column:::
        [Blob kapsayıcısı oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L20)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Blob istemcisi al](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Blobu karşıya yükleme](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L32)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Blob üzerinde meta verileri ayarlama](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L34)
   :::column-end:::
   :::column:::
        [Blob özelliklerini al](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
        [Blobu indirme](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L44)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage 2. örnekleri

:::row:::
   :::column:::
        [Bağlantı dizesi kullanarak bir hizmet istemcisi oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L22)
   :::column-end:::
   :::column:::
        [Bağlantı dizesi kullanarak bir dosya sistemi istemcisi oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L25)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Dosya sistemi oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Dizin oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Dosya oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L52)
   :::column-end:::
   :::column:::
        [Verileri bir dosyaya ekle](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Dosya verilerini boşalt](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L77)
   :::column-end:::
   :::column:::
        [Dosya okuma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Tüm dosya sistemlerini Listele](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L88)
   :::column-end:::
   :::column:::
        [Dosya sistemini Sil](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L102)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure dosyaları örnekleri

:::row:::
    :::column:::
        [Bağlantı dizesi kullanarak bir Share Client oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L18)
    :::column-end:::
    :::column:::
        [Dosya paylaşımı oluşturma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L21)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Bir dosya istemcisi al](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L29)
    :::column-end:::
    :::column:::
        [Dosyayı karşıya yükleme](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L31)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [Dosya üzerinde meta verileri ayarlama](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L33)
    :::column-end:::
    :::column:::
        [Dosya özelliklerini alma](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L36)
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        [Dosya indirme](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L43)
    :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure Code örnek kitaplıkları

C++ örnek kitaplıklarının tamamını görüntülemek için şuraya gidin:

* [Azure Blob kodu örnekleri](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
* [Azure Data Lake kodu örnekleri](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-datalake/sample)
* [Azure dosyaları kod örnekleri](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-shares/sample)

Her kitaplık için GitHub deposuna gözatıp kopyalayabilirsiniz.

## <a name="getting-started-guides"></a>Başlangıç kılavuzları

Azure Storage Istemci kitaplıklarını yüklemek ve kullanmaya başlamak için yönergeler arıyorsanız aşağıdaki kılavuzlara göz atın.

* [Hızlı başlangıç: Azure Blob depolama kitaplığı V12-C++](../blobs/quickstart-blobs-c-plus-plus.md)

## <a name="next-steps"></a>Sonraki adımlar

Diğer dillere yönelik örnekler hakkında bilgi için:

* .NET: [.NET kullanan Azure depolama örnekleri](storage-samples-dotnet.md)
* Java: [Java kullanan Azure depolama örnekleri](storage-samples-java.md)
* Python: [Python kullanan Azure depolama örnekleri](storage-samples-python.md)
* JavaScript/Node.js: [JavaScript kullanan Azure depolama örnekleri](storage-samples-javascript.md)
* Diğer tüm diller: [Azure Storage örnekleri](storage-samples.md)
