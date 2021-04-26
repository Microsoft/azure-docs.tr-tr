---
title: Azure Data Box sınırları | Microsoft Docs
description: Microsoft Azure Data Box bileşenleri ve bağlantıları için sistem sınırlarını ve önerilen boyutları açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 97d8da86565db73aa9a3866f39f793aaf0905470
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900170"
---
# <a name="azure-data-box-limits"></a>Azure Data Box sınırları

Microsoft Azure Data Box dağıtıp işletirken bu limitleri göz önünde bulundurun. Aşağıdaki tabloda Data Box için bu sınırlar açıklanmaktadır.

## <a name="data-box-service-limits"></a>Hizmet sınırlarını Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box sınırları

- Data Box, hem içeri hem de dışarı aktarma için en fazla 500.000.000 dosya saklayabilir.
- Data Box buluttaki en fazla 512 kapsayıcıyı veya paylaşımı destekler. Kullanıcı paylaşımı içindeki en üst düzey dizinler, bulutta kapsayıcılar veya Azure dosya paylaşımları haline gelir. 
- Data Box kullanım kapasitesi, ReFS meta veri alanı tüketimine göre 80 TB 'den az olabilir.
- Data Box, bir NFS paylaşımında en fazla 10 istemci bağlantısını destekler.

## <a name="azure-storage-limits"></a>Azure depolama sınırları

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Veri yükleme uyarıları


### <a name="for-import-order"></a>İçeri aktarma sırası için

İçeri aktarma sırası için uyarıları Data Box şunlar vardır:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>Dışa aktarma sırası için

Bir dışarı aktarma siparişi için Data Box uyarılar şunları içerir:

- Data Box, Windows tabanlı bir aygıttır ve büyük/küçük harfe duyarlı dosya adlarını desteklemez. Örneğin, Azure 'da yalnızca büyük küçük harf bakımından farklı adlara sahip iki farklı dosya olabilir. Aygıtta dosyanın üzerine yazılacak şekilde, bu tür dosyaları dışarı aktarmak için Data Box kullanmayın.
- Giriş dosyalarında veya aynı verilere başvuran etiketlerde yinelenen etiketlere sahipseniz, Data Box dışa aktarma dosyaları atlayabilir veya üzerine yazabilir. Azure portal görüntülediği dosya ve veri boyutu sayısı, cihazdaki verilerin gerçek boyutundan farklı olabileceğini gösterir. 
- Data Box, SMB üzerinden Windows tabanlı sisteme veri aktarır ve dosya ve klasörler için SMB sınırlamalarıyla sınırlıdır. Desteklenmeyen adlara sahip dosyalar ve klasörler aktarılmaz.
- Önekden kapsayıcıya bir 1:1 eşlemesi vardır.
- En büyük dosya adı 1024 karakterdir. Bu uzunluğu aşan dosya adları aktarılmaz.
- *XML* dosyasındaki (sıra oluşturma sırasında karşıya yüklenen) çoğaltılan önekler verilir. Yinelenen ön ekler yok sayılır.
- Sayfa Blobları ve kapsayıcı adları büyük/küçük harfe duyarlıdır. Büyük/küçük harf uyuşmaz, blob ve/veya kapsayıcı bulunamadı.
 

## <a name="azure-storage-account-size-limits"></a>Azure depolama hesabı boyut sınırları

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure nesne boyutu sınırları

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure Blok Blobu, Sayfa Blobu ve dosya adlandırma kuralları

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
