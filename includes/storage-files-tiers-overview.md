---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d06a46d2eaaad362890f1e3e44dbc746fa10898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633382"
---
Azure dosyaları, kendi senaryolarınızın performans ve fiyat gereksinimlerine yönelik olarak paylaşımlarınızı uyarlamanızı sağlamak için dört farklı depolama, Premium, işlem için iyileştirilmiş, sık erişimli ve seyrek katman katmanları sunar:

- **Premium**: Premium dosya paylaşımları katı hal sürücüleri (SSD 'ler) tarafından DESTEKLENIR ve GÇ yoğun iş yükleri IÇIN çoğu GÇ işlemi için tek basamaklı milisaniye içinde tutarlı yüksek performans ve düşük gecikme süresi sağlar. Premium dosya paylaşımları veritabanları, Web sitesi barındırma ve geliştirme ortamları gibi çok çeşitli iş yükleri için uygundur. Premium dosya paylaşımları, sunucu Ileti bloğu (SMB) ve ağ dosya sistemi (NFS) protokolleriyle birlikte kullanılabilir.
- **İşlem için iyileştirilmiş**: işlem için iyileştirilmiş dosya paylaşımları Premium dosya paylaşımları tarafından sunulan gecikmeye gerek olmayan işlem ağır iş yüklerini etkinleştirir. İşlem için iyileştirilmiş dosya paylaşımları, sabit disk sürücüleri (HDD 'Ler) tarafından desteklenen standart depolama donanımında sunulur. İşlem için iyileştirilmiş, "standart" olarak adlandırılmıştı, ancak bu, katmanın kendisi yerine depolama medyası türü anlamına gelir (Standart depolama donanımında olduklarından, sık ve seyrek da "standart" katmanlarıdır).
- **Sık** erişimli: sık erişimli dosya paylaşımları, takım paylaşımları gibi genel amaçlı dosya paylaşım senaryolarında en iyi duruma getirilmiş depolama sunar. Etkin dosya paylaşımları, HDD 'Ler tarafından desteklenen standart depolama donanımında sunulur.
- Seyrek **erişimli: seyrek** erişimli dosya paylaşımları, çevrimiçi arşiv depolama senaryoları için iyileştirilmiş uygun maliyetli depolama sunar. Seyrek Erişimli dosya paylaşımları, HDD 'Ler tarafından desteklenen standart depolama donanımında sunulur.

Premium dosya paylaşımları, **FileStorage depolama hesabı** türüne dağıtılır ve yalnızca sağlanan faturalandırma modelinde kullanılabilir. Premium dosya paylaşımları için sağlanan faturalandırma modeli hakkında daha fazla bilgi için bkz. [Premium dosya paylaşımları için sağlamayı anlama](../articles/storage/files/understanding-billing.md#provisioned-model). İşlem için iyileştirilmiş, sık erişimli ve seyrek erişimli dosya paylaşımları dahil olmak üzere standart dosya paylaşımları **genel amaçlı sürüm 2 (GPv2) depolama hesabı** türüne dağıtılır ve kullandıkça öde faturalandırma üzerinden kullanılabilir. Sık erişimli ve seyrek erişimli dosya paylaşımları, tüm Azure Kamu ve Azure Kamu bölgelerinde kullanılabilir. İşlem için iyileştirilmiş dosya paylaşımları, Azure Çin ve Azure Almanya bölgeleri dahil olmak üzere tüm Azure bölgelerinde kullanılabilir.

İş yükünüz için bir depolama katmanı seçerken, performans ve kullanım gereksinimlerinizi göz önünde bulundurun. İş yükünüz tek basamaklı gecikme gerektiriyorsa veya şirket içi SSD depolama medyası kullanıyorsanız, Premium katman büyük olasılıkla en uygun seçenektir. Düşük gecikme süresi çok önemli değilse, örneğin, Azure 'dan şirket içine takılan veya şirket içinde Azure Dosya Eşitleme aracılığıyla önbelleğe alınmış takım paylaşımları ile standart depolama, maliyet açısından daha iyi bir uyum sağlayabilir.

Depolama hesabında bir dosya paylaşma oluşturduktan sonra, farklı depolama hesabı türlerine özel katmanlara taşıyamazsınız. Örneğin, bir işlem için iyileştirilmiş dosya payını Premium katmana taşımak için, bir FileStorage depolama hesabında yeni bir dosya paylaşma oluşturmanız ve verileri özgün paylaşımınızdan FileStorage hesabındaki yeni bir dosya paylaşımıyla kopyalamanız gerekir. Azure dosya paylaşımları arasında veri kopyalamak için AzCopy kullanmanız önerilir, ancak `robocopy` Windows veya macOS ve Linux gibi araçları da kullanabilirsiniz `rsync` . 

GPv2 depolama hesapları içinde dağıtılan dosya paylaşımları, yeni bir depolama hesabı oluşturmadan ve veri geçirmeden Standart katmanlar (işlem için iyileştirilmiş, sık erişimli ve seyrek erişimli) arasında taşınabilir, ancak katmanınızı değiştirdiğinizde işlem maliyetlerine tabi olursunuz. Bir eklentiyi bir Hotter katmanından daha soğuk bir katmana taşıdığınızda, paylaşımdaki her dosya için daha soğuk katmanın yazma işlemi ücreti uygulanır. Bir dosya paylaşımının daha soğuk bir katmandan Hotter katmanına taşınması, paylaşılan katmanın paylaşımdaki her dosya için okuma işlemi ücretine neden olur.

Daha fazla bilgi için bkz. [Azure dosyalarının faturalandırmasını anlama](../articles/storage/files/understanding-billing.md) .