---
title: Yönetilen örnek için bir sanal ağ oluşturma
description: Bu makalede, Azure SQL veritabanı yönetilen örneğini dağıtabileceğiniz bir sanal ağın nasıl oluşturulacağı açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 9f115d51657993562642391a235de79420aa434a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823372"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği için sanal ağ oluşturma

Bu makalede, Azure SQL veritabanı yönetilen örneğini dağıtabileceğiniz geçerli bir sanal ağ ve alt ağ oluşturma açıklanır.

Azure SQL veritabanı yönetilen örneği bir Azure [sanal ağı](../virtual-network/virtual-networks-overview.md)içinde dağıtılmalıdır. Bu dağıtım, aşağıdaki senaryolara izin vermez:

- Güvenli özel IP adresi
- Yönetilen bir örneğe doğrudan şirket içi ağdan bağlanma
- Yönetilen bir örneği bağlantılı sunucuya veya başka bir şirket içi veri deposuna bağlama
- Yönetilen bir örneği Azure kaynaklarına bağlama  

> [!Note]
> İlk örneği dağıtmadan önce, [yönetilen örnek için alt ağın boyutunu belirlemelisiniz](sql-database-managed-instance-determine-size-vnet-subnet.md) . Kaynakları içine koyduktan sonra alt ağı yeniden boyutlandıramazsınız.
>
> Var olan bir sanal ağı kullanmayı planlıyorsanız, bu ağ yapılandırmasını yönetilen örneğinizi kapsayacak şekilde değiştirmeniz gerekir. Daha fazla bilgi için bkz. [yönetilen örnek için var olan bir sanal ağı değiştirme](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Yönetilen bir örnek oluşturulduktan sonra, yönetilen örneği veya VNet 'i başka bir kaynak grubuna veya aboneliğe taşımak desteklenmez.


## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Bir sanal ağ oluşturmanın ve yapılandırmanın en kolay yolu Azure Resource Manager dağıtım şablonu kullanmaktır.

1. Azure Portal’da oturum açın.

2. **Azure 'A dağıt** düğmesini seçin:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Bu düğme, yönetilen örneği dağıtabileceğiniz ağ ortamını yapılandırmak için kullanabileceğiniz bir form açar.

   > [!Note]
   > Bu Azure Resource Manager şablonu, iki alt ağa sahip bir sanal ağ dağıtır. **ManagedInstances**adlı bir alt ağ, yönetilen örnek için ayrılmıştır ve önceden yapılandırılmış bir yol tablosuna sahiptir. **Varsayılan**olarak adlandırılan diğer alt ağ, yönetilen örneğe erişmesi gereken diğer kaynaklar için kullanılır (örneğin, Azure sanal makineler).

3. Ağ ortamını yapılandırın. Aşağıdaki biçimde, ağ ortamınızın parametrelerini yapılandırabilirsiniz:

   ![Azure ağını yapılandırmak için Kaynak Yöneticisi şablonu](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Sanal ağ ve alt ağların adlarını değiştirebilir ve ağ kaynaklarınızla ilişkili IP aralıklarını ayarlayabilirsiniz. **Satın alma** düğmesini seçtikten sonra bu form ortamınızı oluşturur ve yapılandırır. İki alt ağa ihtiyacınız yoksa, varsayılan olanı silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [yönetilen örnek nedir?](sql-database-managed-instance.md).
- [Yönetilen örnekteki bağlantı mimarisi](sql-database-managed-instance-connectivity-architecture.md)hakkında bilgi edinin.
- [Yönetilen örnek için var olan bir sanal ağı değiştirme](sql-database-managed-instance-configure-vnet-subnet.md)hakkında bilgi edinin.
- Bir sanal ağ oluşturmayı, yönetilen bir örnek oluşturmayı ve bir veritabanını bir veritabanı yedeklemesinden geri yüklemeyi gösteren bir öğretici için bkz. [Azure SQL veritabanı yönetilen örneği oluşturma](sql-database-managed-instance-get-started.md).
- DNS sorunları için bkz. [Özel BIR DNS yapılandırma](sql-database-managed-instance-custom-dns.md).
