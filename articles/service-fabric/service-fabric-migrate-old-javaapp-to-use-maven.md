---
title: Java SDK 'dan Maven 'e geçiş
description: Service Fabric Java SDK’sını kullanan eski JAVA uygulamalarını, Service Fabric Java bağımlılıklarını Maven’dan alacak şekilde güncelleştirin. Bu ayarları tamamlandıktan sonra eski Java uygulamalarınız oluşturmaya hazırdır.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 3efa51f5632dd5cdc274ea39df5178aa0351a01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652305"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Eski Java Service Fabric uygulamanızı Maven’dan Java kitaplıklarını alacak şekilde güncelleştirme
Service Fabric Java ikilileri, Service Fabric Java SDK 'dan Maven barındırmaya taşınmıştır. **Mavencentral** kullanarak en son Java bağımlılıklarını Service Fabric getirebilirsiniz. Bu kılavuz, Maven tabanlı yapıyla uyumlu olacak şekilde Service Fabric Java SDK 'Sı için oluşturulan mevcut Java uygulamalarını güncelleştirmenize yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

1. İlk olarak, var olan Java SDK 'sını kaldırın.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. [Burada](service-fabric-cli.md) belirtilen adımları izleyerek Service Fabric CLI'sını yükleyin.

3. Service Fabric Java uygulamalarını oluşturmak ve üzerinde çalışmak için JDK 1,8 ve Gradle yüklü olduğundan emin olun. Henüz yüklü değilse, JDK 1.8 (openjdk-8-jdk) ve Gradle’ı yüklemek için aşağıdakini çalıştırabilirsiniz.

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. [Buradaki](service-fabric-application-lifecycle-sfctl.md) adımları takip ederek uygulamanızın yükleme ve kaldırma betiklerini yeni Service Fabric CLI’sını kullanacak şekilde güncelleştirin. Hızlı başlangıç [örneklerine](https://github.com/Azure-Samples/service-fabric-java-getting-started) bakabilirsiniz.

>[!TIP]
> Service Fabric Java SDK'sını kaldırdıktan sonra Yeoman çalışmaz. Service Fabric Yeoman Java şablon oluşturucusunu çalıştırmak için [burada](service-fabric-create-your-first-linux-application-with-java.md) belirtilen önkoşulları takip edin.

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

Uygulamanız için Service Fabric Durum Bilgisi Olmayan Hizmet desteği.

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

### <a name="others"></a>Diğer

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

## <a name="migrating-service-fabric-stateless-service"></a>Microsoft Azure Service Fabric Durum Bilgisi Olmayan Hizmeti Geçirme

Service Fabric bağımlılıkları Maven’dan alınan Service Fabric durum bilgisi olmayan Java hizmetinizi oluşturabilmek için hizmet içinden ``build.gradle`` dosyasını güncelleştirmeniz gerekir. Daha önce aşağıdaki gibiydi:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

Artık Maven’dan bağımlılıkları almak için **güncelleştirilmiş** ``build.gradle`` içinde ilgili bölümler aşağıdaki gibi olmalıdır:

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

Genel olarak bir Service Fabric durum bilgisi olmayan Java hizmetinde betiğin nasıl olması gerektiği hakkında genel bir fikir edinmek için başlarken örneklerimizdeki herhangi bir örneğe bakabilirsiniz. EchoServer örneği için [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) böyledir.

## <a name="migrating-service-fabric-actor-service"></a>Service Fabric Aktör Hizmetini Geçirme

Service Fabric bağımlılıkları Maven’dan alınan Service Fabric Aktör Java hizmetinizi oluşturabilmek için arabirim paketi içinde ve Hizmet paketinde ``build.gradle`` dosyasını güncelleştirmeniz gerekir. TestClient paketiniz varsa, bunu da güncelleştirmeniz gerekir. ``Myactor`` aktörünüz için güncelleştirmeniz gereken yerler şunlardır:

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Arabirim projesi için derleme betiğini güncelleştirme

Daha önce aşağıdaki gibiydi:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

Artık Maven’dan bağımlılıkları almak için **güncelleştirilmiş** ``build.gradle`` içinde ilgili bölümler aşağıdaki gibi olmalıdır:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Aktör projesi için derleme betiğini güncelleştirme

Daha önce aşağıdaki gibiydi:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

Artık Maven’dan bağımlılıkları almak için **güncelleştirilmiş** ``build.gradle`` içinde ilgili bölümler aşağıdaki gibi olmalıdır:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Test istemcisi projesi için derleme betiğini güncelleştirme

Buradaki değişiklikler, önceki bölümde (aktör projesi) açıklanan değişikliklere benzerdir. Daha önce kullanılan Gradle betiği şöyleydi:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
    destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

Artık Maven’dan bağımlılıkları almak için **güncelleştirilmiş** ``build.gradle`` içinde ilgili bölümler aşağıdaki gibi olmalıdır:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Linux üzerinde Yeoman kullanarak ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-create-your-first-linux-application-with-java.md)
* [Linux üzerinde Eclipse için Service Fabric Eklentisi kullanarak ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-get-started-eclipse.md)
* [Service Fabric CLI’sını kullanarak Service Fabric kümeleriyle etkileşim kurma](service-fabric-cli.md)
