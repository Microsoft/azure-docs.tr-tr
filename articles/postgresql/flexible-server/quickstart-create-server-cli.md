---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure CLı-PostgreSQL için Azure veritabanı-esnek sunucu'
description: Bu hızlı başlangıçta Azure CLı kullanarak bir Azure Kaynak grubunda PostgreSQL için Azure veritabanı esnek sunucusu oluşturma açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 320457365a36825564154c36ad843ef665fc2d2c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791562"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak PostgreSQL için Azure veritabanı esnek sunucusu oluşturma

Bu hızlı başlangıçta, beş dakika içinde PostgreSQL için Azure veritabanı esnek sunucusu oluşturmak üzere [Azure Cloud Shell](https://shell.azure.com) ' de [Azure CLI](/cli/azure/get-started-with-azure-cli) komutlarının nasıl kullanılacağı gösterilmektedir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!IMPORTANT] 
> PostgreSQL için Azure veritabanı esnek sunucu şu anda önizleme aşamasındadır.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

[Azure Cloud Shell](../../cloud-shell/overview.md) , bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de açabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve çalıştırmak için **ENTER** ' u seçin.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2,0 veya üzerini gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Önkoşullar

[Az Login](/cli/azure/reference-index#az_login) komutunu kullanarak hesabınızda oturum açmanız gerekir. Azure hesabınızın **ABONELIK kimliğini** ifade eden **ID** özelliğine göz önüne alın.

```azurecli-interactive
az login
```

[Az Account set](/cli/azure/account#az_account_set) komutunu kullanarak hesabınız altındaki belirli bir aboneliği seçin. Komutta **abonelik** bağımsız değişkeninin değeri olarak kullanılacak **az Login** çıktısından **ID** değerini bir yere unutmayın. Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. Aboneliğinizi tamamen almak için [az Account List](/cli/azure/account#az_account_list)kullanın.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Esnek sunucu oluşturma

Komutunu kullanarak bir [Azure Kaynak grubu](../../azure-resource-manager/management/overview.md) oluşturun `az group create` ve ardından bu kaynak grubunun Içinde PostgreSQL esnek sunucunuzu oluşturun. Benzersiz bir ad sağlamanız gerekir. Aşağıdaki örnek `westus` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Komutuyla esnek bir sunucu oluşturun `az postgres flexible-server create` . Bir sunucu birden çok veritabanı içerebilir. Aşağıdaki komut, Azure CLı 'nın [Yerel bağlamından](/cli/azure/local-context)hizmet varsayılanlarını ve değerlerini kullanarak bir sunucu oluşturur: 

```azurecli
az postgres flexible-server create
```

Oluşturulan sunucu aşağıdaki özniteliklere sahiptir: 
- Otomatik olarak oluşturulan sunucu adı, Yönetici Kullanıcı adı, yönetici parolası, kaynak grubu adı (zaten yerel bağlamda belirtilmemişse) ve kaynak grubunuzda aynı konumda 
- Kalan sunucu yapılandırmalarının hizmet Varsayılanları: İşlem Katmanı (Genel Amaçlı), işlem boyutu/SKU (D2s_v3-2 sanal çekirdek, 8 GB RAM), yedekleme saklama süresi (7 gün) ve PostgreSQL sürümü (12)
- Varsayılan bağlantı yöntemi, otomatik olarak oluşturulan bir sanal ağ ve alt ağ ile özel erişime (VNet tümleştirme) sahiptir

> [!NOTE] 
> Sunucu oluşturulduktan sonra bağlantı yöntemi değiştirilemez. Örneğin, oluştur sırasında *özel erişim (VNET tümleştirmesi)* seçtiyseniz, oluşturulduktan sonra *ortak erişime (izin verilen IP adresleri)* geçiş yapılamaz. VNet tümleştirmesini kullanarak sunucunuza güvenli bir şekilde erişmek için özel erişime sahip bir sunucu oluşturmanız önemle önerilir. [Kavramlar makalesinde](./concepts-networking.md)özel erişim hakkında daha fazla bilgi edinin.

Herhangi bir Varsayılanı değiştirmek istiyorsanız lütfen Azure CLı başvuru belgelerine başvurun <!--FIXME --> yapılandırılabilir CLı parametrelerinin tüm listesi için. 

> [!NOTE]
> PostgreSQL için Azure veritabanı bağlantıları 5432 numaralı bağlantı noktası üzerinden iletişim kurar. Bir kurumsal ağ içinden bağlanmaya çalışırsanız, 5432 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 5432 numaralı bağlantı noktasını açmadığı müddetçe sunucunuza bağlanamazsınız.

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **fullyQualifiedDomainName** ve **administratorLogin** bilgilerini not alın.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>PostgreSQL kullanarak bağlanma komut satırı istemcisi

Esnek sunucu *özel erişim (VNET tümleştirmesi)* ile oluşturulduğundan, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturup oluşturulan sanal ağa ekleyebilirsiniz. 

VM 'niz oluşturulduktan sonra makineye SSH oluşturabilir ve **[psql](https://www.postgresql.org/download/)** komut satırı aracını yükleyebilirsiniz.

Psql ile aşağıdaki komutu kullanarak bağlanın. Değerleri gerçek sunucu adı ve parolasıyla değiştirin. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu kaynaklara başka bir hızlı başlangıç/öğretici için gereksinim duymuyorsanız aşağıdaki komutu çalıştırarak kaynakları silebilirsiniz:

```azurecli-interactive
az group delete --name myresourcegroup
```

Yalnızca yeni oluşturulan sunucuyu silmek istiyorsanız `az postgres flexible-server delete` komutunu çalıştırabilirsiniz.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[App Service ve PostgreSQL ile Docgo uygulaması dağıtma](tutorial-django-app-service-postgres.md)
