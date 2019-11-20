---
title: Linux 'ta geliştirme ortamınızı ayarlama | Microsoft Docs
description: Çalışma zamanını ve SDK 'Yı yükleyip Linux 'ta yerel bir geliştirme kümesi oluşturun. Bu kurulumu tamamladıktan sonra, uygulama oluşturmaya hazırlanın.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 6471d3f58fbe0e19910f39e9527f193d00a228be
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170614"
---
# <a name="prepare-your-development-environment-on-linux"></a>Linux üzerinde geliştirme ortamınızı hazırlama
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

[Azure Service Fabric uygulamalarını](service-fabric-application-model.md) Linux geliştirme makinenizde dağıtmak ve çalıştırmak için çalışma zamanını ve ortak SDK 'yı yüklemelisiniz. Java ve .NET Core geliştirmesi için isteğe bağlı SDK 'Ları da yükleyebilirsiniz. 

Bu makaledeki adımlarda, Linux 'ta yerel olarak yüklediğiniz veya Service Fabric OneBox kapsayıcı görüntüsünü `microsoft/service-fabric-onebox` ' ı kullandığınızda varsayılmaktadır.

Linux için Windows alt sistemine Service Fabric çalışma zamanının ve SDK 'nın yüklenmesi desteklenmez. Bulutta veya şirket içinde barındırılan Service Fabric varlıklarını, desteklenen Azure Service Fabric komut satırı arabirimi (CLı) ile yönetebilirsiniz. CLı 'yı kurma hakkında daha fazla bilgi için bkz. [SERVICE fabrıc CLI 'Yi ayarlama](./service-fabric-cli.md).


## <a name="prerequisites"></a>Önkoşullar

Bu işletim sistemi sürümleri geliştirme için desteklenir.

* Ubuntu 16,04 (`Xenial Xerus`)

    @No__t-0 paketinin yüklü olduğundan emin olun.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7,4 (Service Fabric Önizleme desteği)


## <a name="installation-methods"></a>Yükleme yöntemleri

### <a name="script-installation-ubuntu"></a>Betik yüklemesi (Ubuntu)

Kolaylık sağlaması için, Service Fabric çalışma zamanını ve Service Fabric ortak SDK 'sını, **sfctl** CLI ile birlikte yüklemek için bir komut dosyası sağlanır. Sonraki bölümde el ile yükleme adımlarını çalıştırın. Nelerin yüklü olduğunu ve ilişkili lisansları görürsünüz. Betiği çalıştırmak, yüklenmekte olan tüm yazılımların lisanslarını kabul etmiş olduğunuzu varsayar.

Betik başarıyla çalıştıktan sonra, [yerel bir küme ayarlamayı](#set-up-a-local-cluster)atlayabilirsiniz.

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="manual-installation"></a>El ile yükleme
Service Fabric çalışma zamanı ve ortak SDK 'Yı el ile yüklemek için bu kılavuzun geri kalanını izleyin.

## <a name="update-your-apt-sources-or-yum-repositories"></a>APT kaynaklarınızı veya Taum depolarınızı güncelleştirin
SDK ve ilişkili çalışma zamanı paketini apt-get komut satırı aracını kullanarak yüklemek için, önce Gelişmiş paketleme aracı (APT) kaynaklarınızı güncelleştirmeniz gerekir.

### <a name="ubuntu"></a>Ubuntu

1. Bir Terminal açın.
2. Kaynak listenize Service Fabric depoyu ekleyin.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Kaynak listenize `dotnet` deposu ekleyin.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

4. Yeni GNU gizlilik koruyucusu (GnuPG veya GPG) anahtarını APT kimlik anahtarlığı ' e ekleyin.

    ```bash
    curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

5. APT kimlik anahtarlığı 'e resmi Docker GPG anahtarını ekleyin.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Docker deposunu ayarlayın.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. APT kimlik anahtarlığı 'e Azul JDK anahtarını ekleyin ve deposunu ayarlayın.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

8. Yeni eklenen depolara göre paket listelerinizi yenileyin.

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7,4 (Service Fabric Önizleme desteği)

1. Bir Terminal açın.
2. Enterprise Linux (EPEL) için ek paketler indirin ve yükleyin.

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. EfficiOS RHEL7 Package deposunu sisteminize ekleyin.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. EfficiOS paketi imzalama anahtarını yerel GPG kimlik anahtarlığı 'ye aktarın.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Microsoft RHEL deposunu sisteminize ekleyin.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. .NET SDK 'Yı yükler.

    ```bash
    yum install rh-dotnet20 -y
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Yerel bir küme için Service Fabric SDK 'Yı yükleyip kurma

Kaynaklarınızı güncelleştirdikten sonra SDK 'Yı yükleyebilirsiniz. Service Fabric SDK paketini yükleyip yüklemeyi onaylayın ve lisans sözleşmesini kabul edin.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Aşağıdaki komutlar Service Fabric paketleri için lisans kabul etmeyi otomatik hale getirir:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7,4 (Service Fabric Önizleme desteği)

```bash
sudo yum install servicefabricsdkcommon
```

SDK yüklemesiyle birlikte gelen Service Fabric çalışma zamanı, aşağıdaki tabloda yer alan paketleri içerir. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | AzulJDK 1,8 | NPM 'den örtük | sürümü |
RHEL for | - | OpenJDK 1,8 | NPM 'den örtük | sürümü |

## <a name="set-up-a-local-cluster"></a>Yerel küme ayarlama
Yükleme bittikten sonra yerel bir küme başlatın.

1. Küme kurulum betiğini çalıştırın.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Bir Web tarayıcısı açın ve [Service Fabric Explorer](http://localhost:19080/Explorer) gidin (`http://localhost:19080/Explorer`). Küme başladığında Service Fabric Explorer panosunu görürsünüz. Kümenin tamamen ayarlanması birkaç dakika sürebilir. Tarayıcınız URL 'YI açamazsa veya Service Fabric Explorer sistemin hazırlanmaya yaramazsa, birkaç dakika bekleyip yeniden deneyin.

    ![Linux üzerinde Service Fabric Explorer][sfx-linux]

    Artık önceden oluşturulmuş Service Fabric uygulama paketlerini veya Konuk kapsayıcılarına veya Konuk yürütülebilir dosyalara göre yenilerini dağıtabilirsiniz. Java veya .NET Core SDK 'larını kullanarak yeni hizmetler oluşturmak için, sonraki bölümlerde sunulan isteğe bağlı kurulum adımlarını izleyin.


> [!NOTE]
> Tek başına kümeler Linux 'ta desteklenmez.


> [!TIP]
> Kullanılabilir bir SSD disk varsa, üst düzey performans için devclustersetup.sh ile `--clusterdataroot` kullanarak bir SSD klasör yolu geçirmeniz önerilir.

## <a name="set-up-the-service-fabric-cli"></a>Service Fabric CLı 'yı ayarlama

[SERVICE fabrıc CLI](service-fabric-cli.md) , kümeler ve uygulamalar da dahil olmak üzere Service Fabric varlıklarla etkileşim kurma komutlarına sahiptir. CLı 'yı yüklemek için [SERVICE fabrıc CLI](service-fabric-cli.md)' deki yönergeleri izleyin.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Kapsayıcılar ve konuk yürütülebilir dosyalar için Yeumman üreteçleri ayarlama
Service Fabric Yeumman şablon oluşturucuları kullanarak terminalden Service Fabric uygulamaları oluşturmanıza yardımcı olan yapı iskelesi araçları sağlar. Service Fabric Yeumman şablon oluşturucularını ayarlamak için şu adımları izleyin:

1. Makinenizde Node. js ve NPM 'yi yükler.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Makinenizde NPM 'den [Yeumman](https://yeoman.io/) şablon oluşturucusunu yükler.

    ```bash
    sudo npm install -g yo
    ```
3. NPM 'den Service Fabric Yeo kapsayıcı Oluşturucu ve konuk yürütülebilir oluşturucusunu yükler.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Oluşturucuları yükledikten sonra, sırasıyla `yo azuresfguest` veya `yo azuresfcontainer` ' i çalıştırarak Konuk yürütülebilir dosyası veya kapsayıcı Hizmetleri oluşturun.

## <a name="set-up-net-core-20-development"></a>.NET Core 2,0 geliştirmeyi ayarlama

[Service Fabric uygulamalar oluşturmaya C# ](service-fabric-create-your-first-linux-application-with-csharp.md)başlamak için [Ubuntu IÇIN .NET Core 2,0 SDK 'sını](https://www.microsoft.com/net/core#linuxubuntu) yükler. NuGet.org, şu anda önizleme aşamasında olan .NET Core 2,0 Service Fabric uygulamaları için paketleri barındırır.

## <a name="set-up-java-development"></a>Java geliştirmeyi ayarlama

Java kullanarak Service Fabric Hizmetleri derlemek için, derleme görevlerini çalıştırmak için Gradle ' yi kullanın. Gradle yüklemek için aşağıdaki komutu çalıştırın. Service Fabric Java kitaplıkları Maven 'den çekilir.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7,4 (Service Fabric Önizleme desteği)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Java yürütülebilirleri için Service Fabric Yeo oluşturucuyu yüklemeniz gerekir. [Yeumman yüklü](#set-up-yeoman-generators-for-containers-and-guest-executables)olduğundan emin olun ve ardından aşağıdaki komutu çalıştırın:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Tutulma eklentisini (isteğe bağlı) yükler

Java geliştiricileri veya Java EE geliştiricileri için, tutulma IDE 'nin içinden Service Fabric için tutulma eklentisini yükleyebilirsiniz. Service Fabric Java uygulamalarına ek olarak, Service Fabric Konuk yürütülebilir uygulamalar ve kapsayıcı uygulamalar oluşturmak için tutulma kullanabilirsiniz.

> [!IMPORTANT]
> Service Fabric eklentisi, tutulma Neon veya sonraki bir sürümü gerektirir. Çakışan Küreler sürümünüzü nasıl denetleyeceğinizi öğrenmek için bu NotDan izlenecek yönergelere bakın. Çakışan bir tutulma sürümü yüklüyse, [tutulma sitesinden](https://www.eclipse.org)daha yeni sürümleri indirebilirsiniz. Var olan bir tutulma yüklemesinin üzerine (üzerine yazma) yüklememeyi öneririz. Yükleyiciyi çalıştırmadan önce kaldırın ya da daha yeni sürümü farklı bir dizine yükleyemezsiniz.
> 
> Ubuntu 'da, bir paket yükleyicisi (`apt` veya `apt-get`) kullanmak yerine, doğrudan tutulma sitesinden yüklemenizi öneririz. Bunun yapılması, tutulma 'nın en güncel sürümünü almanızı sağlar. Java geliştiricileri için veya Java EE geliştiricileri için tutulma IDE 'yi yükleyebilirsiniz.

1. Çakışan Küreler, Neon veya üzeri ve Buildsevkiyat sürüm 2.2.1 veya sonraki sürümlerini yüklediğinizden emin olun. @No__t,  > **Yükleme ayrıntıları**hakkında **Yardım**-1' i seçerek yüklü bileşenlerin sürümlerini denetleyin. [Gradle için, çakışan küreler buildby: tutulma Eklentilerindeki][buildship-update]yönergeleri kullanarak buildsevkiyat ' i güncelleştirebilirsiniz.

2. Service Fabric eklentisini yüklemek için, **yardım** > **yeni yazılım yüklemesi**' ni seçin.

3. **Birlikte çalış** kutusuna **https://dl.microsoft.com/eclipse** yazın.

4. **Ekle**’yi seçin.

    ![Kullanılabilir yazılım sayfası][sf-eclipse-plugin]

5. **Servicefabric** eklentisini seçin ve ardından **İleri**' yi seçin.

6. Yükleme adımlarını gerçekleştirin. Ardından Son Kullanıcı Lisans sözleşmesini kabul edin.

Service Fabric Tutulma eklentisi zaten yüklüyse, en son sürüme sahip olduğunuzdan emin olun. @No__t,  > **yükleme ayrıntısı** **hakkında** **Yardım**-1 ' i seçerek denetleyin. Ardından, yüklü eklentiler listesinde Service Fabric arayın. daha yeni bir sürüm varsa **Güncelleştir** ' i seçin.

Daha fazla bilgi için bkz. [tutulma Java uygulama geliştirmesi için Service Fabric eklentisi](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>SDK ve çalışma zamanını güncelleştirme

SDK ve çalışma zamanının son sürümüne güncelleştirmek için aşağıdaki komutları çalıştırın.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Maven 'den Java SDK ikililerini güncelleştirmek için, ``build.gradle`` dosyasında karşılık gelen ikilinin sürüm ayrıntılarını en son sürümü gösterecek şekilde güncelleştirmeniz gerekir. Sürümü tam olarak nerede güncelleştirmeniz gerektiğini bilmek için, [Service Fabric Başlarken örneklerinde](https://github.com/Azure-Samples/service-fabric-java-getting-started)``build.gradle`` dosyasına bakın.

> [!NOTE]
> Paketlerin güncelleştirilmesi, yerel geliştirme kümenizin çalışmayı durdurmasına neden olabilir. Bu makaledeki yönergeleri izleyerek bir yükseltmeden sonra yerel kümenizi yeniden başlatın.

## <a name="remove-the-sdk"></a>SDK 'Yı kaldırma
Service Fabric SDK 'Ları kaldırmak için aşağıdaki komutları çalıştırın.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7,4 (Service Fabric Önizleme desteği)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Yeumman kullanarak Linux üzerinde ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-create-your-first-linux-application-with-java.md)
* [Service Fabric Çakışan Küreler Service Fabric eklentisini kullanarak Linux üzerinde ilk Java uygulamanızı oluşturma ve dağıtma](service-fabric-get-started-eclipse.md)
* [Linux üzerinde ilk CSharp uygulamanızı oluşturma](service-fabric-create-your-first-linux-application-with-csharp.md)
* [OSX üzerinde geliştirme ortamınızı hazırlama](service-fabric-get-started-mac.md)
* [Windows 'da Linux geliştirme ortamı hazırlama](service-fabric-local-linux-cluster-windows.md)
* [Service Fabric CLı kullanarak uygulamalarınızı yönetin](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows ve Linux farklılıkları](service-fabric-linux-windows-differences.md)
* [Service Fabric CLı ile çalışmaya başlama](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
