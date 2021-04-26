---
title: VM 'Leri Başlat/Durdur v2 (Önizleme) genel bakış
description: Bu makalede, bir zamanlamaya göre Azure Resource Manager ve klasik VM 'leri başlatan veya durduran VM 'Leri Başlat/Durdur (Önizleme) özelliğinin iki sürümü açıklanmaktadır.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: 44bfbaa8b18ebeab3b74bc696a16fc4cfb6c08ec
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220943"
---
# <a name="startstop-vms-v2-preview-overview"></a>VM 'Leri Başlat/Durdur v2 (Önizleme) genel bakış

VM 'Leri Başlat/Durdur v2 (Önizleme) özelliği, Azure sanal makinelerini (VM 'Ler) birden çok abonelik üzerinden başlatır veya durdurur. Kullanıcı tanımlı zamanlamalarda Azure VM 'Leri başlatır veya sonlandırır, [azure Application Insights](../../azure-monitor/app/app-insights-overview.md)aracılığıyla öngörüler sağlar ve [eylem gruplarını](../../azure-monitor/alerts/action-groups.md)kullanarak isteğe bağlı bildirimler gönderir. Bu özellik çoğu senaryo için hem Azure Resource Manager VM 'Leri hem de klasik VM 'Leri yönetebilir.

Başlat/Durdur VM v2 'nin (Önizleme) bu yeni sürümü, VM maliyetlerini iyileştirmek isteyen müşteriler için merkezileşmemiş düşük maliyetli bir Otomasyon seçeneği sağlar. Azure Otomasyonu ile sunulan [özgün sürümle](../../automation/automation-solution-vm-management.md) aynı işlevselliğe sahiptir, ancak Azure 'daki yeni teknolojiden faydalanmak için tasarlanmıştır.

## <a name="overview"></a>Genel Bakış

VM 'Leri Başlat/Durdur v2 (Önizleme) yeniden tasarlanmıştır ve [önceki sürümün](../../automation/automation-solution-vm-management.md)gerektirdiği şekilde Azure Otomasyonu veya Azure izleyici günlüklerine bağlı değildir. Bu sürüm, sanal makine başlangıcını işlemek ve yürütmeyi durdurmak için [Azure işlevlerini](../../azure-functions/functions-overview.md) kullanır.

Bu Azure Işlevleri uygulaması için Azure Active Directory (Azure AD) ' de yönetilen bir kimlik oluşturulur ve mantıksal uygulamalar ve Azure VM 'Leri gibi diğer Azure AD korumalı kaynaklara kolayca erişmek için VM 'Leri Başlat/Durdur v2 (Önizleme) sağlar. Azure AD 'de Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md).

Aşağıdaki tabloda gösterildiği gibi, özelliğine dahil edilen zamanlama ve sıra senaryolarını desteklemek için bir HTTP tetikleyici uç noktası işlevi oluşturulur.

|Name |Tetikleyici |Description |
|-----|--------|------------|
|AlertAvailabilityTest |Zamanlayıcı |Bu işlev, test **sanal makinesinin** her zaman kullanılabilir olduğundan emin olmak için kullanılabilirlik testini gerçekleştirir.|
|Oto durdur |HTTP |Bu işlev, Logic App 'ten çağrılan giriş noktası işlevi olan, **oto durdurma** senaryosunu destekler.|
|AutoStopAvailabilityTest |Zamanlayıcı |Bu işlev, her zaman birincil işlev, **oto** 'nin kullanılabilir olduğundan emin olmak için kullanılabilirlik testini gerçekleştirir.|
|Sanal makineyi oto |HTTP |Bu işlev, uyarı koşulu true olduğunda VM uyarısı tarafından otomatik olarak tetiklenir.|
|Createoto Stopalertexecutor |Kuyruk |Bu işlev, sanal makinede uyarı oluşturmak için, **oto durdur** işlevinden yük bilgilerini alır.|
|Zamanlanan |HTTP |Bu işlev hem zamanlanmış hem de sıralı senaryoya yöneliktir (yük şemasına göre farklılaştırılan). Bu, Logic App 'ten çağrılan ve VM başlatma veya durdurma işlemini işleme yükünü alan giriş noktası işlevidir. |
|Scheduledadvailabilitytest |Zamanlayıcı |Bu işlev, **zamanlanan** birincil işlevin her zaman kullanılabilir olduğundan emin olmak için kullanılabilirlik testini gerçekleştirir.|
|VirtualMachineRequestExecutor |Kuyruk |Bu işlev, VM 'de gerçek başlatma ve durdurma işlemini gerçekleştirir.|
|VirtualMachineRequestOrchestrator |Kuyruk |Bu işlev, **zamanlanan** işlevden yük bilgilerini alır ve VM başlatma ve durdurma isteklerini düzenler.|

Örneğin, **Zamanlanmış** http tetikleyici işlevi zamanlama ve sıra senaryolarını işlemek için kullanılır. Benzer şekilde **, otomatik** durdurma senaryosunu aynı şekilde otomatik durdurma http tetikleme işlevi işler.

Bu özelliği desteklemek için kuyruk tabanlı tetikleyici işlevleri gereklidir. Tüm Zamanlayıcı tabanlı tetikleyiciler, kullanılabilirlik testini gerçekleştirmek ve sistemin sistem durumunu izlemek için kullanılır.

 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) , bir JSON yükü kullanarak IşLEVI çağırarak VM 'nin başlatma ve durdurma zamanlamalarını yapılandırmak ve yönetmek için kullanılır. Varsayılan olarak, ilk dağıtım sırasında, aşağıdaki senaryolar için toplam beş Logic Apps oluşturur:

- Zamanlanan-başlatma ve durdurma eylemleri, Azure Resource Manager ve klasik VM 'Lerde sizin belirttiğiniz bir zamanlamaya göre yapılır. **ststv2_vms_Scheduled_start** ve **ststv2_vms_Scheduled_stop** zamanlanan başlatma ve durdurma yapılandırma.

- Sıralı-başlatma ve durdurma eylemleri, önceden tanımlanmış sıralama etiketlerine sahip VM 'Leri hedefleyen bir zamanlamaya göre yapılır. Yalnızca iki adlandırılmış etiket desteklenir- **sequencestart** ve **sequencestop**. sıralı başlatma ve durdurma **ststv2_vms_Sequenced_start** ve **ststv2_vms_Sequenced_stop** yapılandırın.

    > [!NOTE]
    > Bu senaryo yalnızca Azure Resource Manager VM 'Leri destekler.

- Oto durdur-bu işlevsellik yalnızca, CPU kullanımına bağlı olarak hem Azure Resource Manager hem de klasik VM 'Lerde bir durdurma eylemi gerçekleştirmek için kullanılır. Ayrıca, VM 'lerde uyarı oluşturan ve koşula bağlı olarak, uyarı tetiklenecek eylemi gerçekleştirecek şekilde zamanlanan *bir işlem yapılabilir* **ststv2_vms_AutoStop** otomatik durdurma işlevini yapılandırır.

Her Başlat/Durdur eylemi bir veya daha fazla abonelik, kaynak grubu veya VM 'lerin atanmasını destekler.

Işlevleri için gerekli olan bir Azure depolama hesabı, iki amaçla da Başlat/Durdur VM v2 (Önizleme) tarafından da kullanılır:

   - , Yürütme işlemi meta verilerini depolamak için Azure Tablo depolamayı kullanır (yani VM 'yi Başlat/Durdur eylemi).

   - Azure Işlevleri kuyruk tabanlı Tetikleyicileri desteklemek için Azure kuyruk depolamayı kullanır.

Uygulama yürütmesindeki izleme günlükleri olan tüm telemetri verileri, bağlı Application Insights örneğinize gönderilir. Application Insights ' de depolanan telemetri verilerini, paylaşılan bir [Azure panosunda](../../azure-portal/azure-portal-dashboards.md)sunulan önceden tanımlanmış bir görselleştirmeler kümesinden görüntüleyebilirsiniz.

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="VM paylaşılan durum panosunu Başlat/Durdur":::

Ayrıca, VM 'lerde gerçekleştirilen eylemlerin bir sonucu olarak e-posta bildirimleri de gönderilir.

## <a name="new-releases"></a>Yeni yayınlar

Yeni bir Başlat/Durdur VM v2 (Önizleme) sürümü yayınlanmışsa, örneğiniz el ile yeniden dağıtmaya gerek kalmadan otomatik olarak güncelleştirilir.

## <a name="supported-scoping-options"></a>Desteklenen kapsam seçenekleri

### <a name="subscription"></a>Abonelik

Abonelik kapsamı, aboneliğin tamamında tüm VM 'lerde Başlat ve Durdur eylemini gerçekleştirmeniz gerektiğinde kullanılabilir ve gerekirse birden çok abonelik seçebilirsiniz.  

Ayrıca, dışlanacak VM 'lerin bir listesini belirtebilir ve bunları eylemden yok sayacaktır. Aynı anda yok sayılacak tüm adları belirtmek için joker karakterler de kullanabilirsiniz.

### <a name="resource-group"></a>Kaynak grubu

Bir kaynak grubunun kapsamı, bir veya daha fazla kaynak grubu adı belirterek ve bir veya daha fazla abonelik üzerinde, tüm VM 'lerde Başlat ve Durdur eylemini gerçekleştirmeniz gerektiğinde kullanılabilir.

Ayrıca, dışlanacak VM 'lerin bir listesini belirtebilir ve bunları eylemden yok sayacaktır. Aynı anda yok sayılacak tüm adları belirtmek için joker karakterler de kullanabilirsiniz.

### <a name="vmlist"></a>VMList

VM 'lerin bir listesini belirtmek, belirli bir sanal makine kümesinde ve birden çok aboneliğe yönelik başlatma ve durdurma eylemini gerçekleştirmeniz gerektiğinde kullanılabilir. Bu seçenek, dışlanacak VM 'lerin listesini belirtmeyi desteklemez.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabınız olmalıdır. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

- Hesabınıza, abonelikte [katkıda](../../role-based-access-control/built-in-roles.md#contributor) bulunan izin verildi.

- Başlat/Durdur VM 'Ler v2 (Önizleme), Azure Işlevleri için [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) sayfasında listelenen tüm Azure küresel ve ABD kamu bulut bölgelerinde kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu özelliği dağıtmak için bkz. [VM 'Leri başlatma/durdurma](deploy.md) (Önizleme).