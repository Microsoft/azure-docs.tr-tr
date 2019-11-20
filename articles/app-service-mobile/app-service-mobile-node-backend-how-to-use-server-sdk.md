---
title: Mobile Apps için Node. js arka uç sunucu SDK 'Sı ile çalışma | Microsoft Docs
description: Azure App Service Mobile Apps için Node. js arka uç sunucu SDK 'Sı ile nasıl çalışacağınızı öğrenin.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a3883d233bd621607ec724e0c85734b508195340
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388669"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Mobile Apps Node. js SDK 'sını kullanma

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
>  Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

Bu makalede, Azure App Service Mobile Apps özelliğindeki Node. js arka ucu ile çalışmayı gösteren ayrıntılı bilgiler ve örnekler sağlanmaktadır.

## <a name="Introduction"></a>Giriş

Mobile Apps, bir Web uygulamasına mobil olarak iyileştirilmiş veri erişimi Web API 'SI ekleme özelliği sağlar. Mobile Apps SDK, ASP.NET ve Node. js web uygulamaları için sağlanır. SDK aşağıdaki işlemleri sağlar:

* Veri erişimi için tablo işlemleri (okuma, ekleme, güncelleştirme, silme)
* Özel API işlemleri

Her iki işlem de Azure App Service izin verdiği tüm kimlik sağlayıcıları genelinde kimlik doğrulaması sağlar. Bu sağlayıcılar, Facebook, Twitter, Google ve Microsoft gibi sosyal kimlik sağlayıcılarını ve ayrıca kurumsal kimlik Azure Active Directory içerir.

Her kullanım örneğinin örneğini [GitHub 'da Samples dizini]bulabilirsiniz.

## <a name="supported-platforms"></a>Desteklenen platformlar

Mobile Apps Node. js SDK 'Sı, düğüm ve üzeri için geçerli LTS sürümünü destekler. Şu anda, en son LTS sürümü node v 4.5.0. Diğer düğüm sürümleri çalışabilir ancak desteklenmez.

Mobile Apps Node. js SDK 'Sı iki veritabanı sürücüsünü destekler: 

* Node-MSSQL sürücüsü Azure SQL veritabanı ve yerel SQL Server örneklerini destekler.  
* SQLite3 sürücüsü yalnızca tek bir örnekteki SQLite veritabanlarını destekler.

### <a name="howto-cmdline-basicapp"></a>Komut satırını kullanarak temel bir Node. js arka ucu oluşturma

Her Mobile Apps Node. js arka ucu bir ExpressJS uygulaması olarak başlatılır. ExpressJS, Node. js için kullanılabilen en popüler Web hizmeti çerçevesidir. Temel bir [Express] uygulama oluşturmak için aşağıdaki adımları izleyin:

1. Bir komut veya PowerShell penceresinde, projeniz için bir dizin oluşturun:

        mkdir basicapp

1. Paket yapısını başlatmak için `npm init` ' i çalıştırın:

        cd basicapp
        npm init

   @No__t-0 komutu, projeyi başlatmak için bir soru kümesi ister. Örnek çıktıyı inceleyin:

   ![NPM init çıkışı][0]

1. NPM deposundan `express` ve `azure-mobile-apps` kitaplıklarını yükler:

        npm install --save express azure-mobile-apps

1. Temel mobil sunucuyu uygulamak için bir App. js dosyası oluşturun:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Bu uygulama, dinamik bir şema kullanarak temel bir SQL veri deposuna kimliği doğrulanmamış erişim sağlayan tek bir uç nokta (`/tables/TodoItem`) ile mobil olarak iyileştirilmiş bir Web API 'SI oluşturur. İstemci kitaplığı hızlı başlangıçlarını takip etmek için uygundur:

* [Android istemci hızlı başlangıç]
* [Apache Cordova istemci hızlı başlangıcı]
* [iOS Istemci hızlı başlangıç]
* [Windows Mağazası istemci hızlı başlangıç]
* [Xamarin. iOS istemci hızlı başlangıç]
* [Xamarin. Android istemci hızlı başlangıç]
* [Xamarin. Forms istemci hızlı başlangıç]

Bu temel uygulama için kodu [GitHub 'da basicapp örneği]bulabilirsiniz.

### <a name="howto-vs2015-basicapp"></a>Visual Studio 2015 kullanarak bir Node. js arka ucu oluşturma

Visual Studio 2015, IDE içinde Node. js uygulamaları geliştirmek için bir uzantı gerektirir. Başlamak için, [Visual Studio Için Node. js araçları 1,1]' yi yüklemelisiniz. Yüklemeyi bitirdiğinizde, bir Express 4. x uygulaması oluşturun:

1. **Yeni proje** iletişim kutusunu açın ( **dosyadan** > **Yeni** > **projesi**).
1. **Şablonlar** > **JavaScript** > **Node. js**' yi genişletin.
1. **Temel Azure Node. js Express 4 uygulaması**' nı seçin.
1. Proje adını girin. **Tamam**’ı seçin.

   ![Visual Studio 2015 yeni proje][1]
1. **NPM** düğümüne sağ tıklayın ve **Yeni NPM paketlerini yüklensin**' i seçin.
1. İlk Node. js uygulamanızı oluşturduktan sonra NPM kataloğunu yenilemeniz gerekebilir. Gerekirse **Yenile** ' yi seçin.
1. Arama kutusuna **Azure-Mobil-Apps** yazın. **Azure-Mobile-Apps 2.0.0** paketini ve ardından **paketi Kur**' u seçin.

   ![Yeni NPM paketleri 'ni yükler][2]
1. **Kapat**' ı seçin.
1. Mobile Apps SDK desteği eklemek için App. js dosyasını açın. Kitaplık `require` deyimlerinin altındaki 6. satırda aşağıdaki kodu ekleyin:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Diğer `app.use` deyimlerinden sonra yaklaşık satır 27 ' de aşağıdaki kodu ekleyin:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Dosyayı kaydedin.

1. Uygulamayı yerel olarak çalıştırın (API `http://localhost:3000` ' a sunulur) veya Azure 'da yayımlayın.

### <a name="create-node-backend-portal"></a>Azure portal kullanarak bir Node. js arka ucu oluşturun

[Azure portalda]bir Mobile Apps arka ucu oluşturabilirsiniz. [Mobil uygulama oluşturma](app-service-mobile-ios-get-started.md) öğreticisini izleyerek aşağıdaki adımları tamamlayabilir veya bir istemci ve sunucu oluşturabilirsiniz. Öğretici bu yönergelerin basitleştirilmiş bir sürümünü içerir ve en iyisi kavram kanıtı projelerine yöneliktir.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

**Başlarken** bölmesine geri döndüğünüzde **tablo API 'si oluştur**altında, arka uç diliniz olarak **Node. js** ' yi seçin.
**Bu, tüm site içeriklerinin üzerine yazacak olduğunu kabul ediyorum**kutusunu seçin ve ardından **TodoItem tablosu oluştur**' u seçin.

### <a name="download-quickstart"></a>Git kullanarak Node. js arka uç hızlı başlangıç kodu projesini indirin

Portal 'ın **hızlı başlangıç** bölmesini kullanarak bir Node. js Mobile Apps arka ucu oluşturduğunuzda, sizin Için bir Node. js projesi oluşturulur ve sitenize dağıtılır. Portalda, tablo ve API 'Ler ekleyebilir ve Node. js arka ucu için kod dosyalarını düzenleyebilirsiniz. Ayrıca, arka uç projesini indirmek için, tabloları ve API 'Leri eklemek ya da değiştirmek ve ardından projeyi yeniden yayımlamak için çeşitli dağıtım araçlarını kullanabilirsiniz. Daha fazla bilgi için [Azure App Service dağıtım kılavuzu]bakın.

Aşağıdaki yordam hızlı başlangıç proje kodunu indirmek için bir git deposu kullanır:

1. Henüz yapmadıysanız git 'i yükleyebilirsiniz. Git 'i yüklemek için gereken adımlar işletim sistemleri arasında farklılık gösterir. İşletim sistemine özgü dağıtımlar ve yükleme kılavuzu için bkz. [Git yükleme](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Arka uç siteniz için Git deposunu etkinleştirmek üzere [deponuzu hazırlama](../app-service/deploy-local-git.md#prepare-your-repository) bölümüne bakın. Dağıtım Kullanıcı adı ve parolasını bir yere göz önünde alın.
3. Arka ucu Mobile Apps bölmesinde, **Git kopyası URL 'si** ayarını bir yere getirin.
4. Git kopya URL 'sini kullanarak `git clone` komutunu yürütün. Gerektiğinde parolanızı aşağıdaki örnekte olduğu gibi girin:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Yerel dizine göz atarak (önceki örnekte `/todolist`) ve proje dosyalarının indirildiğine dikkat edin. @No__t-0 dizininde TodoItem. json dosyasını bulun. Bu dosya, tablodaki izinleri tanımlar. Ayrıca, TodoItem. js dosyasını aynı dizinde bulabilirsiniz. Tablo için CRUD işlem betikleri tanımlar.
6. Proje dosyalarında değişiklik yaptıktan sonra, değişiklikleri eklemek, kaydetmek ve sonra siteye yüklemek için aşağıdaki komutları çalıştırın:

        $ git commit -m "updated the table script"
        $ git push origin master

   Projeye yeni dosyalar eklediğinizde önce `git add .` komutunu çalıştırmanız gerekir.

Siteye her yeni bir işleme kümesi gönderildiğinde site yeniden yayımlandı.

### <a name="howto-publish-to-azure"></a>Node. js arka ucu 'nı Azure 'da yayımlayın

Microsoft Azure, Azure hizmetine Mobile Apps Node. js arka ucu yayımlamak için birçok mekanizma sağlar. Bu mekanizmalar, Visual Studio, komut satırı araçları ve kaynak denetimine dayalı sürekli dağıtım seçenekleriyle tümleştirilmiş dağıtım araçlarını içerir. Daha fazla bilgi için [Azure App Service dağıtım kılavuzu]bakın.

Azure App Service, arka ucu yayımlamadan önce incelemeniz gereken Node. js uygulamalarına yönelik belirli bir öneriye sahiptir:

* [Düğüm sürümünü belirtin]
* [Düğüm modüllerini kullanma]

### <a name="howto-enable-homepage"></a>Uygulamanız için bir giriş sayfası etkinleştirme

Birçok uygulama, Web ve mobil uygulamaların bir birleşimidir. ExpressJS çerçevesini iki model birleştirmek için kullanabilirsiniz. Ancak, bazen yalnızca bir mobil arabirim uygulamak isteyebilirsiniz. App Service 'in çalışır olduğundan emin olmak için bir giriş sayfası sağlamak yararlı olur. Kendi giriş sayfanızı verebilir ya da geçici bir giriş sayfasını etkinleştirebilirsiniz. Geçici bir giriş sayfasını etkinleştirmek için aşağıdaki kodu kullanarak Mobile Apps örneğini oluşturun:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Bu seçeneği yalnızca yerel olarak geliştirilirken kullanılabilir olmasını istiyorsanız, azureMobile. js dosyanıza bu ayarı ekleyebilirsiniz.

## <a name="TableOperations"></a>Tablo işlemleri

Azure-Mobile-Apps Node. js sunucu SDK 'sı, Azure SQL veritabanında depolanan veri tablolarını Web API 'SI olarak kullanıma sunma mekanizmaları sağlar. Beş işlem sağlar:

| İşlem | Açıklama |
| --- | --- |
| /Tables/*TABLENAME* al |Tablodaki tüm kayıtları alın. |
| /Tables/*TableName*/: ID Al |Tablodaki belirli bir kaydı alır. |
| POST/Tables/*TableName* |Tabloda bir kayıt oluşturun. |
| PATCH/Tables/*TableName*/: ID |Tablodaki bir kaydı güncelleştirin. |
| /Tables/*TableName*/: ID silme |Tablodaki bir kaydı silin. |

Bu Web API 'SI [OData] 'i destekler ve [Çevrimdışı veri eşitleme]desteklemek için tablo şemasını genişletir.

### <a name="howto-dynamicschema"></a>Dinamik şema kullanarak tabloları tanımlama

Bir tabloyu kullanabilmeniz için önce onu tanımlamanız gerekir. Tabloları statik bir şema (şemada sütunları tanımladığınız) veya dinamik olarak (SDK 'nın, gelen istekleri temel alarak şemayı denetlediğini) kullanarak tanımlayabilirsiniz. Ayrıca, tanım için JavaScript kodu ekleyerek Web API 'sinin belirli yönlerini kontrol edebilirsiniz.

En iyi uygulama olarak, her tabloyu `tables` dizinindeki bir JavaScript dosyasında tanımlamanız ve sonra tabloları içeri aktarmak için `tables.import()` metodunu kullanmanız gerekir. Temel uygulama örneğini genişleterek App. js dosyasını ayarlayabilirsiniz:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Tabloyu./tables/TodoItem.js içinde tanımlayın:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tablolar varsayılan olarak dinamik bir şema kullanır. Dinamik şemayı genel olarak kapatmak için, Azure portal `MS_DynamicSchema` uygulama ayarını false olarak ayarlayın.

[GitHub üzerinde Todo örneği]bir örnek bulabilirsiniz.

### <a name="howto-staticschema"></a>Statik şema kullanarak tabloları tanımlama

Web API 'SI aracılığıyla kullanıma sunulacak sütunları açıkça tanımlayabilirsiniz. Azure-Mobile-Apps Node. js SDK 'Sı, sağladığınız listeye çevrimdışı veri eşitleme için gereken ek sütunları otomatik olarak ekler. Örneğin, hızlı başlangıç istemci uygulamaları iki sütunlu bir tablo gerektirir: `text` (bir dize) ve `complete` (Boolean).  
Tablo, tablo tanımı JavaScript dosyasında (`tables` dizininde bulunur) aşağıdaki gibi tanımlanabilir:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Tabloları statik olarak tanımlarsanız, Başlangıçta veritabanı şemasını oluşturmak için `tables.initialize()` yöntemini çağırmanız gerekir. @No__t-0 yöntemi, Web hizmetinin, veritabanı başlatılmadan önce isteklere hizmet verebilmesi için bir [Ünü] döndürür.

### <a name="howto-sqlexpress-setup"></a>Yerel makinenizde geliştirme veri deposu olarak SQL Server Express kullanın

Mobile Apps Node. js SDK 'Sı, verileri kutudan kullanıma sunmak için üç seçenek sunar:

* Kalıcı olmayan örnek bir depo sağlamak için **bellek** sürücüsünü kullanın.
* Geliştirme için bir SQL Server Express veri deposu sağlamak üzere **MSSQL** sürücüsünü kullanın.
* Üretim için bir Azure SQL veritabanı veri deposu sağlamak üzere **MSSQL** sürücüsünü kullanın.

Mobile Apps Node. js SDK 'Sı, hem SQL Server Express hem de SQL veritabanı ile bağlantı kurmak ve kullanmak için [MSSQL Node. js paketi] kullanır. Bu paket, SQL Server Express örneğiniz üzerinde TCP bağlantılarını etkinleştirmenizi gerektirir.

> [!TIP]
> Bellek sürücüsü, test için kapsamlı bir tesis kümesi sağlamaz. Arka uca yerel olarak test etmek istiyorsanız SQL Server Express veri deposunun ve MSSQL sürücüsünün kullanımını öneririz.

1. [Microsoft SQL Server 2014 Express]indirip yükleyin. SQL Server 2014 Express 'ı Tools Edition ile yüklediğinizden emin olun. Açıkça 64 bit desteğine ihtiyacınız yoksa, 32 bit sürümü çalıştırırken daha az bellek tüketir.
1. SQL Server 2014 Configuration Manager çalıştırın:

   a. Ağaç menüsündeki **SQL Server ağ yapılandırması** düğümünü genişletin.

   b. **SQLExpress Için protokolleri**seçin.

   c. **TCP/IP** öğesine sağ tıklayın ve **Etkinleştir**' i seçin. Açılır iletişim kutusunda **Tamam ' ı** seçin.

   d. **TCP/IP** ' ye sağ tıklayın ve **Özellikler**' i seçin.

   e. **IP adresleri** sekmesini seçin.

   f. **IPAll** düğümünü bulun. **TCP bağlantı noktası** alanına **1433**yazın.

      ![TCP/IP için SQL Server Express yapılandırma][3]

   g. **Tamam**’ı seçin. Açılır iletişim kutusunda **Tamam ' ı** seçin.

   h. Ağaç menüsünde **SQL Server Hizmetleri** ' ni seçin.

   i. **SQL Server (SQLEXPRESS)** öğesine sağ tıklayın ve **Yeniden Başlat**' ı seçin.

   j. SQL Server 2014 Configuration Manager kapatın.

1. SQL Server 2014 Management Studio çalıştırın ve yerel SQL Server Express örneğinize bağlanın:

   1. Nesne Gezgini ' de örneğinizi sağ tıklayın ve **Özellikler**' i seçin.
   1. **Güvenlik** sayfasını seçin.
   1. **SQL Server ve Windows kimlik doğrulaması modunun** seçili olduğundan emin olun.
   1. **Tamam**’ı seçin.

      ![SQL Server Express kimlik doğrulamasını yapılandırma][4]
   1. Nesne Gezgini 'de **güvenlik** > **oturum açmaları** ' ı genişletin.
   1. **Oturum açmalar** ' a sağ tıklayıp **yeni oturum açma**' yı seçin.
   1. Bir oturum açma adı girin. **SQL Server kimlik doğrulaması**’nı seçin. Bir parola girin ve parolayı **Onayla**alanına aynı parolayı girin. Parolanın Windows karmaşıklık gereksinimlerini karşılaması gerekir.
   1. **Tamam**’ı seçin.

      ![SQL Server Express yeni bir kullanıcı ekleyin][5]
   1. Yeni oturum açma bilgilerinizi sağ tıklatın ve **Özellikler**' i seçin.
   1. **Sunucu rolleri** sayfasını seçin.
   1. **Dbcreator** sunucu rolünün onay kutusunu seçin.
   1. **Tamam**’ı seçin.
   1. SQL Server 2015 Management Studio kapatın.

Seçtiğiniz kullanıcı adını ve parolayı kaydettiğinizden emin olun. Veritabanı gereksinimlerinize bağlı olarak, ek sunucu rolleri veya izinleri atamanız gerekebilir.

Node. js uygulaması, bu veritabanı için bağlantı dizesi için `SQLCONNSTR_MS_TableConnectionString` ortam değişkenini okur. Bu değişkeni ortamınızda ayarlayabilirsiniz. Örneğin, bu ortam değişkenini ayarlamak için PowerShell kullanabilirsiniz:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Bir TCP/IP bağlantısı üzerinden veritabanına erişin. Bağlantı için bir Kullanıcı adı ve parola girin.

### <a name="howto-config-localdev"></a>Projenizi yerel geliştirme için yapılandırma

Mobile Apps, yerel dosya sisteminden *azureMobile. js* adlı bir JavaScript dosyasını okur. Üretimde Mobile Apps SDK 'Yı yapılandırmak için bu dosyayı kullanmayın. Bunun yerine, [Azure portalda] **uygulama ayarlarını** kullanın.

AzureMobile. js dosyası bir yapılandırma nesnesini dışarı aktarmalıdır. En yaygın ayarlar şunlardır:

* Veritabanı ayarları
* Tanılama günlük kaydı ayarları
* Alternatif CORS ayarları

Bu örnek **azureMobile. js** dosyası önceki veritabanı ayarlarını uygular:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

Parolaların bulutta depolanmasını engellemek için, **. gitignore** dosyanıza (veya diğer kaynak kodu denetimini yoksay dosyasına) **azureMobile. js** eklemenizi öneririz. [Azure portalda]içindeki **uygulama ayarlarında** her zaman üretim ayarlarını yapılandırın.

### <a name="howto-appsettings"></a>Mobil uygulamanız için uygulama ayarlarını yapılandırma

AzureMobile. js dosyasındaki çoğu ayar [Azure portalda]eşdeğer bir uygulama ayarına sahiptir. Uygulamanızı **uygulama ayarları**'nda yapılandırmak için aşağıdaki listeyi kullanın:

| Uygulama ayarı | azureMobile. js ayarı | Açıklama | Geçerli değerler |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |ad |Uygulamanın adı |string |
| **MS_MobileLoggingLevel** |günlüğe kaydetme. düzeyi |Günlüğe kaydedilecek en düşük ileti günlük düzeyi |hata, uyarı, bilgi, ayrıntılı, hata ayıklama, Silly |
| **MS_DebugMode** |H |Hata ayıklama modunu etkinleştirilir veya devre dışı bırakır |doğru, yanlış |
| **MS_TableSchema** |Data. Schema |SQL tabloları için varsayılan şema adı |dize (varsayılan: dbo) |
| **MS_DynamicSchema** |Data. dynamicSchema |Hata ayıklama modunu etkinleştirilir veya devre dışı bırakır |doğru, yanlış |
| **MS_DisableVersionHeader** |sürüm (tanımsız olarak ayarlanır) |X-ZUMO-Server-Version üst bilgisini devre dışı bırakır |doğru, yanlış |
| **MS_SkipVersionCheck** |SkipVersionCheck |İstemci API 'SI sürüm denetimini devre dışı bırakır |doğru, yanlış |

Bir uygulama ayarı ayarlamak için:

1. [Azure portalda]’ında oturum açın.
1. **Tüm kaynaklar** veya **uygulama hizmetleri**' ni seçin ve ardından mobil uygulamanızın adını seçin.
1. **Ayarlar** bölmesi varsayılan olarak açılır. Değilse, **Ayarlar**' ı seçin.
1. **Genel** menüsünde **uygulama ayarları**' nı seçin.
1. **Uygulama ayarları** bölümüne gidin.
1. Uygulama ayarınız zaten varsa, değeri düzenlemek için uygulama ayarının değerini seçin.
   Uygulama ayarınız yoksa, **anahtar** kutusuna uygulama ayarını ve **değer** kutusundaki değeri girin.
1. **Kaydet**’i seçin.

Çoğu uygulama ayarını değiştirmek için hizmet yeniden başlatması gerekir.

### <a name="howto-use-sqlazure"></a>Üretim veri deposu olarak SQL veritabanını kullanma

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Azure SQL veritabanı 'nı veri deposu olarak kullanmak tüm Azure App Service uygulama türlerinde aynıdır. Daha önce yapmadıysanız, bir Mobile Apps arka ucu oluşturmak için aşağıdaki adımları izleyin:

1. [Azure portalda]’ında oturum açın.
1. Pencerenin sol üst kısmında **+ Yeni** ' yi > **Web ve mobil** > **mobil uygulama**' yı seçin ve ardından Mobile Apps arka ucu için bir ad sağlayın.
1. **Kaynak grubu** kutusuna uygulamanız ile aynı adı girin.
1. Varsayılan App Service planı seçilidir. App Service planınızı değiştirmek istiyorsanız:

   a. **App Service planı** >  **+ Yeni oluştur**' u seçin.

   b. Yeni App Service planının adını girip uygun bir konum seçin.

   c. Hizmet için uygun bir fiyatlandırma katmanı seçin. **Ücretsiz** ve **paylaşılan**gibi daha fazla fiyatlandırma seçeneği görüntülemek için **Tümünü görüntüle** ' yi seçin.

   d. **Seç** düğmesine tıklayın.

   e. **App Service planı** bölmesine geri dönüp **Tamam**' ı seçin.
1. **Oluştur**'u seçin.

Mobile Apps arka ucu sağlama işlemi birkaç dakika sürebilir. Mobile Apps arka ucu sağlandıktan sonra Portal, Mobile Apps arka ucunun **Ayarlar** bölmesini açar.

Mevcut bir SQL veritabanını Mobile Apps arka uca bağlamayı ya da yeni bir SQL veritabanı oluşturmayı seçebilirsiniz. Bu bölümde, bir SQL veritabanı oluşturacağız.

> [!NOTE]
> Mobile Apps arka ucu ile aynı konumda bir veritabanınız zaten varsa, bunun yerine **var olan bir veritabanını kullan** ' ı seçin ve ardından bu veritabanını seçin. Daha yüksek gecikme süreleri nedeniyle bir veritabanının farklı bir konumda kullanılması önerilmez.

1. Yeni Mobile Apps arka uçta **ayarlar** > **mobil uygulama** > **veri** >  **+ Ekle**' yi seçin.
1. **Veri bağlantısı ekle** bölmesinde SQL veritabanı ' nı seçin **-gerekli ayarları Yapılandır** > **Yeni bir veritabanı oluşturun**. **Ad** kutusuna yeni veritabanının adını girin.
1. **Sunucu**' yı seçin. **Yeni sunucu** bölmesinde, **sunucu adı** kutusuna benzersiz bir sunucu adı girin ve uygun bir Sunucu Yöneticisi oturum açma adı ve parola sağlayın. **Azure hizmetlerinin sunucuya erişmesine Izin ver** ' in seçildiğinden emin olun. **Tamam**’ı seçin.

   ![Bir Azure SQL veritabanı oluşturma][6]
1. **Yeni veritabanı** bölmesinde **Tamam**' ı seçin.
1. **Veri bağlantısı ekle** bölmesine geri dönün, **bağlantı dizesi**' ni seçin ve veritabanını oluştururken belirttiğiniz oturum açma ve parolayı girin. Var olan bir veritabanını kullanıyorsanız, bu veritabanı için oturum açma kimlik bilgilerini sağlayın. **Tamam**’ı seçin.
1. **Veri bağlantısı ekle** bölmesine yeniden geri döndüğünüzde veritabanını oluşturmak için **Tamam** ' ı seçin.

<!--- END OF ALTERNATE INCLUDE -->

Veritabanının oluşturulması birkaç dakika sürebilir. Dağıtımın ilerlemesini izlemek için **Bildirimler** alanını kullanın. Veritabanı başarıyla dağıtılana kadar ilerlemeyin. Veritabanı dağıtıldıktan sonra, Mobile Apps arka uç uygulama ayarlarınızda SQL veritabanı örneği için bir bağlantı dizesi oluşturulur. Bu uygulama ayarını, **ayarlar** > **uygulama ayarları** > **bağlantı dizelerinde**görebilirsiniz.

### <a name="howto-tables-auth"></a>Tablolara erişim için kimlik doğrulaması gerektir

@No__t-0 uç noktasıyla App Service kimlik doğrulaması kullanmak istiyorsanız, önce [Azure portalda] App Service kimlik doğrulamasını yapılandırmanız gerekir. Daha fazla bilgi için, kullanmayı düşündüğünüz kimlik sağlayıcısı için yapılandırma kılavuzuna bakın:

* [Azure Active Directory kimlik doğrulamasını yapılandırma]
* [Facebook kimlik doğrulamasını yapılandırma]
* [Google kimlik doğrulamasını yapılandırma]
* [Microsoft kimlik doğrulamasını yapılandırma]
* [Twitter kimlik doğrulamasını yapılandırma]

Her tablo, tabloya erişimi denetlemek için kullanabileceğiniz bir erişim özelliğine sahiptir. Aşağıdaki örnek, kimlik doğrulaması gerektiren statik olarak tanımlanmış bir tabloyu göstermektedir.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Access özelliği üç değerden birini alabilir:

* *anonim* , istemci uygulamanın kimlik doğrulaması olmadan veri okumasına izin verildiğini belirtir.
* *kimliği doğrulanmış* , istemci uygulamanın istekle geçerli bir kimlik doğrulama belirteci gönderebilmesi gerektiğini gösterir.
* *devre dışı* , bu tablonun Şu anda devre dışı olduğunu gösterir.

Erişim özelliği tanımsızdır, kimliği doğrulanmamış erişime izin verilir.

### <a name="howto-tables-getidentity"></a>Tablolarınızda kimlik doğrulama taleplerini kullanma
Kimlik doğrulaması ayarlandığında istenen çeşitli talepler ayarlayabilirsiniz. Bu talepler, `context.user` nesnesi aracılığıyla normalde kullanılabilir değildir. Ancak, `context.user.getIdentity()` yöntemini kullanarak bunları alabilirsiniz. @No__t-0 yöntemi bir nesneye çözümlenen bir Promise döndürür. Nesne, kimlik doğrulama yöntemine (`facebook`, `google`, `twitter`, `microsoftaccount` veya `aad`) göre anahtarlanır.

Örneğin, Microsoft hesabı kimlik doğrulaması ayarlarsanız ve e-posta adreslerini talep ederseniz, e-posta adresini aşağıdaki tablo denetleyicisiyle kayda ekleyebilirsiniz:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Hangi taleplerin kullanılabildiğini görmek için, sitenizin `/.auth/me` uç noktasını görüntülemek için bir Web tarayıcısı kullanın.

### <a name="howto-tables-disabled"></a>Belirli tablo işlemlerine erişimi devre dışı bırakma

Tablo üzerinde görünmesinin yanı sıra, Access özelliği tek tek işlemleri denetlemek için kullanılabilir. Dört işlem vardır:

* `read`, tablodaki Reston al işlemidir.
* `insert`, tablodaki yeniden gönderme işlemidir.
* `update`, tablodaki yeniden düzeltme eki işlemidir.
* `delete`, tablodaki Reston SILME işlemidir.

Örneğin, salt okunurdur bir kimliği doğrulanmamış tablo sağlamak isteyebilirsiniz:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Tablo işlemleriyle kullanılan sorguyu ayarlama

Tablo işlemleri için ortak bir gereksinim, verilerin kısıtlı bir görünümünü sağlamaktır. Örneğin, yalnızca kendi kayıtlarınızı okuyabilmeniz veya güncelleştirebilmeniz için kimliği doğrulanmış kullanıcı KIMLIĞIYLE etiketlenmiş bir tablo sağlayabilirsiniz. Aşağıdaki tablo tanımı bu işlevselliği sağlar:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

Normalde bir sorguyu çalıştıran işlemler, `where` yan tümcesini kullanarak ayarlayabileceğiniz bir sorgu özelliğine sahiptir. Sorgu özelliği, bir OData sorgusunu veri arka ucunun işleyebileceği bir şeye dönüştürmek için kullanılan bir [Queryjs] nesnesidir. Basit eşitlik durumları (önceki bir gibi) için bir eşlem kullanabilirsiniz. Ayrıca, belirli SQL yan tümceleri ekleyebilirsiniz:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Tabloda geçici silme yapılandırma

Geçici silme, kayıtları gerçekten silmez. Bunun yerine, silinen sütunu true olarak ayarlayarak onları veritabanı içinde silinmiş olarak işaretler. Mobil Istemci SDK `IncludeDeleted()` kullanmadığı takdirde Mobile Apps SDK, geçici olarak silinen kayıtları sonuçlardan otomatik olarak kaldırır. Geçici bir silme için tablo yapılandırmak üzere tablo tanımı dosyasında `softDelete` özelliğini ayarlayın:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Kayıtları silmek için bir mekanizma oluşturmanız gerekir: bir istemci uygulaması, WebJob, bir Azure işlevi veya özel bir API.

### <a name="howto-tables-seeding"></a>Veritabanınızı verilerle tohum

Yeni bir uygulama oluştururken verileri içeren bir tabloyu temel almak isteyebilirsiniz. Bunu, tablo tanımı JavaScript dosyasında şu şekilde yapabilirsiniz:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Verilerin dengeli dağıtımı, yalnızca tabloyu oluşturmak için Mobile Apps SDK 'sını kullandıysanız gerçekleşir. Tablo veritabanında zaten mevcutsa, tabloya hiç veri eklenmeyecek. Dinamik şema açıksa, şema, sağlanan verilerden algılanır.

Hizmet çalışmaya başladığında tabloyu oluşturmak için `tables.initialize()` yöntemini açıkça çağırmanız önerilir.

### <a name="Swagger"></a>Swagger desteğini etkinleştir
Mobile Apps, yerleşik [Swagger] desteğiyle birlikte gelir. Swagger desteğini etkinleştirmek için, önce Swagger-UI ' ı bir bağımlılık olarak yüklemeniz gerekir:

    npm install --save swagger-ui

Daha sonra Mobile Apps oluşturucuda Swagger desteğini etkinleştirebilirsiniz:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Büyük olasılıkla yalnızca geliştirme sürümlerinde Swagger desteğini etkinleştirmek isteyebilirsiniz. Bunu, `NODE_ENV` uygulama ayarını kullanarak yapabilirsiniz:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

@No__t-0 uç noktası http://*yoursite*. azurewebsites.net/Swagger konumunda bulunur. Swagger Kullanıcı arabirimine `/swagger/ui` uç noktası aracılığıyla erişebilirsiniz. Tüm uygulamanızda kimlik doğrulaması yapılmasını tercih ederseniz, Swagger bir hata üretir. En iyi sonuçlar için Azure App Service kimlik doğrulaması/yetkilendirme ayarlarında kimliği doğrulanmamış isteklere izin vermeyi seçin ve ardından `table.access` özelliğini kullanarak kimlik doğrulamasını denetleyin.

Yalnızca yerel olarak geliştirmeye yönelik Swagger desteğini isterseniz, azureMobile. js dosyanıza Swagger seçeneğini de ekleyebilirsiniz.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Anında Iletme bildirimleri

Mobile Apps, tüm büyük platformlarda milyonlarca cihaza hedeflenmiş anında iletme bildirimleri gönderebilmeniz için Azure Notification Hubs ile tümleşir. Notification Hubs kullanarak iOS, Android ve Windows cihazlarına anında iletme bildirimleri gönderebilirsiniz. Notification Hubs ile yapabilecekleriniz hakkında daha fazla bilgi için bkz. [Notification Hubs genel bakış](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Anında iletme bildirimleri gönderme

Aşağıdaki kod, kayıtlı iOS cihazlarına yayın anında iletme bildirimi göndermek için `push` nesnesinin nasıl kullanılacağını gösterir:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

İstemciden bir şablon gönderme kaydı oluşturarak, bunun yerine tüm desteklenen platformlardaki cihazlara bir şablon anında iletme iletisi gönderebilirsiniz. Aşağıdaki kod, bir şablon bildiriminin nasıl gönderileceğini göstermektedir:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>Etiketler kullanarak kimliği doğrulanmış bir kullanıcıya anında iletme bildirimleri gönderme
Kimliği doğrulanmış bir Kullanıcı anında iletme bildirimlerini kaydettiğinde, kayda otomatik olarak bir kullanıcı KIMLIĞI etiketi eklenir. Bu etiketi kullanarak, belirli bir kullanıcı tarafından kaydedilen tüm cihazlara anında iletme bildirimleri gönderebilirsiniz. Aşağıdaki kod, isteği yapan kullanıcının SID 'sini alır ve bu kullanıcı için her cihaz kaydına bir şablon anında iletme bildirimi gönderir:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Kimliği doğrulanmış bir istemciden anında iletme bildirimleri için kayıt yaparken, kayda kalkışmadan önce kimlik doğrulamasının tamamlanmasını sağlayın.

## <a name="CustomAPI"></a>Özel API 'Ler

### <a name="howto-customapi-basic"></a>Özel API tanımlama

@No__t-0 uç noktası aracılığıyla veri erişimi API 'sine ek olarak, Mobile Apps özel API kapsamı sağlayabilir. Özel API 'Ler tablo tanımlarına benzer bir şekilde tanımlanır ve kimlik doğrulaması da dahil olmak üzere aynı tesislere erişebilir.

Özel bir API ile App Service kimlik doğrulaması kullanmak istiyorsanız, önce [Azure portalda] App Service kimlik doğrulamasını yapılandırmanız gerekir. Daha fazla bilgi için, kullanmayı düşündüğünüz kimlik sağlayıcısı için yapılandırma kılavuzuna bakın:

* [Azure Active Directory kimlik doğrulamasını yapılandırma]
* [Facebook kimlik doğrulamasını yapılandırma]
* [Google kimlik doğrulamasını yapılandırma]
* [Microsoft kimlik doğrulamasını yapılandırma]
* [Twitter kimlik doğrulamasını yapılandırma]

Özel API 'Ler, tablolar API 'SI ile aynı şekilde tanımlanır:

1. @No__t-0 dizini oluşturun.
1. @No__t-0 dizininde bir API tanımı JavaScript dosyası oluşturun.
1. @No__t-0 dizinini içeri aktarmak için Import metodunu kullanın.

Daha önce kullandığımız temel uygulama örneğine göre prototip API tanımı aşağıda verilmiştir:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

@No__t-0 yöntemini kullanarak sunucu tarihini döndüren örnek bir API alalım. API/Date. js dosyası aşağıda verilmiştir:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Her bir parametre, standart Rehirsiz fiillerden biridir: GET, POST, PATCH veya DELETE. Yöntemi, gerekli çıktıyı gönderen standart bir [ExpressJS ara yazılımı] işlevidir.

### <a name="howto-customapi-auth"></a>Özel API erişimi için kimlik doğrulaması gerektir

Mobile Apps SDK, hem `tables` uç noktası hem de özel API 'Ler için aynı şekilde kimlik doğrulaması uygular. Önceki bölümde geliştirilen API 'ye kimlik doğrulaması eklemek için `access` özelliği ekleyin:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Ayrıca, belirli işlemlerde kimlik doğrulaması belirtebilirsiniz:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

@No__t-0 uç noktası için kullanılan aynı belirtecin, kimlik doğrulaması gerektiren özel API 'Ler için kullanılması gerekir.

### <a name="howto-customapi-auth"></a>Büyük dosya yüklemelerini işle

Mobile Apps SDK, gönderiminizdeki gövde içeriğini kabul etmek ve çözmek için [Body-Parser ara yazılımını](https://github.com/expressjs/body-parser) kullanır. Daha büyük dosya yüklemelerini kabul etmek için Body-Parser ' i önceden yapılandırabilirsiniz:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

Dosya, iletimden önce Base-64 kodlandı. Bu kodlama, gerçek karşıya yükleme boyutunu (ve için hesabınızın olması gereken boyutu) arttırır.

### <a name="howto-customapi-sql"></a>Özel SQL deyimlerini yürütme

Mobile Apps SDK, istek nesnesi aracılığıyla tüm bağlamın erişimine izin verir. Parametreli SQL deyimlerini tanımlı veri sağlayıcısına kolayca çalıştırabilirsiniz:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>Masının

### <a name="howto-diagnostic-logs"></a>Hata ayıklama, tanılama ve sorun giderme Mobile Apps

Azure App Service, Node. js uygulamaları için çeşitli hata ayıklama ve sorun giderme teknikleri sağlar.
Node. js Mobile Apps arka uçta sorun gidermeye başlamak için aşağıdaki makalelere bakın:

* [İzleme Azure App Service]
* [Azure App Service tanılama günlüğünü etkinleştirme]
* [Visual Studio 'da Azure App Service sorunlarını giderme]

Node. js uygulamalarının çok çeşitli tanılama günlüğü araçlarına erişimi vardır. Dahili olarak, Mobile Apps Node. js SDK 'Sı, tanılama günlüğü için [Winston] kullanır. Hata ayıklama modunu etkinleştirdiğinizde veya [Azure portalda]`MS_DebugMode` uygulama ayarını doğru olarak ayarlarsanız günlük kaydı otomatik olarak etkinleştirilir. Oluşturulan Günlükler [Azure portalda]tanılama günlüklerinde görüntülenir.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android istemci hızlı başlangıç]: app-service-mobile-android-get-started.md
[Apache Cordova Istemci hızlı başlangıcı]: app-service-mobile-cordova-get-started.md
[iOS Istemci hızlı başlangıç]: app-service-mobile-ios-get-started.md
[Xamarin. iOS Istemci hızlı başlangıç]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin. Android Istemci hızlı başlangıç]: app-service-mobile-xamarin-android-get-started.md
[Xamarin. Forms Istemci hızlı başlangıç]: app-service-mobile-xamarin-forms-get-started.md
[Windows Mağazası Istemci hızlı başlangıç]: app-service-mobile-windows-store-dotnet-get-started.md
[Çevrimdışı veri eşitleme]: app-service-mobile-offline-data-sync.md
[Azure Active Directory kimlik doğrulamasını yapılandırma]: ../app-service/configure-authentication-provider-aad.md
[Facebook kimlik doğrulamasını yapılandırma]: ../app-service/configure-authentication-provider-facebook.md
[Google kimlik doğrulamasını yapılandırma]: ../app-service/configure-authentication-provider-google.md
[Microsoft kimlik doğrulamasını yapılandırma]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter kimlik doğrulamasını yapılandırma]: ../app-service/configure-authentication-provider-twitter.md
[Azure App Service dağıtım kılavuzu]: ../app-service/deploy-local-git.md
[İzleme Azure App Service]: ../app-service/web-sites-monitor.md
[Azure App Service tanılama günlüğünü etkinleştirme]: ../app-service/troubleshoot-diagnostic-logs.md
[Visual Studio 'da Azure App Service sorunlarını giderme]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Düğüm sürümünü belirtin]: ../nodejs-specify-node-version-azure-apps.md
[Düğüm modüllerini kullanma]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure portalda]: https://portal.azure.com/
[OData]: https://www.odata.org
[Ünü]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[GitHub 'da basicapp örneği]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[GitHub üzerinde Todo örneği]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[GitHub 'da Samples dizini]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Visual Studio için Node. js Araçları 1,1]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node. js paketi]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS ara yazılımı]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
