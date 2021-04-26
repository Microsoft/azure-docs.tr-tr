---
title: Azure portal Kubernetes kaynaklarına erişin
description: Azure portal Azure Kubernetes hizmeti (AKS) kümesini yönetmek için Kubernetes kaynaklarıyla nasıl etkileşim kuracağınızı öğrenin.
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: ce5dc74dc3625b2b1fed447c4e6480308267d32a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578679"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Azure portal Kubernetes kaynaklarına erişin

Azure portal, Azure Kubernetes Service (AKS) kümenizdeki Kubernetes kaynaklarına kolay erişim sağlayan bir Kubernetes kaynak görünümü içerir. Azure portal Kubernetes kaynaklarını görüntüleme, Azure portal ve komut satırı aracı arasındaki bağlam geçişini azaltır ve `kubectl` Kubernetes kaynaklarınızı görüntüleme ve düzenlemeyle ilgili deneyimi kolaylaştırır. Kaynak Görüntüleyicisi Şu anda dağıtımlar, pods ve çoğaltma kümeleri gibi birden çok kaynak türü içerir.

Azure portal Kubernetes kaynak görünümü, kullanım dışı olan [aks Pano eklentisinin][kubernetes-dashboard]yerini alır.

## <a name="prerequisites"></a>Önkoşullar

Azure portal Kubernetes kaynaklarını görüntülemek için bir AKS kümeniz olması gerekir. Herhangi bir küme desteklenir, ancak Azure Active Directory (Azure AD) Tümleştirmesi kullanılıyorsa, kümenizde [aks tarafından yönetilen Azure AD tümleştirmesi][aks-managed-aad]kullanılmalıdır. Kümeniz eski Azure AD kullanıyorsa, kümenizi portalda veya [Azure CLI][cli-aad-upgrade]ile yükseltebilirsiniz. Yeni bir AKS kümesi oluşturmak için [Azure Portal de kullanabilirsiniz][portal-cluster] .

## <a name="view-kubernetes-resources"></a>Kubernetes kaynaklarını görüntüle

Kubernetes kaynaklarını görmek için Azure portal AKS kümenize gidin. Sol taraftaki Gezinti Bölmesi kaynaklarınıza erişmek için kullanılır. Kaynaklar şunları içerir:

- **Ad alanları** , kümenizin ad alanlarını görüntüler. Ad alanı listesinin en üstündeki filtre, ad alanı kaynaklarınızı filtrelemeye ve görüntülemeye yönelik hızlı bir yol sağlar.
- **Iş yükleri** , kümenize dağıtılan dağıtımlar, pods, çoğaltma kümeleri, durum bilgisi bulunan kümeler, Daemon kümeleri, işler ve cron işleri hakkındaki bilgileri gösterir. Aşağıdaki ekran görüntüsünde örnek bir AKS kümesindeki varsayılan sistem kümeleri gösterilmektedir.
- **Hizmetler ve** giriş kaynakları, kümenizin tüm hizmet ve giriş kaynaklarını gösterir.
- **Depolama** , Azure depolama sınıflarınızı ve kalıcı birim bilgilerinizi gösterir.
- **Yapılandırma** , kümenizin yapılandırma haritalarınızı ve sırları gösterir.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Kubernetes Pod bilgileri Azure portal görüntülendi." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Uygulama dağıtma

Bu örnekte, [aks hızlı başlangıç][portal-quickstart]noktasından Azure oy uygulamasını dağıtmak için örnek aks kümemizi kullanacağız.

1. Kaynak görünümlerinden herhangi birinden (ad alanı, Iş yükleri, hizmetler ve giriş, depolama veya yapılandırma) **Ekle** ' yi seçin.
1. [Aks hızlı başlangıç][portal-quickstart]konumundan Azure oy uygulaması için YAML 'yi yapıştırın.
1. Uygulamayı dağıtmak için YAML düzenleyicisinin altında **Ekle** ' yi seçin. 

YAML dosyası eklendikten sonra, Kaynak Görüntüleyicisi oluşturulan Kubernetes hizmetlerini gösterir: Azure oy uygulamasına erişmek için iç hizmet (Azure-oylamalı) ve dış hizmet (Azure-oyubi). Dış hizmet bağlı bir dış IP adresi içerdiğinden, uygulamayı tarayıcınızda kolayca görüntüleyebilmenizi sağlayabilirsiniz.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure oy uygulama bilgileri Azure portal görüntülendi." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Dağıtım öngörülerini izleme

[Kapsayıcılar Için Azure izleyici][enable-monitor] özellikli aks kümeleri, dağıtımı ve diğer öngörüleri hızlıca görüntüleyebilir. Kullanıcılar, Kubernetes kaynakları görünümünden, CPU ve bellek kullanımı da dahil olmak üzere bireysel dağıtımların canlı durumunu görebilir ve belirli düğümler ve kapsayıcılar hakkında daha ayrıntılı bilgi için Azure izleyici 'ye geçiş yapabilir. Örnek bir AKS kümesinden dağıtım öngörülerine bir örnek aşağıda verilmiştir:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Dağıtım öngörüleri Azure portal görüntülendi." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>YAML 'yi Düzenle

Kubernetes kaynak görünümü bir YAML Düzenleyicisi de içerir. Yerleşik bir YAML Düzenleyicisi, Portal içinden Hizmetleri ve dağıtımları güncelleştirebilir veya oluşturabileceğiniz ve değişiklikleri hemen uygulayabileceðiniz anlamına gelir.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Azure portal gösterildiği bir Kubernetes hizmeti için YAML Düzenleyicisi.":::

YAML 'yi düzenledikten sonra değişiklikler, **gözden geçir + kaydet**' i seçerek, değişiklikleri onayladıktan sonra yeniden kaydedilerek uygulanır.

>[!WARNING]
> UI veya CLı aracılığıyla doğrudan üretim değişikliklerinin gerçekleştirilmesi önerilmez, [sürekli tümleştirme (CI) ve sürekli dağıtım (CD) en iyi uygulamalarından](kubernetes-action.md)yararlanın. Azure portalı Kubernetes yönetim özellikleri ve YAML Düzenleyicisi, geliştirme ve test ayarında Yeni dağıtımlar için öğrenmede ve bu dağıtım için oluşturulmuştur.

## <a name="troubleshooting"></a>Sorun giderme

Bu bölümde, yaygın sorunlar ve sorun giderme adımları ele alınmaktadır.

### <a name="unauthorized-access"></a>Yetkisiz erişim

Kubernetes kaynaklarına erişmek için AKS kümesine, Kubernetes API 'sine ve Kubernetes nesnelerine erişiminizin olması gerekir. AKS kümesine erişmek için uygun izinlere sahip bir Küme Yöneticisi veya Kullanıcı olduğunuzdan emin olun. Küme güvenliği hakkında daha fazla bilgi için bkz. [AKS Için erişim ve kimlik seçenekleri][concepts-identity].

>[!NOTE]
> Azure portalındaki Kubernetes kaynak görünümü yalnızca [YÖNETILEN AAD etkin kümeler](managed-aad.md) veya AAD olmayan etkin kümeler tarafından desteklenir. Yönetilen-AAD etkin bir küme kullanıyorsanız, AAD Kullanıcı veya kimliğinizin, [kullanıcıyı `kubeconfig` ](control-kubeconfig-access.md)çekme iznine ek olarak Kubernetes API 'sine erişmek için ilgili roller/rol bağlamaları olması gerekir.

### <a name="enable-resource-view"></a>Kaynak görünümünü etkinleştir

Mevcut kümeler için Kubernetes kaynak görünümünü etkinleştirmeniz gerekebilir. Kaynak görünümünü etkinleştirmek için, kümenizin portalındaki komut istemlerini izleyin.

> [!TIP]
> API sunucusu için [**YETKILENDIRILMIŞ IP aralıklarının**](api-server-authorized-ip-ranges.md) aks ÖZELLIĞI, API sunucusu erişimini yalnızca güvenlik duvarının genel uç noktasına sınırlamak için eklenebilir. Bu tür kümeler için başka bir seçenek de `--api-server-authorized-ip-ranges` yerel bir istemci bilgisayara veya IP adresi aralığına (portala gözatılırken) erişim sağlamak üzere güncelleştiriyoruz. Bu erişime izin vermek için bilgisayarın genel IPv4 adresine sahip olmanız gerekir. Bu adresi aşağıdaki komutla veya bir internet tarayıcısında "IP adresim nedir?" arayarak bulabilirsiniz.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümeniz için Kubernetes kaynaklarına nasıl erişebileceğiniz gösterilmektedir. Küme kaynaklarını ve Kubernetes kaynak görüntüleyicisinden erişilen YAML dosyalarını daha ayrıntılı olarak anlamak için [dağıtımlar ve YAML bildirimleri][deployments] bölümüne bakın.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md