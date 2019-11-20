---
title: Service Fabric ve VS ile Windows kapsayıcılarında hata ayıklama | Microsoft Docs
description: Visual Studio 2019 kullanarak Azure Service Fabric Windows kapsayıcılarındaki hataları nasıl ayıklayacağınızı öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: a5ccf527850e1c05c5d7e273ada905d65d64cee4
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073953"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Nasıl yapılır: Visual Studio 2019 kullanarak Azure Service Fabric 'da Windows kapsayıcılarında hata ayıklama

Visual Studio 2019 ile kapsayıcılardaki .NET uygulamalarında Service Fabric Hizmetleri olarak hata ayıklaması yapabilirsiniz. Bu makalede, ortamınızı nasıl yapılandıracağınız ve ardından yerel bir Service Fabric kümesinde çalışan bir kapsayıcıda bir .NET uygulamasında hata ayıklamanın nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

* Windows 10 ' da Windows 10 ' u Windows [kapsayıcıları çalıştıracak şekilde yapılandırmak](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10) için bu hızlı başlangıcı izleyin
* Windows Server 2016 ' de, Windows 2016 ' yi [Windows kapsayıcıları çalıştıracak şekilde yapılandırmak](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) için bu hızlı başlangıcı izleyin
* [Windows üzerinde geliştirme ortamınızı hazırlama '](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) yı izleyerek Yerel Service Fabric ortamınızı ayarlama

## <a name="configure-your-developer-environment-to-debug-containers"></a>Geliştirici ortamınızı kapsayıcılara hata ayıklama için yapılandırma

1. Sonraki adımla devam etmeden önce Window hizmetinin Docker 'ın çalıştığından emin olun.

1. Kapsayıcılar arasındaki DNS çözümlemesini desteklemek için, makine adını kullanarak yerel geliştirme kümenizi ayarlamanız gerekir. Bu adımlar, Hizmetleri ters proxy üzerinden ele almak istiyorsanız da gereklidir.
   1. PowerShell 'i yönetici olarak aç
   2. Genellikle `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`SDK küme kurulum klasörüne gidin.
   3. Betiği Çalıştır`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Tek düğümlü bir küme `-CreateOneNodeCluster` kurmak için öğesini kullanabilirsiniz. Varsayılan olarak yerel bir beş düğümlü küme oluşturulur.
      >

      Service Fabric 'deki DNS hizmeti hakkında daha fazla bilgi edinmek için bkz. [Azure Service Fabric 'de DNS hizmeti](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Bir kapsayıcıda çalışan hizmetlerden Service Fabric ters proxy kullanma hakkında daha fazla bilgi edinmek için bkz. [kapsayıcılar üzerinde çalışan hizmetler Için ters proxy özel işleme](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Service Fabric kapsayıcılarında hata ayıklama sırasında bilinen sınırlamalar

Service Fabric ve olası çözünürlüklere yönelik hata ayıklama kapsayıcılarıyla ilgili bilinen kısıtlamaların listesi aşağıda verilmiştir:

* ClusterFQDNorIP için localhost kullanılması, kapsayıcılardaki DNS çözümlemesini desteklemez.
    * Çözüm: Makine adını kullanarak yerel kümeyi ayarlayın (yukarıya bakın)
* Windows10 çalıştıran bir sanal makinede DNS yanıtı kapsayıcıya geri alınamaz.
    * Çözüm: Sanal makineler NIC 'inde IPv4 için UDP sağlama boşaltmasını devre dışı bırak
    * Çalışan Windows10, makinede ağ performansını düşürür.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Uygulama Docker Compose kullanılarak dağıtılmışsa, DNS hizmeti adı kullanılarak aynı uygulamadaki hizmetlerin çözümlenmesi Windows10 üzerinde çalışmaz.
    * Çözüm: Hizmet uç noktalarını çözümlemek için ServiceName. ApplicationName kullanın
    * https://github.com/Azure/service-fabric-issues/issues/1062
* ClusterFQDNorIP için IP adresi kullanılıyorsa, konaktaki birincil IP 'yi değiştirmek DNS işlevini keser.
    * Çözüm: Konakta yeni birincil IP 'yi kullanarak kümeyi yeniden oluşturun veya makine adını kullanın. Bu ayırıcıya tasarım.
* Kümenin oluşturulduğu FQDN ağda çözümlenememişse, DNS başarısız olur.
    * Çözüm: Yerel kümeyi konağın birincil IP 'sini kullanarak yeniden oluşturun. Bu hata tasarıma göre yapılır.
* Bir kapsayıcıda hata ayıklarken Docker günlükleri yalnızca Visual Studio çıktı penceresinde kullanılabilir, Service Fabric Explorer dahil Service Fabric API 'Leri üzerinden kullanılamaz

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Service Fabric Docker kapsayıcılarında çalışan bir .NET uygulamasında hata ayıklama

1. Visual Studio 'Yu yönetici olarak çalıştırın.

1. Mevcut bir .NET uygulamasını açın veya yeni bir tane oluşturun.

1. Projeye sağ tıklayın ve **Ekle-> kapsayıcı Orchestrator desteği->** ' ı seçin Service Fabric

1. Uygulamada hata ayıklamayı başlatmak için **F5** 'e basın.

    Visual Studio, .NET ve .NET Core için konsol ve ASP.NET proje türlerini destekler.

## <a name="next-steps"></a>Sonraki adımlar
Service Fabric ve kapsayıcıların özellikleri hakkında daha fazla bilgi edinmek için bkz. [Service Fabric kapsayıcılara genel bakış](service-fabric-containers-overview.md).
