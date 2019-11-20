---
title: Azure Cloud Services bir Node. js Express uygulaması oluşturma ve dağıtma
description: Node. js ' de Azure Cloud Services Express. js uygulaması oluşturma ve dağıtma
services: cloud-services
documentationcenter: nodejs
author: georgewallace
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: gwallace
ms.openlocfilehash: 080ec61df2042a4cf2eac9d5175c4681f98fd9df
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306784"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Azure Cloud Services Express kullanarak Node. js web uygulaması oluşturma ve dağıtma

Node. js, çekirdek çalışma zamanındaki en az bir işlev kümesi içerir.
Geliştiriciler, genellikle bir Node. js uygulaması geliştirirken ek işlevsellik sağlamak için 3. taraf modüllerini kullanır. Bu öğreticide, Node. js web uygulamaları oluşturmak için bir MVC çerçevesi sağlayan [Express](https://github.com/expressjs/express) modülünü kullanarak yeni bir uygulama oluşturacaksınız.

Tamamlanan uygulamanın ekran görüntüsü aşağıda verilmiştir:

![Azure 'da Express 'e hoş geldiniz görüntüleyen bir Web tarayıcısı](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Bulut hizmeti projesi oluşturma
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

' İfade Sapp ' adlı yeni bir bulut hizmeti projesi oluşturmak için aşağıdaki adımları gerçekleştirin:

1. **Başlat menüsünde** veya **Başlat ekranında**, **Windows PowerShell**' i arayın. Son olarak, **Windows PowerShell** ' e sağ tıklayın ve **yönetici olarak çalıştır**' ı seçin.
   
    ![Azure PowerShell simgesi](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Dizinleri **c:\\node** diziniyle değiştirin ve ardından, **WebRole1**adlı bir Web rolü olan **ifade** eden yeni bir çözüm oluşturmak için aşağıdaki komutları girin:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Varsayılan olarak **Add-AzureNodeWebRole** , Node. js ' nin eski bir sürümünü kullanır. Yukarıdaki **set-AzureServiceProjectRole** Ifadesinde, Azure 'un bir Node v 0.10.21 kullanmasını söyler.  Parametreler büyük/küçük harfe duyarlıdır.  **WebRole1\package.JSON**içindeki **Engines** özelliğini denetleyerek Node. js ' nin doğru sürümünün seçildiğini doğrulayabilirsiniz.
    > 
    > 

## <a name="install-express"></a>Express 'ı yükler
1. Aşağıdaki komutu yayımlayarak hızlı oluşturucuyu yükler:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    NPM komutunun çıktısı aşağıdaki sonuca benzer görünmelidir. 
   
    ![NPM Install Express komutunun çıkışını görüntüleyen Windows PowerShell.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Dizinleri **WebRole1** diziniyle değiştirin ve yeni bir uygulama oluşturmak için Express komutunu kullanın:
   
        PS C:\node\expressapp\WebRole1> express
   
    Önceki uygulamanızın üzerine yazmanız istenir. Devam etmek için **y** veya **Evet** girin. Express, App. js dosyası ve uygulamanızı oluşturmak için bir klasör yapısı oluşturur.
   
    ![Hızlı komutun çıkışı](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Package. json dosyasında tanımlı ek bağımlılıklar yüklemek için aşağıdaki komutu girin:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![NPM install komutunun çıktısı](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. **Bin/www** dosyasını **Server. js**' ye kopyalamak için aşağıdaki komutu kullanın. Bu, bulut hizmetinin bu uygulama için giriş noktasını bulabilmesini sağlayacak.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Bu komut tamamlandıktan sonra, WebRole1 dizininde bir **Server. js** dosyası olmalıdır.
5. '. ' Karakterlerinden birini aşağıdaki satırdan kaldırmak için **Server. js** ' i değiştirin.
   
       var app = require('../app');
   
   Bu değişikliği yaptıktan sonra satır aşağıdaki gibi görünmelidir.
   
       var app = require('./app');
   
   Dosyayı (eski adıyla **bin/www**), gereken uygulama dosyası ile aynı dizine taşıdığımızdan bu değişiklik zorunludur. Bu değişikliği yaptıktan sonra, **Server. js** dosyasını kaydedin.
6. Azure öykünücüsünde uygulamayı çalıştırmak için aşağıdaki komutu kullanın:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Express 'e hoş geldiniz içeren bir Web sayfası.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Görünümü değiştirme
Şimdi görünümü, "Azure 'da Express 'e hoş geldiniz" iletisini görüntüleyecek şekilde değiştirin.

1. İndex. Jade dosyasını açmak için aşağıdaki komutu girin:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![İndex. Jade dosyasının içeriği.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade, Express uygulamaları tarafından kullanılan varsayılan görünüm altyapısıdır. Jade Görünüm altyapısı hakkında daha fazla bilgi için bkz [http://jade-lang.com][http://jade-lang.com].
2. Metnin son satırını **Azure**'a ekleyerek değiştirin.
   
   ![İndex. Jade dosyası, son satır okur: p Azure 'da {title \#} uygulamasına hoş geldiniz](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Dosyayı kaydedin ve Not defteri 'nden çıkın.
4. Tarayıcınızı yenileyin, değişikliklerinizi görürsünüz.
   
   ![Bir tarayıcı penceresi olan sayfa, Azure 'da Express 'e hoş geldiniz 'i içerir](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Uygulamayı test ettikten sonra, öykünücüyü durdurmak için **stop-AzureEmulator** cmdlet 'ini kullanın.

## <a name="publishing-the-application-to-azure"></a>Uygulamayı Azure 'da yayımlama
Azure PowerShell penceresinde, uygulamayı bir bulut hizmetine dağıtmak için **Publish-AzureServiceProject** cmdlet 'ini kullanın

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Dağıtım işlemi tamamlandıktan sonra tarayıcınız Web sayfasını açar ve görüntüler.

![Express sayfasını görüntüleyen bir Web tarayıcısı. URL, şu anda Azure üzerinde barındırılıyor demektir.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [Node.js Geliştirici Merkezi](https://docs.microsoft.com/azure/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


