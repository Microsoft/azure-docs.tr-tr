---
title: Azure Red Hat OpenShift 3.11’den Azure Red Hat OpenShift 4’e geçme
description: Azure Red Hat OpenShift 3.11’den Azure Red Hat OpenShift 4’e geçme
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: geçiş, Aro, OpenShift, Red Hat
ms.openlocfilehash: 371672de83a6d745d7b367f8327a64e11059923e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653287"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Azure Red Hat Openshıft 3,11 ' den Azure Red Hat OpenShift 4 ' e geçiş yapın

Openshıft 4 üzerinde Azure Red Hat Openshıft, Red Hat Core işletim sistemi, özel kümeler, kendi sanal ağ desteğini getirme ve tam küme yöneticisi rolü üzerinde Kubernetes 1,16 ' i getirir. Ayrıca, operatör çerçevesi, operatör hub 'ı ve OpenShift hizmeti ağı için destek gibi birçok yeni özellik de kullanılabilir.

Azure Red Hat Openshıft 3,11 ' den Azure Red Hat OpenShift 4 ' e başarıyla geçiş yapmak için [depolama, ağ, günlüğe kaydetme, güvenlik ve izleme farklılıklarını](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html)gözden geçirdiğinizden emin olun.

Bu makalede, bir Azure Red Hat OpenShift 3,11 kümesinden Azure Red Hat 4 kümesine nasıl geçiş yapılacağı gösterilmektedir.

> [!NOTE]
> Denetim düzlemi geçiş yardım aracı gibi Red Hat OpenShift geçiş araçları ve küme uygulaması geçiş aracı (CAM), Azure Red Hat OpenShift 3,11 kümeleriyle birlikte kullanılamaz.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir Azure Red Hat Openshıft 3,11 kümeniz olduğunu varsaymaktadır.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Hedef Azure Red Hat OpenShift 4 kümesi oluşturma

İlk olarak, hedef küme olarak kullanmak istediğiniz [Azure Red Hat OpenShift 4 kümesini oluşturun](tutorial-create-cluster.md) . Bu, temel yapılandırmayı kullanacağız. Farklı ayarlarla ilgileniyorsanız, [Azure Red Hat OpenShift 4 kümesi oluşturma öğreticisi](tutorial-create-cluster.md)' ne bakın.

Ana ve çalışan düğümleri için iki boş alt ağa sahip bir sanal ağ oluşturun.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Ardından, kümeyi oluşturmak için aşağıdaki komutu kullanın.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Hedef OpenShift 4 kümesini yapılandırma

### <a name="authentication"></a>Kimlik Doğrulaması

Kullanıcıların Azure Red Hat OpenShift ile etkileşime geçmesini sağlamak için öncelikle kümeye kimlik doğrulaması yapılmalıdır. Kimlik doğrulama katmanı, isteklerle ilişkili kullanıcıyı Azure Red Hat OpenShift API 'sine göre tanımlar. Yetkilendirme katmanı daha sonra isteğin izin verilip verilmeyeceğini belirlemede istekte bulunan kullanıcı hakkındaki bilgileri kullanır.

Bir Azure Red Hat OpenShift 4 kümesi oluşturulduğunda, geçici bir yönetici kullanıcı oluşturulur. [Kümenize bağlanın](tutorial-connect-cluster.md), kullanıcılar ve gruplar ekleyin ve her ikisi için [uygun izinleri yapılandırın](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html) .

### <a name="networking"></a>Ağ

Azure Red Hat Openshıft 4 kümenizdeki ağı kurmak için birkaç farklı işleç kullanır: [küme ağı işleci](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [DNS operatörü](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)ve giriş [işleci](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html). Bir Azure Red Hat OpenShift 4 kümesinde ağ ayarlama hakkında daha fazla bilgi için [Ağ Diyagramı](concepts-networking.md) ' na bakın ve [ağ](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)' ı anlayın.

### <a name="storage"></a>Depolama
Azure Red Hat OpenShift 4 aşağıdaki PersistentVolume eklentilerini destekler:

- AWS elastik blok deposu (EBS)
- Azure diski
- Azure dosyası
- GCE kalıcı disk
- HostPath
- iSCSI
- Yerel birim
- NFS
- Red Hat OpenShift kapsayıcı depolaması

Bu depolama türlerini yapılandırma hakkında daha fazla bilgi için bkz. [kalıcı depolamayı yapılandırma](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Kayıt Defteri

Azure Red Hat OpenShift 4, kaynak kodınızdan görüntü oluşturabilir, bunları dağıtabilir ve yaşam döngüsünü yönetebilir. Bunu etkinleştirmek için Azure Red Hat Openshıft, görüntüleri yerel olarak yönetmek için Azure Red Hat OpenShift ortamınızda dağıtılabilen 4 [dahili ve tümleşik bir kapsayıcı görüntüsü kayıt defteri](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html) sağlar.

[Azure Container Registry](../container-registry/index.yml), [Red Hat Quay kayıt defterleri](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)veya bir [kimlik doğrulaması etkin Red hat kayıt defteri](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)gibi dış kayıt defterleri kullanıyorsanız, kümenin depolara erişmesine izin vermek için kümeye kimlik bilgileri sağlamak için adımları izleyin.

### <a name="monitoring"></a>İzleme

Azure Red Hat OpenShift, Prometheus açık kaynak projesine ve daha geniş ekonomik sisteme dayalı, önceden yapılandırılmış, önceden yüklenmiş ve kendi kendini güncelleştiren bir izleme yığını içerir. Küme bileşenlerinin izlenmesini sağlar ve küme yöneticisine her türlü sorun ve bir Grafana Pano kümesi hakkında anında bildirim gönderir. Küme izleme yığını yalnızca Azure Red Hat OpenShift kümelerini izlemek için desteklenir. Daha fazla bilgi için bkz. [Azure Red Hat OpenShift Için küme izleme](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html).

Azure [Red Hat openshift 3,11 Için kapsayıcılar Için Azure izleyici](../azure-monitor/containers/container-insights-azure-redhat-setup.md)'yi kullandıysanız, [Azure Red Hat OpenShift 4 kümelerinin](../azure-monitor/containers/container-insights-azure-redhat4-setup.md) kapsayıcıları için Azure izleyicisini da etkinleştirebilir ve aynı Log Analytics çalışma alanını kullanmaya devam edebilirsiniz.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>DNS veya yük dengeleyici yapılandırmanızı yeni kümeye taşıma

Azure Traffic Manager kullanıyorsanız, hedef kümenize başvuracak ve bu uç noktaların önceliğini belirlemek için uç noktalar ekleyin.

## <a name="deploy-application-to-your-target-cluster"></a>Hedef kümenize uygulama dağıtma

Hedef kümenizi iş yükünüz için doğru şekilde yapılandırdıktan sonra, [kümenize bağlanın](tutorial-connect-cluster.md) ve projeleriniz için gerekli uygulamaları, bileşenleri veya hizmetleri oluşturun. Azure Red Hat OpenShift, bunları git, kapsayıcı görüntüleri, Red Hat geliştirici kataloğu, Dockerfile, YAML/JSON tanımı veya katalogdan bir veritabanı hizmeti seçerek oluşturmanıza olanak sağlar.

## <a name="delete-your-source-cluster"></a>Kaynak kümenizi silme
Azure Red Hat Openshıft 4 kümenizin düzgün ayarlandığını doğruladıktan sonra, Azure Red Hat Openshıft 3,11 kümenizi silin.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Sonraki adımlar
Red Hat OpenShift belgelerine [buradan](https://docs.openshift.com/container-platform/4.6/welcome/index.html)göz atın.
