---
title: Linux üzerinde Azure Service Fabric reliable actors Java uygulaması oluşturma | Microsoft Docs
description: Beş dakika içinde Java Service Fabric reliable actors uygulaması oluşturmayı ve dağıtmayı öğrenin.
services: service-fabric
documentationcenter: java
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2018
ms.author: atsenthi
ms.openlocfilehash: 4b008c001e1c4749b6ab6f9f21eff479f007c05c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68599671"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Linux üzerinde ilk Java Service Fabric Reliable Actors uygulamanızı oluşturma
> [!div class="op_single_selector"]
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Bu hızlı başlangıç, bir Linux geliştirme ortamında ilk Azure Service Fabric Java uygulamanızı yalnızca birkaç dakikada oluşturmanıza yardımcı olur.  İşlemi tamamladığınızda, yerel geliştirme kümesinde çalışan basit bir Java tek hizmet uygulamanız olacak.  

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce, Service Fabric SDK, Service Fabric CLI ve Yeoman’ı yükleyin, Java geliştirme ortamını kurun ve [Linux geliştirme ortamınızda](service-fabric-get-started-linux.md) bir geliştirme kümesi kurun. Mac OS X kullanıyorsanız, [Docker kullanarak Mac üzerinde bir geliştirme ortamı ayarlayabilirsiniz](service-fabric-get-started-mac.md).

[Service Fabric CLI](service-fabric-cli.md)'sını da yükleyin.

### <a name="install-and-set-up-the-generators-for-java"></a>Java için oluşturucuları yükleme ve ayarlama
Service Fabric, Yeoman şablon oluşturucu kullanarak terminalden Service Fabric Java uygulaması oluşturmanıza yardımcı olacak yapı iskelesi araçları sağlar.  Yeoman zaten yüklü değilse, Yeoman’ı ayarlama hakkında yönergeler için bkz. [Linux ile Service Fabric’i kullanmaya başlama](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables). Java için Service Fabric Yeoman şablon oluşturucusunu yüklemek için şu komutu çalıştırın.

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Temel kavramlar
Reliable Actors hizmetini kullanmaya başlamak için anlamanız gereken birkaç temel kavram vardır:

* **Aktör hizmeti**. Reliable Actors, Service Fabric altyapısında dağıtılabilen Reliable Services ile paketlenmiştir. Aktör örnekleri adlandırılmış hizmet örneğinde etkinleştirilir.
* **Aktör kaydı**. Reliable Services gibi Reliable Actor hizmetinin de Service Fabric çalışma zamanıyla kaydedilmesi gerekir. Ayrıca aktör türü de Actor çalışma zamanına kaydedilmelidir.
* **Aktör arabirimi**. Aktör arabirimi, bir aktörün baskın türdeki genel arabirimini tanımlamak için kullanılır. Reliable Actor model terminolojisinde aktör arabirimi, aktörün anlayıp işleyebileceği ileti türlerini tanımlamak için kullanılır. Aktör arabirimi diğer aktörler ve istemci uygulamaları tarafından aktöre ileti "göndermek" (zaman uyumsuz) amacıyla kullanılır. Reliable Actors birden fazla arabirim uygulayabilir.
* **ActorProxy sınıfı**. ActorProxy sınıfı, istemci uygulamaları tarafından aktör arabirimi aracılığıyla kullanıma sunulan yöntemleri çağırmak için kullanılır. ActorProxy sınıfı iki önemli işlev sunar:
  
  * Ad çözümlemesi: Kümedeki aktörni bulabilir (barındırılan kümenin düğümünü bulun).
  * Hata işleme: Yöntem çağrılarını yeniden deneyebilir ve sonra aktör konumunu yeniden çözümleyebilir. Örneğin, aktörin kümedeki başka bir düğüme yeniden konumlandırılmasını gerektiren bir hata olabilir.

Aktör arabirimlerinde geçerli olan önemli kurallar aşağıda verilmiştir:

* Aktör arabirim yöntemlerine aşırı yükleme yapılamaz.
* Aktör arabirimi yöntemleri çıkış, başvuru veya isteğe bağlı parametrelere sahip olmamalıdır.
* Genel arabirimler desteklenmez.

## <a name="create-the-application"></a>Uygulama oluşturma
Service Fabric uygulaması bir veya birden çok hizmet içerir ve bu hizmetlerin her biri, uygulamanın işlevselliğini sunma konusunda belirli bir role sahiptir. Son bölümde yüklediğiniz oluşturucu, ilk hizmetinizi oluşturmayı ve daha sonra hizmet eklemeyi kolaylaştırır.  Service Fabric Java uygulamalarını Eclipse’e yönelik bir eklentiyi kullanarak da oluşturabilir, derleyebilir ve dağıtabilirsiniz. Bkz. [Eclipse kullanarak ilk Java uygulamanızı oluşturma ve dağıtma](service-fabric-get-started-eclipse.md). Bu hızlı başlangıç için bir sayaç değerini alan ve depolayan tek bir hizmete sahip bir uygulama oluşturmak üzere Yeoman’ı kullanın.

1. Bir terminal penceresinde ``yo azuresfjava`` yazın.
2. Uygulamanızı adlandırın.
3. Birinci hizmetinizin türünü seçin ve adlandırın. Bu öğretici için bir Reliable Actor Hizmeti seçin. Diğer hizmet türleri hakkında daha fazla bilgi edinmek için bkz. [Service Fabric programlama modeline genel bakış](service-fabric-choose-framework.md).
   ![Java için Service Fabric Yeoman oluşturucusu][sf-yeoman]

Uygulamaya "HelloWorldActorApplication", aktöre de "HelloWorldActor" adını verirseniz aşağıdaki yapı iskelesi oluşturulur:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Reliable Actors temel parçaları
Önceki bölümlerde anlatılan temel kavramlar, Reliable Actor hizmetinin temel parçalarını oluşturur.

### <a name="actor-interface"></a>Aktör arabirimi
Aktör arabirimi tanımını içerir. Bu arabirim, aktör uygulaması ve aktörü çağıran istemciler tarafından paylaşılan aktör anlaşmasını tanımlar. Bu nedenle aktör uygulamasından ayrı bir yerde tanımlayıp birden fazla hizmet veya istemci uygulamasına paylaştırmak mantıklı bir yaklaşımdır.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Aktör hizmeti
Aktör uygulamanızı ve aktör kayıt kodunu içerir. Aktör sınıfı aktör arabirimini uygular. Aktörünüz görevini burada yapar.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Aktör kaydı
Aktör hizmetinin Service Fabric çalışma zamanındaki bir hizmet türüne kaydedilmesi gerekir. Aktör hizmetinin aktör örneklerinizi çalıştırması için aktör türünüzün de aktör hizmetine kaydedilmesi gerekir. `ActorRuntime` kayıt yöntemi bu işi aktörlerin yerine gerçekleştirir.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());
    
public static void main(String[] args) throws Exception {
        
        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Uygulama oluşturma
Service Fabric Yeoman şablonları, uygulamayı terminalden oluşturmak için kullanabileceğiniz bir [Gradle](https://gradle.org/) derleme betiği içerir.
Service Fabric Java bağımlılıkları Maven’dan alınır. Service Fabric Java uygulamalarını oluşturmak ve çalışmak için JDK ve Gradle’ın yüklü olduğundan emin olmanız gerekir. Yüklü değillerse, JDK ve Gradle’ı yükleme hakkında yönergeler için bkz. [Linux ile Service Fabric’i kullanmaya başlama](service-fabric-get-started-linux.md#set-up-java-development).

Uygulamayı derlemek ve paketlemek için şu komutu çalıştırın:

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>Uygulamayı dağıtma
Uygulama oluşturulduktan sonra uygulamayı yerel kümeye dağıtabilirsiniz.

1. Yerel Service Fabric kümesine bağlanın (kümenin [kurulu ve çalışıyor](service-fabric-get-started-linux.md#set-up-a-local-cluster) olması gerekir).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Uygulama paketini kümenin görüntü deposuna kopyalamak, uygulama türünü kaydetmek ve uygulamanın bir örneğini oluşturmak için şablonda verilen yükleme betiğini çalıştırın.

    ```bash
    ./install.sh
    ```

Oluşturulan uygulamayı dağıtma işlemi, diğer tüm Service Fabric uygulamalarında olduğu gibidir. Ayrıntılı yönergeler için [Service Fabric uygulamasını Service Fabric CLI ile yönetme](service-fabric-application-lifecycle-sfctl.md) ile ilgili belgelere bakın.

Bu komutların parametreleri, uygulama paketi içinde oluşturulmuş bildirimlerde bulunabilir.

Uygulama dağıtıldığında bir tarayıcı açın ve [http://localhost:19080/Explorer](http://localhost:19080/Explorer) konumundaki [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)'a gidin.
Ardından, **Uygulamalar** düğümünü genişletin ve geçerli olarak uygulamanızın türü için bir giriş ve bu türün ilk örneği için başka bir giriş olduğuna dikkat edin.

> [!IMPORTANT]
> Uygulamayı Azure 'da güvenli bir Linux kümesine dağıtmak için, Service Fabric çalışma zamanı ile uygulamanızı doğrulamak üzere bir sertifika yapılandırmanız gerekir. Bunun yapılması, Reliable Actors hizmetlerinizin temel alınan Service Fabric çalışma zamanı API 'Leriyle iletişim kurmasını sağlar. Daha fazla bilgi edinmek için bkz. [Reliable Services uygulamasını Linux kümelerinde çalışacak şekilde yapılandırma](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Test istemcisini başlatma ve yük devre gerçekleştirme
Aktörler kendi başına hiçbir şey yapmaz; başka bir hizmet veya istemcinin aktörlere ileti göndermesini gerektirir. Actor şablonu, actor hizmetiyle etkileşim kurmak üzere kullanabileceğiniz basit bir test betiği içerir.

> [!Note]
> Test istemcisi, aktör hizmeti ile aynı kümede çalışması gereken aktörlerle iletişim kurmak için ActorProxy sınıfını kullanır veya aynı IP adresi alanını paylaşır.  Test istemcisini, yerel geliştirme kümesiyle aynı bilgisayarda çalıştırabilirsiniz.  Ancak, uzak bir kümedeki aktörlerle iletişim kurmak için, aktörlerle harici iletişimi işleyen kümeye bir ağ geçidi dağıtmanız gerekir.

1. Actor hizmetinin çıktısını görmek için izleme yardımcı programını kullanarak betiği çalıştırın.  Test betiği bir sayacın değerini yükseltmek için aktörde `setCountAsync()` yöntemine; yeni sayaç değerini edinmek içinse aktörde `getCountAsync()` yöntemine çağrı yapar ve bu değeri konsolda görüntüler.

   MAC OS X söz konusu olduğunda, aşağıdaki ek komutları çalıştırarak HelloWorldTestClient klasörünü kapsayıcının içindeki bazı konumlara kopyalamanız gerekir.    
    
    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer’da actor hizmetinin birincil çoğaltmasını barındıran düğümü bulun. Aşağıdaki ekran görüntüsünde düğüm 3’tür. Birincil hizmet çoğaltması okuma ve yazma işlemlerini işler.  Daha sonra hizmet durumundaki değişiklikler, aşağıdaki ekran görüntüsünde 0 ve 1 düğümlerinde çalışan ikincil çoğaltmalara çoğaltılır.

    ![Service Fabric Explorer’da birincil çoğaltmayı bulma][sfx-primary]

3. **Düğümler**’de, önceki adımda bulduğunuz düğüme tıklayın ve Eylemler menüsünden **Devre dışı bırak (yeniden başlat)** öğesini seçin. Bu eylem, birincil hizmet çoğaltmasını çalıştıran düğümü yeniden başlatır ve başka bir düğümde çalışan ikincil çoğaltmalardan birine yük devretmeyi zorlar.  Bu ikincil çoğaltma birincil konumuna yükseltilir, farklı bir düğümde başka bir ikincil çoğaltma oluşturulur ve birincil çoğaltma okuma/yazma işlemleri almaya başlar. Düğüm yeniden başlatılırken test istemcisinin çıktısına dikkat edin ve yük devretmeye rağmen sayacın artmaya devam ettiğini gözlemleyin.

## <a name="remove-the-application"></a>Uygulamayı kaldırma
Uygulama örneğini silmek, uygulama paketinin kaydını silmek ve uygulama paketini kümenin görüntü deposundan kaldırmak için şablonda sağlanan kaldırma betiğini kullanın.

```bash
./uninstall.sh
```

Service Fabric Explorer’da uygulamanın ve uygulama türünün artık **Uygulamalar** düğümünde olmadığını görürsünüz.

## <a name="service-fabric-java-libraries-on-maven"></a>Maven'da Service Fabric Java kitaplıkları
Maven’da barındırılan Service Fabric Java kitaplıkları. Projelerinizin **mavenCentral**’daki Service Fabric Java kitaplıklarını kullanması için ``pom.xml`` veya ``build.gradle`` altına bağımlılıklar ekleyebilirsiniz. 

### <a name="actors"></a>Aktörler

Uygulamanız için Service Fabric Güvenilir Aktör desteği.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Hizmetler

Uygulamanız için Service Fabric Reliable Services desteği.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Diğerleri
#### <a name="transport"></a>Aktarım

Service Fabric Java uygulaması için Aktarım katmanı desteği. Aktarım katmanında özellikle programlamadığınız sürece bu bağımlılığı Güvenilir Aktör veya Hizmet uygulamalarınız için özellikle eklemeniz gerekmez.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric desteği

Yerel Service Fabric çalışma zamanıyla iletişim kuran Service Fabric için sistem düzeyinde destek. Bu bağımlılığı Güvenilir Aktör veya Hizmet uygulamalarınız için özellikle eklemeniz gerekmez. Yukarıdaki diğer bağımlılıkları eklediğinizde bu otomatik olarak Maven’dan alınır.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>Sonraki adımlar

* [Linux’ta Eclipse kullanarak ilk Service Fabric Java uygulamanızı oluşturma](service-fabric-get-started-eclipse.md)
* [Reliable Actors hakkında daha fazla bilgi edinin](service-fabric-reliable-actors-introduction.md)
* [Service Fabric CLI’sını kullanarak Service Fabric kümeleriyle etkileşim kurma](service-fabric-cli.md)
* [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin
* [Service Fabric CLI ile çalışmaya başlama](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
