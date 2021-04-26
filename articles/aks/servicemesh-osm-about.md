---
title: Açık hizmet ağı (Önizleme)
description: Azure Kubernetes Service (AKS) içinde hizmet ağı 'nı (OSD) açma
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: b0a37400d02ff324e7191bb0b04243d7f16090bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877165"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Hizmet ağı AKS eklentisini aç (Önizleme)

## <a name="overview"></a>Genel Bakış

[Açık hizmet ağı (OSı)](https://docs.openservicemesh.io/) , kullanıcıların yüksek düzeyde dinamik mikro hizmet ortamları için kullanıma hazır Observability özelliklerini bir arada yönetmesine, güvenli hale getirmeye ve kullanıma almasına olanak tanıyan basit, Genişletilebilir, bulut Yerel hizmet ağlarından oluşur.

OSD, Kubernetes üzerinde bir Envoy tabanlı denetim düzlemi çalıştırır, [SMI](https://smi-spec.io/) API 'lerle yapılandırılabilir ve uygulamanızın her bir örneğinin yanında yer alan bir sepet kapsayıcısı olarak ekleme a Envoy proxy 'si ile çalışır. Envoy proxy, erişim denetim ilkeleri etrafında kuralları içerir ve yürütür, yönlendirme yapılandırmasını uygular ve ölçümleri yakalar. Denetim düzlemi, ilkelerin ve yönlendirme kurallarının güncel olmasını sağlamak için proxy 'leri sürekli olarak yapılandırır ve proxy 'lerin sağlıklı olmasını sağlar.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Özellikler ve Özellikler

OSE, Azure Kubernetes Service (AKS) kümeleriniz için bir bulut Yerel hizmet ağı sağlamak üzere aşağıdaki özellik ve özellikler kümesini sağlar:

- MTLS 'yi etkinleştirerek hizmetten hizmete iletişime geçin

- Envoy proxy 'nin otomatik dışarıdan eklemesini etkinleştirerek kolayca ağ üzerinde uygulamalar ekleme

- Dağıtımlar üzerinde kaydırma trafiği için kolay ve şeffaf yapılandırma

- Hizmetler için hassas erişim denetim ilkeleri tanımlama ve yürütme özelliği

- Hata ayıklama ve izleme hizmetleri için uygulama ölçümlerine Observability ve Öngörüler

- Harici sertifika yönetimi hizmetleriyle ve takılabilir bir arabirim ile çözümlerle tümleştirme

## <a name="scenarios"></a>Senaryolar

OSD, AKS dağıtımlarınıza aşağıdaki senaryolarla yardımcı olabilir:

- Kümede dağıtılan hizmet uç noktaları arasında şifrelenmiş iletişimler sağlama

- Ağ üzerinde hem HTTP/HTTPS hem de TCP trafiğinin trafik yetkilendirmesi

- A/B veya kanary dağıtımları için iki veya daha fazla hizmet arasındaki ağırlıklı trafik denetimlerinin yapılandırması

- Uygulama trafiğinden KPI 'lerin toplanması ve görüntülenmesi

## <a name="osm-service-quotas-and-limits-preview"></a>OSA hizmeti kotaları ve limitleri (Önizleme)

Hizmet kotaları ve limitler için OSP önizleme sınırlamaları, AKS [kotaları ve bölgesel sınırlar sayfasında](https://docs.microsoft.com/azure/aks/quotas-skus-regions)bulunabilir.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Kullanarak ikiliden OSD 'yi yüklemeyi denemeyin `osm install` . Bu, AKS için eklenti olarak tümleştirilmeyen bir OSD yüklemesine neden olur.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>`AKS-OpenServiceMesh`Önizleme özelliğini kaydetme

Açık hizmet ağı eklentisini kullanan bir AKS kümesi oluşturmak için, `AKS-OpenServiceMesh` aboneliğinizde Özellik bayrağını etkinleştirmeniz gerekir.

`AKS-OpenServiceMesh`Aşağıdaki örnekte gösterildiği gibi, [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Durumun _kayıtlı_ gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu doğrulayın:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak _Microsoft. Containerservice_ kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Yeni bir AKS kümesi için açık hizmet ağı (OSDK) Azure Kubernetes hizmeti (AKS) eklentisini yükler

Yeni bir AKS küme dağıtım senaryosunda, küme oluşturma işleminde OSı eklentisini etkinleştiren bir AKS kümesinin yepyeni bir dağıtımıyla başlayacaksınız.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. [Az Group Create](/cli/azure/group#az_group_create)kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnek _eastus2_ konumunda (bölge) _Myosmaksgroup_ adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>OSK eklentisi etkinken bir AKS kümesi dağıtma

Artık OSK eklentisi etkinken yeni bir AKS kümesi dağıtacaksınız.

> [!NOTE]
> Lütfen aşağıdaki AKS dağıtım komutu OS geçici disklerini kullanır. Burada, [AKS Için kısa ömürlü işletim sistemi diskleri](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os) hakkında daha fazla bilgi bulabilirsiniz

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AKS kümesi erişim kimlik bilgilerini al

Yeni yönetilen Kubernetes kümesi için erişim kimlik bilgileri alın.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Mevcut bir AKS kümesi için açık hizmet ağı (OSDK) Azure Kubernetes hizmeti (AKS) eklentisini etkinleştir

Mevcut bir AKS kümesi senaryosunda, OSı eklentisini zaten dağıtılmış olan mevcut bir AKS kümesine etkinleştirecektir.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Mevcut AKS kümesine OSD eklentisini etkinleştirin

AKS OSı eklentisini etkinleştirmek için `az aks enable-addons --addons` parametresini geçirerek komutunu çalıştırmanız gerekir `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

AKS OSM eklentisinin yüklendiğini doğrulamak için aşağıda gösterilen çıktıya benzer bir çıktı görmeniz gerekir.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>AKS OSK eklentisi yüklemesini doğrulama

AKS OSD eklentisinin tüm bileşenlerinin, etkin ve çalışır şekilde denetlenmesi için çalıştırılacak birkaç komut vardır:

İlk olarak, yüklü eklentilerin etkin durumunu denetlemek için kümenin eklenti profillerini sorgulayabiliriz. Aşağıdaki komut "true" döndürmelidir.

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Aşağıdaki `kubectl` Komutlar OSA-Controller durumunu rapor eder.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>AKS OSK eklentisi 'ne erişme

Şu anda, configmap aracılığıyla OSA denetleyici yapılandırmasına erişebilir ve bunları yapılandırabilirsiniz. OSı denetleyicisi yapılandırma ayarlarını görüntülemek için, `kubectl` yapılandırma ayarlarını görüntülemek üzere OSU-config configmap öğesini aracılığıyla sorgulayın.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap 'in çıkışı aşağıdaki gibi görünmelidir:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**Permissive_traffic_policy_mode** **true** olarak yapılandırıldığını unutmayın. OSD 'de izin veren trafik ilkesi modu, [SMI](https://smi-spec.io/) trafik ilkesi zorlamasının atlanacağı bir moddur. Bu modda, OSA, bu hizmetlerle iletişim kurabilmesi için her bir Envoy ara sunucu arabasının hizmet kafesi ve programlar trafik ilkesi kurallarının bir parçası olan hizmetleri otomatik olarak bulur.

> [!WARNING]
> Devam etmeden önce lütfen izin veren trafik ilkesi modlarınızın doğru olarak ayarlandığını doğrulayın. Aksi takdirde, lütfen aşağıdaki komutu kullanarak **değeri true** olarak değiştirin

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Açık hizmet ağı (OSE) Azure Kubernetes hizmeti (AKS) eklentisi tarafından yönetilecek yeni bir uygulama dağıtma

### <a name="before-you-begin"></a>Başlamadan önce

Bu kılavuzda açıklanan adımlarda, bir AKS kümesi (Kubernetes `1.19+` RBAC 'nin etkinleştirildiği), kümeyle bir bağlantı kurdığınıza `kubectl` (Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa) ve aks OSI eklentisini yüklemiş [](./kubernetes-walkthrough.md)olmanız gerektiğini varsaymaktadır.

Aşağıdaki kaynakların yüklü olması gerekir:

- Azure CLı, sürüm 2.20.0 veya üzeri
- `aks-preview`Uzantı sürümü 0.5.5 veya üzeri
- OSD sürüm v 0.8.0 veya üzeri
- apt-get JQ 'yi al

### <a name="create-namespaces-for-the-application"></a>Uygulama için ad alanları oluşturma

Bu kılavuzda, aşağıdaki Kubernetes hizmetlerine sahip olan OSı kitaplığı uygulamasını kullanacağız:

- kitap alıcısı
- bookthief
- Kitaplığı
- kitap ambarı

Bu uygulama bileşenlerinin her biri için ad alanları oluşturun.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSD tarafından yönetilecek ad alanlarını ekleme

Ad alanlarını OSE ağı 'na eklediğinizde, bu, OSı denetleyicisinin uygulamanızla birlikte otomatik olarak Envoy dışarıdan ara sunucu kapsayıcıları eklemesine izin verir. OSE kitaplığı uygulaması ad alanlarını eklemek için aşağıdaki komutu çalıştırın.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Kitaplığı uygulamasını AKS kümesine dağıtma

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Dağıtım çıktılarının tümü aşağıda özetlenmiştir.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Kontrol noktası: ne yüklendi?

Örnek kitaplığı uygulaması, dört hizmetten oluşan, kitap içi, bookthief, kitaplığı ve kitap ambarı olan çok katmanlı bir uygulamadır. Hem booksatınalmacı hem de bookthief hizmeti, kitaplığı hizmetinden kitap almak için kitaplığı hizmeti ile iletişim kurar. Kitaplığı hizmeti, booksatınalmacı ve bookthief 'i sağlamak için kitap dışı ambar hizmetinin kitaplarını alır. Bu, uygulama hizmetleri arasındaki iletişimleri korumak ve yetkilendirmek için bir hizmet ağı 'nın nasıl kullanılabileceğini göstermek için iyi bir çok katmanlı uygulamadır. İzlenecek yolda devam ettiğimiz gibi, hizmet kafes arabirimi (SMı) ilkelerini, hizmetlerin OSM üzerinden iletişim kurmasına izin vermek ve bunlara izin vermek için etkinleştirip devre dışı bırakacağız. Aşağıda, kitaplığı uygulaması için nelerin yüklü olduğuna ilişkin bir mimari diyagramı verilmiştir.

![OSA booksatınalmacı uygulaması mimarisi](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS kümesi içinde çalışan kitaplığı uygulamasını doğrulama

Şu andan itibaren, kitaplığı muarlı kapsayıcı uygulamasını dağıttık, ancak yalnızca AKS kümesi içinden erişilebilir. Sonraki öğreticiler, uygulamayı bir giriş denetleyicisi aracılığıyla küme dışında kullanıma sunma konusunda size yardımcı olur. Şimdilik, bir AKS kümesi içindeki booksatınalmacı uygulamasına erişmek için bağlantı noktası iletme, kitaplığı hizmetinden kitap aldığını doğrulamak için kullanılacaktır.

Uygulamanın küme içinde çalıştığını doğrulamak için, hem booksatınalmacı hem de bookthief bileşenleri kullanıcı arabirimini görüntülemek için bir bağlantı noktası kullanacağız.

İlk olarak, bookalıcının Pod adını alalım

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Aşağıdakine benzer bir çıktı görmeniz gerekir. Muhasebeci alıcı Pod 'larınızın eklenmiş benzersiz bir adı olacaktır.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod 'un adı olduktan sonra, şimdi bağlantı noktası-ilet komutunu kullanarak yerel sistemimizden AKS kümesi içindeki uygulamaya bir tünel ayarlayabilirsiniz. Yerel sistem bağlantı noktası 8080 için bağlantı noktasını ileriye doğru ayarlamak için aşağıdaki komutu çalıştırın. Ardından, belirtilen booksatınalmacı Pod adınızı kullanın.

> [!NOTE]
> Tüm bağlantı noktası iletme komutları için, Bu izlenecek yolda çalışmaya devam edebilmeniz ve tünelin bağlantısını kesmemesi için ek bir Terminal kullanmak en iyisidir. Ayrıca, bağlantı noktası iletme tünelini Azure Cloud Shell dışında oluşturmanız da en iyisidir.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Aşağıdakine benzer bir çıktı görmeniz gerekir.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Bağlantı noktası iletme oturumu yerine, bir tarayıcıdan aşağıdaki URL 'ye gidin `http://localhost:8080` . Artık, aşağıdaki görüntüye benzer bir tarayıcıda booksatınalmacı uygulama kullanıcı arabirimini görebilmelisiniz.

![OSA booksatınalmacı uygulaması kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Ayrıca, satın alınan toplam kitap sayısı, kitaplığı v1 hizmetine artmaya devam edeceğini fark edersiniz. Kitaplığı v2 hizmeti henüz dağıtılmadı. SMı trafik bölünmüş ilkeleri gösterdiğimiz sırada, kitaplığı v2 hizmetini dağıtacağız.

Ayrıca, bookthief hizmeti için de aynı denetimi yapabilirsiniz.

```azurecli-interactive
kubectl get pod -n bookthief
```

Aşağıdakine benzer bir çıktı görmeniz gerekir. Bookthief Pod 'larınızın eklenmiş benzersiz bir adı olacaktır.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Bağlantı noktası, bookthief Pod 'a ilet.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Bir tarayıcıdan aşağıdaki URL 'ye gidin `http://localhost:8080` . Bookthief 'in Şu anda kitaplığı hizmetinden kitaplar çalmasını görmeniz gerekir! Daha sonra, bookthief 'i durdurmak için bir trafik ilkesi uygulayacağız.

![OSD bookthief uygulaması kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Ağ için OSD Izin veren trafik modunu devre dışı bırak

OSı küme yapılandırması görüntülenirken daha önce belirtildiği gibi, OSE yapılandırması izin veren trafik modu ilkesini etkinleştirir. Bu modda trafik ilkesi zorlaması atlanır ve OSA, bu hizmetlerle iletişim kurabilmesi için her bir Envoy ara sunucu arabasının hizmet kafesi ve programlar trafik ilkesi kurallarının bir parçası olan hizmetleri otomatik olarak bulur.

Artık izin verilen trafik modu ilkesini devre dışı bırakacağız ve OSD, her bir hizmetten kafeste iletişime izin vermek için kümeye dağıtılmış açık [SMI](https://smi-spec.io/) ilkelerine sahip olacak. İzin veren trafik modunu devre dışı bırakmak için aşağıdaki komutu çalıştırarak, değerini olarak değiştirme configmap özelliğini güncelleştirin `true` `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Aşağıdakine benzer bir çıktı görmeniz gerekir. Bookthief Pod 'larınızın eklenmiş benzersiz bir adı olacaktır.

```Output
configmap/osm-config patched
```

İzin veren trafik modunun devre dışı bırakıldığını doğrulamak için, tarayıcıda Kullanıcı arabirimini görüntülemek için booksatınalmacı veya bookthief Pod 'a geri dönün ve satın alınan kitapların veya çalındığı kitaplarının artık arttırılmadığında emin olun. Tarayıcıyı yenilediğinizden emin olun. Artırma durdurulmuşsa, ilke doğru şekilde uygulandı. Kitap çalmasını önlemek için bookthief 'i başarıyla durdurduk, ancak kitap defterinizden kitapçıya ya da kitapbir kitap, kitap ambarından kitap alabilir. Ardından, yalnızca iletişim kurmak istediğiniz kafesteki hizmetlere izin vermek için [SMI](https://smi-spec.io/) ilkeleri uygulayacağız.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Hizmet kafesi arabirimi (SMı) trafik erişim ilkelerini uygulama

Artık kafesteki tüm iletişimleri devre dışı bırakmış olduğunuza göre, kitap alıcı hizmetimizin satın alma kitapları için kitaplığı hizmetimiz ile iletişim kurmasına izin vereceğiz ve kitaplığı hizmetimizin satmaya yönelik kitaplar almak için kitap ambarı hizmetimizi iletişim kurmasına izin verin.

Aşağıdaki [SMI](https://smi-spec.io/) ilkelerini dağıtın.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Aşağıdakine benzer bir çıktı görmeniz gerekir.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Artık kitap alıcı veya kitapımlar üzerinde bir bağlantı noktası iletme oturumu ayarlayabilir ve hem satın alınan hem de kitap satılan ölçümlerin yeniden artırıldığına bakabilirsiniz. Ayrıca, bookthief pod için de aynı işlemi, hala kitap çalamayacak olduğunu doğrulamak için de kullanabilirsiniz.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Hizmet kafesi arabirimi (SMı) trafik bölünmüş ilkeleri uygulama

Son gösterimimiz için, bir hizmetten arka uç olarak birden fazla hizmete iletişim ağırlığı yapılandırmak üzere bir [SMI](https://smi-spec.io/) trafik bölme ilkesi oluşturacağız. Trafik bölünmüş işlevselliği, trafiği 0 ' dan 100 ' e kadar bir ölçeğe ayırarak, tek bir hizmete yönelik bağlantıları aşamalı olarak diğerine taşımanıza olanak tanır.

Aşağıdaki grafik, bir [SMI](https://smi-spec.io/) trafik bölme ilkesi dağıtılması için bir diyagramdır. Ek bir kitaplığı sürüm 2 ' yi dağıtabeceğiz ve sonra gelen trafiği, trafiğin %25 ' i ve %75 ' ı kitaplığı v2 hizmetine bölüşdüğü kitap alıcı 'dan bölecektir.

![OSA booksatınalmacı trafiği bölünmüş diyagramı](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Kitaplığı v2 hizmetini dağıtın.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Aşağıdaki çıkışı görmelisiniz.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Şimdi iki kitaplığı v1 ve v2 hizmeti arasında booksatınalmacı trafiğini ayırmak için trafik bölme ilkesini dağıtın.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Aşağıdaki çıkışı görmelisiniz.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Booksatınalmacı Pod 'a bir bağlantı noktası iletme tüneli kurun ve şimdi, kitaplığı v2 hizmetinden satın alınan kitapları görmeniz gerekir. Satın alımlarınızın artışını izlemeye devam ederseniz, kitaplığı v2 hizmeti aracılığıyla gerçekleştirilen satınalmaların daha hızlı bir şekilde artışına dikkat etmeniz gerekir.

![OSE booksatınalmacı kitaplar boough Kullanıcı arabirimi](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Açık hizmet ağı (OSE) Azure Kubernetes hizmeti (AKS) eklentisi tarafından yönetilecek mevcut dağıtılan uygulamaları yönetin

### <a name="before-you-begin"></a>Başlamadan önce

Bu kılavuzda açıklanan adımlarda, AKS kümeniz için OSA AKS eklentisini daha önce etkinleştirmiş olduğunuz varsayılır. Aksi takdirde, devam etmeden önce [mevcut BIR aks kümesi Için açık hizmet ağı (OSı) Azure Kubernetes hizmeti (AKS) eklentisini etkinleştirin](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) bölümüne bakın. Ayrıca, AKS kümenizin sürüm Kubernetes `1.19+` ve üzeri olması, KUBERNETES RBAC 'in etkin olması ve kümeyle bir bağlantı oluşturmuş olması gerekir `kubectl` (Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa, [aks](./kubernetes-walkthrough.md)OSU eklentisini de yüklemiş olursunuz.

Aşağıdaki kaynakların yüklü olması gerekir:

- Azure CLı, sürüm 2.20.0 veya üzeri
- `aks-preview`Uzantı sürümü 0.5.5 veya üzeri
- OSD sürüm v 0.8.0 veya üzeri
- apt-get JQ 'yi al

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Açık hizmet ağı (OSD) Izin veren trafik modu Ilkesini doğrulayın

OSA Izin veren trafik Ilkesi modu, [SMI](https://smi-spec.io/) trafik ilkesi zorlamasının atlanacağı bir moddur. Bu modda, OSA, bu hizmetlerle iletişim kurabilmesi için her bir Envoy ara sunucu arabasının hizmet kafesi ve programlar trafik ilkesi kurallarının bir parçası olan hizmetleri otomatik olarak bulur.

Kümenizin geçerli izin veren trafik modunu doğrulamak için şu komutu çalıştırın:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap 'in çıkışı aşağıdaki gibi görünmelidir:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**Permissive_traffic_policy_mode** **true** olarak yapılandırıldıysa, hizmet-hizmet iletişimlerinizi kesintiye uğramadan ad alanlarınızı güvenle ekleyebilirsiniz. **Permissive_traffic_policy_mode** **false** olarak yapılandırılırsa, doğru [SMI](https://smi-spec.io/) trafik erişim ilkesi bildirimlerinin dağıtıldığını ve ad alanında dağıtılan her hizmeti temsil eden bir hizmet hesabınız olduğundan emin olmanız gerekir. Lütfen [yanlış olarak yapılandırılan açık hizmet ağı (OSD) Izin veren trafik ilkesiyle mevcut dağıtılmış uygulamaları](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false) ekleme kılavuzunu izleyin

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Açık hizmet ağı (OSD) Izin veren trafik Ilkesi, doğru olarak yapılandırılmış mevcut dağıtılan uygulamaları ekleme

İlk yapacağımız ilk şey, dağıtılmış uygulama ad uzayını yönetmek için OSD 'ye eklemektir.

```azurecli-interactive
osm namespace add bookstore
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Bir sonraki adımda, ad alanındaki geçerli Pod dağıtımına göz atacağız. Belirlenen ad alanındaki Pod 'leri görüntülemek için aşağıdaki komutu çalıştırın.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Aşağıdaki benzer çıktıyı görmeniz gerekir:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

**1/1** gösteren ve uygulama Pod 'un yalnızca bir kapsayıcıya sahip olduğu anlamına gelen **Ready** sütununa dikkat edin. Ardından, OSR 'nin uygulamanızın Pod 'iyle birlikte Envoy dışarıdan yükleme proxy kapsayıcısını ekleyebilmesi için uygulama dağıtımlarınızı yeniden başlatmaları gerekir. Ad alanındaki dağıtımların bir listesini alalım.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Şimdi, uygulamanızın Pod 'iyle birlikte Envoy dışarıdan yükleme proxy kapsayıcısını eklemek için dağıtımı yeniden başlatacağız. Aşağıdaki komutu çalıştırın.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
deployment.apps/bookbuyer restarted
```

Ad alanındaki yığınlara tekrar göz atalım:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Artık **hazır** sütununun, Pod 'niz için hazır olan **2/2** kapsayıcılarını gösteriyor olduğunu fark edeceksiniz. İkinci kapsayıcı, Envoy sepet proxy 'dir.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Yapılandırmayı görüntülemek için açıkla komutunu çalıştırarak Envoy proxy 'sini görüntülemek için pod 'ı daha fazla inceleyebilirsiniz.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Uygulamanızın, Envoy dışarıdan ara sunucu ekleme işleminden sonra hala işlevsel olduğunu doğrulayın.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Açık hizmet ağı (OSD) Izin veren trafik Ilkesi yanlış olarak yapılandırılmış mevcut dağıtılmış uygulamaları ekleme

İzin veren trafik ilkesi için OSE yapılandırması olarak ayarlandığında `false` , OSD, kümenizde, hizmetten hizmete iletişim için dağıtılan açık [SMI](https://smi-spec.io/) trafik erişim ilkelerinin olması gerekir. Şu anda OSı Ayrıca hizmetten hizmete iletişimleri yetkilendirmenin bir parçası olarak Kubernetes hizmet hesaplarını da kullanıyor. Mevcut dağıtılan uygulamalarınızın OSı ağı tarafından yönetildiğinde iletişim kurmasını sağlamak için, kullanmak üzere bir hizmet hesabının varlığını doğrulamanız, uygulama dağıtımını hizmet hesabı bilgileriyle güncelleştirmek, [SMI](https://smi-spec.io/) trafik erişim ilkelerini uygulamak için bir hizmet hesabı olduğunu doğrulamanız gerekir.

#### <a name="verify-kubernetes-service-accounts"></a>Kubernetes hizmet hesaplarını doğrulama

Uygulamanızın dağıtıldığı ad alanında bir Kubernetes hizmet hesabına sahip olup olmadığını doğrulayın.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Aşağıda, booksatınalmacı ad alanında adlı bir hizmet hesabı bulunur `bookbuyer` .

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Varsayılan hesap dışında listelenen bir hizmet hesabınız yoksa uygulamanız için bir tane oluşturmanız gerekir. Uygulamanın dağıtılan ad alanında bir hizmet hesabı oluşturmak için örnek olarak aşağıdaki komutu kullanın.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Uygulamanızın geçerli dağıtım belirtimini görüntüleme

Önceki bölümde bir hizmet hesabı oluşturmanız gerekiyordu, uygulama dağıtımınızın dağıtım belirtiminde belirli bir şekilde yapılandırılmamış olma olasılığı vardır `serviceAccountName` . Aşağıdaki komutlarla uygulamanızın dağıtım belirtimini görüntüleyebiliriz:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Bir dağıtım listesi çıktıda listelenecektir.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Şimdi Pod şablonu bölümünde listelenen bir hizmet hesabı olup olmadığını görmek için dağıtımı bir denetim olarak anlatmaktadır.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

Bu dağıtımda, Pod şablonu bölümünde listelenen dağıtımla ilişkili bir hizmet hesabı olduğunu görebilirsiniz. Bu dağıtım, hizmet hesabı booksatınalmacı ' i kullanıyor. **Serivce hesabı:** özelliğini görmüyorsanız, dağıtımınız bir hizmet hesabı kullanacak şekilde yapılandırılmamıştır.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Bir Kubernetes hizmet hesabı eklemek için dağıtımınızı güncelleştirmek için çeşitli teknikler vardır. [Bir dağıtımı](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) satır Içi olarak güncelleştirme veya [pods Için hizmet hesaplarını yapılandırma](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)hakkında Kubernetes belgelerini gözden geçirin. Dağıtım belirtimlerinizi hizmet hesabıyla güncelleştirdikten sonra (kubectl Apply-f ınızı-Deployment. YAML) kümeye dağıtın.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Gerekli hizmet kafes arabirimi (SMı) Ilkelerini dağıtma

Ağ üzerinde akışa izin veren yetkili trafiğin en son adımı, uygulamanız için gereken [SMI](https://smi-spec.io/) trafik erişim ilkelerini dağıtmaktır. [SMI](https://smi-spec.io/) trafik erişim ilkeleriyle elde edilebilecek yapılandırma miktarı Bu izlenecek yolun kapsamına göre belirlenir, ancak belirtimin ortak bileşenlerinden bazılarını ayrıntılandırır ve uygulamanız için hizmet-hizmet iletişimini etkinleştirmek üzere hem basit bir TrafficTarget hem de HTTPRouteGroup ilkesinin nasıl yapılandırılacağını göstereceğiz.

[SMI](https://smi-spec.io/) [**trafik Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) belirtimi, kullanıcıların uygulamaları için erişim denetimi ilkesi tanımlamasına olanak tanır. **TrafficTarget** ve **Httproutgroup** API kaynaklarına odaklanacağız.

TrafficTarget kaynağı üç ana yapılandırma ayarları hedefi, kuralları ve kaynağından oluşur. Örnek bir TrafficTarget aşağıda gösterilmiştir.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

Yukarıdaki TrafficTarget belirtiminde, `destination` hedef kaynak hizmeti için yapılandırılan hizmet hesabını belirtir. Daha önce dağıtıma eklenen hizmet hesabının, eklendiği dağıtıma erişim yetkisi vermek için kullanılacağını unutmayın. `rules`Bu bölümde, bu örnekte, bağlantı üzerinden izin VERILEN http trafiğinin türü tanımlanmaktadır. Http üst bilgilerinin HTTP aracılığıyla izin verilen trafiğe özel olması için ince gren Regex desenleri yapılandırabilirsiniz. `sources`Kaynak, hizmet kaynaklı iletişimlerdir. Bu belirtim, kitapçıya iletişim kurmak için booksatınalmacı ihtiyaçlarını okur.

HTTPRouteGroup kaynağı, bir veya bir dizi HTTP üstbilgi bilgisiyle oluşur ve TrafficTarget spec için bir gereksinimdir. Aşağıdaki örnekte, HTTPRouteGroup ' un üç HTTP eylemini, iki GET ve One GÖNDERI yetkilendirerek emin olabilirsiniz.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

TrafficTarget spec bir kural gerektirdiğinden, ön uç uygulamanız uygulamanın diğer katmanlarına yönelik olan HTTP trafiğinin türünü bilmiyorsanız, HTTPRouteGroup için aşağıdaki belirtimi kullanarak tüm kurala izin ver ' in eşdeğerini oluşturabilirsiniz.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

TrafficTarget ve HTTPRouteGroup belirtimini yapılandırdıktan sonra, bunları bir YAML ve dağıtım olarak birlikte koyabilirsiniz. Aşağıda, kitaplığı örnek yapılandırması verilmiştir.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Belirtim hakkında daha ayrıntılı bilgi için [SMI](https://smi-spec.io/) sitesini ziyaret edin.

### <a name="manage-the-applications-namespace-with-osm"></a>Uygulama ad alanını OSD ile yönetme

Ardından, OSD 'yi ad alanını yönetecek şekilde yapılandıracağız ve uygulamayı yeniden başlatarak uygulama ile birlikte eklenmiş olan dağıtım proxy 'sini alacaksınız.

`azure-vote`Ad ALANıNı OSM 'umu yönetilecek şekilde yapılandırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Ardından, aşağıdaki komutlarla hem hem de dağıtımlarını yeniden başlatın `azure-vote-front` `azure-vote-back` .

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Ad alanı için pod 'yi görüyoruz, `azure-vote` her ikisinin de  `azure-vote-front` ve `azure-vote-back` 2/2 olarak, uygulamanın yanı sıra, Envoy dışarıdan yükleme proxy 'sinin nasıl bir şekilde eklenmiş olduğunu göreceğiz.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Öğretici: NGıNX girişi ile açık hizmet ağı (OSD) tarafından yönetilen bir uygulama dağıtma

Açık hizmet ağı (OSı), kullanıcıların yüksek düzeyde dinamik mikro hizmet ortamları için kullanıma hazır Observability özelliklerini bir arada yönetmesine, güvenli hale getirmeye ve kullanıma almasına olanak tanıyan basit, Genişletilebilir, bulut Yerel hizmet ağlarından oluşur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
>
> - Geçerli OSD küme yapılandırmasını görüntüle
> - Ad alanındaki dağıtılan uygulamaları yönetmek için OSD için ad alanı (ler) i oluşturun
> - OSD tarafından yönetilecek ad alanlarını ekleme
> - Örnek uygulamayı dağıtma
> - AKS kümesi içinde çalışan uygulamayı doğrulama
> - Uygulamayı için kullanılan bir NGıNX giriş denetleyicisi oluşturun
> - Azure Application Gateway bir hizmeti internet 'e giriş ile kullanıma sunma

### <a name="before-you-begin"></a>Başlamadan önce

Bu makalede açıklanan adımlarda, bir AKS kümesi (Kubernetes `1.19+` RBAC 'nin etkinleştirildiği Kubernetes ve üstü) oluşturdunuz, `kubectl` kümeyle bir bağlantı kurdu (Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa), [aks](./kubernetes-walkthrough.md)OSI eklentisini yüklemiş olmanız gerekir.

Aşağıdaki kaynakların yüklü olması gerekir:

- Azure CLı, sürüm 2.20.0 veya üzeri
- `aks-preview`Uzantı sürümü 0.5.5 veya üzeri
- OSD sürüm v 0.8.0 veya üzeri
- apt-get JQ 'yi al

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Geçerli OSD küme yapılandırmasını görüntüleyin ve doğrulayın

Aks kümesinde AKS için OSA eklentisi etkinleştirildikten sonra, geçerli yapılandırma parametrelerini OSU-config Kubernetes ConfigMap ' de görüntüleyebilirsiniz. ConfigMap özelliklerini görüntülemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Çıkış, küme için geçerli OSı yapılandırmasını gösterir.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**Permissive_traffic_policy_mode** **true** olarak yapılandırıldığını unutmayın. OSD 'de izin veren trafik ilkesi modu, [SMI](https://smi-spec.io/) trafik ilkesi zorlamasının atlanacağı bir moddur. Bu modda, OSA, bu hizmetlerle iletişim kurabilmesi için her bir Envoy ara sunucu arabasının hizmet kafesi ve programlar trafik ilkesi kurallarının bir parçası olan hizmetleri otomatik olarak bulur.

### <a name="create-namespaces-for-the-application"></a>Uygulama için ad alanları oluşturma

Bu öğreticide, aşağıdaki uygulama bileşenlerine sahip OSK kitaplığı uygulamasını kullanacağız:

- kitap alıcısı
- bookthief
- Kitaplığı
- kitap ambarı

Bu uygulama bileşenlerinin her biri için ad alanları oluşturun.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSD tarafından yönetilecek ad alanlarını ekleme

Ad alanlarını osz bölgesine eklemek, OSG denetleyicisinin uygulamanızla birlikte otomatik olarak Envoy dışarıdan ara sunucu kapsayıcıları eklemesine izin verir. OSE kitaplığı uygulaması ad alanlarını eklemek için aşağıdaki komutu çalıştırın.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Kitaplığı uygulamasını AKS kümesine dağıtma

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Dağıtım çıktılarının tümü aşağıda özetlenmiştir.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Booksatınalmacı hizmetini güncelleştirme

Booksatınalmacı hizmetini aşağıdaki hizmet bildirimiyle doğru gelen bağlantı noktası yapılandırması olarak güncelleştirin.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS kümesi içinde çalışan kitaplığı uygulamasını doğrulama

Şu andan itibaren, kitaplığı muarlı kapsayıcı uygulamasını dağıttık, ancak yalnızca AKS kümesi içinden erişilebilir. Daha sonra, uygulamayı AKS kümesi dışında kullanıma sunmak için Azure Application Gateway giriş denetleyicisi ekleyeceğiz. Uygulamanın küme içinde çalıştığını doğrulamak için, kitap içi bileşen Kullanıcı arabirimini görüntülemek için bir bağlantı noktası İleri kullanacağız.

İlk olarak, bookalıcının Pod adını alalım

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Aşağıdakine benzer bir çıktı görmeniz gerekir. Muhasebeci alıcı Pod 'larınızın eklenmiş benzersiz bir adı olacaktır.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod 'un adı olduktan sonra, şimdi bağlantı noktası-ilet komutunu kullanarak yerel sistemimizden AKS kümesi içindeki uygulamaya bir tünel ayarlayabilirsiniz. Yerel sistem bağlantı noktası 8080 için bağlantı noktasını ileriye doğru ayarlamak için aşağıdaki komutu çalıştırın. Ardından, belirtilen booksatınalmacı Pod adınızı kullanın.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Aşağıdakine benzer bir çıktı görmeniz gerekir.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Bağlantı noktası iletme oturumu yerine, bir tarayıcıdan aşağıdaki URL 'ye gidin `http://localhost:8080` . Artık, aşağıdaki görüntüye benzer bir tarayıcıda booksatınalmacı uygulama kullanıcı arabirimini görebilmelisiniz.

![NGıNX UI görüntüsü için OSA booksatınalmacı uygulaması](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde NGıNX giriş denetleyicisi oluşturma

Giriş denetleyicisi, Kubernetes Hizmetleri için ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırma sağlayan bir yazılım parçasıdır. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını ve rotaları yapılandırmak üzere kullanılır. Bir giriş denetleyicisi ve giriş kuralları kullanarak, bir Kubernetes kümesinde trafiği birden çok hizmete yönlendirmek için tek bir IP adresi kullanılabilir.

Uygulama, OSD tarafından yönetilen uygulamayı internet 'e göstermek için giriş denetleyicisi 'ni kullanacağız. Giriş denetleyicisini oluşturmak için Held 'yi kullanarak NGINX-giriş 'yi kullanın. Daha fazla yedeklilik sağlamak için `--set controller.replicaCount` parametresiyle iki NGINX giriş denetleyicisi çoğaltması dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

Ayrıca giriş denetleyicisinin bir Linux düğümü üzerinde zamanlanması gerekir. Giriş denetleyicisi, Windows Server düğümlerinde çalıştırılmamalıdır. Kubernetes zamanlayıcısına NGINX giriş denetleyicisini Linux tabanlı bir düğümde çalıştırmasını söylemek için `--set nodeSelector` parametresi kullanılarak bir düğüm seçici belirtilir.

> [!TIP]
> Aşağıdaki örnek, _Giriş-Basic_ adlı giriş kaynakları için bir Kubernetes ad alanı oluşturur. Gerektiğinde kendi ortamınız için bir ad alanı belirtin.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Kubernetes yük dengeleyici hizmeti NGıNX giriş denetleyicisi için oluşturulduğunda, aşağıdaki örnek çıktıda gösterildiği gibi dinamik bir genel IP adresi atanır:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Henüz giriş kuralı oluşturulmadı, bu nedenle iç IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir. Giriş kuralları aşağıdaki adımlarda yapılandırılır.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Booksatınalmacı hizmetini Internet 'te kullanıma sunma

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>NGıNX günlüklerini görüntüleme

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

Çıkış, giriş kuralı başarıyla uygulandığında NGıNX giriş denetleyicisi durumunu gösterir:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>NGıNX Hizmetleri ve booksatınalmacı hizmetini dışarıdan görüntüleme

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Giriş bildirimindeki ana bilgisayar adı test için kullanılan bir psuedo adı olduğundan, DNS adı Internet 'te kullanılamayacak. Alternatif olarak, kıvrımlı programını kullanabilir ve ana bilgisayar adı üst bilgisini NGıNX genel IP adresine geçeceğiz ve sizi booksatınalmacı hizmetine başarıyla bağlayan bir 200 kodu alabilirsiniz.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Öğretici: Azure Application Gateway ınress AKS eklentisini kullanarak açık hizmet ağı (OSD) tarafından yönetilen bir uygulamayı dağıtma

Açık hizmet ağı (OSı), kullanıcıların yüksek düzeyde dinamik mikro hizmet ortamları için kullanıma hazır Observability özelliklerini bir arada yönetmesine, güvenli hale getirmeye ve kullanıma almasına olanak tanıyan basit, Genişletilebilir, bulut Yerel hizmet ağlarından oluşur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
>
> - Geçerli OSD küme yapılandırmasını görüntüle
> - Ad alanındaki dağıtılan uygulamaları yönetmek için OSD için ad alanı (ler) i oluşturun
> - OSD tarafından yönetilecek ad alanlarını ekleme
> - Örnek uygulamayı dağıtma
> - AKS kümesi içinde çalışan uygulamayı doğrulama
> - Uygulamayı için giriş denetleyicisi olarak kullanılacak bir Azure Application Gateway oluşturun
> - Azure Application Gateway bir hizmeti internet 'e giriş ile kullanıma sunma

### <a name="before-you-begin"></a>Başlamadan önce

Bu makalede açıklanan adımlarda, bir AKS kümesi (Kubernetes ve üzeri) oluşturduğunuz varsayılır. `1.19+` Kubernetes RBAC etkinken), `kubectl` kümeyle bir bağlantı kurdu (Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa, [aks hızlı](./kubernetes-walkthrough.md)başlangıcı ' na BAKıN, aks OSI eklentisini yüklemiş ve giriş için yeni bir Azure Application Gateway oluşturacak.

Aşağıdaki kaynakların yüklü olması gerekir:

- Azure CLı, sürüm 2.20.0 veya üzeri
- `aks-preview`Uzantı sürümü 0.5.5 veya üzeri
- AKS kümesi sürümü 1.19 + Azure CNı ağını kullanma (bir Azure VNET 'e bağlı)
- OSD sürüm v 0.8.0 veya üzeri
- apt-get JQ 'yi al

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Geçerli OSD küme yapılandırmasını görüntüleyin ve doğrulayın

Aks kümesinde AKS için OSA eklentisi etkinleştirildikten sonra, geçerli yapılandırma parametrelerini OSU-config Kubernetes ConfigMap ' de görüntüleyebilirsiniz. ConfigMap özelliklerini görüntülemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Çıkış, küme için geçerli OSı yapılandırmasını gösterir.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**Permissive_traffic_policy_mode** **true** olarak yapılandırıldığını unutmayın. OSD 'de izin veren trafik ilkesi modu, [SMI](https://smi-spec.io/) trafik ilkesi zorlamasının atlanacağı bir moddur. Bu modda, OSA, bu hizmetlerle iletişim kurabilmesi için her bir Envoy ara sunucu arabasının hizmet kafesi ve programlar trafik ilkesi kurallarının bir parçası olan hizmetleri otomatik olarak bulur.

### <a name="create-namespaces-for-the-application"></a>Uygulama için ad alanları oluşturma

Bu öğreticide, aşağıdaki uygulama bileşenlerine sahip OSK kitaplığı uygulamasını kullanacağız:

- kitap alıcısı
- bookthief
- Kitaplığı
- kitap ambarı

Bu uygulama bileşenlerinin her biri için ad alanları oluşturun.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSD tarafından yönetilecek ad alanlarını ekleme

Ad alanlarını OSE ağı 'na eklediğinizde, bu, OSı denetleyicisinin uygulamanızla birlikte otomatik olarak Envoy dışarıdan ara sunucu kapsayıcıları eklemesine izin verir. OSE kitaplığı uygulaması ad alanlarını eklemek için aşağıdaki komutu çalıştırın.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Aşağıdaki çıkışı görmeniz gerekir:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Kitaplığı uygulamasını AKS kümesine dağıtma

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Dağıtım çıktılarının tümü aşağıda özetlenmiştir.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Booksatınalmacı hizmetini güncelleştirme

Booksatınalmacı hizmetini aşağıdaki hizmet bildirimiyle doğru gelen bağlantı noktası yapılandırması olarak güncelleştirin.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS kümesi içinde çalışan kitaplığı uygulamasını doğrulama

Bundan itibaren, kitaplığı çok Kapsayıcılı uygulamayı dağıttık, ancak yalnızca AKS kümesi içinden erişilebilir. Daha sonra, uygulamayı AKS kümesi dışında kullanıma sunmak için Azure Application Gateway giriş denetleyicisi ekleyeceğiz. Uygulamanın küme içinde çalıştığını doğrulamak için, kitap içi bileşen Kullanıcı arabirimini görüntülemek için bir bağlantı noktası İleri kullanacağız.

İlk olarak, bookalıcının Pod adını alalım

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Aşağıdakine benzer bir çıktı görmeniz gerekir. Muhasebeci alıcı Pod 'larınızın eklenmiş benzersiz bir adı olacaktır.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Pod 'un adı olduktan sonra, şimdi bağlantı noktası-ilet komutunu kullanarak yerel sistemimizden AKS kümesi içindeki uygulamaya bir tünel ayarlayabilirsiniz. Yerel sistem bağlantı noktası 8080 için bağlantı noktasını ileriye doğru ayarlamak için aşağıdaki komutu çalıştırın. Yalnızca belirli bir booksatınalmacı Pod adınızı kullanın.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Aşağıdakine benzer bir çıktı görmeniz gerekir.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Bağlantı noktası iletme oturumu yerine, bir tarayıcıdan aşağıdaki URL 'ye gidin `http://localhost:8080` . Artık, aşağıdaki görüntüye benzer bir tarayıcıda booksatınalmacı uygulama kullanıcı arabirimini görebilmelisiniz.

![Uygulama ağ geçidi Kullanıcı arabirimi görüntüsü için OSA booksatınalmacı uygulaması](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>AKS kümesi dışında booksatınalmacı uygulamasını kullanıma sunmak için bir Azure Application Gateway oluşturma

> [!NOTE]
> Aşağıdaki yönergeler, giriş için kullanılacak Azure Application Gateway yeni bir örneğini oluşturur. Kullanmak istediğiniz mevcut bir Azure Application Gateway varsa, Application Gateway giriş denetleyicisi eklentisini etkinleştirme bölümüne atlayın.

#### <a name="deploy-a-new-application-gateway"></a>Yeni bir Application Gateway dağıtma

> [!NOTE]
> Mevcut bir AKS kümesi için Application Gateway giriş denetleyicisi eklentisini etkinleştirmeye yönelik mevcut belgelere başvuruyoruz. OSE malzemelerinden dolayı bazı değişiklikler yapılmıştır. Konu hakkında daha ayrıntılı belgeler [burada](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)bulunabilir.

Artık, AKS kümenize, _MyCluster_ içindeki trafiğin yükünü dengelemek için kullanmak istediğiniz mevcut bir Application Gateway sahip olmak için yeni bir Application Gateway dağıtırsınız. Application Gateway adı _myApplicationGateway_ olacaktır, ancak önce _Mypublicıp_ adlı bir genel IP kaynağı ve _myvnet_ adres alanı 11.0.0.0/8 ve adres alanı 11.1.0.0/16 ile _Mysubnet_ adlı bir alt ağ ve _mypublicıp_ kullanarak _mysubnet_ öğesine Application Gateway dağıtmanız gerekir.

Bir AKS kümesi ve ayrı sanal ağlarda Application Gateway kullanırken, iki sanal ağın adres alanları çakışmamalıdır. AKS kümesinin dağıttığı varsayılan adres alanı 10.0.0.0/8 olduğundan Application Gateway sanal ağ adresi ön ekini 11.0.0.0/8 olarak ayarlayacağız.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway giriş denetleyicisi (AGIC) eklentisi, Application Gateway v1 SKU 'Larını **değil** , **yalnızca** Application Gateway v2 SKU 'larını (Standart ve WAF) destekler.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Azure CLı aracılığıyla mevcut bir AKS kümesi için AGIC eklentisini etkinleştirme

Azure CLı 'yı kullanmaya devam etmek istiyorsanız, oluşturduğunuz AKS kümesindeki AGIC eklentisini etkinleştirebilir, _MyCluster_ ve oluşturduğunuz var olan Application Gateway ( _myApplicationGateway_) kullanmak üzere agic eklentisini belirtebilirsiniz.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Aşağıdaki komutla Azure Application Gateway AKS eklentisinin etkinleştirildiğini doğrulayabilirsiniz.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Bu komut çıktıyı olarak göstermelidir `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>İki sanal ağı birbirine eşler

AKS kümesini kendi sanal ağında ve başka bir sanal ağdaki Application Gateway dağıttığımız için, trafiğin Application Gateway akışın kümedeki yığınlara akmasını sağlamak üzere iki sanal ağı birbirine eşleyebileceksiniz. İki sanal ağ arasındaki eşleme, bağlantının çift yönlü olduğundan emin olmak için Azure CLı komutunu iki ayrı kez çalıştırmayı gerektirir. İlk komut Application Gateway sanal ağından AKS sanal ağına bir eşleme bağlantısı oluşturacaktır; İkinci komut diğer yönde bir eşleme bağlantısı oluşturacaktır.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Booksatınalmacı hizmetini Internet 'te kullanıma sunma

Azure Application Gateway ile booksatınalmacı hizmetini Internet 'te kullanıma sunmak için aşağıdaki giriş bildirimini AKS kümesine uygulayın.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Aşağıdaki çıkışı görmelisiniz:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Giriş bildirimindeki ana bilgisayar adı test için kullanılan bir sözde ad olduğundan, DNS adı Internet 'te kullanılamayacak. Alternatif olarak, kıvrımlı programı kullanabilir ve ana bilgisayar adı üst bilgisini Azure Application Gateway genel IP adresine geçeceğiz ve sizi booksatınalmacı hizmetine başarıyla bağlayan bir 200 kodu alabilirsiniz.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Aşağıdaki çıkışı görmelisiniz:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Sorun giderme

- [AGIC sorun giderme belgeleri](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Ek sorun giderme araçları, AGIC 'in GitHub deposunda kullanılabilir](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Azure Izleyici ve uygulamalar öngörülerini kullanarak hizmet ağı (OSM) Izleme ve Observability 'i açın

Azure Izleyici ve Azure Application Insights, bulut ve şirket içi ortamlarınızdaki telemetri toplama, çözümleme ve üzerinde işlem yapmaya yönelik kapsamlı bir çözüm sunarak uygulamalarınızın ve hizmetlerinizin kullanılabilirliğini ve performansını en üst düzeye çıkarmanıza yardımcı olur.

OSı AKS eklentisi, bu Azure hizmetlerinin her ikisinde de ayrıntılı tümleştirmelere sahip olacak ve OSK ölçümleri tarafından sunulan kritik KPI 'Leri görüntülemek ve bunlara yanıt vermek için daha az bir Azure deneyimi sağlar. Bu hizmetleri OSM AKS eklentisi için etkinleştirme ve yapılandırma hakkında daha fazla bilgi için daha fazla bilgi için bkz. [OSM Için Azure izleyici](https://aka.ms/azmon/osmpreview) sayfası.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Öğretici: Observability için açık hizmet ağı (OSD) ölçümlerini görüntülemek için Prometheus, Grafana ve Jaeger 'yi El Ile dağıtın

> [!WARNING]
> Prometheus, Grafana ve Jaeger yüklemesi, bu araçların OSı ölçüm verilerini görüntülemek için nasıl kullanılabileceğini göstermek için genel kılavuz olarak sunulmaktadır. Yükleme Kılavuzu, bir üretim kurulumu için kullanılmaz. Gereksinimlerinize göre yüklemelerine en iyi şekilde uyum sağlamak için lütfen her bir aracın belgelerine bakın. Çoğu önemli kalıcı depolamanın olmaması, yani bir Prometheus Grafana ve/veya Jaeger Pod 'ları sonlandırıldıktan sonra tüm verilerin kaybedilmesi anlamına gelir.

Açık hizmet ağı (OSD), ağ içindeki tüm trafikle ilgili ayrıntılı ölçümler oluşturur. Bu ölçümler, kullanıcıların uygulamalarını sorun giderme, bakımını yapma ve analiz etmesine yardımcı olan, kafesteki uygulamaların davranışına ilişkin öngörüler sağlar.

Bugün OSA 'dan başlayarak doğrudan sepet proxy 'lerinde (Envoy) ölçümleri toplar. OSD, kafesteki tüm hizmetler için gelen ve giden trafik için zengin ölçümler sağlar. Bu ölçümler sayesinde Kullanıcı, trafiğin genel hacmi, trafikte hatalar ve isteklerin yanıt süresi hakkında bilgi alabilir.

OSD, ağ içinde çalışan tüm uygulamalar için tutarlı trafik ölçümleri ve istatistikleri toplamak ve depolamak üzere Prometheus kullanır. Prometheus, Kubernetes ve hizmet ağı ortamlarında yaygın olarak kullanılan (ancak bunlarla sınırlı olmamak üzere) açık kaynaklı bir izleme ve uyarı araç setidir.

Kafesin parçası olan her bir uygulama, Prometheus biçiminde ölçümler (proxy ölçümleri) sunan bir Envoy kutucar içeren bir pod 'da çalışır. Ayrıca, kafesin bir parçası olan her Pod, Prometheus sunucusu 'nun uygulamayı dinamik olarak hurdaya koymasına olanak sağlayan Prometheus ek açıklamalarını içerir. Bu mekanizma, her yeni bir ad alanı/Pod/hizmet eklendiğinde, ölçümlerin otomatik olarak yeniden kullanılmasını sağlar.

OSı ölçümleri, açık kaynak görselleştirme ve analiz yazılımı olan Grafana ile görüntülenebilir. Ölçümleri sorgulamanızı, görselleştirmenizi, uyarıları görmenizi ve araştırmanızı sağlar.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
>
> - Prometheus örneği oluşturma ve dağıtma
> - OSD 'yi Prometheus scraping 'e izin verecek şekilde yapılandırma
> - Prometheus Configmap 'i güncelleştirme
> - Grafana örneği oluşturma ve dağıtma
> - Grafana 'yi Prometheus veri kaynağıyla yapılandırma
> - Grafana için OSD panosunu içeri aktarma
> - Bir Caeger örneği oluşturma ve dağıtma
> - OSE için Caeger izlemeyi yapılandırma

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>OSD için bir Prometheus örneği dağıtın ve yapılandırın

Prometheus örneğini dağıtmak için Held kullanacağız. Held aracılığıyla Prometheus yüklemek için aşağıdaki komutları çalıştırın:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Yükleme başarılı olduysa aşağıdaki benzer çıktıyı görmeniz gerekir. Prometheus sunucu bağlantı noktası ve küme DNS adını unutmayın. Bu bilgiler daha sonra, Grafana için bir veri kaynağı olarak Prometheus 'yi yapılandırmak üzere kullanılacaktır.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>OSD 'yi Prometheus scraping 'e izin verecek şekilde yapılandırma

OSE bileşenlerinin Prometheus Scrapes için yapılandırıldığından emin olmak için, OSU-config yapılandırma dosyasında bulunan **prometheus_scraping** yapılandırmasını denetlemek istiyoruz. Aşağıdaki komutla yapılandırmayı görüntüleyin:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Bir önceki komutun çıkışı, `true` Prometheus scraping için OSI yapılandırılmışsa döndürmelidir. Döndürülen değer ise, `false` yapılandırmayı olacak şekilde güncelleştirmeniz gerekecektir `true` . Osu Prometheus scraping **'** i açmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Aşağıdaki çıkışı görmelisiniz.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Prometheus Configmap 'i güncelleştirme

Varsayılan Prometheus yüklemesi iki Kubernetes configmaps içerir. Aşağıdaki komutla Prometheus configmaps listesini görebilirsiniz.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

**Kararlı-Prometheus-Server** configmap 'te bulunan Prometheus. yıml YAPıLANDıRMASıNı aşağıdaki OSU yapılandırmasıyla değiştirmek gerekecektir. Bu görevi gerçekleştirmek için çeşitli dosya düzenleyici teknikleri vardır. Basit ve güvenli bir yöntem configmap 'i dışarı aktarmak, yedekleme için bir kopyasını oluşturmak ve ardından Visual Studio Code gibi bir düzenleyiciyle düzenlemek.

> [!NOTE]
> Visual Studio Code yüklü değilse, [buradan](https://code.visualstudio.com/Download)indirip yükleyebilirsiniz.

İlk olarak **kararlı-Prometheus-Server** configmap 'i dışarı aktarıp yedekleme için bir kopyasını oluşturalım.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Sonra, düzenlemek için Visual Studio Code kullanarak dosyayı açalım.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Visual Studio Code düzenleyicisinde configmap ' i açtıktan sonra, Prometheus. yıml dosyasını aşağıdaki OSı yapılandırmasıyla değiştirin ve dosyayı kaydedin.

> [!WARNING]
> YAML dosyasının girintileme yapısını tutmanız son derece önemlidir. YAML dosya yapısında yapılan tüm değişiklikler, configmap 'in yeniden uygulanamayacak şekilde sonuçlanabilir.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Güncelleştirilmiş configmap YAML dosyasını aşağıdaki komutla uygulayın.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Eksik bir Kubernetes ek açıklaması için gerekli bir ileti alabilirsiniz. Bu şimdilik yok sayılabilir.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Prometheus 'nin OSU kafesi ve API uç noktalarını hurdaya almak için yapılandırıldığını doğrulayın

Prometheus 'ın OSK kafesi ve API uç noktalarına atık olarak yapılandırıldığını doğrulamak için, Prometheus Pod 'a bağlantı göndereceğiz ve hedef yapılandırmayı görüntüleyebiliyoruz. Aşağıdaki komutları çalıştırın.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Şu kadar bir tarayıcı açın `http://localhost:9090/targets`

Aşağı kaydırırsanız, **aşağıda gösterildiği gibi** tanımlanan diğer OSD ölçümlerinin yanı sıra tüm SMI ölçüm uç noktaları durumunu görmeniz gerekir.

![OSA Prometheus hedef ölçümleri Kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>OSD için bir Grafana örneği dağıtma ve yapılandırma

Grafana örneğini dağıtmak için Held kullanacağız. Held aracılığıyla Grafana yüklemek için aşağıdaki komutları çalıştırın:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Daha sonra, Grafana sitesinde oturum açmak için varsayılan Grafana parolasını alacağız.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Grafana parolasını unutmayın.

Daha sonra, oturum açmak için Grafana panosuna doğru Grafana Pod bağlantı noktasını elde edeceğiz.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Şu kadar bir tarayıcı açın `http://localhost:3000`

Aşağıda gösterildiği gibi oturum açma ekranında **yönetici** Kullanıcı adı olarak girin ve daha önce yakalanan Grafana parolasını kullanın.

![OSA Grafana oturum açma sayfası kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Grafana Prometheus veri kaynağını yapılandırma

Grafana 'de başarıyla oturum açtıktan sonra, bir sonraki adım Grafana için veri kaynakları olarak Prometheus eklemektir. Bunu yapmak için, sol taraftaki menüdeki yapılandırma simgesine gidin ve aşağıda gösterildiği gibi veri kaynakları ' nı seçin.

![OSA Grafana DataSources sayfası kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

**Veri kaynağı Ekle** düğmesine tıklayın ve zaman serisi veritabanları ' nın altındaki Prometheus ' ı seçin.

![OSA Grafana DataSources seçim sayfası kullanıcı arabirimi resmi](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

**Aşağıdaki Prometheus veri kaynağınızı yapılandırın** SAYFASıNDA, http url ayarı Için Prometheus hizmeti Için Kubernetes kümesi FQDN 'sini girin. Varsayılan FQDN olmalıdır `stable-prometheus-server.default.svc.cluster.local` . Prometheus hizmet uç noktasını girdikten sonra, sayfanın en altına gidin ve **& testi kaydet**' i seçin. Veri kaynağının çalıştığını belirten yeşil bir onay kutusu almalısınız.

#### <a name="importing-osm-dashboards"></a>OSD panoları içeri aktarılıyor

OSA panoları her ikisi de ile kullanılabilir:

- [Depomız](https://github.com/grafana/grafana)ve Web Yöneticisi portalı aracılığıyla JSON blob 'ları olarak alınabilir.
- veya [Grafana.com adresinden çevrimiçi](https://grafana.com/grafana/dashboards/14145)

Bir panoyu içeri aktarmak için `+` sol taraftaki oturum açma menüsünü bulun ve öğesini seçin `import` .
Panoyu üzerinde kendi KIMLIĞINE göre doğrudan içeri aktarabilirsiniz `Grafana.com` . Örneğin, `OSM Mesh Details` panonuzun kimliği kullandığından `14145` , kimliği doğrudan form üzerinde kullanabilir ve `import` şunları seçebilirsiniz:

![Osu Grafana panosu Içeri aktarma sayfası kullanıcı arabirimi resmi](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

İçeri Aktar ' ı seçtiğinizde, içeri aktarılan panonuz size otomatik olarak gönderilir.

![Osu Grafana Pano kafesi ayrıntıları sayfası kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>OSD için Kubernetes üzerinde bir Jaeger Işleci dağıtın ve yapılandırın

[Jaeger](https://www.jaegertracing.io/) , dağıtılmış sistemleri izlemek ve sorunlarını gidermek için kullanılan açık kaynaklı bir izleme sistemidir. Bu, yeni bir örnek olarak OSD ile dağıtılabilir veya kendi örneğinizi getirebilirsiniz. Aşağıdaki yönergeler, `jaeger` AKS kümesindeki ad alanına yeni bir Jaeger örneği dağıtır.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>AKS kümesine Jaeger dağıtma

Caeger yüklemek için aşağıdaki bildirimi uygulayın:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>OSD eklentisi için Izlemeyi etkinleştir

Sonraki adımda OSK eklentisi için izlemeyi etkinleştirmemiz gerekir.

> [!NOTE]
> Şu andan itibaren, izleme özellikleri şu anda OSI-config configmap içinde yok. Bu, OSA AKS eklentisinin yeni bir sürümünde başlatılabilir hale getirilir.

OSA eklentisi için izlemeyi etkinleştirmek üzere aşağıdaki komutu çalıştırın:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Bağlantı noktası iletme ile Jaeger Kullanıcı arabirimini görüntüleme

Jaeger 'ın Kullanıcı arabirimi 16686 bağlantı noktasında çalışıyor. Web Kullanıcı arabirimini görüntülemek için kubectl bağlantı noktası-ilet ' i kullanabilirsiniz:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

Tarayıcıda, kitaplığı tanıtımına göre dağıtılan çeşitli uygulamalardan seçim yapmanıza olanak sağlayan bir hizmet açılan kutusu görmeniz gerekir. Tüm yayılmaları görüntülemek için bir hizmet seçin. Örneğin, bir saatin geri dönmesi ile booksatınalmacı ' i seçerseniz, bu sürenin zamana göre sıralandığı, kitaplığı-v1 ve kitaplığı-v2 ile etkileşimlerini görebilirsiniz.

![OSp Jaeger Izleme sayfası kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Daha ayrıntılı olarak görüntülemek için herhangi bir öğeyi seçin. İzlemeleri karşılaştırmak için birden çok öğe seçin. Örneğin, kitap defterlerinin bir kez, kitapı ve kitaplığı-v2 ile, zaman içinde belirli bir anda karşılaştırabilirsiniz.

Çeşitli uygulamaların nasıl etkileşim kurduğunu/iletişim kurduğunu gösteren bir grafiği görüntülemek için sistem mimarisi sekmesini de seçebilirsiniz. Bu, uygulamalar arasında trafik akışını nasıl bir fikir verir.

![OSp Jaeger sistem mimarisi Kullanıcı arabirimi görüntüsü](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Açık hizmet ağı (OSDK) AKS eklentisi sorun giderme kılavuzu

OSı AKS eklentisini dağıttığınızda, zaman zaman bir sorunla karşılaşabilirsiniz. Aşağıdaki kılavuzlar, hataları gidermeye ve genel sorunları çözmenize yardımcı olur.

### <a name="verifying-and-troubleshooting-osm-components"></a>OSD bileşenleri doğrulanıyor ve sorunlarını giderme

#### <a name="check-osm-controller-deployment"></a>OSD denetleyici dağıtımını denetle

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Sağlıklı bir OSM denetleyicisi şöyle görünür:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>OSD denetleyicisi Pod 'u denetleyin

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Sağlıklı bir OSM Pod şöyle görünür:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Bir denetleyici bir noktada çıkarıldık olsa da, 1/1 'e ücretsiz olan ve 0 yeniden başlatmalarla çalışan bir tane var. Kullanılabilir sütun 1/1 dışında bir şeydir hizmet ağı kopuk durumda olur.
0/1 ile READY sütunu, denetim düzlemi kapsayıcısının kilitlendiğini belirtiyor-günlükleri almak için ihtiyacımız var. Bkz. Azure Destek Merkezi 'nden OSD denetleyici günlüklerini edinme bölümü. Sütun, daha sonra 1 ' den daha yüksek bir sayı ile kullanılabilir ve yüklü olan sıfları olduğunu gösterir. OSE denetleyicisi, büyük olasılıkla kendisine bağlı herhangi bir çakışmalarla çalışmaz.

> [!NOTE]
> Sürüm v 0.8.2 itibariyle OSA denetleyicisi HA modunda değildir ve 1-tek Pod çoğaltma sayısıyla dağıtılan bir ile çalıştırılır. Pod, sistem durumu araştırmalarını sağlar ve gerekirse kubelet tarafından yeniden başlatılır.

#### <a name="check-osm-controller-service"></a>OSD denetleyici hizmetini denetle

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Sağlıklı bir OSM denetleyicisi hizmeti şöyle görünür:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> KÜME IP 'si farklı olabilir. Hizmet adı ve bağlantı noktalarının Yukarıdaki örnekle aynı olması gerekir.

#### <a name="check-osm-controller-endpoints"></a>OSD denetleyici uç noktalarını denetle

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Sağlıklı bir OSM denetleyicisi uç noktası şu şekilde görünür:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>OSD Injector dağıtımını denetle

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Sağlıklı bir OSM Injector dağıtımı şöyle görünür:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>OSK Injector Pod 'u denetle

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Sağlıklı bir OSM Injecpod, şunun gibi görünür:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>OSD Injector hizmetini denetle

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Sağlıklı bir OSM Injector hizmeti şöyle görünür:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>OSA uç noktalarını denetle

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Sağlıklı bir OSM uç noktası şöyle görünür:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Web kancalarını doğrulamak ve bunları göz atın

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Sağlıklı bir OSM, Web kancasını doğrulamak şuna benzer:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Sağlıklı bir OSM, Web kancasını değiştirici şöyle görünür:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Doğrulama Web kancasının hizmetini ve CA grubunu denetleyin

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

İyi yapılandırılmış bir Web kancası yapılandırması, tam olarak şöyle görünür:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Web kancasının hizmet ve CA grubunu denetleyin

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Çok iyi yapılandırılmış Web kancası yapılandırması, tam olarak şöyle görünür:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>OSD denetçisinin bir CA paketi için doğrulama (veya değiştirici) Web kancasına sahip olup olmadığını denetleyin

> [!NOTE]
> V 0.8.2 itibariyle, AKS RP 'nin doğrulama Web kancasını yüklediklerini, AKS Bağdaştırcıları 'nın var olduğunu, ancak OSı denetleyicisi de CA paketini dolduran biridir.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Bu sayı, bayt sayısını veya CA demeti boyutunu gösterir. Bu boşsa, 0 veya 1000 altında bir sayı, CA paketinin doğru bir şekilde sağlanmadığını gösterir. Doğru bir CA paketi olmadan, doğrulama Web kancası hata verebilir ve kullanıcının kuı-System ad alanındaki OSE-config ConfigMap üzerinde değişiklik yapmasını yasaklamalıdır.

CA demeti yanlış olduğunda örnek bir hata oluştu:

- OSI-config ConfigMap 'i değiştirme girişimi:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Hata:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

**Doğrulama** Web kancası yapılandırmasında hatalı sertifika olduğunda geçici çözüm:

- Seçenek 1-OSD denetleyicisi 'ni yeniden Başlat-bu, OSD denetleyicisini yeniden başlatacak. Başlangıç sırasında, Web kancalarını azaltma ve doğrulama için CA paketinin üzerine yazılır.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Seçenek 2-seçenek 2. Doğrulama Web kancasını silme-doğrulama Web kancasının kaldırılması, configmap 'in zaman aşımı sonrasında `osm-config` artık doğrulanmaz. Herhangi bir düzeltme eki ilerlecektir. AKS Bağdaştırtıcı, bazı noktaları doğrulayan Web kancasının mevcut olduğundan emin olur ve yeniden oluşturur. CA paketini hızlı bir şekilde yeniden yazmak için OSA denetleyicisinin yeniden başlatılması gerekebilir.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Seçenek 3-silme ve Düzeltme Eki: aşağıdaki komut, doğrulama Web kancasını silecek, bu da herhangi bir değer ekleyebilmenizi sağlar ve hemen bir düzeltme eki uygulamaya çalışır. AKS Bağdaştırmlarını en büyük olasılıkla, doğrulama Web kancasını mutabık kılmak ve geri yüklemek için yeterli zaman yoktur:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>`osm-config` **Configmap** 'i denetleyin

> [!NOTE]
> OSE denetleyicisi, `osm-config` configmap 'in kuin-System ad alanında mevcut olmasını gerektirmez. Denetleyici, yapılandırma için makul varsayılan değerlere sahiptir ve bu olmadan çalışabilir.

Mevcut olup olmadığını denetleyin:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

OSI-config ConfigMap içeriğini denetleyin

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap değerleri:

| Anahtar                              | Tür   | İzin Verilen Değerler                                          | Varsayılan değer                          | İşlev                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| giden                           | bool   | doğru, yanlış                                             | `"false"`                              | Kafeste çıkış imkanı sunar.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | doğru, yanlış                                             | `"true"`                               | Proxy bağlantıları, sertifikalar ve SMı ilkeleri gibi ağ ile ilgili bilgileri listelemek için OSA-Controller pod üzerinde bir hata ayıklama uç noktası sağlar.                                                                                    |
| enable_privileged_init_container | bool   | doğru, yanlış                                             | `"false"`                              | Kafeste pod için ayrıcalıklı init kapsayıcıları sunar. False olduğunda, init kapsayıcıları yalnızca NET_ADMIN sahiptir.                                                                                                                                   |
| envoy_log_level                  | string | Trace, Debug, Info, uyarı, Warn, hata, kritik, kapalı | `"error"`                              | Envoy ara sunucu arabasının günlüğe kaydetme ayrıntı düzeyini, yalnızca yeni oluşturulan kümeler için geçerlidir. Mevcut yığınların günlük düzeyini güncelleştirmek için, ile dağıtımı yeniden başlatın `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | string | a. b. c. d/x biçimindeki IP aralıklarının virgülle ayrılmış listesi | `-`                                    | Sepet proxy 'si tarafından giden trafik yakalanmasını hariç tutulacak IP adresi aralıklarının genel listesi.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | doğru, yanlış                                             | `"false"`                              | Olarak ayarlandığında `true` , kafeste izin ver modunu (ağ üzerinde hiçbir trafik ilkesi zorlaması) sağlar. , Olarak ayarlandıysa `false` , ağ içinde Reddet-tüm trafik ilkesi (örneğin, `SMI Traffic Target` hizmetlerin iletişim kurması için gereklidir) sağlar. |
| prometheus_scraping              | bool   | doğru, yanlış                                             | `"true"`                               | Sepet proxy 'lerinde Prometheus ölçümleri korumasını sunar.                                                                                                                                                                                 |
| service_cert_validity_duration   | string | 24h, 1h30m (herhangi bir süre)                          | `"24h"`                                | Her biri isteğe bağlı kesir ve bir birim son eki ile ondalık sayı dizisi olarak temsil edilen hizmet sertifikası geçerlilik süresini ayarlar.                                                                                             |
| tracing_enable                   | bool   | doğru, yanlış                                             | `"false"`                              | Ağ için Jaeger izlemeyi sunar.                                                                                                                                                                                                    |
| tracing_address                  | string | Jaeger. Mesh-Namespace. svc. Cluster. Local                 | `jaeger.kube-system.svc.cluster.local` | İzleme etkinse, Jaeger dağıtımının adresi.                                                                                                                                                                                |
| tracing_endpoint                 | string | /api/v2/yaymalar                                           | /api/v2/yaymalar                          | İzleme etkinse, verileri izlemek için uç nokta.                                                                                                                                                                                          |
| tracing_port                     | int    | sıfır olmayan herhangi bir tamsayı değeri                              | `"9411"`                               | İzlemenin etkinleştirildiği bağlantı noktası.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | doğru, yanlış                                             | `"false"`                              | Ağ üzerinde HTTPS girişini sunar.                                                                                                                                                                                                      |
| config_resync_interval           | string | 1 dakika altında bunu devre dışı bırakır                            | 0 (devre dışı)                           | 1m (60s) üzerindeki bir değer sağlandığında OSM denetleyicisi verilen aralıktaki her bir bağlı olan her bir uygun yapılandırmayı gönderir                                                                                                    |

#### <a name="check-namespaces"></a>Ad alanlarını denetle

> [!NOTE]
> Kuto-System ad alanı hiçbir durumda bir hizmet ağı 'na katılmayacaktır ve aşağıdaki anahtar/değerlerle hiçbir şekilde etiketlendirilmez ve/veya açıklama eklenir.

Bu komutu, `osm namespace add` ad alanlarını belirli bir hizmet ağıyla birleştirmek için kullanırız.
Bir k8s ad alanı, kafesin bir parçası olduğunda (veya ağ 'ın parçası olması için) aşağıdakilerin doğru olması gerekir:

Ek açıklamaları görüntüle

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Aşağıdaki ek açıklamanın mevcut olması gerekir:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Etiketleri görüntüleme

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Aşağıdaki etiket bulunmalıdır:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Bir ad alanı, ile açıklanmaz `"openservicemesh.io/sidecar-injection": "enabled"` veya `"openservicemesh.io/monitored-by": "osm"` osz Injector ile etiketlenmez.

> Note: çağrıldıktan sonra `osm namespace add` yalnızca **Yeni** Pod 'ler bir Envoy sidecar ile eklenir. Mevcut Pod 'ler ile yeniden başlatılmalıdır `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>SMı CRDs 'yi doğrulama:

Kümede gerekli CRDs olup olmadığını denetleyin:

```azurecli-interactive
kubectl get crds
```

Kümede aşağıdakilerin yüklü olması gerekir:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Bu komutla yüklenen CRDs sürümlerini alın:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Beklenen çıktı:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSA denetleyici v 0.8.2 aşağıdaki sürümleri gerektirir:

- traffictargets.access.smi-spec.io- [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io- [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io- [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io-desteklenmiyor
- trafficsplits.split.smi-spec.io- [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*. metrics.smi-spec.io- [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

CRDs eksikse, bunları kümeye yüklemek için aşağıdaki komutları kullanın:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>AKS kümeniz için açık hizmet ağı (OSA) eklentisini devre dışı bırakın

OSı eklentisini devre dışı bırakmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
