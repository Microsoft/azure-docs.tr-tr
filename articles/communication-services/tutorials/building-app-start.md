---
title: Öğretici-Azure Iletişim Hizmetleri için bir Web uygulaması hazırlama (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Azure Iletişim hizmetlerini destekleyen bir temel Web uygulaması oluşturma hakkında bilgi edinin
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a5a23d6a06c8cdff4deabac5251597b7ffe0c833
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728054"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Öğretici: Azure Iletişim Hizmetleri için bir Web uygulaması hazırlama (Node.js)

Uygulamalarınıza gerçek zamanlı iletişimler eklemek için Azure Iletişim Hizmetleri 'ni kullanabilirsiniz. Bu öğreticide, Azure Iletişim hizmetlerini destekleyen bir Web uygulamasını ayarlamayı öğreneceksiniz. Bu, gerçek zamanlı iletişimlerle çalışmaya başlamak isteyen yeni geliştiriciler için tanıtım amaçlı bir öğreticidir.

Bu öğreticinin sonuna kadar, Azure Iletişim Hizmetleri SDK 'Ları ile yapılandırılmış bir temel Web uygulamanız olacaktır. Böylece, gerçek zamanlı iletişim çözümünüzü oluşturmaya başlamak için bu uygulamayı kullanabilirsiniz.

Geri bildirim sağlamak için [Azure Communication Services GitHub sayfasını](https://github.com/Azure/communication) ziyaret edebilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Geliştirme ortamınızı yapılandırın.
> * Yerel bir Web sunucusu ayarlayın.
> * Azure Iletişim Hizmetleri paketlerini Web sitenize ekleyin.
> * Web sitenizi Azure statik Web siteleri 'ne yayımlayın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Ücretsiz hesap, herhangi bir hizmet birleşimini denemek için size Azure kredileri $200 sağlar.
- Yerel geliştirme ortamınızda kodu düzenlemede [Visual Studio Code](https://code.visualstudio.com/) .
- kodunuzu paketleyip yerel olarak barındırmak için [WebPack](https://webpack.js.org/) .
- Azure Iletişim Hizmetleri SDK 'Ları ve WebPack gibi bağımlılıkları yüklemek ve yönetmek için [Node.js](https://nodejs.org/en/) .
- Sürüm denetimini işleyecek [NVM ve NPM](/windows/nodejs/setup-on-windows) .
- Visual Studio Code için [Azure Storage uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) . Uygulamanızı Azure Storage 'da yayımlamak için bu uzantıya ihtiyacınız vardır. [Azure depolama 'da statik Web sitelerini barındırma hakkında daha fazla bilgi edinin](../../storage/blobs/storage-blob-static-website.md).
- [Azure App Service uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Uzantı, tam olarak yönetilen sürekli tümleştirme ve sürekli teslim (CI/CD) yapılandırma seçeneğiyle Web sitelerinin dağıtılmasına olanak tanır.
- Kendi sunucusuz uygulamalarınızı oluşturmak için [Azure işlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . Örneğin, kimlik doğrulama uygulamanızı Azure Işlevleri 'nde barındırabilirsiniz.
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturmayı öğrenin](../quickstarts/create-communication-resource.md).
- Bir Kullanıcı erişim belirteci. Yönergeler için bkz. [erişim belirteçleri oluşturma ve yönetme için hızlı başlangıç](../quickstarts/access-tokens.md?pivots=programming-language-javascript) veya [güvenilir bir kimlik doğrulama hizmeti oluşturma öğreticisi](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Geliştirme ortamınızı yapılandırma

Yerel geliştirme ortamınız şu şekilde yapılandırılır:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Geliştirme ortamının mimarisini gösteren diyagram.":::


### <a name="install-nodejs-nvm-and-npm"></a>Node.js, NVM ve NPM 'yi yükler

İstemci tarafı uygulamamız için gereken çeşitli bağımlılıkları indirmek ve yüklemek üzere Node.js kullanacağız. Daha sonra Azure 'da barındırdığımız statik dosyalar oluşturmak için bu uygulamayı kullanacağız, bu sayede sunucunuzda yapılandırma konusunda endişelenmenize gerek kalmaz.

Windows geliştiricileri, düğüm, NVM ve NPM 'yi yapılandırmak için [bu Node.js öğreticisini](/windows/nodejs/setup-on-windows) izleyebilir. 

Bu öğreticide, LTS 12.20.0 sürümü temel alınarak hesaplanır. NVM 'yi yükledikten sonra, kullanmak istediğiniz sürümü dağıtmak için aşağıdaki PowerShell komutunu kullanın:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Düğüm sürümü dağıtmaya yönelik komutları gösteren ekran görüntüsü.":::

### <a name="configure-visual-studio-code"></a>Visual Studio Code’u yapılandırma

[Visual Studio Code](https://code.visualstudio.com/) , [desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms)birine indirebilirsiniz.

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Azure Iletişim Hizmetleri projeleriniz için bir çalışma alanı oluşturma

Proje dosyalarınızı depolamak için şöyle bir klasör oluşturun: `C:\Users\Documents\ACS\CallingApp` . Visual Studio Code ' de, **Dosya**  >  **klasörü Ekle** ' yi seçin ve klasörü çalışma alanınıza ekleyin.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Bir çalışma alanına dosya ekleme seçimlerini gösteren ekran görüntüsü.":::

Sol bölmedeki **Gezgin** ' e gidin ve `CallingApp` klasörünüzü **Adsız** çalışma alanında görürsünüz.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Gezgin ve başlıksız çalışma alanını gösteren ekran görüntüsü.":::

Çalışma alanınızın adını güncelleştirmeyi ücretsiz olarak hissetmekten çekinmeyin. Node.js sürümünüzü, klasörünüze sağ tıklayıp `CallingApp` **Tümleşik terminalde aç '** ı seçerek doğrulayabilirsiniz.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Tümleşik terminalde bir klasörü açmaya yönelik seçimi gösteren ekran görüntüsü.":::

Terminalde, önceki adımda yüklü Node.js sürümünü doğrulamak için aşağıdaki komutu girin:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Düğüm sürümünün doğrulanmasını gösteren ekran görüntüsü.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Visual Studio Code için Azure uzantıları 'nı yükler

[Azure Storage uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) Visual Studio Marketi aracılığıyla veya **Visual Studio Code (**  >  **uzantıları**  >  **Azure Storage**) aracılığıyla yükler.

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Azure Storage uzantısını yüklemeye yönelik düğmeyi gösteren ekran görüntüsü.":::

[Azure işlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) ve [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) uzantılarına yönelik aynı adımları izleyin.


## <a name="set-up-a-local-web-server"></a>Yerel bir Web sunucusu ayarlama

### <a name="create-an-npm-package"></a>NPM paketi oluşturma

Terminalinizde, çalışma alanı klasörünüzün yolundan şunu girin:

``` console
npm init -y
```

Bu komut yeni bir NPM paketi başlatır ve `package.json` projenizin kök klasörüne ekler.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="J S O N. paketi gösteren ekran görüntüsü.":::

Hakkında daha fazla belge için `npm init` , [Bu komutun NPM docs sayfasına](https://docs.npmjs.com/cli/v6/commands/npm-init)bakın.

### <a name="install-webpack"></a>WebPack 'i yükler

[Web Pack](https://webpack.js.org/) 'i kullanarak kodu Azure 'a dağıtabileceğiniz statik dosyalara paketleyebilirsiniz. Ayrıca, çağıran örnekle kullanmak üzere yapılandıracağınız bir geliştirme sunucusuna sahiptir.

Terminalinizde, Web paketini yüklemek için aşağıdaki komutu girin:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Bu öğretici, önceki komutta belirtilen sürümlerle test edilmiştir. Belirtme `-dev` , paket yöneticisine bu bağımlılığın geliştirme amaçlarıyla olduğunu ve Azure 'a dağıttığınız koda eklenmeyeceğini söyler.

Dosyanıza iki yeni paket eklendiğini görürsünüz `package.json` `devDependencies` . Paketler `./CallingApp/node_modules/` dizine yüklenir.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="WebPack yapılandırmasını gösteren ekran görüntüsü.":::

### <a name="configure-the-development-server"></a>Geliştirme sunucusunu yapılandırma

Tarayıcınızla bir statik uygulama (dosyanız gibi `index.html` ) çalıştırıldığında `file://` protokol kullanılır. NPM modüllerinizin düzgün çalışması için, Web Pack 'i yerel bir geliştirme sunucusu olarak kullanarak HTTP protokolüne ihtiyacınız olacaktır.

İki yapılandırma oluşturacaksınız: bir geliştirme ve diğeri de üretim için. Üretim için hazırlanan dosyalar, kullanılmayan boşlukları ve karakterleri kaldıracaksınız. Bu yapılandırma, gecikme süresinin küçültülmesinin veya kodun karıştırılmış olması gereken üretim senaryoları için uygundur.

`webpack-merge` [Web paketi için farklı yapılandırma dosyalarıyla](https://webpack.js.org/guides/production/)çalışmak üzere aracını kullanacaksınız.

Geliştirme ortamıyla başlayalım. İlk olarak yüklemeniz gerekir `webpack merge` . Terminalinizde aşağıdaki komutu çalıştırın:

```Console
npm install --save-dev webpack-merge
```

`package.json`Dosyanızda, ' ye bir bağımlılık eklendiğini görebilirsiniz `devDependencies` .

Ardından, adlı bir dosya oluşturun `webpack.common.js` ve aşağıdaki kodu ekleyin:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

Ardından, her yapılandırma için bir tane olmak üzere iki dosya ekleyin:

* `webpack.dev.js`
* `webpack.prod.js`

Şimdi `webpack.dev.js` aşağıdaki kodu dosyaya ekleyerek dosyayı değiştirin:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Bu yapılandırmada, ' den ortak parametreleri içeri aktarır, `webpack.common.js` iki dosyayı birleştirir, modu olarak ayarlayın `development` ve kaynak eşlemini olarak yapılandırın `inline-source-map` .

Geliştirme modu, WebPack 'in dosyaları küçültmeye ve en iyi duruma getirilmiş üretim dosyalarını üretmeyeceğini söyler. Web paketi modundaki ayrıntılı belgeleri [WebPack modundaki Web sayfasında](https://webpack.js.org/configuration/mode/)bulabilirsiniz.

Kaynak eşleme seçenekleri [WebPack DevTool Web sayfasında](https://webpack.js.org/configuration/devtool/#root)listelenir. Kaynak eşlemesinin ayarlanması, tarayıcınızda hata ayıklamanızı kolaylaştırır.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="WebPack 'i yapılandırmaya yönelik kodu gösteren ekran görüntüsü.":::

Geliştirme sunucusunu çalıştırmak için adresine gidin `package.json` ve aşağıdaki kodu ekleyin `scripts` :

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Dosyanız şu şekilde görünmelidir:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

NPM 'den kullanılabilecek komutu eklediniz.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="package.jsdeğişikliğini gösteren ekran görüntüsü.":::

### <a name="test-the-development-server"></a>Geliştirme sunucusunu test etme

 Visual Studio Code, projenizin altında üç dosya oluşturun:

* `index.html`
* `app.js`
* `app.css` (uygulamanızı stillendirme için isteğe bağlı)

Bu kodu içine yapıştırın `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="H T M L dosyasını gösteren ekran görüntüsü.":::

Aşağıdaki kodu `app.js` dosyasına ekleyin:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Aşağıdaki kodu `app.css` dosyasına ekleyin:

```CSS
html {
    font-family: sans-serif;
  }
```
Kaydetmeyi unutmayın! Kaydedilmemiş dosya, Gezgin 'deki dosya adlarının yanında bulunan beyaz noktalarla gösterilir.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="JavaScript kodlu App.js dosyasını gösteren ekran görüntüsü.":::

Bu sayfayı açtığınızda, iletinizin tarayıcınızın konsolunda bir uyarıyla görüntülendiğini görmeniz gerekir.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App. css dosyasını gösteren ekran görüntüsü.":::

Geliştirme yapılandırmanızı test etmek için aşağıdaki terminal komutunu kullanın:

```Console
npm run build:dev
```

Konsol, sunucunun nerede çalıştığını gösterir. Varsayılan olarak, bu `http://localhost:8080` . `build:dev`Bu komut, `package.json` daha önce eklediğiniz komuttur.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Geliştirme sunucusu başlangıcını gösteren ekran görüntüsü.":::
 
Tarayıcınızdaki adrese gidin ve önceki adımlarda yapılandırılan sayfayı ve uyarıyı görmeniz gerekir.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="H T M L sayfasının ekran görüntüsü.":::
  
 
Sunucu çalışırken, kodu ve sunucuyu değiştirebilirsiniz. HTML sayfası otomatik olarak yeniden yüklenir. 

Sonra, `app.js` Visual Studio Code dosyasına gidin ve silin `alert('Hello world alert!');` . Dosyanızı kaydedin ve uyarının tarayıcıdan kaybolduğunu doğrulayın.

Sunucunuzu durdurmak için `Ctrl+C` terminalinizde çalıştırabilirsiniz. Sunucunuzu başlatmak için `npm run build:dev` dilediğiniz zaman girin.

## <a name="add-the-azure-communication-services-packages"></a>Azure Iletişim Hizmetleri paketlerini ekleyin

`npm install`JavaScript için SDK 'Yı çağıran Azure Iletişim hizmetlerini yüklemek için komutunu kullanın.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Bu eylem, Azure Iletişim Hizmetleri ortak ve çağrı paketlerini paketinizin bağımlılıkları olarak ekler. Dosyaya iki yeni paket eklendiğini göreceksiniz `package.json` . `npm install` [Bu komut için NPM docs sayfasında](https://docs.npmjs.com/cli/v6/commands/npm-install)hakkında daha fazla bilgi bulabilirsiniz.

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Azure Iletişim Hizmetleri paketlerinin yüklenmesine yönelik kodu gösteren ekran görüntüsü.":::

Bu paketler, Azure Iletişim Hizmetleri ekibi tarafından sağlanır ve kimlik doğrulama ve çağırma kitaplıklarını içerir. `--save`Komut, uygulamanın bu paketlere üretim kullanımı için bağlı olduğunu ve dosyaya dahil edileceğini bildirir `devDependencies` `package.json` . Uygulamayı üretim için yapılandırdığınızda, paketler üretim kodunuza dahil edilir.


## <a name="publish-your-website-to-azure-static-websites"></a>Web sitenizi Azure statik Web siteleri 'ne yayımlama

### <a name="create-a-configuration-for-production-deployment"></a>Üretim dağıtımı için bir yapılandırma oluşturma

Aşağıdaki kodu `webpack.prod.js` dosyasına ekleyin:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Bu yapılandırma ile birleştirilecek `webpack.common.js` (giriş dosyasını belirttiğiniz ve sonuçların depolanacağı konum). Yapılandırma ayrıca modunu olarak ayarlar `production` .
 
İçinde `package.json` , aşağıdaki kodu ekleyin:

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

Dosyanız şu şekilde görünmelidir:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Yapılandırılmış dosyaları gösteren ekran görüntüsü.":::


Terminalde şunu çalıştırın:

```Console
npm run build:prod
```

Komut `dist` içinde bir klasör ve üretime Ready bir `app.js` statik dosya oluşturur. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Üretim derlemesini gösteren ekran görüntüsü.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Uygulamanızı Azure Storage 'a dağıtma

`index.html`Klasörünü ve `app.css` klasörüne kopyalayın `dist` .

`dist`Klasörde bir dosya oluşturun ve bu dosyayı adlandırın `404.html` . Aşağıdaki biçimlendirmeyi bu dosyaya kopyalayın:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Dosyayı kaydedin (CTRL + S).

Klasöre sağ tıklayın `dist` ve **Azure depolama aracılığıyla statik Web sitesine dağıt**' ı seçin.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Azure 'a dağıtmaya başlamak için seçimlerin ekran görüntüsü.":::
 
**Abonelik seçin** altında **Azure 'da oturum aç** ' ı seçin (veya daha önce bir abonelik oluşturmadıysanız **ücretsiz bir Azure hesabı oluşturun** ).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Azure 'da oturum açma seçimlerini gösteren ekran görüntüsü.":::
 
**Yeni depolama hesabı oluştur**  >  **Gelişmiş**' i seçin.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Depolama hesabı grubunu oluşturma seçimlerini gösteren ekran görüntüsü.":::
 
Depolama grubunun adını belirtin.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Hesap için ad eklemeyi gösteren ekran görüntüsü.":::
 
Gerekirse yeni bir kaynak grubu oluşturun.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Yeni bir kaynak grubu oluşturmak için seçimi gösteren ekran görüntüsü.":::
  
**Statik Web sitesi barındırmayı etkinleştirmek** istiyor musunuz?, **Evet**' i seçin.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Statik Web sitesi barındırmayı etkinleştirme seçeneğinin seçildiğinin gösterildiği ekran görüntüsü.":::
  
**Dizin belgesi adını girmek** için varsayılan dosya adını kabul edin. Dosyayı zaten oluşturdunuz `index.html` .

**404 hata belge yolunu girmek** için **404.html** girin.  
  
Uygulamanın konumunu seçin. Seçtiğiniz konum, gelecekte grup çağrılarında uygulamanızı çağırmak için hangi medya işlemcisinin kullanılacağını tanımlayacaktır. 

Azure Iletişim Hizmetleri, uygulama konumuna göre medya işlemcisini seçer.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Konumların listesini gösteren ekran görüntüsü.":::
  
Kaynak ve Web siteniz oluşturuluncaya kadar bekleyin. 
 
**Web sitesine gidin**' i seçin.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Bir Web sitesine göz atmaya yönelik düğme ile dağıtımın tamamlandığını belirten bir ileti gösteren ekran görüntüsü.":::
 
Tarayıcınızın geliştirme araçlarından kaynağı inceleyebilir ve üretime hazırlanan dosyayı görebilirsiniz.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Dosya içeren Web sitesi kaynağının ekran görüntüsü.":::

[Azure Portal](https://portal.azure.com/#home)gidin, kaynak grubunuzu seçin ve oluşturduğunuz uygulamayı seçin. Ardından **Ayarlar**  >  **statik Web sitesi**' ni seçin. Statik web sitelerinin etkin olduğunu görebilirsiniz. Birincil uç nokta, Dizin belgesi adı ve hata belgesi yolunu aklınızda edin.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Statik Web sitesi seçimini gösteren ekran görüntüsü.":::

**Blob hizmeti** bölümünden **Kapsayıcılar**’ı seçin. Liste, biri Günlükler ( `$logs` ) ve biri Web sitenizin () içeriği için oluşturulan iki kapsayıcıyı gösterir `$web` .

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Kapsayıcı yapılandırmasını gösteren ekran görüntüsü.":::

`$web`Kapsayıcıyı açarsanız, Visual Studio 'da oluşturduğunuz ve Azure 'a dağıttığınız dosyaları görürsünüz. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Azure 'a dağıtılan dosyaları gösteren ekran görüntüsü.":::

Uygulamayı dilediğiniz zaman Visual Studio Code yeniden dağıtabilirsiniz.

Şimdi ilk Azure Communication Services Web uygulamanızı oluşturmaya hazırsınız.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulamanıza sesli arama ekleme](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Ayrıca şunları yapmak isteyebilirsiniz:

- [Uygulamanıza sohbet ekleme](../quickstarts/chat/get-started.md)
- [Kullanıcı erişim belirteçleri oluştur](../quickstarts/access-tokens.md)
- [İstemci ve sunucu mimarisi hakkında bilgi edinin](../concepts/client-and-server-architecture.md)
- [Kimlik doğrulaması hakkında bilgi edinin](../concepts/authentication.md)
