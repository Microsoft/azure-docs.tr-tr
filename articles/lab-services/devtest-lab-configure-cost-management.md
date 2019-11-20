---
title: Azure DevTest Labs aylık tahmini laboratuvar maliyeti eğilimini görüntüleyin | Microsoft Docs
description: Azure DevTest Labs aylık tahmini maliyet eğilimi grafiği hakkında bilgi edinin.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: 9180c29b807ef26c6426aab75fe74870fef9669a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318158"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs bir laboratuvarda ilişkili maliyetleri izleyin
Bu makalede, laboratuvarınızın maliyetinin nasıl izleneceği hakkında bilgi verilmektedir. Bu, laboratuvarın geçerli takvim ayının tahmini maliyetinin nasıl görüntüleneceğini gösterir. Makalede ayrıca laboratuvarda kaynak başına aylık maliyet görüntüleme gösterilmektedir.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Aylık tahmini laboratuvar maliyeti eğilimini görüntüleyin 
Bu bölümde, **aylık tahmini maliyet eğilimi** grafiğinin geçerli takvim ayının tahmini maliyetini ve geçerli takvim ayı için öngörülen ayın son tarihini görüntülemek için nasıl kullanılacağını öğreneceksiniz. Ayrıca, ne zaman ulaşılırsa, bu sonuçları size bildirmek için DevTest Labs 'i tetikleyerek harcama hedeflerini ve eşikleri ayarlayarak laboratuvar maliyetlerini yönetmeyi öğreneceksiniz.

Aylık tahmini maliyet eğilimi grafiğini görüntülemek için aşağıdaki adımları izleyin: 

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
3. Laboratuvarlar listesinden laboratuvarınızı seçin.  
4. Sol taraftaki menüden **yapılandırma ve ilkeler** ' i seçin.  
4. Sol menüdeki **Maliyet izleme** bölümünde **maliyet eğilimi** ' ni seçin. Aşağıdaki ekran görüntüsünde maliyet grafiğinin bir örneği gösterilmektedir. 
   
    ![Maliyet grafiği](./media/devtest-lab-configure-cost-management/graph.png)

    **Tahmini maliyet** değeri, geçerli takvim ayının tahmini maliyetlidir. **Öngörülen maliyet** , geçerli takvim ayının tamamı için, önceki beş güne ait laboratuvar maliyeti kullanılarak hesaplanan tahmini maliyettir.

    Maliyet miktarları bir sonraki tam sayıya yuvarlanır. Örneğin: 

   * 5,01, 6 ' ya kadar yuvarlar 
   * 5,50, 6 ' ya kadar yuvarlar
   * 5,99, 6 ' ya kadar yuvarlar

     Grafiğin üzerinde olduğu gibi, grafikte varsayılan olarak gördüğünüz ücretler, [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/) teklifi fiyatları kullanılarak *Tahmini* maliyetlerdir. Ayrıca, [laboratuvarınız için maliyet hedeflerini yöneterek](#managing-cost-targets-for-your-lab) grafiklerde görüntülenen kendi harcama hedeflerini de ayarlayabilirsiniz.

     Maliyet hesaplamasına aşağıdaki maliyetler dahil *değildir* :

   * CSP ve DreamSpark abonelikleri, CSP veya DreamSpark aboneliklerini desteklemeyen laboratuvar maliyetini hesaplamak için [Azure Faturalandırma API 'lerini](../billing/billing-usage-rate-card-overview.md) kullandığından Azure DevTest Labs Şu anda desteklenmemektedir.
   * Teklif ücretleri. Şu anda Microsoft veya Microsoft iş ortakları ile anlaştığınız teklif fiyatlarını (aboneliğiniz altında gösterilen) kullanamazsınız. Yalnızca Kullandıkça Öde ücretleri kullanılır.
   * Vergiler
   * İndirimleriniz
   * Faturalandırma para biriminiz. Şu anda laboratuvar maliyeti yalnızca ABD Doları cinsinden görüntülenir.

### <a name="managing-cost-targets-for-your-lab"></a>Laboratuvarınız için maliyet hedeflerini yönetme
DevTest Labs, aylık tahmini maliyet eğilimi grafiğinde görüntüleyebilmenizi sağlayan bir harcama hedefi ayarlayarak laboratuvarınızda maliyetleri daha iyi yönetmenizi sağlar. DevTest Labs Ayrıca, belirtilen hedef harcama veya eşiğe ulaşıldığında size bir bildirim gönderebilir. 

1. **Maliyet eğilimi** sayfasında **hedefi Yönet**' i seçin.

    ![Hedefi Yönet düğmesi](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. **Hedefi Yönet** sayfasında, bir harcama hedefi ve eşikleri belirtin. Ayrıca, seçilen her eşiğin maliyet eğilimi grafiğinde veya bir Web kancası bildirimi aracılığıyla raporlanıp raporlanmadığını da ayarlayabilirsiniz.

    ![Hedef bölmesini Yönet](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Maliyet hedeflerinin izlenmesini istediğiniz bir zaman aralığı seçin.
      - **Aylık**: maliyet hedefleri her ay izlenir.
      - **Düzeltildi**: maliyet hedefleri, başlangıç ve bitiş tarihlerinde belirttiğiniz tarih aralığı için izlenir. Genellikle, bu değerler projenizin ne kadar süreyle çalıştırılacağını temsil eder.
   - **Hedef maliyet**belirtin. Örneğin, bu laboratuvarda tanımladığınız zaman diliminde ne kadar harcamayı planlıyorsunuz.
   - Belirtilen **hedef maliyetinizi**% 125 25 oranında, raporlanmasını istediğiniz eşiği etkinleştirmek veya devre dışı bırakmak için seçin.
      - **Bildir**: Bu eşik karşılandığında, belirttiğiniz bir Web kancası URL 'SI ile bilgilendirilirsiniz.
      - **Grafik üzerinde çiz**: Bu eşik karşılandığında, sonuçlar, aylık tahmini maliyet eğilimi grafiğini görüntüleme bölümünde açıklandığı gibi, görüntüleyebileceğiniz maliyet eğilimi grafiğinde çizilir.
   - Eşiğin karşılandığı zaman **bildirim** ' ı seçerseniz, bir Web kancası URL 'si belirtmeniz gerekir. Maliyet tümleştirmeleri alanında, **bir tümleştirme eklemek için buraya tıklayın ' ı**seçin. Bildirimi Yapılandır bölmesine bir **Web kancası URL 'si** girin ve ardından **Tamam**' ı seçin.

       ![Bildirim bölmesini yapılandırma](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - **Bildir**' i belirtirseniz bir Web kancası URL 'si tanımlamanız gerekir.
     - Benzer şekilde, bir Web kancası URL 'SI tanımlarsanız, maliyet eşiği  bölmesinde bildirimini **Açık** olarak ayarlamanız gerekir.
     - Buraya girmeden önce bir Web kancası oluşturmanız gerekir.  

       Web kancaları hakkında daha fazla bilgi için bkz. [Web kancası veya API Azure Işlevi oluşturma](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Kaynağa göre maliyeti görüntüle 
Laboratuvardaki aylık maliyet eğilimi özelliği, geçerli takvim ayında ne kadar harcandığını görmenizi sağlar. Ayrıca, son yedi gün içinde harcamalarınızı temel alarak, ayın sonuna kadar harcama projeksiyonunu gösterir. Laboratuvardaki harcamanın neden erken bir şekilde toplantı yaptığını anlamanıza yardımcı olmak için, tablodaki **kaynak başına** aylık maliyeti gösteren **kaynak maliyeti** özelliğini kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
3. Laboratuvarlar listesinden istediğiniz Laboratuvarı seçin.  
4. Sol taraftaki menüden **yapılandırma ve ilkeler** ' i seçin.
5. Soldaki menüdeki **Maliyet izleme** bölümünde **kaynağa göre maliyet** ' i seçin. Bir laboratuvarda ilişkilendirilen her kaynakla ilişkili maliyetleri görürsünüz. 

    ![Kaynağa göre maliyet](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Bu özellik, en çok maliyeti olan kaynakları kolayca belirlemenize yardımcı olur. bu sayede laboratuvar harcamasını azaltmak için eylemler gerçekleştirebilirsiniz. Örneğin, bir VM 'nin maliyeti sanal makine boyutunu temel alır. VM 'nin boyutu arttıkça, daha fazla maliyetlidir. Bu sanal makıne boyutunun neden gerekli olduğunu ve boyutun düşürülme olasılığını anlamak için VM sahibiyle iletişim kurabilirsiniz. böylece, sanal makine ve sahibin boyutunu kolayca bulabilirsiniz.

[Otomatik kapatma ilkesi](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) , laboratuvar VM 'lerini günün belirli bir saatinde kapatarak maliyeti azaltmanıza yardımcı olur. Ancak, bir laboratuvar kullanıcısı kapatma ilkesini geri çevirebilir, bu da VM çalıştırma maliyetini artırır. Otomatik kapatma ilkesinin devre dışı olup olmadığını görmek için tablodaki bir VM 'yi seçebilirsiniz. Bu durumda, sanal makinenin neden devre dışı olduğunu bulmak için VM sahibiyle konuşabilirsiniz.
 
## <a name="next-steps"></a>Sonraki adımlar
Daha sonra deneyebileceğiniz bazı şeyler aşağıda verilmiştir:

* [Laboratuvar Ilkelerini tanımlama](devtest-lab-set-lab-policy.md) -laboratuvarınızın ve VM 'lerinin nasıl kullanıldığını yönetmek için kullanılan çeşitli ilkeleri ayarlamayı öğrenin. 
* [Özel görüntü oluşturma](devtest-lab-create-template.md) -bir VM oluşturduğunuzda, özel bir görüntü veya Market görüntüsü olabilen bir temel belirlersiniz. Bu makalede, bir VHD dosyasından nasıl özel bir görüntü oluşturacağınız gösterilmektedir.
* [Market görüntülerini yapılandırma](devtest-lab-configure-marketplace-images.md) -DevTest Labs, Azure Marketi görüntülerini temel alan sanal makineler oluşturmayı destekler. Bu makalede, bir laboratuvarda VM oluştururken Azure Market görüntülerinin kullanılabileceği, hangilerinin kullanılacağı gösterilmektedir.
* [Laboratuvarda sanal makine oluşturma](devtest-lab-add-vm.md) -bir temel görüntüden (özel veya Market) sanal makine OLUŞTURMAYı ve sanal makinenizde yapıtlarla çalışmayı gösterir.

