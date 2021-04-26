---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011771"
---
Azure dosya paylaşımlardaki verileri veri kaybına veya bozulmaya karşı korumak için tüm Azure dosya paylaşımları yazıldığı gibi her bir dosyanın birden çok kopyasını depolar. İş yükünüzün gereksinimlerine bağlı olarak, ek derece artıklığı seçebilirsiniz. Azure dosyaları şu anda aşağıdaki veri artıklığı seçeneklerini desteklemektedir:

- **Yerel olarak yedekli**: genellikle LRS olarak anılan yerel olarak yedekli depolama, her dosyanın bir Azure Storage kümesi içinde üç kez depolandığı anlamına gelir. Bu, bozuk disk sürücüsü gibi donanım hataları nedeniyle verilerin kaybedilmesine karşı koruma sağlar.
- **Bölge yedekli**: genellikle ZRS olarak anılan bölge yedekli depolama, her dosyanın üç farklı Azure depolama kümelerinde üç kez depolandığı anlamına gelir. Yerel olarak yedekli depolamada olduğu gibi, bölge artıklığı her bir dosyanın üç kopyasını verir ancak bu kopyalar, farklı Azure *kullanılabilirlik bölgelerinde* bulunan üç farklı depolama kümesinde fiziksel olarak yalıtılır. Kullanılabilirlik alanları, bir Azure bölgesi içinde benzersiz fiziksel konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Depolama alanına yazma işlemi, üç kullanılabilirlik bölgesindeki depolama kümelerine yazılana kadar kabul edilmez. 
- **Coğrafi** olarak yedekli: genellikle GRS olarak anılan coğrafi olarak yedekli depolama, yerel olarak yedekli depolama gibidir. Bu, bir dosyanın birincil bölgedeki bir Azure depolama kümesi içinde üç kez depolanmasıdır. Tüm yazma işlemleri, zaman uyumsuz olarak Microsoft tarafından tanımlanan bir ikincil bölgeye çoğaltılır. Coğrafi olarak yedekli depolama, iki Azure bölgesi arasında veri yayılmasının 6 kopyasını sağlar. Doğal bir olağanüstü durum veya başka benzer bir olay nedeniyle, bir Azure bölgesinin kalıcı kaybolması gibi önemli bir olağanüstü durum durumunda Microsoft, tüm işlemlere hizmet vermek için bir yük devretme işlemi gerçekleştirecektir. Birincil ve ikincil bölgeler arasındaki çoğaltma zaman uyumsuz olduğundan, büyük bir olağanüstü durum durumunda ikincil bölgeye henüz çoğaltılmamış veriler kaybolur. Ayrıca, coğrafi olarak yedekli bir depolama hesabının el ile yük devretmesini gerçekleştirebilirsiniz.
- **Coğrafi bölge yedekli**: genellikle GZRS olarak bilinen coğrafi bölge yedekli depolama alanı, bölge yedekli depolama gibidir; bu da bir dosyanın birincil bölgedeki üç ayrı depolama kümesinde üç kez depolanmasıdır. Tüm yazma işlemleri, zaman uyumsuz olarak Microsoft tarafından tanımlanan bir ikincil bölgeye çoğaltılır. Coğrafi bölge yedekli depolama için yük devretme süreci, coğrafi olarak yedekli depolama için olduğu gibi çalışacaktır.

Standart Azure dosya paylaşımları dört artıklık türünü destekler, ancak Premium Azure dosya paylaşımları yalnızca yerel olarak yedekli ve bölgesel olarak yedekli depolamayı destekler.

Genel amaçlı sürüm 2 (GPv2) depolama hesapları, Azure dosyaları tarafından desteklenmeyen iki ek artıklık seçeneği sağlar: Bu, genellikle RA-GRS olarak anılan ve Okuma Erişimli Coğrafi olarak yedekli depolama, genellikle RA-GZRS olarak adlandırılır. Bu seçenekler ayarlanmış olarak depolama hesaplarında Azure dosya paylaşımları sağlayabilirsiniz, ancak Azure dosyaları ikincil bölgeden okumayı desteklemez. Okuma Erişimli Coğrafi veya coğrafi bölge yedekli depolama hesaplarına dağıtılan Azure dosya paylaşımları, sırasıyla coğrafi olarak yedekli veya coğrafi olarak yedekli depolama hesaplarına göre faturalandırılır.