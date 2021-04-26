---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026462"
---
Aşağıdaki tabloda, Azure genel amaçlı v1, v2, BLOB depolama ve blok BLOB depolama hesapları için varsayılan sınırlar açıklanmaktadır. Giriş *sınırı,* bir depolama hesabına gönderilen tüm verileri ifade eder. *Çıkış* sınırı, bir depolama hesabından alınan tüm verileri ifade eder.

> [!NOTE]
> Daha yüksek kapasite ve giriş limitleri isteyebilirsiniz. Bir artış istemek için [Azure desteği](https://azure.microsoft.com/support/faq/)'ne başvurun.

| Kaynak | Sınır |
| --- | --- |
| Standart ve Premium Depolama hesapları dahil olmak üzere, abonelik başına bölge başına depolama hesabı sayısı.| 250 |
| Maksimum depolama hesabı kapasitesi | 5 PiB <sup>1</sup>|
| Depolama hesabı başına en fazla BLOB kapsayıcısı, blob, dosya paylaşımı, tablo, kuyruk, varlık veya ileti sayısı | Sınır yok |
| Depolama hesabı başına en fazla istek oranı<sup>1</sup> | saniye başına 20.000 istek |
| Depolama hesabı başına en fazla giriş<sup>1</sup> (ABD, Avrupa Bölgesi) | 10 Gbps |
| Depolama hesabı başına en fazla giriş<sup>1</sup> (ABD ve Avrupa dışındaki bölgeler) | RA-GRS/GRS etkinse 5 Gbps, LRS/ZRS<sup>2</sup> Için 10 Gbps |
| Genel amaçlı v2 ve BLOB depolama hesapları için maksimum çıkış (tüm bölgeler) | 50 Gbps |
| Genel amaçlı v1 depolama hesapları için maksimum çıkış (ABD bölgeleri) | RA-GRS/GRS etkinse 20 Gbps, LRS/ZRS<sup>2</sup> Için 30 Gbps |
| Genel amaçlı v1 depolama hesapları için maksimum çıkış (ABD dışı bölgeler) | 10 Gbps, RA-GRS/GRS etkinse, LRS/ZRS<sup>2</sup> Için 15 GB/sn |
| Depolama hesabı başına en fazla sanal ağ kuralı sayısı | 200 |
| Depolama hesabı başına en fazla IP adresi kuralı sayısı | 200 |

<sup>1</sup> Azure depolama standart hesapları, isteğe göre giriş için daha yüksek kapasite sınırlarını ve daha yüksek limitleri destekler. Hesap sınırlarında artış istemek için [Azure desteği](https://azure.microsoft.com/support/faq/)'ne başvurun.

<sup>2</sup> depolama hesabınızın, coğrafi olarak yedekli depolama (RA-GRS) veya coğrafi bölge yedekli depolama (ra-GZRS) ile okuma erişimi etkinse, ikincil konum için çıkış hedefleri birincil konumlarından benzerdir. Daha fazla bilgi için bkz. [Azure Storage çoğaltma](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> Microsoft, çoğu senaryo için genel amaçlı v2 depolama hesabı kullanmanızı önerir. Genel amaçlı v1 veya bir Azure Blob Depolama hesabını, kapalı kalma süresi olmadan ve verileri kopyalamaya gerek kalmadan, genel amaçlı bir v2 hesabına kolayca yükseltebilirsiniz. Daha fazla bilgi için bkz. [genel amaçlı v2 depolama hesabına yükseltme](../articles/storage/common/storage-account-upgrade.md).

Tüm depolama hesapları, oluşturulduklarında bağımsız olarak düz bir ağ topolojisi üzerinde çalışır. Azure depolama düz ağ mimarisi ve ölçeklenebilirlik hakkında daha fazla bilgi için bkz. [Microsoft Azure depolama: güçlü tutarlılık Ile yüksek oranda kullanılabilir bir bulut depolama hizmeti](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets).