---
title: Sanal ağ yalıtımı ve güvenliğine genel bakış
titleSuffix: Azure Machine Learning
description: Çalışma alanı kaynaklarını ve işlem ortamlarını güvenli hale getirmek için Azure Machine Learning ile yalıtılmış bir Azure sanal ağı kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 1c3d9b286a8262efa126ba9c661c50dd88e78b64
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573481"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Sanal ağ yalıtımı ve gizliliği genel bakış

Bu makalede, Azure Machine Learning ' de ağ iletişimini güvenli hale getirmek için sanal ağların (VNet 'ler) nasıl kullanılacağını öğrenirsiniz. Bu makalede, bir sanal ağın tamamını nasıl yapılandıracağınızı göstermek için örnek bir senaryo kullanılmaktadır.

Bu makale, bir Azure Machine Learning iş akışını güvenli hale getirmek için size kılavuzluk eden beş bölümlü bir serinin bir parçasıdır. Öncelikle kavramları anlamak için bu genel bakış makalesini okumanız önemle tavsiye ederiz. 

Bu serideki diğer makaleler şunlardır:

**1. VNET 'e genel bakış**  >  [2. Çalışma alanı](how-to-secure-workspace-vnet.md)3 ' ü güvenli hale getirin  >  [. Eğitim ortamının](how-to-secure-training-vnet.md)4 ' ü koruyun  >  [. Invenli ortam](how-to-secure-inferencing-vnet.md)5 ' i güvenli hale getirin  >  [. Studio işlevselliğini etkinleştir](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, aşağıdaki konularda bilginiz olduğunu varsaymaktadır:
+ [Azure sanal ağları](../virtual-network/virtual-networks-overview.md)
+ [IP ağı](../virtual-network/public-ip-addresses.md)
+ [Azure Özel Bağlantı](how-to-configure-private-link.md)
+ [Ağ güvenlik grupları (NSG)](../virtual-network/network-security-groups-overview.md)
+ [Ağ güvenlik duvarları](../firewall/overview.md)
## <a name="example-scenario"></a>Örnek senaryo

Bu bölümde, ortak bir ağ senaryosunun özel IP adresleriyle iletişim Azure Machine Learning güvenli hale getirmek için nasıl ayarlandığını öğrenirsiniz.

Aşağıdaki tabloda, hizmetlerin hem VNet hem de VNet olmadan Azure Machine Learning ağının farklı bölümlerine erişme şekli karşılaştırılır.

| Senaryo | Çalışma alanı | İlişkili kaynaklar | Eğitim işlem ortamı | In, işlem ortamını sınırlama |
|-|-|-|-|-|-|
|**Sanal ağ yok**| Genel IP | Genel IP | Genel IP | Genel IP |
|**Bir sanal ağdaki kaynakları güvenli hale getirme**| Özel IP (Özel uç nokta) | Genel IP (hizmet uç noktası) <br> **veya** <br> Özel IP (Özel uç nokta) | Özel IP | Özel IP  | 

* **Çalışma alanı** -çalışma alanındaki özel bağlantıya bağlanmak için sanal ağınızdan özel bir uç nokta oluşturun. Özel uç nokta, birkaç özel IP adresi aracılığıyla çalışma alanını VNET 'e bağlar.
* **İlişkili kaynak** -Azure depolama, Azure Key Vault ve Azure Container Services gibi çalışma alanı kaynaklarına bağlanmak için hizmet uç noktalarını veya özel uç noktaları kullanın.
    * **Hizmet uç noktaları** , sanal ağınızın kimliğini Azure hizmetine sağlar. Sanal ağınızda hizmet uç noktalarını etkinleştirdikten sonra, Azure hizmet kaynaklarını sanal ağınıza güvenli hale getirmek için bir sanal ağ kuralı ekleyebilirsiniz. Hizmet uç noktaları genel IP adreslerini kullanır.
    * **Özel uç noktalar** , sizi Azure özel bağlantısı tarafından desteklenen bir hizmete güvenli bir şekilde bağlayan ağ arabirimlerdir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor.
* **Eğitim işlem erişimi** -erişim eğitimi, Azure Machine Learning işlem örneği ve Azure Machine Learning işlem kümeleri gıbı özel IP adresleriyle güvenli şekilde işlem hedefleri. 
* **İşlem erişimini sınırlama** -özel IP adresleriyle Azure Kubernetes Hizmetleri (aks) işlem kümelerine erişin.


Sonraki beş bölümde yukarıda açıklanan ağ senaryosunun güvenliğini sağlama gösterilmektedir. Ağınızı güvenli hale getirmek için şunları yapmanız gerekir:

1. [**Çalışma alanının ve ilişkili kaynakların**](#secure-the-workspace-and-associated-resources)güvenliğini sağlayın.
1. [**Eğitim ortamının**](#secure-the-training-environment)güvenliğini sağlama.
1. [**İkinci dereceden sınırlama ortamının**](#secure-the-inferencing-environment)güvenliğini sağlama.
1. İsteğe bağlı: [**Studio işlevselliğini etkinleştirin**](#optional-enable-studio-functionality).
1. [**Güvenlik Duvarı ayarlarını**](#configure-firewall-settings)yapılandırın.
1. [DNS adı çözümlemesini](#custom-dns)yapılandırın.
## <a name="secure-the-workspace-and-associated-resources"></a>Çalışma alanının ve ilişkili kaynakların güvenliğini sağlama

Çalışma alanınızı ve ilişkili kaynaklarınızı güvenli hale getirmek için aşağıdaki adımları kullanın. Bu adımlar, hizmetlerinizin sanal ağda iletişim kurmasına izin verir.

1. VNet ve çalışma alanınız arasında iletişimi etkinleştirmek için [özel bir bağlantı etkin çalışma alanı](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) oluşturun.
1. Bir __hizmet uç noktası__ ya _da_ __Özel uç nokta__ kullanarak aşağıdaki hizmetleri sanal ağa ekleyin. Ayrıca, güvenilen Microsoft hizmetlerinin bu hizmetlere erişmesine de izin vermeniz gerekir:
    
    | Hizmet | Uç nokta bilgileri | Güvenilen bilgilere izin ver |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [Hizmet uç noktası](../key-vault/general/overview-vnet-service-endpoints.md)</br>[Özel uç nokta](../key-vault/general/private-link-service.md) | [Güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına izin ver](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Azure depolama hesabı__ | [Hizmet uç noktası](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)</br>[Özel uç nokta](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints) | [Güvenilen Azure hizmetlerine erişim izni verme](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [Hizmet uç noktası](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)</br>[Özel uç nokta](../container-registry/container-registry-private-link.md) | [Güvenilen hizmetlere izin ver](../container-registry/allow-access-trusted-services.md) |


![Çalışma alanının ve ilişkili kaynakların, hizmet uç noktaları veya VNet 'in içindeki özel uç noktalar üzerinden nasıl iletişim kurduğunu gösteren mimari diyagramı](./media/how-to-network-security-overview/secure-workspace-resources.png)

Bu adımların nasıl tamamlanacağı hakkında ayrıntılı yönergeler için, bkz. [güvenli bir Azure Machine Learning çalışma alanı](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Sınırlamalar

Çalışma alanınızın ve bir sanal ağ içindeki ilişkili kaynakların güvenliğini sağlamak aşağıdaki sınırlamalara sahiptir:
- Azure Kamu veya Azure Çin 21Vianet bölgelerinde özel bağlantıyla birlikte Azure Machine Learning bir çalışma alanı kullanılması kullanılamaz.
- Tüm kaynaklar aynı VNet 'in arkasında olmalıdır. Ancak, aynı sanal ağ içindeki alt ağlara izin verilir.

## <a name="secure-the-training-environment"></a>Eğitim ortamının güvenliğini sağlama

Bu bölümde, Azure Machine Learning 'de eğitim ortamının güvenliğini nasıl sağlayacağınızı öğreneceksiniz. Ayrıca, ağ yapılandırmalarının birlikte nasıl çalıştığını anlamak için Azure Machine Learning bir eğitim işini nasıl tamamladığını öğrenirsiniz.

Eğitim ortamının güvenliğini sağlamak için aşağıdaki adımları kullanın:

1. Eğitim işini çalıştırmak için [Sanal ağda bir Azure Machine Learning işlem örneği ve bilgisayar kümesi](how-to-secure-training-vnet.md#compute-instance) oluşturun.
1. Batch hizmetinin işleri işlem kaynaklarınıza gönderebilmesi için [Azure Batch hizmetinden gelen Iletişime Izin verin](how-to-secure-training-vnet.md#mlcports) . 

![Yönetilen işlem kümelerinin ve örneklerinin nasıl güvence altına alınacağını gösteren mimari diyagramı](./media/how-to-network-security-overview/secure-training-environment.png)

Bu adımların nasıl tamamlanacağı hakkında ayrıntılı yönergeler için bkz. [bir eğitim ortamını güvenli hale getirme](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Örnek eğitim işi gönderimi 

Bu bölümde, eğitim işi göndermek için Azure Machine Learning hizmetleri arasında güvenli bir şekilde nasıl iletişim kurduğu hakkında bilgi edineceksiniz. Bu, iletişimin güvenliğini sağlamak için tüm yapılandırmalarınızın birlikte nasıl çalıştığını gösterir.

1. İstemci, eğitim betikleri ve eğitim verilerini bir hizmet veya özel uç nokta ile korunan depolama hesaplarına yükler.

1. İstemci, Azure Machine Learning çalışma alanına özel uç nokta aracılığıyla bir eğitim işi gönderir.

1. Azure Batch Hizmetleri işi çalışma alanından alır ve bilgi işlem kaynağıyla birlikte sağlanan ortak yük dengeleyici aracılığıyla eğitim işini işlem ortamına gönderir. 

1. İşlem kaynağı işi alır ve eğitime başlar. İşlem kaynakları, eğitim dosyalarını indirmek ve çıktıyı karşıya yüklemek için güvenli depolama hesaplarına erişir.

### <a name="limitations"></a>Sınırlamalar

- Azure Işlem örneği ve Azure Işlem kümeleri, çalışma alanı ve ilişkili kaynakları ile aynı VNet, bölge ve abonelikte olmalıdır. 

## <a name="secure-the-inferencing-environment"></a>İkinci dereceden sınırlama ortamının güvenliğini sağlama

Bu bölümde, bir ınıya sınırlama ortamının güvenliğini sağlamak için kullanılabilen seçenekleri öğrenirsiniz. Yüksek ölçekli, üretim dağıtımları için Azure Kubernetes Hizmetleri (AKS) kümelerini kullanmanızı öneririz.

Bir sanal ağdaki AKS kümeleri için iki seçeneğiniz vardır:

- Sanal ağınıza varsayılan bir AKS kümesi dağıtın veya ekleyin.
- Sanal ağınıza özel bir AKS kümesi iliştirin.

**Varsayılan AKS kümelerinin** genel IP adreslerine sahip bir denetim düzlemi vardır. Dağıtım sırasında sanal ağınıza varsayılan bir AKS kümesi ekleyebilir veya oluşturulduktan sonra bir küme iliştirebilirsiniz.

**Özel AKS kümelerinde** yalnızca özel IP 'ler üzerinden erişilebilen bir denetim düzlemi vardır. Küme oluşturulduktan sonra özel AKS kümeleri eklenmelidir.

Varsayılan ve özel kümelerin nasıl ekleneceği hakkında ayrıntılı yönergeler için bkz. [ınvenli bir ortamı güvenli hale getirme](how-to-secure-inferencing-vnet.md). 

Aşağıdaki ağ diyagramı, sanal ağa bağlı bir özel AKS kümesi ile güvenli bir Azure Machine Learning çalışma alanı gösterir.

![Sanal ağa özel AKS kümesi eklemeyi gösteren mimari diyagramı. AKS denetim düzlemi, müşteri sanal ağı dışına yerleştirildi](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Sınırlamalar
- AKS kümeleri çalışma alanıyla aynı VNet 'e ve ilişkili kaynaklarına ait olmalıdır. 

## <a name="optional-enable-public-access"></a>İsteğe bağlı: genel erişimi etkinleştir

Özel bir uç nokta kullanarak bir sanal ağın arkasındaki çalışma alanını güvenli hale getirebilirsiniz ve yine de genel İnternet üzerinden erişime izin verebilirsiniz. İlk yapılandırma [, çalışma alanının ve ilişkili kaynakların güvenliğini sağlamaya](#secure-the-workspace-and-associated-resources)benzer. 

Çalışma alanının bir özel bağlantıyla güvenliğini doğruladıktan sonra, [genel erişimi etkinleştirin](how-to-configure-private-link.md#enable-public-access). Bundan sonra, çalışma alanına hem genel İnternet 'ten hem de VNet 'ten erişebilirsiniz.

### <a name="limitations"></a>Sınırlamalar

- Azure Machine Learning Studio 'yu genel İnternet üzerinden kullanıyorsanız, tasarımcı gibi bazı özellikler verilerinize erişemez. Bu sorun, veriler VNet 'in arkasında güvenliği sağlanmış bir hizmette depolandığında oluşur. Örneğin, bir Azure depolama hesabı.
## <a name="optional-enable-studio-functionality"></a>İsteğe bağlı: Studio işlevselliğini etkinleştir

[Çalışma alanının](#secure-the-workspace-and-associated-resources)  >  güvenliğini sağlama [Eğitim ortamının](#secure-the-training-environment)  >  güvenliğini sağlama [İkinci dereceden sınırlama ortamının](#secure-the-inferencing-environment)  >  güvenliğini sağlama **Studio Işlevselliğini etkinleştir**  >  [Güvenlik Duvarı ayarlarını yapılandırma](#configure-firewall-settings)

Depolama alanı bir sanal ağda ise, önce [Studio](overview-what-is-machine-learning-studio.md)'da tam işlevselliği etkinleştirmek üzere ek yapılandırma adımları gerçekleştirmeniz gerekir. Varsayılan olarak, aşağıdaki özellik devre dışıdır:

* Studio 'daki verileri önizleyin.
* Tasarımcıda verileri görselleştirin.
* Tasarımcıda bir model dağıtın.
* Bir oto ml denemesi gönder.
* Etiketleme projesi başlatın.

VNet 'in içindeyken tam Studio işlevselliğini etkinleştirmek için bkz. [sanal bir ağda Azure Machine Learning Studio 'Yu kullanma](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio). Studio, hizmet uç noktalarını veya özel uç noktaları kullanarak depolama hesaplarını destekler.

### <a name="limitations"></a>Sınırlamalar

[Ml yardımlı veri etiketleme](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) , bir sanal ağın arkasında güvenliği sağlanmış varsayılan depolama hesaplarını desteklemez. ML yardımlı veri etiketleme için varsayılan olmayan bir depolama hesabı kullanmanız gerekir. Varsayılan olmayan depolama hesabının sanal ağın arkasında güvenliği sağlanmadığını göz önünde bulabilirsiniz. 

## <a name="configure-firewall-settings"></a>Güvenlik duvarı ayarlarını yapılandırma

Güvenlik duvarınızı, Azure Machine Learning çalışma alanı kaynaklarınıza ve genel İnternet 'e erişimi denetlemek üzere yapılandırın. Azure Güvenlik Duvarı 'nı önerdiğimiz sırada ağınızın güvenliğini sağlamak için diğer güvenlik duvarı ürünlerini de kullanabilmeniz gerekir. Güvenlik duvarınız üzerinden iletişime izin verme hakkında sorularınız varsa, lütfen kullandığınız güvenlik duvarının belgelerine başvurun.

Güvenlik Duvarı ayarları hakkında daha fazla bilgi için bkz. [bir güvenlik duvarının arkasında çalışma alanı kullanma](how-to-access-azureml-behind-firewall.md).

## <a name="custom-dns"></a>Özel DNS

Sanal ağınız için özel bir DNS çözümü kullanmanız gerekiyorsa, çalışma alanınız için konak kayıtları eklemeniz gerekir.

Gerekli etki alanı adları ve IP adresleri hakkında daha fazla bilgi için bkz. [Özel BIR DNS sunucusu ile çalışma alanı kullanma](how-to-custom-dns.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, beş bölümlü bir sanal ağ serisinin bir parçasıdır. Bir sanal ağın güvenliğini nasıl sağlayacağınızı öğrenmek için makalelerin geri kalanına bakın:

* [2. Bölüm: sanal ağa genel bakış](how-to-secure-workspace-vnet.md)
* [3. kısım: eğitim ortamının güvenliğini sağlama](how-to-secure-training-vnet.md)
* [4. Bölüm: ikinci dereceden sınırlama ortamının güvenliğini sağlama](how-to-secure-inferencing-vnet.md)
* [5. Bölüm: Studio işlevselliğini etkinleştirme](how-to-enable-studio-virtual-network.md)

Ayrıca, ad çözümlemesi için [özel DNS](how-to-custom-dns.md) kullanma makalesine bakın.