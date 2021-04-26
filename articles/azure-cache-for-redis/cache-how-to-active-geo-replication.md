---
title: Redsıs örnekleri için kurumsal Azure önbelleği için etkin Coğrafi çoğaltmayı yapılandırma
description: Azure bölgelerinde Redsıs kurumsal örnekleri için Azure önbelleğinizi çoğaltmayı öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 3fe3131263d3cf1984eae1692854d8d6bcd2746a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109497"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Redsıs örnekleri için kurumsal Azure önbelleği için etkin Coğrafi çoğaltmayı yapılandırma (Önizleme)

Bu makalede, Azure portal kullanarak etkin bir coğrafi çoğaltmalı Azure önbelleğinin nasıl yapılandırılacağını öğreneceksiniz.

Etkin coğrafi çoğaltma, Redsıs örnekleri için iki veya daha fazla kurumsal Azure önbelleğini Azure bölgeleri arasında yayılan tek bir önbellekte gruplandırır. Tüm örnek yerel olarak işlem görür. Uygulama, okuma ve yazma istekleri için hangi örneklerin kullanılacağını belirler.

## <a name="create-or-join-an-active-geo-replication-group"></a>Etkin bir coğrafi çoğaltma grubu oluşturun veya ekleyin

> [!IMPORTANT]
> Etkin coğrafi çoğaltmanın, redin için bir Azure önbelleği oluşturulduğu sırada etkinleştirilmesi gerekir.
>
>

1. **Yeni Redis Cache** oluşturma kullanıcı arabiriminin **Gelişmiş** sekmesinde **kümeleme ilkesi** için **kuruluş** ' u seçin.

    ![Etkin coğrafi çoğaltmayı yapılandırma](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. **Etkin coğrafi çoğaltma**'yı ayarlamak için **Yapılandır** ' a tıklayın.

1. İlk önbellek örneği için yeni bir çoğaltma grubu oluşturun veya listeden mevcut bir tane seçin.

    ![Bağlantı önbellekleri](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Son için **Yapılandır** ' a tıklayın.

    ![Etkin coğrafi çoğaltma yapılandırıldı](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. İlk önbelleğin başarıyla oluşturulmasını bekleyin. Coğrafi çoğaltma grubundaki her ek önbellek örneği için yukarıdaki adımları tekrarlayın.

## <a name="remove-from-an-active-geo-replication-group"></a>Etkin bir coğrafi çoğaltma grubundan kaldır

Etkin bir coğrafi çoğaltma grubundan bir önbellek örneğini kaldırmak için örneği silmeniz yeterlidir. Kalan örnekler kendilerini otomatik olarak yeniden yapılandıracaktır.

## <a name="next-steps"></a>Sonraki adımlar

Redsıs özellikleri için Azure önbelleği hakkında daha fazla bilgi edinin.

* [Redsıs hizmet katmanları için Azure önbelleği](cache-overview.md#service-tiers)
* [Redsıs için Azure önbelleği için yüksek kullanılabilirlik](cache-high-availability.md)
