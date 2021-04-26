---
title: Azure PowerShell betik örneği-bir blob kapsayıcısının toplam fatura boyutunu hesaplama | Microsoft Docs
description: Azure Blob depolama alanındaki bir kapsayıcının toplam boyutunu Faturalandırma amacıyla hesaplayın.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/29/2020
ms.author: fryu
ms.openlocfilehash: dfc338844e310102447e2498ee9cce8f28a79b9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97809573"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Bir blob kapsayıcısının toplam fatura boyutunu hesaplama

Bu betik, Azure Blob depolama alanındaki bir kapsayıcının boyutunu faturalandırma maliyetlerini tahmin etmek amacıyla hesaplar. Betik, kapsayıcıdaki Blobların boyutunu toplar.

> [!IMPORTANT]
> Bu makalede belirtilen örnek betik, blob anlık görüntüleri için faturalandırma boyutunu doğru şekilde hesaplayamayabilir.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Bu PowerShell betiği, bir kapsayıcının boyutunu Faturalandırma amacıyla hesaplar. Başka amaçlar için kapsayıcı boyutunu hesapladıysanız, bkz. tahmin sağlayan daha basit bir betik için [bir BLOB depolama kapsayıcısının toplam boyutunu hesaplama](../scripts/storage-blobs-container-calculate-size-powershell.md) .

## <a name="determine-the-size-of-the-blob-container"></a>Blob kapsayıcısının boyutunu belirleme

Blob kapsayıcısının toplam boyutu kapsayıcının kendisinin boyutunu ve kapsayıcı altındaki tüm Blobların boyutunu içerir.

Aşağıdaki bölümlerde, depolama kapasitesinin blob kapsayıcıları ve BLOB 'lar için nasıl hesaplandığı açıklanmaktadır. Aşağıdaki bölümde, Len (X), dizedeki karakter sayısını gösterir.

### <a name="blob-containers"></a>Blob kapsayıcıları

Aşağıdaki hesaplama, blob kapsayıcısı başına tüketilen depolama miktarının nasıl tahmin edileceğini açıklar:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Döküm aşağıda verilmiştir:

* her kapsayıcı için 48 bayt ek yük, son değiştirilme zamanı, Izinler, genel ayarlar ve bazı sistem meta verileri içerir.

* Kapsayıcı adı Unicode olarak depolanır, bu nedenle karakterlerin sayısını ve çarpmasını ikiye ayırarak yapın.

* Depolanan BLOB kapsayıcısı meta verilerinin her bloğu için, adın uzunluğunu (ASCII) ve dize değerinin uzunluğunu depolarız.

* Imzalanan tanımlayıcı başına 512 bayt, imzalanmış tanımlayıcı adı, başlangıç zamanı, süre sonu zamanı ve izinleri içerir.

### <a name="blobs"></a>Bloblar

Aşağıdaki hesaplamalar, blob başına tüketilen depolama miktarının nasıl tahmin edilmesi gerektiğini gösterir.

* Blok Blobu (temel blob veya anlık görüntü):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Sayfa Blobu (temel blob veya anlık görüntü):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Döküm aşağıda verilmiştir:

* blob için 124 bayt ek yükü şunları içerir:
    - Son Değişiklik Zamanı
    - Boyut
    - Cache-Control
    - İçerik Türü
    - İçerik-dil
    - İçerik kodlama
    - İçerik-MD5
    - İzinler
    - Anlık görüntü bilgileri
    - Layamaz
    - Bazı sistem meta verileri

* Blob adı Unicode olarak depolanır, bu nedenle karakterlerin sayısını ve çarpmasını ikiye ayırarak yapın.

* Depolanan her meta veri bloğu için, adın uzunluğunu (ASCII olarak depolanır) ve dize değerinin uzunluğunu ekleyin.

* Blok Blobları için:
  * engelleme listesi için 8 bayt.
  * Blok KIMLIĞI boyutunun bayt olarak kaç blok sayısıdır.
  * Tüm işlenen ve işlenmemiş bloklarında verilerin boyutu.

    >[!NOTE]
    >Anlık görüntüler kullanıldığında, bu boyut yalnızca bu temel veya anlık görüntü blobu için benzersiz verileri içerir. İşlenmemiş bloklar bir hafta sonra kullanılmazsa, atık olarak toplanır. Bundan sonra, faturalandırmaya doğru sayılmaz.

* Sayfa Blobları için:
  * Ardışık olmayan sayfa aralıklarının, veri sürelerine 12 bayt olarak sayısı. Bu, **Getpageranges** API 'sini çağırırken gördüğünüz benzersiz sayfa aralıklarının sayısıdır.

  * Verilerin tüm depolanan sayfaların bayt cinsinden boyutu.

    >[!NOTE]
    >Anlık görüntüler kullanıldığında, bu boyut yalnızca temel Blobun veya sayılmakta olan anlık görüntü blobunun benzersiz sayfalarını içerir.

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. bir [BLOB depolama kapsayıcısının,](../scripts/storage-blobs-container-calculate-size-powershell.md) kapsayıcı boyutunun tahminini sağlayan basit bir betik için toplam boyutunu hesaplama.

- Azure depolama faturalandırma hakkında daha fazla bilgi için bkz. [Windows Azure Storage faturalandırmasını anlama](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).

- [Azure depolama Için PowerShell örneklerinde](../blobs/storage-samples-blobs-powershell.md)ek depolama PowerShell betiği örnekleri bulabilirsiniz.