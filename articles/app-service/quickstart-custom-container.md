---
title: 'Hızlı başlangıç: App Service özel kapsayıcı çalıştırma'
description: İlk özel kapsayıcınızı dağıtarak Azure App Service kapsayıcılar ile çalışmaya başlayın.
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 360da015f012822593dbb6390cb7df0017ba85b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745086"
---
# <a name="run-a-custom-container-in-azure"></a>Azure 'da özel kapsayıcı çalıştırma

::: zone pivot="container-windows"
[Azure App Service](overview.md), Windows'da IIS üzerinde çalışan ASP.NET veya Node.js gibi önceden tanımlı uygulama yığınları sunar. Önceden yapılandırılmış Windows kapsayıcı ortamı, işletim sistemini yönetim erişimi, yazılım yüklemeleri, genel derleme önbelleğindeki değişiklikler vb. tarafından kilitlenir. Daha fazla bilgi için bkz. [Azure App Service işletim sistemi işlevselliği](operating-system-functionality.md). Uygulamanız önceden yapılandırılmış ortamın sunduğundan daha fazla erişime ihtiyaç duyuyorsa özel bir Windows kapsayıcısı dağıtabilirsiniz.

Bu hızlı başlangıçta, bir Windows görüntüsünde ASP.NET uygulamasının Visual Studio 'dan [Docker Hub](https://hub.docker.com/) 'a nasıl dağıtılacağı gösterilmektedir. Uygulamayı Azure App Service bir özel kapsayıcıda çalıştırırsınız.

> [!NOTE]
> Windows kapsayıcıları Azure dosyaları ile sınırlıdır ve şu anda Azure Blob 'U desteklememektedir.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- <a href="https://hub.docker.com/" target="_blank">Docker Hub hesabı için kaydolma</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker for Windows'u yükleyin</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Windows kapsayıcılarını çalıştırmak için Docker’a geçiş yapın</a>.
- **ASP.net ve Web geliştirme** ve **Azure geliştirme** Iş yükleriyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ' i yükledikten sonra. Visual Studio 2019 zaten yüklüyse:

    -   >  **Güncelleştirmeler için yardım denetimi**' ni seçerek en son güncelleştirmeleri Visual Studio 'ya yükler.
    - **Araçlar**  >  **Al araçlar ve Özellikler ' i** seçerek iş yüklerini Visual Studio 'da ekleyin.

## <a name="create-an-aspnet-web-app"></a>ASP.NET web uygulaması oluşturma

Aşağıdaki adımları izleyerek bir ASP.NET Web uygulaması oluşturun:

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur** bölümünde C# için **ASP.NET Web uygulaması (.NET Framework)** bulun ve seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın** bölümünde, uygulamayı _myfirstazurewebapp_ olarak adlandırın ve ardından **Oluştur**' u seçin.

   ![Web uygulaması projenizi yapılandırma](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Azure’a herhangi bir türde ASP.NET web uygulaması dağıtabilirsiniz. Bu hızlı başlangıç için **MVC** şablonunu seçin.

1. **Docker desteği**' ni seçin ve kimlik doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığından emin olun. **Oluştur**’u seçin.

   ![ASP.NET Web uygulaması oluşturma](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. _Dockerfile_ dosyası otomatik olarak açılmazsa **Çözüm Gezgini**’nden açın.

1. [Desteklenen bir üst görüntüye](configure-custom-container.md#supported-parent-images)ihtiyacınız vardır. `FROM` satırını aşağıdaki kod ile değiştirerek üst görüntüyü değiştirin ve dosyayı kaydedin:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1.   >  Web uygulamasını yerel olarak çalıştırmak için Visual Studio menüsünden hata ayıklama **olmadan Başlat** ' ı seçin.

   ![Uygulamayı yerel olarak çalıştırma](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Docker Hub'da yayımlama

1. **Çözüm Gezgini**, **myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.

1. **App Service** seçip **Yayımla**' yı seçin.

1. Bir **Yayımlama hedefi** seçin kısmında **Container Registry** ve **Docker Hub**' ı seçin ve ardından **Yayımla**' ya tıklayın.

   ![Projeye genel bakış sayfasından yayımlama](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Docker Hub hesabı kimlik bilgilerinizi girip **Kaydet**' i seçin.

   Dağıtımın tamamlanmasını bekleyin. **Yayımla** sayfası artık daha sonra kullanılacak depo adını gösterir.

   ![Depo adını vurgulayan ekran görüntüsü.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Bu depo adını daha sonrası için kopyalayın.

## <a name="create-a-windows-container-app"></a>Windows kapsayıcı uygulaması oluşturma

1. [Azure portalında]( https://portal.azure.com) oturum açın.

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesini seçin.

1. Azure Marketi kaynakları listesinin üzerindeki arama kutusunda, **kapsayıcılar için Web App** araması yapın ve **Oluştur**' u seçin.

1. **Web uygulaması oluştur** bölümünde aboneliğinizi ve bir **kaynak grubunu** seçin. Gerekirse yeni bir kaynak grubu oluşturabilirsiniz.

1. *Win-Container-demo* gibi bir uygulama adı sağlayın ve **Işletim sistemi** için **Windows** 'u seçin. Ileri ' yi seçin: devam etmek için **Docker** .

   ![Kapsayıcılar için Web App oluşturma](media/quickstart-custom-container/create-web-app-continer.png)

1. **Görüntü kaynağı** Için **Docker Hub** ' ı seçin ve **resim ve etiket** için [Docker Hub 'a yayınla](#publish-to-docker-hub)' da kopyaladığınız depo adını girin.

   ![Kapsayıcılar için Web App yapılandırma](media/quickstart-custom-container/configure-web-app-continer.png)

    Web uygulamanız için [Azure Container Registry](../container-registry/index.yml) gibi başka bir konumda veya başka özel bir depoda bulunan özel görüntünüz varsa bu adımda yapılandırabilirsiniz.

1. **Gözden geçir ' i ve oluştur** **' u seçin** ve ardından Azure 'un gerekli kaynakları oluşturmasını bekleyin.

## <a name="browse-to-the-container-app"></a>Kapsayıcı uygulamasına göz atma

Azure işlemi tamamlandığında bir bildirim kutusu görüntülenir.

![Dağıtım başarılı oldu](media/quickstart-custom-container/portal-create-finished.png)

1. **Kaynağa git**'e tıklayın.

1. Bu kaynağa genel bakış bölümünde **URL**'nin yanındaki bağlantıyı izleyin.

Yeni bir tarayıcı sayfası aşağıdaki sayfada açılır:

![Windows kapsayıcı uygulaması başlatılıyor](media/quickstart-custom-container/app-starting.png)

Birkaç dakika bekleyin ve varsayılan ASP.NET giriş sayfasını görene kadar tekrar deneyin:

![Windows kapsayıcı uygulaması çalışıyor](media/quickstart-custom-container/app-running-vs.png)

**Tebrikler!** Azure App Service'te ilk özel Windows kapsayıcınızı çalıştırıyorsunuz.

## <a name="see-container-start-up-logs"></a>Kapsayıcı başlangıç günlüklerini inceleme

Windows kapsayıcısının yüklenmesi biraz zaman alabilir. İlerleme durumunu görmek için uygulamanızın adıyla değiştirerek aşağıdaki URL 'ye gidin *\<app_name>* .
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

1. Visual Studio 'da, **Çözüm Gezgini**, **Görünümler**  >  **ana**  >  **Dizin. cshtml** dosyasını açın.

1. Üst kısımda `<div class="jumbotron">` HTML etiketini bulun ve tüm öğeyi aşağıdaki kodla değiştirin:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure 'a yeniden dağıtmak için, **Çözüm Gezgini** ' de **myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.

1. Yayımlama sayfasında **Yayımla**’yı seçin ve yayımlama işleminin tamamlanmasını bekleyin.

1. App Service’in Docker Hub’dan yeni görüntüyü çekmesini istemek için uygulamayı yeniden başlatın. Portalda uygulama sayfasına dönün, Evet ' **e tıklayın**  >  .

   ![Azure’da web uygulamasını yeniden başlatma](./media/quickstart-custom-container/portal-restart-app.png)

Yeniden [Kapsayıcı uygulamasına göz atın](#browse-to-the-container-app). Web sayfasını yenilediğinizde, uygulama ilk olarak "Başlangıç" sayfasına geri dönmeli, birkaç dakika sonra ise güncellenmiş web sayfasını tekrar görüntülemelidir.

![Azure’da güncelleştirilmiş web uygulaması](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure'da Windows kapsayıcısına geçirme](tutorial-custom-container.md)

Ya da diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [Özel kapsayıcı yapılandırma](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
Linux üzerinde App Service, Linux üzerinde .NET, PHP, Node.js ve diğerleri gibi diller desteğiyle önceden tanımlanmış uygulama yığınları sağlar. Ayrıca web uygulamanızı Azure’da zaten tanımlı olmayan bir uygulama yığınında çalıştırmak için özel bir Docker görüntüsü de kullanabilirsiniz. Bu hızlı başlangıçta, bir [Azure Container Registry](../container-registry/index.yml) (ACR) App Service bir görüntünün nasıl dağıtılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure hesabı](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Vs Code için Azure App Service uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Bu uzantıyı Azure platformunda bir hizmet olarak (PaaS) Linux Web Apps oluşturmak, yönetmek ve dağıtmak için kullanabilirsiniz.
* [Vs Code Için Docker uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). Bu uzantıyı, yerel Docker görüntüleri ve komutlarının yönetimini basitleştirmek ve oluşturulan uygulama görüntülerini Azure 'a dağıtmak için kullanabilirsiniz.

## <a name="create-an-image"></a>Görüntü oluşturma

Bu hızlı başlangıcı tamamlayabilmeniz için, [Azure Container Registry](../container-registry/index.yml)depolanan uygun bir Web uygulaması görüntüsüne ihtiyacınız olacaktır. [Hızlı başlangıç: Azure Portal kullanarak özel kapsayıcı kayıt defteri oluşturma](../container-registry/container-registry-get-started-portal.md)' daki yönergeleri izleyin, ancak görüntü `mcr.microsoft.com/azuredocs/go` yerine görüntüyü kullanın `hello-world` . Başvuru için, [Azure örnekleri deposunda örnek Dockerfile bulunur](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Kapsayıcı kayıt defterini oluştururken **Yönetici Kullanıcı** seçeneğini **etkinleştirmek** için ayarladığınızdan emin olun. Ayrıca, Azure portal kayıt defteri sayfanızın **erişim tuşları** bölümünden de ayarlayabilirsiniz. App Service erişim için bu ayar gereklidir.

## <a name="sign-in"></a>Oturum açın

Sonra, VS Code başlatın ve App Service uzantısını kullanarak Azure hesabınızda oturum açın. Bunu yapmak için etkinlik çubuğunda Azure logosu ' nu seçin, **App SERVICE** Gezgini ' ne gidin ve **Azure 'da oturum aç** ' ı seçin ve yönergeleri izleyin.

![Azure 'da oturum açın](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Önkoşulları denetleme

Artık tüm önkoşulların doğru yüklenip yapılandırılmadığını kontrol edebilirsiniz.

VS Code, Azure e-posta adresinizi durum çubuğunda ve aboneliğiniz için **App SERVICE** Explorer 'da görmeniz gerekir.

Ardından, Docker 'ın yüklü ve çalışır olduğunu doğrulayın. Aşağıdaki komut, çalışıyorsa Docker sürümünü görüntüler.

```bash
docker --version
```

Son olarak, Azure Container Registry bağlı olduğundan emin olun. Bunu yapmak için etkinlik çubuğunda Docker logosunu seçin ve ardından **kayıt defterleri**' ne gidin.

![Ekran görüntüsünde, Azure genişletilmiş kayıt defterleri değeri ve nokta ı o dosya adı uzantısı olan bir dosya gösterilmektedir.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Görüntüyü Azure App Service dağıtma

Artık her şey yapılandırıldığına göre, görüntünüzü doğrudan Docker uzantı Gezgini 'nden [Azure App Service](https://azure.microsoft.com/services/app-service/) dağıtabilirsiniz.

**Docker** Gezgini 'Ndeki **kayıt defterleri** düğümü altında görüntüyü bulun ve etiketlerini göstermek için genişletin. Bir etikete sağ tıklayın ve sonra **Azure App Service görüntüyü dağıt**' ı seçin.

Buradan bir abonelik, genel olarak benzersiz bir uygulama adı, kaynak grubu ve bir App Service planı seçmek için istemleri izleyin. Fiyatlandırma Katmanı ve bir bölge için **B1 Basic** ' i seçin.

Dağıtımdan sonra uygulamanız ' de kullanılabilir `http://<app name>.azurewebsites.net` .

**Kaynak grubu** , Azure 'daki tüm uygulamanızın kaynaklarının adlandırılmış bir koleksiyonudur. Örneğin, bir kaynak grubu bir Web sitesi, bir veritabanı ve bir Azure Işlevi için başvuru içerebilir.

**App Service planı** , Web sitenizi barındırmak için kullanılacak fiziksel kaynakları tanımlar. Bu hızlı başlangıç, **Linux** altyapısında **temel** bir barındırma planı kullanır. Bu, sitenin diğer Web sitelerinden daha farklı bir Linux makinesinde barındırılması anlamına gelir. **Temel** planla başlatırsanız, bir makinede çalışan tek site olmak üzere ölçeğini ölçeklendirmek için Azure Portal kullanabilirsiniz.

## <a name="browse-the-website"></a>Web sitesine gidin

İşlemin durumunu göstermek için dağıtım sırasında **Çıkış** paneli açılır. İşlem tamamlandığında, **App SERVICE** Explorer 'da oluşturduğunuz uygulamayı bulun, sağ tıklayın ve siteyi tarayıcınızda açmak Için **Web sitesine gidin** ' i seçin.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler, bu hızlı başlangıcı başarıyla tamamladınız!

Daha sonra diğer Azure uzantılarına göz atın.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure İşlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLı araçları](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ya da [Azure Araçları](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) uzantısı paketini yükleyerek tümünü alın.

Diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [Özel kapsayıcı yapılandırma](configure-custom-container.md)

::: zone-end
