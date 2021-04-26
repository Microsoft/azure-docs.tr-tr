---
title: 'Hızlı başlangıç: ilk JavaScript sorgunuz'
description: Bu hızlı başlangıçta, JavaScript için kaynak grafik kitaplığını etkinleştirmek ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: ef93378acaf2c92cf4f7761345cea326af63d300
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533048"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Hızlı başlangıç: JavaScript kullanarak ilk kaynak grafik sorgunuzu çalıştırma

Bu hızlı başlangıç, JavaScript yüklemenize kitaplıkları ekleme sürecinde size yol gösterir. Azure Kaynak Grafiği 'ni kullanmanın ilk adımı, gerekli kitaplıkların bulunduğu bir JavaScript uygulamasını başlatmaktır.

Bu işlemin sonunda, JavaScript yüklemenize kitaplıkları eklemiş ve ilk kaynak Graf sorgunuzu çalıştıracaksınız.

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- **Node.js**: [Node.js](https://nodejs.org/) sürüm 12 veya üzeri gereklidir.

## <a name="application-initialization"></a>Uygulama başlatma

JavaScript 'i Azure Kaynak Grafiği 'ni sorgulamak üzere etkinleştirmek için, ortamın yapılandırılması gerekir. Bu kurulum, [Windows 10 üzerinde Bash](/windows/wsl/install-win10)dahil olmak üzere JavaScript 'in kullanılabileceği her yerde çalışmaktadır.

1. Aşağıdaki komutu çalıştırarak yeni bir Node.js projesi başlatın.

   ```bash
   npm init -y
   ```

1. Yargs modülüne bir başvuru ekleyin.

   ```bash
   npm install yargs
   ```

1. Azure Kaynak Grafiği modülüne bir başvuru ekleyin.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Azure kimlik doğrulama kitaplığına bir başvuru ekleyin.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ `@azure/arm-resourcegraph` 'ın Version **2.0.0** veya üzeri olduğunu ve `@azure/ms-rest-nodeauth` Sürüm **3.0.3** veya üzeri olduğunu doğrulayın.

## <a name="query-the-resource-graph"></a>Kaynak grafiğini sorgulama

1. _index.js_ adlı yeni bir dosya oluşturun ve aşağıdaki kodu girin.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Terminalde aşağıdaki komutu girin:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   `<YOUR_SUBSCRIPTION_ID_LIST>`Yer tutucusunu, virgülle ayrılmış Azure abonelik kimlikleri listeniz ile değiştirdiğinizden emin olun.

   > [!NOTE]
   > Bu sorgu örneği gibi bir sıralama değiştiricisi sağlamadığından `order by` , bu sorguyu birden çok kez çalıştırmak, istek başına farklı bir kaynak kümesi sunmamasından kaynaklanıyor olabilir.

1. İlk parametreyi öğesine değiştirin `index.js` ve sorguyu Name özelliği olarak değiştirin `order by` .  `<YOUR_SUBSCRIPTION_ID_LIST>`ABONELIK Kimliğinizle değiştirin:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Betik kimlik doğrulamaya çalıştığında, terminalde aşağıdaki iletiye benzer bir ileti görüntülenir:

   > Oturum açmak için, sayfayı açmak üzere bir Web tarayıcısı kullanın https://microsoft.com/devicelogin ve kimlik doğrulaması yapmak IÇIN FGB56WJUGK kodunu girin.

   Tarayıcıda kimlik doğrulamasından sonra betik çalışmaya devam eder.

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. İlk parametreyi olarak değiştirin `index.js` ve sorguyu ilk olarak `order by` **ad** özelliğine, sonra da `limit` en üstteki beş sonuca değiştirin. `<YOUR_SUBSCRIPTION_ID_LIST>`ABONELIK Kimliğinizle değiştirin:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüklü kitaplıkları uygulamanızdan kaldırmak istiyorsanız aşağıdaki komutu çalıştırın.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak grafik kitaplıklarını JavaScript ortamınıza eklediniz ve ilk sorgunuzu çalıştırdık. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)
