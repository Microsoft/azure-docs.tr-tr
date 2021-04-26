---
title: 'Öğretici: SQL API verilerini yönetmek için Azure Cosmos DB JavaScript SDK ile Node.js Web uygulaması oluşturma'
description: Bu Node.js öğreticide, Microsoft Azure App Service Web Apps özelliğinde barındırılan bir Node.js Express web uygulamasındaki verileri depolamak ve erişmek için Microsoft Azure Cosmos DB nasıl kullanılacağı açıklanır.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 49cf54bda985f7d97b2db6a3ada7859aee829cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359549"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Öğretici: Azure Cosmos DB ' de bir SQL API hesabını yönetmek için JavaScript SDK 'sını kullanarak Node.js Web uygulaması oluşturma 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Geliştirici olarak, NoSQL belge verileri kullanan uygulamalarınız olabilir. Bu belge verilerini depolamak ve erişmek için Azure Cosmos DB bir SQL API hesabı kullanabilirsiniz. Bu Node.js öğreticide, Microsoft Azure App Service Web Apps özelliğinde barındırılan bir Node.js Express uygulamasını kullanarak Azure Cosmos DB bir SQL API hesabındaki verileri nasıl depolayabileceği ve erişebileceğiniz gösterilmektedir. Bu öğreticide, görevleri oluşturmanıza, almanıza ve tamamlamanıza izin veren bir Web tabanlı uygulama (ToDo uygulaması) oluşturacaksınız. Görevler, JSON belgeleri olarak Azure Cosmos DB'de depolanır. 

Bu öğretici, Azure portal kullanarak Azure Cosmos DB bir SQL API hesabı oluşturmayı gösterir. Daha sonra bir veritabanı ve kapsayıcı oluşturmak ve kapsayıcıya öğe eklemek için Node.js SDK üzerinde oluşturulmuş bir Web uygulaması oluşturup çalıştırın. Bu öğretici, JavaScript SDK 3,0 sürümünü kullanır.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Azure Cosmos DB hesabı oluşturma
> * Yeni bir Node.js uygulaması oluşturma
> * Uygulamayı Azure Cosmos DB’ye bağlama
> * Uygulamayı Azure'da çalıştırma ve dağıtma

## <a name="prerequisites"></a><a name="_Toc395783176"></a>Önkoşullar

Bu makaledeki yönergeleri izlemeden önce aşağıdaki kaynaklara sahip olduğunuzdan emin olun:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] sürüm 6.10 veya üzeri.
* [Express oluşturucu](https://www.expressjs.com/starter/generator.html) (Express'i `npm install express-generator -g` aracılığıyla yükleyebilirsiniz)
* [Git][Git]'i yerel iş istasyonunuza yükleyin.

## <a name="create-an-azure-cosmos-db-account"></a><a name="_Toc395637761"></a>Azure Cosmos DB hesabı oluşturma
İlk olarak bir Azure Cosmos DB hesabı oluşturalım. Zaten bir hesabınız varsa veya bu öğretici için Azure Cosmos DB Öykünücüsü’nü kullanıyorsanız [2. Adım: Yeni Node.js uygulaması oluşturma](#_Toc395783178) adımına atlayabilirsiniz.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="_Toc395783178"></a>Yeni bir Node.js uygulaması oluşturma
Şimdi Express altyapısını kullanarak temel bir Hello World Node.js projesi oluşturmayı öğrenelim.

1. Node.js komut istemi gibi istediğiniz bir terminal uygulamasını açın.

1. Yeni uygulamanın depolanmasını istediğiniz dizine gidin.

1. Express oluşturucuyu kullanarak **todo** adlı yeni bir uygulama oluşturun.

   ```bash
   express todo
   ```

1. **todo** dizinini açın ve bağımlılıkları yükleyin.

   ```bash
   cd todo
   npm install
   ```

1. Yeni uygulamayı çalıştırın.

   ```bash
   npm start
   ```

1. Tarayıcınızda `http://localhost:3000` adresine giderek yeni uygulamanızı görüntüleyebilirsiniz.
   
   :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-express.png" alt-text="Node.js öğrenin - Bir tarayıcı penceresinde Hello World uygulamasının ekran görüntüsü":::

   Terminal penceresinde CTRL + C kullanarak uygulamayı durdurun ve toplu işi sonlandırmak için **y** ' yi seçin.

## <a name="install-the-required-modules"></a><a name="_Toc395783179"></a>Gerekli modülleri yükler

**Package.json** dosyası, projenin kökünde oluşturulan dosyalardan biridir. Bu dosya, Node.js uygulamanız için gerekli olan ek modüllerin listesini içerir. Bu uygulamayı Azure'a dağıttığınızda uygulamanızı desteklemek amacıyla Azure'a hangi modüllerin yüklenmesi gerektiğini belirlemek için bu dosya kullanılır. Bu öğretici için iki paket daha yükleyeceksiniz.

1. **\@ Azure/Cosmos** modülünü NPM aracılığıyla yükler. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="_Toc395783180"></a>Node.js uygulamayı Azure Cosmos DB bağlama
İlk kurulum ve yapılandırma adımlarını tamamladığınıza göre yapılacak işler uygulamasının Azure Cosmos DB ile iletişim kurması için gereken kodu yazabilirsiniz.

### <a name="create-the-model"></a>Modeli oluşturma
1. Proje dizininizin kökünde, **modeller** adlı yeni bir dizin oluşturun.  

2. **models** dizininde **taskDao.js** adında yeni bir dosya oluşturun. Bu dosya, veritabanını ve kapsayıcısını oluşturmak için gereken kodu içerir. Ayrıca, Azure Cosmos DB görevleri okuma, güncelleştirme, oluşturma ve bulma yöntemlerini de tanımlar. 

3. Aşağıdaki kodu **taskDao.js** dosyasına kopyalayın:

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. **taskDao.js** dosyasını kaydedin ve kapatın.  

### <a name="create-the-controller"></a>Denetleyiciyi oluşturma

1. Projenizin **routes** dizininde **tasklist.js** adlı yeni bir dosya oluşturun.  

2. Aşağıdaki kodu **tasklist.js**'ye ekleyin. Bu kod, **tasklist.js** tarafından kullanılan CosmosClient ve async modüllerini yükler. Bu kod, daha önce tanımladığımız **TaskDao** nesnesinin bir örneği olarak geçirilmiş **TaskList** sınıfını da tanımlar:
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. **tasklist.js** dosyasını kaydedin ve kapatın.

### <a name="add-configjs"></a>Config.js ekleme

1. Projenizin kök dizininde **config.js** adlı yeni bir dosya oluşturun. 

2. **config.js** dosyasına aşağıdaki kodu ekleyin. Bu kod, uygulamamız için gereken yapılandırma ayarlarını ve değerlerini tanımlar.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. **config.js** dosyasında, konak ve AUTH_KEY değerlerini [Azure Portal](https://portal.azure.com)Azure Cosmos DB hesabınızın anahtarlar sayfasında bulunan değerleri kullanarak güncelleştirin. 

4. **config.js** dosyasını kaydedin ve kapatın.

### <a name="modify-appjs"></a>App.js'yi değiştirme

1. Proje dizininde **app.js** dosyasını açın. Bu dosya daha önce Express web uygulaması oluşturulduğu zaman oluşturulmuştur.  

2. **app.js** dosyasına aşağıdaki kodu ekleyin. Bu kod, kullanılacak yapılandırma dosyasını tanımlar ve değerleri sonraki bölümlerde kullanacağınız değişkenlere yükler. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. Son olarak, **app.js** dosyasını kaydedip kapatın.

## <a name="build-a-user-interface"></a><a name="_Toc395783181"></a>Kullanıcı arabirimi oluşturma

Şimdi bir kullanıcının uygulamayla etkileşime girebilmesi için Kullanıcı arabirimini oluşturalım. Önceki bölümlerde oluşturduğumuz Express uygulaması, görüntüleme altyapısı olarak **Jade**'i kullanır.

1. **views** dizinindeki **layout.jade** dosyası diğer **.jade** dosyaları için genel bir şablon olarak kullanılır. Bu adımda, web sitesi tasarlamak için kullanılan bir araç seti olan Twitter Bootstrap'i kullanmak için bu dosyayı değiştireceksiniz.  

2. **views** klasöründe bulunan **layout.jade** dosyasını açın ve içeriğini aşağıdaki kodla değiştirin:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Bu kod, **Jade** altyapısına uygulamamız IÇIN bazı HTML işlemesini söyler ve içerik sayfalarımıza yönelik düzen sağlayabilmemiz için **içerik** olarak adlandırılan bir **blok** oluşturur. **layout.jade** dosyasını kaydedin ve kapatın.

3. Şimdi uygulamamız tarafından kullanılacak görünüm olan **index.jade** dosyasını açın ve dosyanın içeriğini aşağıdaki kodla değiştirin:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Bu kod, düzeni genişletir ve daha önce **layout.jade** dosyasında gördüğümüz **content** yer tutucusu için içerik sağlar. Bu düzende iki HTML formu oluşturduk.

İlk form, öğeleri denetleyicinin **/completeTask** yöntemine göndererek güncelleştirmenizi sağlayan bir düğmeyi ve verileriniz için bir tabloyu içerir.
    
İkinci form, yeni bir öğeyi denetleyicinin **/addtask** yöntemine göndererek oluşturmanızı sağlayan bir düğmeyi ve iki giriş alanını içerir. Bunlar uygulamayı çalıştırmak için yeterli olacaktır.

## <a name="run-your-application-locally"></a><a name="_Toc395783181"></a>Uygulamanızı yerel olarak çalıştırma

Artık uygulamayı oluşturduğunuza göre, aşağıdaki adımları kullanarak yerel olarak çalıştırabilirsiniz:  

1. Uygulamayı yerel makinenizde test etmek için `npm start` terminalde çalıştırarak uygulamanızı başlatın ve `http://localhost:3000` tarayıcı sayfasını yenileyin. Sayfa, aşağıdaki ekran görüntüsünde gösterildiği gibi görünmelidir:
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png" alt-text="Bir tarayıcı penceresinde Yapılacaklar Listem uygulamasının ekran görüntüsü":::

    > [!TIP]
    > Layout. Jade dosyasında veya index. Jade dosyasında girintileme hakkında bir hata alırsanız, her iki dosyada bulunan ilk iki satırın, boşluk olmadan sola hizalı olduğundan emin olun. İlk iki satırdan önce boşluklar varsa, bunları kaldırın, her iki dosyayı da kaydedin ve ardından tarayıcı pencerenizi yenileyin. 

2. Öğe, öğe adı ve kategori alanlarını kullanarak yeni bir görev girip **öğe Ekle**' yi seçin. Bu işlem, Azure Cosmos DB içinde bu özelliklere sahip bir belge oluşturur. 

3. Sayfa, Yapılacaklar listesinde yeni oluşturulan öğeyi görüntülemek üzere güncelleştirilmelidir.
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png" alt-text="Yapılacaklar listesinde yeni bir öğeyi içeren uygulamanın ekran görüntüsü":::

4. Bir görevi gerçekleştirmek için, tamam sütunundaki onay kutusunu seçin ve ardından **görevleri güncelleştir**' i seçin. Bu işlem önceden oluşturduğunuz belgeyi güncelleştirir ve görünümden kaldırır.

5. Uygulamayı durdurmak için terminal penceresinde CTRL+C tuşlarına basın ve ardından toplu işlemi sonlandırmak için **Y** öğesini seçin.

## <a name="deploy-your-application-to-web-apps"></a><a name="_Toc395783182"></a>Uygulamanızı Web Apps dağıtma

Uygulamanız yerel olarak başarılı olduktan sonra, aşağıdaki adımları kullanarak Azure 'a dağıtabilirsiniz:

1. Daha önce yapmadıysanız, Web Apps uygulamanız için bir git deposu etkinleştirin.

2. Web Apps uygulamanızı git uzak olarak ekleyin.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Uygulamayı uzak öğeye göndererek dağıtın.
   
   ```bash
   git push azure main
   ```

4. Web uygulamanız birkaç saniye içinde yayımlanır ve bir tarayıcıda başlatılır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu kaynaklara artık ihtiyaç duyulmadığında, kaynak grubunu, Azure Cosmos DB hesabı ve tüm ilgili kaynakları silebilirsiniz. Bunu yapmak için Azure Cosmos DB hesabı için kullandığınız kaynak grubunu seçin, **Sil**' i seçin ve ardından silinecek kaynak grubunun adını onaylayın.

## <a name="next-steps"></a><a name="_Toc395637775"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Xamarin ve Azure Cosmos DB ile mobil uygulamalar derleme](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app