---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a48c1352e4628d8e1776a9479aceac7c294a2ea1
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72023075"
---
Aşağıdaki tabloda, Azure genel amaçlı v1, v2 ve BLOB depolama hesapları için varsayılan sınırlar açıklanmaktadır. Giriş *sınırı,* bir depolama hesabına gönderilen isteklerin tüm verilerini ifade eder. *Çıkış* sınırı, bir depolama hesabından alınan yanıtların tüm verilerine başvurur.

| Kaynak | Varsayılan limit |
| --- | --- |
| Her iki standart ve Premium hesabı da dahil olmak üzere her abonelik için bölge başına depolama hesabı sayısı | 250 |
| Maksimum depolama hesabı kapasitesi | ABD ve Avrupa için 2 PiB ve diğer tüm bölgeler için 500 TiB (UK dahil)<sup>1</sup>|
| Depolama hesabı başına en fazla BLOB kapsayıcısı, blob, dosya paylaşımı, tablo, kuyruk, varlık veya ileti sayısı | Sınırsız |
| Depolama hesabı başına en fazla istek oranı<sup>1</sup> | saniye başına 20.000 istek |
| Depolama hesabı başına en fazla giriş<sup>1</sup> (ABD, Avrupa Bölgesi) | 25 Gbps |
| Depolama hesabı başına en fazla giriş<sup>1</sup> (ABD ve Avrupa dışındaki bölgeler) | RA-GRS/GRS etkinse 5 Gbps, LRS/ZRS<sup>2</sup> Için 10 Gbps |
| Genel amaçlı v2 ve BLOB depolama hesapları için maksimum çıkış (tüm bölgeler) | 50 Gbps |
| Genel amaçlı v1 depolama hesapları için maksimum çıkış (ABD bölgeleri) | RA-GRS/GRS etkinse 20 Gbps, LRS/ZRS<sup>2</sup> Için 30 Gbps |
| Genel amaçlı v1 depolama hesapları için maksimum çıkış (ABD dışı bölgeler) | 10 Gbps, RA-GRS/GRS etkinse, LRS/ZRS<sup>2</sup> Için 15 GB/sn |

<sup>1</sup> Azure Standart depolama hesapları, isteğe göre giriş için daha yüksek kapasite sınırlarını ve daha yüksek limitleri destekler. Giriş için hesap sınırlarında artış istemek için [Azure desteğine](https://azure.microsoft.com/support/faq/)başvurun. Daha fazla bilgi için bkz. daha [büyük, daha yüksek ölçekli depolama hesapları duyurusu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>2</sup> okuma erişimi ETKINSE (RA-GRS/ra-GZRS), ikincil konum için çıkış hedefleri birincil konumlarından benzerdir. [Azure depolama çoğaltma](https://docs.microsoft.com/azure/storage/common/storage-redundancy) seçenekleri şunlardır:  
[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> Çoğu senaryo için genel amaçlı v2 depolama hesabı kullanmanızı öneririz. Genel amaçlı v1 veya bir Azure Blob Depolama hesabını, kapalı kalma süresi olmadan ve verileri kopyalamaya gerek kalmadan, genel amaçlı bir v2 hesabına kolayca yükseltebilirsiniz.
>
> Azure depolama hesapları hakkında daha fazla bilgi için bkz. [depolama hesabına genel bakış](../articles/storage/common/storage-account-overview.md).

Uygulamanızın ihtiyaçları tek bir depolama hesabının ölçeklenebilirlik hedeflerini aşarsa, uygulamanızı birden çok depolama hesabı kullanacak şekilde oluşturabilirsiniz. Daha sonra veri nesnelerinizi bu depolama hesaplarında bölümleyebilirsiniz. Toplu fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Storage fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

Tüm depolama hesapları, bir düz ağ topolojisinde çalışır ve ne zaman oluşturulduklarında bağımsız olarak bu makalede özetlenen ölçeklenebilirlik ve performans hedeflerini destekler. Azure depolama düz ağ mimarisi ve ölçeklenebilirlik hakkında daha fazla bilgi için bkz. [Microsoft Azure depolama: güçlü tutarlılık Ile yüksek oranda kullanılabilir bir bulut depolama hizmeti](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

