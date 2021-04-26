---
title: VS Code Başlarken Azure Service Fabric
description: Bu makalede Visual Studio Code kullanarak Service Fabric uygulamalar oluşturmaya genel bakış sunulmaktadır.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: 7d54b4b048632324a58708f893a4778a56137916
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876088"
---
# <a name="service-fabric-for-visual-studio-code"></a>Visual Studio Code için Service Fabric

[Vs Code için Service Fabric Reliable Services uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) , Windows, Linux ve MacOS işletim sistemlerinde Service Fabric uygulamaları oluşturmak, derlemek ve hatalarını ayıklamak için gereken araçları sağlar.

Bu makalede, uzantının gereksinimlerine ve kurulumuna ilişkin bir genel bakış ve uzantı tarafından sağlanan çeşitli komutların kullanımı sunulmaktadır. 

> [!IMPORTANT]
> Service Fabric Java uygulamaları Windows makinelerde geliştirilebilir, ancak yalnızca Azure Linux kümelerine dağıtılabilir. Java uygulamalarında hata ayıklama Windows üzerinde desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

Tüm ortamlarda aşağıdaki önkoşulların yüklü olması gerekir.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Yeumman Generıcılar--uygulamanız için uygun oluşturucuları yükler

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Java geliştirmesi için aşağıdaki önkoşulların yüklü olması gerekir:

* [Java SDK 'sı](/azure/developer/java/fundamentals/java-jdk-long-term-support) (sürüm 1,8)
* [Gradle](https://gradle.org/install/)
* [Java vs Code uzantısı Için hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Java hizmetlerinde hata ayıklamak için gereklidir. Java hizmetleri 'nde hata ayıklama yalnızca Linux 'ta desteklenir. VS Code ve uzantıyı arayarak ya da VS Code marketinden, **etkinlik çubuğundaki** uzantılar simgesine tıklayarak yükleyebilirsiniz.

.NET Core/C# geliştirmesi için aşağıdaki önkoşulların yüklü olması gerekir:

* [.NET Core](https://dotnet.microsoft.com/download) (sürüm 2.0.0 veya üzeri)
* [Visual Studio Code Için C# (Omnisharp tarafından desteklenir) vs Code uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) C# hizmetlerinde hata ayıklamak için gereklidir. VS Code ve uzantıyı arayarak ya da VS Code marketinden, **etkinlik çubuğundaki** uzantılar simgesine tıklayarak yükleyebilirsiniz.

## <a name="setup"></a>Kurulum

1. VS Code’u açın.
2. VS Code sol tarafındaki **etkinlik çubuğunda** uzantılar simgesine tıklayın. "Service Fabric" araması yapın. Service Fabric Reliable Services uzantısı için **yüklensin** ' e tıklayın.

## <a name="commands"></a>Komutlar
VS Code için Service Fabric Reliable Services uzantısı, geliştiricilerin Service Fabric projeleri oluşturup dağıtmalarına yardımcı olmak için birçok komut sağlar. **Komut paletinden** komutları çağırarak `(Ctrl + Shift + p)` , giriş çubuğuna komut adını yazarak ve istem listesinden istenen komutu seçebilirsiniz. 

* Service Fabric: uygulama oluştur 
* Service Fabric: uygulama yayımlama 
* Service Fabric: uygulama dağıtma 
* Service Fabric: uygulamayı kaldır  
* Service Fabric: uygulama oluşturma 
* Service Fabric: uygulamayı temizle 

### <a name="service-fabric-create-application"></a>Service Fabric: uygulama oluştur

**Service Fabric: Create application** komutu geçerli çalışma alanınızda yeni bir Service Fabric uygulaması oluşturur. Geliştirme makinenizde hangi yeumman, yüklü olduğuna bağlı olarak, Java, C#, Container ve konuk projeler dahil olmak üzere çeşitli Service Fabric uygulamalar oluşturabilirsiniz. 

1.  **Service Fabric: uygulama oluştur** komutunu seçin
2.  Yeni Service Fabric uygulamanızın türünü seçin. 
3.  Oluşturmak istediğiniz uygulamanın adını girin
3.  Service Fabric uygulamanıza eklemek istediğiniz hizmetin türünü seçin. 
4.  Hizmeti adlandırmak için istemleri izleyin. 
5.  Yeni Service Fabric uygulama çalışma alanında görüntülenir.
6.  Yeni uygulama klasörünü, çalışma alanındaki kök klasör olacak şekilde açın. Komutları buradan yürütmeye devam edebilirsiniz.

### <a name="service-fabric-add-service"></a>Service Fabric: hizmet ekle
**Service Fabric: Add Service** komutu, var olan bir Service Fabric uygulamasına yeni bir hizmet ekler. Hizmetin ekleneceği uygulamanın, çalışma alanının kök dizini olması gerekir. 

1.  **Service Fabric: hizmet ekle** komutunu seçin.
2.  Geçerli Service Fabric uygulamanızın türünü seçin. 
3.  Service Fabric uygulamanıza eklemek istediğiniz hizmetin türünü seçin. 
4.  Hizmeti adlandırmak için istemleri izleyin. 
5.  Yeni hizmet, proje dizininizde görüntülenir. 

### <a name="service-fabric-publish-application"></a>Service Fabric: uygulama yayımlama
**Service Fabric: Publish Application** komutu, Service Fabric uygulamanızı uzak bir kümede dağıtır. Hedef küme güvenli ya da güvensiz bir küme olabilir. Parametreler on üzerinde Cloud.jsayarlanmamışsa, uygulama yerel kümeye dağıtılır.

1.  Uygulama ilk kez oluşturulduğunda, proje dizininde bir Cloud.jsdosyası oluşturulur.
2.  Cloud.jsdosyaya bağlanmak istediğiniz küme için değerleri girin.
3.  **Service Fabric: uygulamayı Yayımla** komutunu seçin.
4.  Uygulamanın yüklendiğini onaylamak için hedef kümeyi Service Fabric Explorer görüntüleyin. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: uygulama dağıtma (localhost)
**Service Fabric: Deploy Application** komutu, Service Fabric uygulamanızı yerel kümenize dağıtır. Komutunu kullanmadan önce yerel kümenizin çalıştığından emin olun. 

1. **Service Fabric: uygulamayı dağıt** komutunu seçin
2. Uygulamanın yüklendiğini onaylamak için Service Fabric Explorer yerel kümeyi (http: \/ /localhost: 19080/Explorer) ile görüntüleyin. Bu işlem biraz zaman alabilir, bu nedenle sabırlı olun.
3. Ayrıca, yerel bir kümeye dağıtmak için Cloud.jsüzerinde hiçbir parametre ayarlanmamış **Service Fabric: Publish Application** komutunu da kullanabilirsiniz.

> [!NOTE]
> Java uygulamalarını yerel kümeye dağıtmak Windows makinelerinde desteklenmez.

### <a name="service-fabric-remove-application"></a>Service Fabric: uygulamayı kaldır
**Service Fabric: uygulamayı kaldır** komutu, daha önce vs Code uzantısı kullanılarak dağıtıldığı kümeden bir Service Fabric uygulamasını kaldırır. 

1.  **Service Fabric: uygulamayı kaldır** komutunu seçin.
2.  Uygulamanın kaldırıldığını onaylamak için Service Fabric Explorer kümeyi görüntüleyin. Bu işlem biraz zaman alabilir, bu nedenle sabırlı olun.

### <a name="service-fabric-build-application"></a>Service Fabric: uygulama oluşturma
**Service Fabric: Build Application** komutu Java ya da C# Service Fabric uygulamaları oluşturabilir. 

1.  Bu komutu yürütmeden önce uygulama kök klasöründe olduğunuzdan emin olun. Komut uygulamanın türünü tanımlar (C# veya Java) ve uygulamanızı uygun şekilde oluşturur.
2.  **Service Fabric: Build Application** komutunu seçin.
3.  Yapı işleminin çıktısı tümleşik terminale yazılır.

### <a name="service-fabric-clean-application"></a>Service Fabric: uygulamayı temizle
**Service Fabric: uygulamayı temizle** komutu, derleme tarafından oluşturulan tüm jar dosyalarını ve yerel kitaplıkları siler. Yalnızca Java uygulamaları için geçerlidir. 

1.  Bu komutu yürütmeden önce uygulama kök klasöründe olduğunuzdan emin olun. 
2.  **Service Fabric: uygulamayı temizle** komutunu seçin.
3.  Temizleme işleminin çıktısı tümleşik terminale yazılır.

## <a name="next-steps"></a>Sonraki adımlar

* [Vs Code ile C# Service Fabric uygulamaları geliştirmeyi ve hata ayıklamayı](./service-fabric-develop-csharp-applications-with-vs-code.md)öğrenin.
* [Vs Code Ile Java Service Fabric uygulamaları geliştirmeyi ve hata ayıklamayı](./service-fabric-develop-java-applications-with-vs-code.md)öğrenin.