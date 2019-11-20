---
title: Azure Dev Spaces hakkında sık sorulan sorular
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Azure Dev Spaces hakkında bazı yaygın soruların yanıtlarını bulun
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s '
ms.openlocfilehash: 1f25ccd26aed832c068c04198486e769ec980380
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072209"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces hakkında sık sorulan sorular

Bu, Azure Dev Spaces hakkında sıkça sorulan soruları ele alınmaktadır.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Şu anda hangi Azure bölgeleri Azure Dev Spaces sağlıyor?

Kullanılabilir bölgelerin tüm listesi için bkz. [Desteklenen bölgeler ve Konfigürasyonlar][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Genel IP adresi olmadan Azure Dev Spaces kullanabilir miyim?

Hayır, genel IP olmadan AKS kümesinde Azure Dev Spaces sağlayamazsınız. [Yönlendirme için Azure dev Spaces][dev-spaces-routing]genel bir IP gerekir.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces ile kendi giriş sayfamı kullanabilir miyim?

Evet, bir Azure Dev Spaces oluşturduğu bir yandan kendi giriş hesabınızı yapılandırabilirsiniz. Örneğin, [traefik][ingress-traefik]kullanabilirsiniz.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces ile HTTPS kullanabilir miyim?

Evet, [traefik][ingress-https-traefik]kullanarak kendi GIRIŞ hesabınızı https ile yapılandırabilirsiniz.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Azure Dev Spaces, kubenet yerine CNı kullanan bir kümede kullanabilir miyim? 

Evet, ağ için CNı kullanan bir AKS kümesinde Azure Dev Spaces kullanabilirsiniz. Örneğin, bir AKS kümesinde Azure Dev Spaces, ağ için CNı kullanan [mevcut Windows kapsayıcılarıyla][windows-containers]birlikte kullanabilirsiniz.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Windows kapsayıcılarıyla Azure Dev Spaces kullanabilir miyim?

Şu anda Azure Dev Spaces yalnızca Linux düğüm ve düğümleri üzerinde çalışmak üzere tasarlanmıştır, ancak [mevcut Windows kapsayıcılarıyla][windows-containers]bir aks kümesinde Azure dev Spaces çalıştırabilirsiniz.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API sunucusu yetkilendirilmiş IP adresi aralıkları etkin olan AKS kümelerinde Azure Dev Spaces kullanabilir miyim?

Evet, [API sunucusu YETKILENDIRILMIŞ IP adresi aralıkları][aks-auth-range] etkin olan aks kümelerinde Azure dev Spaces kullanabilirsiniz. Kümenizi [oluştururken][aks-auth-range-create] , [bölgeniz temelinde ek aralıklara izin vermeniz][aks-auth-range-ranges]gerekir. Ayrıca, bu ek aralıklara izin vermek için var olan kümeyi [güncelleştirebilir][aks-auth-range-update] .

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md