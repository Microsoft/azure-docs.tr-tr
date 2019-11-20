---
title: Güncelleştirme ve değişiklik izleme çözümlerini Azure Otomasyonu’na ekleme
description: Güncelleştirme ve değişiklik izleme çözümlerini Azure Otomasyonu’na nasıl ekleyeceğinizi öğrenin.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: cacd9206eb0213d410a11ee3a2dcf5c0e28989c6
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372826"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Güncelleştirme ve değişiklik izleme çözümlerini Azure Otomasyonu’na ekleme

Bu öğreticide VM’lere yönelik Güncelleştirme, Değişiklik İzleme ve Sayım çözümlerini Azure Otomasyonu’na nasıl otomatik olarak ekleyeceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * Bir Azure VM ekleme
> * Çözümleri etkinleştirme
> * Modülleri yükleme ve güncelleştirme
> * Ekleme runbook’unu içeri aktarma
> * Runbook’u başlatma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* Azure aboneliği. Henüz bir aboneliğiniz yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ya da [ücretsiz hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) için kaydolabilirsiniz.
* Makineleri yönetmek için [Otomasyon hesabı](automation-offering-get-started.md).
* Sisteme eklenecek bir [sanal makine](../virtual-machines/windows/quick-create-portal.md).

## <a name="onboard-an-azure-vm"></a>Bir Azure VM ekleme

Makine eklemenin birden fazla yolu vardır. Çözümü [bir sanal makineden](automation-onboard-solutions-from-vm.md), [birden çok makineye gözatmadan](automation-onboard-solutions-from-browse.md), [Otomasyon hesabınızdan](automation-onboard-solutions-from-automation-account.md) veya runbook ile ekleyebilirsiniz. Bu öğretici bir runbook üzerinden Güncelleştirme Yönetimi’ni etkinleştirme konusunda size yol gösterir. Uygun ölçekte Azure Sanal Makinelerin eklenmesi için mevcut bir VM’ye Değişiklik izleme veya Güncelleştirme yönetimi çözümünün eklenmesi gerekir. Bu adımda, bir sanal makineye Güncelleştirme yönetimi ve Değişiklik izleme özelliğini eklersiniz.

### <a name="enable-change-tracking-and-inventory"></a>Değişiklik İzlemeyi ve Sayımı Etkinleştirme

Değişiklik İzleme ve Sayım çözümü, sanal makinelerinizde [değişiklikleri izleme](automation-vm-change-tracking.md) ve [sayım](automation-vm-inventory.md) olanağı sağlar. Bu adımda çözümü bir sanal makine üzerinde etkinleştirirsiniz.

1. Sol taraftaki menüden **Otomasyon Hesapları**’nı seçin ve sonra listeden otomasyon hesabınızı seçin.
1. **YAPILANDIRMA YÖNETİMİ** bölümünde **Sayım**’ı seçin.
1. Mevcut bir Log Analytics çalışma alanını seçin veya yenisini oluşturun. **Etkinleştir** düğmesine tıklayın.

![Güncelleştirme çözümü ekleme](media/automation-onboard-solutions/inventory-onboard.png)

Değişiklik izleme ve sayım çözümü ekleme bildirimi tamamlandığında **YAPILANDIRMA YÖNETİMİ** bölümünde **Güncelleştirme Yönetimi**’ne tıklayın.

### <a name="enable-update-management"></a>Güncelleştirme Yönetimi’ni etkinleştirme

Güncelleştirme Yönetimi çözümü, Azure Windows VM’leriniz için güncelleştirmeleri ve yamaları yönetmenizi sağlar. Kullanılabilir güncelleştirmelerin durumunu değerlendirebilir, gerekli güncelleştirmelerin yüklemesini zamanlayabilir ve güncelleştirmelerin VM’ye başarıyla uygulandığını doğrulamak için dağıtım sonuçlarını gözden geçirebilirsiniz. Bu adımda çözümü VM’niz için etkinleştirirsiniz.

1. Otomasyon Hesabınızdan **GÜNCELLEŞTİRME YÖNETİMİ** bölümünde **Güncelleştirme yönetimi**’ni seçin.
1. Seçilen Log Analytics çalışma alanı, önceki adımda kullanılan çalışma alanıdır. Güncelleştirme yönetimi çözümünü eklemek için **Etkinleştir**’e tıklayın.

![Güncelleştirme çözümü ekleme](media/automation-onboard-solutions/update-onboard.png)

Güncelleştirme yönetimi çözümü yüklenirken mavi renkli bir başlık gösterilir. Çözüm etkinleştirildiğinde bir sonraki adıma geçmek için **YAPILANDIRMA YÖNETİMİ** bölümünde **Değişiklik izleme** seçeneğini belirleyin.

### <a name="select-azure-vm-to-be-managed"></a>Yönetilecek Azure VM’yi seçme

Çözümler etkinleştirildiğine göre artık bu çözümleri eklemek için bir Azure VM ekleyebilirsiniz.

1. Otomasyon Hesabınızdan **Değişiklik izleme** sayfasında sanal makinenizi eklemek için **+ Azure VM Ekle** seçeneğini belirleyin.

1. Listeden VM’nizi seçin ve **Etkinleştir** seçeneğini belirleyin. Bu eylem, sanal makine için Değişiklik izleme ve Sayım çözümünü etkinleştirir.

   ![VM için güncelleştirme çözümünü etkinleştirme](media/automation-onboard-solutions/enable-change-tracking.png)

1. VM ekleme bildirimi tamamlandığında Otomasyon Hesabınızdan **GÜNCELLEŞTİRME YÖNETİMİ** bölümündeki **Güncelleştirme yönetimi** seçeneğini belirleyin.

1. Sanal makinenizi eklemek için **+ Azure VM ekle** seçeneğini belirleyin.

1. Listeden VM’nizi seçin ve **Etkinleştir** seçeneğini belirleyin. Bu eylem, Güncelleştirme yönetimi çözümünü sanal makine için etkinleştirir.

   ![VM için güncelleştirme çözümünü etkinleştirme](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Diğer çözümün tamamlanmasını beklerseniz, bir sonraki çözümü etkinleştirirken, *Bu veya farklı bir sanal makinede başka bir çözümün yüklenmesi durumunda olduğunu belirten bir ileti alırsınız. Yükleme tamamlandığında, etkinleştir düğmesi etkinleştirilir ve çözümün bu sanal makineye yüklenmesini isteyebilirsiniz.*

## <a name="install-and-update-modules"></a>Modülleri yükleme ve güncelleştirme

Çözüm eklemeyi başarıyla otomatik hale getirmek için en son Azure modüllerine güncelleştirme yapılması ve `AzureRM.OperationalInsights` modülünün içeri aktarılması gerekir.

## <a name="update-azure-modules"></a>Azure Modüllerini etkinleştirme

Otomasyon Hesabınızdan **PAYLAŞILAN KAYNAKLAR** bölümündeki **Modüller** seçeneğini belirleyin. Azure modüllerini en son sürüme güncelleştirmek için **Azure Modüllerini Güncelleştir** seçeneğini belirleyin. Tüm mevcut Azure modüllerini son sürüme güncelleştirmek için istemde **Evet**’i seçin.

![Modülleri güncelleştirme](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>AzureRM.OperationalInsights modülünü yükleme

**Modüller** sayfasından, modül galerisini açmak için **Galeriye gözat** seçeneğini belirleyin. AzureRM.OperationalInsights araması yapın ve bu modülü Otomasyon hesabına aktarın.

![OperationalInsights modülünü içeri aktarma](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Ekleme runbook’unu içeri aktarma

1. Otomasyon Hesabınızdan **İŞLEM OTOMASYONU** bölümündeki **Runbook’lar** seçeneğini belirleyin.
1. **Galeriye gözat** seçeneğini belirleyin.
1. **Güncelleştirme ve değişiklik izleme** araması yapın, runbook’a tıklayın ve **Kaynağı Görüntüle** sayfasındaki **İçeri Aktar** seçeneğini belirleyin. Runbook’u Otomasyon hesabına aktarmak için **Tamam** seçeneğini belirleyin.

   ![Ekleme runbook’unu içeri aktarma](media/automation-onboard-solutions/import-from-gallery.png)

1. **Runbook** sayfasında **Düzenle** seçeneğini, sonra da **Yayımla** seçeneğini belirleyin. Runbook’u yayımlamak için **Runbook’u Yayımla** iletişim kutusunda **Evet**’i seçin.

## <a name="start-the-runbook"></a>Runbook’u başlatma

Bu runbook’u başlatmak için değişiklik izleme çözümünü veya güncelleştirme çözümünü bir Azure VM’ye eklemiş olmanız gerekir. Parametreler için çözümün eklenmiş olduğu mevcut bir sanal makine ve kaynak grubu gereklidir.

1. Enable-MultipleSolution runbook’unu açın.

   ![Çoklu çözüm runbook’ları](media/automation-onboard-solutions/runbook-overview.png)

1. Başlat düğmesine tıklayın ve parametreler için aşağıdaki değerleri girin.

   * **VMNAME** - Boş bırakın. Güncelleştirme veya değişiklik izleme çözümünün ekleneceği mevcut bir VM’nin adıdır. Bu değeri bış bıraktığınızda kaynak grubundaki tüm VM’lere ekleme yapılır.
   * **VMRESOURCEGROUP** - Ekleme yapılacak VM’lere ilişkin kaynak grubunun adıdır.
   * **SUBSCRIPTIONID** - Boş bırakın. Ekleme yapılacak yeni VM’nin abonelik kimliğidir. Boş bırakılırsa çalışma alanının aboneliği kullanılır. Farklı bir abonelik kimliği belirtildiğinde, bu otomasyon hesabına yönelik RunAs hesabının da söz konusu abonelik için katkıda bulunan olarak eklenmesi gerekir.
   * **ALREADYONBOARDEDVM** - Güncelleştirme veya Değişiklik İzleme çözümünün el ile eklendiği VM’nin adıdır.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM’nin üyesi olduğu kaynak grubunun adıdır.
   * **SOLUTIONTYPE** - **Güncelleştirmeler** veya **Değişiklik İzleme** girin

   ![Enable-MultipleSolution runbook parametreleri](media/automation-onboard-solutions/runbook-parameters.png)

1. Runbook işini başlatmak için **Tamam**’ı seçin.
1. Runbook işi sayfasında ilerlemeyi ve hataları izleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sanal makineyi Güncelleştirme Yönetimi kaldırmak için:

* Log Analytics çalışma alanınızda, kapsam yapılandırması için kayıtlı aramadan VM 'yi kaldırın `MicrosoftDefaultScopeConfig-Updates`. Kayıtlı aramalar, çalışma alanınızda **genel** altında bulunabilir.
* Linux için [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) 'ı veya [Log Analytics aracısını](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir Azure sanal makinesine el ile ekleme yapma.
> * Gerekli Azure modüllerini yükleme ve güncelleştirme.
> * Azure VM’lerine ekleme yapan bir runbook’u içeri aktarma.
> * Azure VM’lerine otomatik olarak ekleme yapan runbook’u başlatma.

Runbook’ların zamanlanması hakkında daha fazla bilgi edinmek için şu bağlantıyı izleyin.

> [!div class="nextstepaction"]
> [Runbook’ları zamanlama](automation-schedules.md).
