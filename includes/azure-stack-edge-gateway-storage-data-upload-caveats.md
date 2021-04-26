---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96468015"
---
Aşağıdaki uyarılar, Azure 'a taşırken veriler için geçerlidir.

- Birden fazla cihazın aynı kapsayıcıya yazmamalıdır.
- Bulutta, Kopyalanmakta olan nesneyle aynı ada sahip mevcut bir Azure nesneniz (blob veya bir dosya gibi) varsa, cihaz buluttaki dosyanın üzerine yazar.
- Klasörleri paylaşma altında oluşturulan boş bir dizin hiyerarşisi (herhangi bir dosya olmadan) blob kapsayıcılarına yüklenemez.
- Verileri Dosya Gezgini'yle sürükleyip bırakarak veya komut satırı aracılığıyla kopyalayabilirsiniz. Kopyalanmakta olan dosyaların toplam boyutu 10 GB'tan büyükse Robocopy veya rsync gibi bir toplu kopyalama programı kullanmanızı öneririz. Toplu kopyalama araçları aralıklı hatalar oluştuğunda kopyalama işlemini yeniden dener ve ek dayanıklılık sağlar. REST aracılığıyla BLOB depolama kullanmak için AzCopy veya Azure Depolama Gezgini kullanılabilir.
- Azure depolama kapsayıcısı ile ilişkilendirilen paylaşımın, oluşturma sırasında, paylaşma için tanımlanan Blobların türüyle eşleşmeyen Blobları karşıya yüklediğinde, bu tür Bloblar güncellenmez. Örneğin cihazda bir blok blobu paylaşımı oluşturun. Paylaşımı sayfa blobları içeren mevcut bir bulut kapsayıcısıyla ilişkilendirin. Dosyaları indirmek için paylaşımı yenileyin. Bulutta zaten sayfa blobu olarak depolanan, yenilenmiş dosyalardan bazılarında değişiklik yapın. Karşıya yükleme hataları görürsünüz.
- Paylaşımlardaki bir dosya oluşturulduktan sonra dosyanın yeniden adlandırılması desteklenmez.
- Paylaşımdan dosya silindiğinde, depolama hesabındaki girdi silinmez.
- Verileri kopyalamak için rsync kullanılıyorsa, `rsync -a` seçeneği desteklenmez.
