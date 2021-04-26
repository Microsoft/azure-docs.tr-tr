---
title: .NET kullanarak Azure Storage örnekleri | Microsoft Docs
description: Azure depolama için örnek kod ve uygulamaları görüntüleyin, indirin ve çalıştırın. .NET depolama istemci kitaplıklarını kullanarak blob, kuyruk, tablo ve dosya kullanmaya başlama örneklerini bulun.
author: twooley
ms.author: twooley
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: c140234ce93ff9146ef3b1a5a135258ad707c807
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275711"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>V12 .NET istemci kitaplıklarını kullanan Azure depolama örnekleri

Aşağıdaki tabloda, örnek havuzumuza ve her örnekte ele alınan senaryolara bir genel bakış sunulmaktadır. GitHub 'da karşılık gelen örnek kodu görüntülemek için bağlantılara tıklayın.

> [!NOTE]
> Bu örnekler en son Azure Storage .NET V12 kitaplığını kullanır. Eski v11 kodu için bkz. GitHub deposunda [.net Için Azure Blob depolama örnekleri](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started) .

## <a name="blob-samples"></a>Blob örnekleri

### <a name="authentication"></a>Kimlik Doğrulaması

:::row:::
   :::column span="":::
      [Bağlantı dizesi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Paylaşılan anahtar kimlik bilgisi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Azure kimliğiyle kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Active Directory belirteci kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Genel bir blob 'a anonim erişim](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Toplu İşleme

:::row:::
   :::column span="":::
      [Tek bir istekte birkaç blob silme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Bir istekte birkaç blob erişim katmanı ayarlama](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Toplu istekte ayrıntılı denetim](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Başarısız bir alt işlemden oluşan hataları yakala](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Blob 'a dosya yükleme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Bir dosyaya blob indirme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Görüntü indirme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Bir kapsayıcıdaki tüm Blobları listeleme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Sorun giderme
:::row:::
   :::column span="2":::
      [Kapsayıcı istemci kullanarak kurtarılabilir bir hata tetikleyin](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage 2. örnekleri

### <a name="authentication"></a>Kimlik Doğrulaması

:::row:::
   :::column span="":::
      [Ortak bir dosyaya anonim erişim](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Paylaşılan anahtar kimlik bilgisi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Paylaşılan erişim imzasını (SAS) kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Active Directory belirteci kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Dosya sistemi
:::row:::
   :::column span="":::
      [Dosya sistemi istemcisi kullanarak dosya oluşturma](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Dosya ve dizin üzerinde Özellikler alın](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Bir dosyayı ve dizini yeniden adlandırma](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Dizin

:::row:::
   :::column span="":::
      [Dizin oluşturma](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [Dizin istemcisi kullanarak bir dosya oluşturma](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Dizinleri Listele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Dosya ve dizinleri gez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Dosya
:::row:::
   :::column span="":::
      [Dosyayı karşıya yükleme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Dosyaya ekleyerek karşıya yükle](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Dosya indirme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Dosya erişim denetim listesi ayarlama ve edinme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Bir dosyanın izinlerini ayarlama ve al](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Sorun giderme

:::row:::
   :::column span="2":::
      [Kurtarılabilir bir hata tetikleyin](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure dosyaları örnekleri

### <a name="authentication"></a>Kimlik Doğrulaması

:::row:::
   :::column span="":::
      [Bağlantı dizesi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Paylaşılan anahtar kimlik bilgisi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Paylaşılan erişim imzasını (SAS) kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Dosya paylaşımları

:::row:::
   :::column span="":::
      [Bir paylaşma oluşturun ve bir dosyayı karşıya yükleyin](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Dosya indirme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Dosya ve dizinleri gez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Sorun giderme

:::row:::
   :::column span="2":::
      [Share Client kullanarak kurtarılabilir bir hata tetikleyin](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Kuyruk örnekleri

### <a name="authentication"></a>Kimlik Doğrulaması

:::row:::
   :::column span="":::
      [Azure Active Directory'yi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Bağlantı dizesi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Paylaşılan anahtar kimlik bilgisi kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Paylaşılan erişim imzasını (SAS) kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Active Directory belirteci kullanarak kimlik doğrulama](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Kuyruk

:::row:::
   :::column span="2":::
      [Sıra oluşturma ve ileti ekleme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>İleti

:::row:::
   :::column span="":::
      [İletileri alma ve işleme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [İletilere göz atın](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [İletileri al ve görünürlük zaman aşımını Güncelleştir](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Sorun giderme 
:::row:::
   :::column span="2":::
      [Kuyruk istemcisini kullanarak kurtarılabilir bir hata tetikleyin](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Tablo örnekleri (v11)

:::row:::
   :::column span="":::
      [Tablo oluştur](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [Varlığı/tabloyu Sil](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Varlığı Ekle/Birleştir/Değiştir](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Sorgu varlıkları](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Sorgu tabloları](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Tablo ACL 'SI/özellikleri](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Varlığı Güncelleştir](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure Code örnek kitaplıkları

.NET örnek kitaplıklarının tamamını görüntülemek için şuraya gidin:

* [Azure Blob kodu örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
* [Azure Data Lake kodu örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
* [Azure dosyaları kod örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
* [Azure kuyruk kodu örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

Her kitaplık için GitHub deposuna gözatıp kopyalayabilirsiniz.

## <a name="getting-started-guides"></a>Başlangıç kılavuzları

Azure Storage Istemci kitaplıklarını yüklemek ve kullanmaya başlamak için yönergeler arıyorsanız aşağıdaki kılavuzlara göz atın.

* [.NET ' te Azure Blob hizmetini kullanmaya başlama](../blobs/storage-quickstart-blobs-dotnet.md)
* [.NET ' te Azure kuyruk hizmeti ile çalışmaya başlama](../queues/storage-quickstart-queues-dotnet.md)
* [.NET ' te Azure Tablo hizmeti ile çalışmaya başlama](../../cosmos-db/tutorial-develop-table-dotnet.md)
* [.NET ' te Azure dosya hizmeti ile çalışmaya başlama](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Sonraki adımlar

Diğer dillere yönelik örnekler hakkında bilgi için:

* Java: [Java kullanan Azure depolama örnekleri](storage-samples-java.md)
* Python: [Python kullanan Azure depolama örnekleri](storage-samples-python.md)
* JavaScript/Node.js: [JavaScript kullanan Azure depolama örnekleri](storage-samples-javascript.md)
* C++: [c++ kullanan Azure depolama örnekleri](storage-samples-c-plus-plus.md)
* Diğer tüm diller: [Azure Storage örnekleri](storage-samples.md)
