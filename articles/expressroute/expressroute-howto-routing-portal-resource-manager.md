---
title: 'Azure ExpressRoute: eşlemeyi yapılandırma'
description: Bu makalede, ExpressRoute özel ve Microsoft eşlemesi oluşturma ve sağlama adımları belgelemekte. Bu makalede ayrıca, bir devre için durumun durumunu denetleme, güncelleştirme veya silme işlemlerinin nasıl yapılacağı gösterilir.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.openlocfilehash: 5fb728cccd77d0cefd10c124cb7215dc3b880fe3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083533"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Bir ExpressRoute bağlantı hattı için eşlemesi oluşturma ve değiştirme

Bu makale, Azure portal kullanarak bir Azure Resource Manager (ARM) ExpressRoute devresi için Yönlendirme yapılandırması oluşturmanıza ve yönetmenize yardımcı olur. Ayrıca, durum, update veya delete denetleyin ve eşlemeler için ExpressRoute bağlantı hattı sağlamasını kaldırma. Bağlantı hattınız ile çalışmak için farklı bir yöntem kullanmak istiyorsanız, bir makale aşağıdaki listeden seçin:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - özel eşdüzey hizmet sağlama](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - genel eşdüzey hizmet sağlama](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft eşdüzey hizmet sağlama](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-routing-classic.md)
> 

ExpressRoute bağlantı hattı için Azure özel ve Microsoft eşlemesi yapılandırabilirsiniz (Azure genel eşleme yeni devreler için kullanım dışıdır). Eşlemeleri seçtiğiniz herhangi bir sırayla yapılandırabilirsiniz. Ancak, her eşlemenin yapılandırmasını birer birer tamamladığınızdan emin olmanız gerekir. Yönlendirme etki alanları ve eşlemeler hakkında daha fazla bilgi için bkz. [devreler ve eşlemeler hakkında](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Yapılandırma önkoşulları

* Yapılandırmaya başlamadan önce [önkoşullar](expressroute-prerequisites.md) sayfasını, [yönlendirme gereksinimleri](expressroute-routing.md) sayfasını ve [iş akışları](expressroute-workflows.md) sayfasını gözden geçirdiğinizden emin olun.
* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. Devam etmeden önce [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-portal-resource-manager.md) yönergelerini izleyin ve bağlantı sağlayıcınızın bağlantı hattını etkinleştirmesini isteyin. Eşlemeyi yapılandırmak için ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir. 
* Paylaşılan bir anahtar/MD5 karması kullanmayı planlıyorsanız, bunu tünelin her iki tarafında da kullandığınızdan emin olun ve alfasayısal karakter sayısını en fazla 25 olarak sınırlandırın. Özel karakterler desteklenmez. 

Bu yönergeler yalnızca Katman 2 bağlantı hizmetleri sunan hizmet sağlayıcıları ile oluşturulan bağlantı hatları için geçerlidir. Katman 3 Hizmetleri (genellikle gibi bir IPVPN MPLS) yönetilen sunan bir hizmet sağlayıcısı kullanıyorsanız, bağlantı sağlayıcınız yapılandırır ve yönlendirmeyi sizin için yönetir. 

> [!IMPORTANT]
> Şu anda hizmet yönetim portalı aracılığıyla hizmet sağlayıcılar tarafından yapılandırılan eşlemeleri tanıtmıyoruz. Bu özelliği yakında etkinleştirmek için çalışıyoruz. BGP eşlemelerini yapılandırmadan önce hizmet sağlayıcınıza başvurun.
> 
> 

## <a name="msft"></a>Microsoft eşlemesi

Bu bölümde, oluşturma, alma, güncelleştirme ve bir ExpressRoute bağlantı hattı için Microsoft eşleme yapılandırmasını silme yardımcı olur.

> [!IMPORTANT]
> 1 Ağustos 2017'den önce yapılandırılmış olan ExpressRoute devrelerinin Microsoft eşdüzey hizmet sağlama, tüm hizmet ön eklerin rota filtreleri tanımlanmamış olsa bile, Microsoft eşlemesi tanıtılan sahip olur. 1 Ağustos 2017 veya sonrasında yapılandırılmış ExpressRoute devrelerinin Microsoft eşlemesi tüm ön ekleri olmaz bağlantı hattına bir rota filtresinde bağlanana kadar tanıtılan. Daha fazla bilgi için [Microsoft eşlemesi için rota filtresini Yapılandır](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft eşlemesi oluşturmak için

1. ExpressRoute bağlantı hattını yapılandırın. Devreye devam etmeden önce bağlantı sağlayıcı tarafından tam olarak sağlandığından emin olmak için **sağlayıcı durumunu** kontrol edin.

   Bağlantı sağlayıcınız yönetilen Katman 3 Hizmetleri sunuyorsa, bağlantı sağlayıcınızdan sizin için eşlemeyi Microsoft etkinleştirmek isteyebilirsiniz. Bu durumda, sonraki bölümlerde listelenen yönergeleri izlemeniz gerekmez. Ancak, bağlantı sağlayıcınız yönlendirmeyi sizin için yönetmezse, devrenizi oluşturduktan sonra bu adımlarla devam edin.

   **Devre dışı sağlayıcı durumu: sağlanmadı**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Devre dışı sağlayıcı durumu: sağlandı**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Bağlantı hattı için Microsoft eşlemesini yapılandırın. Devam etmeden önce aşağıdaki bilgilere sahip olduğunuzdan emin olun.

   * Birincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır. Microsoft, yönlendirici için ikinci kalmayacak IP kullandığından bu alt ağından yönlendiriciniz için ilk kullanılabilir IP adresi atar.
   * İkincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır. Microsoft, yönlendirici için ikinci kalmayacak IP kullandığından bu alt ağından yönlendiriciniz için ilk kullanılabilir IP adresi atar.
   * Bu eşlemenin kurulacak geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun. Birincil ve ikincil bağlantı için aynı VLAN kimliğini kullanmanız gerekir
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.
   * Tanıtılan önekler: BGP oturumunda tanıtmayı planladığınız tüm öneklerin bir listesini sağlamanız gerekir. Yalnızca ortak IP adresi ön ekleri kabul edilir. Ön ek kümesi göndermeyi planlıyorsanız, virgülle ayrılmış bir liste gönderebilirsiniz. Bu ön ekler size bir RIR / IRR içinde kaydedilmiş olmalıdır.
   * **İsteğe bağlı -** müşteri ASN'si: eşleme AS numarasına kayıtlı olmayan önekler Tanıtıyorsanız, kayıtlı oldukları AS numarasını belirtebilirsiniz.
   * Yönlendirme Kayıt Defteri Adı: AS numarası ve öneklerinin kaydedildiği RIR / IRR’yi belirtebilirsiniz.
   * **İsteğe bağlı -** kullanmayı seçerseniz bir MD5 karma değeri.
3. Aşağıdaki örnekte gösterildiği gibi yapılandırmak için istediğiniz eşlemeyi seçebilirsiniz. Microsoft eşleme satırını seçin.

   [![Microsoft eşleme satırını seçin](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Microsoft eşleme satırını seçin")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Microsoft eşlemesini yapılandırın. Tüm parametreleri belirttikten sonra yapılandırmayı **kaydedin** . Aşağıdaki görüntüde örnek bir yapılandırma gösterilmektedir:

   ![Microsoft eşlemesini yapılandırın](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft, belirtilen ' tanıtılan genel ön eklerin ' ve ' eşdüzey ASN ' (veya ' müşteri ASN ') tarafından Internet yönlendirme kayıt defterinde size atanıp atanmadığını doğrular. Diğer bir varlıktan ortak ön ekleri alıyorsanız ve atama, yönlendirme kayıt defteriyle birlikte kaydedilmetiyse, otomatik doğrulama tamamlanmaz ve el ile doğrulama gerektirir. Otomatik doğrulama başarısız olursa, ' doğrulama gerekiyor ' iletisini görürsünüz. 
>
> ' Doğrulama gerekli ' iletisini görürseniz, genel önekleri gösteren belge (ler) i, yönlendirme kayıt defterindeki ön eklerin sahibi olarak listelenen varlık tarafından kuruluşunuza atanır ve bu belgeleri el ile doğrulama için aşağıda gösterildiği gibi bir destek bileti açılıyor. 
>

   Devre dışı bir ' doğrulama gerekli ' durumuna alırsa, ön eklerin sahipliğinin destek ekibimize gösterilmesi için bir destek bileti açmanız gerekir. Aşağıdaki örnekte gösterildiği gibi doğrudan portalından bir destek bileti açabilirsiniz:

   ![Doğrulama gerekiyor-destek bileti](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Yapılandırma başarıyla kabul edildikten sonra aşağıdaki görüntüye benzer bir şey görürsünüz:

   ![Eşleme durumu: yapılandırıldı](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Eşleme durumu: yapılandırıldı")]

### <a name="getmsft"></a>Microsoft eşleme ayrıntılarını görüntülemek için

Eşleme satırını seçerek Microsoft eşlemesi özelliklerini görüntüleyebilirsiniz.

[![Microsoft eşleme özelliklerini görüntüle](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Özellikleri görüntüle")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Microsoft eşleme yapılandırmasını güncelleştirmek için

Değiştirmek istediğiniz eşlemenin satırını seçip eşleme özelliklerini değiştirebilir ve değişikliklerinizi kaydedebilirsiniz.

![Eşleme satırı seç](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Microsoft eşlemesini silmek için

Aşağıdaki görüntüde gösterildiği gibi, silme simgesine tıklayarak eşleme yapılandırmanızı kaldırabilirsiniz:

![Eşlemeyi Sil](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Azure özel eşdüzey hizmet sağlama

Bu bölümde, oluşturma, alma, güncelleştirme ve bir ExpressRoute bağlantı hattı için Azure özel eşleme yapılandırmasını silme yardımcı olur.

### <a name="to-create-azure-private-peering"></a>Azure özel eşlemesi oluşturmak için

1. ExpressRoute bağlantı hattını yapılandırın. Devam etmeden önce bağlantı sağlayıcı tarafından bağlantı hattının tam olarak sağlandığından emin olun. 

   Bağlantı sağlayıcınız yönetilen Katman 3 Hizmetleri sunuyorsa, bağlantı sağlayıcınızdan Azure özel, eşlemeyi etkinleştirmesini isteyebilirsiniz. Bu durumda, sonraki bölümlerde listelenen yönergeleri izlemeniz gerekmez. Ancak, bağlantı sağlayıcınız yönlendirmeyi sizin için yönetmezse, devrenizi oluşturduktan sonra bir sonraki adımla devam edin.

   **Devre dışı sağlayıcı durumu: sağlanmadı**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Devre dışı sağlayıcı durumu: sağlandı**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Bağlantı hattı için Azure özel eşlemesini yapılandırın. Sonraki adımlara devam etmeden önce aşağıdaki öğelerin bulunduğundan emin olun:

   * Birincil bağlantı için bir /30 alt ağı. Alt ağ, sanal ağlar için ayrılmış herhangi bir adres alanının parçası olmamalıdır. Microsoft, yönlendirici için ikinci kalmayacak IP kullandığından bu alt ağından yönlendiriciniz için ilk kullanılabilir IP adresi atar.
   * İkincil bağlantı için bir /30 alt ağı. Alt ağ, sanal ağlar için ayrılmış herhangi bir adres alanının parçası olmamalıdır. Microsoft, yönlendirici için ikinci kalmayacak IP kullandığından bu alt ağından yönlendiriciniz için ilk kullanılabilir IP adresi atar.
   * Bu eşlemenin kurulacak geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun. Birincil ve ikincil bağlantı için aynı VLAN kimliğini kullanmanız gerekir
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz. 65515 ile 65520 arasında (ikisi de dahil olmak üzere), bu eşleme için özel bir AS numarası kullanabilirsiniz.
   * Özel eşlemeyi ayarlarken, şirket içi kenar yönlendiricinizin yollarını BGP aracılığıyla Azure 'a duyurmanız gerekir.
   * **İsteğe bağlı -** kullanmayı seçerseniz bir MD5 karma değeri.
3. Aşağıdaki örnekte gösterildiği gibi Azure özel eşleme satırını seçin:

   [![Özel eşleme satırını seçin](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Özel eşleme satırını seçin")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Özel eşleme oluşturun. Tüm parametreleri belirttikten sonra yapılandırmayı **kaydedin** .

   ![Özel eşleme oluşturun](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Yapılandırma başarıyla kabul edildikten sonra aşağıdaki örneğe benzer bir şey görürsünüz:

   ![özel eşleme kaydedildi](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Azure özel eşleme ayrıntılarını görüntülemek için

Eşlemeyi seçerek Azure özel eşleme özelliklerini görüntüleyebilirsiniz.

[![Özel eşleme özelliklerini görüntüle](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Özel eşleme özelliklerini görüntüle")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Azure özel eşleme yapılandırmasını güncelleştirmek için

Eşleme için satırı seçebilir ve eşleme özelliklerini değiştirebilirsiniz. Güncelleştirme sonrasında yaptığınız değişiklikleri kaydedin.

![Özel eşleme güncelleştir](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Azure özel eşlemeyi silmek için

Aşağıdaki görüntüde gösterilen silme simgesini seçerek eşleme yapılandırmanızı kaldırabilirsiniz:

> [!WARNING]
> Bu örneği çalıştırmadan önce tüm sanal ağlar ve ExpressRoute Global erişim bağlantıları kaldırıldı emin olmanız gerekir. 
> 
> 

![Özel eşleme Sil](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Azure genel eşdüzey hizmet sağlama

Bu bölümde, oluşturma, alma, güncelleştirme ve bir ExpressRoute bağlantı hattı için Azure ortak eşleme yapılandırmasını silme yardımcı olur.

> [!Note]
> Azure genel eşlemesi yeni devreler için kullanım dışıdır. Daha fazla bilgi için bkz. [ExpressRoute eşlemesi](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Azure ortak eşleme ayrıntılarını görüntülemek için

Eşlemeyi seçerek Azure ortak eşleme özelliklerini görüntüleyin.

### <a name="updatepublic"></a>Azure ortak eşleme yapılandırmasını güncelleştirmek için

Eşleme için satırı seçin ve ardından eşleme özelliklerini değiştirin.

### <a name="deletepublic"></a>Azure ortak eşlemesini silmek için

Eşleme yapılandırmanızı kaldırın ve Sil simgesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım, [bir ExpressRoute bağlantı hattına bir VNet bağlama](expressroute-howto-linkvnet-portal-resource-manager.md)
* ExpressRoute iş akışları hakkında daha fazla bilgi için bkz. [ExpressRoute iş akışları](expressroute-workflows.md).
* Bağlantı hattı eşlemesi hakkında daha fazla bilgi için bkz. [ExpressRoute bağlantı hattı ve yönlendirme etki alanları](expressroute-circuit-peerings.md).
* Sanal ağlarla çalışma hakkında daha fazla bilgi için bkz. [Sanal ağa genel bakış](../virtual-network/virtual-networks-overview.md).
