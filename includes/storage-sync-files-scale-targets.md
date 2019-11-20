---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391813"
---
| Kaynak | Hedefleyin | Sabit sınır |
|----------|--------------|------------|
| Bölge başına depolama Eşitleme Hizmetleri | 20 depolama eşitleme hizmeti | Yes |
| Depolama eşitleme hizmeti başına eşitleme grupları | 100 eşitleme grupları | Yes |
| Depolama eşitleme hizmeti başına kayıtlı sunucular | 99 sunucu | Yes |
| Eşitleme grubu başına bulut uç noktaları | 1 bulut uç noktası | Yes |
| Eşitleme grubu başına sunucu uç noktaları | 50 sunucu uç noktaları | Hayır |
| Sunucu başına sunucu uç noktaları | 30 sunucu uç noktası | Yes |
| Eşitleme grubu başına dosya sistemi nesneleri (dizinler ve dosyalar) | 50.000.000 nesneleri | Hayır |
| Bir dizindeki en fazla dosya sistemi nesnesi (Dizin ve dosya) sayısı | 5\.000.000 nesneleri | Yes |
| En fazla nesne (dizinler ve dosyalar) güvenlik tanımlayıcısı boyutu | 64 KiB | Yes |
| Dosya boyutu | 100 GiB | Hayır |
| Katman oluşturulacak bir dosya için en küçük dosya boyutu | 64 KiB | Yes |
| Eşzamanlı eşitleme oturumları | V4 Aracısı ve üzeri: sınır, kullanılabilir sistem kaynaklarına göre farklılık gösterir. <BR> V3 Aracısı: işlemci başına Iki etkin eşitleme oturumu veya sunucu başına en fazla sekiz etkin eşitleme oturumu. | Yes

> [!Note]  
> Azure Dosya Eşitleme uç noktası, bir Azure dosya paylaşımının boyutuna kadar ölçeklendirilebilir. Azure dosya paylaşımının boyut sınırına ulaşıldığında, eşitleme çalışamayacak.
