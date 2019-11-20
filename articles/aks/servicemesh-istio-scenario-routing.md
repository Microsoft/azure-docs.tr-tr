---
title: Azure Kubernetes Service (aks) içinde istio ile akıllı yönlendirme ve kanarya yayınları
description: Azure Kubernetes Service (aks) kümesinde akıllı yönlendirme ve kanarya yayınları dağıtma sağlamak için ampo 'ı nasıl kullanacağınızı öğrenin
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 48daf2be4a05922982479a86e6574f3aa85d2130
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530296"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (aks) içinde istio ile akıllı yönlendirme ve kanarya sürümlerini kullanma

[Istio][istio-github] , bir Kubernetes kümesindeki mikro hizmetlerde anahtar bir işlevsellik kümesi sağlayan açık kaynaklı bir hizmet kafesidir. Bu özellikler trafik yönetimi, hizmet kimliği ve güvenlik, ilke zorlama ve Observability içerir. Istio hakkında daha fazla bilgi için resmi [nedir?][istio-docs-concepts] belgesine bakın.

Bu makalede, Istio 'nun trafik yönetimi işlevselliğinin nasıl kullanılacağı gösterilmektedir. Akıllı yönlendirme ve kanarya sürümlerini araştırmak için örnek bir aks oylama uygulaması kullanılır.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Uygulamayı dağıtma
> * Uygulamayı güncelleştirme
> * Uygulamanın kanarya bir sürümünü kullanıma alma
> * Dağıtımı sonlandırma

## <a name="before-you-begin"></a>Başlamadan önce

> [!NOTE]
> Bu senaryo, Istio sürüm `1.3.2` karşı test edilmiştir.

Bu makalede açıklanan adımlarda, RBAC etkinleştirilmiş bir AKS kümesi (Kubernetes `1.13` ve üzeri) oluşturdunuz ve kümeyle `kubectl` bir bağlantı oluşturmuş olduğunuz varsayılır. Ayrıca, kümenizde yüklü olan Ida gerekir.

Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa, aks [hızlı][aks-quickstart] başlangıcı ' na bakın ve [aks kılavuzuna Istio 'yu yükleyebilirsiniz][istio-install] .

## <a name="about-this-application-scenario"></a>Bu uygulama senaryosu hakkında

Örnek AKS oylama uygulaması, kullanıcılara iki oylama seçeneği (**kediler** veya **köpekler**) sağlar. Her seçenek için oy sayısını sürekli olarak sürdüren bir depolama bileşeni vardır. Ayrıca, her bir seçenek için oy saçılması etrafında ayrıntılar sağlayan bir analiz bileşeni de vardır.

Bu uygulama senaryosunda, analiz bileşeninin oylama uygulamasının ve sürüm `1.0` sürüm `1.0` dağıtarak başlar. Analytics bileşeni, Oy sayısı için basit sayımlar sağlar. Oylama uygulaması ve analiz bileşeni, Reddir tarafından desteklenen depolama bileşeninin sürüm `1.0` ile etkileşime geçin.

Analiz bileşenini, sayımlar ve artık toplam ve yüzdeleri sağlayan `1.1` sürümüne yükseltirsiniz.

Bir kanarya sürümü aracılığıyla uygulamanın kullanıcı `2.0` test bir alt kümesi. Bu yeni sürüm, bir MySQL veritabanı tarafından desteklenen bir depolama bileşeni kullanır.

Sürüm `2.0`, kullanıcılarınızın alt kümesinde beklendiği gibi çalıştığından emin olduktan sonra, sürüm `2.0` tüm kullanıcılarınıza dağıtabilirsiniz.

## <a name="deploy-the-application"></a>Uygulamayı dağıtma

Uygulamayı Azure Kubernetes Service (AKS) kümenize dağıtarak başlayalım. Aşağıdaki diyagramda, bu bölümün sonuna kadar hangi çalışmaların çalıştığı gösterilmektedir-bu durum, çalışan ağ geçidi aracılığıyla hizmet verilen gelen isteklerle tüm bileşenlerin sürümü `1.0`:

![AKS oylama uygulama bileşenleri ve yönlendirme.](media/servicemesh/istio/scenario-routing-components-01.png)

Bu makaleyle birlikte izlemeniz gereken yapıtlar [Azure-Samples/aks-oylama-App][github-azure-sample] GitHub deposunda bulunabilir. Yapıtları indirebilir veya depoyu şu şekilde klonlayabilirsiniz:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

İndirilen/kopyalanan depolarda aşağıdaki klasöre geçin ve sonraki tüm adımları bu klasörden çalıştırın:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

İlk olarak, AKS kümenizde `voting` adlı örnek AKS oylama uygulaması için aşağıdaki gibi bir ad alanı oluşturun:

```azurecli
kubectl create namespace voting
```

Ad alanını `istio-injection=enabled` etiketleyin. Bu etiket, bu ad alanındaki tüm yığınlarınızın tüm yığınlarınıza otomatik olarak istik-proxy 'leri eklemesini sağlar.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Şimdi AKS oylama uygulaması için bileşenleri oluşturalım. Bu bileşenleri, önceki adımda oluşturulan `voting` ad alanında oluşturun.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Aşağıdaki örnek çıktıda oluşturulan kaynaklar gösterilmektedir:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> IBU, Pod ve hizmetler etrafında bazı özel gereksinimlere sahiptir. Daha fazla bilgi için bkz. [pods ve hizmet belgeleri Için Istio gereksinimleri][istio-requirements-pods-and-services].

Oluşturulan Pod 'leri görmek için [kubectl Get Pod][kubectl-get] komutunu aşağıdaki gibi kullanın:

```azurecli
kubectl get pods -n voting --show-labels
```

Aşağıdaki örnek çıktıda, `voting-app` Pod 'un üç örneği ve `voting-analytics` ve `voting-storage` pods 'nin tek bir örneği gösterilmektedir. Her birinin iki kapsayıcısı vardır. Bu kapsayıcılardan biri bileşendir ve diğeri `istio-proxy`.

```console
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Pod hakkındaki bilgileri görmek için, Pod `voting-analytics` seçmek üzere etiket seçicileri ile [kubectl açıklagöster][kubectl-describe] komutunu kullanacağız. Pod 'da bulunan iki kapsayıcının ayrıntılarını göstermek için çıktıyı filtreleyeceğiz:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Bu [ağ geçidini][istio-reference-gateway] ve [sanal hizmeti][istio-reference-virtualservice]oluşturana kadar oylama uygulamasına bağlanamazsınız. Bu Istio kaynakları, trafiği varsayılan Istio ağ geçidinden uygulamamıza yönlendirir.

> [!NOTE]
> **Ağ geçidi** , hizmet ağı tarafında gelen veya giden http ve TCP trafiğini alan bir bileşendir.
> 
> Bir **sanal hizmet** , bir veya daha fazla hedef hizmet için bir yönlendirme kuralları kümesi tanımlar.

Ağ geçidini ve sanal hizmet YAML 'yi dağıtmak için `kubectl apply` komutunu kullanın. Bu kaynakların dağıtıldığı ad alanını belirtmeyi unutmayın.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Aşağıdaki örnek çıktı yeni ağ geçidini ve oluşturulmakta olan sanal hizmeti gösterir:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Aşağıdaki komutu kullanarak, Istio giriş ağ geçidinin IP adresini alın:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Aşağıdaki örnek çıktıda, giriş ağ geçidinin IP adresi gösterilmektedir:

```
20.188.211.19
```

Bir tarayıcı açın ve IP adresini yapıştırın. Örnek AKS oylama uygulaması görüntülenir.

![IKS oylama uygulaması, ampo 'un etkin olduğu AKS kümesinde çalışıyor.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Ekranın alt kısmındaki bilgiler, uygulamanın `voting-storage` (redo) `voting-app` ve sürüm `1.0` `1.0` sürümünü kullandığını gösterir.

## <a name="update-the-application"></a>Uygulamayı güncelleştirme

Analiz bileşeninin yeni bir sürümünü dağıtalım. Bu yeni sürüm `1.1` her kategori için sayıma ek olarak toplamları ve yüzdeleri görüntüler.

Aşağıdaki diyagramda, bu bölümün sonunda ne olacağı gösterilmektedir-`voting-analytics` bileşenimizin yalnızca `1.1` sürümü `voting-app` bileşeninden yönlendirilen trafiğe sahiptir. @No__t_1 bileşenimizin sürüm `1.0` çalışmaya devam ediyor ve `voting-analytics` hizmeti tarafından başvurulduğu halde, Ise proxy 'leri bu bilgisayardan gelen ve giden trafiğe izin vermez.

![AKS oylama uygulama bileşenleri ve yönlendirme.](media/servicemesh/istio/scenario-routing-components-02.png)

@No__t_1 bileşeninin sürüm `1.1` dağıtalım. @No__t_0 ad alanında bu bileşeni oluşturun:

```azurecli
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Aşağıdaki örnek çıktıda oluşturulan kaynaklar gösterilmektedir:

```console
deployment.apps/voting-analytics-1-1 created
```

Örnek AKS oylama uygulamasını bir tarayıcıda yeniden açarak, önceki adımda elde edilen Istio giriş ağ geçidinin IP adresini kullanın.

Tarayıcınız aşağıda gösterilen iki görünüm arasında alternatifler vardır. @No__t_1 bileşeni için yalnızca tek etiketli seçiciyle (`app: voting-analytics`) bir Kubernetes [hizmeti][kubernetes-service] kullandığınızdan, Kubernetes, bu seçiciyle eşleşen Pod 'ler arasında hepsini bir kez deneme için varsayılan davranışı kullanır. Bu durumda, hem sürüm `1.0` hem de `voting-analytics` pods `1.1`.

![AKS oylama uygulamamız üzerinde çalışan analiz bileşeninin sürüm 1,0.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![AKS oylama uygulamamız üzerinde çalışan analiz bileşeninin sürüm 1,1.](media/servicemesh/istio/scenario-routing-update-app-01.png)

@No__t_0 bileşeninin iki sürümü arasındaki geçişi aşağıdaki gibi görselleştirebilirsiniz. Kendi Istik giriş ağ geçidinizin IP adresini kullanmayı unutmayın.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Aşağıdaki örnek çıktıda, sürümler arasında site anahtarları olarak döndürülen Web sitesinin ilgili bölümü gösterilmektedir:

```console
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Trafiği uygulamanın 1,1 sürümüne kilitle

Şimdi, trafiği yalnızca `voting-analytics` bileşeninin `1.1` sürümüne ve `voting-storage` bileşeninin `1.0` sürümüne kilitleyelim. Daha sonra diğer tüm bileşenler için yönlendirme kuralları tanımlarsınız.

> * Bir **sanal hizmet** , bir veya daha fazla hedef hizmet için bir yönlendirme kuralları kümesi tanımlar.
> * Bir **hedef kural** , trafik ilkelerini ve sürüme özgü ilkeleri tanımlar.
> * Bir **ilke** , iş yükleri üzerinde hangi kimlik doğrulama yöntemlerinin kabul edilebilir olduğunu tanımlar.

@No__t_1 sanal hizmet tanımını değiştirmek ve diğer bileşenlere yönelik [hedef kuralları][istio-reference-destinationrule] ve [sanal hizmetleri][istio-reference-virtualservice] eklemek için `kubectl apply` komutunu kullanın. Hizmetler arasındaki tüm iletişim güvenliğini karşılıklı TLS ve istemci sertifikaları kullanarak güvence altına almak için `voting` ad alanına bir [ilke][istio-reference-policy] ekleyeceksiniz.

* Ilke, `voting` ad alanındaki hizmetleriniz arasında karşılıklı TLS 'nin uygulanmasını sağlamak için `STRICT` olarak ayarlanmıştır `peers.mtls.mode`.
* Ayrıca, tüm hedef kurallarımızda `trafficPolicy.tls.mode` `ISTIO_MUTUAL` olarak ayarlandık. İstio, güçlü kimlikler içeren hizmetler sağlar ve karşılıklı olarak yönettiği karşılıklı TLS ve istemci sertifikalarını kullanarak hizmetler arasındaki iletişimin güvenliğini sağlar.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Aşağıdaki örnek çıktıda yeni Ilke, hedef kuralları ve güncelleştirilmekte/oluşturulan sanal hizmetler gösterilmektedir:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

AKS oylama uygulamasını bir tarayıcıda yeniden açarsanız, `voting-app` bileşeni tarafından yalnızca `voting-analytics` bileşeninin yeni sürüm `1.1` kullanılır.

![AKS oylama uygulamamız üzerinde çalışan analiz bileşeninin sürüm 1,1.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Artık yalnızca `voting-analytics` bileşeninizin sürüm `1.1` şu şekilde yönlendirildiğini görselleştirebilirsiniz. Kendi Istio giriş ağ geçidinizin IP adresini kullanmayı unutmayın:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Aşağıdaki örnek çıktı, döndürülen Web sitesinin ilgili bölümünü gösterir:

```console
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Şimdi de, tüm hizmetlerimiz arasındaki iletişimleri güvenli hale getirmek için IBU ' ın karşılıklı TLS kullandığını doğrulayalim. Bunun için, aşağıdaki biçimi alan `istioctl` Client binary üzerinde [AuthN TLS-Check][istioctl-authn-tls-check] komutunu kullanacağız.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Bu komut kümesi, bir ad alanında olan ve bir etiket kümesiyle eşleşen tüm yığınlardan belirtilen hizmetlere erişim hakkında bilgi sağlar:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Aşağıdaki örnek çıktıda, yukarıdaki sorgularımızın her biri için karşılıklı TLS 'nin zorunlu olduğu gösterilmektedir. Çıktı Ayrıca, karşılıklı TLS 'yi zorlayan Ilke ve hedef kurallarını da gösterir:

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Uygulamanın kanarya bir sürümünü kullanıma alma

Şimdi `voting-app`, `voting-analytics` ve `voting-storage` bileşenlerinin yeni bir sürümünü `2.0` dağıtalım. Yeni `voting-storage` bileşeni redin yerine MySQL kullanır ve `voting-app` ve `voting-analytics` bileşenleri, bu yeni `voting-storage` bileşeni kullanmasına izin verecek şekilde güncelleştirilir.

@No__t_0 bileşen artık Özellik bayrağı işlevini desteklemektedir. Bu özellik bayrağı, bir kullanıcı alt kümesi için istio 'nun kanarya yayın yeteneğini test etmenize olanak tanır.

Aşağıdaki diyagramda, bu bölümün sonunda ne çalıştırdıklarınız gösterilmektedir.

* @No__t_1 bileşeninin sürüm `1.0`, `1.0` bileşenin `voting-analytics` bileşeni ve sürüm `voting-storage` sürümü `1.1` birbirleriyle iletişim kurabilir.
* @No__t_1 bileşeninin sürüm `2.0`, `2.0` bileşenin `voting-analytics` bileşeni ve sürüm `voting-storage` sürümü `2.0` birbirleriyle iletişim kurabilir.
* @No__t_1 bileşeninin sürüm `2.0` yalnızca belirli bir özellik bayrağı ayarlanmış olan kullanıcılar tarafından erişilebilir. Bu değişiklik, tanımlama bilgisi aracılığıyla bir özellik bayrağı kullanılarak yönetilir.

![AKS oylama uygulama bileşenleri ve yönlendirme.](media/servicemesh/istio/scenario-routing-components-03.png)

İlk olarak, bu yeni bileşenler için istio hedef kurallarını ve sanal hizmetlerini karşılamak olarak güncelleştirin. Bu güncelleştirmeler, trafiği yeni bileşenlere doğru bir şekilde yönlendirmemenizi ve kullanıcıların beklenmeyen bir erişim almaz olmasını sağlar:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Aşağıdaki örnek çıktıda, güncelleştirilmekte olan hedef kurallar ve sanal hizmetler gösterilmektedir:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Ardından, yeni sürüm `2.0` bileşenleri için Kubernetes nesnelerini ekleyelim. Ayrıca, `voting-storage` hizmetini MySQL için `3306` bağlantı noktasını içerecek şekilde güncelleştirebilirsiniz:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Aşağıdaki örnek çıktıda, Kubernetes nesnelerinin başarıyla güncelleştirildiği veya oluşturulduğu gösterilmektedir:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Tüm sürüm `2.0` Pod 'leri çalıştırmaya kadar bekleyin. @No__t_2 ad alanındaki tüm düğüm üzerindeki değişiklikleri izlemek için `-w` Watch anahtarıyla [kubectl Get Pod][kubectl-get] komutunu kullanın:

```azurecli
kubectl get pods --namespace voting -w
```

Artık oylama uygulamasının sürüm `1.0` ve sürüm `2.0` (Canary) arasında geçiş yapabiliyor olmanız gerekir. Ekranın alt kısmındaki Özellik bayrağı değiştirme bir tanımlama bilgisi ayarlar. Bu tanımlama bilgisi, `voting-app` sanal hizmeti tarafından, kullanıcıları yeni sürüme `2.0` yönlendirmek için kullanılır.

![AKS oylama uygulaması-Özellik bayrağının 1,0 sürümü ayarlanmamış.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![AKS oylama uygulaması-Özellik bayrağının 2,0 sürümü ayarlanır.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Oy sayıları, uygulamanın sürümleri arasında farklılık görüntüler. Bu fark, iki farklı depolama arka ucu kullandığınızı vurgular.

## <a name="finalize-the-rollout"></a>Dağıtımı sonlandırma

Kanarya sürümünü başarıyla sınadıktan sonra, `voting-app` sanal hizmetini, tüm trafiği `voting-app` bileşeninin `2.0` sürümüne yönlendirecek şekilde güncelleştirin. Daha sonra tüm kullanıcılar, özellik bayrağının ayarlanmış olup olmamasına bakılmaksızın uygulamanın sürüm `2.0` görür:

![AKS oylama uygulama bileşenleri ve yönlendirme.](media/servicemesh/istio/scenario-routing-components-04.png)

Artık etkin olmasını istemediğiniz bileşenlerin sürümlerini kaldırmak için tüm hedef kurallarını güncelleştirin. Ardından, bu sürümlere başvurmayı durdurmak için tüm sanal Hizmetleri güncelleştirin.

Artık bileşenlerin eski sürümlerinden hiçbirine hiç trafik olmadığından, bu bileşenlere yönelik tüm dağıtımları güvenle silebilirsiniz.

![AKS oylama uygulama bileşenleri ve yönlendirme.](media/servicemesh/istio/scenario-routing-components-05.png)

Artık AKS oylama uygulamasının yeni bir sürümünü başarıyla tamamladınız.

## <a name="clean-up"></a>Temizleme 

Bu senaryoda kullandığımız AKS oylama uygulamasını, aşağıdaki gibi `voting` ad alanını silerek AKS kümenizdeki bir şekilde kaldırabilirsiniz:

```azurecli
kubectl delete namespace voting
```

Aşağıdaki örnek çıktı, AKS oylama uygulamasının tüm bileşenlerinin AKS kümenizdeki kaldırıldığını gösterir.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Sonraki adımlar

[Istio BookInfo uygulama örneğini][istio-bookinfo-example]kullanarak ek senaryolar keşfedebilirsiniz.

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
