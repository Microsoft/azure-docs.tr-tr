---
title: Şablonla bir Web uygulaması dağıtma-Azure Cosmos DB
description: Azure Resource Manager şablonu kullanarak bir Azure Cosmos hesabını, Azure App Service Web Apps ve örnek bir Web uygulamasını dağıtmayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333588"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak GitHub 'dan bir Web uygulamasıyla Azure Cosmos DB ve Azure App Service dağıtma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu öğreticide, Azure portal bağlantı bilgilerini Azure Cosmos DB ' dan `appsettings.json` veya Azure App Services uygulama ayarlarına kesip yapıştırmaya gerek kalmadan ilk çalıştırmada Azure Cosmos DB bağlanan bir Web uygulamasının "Touch" dağıtımını nasıl yapabileceğiniz gösterilmektedir. Tüm bu eylemler tek bir işlemde bir Azure Resource Manager şablonu kullanılarak gerçekleştirilir. Buradaki örnekte, [Azure Cosmos DB Todo örneğini](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) bir [Web uygulaması öğreticiden](sql-api-dotnet-application.md)dağıtacağız.

Kaynak Yöneticisi şablonlar, oldukça esnektir ve Azure 'daki herhangi bir hizmette karmaşık dağıtımlar oluşturmaya olanak sağlar. Bu, GitHub ve ekleme bağlantı bilgilerini, Azure portal Azure App Service uygulama ayarlarına dağıtmak gibi gelişmiş görevleri içerir. Bu öğretici, tek bir Kaynak Yöneticisi şablonu kullanarak aşağıdaki şeyleri nasıl yapılacağını gösterir.

* Azure Cosmos hesabı dağıtın.
* Azure App Service barındırma planı dağıtın.
* Azure App Service dağıtın.
* Azure Cosmos hesabından bitiş noktasını ve anahtarları Azure portal App Service uygulama ayarlarına ekleyin.
* Bir GitHub deposundan App Service bir Web uygulaması dağıtın.

Elde edilen dağıtımda, Azure Cosmos DB uç nokta URL 'sini veya Azure portal kimlik doğrulama anahtarlarını kesip yapıştırmaya gerek kalmadan Azure Cosmos DB bağlanabilir tam işlevli bir Web uygulaması vardır.

## <a name="prerequisites"></a>Önkoşullar

> [!TIP]
> Bu öğretici Azure Resource Manager şablonları veya JSON ile önceki deneyimle karşılaşmadığından, başvurulan şablonları veya dağıtım seçeneklerini değiştirmek ister, bu alanların her biri için bilgi gereklidir.

## <a name="step-1-deploy-the-template"></a>1. Adım: şablonu dağıtma

İlk olarak, bir özel dağıtım oluşturmak için Azure portal açmak üzere aşağıdaki **Azure 'A dağıt** düğmesini seçin. Azure [hızlı başlangıç şablonları galerisinden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) Azure Kaynak Yönetimi şablonunu da görüntüleyebilirsiniz

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Azure portal bir kez, dağıtmak istediğiniz aboneliği seçin ve yeni bir kaynak grubu seçin veya oluşturun. Ardından aşağıdaki değerleri girin.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Şablon dağıtımı Kullanıcı arabiriminin ekran görüntüsü":::

* **Bölge** -bu Kaynak Yöneticisi gereklidir. Kaynaklarınızın bulunduğu konum parametresi tarafından kullanılan bölgeyi girin.
* **Uygulama adı** -bu ad, bu dağıtımın tüm kaynakları tarafından kullanılır. Mevcut Azure Cosmos DB ve App Service hesaplarıyla çakışmaları önlemek için benzersiz bir ad seçtiğinizden emin olun.
* **Konum** -kaynaklarınızın dağıtıldığı bölge.
* **App Service plan katmanı** App Service planının fiyatlandırma katmanı.
* **App Service plan örnekleri** -App Service planı için çalışan sayısı.
* **Depo URL 'si** -GitHub 'daki Web uygulamasına yönelik depo.
* **Dal** -GitHub deposu için dal.
* **Veritabanı adı** -Azure Cosmos veritabanı adı.
* **Kapsayıcı adı** -Azure Cosmos kapsayıcı adı.

Değerleri doldurduktan sonra, dağıtımı başlatmak için **Oluştur** düğmesini seçin. Bu adımın tamamlanması 5 ila 10 dakika sürer.

> [!TIP]
> Şablon, şablona girilen Azure App Service adı ve Azure Cosmos hesap adının geçerli ve kullanılabilir olduğunu doğrulamaz. Dağıtımı göndermeden önce sağlamayı planladığınız adların kullanılabilirliğini doğrulamanız kesinlikle önerilir.


## <a name="step-2-explore-the-resources"></a>2. Adım: kaynakları araştırma

### <a name="view-the-deployed-resources"></a>Dağıtılan kaynakları görüntüleme

Şablon kaynakları dağıttıktan sonra, bunların her birini kaynak grubunuzda görebilirsiniz.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Kaynak Grubu":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Cosmos DB uç noktası ve anahtarları görüntüleme

Ardından, portalda Azure Cosmos hesabını açın. Aşağıdaki ekran görüntüsünde bir Azure Cosmos hesabı için uç nokta ve anahtarlar gösterilmektedir.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos anahtarları":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Uygulama ayarlarındaki Azure Cosmos DB anahtarlarını görüntüleme

Sonra kaynak grubundaki Azure App Service gidin. App Service uygulama ayarlarını görüntülemek için yapılandırma sekmesine tıklayın. Uygulama ayarları, Cosmos DB bağlanmak için gereken Cosmos DB hesabı ve birincil anahtar değerlerini ve şablon dağıtımından geçirilen veritabanı ve kapsayıcı adlarını içerir.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Uygulama Ayarları":::

### <a name="view-web-app-in-deployment-center"></a>Web uygulamasını dağıtım merkezi 'nde görüntüle

Daha sonra App Service için dağıtım merkezine gidin. Burada, şablona geçirilen GitHub deposuna yönelik depo noktalarını görürsünüz. Ayrıca, aşağıdaki durum, uygulamanın başarıyla dağıtıldığını ve başlatıldığını belirten başarılı (etkin) anlamına gelir.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Dağıtım Merkezi":::

### <a name="run-the-web-application"></a>Web uygulamasını çalıştırma

Web uygulamasını açmak için dağıtım merkezi 'nin en üstündeki **Araştır** ' a tıklayın. Web uygulaması, giriş ekranına açılır. **Yeni oluştur** ' a tıklayın ve alanlara bazı veriler girip kaydet ' e tıklayın. Elde edilen ekranda Cosmos DB kaydedilen veriler gösterilir.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Ana ekran":::

## <a name="step-3-how-does-it-work"></a>3. Adım: nasıl çalışır?

Bunun çalışması için gereken üç öğe vardır.

### <a name="reading-app-settings-at-runtime"></a>Çalışma zamanında uygulama ayarları okunuyor

İlk olarak, uygulamanın, `Startup` ASP.NET MVC web uygulamasındaki sınıfında Cosmos DB uç noktası ve anahtarı istemesi gerekir. [Cosmos DB örnek](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) , bağlantı bilgilerini üzerine appsettings.jsgirebileceğiniz yerel olarak çalıştırılabilir. Ancak dağıtıldığında, bu dosya uygulamayla birlikte dağıtılır. Kırmızı renkte bu satırlar, appsettings.js'daki ayarlara erişemeyecektir Azure App Service uygulama ayarlarından çalışacaktır.

:::image type="content" source="./media/create-website/startup.png" alt-text="Ekran görüntüsü databaseName, Kapsayıcıadı, hesap ve anahtar dahil olmak üzere kırmızı renkte işaretlenmiş birkaç dize değişkeni içeren bir yöntemi gösterir.":::

### <a name="using-special-azure-resource-management-functions"></a>Özel Azure Kaynak yönetimi işlevlerini kullanma

Bu değerlerin dağıtıldığında uygulama için kullanılabilir olması için, Azure Resource Manager şablonu, Cosmos DB hesabından değerleri alan ve uygulama ayarları değerlerine, bu değerleri bir ' {section: Key} ' biçiminde Yukarıdaki [](../azure-resource-manager/templates/template-functions-resource.md#listkeys) uygulamada kullanılanlarla eşleşen anahtar adlarıyla [eklemek gibi özel](../azure-resource-manager/templates/template-functions-resource.md#reference) Azure kaynak yönetimi işlevleri kullanarak Cosmos DB hesabından bu değerleri isteyebilir. Örneğin, `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Şablon anahtarları":::

### <a name="deploying-web-apps-from-github"></a>GitHub 'dan Web uygulamaları dağıtma

Son olarak, Web uygulamasını GitHub 'dan App Service dağıtmanız gerekir. Bu işlem aşağıdaki JSON kullanılarak yapılır. Bu kaynağa dikkat etmeniz gereken iki şey, bu kaynağın türü ve adıdır. Hem `"type": "sourcecontrols"` hem de `"name": "web"` özellik değerleri sabit kodludur ve değiştirilmemelidir.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="GitHub 'Dan dağıtma":::

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler! Azure Cosmos DB, Azure App Service ve bir örnek Web uygulamasını, tek bir işlemde Cosmos DB bağlanmak için gereken bağlantı bilgilerini otomatik olarak ve gizli bilgileri kesip yapıştırmaya gerek kalmadan dağıttık. Bu şablonu bir başlangıç noktası olarak kullanarak, kendi web uygulamalarınızı aynı şekilde dağıtmak için bunu değiştirebilirsiniz.

* Bu örnek için Azure Resource Manager şablonu için [Azure hızlı başlangıç şablonları galerisine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) gidin
* Örnek uygulamanın kaynak kodu için [GitHub 'Da uygulamayı Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)gidin.
