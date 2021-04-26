---
title: Sanal ağ kurallarını kullanma-Azure CLı-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, Azure CLı komut satırı kullanılarak PostgreSQL için Azure veritabanı için VNet hizmet uç noktaları ve kuralları oluşturma ve yönetme açıklanır.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0de1f00823bd34e18a727b8e2929f64a8769c93a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789906"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Azure CLı kullanarak PostgreSQL için Azure veritabanı için sanal ağ hizmet uç noktaları oluşturma ve yönetme-tek sunucu
Sanal ağ (VNet) Hizmetleri uç noktaları ve kuralları, bir sanal ağın özel adres alanını PostgreSQL için Azure veritabanı sunucusuna genişletir. Uygun Azure komut satırı arabirimi (CLı) komutlarını kullanarak sunucunuzu yönetmek için VNet hizmet uç noktalarını ve kurallarını oluşturabilir, güncelleştirebilir, silebilir, listeleyebilir ve gösterebilirsiniz. PostgreSQL için Azure veritabanı VNet hizmeti uç noktalarına yönelik sınırlamalar da dahil olmak üzere, bkz. [PostgreSQL Için Azure veritabanı sunucu VNET hizmet uç noktaları](concepts-data-access-and-security-vnet.md). VNet hizmet uç noktaları, PostgreSQL için Azure veritabanı için desteklenen tüm bölgelerde kullanılabilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için:
- [Azure CLI](/cli/azure/install-azure-cli) 'yı yükler veya tarayıcıda Azure Cloud Shell kullanın.
- [PostgreSQL Için Azure veritabanı sunucusu ve veritabanı](quickstart-create-server-database-azure-cli.md)oluşturun.

    > [!NOTE]
    > VNet hizmet uç noktaları için destek yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş sunucular içindir.
    > VNet eşlemesi söz konusu olduğunda trafik, hizmet uç noktaları içeren bir ortak VNet ağ geçidi üzerinden akar ve eşe akışı gerekiyorsa, ağ geçidi VNet 'teki Azure sanal makinelerinin PostgreSQL için Azure veritabanı sunucusuna erişmesine izin vermek için lütfen bir ACL/VNet kuralı oluşturun.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı için sanal ağ hizmet uç noktalarını yapılandırma
[Az Network VNET](/cli/azure/network/vnet) komutları, sanal ağları yapılandırmak için kullanılır.

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](/cli/azure/account#az_account_set) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. Aboneliğinizin **az login** çıktısındaki **id** özelliğini abonelik kimliği yer tutucusuyla değiştirin.

- Hesap, sanal ağ ve hizmet uç noktası oluşturma izinlerine sahip olmalıdır.

Hizmet uç noktaları sanal ağ üzerinde, sanal ağa yazma erişimi olan bir kullanıcı tarafından bağımsız olarak yapılandırılabilir.

Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için, eklenmekte olan alt ağlar için kullanıcının "Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/" iznine sahip olması gerekir. Bu izin varsayılan olarak yerleşik hizmet yöneticisi rollerinde mevcuttur ve özel roller oluşturularak değiştirilebilir.

[Yerleşik roller](../role-based-access-control/built-in-roles.md) ve [özel rollere](../role-based-access-control/custom-roles.md) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde olabilir. VNet ve Azure hizmet kaynakları farklı aboneliklerdeyse, kaynakların aynı Active Directory (AD) kiracısı altında olması gerekir. Her iki aboneliğin de **Microsoft. SQL** kaynak sağlayıcısı 'nın kayıtlı olduğundan emin olun. Daha fazla bilgi için bkz. [Resource-Manager-kayıt][resource-manager-portal].

> [!IMPORTANT]
> Aşağıdaki örnek betiği çalıştırmadan veya hizmet uç noktalarını yapılandırmadan önce, hizmet uç noktası yapılandırmalarına ve noktalara ilişkin bu makaleye yönelik bu makaleyi okumanız önemle tavsiye edilir. **Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) , özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağıdır. VNet Hizmetleri uç noktaları, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL** hizmet türü adını kullanır. Bu hizmet etiketi, Azure SQL veritabanı, PostgreSQL için Azure veritabanı ve MySQL Hizmetleri için de geçerlidir. **Microsoft. SQL** hizmet etiketi bir sanal ağ hizmeti uç noktasına uygulandığında, Azure SQL veritabanı, PostgreSQL Için Azure veritabanı ve alt ağdaki MySQL sunucuları Için Azure veritabanı dahil olmak üzere tüm Azure veritabanı hizmetleri için hizmet uç noktası trafiğini yapılandırır. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Örnek betik PostgreSQL için Azure veritabanı veritabanı oluşturma, VNet, VNet hizmeti uç noktası oluşturma ve sunucunun alt ağa VNet kuralıyla güvenliğini sağlama
Bu örnek betikte, vurgulanan satırları değiştirerek yönetici kullanıcı adını ve parolasını özelleştirin. Komutta kullanılan SubscriptionID değerini `az account set --subscription` kendi abonelik tanımlayıcıınızla değiştirin.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
