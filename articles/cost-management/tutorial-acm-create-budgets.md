---
title: Öğretici - Azure bütçelerini yönetin ve oluşturun | Microsoft Docs
description: Bu öğretici, plan ve hesap Fiyatlar, kullandığınız Azure Hizmetleri için yardımcı olur.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: c4de8b0d78d66709d13526c69f9d33b16dbad1dc
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010231"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Öğretici: Oluşturma ve Azure bütçelerini yönetin

Maliyet Yönetimi hizmetindeki bütçe işlevi, kuruluşunuzda sorumluluk kültürünü planlamanıza ve güçlendirmenize yardımcı olur. Bütçeleri kullanarak belirli bir dönem içinde kullandığınız veya abone olduğunuz Azure hizmetlerini takip edebilirsiniz. Diğerleri kendi proaktif olarak maliyetleri yönetmek ve harcama zaman içinde nasıl ilerledikçe izlemek için harcama hakkında bildirmenize yardımcı olurlar. Oluşturduğunuz bütçe eşikleri aşıldığında yalnızca bildirimleri tetiklenir. Kaynaklarınızı hiçbiri etkilenir ve tüketiminiz durduruldu değil. Bütçe karşılaştırın ve maliyetleri analiz ederken harcama izlemek için kullanabilirsiniz.

Maliyet ve kullanım verileri genellikle 12-16 saat içinde kullanılabilir ve bütçeler her dört saatte bir bu maliyetlere göre değerlendirilir. E-posta bildirimleri normalde 12-16 saat içinde alınır.

Bütçe gelecekte bir sona erme tarihi seçtiğinizde aynı bütçe tutarını (aylık, üç aylık veya yıllık) bir süre sonunda otomatik olarak sıfırlama. Bütçelenen zaman ayrı bütçelerini oluşturmanıza gerek bunlar ile aynı bütçe miktarını sıfırlamak için para birimi tutarlar gelecek dönemlere ait farklı.

Bu öğreticideki örneklerde oluşturmak ve düzenlemek için Azure Kurumsal Anlaşma (EA) aboneliğiniz bir bütçe size yol gösterir.

Azure 'da harcamalarınızı izlemek için Azure [maliyet yönetimi videosunun nasıl bütçe oluşturulacağını](https://www.youtube.com/watch?v=ExIVG_Gr45A) izleyin.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure portalında bir bütçe oluşturun
> * Bütçe Düzenle

## <a name="prerequisites"></a>Önkoşullar

Bütçeler, çeşitli Azure hesap türleri için desteklenir. Desteklenen hesap türlerinin tam listesini görüntülemek için bkz. [maliyet yönetimi verilerini anlama](understand-cost-mgt-data.md). Bütçeleri görüntülemek için, Azure hesabınız için en azından okuma erişiminizin olması gerekir.

 Azure EA abonelikleri için, bütçeleri görüntülemek için okuma erişiminizin olması gerekir. Oluşturma ve bütçelerini yönetmek için katkıda bulunan izni olmalıdır. EA aboneliklerinde ve kaynak gruplarınız için ayrı ayrı bütçeden oluşturabilirsiniz. Ancak, EA hesapları faturalama bütçelerini oluşturulamıyor.

Aşağıdaki Azure izinleri veya kapsamları, Kullanıcı ve grup bütçeleri için abonelik başına desteklenir. Kapsamlar hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).

- Sahip – Bir abonelik için bütçe oluşturabilir, değiştirebilir veya silebilir.
- Katkıda bulunan ve maliyet Yönetimi katkıda bulunan – oluşturmak, değiştirmek veya kendi bütçelerini silin. Başkaları tarafından oluşturulan bütçelerin miktarlarını değiştirebilir.
- Okuyucu ve maliyet Yönetimi okuyucu – iznine sahip oldukları bütçelerini görüntüleyebilirsiniz.

Maliyet Yönetimi verilerine izin atama hakkında daha fazla bilgi için bkz. [maliyet Yönetimi verilerine erişim atama](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

- https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-budget-in-the-azure-portal"></a>Azure portalında bir bütçe oluşturun

Aylık, üç aylık veya yıllık bir dönem için bir Azure aboneliği bütçe oluşturabilirsiniz. Azure portal gezinti içeriğiniz, bir abonelik veya bir yönetim grubu için bir bütçe oluşturup oluşturamayacağını belirler.

Bir bütçe oluşturmak veya görüntülemek için, Azure portal istenen kapsamı açın ve menüdeki **bütçeler** ' ı seçin. Örneğin, **abonelikler**' e gidin, listeden bir abonelik seçin ve ardından menüdeki **bütçeler** ' ı seçin. Bütçeleri bir yönetim grubu gibi farklı bir kapsama geçmek için **kapsam** hap 'i kullanın. Kapsamlar hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).

Bütçe oluşturduktan sonra bunlar geçerli bunlara karşı harcamalarınızı basit bir görünümü gösterir.

**Ekle**'ye tıklayın.

![Zaten oluşturulan bütçelerin listesini gösteren örnek](./media/tutorial-acm-create-budgets/budgets01.png)

**Bütçe oluştur** penceresinde, gösterilen kapsamın doğru olduğundan emin olun. Eklemek istediğiniz filtreleri seçin. Filtreler, bir abonelikteki kaynak grupları veya sanal makineler gibi bir hizmet gibi belirli maliyetlerde bütçeler oluşturmanızı sağlar. Maliyet analizinde kullanabileceğiniz herhangi bir filtre de bir bütçeye uygulanabilir.

Kapsamınızı ve filtrelerinizi tanımladıktan sonra bir bütçe adı yazın. Ardından, aylık, üç aylık veya yıllık bir bütçe sıfırlama dönemi seçin. Bu sıfırlama süresi, bütçe tarafından çözümlenen zaman penceresini belirler. Bütçe tarafından değerlendirilen maliyet her yeni dönemin başlangıcında sıfırdan başlar. Üç aylık bir bütçe oluşturduğunuzda, bir aylık bütçe aynı şekilde çalışır. Üç aylık dönem arasında üç aylık üç aylık dönem için bütçe miktarını şekilde eşit bölünür farktır. Yıllık bir bütçe miktarı, takvim yılının 12 ay boyunca eşit olarak bölünür.

Kullandıkça öde, MSDN veya Visual Studio aboneliğiniz varsa faturanızda fatura dönemi takvim ayına hizalanmayabilir. Bu abonelik türleri ve kaynak grupları için, fatura döneminize veya takvim aylarınıza göre hizalanan bir bütçe oluşturabilirsiniz. Fatura döneminize hizalanmış bir bütçe oluşturmak için fatura **ayı**, **Fatura çeyreği**veya **Fatura yılından**bir sıfırlama dönemi seçin. Takvim aya hizalanmış bir bütçe oluşturmak için **aylık**, **üç aylık**veya **yıllık**bir sıfırlama dönemi seçin.

Ardından, bütçenin geçersiz hale geldiği ve maliyetlerinizi değerlendirmeyi durdurduğu sona erme tarihini belirleyebilirsiniz.

Bütçeye şu ana kadar seçtiğiniz alanlara bağlı olarak, bütçeniz için kullanılacak bir eşik seçmenize yardımcı olacak bir grafik gösterilir. Önerilen bütçe, gelecekteki dönemlerde tabi olabileceğiniz en yüksek tahmini maliyeti temel alır. Bütçe tutarını değiştirebilirsiniz.

![Aylık maliyet verileriyle bütçe oluşturmayı gösteren örnek ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Bütçe tutarını yapılandırdıktan sonra, bütçe uyarılarını yapılandırmak için **İleri** ' ye tıklayın. Bütçe en az bir maliyet eşiği (% bütçe) ve karşılık gelen e-posta adresi gerektirir. İsteğe bağlı olarak, en fazla beş eşikleri ve tek bir bütçe içinde beş e-posta adresi ekleyebilirsiniz. Bir bütçe eşiği karşılandığında, e-posta bildirimleri genellikle 20 saatten az bir sürede alınır. Bildirimler hakkında daha fazla bilgi için bkz. [kullanım maliyeti uyarıları](cost-mgt-alerts-monitor-usage-spending.md). Aşağıdaki örnekte, bütçenin %90 ' ına ulaşıldığında bir e-posta uyarısı oluşturulur. Bütçeler API 'SI ile bir bütçe oluşturursanız, kullanıcılara uyarı almak için roller de atayabilirsiniz. Kişilere rol atama Azure portal desteklenmez. Azure bütçeleri API 'SI hakkında daha fazla bilgi için bkz. [Bütçe API 'si](/rest/api/consumption/budgets).

![Uyarı koşullarını gösteren örnek](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Bütçe oluşturduktan sonra maliyet analizi gösterilmektedir. Bütçenize göre harcama eğilimi görüntüleme olduğunda ilk adımlarından biri için başlangıç [maliyetlerinizi analiz edin ve harcama](quick-acm-cost-analysis.md).

![Örnek bütçe ve maliyet analizi gösterilen harcama](./media/tutorial-acm-create-budgets/cost-analysis.png)

Önceki örnekte, bir abonelik için bir bütçe oluşturuldu. Ancak, bir kaynak grubu için bütçe oluşturabilirsiniz. Bir kaynak grubu için bütçe oluşturmak istiyorsanız, gitmek **maliyet Yönetimi + faturalandırma** &gt; **abonelikleri** &gt; bir aboneliği seçin > **kaynak grupları** > bir kaynak grubu seçin > **bütçelerini** > ardından **Ekle** bütçe.

## <a name="trigger-an-action-group"></a>Bir eylem grubu tetikleyin

Bir abonelik veya kaynak grubu kapsamı için bir bütçe oluşturduğunuzda ya da düzenlediğinizde, bir eylem grubu çağırmak için yapılandırabilirsiniz. Bütçe eşiğiniz karşılandığında eylem grubu çeşitli farklı eylemler gerçekleştirebilir. Eylem grupları Şu anda yalnızca abonelik ve kaynak grubu kapsamları için desteklenir. Eylem grupları hakkında daha fazla bilgi için [Azure Portal eylem grupları oluşturma ve yönetme](../azure-monitor/platform/action-groups.md)konusuna bakın. İşlem gruplarıyla bütçe tabanlı Otomasyon kullanma hakkında daha fazla bilgi için bkz. [Azure bütçeleriyle maliyetleri yönetme](../billing/billing-cost-management-budget-scenario.md).



Eylem grupları oluşturmak veya güncelleştirmek için, bir bütçe oluştururken veya düzenlenirken **eylem gruplarını yönet** ' e tıklayın.

![Eylem gruplarını yönetme göstermek için bir bütçe oluşturma örneği](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Sonra, **eylem grubu Ekle** ' ye tıklayın ve eylem grubunu oluşturun.


![Eylem Ekle Grup kutusunun görüntüsü](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Eylem grubu oluşturulduktan sonra, bütçenize geri dönmek için kutuyu kapatın.

Tek bir eşik karşılandığında, bütçenizi eylem grubunuzu kullanacak şekilde yapılandırın. En fazla beş farklı eşik desteklenir.

![Uyarı koşulunun eylem grubu seçimini gösteren örnek](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Aşağıdaki örnek, %50, 75% ve %100 olarak ayarlanan bütçe eşiklerini gösterir. Her biri belirlenen eylem grubu içinde belirtilen eylemleri tetiklemek üzere yapılandırılır.

![Çeşitli eylem gruplarıyla ve eylem türüyle yapılandırılan uyarı koşullarını gösteren örnek](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Eylem gruplarıyla bütçe tümleştirmesi yalnızca ortak uyarı şeması devre dışı olan eylem grupları için geçerlidir. Şemayı devre dışı bırakma hakkında daha fazla bilgi için, bkz [. nasıl yaparım? ortak uyarı şemasını etkinleştirmek istiyor musunuz?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure portalında bir bütçe oluşturun
> * Bütçe Düzenle

Yinelenen bir dışarı aktarma, maliyet Yönetimi verilerine için oluşturmak için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Oluşturma ve dışarı aktarılan verileri yönetme](tutorial-export-acm-data.md)
