---
title: 'ExpressRoute: devre oluşturma ve değiştirme: Azure portal'
description: Oluşturma, sağlama, doğrulayın, güncelleştirme, silme ve bir ExpressRoute bağlantı hattının sağlamasını Kaldır.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 42fe0a91261453251d56f1c556083e93f5c76bec
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083559"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>ExpressRoute devre oluşturma ve değiştirme

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager şablonu](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-circuit-classic.md)
>

Bu makalede Azure portalı ve Azure Resource Manager dağıtım modeli kullanarak ExpressRoute devresi oluşturmanıza yardımcı olur. Ayrıca durumu denetleme, güncelleştirme silin veya bir bağlantı hattının sağlamasını Kaldır.

## <a name="before-you-begin"></a>Başlamadan önce

* Gözden geçirme [önkoşulları](expressroute-prerequisites.md) ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.
* Erişimi olduğundan emin olun [Azure portalında](https://portal.azure.com).
* Yeni ağ kaynakları oluşturma izni olduğundan emin olun. Doğru izinlere sahip değilsiniz, hesap yöneticinize başvurun.
* Yapabilecekleriniz [video görüntüleme](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) adımları daha iyi anlamak için başlamadan önce.

## <a name="create"></a>Oluşturma ve bir ExpressRoute bağlantı hattı sağlama

### <a name="1-sign-in-to-the-azure-portal"></a>1. Azure portal oturum açın

Bir tarayıcıdan [Azure portalına](https://portal.azure.com) gidin ve Azure hesabınızla oturum açın.

### <a name="2-create-a-new-expressroute-circuit"></a>2. yeni bir ExpressRoute devresi oluşturun

> [!IMPORTANT]
> ExpressRoute bağlantı hattı, bir hizmet anahtarı verildiğinde andan itibaren faturalandırılır. Bağlantı sağlayıcısı devreyi sağlamak hazır olduğunda bu işlem bir şekilde gerçekleştirdiğinizden emin olun.

1. Yeni bir kaynak oluşturma seçeneğini belirleyerek bir ExpressRoute bağlantı hattı oluşturabilirsiniz. Tıklayın **kaynak Oluştur** > **ağ** > **ExpressRoute**, aşağıdaki görüntüde gösterildiği gibi:

   ![ExpressRoute bağlantı hattı oluşturma](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Tıkladıktan sonra **ExpressRoute**, gördüğünüz **oluşturma ExpressRoute bağlantı hattı** sayfası. Bu sayfadaki değerleri doldurmayı, faturalandırma modeli (sınırsız veya ölçülen) kullanım ölçümü verilerini ve doğru SKU katmanını (standart veya Premium) belirtmeniz emin olun.

   ![SKU katmanı ve ölçüm verilerini yapılandırma](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Katman** ExpressRoute standart ya da ExpressRoute premium eklenti etkin olup olmadığını belirler. Belirtebileceğiniz **standart** standart SKU'nun almak veya **Premium** premium eklenti için.
   * **Ölçüm verileri** fatura türünü belirler. Belirtebileceğiniz **ölçülen** ölçülen veri planı için ve **sınırsız** sınırsız veri planı için. Faturalandırma türünü **tarifeli** iken **sınırsız**olarak değiştirebileceğinizi unutmayın.

     > [!IMPORTANT]
     > Türü **sınırsız** iken **tarifeli**olarak değiştiremezsiniz.

   * **Eşdüzey hizmet sağlama konumu** nerede sizin eşlemeyi Microsoft ile fiziksel konumu.

     > [!IMPORTANT]
     > Eşleme konumu gösteren [fiziksel konum](expressroute-locations.md) nerede sizin eşlemeyi Microsoft ile. Bu **değil** başvuran Azure ağ kaynak sağlayıcısı bulunduğu coğrafi konum "Location" özelliğine bağlı. Bunlar ilişkili değildir, ancak bir ağ kaynağı sağlayıcı eşleme konumu bağlantı hattının coğrafi olarak yakın seçmek için iyi bir uygulamadır.

### <a name="3-view-the-circuits-and-properties"></a>3. devreleri ve özellikleri görüntüleyin

**Tüm devreler görüntüleyin**

Seçerek oluşturulan bağlantı hatları görüntüleyebileceğiniz **tüm kaynakları** sol taraftaki menüsünden.

![Görünüm devreler](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Özelliklerini görüntülemek**

Bağlantı hattı özelliklerini seçerek görüntüleyebilirsiniz. Üzerinde **genel bakış** sayfasında, bağlantı hattı için hizmet anahtarı hizmet anahtar alanında görüntülenir. Bağlantı hattınız için hizmet anahtarı kopyalayın ve sağlama işlemini tamamlamak için hizmet sağlayıcısı aşağı geçmesi gerekir. Bağlantı hattı hizmet anahtarını bağlantı hattınız için özeldir.

![Özellikleri görüntüle](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. sağlama için hizmet anahtarını bağlantı sağlayıcınıza gönderin

Bu sayfada **sağlayıcısı durumu** hizmet sağlayıcı tarafında sağlama geçerli durumu hakkında bilgi sağlar. **Bağlantı hattı durumu** Microsoft tarafında durumu sağlar. Bağlantı hattı sağlama durumları hakkında daha fazla bilgi için bkz. [iş akışları](expressroute-workflows.md#expressroute-circuit-provisioning-states) makalesi.

Yeni bir ExpressRoute bağlantı hattı'ı oluşturduğunuzda, bağlantı hattı şu durumda olur:

Sağlayıcı Durumu: sağlanmadı<BR>
Bağlantı hattı durumu: etkin

![Sağlama işlemini başlatın](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Bağlantı sağlayıcısı, etkinleştirmeden sürecinde olduğunda bağlantı hattının aşağıdaki duruma değiştirir:

Sağlayıcı Durumu: sağlama<BR>
Bağlantı hattı durumu: etkin

Bir ExpressRoute bağlantı hattı kullanabilmek için şu durumda olmalıdır:

Sağlayıcı Durumu: sağlanan<BR>
Bağlantı hattı durumu: etkin

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. devre anahtarının durumunu ve durumunu düzenli olarak kontrol edin

Siz de seçerek ilginizi çeken bağlantı hattı özelliklerini görüntüleyebilirsiniz. Denetleme **sağlayıcısı durumu** ve azure'a taşındı olun **sağlanan** devam etmeden önce.

![Bağlantı hattı ve sağlayıcısı durumu](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. yönlendirme yapılandırmanızı oluşturun

Adım adım yönergeler için başvurmak [ExpressRoute bağlantı hattı yönlendirme yapılandırması](expressroute-howto-routing-portal-resource-manager.md) makale oluşturma ve değiştirme devre eşlemeleri.

> [!IMPORTANT]
> Bu yönergeler yalnızca Katman 2 bağlantı hizmetleri sunan hizmet sağlayıcıları ile oluşturulan bağlantı hatları için geçerlidir. Yönetilen sunan bir hizmet sağlayıcısı kullanıyorsanız, Katman 3 Hizmetleri (genellikle bir IP VPN, MPLS gibi), bağlantı sağlayıcınız yapılandırır ve yönlendirmeyi sizin için yönetir.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. bir ExpressRoute devresine sanal ağ bağlama

Ardından, bir sanal ağ, ExpressRoute bağlantı hattına bağlayın. Kullanım [sanal ağları ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md) makale Resource Manager dağıtım modeliyle çalışırken.

## <a name="status"></a>ExpressRoute bağlantı hattının durumunu alma

Seçip genel bakış sayfası görüntüleme bir bağlantı hattının durumunu görüntüleyebilirsiniz.

## <a name="modify"></a>Bir ExpressRoute bağlantı hattını değiştirme

Belirli bir ExpressRoute bağlantı hattı özelliklerini bağlantıyı etkilemeden değiştirebilirsiniz. Bant genişliği, SKU, faturalandırma modeli değiştirmek ve klasik işlemlere izin ver **yapılandırma** sayfası. Sınırlar ve sınırlamalar hakkında daha fazla bilgi için bkz: [ExpressRoute SSS](expressroute-faqs.md).

Kapalı kalma süresi olmadan aşağıdaki görevleri gerçekleştirebilirsiniz:

* Etkinleştirmek veya ExpressRoute bağlantı hattı için bir ExpressRoute Premium eklentisi devre dışı bırakın.
* Bant genişliği var. sağlanan ExpressRoute devreniz bağlantı noktasında kapasite artıştır.

  > [!IMPORTANT]
  > Bağlantı hattı bant önceki sürüme indirme desteklenmiyor.

* Ölçüm plandan değiştirme *ölçülen veri* için *sınırsız veri*.

  > [!IMPORTANT]
  > Ölçüm plan sınırsız verilerden ölçülen veri değiştirme desteklenmiyor.

* Etkinleştirebilir ve devre dışı *Klasik işlemlere izin Ver'i*.
  > [!IMPORTANT]
  > ExpressRoute bağlantı hattı mevcut bağlantı noktası üzerinde yetersiz kapasite ise yeniden oluşturmanız gerekebilir. Yoksa hiçbir ek kapasite kullanılabilir o konumda devre yükseltemezsiniz.
  >
  > Bant genişliği sorunsuzca yükseltme yapabilirsiniz ancak kesintiye uğratmadan ExpressRoute bağlantı hattının bant genişliğini indiremezsiniz. Bant genişliği eski sürüme düşürme, ExpressRoute bağlantı hattının sağlamasını kaldırma ve ardından yeni ExpressRoute bağlantı hattı yeniden sağlamak istiyor.
  >
  > Standart bağlantı hattı için izin daha büyük olan kaynaklar kullanıyorsanız, Premium eklenti işlemi devre dışı bırakma başarısız olabilir.

Bir ExpressRoute bağlantı hattı değiştirmek için tıklayın **yapılandırma**.

![Bağlantı hattını değiştirme](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Sağlama kaldırmayı ve bir ExpressRoute bağlantı hattı siliniyor

ExpressRoute devreniz seçerek silebilirsiniz **Sil** simgesi. Aşağıdaki bilgileri not edin:

* ExpressRoute bağlantı hattınızdaki tüm sanal ağların bağlantısını kaldırmanız gerekir. Bu işlem başarısız olursa, herhangi bir sanal ağa bağlantı hattına bağlı olup olmadığını denetleyin.
* ExpressRoute bağlantı hattı Hizmet Sağlayıcısı sağlama durumu ise **sağlama** veya **sağlanan** kendi tarafında bağlantı hattını sağlamasını kaldırmak için hizmet sağlayıcınızla birlikte çalışmanız gerekir. Kaynak ayırmanıza ve hizmeti sağlayıcısı devreyi sağlamayı kaldırma tamamlandıktan ve bize bildiren kadar faturalandırılırsınız devam ediyoruz.
* Hizmet sağlayıcısı devreyi sağlamayı durdurduğunda varsa (Hizmet Sağlayıcısı sağlama durumu ayarlamak **sağlanmadı**), bağlantı hattının silebilirsiniz. Bu durumda bağlantı hattının faturalandırılması durdurulur.

## <a name="next-steps"></a>Sonraki adımlar

Bağlantı hattınızı oluşturduktan sonra sonraki adımlara devam edin:

* [ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](expressroute-howto-routing-portal-resource-manager.md)
* [Sanal ağınız, ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md)
