---
title: Kapsayıcı için Azure Defender kayıt defterleri-avantajlar ve Özellikler
description: Kapsayıcı kayıt defterleri için Azure Defender 'ın avantajları ve özellikleri hakkında bilgi edinin.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4840fc27133b1d92cb8aaad80921f9d21901569d
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010701"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Kapsayıcı kayıt defterleri için Azure Defender’a giriş

Azure Container Registry (ACR), Azure dağıtımları için kapsayıcı görüntülerinizi merkezi bir kayıt defterinde depolayan ve yöneten, yönetilen ve özel bir Docker kayıt defteri hizmetidir. Bu, açık kaynaklı Docker kayıt defteri 2,0 ' i temel alır.

Aboneliğinizdeki tüm Azure Resource Manager tabanlı kayıt defterlerine karşı korumak için abonelik düzeyinde **kapsayıcı kayıt defterleri Için Azure Defender** 'ı etkinleştirin. Güvenlik Merkezi daha sonra kayıt defterine gönderilen, kayıt defterine aktarılan ve son 30 gün içinde çekilen görüntüleri tarar. Bu özellik görüntü başına ücretlendirilir.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Azure Defender 'ın kapsayıcı kayıt defterleri 'nin avantajları nelerdir?

Güvenlik Merkezi aboneliğinizdeki Azure Resource Manager tabanlı ACR kayıt defterlerini tanımlar ve kayıt defterinizin görüntüleri için Azure yerel güvenlik açığı değerlendirmesi ve yönetimini sorunsuz bir şekilde sağlar.

**Kapsayıcı kayıt defterleri Için Azure Defender** , Azure Resource Manager tabanlı Azure Container Registry kayıt defterlerinin görüntülerini taramak ve görüntülerinize daha derin görünürlük sağlamak için bir güvenlik açığı tarayıcısı içerir. Tümleşik tarayıcı, sektörde önde gelen güvenlik açığı tarama satıcısı olan Qualys tarafından desteklenmektedir.

Sorunlar bulunduğunda: Qualys veya Güvenlik Merkezi tarafından – Güvenlik Merkezi panosunda bildirim alırsınız. Güvenlik Merkezi her güvenlik açığı için, eyleme dönüştürülebilir öneriler, bir önem sınıflandırması ve sorunun nasıl düzeltileceği ile ilgili rehberlik sağlar. Güvenlik Merkezi 'nin kapsayıcılar için önerilerin ayrıntıları için bkz. [önerilerin başvuru listesi](recommendations-reference.md#recs-compute).

Güvenlik Merkezi, tarayıcıdan bulguları filtreler ve sınıflandırır. Bir görüntü sağlıklı olduğunda, güvenlik merkezi bunu bu şekilde işaretler. Güvenlik Merkezi yalnızca çözümleme sorunları olan görüntüler için güvenlik önerileri oluşturur. Güvenlik Merkezi, bildirilen her güvenlik açığının ayrıntılarını ve önem derecesini sağlar. Ayrıca, her görüntüde bulunan belirli güvenlik açıklarının nasıl düzeltileceğine ilişkin yönergeler sağlar.

Güvenlik Merkezi, yalnızca sorun olduğunda bilgilendirerek istenmeyen bilgi uyarıları için potansiyelini azaltır.


> [!TIP]
> Güvenlik Merkezi 'nin kapsayıcı güvenlik özellikleri hakkında daha fazla bilgi edinmek için bkz.:
>
> - [Azure Güvenlik Merkezi ve kapsayıcı güvenliği](container-security.md)
> - [Kubernetes için Azure Defender 'a giriş](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>Görüntüler ne zaman taranır?

Bir görüntü taraması için üç tetikleyici vardır:

- **Gönderim sırasında** , Kayıt defterinize her görüntü gönderildiğinde, güvenlik merkezi bu görüntüyü otomatik olarak tarar. Bir görüntünün taramasını tetiklemek için onu deponuza gönderin.

- **Yakın zamanda çekilen** her gün yeni güvenlik açıkları bulunduğundan, **kapsayıcı kayıt defterleri için Azure Defender** , haftalık olarak, son 30 gün içinde çekilen tüm resimleri de tarar. Bu yeniden taramalar için ek ücret alınmaz. Yukarıda belirtildiği gibi, her görüntü için bir kez faturalandırılırsınız.

- **Import** -Azure Container Registry, görüntüleri Docker Hub, Microsoft Container Registry veya başka bir Azure Container Registry 'den Kayıt defterinize getirmek için içeri aktarma araçlarına sahiptir. **Kapsayıcı kayıt defterleri Için Azure Defender** , içeri aktardığınız tüm desteklenen görüntüleri tarar. Kapsayıcı [görüntülerini kapsayıcı kayıt defterine Içeri aktarma](../container-registry/container-registry-import-images.md)hakkında daha fazla bilgi edinin.
 
Tarama genellikle 2 dakika içinde tamamlanır, ancak 15 dakikaya kadar sürebilir. Bulgular, bunun gibi güvenlik merkezi önerileri olarak kullanılabilir hale getirilir:

[![Azure Container Registry (ACR) barındırılan görüntüde bulunan güvenlik açıkları hakkında örnek Azure Güvenlik Merkezi önerisi](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Güvenlik Merkezi Azure Container Registry ile nasıl çalışır?

Aşağıda, güvenlik merkezi ile kayıt defterlerinden koruma bileşenlerinin ve avantajlarının üst düzey bir diyagramı verilmiştir.

![Azure Güvenlik Merkezi ve Azure Container Registry (ACR) üst düzey genel bakış](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry resim taraması hakkında SSS

### <a name="how-does-security-center-scan-an-image"></a>Güvenlik Merkezi bir görüntüyü nasıl tarar?
Güvenlik Merkezi, görüntüyü kayıt defterinden çeker ve Qualys tarayıcısı ile yalıtılmış bir korumalı alanda çalıştırır. Tarayıcı bilinen güvenlik açıklarının listesini ayıklar.

Güvenlik Merkezi, tarayıcıdan bulguları filtreler ve sınıflandırır. Bir görüntü sağlıklı olduğunda, güvenlik merkezi bunu bu şekilde işaretler. Güvenlik Merkezi yalnızca çözümleme sorunları olan görüntüler için güvenlik önerileri oluşturur. Güvenlik Merkezi, yalnızca sorun olduğunda size bildirimde bulunmak istenmeyen bilgi uyarıları için potansiyelini azaltır.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Tarama sonuçlarını REST API aracılığıyla alabilir miyim?
Evet. Sonuçlar [Alt Değerlendirmeler REST API’si](/rest/api/securitycenter/subassessments/list/) altında yer alır. Ayrıca, tüm kaynaklarınız için kusto benzeri API olan Azure Kaynak Grafiği 'ni (ARG) kullanabilirsiniz: bir sorgu belirli bir taramayı getirebilir.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Hangi kayıt defteri türleri taranır? Hangi türler faturalandırılır?
Azure Defender tarafından kapsayıcı kayıt defterleri için desteklenen kapsayıcı kayıt defterleri türlerinin listesi için bkz. [kullanılabilirlik](#availability).

Desteklenmeyen kayıt defterlerini Azure aboneliğinize bağladığınızda, Azure Defender bunları taramayacaktır ve bunlar için faturalandırmaz.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Güvenlik açığı tarayıcılarından bulguları özelleştirebilir miyim?
Evet. Bir bulmayı yok saymanız gereken bir kuruluş varsa, bunu düzeltmek yerine isteğe bağlı olarak devre dışı bırakabilirsiniz. Devre dışı bulgular, güvenli puanınızı etkilemez veya istenmeyen gürültü oluşturmaz.

[Tümleşik güvenlik açığı değerlendirmesi aracından bulguları devre dışı bırakmak için kurallar oluşturma hakkında bilgi edinin](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Güvenlik Merkezi neden kayıt defterimde olmayan bir görüntüyle ilgili güvenlik açıklarına neden uyarır?
Güvenlik Merkezi, bir kayıt defterine gönderilen veya çekilen her görüntü için güvenlik açığı değerlendirmeleri sağlar. Bazı görüntüler zaten taranmış bir görüntüden etiketleri yeniden kullanabilir. Örneğin, bir Özet 'e her görüntü eklediğinizde "en son" etiketini yeniden atayabilirsiniz. Bu gibi durumlarda, ' eski ' görüntü kayıt defterinde hala bulunur ve yine de Özet tarafından çekmeye devam edebilir. Görüntüde güvenlik bulguları varsa ve bu, çekilmışsa güvenlik açıklarını açığa çıkarır.


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenlik açıkları için görüntülerinizi tarayın](defender-for-container-registries-usage.md)