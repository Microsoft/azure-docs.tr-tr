---
title: Azure Otomasyonu Değişiklik İzleme ve envanterinden VM 'Leri kaldırma
description: Bu makalede VM 'Leri Değişiklik İzleme ve envanterden nasıl kaldıracakları açıklanır.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b79fa22d3203504e63161aba03b32830d74d016
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131284"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Değişiklik İzleme ve Stok özelliğinden VM’leri kaldırma

Ortamınızdaki VM 'lerde yapılan değişiklikleri izlemeyi tamamladığınızda, [değişiklik izleme ve envanter](overview.md) özelliği ile yönetmeyi durdurabilirsiniz. Bunları yönetmeyi durdurmak için, `MicrosoftDefaultComputerGroup` Log Analytics çalışma alanınızda Otomasyon hesabınıza bağlı olan kayıtlı arama sorgusunu düzenlersiniz.

## <a name="sign-into-the-azure-portal"></a>Azure portalda oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="to-remove-your-vms"></a>VM 'lerinizi kaldırmak için

1. Azure portal, Azure portal üst gezintiden **Cloud Shell** başlatın. Azure Cloud Shell hakkında bilginiz yoksa bkz. [Azure Cloud Shell Genel Bakış](../../cloud-shell/overview.md).

2. Yönetimden kaldırmak istediğiniz bir makinenin UUID 'sini tanımlamak için aşağıdaki komutu kullanın.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure portal, **Log Analytics çalışma alanları**' na gidin. Listeden çalışma alanınızı seçin.

4. Log Analytics çalışma alanınızda **Günlükler** ' i seçin ve ardından üstteki Eylemler menüsünden **sorgu Gezgini** ' ni seçin.

5. Sağ bölmedeki **sorgu Gezgini** ' nden, **kaydedilmiş Queries\Updates** ' i genişletin ve kayıtlı arama sorgusunu seçerek `MicrosoftDefaultComputerGroup` düzenleyin.

6. Sorgu Düzenleyicisi 'nde, sorguyu gözden geçirin ve VM için UUID 'yi bulun. VM için UUID 'yi kaldırın ve kaldırmak istediğiniz diğer VM 'Ler için adımları tekrarlayın.

7. En üstteki çubuktan **Kaydet** ' i seçerek, kayıt düzenlemesini tamamladıktan sonra kaydedilmiş aramayı kaydedin.

>[!NOTE]
>Son 24 saat içinde değerlendirilen tüm makinelere rapor verdiğimiz için makineler kaydolduktan sonra gösterilmeye devam eder. Makineyi kaldırdıktan sonra, artık listelenmemesi için 24 saat beklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu özelliği yeniden etkinleştirmek için bkz. [Otomasyon hesabından değişiklik izleme ve envanteri etkinleştirme](enable-from-automation-account.md), [Azure portal göz atarak değişiklik izleme ve envanteri etkinleştirme](enable-from-portal.md), [runbook 'tan değişiklik izleme ve envanteri ETKINLEŞTIRME](enable-from-runbook.md)ya da [bir Azure VM 'den değişiklik izleme ve envanteri etkinleştirme](enable-from-vm.md).