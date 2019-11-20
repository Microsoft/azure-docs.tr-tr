---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029876"
---
Bir depolama hesabına yönelik çoğaltma seçeneklerine şunlar dahildir:

* [Yerel olarak yedekli depolama (LRS)](../articles/storage/common/storage-redundancy-lrs.md): Basit, düşük maliyetli bir çoğaltma stratejisi. Veriler, birincil bölge içinde eşzamanlı olarak üç kez çoğaltılır.
* Bölgesel olarak [yedekli depolama (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): Yüksek kullanılabilirlik gerektiren senaryolar için çoğaltma. Veriler, birincil bölgedeki üç Azure kullanılabilirlik bölgesi arasında zaman uyumlu olarak çoğaltılır.
* [Coğrafi olarak yedekli depolama (GRS)](../articles/storage/common/storage-redundancy-grs.md): Bölgesel kesintilere karşı koruma için çapraz bölgesel çoğaltma. Veriler birincil bölgede zaman uyumlu olarak üç kez çoğaltılır ve ardından zaman uyumsuz olarak ikincil bölgeye çoğaltılır. İkincil bölgedeki verilere yönelik okuma erişimi için Okuma Erişimli Coğrafi olarak yedekli depolamayı (RA-GRS) etkinleştirin.
* [Coğrafi bölge yedekli depolama (GZRS) (Önizleme)](../articles/storage/common/storage-redundancy-gzrs.md): Hem yüksek kullanılabilirlik hem de maksimum dayanıklılık gerektiren senaryolar için çoğaltma. Veriler, birincil bölgedeki üç Azure kullanılabilirlik alanı arasında zaman uyumlu olarak çoğaltılır ve ardından zaman uyumsuz olarak ikincil bölgeye çoğaltılır. İkincil bölgedeki verilere yönelik okuma erişimi için, Okuma Erişimli Coğrafi bölge yedekli depolamayı (RA-GZRS) etkinleştirin.
