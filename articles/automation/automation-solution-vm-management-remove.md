---
title: Azure Otomasyonu VM'leri çalışma saatleri dışında başlat/durdur genel bakış 'ı kaldır
description: Bu makalede, VM'leri çalışma saatleri dışında başlat/durdur özelliğinin nasıl kaldırılacağı ve bir Otomasyon hesabının Log Analytics çalışma alanından nasıl kaldırılacağı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533946"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Otomasyon hesabından VM'leri çalışma saatleri dışında başlat/durdur kaldır

Azure VM 'lerinizin çalışma durumunu yönetmek için VM'leri çalışma saatleri dışında başlat/durdur özelliğini etkinleştirdikten sonra, kullanmayı durdurmayı seçebilirsiniz. Bu özelliğin kaldırılması, desteklenen dağıtım modellerine bağlı olarak aşağıdaki yöntemlerden biri kullanılarak yapılabilir:

* Otomasyon hesabını ve bağlı Azure Izleyici Log Analytics çalışma alanını içeren kaynak grubunu silin, her biri bu özelliği desteklemek için ayrılır.
* Log Analytics çalışma alanının Otomasyon hesabından bağlantısını kaldırın ve bu özellik için adanmış Otomasyon hesabını silin.
* Özelliği bir Otomasyon hesabından ve diğer yönetim ve izleme hedeflerini destekleyen bağlantılı çalışma alanından silin.

Bu özelliğin silinmesi yalnızca ilişkili runbook 'ları kaldırır, dağıtım sırasında oluşturulan zamanlamaları veya değişkenleri veya sonrasında oluşturulan özel tanımlı herhangi bir özelliği silmez.

> [!NOTE]
> Devam etmeden önce, abonelik, kaynak grubu veya kaynak üzerinde, önemli kaynakların yanlışlıkla silinmesini veya değiştirilmesini önleyen [Kaynak Yöneticisi kilit](../azure-resource-manager/management/lock-resources.md) olmadığından emin olun. VM'leri çalışma saatleri dışında başlat/durdur çözümünü dağıttığınızda, Otomasyon hesabındaki birkaç bağımlı kaynağa (özellikle runbook 'lar ve değişkenler) karşı kilit düzeyini **Cannotdelete** olarak ayarlar. Otomasyon hesabını silebilmeniz için önce tüm kilitlerin kaldırılması gerekir.

## <a name="delete-the-dedicated-resource-group"></a>Adanmış kaynak grubunu silme

Kaynak grubunu silmek için [Azure Resource Manager kaynak grubu ve kaynak silme](../azure-resource-manager/management/delete-resource-group.md) makalesinde özetlenen adımları izleyin.

## <a name="delete-the-automation-account"></a>Otomasyon hesabını silme

VM'leri çalışma saatleri dışında başlat/durdur için ayrılmış Otomasyon hesabınızı silmek için aşağıdaki adımları gerçekleştirin.

1. Adresinden Azure 'da oturum açın [https://portal.azure.com](https://portal.azure.com) .

2. Otomasyon hesabınıza gidin ve **ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

3. **Çalışma alanına git**' i seçin.

4. **Genel** altında **çözümler** ' e tıklayın.

5. Çözümler sayfasında, **Başlat-Durdur-VM [çalışma alanı]** seçeneğini belirleyin.

6. **Vmmanagementsolution [çalışma alanı]** sayfasında menüden **Sil** ' i seçin.

7. Bilgiler doğrulanırken ve özellik silinirken, menüden seçili olan **Bildirimler** altında ilerlemeyi izleyebilirsiniz. Kaldırma işleminden sonra çözümler sayfasına döndürülürsünüz.

### <a name="unlink-workspace-from-automation-account"></a>Çalışma alanının Otomasyon hesabından bağlantısını kaldırma

Otomasyon hesabınızdan Log Analytics çalışma alanının bağlantısını kesmek için iki seçenek vardır. Bu işlemi Otomasyon hesabından veya bağlı çalışma alanından gerçekleştirebilirsiniz.

Otomasyon Hesabınızın bağlantısını kaldırmak için aşağıdaki adımları gerçekleştirin.

1. Azure portal **Otomasyon hesapları**' nı seçin.

2. Otomasyon hesabınızı açın ve sol taraftaki **Ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

3. **Çalışma alanının bağlantısını kaldır** sayfasında, **çalışma alanının bağlantısını kaldır** ' ı seçin ve istemlere yanıtlayın.

   ![Çalışma alanının bağlantısını Kaldır sayfası](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics çalışma alanının bağlantısını kesmeyi denediğinde, menüdeki **Bildirimler** altında ilerlemeyi izleyebilirsiniz.

Çalışma alanından bağlantısını kaldırmak için aşağıdaki adımları gerçekleştirin.

1. Azure portal, **Log Analytics çalışma alanları**' nı seçin.

2. Çalışma alanından **Ilgili kaynaklar** altında **Otomasyon hesabı** ' nı seçin.

3. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır** ve istemlere yanıt ver ' i seçin.

Otomasyon hesabının bağlantısını kaldırmayı denediğinde, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

### <a name="delete-automation-account"></a>Otomasyon hesabını Sil

1. Azure portal **Otomasyon hesapları**' nı seçin.

2. Otomasyon hesabınızı açın ve menüden **Sil** ' i seçin.

Bilgiler doğrulanırken ve hesap silinirken, menüden seçili olan **Bildirimler** altında ilerlemeyi izleyebilirsiniz.

## <a name="delete-the-feature"></a>Özelliği silme

Otomasyon hesabınızdan VM'leri çalışma saatleri dışında başlat/durdur silmek için aşağıdaki adımları gerçekleştirin. Otomasyon hesabı ve Log Analytics çalışma alanı bu işlemin bir parçası olarak silinmez. Log Analytics çalışma alanını korumak istemiyorsanız, el ile silmeniz gerekir. Çalışma alanınızı silme hakkında daha fazla bilgi için bkz. [Azure Log Analytics çalışma alanını silme ve kurtarma](../azure-monitor/logs/delete-workspace.md).

1. Otomasyon hesabınıza gidin ve **ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

2. **Çalışma alanına git**' i seçin.

3. **Genel** altında **çözümler** ' e tıklayın.

4. Çözümler sayfasında, **Başlat-Durdur-VM [çalışma alanı]** seçeneğini belirleyin.

5. **Vmmanagementsolution [çalışma alanı]** sayfasında menüden **Sil** ' i seçin.

    ![VM yönetimi özelliğini Sil](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Çözümü Sil penceresinde, özelliği silmek istediğinizi onaylayın.

7. Bilgiler doğrulanırken ve özellik silinirken, menüden seçili olan **Bildirimler** altında ilerlemeyi izleyebilirsiniz. Kaldırma işleminden sonra çözümler sayfasına döndürülürsünüz.

8. Özellik tarafından oluşturulan [kaynakları](automation-solution-vm-management.md#components) tutmak istemiyorsanız (örneğin, değişkenler, zamanlamalar, vb.), bunları hesaptan el ile silmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu özelliği yeniden etkinleştirmek için, bkz. [çalışma saatleri dışında başlatma/durdurma 'Yı etkinleştirme](automation-solution-vm-management-enable.md).