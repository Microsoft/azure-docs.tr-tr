---
title: Azure 'da SAP HANA sanal makinelerden bağlantı kurulumu (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA (büyük örnekler) kullanarak sanal makinelerden bağlantı kurulumu.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 919e253b6d9ddf8d65f86897a299416e93f3e660
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099912"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure VM'lerini HANA Büyük Örnekleri'ne bağlama

[Azure 'da SAP HANA nedir (büyük örnekler)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Azure 'da SAP uygulama katmanıyla en az HANA büyük örnek dağıtımının aşağıdaki gibi göründüğünü bahsetmeler:

![Azure 'da SAP HANA bağlı Azure VNet (büyük örnekler) ve şirket içi](./media/hana-overview-architecture/image1-architecture.png)

Azure sanal ağ tarafında daha yakından bakıldığında şunlar için bir gereksinim vardır:

- SAP uygulama katmanının VM 'lerini dağıtacaksınız bir Azure sanal ağının tanımı.
- Azure sanal ağında, VM 'Lerin dağıtıldığı bir varsayılan alt ağın tanımı.
- Oluşturulan Azure sanal ağının en az bir VM alt ağına ve bir Azure ExpressRoute sanal ağ geçidi alt ağına sahip olması gerekir. Bu alt ağlara, belirtilen ve aşağıdaki bölümlerde açıklandığı gibi IP adresi aralıkları atanmalıdır.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>HANA büyük örnekleri için Azure sanal ağını oluşturma

>[!Note]
>HANA büyük örnekleri için Azure sanal ağı, Azure Resource Manager dağıtım modeli kullanılarak oluşturulmalıdır. Klasik dağıtım modeli olarak bilinen eski Azure dağıtım modeli, HANA büyük örnek çözümü tarafından desteklenmez.

Sanal ağı oluşturmak için Azure portal, PowerShell, Azure şablonu veya Azure CLı 'yı kullanabilirsiniz. (Daha fazla bilgi için bkz. [Azure Portal kullanarak sanal ağ oluşturma](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). Aşağıdaki örnekte, Azure portal kullanılarak oluşturulan bir sanal ağa bakacağız.

Bu belgede **Adres alanına** başvururken, Azure sanal ağının kullanmasına izin verilen adres alanı. Bu adres alanı Ayrıca sanal ağın BGP yol yayma için kullandığı adres aralığıdır. Bu **Adres alanına** buradan görünebilirler:

![Azure portal gösterildiği bir Azure sanal ağının adres alanı](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Önceki örnekte, 10.16.0.0/16 ile Azure sanal ağına kullanılacak büyük ve geniş bir IP adresi aralığı verildi. Bu nedenle, bu sanal ağ içindeki sonraki alt ağların tüm IP adresi aralıklarının bu adres alanı içinde aralıkları olabilir. Azure 'da tek bir sanal ağ için genellikle bu tür büyük bir adres aralığı önermiyoruz. Ancak Azure sanal ağında tanımlı olan alt ağlara bakalım:

![Azure sanal ağ alt ağları ve IP adresi aralıkları](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

İlk VM alt ağına ("varsayılan" olarak adlandırılır) ve "GatewaySubnet" adlı bir alt ağa sahip bir sanal ağa bakıyoruz.

Önceki iki grafik içinde, **sanal ağ adres alanı** , hem Azure VM 'nin hem de sanal ağ geçidinin **alt ağ IP adresi aralığını** kapsıyor.

**Sanal ağ adres alanını** her alt ağ tarafından kullanılan belirli aralıklar ile kısıtlayabilirsiniz. Bir sanal ağın **sanal ağ adres alanını** , burada gösterildiği gibi, birden çok belirli Aralık olarak da tanımlayabilirsiniz:

![İki boşlukla Azure sanal ağ adres alanı](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Bu durumda, **sanal ağ adres alanının** tanımlı iki alanı vardır. Bunlar, Azure VM 'nin alt ağ IP adresi aralığı ve sanal ağ geçidi için tanımlanan IP adresi aralıklarıyla aynıdır. 

Bu kiracı alt ağları (VM alt ağları) için istediğiniz herhangi bir adlandırma standardını kullanabilirsiniz. Ancak, Azure (büyük örnekler) ExpressRoute bağlantı hattının SAP HANA bağlanan **her sanal ağ için her zaman bir ve yalnızca bir ağ geçidi alt ağı** olmalıdır. ExpressRoute ağ geçidinin düzgün yerleştirildiğinden emin olmak için **Bu ağ geçidi alt ağının "GatewaySubnet" olarak adlandırılması gerekiyor** .

> [!WARNING] 
> Ağ geçidi alt ağının her zaman "GatewaySubnet" olarak adlandırılması kritik öneme sahiptir.

Birden çok VM alt ağını ve bitişik olmayan adres aralıklarını kullanabilirsiniz. Bu adres aralıkları, sanal ağın **sanal ağ adresi alanı** tarafından kapsanmalıdır. Bunlar, toplanmış bir biçimde olabilir. Ayrıca VM alt ağlarının ve ağ geçidi alt ağının tam aralıklarının bir listesinde de olabilirler.

Aşağıda, HANA büyük örneklerine bağlanan bir Azure sanal ağı hakkındaki önemli olguların bir özeti verilmiştir:

- HANA büyük örneklerinin ilk dağıtımını gerçekleştirirken, **sanal ağ adres alanını** Microsoft 'a göndermeniz gerekir. 
- **Sanal ağ adres alanı** , hem Azure VM 'nin hem de sanal ağ geçidinin alt ağ IP adresi aralığına yönelik aralıkları içeren bir daha büyük bir Aralık olabilir.
- Ya da VM alt ağı IP adresi aralıklarının ve sanal ağ geçidi IP adresi aralığının farklı IP adresi aralıklarını kapsayan birden çok Aralık gönderebilirsiniz.
- Tanımlanan **sanal ağ adresi alanı** BGP yönlendirme yayılması için kullanılır.
- Ağ geçidi alt ağının adı şu olmalıdır: **"Gatewaysubnet"** .
- Adres alanı, Azure 'dan HANA büyük örnek birimlerine giden trafiğe izin vermek veya bu trafiği engellemek için HANA büyük örnek tarafında bir filtre olarak kullanılır. Azure sanal ağının BGP yönlendirme bilgileri ve HANA büyük örnek tarafında filtreleme için yapılandırılan IP adresi aralıkları eşleşmelidir. Aksi takdirde, bağlantı sorunları meydana gelebilir.
- **Sanal ağı Hana büyük örnek ExpressRoute 'A bağlama** bölümünde daha sonra ele alınan ağ geçidi alt ağıyla ilgili bazı ayrıntılar bulunur.



## <a name="different-ip-address-ranges-to-be-defined"></a>Tanımlanacak farklı IP adresi aralıkları 

HANA büyük örneklerinin dağıtımı için gerekli olan bazı IP adresi aralıkları zaten tanıtılmıştır. Ancak aynı zamanda önemli olan daha fazla IP adresi aralığı vardır. Aşağıdaki IP adresi aralıklarının tümünün Microsoft 'a gönderilmesi gerekmez. Ancak, ilk dağıtım için bir istek göndermeden önce bunları tanımlamanız gerekir:

- **Sanal ağ adres alanı**: **Sanal ağ adres alanı** , Azure sanal ağlarında adres alanı PARAMETREYE atadığınız IP adres aralığıdır. Bu ağlar SAP HANA büyük örnek ortamına bağlanır. Bu adres alanı parametresinin çok satırlı bir değer olmasını öneririz. Azure VM 'nin alt ağ aralığından ve Azure ağ geçidinin alt ağ aralığından oluşmalıdır. Bu alt ağ aralığı önceki grafiklerde gösteriliyor. Şirket içi veya sunucu IP havuzunuzun veya ER-P2P adres aralıklarıyla çakışmamalıdır. Bu IP adresi aralıklarını nasıl alırsınız? Şirket ağı takımınız veya hizmet sağlayıcınız, ağınızda kullanılmayan bir veya birden çok IP adres aralığı sağlamalıdır. Örneğin, Azure sanal makinenizin alt ağı 10.0.1.0/24 ve Azure Gateway alt ağınızın alt ağı 10.0.2.0/28 olur.  Azure sanal ağ adresi alanınızı şöyle tanımlanmalıdır: 10.0.1.0/24 ve 10.0.2.0/28. Adres alanı değerleri toplanabilse de, bunları alt ağ aralıklarıyla eşleştirmeyi öneririz. Bu şekilde, ağınızdaki başka bir yerde daha büyük adres alanları içinde kullanılmayan IP adresi aralıklarını yeniden kullanmaktan kaçınabilirsiniz. **Sanal ağ adres alanı bir IP adresi aralığıdır. İlk dağıtımı**istediğinizde Microsoft 'a gönderilmesi gerekir.
- **Azure VM alt ağı IP adresi aralığı:** Bu IP adresi aralığı, Azure sanal ağ alt ağı parametresine atadığınız bir adrestir. Bu parametre, Azure sanal ağınızda bulunur ve SAP HANA büyük örnek ortamına bağlanır. Bu IP adresi aralığı, Azure VM 'lerinize IP adresleri atamak için kullanılır. Bu aralığın dışında olan IP adreslerinin SAP HANA büyük örnek sunucunuza bağlanmasına izin verilir. Gerekirse, birden çok Azure VM alt ağı kullanabilirsiniz. Her bir Azure VM alt ağı için bir/24 CıDR bloğu önerilir. Bu adres aralığı, Azure sanal ağ adresi alanında kullanılan değerlerin bir parçası olmalıdır. Bu IP adresi aralığını nasıl alırsınız? Şirket ağı takımınız veya hizmet sağlayıcınız ağınızda kullanılmayan bir IP adresi aralığı sağlamalıdır.
- **Sanal ağ geçidi alt ağı IP adresi aralığı:** Kullanmayı planladığınız özelliklere bağlı olarak önerilen boyut:
   - Ultra performanslı ExpressRoute ağ geçidi:/26 adres bloğu--SKU 'ların tür II sınıfı için gereklidir.
   - Yüksek performanslı bir ExpressRoute sanal ağ geçidi (veya daha küçük) kullanarak VPN ve ExpressRoute ile birlikte bulunma:/27 adres bloğu.
   - Tüm diğer durumlar:/28 adres bloğu. Bu adres aralığı, "VNet adres alanı" değerlerinde kullanılan değerlerin bir parçası olmalıdır. Bu adres aralığı, Microsoft 'a gönderdiğiniz Azure sanal ağ adres alanı değerlerinde kullanılan değerlerin bir parçası olmalıdır. Bu IP adresi aralığını nasıl alırsınız? Şirket ağı takımınız veya hizmet sağlayıcınız, ağınızda Şu anda kullanılmayan bir IP adresi aralığı sağlamalıdır. 
- **ER-P2P bağlantısı için adres aralığı:** Bu Aralık SAP HANA büyük örnek ExpressRoute (ER) P2P bağlantınızın IP aralığıdır. Bu IP adresi aralığı bir/29 CıDR IP adresi aralığı olmalıdır. Bu Aralık, şirket içi veya diğer Azure IP adresi aralıklarıyla çakışmamalıdır. Bu IP adresi aralığı, ExpressRoute sanal ağ Geçidinizden SAP HANA büyük örnek sunucularına ER bağlantısı kurmak için kullanılır. Bu IP adresi aralığını nasıl alırsınız? Şirket ağı takımınız veya hizmet sağlayıcınız, ağınızda Şu anda kullanılmayan bir IP adresi aralığı sağlamalıdır. **Bu Aralık bir IP adresi aralığıdır. İlk dağıtımı**istediğinizde Microsoft 'a gönderilmesi gerekir.  
- **Sunucu IP havuzu adres aralığı:** Bu IP adresi aralığı, tek tek IP adresini HANA büyük örnek sunucularına atamak için kullanılır. Önerilen alt ağ boyutu bir/24 CıDR bloğudur. Gerekirse, en az 64 IP adresi olacak şekilde daha küçük olabilir. Bu aralıktan, ilk 30 IP adresi Microsoft tarafından kullanılmak üzere ayrılmıştır. Aralığın boyutunu seçerken bu olguyu hesaba aldığınızdan emin olun. Bu Aralık, şirket içi veya diğer Azure IP adresleriyle çakışmamalıdır. Bu IP adresi aralığını nasıl alırsınız? Şirket ağı takımınız veya hizmet sağlayıcınız, ağınızda Şu anda kullanılmayan bir IP adresi aralığı sağlamalıdır.  **Bu Aralık, bir ilk dağıtım Isterken Microsoft 'a gönderilmesi gereken bır IP adresi aralığıdır**.

Son olarak Microsoft 'a gönderilmesi gereken isteğe bağlı IP adresi aralıkları:

- Şirket içinden HANA büyük örnek birimlerine doğrudan yönlendirmeyi etkinleştirmek üzere [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) kullanmayı seçerseniz, başka bir/29 IP adresi aralığı ayırmanız gerekir. Bu Aralık, daha önce tanımladığınız diğer IP adresi aralıklarıyla çakışmayabilir.
- Bir Azure bölgesindeki bir HANA büyük örnek kiracısından başka bir Azure bölgesindeki başka bir HANA büyük örnek kiracıya doğrudan yönlendirmeyi etkinleştirmek üzere [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) kullanmayı seçerseniz, başka bir/29 IP adresi aralığı ayırmanız gerekir. Bu Aralık, daha önce tanımladığınız diğer IP adresi aralıklarıyla çakışmayabilir.

ExpressRoute Global Reach ve HANA büyük örnekleri etrafında kullanım hakkında daha fazla bilgi için belgelere bakın:

- [SAP HANA (büyük örnekler) ağ mimarisi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Sanal ağı HANA büyük örneklerine bağlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
Daha önce açıklanan IP adresi aralıklarını tanımlamanız ve planlamanız gerekir. Ancak, bunların tümünü Microsoft 'a iletmeniz gerekmez. Microsoft 'a ad almanız gereken IP adresi aralıkları şunlardır:

- Azure sanal ağ adres alanları
- ER-P2P bağlantısı için adres aralığı
- Sunucu IP havuzu adres aralığı

HANA büyük örneklerine bağlanması gereken ek sanal ağlar eklerseniz, Microsoft 'a eklemekte olduğunuz yeni Azure sanal ağ adresi alanını göndermeniz gerekir. 

Aşağıda, yapılandırmak ve son olarak Microsoft 'a sağlamak için farklı aralıkların ve bazı örnek aralıkların bir örneği verilmiştir. Azure sanal ağ adres alanının değeri ilk örnekte toplanmaz. Ancak, ilk Azure VM alt ağı IP adresi aralığı ve sanal ağ geçidi alt ağı IP adresi aralığı aralıklarından tanımlanır. 

Ek VM alt ağlarının ek IP adresi aralıklarını yapılandırıp Azure sanal ağ adresi alanının bir parçası olarak gönderdiğinizde Azure sanal ağı içinde birden fazla VM alt ağı kullanabilirsiniz.

![Azure 'da SAP HANA gereken IP adresi aralıkları (büyük örnekler) en az dağıtım](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Grafik, ExpressRoute Global Reach 'nin isteğe bağlı kullanımı için gerekli olan ek IP adresi aralıklarını göstermez.

Ayrıca, Microsoft 'a gönderdiğiniz verileri de toplayabilirsiniz. Bu durumda, Azure sanal ağının adres alanı yalnızca bir boşluk içerir. Önceki örnekteki IP adresi aralıklarını kullanarak, toplanmış sanal ağ adresi alanı aşağıdaki görüntüye benzeyebilir:

![Azure 'da SAP HANA gereken IP adresi aralıklarının (büyük örnekler) en az sayıda dağıtım olması gereken ikinci bir olasılık](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Örnekte, Azure sanal ağının adres alanını tanımlayan iki küçük Aralık yerine 4096 IP adresini içeren bir daha büyük aralığınız vardır. Bu tür bir büyük Aralık, adres alanının büyük bir tanımına çok büyük aralıklar kullanılmamış halde kalır. Sanal ağ adres alanı değerleri BGP yol yayma için kullanıldığından, kullanılmayan aralıkların şirket içinde veya başka bir yerde kullanımı yönlendirme sorunlarına neden olabilir. Grafik, ExpressRoute Global Reach 'nin isteğe bağlı kullanımı için gerekli olan ek IP adresi aralıklarını göstermez.

Adres alanını, kullandığınız gerçek alt ağ adres alanı ile sıkı bir şekilde tutmanızı öneririz. Gerekirse, sanal ağ üzerinde kapalı kalma süresi olmadan, her zaman yeni adres alanı değerleri ekleyebilirsiniz.
 
> [!IMPORTANT] 
> ER-P2P, sunucu IP havuzu ve Azure sanal ağ adres alanı içindeki her IP adresi aralığı birbirleriyle veya ağınızda kullanılan başka bir aralıkla çakışmamalıdır. Her birinin ayrı olması gerekir. Önceki iki grafik de gösterildiği gibi, diğer herhangi bir aralığın alt ağı olamaz. Aralıklar arasında örtüşmeler oluşursa, Azure sanal ağı ExpressRoute devresine bağlanmayabilir.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Adres aralıkları tanımlandıktan sonraki adımlar
IP adresi aralıkları tanımlandıktan sonra, aşağıdaki işlemlerin gerçekleşmesi gerekir:

1. Azure sanal ağ adres alanı, ER-P2P bağlantısı ve sunucu IP havuzu adres aralığı için IP adresi aralıklarını belgenin başlangıcında listelenmiş diğer verilerle birlikte gönderebilirsiniz. Bu noktada, sanal ağ ve VM alt ağlarını oluşturmaya de başlayabilirsiniz. 
2. Azure aboneliğiniz ve HANA büyük örnek damgası arasında Microsoft tarafından bir ExpressRoute devresi oluşturulur.
3. Microsoft tarafından büyük örnek damgasında bir kiracı ağı oluşturulur.
4. Microsoft, Azure sanal ağ adres alanınızda HANA büyük örneklerle iletişim kuran IP adreslerini kabul etmek için SAP HANA Azure (büyük örnekler) altyapısında ağ yapılandırır.
5. Satın aldığınız Azure (büyük örnekler) SKU 'sunda belirli SAP HANA bağlı olarak, Microsoft bir kiracı ağına bir işlem birimi atar. Ayrıca depolama alanını ayırır ve takar ve işletim sistemini (SUSE veya Red Hat Linux) kurar. Bu birimlerin IP adresleri, Microsoft 'a gönderdiğiniz sunucu IP havuzu adres aralığından alınmıştır.

Dağıtım sürecinin sonunda, Microsoft size aşağıdaki verileri sağlar:
- Azure sanal ağlarınızı Azure sanal ağlarını HANA büyük örneklerine bağlayan ExpressRoute devresine bağlamak için gereken bilgiler:
     - Yetkilendirme anahtarları
     - ExpressRoute peerID
- ExpressRoute bağlantı hattını ve Azure sanal ağını oluşturduktan sonra HANA büyük örneklerine erişim için veriler.

Ayrıca, [Azure 'da (büyük örnekler) kurulum 'da SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)Hana büyük örnekleri bağlama sırasını bulabilirsiniz. Aşağıdaki adımların birçoğu, bu belgede örnek bir dağıtımda gösterilmiştir. 

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal ağı Hana büyük örnek ExpressRoute 'A bağlama](hana-connect-vnet-express-route.md)bölümüne bakın.
