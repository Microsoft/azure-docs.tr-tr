---
title: Özel konumlar-Azure Arc etkin Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Bu makalede, Azure Arc etkin Kubernetes 'in özel konumlar özelliğine kavramsal bir genel bakış sunulmaktadır
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451199"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes 'in üzerine özel konumlar

Azure location yapısının bir uzantısı olarak *özel konumlar* , kiracı yöneticilerinin Azure 'un etkin Kubernetes kümelerini Azure hizmetleri örneklerinin dağıtımına yönelik hedef konumlar olarak kullanması için bir yol sağlar. Azure Kaynak örnekleri, Azure Arc etkin SQL yönetilen örneği ve Azure Arc etkin PostgreSQL hiper ölçeğini içerir.

Azure konumlarına benzer şekilde, kiracının özel konumlarına erişimi olan son kullanıcılar, şirketinin özel işlem kullanımını kullanarak kaynakları dağıtabilir.

[![Yay platformu katmanları ](./media/conceptual-arc-platform-layers.png)](./media/conceptual-arc-platform-layers.png#lightbox)

Özel konumları, Azure Arc etkin Kubernetes kümesi, küme bağlantısı ve küme uzantılarının en üstünde bir Özet katman olarak görselleştirebilirsiniz. Özel konumlar, diğer Azure hizmetlerinin kümeye erişmesi için gereken ayrıntılı [rolebindings ve clusterrolebindings ' i](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) oluşturur. Bu diğer Azure Hizmetleri, müşterinin kümelerine dağıtmak istediği kaynakları yönetmek için küme erişimi gerektirir.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Mimari

Yönetici, kümede özel konumlar özelliğini etkinleştirirken, küme üzerinde özel konumlar kaynak sağlayıcısı (RP) tarafından kullanılan Azure AD uygulamasına yetki veren bir ClusterRoleBinding oluşturulur. Yetkilendirildikten sonra özel konumlar RP, bu kümede özel kaynaklar oluşturmak için diğer Azure RPs 'Ler tarafından gereken ClusterRoleBindings veya RoleBindings oluşturabilir. Kümede yüklü olan küme uzantıları, yetkilendirmek için RPs listesini belirler.

[![Özel konumlar ](./media/conceptual-custom-locations-usage.png) kullanın](./media/conceptual-custom-locations-usage.png#lightbox)

Kullanıcı kümede bir veri hizmeti örneği oluşturduğunda: 
1. PUT isteği Azure Resource Manager gönderilir.
1. PUT isteği, Azure Arc etkin veri Hizmetleri RP 'ye iletilir. 
1. RP, `kubeconfig` özel konumun bulunduğu Azure Arc etkinleştirilmiş Kubernetes kümesiyle ilişkili dosyayı getirir. 
   * Özel konuma `extendedLocation` özgün PUT isteğinde olduğu gibi başvurulur. 
1. Azure Arc etkin veri Hizmetleri RP, `kubeconfig` özel konuma eşlenmiş ad alanındaki Azure Arc etkin veri Hizmetleri türünün özel bir kaynağını oluşturmak üzere kümeyle iletişim kurmak için öğesini kullanır. 
   * Azure Arc etkin veri Hizmetleri işleci, özel konum var olmadan önce küme uzantısı oluşturma yoluyla dağıtıldı. 
1. Azure Arc etkin veri Hizmetleri işleci, kümede oluşturulan yeni özel kaynağı okur ve küme üzerinde istenen durumu gerçekleştirme içine çevirerek veri denetleyicisi oluşturur. 

SQL yönetilen örneği ve PostgreSQL örneğini oluşturma adımlarının sırası, yukarıda açıklanan adımların dizisiyle aynıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıçmızı kullanın.
* Azure Arc etkin Kubernetes kümesinde [özel bir konum oluşturun](./custom-locations.md) .