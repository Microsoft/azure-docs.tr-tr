---
title: 'Öğretici: Azure App Service özel görüntü oluşturma ve çalıştırma'
description: Özel bir Linux veya Windows görüntüsü oluşturmak, görüntüyü Azure Container Registry göndermek ve sonra bu görüntüyü Azure App Service dağıtmak için adım adım kılavuz. Özel bir kapsayıcıda App Service özel yazılım dağıtmayı nasıl geçirebileceğinizi öğrenin.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: Azure App Service, Web uygulaması, Linux, Windows, Docker, kapsayıcı
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 0770b46a60f497d3a3da772e7be13ece0526eca0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765712"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Özel bir kapsayıcı kullanarak Azure App Service özel yazılım geçirme

::: zone pivot="container-windows"  

[Azure App Service](overview.md), Windows'da IIS üzerinde çalışan ASP.NET veya Node.js gibi önceden tanımlı uygulama yığınları sunar. Önceden yapılandırılmış Windows ortamı, işletim sistemini yönetimsel erişime, yazılım yüklemesine ve genel derleme önbelleğine ve benzeri uygulamalara karşı kilitler (bkz. [Azure App Service'teki işletim sistemi işlevleri](operating-system-functionality.md)). Ancak App Service içinde özel bir Windows kapsayıcısı kullanmak, uygulamanızın ihtiyaç duyduğu işletim sistemi değişikliklerini kolayca gerçekleştirmenizi sağlar. Bu sayede özel işletim sistemi ve yazılım yapılandırmasına ihtiyaç duyan bir şirket içi uygulamayı geçirmek oldukça kolaydır. Bu öğreticide Windows yazı tipi kitaplığında yüklü olan özel yazı tiplerini kullanan bir ASP.NET uygulamasını App Service'e geçirme adımları gösterilmektedir. Visual Studio'dan [Azure Container Registry](../container-registry/index.yml)'ye özel olarak yapılandırılmış bir Windows görüntüsü dağıtıp ardından bunu App Service'te çalıştıracaksınız.

![Bir Windows kapsayıcısında çalışan Web uygulamasını gösterir.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- <a href="https://hub.docker.com/" target="_blank">Docker Hub hesabı için kaydolma</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker for Windows'u yükleyin</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Windows kapsayıcılarını çalıştırmak için Docker’a geçiş yapın</a>.
- **ASP.net ve Web geliştirme** ve **Azure geliştirme** Iş yükleriyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ' i yükledikten sonra. Visual Studio 2019 zaten yüklüyse:
    -   >  **Güncelleştirmeler için yardım denetimi**' ne tıklayarak Visual Studio 'ya en son güncelleştirmeleri yüklersiniz.
    - **Araçlar**  >  **ve Özellikler al**' a tıklayarak iş yüklerini Visual Studio 'da ekleyin.

## <a name="set-up-the-app-locally"></a>Uygulamayı yerel ortamda oluşturma

### <a name="download-the-sample"></a>Örneği indirme

Bu adımda yerel .NET projesini oluşturacaksınız.

- [Örnek projeyi indirin](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- *custom-font-win-container.zip* dosyasını açın.

Örnek projede Windows yazı tipi kitaplığına yüklenmiş olan özel bir yazı tipini kullanan basit bir ASP.NET uygulaması bulunmaktadır. Yazı tiplerini yüklemenize gerek yoktur işletim sistemine tümleşik bir uygulama örneği olarak verilmiştir. Bu tür bir uygulamayı App Service'e geçirmek için kodunuzu yeniden düzenleyip tümleştirmeyi kaldırabilir veya özel Windows kapsayıcısında olduğu için olduğu şekilde geçirebilirsiniz.

### <a name="install-the-font"></a>Yazı tipini yükleme

Windows Gezgini'nde _custom-font-win-container-master/CustomFontSample_ dizinine gidin, _FrederickatheGreat-Regular.ttf_ dosyasına sağ tıklayın ve **Yükle**'yi seçin.

Bu yazı tipi [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great) sayfasında genel kullanıma açık bir şekilde sunulmaktadır.

### <a name="run-the-app"></a>Uygulamayı çalıştırma

*custom-font-win-container/CustomFontSample.sln* dosyasını Visual Studio'da açın. 

Uygulamayı hata ayıklaması yapılmadan çalıştırmak için `Ctrl+F5` yazın. Uygulama varsayılan tarayıcınızda görüntülenir. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Varsayılan tarayıcıda gösterilen uygulamayı gösteren ekran görüntüsü.":::

Yüklenmiş olan bir yazı tipini kullandığından uygulama App Service korumalı alanında çalışmayacaktır. Ancak yazı tipini Windows kapsayıcısına yükleyebileceğiniz için uygulamayı Windows kapsayıcısı kullanarak dağıtabilirsiniz.

### <a name="configure-windows-container"></a>Windows kapsayıcısını yapılandırma

Çözüm Gezgini'nde **CustomFontSample** projesine sağ tıklayıp **Ekle** > **Kapsayıcı Düzenleme Desteği**'ne tıklayın.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="CustomFontSample projesi, ekleme ve kapsayıcı Orchestrator desteği menü öğelerini gösteren Çözüm Gezgini penceresinin ekran görüntüsü.":::

**Docker Compose**  >  **Tamam ' ı** seçin.

Projeniz Windows kapsayıcısında çalışacak şekilde ayarlanır. **CustomFontSample** projesine bir _Dockerfile_ ve çözüme bir **docker-compose** projesi eklenir. 

Çözüm Gezgini'nden **Dockerfile** dosyasını açın.

[Desteklenen bir üst görüntü](configure-custom-container.md#supported-parent-images) kullanmanız gerekir. `FROM` satırını aşağıdaki kod ile değiştirerek üst görüntüyü değiştirin:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Dosyanın en sonuna şu satırı ekleyin ve dosyayı kaydedin:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

_InstallFont.ps1_ dosyasını **CustomFontSample** projesinde bulabilirsiniz. Yazı tipini yükleyen basit bir betiktir. Betiğin daha karmaşık sürümünü [Betik Merkezi](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133) sayfasında bulabilirsiniz.

> [!NOTE]
> Windows kapsayıcısını yerel olarak test etmek için, Docker 'ın yerel makinenizde başlatıldığından emin olun.
>

## <a name="publish-to-azure-container-registry"></a>Azure Container Registry'de yayımlama

[Azure Container Registry](../container-registry/index.yml), kapsayıcı dağıtımlarınızın görüntülerini depolayabilir. App Service'i Azure Container Registry'de barındırılan görüntüleri kullanacak şekilde yapılandırabilirsiniz.

### <a name="open-publish-wizard"></a>Yayımlama sihirbazını açma

Çözüm Gezgini'nde **CustomFontSample** projesine sağ tıklayın ve **Yayımla**'yı seçin.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="CustomFontSample projesini gösteren Çözüm Gezgini ekran görüntüsü ve seçili yayımlama.":::

### <a name="create-registry-and-publish"></a>Kayıt defterini oluşturma ve yayımlama

Yayımla sihirbazında **Container Registry**  >  **yeni Azure Container Registry oluştur**' u seçin  >  .

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Container Registry, yeni Azure Container Registry oluştur ve Yayımla düğmesi seçili olan Yayımla Sihirbazı ekran görüntüsü.":::

### <a name="sign-in-with-azure-account"></a>Azure hesabınızla oturum açın

**Yeni Azure Container Registry oluştur** iletişim kutusunda **Hesap ekle**’yi seçin ve Azure aboneliğinizde oturum açın. Oturumunuz zaten açıksa, açılan menüden istediğiniz aboneliği içeren hesabı seçin.

![Azure'da oturum açma](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Kayıt defterini yapılandırma

Yeni kapsayıcı kayıt defterini aşağıdaki tabloda bulunan değerleri kullanarak yapılandırın. Tamamladığınızda **Oluştur**’a tıklayın.

| Ayar  | Önerilen değer | Daha fazla bilgi edinmek için |
| ----------------- | ------------ | ----|
|**DNS Ön Eki**| Oluşturulan kayıt defteri adını kullanın veya benzersiz bir adla değiştirin. |  |
|**Kaynak Grubu**| **Yeni**'ye tıklayın, **myResourceGroup** yazın ve **Tamam**'a tıklayın. |  |
|**SKU**| Temel | [Fiyatlandırma katmanları](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Kayıt Defteri Konumu**| West Europe | |

![Azure Container Registry yapılandırması](./media/tutorial-custom-container/configure-registry.png)

Bir terminal penceresi açılır ve görüntü dağıtımı ilerleme durumunu görüntüler. Dağıtımın tamamlanmasını bekleyin.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Sol menüden **kaynak oluştur**  >  **Web**  >  **kapsayıcılar için Web App** seçin.

### <a name="configure-app-basics"></a>Uygulama temellerini yapılandırma

**Temel bilgiler** sekmesinde, aşağıdaki tabloya göre ayarları yapılandırın ve ardından İleri ' ye tıklayın **: Docker**.

| Ayar  | Önerilen değer | Daha fazla bilgi edinmek için |
| ----------------- | ------------ | ----|
|**Abonelik**| Doğru aboneliğin seçildiğinden emin olun. |  |
|**Kaynak Grubu**| **Yeni oluştur**' u seçin, **myresourcegroup** yazın ve **Tamam**' a tıklayın. |  |
|**Ad**| Benzersiz bir ad yazın. | Web uygulamasının URL'si `http://<app-name>.azurewebsites.net` şeklindedir; burada `<app-name>`, uygulamanızın adıdır. |
|**Yayımla**| Docker kapsayıcısı | |
|**İşletim Sistemi**| Windows | |
|**Bölge**| West Europe | |
|**Windows Planı**| **Yeni oluştur**' u seçin, **myappserviceplan** yazın ve **Tamam**' a tıklayın. | |

**Temel kavramlar** sekmesi şöyle görünmelidir:

![Web uygulamasını yapılandırmak için kullanılan temel bilgiler sekmesini gösterir.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Windows kapsayıcısını yapılandırma

**Docker** sekmesinde, aşağıdaki tabloda gösterildiği gibi özel Windows kapsayıcınızı yapılandırın ve **gözden geçir + oluştur**' u seçin.

| Ayar  | Önerilen değer |
| ----------------- | ------------ |
|**Görüntü Kaynağı**| Azure Container kaydı |
|**Kapsayıcı Kayıt Defteri**| [Daha önce oluşturduğunuz kayıt defterini](#publish-to-azure-container-registry)seçin. |
|**Görüntü**| customfontsample |
|**Tag**| en son |

### <a name="complete-app-creation"></a>Uygulama oluşturmayı tamamlama

**Oluştur**'a tıklayın ve Azure'un gereken kaynakları oluşturmasını bekleyin.

## <a name="browse-to-the-web-app"></a>Web uygulamasına göz atma

Azure işlemi tamamlandığında bir bildirim kutusu görüntülenir.

![Azure işleminin tamamlandığını gösterir.](media/tutorial-custom-container/portal-create-finished.png)

1. **Kaynağa git**'e tıklayın.

2. Uygulama sayfasında **URL**'nin altındaki bağlantıya tıklayın.

Aşağıdaki sayfayla yeni bir tarayıcı sayfası açılır:

![Web uygulaması için yeni tarayıcı sayfasını gösterir.](media/tutorial-custom-container/app-starting.png)

Birkaç dakika bekleyin ve beklediğiniz güzel yazı tipine sahip giriş sayfası açılana kadar yeniden deneyin:

![Yapılandırdığınız yazı tipine sahip giriş sayfasını gösterir.](media/tutorial-custom-container/app-running.png)

**Tebrikler!** Bir ASP.NET uygulamasını Windows kapsayıcısında Azure App Service'e geçirdiniz.

## <a name="see-container-start-up-logs"></a>Kapsayıcı başlangıç günlüklerini inceleme

Windows kapsayıcısının yüklenmesi biraz zaman alabilir. İlerleme durumunu görmek için uygulamanızın adıyla değiştirerek aşağıdaki URL 'ye gidin *\<app-name>* .
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Akışı yapılan günlükler şuna benzer:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service, hem yerleşik görüntüleri hem de özel görüntüleri barındırmak için Docker kapsayıcı teknolojisini kullanır. Yerleşik görüntülerin bir listesini görmek için, [' az WebApp List-çalışma zamanları--Linux '](/cli/azure/webapp#az_webapp_list_runtimes)Azure CLI komutunu çalıştırın. Bu görüntüler gereksinimlerinizi karşılamadığı takdirde, özel bir görüntü oluşturup dağıtabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Gereksinimlerinize uygun bir yerleşik görüntü yoksa özel bir görüntü oluşturun
> * Özel görüntüyü Azure 'da özel bir kapsayıcı kayıt defterine gönderme
> * Özel görüntüyü App Service Çalıştır
> * Ortam değişkenlerini yapılandırma
> * Görüntüyü güncelleştirme ve yeniden dağıtma
> * Tanılama günlüklerine erişim
> * SSH kullanarak kapsayıcıya bağlanma

Bu öğreticiyi tamamlamak, kapsayıcı kayıt defteri için Azure hesabınızda küçük bir ücret ödemektedir ve kapsayıcıyı bir aydan uzun süreyle barındırmak için ek maliyetler oluşturabilir.

## <a name="set-up-your-initial-environment"></a>İlk ortamınızı ayarlama

- Etkin aboneliği olan bir Azure hesabına sahip olmanız gerekir. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Docker görüntülerini oluşturmak için kullandığınız [Docker](https://docs.docker.com/get-started/#setup)'ı yükleyebilirsiniz. Docker 'ı yüklemek için bilgisayarın yeniden başlatılması gerekebilir.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Bu öğretici, Azure CLı 'nin sürüm 2.0.80 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

Docker 'ı yükledikten veya Azure Cloud Shell çalıştırdıktan sonra, bir Terminal penceresi açın ve Docker 'ın yüklü olduğunu doğrulayın:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Örnek uygulamayı kopyalayın veya indirin

Bu öğreticide git Clone veya Download aracılığıyla örnek elde edebilirsiniz.

### <a name="clone-with-git"></a>Git ile Kopyala

Örnek depoyu kopyalayın:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

`--config core.autocrlf=input`Linux kapsayıcısının içinde kullanılan dosyalardaki uygun satır sonlarını güvence altına almak için bağımsız değişkenini eklediğinizden emin olun:

Daha sonra bu klasöre gidin:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>GitHub’dan indirin

Git klonu kullanmak yerine, ' ı ziyaret edebilir [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , **Kopyala**' yı seçebilir ve **ZIP indir**' i seçebilirsiniz. 

ZIP dosyasını *Docker-docgo-WebApp-Linux* adlı bir klasöre ayıklayın. 

Ardından, bu *Docker-docgo-WebApp-Linux* klasöründe bir Terminal penceresi açın.

## <a name="optional-examine-the-docker-file"></a>Seçim Docker dosyasını inceleyin

Docker görüntüsünü açıklayan ve yapılandırma yönergelerini içeren _Dockerfile_ adlı örnekteki dosya:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* İlk komut grubu, uygulamanın gereksinimlerini ortama yüklerken.
* İkinci komut grubu, kapsayıcı ve konak arasında güvenli iletişim için bir [SSH](https://www.ssh.com/ssh/protocol/) sunucusu oluşturur.
* Son satır, `ENTRYPOINT ["init.sh"]` `init.sh` SSH hizmetini ve Python sunucusunu başlatmak için çağırır.

## <a name="build-and-test-the-image-locally"></a>Görüntüyü yerel olarak derleyin ve test edin

> [!NOTE]
> Docker Hub [, IP başına anonim giriş sayısı ve ücretsiz Kullanıcı başına kimliği doğrulanmış giriş sayısı üzerinde kotalar içerir (bkz. **veri aktarımı**)](https://www.docker.com/pricing). Docker Hub 'dan çekmenize sınırlı olduğunu fark ederseniz, `docker login` oturum açmadıysanız deneyin.
> 

1. Görüntüyü derlemek için aşağıdaki komutu çalıştırın:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. , Docker kapsayıcısını yerel olarak çalıştırarak, yapılandırmanın çalıştığını test edin:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Bu [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) komut, ve `-p` ardından görüntünün adı ile birlikte gelen bağlantı noktasını belirtir. 
    
    > [!TIP]
    > Windows üzerinde çalıştırıyorsanız ve hatayı görürseniz, *standard_init_linux. go: 211: exec Kullanıcı işlemi "böyle bir dosya veya dizin yok" hatası nedeniyle*, *INIT.sh* dosyası beklenen LF SONLARı yerine CR-LF satır sonlarını içeriyor. Bu hata, örnek depoyu kopyalamak için git kullandıysanız ancak parametreyi atlarsanız oluşur `--config core.autocrlf=input` . Bu durumda, depoyu '--config ' ' bağımsız değişkeniyle yeniden kopyalayın. Ayrıca, *init.sh* öğesini DÜZENLEDIYSENIZ ve CRLF sonları ile kaydettiyseniz hatayı görebilirsiniz. Bu durumda, dosyayı yalnızca LF sonları ile yeniden kaydedin.

1. `http://localhost:8000`Web uygulamasının ve kapsayıcının düzgün çalıştığını doğrulamak için öğesine gidin.

    ![Web uygulamasını yerel olarak test etme](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bu bölümde ve bundan sonra, görüntüyü gönderdiğiniz ve ardından Azure App Service bir kapsayıcı dağıttığınız Azure 'da kaynak temin edersiniz. Bu kaynakların tümünün toplanacağı bir kaynak grubu oluşturarak başlayın.

Bir kaynak grubu oluşturmak için [az Group Create](/cli/azure/group#az_group_create) komutunu çalıştırın:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

`--location`Size yakın bir bölge belirtmek için değeri değiştirebilirsiniz.

## <a name="push-the-image-to-azure-container-registry"></a>Görüntüyü Azure Container Registry gönderin

Bu bölümde, görüntüyü App Service dağıtabilecek Azure Container Registry için gönderebilirsiniz.

1. [`az acr create`](/cli/azure/acr#az_acr_create)Azure Container Registry oluşturmak için komutunu çalıştırın:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    `<registry-name>`Kayıt defteriniz için uygun bir adla değiştirin. Ad yalnızca harf ve rakam içermeli ve tüm Azure genelinde benzersiz olmalıdır.

1. [`az acr show`](/cli/azure/acr#az_acr_show)Kayıt defteri için kimlik bilgilerini almak için komutunu çalıştırın:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    Bu komutun JSON çıktısı, kayıt defterinin Kullanıcı adı ile birlikte iki parola sağlar.
    
1. `docker login`Kapsayıcı kayıt defterinde oturum açmak için komutunu kullanın:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    `<registry-name>`Ve `<registry-username>` değerlerini önceki adımlardan gelen değerlerle değiştirin. İstendiğinde, önceki adımda bulunan parolalardan birini yazın.

    Bu bölümün kalan adımlarında aynı kayıt defteri adını kullanırsınız.

1. Oturum açma başarılı olduktan sonra kayıt defteri için yerel Docker görüntünüzü etiketleyin:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. `docker push`Görüntüyü kayıt defterine göndermek için komutunu kullanın:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Görüntüyü karşıya yüklemek, temel görüntüyü içerdiği için ilk kez birkaç dakika sürebilir. Sonraki yüklemeler genellikle daha hızlıdır.

    Beklerken, kayıt defterinden dağıtılacak App Service yapılandırmak için sonraki bölümdeki adımları tamamlayabilirsiniz.

1. `az acr repository list`Gönderme işleminin başarılı olduğunu doğrulamak için komutunu kullanın:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    Çıktıda görüntünüzün adı gösterilmelidir.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Görüntüyü kayıt defterinden dağıtmak için App Service yapılandırma

Azure App Service bir kapsayıcı dağıtmak için, önce App Service üzerinde bir Web uygulaması oluşturun, ardından Web uygulamasını kapsayıcı kayıt defterine bağlayın. Web uygulaması başladığında, App Service görüntüyü otomatik olarak kayıt defterinden çeker.

1. Komutunu kullanarak bir App Service planı oluşturun [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) :

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    App Service planı, Web uygulamasını barındıran sanal makineye karşılık gelir. Varsayılan olarak, önceki komut ilk ay için ücretsiz olan pahalı bir [B1 fiyatlandırma katmanını](https://azure.microsoft.com/pricing/details/app-service/linux/) kullanır. Katmanı parametresiyle kontrol edebilirsiniz `--sku` .

1. Şu komutla Web uygulamasını oluşturun [`az webpp create`](/cli/azure/webapp#az_webapp_create) :

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    `<app-name>`Web uygulaması için bir adla değiştirin. Bu, tüm Azure genelinde benzersiz olmalıdır. Ayrıca, `<registry-name>` önceki bölümde bulunan kayıt defterinizin adıyla değiştirin.

1. [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) `WEBSITES_PORT` Ortam değişkenini uygulama kodu tarafından beklendiği gibi ayarlamak için kullanın: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    `<app-name>`Önceki adımda kullandığınız adla değiştirin.
    
    Bu ortam değişkeni hakkında daha fazla bilgi için örneğin [GitHub deposundaki Benioku dosyasına](https://github.com/Azure-Samples/docker-django-webapp-linux)bakın.

1. Şu komutu kullanarak Web uygulaması için [yönetilen kimliği](./overview-managed-identity.md) etkinleştirin [`az webapp identity assign`](/cli/azure/webapp/identity#az_webapp_identity-assign) :

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    `<app-name>`Önceki adımda kullandığınız adla değiştirin. Komutun çıkışı ( `--query` ve bağımsız değişkenlerine göre filtrelenmiş), `--output` Bu, kısa süre içinde kullandığınız atanan kimliğin hizmet sorumlusu olur.

    Yönetilen kimlik, belirli kimlik bilgilerine gerek duymadan Web uygulamasına diğer Azure kaynaklarına erişim izni vermenizi sağlar.

1. Bir [`az account show`](/cli/azure/account#az_account_show) sonraki adımda ihtiyacınız olan komutuyla ABONELIK Kimliğinizi alın:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Web uygulamasına kapsayıcı kayıt defterine erişim izni verin:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Aşağıdaki değerleri değiştirin:
    - `<principal-id>`komutun hizmet sorumlusu KIMLIĞIYLE `az webapp identity assign`
    - `<registry-name>` kapsayıcı kayıt defterinizin adıyla
    - `<subscription-id>`komuttan alınan abonelik KIMLIĞIYLE `az account show`

Bu izinler hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi nedir](../role-based-access-control/overview.md) ve 

## <a name="deploy-the-image-and-test-the-app"></a>Görüntüyü dağıtın ve uygulamayı test edin

Görüntü kapsayıcı kayıt defterine gönderildikten sonra App Service tam olarak sağlandıktan sonra bu adımları tamamlayabilirsiniz.

1. [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set)Kapsayıcı kayıt defterini ve Web uygulaması için dağıtılacak görüntüyü belirtmek için komutunu kullanın:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    `<app_name>`Web uygulamanızın adıyla değiştirin ve `<registry-name>` iki yerde, kayıt defterinizin adıyla değiştirin. 

    - Docker Hub 'dan farklı bir kayıt defteri kullanırken (Bu örnekte gösterildiği gibi), `--docker-registry-server-url` `https://` kayıt defterinin tam etki alanı adı tarafından izlenen şekilde biçimlendirilmesi gerekir.
    - İleti, "erişim için kimlik bilgisi sağlanmadı Azure Container Registry. Aramaya çalışılıyor... " Azure 'un, bir Kullanıcı adı ve parola sormak yerine kapsayıcı kayıt defteri ile kimlik doğrulaması yapmak için uygulamanın yönetilen kimliğini kullandığını söyler.
    - Hatayla karşılaşırsanız, "AttributeError: ' NoneType ' nesnesinde ' ayrılmış ' özniteliği yoktur, doğru olduğundan emin olun `<app-name>` .

    > [!TIP]
    > Web uygulamasının kapsayıcı ayarlarını, komutunu kullanarak istediğiniz zaman alabilirsiniz `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . Görüntü, özelliğinde belirtilmiştir `DOCKER_CUSTOM_IMAGE_NAME` . Web uygulaması Azure DevOps veya Azure Resource Manager şablonları aracılığıyla dağıtıldığında, görüntü adlı bir özellikte de görünebilir `LinuxFxVersion` . Her iki özellik de aynı amaca sahiptir. Web uygulamasının yapılandırmasında her ikisi de varsa, `LinuxFxVersion` öncelik kazanır.

1. `az webapp config container set`Komut tamamlandıktan sonra, Web uygulamasının App Service kapsayıcıda üzerinde çalışıyor olması gerekir.

    Uygulamayı test etmek için ' e gidin `http://<app-name>.azurewebsites.net` , `<app-name>` Web uygulamanızın adıyla değiştirin. İlk erişimde, App Service tüm görüntüyü kayıt defterinden çekmek gerektiğinden uygulamanın yanıt vermesi biraz zaman alabilir. Tarayıcı zaman aşımına uğrarsa sayfayı yenilemeniz yeterlidir. İlk görüntü çekildikten sonra, sonraki testler çok daha hızlı çalışacaktır.

    ![Azure 'da Web uygulamasının başarılı testi](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Uygulama kodunu değiştirin ve yeniden dağıtın

Bu bölümde, Web uygulaması kodunda bir değişiklik yapar, kapsayıcıyı yeniden derleyin ve ardından kapsayıcıyı kayıt defterine gönderirsiniz. App Service, çalışan Web uygulamasını güncelleştirmek için, güncelleştirilmiş görüntüyü otomatik olarak kayıt defterinden çeker.

1. Yerel *Docker-docgo-WebApp-Linux* klasörünüzde *App/Templates/App/index.html* dosyasını açın.

1. İlk HTML öğesini aşağıdaki kodla eşleşecek şekilde değiştirin.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Yaptığınız değişiklikleri kaydedin.

1. *Docker-docgo-WebApp-Linux* klasörünü değiştirin ve görüntüyü yeniden oluşturun:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Görüntünün etiketindeki sürüm numarasını v 1.0.1 olarak güncelleştirin:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    `<registry-name>` değerini kayıt defterinizin adıyla değiştirin.

1. Görüntüyü kayıt defterine gönderin:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Web uygulamasını yeniden başlatın:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    `<app_name>` değerini web uygulamanızın adıyla değiştirin. Yeniden başlattıktan sonra, App Service güncelleştirilmiş görüntüyü kapsayıcı kayıt defterinden çeker.

1. Güncelleştirmenin öğesine göz atarak dağıtıldığını doğrulayın `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

1. Kapsayıcı günlüğünü aç:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Günlük akışını etkinleştirin:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Konsol günlüklerini hemen görmüyorsanız, 30 saniye içinde yeniden kontrol edin.

    Ayrıca, tarayıcıdan `https://<app-name>.scm.azurewebsites.net/api/logs/docker` adresine giderek günlük dosyalarını inceleyebilirsiniz.

1. Günlük akışını istediğiniz zaman durdurmak için **CTRL** + **C** yazın.

## <a name="connect-to-the-container-using-ssh"></a>SSH kullanarak kapsayıcıya bağlanma

SSH, kapsayıcı ile istemci arasında güvenli iletişime olanak tanır. Kapsayıcınıza SSH bağlantısı sağlamak için özel görüntünüzün yapılandırılması gerekir. Kapsayıcı çalışmaya başladıktan sonra bir SSH bağlantısı açabilirsiniz.

### <a name="configure-the-container-for-ssh"></a>SSH için kapsayıcıyı yapılandırma

Bu öğreticide kullanılan örnek uygulama, SSH sunucusunu yükleyecek ve ayrıca oturum açma kimlik bilgilerini ayarlayan *Dockerfile*'da gerekli yapılandırmaya zaten sahiptir. Bu bölüm yalnızca bilgilendirme amaçlıdır. Kapsayıcıya bağlanmak için sonraki bölüme atlayın

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Bu yapılandırma kapsayıcıya dış bağlantılara izin vermiyor. SSH yalnızca Kudu/SCM Sitesi aracılığıyla kullanılabilir. Kudu/SCM sitesinin kimliği, Azure hesabınızla doğrulanır.

*Dockerfile* Ayrıca *sshd_config* dosyasını */etc/ssh/* klasörüne kopyalar ve kapsayıcıda 2222 numaralı bağlantı noktasını kullanıma sunar:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Bağlantı noktası 2222, yalnızca özel bir sanal ağın köprü ağı içindeki kapsayıcılar tarafından erişilebilen bir iç bağlantı noktasıdır. 

Son olarak, *init.sh* GIRIŞ betiği SSH sunucusunu başlatır.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Kapsayıcıya SSH bağlantısı açma

1. Gidin `https://<app-name>.scm.azurewebsites.net/webssh/host` ve Azure hesabınızla oturum açın. `<app-name>` değerini web uygulamanızın adıyla değiştirin.

1. Oturum açıldıktan sonra, Web uygulaması için bir bilgilendirme sayfasına yönlendirilirsiniz. Kabuğu açmak ve komutları kullanmak için sayfanın üst kısmındaki **SSH** ' ı seçin.

    Örneğin, komutunu kullanarak, içinde çalışan işlemi inceleyebilirsiniz `top` .
    
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturduğunuz kaynaklar devam eden maliyetlerine neden olabilir. kaynakları temizlemek için, yalnızca bunları içeren kaynak grubunu silmeniz gerekir:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

Öğrendikleriniz:

> [!div class="checklist"]
> * Özel bir kapsayıcı kayıt defterine özel bir görüntü dağıtma
> * App Service ve özel görüntüyü dağıtın
::: zone pivot="container-linux"
> * Görüntüyü güncelleştirme ve yeniden dağıtma
::: zone-end
> * Tanılama günlüklerine erişim
::: zone pivot="container-linux"
> * SSH kullanarak kapsayıcıya bağlanma
::: zone-end

Sonraki öğreticide, uygulamanıza özel bir DNS adı eşlemeyi öğreneceksiniz.

> [!div class="nextstepaction"]
> [Öğretici: özel DNS adını uygulamanıza eşleyin](app-service-web-tutorial-custom-domain.md)

Ya da diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [Özel kapsayıcı yapılandırma](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Öğretici: çok Kapsayıcılı WordPress uygulaması](tutorial-multi-container-app.md)
::: zone-end
