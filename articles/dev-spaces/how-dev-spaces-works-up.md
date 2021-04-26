---
title: Kodunuzu Azure Dev Spaces çalışır şekilde çalıştırma
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Kodunuzu Azure Kubernetes hizmetinde Azure Dev Spaces ile çalıştırma işlemlerini açıklar
keywords: azds. YAML, Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014440"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Kodunuzu Azure Dev Spaces çalışır şekilde çalıştırma

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces, Kubernetes uygulamalarının hızla yinelenemez ve hata ayıklamanın yanı sıra Azure Kubernetes hizmeti (AKS) kümesinde ekibinizle işbirliği yapmak için birden çok yol sağlar. [Projeniz bir geliştirme alanında çalıştırılmak üzere hazırlandıktan][how-it-works-prep]sonra, geliştirme alanlarını kullanarak projenizi derleyip aks kümenizde çalıştırabilirsiniz.

Bu makalede, hangi işlemlerin geliştirme alanları ile AKS 'de çalıştırılacağını açıklar.

## <a name="run-your-code"></a>Kodunuzu çalıştırın

Kodunuzu bir geliştirme alanında çalıştırmak için, `up` dosyanız ile aynı dizinde komutunu verin `azds.yaml` :

```cmd
azds up
```

Bu `up` komut, projenizi derlemek ve geliştirme alanında çalıştırmak için gereken uygulama kaynak dosyalarınızı ve diğer yapıtları karşıya yükler. Buradan, geliştirme alanınızdaki denetleyici:

1. Uygulamanızı dağıtmak için Kubernetes nesnelerini oluşturur.
1. Uygulamanız için kapsayıcıyı oluşturur.
1. Uygulamanızı geliştirme alanına dağıtır.
1. Yapılandırıldıysa, uygulama uç noktanız için genel olarak erişilebilen bir DNS adı oluşturur.
1. Kullanarak uygulama uç noktanıza erişim sağlamak için *bağlantı noktası iletme* kullanır http://localhost .
1. Stdout ve stderr 'i istemci tarafı araçlarına iletir.


## <a name="starting-a-service"></a>Hizmet başlatılıyor

Bir hizmeti bir geliştirme alanında başlattığınızda, istemci tarafı araçları ve denetleyicisi, kaynak dosyalarınızı eşitlemeye, kapsayıcınızı ve Kubernetes nesnelerinizi oluşturmaya ve uygulamanızı çalıştırmaya yönelik olarak çalışır.

Daha ayrıntılı bir düzeyde, şunu çalıştırdığınızda ne olur `azds up` :

1. [Dosyalar][sync-section] , kullanıcının bilgisayarından, kullanıcının aks kümesi için benzersiz olan bir Azure dosya depolama alanına eşitlenir. Kaynak kodu, Helz grafiği ve yapılandırma dosyaları karşıya yüklenir.
1. Denetleyici yeni bir oturum başlatma isteği oluşturur. Bu istek, benzersiz KIMLIK, alan adı, kaynak kodu yolu ve bir hata ayıklama bayrağı dahil olmak üzere çeşitli özellikler içerir.
1. Denetleyici, Held grafiğindeki *$ (Tag)* yer tutucusunu BENZERSIZ oturum kimliğiyle değiştirir ve hizmetiniz Için Held grafiğini yüklüyor. Hele grafiğine benzersiz oturum KIMLIĞI başvurusu eklemek, bu belirli oturumun AKS kümesine dağıtılan kapsayıcının oturum isteğine ve ilgili bilgilere geri bağlanması için izin verir.
1. Helk grafiğinin yüklenmesi sırasında Kubernetes Web kancası giriş sunucusu, uygulamanızın Pod öğesine yönetim için ek kapsayıcılar ekler ve projenin kaynak koduna erişin. Devspaces-proxy ve devspaces-proxy-init kapsayıcıları, HTTP izleme ve alan yönlendirmesi sağlamak için eklenir. Devspaces-Build kapsayıcısı, uygulama kapsayıcısını oluşturmak için Docker örneğine ve proje kaynak koduna erişimi olan Pod 'yu sağlamak için eklenmiştir.
1. Uygulamanın Pod 'ı başlatıldığında, uygulama kapsayıcısını oluşturmak için devspaces oluşturma kapsayıcısı ve devspaces-proxy-init kapsayıcısı kullanılır. Uygulama kapsayıcısı ve devspaces-proxy kapsayıcıları başlatılır.
1. Uygulama kapsayıcısı başladıktan sonra, istemci tarafı işlevselliği, uygulamanıza HTTP erişimi sağlamak için Kubernetes *bağlantı noktası iletme* işlevini kullanır http://localhost . Bu bağlantı noktası iletme geliştirici bilgisayarınızı geliştirme alanınızdaki hizmete bağlar.
1. Pod 'daki tüm kapsayıcılar başlatıldığında hizmet çalışır. Bu noktada, istemci tarafı işlevleri HTTP izlemelerinin, stdout 'ın ve stderr 'in akışa başlamaktadır. Bu bilgiler, geliştiricinin istemci tarafı işlevselliği tarafından görüntülenir.

## <a name="updating-a-running-service"></a>Çalışan bir hizmet güncelleştiriliyor

Bir hizmet çalışırken, proje kaynak dosyalarından herhangi biri değiştiğinde bu hizmeti güncelleştirebilme Azure Dev Spaces. Geliştirme alanları, değiştirilen dosya türüne bağlı olarak hizmetin güncelleştirilmesini de işler. Geliştirme alanlarının çalışan bir hizmeti güncelleştirebilmenin üç yolu vardır:

* Bir dosyayı doğrudan güncelleştirme
* Uygulamanın çalışan uygulamanın kapsayıcısı içindeki işlemini yeniden oluşturma ve yeniden başlatma
* Uygulamanın kapsayıcısını yeniden oluşturma ve yeniden dağıtma

![Azure Dev Spaces dosya eşitleme](media/how-dev-spaces-works/file-sync.svg)

HTML, CSS ve cshtml dosyaları gibi statik varlıklar olan bazı proje dosyaları, hiçbir şeyi yeniden başlatmadan doğrudan uygulamanın kapsayıcısında güncelleştirilemeyebilir. Statik bir varlık değişirse, yeni dosya dev alanıyla eşitlenir ve ardından çalışan kapsayıcı tarafından kullanılır.

Kaynak kodu veya uygulama yapılandırma dosyaları gibi dosyalardaki değişiklikler, uygulamanın çalışan kapsayıcı içindeki işlemi yeniden başlatılarak uygulanabilir. Bu dosyalar eşitlendikten sonra, *devhostagent* işlemi kullanılarak çalışan kapsayıcı içinde uygulamanın işlemi yeniden başlatılır. Başlangıçta uygulamanın kapsayıcısını oluştururken denetleyici, uygulama için başlatma komutunun yerine *devhostagent* adlı farklı bir işlem koyar. Uygulamanın gerçek işlemi daha sonra *devhostagent* altında bir alt işlem olarak çalıştırılır ve çıkışı *devhostagent*'ın çıkışı kullanılarak gönderilir. *Devhostagent* Işlemi de dev alanlarının bir parçasıdır ve çalışan kapsayıcıda geliştirme alanları adına komutları yürütebilir. Yeniden başlatma işlemi gerçekleştirirken *devhostagent*:

* Uygulamayla ilişkili geçerli işlem veya işlemleri durdur
* Uygulamayı yeniden oluşturur
* Uygulamayla ilişkili işlem veya işlemleri yeniden başlatır

*Devhostagent* 'ın önceki adımları yürütme yöntemi [' de `azds.yaml` yapılandırılır][azds-yaml-section].

Dockerfiles, csproj dosyaları veya hele grafiğinin herhangi bir bölümü gibi proje dosyalarına yapılan güncelleştirmeler, uygulamanın kapsayıcısının yeniden oluşturulmasını ve dağıtılmasını gerektirir. Bu dosyalardan biri dev alanı ile eşitlendiğinde, denetleyici [HELI Upgrade][helm-upgrade] komutunu çalıştırır ve uygulamanın kapsayıcısı yeniden oluşturulur ve yeniden dağıtılır.

## <a name="file-synchronization"></a>Dosya eşitleme

Bir geliştirme alanında uygulama ilk kez başlatıldığında, uygulamanın tüm kaynak dosyaları karşıya yüklenir. Uygulama çalışırken ve daha sonra yeniden başlatıldığında yalnızca değiştirilen dosyalar karşıya yüklenir. Bu işlemi koordine etmek için iki dosya kullanılır: bir istemci tarafı dosyası ve bir denetleyici tarafı dosyası.

İstemci tarafı dosyası geçici bir dizinde depolanır ve geliştirme alanlarında çalıştırdığınız proje dizini karmasını temel alarak adlandırılır. Örneğin, Windows 'da, projeniz için *Users\username\appdata\local\temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef synclog* gibi bir dosyanız olur. Linux 'ta, istemci tarafı dosya */tmp* dizininde depolanır. Komutunu çalıştırarak macOS 'ta dizini bulabilirsiniz `echo $TMPDIR` .

Bu dosya JSON biçimindedir ve şunları içerir:

* Geliştirme alanı ile eşitlenen her proje dosyası için bir giriş
* Bir eşitleme KIMLIĞI
* Son eşitleme işleminin zaman damgası

Her proje dosyası girişi, dosyanın ve zaman damgasının yolunu içerir.

Denetleyici tarafı dosya AKS kümesinde depolanır. Eşitleme KIMLIĞINI ve son eşitlemenin zaman damgasını içerir.

Eşitleme zaman damgaları, istemci tarafı ve denetleyici tarafı dosyaları arasında eşleşmediği zaman eşitleme gerçekleşir. Eşitleme sırasında, istemci tarafı araçları, istemci tarafı dosyadaki dosya girdileri üzerinde yinelenir. Dosyanın zaman damgası eşitleme zaman damgasından sonra ise, bu dosya geliştirme alanıyla eşitlenir. Eşitleme tamamlandıktan sonra, eşitleme zaman damgaları hem istemci tarafı hem de denetleyici tarafı dosyalarda güncelleştirilir.

İstemci tarafı dosyası yoksa, tüm proje dosyaları eşitlenir. Bu davranış, istemci tarafı dosyasını silerek tam eşitlemeye zorlamanıza olanak sağlar.

## <a name="how-running-your-code-is-configured"></a>Kodunuzun çalıştırılması nasıl yapılandırılır?

Azure Dev Spaces, `azds.yaml` hizmetinizi yüklemek ve yapılandırmak için dosyasını kullanır. Denetleyici, `install` `azds.yaml` helk grafiğini yüklemek ve Kubernetes nesnelerini oluşturmak için dosyadaki özelliğini kullanır:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Varsayılan olarak, `prep` komut HELI grafiğini oluşturacaktır. Ayrıca *Install. Chart* özelliğini Held grafiğinin dizinine ayarlar. Farklı bir konumda HELI grafiği kullanmak isterseniz, bu özelliği bu konumu kullanacak şekilde güncelleştirebilirsiniz.

Hele grafiklerini yüklerken Azure Dev Spaces Helu grafiğindeki değerleri geçersiz kılmak için bir yol sağlar. Held grafiğinin varsayılan değerleri içinde bulunur `charts/APP_NAME/values.yaml` .

*Install. Values* özelliğini kullanarak, Held grafiğinde değiştirilmesini istediğiniz değerleri tanımlayan bir veya daha fazla dosyayı listeleyebilirsiniz. Örneğin, uygulamanızı bir geliştirme alanında çalıştırırken bir ana bilgisayar adı veya veritabanı yapılandırması isterseniz, bu geçersiz kılma işlevini kullanabilirsiniz. Ayrıca *, bir de* ekleyebilirsiniz. dosya adlarından sonuna kadar isteğe bağlı olarak ayarlayın.

*Install. set* özelliği, hele grafiğinde değiştirilmesini istediğiniz bir veya daha fazla değeri yapılandırmanıza olanak tanır. *Install. set* içinde yapılandırılan tüm değerler, *Install. Values* içinde listelenen dosyalarda yapılandırılan değerleri geçersiz kılar. *Install. set* altındaki özellikler, Held grafiğindeki değerlere bağımlıdır ve oluşturulan Held grafiğine bağlı olarak farklı olabilir.

Yukarıdaki örnekte, *Install. set. replicaCount* özelliği denetleyiciye geliştirme alanınızda uygulamanızın kaç örnek çalıştırılacağını söyler. Senaryonuza bağlı olarak, bu değeri artırabilirsiniz, ancak uygulamanızın Pod öğesine bir hata ayıklayıcı ekleme etkisi olur. Daha fazla bilgi için bkz. [sorun giderme makalesi][troubleshooting].

Oluşturulan HELI grafiğinde kapsayıcı görüntüsü *{{olarak ayarlanır. Values. Image. Repository}}: {{. Values. Image. Tag}}*. `azds.yaml`Dosya, *install. set. Image. Tag* özelliğini varsayılan olarak *$ (Tag)* olarak tanımlar; bu, {{için değer olarak kullanılır *. Values. Image. Tag}}*. *Install. set. Image. Tag* özelliğini bu şekilde ayarlayarak, uygulamanızın kapsayıcı görüntüsünün Azure dev Spaces çalıştırılırken farklı bir şekilde etiketlenmesine izin verir. Bu özel durumda, görüntü şöyle etiketlenebilir *\<value from image.repository> : $ (etiket)*. Geliştirme alanları tanıması ve AKS kümesindeki kapsayıcıyı bulmak için, *$ (Tag)* değişkenini   *Install. set. image. Tag* değeri olarak kullanmanız gerekir.

Yukarıdaki örnekte `azds.yaml` *Install. set. ınress. Konakları* tanımlanmaktadır. *Install. set. ingress. hosts* özelliği, genel uç noktalar için bir ana bilgisayar adı biçimi tanımlar. Bu özellik ayrıca, denetleyici tarafından belirtilen değerler olan *$ (Spaceprefix)*, *$ (rootspaceprefix)* ve *$ (hostsuffix*) kullanır.

*$ (Spaceprefix)* , *spacename. s* biçimini alan alt dev alanının adıdır. *$ (Rootspaceprefix)* , üst alanın adıdır. Örneğin, *azureuser* *varsayılan* bir alt alandır, *$ (rootspaceprefix)* değeri *varsayılandır* ve *$ (spaceprefix)* değeri *azureuser. s*' dir. Boşluk bir alt boşluk değilse, *$ (Spaceprefix)* boştur. Örneğin, *varsayılan* alanın üst alanı yoksa, *$ (rootspaceprefix)* değeri *varsayılandır* ve *$ (spaceprefix)* değeri boştur. *$ (Hostsuffix)* , aks kümenizde çalışan Azure dev Spaces giriş denetleyicisine işaret eden bir DNS son ekidir. Bu DNS son eki, örneğin bir joker karakter DNS girdisine karşılık gelir *\* . Azure Dev Spaces denetleyicisi AKS kümenize eklendiğinde oluşturulan RANDOM_VALUE. EUS. azds. IO*.

Yukarıdaki `azds.yaml` dosyada *Install. set. ınress. konaklarına* , uygulamanızın ana bilgisayar adını değiştirme de güncelleştirebilirsiniz. Örneğin, uygulamanızın ana bilgisayar adını *$ (Spaceprefix) $ (rootSpacePrefix) webön uç $ (hostSuffix* ) $ ( *rootspaceönek) Web $ (hostsuffix*) ile $ (hostsuffix) olarak basitleştirecek şekilde basitleştirmek istiyorsanız.

Uygulamanızın kapsayıcısını oluşturmak için, denetleyici yapılandırma dosyasının aşağıdaki bölümlerini kullanır `azds.yaml` :

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Denetleyici, uygulamanızı derlemek ve çalıştırmak için bir Dockerfile kullanır.

*Build. Context* özelliği, Dockerfiles 'ın mevcut olduğu dizini listeler. *build.dockerfile* özelliği, uygulamanın üretim sürümünü oluşturmak Için dockerfile adını tanımlar. *configurations.develop.build.dockerfile* özelliği, uygulamanın geliştirme sürümü Için dockerfile adını yapılandırır.

Geliştirme ve üretim için farklı Dockerfiles bulundurmak, geliştirme sırasında belirli şeyleri etkinleştirmenizi ve üretim dağıtımlarında bu öğeleri devre dışı bırakmanızı sağlar. Örneğin, bir üretim ortamında geliştirme ve devre dışı bırakma sırasında hata ayıklamayı veya daha ayrıntılı günlük kaydını etkinleştirebilirsiniz. Ayrıca, Dockerfiles farklı şekilde adlandırılmışsa veya farklı bir konumdaysa bu özellikleri güncelleştirebilirsiniz.

Geliştirme sırasında hızlıca yinelemenize yardımcı olmak için Azure Dev Spaces değişiklikleri yerel projenizden eşitler ve uygulamanızı artımlı olarak güncelleştirir. `azds.yaml`Eşitleme ve güncelleştirmeyi yapılandırmak için yapılandırma dosyasının aşağıdaki bölümü kullanılır:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Değişiklikleri eşitlenecek dosyalar ve dizinler, *Configurations. geliþme. Container. Sync* özelliğinde listelenir. Bu dizinler, komutu çalıştırdığınız zaman ve `up` değişikliklerin algılandığına göre başlangıçta eşitlenir. Geliştirme alanınızda eşitlenmesini istediğiniz ek veya farklı dizinler varsa, bu özelliği değiştirebilirsiniz.

*Configurations. geliştirmenin. Container. Iterate. buildCommands* özelliği, uygulamanın geliştirme senaryosunda nasıl oluşturulacağını belirtir. *Configurations. geliştirmenin. Container. Command* özelliği, uygulamayı bir geliştirme senaryosunda çalıştırmak için komutunu sağlar. Geliştirme sırasında kullanmak istediğiniz ek derleme veya çalışma zamanı bayrakları veya parametreler varsa, bu özelliklerden birini güncellemek isteyebilirsiniz.

*Configurations. geliþme. Container. Iterate. Processestokıll* , uygulamayı durdurmak için sonlandırılmak üzere süreçler listeler. Geliştirme sırasında uygulamanızın yeniden başlatma davranışını değiştirmek istiyorsanız bu özelliği güncellemek isteyebilirsiniz. Örneğin, *konfigürasyonları güncelleştirdiyseniz. geliştirme. Container. Iterate. buildCommands* veya *Configurations. geliþme. Container. Command* özellikleri, uygulamanın nasıl oluşturulduğunu veya başlatıldığını değiştirmek için, hangi işlemlerin durdurulduğunu değiştirmeniz gerekebilir.

Komutunu kullanarak kodunuzu hazırlarken `azds prep` bayrağını ekleme seçeneğiniz vardır `--enable-ingress` . Bayrağı eklemek, `--enable-ingress` uygulamanız için herkese açık olarak erişilebilen BIR URL oluşturur. Bu bayrağı atlarsanız, uygulamaya yalnızca küme içinden veya localhost tüneli kullanılarak erişilebilir. Komutu çalıştırdıktan sonra `azds prep` Bu ayarı değiştirmek için içindeki giriş *. Enabled* özelliğini değiştirme `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Sonraki adımlar

Ağ ve isteklerin nasıl yönlendirildiği hakkında daha fazla bilgi edinmek için Azure Dev Spaces [yönlendirme 'nin Azure dev Spaces nasıl çalıştığını][how-it-works-routing]görün.

Hızlı yineleme ve Kubernetes ile geliştirme hakkında daha fazla bilgi edinmek için bkz. [Kubernetes Köprüsü nasıl çalıştığı][how-it-works-bridge-to-kubernetes] ve [Azure dev Spaces ile kodunuzda uzaktan hata ayıklamanın nasıl çalıştığı][how-it-works-remote-debugging].


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md