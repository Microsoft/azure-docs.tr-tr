---
title: Azure Arc etkin sunucularına genel bakış
description: Azure 'un dışında barındırılan sunucuları Azure kaynağı gibi yönetmek için Azure Arc etkin sunucularını nasıl kullanacağınızı öğrenin.
keywords: Azure Otomasyonu, DSC, PowerShell, istenen durum yapılandırması, güncelleştirme yönetimi, değişiklik izleme, envanter, runbook 'lar, Python, grafik, karma
ms.date: 04/21/2021
ms.topic: overview
ms.openlocfilehash: 324f6cc29bd9e4eca1a20413032c213c2618a11e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832005"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Azure Arc özellikli sunucular nedir?

Azure Arc etkin sunucuları, Windows ve Linux fiziksel sunucularınızı ve Azure *dışında* barındırılan sanal makinelerinizi şirket ağınızda veya diğer bulut sağlayıcınızdan yönetmenizi sağlar. Bu yönetim deneyimi, yerel Azure sanal makinelerini nasıl yöneteceğiniz ile tutarlı olacak şekilde tasarlanmıştır. Bir karma makine Azure 'a bağlıyken, bağlı bir makine olur ve Azure 'da kaynak olarak kabul edilir. Her bağlı makinenin bir kaynak KIMLIĞI vardır, bir kaynak grubuna dahildir ve Azure Ilkesi gibi standart Azure yapılarından ve Etiketler uygulayarak faydalanır. Bir müşterinin Şirket içi altyapısını yöneten hizmet sağlayıcıları, Azure Arc ile [Azure Hithouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) ' ı kullanarak, yerel Azure kaynaklarıyla, aynı anda birden çok müşteri ortamında olduğu gibi karma makinelerini yönetebilir.

Bu deneyimi Azure dışında barındırılan karma makinelerinizle birlikte sunmak için Azure 'a bağlanmayı planladığınız her makinede Azure bağlı makine aracısının yüklü olması gerekir. Bu aracı başka bir işlevsellik sunmaz ve Azure [Log Analytics aracısının](../../azure-monitor/agents/log-analytics-agent.md)yerini almaz. Makinede çalışan işletim sistemi ve iş yüklerini önceden izlemek, Otomasyon Runbook 'larını veya Güncelleştirme Yönetimi gibi çözümleri kullanarak yönetmek ya da [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)gibi diğer Azure hizmetlerini kullanmak istediğinizde Windows ve Linux için Log Analytics Aracısı gerekir.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Makinenizi Azure Arc etkin sunucularına bağladığınızda, aşağıdaki yapılandırma yönetimi ve izleme görevlerini gerçekleştirme olanağı sağlar:

- Azure sanal makineleri için ilke atamayla aynı deneyimi kullanarak [Azure ilke Konuk yapılandırması](../../governance/policy/concepts/guest-configuration.md) atayın. Günümüzde, çoğu konuk yapılandırma ilkesi yapılandırmaları uygulamaz, yalnızca makinenin içindeki ayarları denetlarlar. Azure Ilke Konuk yapılandırma ilkelerini Arc etkin sunucularla kullanma maliyetini anlamak için bkz. Azure Ilkesi [fiyatlandırma Kılavuzu](https://azure.microsoft.com/pricing/details/azure-policy/).

- Azure Otomasyonu [değişiklik izleme ve envanter](../../automation/change-tracking/overview.md) ve [Azure Güvenlik Merkezi dosya bütünlüğü izlemeyi](../../security-center/security-center-file-integrity-monitoring.md)kullanarak, [sunucular için Azure Defender](../../security-center/defender-for-servers-introduction.md)ile etkinleştirilen sunucular için yüklü yazılımlar, Microsoft Hizmetleri, Windows kayıt defteri ve dosyalar ve Linux Daemon 'ları hakkındaki yapılandırma değişikliklerini bildirin.

- Bağlı makine konuk işletim sistemi performansınızı izleyin ve uygulama bileşenlerini, uygulamanın [VM öngörülerini](../../azure-monitor/vm/vminsights-overview.md)kullanarak iletişim kurduğu diğer kaynaklarla işlem ve bağımlılıklarını izlemeye yönelik şekilde bulun.

- Azure Otomasyonu [Durum Yapılandırması](../../automation/automation-dsc-overview.md) ve azure izleyici Log Analytics çalışma alanı gibi diğer Azure hizmetlerini kullanarak dağıtımı, Azure olmayan Windows veya Linux makineniz Için desteklenen [Azure VM uzantılarını](manage-vm-extensions.md) kullanarak kolaylaştırın. Bu, dağıtım sonrası yapılandırma veya özel Betik uzantısı kullanılarak yazılım yükleme işlemlerini içerir.

- Windows ve Linux sunucularınız için işletim sistemi güncelleştirmelerini yönetmek üzere Azure Otomasyonu 'nda [güncelleştirme yönetimi](../../automation/update-management/overview.md) kullanın

    > [!NOTE]
    > Şu anda, bir yay etkin sunucusundan Güncelleştirme Yönetimi doğrudan etkinleştirme desteklenmez. Gereksinimleri anlamak ve sunucunuz için nasıl etkinleştireceğinizi anlamak için bkz. [Otomasyon hesabınızdan güncelleştirme yönetimi etkinleştirme](../../automation/update-management/enable-from-automation-account.md) .

- Azure [Güvenlik Merkezi](../../security-center/security-center-introduction.md)'ni kullanarak tehdit algılama için Azure dışı sunucularınızı ve olası güvenlik tehditlerini proaktif bir şekilde izlemeyi dahil edin.

Karma makineden bir Log Analytics çalışma alanında toplanan ve depolanan günlük verileri artık makineye özgü olan bir kaynak KIMLIĞI gibi özellikleri içerir. Bu, [kaynak bağlamı](../../azure-monitor/logs/design-logs-deployment.md#access-mode) günlük erişimini desteklemek için kullanılabilir.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Desteklenen bölgeler

Azure Arc etkin sunucularıyla desteklenen bölgelerin kesin bir listesi için bkz. [bölgeye göre Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) sayfası.

Çoğu durumda, yükleme betiğini oluştururken seçtiğiniz konum coğrafi olarak makinenizin konumuna en yakın olan Azure bölgesi olmalıdır. Bekleyen veriler, belirttiğiniz bölgeyi içeren Azure Coğrafya içinde depolanır ve bu da veri yerleşimi gereksinimleriniz varsa bölge seçiminizi de etkileyebilir. Makinenizin bağlandığı Azure bölgesi bir kesinti nedeniyle etkileniyorsa, bağlı makine etkilenmez, ancak Azure 'u kullanan yönetim işlemleri tamamlanmayabilir. Bölgesel bir kesinti varsa ve coğrafi olarak yedekli bir hizmeti destekleyen birden çok konumunuz varsa, her konumdaki makineleri farklı bir Azure bölgesine bağlamak en iyisidir.

Bağlı makineyle ilgili aşağıdaki meta veri bilgileri, Azure Arc makine kaynağının yapılandırıldığı bölgede toplanır ve depolanır:

- İşletim sistemi adı ve sürümü
- Bilgisayar adı
- Bilgisayar tam etki alanı adı (FQDN)
- Bağlı makine Aracısı sürümü

Örneğin, makine Doğu ABD bölgesinde Azure Arc ile kayıtlıysa, bu veriler ABD bölgesinde saklanır.

### <a name="supported-environments"></a>Desteklenen ortamlar

Yay özellikli sunucular, Azure *dışında* barındırılan fiziksel sunucuların ve sanal makinelerin yönetimini destekler. VM 'Leri barındıran karma bulut ortamlarının desteklendiği belirli Ayrıntılar için bkz. [bağlı makine Aracısı önkoşulları](agent-overview.md#supported-environments).

> [!NOTE]
> Yay özellikli sunucular, Azure 'da çalışan sanal makinelerin yönetimini etkinleştirmek için tasarlanmamıştır veya desteklenmez.

### <a name="agent-status"></a>Aracı durumu

Bağlı makine Aracısı, her 5 dakikada bir hizmete düzenli bir sinyal iletisi gönderir. Hizmet, bu sinyal mesajlarını bir makineden almayı durduruyor, bu makine çevrimdışı olarak değerlendirilir ve portalda 15 ila 30 dakika içinde otomatik olarak, durum **bağlantısı kesilecek** şekilde değiştirilir. Bağlı makine aracısından sonraki bir sinyal iletisi alındıktan sonra, durumu otomatik olarak **bağlı** olarak değiştirilir.

## <a name="next-steps"></a>Sonraki adımlar

Birden çok karma makinede yay etkin sunucuları değerlendirmeden veya etkinleştirmeden önce, gereksinimleri anlamak için [bağlı makine aracısına genel bakış](agent-overview.md) ' ı ve aracı hakkındaki teknik ayrıntıları ve dağıtım yöntemlerini gözden geçirin.