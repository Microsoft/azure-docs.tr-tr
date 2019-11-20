---
title: Azure sanal makineleri için genişletilmiş ölçümler ekleme | Microsoft Docs
description: Bu makale, Azure VM 'leriniz için genişletilmiş tanılama ölçümlerini etkinleştirmenize ve yapılandırmanıza yardımcı olur.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
ms.custom: seodec18
ms.openlocfilehash: e1d0beb6ced0d582166d556c1ae2fc17b375dddf
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695357"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Azure sanal makineleri için genişletilmiş ölçümler ekleme

Cloudyn, kaynakları hakkındaki ayrıntılı bilgileri göstermek için Azure sanal makinelerinizdeki Azure ölçüm verilerini kullanır. Performans sayaçları olarak da bilinen ölçüm verileri, rapor oluşturmak için Cloudyn tarafından kullanılır. Ancak, Cloudyn tüm Azure ölçüm verilerini Konuk VM 'lerden otomatik olarak toplamaz; ölçüm toplamayı etkinleştirmeniz gerekir. Bu makale, Azure VM 'leriniz için ek tanılama ölçümlerini etkinleştirmenize ve yapılandırmanıza yardımcı olur.

Ölçüm toplamayı etkinleştirdikten sonra şunları yapabilirsiniz:

- Sanal makinelerinizin bellek, disk ve CPU sınırlarına ne zaman ulaşmakta olduğunu öğrenin.
- Kullanım eğilimlerini ve anormallerini algılayın.
- Kullanıma göre boyutlandırarak maliyetlerinizi kontrol edin.
- Cloudyn 'in maliyet açısından etkili boyut iyileştirme önerilerini alın.

Örneğin, Azure sanal makinelerinizin CPU% ve bellek boyutunu izlemek isteyebilirsiniz. Azure VM ölçümleri, _Yüzde CPU_ ve _\bellek @ No__t-2 kaydedilmiş bayt kullanımı_' na karşılık gelir.

> [!NOTE]
> Genişletilmiş ölçüm verileri toplama yalnızca Azure Konuk düzeyinde izleme ile desteklenir. Cloudyn [Log Analytics aracısıyla](../azure-monitor/platform/agents-overview.md)uyumlu değildir. 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Genişletilmiş ölçümlerin etkinleştirilip etkinleştirilmeyeceğini belirleme

1. Azure portal https://portal.azure.com ' da oturum açın.
2. **Sanal makineler**altında bir VM seçip **izleme**altında **ölçümler**' i seçin. Kullanılabilir ölçümlerin bir listesi gösterilir.
3. Bazı ölçümler ' i ve bir grafik için verileri görüntüler.  
    ![Örnek ölçüm – ana bilgisayar yüzdesi CPU](./media/azure-vm-extended-metrics/metric01.png)

Yukarıdaki örnekte, ana bilgisayarınız için sınırlı bir standart ölçüm kümesi vardır, ancak bellek ölçümleri değildir. Bellek ölçümleri, genişletilmiş ölçümlerin bir parçasıdır. Bu durumda, sanal makine için genişletilmiş ölçümler etkinleştirilmez. Genişletilmiş ölçümleri etkinleştirmek için bazı ek adımlar gerçekleştirmeniz gerekir. Aşağıdaki bilgiler, bunları etkinleştirme sırasında size rehberlik eder.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Azure portal genişletilmiş ölçümleri etkinleştirin

Standart ölçümler ana bilgisayar ölçümleridir. _CPU Ölçümü yüzdesi_ bir örnektir. Konuk VM 'Ler için de temel ölçümler vardır ve bunlar da genişletilmiş ölçümler olarak adlandırılırlar. Genişletilmiş ölçümlere örnek olarak, _Kullanımdaki \Memory @ no__t-1 kaydedilmiş bayt_ ve _\Memory\available Byte_verilebilir.

Genişletilmiş ölçümlerin etkinleştirilmesi basittir. Her VM için, Konuk düzeyinde izlemeyi etkinleştirin. Konuk düzeyinde izlemeyi etkinleştirdiğinizde, Azure tanılama Aracısı VM 'ye yüklenir. Varsayılan olarak, temel bir genişletilmiş ölçümler kümesi eklenir. Aşağıdaki işlem, klasik ve normal VM 'Ler için aynıdır ve Windows ve Linux VM 'Leri için aynıdır.

Hem Azure hem de Linux konuk düzeyinde izlemenin bir depolama hesabı gerektirdiğini aklınızda bulundurun. Konuk düzeyinde izlemeyi etkinleştirdiğinizde, var olan bir depolama hesabını seçmezseniz, bir tane sizin için oluşturulur.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Mevcut VM 'lerde Konuk düzeyinde izlemeyi etkinleştir

1. **Sanal makinelerde**VM 'nizin listesini görüntüleyin ve ardından bir VM seçin.
2. **İzleme**altında **Tanılama ayarları**' nı seçin.
3. Tanılama Ayarları sayfasında, **Konuk düzeyinde Izlemeyi etkinleştir**' e tıklayın.  
    ![ Genel Bakış sayfasında Konuk düzeyinde izlemeyi etkinleştir @ no__t-1
4. Birkaç dakika sonra, VM 'ye Azure tanılama Aracısı yüklenir. Temel bir ölçüm kümesi eklendi. Sayfayı yenileyin. Eklenen performans sayaçları Genel Bakış sekmesinde görünür.
5. Izleme altında **ölçümler**' i seçin.
6. **Ölçüm ad alanı**altındaki ölçümler grafiğinde **Konuk (klasik)** seçeneğini belirleyin.
7. Ölçüm listesinde, Konuk VM için tüm kullanılabilir performans sayaçlarını görüntüleyebilirsiniz.  
    ![örnek genişletilmiş ölçümler listesi](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Yeni VM 'lerde Konuk düzeyinde izlemeyi etkinleştir

Yeni VM 'Ler oluşturduğunuzda, Yönetim sekmesinde **Işletim sistemi Konuk tanılama**için **Açık** ' ı seçin.

![Konuk işletim sistemi tanılamayı açık olarak ayarla](./media/azure-vm-extended-metrics/new-enable-diag.png)

Azure sanal makineleri için genişletilmiş ölçümleri etkinleştirme hakkında daha fazla bilgi için bkz. [Azure Linux aracısını anlama ve kullanma](../virtual-machines/extensions/agent-linux.md) ve [Azure sanal makine aracısına genel bakış](../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Kaynak Yöneticisi kimlik bilgileri

Genişletilmiş ölçümleri etkinleştirdikten sonra Cloudyn 'ın [Kaynak Yöneticisi kimlik bilgilerine](activate-subs-accounts.md)erişimi olduğundan emin olun. Cloudyn 'in sanal makinelerinize ilişkin performans verilerini toplaması ve görüntülemesi için kimlik bilgileriniz gereklidir. Maliyet iyileştirme önerilerini oluşturmak için de kullanılır. Cloudyn, bir örnekten aşağı boyutlandırma önerisi için bir aday olup olmadığını tespit etmek üzere bir örnekten en az üç günlük performans verisi gerektirir.

## <a name="enable-vm-metrics-with-a-script"></a>Bir komut dosyası ile VM ölçümlerini etkinleştirme

VM ölçümlerini Azure PowerShell betiklerle etkinleştirebilirsiniz. Üzerinde ölçümleri etkinleştirmek istediğiniz çok sayıda sanal makine varsa, işlemi otomatikleştirmek için bir komut dosyası kullanabilirsiniz. [Azure 'Da tanılama 'Yı etkinleştirmek](https://github.com/Cloudyn/azure-enable-diagnostics)için örnek betikler GitHub 'da bulunmaktadır.

## <a name="view-azure-performance-metrics"></a>Azure performans ölçümlerini görüntüleme

Cloudyn portalındaki Azure örneklerinizde performans ölçümlerini görüntülemek için **varlıklar** > **işlem** > **örnek Gezgini**' ne gidin. VM örnekleri listesinde bir örneği genişletin ve sonra ayrıntıları görüntülemek için bir kaynağı genişletin.

![örnek Gezgininde gösterilen örnek bilgiler](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Sonraki adımlar

- Hesaplarınız için Azure Resource Manager API erişimi henüz etkinleştirilmemişse, [Azure aboneliklerini ve hesaplarını etkinleştirme](activate-subs-accounts.md)adımına geçin.
