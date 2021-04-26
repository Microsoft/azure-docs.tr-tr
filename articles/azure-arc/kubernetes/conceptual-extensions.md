---
title: Küme uzantıları-Azure Arc etkin Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Bu makalede, Azure Arc etkin Kubernetes 'in küme uzantıları özelliğine kavramsal bir genel bakış sunulmaktadır.
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451210"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Azure yay etkin Kubernetes üzerinde küme uzantıları

[HELI grafikleri](https://helm.sh/) , en karmaşık Kubernetes uygulamalarını bile tanımlamak, yüklemek ve yükseltmek için gereken yapı taşlarını sağlayarak Kubernetes uygulamalarını yönetmenize yardımcı olur. Küme uzantısı özelliği, Held 'nin paketleme bileşenlerinin üzerine inşa etmek için arar. Bunun için Azure Izleyici ve Kubernetes gibi küme uzantılarının yükleme ve yaşam döngüsü yönetimi için Azure Resource Manager temelli bir deneyim sağlar. Küme uzantıları özelliği, Held grafikleriyle yerel olarak zaten kullanılabilir olan ve üzeri sürümlerde aşağıdaki ek avantajları sağlar:

- Tüm kümelerin ve bu kümelere yüklenen uzantıların envanterini alın.
- Küme uzantılarının ölçekli dağıtımını otomatikleştirmek için Azure Ilkesini kullanın.
- Her uzantının yayını için abone olun.
- Uzantılar için otomatik yükseltmeyi ayarlayın.
- Güncelleştirme ve silme için uzantı örneği oluşturma ve yaşam döngüsü yönetimi olayları için desteklenebilirlik.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Mimari

[![Küme uzantıları mimarisi ](./media/conceptual-extensions.png)](./media/conceptual-extensions.png#lightbox)

Küme uzantısı örneği, `Microsoft.KubernetesConfiguration/extensions` Azure Arc etkin Kubernetes kaynağı (tarafından temsil edilen) üzerinde Azure Resource Manager bir uzantı Azure Resource Manager kaynak () olarak oluşturulur `Microsoft.Kubernetes/connectedClusters` . Azure Resource Manager gösterimi, belirli bir küme uzantısı olan veya olmayan tüm Azure Arc etkin Kubernetes kaynaklarını denetleyen bir ilke yazmanıza izin verir. Hangi kümelerin istenen özellik değerlerini içeren küme uzantılarına sahip olmadığını belirledikten sonra, bu uyumlu olmayan kaynakları Azure Ilkesi kullanarak düzeltebilirsiniz.

`config-agent`Kümenizde çalışan, Azure Arc etkin Kubernetes kaynağında yeni veya güncelleştirilmiş uzantı kaynaklarını izler. `extensions-manager`Kümenizde çalışan Azure Container Registry veya Microsoft Container Registry Held grafiğini çeker ve kümeye yüklenir. 

Hem hem `config-agent` de `extensions-manager` küme üzerinde çalışan bileşenleri, sürüm güncelleştirmelerini ve uzantı örneği silme işlemini işler.

> [!NOTE]
> * `config-agent` Yeni veya güncelleştirilmiş uzantı kaynaklarını yay etkinleştirilmiş Kubernetes kaynağında kullanılabilir olacak şekilde izler. Bu nedenle, aracıların kümeye çekilmesi için istenen durumun bağlantısı olması gerekir. Aracılar Azure 'a bağlanamazsa, istenen durumun kümeye yayılması gecikilir.
> * Bir uzantının korumalı yapılandırma ayarları, Azure Arc etkinleştirilmiş Kubernetes hizmetlerinde 48 saate kadar saklanır. Sonuç olarak, uzantı kaynağı Azure 'da oluşturulduktan sonra kümenin 48 saat boyunca bağlantısı kesildiyse, uzantı bir `Pending` durumdan `Failed` duruma geçer. Kümeleri, mümkün olduğunca düzenli olarak çevrimiçi duruma getiriyoruz.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıçmızı kullanın.
* Azure Arc etkin Kubernetes kümesinde [küme uzantıları dağıtın](./extensions.md) .