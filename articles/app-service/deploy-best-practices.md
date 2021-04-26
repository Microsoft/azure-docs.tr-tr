---
title: Dağıtım en iyi uygulamaları
description: Azure App Service dağıtmaya yönelik anahtar mekanizmaları hakkında bilgi edinin. Dile özgü önerileri ve diğer uyarıları bulun.
keywords: Azure App Service, Web uygulaması, dağıtma, dağıtım, işlem hatları, derleme
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: ac4e8d93e2e729db387b0f9cf81017b6c58b6750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951343"
---
# <a name="deployment-best-practices"></a>Dağıtım En Iyi uygulamaları

Her geliştirme ekibinin, her türlü bulut hizmetinde verimli bir dağıtım işlem hattı uygulamayı zorlaştırmaları için benzersiz gereksinimleri vardır. Bu makalede, App Service dağıtım kaynakları, derleme işlem hatları ve dağıtım mekanizmaları için dağıtmanın üç ana bileşeni tanıtılmaktadır. Bu makalede ayrıca belirli dil yığınları için bazı en iyi uygulamalar ve ipuçları ele alınmaktadır.

## <a name="deployment-components"></a>Dağıtım bileşenleri

### <a name="deployment-source"></a>Dağıtım kaynağı

Dağıtım kaynağı, uygulama kodunuzun konumudur. Üretim uygulamaları için dağıtım kaynağı genellikle [GitHub, BitBucket veya Azure Repos](deploy-continuous-deployment.md)gibi sürüm denetimi yazılımıyla barındırılan bir depodur. Geliştirme ve test senaryolarında, dağıtım kaynağı [Yerel makinenizde bir proje](deploy-local-git.md)olabilir. App Service [, OneDrive ve Dropbox klasörlerini](deploy-content-sync.md) de dağıtım kaynakları olarak destekler. Bulut klasörleri App Service kullanmaya başlamanızı kolaylaştırırken, genellikle bu kaynak kurumsal düzeyde üretim uygulamaları için kullanılması önerilmez. 

### <a name="build-pipeline"></a>Derleme İşlem Hattı

Bir dağıtım kaynağına karar verdikten sonra bir sonraki adımınız bir yapı işlem hattı seçmek olur. Derleme işlem hattı kaynak kodunuzu dağıtım kaynağından okur ve uygulamayı bir çalıştırılabilir durumda almak için bir dizi adımı (kodu derleme, HTML ve JavaScript 'i çalıştırma, testleri çalıştırma ve paketleme bileşenleri) yürütür. Yapı işlem hattı tarafından yürütülen belirli komutlar, dil yığınınıza bağlıdır. Bu işlemler Azure Pipelines gibi bir yapı sunucusunda yürütülebilir veya yerel olarak yürütülür.

### <a name="deployment-mechanism"></a>Dağıtım mekanizması

Dağıtım mekanizması, oluşturulan uygulamanızı Web uygulamanızın */Home/site/Wwwroot* dizinine koymak için kullanılan eylemdir. */Wwwroot* dizini, Web uygulamanızın tüm örnekleri tarafından paylaşılan bağlı bir depolama konumudur. Dağıtım mekanizması uygulamanızı bu dizine yerleştiriyorsa, örneklerinizin yeni dosyaları eşitlemek için bir bildirim alır. App Service aşağıdaki dağıtım mekanizmalarını destekler:

- Kudu uç noktaları: [kudu](https://github.com/projectkudu/kudu/wiki) , Windows App Service 'de ayrı bir işlem olarak ve Linux App Service ikinci bir kapsayıcı olarak çalışan açık kaynaklı geliştirici üretkenlik aracıdır. Kudu sürekli dağıtımları işler ve dağıtım için zipdeploy gibi HTTP uç noktaları sağlar.
- FTP ve WebDeploy: [sitenizi veya Kullanıcı kimlik bilgilerinizi](deploy-configure-credentials.md)kullanarak FTP veya WebDeploy [aracılığıyla](deploy-ftp.md) dosya yükleyebilirsiniz. Bu mekanizmalar kudu 'ye gitmez.  

Azure Pipelines, Jenkins ve düzenleyici eklentileri gibi dağıtım araçları bu dağıtım mekanizmalarından birini kullanır.

## <a name="use-deployment-slots"></a>Dağıtım yuvalarını kullanma

Mümkün olduğunda, yeni bir üretim derlemesi dağıttığınızda [dağıtım yuvalarını](deploy-staging-slots.md) kullanın. Standart bir App Service planı katmanını veya daha iyi bir şekilde kullanırken, uygulamanızı bir hazırlama ortamına dağıtabilir, değişikliklerinizi doğrulayabilir ve duman testleri yapabilirsiniz. Hazırsanız, hazırlama ve üretim yuvalarınızı değiştirebilirsiniz. Değiştirme işlemi, gerekli çalışan örneklerini üretim ölçeklendirmenize uyacak şekilde çarpıtılarak kapalı kalma süresini ortadan kaldırır.

### <a name="continuously-deploy-code"></a>Kodu sürekli dağıt

Projenizde test, QA ve hazırlama için dallar belirlendiyse, bu dalların her biri sürekli olarak bir hazırlama yuvasına dağıtılmalıdır. (Bu, [Gitflow tasarımı](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)olarak bilinir.) Bu, paydaşlarınızın dalı kolayca değerlendirmesine ve test etmesine olanak tanır. 

Sürekli dağıtım, üretim yuvalarınız için asla etkinleştirilmemelidir. Bunun yerine, üretim dalınızın (genellikle ana) üretim dışı bir yuvaya dağıtılması gerekir. Temel dalı serbest bırakmaya hazırsanız, uygulamayı üretim yuvasına değiştirin. Üretime dönüştürme (üretime dağıtmak yerine), kapalı kalma süresini engeller ve yeniden takas yaparak değişiklikleri geri almanıza olanak sağlar. 

![Geliştirme, hazırlama ve ana dallar ile dağıtıldığı Yuvalar arasındaki akışı gösteren diyagram.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Kapsayıcıları sürekli dağıt

Docker veya diğer kapsayıcı kayıt defterlerinden özel kapsayıcılar için, görüntüyü bir hazırlama yuvasına dağıtın ve kapalı kalma süresini engellemek için üretime değiştirin. Görüntüyü bir kapsayıcı kayıt defterine göndermeniz ve WebApp üzerinde Image etiketini güncelleştirmeniz gerektiğinden, Otomasyon kod dağıtımından daha karmaşıktır.

Bir yuvaya dağıtmak istediğiniz her dal için, dala yapılan her bir yürütmede aşağıdakini yapmak üzere Otomasyonu ayarlayın.

1. **Görüntüyü derleyin ve etiketleyin**. Yapı ardışık düzeninin bir parçası olarak, görüntüyü git işleme KIMLIĞI, zaman damgası veya diğer tanımlanabilir bilgilerle etiketleyin. Varsayılan "en son" etiketini kullanmak en iyisidir. Aksi halde, şu anda hangi kodun dağıtıldığını izlemek zordur, bu da hata ayıklamayı çok daha zordur.
1. **Etiketli görüntüyü gönderin**. Görüntü oluşturulup etiketledikten sonra, işlem hattı görüntüyü kapsayıcı kayıt defterimize iter. Sonraki adımda, dağıtım yuvası etiketli görüntüyü kapsayıcı kayıt defterinden çeker.
1. **Yeni görüntü etiketiyle birlikte dağıtım yuvasını güncelleştirin**. Bu özellik güncelleştirilirken, site otomatik olarak yeniden başlatılır ve yeni kapsayıcı görüntüsünü çeker.

![Yuva kullanımı görseli](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Yaygın Otomasyon çerçeveleri için aşağıda örnek verilmiştir.

### <a name="use-azure-devops"></a>Azure DevOps kullanma

App Service, dağıtım merkezi aracılığıyla kapsayıcılar için [yerleşik sürekli teslimi](deploy-continuous-deployment.md) vardır. [Azure Portal](https://portal.azure.com/) uygulamanıza gidin ve **dağıtım** altındaki **Dağıtım Merkezi** ' ni seçin. Deponuzu ve dalınızı seçmek için yönergeleri izleyin. Bu işlem, seçili dala yeni işlemeler gönderildiğinde kapsayıcıyı otomatik olarak oluşturmak, etiketlemek ve dağıtmak için bir DevOps derleme ve sürüm işlem hattı yapılandırır.

### <a name="use-github-actions"></a>GitHub eylemlerini kullanma

Ayrıca, kapsayıcı dağıtımınızı [GitHub eylemleri ile](./deploy-ci-cd-custom-container.md)otomatikleştirebilir.  Aşağıdaki iş akışı dosyası, kapsayıcıyı işlem KIMLIĞIYLE oluşturup etiketleyerek, kapsayıcıyı bir kapsayıcı kayıt defterine ittir ve belirtilen site yuvasını yeni resim etiketiyle güncelleştirir.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Diğer otomasyon sağlayıcılarını kullanma

Daha önce listelenen adımlar, Circlecı veya Travis CI gibi diğer otomasyon yardımcı programları için geçerlidir. Ancak, son adımda dağıtım yuvalarını yeni görüntü etiketleriyle güncelleştirmek için Azure CLı 'yi kullanmanız gerekir. Automation betiğinizdeki Azure CLı 'yi kullanmak için, aşağıdaki komutu kullanarak bir hizmet sorumlusu oluşturun.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Betiğinizdeki ile oturum açın `az login --service-principal` , ana bilgisayarın bilgilerini sağlar. Daha sonra `az webapp config container set` kapsayıcı adını, etiketi, kayıt defteri URL 'sini ve kayıt defteri parolasını ayarlamak için öğesini kullanabilirsiniz. Kapsayıcı CI işleminizi oluşturmak için kullanabileceğiniz bazı yararlı bağlantılar aşağıda verilmiştir.

- [Azure CLı 'da daire CI üzerinde oturum açma](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Language-Specific konuları

### <a name="java"></a>Java

JAR uygulamalarını dağıtmak için kudu [zipdeploy/](deploy-zip.md) API ve War uygulamaları için [wardeploy/](deploy-zip.md#deploy-war-file) API kullanın. Jenkins kullanıyorsanız, bu API 'Leri doğrudan dağıtım aşamasınca kullanabilirsiniz. Daha fazla bilgi için [bu makaleye](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli) bakın.

### <a name="node"></a>Düğüm

Varsayılan olarak kudu, düğüm uygulamanız () için derleme adımlarını yürütür `npm install` . Azure DevOps gibi bir yapı hizmeti kullanıyorsanız kudu derlemesi gereksizdir. Kudu derlemesini devre dışı bırakmak için değeri olan bir uygulama ayarı oluşturun `SCM_DO_BUILD_DURING_DEPLOYMENT` `false` .

### <a name="net"></a>.NET 

Varsayılan olarak, kudu .NET uygulamanız () için derleme adımlarını yürütür `dotnet build` . Azure DevOps gibi bir yapı hizmeti kullanıyorsanız kudu derlemesi gereksizdir. Kudu derlemesini devre dışı bırakmak için değeri olan bir uygulama ayarı oluşturun `SCM_DO_BUILD_DURING_DEPLOYMENT` `false` .

## <a name="other-deployment-considerations"></a>Diğer dağıtım konuları

### <a name="local-cache"></a>Yerel önbellek

Azure App Service içerik Azure Storage 'da depolanır ve bir içerik paylaşımında dayanıklı bir biçimde ortaya çıkmış olur. Ancak, bazı uygulamaların yüksek kullanılabilirliğe sahip yalnızca yüksek performanslı, salt okunurdur bir içerik deposu olması yeterlidir. Bu uygulamalar, [Yerel önbellek](overview-local-cache.md)kullanmanın avantajlarından yararlanabilir. WordPress gibi içerik yönetimi siteleri için yerel önbellek önerilmez.

Kapalı kalma süresini engellemek için her zaman yerel önbelleği [dağıtım yuvalarıyla](deploy-staging-slots.md) birlikte kullanın. Bu özellikleri birlikte kullanma hakkında bilgi için [Bu bölüme](overview-local-cache.md#best-practices-for-using-app-service-local-cache) bakın.

### <a name="high-cpu-or-memory"></a>Yüksek CPU veya bellek

App Service planınız kullanılabilir CPU veya belleğin %90 ' den fazla kullanılıyorsa, temeldeki sanal makinede dağıtımınızı işleme sorunu olabilir. Bu durumda, dağıtımı gerçekleştirmek için örnek sayınız geçici olarak ölçeklendirin. Dağıtım tamamlandıktan sonra, örnek sayısını önceki değerine döndürebilirsiniz.

En iyi uygulamalar hakkında daha fazla bilgi için [App Service tanılama](./overview-diagnostics.md) ' yı ziyaret ederek kaynağına özgü eyleme dönüştürülebilir en iyi yöntemleri bulabilirsiniz.

- [Azure Portal](https://portal.azure.com)Web uygulamanıza gidin.
- App Service Tanılama ' yı açan sol gezinmede **sorunları Tanıla ve çöz** ' e tıklayın.
- **En Iyi Yöntemler** giriş sayfası kutucuğu seçin.
- En iyi yapılandırma uygulamalarına göre uygulamanızın geçerli durumunu görüntülemek için en iyi yapılandırmanın kullanılabilirlik & performansı veya **en iyi** uygulamaları **Için en iyi yöntemler** ' e tıklayın.

Bu bağlantıyı, kaynağınız için App Service tanılamayı doğrudan açmak için de kullanabilirsiniz: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .