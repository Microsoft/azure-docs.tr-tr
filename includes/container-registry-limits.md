---
title: include dosyası
description: include dosyası
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bc4ac68cb415a43ac34d36afc2adc30307e6d37c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795295"
---
| Kaynak | Temel | Standart | Premium |
|---|---|---|---|
| Depolama<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maksimum görüntü katman boyutu | 200 GiB | 200 GiB | 200 GiB |
| Dakikada ReadOps<sup>2, 3</sup> | 1000 | 3\.000 | 10,000 |
| Dakikada WriteOps<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| İndirme bant genişliği MBps<sup>2</sup> | 30 | 60 | 100 |
| Karşıya yükleme bant genişliği MBps<sup>2</sup> | 10 | 20 | 50 |
| Web Kancaları | 2 | 10 | 100 |
| Coğrafi çoğaltma | Yok | Yok | [Destekleniyor][geo-replication] |
| İçerik güveni | Yok | Yok | [Destekleniyor][content-trust] |
| Sanal ağ erişimi | Yok | Yok | [Önizleme][vnet] |
| Depo kapsamlı izinler | Yok | Yok | [Önizleme][token]|
| &bull; belirteçleri | Yok | Yok | 20.000 |
| &bull; kapsam haritaları | Yok | Yok | 20.000 |
| kapsam eşleme başına &bull; depoları | Yok | Yok | 500 |


<sup>1</sup> Belirtilen depolama sınırları, her katman için *dahil edilen* depolama miktarıdır. Bu limitlerin üzerinde görüntü depolaması için GiB başına ek bir günlük ücret ödersiniz. Hız bilgileri için bkz. [Azure Container Registry fiyatlandırması][pricing].

<sup>2</sup>*readops*, *writeops*ve *bant genişliği* minimum tahminlerdir. Azure Container Registry, kullanım gerektirdiğinden performansı artırmak için çaba harcar.

<sup>3</sup> [Docker Pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) , görüntüdeki katmanların sayısına ve bildirim almaya göre birden çok okuma işlemine çevrilir.

<sup>4</sup> [Docker Push](https://docs.docker.com/registry/spec/api/#pushing-an-image) , itilmiş olması gereken katmanların sayısına bağlı olarak birden çok yazma işlemine çevrilir. Bir `docker push` var olan bir görüntü için bildirim almak üzere *Readops 'leri* içerir.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md