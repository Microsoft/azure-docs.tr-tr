---
title: Azure Işlevleri için ZIP Push dağıtımı
description: Azure Işlevlerinizi yayımlamak için kudu dağıtım hizmetinin. zip dosya dağıtım olanaklarını kullanın.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: fb6867d7719f9650acb00f80ac3a933713ce0e23
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777660"
---
# <a name="zip-deployment-for-azure-functions"></a>Azure İşlevleri için zip dosyasıyla dağıtım

Bu makalede, işlev uygulaması proje dosyalarınızın bir. zip (sıkıştırılmış) dosyasından Azure 'a nasıl dağıtılacağı açıklanır. Azure CLı kullanarak ve REST API 'Lerini kullanarak bir anında iletme dağıtımı yapmayı öğrenirsiniz. [Azure Functions Core Tools](functions-run-local.md) Ayrıca, Azure 'da yerel bir proje yayımlarken bu dağıtım API 'lerini kullanır.

Azure Işlevleri, Azure App Service tarafından sunulan sürekli dağıtım ve tümleştirme seçeneklerinin eksiksiz aralığıdır. Daha fazla bilgi için bkz. [Azure işlevleri Için sürekli dağıtım](functions-continuous-deployment.md).

Geliştirme sürecini hızlandırmak için, işlev uygulaması proje dosyalarınızı doğrudan bir. zip dosyasından dağıtmayı daha kolay bulabilirsiniz. . Zip dağıtım API 'SI bir. zip dosyasının içeriğini alır ve içeriği `wwwroot` işlev uygulamanızın klasörüne ayıklar. Bu. zip dosya dağıtımı, aşağıdakiler de dahil olmak üzere sürekli tümleştirme tabanlı dağıtımları destekleyen kudu hizmetini kullanır:

+ Önceki dağıtımlardan bırakılmış dosyaların silinmesi.
+ Dağıtım komut dosyalarını çalıştırma dahil dağıtım özelleştirmesi.
+ Dağıtım günlükleri.
+ [Tüketim planı](functions-scale.md) işlev uygulamasındaki işlev Tetikleyicileri eşitleniyor.

Daha fazla bilgi için bkz [.. zip dağıtım başvurusu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Dağıtım. zip dosya gereksinimleri

Gönderme dağıtımı için kullandığınız. zip dosyası, işlevinizi çalıştırmak için gereken tüm dosyaları içermelidir.

>[!IMPORTANT]
> . Zip dağıtımını kullandığınızda, var olan bir dağıtımdan. zip dosyasında bulunmayan tüm dosyalar, işlev uygulamanızdan silinir.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Bir işlev uygulaması, dizindeki tüm dosya ve klasörleri içerir `wwwroot` . Bir. zip dosyası dağıtımı dizinin içeriğini içerir `wwwroot` , ancak dizinin kendisidir. C# sınıf kitaplığı projesi dağıtıldığında, derlenmiş kitaplık dosyalarını ve bağımlılıklarını `bin` . zip paketinizin bir alt klasöre eklemeniz gerekir.

## <a name="download-your-function-app-files"></a>İşlev uygulaması dosyalarınızı indirin

Yerel bir bilgisayar üzerinde geliştirme yaparken, geliştirme bilgisayarınızda App Project klasörünün bir. zip dosyasını oluşturmak kolaydır.

Ancak, Azure portal düzenleyiciyi kullanarak işlevlerinizi oluşturmuş olabilirsiniz. Varolan bir işlev uygulaması projesini şu yollarla indirebilirsiniz:

+ **Azure portal:**

  1. [Azure Portal](https://portal.azure.com)oturum açın ve ardından işlev uygulamanıza gidin.

  2. **Genel bakış** sekmesinde **uygulama içeriğini indir**' i seçin. İndirme seçeneklerinizi belirleyin ve ardından **İndir**' i seçin.

      ![İşlev uygulaması projesini indirin](./media/deployment-zip-push/download-project.png)

     İndirilen. zip dosyası,. zip Push dağıtımı kullanılarak işlev uygulamanıza yeniden yayımlanmak için doğru biçimde. Portal indirme işlemi, işlev uygulamanızı doğrudan Visual Studio 'da açmak için gereken dosyaları da ekleyebilir.

+ **REST API 'Leri kullanma:**

    Projenizden dosyaları indirmek için aşağıdaki dağıtımı al API 'sini kullanın `<function_app>` : 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Dahil olmak üzere `/site/wwwroot/` , ZIP dosyanızın tüm siteyi değil yalnızca uygulama proje dosyalarını içerdiğinden emin olur. Zaten Azure 'da oturum açmadıysanız bunu yapmanız istenir.  

Ayrıca, bir GitHub deposundan bir. zip dosyası indirebilirsiniz. Bir GitHub deposunu. zip dosyası olarak indirdiğinizde GitHub, dal için ek bir klasör düzeyi ekler. Bu ek klasör düzeyi, GitHub 'dan indirdiğiniz. zip dosyasını doğrudan dağıtabileceğiniz anlamına gelir. İşlev uygulamanızı sürdürmek için bir GitHub deposu kullanıyorsanız, uygulamanızı dağıtmak için [sürekli tümleştirme](functions-continuous-deployment.md) kullanmanız gerekir.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Azure CLI'yi kullanarak dağıtma

Azure CLı 'yi bir anında iletme dağıtımı tetikleyebilmeniz için kullanabilirsiniz. [Az functionapp Deployment Source config-ZIP](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) komutunu kullanarak bir. zip dosyasını işlev uygulamanıza dağıtın. Bu komutu kullanmak için Azure CLı sürüm 2.0.21 veya sonraki bir sürümünü kullanmanız gerekir. Kullanmakta olduğunuz Azure CLı sürümünü görmek için `az --version` komutunu kullanın.

Aşağıdaki komutta `<zip_file_path>` yer tutucusunu,. zip dosyanızın konumunun yolu ile değiştirin. Ayrıca, `<app_name>` işlev uygulamanızın benzersiz adıyla değiştirin ve öğesini `<resource_group>` kaynak grubunuzun adıyla değiştirin.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Bu komut, indirilen. zip dosyasındaki proje dosyalarını Azure 'daki işlev uygulamanıza dağıtır. Ardından uygulamayı yeniden başlatır. Bu işlev uygulaması için dağıtımların listesini görüntülemek için REST API 'Lerini kullanmanız gerekir.

Yerel bilgisayarınızda Azure CLı kullanırken, `<zip_file_path>` bilgisayarınızdaki. zip dosyasının yoludur. Azure CLı [Azure Cloud Shell](../cloud-shell/overview.md)de çalıştırabilirsiniz. Cloud Shell kullandığınızda, önce Deployment. zip dosyanızı Cloud Shell ilişkili Azure dosyaları hesabına yüklemeniz gerekir. Bu durumda, `<zip_file_path>` Cloud Shell hesabınızın kullandığı depolama konumudur. Daha fazla bilgi için bkz. [Azure Cloud Shell dosyaları kalıcı hale](../cloud-shell/persisting-shell-storage.md)getirme.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Dağıtım paketinden işlevleri Çalıştır

İşlevlerinizi doğrudan dağıtım paketi dosyasından çalıştırmayı da tercih edebilirsiniz. Bu yöntem, dosyaları paketten işlev uygulamanızın dizinine kopyalama dağıtım adımını atlar `wwwroot` . Bunun yerine, paket dosyası Işlevler çalışma zamanına göre bağlanır ve `wwwroot` dizinin içeriği salt okunurdur.  

ZIP dağıtımı, işlev uygulaması ayarını bir değerine ayarlayarak etkinleştirebileceğiniz bu özellikle tümleştirilir `WEBSITE_RUN_FROM_PACKAGE` `1` . Daha fazla bilgi için bkz. [bir dağıtım paketi dosyasından Işlevlerinizi çalıştırma](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure İşlevleri için sürekli dağıtım](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
