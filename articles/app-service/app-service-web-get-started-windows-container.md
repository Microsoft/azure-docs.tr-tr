---
title: Özel Windows kapsayıcısını (Önizleme) Çalıştır-Azure App Service | Microsoft Docs
description: Azure App Service'e özel bir Windows kapsayıcısı dağıtmayı öğrenin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 230ff96aaf2c78827c7c4da92abe0f356cc2643e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241863"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Azure'da özel Windows kapsayıcısı çalıştırma (Önizleme)

[Azure App Service](overview.md), Windows'da IIS üzerinde çalışan ASP.NET veya Node.js gibi önceden tanımlı uygulama yığınları sunar. Önceden yapılandırılmış Windows ortamı, işletim sistemini yönetim erişimi, yazılım yüklemeleri, genel derleme önbelleğindeki değişiklikler vb. ile kilitler. Daha fazla bilgi için bkz. [Azure App Service işletim sistemi işlevselliği](operating-system-functionality.md). Uygulamanız önceden yapılandırılmış ortamın sunduğundan daha fazla erişime ihtiyaç duyuyorsa özel bir Windows kapsayıcısı dağıtabilirsiniz.

Bu hızlı başlangıçta, bir Windows görüntüsünde ASP.NET uygulamasının Visual Studio 'dan [Docker Hub](https://hub.docker.com/) 'a nasıl dağıtılacağı gösterilmektedir. Uygulamayı Azure App Service bir özel kapsayıcıda çalıştırırsınız.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- <a href="https://hub.docker.com/" target="_blank">Docker Hub hesabı için kaydolma</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker for Windows'u yükleyin</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Windows kapsayıcılarını çalıştırmak için Docker’a geçiş yapın</a>.
- **ASP.net ve Web geliştirme** ve **Azure geliştirme** Iş yükleriyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ' i yükledikten sonra. Visual Studio 2019 zaten yüklüyse:

    - **Güncelleştirmeler için** **Yardım** > denetimi ' ni seçerek en son güncelleştirmeleri Visual Studio 'ya yükler.
    - **Araçlar** > **Al araçlar ve Özellikler ' i**seçerek iş yüklerini Visual Studio 'da ekleyin.

## <a name="create-an-aspnet-web-app"></a>ASP.NET web uygulaması oluşturma

Aşağıdaki adımları izleyerek bir ASP.NET Web uygulaması oluşturun:

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur**' da, Için C# **ASP.NET Web uygulaması (.NET Framework)** bulun ve seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde, uygulamayı _Myfirstazurewebapp_olarak adlandırın ve ardından **Oluştur**' u seçin.

   ![Web uygulaması projenizi yapılandırma](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Azure’a herhangi bir türde ASP.NET web uygulaması dağıtabilirsiniz. Bu hızlı başlangıç için **MVC** şablonunu seçin.

1. **Docker desteği**' ni seçin ve kimlik doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığından emin olun. **Oluştur**’u seçin.

   ![ASP.NET Web uygulaması oluşturma](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. _Dockerfile_ dosyası otomatik olarak açılmazsa **Çözüm Gezgini**’nden açın.

1. [Desteklenen bir üst görüntüye](#use-a-different-parent-image)ihtiyacınız vardır. `FROM` satırını aşağıdaki kod ile değiştirerek üst görüntüyü değiştirin ve dosyayı kaydedin:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Web uygulamasını yerel olarak çalıştırmak için Visual Studio **menüsünden hata ayıklama** > **olmadan Başlat** ' ı seçin.

   ![Uygulamayı yerel olarak çalıştırma](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Docker Hub'da yayımlama

1. **Çözüm Gezgini**, **Myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.

1. **App Service** seçip **Yayımla**' yı seçin.

1. Bir **Yayımlama hedefi**seçin kısmında **Container Registry** ve **Docker Hub**' ı seçin ve ardından **Yayımla**' ya tıklayın.

   ![Projeye genel bakış sayfasından yayımlama](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Docker Hub hesabı kimlik bilgilerinizi girip **Kaydet**' i seçin.

   Dağıtımın tamamlanmasını bekleyin. **Yayımla** sayfası artık daha sonra kullanılacak depo adını gösterir.

   ![Projeye genel bakış sayfasından yayımlama](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Bu depo adını daha sonrası için kopyalayın.

## <a name="create-a-windows-container-app"></a>Windows kapsayıcı uygulaması oluşturma

1. [Azure Portal]( https://portal.azure.com) oturum açın.

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesini seçin.

1. Azure Marketi kaynakları listesinin üzerindeki arama kutusunda, **kapsayıcılar için Web App**araması yapın ve **Oluştur**' u seçin.

1. **Web uygulaması oluştur**bölümünde aboneliğinizi ve bir **kaynak grubunu**seçin. Gerekirse yeni bir kaynak grubu oluşturabilirsiniz.

1. *Win-Container-demo* gibi bir uygulama adı sağlayın ve **Işletim sistemi**için **Windows** 'u seçin. İleri **' yi seçin: Devam etmek** için Docker.

   ![Kapsayıcılar için Web App oluşturma](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. **Görüntü kaynağı**Için **Docker Hub** ' ı seçin ve **resim ve etiket**için [Docker Hub 'a yayınla](#publish-to-docker-hub)' da kopyaladığınız depo adını girin.

   ![Kapsayıcılar için Web App yapılandırma](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Web uygulamanız için [Azure Container Registry](/azure/container-registry/) gibi başka bir konumda veya başka özel bir depoda bulunan özel görüntünüz varsa bu adımda yapılandırabilirsiniz.

1. **Gözden geçir ' i ve oluştur** **' u seçin** ve ardından Azure 'un gerekli kaynakları oluşturmasını bekleyin.

## <a name="browse-to-the-container-app"></a>Kapsayıcı uygulamasına göz atma

Azure işlemi tamamlandığında bir bildirim kutusu görüntülenir.

![Dağıtım başarılı oldu](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. **Kaynağa git**'e tıklayın.

1. Bu kaynağa genel bakış bölümünde **URL**'nin yanındaki bağlantıyı izleyin.

Yeni bir tarayıcı sayfası aşağıdaki sayfada açılır:

![Windows kapsayıcı uygulaması başlatılıyor](media/app-service-web-get-started-windows-container/app-starting.png)

Birkaç dakika bekleyin ve varsayılan ASP.NET giriş sayfasını görene kadar tekrar deneyin:

![Windows kapsayıcı uygulaması çalışıyor](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Tebrikler!** Azure App Service'te ilk özel Windows kapsayıcınızı çalıştırıyorsunuz.

## <a name="see-container-start-up-logs"></a>Kapsayıcı başlangıç günlüklerini inceleme

Windows kapsayıcısının yüklenmesi biraz zaman alabilir. İlerleme durumunu görmek için aşağıdaki URL'ye gidin. *\<app_name>* yerine uygulamanızın adını yazın.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Akışı yapılan günlükler şuna benzer:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Yerel olarak güncelleştirme ve yeniden dağıtma

1. Visual Studio 'da, **Çözüm Gezgini**, **Görünümler** > **ana** > **Dizin. cshtml**dosyasını açın.

1. Üst kısımda `<div class="jumbotron">` HTML etiketini bulun ve tüm öğeyi aşağıdaki kodla değiştirin:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure 'a yeniden dağıtmak için, **Çözüm Gezgini** ' de **Myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.

1. Yayımlama sayfasında **Yayımla**’yı seçin ve yayımlama işleminin tamamlanmasını bekleyin.

1. App Service’in Docker Hub’dan yeni görüntüyü çekmesini istemek için uygulamayı yeniden başlatın. Portaldaki uygulama sayfasına geri dönerek **Yeniden Başlat** > **Evet**’e tıklayın.

   ![Azure’da web uygulamasını yeniden başlatma](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

Yeniden [Kapsayıcı uygulamasına göz atın](#browse-to-the-container-app). Web sayfasını yenilediğinizde, uygulama ilk olarak "Başlangıç" sayfasına geri dönmeli, birkaç dakika sonra ise güncellenmiş web sayfasını tekrar görüntülemelidir.

![Azure’da güncelleştirilmiş web uygulaması](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Farklı bir üst görüntü kullanma

Uygulamanızı çalıştırmak için farklı bir özel Docker görüntüsü kullanabilirsiniz. Ancak istediğiniz çerçeve için doğru [üst görüntüyü](https://docs.docker.com/develop/develop-images/baseimages/) seçmeniz gerekir:

- .NET Framework uygulamaları dağıtmak için Windows Server Core 2019 [uzun süreli bakım kanalı (LTSC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) sürümüne göre bir üst görüntü kullanın. 
- .NET Core uygulamaları dağıtmak için Windows Server nano 1809 [yarı yıllık bakım kanalı (sac)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) sürümüne göre bir üst görüntü kullanın. 

Uygulama başlatılırken üst görüntünün indirilmesi zaman alabilir. Ancak Azure App Service önbelleğinde bulunan aşağıdaki üst görüntülerden birini kullanarak başlangıç süresini kısaltabilirsiniz:

- [MCR.Microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4.7.2-windowsservercore-ltsc2019
- [MCR.Microsoft.com/Windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/): 1809-bu görüntü, Microsoft [ASP.NET Core](https://hub.docker.com/_microsoft-dotnet-cores-aspnet) Microsoft Windows nano Server görüntüleri genelinde kullanılan temel kapsayıcıdır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure'da Windows kapsayıcısına geçirme](app-service-web-tutorial-windows-containers-custom-fonts.md)
