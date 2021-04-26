---
title: 'Hızlı başlangıç: sunucu oluşturma-az Postgres up-PostgreSQL için Azure veritabanı-tek sunucu'
description: Azure CLı (komut satırı arabirimi) up komutunu kullanarak PostgreSQL için Azure veritabanı oluşturma kılavuzu-tek sunucu.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0f0dc56dcaad73ff945d319cf98dc30483ee07e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875098"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Hızlı başlangıç: PostgreSQL için Azure veritabanı-tek sunucu oluşturmak için az Postgres up (Önizleme) adlı bir Azure CLı komutu kullanın

> [!IMPORTANT]
> [Az Postgres up](/cli/azure/postgres#az_postgres_up) Azure CLI komutu önizlemededir.

PostgreSQL için Azure Veritabanı, bulutta son derece kullanılabilir olan PostgreSQL veritabanları çalıştırmanızı, yönetmenizi ve ölçeklendirmenizi sağlayan ve yönetilen bir hizmettir. Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıç, Azure CLı kullanarak PostgreSQL için Azure veritabanı sunucusu oluşturmak üzere [az Postgres up](/cli/azure/postgres#az_postgres_up) komutunu nasıl kullanacağınızı gösterir. Komut, sunucu oluşturmaya ek olarak `az postgres up` bir örnek veritabanı, veritabanında bir kök kullanıcı oluşturur, Azure hizmetleri için güvenlik duvarını açar ve istemci bilgisayar için varsayılan güvenlik duvarı kuralları oluşturur. Bu varsayılanlar geliştirme sürecini hızlandırmeye yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

Bu makalede, Azure CLı sürüm 2,0 veya üstünü yerel olarak çalıştırıyor olmanız gerekir. Yüklü sürümü görmek için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

[Az Login](/cli/azure/authenticate-azure-cli) komutunu kullanarak hesabınızda oturum açmanız gerekir. Karşılık gelen abonelik adı için komut çıktısından **ID** özelliğine göz önünde edin.

```azurecli
az login
```

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](/cli/azure/account) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. **Abonelik kimliği özelliğini aboneliğiniz** için **az oturum açma** çıktısından abonelik kimliği yer tutucusuna koyun.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure Veritabanı sunucusu oluşturma

Komutları kullanmak için, [DB-up](/cli/azure/) uzantısını yükler. Bir hata döndürülürse, Azure CLı 'nin en son sürümünü yüklediğinizden emin olun. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Aşağıdaki komutu kullanarak PostgreSQL için Azure veritabanı sunucusu oluşturun:

```azurecli
az postgres up
```

Sunucu, aşağıdaki varsayılan değerlerle oluşturulur (bunları el ile geçersiz kılmadığınız müddetçe):

**Ayar** | **Varsayılan değer** | **Açıklama**
---|---|---
server-name | Sistem tarafından oluşturulan | PostgreSQL için Azure Veritabanı sunucunuzu tanıtan benzersiz bir ad.
resource-group | Sistem tarafından oluşturulan | Yeni bir Azure Kaynak grubu.
sku-name | GP_Gen5_2 | Sku'nun adı. Kısaca {fiyatlandırma katmanı}\_{işlem nesli}\_{sanal çekirdek sayısı} kuralına uyar. Varsayılan değer 2 sanal çekirdekte olan bir Genel Amaçlı 5. nesil sunucusudur. Katmanlar hakkında daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/postgresql/) .
backup-retention | 7 | Bir yedeklemenin ne kadar süreyle korunduğu. Birim olarak gün kullanılır.
geo-redundant-backup | Devre dışı | Coğrafi olarak yedekli yedeklemelerin bu sunucu için etkinleştirilip etkinleştirilmeyeceği.
location | westus2 | Sunucu için Azure konumu.
ssl-enforcement | Devre dışı | Bu sunucu için TLS/SSL 'nin etkinleştirilmesi gerekip gerekmediğini belirtir.
storage-size | 5120 | Sunucunun depolama kapasitesi (birim olan megabayt kullanılır).
sürüm | 10 | PostgreSQL ana sürümü.
admin-user | Sistem tarafından oluşturulan | Yöneticinin Kullanıcı adı.
admin-password | Sistem tarafından oluşturulan | Yönetici kullanıcının parolası.

> [!NOTE]
> Komutu ve ek parametreleri hakkında daha fazla bilgi için `az postgres up` bkz. [Azure CLI belgeleri](/cli/azure/postgres#az_postgres_up).

Sunucunuz oluşturulduktan sonra, aşağıdaki ayarlarla birlikte gelir:

- "Devbox" adlı bir güvenlik duvarı kuralı oluşturulur. Azure CLı, komutun çalıştırıldığı makinenin IP adresini algılamaya çalışır `az postgres up` ve bu IP adresine izin verir.
- "Azure hizmetlerine erişime izin ver" ayarı açık olarak ayarlanmıştır. Bu ayar, sunucu güvenlik duvarını aboneliğinizdeki kaynaklar dahil olmak üzere tüm Azure kaynaklarından gelen bağlantıları kabul edecek şekilde yapılandırır.
- "SampleDB" adlı boş bir veritabanı oluşturuldu
- "SampleDB" ayrıcalıklarına sahip "root" adlı yeni bir Kullanıcı oluşturuldu

> [!NOTE]
> PostgreSQL için Azure veritabanı 5432 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanıyorsanız ağınızın güvenlik duvarı tarafından 5432 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. BT departmanınızın sunucunuza bağlanmak için 5432 bağlantı noktasını açmasını sağlayabilirsiniz.

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

`az postgres up`Komut tamamlandıktan sonra, popüler programlama dillerinin bağlantı dizelerinin bir listesi size döndürülür. Bu bağlantı dizeleri, yeni oluşturulan PostgreSQL için Azure veritabanı sunucunuzun belirli öznitelikleriyle önceden yapılandırılmıştır.

Bu bağlantı dizelerini yeniden listelemek için [az Postgres Show-Connection-String](/cli/azure/postgres#az_postgres_show_connection_string) komutunu kullanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Aşağıdaki komutu kullanarak hızlı başlangıçta oluşturduğunuz tüm kaynakları temizleyin. Bu komut, PostgreSQL için Azure veritabanı sunucusunu ve kaynak grubunu siler.

```azurecli
az postgres down --delete-group
```

Yalnızca yeni oluşturulan sunucuyu silmek istiyorsanız [az Postgres](/cli/azure/postgres#az_postgres_down) -komutunu çalıştırabilirsiniz.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dışarı Aktarma ve İçeri Aktarma seçeneğini kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md)
