---
title: Oracle iş yükleri için BareMetal 'da depolama
description: Oracle iş yükleri için BareMetal altyapısı tarafından sunulan depolama hakkında bilgi edinin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559214"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Oracle iş yükleri için BareMetal 'da depolama

Bu makalede, Oracle iş yükleri için BareMetal altyapısı tarafından sunulan depolamaya genel bir bakış sunuyoruz.

Oracle için BareMetal altyapısı, NetApp ağ dosya sistemi (NFS) depolaması sunar. NFS depolama, Oracle gerçek uygulama kümeleri (RAC) sertifikası gerektirmez. Daha fazla bilgi için bkz. [Linux kümeleri Için Oracle RAC teknolojileri matrisi](https://www.oracle.com/database/technologies/tech-generic-linux-new.html).

Bu depolama teklifi, A700s veya A800s depolama denetleyicileri kullanarak bir OEM ortağından katman 3 desteğini içerir.

BareMetal altyapı depolaması, bu Premium depolama özelliklerini sunar:

- DNFS protokolü aracılığıyla sunulan veri/günlük/çekirdek/FSA depolama birimleri.
- Disk artıklığı (*en fazla iki disk arızasına karşı koruma*).
- Verileri birim başına 100 TB ile sınırlı birden çok birime ölçeklendirin.
- 12 denetleyicilere kadar birden çok depolama denetleyicisine ölçeği genişletme.
- Disk düzeyi yönetimi yok (disk *Ekle/Kaldır*), infra tarafından otomatik olarak ele alınır.
- Dosya içeriğini farklı birimlere yeniden dağıtma için kapalı kalma süresi yok.
- Birimleri büyüme/küçültme özelliği.
- Kopyalama ve anlık görüntü Kasası kullanarak yedekleme için SnapCenter tümleştirmesi.
- Bekleyen veri şifrelemesi, FIPS 'yi (140-2) destekler.

## <a name="next-steps"></a>Sonraki adımlar

BareMetal altyapı düzeltme eki uygulama konuları hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Oracle için BareMetal için düzeltme eki uygulama konuları](oracle-baremetal-patching.md)

