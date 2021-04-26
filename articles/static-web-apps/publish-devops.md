---
title: 'Öğretici: Azure DevOps ile Azure statik Web Apps yayımlama'
description: Azure statik Web Apps yayımlamak için Azure DevOps kullanmayı öğrenin.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: f82ae60ab7f57b20a727deefa6e286d698ee5b6c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365765"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Öğretici: Azure DevOps ile Azure statik Web Apps yayımlama

Bu makalede [Azure DevOps](https://dev.azure.com/)kullanılarak [Azure statik Web Apps](./overview.md) dağıtma işlemi gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

- Azure statik Web Apps sitesini ayarlama
- Statik bir Web uygulaması derlemek ve yayımlamak için bir Azure DevOps işlem hattı oluşturma

## <a name="prerequisites"></a>Önkoşullar

- **Etkin Azure hesabı:** Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/).
- **Azure DevOps projesi:** Bir tane yoksa, [ücretsiz olarak bir proje oluşturabilirsiniz](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Azure DevOps Işlem hattı:** Başlamak için yardıma ihtiyacınız varsa, bkz. [ilk işlem hattınızı oluşturma](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Azure DevOps deposunda statik bir Web uygulaması oluşturma

  > [!NOTE]
  > Deponuzda mevcut bir uygulamanız varsa, sonraki bölüme atlayabilirsiniz.

1. Azure DevOps deponuza gidin.

1. Örnek bir uygulamayı içeri aktarmaya başlamak için **Içeri aktar** ' ı seçin.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps deposu":::

1. **Kopya URL**'sinde, girin `https://github.com/staticwebdev/vanilla-api.git` .

1. **İçeri aktar**'ı seçin.

## <a name="create-a-static-web-app"></a>Statik web uygulaması oluşturma

1. [Azure Portal](https://portal.azure.com)gidin.

1. **Kaynak Oluştur**’u seçin.

1. **Statik Web Apps** arayın.

1. **Statik Web Apps seçin (Önizleme)**.

1. **Oluştur**’u seçin.

1. _Dağıtım ayrıntıları_ ' nın altında **diğer**' i seçtiğinizden emin olun. Bu, Azure DevOps deponuzdaki kodu kullanmanıza olanak sağlar.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Dağıtım ayrıntıları-diğer":::

1. Dağıtım başarılı olduktan sonra yeni statik Web Apps kaynağına gidin.

1. **Dağıtım belirtecini Yönet**' i seçin.

1. **Dağıtım belirtecini** kopyalayın ve başka bir ekranda kullanmak üzere bir metin düzenleyicisine yapıştırın.

    > [!NOTE]
    > Bu değer, önümüzdeki adımlarda daha fazla değer kopyalayacağından, bu değerin Şu anda ayrılmış olarak ayarlanır.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Dağıtım belirteci":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Azure DevOps 'da işlem hattı görevi oluşturma

1. Daha önce oluşturulan Azure DevOps deposuna gidin.

1. **Derlemeyi ayarla**' yı seçin.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Derleme işlem hattı":::

1. *Ardışık düzeni yapılandırın* ekranında **Başlatıcı işlem hattı**' nı seçin.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="İşlem hattını yapılandırma":::

1. Aşağıdaki YAML 'yi kopyalayıp işlem hattınızla yapıştırın.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Örnek uygulamayı kullanmıyorsanız,, `app_location` `api_location` ve değerlerinin `output_location` uygulamanızdaki değerlerle eşleşecek şekilde değiştirilmesi gerekir.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token`Değer kendi kendine yönetilir ve el ile yapılandırılır.

1. **Değişkenler**' i seçin.

1. Yeni bir değişken oluşturun.

1. Değişkeni **deployment_token** (iş akışındaki ad ile eşleşen) adlandırın.

1. Daha önce bir metin düzenleyicisine yapıştırdığınız dağıtım belirtecini kopyalayın.

1. _Değer_ kutusuna dağıtım belirtecine yapıştırın.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Değişken belirteci":::

1. **Bu değeri gizli tut ' u** seçin.

1. **Tamam**’ı seçin.

1. İşlem hattınızda YAML 'ye geri dönmek için **Kaydet** ' i seçin.

1. Kaydet _ve Çalıştır iletişim kutusunu_ açmak için **Kaydet ve Çalıştır ' ı** seçin.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="İşlem Hattı":::

1. Komut zinciri çalıştırmak için **Kaydet ve Çalıştır ' ı** seçin.

1. Dağıtım başarılı olduktan sonra dağıtım yapılandırmasına bağlantılar içeren Azure statik Web Apps **genel bakışa** gidin. _Kaynak_ bağlantının artık Azure DevOps deposunun dalını ve konumunu nasıl işaret ettiğini unutmayın.

1. Yeni dağıtılan Web sitenizi görmek için **URL 'yi** seçin.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Dağıtım konumu":::

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure statik Web Apps yapılandırma](./configuration.md)
