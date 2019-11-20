---
title: Azure HPC önbelleği bağlama
description: İstemcileri Azure HPC önbellek hizmetine bağlama
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582223"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC önbelleğini bağlama

Önbellek oluşturulduktan sonra, NFS istemcileri basit bir Mount komutuyla erişebilir.

Bağlama komutu iki öğeden oluşur:

* Önbelleğin bağlama adreslerinden biri (önbelleğe Genel Bakış sayfasında listelenir)
* Depolama hedefini oluştururken ayarladığınız sanal ad alanı yolu

![Azure HPC önbellek örneğinin genel bakış sayfasının ekran görüntüsü, sağ alt taraftaki bağlama adresleri listesi etrafında bir vurgulama kutusuyla](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Önbellek bağlama adresleri, önbelleğin alt ağının içindeki ağ arabirimlerine karşılık gelir. Bir kaynak grubunda, bu NIC 'ler `-cluster-nic-` ile biten adlarla ve bir sayı ile listelenir. Bu arabirimleri değiştirmeyin veya silmeyin, aksi durumda önbellek kullanılamaz hale gelir.

Sanal ad alanı yolları, **depolama hedefleri** sayfasında gösterilir. Ayrıntılarını görmek için, bununla ilişkili toplanmış ad alanı yolları da dahil olmak üzere, ayrı bir depolama hedefi adına tıklayın.

![tablonun yol sütunundaki bir girdinin etrafında vurgulama kutusuyla, önbelleğin depolama hedefi panelinin ekran görüntüsü](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Bağlama komutu sözdizimi

Aşağıdaki gibi bir mount komutu kullanın:

> sudo Mount *cache_mount_address*:/*namespace_path* *local_path* {*Seçenekler*}

Örnek:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Bu komut başarılı olduktan sonra, depolama dışarı aktarmanın içerikleri istemcideki ``hpccache`` dizininde görünür olmalıdır.

> [!NOTE] 
> İstemcileriniz, önbelleğinizi barındıran sanal ağa ve alt ağa erişebilmelidir. Örneğin, aynı sanal ağ içinde istemci VM 'Leri oluşturun veya dışarıdaki bir erişim için bir uç nokta, ağ geçidi veya sanal ağda başka bir çözüm kullanın. Önbelleğin alt ağı içinde başka hiçbir şeyin barındırılayamadığını unutmayın.

### <a name="mount-command-options"></a>Bağlama komutu seçenekleri

Sağlam bir istemci bağlama için, bu ayarları ve bağımsız değişkenleri bağlama komutunuz geçirin: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Önerilen bağlama komutu ayarları | |
--- | --- 
``hard`` | Azure HPC Cache 'e yönelik hafif bağlar, uygulama hatalarıyla ve olası veri kaybı ile ilişkilendirilir. 
``proto=netid`` | Bu seçenek NFS ağ hatalarının uygun işlenmesini destekler.
``mountproto=netid`` | Bu seçenek, bağlama işlemleri için ağ hatalarının uygun işlenmesini destekler.
``retry=n`` | Geçici bağlama hatalarından kaçınmak için ``retry=30`` ayarlayın. (Ön plan takmaları farklı bir değer önerilir.)

## <a name="next-steps"></a>Sonraki adımlar

* Verileri önbelleğin depolama hedeflerine taşımak için [yeni Azure Blob depolama alanını](hpc-cache-ingest.md)okuyun.
