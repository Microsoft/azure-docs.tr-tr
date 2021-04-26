---
title: Azure Lab Services bir eş ağa bağlan | Microsoft Docs
description: Laboratuvar ağınızı bir eş olarak başka bir ağla bağlamayı öğrenin. Örneğin, şirket içi kuruluşunuzu/üniversite ağını Azure 'daki sanal ağ ile birlikte, laboratuvar için bağlayın.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 99ea72f11c8f389a15171a1fe3b376646494903a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434252"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Laboratuvarınızın ağını Azure Lab Services içindeki bir eş sanal ağla bağlayın

Bu makalede, Labs ağınızı başka bir ağla eşleme hakkında bilgi sağlanır.

## <a name="overview"></a>Genel Bakış

Sanal ağ eşlemesi, Azure sanal ağlarına sorunsuz bir şekilde bağlanmanıza olanak sağlar. Eşleme yapıldıktan sonra, bağlantı açısından sanal ağlar tek bir sanal ağ gibi görünür. Eşlenen sanal ağlardaki sanal makineler arasındaki trafik, Microsoft omurga altyapısı aracılığıyla yönlendirilir ve aynı sanal ağdaki sanal makineler arasında çok benzer trafik, yalnızca özel IP adresleri üzerinden yönlendirilir. Daha fazla bilgi için bkz. [sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md).

Laboratuvar ağınızı, bazı senaryolarda aşağıdakiler de dahil olmak üzere bir eş sanal ağla bağlamanız gerekebilir:

- Laboratuvardaki sanal makinelerde lisans almak için şirket içi lisans sunucularına bağlanan yazılımlar vardır.
- Laboratuvardaki sanal makinelerin, University 'in ağ paylaşımlarında veri kümelerine (veya diğer dosyalara) erişmesi gerekir.

Belirli şirket içi ağlar, [ExpressRoute](../expressroute/expressroute-introduction.md) veya [sanal ağ geçidi](../vpn-gateway/vpn-gateway-about-vpngateways.md)aracılığıyla Azure sanal ağı 'na bağlanır. Bu hizmetlerin Azure Lab Services dışında ayarlanması gerekir. ExpressRoute kullanarak şirket içi bir ağı Azure 'a bağlama hakkında daha fazla bilgi edinmek için bkz. [ExpressRoute 'a genel bakış](../expressroute/expressroute-introduction.md). Bir sanal ağ geçidini kullanarak şirket içi bağlantı için, ağ geçidinin, belirtilen sanal ağın ve laboratuar hesabının hepsi aynı bölgede olmalıdır.

> [!NOTE]
> Laboratuvar hesabıyla eşlenmeyecek bir Azure sanal ağı oluştururken, sanal ağın bölgesinin laboratuvarların nerede oluşturulduğunu nasıl etkilediğini anlamak önemlidir.  Daha fazla bilgi için, [regions\locations](./administrator-guide.md#regionslocations)içindeki Yönetici Kılavuzu bölümüne bakın.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Laboratuvar hesabı oluşturma sırasında yapılandırma

Yeni [Laboratuvar hesabı oluşturma](tutorial-setup-lab-account.md)sırasında, **Gelişmiş** sekmesinde **eş sanal ağ** açılan listesinde gösterilen mevcut bir sanal ağı seçebilirsiniz.  Bu liste yalnızca laboratuvar hesabıyla aynı bölgedeki sanal ağları gösterir. Seçilen sanal ağ, laboratuvar hesabı altında oluşturulan laboratuvarlara bağlandı (eşlenmiş).  Bu değişikliğin yapılması sonrasında oluşturulan laboratuvarlarda bulunan tüm sanal makinelerin eşlenen sanal ağdaki kaynaklara erişimi olur.

![VNet 'ten eşe seçin](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Adres aralığı

Ayrıca, laboratuvarlara yönelik sanal makineler için **adres aralığı** sağlama seçeneği de vardır.  **Adres aralığı** özelliği yalnızca laboratuvar için bir **eş sanal ağ** etkinse geçerlidir. Adres aralığı sağlanmışsa, laboratuvar hesabı kapsamındaki laboratuvarlarda bulunan tüm sanal makineler bu adres aralığında oluşturulur. Adres aralığı CıDR gösteriminde (örneğin, 10.20.0.0/20) olmalıdır ve var olan adres aralıklarıyla çakışmaz.  Bir adres aralığı sağlarken, oluşturulacak *laboratuvarların* sayısını göz önünde bulundurmamak ve buna uyum sağlamak için bir adres aralığı sağlamanız gerekir. Laboratuvar Hizmetleri, laboratuvar başına en fazla 512 sanal makine olduğunu varsayar.  Örneğin, '/23 ' olan bir IP aralığı yalnızca bir laboratuvar oluşturabilir.  '/21 ' olan bir Aralık dört laboratuvarın oluşturulmasına olanak sağlar.

**Adres aralığı** belirtilmemişse, Laboratuvar Hizmetleri sanal ağınızla ilişkilendirilen sanal ağı oluştururken Azure tarafından verilen varsayılan adres aralığını kullanır.  Aralık genellikle 10. x. 0.0/16 gibi bir şeydir.  Bu, IP aralığı çakışmasına neden olabilir, bu nedenle laboratuvar ayarlarında bir adres aralığı belirttiğinizden emin olun veya sanal ağınızın eşlenmekte olduğu adres aralığını kontrol edin.

> [!NOTE]
> Laboratuvar hesabı bir sanal ağ ile eşlenirse ancak bir IP adresi aralığına çok dar sahipse Laboratuvar oluşturma başarısız olabilir. Laboratuvar hesabında çok fazla sayıda Labs varsa (her laboratuvar 512 adres kullanıyorsa) adres aralığındaki boş alan tükendirebilirsiniz. 
> 
> Laboratuvar oluşturma işlemi başarısız olursa, laboratuvar hesabı sahibine/yöneticinize başvurun ve adres aralığının artırılmasını isteyin. Yönetici, [bir laboratuvar hesabındaki VM 'ler için adres aralığı belirt](#specify-an-address-range-for-vms-in-the-lab-account) bölümünde bahsedilen adımları kullanarak adres aralığını artırabilir. 

## <a name="configure-after-the-lab-account-is-created"></a>Laboratuvar hesabı oluşturulduktan sonra yapılandırma

Laboratuvar hesabı oluşturma sırasında bir eş ağ ayarlamadıysanız, **Laboratuvar hesabı** sayfasının **Labs yapılandırma** sekmesinden aynı özellik etkinleştirilebilir. Bu ayarda yapılan değişiklik yalnızca değişiklikten sonra oluşturulan laboratuvarlara uygulanır. Görüntüde görebileceğiniz gibi, laboratuvar hesabındaki laboratuvarlar için **eş sanal ağı** etkinleştirebilir veya devre dışı bırakabilirsiniz.

![Laboratuvar oluşturulduktan sonra VNet eşlemesini etkinleştirme veya devre dışı bırakma](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

**Eş sanal ağ** alanı için bir sanal ağ seçtiğinizde, laboratuvar **oluşturucusunun laboratuvar konumunu seçmesine izin ver** seçeneği devre dışı bırakılır. Bunun nedeni, laboratuvar hesabındaki laboratuvarların, eş sanal ağdaki kaynaklarla bağlantı kurmak için laboratuvar hesabıyla aynı bölgede olması gerekir.

> [!IMPORTANT]
> Eşlenen sanal ağ ayarı yalnızca değişiklik yapıldıktan sonra oluşturulan Labs için geçerlidir, mevcut laboratuvarlara uygulanmaz.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Laboratuvar hesabındaki VM 'Ler için bir adres aralığı belirtin
Aşağıdaki yordamda, laboratuvardaki VM 'Ler için bir adres aralığı belirtme adımları vardır. Daha önce belirttiğiniz aralığı güncelleştirirseniz, değiştirilen adres aralığı yalnızca değişiklik yapıldıktan sonra oluşturulan VM 'Ler için geçerlidir. 

Göz önünde bulundurmanız gereken adres aralığını belirtirken bazı kısıtlamalar aşağıda verilmiştir. 

- Ön ek, 23 ' ten küçük veya buna eşit olmalıdır. 
- Bir sanal ağ, laboratuvar hesabına eşlenirse, belirtilen adres aralığı eşlenen sanal ağın adres aralığıyla çakışamaz.

1. **Laboratuvar hesabı** sayfasında, soldaki menüden **Labs ayarları** ' nı seçin.
2. **Adres aralığı** alanı için, laboratuvarda oluşturulacak VM 'ler için adres aralığını belirtin. Adres aralığı, sınıfsız etki alanları arası yönlendirme (CıDR) gösteriminde olmalıdır (örnek: 10.20.0.0/23). Laboratuvardaki sanal makineler, bu adres aralığında oluşturulacaktır.
3. Araç çubuğunda **Kaydet**’i seçin. 

    ![Adres aralığını yapılandır](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın:

- [Laboratuvar oluşturucunun laboratuvar konumunu seçmesine izin verme](allow-lab-creator-pick-lab-location.md)
- [Laboratuvara paylaşılan görüntü Galerisi iliştirme](how-to-attach-detach-shared-image-gallery.md)
- [Laboratuvar sahibi olarak Kullanıcı ekleme](how-to-add-user-lab-owner.md)
- [Laboratuvar için güvenlik duvarı ayarlarını görüntüleme](how-to-configure-firewall-settings.md)
- [Laboratuvar için diğer ayarları yapılandırma](how-to-configure-lab-accounts.md)