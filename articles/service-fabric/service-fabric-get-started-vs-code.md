---
title: VS Code kullanmaya başlama ile Azure Service Fabric | Microsoft Docs
description: Bu makalede Visual Studio Code kullanarak Service Fabric uygulamalar oluşturmaya genel bakış sunulmaktadır.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 9662ebd26a263fa006c8fccf877fdc950e9014c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102952"
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
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeumman Generıcılar--uygulamanız için uygun oluşturucuları yükler

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Java geliştirmesi için aşağıdaki önkoşulların yüklü olması gerekir:

* [Java SDK 'sı](https://aka.ms/azure-jdks) (sürüm 1,8)
* [Gradle](https://gradle.org/install/)
* [Java vs Code uzantısı Için hata ayıklayıcı](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Java hizmetlerinde hata ayıklamak için gereklidir. Java hizmetleri 'nde hata ayıklama yalnızca Linux 'ta desteklenir. VS Code ve uzantıyı arayarak ya da VS Code marketinden, **etkinlik çubuğundaki** uzantılar simgesine tıklayarak yükleyebilirsiniz.

.NET Core/C# geliştirme için aşağıdaki önkoşulların yüklü olması gerekir:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (sürüm 2.0.0 veya üzeri)
* [Visual Studio Code için (omnisharp tarafından desteklenen) vs Code uzantısı C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) Hata ayıklama C# için gerekli. VS Code ve uzantıyı arayarak ya da VS Code marketinden, **etkinlik çubuğundaki** uzantılar simgesine tıklayarak yükleyebilirsiniz.

## <a name="setup"></a>Kurulum

1. VS Code açın.
2. VS Code sol tarafındaki **etkinlik çubuğunda** uzantılar simgesine tıklayın. "Service Fabric" araması yapın. Service Fabric Reliable Services uzantısı için **yüklensin** ' e tıklayın.

## <a name="commands"></a>Komutlar
VS Code için Service Fabric Reliable Services uzantısı, geliştiricilerin Service Fabric projeleri oluşturup dağıtmalarına yardımcı olmak için birçok komut sağlar. **Komut paletinden** `(Ctrl + Shift + p)`komutları çağırarak, giriş çubuğuna komut adını yazarak ve istem listesinden istenen komutu seçebilirsiniz. 

* Service Fabric: Uygulama Oluştur 
* Service Fabric: Uygulama yayımlama 
* Service Fabric: Uygulama dağıtma 
* Service Fabric: Uygulamayı Kaldır  
* Service Fabric: Uygulama oluştur 
* Service Fabric: Uygulamayı temizle 

### <a name="service-fabric-create-application"></a>Service Fabric: Uygulama Oluştur

**Service Fabric: Uygulama** Oluştur komutu, geçerli çalışma alanınızda yeni bir Service Fabric uygulaması oluşturur. Geliştirme makinenizde hangi yeumman, yüklü olduğuna bağlı olarak, Java, C#, Container ve konuk projeler dahil olmak üzere çeşitli Service Fabric uygulamalar oluşturabilirsiniz. 

1.  **Service Fabric seçin: Hizmet** Ekle komutu
2.  Yeni Service Fabric uygulamanızın türünü seçin. 
3.  Oluşturmak istediğiniz uygulamanın adını girin
3.  Service Fabric uygulamanıza eklemek istediğiniz hizmetin türünü seçin. 
4.  Hizmeti adlandırmak için istemleri izleyin. 
5.  Yeni Service Fabric uygulama çalışma alanında görüntülenir.
6.  Yeni uygulama klasörünü, çalışma alanındaki kök klasör olacak şekilde açın. Komutları buradan yürütmeye devam edebilirsiniz.

### <a name="service-fabric-add-service"></a>Service Fabric: Hizmet Ekle
**Service Fabric: Hizmet** Ekle komutu, mevcut bir Service Fabric uygulamasına yeni bir hizmet ekler. Hizmetin ekleneceği uygulamanın, çalışma alanının kök dizini olması gerekir. 

1.  **Service Fabric seçin: Hizmet** komutu Ekle.
2.  Geçerli Service Fabric uygulamanızın türünü seçin. 
3.  Service Fabric uygulamanıza eklemek istediğiniz hizmetin türünü seçin. 
4.  Hizmeti adlandırmak için istemleri izleyin. 
5.  Yeni hizmet, proje dizininizde görüntülenir. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Uygulama yayımlama
**Service Fabric: Uygulama** Yayımla komutu, Service Fabric uygulamanızı uzak bir kümede dağıtır. Hedef küme güvenli ya da güvensiz bir küme olabilir. Cloud. JSON içinde parametreler ayarlanmamışsa, uygulama yerel kümeye dağıtılır.

1.  Uygulama ilk kez oluşturulduğunda, proje dizininde bir Cloud. JSON dosyası oluşturulur.
2.  Cloud. json dosyasında bağlanmak istediğiniz kümenin değerlerini girin.
3.  **Service Fabric seçin: Uygulama** Yayımla komutu.
4.  Uygulamanın yüklendiğini onaylamak için hedef kümeyi Service Fabric Explorer görüntüleyin. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Uygulama dağıtma (localhost)
**Service Fabric: Application** komutunu dağıt, Service Fabric uygulamanızı yerel kümenize dağıtır. Komutunu kullanmadan önce yerel kümenizin çalıştığından emin olun. 

1. **Service Fabric seçin: Uygulama** komutunu dağıt
2. Uygulamanın yüklendiğini onaylamak için Service Fabric Explorer yerel kümeyi (http\/:/localhost: 19080/Explorer) ile görüntüleyin. Bu işlem biraz zaman alabilir, bu nedenle sabırlı olun.
3. Service Fabric de kullanabilirsiniz **: Cloud.** json dosyasında yerel bir kümeye dağıtılacak hiçbir parametre ayarlanmamış şekilde uygulama komutunu yayımlayın.

> [!NOTE]
> Java uygulamalarını yerel kümeye dağıtmak Windows makinelerinde desteklenmez.

### <a name="service-fabric-remove-application"></a>Service Fabric: Uygulamayı Kaldır
**Service Fabric: Uygulamayı** Kaldır komutu, daha önce vs Code uzantısı kullanılarak dağıtılan Service Fabric bir uygulamayı kümeden kaldırır. 

1.  **Service Fabric seçin: Uygulama** komutunu kaldır.
2.  Uygulamanın kaldırıldığını onaylamak için Service Fabric Explorer kümeyi görüntüleyin. Bu işlem biraz zaman alabilir, bu nedenle sabırlı olun.

### <a name="service-fabric-build-application"></a>Service Fabric: Uygulama oluştur
**Service Fabric: Yapı uygulaması** komutu Java veya C# Service Fabric uygulamaları oluşturabilir. 

1.  Bu komutu yürütmeden önce uygulama kök klasöründe olduğunuzdan emin olun. Komut, uygulamanın (C# veya Java) türünü tanımlar ve uygulamanızı uygun şekilde oluşturur.
2.  **Service Fabric seçin: Uygulama** komutu oluştur.
3.  Yapı işleminin çıktısı tümleşik terminale yazılır.

### <a name="service-fabric-clean-application"></a>Service Fabric: Uygulamayı temizle
**Service Fabric: Uygulamayı** Temizle komutu, derleme tarafından oluşturulan tüm jar dosyalarını ve yerel kitaplıkları siler. Yalnızca Java uygulamaları için geçerlidir. 

1.  Bu komutu yürütmeden önce uygulama kök klasöründe olduğunuzdan emin olun. 
2.  **Service Fabric seçin: Uygulamayı** Temizle komutu.
3.  Temizleme işleminin çıktısı tümleşik terminale yazılır.

## <a name="next-steps"></a>Sonraki adımlar

* [Vs Code ile Service Fabric uygulamaları geliştirmeyi ve C# hata ayıklamayı](./service-fabric-develop-csharp-applications-with-vs-code.md)öğrenin.
* [Vs Code Ile Java Service Fabric uygulamaları geliştirmeyi ve hata ayıklamayı](./service-fabric-develop-java-applications-with-vs-code.md)öğrenin.
