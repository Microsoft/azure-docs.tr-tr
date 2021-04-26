---
title: "Hızlı başlangıç: Node.js MongoDB uygulamasını Azure Cosmos DB 'ye bağlama"
description: Bu hızlı başlangıçta, Node.js yazılmış mevcut bir MongoDB uygulamasının Azure Cosmos DB 'ye nasıl bağlanacağı gösterilmektedir.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 588c12addbdbd686038699026bb9dccd8671717c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765271"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Hızlı başlangıç: mevcut bir MongoDB Node.js Web uygulamasını Azure Cosmos DB geçirme 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Bu hızlı başlangıçta, Azure Cloud Shell ve GitHub 'dan kopyalanmış bir ortalama (MongoDB, Express, angular ve Node.js) uygulaması ile Mongo DB API hesabı için bir Azure Cosmos DB oluşturup yönetirsiniz. Azure Cosmos DB, genel dağıtım ve yatay ölçeklendirme özellikleri ile belge, tablo, anahtar değer ve grafik veritabanlarını hızlıca oluşturmanıza ve sorgulamanızı sağlayan çok modelli bir veritabanı hizmetidir.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) . [Azure Cosmos DB öykünücüsünü](https://aka.ms/cosmosdb-emulator) bağlantı dizesiyle de kullanabilirsiniz `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .

- [Node.js](https://nodejs.org/)ve Node.js çalışma bilgileri.

- [Git](https://git-scm.com/downloads).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.


## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Örnek depoyu kopyalamak için aşağıdaki komutları çalıştırın. Bu örnek depo, varsayılan [MEAN.js](https://meanjs.org/) uygulamasını içerir.

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Node.js yazılan bu MongoDB uygulaması, MongoDB istemcisini destekleyen Azure Cosmos DB veritabanınıza bağlanır. Diğer bir deyişle, verilerin bir Azure Cosmos DB veritabanında depolandığı uygulamaya saydamdır.

Gereken paketleri yükleyip uygulamayı başlatın.

```bash
cd mean
npm install
npm start
```
Uygulama bir MongoDB kaynağına bağlanmayı deneyip başarısız olur, çıkış "[MongoError: connect ECONNREFUSED 127.0.0.1:27017]" döndürdüğünde uygulamadan çıkabilirsiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Yüklü bir Azure CLı kullanıyorsanız, [az Login](/cli/azure/reference-index#az_login) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin. Azure Cloud Shell'i kullanıyorsanız bu adımı atlayabilirsiniz.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Azure Cosmos DB modülü ekleme

Yüklenen bir Azure CLI kullanıyorsanız `az` komutunu çalıştırarak `cosmosdb` bileşeninin zaten yüklü olup olmadığını kontrol edin. `cosmosdb`, temel komutlar listesindeyse bir sonraki komuta geçin. Azure Cloud Shell'i kullanıyorsanız bu adımı atlayabilirsiniz.

`cosmosdb`, temel komutlar listesinde değilse [Azure CLI](/cli/azure/install-azure-cli)'yi yeniden yükleyin.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Az Group Create](/cli/azure/group#az_group_create)ile bir [kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Azure kaynak grubu; web uygulamaları, veritabanları ve depolama hesapları gibi Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnekte Batı Avrupa bölgesinde bir kaynak grubu oluşturulmaktadır. Kaynak grubu için benzersiz bir ad seçin.

Azure Cloud Shell kullanıyorsanız, **deneyin**' i seçin, oturum açmak için ekrandaki istemleri izleyin, sonra komutu komut istemine kopyalayın.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

[Az cosmosdb Create](/cli/azure/cosmosdb#az_cosmosdb_create) komutuyla bir Cosmos hesabı oluşturun.

Aşağıdaki komutta, lütfen yer tutucuyu gördüğünüz benzersiz Cosmos hesabınızın adını yerine koyun `<cosmosdb-name>` . Bu benzersiz ad Cosmos DB uç noktasının bir parçası olarak kullanılır `https://<cosmosdb-name>.documents.azure.com/` . bu nedenle, adın Azure 'daki tüm Cosmos hesaplarında benzersiz olması gerekir. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` parametresi MongoDB istemci bağlantılarını etkinleştirir.

Azure Cosmos DB hesabı oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir. 

> [!NOTE]
> Bu örnek, varsayılan Azure CLI çıktı biçimi olarak JSON kullanır. Başka bir çıktı biçimi kullanmak için bkz. [Azure CLI komutları için çıktı biçimleri](/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Node.js uygulamanızı veritabanına bağlama

Bu adımda, MEAN.js örnek uygulamanızı yeni oluşturduğunuz Azure Cosmos DB veritabanı hesabına bağlayadınız. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js uygulamanızda bağlantı dizesini yapılandırma

MEAN.js deponuzda `config/env/local-development.js` dosyasını açın.

Bu dosyanın içeriğini aşağıdaki kodla değiştirin. İki `<cosmosdb-name>` yer tutucuyu Cosmos hesap adınızla değiştirdiğinizden de emin olun.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Anahtarı alma

Cosmos veritabanına bağlanmak için veritabanı anahtarına ihtiyacınız vardır. Birincil anahtarı almak için [az cosmosdb Keys List](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) komutunu kullanın.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI aşağıdaki örneğe benzer bilgiler çıkarır. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

`primaryMasterKey` değerini kopyalayın. `local-development.js` içinde bulunan `<primary_master_key>` üzerine yapıştırın.

Yaptığınız değişiklikleri kaydedin.

### <a name="run-the-application-again"></a>Uygulamayı yeniden çalıştırın.

`npm start` komutunu yeniden çalıştırın. 

```bash
npm start
```

Bir konsol iletisi, geliştirme ortamının çalışır durumda olduğunu söyleyecektir. 

`http://localhost:3000`Bir tarayıcıda adresine gidin. Üstteki menüden **Kaydol** ' u seçin ve iki kukla Kullanıcı oluşturmayı deneyin. 

MEAN.js örnek uygulaması, kullanıcı verilerini veritabanında depolar. Başarılı olursanız ve MEAN.js oluşturulan kullanıcının oturumunu otomatik olarak açarsa, Azure Cosmos DB bağlantınız çalışıyordur. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="MEAN.js, MongoDB’ye başarıyla bağlanır":::

## <a name="view-data-in-data-explorer"></a>Veri Gezgini’nde verileri görüntüleme

Cosmos veritabanında depolanan veriler, Azure portal görüntülemek ve sorgulamak için kullanılabilir.

Önceki adımda oluşturulan verileri görüntülemek, sorgulamak ve üzerinde çalışmak için web tarayıcınızda [Azure portalı](https://portal.azure.com) oturumunu açın.

Üst arama kutusuna **Azure Cosmos DB** girin. Cosmos hesabı dikey penceresi açıldığında, Cosmos hesabınızı seçin. Sol gezinti bölmesinde **Veri Gezgini**' yi seçin. Koleksiyonlar bölmesinde koleksiyonunuzu genişletin; bundan sonra koleksiyondaki belgeleri görüntüleyebilir, verileri sorgulayabilir ve hatta saklı yordam, tetikleyici ve UDF’ler oluşturup çalıştırabilirsiniz. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Azure portalında Veri Gezgini":::


## <a name="deploy-the-nodejs-application-to-azure"></a>Node.js uygulamasını Azure’a dağıtma

Bu adımda, Node.js uygulamanızı Cosmos DB dağıtırsınız.

Daha önce değiştirdiğiniz yapılandırma dosyasının geliştirme ortamına (`/config/env/local-development.js`) yönelik olduğunu fark etmiş olabilirsiniz. Uygulamanızı App Service’e dağıttığınızda, varsayılan olarak üretim ortamında çalıştırılır. O halde şimdi, ilgili yapılandırma dosyasında aynı değişikliği yapmanız gerekir.

MEAN.js deponuzda `config/env/production.js` dosyasını açın.

`db` nesnesinde `uri` değerini aşağıdaki örnekte gösterilen şekilde değiştirin. Yer tutucuları daha önceki gibi değiştirdiğinizden emin olun.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> `ssl=true`Cosmos DB gereksinimler nedeniyle bu seçenek önemlidir. Daha fazla bilgi için bkz. [bağlantı dizesi gereksinimleri](connect-mongodb-account.md#connection-string-requirements).
>
>

Terminalde tüm değişikliklerinizi Git’e uygulayın. Her iki komutu birlikte çalıştırmak üzere kopyalayabilirsiniz.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Cloud Shell kullanarak Azure Cosmos DB MongoDB API hesabı oluşturmayı ve hesaba kullanıcı eklemek için bir MEAN.js uygulaması oluşturup çalıştırmayı öğrendiniz. Şimdi Azure Cosmos DB hesabınıza ek veriler aktarabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB’ye MongoDB verileri aktarma](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)