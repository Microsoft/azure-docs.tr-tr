---
title: VNet uç noktalarını yönetme-Azure CLı-MySQL için Azure veritabanı
description: Bu makalede, Azure CLı komut satırını kullanarak MySQL VNet hizmet uç noktaları ve kuralları için Azure veritabanı 'nın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bc686efbd07cf39d7932b175b6f9800b1ff185f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774674"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Azure CLı kullanarak MySQL VNet için Azure veritabanı hizmet uç noktaları oluşturma ve yönetme
Sanal Ağ (VNet) hizmetleri uç noktaları ve kuralları, bir Sanal Ağın özel adres alanını MySQL için Azure Veritabanı sunucunuza genişletir. Uygun Azure komut satırı arabirimi (CLı) komutlarını kullanarak sunucunuzu yönetmek için VNet hizmet uç noktalarını ve kurallarını oluşturabilir, güncelleştirebilir, silebilir, listeleyebilir ve gösterebilirsiniz. MySQL VNet hizmet uç noktaları için Azure veritabanı 'nda sınırlamalar da dahil genel bir bakış için bkz. [MySQL Için Azure veritabanı sunucu VNET hizmet uç noktaları](concepts-data-access-and-security-vnet.md). Sanal ağ hizmeti uç noktaları, MySQL için Azure veritabanı için desteklenen tüm bölgelerde kullanılabilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- [MySQL Için Azure veritabanı sunucusu ve veritabanı](quickstart-create-mysql-server-database-using-azure-cli.md)gerekir.
 
- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

> [!NOTE]
> VNet hizmet uç noktaları için destek yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş sunucular içindir.
> VNet eşlemesi söz konusu olduğunda trafik, hizmet uç noktaları içeren bir ortak VNet ağ geçidi üzerinden akar ve eşe akışı gerekiyorsa, ağ geçidi VNet 'teki Azure sanal makinelerinin MySQL sunucusuna Azure veritabanı 'na erişmesine izin vermek için lütfen bir ACL/VNet kuralı oluşturun.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı için sanal ağ hizmet uç noktalarını yapılandırma
[Az Network VNET](/cli/azure/network/vnet) komutları, sanal ağları yapılandırmak için kullanılır.

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](/cli/azure/account#az_account_set) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. Abonelik KIMLIĞI yer tutucusuna aboneliğiniz için **az Login** çıktısından **ID** özelliğini değiştirin.

- Hesap, sanal ağ ve hizmet uç noktası oluşturma izinlerine sahip olmalıdır.

Hizmet uç noktaları sanal ağ üzerinde, sanal ağa yazma erişimi olan bir kullanıcı tarafından bağımsız olarak yapılandırılabilir.

Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için, eklenmekte olan alt ağlar için kullanıcının "Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/" iznine sahip olması gerekir. Bu izin varsayılan olarak yerleşik hizmet yöneticisi rollerinde mevcuttur ve özel roller oluşturularak değiştirilebilir.

[Yerleşik roller](../role-based-access-control/built-in-roles.md) ve [özel rollere](../role-based-access-control/custom-roles.md) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde olabilir. VNet ve Azure hizmet kaynakları farklı aboneliklerdeyse, kaynakların aynı Active Directory (AD) kiracısı altında olması gerekir. Her iki aboneliğin de **Microsoft. SQL** kaynak sağlayıcısı 'nın kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

> [!IMPORTANT]
> Aşağıdaki örnek betiği çalıştırmadan veya hizmet uç noktalarını yapılandırmadan önce, hizmet uç noktası yapılandırmalarına ve noktalara ilişkin bu makaleye yönelik bu makaleyi okumanız önemle tavsiye edilir. **Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) , özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağıdır. VNet Hizmetleri uç noktaları, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL** hizmet türü adını kullanır. Bu hizmet etiketi, Azure SQL veritabanı, PostgreSQL için Azure veritabanı ve MySQL Hizmetleri için de geçerlidir. **Microsoft. SQL** hizmet etiketi bir sanal ağ hizmeti uç noktasına uygulandığında, Azure SQL veritabanı, PostgreSQL Için Azure veritabanı ve alt ağdaki MySQL sunucuları Için Azure veritabanı dahil olmak üzere tüm Azure veritabanı hizmetleri için hizmet uç noktası trafiğini yapılandırır. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Örnek betik bir MySQL için Azure veritabanı veritabanı oluşturma, VNet, VNet hizmeti uç noktası oluşturma ve sunucunun alt ağa VNet kuralıyla güvenliğini sağlama
Bu örnek betikte, vurgulanan satırları değiştirerek yönetici kullanıcı adını ve parolasını özelleştirin. Komutta kullanılan SubscriptionID değerini `az account set --subscription` kendi abonelik tanımlayıcıınızla değiştirin.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
