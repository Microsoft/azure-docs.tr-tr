---
title: 'Öğretici: ExpressRoute bağlantı hattı için eşlemeyi Yapılandırma-Azure portal'
description: Bu öğreticide, Azure portal kullanarak ExpressRoute özel ve Microsoft eşlemesi oluşturma ve sağlama işlemlerinin nasıl yapılacağı gösterilir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: c4f76a02a88b91c082106566ca236abe25d4ed88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567357"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak bir ExpressRoute bağlantı hattı için eşleme oluşturma ve değiştirme

Bu öğreticide, Azure portal kullanarak Azure Resource Manager ExpressRoute bağlantı hattı için Yönlendirme yapılandırması oluşturma ve yönetme işlemlerinin nasıl yapılacağı gösterilir. Ayrıca, bir ExpressRoute bağlantı hattı için durum, güncelleştirme veya silme ve yinelenenleri kaldırma ve sağlama ayarlarını da denetleyebilirsiniz. Devrenize çalışmak için farklı bir yöntem kullanmak istiyorsanız, aşağıdaki listeden bir makale seçin:

> [!div class="op_single_selector"]
> * [Azure portalı](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Ortak eşleme](about-public-peering.md)
> * [Video-özel eşleme](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video-Microsoft eşlemesi](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-routing-classic.md)
> 

ExpressRoute bağlantı hattı için özel eşleme ve Microsoft eşlemesi yapılandırabilirsiniz (Azure genel eşleme yeni devreler için kullanım dışıdır). Eşlemeler, seçtiğiniz herhangi bir sırada yapılandırılabilir. Ancak, her eşlemenin yapılandırmasını birer birer tamamladığınızdan emin olmanız gerekir. Yönlendirme etki alanları ve eşlemeler hakkında daha fazla bilgi için bkz. [ExpressRoute yönlendirme etki alanları](expressroute-circuit-peerings.md). Ortak eşleme hakkında daha fazla bilgi için bkz. [ExpressRoute genel eşleme](about-public-peering.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Devre için Microsoft eşlemesini yapılandırma, güncelleştirme ve silme
> - Devre için Azure özel eşlemesini yapılandırma, güncelleştirme ve silme

## <a name="prerequisites"></a>Önkoşullar

* Yapılandırmaya başlamadan önce aşağıdaki sayfaları gözden geçirdiğinizden emin olun:
    * [Önkoşullar](expressroute-prerequisites.md) 
    * [Yönlendirme gereksinimleri](expressroute-routing.md)
    * [İş Akışları](expressroute-workflows.md)
* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. Devam etmeden önce [ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) bağlantı hattı oluşturma ve bağlantı sağlayıcınız için devre dışı bırakma yönergelerini izleyin. Eşlemeyi yapılandırmak için ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir. 
* Paylaşılan anahtar/MD5 karması kullanmayı planlıyorsanız, tünelin her iki tarafında da anahtarı kullandığınızdan emin olun. Sınır en fazla 25 alfasayısal karakterdir. Özel karakterler desteklenmez. 

Bu yönergeler yalnızca Katman 2 bağlantı hizmetleri sunan hizmet sağlayıcıları ile oluşturulan bağlantı hatları için geçerlidir. Yönetilen katman 3 Hizmetleri (genellikle MPLS gibi bir ıPVPN) sunan bir hizmet sağlayıcısı kullanıyorsanız, bağlantı sağlayıcınız yönlendirmeyi yapılandırır ve yönetir. 

> [!IMPORTANT]
> Şu anda hizmet yönetim portalı aracılığıyla hizmet sağlayıcılar tarafından yapılandırılan eşlemeleri tanıtmıyoruz. Bu özelliği yakında etkinleştirmek için çalışıyoruz. BGP eşayarlarını yapılandırmadan önce hizmet sağlayıcınıza danışın.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft eşlemesi

Bu bölüm, bir ExpressRoute bağlantı hattı için Microsoft eşleme yapılandırmasını oluşturmanıza, almanıza, güncelleştirmenize ve silmesine yardımcı olur.

> [!IMPORTANT]
> 1 Ağustos 2017 ' den önce yapılandırılmış ExpressRoute bağlantı hattı Microsoft eşlemesi, yönlendirme filtreleri tanımlanmasa bile Microsoft eşlemesi aracılığıyla tanıtılan tüm hizmet öneklerini de alacak. 1 Ağustos 2017 ' de veya sonrasında yapılandırılan ExpressRoute devrelerinin Microsoft eşlemesi, bir yol filtresi devresine iliştirilene kadar tanıtılan öneklere sahip olmayacaktır. Daha fazla bilgi için bkz. [Microsoft eşlemesi için rota filtresi yapılandırma](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft eşlemesi oluşturmak için

1. ExpressRoute bağlantı hattını yapılandırın. Devreye devam etmeden önce bağlantı sağlayıcı tarafından tam olarak sağlandığından emin olmak için **sağlayıcı durumunu** kontrol edin.

   Bağlantı sağlayıcınız yönetilen katman 3 hizmetleri sunuyorsa, bağlantı sağlayıcınızdan Microsoft eşlemesini sizin için etkinleştirmesini isteyebilirsiniz. Sonraki bölümlerde listelenen yönergeleri izlemeniz gerekmez. Ancak, bağlantı sağlayıcınız yönlendirmeyi sizin için yönetmezse, devrenizi oluşturduktan sonra bu adımlarla devam edin.

   **Devre dışı sağlayıcı durumu: sağlanmadı**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Sağlayıcı durumunun sağlanmamış olarak ayarlandığını belirten kırmızı bir kutu ile ExpressRoute tanıtım devresi için genel bakış sayfasını gösteren ekran görüntüsü":::

   **Devre dışı sağlayıcı durumu: sağlandı**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Sağlayıcı durumunun sağlanmak üzere olduğunu vurgulama kırmızı kutusuyla ExpressRoute tanıtım devresi için genel bakış sayfasını gösteren ekran görüntüsü":::

2. Bağlantı hattı için Microsoft eşlemesini yapılandırın. Devam etmeden önce aşağıdaki bilgilere sahip olduğunuzdan emin olun.

   * Size ait ve bir RıR/IÇ_VERIM_ORANı 'ye kayıtlı bir alt ağ çifti. Birincil bağlantı için bir alt ağ kullanılır, diğeri ise ikincil bağlantı için kullanılır. Bu alt ağların her birinde, Microsoft 'un yönlendiricisi için kullanılabilen ikinci IP 'yi kullandığından, ilk kullanılabilir IP adresini yönlendiricinize atayacaksınız. Bu alt ağ çifti için üç seçeneğiniz vardır:
       * IPv4: Iki/30 alt ağı. Bunların geçerli ortak IPv4 önekleri olması gerekir.
       * IPv6: Iki/126 alt ağı. Bunlar geçerli genel IPv6 ön ekleri olmalıdır.
       * Her ikisi: Iki/30 alt ağ ve iki/126 alt ağı.
   * Bu eşlemenin kurulacağı geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun. Birincil ve Ikincil bağlantılar için aynı VLAN KIMLIĞINI kullanmanız gerekir.
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.
   * Tanıtılan ön ekler: BGP oturumunda tanıtmayı planladığınız tüm ön eklerin bir listesini sağlarsınız. Yalnızca ortak IP adresi ön ekleri kabul edilir. Bir önek kümesi gönderilmesini planlıyorsanız, virgülle ayrılmış bir liste gönderebilirsiniz. Bu önekler size bir RIR / IRR içinde kaydedilmiş olmalıdır.
   * **Isteğe bağlı-** Müşteri ASN: eşdeğer olarak eşleme kayıtlı olmayan ön ekler varsa, birlikte kaydettirildikleri AS numarasını belirtebilirsiniz.
   * Yönlendirme Kayıt Defteri Adı: AS numarası ve öneklerinin kaydedildiği RIR / IRR’yi belirtebilirsiniz.
   * **Isteğe bağlı-** Kullanmayı seçerseniz bir MD5 karma değeri.
1. Yapılandırmak istediğiniz eşlemeyi aşağıdaki örnekte gösterildiği gibi seçebilirsiniz. Microsoft eşleme satırını seçin.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Microsoft eşleme satırını seçin":::

4. Microsoft eşlemesini yapılandırın. Tüm parametreleri belirttikten sonra yapılandırmayı **kaydedin** . Aşağıdaki görüntüde örnek bir yapılandırma gösterilmektedir:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Microsoft eşleme doğrulamasını yapılandırma gerekli":::

> [!IMPORTANT]
> Microsoft, belirtilen ' tanıtılan genel ön eklerin ' ve ' eşdüzey ASN ' (veya ' müşteri ASN ') tarafından Internet yönlendirme kayıt defterinde size atanıp atanmadığını doğrular. Diğer bir varlıktan ortak ön ekleri alıyorsanız ve atama, yönlendirme kayıt defteriyle birlikte kaydedilmetiyse, otomatik doğrulama tamamlanmaz ve el ile doğrulama gerektirir. Otomatik doğrulama başarısız olursa, ' doğrulama gerekiyor ' iletisini görürsünüz. 
>
> ' Doğrulama gerekli ' iletisini görürseniz, genel önekleri gösteren belge (ler) i, yönlendirme kayıt defterindeki ön eklerin sahibi olarak listelenen varlık tarafından kuruluşunuza atanır ve bir destek bileti açarak bu belgeleri el ile doğrulama için gönderir. 
>

   Devre dışı bir ' doğrulama gerekli ' durumuna alırsa, ön eklerin sahipliğinin destek ekibimize gösterilmesi için bir destek bileti açmanız gerekir. Aşağıdaki örnekte gösterildiği gibi, doğrudan portaldan bir destek bileti açabilirsiniz:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Doğrulama gerekiyor-destek bileti":::

5. Yapılandırma başarıyla kabul edildikten sonra aşağıdaki görüntüye benzer bir şey görürsünüz:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Eşleme durumu: yapılandırıldı":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft eşleme ayrıntılarını görüntülemek için

Eşleme satırını seçerek Microsoft eşlemesi özelliklerini görüntüleyebilirsiniz.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Microsoft eşleme özelliklerini görüntüle":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft eşlemesi yapılandırmasını güncelleştirmek için

Değiştirmek istediğiniz eşlemenin satırını seçip eşleme özelliklerini değiştirebilir ve değişikliklerinizi kaydedebilirsiniz.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Eşleme satırı seç":::

## <a name="azure-private-peering"></a><a name="private"></a>Azure özel eşlemesi

Bu bölüm, bir ExpressRoute bağlantı hattı için Azure özel eşleme yapılandırmasını oluşturmanıza, almanıza, güncelleştirmenize ve silmenize yardımcı olur.

> [!IMPORTANT]
> Özel eşleme için IPv6 desteği şu anda **genel önizlemededir**. Sanal ağınızı IPv6 tabanlı özel eşleme ile bir ExpressRoute devresine bağlamak istiyorsanız, lütfen sanal ağınızın ikili bir yığın olduğundan ve [burada](../virtual-network/ipv6-overview.md)açıklanan yönergeleri izlediğinden emin olun.
> 
> 

### <a name="to-create-azure-private-peering"></a>Azure özel eşlemesi oluşturmak için

1. ExpressRoute bağlantı hattını yapılandırın. Devam etmeden önce bağlantı sağlayıcı tarafından bağlantı hattının tam olarak sağlandığından emin olun. 

   Bağlantı sağlayıcınız yönetilen katman 3 hizmetleri sunuyorsa, bağlantı sağlayıcınızdan sizin için Azure özel eşlemeyi etkinleştirmesini isteyebilirsiniz. Sonraki bölümlerde listelenen yönergeleri izlemeniz gerekmez. Ancak, bağlantı sağlayıcınız yönlendirmeyi sizin için yönetmezse, devrenizi oluşturduktan sonra sonraki adımlarla devam edin.

   **Devre dışı sağlayıcı durumu: sağlanmadı**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Sağlanmadı olarak ayarlanan sağlayıcı durumunu vurgulamakta olan ExpressRoute tanıtım devresi için genel bakış sayfasını gösteren ekran görüntüsü":::

   **Devre dışı sağlayıcı durumu: sağlandı**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Sağlanan sağlayıcının durumunu gösteren kırmızı bir kutu olan ExpressRoute tanıtım devresi için genel bakış sayfasını gösteren ekran görüntüsü":::

2. Bağlantı hattı için Azure özel eşlemesini yapılandırın. Sonraki adımlarla devam etmeden önce aşağıdaki öğelere sahip olduğunuzdan emin olun:

   * Sanal ağlar için ayrılan herhangi bir adres alanının parçası olmayan bir alt ağ çifti. Birincil bağlantı için bir alt ağ kullanılır, diğeri ise ikincil bağlantı için kullanılır. Bu alt ağların her birinde, Microsoft 'un yönlendiricisi için kullanılabilen ikinci IP 'yi kullandığından, ilk kullanılabilir IP adresini yönlendiricinize atayacaksınız. Bu alt ağ çifti için üç seçeneğiniz vardır:
       * IPv4: Iki/30 alt ağı.
       * IPv6: Iki/126 alt ağı.
       * Her ikisi: Iki/30 alt ağ ve iki/126 alt ağı.
   * Bu eşlemenin kurulacağı geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun. Birincil ve Ikincil bağlantılar için aynı VLAN KIMLIĞINI kullanmanız gerekir.
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz. 65515 ile 65520 arasında (ikisi de dahil olmak üzere), bu eşleme için özel bir AS numarası kullanabilirsiniz.
   * Özel eşlemeyi yapılandırırken şirket içi kenar yönlendiricinizin yollarını BGP aracılığıyla Azure 'a duyurmanız gerekir.
   * **Isteğe bağlı-** Kullanmayı seçerseniz bir MD5 karma değeri.
3. Aşağıdaki örnekte gösterildiği gibi Azure özel eşleme satırını seçin:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Özel eşleme satırını seçin":::

4. Özel eşleme oluşturun. Tüm parametreleri belirttikten sonra yapılandırmayı **kaydedin** .

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Özel eşlemeyi yapılandırma":::

5. Yapılandırma başarıyla kabul edildikten sonra, aşağıdaki örneğe benzer bir şey görürsünüz:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Özel eşleme kaydedildi":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure özel eşleme ayrıntılarını görüntülemek için

Eşlemeyi seçerek Azure özel eşleme özelliklerini görüntüleyebilirsiniz.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Özel eşleme özelliklerini görüntüle":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure özel eşleme yapılandırmasını güncelleştirmek için

Eşleme için satırı seçebilir ve eşleme özelliklerini değiştirebilirsiniz. Güncelleştirme sonrasında yaptığınız değişiklikleri kaydedin.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Özel eşlemeyi Güncelleştir":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft eşlemesini silmek için

Eşlemeyi sağ tıklayıp aşağıdaki görüntüde gösterildiği gibi **Sil** ' i seçerek, Microsoft eşleme yapılandırmanızı kaldırabilirsiniz:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Microsoft eşlemesini Sil":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure özel eşlemesini silmek için

Eşlemeyi sağ tıklayıp aşağıdaki görüntüde gösterildiği gibi **Sil** ' i seçerek özel eşleme yapılandırmanızı kaldırabilirsiniz:

> [!WARNING]
> Bu işlemi çalıştırmadan önce tüm sanal ağların ve ExpressRoute Global Reach bağlantılarının kaldırıldığından emin olmanız gerekir. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Özel eşlemeyi Sil":::

## <a name="next-steps"></a>Sonraki adımlar

Azure özel eşlemesini yapılandırdıktan sonra, sanal bir ağı devresine bağlamak için bir ExpressRoute ağ geçidi oluşturabilirsiniz. 

> [!div class="nextstepaction"]
> [ExpressRoute için sanal ağ geçidi yapılandırma](expressroute-howto-add-gateway-resource-manager.md)