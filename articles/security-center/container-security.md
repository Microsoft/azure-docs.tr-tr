---
title: Azure Güvenlik Merkezi ve Azure Defender ile kapsayıcı güvenliği
description: Azure Güvenlik Merkezi 'nin kapsayıcı güvenlik özellikleri hakkında bilgi edinin
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 9fddb27ee6a1139fa8b07c6c19dd4fdf1a20096e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029153"
---
# <a name="container-security-in-security-center"></a>Güvenlik Merkezi’nde kapsayıcı güvenliği

Azure Güvenlik Merkezi, kapsayıcılarınızın güvenliğini sağlamaya yönelik Azure Native çözümüdür.

Güvenlik Merkezi aşağıdaki kapsayıcı kaynak türlerini koruyabilir:

| Kaynak türü | Güvenlik Merkezi tarafından sunulan korumalar |
|:--------------------:|-----------|
| ![Kubernetes hizmeti](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Kubernetes kümeleri** | Belirlenen tehditleri azaltmanıza yardımcı olacak yanlış yapılandırma ve kılavuz kuralları hakkında görünürlük sağlamak için kümelerinizin sürekli değerlendirmesi. [Güvenlik önerileri aracılığıyla ortam sağlamlaştırma](#environment-hardening)hakkında daha fazla bilgi edinin.<br><br>Kümeler ve Linux düğümleri için tehdit koruması. Şüpheli etkinlik uyarıları, [Kubernetes Için Azure Defender](defender-for-kubernetes-introduction.md)tarafından sağlanır. Bu Azure Defender planı, Kubernetes kümelerinizi Azure Kubernetes hizmeti (AKS), şirket içi veya diğer bulut sağlayıcılarında barındırılıp barındırılmadığını savunmaktadır. leriniz. <br>[Kubernetes düğümleri ve kümeleri için çalışma zamanı koruması](#run-time-protection-for-kubernetes-nodes-and-clusters)hakkında daha fazla bilgi edinin.|
| ![Kapsayıcı Konağı](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Kapsayıcı Konakları**<br>(Docker çalıştıran VM 'Ler) | [Sunucular için isteğe bağlı Azure Defender](defender-for-servers-introduction.md)tarafından tanımlanan tehditleri azaltmanıza yardımcı olmak üzere, yanlış yapılandırma ve talimatlara görünürlük sağlamak Için Docker ortamlarınızın sürekli değerlendirmesi.<br>[Güvenlik önerileri aracılığıyla ortam sağlamlaştırma](#environment-hardening)hakkında daha fazla bilgi edinin.|
| ![Kapsayıcı kayıt defteri](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry (ACR) kayıt defterleri** | [Kapsayıcı kayıt defterleri için isteğe bağlı Azure Defender](defender-for-container-registries-introduction.md)ile Azure Resource Manager tabanlı ACR kayıt defterlerinden görüntüler için güvenlik açığı değerlendirmesi ve yönetim araçları.<br>[Güvenlik açıkları için kapsayıcı görüntülerinizi tarama](#vulnerability-management---scanning-container-images)hakkında daha fazla bilgi edinin. |
|||

Bu makalede, kapsayıcılarınızın ve uygulamalarının güvenliğini artırmak, izlemek ve korumak için kapsayıcı kayıt defterleri, SEI 'ler ve Kubernetes için isteğe bağlı Azure Defender planlarıyla birlikte Güvenlik Merkezi 'ni nasıl kullanabileceğiniz açıklanır.

Güvenlik Merkezi 'nin kapsayıcı güvenliğinin bu temel yönlerini nasıl yardımcı olduğunu öğreneceksiniz:

- [Güvenlik açığı yönetimi-kapsayıcı görüntülerini tarama](#vulnerability-management---scanning-container-images)
- [Ortam sağlamlaştırma](#environment-hardening)
- [Kubernetes düğümleri ve kümeleri için çalışma zamanı koruması](#run-time-protection-for-kubernetes-nodes-and-clusters)

Aşağıdaki ekran görüntüsünde, varlık Envanteri sayfası ve Güvenlik Merkezi tarafından korunan çeşitli kapsayıcı kaynak türleri gösterilmektedir.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Güvenlik Merkezi 'nin varlık envanteri sayfasında kapsayıcı ile ilgili kaynaklar" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Güvenlik açığı yönetimi-kapsayıcı görüntülerini tarama

Azure Resource Manager tabanlı Azure Container kayıt defterlerinden görüntüleri izlemek için, [kapsayıcı kayıt defterleri Için Azure Defender](defender-for-container-registries-introduction.md)'ı etkinleştirin. Güvenlik Merkezi, son 30 gün içinde çekilen, Kayıt defterinize gönderilen veya içeri aktardığınız tüm görüntüleri tarar. Tümleşik tarayıcı, sektörde önde gelen güvenlik açığı tarama satıcısı, Qualys tarafından sağlanır.

Sorunlar bulunduğunda, Qualys veya Güvenlik Merkezi tarafından – [Azure Defender panosunda](azure-defender-dashboard.md)bildirim alırsınız. Güvenlik Merkezi her güvenlik açığı için, eyleme dönüştürülebilir öneriler, bir önem sınıflandırması ve sorunun nasıl düzeltileceği ile ilgili rehberlik sağlar. Güvenlik Merkezi 'nin kapsayıcılar için önerilerin ayrıntıları için bkz. [önerilerin başvuru listesi](recommendations-reference.md#recs-compute).

Güvenlik Merkezi, tarayıcıdan bulguları filtreler ve sınıflandırır. Bir görüntü sağlıklı olduğunda, güvenlik merkezi bunu bu şekilde işaretler. Güvenlik Merkezi yalnızca çözümleme sorunları olan görüntüler için güvenlik önerileri oluşturur. Güvenlik Merkezi, yalnızca sorun olduğunda bilgilendirerek istenmeyen bilgi uyarıları için potansiyelini azaltır.

## <a name="environment-hardening"></a>Ortam sağlamlaştırma

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker yapılandırmanızın sürekli izlenmesi

Azure Güvenlik Merkezi, IaaS Linux VM 'lerinde barındırılan yönetilmeyen kapsayıcıları veya Docker Kapsayıcıları çalıştıran diğer Linux makinelerini tanımlar. Güvenlik Merkezi, bu kapsayıcıların yapılandırmalarının sürekli değerlendirir. Daha sonra bunları [Internet güvenliği (CIS) Docker kıyaslaması Için merkezi](https://www.cisecurity.org/benchmark/docker/)ile karşılaştırır.

Güvenlik Merkezi, CIS Docker kıyaslaması 'nın kural kümesinin tamamını içerir ve kapsayıcılarınız denetimlerden herhangi birini karşılamadığı takdirde sizi uyarır. Güvenlik Merkezi, yanlış yapılandırma bulduğunda güvenlik önerileri oluşturur. Önerileri görüntülemek ve sorunları düzeltmek için Güvenlik Merkezi 'nin **öneriler sayfasını** kullanın. CIS kıyaslama denetimleri, AKS tarafından yönetilen örneklerde veya Databricks tarafından yönetilen VM 'lerde çalıştırılmayın.

Bu özellik için görünebilen ilgili güvenlik merkezi önerilerinin ayrıntıları için, öneriler başvuru tablosunun [işlem bölümüne](recommendations-reference.md#recs-compute) bakın.

Bir VM 'nin güvenlik sorunlarını araştırırken, güvenlik merkezi makinedeki kapsayıcılar hakkında ek bilgiler sağlar. Bu tür bilgiler, Docker sürümünü ve konakta çalışan görüntü sayısını içerir. 

IaaS Linux VM 'lerinde barındırılan yönetilmeyen kapsayıcıları izlemek için, [sunucular için isteğe bağlı Azure Defender 'ı](defender-for-servers-introduction.md)etkinleştirin.


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes kümelerinizi sürekli izleme
Güvenlik Merkezi Azure Kubernetes hizmeti (AKS) ile birlikte çalışarak Kapsayıcılı uygulamalar geliştirmek, dağıtmak ve yönetmek için Microsoft 'un yönetilen kapsayıcı düzenleme hizmetidir.

AKS, kümelerinizin güvenlik duruşuna güvenlik denetimleri ve görünürlük sağlar. Güvenlik Merkezi, AKS kümelerinizin yapılandırmasını sürekli izlemek ve sektör standartlarıyla uyumlu güvenlik önerileri oluşturmak için bu özellikleri kullanır.

Bu, Azure Güvenlik Merkezi, Azure Kubernetes hizmeti ve Azure Ilkesi arasındaki etkileşimin üst düzey bir diyagramıdır:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure Güvenlik Merkezi, Azure Kubernetes hizmeti ve Azure Ilkesi arasındaki etkileşimin üst düzey mimarisi" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Güvenlik Merkezi tarafından alınan ve çözümlenen öğelerin şunları görmenizi sağlayabilirsiniz:

- API sunucusundan denetim günlükleri
- Log Analytics aracısından ham güvenlik olayları

    > [!NOTE]
    > Şu anda sanal makine ölçek kümelerinde çalışan Azure Kubernetes hizmet kümelerine Log Analytics aracısının yüklenmesini desteklemiyoruz.

- AKS kümesindeki küme yapılandırma bilgileri
- Azure Ilkesinden iş yükü yapılandırması ( **Kubernetes Için Azure ilke eklentisi** aracılığıyla)

Bu özellik için görünebilen ilgili güvenlik merkezi önerilerinin ayrıntıları için, öneriler başvuru tablosunun [işlem bölümüne](recommendations-reference.md#recs-compute) bakın.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>İş yükü koruma en iyi-Kubernetes giriş denetimi kullanan uygulamalar

Kubernetes kapsayıcılarınızın iş yüklerini korumaya yönelik bir dizi öneri için,  **Kubernetes Için Azure İlkesi eklentisi**' ni yükler. Ayrıca, bu eklentiyi otomatik olarak [Log Analytics Aracısı ve uzantılarının otomatik sağlamayı etkinleştir](security-center-enable-data-collection.md#auto-provision-mma)bölümünde açıklandığı gibi dağıtabilirsiniz. Eklenti için otomatik sağlama "açık" olarak ayarlandığında, uzantı tüm mevcut ve gelecekteki kümelerde (eklenti yükleme gereksinimlerini karşılayan) varsayılan olarak etkinleştirilir.

[Bu Azure Ilkesinde Kubernetes sayfasında](../governance/policy/concepts/policy-for-kubernetes.md)açıklandığı gibi, eklenti açık kaynak [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)   giriş denetleyicisi Web kancasını [Açık ilke aracısına](https://www.openpolicyagent.org/)genişletir. Kubernetes giriş denetleyicileri, kümelerinizin nasıl kullanıldığını zorlayan eklentilerdir. Eklenti, Kubernetes giriş denetimine bir Web kancası olarak kaydedilir ve kümelerinizde yerleşik olarak öngörülebilir ve korumalar üzerinde merkezi ve tutarlı bir şekilde uygulanmasını sağlar. 

AKS kümenizdeki eklenti sayesinde, Kubernetes API sunucusuna gönderilen her istek, kümede kalıcı hale gelmeden önce önceden tanımlanmış en iyi uygulamalar kümesine göre izlenir. Daha sonra, en iyi uygulamaları zorlamak ve gelecekteki iş yükleri için bunları **zorunlu kılmak** üzere yapılandırabilirsiniz. 

Örneğin, ayrıcalıklı kapsayıcıların oluşturulmaması ve ileride yapılacak istekleri engellenemeyeceksiniz.

[Kubernetes iş yüklerinizi koruma](kubernetes-workload-protections.md)hakkında daha fazla bilgi edinin.


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>Kubernetes düğümleri ve kümeleri için çalışma zamanı koruması

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Sonraki adımlar

Bu genel bakışta, Azure Güvenlik Merkezi 'nde kapsayıcı güvenliğinin temel öğeleri hakkında bilgi edindiniz. İlgili malzemeler için bkz:

- [Kubernetes için Azure Defender 'a giriş](defender-for-kubernetes-introduction.md)
- [Kapsayıcı kayıt defterleri için Azure Defender’a giriş](defender-for-container-registries-introduction.md)