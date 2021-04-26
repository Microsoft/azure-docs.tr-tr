---
title: Sanal ağları yönetme-Azure portal-MySQL için Azure veritabanı-esnek sunucu
description: Azure portal kullanarak MySQL için Azure veritabanı için sanal ağlar oluşturma ve yönetme-esnek sunucu
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4906ce9f562910f0a087cd25167457ec1fb301ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110007"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı için sanal ağlar oluşturma ve yönetme-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

MySQL için Azure Veritabanı Esnek Sunucusu, esnek sunucunuza bağlanmak için birbirini dışlayan iki tür ağ bağlantısı yöntemini destekler. Bu iki seçenek şunlardır:

- Genel erişim (izin verilen IP adresleri)
- Özel erişim (Sanal Ağ Tümleştirmesi)

Bu makalede, Azure portal kullanarak **özel erişim (VNET tümleştirmesi)** ile MySQL sunucusu oluşturmaya odaklanacağız. Özel erişim (VNet tümleştirmesi) ile esnek sunucunuzu kendi [Azure sanal ağınıza](../../virtual-network/virtual-networks-overview.md)dağıtabilirsiniz. Azure sanal ağları özel ve güvenli ağ iletişimi sağlar. Özel erişim ile MySQL sunucusuna bağlantılar sanal ağınızla kısıtlıdır. Bu konuda daha fazla bilgi edinmek için bkz. [özel erişim (VNET tümleştirmesi)](./concepts-networking.md#private-access-vnet-integration).

Sunucu oluşturma işlemi sırasında esnek sunucunuzu bir sanal ağa ve alt ağa dağıtabilirsiniz. Esnek sunucu dağıtıldıktan sonra başka bir sanal ağa, alt ağa veya *Genel erişime (izin verilen IP adresleri)* taşıyamazsınız.

## <a name="prerequisites"></a>Önkoşullar
Bir sanal ağda esnek sunucu oluşturmak için şunlar gerekir:
- Bir [sanal ağ](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Sanal ağ ve alt ağ, esnek sunucunuz ile aynı bölgede ve abonelikte olmalıdır.

-  [Sanal ağ temsilcisi](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) olarak **Microsoft.DBforMySQL/flexibleServers**. Bu temsil ilgili alt ağı yalnızca MySQL için Azure Veritabanı Esnek Sunucularının kullanabileceği anlamına gelir. Temsilci alt ağında diğer türdeki Azure kaynakları bulunamaz.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Zaten var olan bir sanal ağda MySQL için Azure veritabanı esnek sunucusu oluşturma

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** (+) seçeneğini belirleyin.
2. **Veritabanları**  >  **MySQL için Azure veritabanı**' nı seçin. Ayrıca, arama kutusuna **MySQL** girerek hizmeti bulabilirsiniz.
3. Dağıtım seçeneği olarak **esnek sunucu** ' yı seçin.
4. **Temel bilgiler** formunu doldurun.
5. Sunucunuza nasıl bağlanmak istediğinizi yapılandırmak için **ağ** sekmesine gidin.
6. **Bağlantı yönteminde** **özel erişim (VNET tümleştirmesi)** öğesini seçin. **Sanal ağ ' a** gidin ve yukarıdaki önkoşulların bir parçası olarak zaten mevcut olan *sanal ağı* ve *alt ağı* seçin.
7. Esnek sunucu yapılandırmanızı gözden geçirmek için **gözden geçir + oluştur** ' u seçin.
8. Sunucuyu sağlamak için **Oluştur**’u seçin. Sağlama birkaç dakika sürebilir.

>[!Note]
> Esnek sunucu bir sanal ağa ve alt ağa dağıtıldıktan sonra, bunu ortak erişime (izin verilen IP adresleri) taşıyamazsınız.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-cli.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin
- [MySQL Için Azure veritabanı esnek sunucu sanal ağı](./concepts-networking.md#private-access-vnet-integration)hakkında daha fazla bilgi edinin.
