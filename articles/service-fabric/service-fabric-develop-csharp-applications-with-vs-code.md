---
title: Visual Studio Code ile .NET Core Uygulamaları geliştirin
description: Bu makalede, Visual Studio Code kullanılarak .NET Core Service Fabric uygulamalarının nasıl oluşturulacağı, dağıtılacağı ve hata ayıklamanın nasıl yapılacağı gösterilir.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 5fbd523a38b3c4860316e45b8b7c03a17de19499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92678339"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Visual Studio Code ile C# Service Fabric uygulamaları geliştirme

[Vs Code için Service Fabric Reliable Services uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) , Windows, Linux ve MacOS işletim sistemlerinde .net Core Service Fabric uygulamaları oluşturmayı kolaylaştırır.

Bu makalede Visual Studio Code kullanarak bir .NET Core Service Fabric uygulamasının nasıl oluşturulacağı, dağıtılacağı ve hata ayıklamanın nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, VS Code için Service Fabric Reliable Services uzantısı ve geliştirme ortamınız için gereken tüm bağımlılıklar VS Code, zaten yüklemiş olduğunuz varsayılır. Daha fazla bilgi için bkz [. Başlarken](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Örneği indirme
Bu makale, [Service Fabric .NET Core ile çalışmaya başlama örnekleri GitHub deposunda](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)CounterService uygulamasını kullanır. 

Depoyu geliştirme makinenize kopyalamak için, bir Terminal penceresinde aşağıdaki komutu çalıştırın (Windows 'da komut penceresi):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Uygulamayı VS Code açın

### <a name="windows"></a>Windows
Başlat menüsünde VS Code simgesine sağ tıklayın ve **yönetici olarak çalıştır**' ı seçin. Hata ayıklayıcıyı hizmetlerinize eklemek için VS Code yönetici olarak çalıştırmanız gerekir.

### <a name="linux"></a>Linux
Terminal kullanarak, uygulamanın yerel olarak kopyalandığı dizinden/service-fabric-dotnet-core-getting-started/Services/CounterService yoluna gidin.

Hata ayıklayıcının hizmetlerinizi iliştirebilmesi için VS Code kök kullanıcı olarak açmak üzere aşağıdaki komutu çalıştırın.
```
sudo code . --user-data-dir='.'
```

Uygulama artık VS Code çalışma alanınızda görünmelidir.

![Çalışma alanındaki sayaç hizmeti uygulaması](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Uygulama oluşturma
1. VS Code içinde **komut paletini** açmak Için (Ctrl + Shift + p) tuşuna basın.
2. **Service Fabric: Build Application** komutunu arayın ve seçin. Derleme çıkışı tümleşik terminale gönderilir.

   ![VS Code uygulama komutu oluştur](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Uygulamayı yerel kümeye dağıtma
Uygulamayı oluşturduktan sonra yerel kümeye dağıtabilirsiniz. 

1. **Komut paletinden** **Service Fabric: Deploy Application (localhost) komutunu** seçin. Install işleminin çıktısı tümleşik terminale gönderilir.

   ![Uygulama komutunu VS Code içinde dağıt](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Dağıtım tamamlandığında bir tarayıcı başlatın ve Service Fabric Explorer açın: http: \/ /localhost: 19080/Explorer. Uygulamanın çalıştığını görmeniz gerekir. Bu işlem biraz zaman alabilir, bu nedenle sabırlı olun. 

   ![Service Fabric Explorer 'de sayaç hizmeti uygulaması](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Uygulamanın çalıştığını doğruladıktan sonra bir tarayıcı başlatın ve şu sayfayı açın: http: \/ /localhost: 31002. Bu, uygulamanın Web ön ucu olur. Sayacın geçerli değerini artan şekilde görmek için sayfayı yenileyin.

   ![Tarayıcıda sayaç hizmeti uygulaması](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Uygulamayı Azure Service Fabric kümesinde yayımlama
Uygulamayı yerel kümeye dağıtmaya birlikte, uygulamayı uzak bir Azure Service Fabric kümesine de yayımlayabilirsiniz. 

1. Uygulamanızı yukarıdaki yönergeleri kullanarak oluşturduğunuzdan emin olun. Oluşturulan yapılandırma dosyasını, `Cloud.json` yayımlamak istediğiniz uzak kümenin ayrıntılarıyla güncelleştirin.

2. **Komut paletinden** **Service Fabric: uygulamayı Yayımla komutunu** seçin. Install işleminin çıktısı tümleşik terminale gönderilir.

   ![Uygulama komutunu VS Code içinde Yayımla](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Dağıtım tamamlandığında bir tarayıcı başlatın ve Service Fabric Explorer açın: `https:<clusterurl>:19080/Explorer` . Uygulamanın çalıştığını görmeniz gerekir. Bu işlem biraz zaman alabilir, bu nedenle sabırlı olun. 

## <a name="debug-the-application"></a>Uygulamada hata ayıklama
VS Code içindeki uygulamalarda hata ayıklarken, uygulamanın yerel bir kümede çalışıyor olması gerekir. Kesme noktaları daha sonra koda eklenebilir.

Bir kesme noktası ve hata ayıklama ayarlamak için aşağıdaki adımları izleyin:
1. Gezgin 'de, */src/CounterServiceApplication/CounterService/CounterService.cs* dosyasını açın ve yöntemi içindeki 62. satırda bir kesme noktası ayarlayın `RunAsync` .
3. VS Code hata ayıklayıcı görünümünü açmak için **etkinlik çubuğundaki** hata ayıkla simgesine tıklayın. Hata ayıklayıcı görünümünün en üstündeki dişli simgesine tıklayın ve açılan ortam menüsünden **.NET Core** ' u seçin. launch.jsdosya açılır. Bu dosyayı kapatabilirsiniz. Şimdi Çalıştır düğmesinin (yeşil ok) yanında bulunan hata ayıklama yapılandırma menüsünde yapılandırma seçimlerini görmeniz gerekir.

   ![VS Code çalışma alanında hata ayıklama simgesi](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Hata ayıklama yapılandırması menüsünden **.NET Core Ekle** ' yi seçin.

   ![Hata ayıklama yapılandırma menüsünde .NET Core Iliştirmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Service Fabric Explorer bir tarayıcıda açın: http: \/ /localhost: 19080/Explorer. Onay hizmetinin üzerinde çalıştığı birincil düğümü öğrenmek için **uygulamalar** ' a tıklayın ve ayrıntıya gidin. Aşağıdaki görüntüde, CounterService 'in birincil düğümünün düğüm 0 ' dır.

   ![CounterService için birincil düğüm](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. VS Code ' de, **.NET Core Ekle** hata ayıklama yapılandırması ' nın yanındaki Çalıştır simgesine (yeşil ok) tıklayın. İşlem seçimi iletişim kutusunda, adım 4 ' te belirlediğiniz birincil düğümde çalışan onay hizmeti işlemini seçin.

   ![Birincil Işlem](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. *CounterService. cs* dosyasındaki kesme noktası çok çabuk olacak. Daha sonra yerel değişkenlerin değerlerini keşfedebilirsiniz. Yürütmeye devam etmek, satırlarda adımla, yöntemlere adımla veya geçerli yöntemin dışına adımlamak için VS Code en üstündeki hata ayıklama araç çubuğunu kullanın. 

   ![Hata ayıklama değerleri](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Hata ayıklama oturumunu sonlandırmak için VS Code üstündeki hata ayıklama araç çubuğundaki tak simgesine tıklayın.
   
   ![Hata ayıklayıcıyla bağlantıyı kes](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Hata ayıklamayı bitirdiğinizde, CounterService uygulamasını yerel kümeinizden kaldırmak için **Service Fabric: uygulamayı kaldır** komutunu kullanabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

* [Vs Code Ile Java Service Fabric uygulamaları geliştirmeyi ve hata ayıklamayı](./service-fabric-develop-java-applications-with-vs-code.md)öğrenin.



