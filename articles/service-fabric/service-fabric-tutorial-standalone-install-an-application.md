---
title: 'Öğretici: Tek başına Service Fabric kümenize uygulama yükleme - Azure Service Fabric | Microsoft Docs'
description: Bu öğreticide, tek başına Service Fabric kümenize bir uygulamanın nasıl yükleneceğini öğreneceksiniz.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 5e5260a2001d9cb0a38f4182e923a5416f76712b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384983"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Öğretici: Service Fabric tek başına kümenizde uygulama dağıtma

Service Fabric tek başına kümeleri, kendi ortamınızı seçme ve Service Fabric’in benimsediği "her işletim sistemi, her bulut" yaklaşımının bir parçası olarak bir küme oluşturma seçeneği sunar. Bu öğretici serisinde, AWS üzerinde barındırılan bir tek başına küme oluşturur ve bir uygulamayı buna dağıtırsınız.

Bu öğretici, bir serinin üçüncü bölümüdür.  Tek başına kümeler Service Fabric kendi ortamınızı seçme ve "tüm işletim sistemi, herhangi bir bulut" Service Fabric yaklaşımızın parçası olarak bir küme oluşturma seçeneğini sunar. Bu öğreticide, bu tek başına kümeyi barındırmak için gereken AWS altyapısını oluşturma işlemi gösterilmektedir.

Serinin üçüncü bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Örnek uygulamayı indirme
> * Kümeye dağıtma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* [Visual Studio 2019](https://www.visualstudio.com/) ' i yükleyip **Azure geliştirme** ve **ASP.net ve Web geliştirme** iş yüklerini yüklersiniz.
* [Service Fabric SDK'yı yükleyin](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Voting örnek uygulamasını indirme

Komut penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Uygulamayı Service Fabric kümesine dağıtma

Uygulama indirildikten sonra, doğrudan Visual Studio'dan bir kümeye dağıtabilirsiniz.

1. Visual Studio’yu açın

2. **Dosya** > **Aç**’ı seçin

3. Git deposunu kopyaladığınız klasöre gidin ve Voting.sln dosyasını seçin

4. Çözüm Gezgini'nde `Voting` uygulama projesine sağ tıklayın ve **Yayımla**’yı seçin

5. **Bağlantı Uç Noktası** açılır listesini seçin ve kümenizdeki düğümlerden birinin genel DNS Adını girin.  Örneğin: `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Azure 'da tam etki alanı adı (FQDN) otomatik olarak verilmediğini, ancak [VM 'ye Genel Bakış sayfasında](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn) kolayca ayarlanabildiğini unutmayın.

6. Tercih ettiğiniz tarayıcıyı açın ve küme adresini girin (bu uygulamanın 8080 numaralı bağlantı noktasında dağıttığı bağlantı uç noktası - örneğin, ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Kümeden API Yanıtı](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Sonraki adımlar

Serinin üçüncü bölümünde, kümenize bir uygulamanın nasıl dağıtılacağı öğrendiniz:

> [!div class="checklist"]
> * Örnek uygulamayı indirme
> * Kümeye dağıtma

Kümenizi temizlemek için serinin dördüncü bölümüne ilerleyin.

> [!div class="nextstepaction"]
> [Kaynaklarınızı temizleme](service-fabric-tutorial-standalone-clean-up.md)