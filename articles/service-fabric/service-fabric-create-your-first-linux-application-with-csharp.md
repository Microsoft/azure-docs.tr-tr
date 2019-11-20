---
title: Linux 'ta ilk Azure Service Fabric uygulamanızı oluşturma C#| Microsoft Docs
description: Ve .NET Core 2,0 kullanarak C# bir Service Fabric uygulaması oluşturmayı ve dağıtmayı öğrenin.
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: atsenthi
ms.openlocfilehash: 8c34f50e8eb9d9793c146c38aca47691b04049c0
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167295"
---
# <a name="create-your-first-azure-service-fabric-application"></a>İlk Azure Service Fabric uygulamanızı oluşturma
> [!div class="op_single_selector"]
> * [Java-Linux (Önizleme)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C#-Linux (Önizleme)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric hem .NET Core hem de Java 'da Linux üzerinde hizmet oluşturmaya yönelik SDK 'lar sağlar. Bu öğreticide, Linux için uygulama oluşturma ve .NET Core 2,0 kullanarak C# bir hizmet oluşturma konusuna baktık.

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce, [Linux geliştirme ortamınızı ayarladığınızdan](service-fabric-get-started-linux.md)emin olun. Mac OS X kullanıyorsanız, [Vagrant kullanarak bir sanal makinede Linux One-Box ortamı ayarlayabilirsiniz](service-fabric-get-started-mac.md).

[SERVICE fabrıc CLI](service-fabric-cli.md) 'yi de yüklemek isteyeceksiniz

### <a name="install-and-set-up-the-generators-for-c"></a>İçin oluşturucuları kurun ve ayarlayınC#
Service Fabric, Yeumman şablon oluşturucuları kullanarak terminalden Service Fabric uygulamaları oluşturmanıza yardımcı olan yapı iskelesi araçları sağlar. İçin C#Service Fabric yeumman şablon oluşturucularını ayarlamak için aşağıdaki adımları izleyin:

1. Makinenizde NodeJS ve NPM 'yi yükleyip

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. NPM 'den makinenize [Yeumman](https://yeoman.io/) şablon Oluşturucu 'yı yükler

   ```bash
   npm install -g yo
   ```
3. NPM 'den Service Fabric Yeumman C# uygulama oluşturucusunu yükler

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Uygulamayı oluşturma
Service Fabric bir uygulama, her biri uygulamanın işlevlerini sunarken belirli bir role sahip bir veya daha fazla hizmet içerebilir. Son adımda yüklediğiniz Service Fabric [Yeumman](https://yeoman.io/) Oluşturucu C#, ilk hizmetinizi oluşturmayı ve daha sonra daha fazlasını eklemenizi kolaylaştırır. Tek bir hizmet içeren bir uygulama oluşturmak için Yeumman kullanalım.

1. Bir terminalde, yapı iskelesi oluşturmaya başlamak için aşağıdaki komutu yazın: `yo azuresfcsharp`
2. Uygulamanızı adlandırın.
3. İlk hizmetinizin türünü seçin ve adlandırın. Bu öğreticinin amaçları doğrultusunda güvenilir bir aktör hizmeti seçtik.

   ![İçin Yeumman Oluşturucu Service FabricC#][sf-yeoman]

> [!NOTE]
> Seçenekler hakkında daha fazla bilgi için bkz. [Service Fabric programlama modeline genel bakış](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Uygulamayı oluşturma
Service Fabric Yeumman şablonları, uygulamayı terminalden oluşturmak için kullanabileceğiniz bir derleme betiği içerir (uygulama klasörüne gittikten sonra).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Uygulamayı dağıtma

Uygulama oluşturulduktan sonra yerel kümeye dağıtabilirsiniz.

1. Yerel Service Fabric kümesine bağlanın.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Uygulama paketini kümenin görüntü deposuna kopyalamak, uygulama türünü kaydetmek ve uygulamanın bir örneğini oluşturmak için şablonda belirtilen Install betiğini çalıştırın.

    ```bash
    ./install.sh
    ```

Oluşturulan uygulamayı dağıtmak, diğer tüm Service Fabric uygulamaları ile aynıdır. Ayrıntılı yönergeler için [bir Service Fabric uygulamasını SERVICE fabrıc CLI ile yönetme ile](service-fabric-application-lifecycle-sfctl.md) ilgili belgelere bakın.

Bu komutlara yönelik parametreler, uygulama paketinin içindeki oluşturulan bildirimlerde bulunabilir.

Uygulama dağıtıldıktan sonra bir tarayıcı açın ve [http://localhost:19080/Explorer](http://localhost:19080/Explorer)' [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) gidin. Ardından, **uygulamalar** düğümünü genişletin ve uygulama türü için bir giriş ve bu türün ilk örneği için başka bir giriş olduğunu unutmayın.

> [!IMPORTANT]
> Uygulamayı Azure 'da güvenli bir Linux kümesine dağıtmak için, Service Fabric çalışma zamanı ile uygulamanızı doğrulamak üzere bir sertifika yapılandırmanız gerekir. Bunun yapılması, Reliable Services hizmetlerinizin temel alınan Service Fabric çalışma zamanı API 'Leriyle iletişim kurmasını sağlar. Daha fazla bilgi edinmek için bkz. [Reliable Services uygulamasını Linux kümelerinde çalışacak şekilde yapılandırma](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Test istemcisini başlatma ve yük devretme gerçekleştirme
Aktör projeleri kendi kendilerine hiçbir şey yapmaz. İletileri göndermek için başka bir hizmet veya istemci gerekir. Aktör şablonu, aktör hizmetiyle etkileşim kurmak için kullanabileceğiniz basit bir test betiği içerir.

1. Aktör hizmetinin çıkışını görmek için izleme yardımcı programını kullanarak betiği çalıştırın.

   MAC OS X söz konusu olduğunda, aşağıdaki ek komutları çalıştırarak myactorsvcTestClient klasörünü kapsayıcının içindeki aynı konuma kopyalamanız gerekir.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Service Fabric Explorer, aktör hizmetinin birincil çoğaltmasını barındıran düğümü bulun. Aşağıdaki ekran görüntüsünde, düğüm 3 ' dür.

    ![Service Fabric Explorer birincil çoğaltmayı bulma][sfx-primary]
3. Önceki adımda bulduğunuz düğüme tıklayın ve sonra Eylemler menüsünden **devre dışı bırak (yeniden Başlat)** öğesini seçin. Bu eylem, yerel kümenizdeki bir düğümü yeniden başlatır ve başka bir düğümde çalışan ikincil bir çoğaltmaya yük devretmeyi zorluyor. Bu eylemi gerçekleştirirken, test istemcisinden çıktıya dikkat edin ve yük devretmeye rağmen sayacın artmaya devam ettiğini unutmayın.

## <a name="adding-more-services-to-an-existing-application"></a>Mevcut bir uygulamaya daha fazla hizmet ekleme

Zaten `yo` kullanılarak oluşturulmuş bir uygulamaya başka bir hizmet eklemek için aşağıdaki adımları uygulayın:
1. Dizini mevcut uygulamanın köküne değiştirin.  Örneğin, `MyApplication`, Yeumman tarafından oluşturulan uygulamadır `cd ~/YeomanSamples/MyApplication`.
2. `yo azuresfcsharp:AddService` öğesini çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

* [Service Fabric CLı kullanarak Service Fabric kümeleriyle etkileşim kurma](service-fabric-cli.md)
* [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin
* [Service Fabric CLı ile çalışmaya başlama](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
