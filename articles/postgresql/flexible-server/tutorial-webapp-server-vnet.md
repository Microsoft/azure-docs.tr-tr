---
title: 'Öğretici: PostgreSQL için Azure veritabanı oluşturma-aynı sanal ağda esnek sunucu ve Azure App Service Web uygulaması'
description: PostgreSQL için Azure veritabanı oluşturmak için hızlı başlangıç kılavuzu-Sanal ağda Web uygulamasıyla esnek sunucu
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9af90ca0b6b80ffece5ccd7d919c1d93e210c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657595"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Öğretici: Sanal ağda, PostgreSQL için Azure veritabanı-uygulama Hizmetleri Web uygulaması ile esnek sunucu oluşturma

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Bu öğreticide, bir [sanal ağ](../../virtual-network/virtual-networks-overview.md)Içindeki PostgreSQL Için Azure veritabanı-esnek sunucu (Önizleme) ile bir Azure App Service Web uygulaması oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
>[!div class="checklist"]
> * Sanal ağda bir PostgreSQL esnek sunucusu oluşturma
> * App Service temsilci seçmek için bir alt ağ oluşturun
> * Web uygulaması oluşturma
> * Web uygulamasını sanal ağa ekleme
> * Web uygulamasından Postgres 'e bağlanma 

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

Bu makalede, Azure CLı sürüm 2,0 veya üstünü yerel olarak çalıştırıyor olmanız gerekir. Yüklü sürümü görmek için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

[Az Login](/cli/azure/authenticate-azure-cli) komutunu kullanarak hesabınızda oturum açmanız gerekir. Komut çıktısındaki ilgili abonelik adına karşılık gelen **id** özelliğinin değerini not edin.

```azurecli
az login
```

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](/cli/azure/account) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. **Abonelik kimliği özelliğini aboneliğiniz** için **az oturum açma** çıktısından abonelik kimliği yer tutucusuna koyun.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Yeni bir sanal ağda bir PostgreSQL esnek sunucusu oluşturma

Aşağıdaki komutu kullanarak bir sanal ağ (VNET) içinde özel bir esnek sunucu oluşturun:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Bu komut, birkaç dakika sürebilen aşağıdaki eylemleri gerçekleştirir:

- Zaten mevcut değilse kaynak grubunu oluşturun.
- Sağlanmazsa bir sunucu adı oluşturur.
- Yeni postgreSQL sunucunuz için yeni bir sanal ağ oluşturun. Web uygulamasını aynı sanal ağa eklemeniz gerektiğinden, sunucunuz için oluşturulan sanal ağ adını ve alt ağ adını bir yere unutmayın.
- Sağlanmazsa, sunucunuz için Yönetici Kullanıcı adı, parola oluşturur.
- **Postgres** adlı boş bir veritabanı oluşturur

> [!NOTE]
> - Sağlanmazsa, sizin için üretilecek olan parolanızı not edin. Parolayı unutursanız, komutunu kullanarak parolayı sıfırlamanız gerekir ``` az postgres flexible-server update```
> - App Service Ortamı kullanmıyorsanız, bu komutu kullanarak herhangi bir Azure IP 'sinden erişime Izin ver ' i etkinleştirmeniz gerekir. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```

## <a name="create-subnet-for-app-service-endpoint"></a>App Service uç noktası için alt ağ oluşturma
Artık Web uygulaması uç noktası App Service için temsilci atanmış alt ağa sahip olmanız gerekir. Veritabanı sunucusu oluşturulduğu sanal ağda yeni bir alt ağ oluşturmak için aşağıdaki komutu çalıştırın. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Bu komuttan sonra sanal ağ adını ve alt ağ adını bir yere, oluşturulduktan sonra Web uygulaması için VNET tümleştirme kuralı eklemesi gerekir. 

## <a name="create-a-web-app"></a>Web Uygulaması oluşturma
Bu bölümde App Service uygulamasında uygulama konağı oluşturur, bu uygulamayı Postgres veritabanına bağlayın ve ardından kodunuzu bu konağa dağıtın. Terminalde uygulama kodunuzun depo kökünde olduğunuzdan emin olun. Not temel planı VNET tümleştirmesini desteklemez. Lütfen standart veya Premium kullanın. 

Az WebApp up komutuyla bir App Service uygulaması (ana bilgisayar işlemi) oluşturun

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - --Location bağımsız değişkeni için, önceki bölümde veritabanıyla aynı konumu kullanın.
> - <app-name> tüm Azure genelinde benzersiz bir adla değiştirin (sunucu uç noktası https:// \<app-name> . azurewebsites.net). <app-name> için izin verilen karakterler A-Z, 0-9 ve-. İyi bir model, şirketinizin adının ve uygulama tanımlayıcısının bir birleşimini kullanmaktır.

Bu komut, birkaç dakika sürebilen aşağıdaki eylemleri gerçekleştirir:

- Zaten mevcut değilse kaynak grubunu oluşturun. (Bu komutta veritabanını daha önce oluşturduğunuz kaynak grubunu kullanırsınız.)
- Yoksa App Service uygulamasını oluşturun.
- Zaten etkinleştirilmemişse, uygulama için varsayılan günlüğü etkinleştirin.
- Derleme Otomasyonu etkinken ZIP dağıtımını kullanarak depoyu karşıya yükleyin.

## <a name="add-the-web-app-to-the-virtual-network"></a>Web uygulamasını sanal ağa ekleme
Bir WebApp 'ye bölgesel sanal ağ tümleştirmesi eklemek için **az WebApp VNET-Integration** komutunu kullanın. <VNET-Name> ve <alt ağ adı>, esnek sunucunun kullandığı sanal ağ ve alt ağ adıyla değiştirin.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Veritabanını bağlamak için ortam değişkenlerini yapılandırma
Kod artık App Service 'e dağıtıldığına göre, bir sonraki adım, uygulamayı Azure 'daki esnek sunucuya bağlayabilmektedir. Uygulama kodu, çeşitli ortam değişkenlerinde veritabanı bilgilerini bulmayı bekler. App Service ortam değişkenlerini ayarlamak için set komutuyla "uygulama ayarları" oluşturursunuz ```az webapp config appsettings``` .

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- ```postgres-server-name``` ```username``` ```password``` Yeni oluşturulan esnek sunucu komutu için değiştirin.
- <username>Ve ' i, <password> komutun sizin için oluşturduğu kimlik bilgileriyle değiştirin.
- Kaynak grubu ve uygulama adı,. Azure/config dosyasındaki önbelleğe alınmış değerlerden çizilir.
- Komutu,, ve adlı ayarları oluşturur ```DBHOST``` ```DBNAME``` ```DBUSER``` ```DBPASS``` . Uygulama kodunuz veritabanı bilgileri için farklı bir ad kullanıyorsa, bu adları kodda belirtildiği gibi uygulama ayarları için kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Aşağıdaki komutu kullanarak öğreticide oluşturduğunuz tüm kaynakları temizleyin. Bu komut, bu kaynak grubundaki tüm kaynakları siler.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Mevcut bir özel DNS adını Azure App Service eşleme](../../app-service/app-service-web-tutorial-custom-domain.md)
