---
title: Azure Dev Spaces kullanarak Kubernetes 'te uygulama dağıtma
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Azure Dev Spaces ile AKS 'de mikro hizmet dağıtma
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 58cb436a7c2ba0d6a49f5138e46a1f92cdc97a94
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815832"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Hızlı başlangıç: Azure Dev Spaces kullanarak Kubernetes 'te uygulama geliştirme
Bu kılavuzda şunların nasıl yapıldığını öğreneceksiniz:

- Azure’da yönetilen bir Kubernetes ile Azure Dev Spaces’ı ayarlayın.
- Komut satırını kullanarak kapsayıcılarda kod geliştirin ve çalıştırın.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/free) oluşturabilirsiniz.
- [Yüklü Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes hizmet kümesi oluşturma

[Desteklenen bir bölgede][supported-regions]aks kümesi oluşturmanız gerekir. Aşağıdaki komutlar *Myresourcegroup* adlı bir kaynak grubu ve *myaks*adlı bir aks kümesi oluşturur.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS kümenizde Azure Dev Spaces etkinleştirme

AKS kümenizde dev alanlarını etkinleştirmek ve istemleri izlemek için `use-dev-spaces` komutunu kullanın. Aşağıdaki komut *Myresourcegroup* grubundaki *myaks* kümesinde dev alanlarını etkinleştiriyor ve *varsayılan* bir dev alanı oluşturuyor.

> [!NOTE]
> @No__t-0 komutu, zaten yüklenmemişse Azure Dev Spaces CLı 'yi de yükler. Azure Dev Spaces CLı 'yi Azure Cloud Shell yükleyemezsiniz.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Örnek uygulama kodu al

Bu makalede, Azure Dev Spaces kullanmayı göstermek için [Azure dev Spaces örnek uygulamayı](https://github.com/Azure/dev-spaces) kullanırsınız.

Uygulamayı GitHub 'dan kopyalayın ve *dev-Spaces/Samples/NodeJS/Başlarken/webön uç* dizinine gidin:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Uygulamayı hazırlama

Uygulamanızı Azure Dev Spaces çalıştırmak için bir Dockerfile ve HELI grafiğinin olması gerekir. [Java][java-quickstart], [.NET Core][netcore-quickstart]ve [Node. js][nodejs-quickstart]gibi bazı dillerde, Azure dev Spaces istemci araçları, ihtiyacınız olan tüm varlıkları oluşturabilir. Go, PHP ve Python gibi diğer birçok dil için, istemci araçları, geçerli bir Dockerfile sağlayabilmeniz koşuluyla Held grafiğini oluşturabilir.

@No__t-0 komutunu kullanarak Kubernetes 'te uygulamayı çalıştırmak için Docker ve helk grafik varlıkları oluşturun:

```cmd
azds prep --public
```

Docker ve hele grafik varlıklarını doğru şekilde oluşturmak için, *dev-Spaces/Samples/NodeJS/alma-başlatma/webön uç* dizininden `prep` komutunu çalıştırmanız gerekir.

## <a name="build-and-run-code-in-kubernetes"></a>Kubernetes'de kodu oluşturma ve çalıştırma

@No__t-0 komutunu kullanarak kodu AKS 'de derleyin ve çalıştırın:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

@No__t-0 komutunun çıktısında gösterildiği genel URL 'YI açarak hizmetin çalıştığını görebilirsiniz. Bu örnekte, genel URL *http://webfrontend.1234567890abcdef1234.eus.azds.io/* ' dir.

> [!NOTE]
> @No__t-0 çalıştırılırken hizmetinize gittiğinizde, HTTP isteği izlemeleri de `azds up` komutunun çıktısında görüntülenir. Bu izlemeler, hizmetinizde sorun gidermenize ve hata ayıklamanıza yardımcı olabilir. @No__t-1 çalıştırırken `--disable-http-traces` kullanarak bu izlemeleri devre dışı bırakabilirsiniz.

*Ctrl + c*kullanarak `azds up` komutunu durdurursanız, hizmet aks 'de çalışmaya devam edecektir ve genel URL kullanılabilir kalır.

## <a name="update-code"></a>Kodu güncelleştirme

Hizmetinizin güncelleştirilmiş bir sürümünü dağıtmak için, projenizdeki herhangi bir dosyayı güncelleştirebilir ve `azds up` komutunu yeniden çalıştırabilirsiniz. Örnek:

1. @No__t-0 hala çalışıyorsa *Ctrl + c*tuşlarına basın.
1. [@No__t-1 ' de satır 13 '](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) ü güncelleştir:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Yaptığınız değişiklikleri kaydedin.
1. @No__t-0 komutunu yeniden çalıştırın:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Çalışan hizmetinize gidin ve değişikliklerinizi gözlemleyin.
1. @No__t-1 komutunu durdurmak için *Ctrl + c* tuşlarına basın.

## <a name="clean-up-your-azure-resources"></a>Azure kaynaklarınızı Temizleme

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces birden çok kapsayıcı genelinde daha karmaşık uygulamalar geliştirmenize nasıl yardımcı olduğunu ve farklı alanlarda kodunuzun farklı sürümleriyle veya dallarıyla çalışarak işbirliğine dayalı geliştirmeyi nasıl kolaylaştırabileceğinizi öğrenin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces 'de takım geliştirme][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations