---
title: Azure HPC önbelleği oluşturma
description: Azure HPC önbellek örneği oluşturma
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582190"
---
# <a name="plan-the-aggregated-namespace"></a>Toplanan ad alanını planlama

Azure HPC önbelleği, istemcilerin, arka uç depolama sisteminin ayrıntılarını gizleyen bir sanal ad alanı aracılığıyla çeşitli depolama sistemlerine erişmesini sağlar.

Bir depolama hedefi eklediğinizde, istemciye yönelik dosya yolunu ayarlarsınız. İstemci makineler bu dosya yolunu bağlayabilir ve depolama sistemini doğrudan bağlamak yerine önbelleğe dosya okuma istekleri yapabilir.

Azure HPC Cache bu sanal dosya sistemini yönettiği için, istemciye yönelik yolu değiştirmeden depolama hedefini değiştirebilirsiniz. Örneğin, bir donanım depolama sistemini, istemciye yönelik yordamları yeniden yazmaya gerek kalmadan bulut depolama ile değiştirebilirsiniz.

## <a name="aggregated-namespace-example"></a>Toplanmış ad alanı örneği

İstemci makinelerin ihtiyaç duydukları bilgilere rahat bir şekilde ulaşabilmesi için toplanan ad alanınızı planlayın ve yöneticiler ile iş akışı mühendislerinin yolları kolayca ayırabilmesini sağlayın.

Örneğin, Azure Blob 'da depolanan verileri işlemek için bir Azure HPC önbellek örneğinin kullanıldığı bir sistemi düşünün. Analiz, şirket içi veri merkezinde depolanan şablon dosyalarını gerektirir.

Şablon verileri bir veri merkezinde depolanır ve bu iş için gereken bilgiler şu alt dizinlerde saklanır:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Veri merkezi depolama sistemi bu dışarı aktarmaları kullanıma sunar:

    /
    /goldline
    /goldline/templates

Çözümlenecek veriler, [Clfsload yardımcı programı](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)kullanılarak "SourceCollection" adlı bir Azure Blob depolama kapsayıcısına kopyalanmış.

Önbellek üzerinden kolay erişime izin vermek için bu sanal ad alanı yollarıyla depolama hedefleri oluşturmayı göz önünde bulundurun:

| Arka uç depolama sistemi <br/> (NFS dosya yolu veya blob kapsayıcısı) | Sanal ad alanı yolu |
|-----------------------------------------|------------------------|
| /Goldline/Templates/acme2017/sku798     | /Templates/sku798      |
| /Goldline/Templates/acme2017/sku980     | /Templates/sku980      |
| SourceCollection                        | /Source               |

Bir NFS depolama hedefi, her biri benzersiz bir dışarı aktarma yoluna başvurduğu sürece birden fazla sanal ad alanı yoluna sahip olabilir.

NFS kaynak yolları aynı dışarı aktarmanın alt dizinleri olduğundan, aynı depolama hedefinden birden çok ad alanı yolu tanımlamanız gerekir.

| Depolama hedef konak adı  | NFS dışarı aktarma yolu      | Alt dizin yolu | Ad alanı yolu    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP adresi veya ana bilgisayar adı* | /Goldline/Templates  | acme2017/sku798   | /Templates/sku798 |
| *IP adresi veya ana bilgisayar adı* | /Goldline/Templates  | acme2017/sku980   | /Templates/sku980 |

Bir istemci uygulaması önbelleği bağlayabilir ve ``/source``, ``/templates/sku798``ve ``/templates/sku980``toplanmış ad alanı dosya yollarına kolayca erişebilir.

## <a name="next-steps"></a>Sonraki adımlar

Sanal dosya sisteminizi ayarlamaya karar verdikten sonra, arka uç depolama alanınızı istemciye yönelik sanal dosya yollarına eşlemek için [depolama hedefleri oluşturun](hpc-cache-add-storage.md) .
