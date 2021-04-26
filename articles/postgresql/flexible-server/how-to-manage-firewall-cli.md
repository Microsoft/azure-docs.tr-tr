---
title: Güvenlik duvarı kurallarını yönetme-Azure CLı-PostgreSQL için Azure veritabanı-esnek sunucu
description: PostgreSQL için Azure veritabanı-Azure CLı komut satırını kullanarak esnek sunucu için güvenlik duvarı kuralları oluşturun ve yönetin.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: beed3dac1a2ca5bc6d2a87ba2a9044333e798fa9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778526"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Azure CLı kullanarak PostgreSQL için Azure veritabanı oluşturma ve yönetme-esnek sunucu güvenlik duvarı kuralları

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure veritabanı-esnek sunucu, esnek sunucunuza bağlanmak için iki tür karşılıklı kullanım dışı ağ bağlantısı yöntemini destekler. Bu iki seçenek şunlardır:

* Genel erişim (izin verilen IP adresleri)
* Özel erişim (Sanal Ağ Tümleştirmesi)

Bu makalede, Azure CLı kullanarak **genel erişime (izin VERILEN IP adresleri)** sahip PostgreSQL sunucusu oluşturmaya odaklanacağız ve sunucu oluşturulduktan sonra güvenlik duvarı kuralları oluşturmak, güncelleştirmek, silmek, listelemek ve göstermek Için KULLANABILECEĞINIZ Azure CLI komutlarına genel bir bakış sağlar. *Ortak erişim (izin VERILEN IP adresleri)* Ile, PostgreSQL sunucusuna yapılan bağlantılar yalnızca ızın verilen IP adresleriyle kısıtlıdır. Güvenlik duvarı kurallarında istemci IP adreslerine izin verilmesi gerekir. Daha fazla bilgi edinmek için bkz. [genel erişim (izin VERILEN IP adresleri)](./concepts-networking.md#public-access-allowed-ip-addresses). Güvenlik duvarı kuralları sunucu oluşturma sırasında tanımlanabilir (önerilir), ancak daha sonra da eklenebilir.

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

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Azure CLı kullanarak esnek sunucu oluşturma sırasında güvenlik duvarı kuralı oluşturma

`az postgres flexible-server --public access`Komutunu, *ortak erişim (ızın verilen IP adresleri)* ile esnek sunucuyu oluşturmak ve esnek sunucu oluşturma sırasında güvenlik duvarı kurallarını yapılandırmak için kullanabilirsiniz. Sunucuya bağlanabilecek izin verilen IP adreslerini sağlamak için **--Public-Access** anahtarını kullanabilirsiniz. İzin verilen IP 'Ler listesine dahil edilecek tek veya IP adresi aralığı sağlayabilirsiniz. IP adresi aralığı tire ile ayrılmış olmalı ve boşluk içermemelidir. Aşağıdaki örnekte gösterildiği gibi CLı kullanarak esnek sunucu oluşturmaya yönelik çeşitli seçenekler vardır.

Azure CLı başvuru belgelerine başvurun <!--FIXME --> yapılandırılabilir CLı parametrelerinin tüm listesi için. Örneğin, aşağıdaki komutlarda kaynak grubunu isteğe bağlı olarak belirtebilirsiniz.

- Ortak erişime sahip esnek bir sunucu oluşturun ve sunucuya erişimi olan istemci IP adresini ekleyin
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Ortak erişime sahip esnek bir sunucu oluşturun ve bu sunucuya erişim sağlamak için IP adresi aralığını ekleyin

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Ortak erişime sahip esnek bir sunucu oluşturun ve Azure IP adreslerinden gelen uygulamaların esnek sunucunuza bağlanmasına izin verin
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Bu seçenek, güvenlik duvarını, diğer müşterilerin aboneliklerinden gelen bağlantılar da dahil olmak üzere Azure hizmetleri ve kaynaklarından bu sunucuya genel erişime izin verecek şekilde yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
    >
- - Ortak erişime sahip esnek sunucu oluşturma ve tüm IP adreslerine izin verme
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Yukarıdaki komut, başlangıç IP adresi = 0.0.0.0, bitiş IP adresi = 255.255.255.255 ve IP adresi engellenmeyecek bir güvenlik duvarı kuralı oluşturur. Internet üzerindeki herhangi bir ana bilgisayar bu sunucuya erişebilir. Bu kural yalnızca geçici olarak ve yalnızca hassas veriler içermeyen test sunucularında kullanılması önemle önerilir.
- Ortak erişime sahip ve IP adresi olmayan esnek sunucu oluşturma
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > herhangi bir güvenlik duvarı kuralı olmadan sunucu oluşturmanız önerilmez. Herhangi bir güvenlik duvarı kuralı eklemedıysanız, hiçbir istemci sunucuya bağlanamaz.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Sunucu oluşturulduktan sonra güvenlik duvarı kuralı oluştur ve Yönet
**Az Postgres esnek-sunucu güvenlik duvarı-kuralı** komutu, güvenlik duvarı kuralları oluşturma, silme, listeleme, gösterme ve güncelleştirme IÇIN Azure CLI 'dan kullanılır.

Komut
- **Oluştur**: esnek sunucu güvenlik duvarı kuralı oluşturma.
- **liste**: esnek sunucu güvenlik duvarı kurallarını listeleyin.
- **güncelleştirme**: esnek sunucu güvenlik duvarı kuralını güncelleştirin.
- **göster**: esnek sunucu güvenlik duvarı kuralının ayrıntılarını göster.
- **Sil**: esnek sunucu güvenlik duvarı kuralını silin.

Azure CLı başvuru belgelerine başvurun <!--FIXME --> yapılandırılabilir CLı parametrelerinin tüm listesi için. Örneğin, aşağıdaki komutlarda kaynak grubunu isteğe bağlı olarak belirtebilirsiniz.

### <a name="create-a-firewall-rule"></a>Güvenlik duvarı kuralı oluşturma
`az postgres flexible-server firewall-rule create`Sunucuda yeni güvenlik duvarı kuralı oluşturmak için komutunu kullanın.
Bir IP adresi aralığına erişime izin vermek için, bu örnekte olduğu gibi, başlangıç IP adresi ve bitiş IP adresi olarak IP adresini sağlayın.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Tek bir IP adresi için erişime izin vermek üzere, bu örnekte olduğu gibi tek bir IP adresi sağlamanız yeterlidir.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Azure IP adreslerinden gelen uygulamaların esnek sunucunuza bağlanmasına izin vermek için, bu örnekte olduğu gibi 0.0.0.0 IP adresini başlangıç IP 'si olarak sağlayın.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Bu seçenek, güvenlik duvarını, diğer müşterilerin aboneliklerinden gelen bağlantılar da dahil olmak üzere Azure hizmetleri ve kaynaklarından bu sunucuya genel erişime izin verecek şekilde yapılandırır. Bu seçeneği belirlerken, oturum açma ve kullanıcı izinlerinizin erişimi yalnızca yetkili kullanıcılarla sınırladığından emin olun.
> 
Başarılı olduğunda, her bir komut çıkışı oluşturma, oluşturduğunuz güvenlik duvarı kuralının (varsayılan olarak) JSON biçiminde ayrıntılarını listeler. Bir hata oluşursa, çıkış bunun yerine hata iletisi metnini gösterir.

### <a name="list-firewall-rules"></a>Güvenlik duvarı kurallarını Listele 
`az postgres flexible-server firewall-rule list`Sunucuda var olan sunucu güvenlik duvarı kurallarını listelemek için komutunu kullanın. Sunucu adı özniteliğinin **--Name** anahtarında belirtildiğine dikkat edin. 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
Çıktı, varsa, JSON biçiminde (varsayılan olarak) kuralları listeler. Sonuçları daha okunabilir bir tablo biçiminde çıkarmak için--output tablosu * * anahtarını kullanabilirsiniz.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Güvenlik duvarı kuralını güncelleştirme
`az postgres flexible-server firewall-rule update`Sunucuda var olan bir güvenlik duvarı kuralını güncelleştirmek için komutunu kullanın. Güncelleştirilecek, başlangıç IP adresi ve bitiş IP adresi özniteliklerinin yanı sıra mevcut güvenlik duvarı kuralının adını girin.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Başarılı olduğunda, komut çıktısı güncelleştirdiğiniz güvenlik duvarı kuralının ayrıntılarını JSON biçiminde (varsayılan olarak) listeler. Bir hata oluşursa, çıkış bunun yerine hata iletisi metnini gösterir.

> [!NOTE]
> Güvenlik duvarı kuralı yoksa, kural Update komutu tarafından oluşturulur.
### <a name="show-firewall-rule-details"></a>Güvenlik duvarı kuralı ayrıntılarını göster
`az postgres flexible-server firewall-rule show`Sunucudan mevcut güvenlik duvarı kuralı ayrıntılarını göstermek için komutunu kullanın. Mevcut güvenlik duvarı kuralının adını girdi olarak belirtin.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Başarılı olduğunda komut çıktısı, belirttiğiniz güvenlik duvarı kuralının, JSON biçiminde (varsayılan olarak) ayrıntılarını listeler. Bir hata oluşursa, çıkış bunun yerine hata iletisi metnini gösterir.

### <a name="delete-a-firewall-rule"></a>Güvenlik duvarı kuralını silme
`az postgres flexible-server firewall-rule delete`Sunucudan mevcut bir güvenlik duvarı kuralını silmek için komutunu kullanın. Mevcut güvenlik duvarı kuralının adını belirtin.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Başarılı olduğunda, çıkış yok. Hata durumunda hata iletisi metni görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar
- [PostgreSQL Için Azure veritabanı 'Nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin-esnek sunucu
- [PostgreSQL Için Azure veritabanı-esnek sunucu güvenlik duvarı kuralları](./concepts-networking.md#public-access-allowed-ip-addresses) hakkında daha fazla bilgi edinin
- [Azure Portal kullanarak PostgreSQL Için Azure veritabanı-esnek sunucu güvenlik duvarı kuralları oluşturun ve yönetin](./how-to-manage-firewall-portal.md).
