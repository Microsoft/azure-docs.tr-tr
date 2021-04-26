---
title: Sanal ağ kullanma senaryoları
description: Azure sanal ağına kapsayıcı grupları dağıtmaya yönelik senaryolar, kaynaklar ve sınırlamalar.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 6de99c68c3f05e4734dd46a579d28a6f1a3b824e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763784"
---
# <a name="virtual-network-scenarios-and-resources"></a>Sanal ağ senaryoları ve kaynakları

[Azure sanal ağ](../virtual-network/virtual-networks-overview.md) , Azure ve şirket içi kaynaklarınız için güvenli, özel ağ sağlar. Kapsayıcı gruplarını bir Azure sanal ağına dağıtarak, kapsayıcılarınız sanal ağdaki diğer kaynaklarla güvenli bir şekilde iletişim kurabilir. 

Bu makale sanal ağ senaryoları, sınırlamaları ve kaynakları hakkında arka plan sağlar. Azure CLı kullanan dağıtım örnekleri için bkz. [Azure sanal ağ 'da kapsayıcı örnekleri dağıtma](container-instances-vnet.md).

> [!IMPORTANT]
> Bir sanal ağa kapsayıcı grubu dağıtımı, Azure Container Instances kullanılabildiği birçok bölgede genellikle Linux kapsayıcıları için kullanılabilir. Ayrıntılar için bkz. [bölgeler ve kaynak kullanılabilirliği](container-instances-region-availability.md). 

## <a name="scenarios"></a>Senaryolar

Bir Azure sanal ağına dağıtılan kapsayıcı grupları, şunun gibi senaryolar sağlar:

* Aynı alt ağdaki kapsayıcı grupları arasında doğrudan iletişim
* Kapsayıcı örneklerinden [görev tabanlı](container-instances-restart-policy.md) iş yükü çıkışını sanal ağdaki bir veritabanına gönderin
* Sanal ağdaki bir [hizmet uç](../virtual-network/virtual-network-service-endpoints-overview.md) noktasından kapsayıcı örnekleri için içerik alma
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-introduction.md) aracılığıyla şirket içi kaynaklarla kapsayıcı iletişimini etkinleştirme
* Kapsayıcıdan kaynaklanan giden trafiği belirlemek için [Azure Güvenlik Duvarı](../firewall/overview.md) ile tümleştirme 
* Sanal makineler gibi sanal ağdaki Azure kaynaklarıyla iletişim için dahili Azure DNS adları çözün
* Alt ağlara veya diğer ağ kaynaklarına kapsayıcı erişimini denetlemek için NSG kurallarını kullanma

## <a name="unsupported-networking-scenarios"></a>Desteklenmeyen ağ senaryoları 

* **Azure Load Balancer** -ağa bağlı bir kapsayıcı grubundaki kapsayıcı örneklerinin önüne bir Azure Load Balancer yerleştirme desteklenmez
* **Küresel sanal ağ eşlemesi** -genel eşleme (Azure bölgelerindeki sanal ağları bağlama) desteklenmez
* **Genel IP veya DNS etiketi** -bir sanal ağa dağıtılan kapsayıcı grupları, şu anda KAPSAYıCıLARı genel IP adresi veya tam etki alanı adı ile doğrudan internet 'te kullanıma sunma desteği vermez
* **Sanal ağ NAT** -sanal bir ağa dağıtılan kapsayıcı grupları Şu anda giden internet bağlantısı IÇIN bir NAT ağ geçidi kaynağı kullanmayı desteklememektedir.

## <a name="other-limitations"></a>Diğer sınırlamalar

* Şu anda, bir sanal ağa dağıtılan bir kapsayıcı grubunda yalnızca Linux kapsayıcıları desteklenir.
* Kapsayıcı gruplarını bir alt ağa dağıtmak için alt ağ diğer kaynak türlerini içeremez. Mevcut bir alt ağdan kapsayıcı grupları dağıtmadan önce mevcut olan tüm kaynakları kaldırın veya yeni bir alt ağ oluşturun.
* Bir sanal ağa dağıtılan bir kapsayıcı grubunda [yönetilen bir kimlik](container-instances-managed-identity.md) kullanamazsınız.
* Bir sanal ağa dağıtılan bir kapsayıcı grubunda, bir veya daha fazla [araştırma](container-instances-liveness-probe.md) veya [hazırlık araştırması](container-instances-readiness-probe.md) etkinleştiremezsiniz.
* Dahil edilen ek ağ kaynakları nedeniyle, bir sanal ağ dağıtımları genellikle standart bir kapsayıcı örneği dağıtmaktan daha yavaştır.
* Kapsayıcı grubunuzu bir Azure depolama hesabına bağlıyorsanız, bu kaynağa bir [hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) eklemeniz gerekir.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Gerekli ağ kaynakları

Kapsayıcı gruplarını bir sanal ağa dağıtmak için gereken üç Azure sanal ağ kaynağı vardır: [sanal ağın](#virtual-network) kendisi, sanal ağ içindeki [atanmış bir alt ağ](#subnet-delegated) ve bir [ağ profili](#network-profile). 

### <a name="virtual-network"></a>Sanal ağ

Bir sanal ağ, bir veya daha fazla alt ağ oluşturduğunuz adres alanını tanımlar. Daha sonra Azure kaynaklarını (kapsayıcı grupları gibi) sanal ağınızdaki alt ağlara dağıtırsınız.

### <a name="subnet-delegated"></a>Alt ağ (temsilci)

Alt ağlar sanal ağı, yerleştirdiğiniz Azure kaynakları tarafından kullanılabilen ayrı adres alanlarına bölüler. Bir sanal ağ içinde bir veya birden çok alt ağ oluşturursunuz.

Kapsayıcı grupları için kullandığınız alt ağ yalnızca kapsayıcı grupları içerebilir. Bir alt ağa ilk kez bir kapsayıcı grubu dağıttığınızda, Azure bu alt ağı Azure Container Instances için devreder. Temsilci seçildikten sonra alt ağ yalnızca kapsayıcı grupları için kullanılabilir. Bir temsilci alt ağına kapsayıcı grupları dışında kaynak dağıtmaya çalışırsanız, işlem başarısız olur.

### <a name="network-profile"></a>Ağ profili

Bir ağ profili, Azure kaynakları için bir ağ yapılandırması şablonudur. Kaynak için belirli ağ özelliklerini belirtir, örneğin, dağıtılması gereken alt ağ. Bir alt ağa (ve dolayısıyla bir sanal ağa) bir kapsayıcı grubu dağıtmak için [az Container Create][az-container-create] komutunu ilk kez kullandığınızda, Azure sizin için bir ağ profili oluşturur. Daha sonra bu ağ profilini alt ağa gelecek dağıtımlar için kullanabilirsiniz. 

Bir alt ağa kapsayıcı grubu dağıtmak için bir Kaynak Yöneticisi şablonu, YAML dosyası ya da bir programlama yöntemi kullanmak için, bir ağ profilinin tam Kaynak Yöneticisi kaynak KIMLIĞI sağlamanız gerekir. Daha önce [az Container Create][az-container-create]kullanılarak oluşturulmuş bir profil kullanabilir veya Kaynak Yöneticisi şablonu kullanarak bir profil oluşturabilirsiniz (bkz. [şablon örneği](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) ve [başvurusu](/azure/templates/microsoft.network/networkprofiles)). Daha önce oluşturulmuş bir profilin KIMLIĞINI almak için [az Network Profile List][az-network-profile-list] komutunu kullanın. 

Aşağıdaki diyagramda, Azure Container Instances için temsilci atanmış bir alt ağa birkaç kapsayıcı grubu dağıtıldı. Bir alt ağa bir kapsayıcı grubu dağıttıktan sonra, aynı ağ profilini belirterek buna ek kapsayıcı grupları dağıtabilirsiniz.

![Bir sanal ağ içindeki kapsayıcı grupları][aci-vnet-01]

## <a name="next-steps"></a>Sonraki adımlar

* Azure CLı ile dağıtım örnekleri için bkz. [Azure sanal ağ 'da kapsayıcı örnekleri dağıtma](container-instances-vnet.md).
* Bir Kaynak Yöneticisi şablonu kullanarak yeni bir sanal ağ, alt ağ, ağ profili ve kapsayıcı grubu dağıtmak için bkz. [VNET Ile Azure Kapsayıcı grubu oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).
* Bir kapsayıcı örneği oluşturmak için [Azure Portal](container-instances-quickstart-portal.md) kullanırken, **ağ** sekmesinden yeni veya bir sanal ağ için ayarlar da sağlayabilirsiniz.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
