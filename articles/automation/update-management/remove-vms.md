---
title: Azure Otomasyonu Güncelleştirme Yönetimi VM 'Leri kaldırma
description: Bu makalede, Güncelleştirme Yönetimi ile yönetilen makinelerin nasıl kaldırılacağı açıklanır.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97913201"
---
# <a name="remove-vms-from-update-management"></a>VM’leri Güncelleştirme Yönetimi'nden kaldırma

Ortamınızdaki sanal makinelerinizdeki güncelleştirmeleri yönetmeyi tamamladığınızda, [güncelleştirme yönetimi](overview.md) özelliğini kullanarak VM 'leri yönetmeyi durdurabilirsiniz. Bunları yönetmeyi durdurmak için, `MicrosoftDefaultComputerGroup` Log Analytics çalışma alanınızda Otomasyon hesabınıza bağlı olan kayıtlı arama sorgusunu düzenlersiniz.

## <a name="sign-into-the-azure-portal"></a>Azure portalda oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="to-remove-your-vms"></a>VM 'lerinizi kaldırmak için

1. Azure portal, Azure portal üst gezintiden **Cloud Shell** başlatın. Azure Cloud Shell hakkında bilginiz yoksa bkz. [Azure Cloud Shell Genel Bakış](../../cloud-shell/overview.md).

2. Yönetimden kaldırmak istediğiniz bir makinenin UUID 'sini tanımlamak için aşağıdaki komutu kullanın.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure portal, **Log Analytics çalışma alanları**' na gidin. Listeden çalışma alanınızı seçin.

4. Log Analytics çalışma alanınızda, **Gelişmiş ayarlar** ' ı seçin ve sol taraftaki menüden **bilgisayar grupları** ' nı seçin.

5. Sağ bölmedeki **bilgisayar gruplarından** , **kaydedilmiş gruplar**' ı seçin.

6. Tablodan, kayıtlı arama sorgusu **güncelleştirmeleri: MicrosoftDefaultComputerGroup** için, çalıştırılacak **üyeleri görüntüle** simgesine tıklayın ve üyelerini görüntüleyin.

7. Sorgu Düzenleyicisi 'nde, sorguyu gözden geçirin ve VM için UUID 'yi bulun. VM için UUID 'yi kaldırın ve kaldırmak istediğiniz diğer VM 'Ler için adımları tekrarlayın.

8. En üstteki çubuktan **Kaydet** ' i seçerek, kayıt düzenlemesini tamamladıktan sonra kaydedilmiş aramayı kaydedin. İstendiğinde, aşağıdakileri belirtin:

    * **Ad**: microsoftdefaultcomputergroup
    * **Farklı kaydet**: işlev
    * **Diğer ad**: Updates__MicrosoftDefaultComputerGroup
    * **Kategori**: güncelleştirmeler

>[!NOTE]
>Son 24 saat içinde değerlendirilen tüm makinelere rapor verdiğimiz için makineler kaydolduktan sonra gösterilmeye devam eder. Makineyi kaldırdıktan sonra, artık listelenmemesi için 24 saat beklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Sanal makinenizi yönetmeyi yeniden etkinleştirmek için bkz. [Azure Portal göz atarak güncelleştirme yönetimi etkinleştirme](enable-from-portal.md) veya [bir Azure VM 'den güncelleştirme yönetimi etkinleştirme](enable-from-vm.md).