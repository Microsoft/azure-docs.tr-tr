---
title: 'Öğretici: MongoDB ile uygulama Node.js'
description: Azure 'da çalışan bir Node.js uygulamasının Azure 'da bir MongoDB veritabanıyla bağlantı ile nasıl alınacağını öğrenin (Cosmos DB). MEAN.js öğreticide kullanılır.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b1dcd413f301f25460cb29f1bb20e67a37ac6ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767333"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Öğretici: Azure 'da Node.js ve MongoDB uygulaması oluşturma

::: zone pivot="platform-windows"  

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu öğreticide, Windows 'da App Service Node.js uygulamasının nasıl oluşturulacağı ve bir MongoDB veritabanına nasıl bağlanacağı gösterilmektedir. İşiniz bittiğinde, [Azure App Service](overview.md)’te çalışan bir MEAN uygulamanız (MongoDB, Express, AngularJS ve Node.js) olacaktır. Kolaylık olması için örnek uygulama [MEAN.js web çerçevesi](https://meanjs.org/)’ni kullanır.

::: zone-end

::: zone pivot="platform-linux"


[Azure App Service](overview.md) , Linux işletim sistemini kullanarak yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. Bu öğreticide, Linux üzerinde App Service bir Node.js uygulamasının nasıl oluşturulacağı, bir MongoDB veritabanına yerel olarak nasıl bağlanacağı ve sonra MongoDB için Azure Cosmos DB API 'sindeki bir veritabanına nasıl dağıtılacağı gösterilmektedir. İşiniz bittiğinde, Linux üzerinde App Service’te çalışan bir MEAN uygulamanız (MongoDB, Express, AngularJS ve Node.js) olacaktır. Kolaylık olması için örnek uygulama [MEAN.js web çerçevesi](https://meanjs.org/)’ni kullanır.

::: zone-end

![Azure App Service’te çalışan MEAN.js uygulaması](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Öğrenecekleriniz:

> [!div class="checklist"]
> * Azure’da MongoDB veritabanı oluşturma
> * Node.js uygulamasını MongoDB’ye bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- [Git'i yükleyin](https://git-scm.com/)
- [Node.js ve NPM'yi yükleyin](https://nodejs.org/)
- [Bower yükleme](https://bower.io/) ([MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) için gerekli)
- [Gulp.js yükleme](https://gulpjs.com/) ([MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) için gerekli)
- [MongoDB Community Edition 'ı yükleyin ve çalıştırın](https://docs.mongodb.com/manual/administration/install-community/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="test-local-mongodb"></a>Yerel MongoDB’yi test etme

Terminal penceresini açın ve MongoDB yüklemenizin `bin` dizinine `cd` yazın. Bu öğreticideki tüm komutları çalıştırmak için bu terminal penceresini kullanabilirsiniz.

Yerel MongoDB sunucunuza bağlanmak için terminalde `mongo` komutunu çalıştırın.

```bash
mongo
```

Bağlantınız başarılı olursa, MongoDB veritabanınız zaten çalışıyor demektir. Aksi takdirde, yerel MongoDB veritabanınızın [MongoDB Community Edition’ı yükleme](https://docs.mongodb.com/manual/administration/install-community/) konusundaki adımları izleyerek başlatıldığından emin olun. Bazen MongoDB yüklenmiş olsa da, `mongod` komutunu çalıştırarak başlatmak gerekir. 

MongoDB veritabanınızı test etmeyi bitirdiğinizde, terminale `Ctrl+C` yazın. 

## <a name="create-local-nodejs-app"></a>Yerel Node.js uygulaması oluşturma

Bu adımda, yerel Node.js projesini ayarlarsınız.

### <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Terminal penceresinde, `cd` ile bir çalışma dizinine gidin.  

Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Bu örnek depo, [MEAN.js deposu](https://github.com/meanjs/mean)’nun bir kopyasını içerir. App Service’te çalıştırılması için değiştirilmiştir (daha fazla bilgi için MEAN.js deposu [BENİOKU dosyasını](https://github.com/Azure-Samples/meanjs/blob/master/README.md) görüntüleyin).

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Gerekli paketleri yüklemek ve uygulamayı başlatmak için aşağıdaki komutları çalıştırın.

```bash
cd meanjs
npm install
npm start
```

Config.domain uyarısını yoksayın. Uygulama tam olarak yüklendiğinde, şu iletiye benzer bir şey görürsünüz:

<pre>
--
MEAN.JS geliştirme ortamı

Ortam: geliştirme sunucusu:          http://0.0.0.0:3000 veritabanı: MongoDB://localhost/Mean-dev App Version: 0.5.0 MEAN.JS sürümü: 0.5.0--
</pre>

Bir tarayıcıda `http://localhost:3000` sayfasına gidin. Üst menüde **Kaydol**’a tıklayın ve bir test kullanıcısı oluşturun. 

MEAN.js örnek uygulaması, kullanıcı verilerini veritabanında depolar. Kullanıcı oluşturma ve oturum açmada başarılı olursanız, uygulamanız yerel MongoDB veritabanına veri yazıyor demektir.

![MEAN.js, MongoDB’ye başarıyla bağlanır](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Birkaç makale eklemek için **Yönetici > Makaleleri Yönet**’i seçin.

Node.js’yi dilediğiniz zaman durdurmak için, terminalde `Ctrl+C` tuşlarına basın. 

## <a name="create-production-mongodb"></a>Üretim MongoDB’si oluşturma

Bu adımda, Azure’da bir MongoDB veritabanı oluşturursunuz. Uygulamanız Azure’da dağıtıldığında bu bulut veritabanını kullanır.

MongoDB için bu öğreticide [Azure Cosmos DB](/azure/documentdb/) kullanılır. Cosmos DB, MongoDB istemci bağlantılarını destekler.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Cosmos DB hesabı oluşturma

> [!NOTE]
> Bu öğreticideki Azure Cosmos DB veritabanlarını kendi Azure aboneliğinizde oluşturmanız halinde ücret alınır. Azure Cosmos DB hesabını yedi gün boyunca ücretsiz kullanmak için [Azure Cosmos DB'yi ücretsiz deneyin](https://azure.microsoft.com/try/cosmosdb/) deneyimini kullanabilirsiniz. Azure'da ücretsiz bir MongoDB veritabanı oluşturmak için MongoDB kutucuğundaki **Oluştur** düğmesine tıklamanız yeterlidir. Veritabanı oluşturulduktan sonra portalda **Bağlantı Dizesi** sayfasına giderek öğreticinin sonraki bölümlerinde kullanmak üzere Azure Cosmos DB bağlantı dizenizi alın.
>

Cloud Shell, komutuyla bir Cosmos DB hesabı oluşturun [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) .

Aşağıdaki komutta, yer tutucu için benzersiz bir Cosmos DB adı değiştirin *\<cosmosdb-name>* . Bu ad, Cosmos DB uç noktasının bir parçası olan `https://<cosmosdb-name>.documents.azure.com/` olarak kullanıldığından, adın Azure’daki tüm Cosmos DB hesaplarında benzersiz olması gerekir. Ad yalnızca küçük harf, rakam ve tire (-) karakteri içerebilir; 3 ila 50 karakter uzunluğunda olmalıdır.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

*--kind MongoDB* parametresi MongoDB istemci bağlantılarını etkinleştirir.

Cosmos DB hesabı oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir:

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>Üretim MongoDB’sine uygulama bağlama

Bu adımda, MEAN.js örnek uygulamanızı, MongoDB bağlantı dizesi kullanarak yeni oluşturduğunuz Cosmos DB veritabanına bağlayacaksınız. 

### <a name="retrieve-the-database-key"></a>Veritabanı anahtarını alma

Cosmos DB veritabanına bağlanmak için veritabanı anahtarı gerekir. Cloud Shell, [`az cosmosdb list-keys`](/cli/azure/cosmosdb#az_cosmosdb_list_keys) birincil anahtarı almak için komutunu kullanın.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

Azure CLI aşağıdaki örneğe benzer bilgiler görüntüler:

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

`primaryMasterKey` değerini kopyalayın. Bu bilgiler sonraki adımda gerekli olacaktır.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js uygulamanızda bağlantı dizesini yapılandırma

Yerel MEAN.js deponuzda, _config/env/_ klasöründe _local-production.js_ adlı bir dosya oluşturun. _. gitignore_ , bu dosyayı deponun dışında tutmak için zaten yapılandırılmış. 

Aşağıdaki kodu dosyanın içine kopyalayın. İki *\<cosmosdb-name>* yer tutucuyu Cosmos DB veritabanı adınızla değiştirdiğinizden emin olun ve *\<primary-master-key>* yer tutucusunu, önceki adımda kopyaladığınız anahtarla değiştirin.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

[Cosmos DB, SSL gerektirdiğinden](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)`ssl=true` seçeneği gereklidir. 

Yaptığınız değişiklikleri kaydedin.

### <a name="test-the-application-in-production-mode"></a>Uygulamayı üretim modunda test etme 

Yerel terminal penceresinde, üretim ortamı için şu komutu çalıştırarak betikleri küçültün ve paketleyin. Bu işlem, üretim ortamı tarafından gerekli olan dosyaları oluşturur.

```bash
gulp prod
```

Yerel terminal penceresinde, _config/env/local-production.js_ konumunda yapılandırdığınız bağlantı dizesini kullanmak için şu komutu çalıştırın. Sertifika hatasını ve config.domain uyarısını yoksayın.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production`, Node.js’ye üretim ortamında çalışmasını söyleyen ortam değişkenini ayarlar.  `node server.js`, Node.js sunucusunu `server.js` ile depo kökünüzde başlatır. Node.js uygulamanız Azure’a bu şekilde yüklenir. 

Uygulama yüklendiğinde, üretim ortamında çalıştığından emin olmak için kontrol edin:

<pre>
--
MEAN.JS

Ortam: üretim sunucusu: http://0.0.0.0:8443 veritabanı: MongoDB:// &lt; cosmosdb-Name &gt; : &lt; PRIMARY-Master-Key &gt; @ &lt; cosmosdb-Name &gt; . Documents.Azure.com:10250/Mean?SSL=true&sslverifycertificate = false uygulama sürümü: 0.5.0 MEAN.JS Version: 0.5.0
</pre>

Bir tarayıcıda `http://localhost:8443` sayfasına gidin. Üst menüde **Kaydol**’a tıklayın ve bir test kullanıcısı oluşturun. Kullanıcı oluşturma ve oturum açmada başarılı olursanız, uygulamanız Azure’da Cosmos DB veritabanına veri yazıyor demektir. 

Terminalde `Ctrl+C` yazarak Node.js’yi durdurun. 

## <a name="deploy-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Bu adımda, MongoDB’ye bağlı Node.js uygulamanızı Azure App Service’e dağıtacaksınız.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Web uygulaması oluşturma

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>Ortam değişkeni yapılandırma

Varsayılan olarak, MEAN.js projesi _config/env/local-production.js_ öğesini Git deposu dışında tutar. Bu nedenle, Azure uygulamanız için MongoDB Bağlantı dizenizi tanımlamak üzere uygulama ayarlarını kullanırsınız.

Uygulama ayarlarını ayarlamak için [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) Cloud Shell komutunu kullanın. 

Aşağıdaki örnek, `MONGODB_URI` Azure uygulamanızda bir uygulama ayarı yapılandırır. *\<app-name>*, *\<cosmosdb-name>* Ve *\<primary-master-key>* yer tutucularını değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

Node.js kodda, tüm ortam değişkenlerine erişecekle tıpkı, [Bu uygulama ayarına ile erişirsiniz](configure-language-nodejs.md#access-environment-variables) `process.env.MONGODB_URI` . 

Yerel MEAN.js deponuzda, üretim ortamına özel yapılandırma içeren _config/env/production.js_ (_config/env/local-production.js_ değil) dosyasını açın. Varsayılan MEAN.js uygulaması, zaten `MONGODB_URI` ortam değişkenini kullanmak üzere yapılandırılmıştır.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

Dağıtım işleminin `npm install` komutundan sonra [Gulp](https://gulpjs.com/)’ı çalıştırdığını fark etmişsinizdir. App Service, dağıtım sırasında Gulp veya Grunt görevlerini çalıştırmadığı için bu örnek depoyu etkinleştirmek üzere kök dizinde iki ek dosya bulunur: 

- _.deployment_ - Bu dosya, App Service’ten özel dağıtım betiği olarak `bash deploy.sh` komutunu çalıştırmasını ister.
- _deploy.sh_ - Özel dağıtım betiği. Dosyayı gözden geçirirseniz, `npm install` ve `bower install` komutundan sonra `gulp prod` çalıştırdığını görürsünüz. 

Git tabanlı dağıtımınıza herhangi bir adım eklemek için bu yaklaşımı kullanabilirsiniz. Azure uygulamanızı herhangi bir noktada yeniden başlatırsanız App Service Bu Otomasyon görevlerini yeniden çalıştırmaz. Daha fazla bilgi için bkz. [Grsıt/Bower/Gulp çalıştırma](configure-language-nodejs.md#run-gruntbowergulp).

### <a name="browse-to-the-azure-app"></a>Azure uygulamasına gidin 

Web tarayıcınızı kullanarak dağıtılan uygulamaya gidin. 

```bash 
http://<app-name>.azurewebsites.net 
``` 

Üst menüde **Kaydol**’a tıklayın ve bir işlevsiz kullanıcı oluşturun. 

Başarılı olursanız ve uygulama otomatik olarak oluşturulan kullanıcıda oturum açarsa, Azure'daki MEAN.js uygulamanızın MongoDB (Cosmos DB) veritabanına bağlantısı vardır. 

![Azure App Service’te çalışan MEAN.js uygulaması](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Birkaç makale eklemek için **Yönetici > Makaleleri Yönet**’i seçin. 

**Tebrikler!** Azure App Service’te veri temelli bir Node.js uygulaması çalıştırıyorsunuz.

## <a name="update-data-model-and-redeploy"></a>Veri modelini güncelleştirme ve yeniden dağıtma

Bu adımda, `article` veri modelini değiştiriyorsunuz ve değişikliklerinizi Azure'da yayımlıyorsunuz.

### <a name="update-the-data-model"></a>Veri modelini güncelleştirme

Yerel MEAN.js deponuzda, _modules/articles/server/models/article.server.model.js_ dosyasını açın.

`ArticleSchema` içinde, bir `String` type called `comment` ekleyin. İşiniz bittiğinde, şema kodunuz şu şekilde görünür:

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Makaleler kodunu güncelleştirme

`comment` kullanmak için `articles` kodunuzun kalanını güncelleştirin.

Değiştirmeniz gereken beş dosya vardır: sunucu denetleyici ve dört istemci görünümü. 

_modules/articles/server/controllers/articles.server.controller.js_ dosyasını açın.

`update` işlevinde, `article.comment` için bir atama ekleyin. Şu kod tamamlanmış `update` işlevini gösterir:

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

_modules/articles/client/views/view-article.client.view.html_ dosyasını açın.

`</section>` kapanış etiketinin hemen üzerinde, `comment` öğesini makale verilerinin geri kalanı ile birlikte görüntülemek için şu satırı ekleyin:

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

_modules/articles/client/views/list-articles.client.view.html_ dosyasını açın.

`</a>` kapanış etiketinin hemen üzerinde, `comment` öğesini makale verilerinin geri kalanı ile birlikte görüntülemek için şu satırı ekleyin:

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

_modules/articles/client/views/admin/list-articles.client.view.html_ dosyasını açın.

`<div class="list-group">` öğesinin içinde ve `</a>` kapanış etiketinin hemen üzerinde, `comment` öğesini makale verilerinin geri kalanıyla birlikte görüntülemek için şu satırı ekleyin:

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

_modules/articles/client/views/admin/form-article.client.view.html_ öğesini açın.

Gönder düğmesini içeren ve şuna benzeyen `<div class="form-group">` öğesini bulun:

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Bu etiketin hemen üzerinde, kişilerin `comment` alanını düzenleyebilmesini sağlayan başka bir `<div class="form-group">` öğesi ekleyin. Yeni öğeniz şöyle görünmelidir:

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Değişikliklerinizi yerel olarak test etme

Yaptığınız tüm değişiklikleri kaydedin.

Yerel terminal penceresinde, değişikliklerinizi üretim modunda yeniden test edin.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Bir tarayıcıda `http://localhost:8443` konumuna gidin ve oturum açtığınızdan emin olun.

**Yönetici > Makaleleri Yönet**’i seçin, ardından **+** düğmesini seçerek bir makale ekleyin.

Artık yeni `Comment` metin kutusunu görebilirsiniz.

![Makalelere eklenen açıklama alanı](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

Terminalde `Ctrl+C` yazarak Node.js’yi durdurun. 

### <a name="publish-changes-to-azure"></a>Değişiklikleri Azure’da yayımlama

Yerel terminal penceresinde, değişikliklerinizi Git’e işleyin ve ardından kod değişikliklerini Azure’a gönderin.

```bash
git commit -am "added article comment"
git push azure main
```

Tamamlandıktan sonra `git push` Azure uygulamanıza gidin ve yeni işlevleri deneyin.

![Azure’da yayımlanan model ve veritabanı değişiklikleri](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Önceden makale eklediyseniz, bunları yine de görüntüleyebilirsiniz. Cosmos DB’nizdeki mevcut veriler kaybolmaz. Ayrıca, veri şemasına yaptığınız güncelleştirmeler de kaybolmadığı gibi, mevcut verileriniz olduğu gibi bırakılır.

## <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma 

::: zone pivot="platform-windows"  

Node.js uygulamanız Azure App Service'te çalışırken, terminalinize yönlendirilen konsol günlüklerini alabilirsiniz. Böylece, uygulama hatalarını ayıklamanıza yardımcı olan tanılama iletilerinin aynısını alabilirsiniz.

Günlük akışını başlatmak için [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) Cloud Shell komutunu kullanın.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

Günlük akışı başladıktan sonra, Web trafiği almak için tarayıcıda Azure uygulamanızı yenileyin. Artık konsol günlüklerinin terminalinize yönlendirildiğini görebilirsiniz.

`Ctrl+C` yazarak günlük akışını istediğiniz zaman durdurabilirsiniz. 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>Azure uygulamanızı yönetme

Oluşturduğunuz uygulamayı görmek için [Azure Portal](https://portal.azure.com) gidin.

Sol menüden **uygulama hizmetleri**' ne ve ardından Azure uygulamanızın adına tıklayın.

![Azure uygulamasına portal gezintisi](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Varsayılan olarak, Portal uygulamanızın **genel bakış** sayfasını gösterir. Bu sayfa, uygulamanızın nasıl çalıştığını gösterir. Buradan ayrıca göz atma, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz. Sayfanın sol tarafındaki sekmeler, açabileceğiniz farklı yapılandırma sayfalarını gösterir.

![Azure portalında App Service sayfası](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Sonraki adımlar

Öğrendikleriniz:

> [!div class="checklist"]
> * Azure’da MongoDB veritabanı oluşturma
> * Node.js uygulamasını MongoDB’ye bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki günlüklerin terminalinize akışını sağlama
> * Uygulamayı Azure portalında yönetme

Uygulamaya özel bir DNS adı eşlemeyle ilgili bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"] 
> [Mevcut bir özel DNS adını Azure App Service eşleme](app-service-web-tutorial-custom-domain.md)

Ya da diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [Node.js uygulamasını yapılandırma](configure-language-nodejs.md)
