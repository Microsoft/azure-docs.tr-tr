---
title: VM 'Leri başlatma/durdurma sorunlarını giderme (Önizleme)
description: Bu makalede, Azure sanal makinelerinize yönelik VM 'Leri Başlat/Durdur (Önizleme) özelliği ile karşılaşılan sorunları gidermeye ilişkin yönergeler açıklanmaktadır.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112030"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Başlat/Durdur VM 'lerle ilgili sık karşılaşılan sorunları giderme (Önizleme)

Bu makalede, başlatma/durdurma VM 'Leri (Önizleme) yüklenmeye ve yapılandırmaya çalışırken oluşabilecek sorunların giderilmesi ve çözümlenmesi hakkında bilgi verilmektedir. Genel bilgiler için bkz. [VM 'Leri Başlat/Durdur genel bakış](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Genel doğrulama ve sorun giderme

Bu bölümde, zamanlamalar senaryolarıyla ilgili genel sorunların nasıl giderileceği ele alınmaktadır ve kök neden tanımlanmakta yardımcı olur.

### <a name="azure-dashboard"></a>Azure panosu

Azure Paylaşılan panosunu inceleyerek başlayabilirsiniz. VM 'lerde gerçekleştirilen her bir işlemin durumunu doğrulamak için hızlı ve kolay bir yol olan VM 'Leri Başlat/Durdur v2 (Önizleme) kapsamında dağıtılan Azure Paylaşılan panosu. Sanal makinelerinizdeki tüm son işlemleri görmek için VM 'lerde gerçekleştirilen **son denenen eylemlere** bakın. Verilerin Application Insights kaynaktaki verileri aldığı şekilde raporda görünmesi için beş dakika içinde bazı gecikme süreleri vardır.

### <a name="logic-apps"></a>Logic Apps

Başlangıç/durdurma senaryonuzu desteklemeye hangi Logic Apps seçtiğinize bağlı olarak, zamanlanan başlatma/kapatılma senaryosunun bir veya daha fazla hedef VM için başarıyla tamamlanmamış olduğunu belirlemenize yardımcı olması için çalıştırma geçmişini gözden geçirebilirsiniz. Bunu ayrıntılı olarak gözden geçirmeyi öğrenmek için [Logic Apps çalıştırma geçmişi](../../logic-apps/monitor-logic-apps.md#review-runs-history)' ne bakın.

### <a name="azure-storage"></a>Azure Depolama

Başlat/Durdur VM 'Ler v2 (Önizleme) için kullanılan Azure depolama hesabındaki **requestsstoretable** tablosuna yapılan işlemlerin ayrıntılarını gözden geçirebilirsiniz. Bu kayıtları görüntülemek için aşağıdaki adımları gerçekleştirin.

1. Azure portal depolama hesabına gidin ve hesapta sol bölmedeki * * Depolama Gezgini (Önizleme) seçeneğini belirleyin.
1. **Tablolar** ' ı seçin ve ardından **requeststoretable** öğesini seçin.
1. Tablodaki her kayıt, Logic App senaryosunda tanımlanan hedef kapsamına bağlı olarak bir Azure VM 'ye karşı gerçekleştirilen başlatma/durdurma eylemini temsil eder. Sonuçları, kayıt özelliklerinden herhangi birine göre filtreleyebilirsiniz (örneğin, zaman DAMGASı, eylem veya TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Azure İşlevleri

VM başlatma ve yürütmeyi durdurma ile ilgili Azure Işlevlerinin herhangi biri için en son çağırma ayrıntılarını gözden geçirebilirsiniz. Önce yürütme akışını gözden geçirelim.

Hem **Zamanlanmış** hem de **sıralı** senaryonun yürütme akışı aynı işlevle denetlenir. Yük şeması hangi senaryonun gerçekleştirileceğini belirler. **Zamanlanan** senaryo için, yürütme akışı **Zamanlanmış** HTTP > **VirtualMachineRequestOrchestrator** Queue > **Virtualmachinerequestexecutor** Queue.

Mantıksal uygulamadan, **zamanlanan** http işlevi yük şeması ile çağrılır. **Zamanlanan** http işlevi isteği aldıktan sonra, bu bilgileri **Orchestrator** kuyruğu işlevine gönderir ve bu işlem, eylemi GERÇEKLEŞTIRMEK için her bir VM için birkaç kuyruk oluşturur.

Çağırma ayrıntılarını görmek için aşağıdaki adımları gerçekleştirin.

1. Azure portal **Azure işlevleri**' ne gidin.
1. Listeden Başlat/Durdur VM v2 (Önizleme) için Işlev uygulamasını seçin.
1. Sol bölmedeki **işlevleri** seçin.
1. Listede, her senaryo için ilişkili birkaç işlev görürsünüz. **Zamanlanan** http işlevini seçin.
1. Sol bölmeden **izleyici** ' yi seçin.
1. Ayrıntılı günlük kaydı için çağrı ayrıntılarını ve ileti bölümünü görmek üzere en son yürütme izlemesini seçin.
1. Daha önce yürütme akışını gözden geçirmesinin parçası olarak açıklanan her bir işlev için aynı adımları yineleyin.

Azure Işlevlerini izleme hakkında daha fazla bilgi için bkz. [Application Insights Azure işlevleri telemetrisini çözümleme](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevlerini ve mantıksal uygulamaları izleme hakkında daha fazla bilgi edinin:

* [Azure Işlevlerini izleyin](../../azure-functions/functions-monitoring.md).

* [Azure işlevleri için izlemeyi yapılandırma](../../azure-functions/configure-monitoring.md).

* [Mantıksal uygulamaları izleyin](../../logic-apps/monitor-logic-apps.md).