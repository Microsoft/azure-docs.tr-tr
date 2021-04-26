---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582667"
---
Aşağıdaki uyarılar, Azure 'a taşırken veriler için geçerlidir.

- Birden fazla cihazın aynı kapsayıcıya yazmamalıdır.
- Bulutta, Kopyalanmakta olan nesneyle aynı ada sahip mevcut bir Azure nesneniz (blob veya bir dosya gibi) varsa, cihaz buluttaki dosyanın üzerine yazar.
- Klasörleri paylaşma altında oluşturulan boş bir dizin hiyerarşisi (herhangi bir dosya olmadan) blob kapsayıcılarına yüklenemez.
- Dosya Gezgini ile veya komut satırı aracılığıyla sürükle ve bırak kullanarak verileri kopyalayabilirsiniz. Kopyalanan dosyaların toplam boyutu 10 GB 'den büyükse, veya gibi bir toplu kopyalama programı kullanmanızı öneririz `Robocopy` `rsync` . Toplu kopyalama araçları, zaman aralıklı hatalar için kopyalama işlemini yeniden dener ve ek dayanıklılık sağlar.
- Azure depolama kapsayıcısı ile ilişkilendirilen paylaşımın, oluşturma sırasında, paylaşma için tanımlanan Blobların türüyle eşleşmeyen Blobları karşıya yüklediğinde, bu tür Bloblar güncellenmez. Örneğin, cihazda bir Blok Blobu paylaşma oluşturup, paylaşılan dosyayı sayfa Blobları olan mevcut bir bulut kapsayıcısı ile ilişkilendirin, dosyaları indirmek için bu paylaşımın yenilenmesini yapın ve ardından bulutta sayfa Blobları olarak zaten depolanmış olan yenilenmiş dosyalardan bazılarını değiştirin, karşıya yükleme başarısızlıklarını görürsünüz.
- Paylaşımlardaki bir dosya oluşturulduktan sonra dosyanın yeniden adlandırılması desteklenmez.
- Paylaşımdan dosya silindiğinde, depolama hesabındaki girdi silinmez.
- `rsync`Verileri kopyalamak için kullanıyorsanız, `rsync -a` seçeneği desteklenmez.