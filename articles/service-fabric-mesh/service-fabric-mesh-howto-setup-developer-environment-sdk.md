---
title: Service Fabric ağ için Windows geliştirme ortamı ayarlama
description: Service Fabric Mesh uygulaması geliştirebilmek ve bunu Azure Service Fabric Mesh'e dağıtabilmek için Windows geliştirme ortamınızı ayarlayın.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625744"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Service Fabric Mesh uygulamalarını derlemek için Windows geliştirme ortamınızı ayarlayın

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Windows geliştirme makinenizde Azure Service Fabric kafes uygulamaları derlemek ve çalıştırmak için şunları yapmanız gerekir:

* Docker
* Visual Studio 2017 veya üzeri
* Service Fabric kafes çalışma zamanı
* Service Fabric kafes SDK 'Sı ve araçları.

Ve Windows 'un aşağıdaki sürümlerinden biri:

* Windows 10 (Enterprise, Professional veya eğitim) sürümleri 1709 (Fall Creators Update) veya 1803 (Windows 10 Nisan 2018 güncelleştirmesi)
* Windows Server sürüm 1709
* Windows Server sürüm 1803

Aşağıdaki yönergeler, çalıştırmakta olduğunuz Windows sürümüne bağlı olarak her şeyi almanıza yardımcı olur.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Service Fabric kafes uygulamalarını dağıtmak için Visual Studio 2017 veya sonraki bir sürümü gereklidir. [Sürüm 15.6.0'ı][download-visual-studio] veya üstünü yükleyin ve aşağıdaki iş yüklerini etkinleştirin:

* ASP.NET ve web geliştirme
* Azure Geliştirme

## <a name="install-docker"></a>Docker'ı yükleme

Zaten Docker yüklüyse, en son sürüme sahip olduğunuzdan emin olun. Docker, yeni bir sürüm olduğunda sizi uyarır, ancak en son sürüme sahip olduğunuzdan emin olmak için el ile kontrol edebilir.

#### <a name="install-docker-on-windows-10"></a>Windows 10 ' da Docker 'ı yükler

Service Fabric Mesh tarafından kullanılan kapsayıcılı Service Fabric uygulamalarını desteklemek için en yeni [Docker Community Edition for Windows][download-docker] sürümünü indirin ve yükleyin.

Yükleme sırasında, istendiğinde **Linux kapsayıcıları yerine Windows kapsayıcılarını kullan**'ı seçin.

Makinenizde Hyper-V etkinleştirilmemişse, Docker 'ın yükleyicisi uygulamayı etkinleştirir. Bu seçenek sunulursa **Tamam**'a tıklayın.

#### <a name="install-docker-on-windows-server-2016"></a>Windows Server 2016 ' de Docker 'ı yükler

Hyper-V rolleri etkin değilse PowerShell'i yönetici olarak açın ve aşağıdaki komutu çalıştırarak Hyper-V'yi etkinleştirdikten sonra bilgisayarınızı yeniden başlatın. Daha fazla bilgi için bkz. [Windows Server için Docker Enterprise Edition][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Bilgisayarınızı yeniden başlatın.

Docker'ı yüklemek için PowerShell'i yönetici olarak açın ve aşağıdaki komutu çalıştırın:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK ve araçlar

Aşağıdaki sırayla Service Fabric Mesh çalışma zamanını, SDK'yı ve araçları yükleyin.

1. Web Platformu Yükleyicisi'ni kullanarak [Service Fabric Mesh SDK'sını][download-sdkmesh] yükleyin. Bu işlem Microsoft Azure Service Fabric SDK’sını ve çalışma zamanını da yükler.
2. Visual Studio Market'ten [Visual Studio Service Fabric Mesh Araçları (önizleme) uzantısını][download-tools] yükleyin.

## <a name="build-a-cluster"></a>Küme oluşturma

> [!IMPORTANT]
> Küme oluşturabilmeniz için Docker'in çalışıyor olması **gerekir**.
> Terminal penceresi açarak ve hata oluşup oluşmadığını görmek için `docker ps` komutunu çalıştırarak Docker'ı çalışmasını test edin. Yanıt bir hata göstermiyorsa, Docker çalışıyor ve siz de küme oluşturmaya hazırsınız demektir.

> [!Note]
> Windows Fall Creators Update (sürüm 1709) makinesi üzerinde geliştiriyorsanız, yalnızca Windows sürümü 1709 Docker görüntülerini kullanabilirsiniz.
> Windows 10 Nisan 2018 Güncelleştirmesi (sürüm 1803) makinesinde geliştiriyorsanız, Windows sürüm 1709 veya 1803 Docker görüntülerini kullanabilirsiniz.

Visual Studio kullanıyorsanız, Visual Studio hesabınız yoksa sizin için yerel bir küme oluşturduğundan bu bölümü atlayabilirsiniz.

Tek bir Service Fabric uygulaması oluştururken ve çalıştırırken en iyi hata ayıklama performansı için tek düğümlü yerel bir geliştirme kümesi oluşturun. Aynı anda birden çok uygulama çalıştırıyorsanız, beş düğümlü bir yerel geliştirme kümesi oluşturun. Service Fabric bir kafes projesi dağıttığınızda veya hata ayıkladığınızda kümenin çalışıyor olması gerekir.

Çalışma zamanını, SDK 'Ları, Visual Studio araçlarını ve Docker 'ı yükledikten sonra bir geliştirme kümesi oluşturun.

1. PowerShell pencerenizi kapatın.
2. Yönetici olarak yeni, yükseltilmiş bir PowerShell penceresi açın. Yüklediğiniz en son yüklenen Service Fabric modüllerinin açılması için bu adım gereklidir.
3. Aşağıdaki PowerShell komutunu çalıştırarak geliştirme kümesini oluşturun:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Yerel küme yöneticisi aracını başlatmak için, aşağıdaki PowerShell komutunu çalıştırın:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Service Cluster Manager aracı çalışmaya başladıktan sonra (sistem tepsisinde görünür), sağ tıklayın ve **Yerel kümeyi Başlat**' a tıklayın.

![Şekil 1-yerel kümeyi başlatma](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Artık Service Fabric Mesh uygulamaları oluşturmaya hazırsınız!

## <a name="next-steps"></a>Sonraki adımlar

[Azure Service Fabric uygulaması oluşturma](service-fabric-mesh-tutorial-create-dotnetcore.md) öğreticisini okuyun.

[Yaygın soruların ve bilinen sorunların](service-fabric-mesh-faq.md)yanıtlarını bulun.

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
