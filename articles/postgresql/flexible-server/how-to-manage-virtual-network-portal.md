---
title: Sanal ağları yönetme-Azure portal-PostgreSQL için Azure veritabanı-esnek sunucu
description: Azure portal kullanarak PostgreSQL için Azure veritabanı için sanal ağlar oluşturma ve yönetme-esnek sunucu
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d5b60cfeb3bfce1253ece3708e6b48ce6bcb0b4f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226914"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure portal kullanarak PostgreSQL için Azure veritabanı için sanal ağlar oluşturma ve yönetme-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure veritabanı-esnek sunucu, esnek sunucunuza bağlanmak için iki tür karşılıklı kullanım dışı ağ bağlantısı yöntemini destekler. Bu iki seçenek şunlardır:

* Genel erişim (izin verilen IP adresleri)
* Özel erişim (Sanal Ağ Tümleştirmesi)

Bu makalede, Azure portal kullanarak **özel erişim (VNET tümleştirmesi)** Ile PostgreSQL sunucusu oluşturmaya odaklanacağız. Özel erişim (VNet tümleştirmesi) ile esnek sunucunuzu kendi [Azure sanal ağınıza](../../virtual-network/virtual-networks-overview.md)dağıtabilirsiniz. Azure sanal ağları özel ve güvenli ağ iletişimi sağlar. Özel erişim ile, PostgreSQL sunucusuna yapılan bağlantılar sanal ağınızla kısıtlıdır. Bu konuda daha fazla bilgi edinmek için bkz. [özel erişim (VNET tümleştirmesi)](./concepts-networking.md#private-access-vnet-integration).

Sunucu oluşturma işlemi sırasında esnek sunucunuzu bir sanal ağa ve alt ağa dağıtabilirsiniz. Esnek sunucu dağıtıldıktan sonra başka bir sanal ağa, alt ağa veya *Genel erişime (izin verilen IP adresleri)* taşıyamazsınız.

## <a name="prerequisites"></a>Önkoşullar
Bir sanal ağda esnek sunucu oluşturmak için şunlar gerekir:
- Bir [sanal ağ](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Sanal ağ ve alt ağ, esnek sunucunuz ile aynı bölgede ve abonelikte olmalıdır.

-  [Bir alt ağı](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) **Microsoft. DBforPostgreSQL/Flexıtaservers**'a devretmek. Bu temsili, yalnızca PostgreSQL için Azure veritabanı 'nın esnek sunucularının bu alt ağı kullanabileceği anlamına gelir. Temsilci alt ağında diğer türdeki Azure kaynakları bulunamaz.
-  `Microsoft.Storage`Esnek sunuculara temsilci atanmış alt ağ için hizmet uç noktasına ekleyin. Bu işlem aşağıdaki adımları gerçekleştirerek yapılır:
     1. Sanal ağ sayfanıza gidin.
     2. Esnek sunucunuzu dağıtmayı planladığınız sanal ağı seçin.
     3. Esnek sunucu için temsilci atanmış alt ağı seçin.
     4. Kullanıma alma ekranında, **hizmet uç noktası**' nın altında, `Microsoft.storage` açılır listeden seçim yapın.
     5. Değişiklikleri kaydedin.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Zaten var olan bir sanal ağda, PostgreSQL için Azure veritabanı-esnek sunucu oluşturma

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** (+) seçeneğini belirleyin.
2. **Veritabanları**  >  **PostgreSQL için Azure veritabanı**' nı seçin. Hizmeti bulmak için arama kutusuna **PostgreSQL** ' i de girebilirsiniz.
3. Dağıtım seçeneği olarak **esnek sunucu** ' yı seçin.
4. **Temel bilgiler** formunu doldurun.
5. Sunucunuza nasıl bağlanmak istediğinizi yapılandırmak için **ağ** sekmesine gidin.
6. **Bağlantı yönteminde** **özel erişim (VNET tümleştirmesi)** öğesini seçin. **Sanal ağ ' a** gidin ve yukarıdaki önkoşulların bir parçası olarak zaten mevcut olan *sanal ağı* ve *alt ağı* seçin.
7. Esnek sunucu yapılandırmanızı gözden geçirmek için **gözden geçir + oluştur** ' u seçin.
8. Sunucuyu sağlamak için **Oluştur**’u seçin. Sağlama birkaç dakika sürebilir.

>[!Note]
> Esnek sunucu bir sanal ağa ve alt ağa dağıtıldıktan sonra, bunu ortak erişime (izin verilen IP adresleri) taşıyamazsınız.
## <a name="next-steps"></a>Sonraki adımlar
- [Azure CLI kullanarak PostgreSQL Için Azure veritabanı-esnek sunucu sanal ağı oluşturun ve yönetin](./how-to-manage-virtual-network-cli.md).
- [PostgreSQL Için Azure veritabanı 'nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin-esnek sunucu
- [PostgreSQL Için Azure veritabanı-esnek sunucu sanal ağı](./concepts-networking.md#private-access-vnet-integration)hakkında daha fazla bilgi edinin.