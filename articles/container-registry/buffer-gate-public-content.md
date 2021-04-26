---
title: Özel kapsayıcı kayıt defterinde ortak içeriği yönetme
description: Docker Hub 'ından ve diğer ortak içeriklerden ortak görüntülerde bağımlılıkları yönetmek için Azure Container Registry uygulamalar ve iş akışları
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024747"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Azure Container Registry ortak içeriği yönetme

Bu makalede, Docker Hub 'daki kapsayıcı görüntüleri gibi genel içeriklerin kopyalarını sürdürmek için [Azure Container Registry](container-registry-intro.md) gibi yerel bir kayıt defteri kullanmak üzere yöntemlere ve iş akışlarına genel bakış sunulmaktadır. 


## <a name="risks-with-public-content"></a>Ortak içerikli riskler

Ortamınız ortak kapsayıcı görüntüleri, [HELI grafikleri](https://helm.sh/), [Açık ilke aracısı](https://www.openpolicyagent.org/) (Opa) ilkeleri veya diğer yapıtlar gibi ortak içeriklere yönelik bağımlılıklara sahip olabilir. Örneğin, hizmet yönlendirme için [NGINX](https://hub.docker.com/_/nginx) veya `docker build FROM alpine` doğrudan Docker Hub veya başka bir genel kayıt defteri 'nden görüntü çekerek çalıştırabilirsiniz. 

Doğru denetimler olmadan, ortak kayıt defteri içeriğine bağımlılıklar olması, görüntü geliştirme ve dağıtım iş akışlarınızla riskleri ortaya çıkarabilir. Riskleri azaltmak için, mümkün olduğunda ortak içeriğin yerel kopyalarını saklayın. Ayrıntılar için bkz. [kapsayıcı girişimi blogu](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Docker Hub ile kimlik doğrulama

İlk adım olarak, bir derleme veya dağıtım iş akışının parçası olarak Docker Hub 'ından ortak görüntüler çekiyorsanız, anonim bir çekme isteği yapmak yerine [bir Docker Hub hesabı kullanarak kimlik doğrulaması](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) yapmanızı öneririz.

Sık sık anonim çekme istekleri yaparken, bu hataları engellemek için Docker hata ile benzer `ERROR: toomanyrequests: Too Many Requests.` veya `You have reached your pull rate limit.` Bu hata için kimlik doğrulaması yapabilirsiniz.

> [!NOTE]
> 2 Kasım [2020 ' den itibaren geçerli olan](https://docs.docker.com/docker-hub/download-rate-limit) Docker Hub 'a yönelik anonim ve kimliği doğrulanmış Istekler, Docker Ücretsiz plan hesaplarından ve sırasıyla IP adresi ve DOCKER ID tarafından zorlanır. 
>
> Çekme isteklerinizin sayısını tahmin etmek için, bulut sağlayıcısı hizmetlerini kullanırken veya bir kurumsal NAT 'ın arkasında çalışırken birden çok Kullanıcı, IP adreslerinin bir alt kümesi olarak toplama bölümünde yer alan Docker Hub 'ına sunulur. Docker Hub 'a yapılan isteklere Docker ücretli hesap kimlik doğrulaması eklemek, hız sınırı azaltma nedeniyle olası hizmet kesintilerine engel olur.
>
> Ayrıntılar için bkz. [Docker fiyatlandırma ve abonelikler](https://www.docker.com/pricing) ve [Docker hizmet koşulları](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Docker Hub erişim belirteci

Docker Hub, Docker Hub 'da kimlik doğrulanırken Docker parolasının alternatifleri olarak [kişisel erişim belirteçlerini](https://docs.docker.com/docker-hub/access-tokens/) destekler. Docker Hub 'ından görüntü çekmesini sağlayan otomatik hizmetler için belirteçler önerilir. Farklı kullanıcılar veya hizmetler için birden çok belirteç oluşturabilir ve artık gerekli olmadığında belirteçleri iptal edebilirsiniz.

Belirteç kullanarak kimlik doğrulamak için `docker login` komut satırındaki parolayı atlayın. Parola istendiğinde, bunun yerine belirteci girin. Docker Hub hesabınızda iki öğeli kimlik doğrulamayı etkinleştirdiyseniz, Docker CLı 'dan oturum açarken bir kişisel erişim belirteci kullanmanız gerekir.

### <a name="authenticate-from-azure-services"></a>Azure hizmetlerinden kimlik doğrulama

App Service ve Azure Container Instances dahil olmak üzere çeşitli Azure Hizmetleri, kapsayıcı dağıtımları için Docker Hub gibi ortak kayıt defterlerinden görüntü çekmesini destekler. Docker Hub 'dan bir görüntü dağıtmanız gerekiyorsa, bir Docker Hub hesabı kullanarak kimlik doğrulaması yapmak için ayarları yapılandırmanızı öneririz. Örnekler:

**App Service**

* **Görüntü kaynağı**: Docker Hub
* **Depo erişimi**: özel
* **Oturum aç**: \<Docker Hub username>
* **Parola**: \<Docker Hub token>

Ayrıntılar için bkz. [Docker Hub kimliği doğrulanmış çeker App Service](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Azure Container Instances**

* **Görüntü kaynağı**: Docker Hub veya diğer kayıt defteri
* **Görüntü türü**: özel
* **Görüntü kayıt defteri oturum açma sunucusu**: Docker.io
* **Görüntü kayıt defteri Kullanıcı adı**: \<Docker Hub username>
* **Görüntü kayıt defteri parolası**: \<Docker Hub token>
* **Görüntü**: Docker.io/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Azure Container Registry 'ye görüntü aktarma
 
Ortak görüntülerin kopyalarını yönetmeye başlamak için, henüz yoksa bir Azure Container Registry oluşturabilirsiniz. [Azure CLI](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)veya diğer araçları kullanarak bir kayıt defteri oluşturun. 

Önerilen bir kerelik adım olarak, temel görüntüleri ve diğer ortak içerikleri Azure Container Registry 'nize [aktarın](container-registry-import-images.md) . Azure CLı 'deki [az ACR Import](/cli/azure/acr#az_acr_import) komutu, Docker Hub ve Microsoft Container Registry gibi ortak kayıt defterlerinden ve diğer özel kapsayıcı kayıt defterlerinden görüntü içeri aktarmayı destekler. 

`az acr import` Yerel bir Docker yüklemesi gerektirmez. Azure CLı yerel yüklemesiyle veya doğrudan Azure Cloud Shell ' de çalıştırabilirsiniz. Herhangi bir işletim sistemi türünün, çok mimarili görüntülerin veya HELI grafikleri gibi OCı yapıtlarının görüntülerini destekler.

Örnek:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

Kuruluşunuzun ihtiyaçlarına bağlı olarak, paylaşılan bir kayıt defterindeki adanmış bir kayıt defterine veya depoya aktarabilirsiniz.

## <a name="automate-application-image-updates"></a>Uygulama görüntüsü güncelleştirmelerini otomatikleştirin

Uygulama görüntülerinin geliştiricileri, kendi kendi denetimleri altındaki yerel içeriğe başvuruda bulunduğundan emin olmalıdır. Örneğin, `Docker FROM` bir Dockerfile içindeki bir ifade, ortak bir kayıt defteri yerine özel bir temel görüntü kayıt defterindeki bir görüntüye başvurmalıdır. 

Görüntü içeri aktarma üzerine genişleterek, temel görüntüler güncelleştirilirken uygulama görüntüsü derlemelerini otomatikleştirmek için bir [Azure Container Registry görevi](container-registry-tasks-overview.md) ayarlayın. Otomatik derleme görevi, hem [temel görüntü güncelleştirmelerini](container-registry-tasks-base-images.md) hem de [kaynak kodu güncelleştirmelerini](container-registry-tasks-overview.md#trigger-task-on-source-code-update)izleyebilir.

Ayrıntılı bir örnek için bkz. [Azure Container Registry görevlerle ortak Içeriği kullanma ve koruma](tasks-consume-public-content.md). 

> [!NOTE]
> Tek bir önceden yapılandırılmış görev, bağımlı bir temel görüntüye başvuran her uygulama görüntüsünü otomatik olarak yeniden oluşturabilir. 
 
## <a name="next-steps"></a>Sonraki adımlar
 
* Azure 'da kapsayıcı görüntülerini derlemek, çalıştırmak, göndermek ve bunlara yama yapmak için [ACR görevleri](container-registry-tasks-overview.md) hakkında daha fazla bilgi edinin.
* Temel görüntüleri ortamınıza güncelleştirmek üzere otomatik bir çalışma iş akışı için [Azure Container Registry görevlerle ortak Içeriği nasıl kullanacağınızı ve koruyaöğrenin](tasks-consume-public-content.md) . 
* Görüntü yapılarını ve güncelleştirmelerini otomatikleştirmeye yönelik daha fazla örnek için [ACR görev öğreticilerine](container-registry-tutorial-quick-task.md) bakın.
