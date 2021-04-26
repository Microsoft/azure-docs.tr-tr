---
title: VM öngörüleri Konuk sistem durumu uyarıları (Önizleme)
description: VM öngörüleri Konuk sistem durumu tarafından, bunların nasıl etkinleştirileceği ve bildirimlerin nasıl yapılandırılacağı dahil olmak üzere oluşturulan uyarıları açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 9c4b15fa2a8be32aa7397ae425f28f1deaac6b71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024617"
---
# <a name="vm-insights-guest-health-alerts-preview"></a>VM öngörüleri Konuk sistem durumu uyarıları (Önizleme)
VM öngörüleri Konuk durumu, bir sanal makinenin durumunu, düzenli aralıklarla örneklendiği bir dizi performans ölçümlerine göre tanımlanan şekilde görüntülemenize olanak sağlar. Bir sanal makine veya izleyici uygun olmayan bir durumda değiştiğinde bir uyarı oluşturulabilir. Bu uyarıları [Azure izleyici 'de uyarı kuralları tarafından oluşturulanlar](../alerts/alerts-overview.md) ile görüntüleyebilir ve yönetebilir ve yeni bir uyarı oluşturulduğunda, önceden bildirim gönderilmesini seçebilirsiniz.

## <a name="configure-alerts"></a>Uyarı yapılandırma
Bu özellik önizlemedeyken VM öngörüleri Konuk durumu için açık bir uyarı kuralı oluşturamazsınız. Varsayılan olarak, her bir sanal makine için uyarılar oluşturulur, ancak her izleyici için değildir.  Yani, bir izleyici sanal makinenin geçerli durumunu etkilemeyen bir duruma değişirse, sanal makine durumu değişmediği için hiçbir uyarı oluşturulmaz. 

Belirli bir sanal makine veya bir sanal makinedeki belirli bir izleyici için uyarıları, Azure portal sanal makinenin yapılandırmasındaki **uyarı durumu** ayarından devre dışı bırakabilirsiniz. Azure portal izleyicileri yapılandırma hakkında ayrıntılı bilgi için bkz. [VM öngörüleri Konuk sistem durumu 'nda Izlemeyi yapılandırma (Önizleme)](vminsights-health-configure.md) . Bir sanal makine kümesi genelinde izleyicileri yapılandırma hakkında ayrıntılı bilgi için bkz. [veri toplama kuralları (Önizleme) kullanarak VM öngörüleri Konuk durumunda Izlemeyi yapılandırma](vminsights-health-configure-dcr.md) .

## <a name="alert-severity"></a>Uyarı önem derecesi
Konuk sistem durumu tarafından oluşturulan uyarının önem derecesi doğrudan sanal makinenin önem derecesine veya uyarıyı tetikleyen monitöre eşlenir.

| İzleme durumu | Uyarı önem derecesi |
|:---|:---|
| Kritik | Sev1 |
| Uyarı  | Sev2 |
| Sağlam  | Sev4 |

## <a name="alert-lifecycle"></a>Uyarı yaşam döngüsü
Her sanal makine için bir **Uyarı** veya **kritik** duruma her değişiklik yaptığı zaman bir [Azure uyarısı](../alerts/alerts-overview.md) oluşturulacaktır. **Uyarıları** **Azure izleyici** menüsünde veya sanal makinenin menüsündeki Azure Portal görüntüleyin.

Sanal makine durumu değiştiğinde bir uyarı zaten **başlatılmış durumdaysa,** ikinci bir uyarı oluşturulmaz, ancak aynı uyarının önem derecesi, sanal makinenin durumuyla eşleşecek şekilde değiştirilir. Örneğin, bir **Uyarı** uyarısı zaten **tetiklenme** durumunda sanal makine **kritik** duruma değişirse, bu uyarının önem derecesi **Sev1** olarak değiştirilir. Bir **Sev1** uyarısı zaten **tetiklendiğinde** sanal makine bir **Uyarı** durumuna değişirse, bu uyarının önem derecesi **Sev2** olarak değiştirilir. Sanal makine **sağlıklı** bir duruma geri taşınırsa, uyarı **Sev4** olarak değiştirilen önem derecesine sahip olacak şekilde çözümlenir.

## <a name="viewing-alerts"></a>Uyarıları görüntüleme
VM öngörüleri Konuk durumu tarafından oluşturulan uyarıları Azure portal diğer [uyarılarla](../alerts/alerts-overview.md#alerts-experience)görüntüleyin. Tüm izlenen kaynakların uyarılarını görüntülemek için **Azure izleyici** menüsünden **Uyarılar** ' ı seçebilir veya yalnızca o sanal makine için uyarıları görüntülemek üzere bir sanal makinenin menüsündeki **uyarıları** seçebilirsiniz.

## <a name="alert-properties"></a>Uyarı Özellikleri

### <a name="properties-in-the-azure-portal"></a>Azure portal Özellikler
Azure portal görüntülerken uyarının özellikleri, aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:---|:---|
| Uyarı oluşturulmadan önce durumu izle | Bu uyarı ilk kez tetiklenmadan önce izleyici veya sanal makine durumu. |
| Uyarı oluşturulduğunda durumu izle | Uyarı ilk kez tetiklendiğinde izleyici veya sanal makine durumu. Bu, Uyarının tetiklenmesine neden olan durumdur. |
| Uyarı oluşturulduğunda durum geçişi hakkında daha fazla bilgi için | Tam durum geçişini görebileceğiniz VM Health sayfasına bağlantı. Bu durum geçişi izleyici ilk olarak **sağlıklı** durumdan sağlıklı olmayan bir duruma geldiğinde örneği temsil eder. |

Örneğin, bir izleyici **sağlıklı** **olarak t0 zamanında çalışır** ve **Sev1** ile yeni bir uyarı tetiklenir. Daha sonra T1 zamanında **kritik** ve **Uyarı** önem derecesi **Sev2** olarak güncelleştirilir. T2 zaman zaman **sağlıklı** hale gelir ve uyarı çözümlenir.

Bu tüm sıra için uyarı özellikleri bu değerlere sahip olacaktır.

- Uyarı oluşturulmadan önce durumu izle: sağlıklı
- Uyarı oluşturulduğunda durumu izle: kritik
- Uyarı oluşturulduğunda durum geçişi hakkında daha fazla bilgi için: t0 zamanında durum geçişine yönelik gezinti bağlantısı.


### <a name="properties-in-notifications"></a>Bildirimlerde Özellikler
Bildirimlere dahil olan uyarının özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:---|:---|
| Önceki izleme durumu | Durumu değiştirmeden önce izleyici veya sanal makine durumu. Uyarı önem derecesi güncelleştirmesi bu bildirimi tetiklerse, bu özellik önem güncelleştirmesinden hemen önceki durumu temsil eder. |
| Geçerli izleyici durumu | Durumu değiştirdiği izleyici veya sanal makine durumu. Uyarı önem derecesi güncelleştirmesi bu bildirimi tetiklerse, bu özellik yeni durumu temsil eder. |
| Bu durum geçişi hakkında daha fazla bilgi için | Tam durum geçişini görebileceğiniz VM Health sayfasına bağlantı. Bu durum geçişi, izleyicinin bu bildirimi tetikleyen durumu değiştiği zaman örneği temsil eder. |

Yukarıdaki örneği kullanarak, bildirimler her seferinde aşağıdaki özelliklere sahip olur.

Bildirim zamanında alındı T0
- Önceki izleyici durumu: sağlıklı
- Geçerli izleyici durumu: kritik
- Bu durum geçişi hakkında daha fazla bilgi için: t0 zamanında gerçekleşen durum geçişine yönelik gezinti bağlantısı.

Bildirim zamanında alındı
- Önceki izleyici durumu: kritik
- Geçerli izleyici durumu: uyarı
- Bu durum geçişi hakkında daha fazla bilgi için: T1 zamanında durum geçişine yönelik gezinti bağlantısı.

Bildirim zamanında alındı T2
- Önceki izleyici durumu: uyarı
- Geçerli izleyici durumu: sağlıklı
- Bu durum geçişi hakkında daha fazla bilgi için: T2 saatinde durum geçişine yönelik gezinti bağlantısı.

## <a name="configure-notifications"></a>Bildirimleri yapılandırma
Konuk sistem durumu tarafından tetiklenen bir uyarı için önlem almak üzere, bir SMS iletisi gönderme veya bir mantıksal uygulama başlatma gibi farklı işlemleri tanımlamak için bir [eylem grubu](../alerts/action-groups.md) oluşturun. Sonra izleyicilerin ve sanal makinelerin kapsamını belirten bir [eylem kuralı](../alerts/alerts-action-rules.md) oluşturun ve bu eylem grubunu kullanır.

Azure portal **izleyici** menüsünde **Uyarılar**' ı seçin.  **Işlemleri Yönet** ' i ve ardından **Eylem kuralları ' nı (Önizleme)** seçin. 

![Yeni eylem kuralı](media/vminsights-health-alerts/action-rule-new.png)

Yeni bir kural oluşturmak için **yeni eylem kuralı** ' na tıklayın. Kapsam ' ın yanındaki **Seç** ' e tıklayın ve bir abonelik, kaynak grubu ya da bir veya daha fazla belirli sanal makine seçin. Bildirim yalnızca kapsam içinde yer alan sanal makineler için tetiklenecektir.

![Eylem kuralı kapsamı](media/vminsights-health-alerts/action-rule-scope.png)

**Filtrenin** yanına **Ekle** ' ye tıklayın. **İzleme hizmeti 'nin VM öngörüleri-sistem durumu**'nda olduğu bir filtre oluşturun. Bildirimi tetiklemesi gereken belirli uyarıları belirtmek için başka filtreler ekleyin. Örneğin, belirli bir önem derecesine sahip olan tüm izleyicilerin uyarılarını eşleştirmek için **önem derecesini** kullanabilirsiniz.

![Eylem kuralı filtresi](media/vminsights-health-alerts/action-rule-filter.png)

**Bu kapsamda tanımla**' da **eylem grubu** ' nu seçin ve ardından izleyiciyle ilişkilendirilecek eylem grubunu seçin. Kurala bir ad verin ve kaydedilecek kaynak grubunu seçin. Kuralı oluşturmak için **Oluştur** ' a tıklayın.

![Eylem kuralı](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Sonraki adımlar

- [VM öngörüleri ve aracı ekleme aracılarında Konuk sistem durumunu etkinleştirin.](vminsights-health-enable.md)
- [Azure portal kullanarak izleyicileri yapılandırın.](vminsights-health-configure.md)
- [Veri toplama kurallarını kullanarak izleyicileri yapılandırın.](vminsights-health-configure-dcr.md)