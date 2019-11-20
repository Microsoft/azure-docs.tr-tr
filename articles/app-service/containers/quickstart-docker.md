---
title: Linux 'ta Docker uygulaması dağıtma-Azure App Service
description: Linux için Azure Uygulama Hizmetleri 'ne Docker görüntüsü dağıtma
author: msangapu
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.service: app-service
ms.openlocfilehash: 2a7dc477b4cd0be0c50569d84e10cfe1d666eac9
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70392117"
---
# <a name="deploy-to-azure-using-docker"></a>Docker kullanarak Azure 'a dağıtma

Linux üzerinde App Service, Linux üzerinde .NET, PHP, Node. js ve diğerleri gibi diller desteğiyle önceden tanımlanmış uygulama yığınları sağlar. Ayrıca web uygulamanızı Azure’da zaten tanımlı olmayan bir uygulama yığınında çalıştırmak için özel bir Docker görüntüsü de kullanabilirsiniz. Bu hızlı başlangıçta, bir [Azure Container Registry](/azure/container-registry) (ACR) App Service bir görüntünün nasıl dağıtılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

* Bir [Azure hesabı](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Vs Code için Azure App Service uzantısı](vscode:extension/ms-azuretools.vscode-azureappservice). Bu uzantıyı Azure platformunda bir hizmet olarak (PaaS) Linux Web Apps oluşturmak, yönetmek ve dağıtmak için kullanabilirsiniz.
* [Vs Code Için Docker uzantısı](vscode:extension/ms-azuretools.vscode-docker). Bu uzantıyı, yerel Docker görüntüleri ve komutlarının yönetimini basitleştirmek ve oluşturulan uygulama görüntülerini Azure 'a dağıtmak için kullanabilirsiniz.

## <a name="create-an-image"></a>Görüntü oluştur

Bu hızlı başlangıcı tamamlayabilmeniz için, [Azure Container Registry](/azure/container-registry)depolanan uygun bir Web uygulaması görüntüsüne ihtiyacınız olacaktır. Hızlı Başlangıç bölümündeki [yönergeleri izleyin: Azure Portal](/azure/container-registry/container-registry-get-started-portal)kullanarak bir özel kapsayıcı kayıt defteri oluşturun, ancak görüntü yerine `mcr.microsoft.com/azuredocs/go` `hello-world` görüntüyü kullanın.

> [!IMPORTANT]
> Kapsayıcı kayıt defterini oluştururken **Yönetici Kullanıcı** seçeneğini **etkinleştirmek** için ayarladığınızdan emin olun. Ayrıca, Azure portal kayıt defteri sayfanızın **erişim tuşları** bölümünden de ayarlayabilirsiniz. App Service erişim için bu ayar gereklidir.

## <a name="sign-in"></a>Oturum aç

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

![Kayıt Defterleri](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Görüntüyü Azure App Service dağıtma

Artık her şey yapılandırıldığına göre, görüntünüzü doğrudan Docker uzantı Gezgini 'nden [Azure App Service](https://azure.microsoft.com/services/app-service/) dağıtabilirsiniz.

**Docker** Gezgini 'Ndeki **kayıt defterleri** düğümü altında görüntüyü bulun ve etiketlerini göstermek için genişletin. Bir etikete sağ tıklayın ve sonra **Azure App Service görüntüyü dağıt**' ı seçin.

Buradan bir abonelik, genel olarak benzersiz bir uygulama adı, kaynak grubu ve bir App Service planı seçmek için istemleri izleyin. Fiyatlandırma Katmanı ve bir bölge için **B1 Basic** ' i seçin.

Dağıtımdan sonra uygulamanız ' de kullanılabilir `http://<app name>.azurewebsites.net`.

**Kaynak grubu** , Azure 'daki tüm uygulamanızın kaynaklarının adlandırılmış bir koleksiyonudur. Örneğin, bir kaynak grubu bir Web sitesi, bir veritabanı ve bir Azure Işlevi için başvuru içerebilir.

**App Service planı** , Web sitenizi barındırmak için kullanılacak fiziksel kaynakları tanımlar. Bu hızlı başlangıç, **Linux** altyapısında **temel** bir barındırma planı kullanır. Bu, sitenin diğer Web sitelerinden daha farklı bir Linux makinesinde barındırılması anlamına gelir. **Temel** planla başlatırsanız, bir makinede çalışan tek site olmak üzere ölçeğini ölçeklendirmek için Azure Portal kullanabilirsiniz.

## <a name="browse-the-website"></a>Web sitesine Gözat

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
