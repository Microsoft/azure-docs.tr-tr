---
title: Node.js için Azure CDN SDK ile çalışmaya başlama | Microsoft Docs
description: Azure CDN profillerinin ve uç noktaların oluşturulması ve yönetiminin nasıl otomatikleştirildiğini gösteren basit bir Node.js konsol uygulaması oluşturmayı öğrenin.
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
ms.topic: how-to
ms.date: 04/02/2021
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: 386a424e45d1b718b68cbbf53322fd704317a06b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285233"
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN ile geliştirmeye başlama
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

CDN profillerinin ve uç noktaların oluşturulmasını ve yönetimini otomatikleştirmek için [JavaScript için Azure CDN SDK 'sını](https://www.npmjs.com/package/@azure/arm-cdn) kullanabilirsiniz.  Bu öğreticide, kullanılabilir işlemlerin birkaçını gösteren basit bir Node.js konsol uygulamasının oluşturulması gösterilmektedir.  Bu öğretici, JavaScript için Azure CDN SDK 'sının tüm yönlerini ayrıntılı olarak tanımlamaya yönelik değildir.

Bu öğreticiyi tamamlayabilmeniz için, zaten [Node.js](https://www.nodejs.org) **6. x. x** veya daha yüksek bir sürümü yüklenmiş ve yapılandırılmış olmalıdır.  Node.js uygulamanızı oluşturmak istediğiniz herhangi bir metin düzenleyicisini kullanabilirsiniz.  Bu öğreticiyi yazmak için [Visual Studio Code](https://code.visualstudio.com)kullandım.  


[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Projenizi oluşturun ve NPM bağımlılıklarını ekleyin
CDN profillerimiz için bir kaynak grubu oluşturduğumuzdan ve bu grup içindeki CDN profillerini ve uç noktalarını yönetmek için Azure AD uygulama iznimize verildik. uygulamamızı oluşturmaya başlayabiliriz.

Uygulamanızı depolamak için bir klasör oluşturun.  Geçerli yolunuzda Node.js araçları olan bir konsolundan, geçerli konumunuzu bu yeni klasöre ayarlayın ve şunu yürüterek projenizi başlatın:

```console
npm init
```

Daha sonra projenizi başlatmak için bir dizi soru sunulacaktır.  **Giriş noktası** için bu öğretici *app.js* kullanır.  Aşağıdaki örnekte diğer seçimlerimi görebilirsiniz.

![NPM init çıkışı](./media/cdn-app-dev-node/cdn-npm-init.png)

Projemiz artık *packages.js* dosyası ile başlatılmış.  Projemiz NPM paketlerinde bulunan bazı Azure kitaplıklarını kullanacak.  Node.js ( @azure/ms-rest-nodeauth ) ve JavaScript () için Azure CDN Istemci kitaplığı 'nda Azure Active Directory kimlik doğrulaması için kitaplığı kullanacağız @azure/arm-cdn .  Projeye bağımlılıklar olarak ekleyelim.

```console
npm install --save @azure/ms-rest-nodeauth
npm install --save @azure/arm-cdn
```

Paketler yüklendikten sonra, dosyadaki *package.js* bu örneğe benzer görünmelidir (sürüm numaraları farklı olabilir):

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
    "@azure/arm-cdn": "^5.2.0",
    "@azure/ms-rest-nodeauth": "^3.0.0"
  }
}
```

Son olarak, metin düzenleyicinizi kullanarak boş bir metin dosyası oluşturun ve proje klasörünüzün köküne *app.js* olarak kaydedin.  Artık kod yazmaya başlamaya hazırsınız.

## <a name="requires-constants-authentication-and-structure"></a>, Sabitler, kimlik doğrulaması ve yapı gerektirir
Düzenleyicimizde *app.js* açık olduğunda programımızın temel yapısını yazalım.

1. En üstteki NPM paketlerimiz için "gerektirir" öğesini aşağıdaki şekilde ekleyin:
   
    ``` javascript
    var msRestAzure = require('@azure/ms-rest-nodeauth');
    const { CdnManagementClient } = require('@azure/arm-cdn');
    ```
2. Yöntemlerimizin kullanacağı bazı sabitleri tanımlamanız gerekiyor.  Aşağıdakileri ekleyin.  **&lt; Açılı ayraçlar &gt;** dahil olmak üzere yer tutucuları, gerektiğinde kendi değerlerinizle değiştirdiğinizden emin olun.
   
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
3. Ardından, CDN yönetim istemcisini örnekliyoruz ve kimlik bilgilerinizle bize sunacağız.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new CdnManagementClient(credentials, subscriptionId);
    ```

4. Node.js konsolu uygulamamız bazı komut satırı parametreleri alacak.  En az bir parametrenin geçtiğini doğrulayalim.
   
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
5. Bu, bize, hangi parametrelerin geçtiğini temel alarak diğer işlevlere daldığımız, programımızın ana kısmına getiriyor.
   
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
6. Programımız birkaç yerde, doğru şekilde parametre sayısının doğru şekilde geçirildiğinden emin olmamız gerekir.  Bunu yapmak için işlevler oluşturalım.
   
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
7. Son olarak, CDN yönetim istemcisinde kullanacağımız işlevler zaman uyumsuzdur ve bu nedenle, bittiğinde geri çağrı yapmak için bir yönteme ihtiyaç duyar.  CDN yönetim istemcisinden (varsa) çıktıyı görüntüleyebilen bir tane oluşturalım ve programdan sorunsuz bir şekilde çıkış yapalim.
   
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

Artık programımızın temel yapısı yazıldığı için, parametrelerimize göre adlı işlevleri oluşturacağız.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN profillerini ve uç noktalarını listeleme
Mevcut profillerimizi ve uç noktalarınızı listelemek için kod ile başlayalım.  Her parametrenin nerede olduğunu bilmemiz için kod açıklamalarım beklenen sözdizimini sağlar.

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
            cdnClient.endpoints.listByProfile(resourceGroupName, parms[2], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN profilleri ve uç noktaları oluşturma
Daha sonra, profil ve uç noktalar oluşturmak için işlevleri yazacağız.

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

    cdnClient.profiles.create( resourceGroupName, parms[2], standardCreateParameters, callback);
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

    cdnClient.endpoints.create(resourceGroupName, parms[2], parms[3], endpointProperties, callback);
}
```

## <a name="purge-an-endpoint"></a>Bir uç noktayı Temizleme
Uç noktanın oluşturulduğu varsayılarak, programımızda gerçekleştirmek isteyebileceğiniz ortak bir görev, uç noktanızda içerik temizliyoruz.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(resourceGroupName, parms[2], parms[3], purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN profillerini ve uç noktalarını silme
En son işlev, uç noktaları ve profilleri siler.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteMethod(resourceGroupName, parms[2], callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteMethod(resourceGroupName, parms[2], parms[3], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Programı çalıştırma
Artık sık kullanılan hata ayıklayıcımız veya konsolda Node.js programımızı yürütebiliriz.

> [!TIP]
> Hata ayıklayıcı olarak Visual Studio Code kullanıyorsanız, ortamınızı komut satırı parametrelerine geçirilecek şekilde ayarlamanız gerekir.  Visual Studio Code bunu dosyada **launch.js** .  **Bağımsız değişkenler** adlı bir özellik bulun ve parametreleriniz için bir dize değerleri dizisi ekleyin, böylece şuna benzer olur: `"args": ["list", "profiles"]` .
> 
> 

Profillerimizi listeleyerek başlayalım.

![Liste profilleri](./media/cdn-app-dev-node/cdn-list-profiles.png)

Boş bir diziyi geri aldık.  Kaynak grubunuzda hiç bir profil olmadığı için bu beklenmez.  Şimdi bir profil oluşturalım.

![Profil oluştur](./media/cdn-app-dev-node/cdn-create-profile.png)

Şimdi bir uç nokta ekleyelim.

![Uç nokta oluştur](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Son olarak, profilimi silemem.

![Profili sil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Sonraki Adımlar

JavaScript için Azure CDN SDK başvurusunu görmek için [başvuruyu](https://docs.microsoft.com/javascript/api/@azure/arm-cdn)görüntüleyin.

JavaScript için Azure SDK ile ilgili ek belgeler bulmak için [tam başvuruyu](https://docs.microsoft.com/javascript/api/?view=azure-node-latest)görüntüleyin.

CDN kaynaklarınızı [PowerShell](cdn-manage-powershell.md)ile yönetin.
