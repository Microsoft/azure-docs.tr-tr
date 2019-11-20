---
title: Azure Güvenlik Merkezi 'nde kapsayıcı güvenliği | Microsoft Docs
description: Azure Güvenlik Merkezi 'nin kapsayıcı güvenlik özellikleri hakkında bilgi edinin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fb8f5f2adf497a12b4ed54b381a2dbf85e160b8e
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123961"
---
# <a name="container-security-in-security-center"></a>Güvenlik Merkezi 'nde kapsayıcı güvenliği

Azure Güvenlik Merkezi, kapsayıcı güvenliği için Azure Native çözümüdür. Güvenlik Merkezi Ayrıca, bulut iş yüklerinizin, VM 'lerinin, sunucularınızın ve kapsayıcılarınızın güvenliği için en iyi cam deneyim bölmesidir.

Bu makalede, kapsayıcılarınızın ve uygulamalarının güvenliğini nasıl iyileştirebileceğinizi, izleyeceğinizi ve koruyabileceğinizi açıklanmaktadır. Güvenlik Merkezi 'nin kapsayıcı güvenliğinin bu temel yönlerini nasıl yardımcı olduğunu öğreneceksiniz:

* Güvenlik açığı yönetimi
* Kapsayıcının ortamını sağlamlaştırma
* Çalışma zamanı koruması

[Azure Güvenlik Merkezi 'nin kapsayıcı Güvenlik sekmesini ![](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Güvenlik açığı yönetimi-kapsayıcı görüntülerini tarama (Önizleme)
Azure Container Registry izlemek için, güvenlik merkezi 'ne bağlayın. Yeni bir görüntü gönderildiğinde, Güvenlik Merkezi, sektör lideri güvenlik açığı tarama satıcısı, Qualys 'den bir tarayıcı kullanarak görüntüyü tarar.

Sorunlar bulunduğunda: Qualys veya Güvenlik Merkezi tarafından – Güvenlik Merkezi panosunda bildirim alırsınız. Güvenlik Merkezi her güvenlik açığı için, eyleme dönüştürülebilir öneriler, bir önem sınıflandırması ve sorunun nasıl düzeltileceği ile ilgili rehberlik sağlar. Güvenlik Merkezi 'nin önerilerin ayrıntıları için, [sanal makine korumasında](security-center-virtual-machine-protection.md##compute-and-app-recs)önerilerin listesine bakın.

## <a name="environment-hardening"></a>Ortam sağlamlaştırma

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker yapılandırmanızın sürekli izlenmesi
Azure Güvenlik Merkezi, IaaS Linux VM 'lerinde barındırılan yönetilmeyen kapsayıcıları veya Docker Kapsayıcıları çalıştıran diğer Linux makinelerini tanımlar. Güvenlik Merkezi, bu kapsayıcıların yapılandırmalarının sürekli değerlendirir. Daha sonra bunları [Internet güvenliği (CIS) Docker kıyaslaması Için merkezi](https://www.cisecurity.org/benchmark/docker/)ile karşılaştırır. 

Güvenlik Merkezi, CIS Docker kıyaslaması 'nın kural kümesinin tamamını içerir ve kapsayıcılarınız denetimlerden herhangi birini karşılamadığı takdirde sizi uyarır. Güvenlik Merkezi, yanlış yapılandırma bulduğunda güvenlik önerileri oluşturur. Önerileri görüntülemek ve sorunları düzeltmek için **öneriler sayfasını** kullanın. Ayrıca, Docker ile dağıtılan tüm sanal makineleri görüntüleyen **kapsayıcılar** sekmesinde öneriler görürsünüz. Bir sanal makinede güvenlik sorunlarını araştırırken, güvenlik merkezi makinedeki kapsayıcılar hakkında ek bilgiler sağlar. Bu tür bilgiler, Docker sürümünü ve konakta çalışan görüntü sayısını içerir. Önerilerin ayrıntıları için [buraya](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection)bakın. 

>[!NOTE]
> Bu CIS kıyaslama denetimleri, AKS tarafından yönetilen örneklerde veya Databricks tarafından yönetilen VM 'lerde çalışmaz.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Kubernetes kümelerinizi sürekli izleme (Önizleme)
Güvenlik Merkezi Azure Kubernetes hizmeti (AKS) ile birlikte çalışarak Kapsayıcılı uygulamalar geliştirmek, dağıtmak ve yönetmek için Microsoft 'un yönetilen kapsayıcı düzenleme hizmetidir.

AKS, kümelerinizin güvenlik duruşuna güvenlik denetimleri ve görünürlük sağlar. Güvenlik Merkezi şu özellikleri kullanır:
* AKS kümelerinizin yapılandırmasını sürekli izleyin
* Endüstri standartlarına göre hizalanmış güvenlik önerileri oluşturun

Güvenlik Merkezi 'nin önerilerine ilişkin ayrıntılar için bkz. [sanal makine koruması](security-center-virtual-machine-protection.md).

## <a name="run-time-protection---real-time-threat-detection"></a>Çalışma zamanı koruması-gerçek zamanlı tehdit algılama 

Güvenlik Merkezi, Kapsayıcılı ortamlarınız için gerçek zamanlı tehdit algılama sağlar ve şüpheli etkinlikler için uyarı oluşturur. Bu bilgileri kullanarak güvenlik sorunlarını hızlı bir şekilde çözebilir ve kapsayıcılarınızın güvenlik düzeyini artırabilirsiniz.

Konuk ve AKS küme düzeyindeki tehditleri tespit ediyoruz. Tam Ayrıntılar için bkz. [Azure Container Service](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>Kapsayıcılarınızla ilgili kaynaklarınızın güvenlik duruşunu görüntülemek için
1.  Güvenlik Merkezi 'nin **işlem & uygulamalar** sayfasını açın.
2.  **Kapsayıcılar** sekmesine tıklayın. AKS kümeleriniz, ACR kayıt defterleri ve Docker çalıştıran VM 'Ler görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik Merkezi 'nde kapsayıcı güvenliği hakkında daha fazla bilgi edinmek için bkz.:
* [Azure Kubernetes hizmeti ile tümleştirmenin](azure-kubernetes-service-integration.md) ayrıntıları

* [Azure Container Registry ile tümleştirmenin](azure-container-registry-integration.md) ayrıntıları