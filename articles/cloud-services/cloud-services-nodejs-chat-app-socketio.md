---
title: Socket.io-Azure kullanarak uygulama Node.js
description: Bir yuvayı nasıl barındıracağınızı öğrenmek için bu öğreticiyi kullanın. Azure 'da GÇ tabanlı sohbet uygulaması. Socket.IO, node.js sunucusu ve istemcileri için gerçek zamanlı iletişim sağlar.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: abc02769d7d978e14975d90ae0f98547bdc4faf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743330"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service-classic"></a>Azure bulut hizmetinde Socket.IO ile Node.js sohbet uygulaması oluşturma (klasik)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Socket.IO, node.js sunucunuz ve istemcileriniz arasında gerçek zamanlı iletişim sağlar. Bu öğretici, bir yuva barındırırken size kılavuzluk eder. Azure 'da GÇ tabanlı sohbet uygulaması. Socket.IO hakkında daha fazla bilgi için bkz. [Socket.io](https://socket.io).

Tamamlanan uygulamanın ekran görüntüsü aşağıda verilmiştir:

![Azure 'da barındırılan hizmeti görüntüleyen bir tarayıcı penceresi][completed-app]

## <a name="prerequisites"></a>Önkoşullar
Bu makaledeki örneği başarıyla tamamlayabilmeniz için aşağıdaki ürünlerin ve sürümlerin yüklü olduğundan emin olun:

* [Visual Studio 'yu](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) yükler
* [Node.js](https://nodejs.org/download/) yüklensin
* [Python sürüm 2.7.10](https://www.python.org/) 'i yükler

## <a name="create-a-cloud-service-project"></a>Bulut hizmeti projesi oluşturma
Aşağıdaki adımlarda, Socket.IO uygulamasını barındıracak bulut hizmeti projesi oluşturulur.

1. **Başlat menüsünde** veya **Başlat ekranında**, **Windows PowerShell**' i arayın. Son olarak, **Windows PowerShell** ' e sağ tıklayın ve **yönetici olarak çalıştır**' ı seçin.

    ![Azure PowerShell simgesi][powershell-menu]
2. **C: \\ node** adlı bir dizin oluşturun.

    ```powershell
    PS C:\> md node
    ```

3. Dizinleri **c: \\ node** diziniyle değiştirme

    ```powershell
    PS C:\> cd node
    ```

4. Adlı yeni bir çözüm `chatapp` ve adlı bir çalışan rolü oluşturmak için aşağıdaki komutları girin `WorkerRole1` :

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    Aşağıdaki yanıtı görürsünüz:

    ![New-azureservice ve Add-azurenodeworkerrolecmdlet 'lerinin çıktısı](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Sohbet örneğini indirin
Bu proje için, [Socket.IO GitHub deposundan]sohbet örneğini kullanacağız. Örneği indirmek ve daha önce oluşturduğunuz projeye eklemek için aşağıdaki adımları gerçekleştirin.

1. **Kopyala** düğmesini kullanarak deponun yerel bir kopyasını oluşturun. Ayrıca, projeyi indirmek için **ZIP** düğmesini de kullanabilirsiniz.

   ![https://github.com/LearnBoost/socket.io/tree/master/examples/chatZIP indirme simgesi vurgulanmış şekilde bir tarayıcı penceresi görüntüleme](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. **Örnekler \\ sohbet** dizinine ulaşana kadar yerel deponun dizin yapısına gidin. Bu dizinin içeriğini daha önce oluşturulan **C: \\ node \\ chatapp \\ WorkerRole1** dizinine kopyalayın.

   ![Gezgin, \\ arşivden ayıklanan örnek sohbet dizininin içeriğini görüntüleme][chat-contents]

   Yukarıdaki ekran görüntüsünde vurgulanan öğeler, **örnekler \\ sohbet** dizininden kopyalanan dosyalardır
3. **C: \\ node \\ chatapp \\ WorkerRole1** dizininde **server.js** dosyasını silin ve **app.js** dosyasını **server.js** olarak yeniden adlandırın. Bu, daha önce **Add-AzureNodeWorkerRole** cmdlet 'i tarafından oluşturulan varsayılan **server.js** dosyasını kaldırır ve sohbet örneğinizden uygulama dosyası ile değiştirir.

### <a name="modify-serverjs-and-install-modules"></a>Server.js değiştirin ve modülleri yükler
Azure öykünücüsünde uygulamayı test etmeden önce bazı küçük değişiklikler yapacağız. server.js dosyasında aşağıdaki adımları gerçekleştirin:

1. **server.js** dosyasını Visual Studio 'da veya herhangi bir metin düzenleyicisinde açın.
2. server.js başlangıcında **Modül bağımlılıkları** bölümünü bulun ve **Sio = gerektir (') içeren satırı değiştirin. //.. LIB//Socket. IO ')** , aşağıda gösterildiği gibi **Sio = gerektir (' Socket. IO ')** ile:

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. Uygulamanın doğru bağlantı noktasını dinlediğinden emin olmak için, Not defteri 'nde veya en sevdiğiniz düzenleyicide server.js açın ve aşağıdaki satırı aşağıda gösterildiği gibi **Process. env. Port** ile **3000** değiştirerek aşağıdaki satırı değiştirin:

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

Değişiklikleri **server.js** kaydettikten sonra, gerekli modülleri yüklemek için aşağıdaki adımları kullanın ve ardından uygulamayı Azure öykünücüsünde test edin:

1. **Azure PowerShell** kullanarak dizinleri **C: \\ node \\ chatapp \\ WorkerRole1** diziniyle değiştirin ve bu uygulamanın gerektirdiği modülleri yüklemek için şu komutu kullanın:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   Bu işlem, package.jsdosyasında listelenen modülleri yükler. Komut tamamlandıktan sonra aşağıdakine benzer bir çıktı görmeniz gerekir:

   ![NPM install komutunun çıktısı][The-output-of-the-npm-install-command]
2. Bu örnek başlangıçta Socket.IO GitHub deposunun bir parçası olduğundan ve Socket.IO kitaplığına göreli yol tarafından doğrudan başvurulduğundan, Socket.IO dosyasında package.jsbaşvurulmadı. bu nedenle, aşağıdaki komutu vererek yüklemesi gerekir:

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>Test edin ve dağıtın
1. Aşağıdaki komutu vererek öykünücüyü başlatın:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > Öykünücü başlatma ile ilgili sorunlarla karşılaşırsanız, örn.: Start-AzureEmulator: beklenmeyen bir hata oluştu.  Ayrıntılar: beklenmeyen bir hatayla karşılaşıldı çünkü System. ServiceModel. Channels. ServiceChannel iletişim nesnesi, hatalı durumda olduğundan iletişim için kullanılamaz.
   >
   > AzureAuthoringTools v 2.7.1 ve AzureComputeEmulator v 2,7 ' i yeniden yükleyin-sürümün eşleştiğinden emin olun.

2. Bir tarayıcı açın ve adresine gidin **http://127.0.0.1** .
3. Tarayıcı penceresi açıldığında, bir takma ad girin ve ENTER tuşuna basın.
   Bu, iletileri belirli bir takma ad olarak göndermenize imkan tanır. Çoklu Kullanıcı işlevselliğini test etmek için aynı URL 'YI kullanarak ek tarayıcı pencerelerini açın ve farklı takma adlar girin.

   ![Kullanıcı1 ve kullanıcı2 'den sohbet iletilerini görüntüleyen iki tarayıcı penceresi](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Uygulamayı test ettikten sonra, aşağıdaki komutu vererek öykünücüyü durdurun:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Uygulamayı Azure 'a dağıtmak için **Publish-AzureServiceProject** cmdlet 'ini kullanın. Örnek:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > Benzersiz bir ad kullandığınızdan emin olun, aksi takdirde Yayımlama işlemi başarısız olur. Dağıtım tamamlandıktan sonra tarayıcı açılır ve dağıtılan hizmete gider.
   >
   > Belirtilen abonelik adının içeri aktarılan yayımlama profilinde mevcut olmadığını belirten bir hata alırsanız, Azure 'a dağıtılmadan önce aboneliğinizin yayımlama profilini indirip içeri aktarmanız gerekir. [Azure bulut hizmetine Node.js uygulama oluşturma ve dağıtma](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/) **konusundaki uygulamayı Azure 'a dağıtma** bölümüne bakın
   >
   >

   ![Azure 'da barındırılan hizmeti görüntüleyen bir tarayıcı penceresi][completed-app]

   > [!NOTE]
   > Belirtilen abonelik adının içeri aktarılan yayımlama profilinde mevcut olmadığını belirten bir hata alırsanız, Azure 'a dağıtılmadan önce aboneliğinizin yayımlama profilini indirip içeri aktarmanız gerekir. [Azure bulut hizmetine Node.js uygulama oluşturma ve dağıtma](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/) **konusundaki uygulamayı Azure 'a dağıtma** bölümüne bakın
   >
   >

Uygulamanız artık Azure 'da çalışıyor ve Socket.IO kullanarak farklı istemciler arasında sohbet iletilerini geçirebilir.

> [!NOTE]
> Kolaylık olması için bu örnek, aynı örneğe bağlı kullanıcılar arasında sohbet ile sınırlandırılmıştır. Yani, bulut hizmeti iki çalışan rolü örneği oluşturursa, kullanıcılar yalnızca aynı çalışan rolü örneğine bağlı diğer kullanıcılarla sohbet edebilir. Uygulamayı birden çok rol örneğiyle çalışacak şekilde ölçeklendirmek için Service Bus gibi bir teknolojiyi kullanarak Socket.IO depolama durumunu örnekler arasında paylaşabilirsiniz. Örnekler için [Azure SDK 'da Node.js GitHub deposu için](https://github.com/WindowsAzure/azure-sdk-for-node)Service Bus kuyruklar ve konular kullanım örnekleri bölümüne bakın.
>
>

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir Azure bulut hizmetinde barındırılan temel bir sohbet uygulaması oluşturmayı öğrendiniz. Bu uygulamayı bir Azure Web sitesinde nasıl barındıralabileceğinizi öğrenmek için bkz. [Azure Web sitesinde Socket.IO ile Node.js sohbet uygulaması oluşturma][chatwebsite].

Daha fazla bilgi için bkz. [Node.js Geliştirici Merkezi](/azure/developer/javascript/).

[chatwebsite]: ./cloud-services-nodejs-develop-deploy-app.md

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub deposu]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png