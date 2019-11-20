---
title: Öğretici-optimizasyon önerileriyle Azure maliyetlerini azaltma | Microsoft Docs
description: Bu öğretici, iyileştirme önerilerine göre hareket ettirmeniz durumunda Azure maliyetlerini azaltmanıza yardımcı olur.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 603de4d9bed936ecb91f130b0e30f6d1383a9092
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935790"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Öğretici: önerilerin maliyetlerini Iyileştirme

Azure Maliyet Yönetimi, Azure Danışmanı ile birlikte çalışarak maliyet iyileştirme önerileri sunar. Azure Danışmanı boşta olan ve az kullanılan kaynakları belirleyerek verimliliği iyileştirmenize ve geliştirmenize yardımcı olur. Bu öğreticide, az kullanılan Azure kaynaklarını tespit ettiğiniz ve maliyetleri azaltmak için işlem yaptığınız bir örnek adım adım gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Potansiyel kullanım verimsizlikleri görüntülemek için maliyet iyileştirme önerilerini görüntüleyin
> * Bir sanal makineyi daha uygun maliyetli bir seçeneğe göre yeniden boyutlandırma önerisi üzerinde işlem yapın
> * Sanal makinenin başarıyla yeniden boyutlandırıldığından emin olmak için eylemi doğrulayın

## <a name="prerequisites"></a>Önkoşullar
Çeşitli kapsamlar ve Azure hesap türleri için öneriler mevcuttur. Desteklenen hesap türlerinin tam listesini görüntülemek için bkz. [maliyet yönetimi verilerini anlama](understand-cost-mgt-data.md). Maliyet verilerini görüntülemek için aşağıdaki kapsamlardan birine veya daha fazlasına en azından yazma erişiminiz olmalıdır. Kapsamlar hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).

- Abonelik
- Kaynak grubu

En az 14 günlük etkinlik içeren etkin sanal makinelere sahip olmanız gerekir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın
[https://portal.azure.com](https://portal.azure.com/) adresinden Azure portalında oturum açın.

## <a name="view-cost-optimization-recommendations"></a>Maliyet iyileştirme önerilerini görüntüleme

Bir aboneliğin maliyet iyileştirme önerilerini görüntülemek için Azure portal istenen kapsamı açın ve **danışman önerileri**' ni seçin.

Bir yönetim grubunun önerilerini görüntülemek için, istenen kapsamı Azure portal açın ve menüden **Maliyet Analizi** ' ni seçin. Yönetim grubu gibi farklı bir kapsama geçiş yapmak için **kapsam** hap ' i kullanın. Menüdeki **danışman önerilerini** seçin. Kapsamlar hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).

![Azure portal gösterilen maliyet yönetimi Danışmanı önerileri](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Önerilerin listesi kullanım verimsizlikleri tanımlar veya ek para tasarrufu yapmanıza yardımcı olabilecek satın alma önerilerini gösterir. Toplanan **olası yıllık tasarruflar** , öneri kurallarını karşılayan tüm VM 'lerinizi kapatırsanız veya serbest bırakırsanız kaydedebilmeniz gereken toplam miktarı gösterir. Onları kapatmak istemiyorsanız, bunları daha az maliyetli bir VM SKU 'SU olarak yeniden boyutlandırmayı göz önünde bulundurmanız gerekir.

Olası **yıllık tasarruflarla**birlikte **etki** kategorisi, mümkün olduğunca tasarruf etme potansiyeli olan önerileri belirlemenize yardımcı olmak için tasarlanmıştır.

Yüksek etki önerileri şunlardır:
- [Kullandıkça Öde maliyetlerinden tasarruf etmek için ayrılmış sanal makine örnekleri satın alın](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Az kullanılan örnekleri yeniden boyutlandırarak veya kapatarak sanal makineyi harcamayı iyileştirin](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Yönetilen disk anlık görüntülerini depolamak için standart depolama kullanma](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Orta etki önerileri şunlardır:
- [Başarısız olan Azure Data Factory işlem hatlarını silme](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Sağlanmamış ExpressRoute devreleri ortadan kaldırarak maliyetleri düşürün](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Boştaki Sanal ağ geçitlerini silerek veya yeniden yapılandırarak maliyetleri azaltın](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Öneri üzerinde işlem yapın

Azure Danışmanı, sanal makine kullanımınızı yedi gün boyunca izler ve daha sonra az kullanılan sanal makineleri tanımlar. CPU kullanımı yüzde beş veya daha az olan sanal makineler ve ağ kullanımı, dört veya daha fazla gün için yedi MB veya daha az gündür ve düşük kullanım sanal makineler olarak değerlendirilir.

%5 veya daha az CPU kullanımı ayarı varsayılandır ancak ayarları ayarlayabilirsiniz. Ayarı ayarlama hakkında daha fazla bilgi için, [Ortalama CPU kullanımı kuralını yapılandırma veya düşük kullanım sanal makine önerisi](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)bölümüne bakın.

Bazı senaryolar tasarıma göre düşük kullanıma yol açabilir, ancak sanal makinelerinizin boyutunu daha ucuz boyutlara değiştirerek genellikle tasarruf edebilirsiniz. Yeniden boyutlandırma eylemi seçerseniz gerçek tasarruflarınız farklılık gösterebilir. Bir sanal makineyi yeniden boyutlandırma örneği hakkında yol açalım.

Öneriler listesinde, **doğru boyut ' a tıklayın veya kapalı sanal makineler** önerisi ' ne tıklayın. Sanal makine adayları listesinde, yeniden boyutlandırmak için bir sanal makine seçin ve ardından sanal makineye tıklayın. Kullanım ölçümlerini doğrulayabilmeniz için sanal makinenin ayrıntıları gösterilir. **Olası yıllık tasarruf** değeri, sanal makineyi kapatırsanız veya kaldırırsanız kaydedebilecekleri şeydir. Bir VM 'nin yeniden boyutlandırılması muhtemelen paradan tasarruf etmenizi sağlar, ancak olası yıllık tasarruflarının tamamını kaydedemezsiniz.

![Öneri ayrıntıları örneği](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

VM ayrıntılarında, sanal makinenin uygun bir yeniden boyutlandırma adayı olduğunu doğrulamak için kullanımını kontrol edin.

![Geçmiş kullanımı gösteren örnek VM ayrıntıları](./media/tutorial-acm-opt-recommendations/vm-details.png)

Geçerli sanal makinenin boyutunu aklınızda edin. Sanal makinenin yeniden boyutlandırılması gerektiğini doğruladıktan sonra, sanal makinelerin listesini görmeniz için VM ayrıntılarını kapatın.

Kapatılacak veya yeniden boyutlandırılacak aday listesinde, * * yeniden boyutlandır *&lt;Fromvirtualdeninesku&gt;* "&lt;Tovirtual, inesku&gt;* * * olarak belirleyin.
sanal makineyi yeniden boyutlandırma seçeneğiyle ![örnek önerisi](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Ardından, kullanılabilir yeniden boyutlandırma seçeneklerinin bir listesi sunulur. Senaryonuz için en iyi performansı ve maliyet verimliliğini sağlayacak olanı seçin. Aşağıdaki örnekte, seçilen seçenek **Standard_D8s_v3** iken **Standard_D2s_v3**olarak yeniden boyutlandırılır.

![Kullanılabilir VM boyutlarının bir boyut seçebileceği örnek listesi](./media/tutorial-acm-opt-recommendations/choose-size.png)

Uygun bir boyut seçtikten sonra yeniden boyutlandır eylemini başlatmak için yeniden **Boyutlandır** ' ı tıklatın.

Yeniden boyutlandırma için etkin olarak çalışan bir sanal makinenin yeniden başlatılması gerekir. Sanal makine bir üretim ortamındaysanız, çalışma saatlerinden sonra yeniden boyutlandırma işlemini çalıştırmanızı öneririz. Yeniden başlatmanın zamanlanması, geçici olarak kullanım dışı kalması nedeniyle oluşan kesintilere engel olabilir.

## <a name="verify-the-action"></a>Eylemi doğrulama

VM yeniden boyutlandırma işlemi başarıyla tamamlandığında, bir Azure bildirimi gösterilir.

![Başarılı yeniden boyutlandırılan sanal makine bildirimi](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Potansiyel kullanım verimsizlikleri görüntülemek için maliyet iyileştirme önerilerini görüntüleyin
> * Bir sanal makineyi daha uygun maliyetli bir seçeneğe göre yeniden boyutlandırma önerisi üzerinde işlem yapın
> * Sanal makinenin başarıyla yeniden boyutlandırıldığından emin olmak için eylemi doğrulayın

Maliyet yönetimi en iyi yöntemler makalesini henüz okumadıysanız, maliyetlerin yönetilmesine yardımcı olmak için göz önünde bulundurmanız gereken üst düzey kılavuz ve ilkeler sağlar.

> [!div class="nextstepaction"]
> [Maliyet yönetimi en iyi uygulamaları](cost-mgt-best-practices.md)
