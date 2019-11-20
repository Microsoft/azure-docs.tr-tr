---
title: Azure Dev Spaces ile AKS 'de Visual Studio ve .NET Core ile hata ayıklama ve yineleme
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 11/13/2019
ms.topic: quickstart
description: Azure’da kapsayıcılar ve mikro hizmetlerle hızlı Kubernetes geliştirme
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 5a7ab993c58730594a0c4e10572939d3dccbdf02
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091808"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-and-net-core-on-kubernetes-with-azure-dev-spaces"></a>Hızlı başlangıç: Azure Dev Spaces ile Kubernetes üzerinde Visual Studio ve .NET Core ile hata ayıklama ve yineleme

Bu kılavuzda şunların nasıl yapıldığını öğreneceksiniz:

- Azure’da yönetilen bir Kubernetes ile Azure Dev Spaces’ı ayarlayın.
- Visual Studio kullanarak kapsayıcılarda yinelemeli kod geliştirin.
- Visual Studio kullanarak kümenizde çalışan kodda hata ayıklayın.

Azure Dev Spaces ayrıca şunları kullanarak hata ayıklamanıza ve yinelemenize olanak tanır:
- [Java ve Visual Studio Code](quickstart-java.md)
- [Node. js ve Visual Studio Code](quickstart-nodejs.md)
- [.NET Core ve Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Hesabınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturabilirsiniz.
- Azure geliştirme iş yükü yüklü olan Windows üzerinde Visual Studio 2019. Ayrıca, Web geliştirme iş yükü ve yüklü [Kubernetes için Visual Studio Araçları](https://aka.ms/get-vsk8stools) Windows üzerinde Visual Studio 2017 ' i de kullanabilirsiniz. Visual Studio yüklü değilse, [buradan](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)indirin.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes hizmet kümesi oluşturma

[Desteklenen bir bölgede][supported-regions]aks kümesi oluşturmanız gerekir. Bir küme oluşturmak için:

1. [Azure portalda](https://portal.azure.com) oturum açma
1. *Kubernetes hizmeti > + kaynak oluştur*' u seçin. 
1. _Abonelik_, _kaynak grubu_, _Kubernetes kümesi adı_, _bölge_, _Kubernetes sürümü_ve _DNS adı ön eki_girin.

    ![Azure portal AKS oluşturma](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. *Gözden geçir ve oluştur*’a tıklayın.
1. *Oluştur*'a tıklayın.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS kümenizde Azure Dev Spaces etkinleştirme

Azure portal AKS kümenize gidin ve *geliştirme alanları*' na tıklayın. *Geliştirme alanlarını kullan* seçeneğini *Evet* olarak değiştirin ve *Kaydet*' e tıklayın.

![Azure portal dev alanlarını etkinleştirme](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Yeni bir ASP.NET Web uygulaması oluşturma

1. Visual Studio'yu açın.
1. Yeni bir proje oluşturma.
1. *ASP.NET Core Web uygulaması* ' nı seçin ve *İleri*' ye tıklayın.
1. Projenizin *Web ön uç* adını adlandırın ve *Oluştur*' a tıklayın.
1. İstendiğinde, şablon için *Web uygulaması (Model-View-Controller)* öğesini seçin.
1. En üstteki *.NET Core* ve *2,1 ASP.NET Core* seçin.
1. *Oluştur*'a tıklayın.

## <a name="connect-your-project-to-your-dev-space"></a>Projenizi geliştirme alanınıza bağlayın

Projenizde, aşağıda gösterildiği gibi başlatma ayarları açılır listesinden **Azure dev Spaces** ' yi seçin.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Azure Dev Spaces iletişim kutusunda, *aboneliğinizi* ve *Azure Kubernetes kümenizi*seçin. *Alanı* *varsayılan* olarak ayarlayın ve *herkese açık olarak erişilebilir* onay kutusunu etkinleştirin. *Tamam* düğmesine tıklayın.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Bu işlem, hizmetinizi genel olarak erişilebilen bir URL ile *varsayılan* geliştirme alanına dağıtır. Azure Dev Spaces ile çalışacak şekilde yapılandırılmamış bir küme seçerseniz, yapılandırmak isteyip istemediğinizi soran bir ileti görürsünüz. *Tamam* düğmesine tıklayın.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

*Varsayılan* dev alanında çalışan hizmetin genel URL 'si *Çıkış* penceresinde görüntülenir:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

Yukarıdaki örnekte, genel URL http://default.webfrontend.1234567890abcdef1234.eus.azds.io/. Hizmetinizin genel URL 'sine gidin ve geliştirme alanınızda çalışan hizmetle etkileşim kurun.

Bu işlem hizmetinize genel erişimi devre dışı bırakmış olabilir. Ortak erişimi etkinleştirmek için, [ *values. YAML*değerindeki giriş değerini][ingress-update]güncelleştirebilirsiniz.

## <a name="update-code"></a>Kodu güncelleştirme

Visual Studio hala geliştirme alanınıza bağlıysa Durdur düğmesine tıklayın. `Controllers/HomeController.cs` satır 20 ' sini değiştirin:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Değişiklikleri kaydedin ve başlatma ayarları açılır listesinden **Azure dev Spaces** kullanarak hizmetinizi başlatın. Hizmetinizin genel URL 'sini bir tarayıcıda açın ve *hakkında*' ya tıklayın. Güncelleştirilmiş iletinizin göründüğünü gözlemleyin.

Kod düzenlemeleri her yapıldığında yeni bir kapsayıcı görüntüsünü yeniden oluşturmak ve yeniden dağıtmak yerine, daha hızlı bir düzenleme/hata ayıklama döngüsü sağlamak üzere mevcut kapsayıcı içindeki kodu artımlı olarak yeniden derler Azure Dev Spaces.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Hata ayıklama için kesme noktaları ayarlama ve kullanma

Visual Studio hala geliştirme alanınıza bağlıysa Durdur düğmesine tıklayın. `Controllers/HomeController.cs` açın ve imlecinizi buraya yerleştirmek için 20. satırda bir yere tıklayın. Kesme noktası isabet *F9* ayarlamak Için, *Hata Ayıkla* ' ya tıklayın ve ardından *kesme noktasını değiştirin* Uygulamanızı geliştirme alanınızda hata ayıklama modunda başlatmak için *F5* tuşuna basın veya *Hata Ayıkla* ' ya tıkladıktan sonra *hata ayıklamayı başlatın*.

Hizmetinizi bir tarayıcıda açın ve hiçbir ileti görüntülenmediğini unutmayın. Visual Studio 'ya dönün ve 20. satırı vurgulanmıştır. Ayarladığınız kesme noktası, hizmeti 20. satırda duraklattı. Hizmeti sürdürmek için *F5* 'e basın veya *Hata Ayıkla* 'Ya tıkladıktan sonra *devam edin*. Tarayıcınıza geri dönün ve iletinin şimdi görüntülendiğini unutmayın.

Bir hata ayıklayıcı eklenmiş olarak Kubernetes 'de hizmetinizi çalıştırırken, çağrı yığını, yerel değişkenler ve özel durum bilgileri gibi bilgileri hata ayıklamaya yönelik tam erişime sahip olursunuz.

İmleci `Controllers/HomeController.cs` ve *F9*tuşuna basarak kesme noktasını kaldırın.

## <a name="clean-up-your-azure-resources"></a>Azure kaynaklarınızı Temizleme

Azure portal kaynak grubunuza gidin ve *kaynak grubunu sil*' e tıklayın. Alternatif olarak, [az aks Delete](/cli/azure/aks#az-aks-delete) komutunu kullanabilirsiniz:

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Birden çok kapsayıcı ve takım geliştirme ile çalışma](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
