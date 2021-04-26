---
title: Azure Dev Spaces için küme ayarlama çalışması
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces çalışması için bir Azure Kubernetes hizmet kümesi ayarlamayı açıklar
keywords: Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972688"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Azure Dev Spaces için küme ayarlama çalışması

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces, Kubernetes uygulamalarının hızla yinelenemez ve hata ayıklamanın yanı sıra Azure Kubernetes hizmeti (AKS) kümesinde ekibinizle işbirliği yapmak için birden çok yol sağlar. Tek yönlü, [Hizmetleri doğrudan kümenizde çalıştırabilmeniz][how-it-works-up] ve [ek ağ ve yönlendirme özellikleri][how-it-works-routing]kullanabilmeniz için aks kümenizde Azure dev Spaces etkinleştirmektir. Bu makalede, kümenizi hazırlarken ve Azure Dev Spaces etkinleştirdiğinizde ne olacağı açıklanır.

## <a name="prepare-your-aks-cluster"></a>AKS kümenizi hazırlama

AKS kümenizi geliştirme alanları için hazırlamak üzere AKS kümenizin [Azure dev Spaces tarafından desteklenen][supported-regions] bir bölgede olduğunu ve Kubernetes 1.10.3 veya üstünü çalıştırdığınızı doğrulayın. ' İ çalıştırarak Azure CLı 'dan Azure Dev Spaces etkinleştirebilirsiniz `az aks use-dev-spaces` .

AKS kümenizde Azure Dev Spaces etkinleştirildiğinde, kümeniz için denetleyiciyi yükler. Denetleyici AKS kümenizin dışında bulunur. İstemci tarafı araçları ve AKS kümesi arasındaki davranışı ve iletişimi yürütür. Etkinleştirildikten sonra, istemci tarafı araçları kullanarak denetleyiciyle etkileşime geçebilirsiniz.

Denetleyici aşağıdaki eylemleri gerçekleştirir:

* Geliştirme alanı oluşturmayı ve seçimini yönetir.
* Uygulamanızın helk grafiğini yükleyip Kubernetes nesnelerini oluşturur.
* Uygulamanızın kapsayıcı görüntüsünü oluşturur.
* Uygulamanızı AKS 'e dağıtır.
* , Kaynak kodunuz değiştiğinde Artımlı derlemeler ve yeniden başlatılır.
* Günlükleri ve HTTP izlemelerini yönetir.
* Stdout ve stderr 'i istemci tarafı araçlarına iletir.
* Bir boşluk içindeki uygulamalar için ve üst ve alt boşluklar arasında yönlendirmeyi yapılandırır.

Denetleyici, kümenizin dışındaki ayrı bir Azure kaynağıdır ve kümenizdeki kaynaklar için aşağıdakileri yapar:

* Geliştirme alanı olarak kullanılacak bir Kubernetes ad alanı oluşturur veya belirtir.
* Varsa *azds* adlı tüm Kubernetes ad alanını kaldırır ve yeni bir tane oluşturur.
* Bir Kubernetes Web kancası yapılandırması dağıtır.
* Bir Web kancası giriş sunucusu dağıtır.

AKS kümenizin diğer Azure Dev Spaces bileşenlerine hizmet çağrısı yapmak için kullandığı hizmet sorumlusunu kullanır.

![Kümeyi hazırlama Azure Dev Spaces](media/how-dev-spaces-works/prepare-cluster.svg)

Azure Dev Spaces kullanmak için en az bir dev alanı olmalıdır. Azure Dev Spaces, AKS kümenizdeki geliştirme alanları için Kubernetes ad alanlarını kullanır. Bir denetleyici yüklenirken, yeni bir Kubernetes ad alanı oluşturmanızı veya ilk geliştirme alanım olarak kullanılacak mevcut bir ad alanını seçmenizi ister. Varsayılan olarak, denetleyici mevcut *varsayılan* Kubernetes ad alanını ilk geliştirme alanınıza yükseltmeyi önerir.

Bir ad alanı bir geliştirme alanı olarak belirlendiyse, denetleyici *azds.io/Space=true* etiketini bir geliştirme alanı olarak tanımlamak için bu ad alanına ekler. Kümenizi hazırladıktan sonra oluşturduğunuz veya belirleyeceğiniz ilk geliştirme alanı varsayılan olarak seçilidir. Bir boşluk seçildiğinde, yeni iş yükleri oluşturmak için Azure Dev Spaces tarafından kullanılır.

Yeni dev alanları oluşturmak ve var olan geliştirme alanlarını kaldırmak için istemci tarafı araçları 'nı kullanabilirsiniz. Kubernetes kısıtlamasından dolayı *varsayılan* dev alanı kaldırılamaz. Denetleyici Ayrıca, *azds* adlı mevcut Kubernetes ad alanlarını kaldırır ve bu `azds` , istemci tarafı araçları tarafından kullanılan komutla çakışmaları ortadan kaldırır.

Kubernetes Web kancası giriş sunucusu, izleme dağıtımı sırasında üç kapsayıcıyla düğüm eklemek için kullanılır: bir devspaces-proxy kapsayıcısı, bir devspaces-proxy-init kapsayıcı ve bir devspaces oluşturma kapsayıcısı. **Bu kapsayıcıların üçü de AKS kümenizde kök erişimle çalışır.** Ayrıca, AKS kümenizin diğer Azure Dev Spaces bileşenlerine hizmet çağrısı yapmak için kullandığı hizmet sorumlusunu kullanır.

![Kubernetes Web kancası giriş sunucusu Azure Dev Spaces](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces-proxy kapsayıcısı, uygulama kapsayıcısının içindeki ve giden tüm TCP trafiğini işleyen ve yönlendirmeyi gerçekleştirmeye yardımcı olan bir sepet kapsayıcısıdır. Bazı boşluklar kullanılıyorsa devspaces-proxy kapsayıcısı reroutes HTTP iletileri. Örneğin, üst ve alt alanlarda uygulamalar arasında HTTP iletileri yönlendirmenize yardımcı olabilir. HTTP olmayan tüm trafik, devspaces-proxy değiştirilmeden geçer. Devspaces-proxy kapsayıcısı, tüm gelen ve giden HTTP iletilerini de günlüğe kaydeder ve bunları izleme olarak istemci tarafı araçlarına gönderir. Bu izlemeler daha sonra uygulamanın davranışını incelemek üzere geliştirici tarafından görüntülenebilir.

Devspaces-proxy-init kapsayıcısı, uygulamanızın kapsayıcısına alan hiyerarşisi temelinde ek yönlendirme kuralları ekleyen bir [init kapsayıcıdır](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) . Uygulama kapsayıcısının */etc/resolv.exe* dosyasını ve Iptables yapılandırmasını başlamadan önce güncelleştirerek yönlendirme kuralları ekler. */Etc/resolv.exe* güncelleştirmeleri, üst alanlarda hizmetlerin DNS çözümlemesine izin verir. Iptables yapılandırma güncelleştirmeleri, uygulamanın kapsayıcısının içindeki ve giden tüm TCP trafiğinin devspaces-proxy olmasına rağmen yönlendirilmesini güvence altına alınır. Devspaces tarafından yapılan tüm güncelleştirmeler-proxy-init, Kubernetes 'in eklediği kurallara ek olarak gerçekleşir.

Devspaces oluşturma kapsayıcısı bir başlangıç kapsayıcısıdır ve proje kaynak kodu ve Docker yuvası takılı olur. Proje kaynak kodu ve Docker erişimi, uygulama kapsayıcısının doğrudan Pod tarafından oluşturulmasına izin verir.

> [!NOTE]
> Azure Dev Spaces uygulamanızın kapsayıcısını derlemek ve çalıştırmak için aynı düğümü kullanır. Sonuç olarak, Azure Dev Spaces uygulamanızı oluşturmak ve çalıştırmak için bir dış kapsayıcı kayıt defteri gerekmez.

Kubernetes Web kancası giriş sunucusu, AKS kümesinde oluşturulan yeni Pod 'ları dinler. Bu Pod, *azds.io/Space=true* etiketi ile herhangi bir ad alanına dağıtılmışsa, bu Pod öğesini ek kapsayıcılar ile çıkartır. Devspaces oluşturma kapsayıcısı yalnızca uygulamanın kapsayıcısı istemci tarafı araçları kullanılarak çalıştırıldığında eklenir.

AKS kümenizi hazırladıktan sonra, geliştirme alanınızda kodunuzu hazırlamak ve çalıştırmak için istemci tarafı araçları 'nı kullanabilirsiniz.

## <a name="client-side-tooling"></a>İstemci tarafı araçları

İstemci tarafı araçları, kullanıcının şunları yapmasına izin verir:
* Uygulama için bir Dockerfile, Held grafik ve Azure Dev Spaces yapılandırma dosyası oluşturun.
* Üst ve alt dev alanları oluşturun.
* Denetleyiciyi, uygulamanızı derleyip başlatacak şekilde söyleyin.

Uygulamanız çalışırken, istemci tarafı araçları da:
* AKS 'de çalışan uygulamanızdan stdout ve stderr 'leri alır ve görüntüler.
* Http:/localhost. kullanarak uygulamanıza Web erişimi sağlamak için [bağlantı noktası iletme](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) kullanır \/
* AKS 'teki çalışan uygulamanıza bir hata ayıklayıcı ekler.
* Artımlı derlemeler için bir değişiklik algılandığında hızlı yinelemeye izin veren kaynak kodu dev alanınızda eşitler.
* Geliştirici makinenize doğrudan AKS kümenize bağlanmanızı sağlar.

Komutun bir parçası olarak komut satırından istemci tarafı araçları ' nı kullanabilirsiniz `azds` . Ayrıca, ile birlikte istemci tarafı araçları 'nı kullanabilirsiniz:

* [Azure dev Spaces uzantısını](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)kullanarak Visual Studio Code.
* Azure geliştirme iş yüküyle Visual Studio.

## <a name="next-steps"></a>Sonraki adımlar

Geliştirme alanınızda kodunuzu hazırlamak ve çalıştırmak için istemci tarafı araçları 'nı kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure dev Spaces bir proje hazırlama nasıl çalışır][how-it-works-prep].


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service