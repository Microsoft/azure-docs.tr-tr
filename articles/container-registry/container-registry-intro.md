---
title: Yönetilen kapsayıcı kayıt defterleri
description: Bulut tabanlı, yönetilen, özel Docker kayıt defterleri sağlayan Azure Container Kayıt Defteri hizmetine giriş.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b5c81d8b2e6d7eac2dcd9070bf1f448340ea1a18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341274"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure'da özel Docker kapsayıcısı kayıt defterlerine giriş

Azure Container Registry, açık kaynaklı Docker kayıt defteri 2,0 ' i temel alan yönetilen, özel bir Docker kayıt defteri hizmetidir. Özel Docker kapsayıcı görüntülerinizi ve ilgili yapıtları depolamak ve yönetmek için Azure Container kayıt defterleri oluşturun ve saklayın.

Azure Container Registry 'yi mevcut kapsayıcı geliştirme ve dağıtım işlem hatlarınız ile birlikte kullanın veya Azure 'da kapsayıcı görüntüleri oluşturmak için Azure Container Registry görevleri kullanın. İstek üzerine derleyin veya kaynak kodu işleme ve temel görüntü güncelleştirmeleri gibi tetikleyicilerle yapıları tamamen otomatikleştirin.

Docker ve kayıt defteri kavramları hakkında daha fazla bilgi için bkz. [Docker 'a genel bakış](https://docs.docker.com/engine/docker-overview/) ve kayıt [defterleri, depolar ve görüntüler hakkında](container-registry-concepts.md).

## <a name="use-cases"></a>Uygulama alanları

Azure kapsayıcısı kayıt defterinden çeşitli dağıtım hedeflerine görüntü çekme:

* [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) ve [Docker Swarm](https://docs.docker.com/get-started/swarm-deploy/) dahil olmak üzere konak kümeleri arasında kapsayıcı haline getirilmiş uygulamaları yöneten **ölçeklenebilir düzenleme sistemleri**.
* [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) ve diğerleri gibi uygun ölçekte uygulama oluşturulmasını ve çalıştırılmasını destekleyen **Azure hizmetleri**.

Geliştiriciler bir kapsayıcı geliştirme iş akışı kapsamında bir kapsayıcı kayıt defterine de öğe itebilir. Örneğin, [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) veya [Jenkins](https://jenkins.io/)gibi bir sürekli tümleştirme ve teslim aracından bir kapsayıcı kayıt defteri hedefleyin.

ACR görevlerini, temel görüntülerinin güncelleştirildiği sırada uygulama görüntülerini otomatik olarak yeniden oluşturmak üzere yapılandırın veya ekibiniz bir git deposuna kod onayladığınızda görüntü derlemelerini otomatikleştirin. Bulutta paralel olarak birden çok kapsayıcı görüntüsünü oluşturma, test etme ve düzeltme eki uygulama işlemlerini otomatik hale getirmek için çok adımlı Görevler oluşturun.

Azure, Azure Container kayıt zincirlerinizi yönetmek için Azure Command-Line arabirimi, Azure portal ve API desteği dahil olmak üzere araç sağlar. İsteğe bağlı olarak [Visual Studio Code Için Docker uzantısını](https://code.visualstudio.com/docs/azure/docker) ve Azure Container Registry 'larınız ile birlikte çalışmak Için [Azure hesap](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) uzantısını yükler. Azure Container Registry 'ye görüntü çekme ve gönderme veya ACR görevlerini Visual Studio Code.

## <a name="key-features"></a>Önemli özellikler

* **Kayıt defteri hizmet katmanları** -Azure aboneliğinizde bir veya daha fazla kapsayıcı kayıt defteri oluşturun. Kayıt defterleri üç katmanda kullanılabilir: [temel, standart ve Premium](container-registry-skus.md), her biri Web kancası tümleştirmesini, Azure Active Directory ile kayıt defterini doğrulamayı ve silme işlevini destekler. Kapsayıcı görüntülerinizin yerel, kapalı bir ağda depolanmasının avantajlarından yararlanmak için dağıtımlarınızla aynı Azure konumunda bir kayıt defteri oluşturun. Gelişmiş çoğaltma ve kapsayıcı görüntüsü dağıtma senaryoları için Premium kayıt defterlerinin [coğrafi çoğaltma](container-registry-geo-replication.md) özelliğini kullanın. 

* **Güvenlik ve erişim** -Azure CLI veya standart komutunu kullanarak bir kayıt defterinde oturum açın `docker login` . Azure Container Registry, kapsayıcı görüntülerini HTTPS üzerinden aktarır ve istemci bağlantılarının güvenliğini sağlamak için TLS 'yi destekler. 

  > [!IMPORTANT]
  > Azure Container Registry 13 Ocak 2020 tarihinden itibaren, sunuculardan ve uygulamalardan gelen tüm güvenli bağlantıların TLS 1,2 kullanması gerekir. Herhangi bir yeni Docker istemcisini (sürüm 18.03.0 veya üzeri) kullanarak TLS 1,2 'yi etkinleştirin. TLS 1,0 ve 1,1 desteği kullanımdan kaldırılacak. 

  Bir Azure kimliği, Azure Active Directory ile desteklenen bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md)veya sağlanmış bir yönetici hesabı kullanarak bir kapsayıcı kayıt defterine [erişimi kontrol](container-registry-authentication.md) edersiniz. Kullanıcıları veya sistemleri bir kayıt defterine ayrıntılı izinler atamak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanın.

  Premium hizmet katmanının güvenlik özellikleri, kayıt defterine erişimi kısıtlamak için görüntü etiketi imzalama ve [güvenlik duvarları ve sanal ağlar (Önizleme)](container-registry-vnet.md) için [içerik güveni](container-registry-content-trust.md) içerir. Azure Güvenlik Merkezi, bir görüntü kayıt defterine gönderildiğinde [görüntüleri taramak](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) için isteğe bağlı olarak Azure Container Registry tümleştirilir.

* **Desteklenen görüntüler ve yapıtlar** -bir depoda gruplandırılır, her görüntü Docker uyumlu kapsayıcının salt okunurdur. Azure kapsayıcısı kayıt defterleri hem Windows hem de Linux görüntüleri içerebilir. Tüm kapsayıcı dağıtımlarınız için görüntü adlarını siz denetlersiniz. Bir depoya görüntü itmek ya da bir depodan görüntü çekmek için standart [Docker komutlarını](https://docs.docker.com/engine/reference/commandline/) kullanın. Docker kapsayıcı görüntülerine ek olarak, Azure Container Registry, [Held grafikleri](container-registry-helm-repos.md) ve [Açık KAPSAYıCı girişimi (OCI) görüntü biçimi belirtimine](https://github.com/opencontainers/image-spec/blob/master/spec.md)göre oluşturulan görüntüler gibi [ilgili içerik biçimlerini](container-registry-image-formats.md) depolar.

* **Otomatik görüntü yapıları** -Azure 'da görüntü oluşturma, test etme, iletme ve dağıtma işlemlerini kolaylaştırmak Için [Azure Container Registry görevleri](container-registry-tasks-overview.md) (ACR görevleri) kullanın. Örneğin, Azure 'a işlemleri boşaltarak geliştirme iç döngüsünü buluta genişletmek için ACR görevlerini kullanın `docker build` . Kapsayıcı işletim sisteminizi ve çerçeve düzeltme eki uygulama işlem hattınızı otomatikleştirmek ve ekibiniz kaynak denetiminde kod yürüttüğünde otomatik olarak görüntü oluşturmak için oluşturma görevleri yapılandırın.

  [Çok adımlı görevler](container-registry-tasks-overview.md#multi-step-tasks) , bulutta kapsayıcı görüntüleri oluşturmak, test etmek ve düzeltme eki uygulamak için adım tabanlı görev tanımı ve yürütme sağlar. Görev adımları, tek tek kapsayıcı derleme ve gönderme işlemlerini tanımlar. Ayrıca, her adımın kapsayıcıyı kendi yürütme ortamı olarak kullanmasıyla bir veya daha fazla kapsayıcının yürütülmesini de tanımlayabilirler.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure portalını kullanarak kapsayıcı kayıt defteri oluşturma](container-registry-get-started-portal.md)
* [Azure CLI’yı kullanarak kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md)
* [ACR görevleriyle kapsayıcı derlemelerini ve bakımını otomatikleştirin](container-registry-tasks-overview.md)