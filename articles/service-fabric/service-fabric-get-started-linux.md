---
title: Linux üzerinde geliştirme ortamınızı ayarlama
description: Linux üzerinde çalışma zamanını ve SDK'yı yükleyip yerel bir geliştirme kümesi oluşturun. Bu kurulumu tamamladıktan sonra uygulama derlemek için hazır hale gelirsiniz.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: fcf0aeec27415d03c528e42ad5341a92bd299d88
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869410"
---
# <a name="prepare-your-development-environment-on-linux"></a>Linux üzerinde geliştirme ortamınızı hazırlama
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Linux geliştirme makinenizde [Azure Service Fabric uygulamaları](service-fabric-application-model.md) dağıtıp çalıştırmak için çalışma zamanını ve ortak SDK'yı yükleyin. Ayrıca isteğe bağlı Java ve .NET Core geliştirme SDK'larını yükleyebilirsiniz. 

Bu makaledeki adımlarda, Linux 'ta yerel olarak yüklediğiniz veya [Service Fabric OneBox kapsayıcı görüntüsünü](https://hub.docker.com/_/microsoft-service-fabric-onebox)(örn.) kullandığınız varsayılır `mcr.microsoft.com/service-fabric/onebox:u18` .

Bulutta veya şirket içinde barındırılan Service Fabric varlıklarını Azure Service Fabric komut satırı arabirimi (CLı) ile yönetebilirsiniz. CLI'yi yükleme hakkında bilgi için bkz. [Service Fabric CLI'sini ayarlama](./service-fabric-cli.md).


## <a name="prerequisites"></a>Önkoşullar

Geliştirme için şu işletim sistemi sürümleri desteklenir.

* Ubuntu 16,04 ( `Xenial Xerus` ), 18,04 ( `Bionic Beaver` )

    `apt-transport-https` paketinin yüklü olduğundan emin olun.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (Service Fabric önizleme desteği)


## <a name="installation-methods"></a>Yükleme yöntemleri

<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

# <a name="ubuntu"></a>[Ubuntu](#tab/sdksetupubuntu)

## <a name="update-your-apt-sources"></a>APT kaynaklarınızı güncelleştirme
SDK ve ilişkili çalışma zamanı paketini apt-get komut satırı aracıyla yüklemek için, öncelikle Advanced Packaging Tool (APT) kaynaklarınızı güncelleştirmelisiniz.

## <a name="script-installation"></a>Betikle yükleme

Kolaylık sağlaması için, Service Fabric çalışma zamanını ve Service Fabric ortak SDK 'sını, [ **sfctl** CLI](service-fabric-cli.md)ile birlikte yüklemek için bir komut dosyası sağlanır. Betiği çalıştırdığınızda yüklenen yazılıma ait tüm lisans şartlarını kabul ettiğiniz varsayılır. Alternatif olarak, ilgili lisansların yanı sıra ilişkili lisansların de bulunduğu sonraki bölümde [el ile yükleme](#manual-installation) adımlarını çalıştırabilirsiniz.

Betik başarılı bir şekilde çalıştırıldıktan sonra [Yerel kümeyi ayarlama](#set-up-a-local-cluster) bölümüne geçebilirsiniz.

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

## <a name="manual-installation"></a>El ile yükleme
Service Fabric çalışma zamanı ve ortak SDK'yı el ile yüklemek için bu kılavuzdaki adımları izleyin.

1. Bir terminal açın.

2. Deponuza `dotnet` karşılık gelen kaynak listenize depoyu ekleyin.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Yeni MS Open Tech GNU gizlilik koruyucusu (GnuPG veya GPG) anahtarını APT kimlik anahtarlığı ' e ekleyin.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Resmi Docker GPG anahtarını APT anahtarlığınıza ekleyin.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Docker deposunu ayarlayın.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. APT kimlik anahtarlığı 'e Azul JDK anahtarını ekleyin ve deposunu ayarlayın.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Paket listelerinizi yeni eklenen depolara göre yenileyin.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Yerel küme için Service Fabric SDK'sını yükleme ve ayarlama

Kaynaklarınızı güncelleştirdikten sonra SDK’yı yükleyebilirsiniz. Service Fabric SDK paketini yükleyin, yüklemeyi onaylayın ve lisans sözleşmesini kabul edin.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Aşağıdaki komutlar, Service Fabric paketlerine yönelik lisansı kabul etme işlemini otomatik hale getirir:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

# <a name="red-hat-enterprise-linux-74"></a>[Red Hat Enterprise Linux 7.4](#tab/sdksetuprhel74)

## <a name="update-your-yum-repositories"></a>VUM depolarınızı güncelleştirme
Asum komut satırı aracı aracılığıyla SDK ve ilişkili çalışma zamanı paketini yüklemek için, önce paket kaynaklarınızı güncelleştirmeniz gerekir.

## <a name="manual-installation-rhel"></a>El ile yükleme (RHEL)
Service Fabric çalışma zamanı ve ortak SDK'yı el ile yüklemek için bu kılavuzdaki adımları izleyin.

1. Bir terminal açın.
2. Extra Packages for Enterprise Linux’u (EPEL) indirip yükleyin.

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Sisteminize EfficiOS RHEL7 paket deposunu ekleyin.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. EfficiOS paketi imzalama anahtarını yerel GPG kimlik anahtarlığına aktarın.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Microsoft RHEL deposunu sisteminize ekleyin.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster-rhel"></a>Yerel küme için Service Fabric SDK 'sını (RHEL) kurma ve kurma

Kaynaklarınızı güncelleştirdikten sonra SDK’yı yükleyebilirsiniz. Service Fabric SDK paketini yükleyin, yüklemeyi onaylayın ve lisans sözleşmesini kabul edin.

```bash
sudo yum install servicefabricsdkcommon
```

---

## <a name="included-packages"></a>Dahil edilen paketler
SDK yüklemesiyle birlikte gelen Service Fabric çalışma zamanı, aşağıdaki tabloda yer alan paketleri içerir. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.7 | AzulJDK 1,8 | Npm’de örtük | en son |
**RHEL** | - | OpenJDK 1.8 | Npm’de örtük | en son |

## <a name="set-up-a-local-cluster"></a>Yerel küme oluşturma
1. Geliştirme için yerel bir Service Fabric kümesi başlatın.

# <a name="container-based-local-cluster"></a>[Kapsayıcı tabanlı yerel küme](#tab/localclusteroneboxcontainer)

Kapsayıcı tabanlı [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) kümesi başlatın.

1. Docker Kapsayıcıları dağıtabilmek için Moby 'yi yükler.
    ```bash
    sudo apt-get install moby-engine moby-cli -y
    ```
2. Aşağıdaki ayarlarla, ana bilgisayarınızda Docker Daemon yapılandırmasını güncelleştirin ve Docker Daemon programını yeniden başlatın. Ayrıntılar: [IPv6 desteğini etkinleştir](https://docs.docker.com/config/daemon/ipv6/)

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```

3. Kümeyi başlatın.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Varsayılan olarak bu, görüntüyü Service Fabric’in en son sürümüyle çeker. Belirli düzeltmeler için lütfen [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) sayfasını ziyaret edin.

# <a name="local-cluster"></a>[Yerel küme](#tab/localcluster)

Yukarıdaki adımları kullanarak SDK 'Yı yükledikten sonra yerel bir küme başlatın.

1. Küme kurulum betiğini çalıştırın.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

---

2. Bir Web tarayıcısı açın ve **Service Fabric Explorer** () sayfasına gidin `http://localhost:19080/Explorer` . Küme başlatıldığında, Service Fabric Explorer panosunu görürsünüz. Kümenin tamamen ayarlanması birkaç dakika sürebilir. Tarayıcınız URL’yi açamazsa veya Service Fabric Explorer sistemin hazır olduğunu göstermezse, birkaç dakika bekleyip tekrar deneyin.

    ![Linux üzerinde Service Fabric Explorer][sfx-linux]

    Şimdi önceden oluşturulmuş Service Fabric uygulama paketlerini ve yeni paketleri konuk kapsayıcılar veya konuk yürütülebilir öğelerine göre dağıtabilirsiniz. Java veya .NET Core SDK’larını kullanarak yeni hizmetler oluşturmak için sonraki bölümlerde yer alan kurulum adımlarını izleyin.


> [!NOTE]
> Tek başına kümeler Linux’da desteklenmez.


> [!TIP]
> Bir SSD diskiniz varsa, üstün performans için devclustersetup.sh ile `--clusterdataroot` kullanarak bir SSD klasör yolu geçirmenizi öneririz.

## <a name="set-up-the-service-fabric-cli"></a>Service Fabric CLI’sını ayarlama

[Service Fabric CLI](service-fabric-cli.md) kümeler ve uygulamalar da dahil olmak üzere Service Fabric varlıklarıyla etkileşime yönelik komutlar içerir. CLI'yi yüklemek için, [Service Fabric CLI'si](service-fabric-cli.md) yönergelerini izleyin.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Kapsayıcılar ve konuk yürütülebilir dosyalar için Yeoman oluşturucularını ayarlama
Service Fabric, Yeoman şablon oluşturucuları kullanarak terminalden Service Fabric uygulamaları oluşturmanıza yardımcı olan yapı iskelesi araçları sağlar. Service Fabric Yeoman şablon oluşturucularını ayarlamak için bu adımları izleyin:

1. Makinenize nodejs ve npm’yi yükleyin.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Makinenize npm’den [Yeoman](https://yeoman.io/) şablon oluşturucuyu yükleyin.

    ```bash
    sudo npm install -g yo
    ```
3. npm’den Service Fabric Yeo kapsayıcı oluşturucusunu ve konuk yürütülebilir dosya oluşturucusunu yükleyin.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Oluşturucuları yükledikten sonra, sırasıyla `yo azuresfguest` ve `yo azuresfcontainer` komutlarını çalıştırarak konuk yürütülebilir dosyasını veya kapsayıcı hizmetlerini oluşturun.

## <a name="set-up-net-core-31-development"></a>.NET Core 3,1 geliştirmeyi ayarlama

[C# Service Fabric uygulamaları oluşturmaya](service-fabric-create-your-first-linux-application-with-csharp.md)başlamak Için [Ubuntu için .NET Core 3,1 SDK 'sını](/dotnet/core/install/linux-ubuntu) yükler. .NET Core Service Fabric uygulamaları için paketler NuGet.org üzerinde barındırılır.

## <a name="set-up-java-development"></a>Java ile geliştirmeyi ayarlama

Java kullanarak Service Fabric Hizmetleri derlemek için, derleme görevlerini çalıştırmak için Gradle ' yi kullanın. Gradle yüklemek için aşağıdaki komutu çalıştırın. Service Fabric Java kitaplıkları Maven’dan alınır.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (Service Fabric önizleme desteği)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Ayrıca Java yürütülebilir dosyaları için Service Fabric Yeo oluşturucusunu da yüklemelisiniz. [Yeoman'ı yüklediğinizden](#set-up-yeoman-generators-for-containers-and-guest-executables) emin olun ve ardından aşağıdaki komutu çalıştırın:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Eclipse eklentisini yükleme (isteğe bağlı)

Service Fabric için Eclipse eklentisini Java EE Geliştiricileri veya Java Geliştiricileri için Eclipse IDE içinden yükleyebilirsiniz. Eclipse kullanarak, Service Fabric Java uygulamalarına ek olarak Service Fabric konuk yürütülebilir uygulamaları ve kapsayıcı uygulamaları oluşturabilirsiniz.

> [!IMPORTANT]
> Service Fabric eklentisi için Eclipse Neon veya üzeri bir sürüm gerekir. Eclipse sürümünüzün nasıl denetleneceği hakkında bu nottan sonraki yönergelere bakın. Eclipse’in önceki bir sürümünü yüklediyseniz, [Eclipse sitesinden](https://www.eclipse.org) daha yeni sürümleri indirebilirsiniz. Mevcut bir Eclipse yüklemesinin üzerine yüklemenizi (üzerine yazmanızı) önermiyoruz. Yükleyiciyi çalıştırmadan önce bunu kaldırın veya yeni sürümü farklı bir dizine yükleyin.
> 
> Ubuntu üzerinde, paket yükleyici (`apt` veya `apt-get`) kullanmak yerine doğrudan Eclipse sitesinden yükleme yapılmasını öneririz. Böylece, Eclipse’in en güncel sürümünü elde etmeniz sağlanır. Java EE Geliştiricileri için veya Java Geliştiricileri için Eclipse IDE’yi yükleyebilirsiniz.

1. Eclipse’te, Eclipse Neon veya sonraki bir sürümünün ve Buildship 2.2.1 veya sonraki bir sürümünün yüklü olduğundan emin olun.   >  **Çakışan Küreler**  >  **Yükleme ayrıntıları** hakkında Yardım ' i seçerek yüklü bileşenlerin sürümlerini denetleyin. [Eclipse Buildship: Gradle için Eclipse eklentileri][buildship-update] bölümünde sağlanan yönergelerden yararlanarak Buildship’i güncelleştirebilirsiniz.

2. Service Fabric eklentisini yüklemek için **Yardım**  >  **yeni yazılım yüklemesi**' ni seçin.

3. **Birlikte çalış** kutusuna **https: \/ /DL.Microsoft.com/Eclipse** girin.

4. **Add (Ekle)** seçeneğini belirleyin.

    ![Kullanılabilir Yazılım sayfası][sf-eclipse-plugin]

5. **ServiceFabric** eklentisini ve ardından **İleri**’yi seçin.

6. Yükleme adımlarını gerçekleştirin. Ardından son kullanıcı lisans sözleşmesini kabul edin.

Service Fabric Eclipse eklentisi zaten yüklüyse, en yeni sürümü kullandığınızdan emin olun.   >  **Çakışan Küreler**  >  **Yükleme ayrıntıları** hakkında yardım seçerek denetleyin. Ardından, yüklü eklentiler listesinde Service Fabric aratın. Daha yeni bir sürüm varsa **Güncelleştir** ' i seçin.

Daha fazla bilgi için bkz. [Eclipse Java uygulama geliştirmesi için Service Fabric eklentisi](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>SDK ve çalışma zamanını güncelleştirme

SDK ve çalışma zamanının son sürümüne güncelleştirmek için aşağıdaki komutları çalıştırın.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Maven’dan alınan Java SDK'sı ikili dosyalarını güncelleştirmek için ``build.gradle`` dosyasında karşılık gelen ikili sürüm ayrıntılarını en son sürüme işaret edecek şekilde güncelleştirmeniz gerekir. Sürümü tam olarak nerede güncelleştirmeniz gerektiğini öğrenmek için [Service Fabric başlangıç örneklerindeki](https://github.com/Azure-Samples/service-fabric-java-getting-started) herhangi bir ``build.gradle`` dosyasına bakın.

> [!NOTE]
> Paketlerin güncelleştirilmesi, yerel geliştirme kümenizin çalışmayı durdurmasına neden olabilir. Yükseltme sonrasında bu makaledeki yönergeleri izleyerek yerel kümenizi yeniden başlatın.

## <a name="remove-the-sdk"></a>SDK'yı kaldırma
Service Fabric SDK'larını kaldırmak için aşağıdaki komutları çalıştırın.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (Service Fabric önizleme desteği)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Linux üzerinde Yeoman kullanarak ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-create-your-first-linux-application-with-java.md)
* [Linux üzerinde Eclipse için Service Fabric Eklentisi kullanarak ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-get-started-eclipse.md)
* [Linux üzerinde ilk CSharp uygulamanızı oluşturma](service-fabric-create-your-first-linux-application-with-csharp.md)
* [OSX üzerinde geliştirme ortamınızı hazırlama](service-fabric-get-started-mac.md)
* [Windows üzerinde Linux geliştirme ortamı hazırlama](service-fabric-local-linux-cluster-windows.md)
* [Uygulamalarınızı yönetmek için Service Fabric CLI'yı kullanma](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows ile Linux arasındaki farklar](service-fabric-linux-windows-differences.md)
* [Service Fabric CLI kullanmaya başlama](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
