---
title: Azure portal kullanarak MariaDB VNet hizmet uç noktaları ve kuralları için Azure veritabanı oluşturun ve yönetin | Microsoft Docs
description: Azure portal kullanarak MariaDB VNet hizmet uç noktaları ve kuralları için Azure veritabanı oluşturma ve yönetme
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 392428c0498458f49c439a0553d581a302c0c242
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610103"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure portal kullanarak MariaDB VNet hizmet uç noktaları ve VNet kuralları için Azure veritabanı oluşturun ve yönetin

Sanal ağ (VNet) Hizmetleri uç noktaları ve kuralları, bir sanal ağın özel adres alanını MariaDB sunucusu için Azure veritabanınıza genişletir. MariaDB VNet hizmet uç noktaları için Azure veritabanı 'nda sınırlamalar da dahil genel bir bakış için bkz. [MariaDB sunucu VNET hizmet uç noktaları Için Azure veritabanı](concepts-data-access-security-vnet.md). Sanal ağ hizmeti uç noktaları, MariaDB için Azure veritabanı için desteklenen tüm bölgelerde kullanılabilir.

> [!NOTE]
> Yalnızca genel amaçlı ve bellek için iyileştirilmiş sunucuları için sanal ağ hizmet uç noktaları desteğidir.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>VNet kuralı oluşturma ve hizmet uç noktalarını etkinleştirme

1. MariaDB sunucusu sayfasında, ayarlar başlığı altında **bağlantı güvenliği** ' ne tıklayarak MariaDB Için Azure veritabanı bağlantı güvenliği bölmesini açın.

2. Azure hizmetleri denetimine erişime Izin ver ' in **kapalı**olarak ayarlandığından emin olun.

> [!Important]
> AÇıK olarak ayarlarsanız, Azure MariaDB veritabanı sunucunuz herhangi bir alt ağdan iletişim kabul eder. Denetim kümesini açık olarak bırakmak, bir güvenlik noktasından aşırı erişim sağlayabilir. Microsoft Azure Sanal Ağ Hizmeti uç noktası özelliği, MariaDB için Azure veritabanı 'nın sanal ağ kuralı özelliğiyle birlikte, güvenlik yüzeyi alanınızı azaltabilir.

3. Ardından, **var olan sanal ağı ekleme**' ye tıklayın. Mevcut bir VNet 'iniz yoksa bir tane oluşturmak için **+ Yeni sanal ağ oluştur ' a** tıklayabilirsiniz. Bkz [. hızlı başlangıç: Azure portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

   ![Azure portal-bağlantı güvenliği ' ne tıklayın](./media/howto-manage-vnet-portal/1-connection-security.png)

4. VNet kural adı girin, abonelik, sanal ağ ve alt ağ adını seçin ve ardından **Etkinleştir**' e tıklayın. Bu, alt ağdaki VNet hizmet uç noktalarını **Microsoft. SQL** hizmet etiketi kullanılarak otomatik olarak sunar.

   ![Azure portal-sanal ağı yapılandırma](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Hesap, bir sanal ağ ve hizmet uç noktası oluşturmak için gerekli izinlere sahip olmalıdır.

   Hizmet uç noktaları sanal ağ üzerinde, sanal ağa yazma erişimi olan bir kullanıcı tarafından bağımsız olarak yapılandırılabilir.
    
   Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için, eklenmekte olan alt ağlar için kullanıcının "Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/" iznine sahip olması gerekir. Bu izin varsayılan olarak yerleşik hizmet yöneticisi rollerinde mevcuttur ve özel roller oluşturularak değiştirilebilir.
    
   [Yerleşik roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) ve [özel rollere](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) belirli izinlerin atanması hakkında daha fazla bilgi edinin.
    
   Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde olabilir. VNet ve Azure hizmet kaynakları farklı aboneliklerdeyse, kaynakların aynı Active Directory (AD) kiracısı altında olması gerekir. Her iki aboneliğin de **Microsoft. SQL** kaynak sağlayıcısı 'nın kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

   > [!IMPORTANT]
   > Hizmet uç noktalarını yapılandırmadan önce hizmet uç noktası yapılandırmalarına ve noktalara ilişkin bu makalenin okunması kesinlikle önerilir. **Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) , özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağıdır. VNet Hizmetleri uç noktaları, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL**hizmet türü adını kullanır. Bu hizmet etiketi, Azure SQL veritabanı, MariaDB için Azure veritabanı, PostgreSQL ve MySQL Hizmetleri için de geçerlidir. **Microsoft. SQL** hizmet etiketi bir sanal ağ hizmeti uç noktasına uygulandığında, Azure SQL veritabanı, PostgreSQL Için Azure veritabanı, Için Azure veritabanı dahil olmak üzere tüm Azure veritabanı hizmetleri için hizmet uç noktası trafiğini yapılandırır. MariaDB ve alt ağdaki MySQL sunucuları için Azure veritabanı.
   > 

5. Etkinleştirildikten sonra **Tamam** ' a tıkladığınızda, VNET hizmet uç noktalarının sanal ağ kuralıyla birlikte etkinleştirildiğini görürsünüz.

   ![VNet hizmet uç noktaları etkin ve VNet kuralı oluşturuldu](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Sonraki adımlar
- [MariaDB Için Azure veritabanı 'NDA SSL 'yi yapılandırma](howto-configure-ssl.md) hakkında daha fazla bilgi edinin
- Benzer şekilde, [VNET hizmet uç noktalarını etkinleştirmek ve Azure CLI kullanarak MariaDB Için Azure veritabanı için BIR VNET kuralı oluşturmak](howto-manage-vnet-cli.md)üzere komut dosyası oluşturabilirsiniz.

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md