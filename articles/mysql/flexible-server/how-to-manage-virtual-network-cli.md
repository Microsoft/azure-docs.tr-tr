---
title: Sanal ağları yönetme-Azure CLı-MySQL için Azure veritabanı-esnek sunucu
description: Azure CLı kullanarak MySQL için Azure veritabanı için sanal ağlar oluşturma ve yönetme-esnek sunucu
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7da8062f18d737af9d19df54863bc56c7268910c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776941"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı için sanal ağlar oluşturma ve yönetme-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı esnek sunucu şu anda genel önizlemede

MySQL için Azure Veritabanı Esnek Sunucusu, esnek sunucunuza bağlanmak için birbirini dışlayan iki tür ağ bağlantısı yöntemini destekler. Bu iki seçenek şunlardır:

- Genel erişim (izin verilen IP adresleri)
- Özel erişim (Sanal Ağ Tümleştirmesi)

Bu makalede, Azure CLı kullanarak **özel erişim (VNET tümleştirmesi)** ile MySQL sunucusu oluşturmaya odaklanacağız. *Özel erişim (VNET tümleştirmesi)* ile esnek sunucunuzu kendi [Azure sanal ağınıza](../../virtual-network/virtual-networks-overview.md)dağıtabilirsiniz. Azure sanal ağları özel ve güvenli ağ iletişimi sağlar. Özel erişim ' de MySQL sunucusuna olan bağlantılar yalnızca sanal ağınız dahilinde kısıtlıdır. Bu konuda daha fazla bilgi edinmek için bkz. [özel erişim (VNET tümleştirmesi)](./concepts-networking.md#private-access-vnet-integration).

MySQL için Azure veritabanı esnek sunucusu 'nda sunucu oluşturma sırasında sunucuyu yalnızca bir sanal ağa ve alt ağa dağıtabilirsiniz. Esnek sunucu bir sanal ağa ve alt ağa dağıtıldıktan sonra, başka bir sanal ağa, alt ağa veya *ortak erişime (izin VERILEN IP adresleri)* taşıyamazsınız.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

[Azure Cloud Shell](../../cloud-shell/overview.md) , bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de açabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve çalıştırmak için **ENTER** ' u seçin.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2,0 veya üzerini gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Önkoşullar

[Az Login](/cli/azure/reference-index#az_login) komutunu kullanarak hesabınızda oturum açmanız gerekir. Azure hesabınızın **ABONELIK kimliğini** Ifade eden **ID** özelliğine göz önüne alın.

```azurecli-interactive
az login
```

[Az Account set](/cli/azure/account#az_account_set) komutunu kullanarak hesabınız altındaki belirli bir aboneliği seçin. Komutta **abonelik** bağımsız değişkeninin değeri olarak kullanılacak **az Login** çıktısından **ID** değerini bir yere unutmayın. Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. Aboneliğinizi tamamen almak için [az Account List](/cli/azure/account#az_account_list)kullanın.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>CLı kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma
`az mysql flexible-server` *Özel erişim (VNET tümleştirmesi)* ile esnek sunucu oluşturmak için komutunu kullanabilirsiniz. Bu komut, varsayılan bağlantı yöntemi olarak özel erişim (VNet tümleştirmesi) kullanır. Hiçbiri sağlanmazsa, sizin için bir sanal ağ ve alt ağ oluşturulur. Zaten var olan sanal ağı ve alt ağı, alt ağ KIMLIĞINI kullanarak da sağlayabilirsiniz. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Aşağıdaki örneklerde gösterildiği gibi CLı kullanarak esnek sunucu oluşturmaya yönelik çeşitli seçenekler vardır.

>[!Important]
> Bu komutun kullanılması, alt ağı **Microsoft. DBforMySQL/Flexibtaservers**'a devredebilir. Bu temsil ilgili alt ağı yalnızca MySQL için Azure Veritabanı Esnek Sunucularının kullanabileceği anlamına gelir. Temsilci alt ağında diğer türdeki Azure kaynakları bulunamaz.
>

Yapılandırılabilir CLı parametrelerinin tüm listesi için Azure CLı [başvuru belgelerine](/cli/azure/mysql/flexible-server) bakın. Örneğin, aşağıdaki komutlarda kaynak grubunu isteğe bağlı olarak belirtebilirsiniz.

- Varsayılan sanal ağı kullanarak esnek sunucu oluşturma, varsayılan adres ön eki olan alt ağ
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Zaten var olan bir sanal ağı ve alt ağı kullanarak esnek bir sunucu oluşturun. Belirtilen sanal ağ ve alt ağ yoksa, varsayılan adres ön ekine sahip sanal ağ ve alt ağ oluşturulur.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Zaten var olan bir sanal ağ, alt ağ ve alt ağ KIMLIĞINI kullanarak esnek bir sunucu oluşturun. Belirtilen alt ağa dağıtılmış başka bir kaynak olmamalıdır ve bu alt ağ, henüz temsilci yoksa **Microsoft. Dbformısql/Flexibtaservers** için temsilci olarak atanacaktır.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Sanal ağ ve alt ağ, esnek sunucunuz ile aynı bölgede ve abonelikte olmalıdır.
<
- Yeni sanal ağ, varsayılan olmayan adres ön eki olan alt ağ kullanarak esnek bir sunucu oluşturun.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Yapılandırılabilir CLı parametrelerinin tüm listesi için Azure CLı [başvuru belgelerine](/cli/azure/mysql/flexible-server) bakın.


## <a name="next-steps"></a>Sonraki adımlar
- [MySQL Için Azure veritabanı esnek sunucusu 'nda ağ](./concepts-networking.md)hakkında daha fazla bilgi edinin.
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-portal.md).
- [MySQL Için Azure veritabanı esnek sunucu sanal ağı](./concepts-networking.md#private-access-vnet-integration)hakkında daha fazla bilgi edinin.