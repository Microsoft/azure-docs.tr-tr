---
title: Azure Arc etkin Kubernetes planlaması ve dağıtımı
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Yapılandırma yönetimi için Azure Arc etkin Kubernetes 'e çok sayıda küme ekleme
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315619"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes 'i planlayın ve dağıtın

Bir BT Altyapısı hizmetinin veya iş uygulamasının dağıtımı, tüm şirketler için zorluk sergilemektir. Herhangi bir hoş geldiniz veya plansız maliyetlerin oluşmasını engellemek için, mümkün olduğunca uygun olduğundan emin olmak üzere kapsamlı bir plan yapmanız gerekir. Bu tür bir plan, görevleri tamamlamaya yönelik karşılanması gereken tasarım ve dağıtım ölçütlerini tanımlamalıdır.

Dağıtımın sorunsuz bir şekilde devam edebilmesi için planınızı net bir şekilde anlayabilmelidir:

* Roller ve sorumluluklar.
* Tüm Kubernetes kümelerinin envanteri
* Ağ gereksinimlerini karşılayın.
* Başarılı dağıtımı ve devam eden yönetimi etkinleştirmek için gereken yetenek kümesi ve eğitim.
* Kabul ölçütleri ve başarısını nasıl izlediğiniz.
* Dağıtımları otomatik hale getirmek için kullanılacak araçlar veya Yöntemler.
* Gecikme ve kesintilerden kaçınmak için riskler ve risk azaltma planları.
* Dağıtım sırasında kesintiye uğramaktan kaçının.
* Önemli bir sorun oluştuğunda ilerletme yolu nedir?

Bu makalenin amacı, ortamınızdaki birden çok üretim kümesinde Azure Arc etkin Kubernetes dağıtımı için hazırlandığınızdan emin olunması sağlamaktır.

## <a name="prerequisites"></a>Önkoşullar

* Mevcut bir Kubernetes kümesi. Bir tane yoksa, aşağıdaki seçeneklerden birini kullanarak bir küme oluşturabilirsiniz:
    - [Docker 'da Kubernetes (tür)](https://kind.sigs.k8s.io/)
    - [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) veya [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) için Docker kullanarak bir Kubernetes kümesi oluşturma
    - [Küme API 'sini](https://cluster-api.sigs.k8s.io/user/quick-start.html) kullanarak kendi kendine yönetilen Kubernetes kümesi

* Makineleriniz, şirket içi ağınızdan veya diğer bulut ortamlarınızdan doğrudan veya bir ara sunucu aracılığıyla Azure 'daki kaynaklara bağlantı sağlar. Daha fazla ayrıntı için [ağ önkoşulları](quickstart-connect-cluster.md#meet-network-requirements)altında bulunabilir.

* `kubeconfig`Azure yaya bağlamak istediğiniz kümeye işaret eden bir dosya.
* Kullanıcı veya hizmet sorumlusu için ' Read ' ve ' Write ' izinleri, Azure Arc etkinleştirilmiş Kubernetes kaynak türünü oluşturuyor `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>Pilot

Tüm üretim kümelerine dağıtılmadan önce, bu dağıtım işlemini ortamınıza göre benimsemeden önce değerlendirerek başlayın. Pilot için, şirketlerin iş yürütme yeteneği açısından önemli olmayan kümelerin temsili bir örneklemesini belirler. Pilot uygulamayı çalıştırmaya yetecek kadar zaman izin verdiğinizden emin olmak ve etkisini değerlendirmek için, yaklaşık 30 gün kullanmanızı öneririz.

Pilot 'ın kapsamını ve ayrıntılarını açıklayan resmi bir plan oluşturun. Aşağıdaki örnek plan başlamanıza yardımcı olmalıdır.

* **Hedefler** -bir pilot uygulamanın gerekli olduğu kararı veren iş ve teknik sürücüleri açıklar.
* **Seçim ölçütleri** -çözümün bir pilot aracılığıyla ne şekilde gösterilolacağını seçmek için kullanılan ölçütü belirtir.
* **Kapsam** -çözüm bileşenlerini, beklenen zamanlamayı, pilot işlem süresini ve hedeflenecek küme sayısını içerir.
* **Başarı ölçütleri ve ölçümleri** -pilot 'ın başarı ölçütlerini ve başarı düzeyini belirlemek için kullanılan belirli ölçüleri tanımlayın.
* **Eğitim planı** -pilot sırasında Azure ve BT Hizmetleri ile ilgili eğitim sistemi mühendisleri, Yöneticiler vb. için plan açıklanmıştır.
* **Geçiş planı** -pilot 'dan üretime geçişe kılavuzluk etmek için kullanılan stratejiyi ve kriterleri açıklar.
* **Geri alma** -dağıtım öncesi durumuna bir pilot geri dönme yordamlarını açıklar.
* **Riskler** -pilot yürütmek ve üretim dağıtımıyla ilişkilendirmek için tüm belirtilen riskleri listeleyin.

## <a name="phase-1-build-a-foundation"></a>1. Aşama: temel derleme

Bu aşamada, sistem mühendisleri veya Yöneticiler kaynak grupları, Etiketler, rol atamaları oluşturma gibi temel etkinlikleri, Azure Arc etkin Kubernetes kaynaklarının daha sonra oluşturulup çalışabilmesi için gerçekleştirir.

|Görev |Ayrıntı |Süre |
|-----|-------|---------|
| [Kaynak grubu oluşturma](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Yalnızca Azure Arc etkin Kubernetes kaynaklarını dahil etmek ve bu kaynakları merkezileştirmek ve izlemek için ayrılmış bir kaynak grubu. | Bir saat |
| Makineleri düzenlemeye yardımcı olmak için [Etiketler](../../azure-resource-manager/management/tag-resources.md) uygulayın. | BT hizalanmış [etiketleme stratejisini](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)değerlendirin ve geliştirin. Bu, Azure Arc etkin Kubernetes kaynaklarınızı yönetme karmaşıklığını azaltmaya ve yönetim kararları almayı basitleştirmeye yardımcı olabilir. | Bir günde |
| Giüstler için [konfigürasyonları](tutorial-use-gitops-connected-cluster.md) tanımla | `PodSecurityPolicy`Kümelerinize dağıtmak istediğiniz uygulama veya gibi taban çizgisi yapılandırmalarının belirlenmesi `NetworkPolicy` | Bir günde |
| [Azure ilke](../../governance/policy/overview.md) Idare planı geliştirme | Abonelikte veya kaynak grubu kapsamında Azure Ilkesi ile Azure Arc etkinleştirilmiş Kubernetes kümelerini nasıl uygulayacağınızı öğrenin. | Bir günde |
| [Rol tabanlı erişim denetimini](../../role-based-access-control/overview.md) (RBAC) yapılandırma | Kümelerinizde kimin okuma/yazma/tüm izinleri olduğunu belirlemek için bir erişim planı geliştirin | Bir günde |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>2. Aşama: Azure yay etkin Kubernetes dağıtma

Bu aşamada, Kubernetes kümelerinizi Azure 'a bağlayacağız:

|Görev |Ayrıntı |Süre |
|-----|-------|---------|
| [İlk Kubernetes kümenizi Azure yaya bağlama](quickstart-connect-cluster.md) | İlk kümenizi Azure yaya bağlama kapsamında, ekleme ortamınızı Azure CLI, Held ve Azure CLı uzantısı gibi tüm gerekli araçlarla birlikte ayarlayın `connectedk8s` . | 15 dakika |
| [Hizmet sorumlusu oluşturma](create-onboarding-service-principal.md) | Azure CLı veya PowerShell kullanarak Kubernetes kümelerini etkileşimli olmayan şekilde bağlamaya yönelik bir hizmet sorumlusu oluşturun. | Bir saat |


## <a name="phase-3-manage-and-operate"></a>3. Aşama: yönetme ve çalıştırma

Bu aşamada, Kubernetes kümelerinize uygulamalar ve temel yapılandırma dağıyoruz.

|Görev |Ayrıntı |Süre |
|-----|-------|---------|
|Kümelerinizde [yapılandırma oluşturma](tutorial-use-gitops-connected-cluster.md) | Uygulamalarınızı Azure Arc etkin Kubernetes kaynağında dağıtmaya yönelik yapılandırma oluşturma. | 15 dakika |
|Yapılandırmaların ölçeklendirilmesi için [Azure Ilkesini kullanma](use-azure-policy.md) | Bir abonelik veya kaynak grubu kapsamındaki tüm kümelerinizde temel yapılandırmaların dağıtımını otomatik hale getirmek için ilke atamaları oluşturun. | 15 dakika |
| [Azure Arc aracılarını yükseltme](agent-upgrade.md) | Kümelerinizde aracıların otomatik yükseltmesini devre dışı bırakırsanız, en son güvenlik ve hata düzeltmelerine sahip olduğunuzdan emin olmak için aracılarınızı en son sürüme el ile güncelleştirin. | 15 dakika |

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıçmızı kullanın.
* Azure Arc etkin Kubernetes kümesinde [yapılandırma oluşturma](./tutorial-use-gitops-connected-cluster.md) .
* [Yapılandırmalara göre yapılandırma uygulamak Için Azure Ilkesini kullanın](./use-azure-policy.md).