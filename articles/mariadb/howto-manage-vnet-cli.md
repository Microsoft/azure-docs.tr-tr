---
title: VNet uç noktalarını yönetme-Azure CLı-MariaDB için Azure veritabanı
description: Bu makalede, Azure CLı komut satırını kullanarak MariaDB VNet hizmet uç noktaları ve kuralları için Azure veritabanı 'nın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf87865fb2fc70251e1e417361333cfd750d6e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783674"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Azure CLı kullanarak MariaDB VNet hizmet uç noktaları için Azure veritabanı oluşturma ve yönetme

Sanal Ağ (VNet) hizmetleri uç noktaları ve kuralları, bir Sanal Ağın özel adres alanını MariaDB için Azure Veritabanı sunucunuza genişletir. Uygun Azure komut satırı arabirimi (CLı) komutlarını kullanarak sunucunuzu yönetmek için VNet hizmet uç noktalarını ve kurallarını oluşturabilir, güncelleştirebilir, silebilir, listeleyebilir ve gösterebilirsiniz. MariaDB VNet hizmet uç noktaları için Azure veritabanı 'nda sınırlamalar da dahil genel bir bakış için bkz. [MariaDB sunucu VNET hizmet uç noktaları Için Azure veritabanı](concepts-data-access-security-vnet.md). Sanal ağ hizmeti uç noktaları, MariaDB için Azure veritabanı için desteklenen tüm bölgelerde kullanılabilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- [MariaDB sunucusu ve veritabanı Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-cli.md)gerekir.

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

> [!NOTE]
> VNet hizmet uç noktaları için destek yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş sunucular içindir.

## <a name="configure-vnet-service-endpoints"></a>VNet hizmet uç noktalarını yapılandırma

[Az Network VNET](/cli/azure/network/vnet) komutları, sanal ağları yapılandırmak için kullanılır.

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](/cli/azure/account#az_account_set) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. Aboneliğinizin **az login** çıktısındaki **id** özelliğini abonelik kimliği yer tutucusuyla değiştirin.

- Hesap, sanal ağ ve hizmet uç noktası oluşturma izinlerine sahip olmalıdır.

Hizmet uç noktaları sanal ağ üzerinde, sanal ağa yazma erişimi olan bir kullanıcı tarafından bağımsız olarak yapılandırılabilir.

Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için, eklenmekte olan alt ağlar için kullanıcının "Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/" iznine sahip olması gerekir. Bu izin varsayılan olarak yerleşik hizmet yöneticisi rollerinde mevcuttur ve özel roller oluşturularak değiştirilebilir.

[Yerleşik roller](../role-based-access-control/built-in-roles.md) ve [özel rollere](../role-based-access-control/custom-roles.md) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde olabilir. VNet ve Azure hizmet kaynakları farklı aboneliklerdeyse, kaynakların aynı Active Directory (AD) kiracısı altında olması gerekir. Her iki aboneliğin de **Microsoft. SQL** kaynak sağlayıcısı 'nın kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

> [!IMPORTANT]
> Hizmet uç noktalarını yapılandırmadan önce hizmet uç noktası yapılandırmalarına ve noktalara ilişkin bu makalenin okunması kesinlikle önerilir. **Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) , özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağıdır. VNet Hizmetleri uç noktaları, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL** hizmet türü adını kullanır. Bu hizmet etiketi, Azure SQL veritabanı, MariaDB için Azure veritabanı, PostgreSQL ve MySQL Hizmetleri için de geçerlidir. **Microsoft. SQL** hizmet etiketi bir sanal ağ hizmeti uç noktasına uygulandığında, Azure SQL veritabanı, PostgreSQL Için Azure veritabanı, MariaDB için Azure veritabanı ve alt ağdaki MySQL sunucuları Için Azure veritabanı gibi tüm Azure veritabanı hizmetleri için hizmet uç noktası trafiğini yapılandırır.

### <a name="sample-script"></a>Örnek betik

Bu örnek betik, MariaDB sunucusu için Azure veritabanı oluşturmak, VNet, VNet hizmeti uç noktası oluşturmak ve sunucunun bir VNet kuralıyla alt ağa güvenliğini sağlamak için kullanılır. Bu örnek betikte yönetici kullanıcı adını ve parolasını değiştirin. Komutta kullanılan SubscriptionID değerini `az account set --subscription` kendi abonelik tanımlayıcıınızla değiştirin.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md