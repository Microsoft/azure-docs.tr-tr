---
title: Azure CLı kullanarak MySQL VNet için Azure veritabanı hizmet uç noktaları ve kuralları oluşturma ve yönetme | Microsoft Docs
description: Bu makalede, Azure CLı komut satırını kullanarak MySQL VNet hizmet uç noktaları ve kuralları için Azure veritabanı 'nın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: d08b99c0a668286d9e9b94f2229915be8ff106f0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610398"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Azure CLı kullanarak MySQL VNet için Azure veritabanı hizmet uç noktaları oluşturma ve yönetme
Sanal Ağ (VNet) hizmetleri uç noktaları ve kuralları, bir Sanal Ağın özel adres alanını MySQL için Azure Veritabanı sunucunuza genişletir. Uygun Azure komut satırı arabirimi (CLı) komutlarını kullanarak sunucunuzu yönetmek için VNet hizmet uç noktalarını ve kurallarını oluşturabilir, güncelleştirebilir, silebilir, listeleyebilir ve gösterebilirsiniz. MySQL VNet hizmet uç noktaları için Azure veritabanı 'nda sınırlamalar da dahil genel bir bakış için bkz. [MySQL Için Azure veritabanı sunucu VNET hizmet uç noktaları](concepts-data-access-and-security-vnet.md). Sanal ağ hizmeti uç noktaları, MySQL için Azure veritabanı için desteklenen tüm bölgelerde kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- [Azure CLI](/cli/azure/install-azure-cli) 'yı yükler veya tarayıcıda Azure Cloud Shell kullanın.
- Bir [MySQL sunucusu ve veritabanı için Azure veritabanı](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Yalnızca genel amaçlı ve bellek için iyileştirilmiş sunucuları için sanal ağ hizmet uç noktaları desteğidir.
> VNet eşlemesi söz konusu olduğunda trafik, hizmet uç noktaları içeren bir ortak VNet ağ geçidi üzerinden akar ve eşe akışı gerekiyorsa, ağ geçidi VNet 'teki Azure sanal makinelerinin MySQL sunucusuna Azure veritabanı 'na erişmesine izin vermek için lütfen bir ACL/VNet kuralı oluşturun.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı için sanal ağ hizmet uç noktalarını yapılandırma
[Az Network VNET](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) komutları, sanal ağları yapılandırmak için kullanılır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Yüklü sürümü görmek için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli). 

CLI’yi yerel olarak çalıştırıyorsanız, [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) komutunu kullanarak hesabınızda oturum açmanız gerekir. Komut çıktısındaki ilgili abonelik adına karşılık gelen **id** özelliğinin değerini not edin.
```azurecli-interactive
az login
```

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. Aboneliğinizin **az login** çıktısındaki **id** özelliğini abonelik kimliği yer tutucusuyla değiştirin.

- Hesap, bir sanal ağ ve hizmet uç noktası oluşturmak için gerekli izinlere sahip olmalıdır.

Hizmet uç noktaları sanal ağ üzerinde, sanal ağa yazma erişimi olan bir kullanıcı tarafından bağımsız olarak yapılandırılabilir.

Azure hizmet kaynaklarını bir sanal ağa güvenli hale getirmek için, eklenmekte olan alt ağlar için kullanıcının "Microsoft. Network/virtualNetworks/alt ağları/Jodavetli Aserviceendpoint/" iznine sahip olması gerekir. Bu izin varsayılan olarak yerleşik hizmet yöneticisi rollerinde mevcuttur ve özel roller oluşturularak değiştirilebilir.

[Yerleşik roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) ve [özel rollere](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

Sanal ağlar ve Azure hizmet kaynakları aynı ağda veya farklı aboneliklerde olabilir. VNet ve Azure hizmet kaynakları farklı aboneliklerdeyse, kaynakların aynı Active Directory (AD) kiracısı altında olması gerekir. Her iki aboneliğin de **Microsoft. SQL** kaynak sağlayıcısı 'nın kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

> [!IMPORTANT]
> Aşağıdaki örnek betiği çalıştırmadan veya hizmet uç noktalarını yapılandırmadan önce, hizmet uç noktası yapılandırmalarına ve noktalara ilişkin bu makaleye yönelik bu makaleyi okumanız önemle tavsiye edilir. **Sanal ağ hizmeti uç noktası:** [Sanal ağ hizmeti uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) , özellik değerleri bir veya daha fazla resmi Azure hizmet türü adı içeren bir alt ağıdır. VNet Hizmetleri uç noktaları, SQL veritabanı adlı Azure hizmetine başvuran **Microsoft. SQL**hizmet türü adını kullanır. Bu hizmet etiketi, Azure SQL veritabanı, PostgreSQL için Azure veritabanı ve MySQL Hizmetleri için de geçerlidir. **Microsoft. SQL** hizmet etiketi bir sanal ağ hizmeti uç noktasına uygulandığında, Azure SQL veritabanı, PostgreSQL Için Azure veritabanı ve Için Azure veritabanı dahil olmak üzere tüm Azure veritabanı hizmetleri için hizmet uç noktası trafiğini yapılandırır. Alt ağdaki MySQL sunucuları. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Örnek betik bir MySQL için Azure veritabanı veritabanı oluşturma, VNet, VNet hizmeti uç noktası oluşturma ve sunucunun alt ağa VNet kuralıyla güvenliğini sağlama
Bu örnek betikte, vurgulanan satırları değiştirerek yönetici kullanıcı adını ve parolasını özelleştirin. `az account set --subscription` Komutta kullanılan SubscriptionID değerini kendi abonelik tanımlayıcıınızla değiştirin.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik örneği çalıştırıldıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komut kullanılabilir.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md

