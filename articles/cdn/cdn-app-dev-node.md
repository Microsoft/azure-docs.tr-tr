---
title: Node.js için Azure CDN SDK'sı ile çalışmaya başlama | Microsoft Docs
description: Azure CDN'yi yönetmek için Node.js uygulamalarını yazmayı öğrenin.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 18dbcbf93947306334ccc2c156d9266884198e19
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594147"
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN ile geliştirmeye başlama
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Kullanabileceğiniz [Node.js için Azure CDN SDK](https://www.npmjs.com/package/azure-arm-cdn) oluşturma ve CDN profili ve uç noktaları yönetimini otomatikleştirmek için.  Bu öğreticide, birkaç kullanılabilir işlemleri gösteren basit bir Node.js konsol uygulaması oluşturulmasını adım adım göstermektedir.  Bu öğreticide, ayrıntılı olarak Node.js için Azure CDN SDK'sı tüm yönlerini açıklamak için tasarlanmamıştır.

Bu öğreticiyi tamamlamak için zaten olmalıdır [Node.js](https://www.nodejs.org) **4.x.x** veya üzeri yüklü ve yapılandırılmış.  Node.js uygulamanızı oluşturmak istediğiniz herhangi bir metin düzenleyicisi kullanabilirsiniz.  Bu öğreticide yazmak için kullandım [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Projeyi bu öğreticiden](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) MSDN'de indirilebilir.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Projenizi oluşturma ve NPM bağımlılıkları Ekle
Size sunduğumuz CDN profili için bir kaynak grubu oluşturduğunuz ve CDN profili ve uç noktaları grubu içindeki yönetmek için Azure AD uygulama izin verilen göre biz uygulamamızı oluşturmaya başlayabilir.

Uygulamanızı depolamak için bir klasör oluşturun.  Geçerli yolda Node.js araçları ile bir konsoldan bu yeni klasör için geçerli konumunuzu ayarlayın ve yürüterek projenizi başlatın:

    npm init

Ardından bir dizi soru projenizi başlatmak için sunulur.  İçin **giriş noktası**, bu öğreticide *app.js*.  Aşağıdaki örnekte diğer bir seçenek my görebilirsiniz.

![NPM init çıkış](./media/cdn-app-dev-node/cdn-npm-init.png)

Projemizin artık ile başlatılmış bir *packages.json* dosya.  Projemizin NPM paketlerinde bulunan bazı Azure kitaplıkları kullanmayı düşünüyor olabilir.  (Azure-arm-cd) Node.js için Azure istemci çalışma zamanı (ms-rest-azure) node.js ve Azure CDN istemci kitaplığı kullanacağız.  Bu proje bağımlılıkları olarak ekleyelim.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Paketleri tamamladıktan sonra yükleme, *package.json* dosyası (sürüm numaralarını farklı olabilir) bu örnektekine benzer görünmelidir:

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Son olarak, Metin Düzenleyicisi'ni kullanarak ve bir boş metin dosyası oluşturun, proje klasörünün kaydedin *app.js*.  Artık kod yazmaya başlamak hazırız.

## <a name="requires-constants-authentication-and-structure"></a>Gerektirir, sabitleri, kimlik doğrulama ve yapısı
İle *app.js* bizim düzenleyicisinde açın, yazılan programımız temel yapısını geçelim.

1. "Gerektirir" bizim NPM paketleri için en üstünde şu ekleyin:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Bizim yöntemleri kullanacaksınız bazı sabitleri tanımlamak ihtiyacımız var.  Aşağıdakileri ekleyin.  Dahil olmak üzere, yer tutucuları değiştirdiğinizden emin olun  **&lt;açılı ayraçlar&gt;** , gerektiğinde kendi değerlerinizle.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Ardından, CDN yönetim istemci örneği oluşturun ve kendi kimlik bilgilerini verin.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Tek tek kullanıcı kimlik doğrulaması kullanıyorsanız, şu iki satırı biraz farklı görünür.
   
   > [!IMPORTANT]
   > Yalnızca bireysel kullanıcı kimlik doğrulaması yerine bir hizmet sorumlusu seçme durumlarda bu kod örneği kullanın.  Bireysel kullanıcı kimlik bilgilerinizi korumak ve bunları gizli tutmanız konusunda dikkatli olun.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Öğeleri değiştirdiğinizden emin olun **&lt;açılı ayraçlar&gt;** doğru bilgileri.  İçin `<redirect URI>`, yeniden yönlendirme URI'si, girdiğiniz uygulamanın Azure AD'de kayıtlı olduğunda kullanın.
4. Bizim Node.js konsol uygulaması bazı komut satırı parametreleri almak zordur.  Şimdi en az bir doğrulama parametresi geçirildi.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Burada size hangi parametreler geçirilmiş tabanlı diğer işlevlere dal programımız ana bölümü için olduk.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Programımız çeşitli yerlerde en doğru sayıda parametre olarak geçirilen ve doğru görünmüyorsa Yardım görüntüleme emin olmak biz gerekir.  Bunu yapmak için işlevleri oluşturalım.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Son olarak, ihtiyaç duydukları geri işiniz bittiğinde çağrılacak bir yöntem için CDN yönetim istemcide kullanacağız işlevleri zaman uyumsuz.  CDN yönetim istemci (varsa) çıktısını görüntülemek ve düzgün bir şekilde programdan çıkmak olalım.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Temel yapısını programımız yazılır, biz bizim parametrelere göre çağrılan işlevler oluşturmanız gerekir.

## <a name="list-cdn-profiles-and-endpoints"></a>Liste CDN profili ve uç noktaları
Bizim varolan profillerini ve uç noktalarını listelemek için kod ile başlayalım.  My kod açıklamaları, beklenen söz dizimi sağlar, bu nedenle burada her parametre gider biliyoruz.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN profili ve uç noktaları oluşturma
Ardından, biz profillerini ve uç noktaları oluşturmak için işlevleri yazacaksınız.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Bir uç noktasını temizleme
Uç noktası oluşturuldu varsayıldığında, biz Programımıza gerçekleştirmek isteyebileceğiniz yaygın görevlerden biri içerik bizim uç noktasını temizleme.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN profili ve uç noktalarını silme
Son işlevi dahil edilir, uç noktaları ve profilleri siler.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Programı çalıştırma
Biz artık Node.js programımız bizim sık kullanılan hata ayıklayıcıyı kullanarak çalıştırabilirsiniz veya Konsolu.

> [!TIP]
> Visual Studio Code, hata ayıklayıcı kullanıyorsanız, komut satırı parametreleri geçirmek için ortamı oluşturmanız gerekir.  Visual Studio Code bunu yapar **launch.json** dosya.  Adlı bir özellik arayın **args** ve dize değerleri, parametre için bir dizi ekleyebilirsiniz, böylece şuna benzer: `"args": ["list", "profiles"]`.
> 
> 

Bizim profilleri listeleyerek başlayalım.

![Liste profilleri](./media/cdn-app-dev-node/cdn-list-profiles.png)

Boş bir dizi geri aldık.  Herhangi bir profil kaynak grubumuz yoksa, bekleniyor.  Artık bir profili oluşturalım.

![Profil Oluştur](./media/cdn-app-dev-node/cdn-create-profile.png)

Artık, bir uç nokta ekleyelim.

![Uç nokta oluşturma](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Son olarak, bizim profili şimdi silin.

![Profili Sil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Sonraki Adımlar
Bu kılavuz, tamamlanan projeden görmek için [örneği indirin](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Başvuru için Azure CDN SDK'sı için Node.js görmek için görüntüleyin [başvuru](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Ek belgeler Node.js için Azure SDK'sı üzerinde bulmak için görüntüleme [tam başvuru](https://azure.github.io/azure-sdk-for-node/).

CDN kaynaklarınızı yönetmek [PowerShell](cdn-manage-powershell.md).

