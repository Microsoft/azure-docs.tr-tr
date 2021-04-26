---
title: Azure portal 'ten VM uzantısını etkinleştir
description: Bu makalede, karma bulut ortamlarında çalışan Azure Arc etkin sunucularına sanal makine uzantılarının Azure portal nasıl dağıtılacağı açıklanır.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388287"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure portal Azure VM uzantılarını etkinleştirin

Bu makalede, Azure Arc etkin sunucuları tarafından desteklenen Azure VM uzantılarını Azure portal aracılığıyla bir Linux veya Windows hibrit makinesine dağıtma ve kaldırma işlemi gösterilmektedir.

> [!NOTE]
> Key Vault VM Uzantısı (Önizleme), Azure portal dağıtımını desteklemez, yalnızca Azure CLı, Azure PowerShell veya Azure Resource Manager şablonu kullanılarak desteklenmez.

> [!NOTE]
> Azure yay özellikli sunucular, VM uzantılarının Azure sanal makinelerine dağıtılmasını ve yönetilmesini desteklemez. Azure VM 'Leri için aşağıdaki [VM uzantısına genel bakış](../../virtual-machines/extensions/overview.md) makalesine bakın.

## <a name="enable-extensions-from-the-portal"></a>Portaldan uzantıları etkinleştirme

VM uzantıları, Azure portal aracılığıyla sunucu tarafından yönetilen makineye yönelik yaya uygulanabilir.

1. Tarayıcınızdan [Azure Portal](https://portal.azure.com)gidin.

2. Portalda, **sunucular-Azure Arc** ' a gidin ve listeden karma makinenizi seçin.

3. **Uzantılar**' ı seçin ve **Ekle**' yi seçin. Kullanılabilir uzantılar listesinden istediğiniz uzantıyı seçin ve sihirbazdaki yönergeleri izleyin. Bu örnekte, Log Analytics VM uzantısını dağıtacağız.

    ![Seçilen makine için VM Uzantısı Seç](./media/manage-vm-extensions/add-vm-extensions.png)

    Aşağıdaki örnek, Azure portal Log Analytics VM uzantısının yüklenmesini gösterir:

    ![Log Analytics VM uzantısını yükler](./media/manage-vm-extensions/mma-extension-config.png)

    Yüklemeyi gerçekleştirmek için çalışma alanı KIMLIĞI ve birincil anahtarı sağlamanız gerekir. Bu bilgilerin nasıl bulunacağını bilmiyorsanız bkz. [çalışma alanı kimliğini ve anahtarını alma](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

4. Belirtilen gerekli bilgileri onayladıktan sonra **Oluştur**' u seçin. Dağıtımın bir özeti görüntülenir ve dağıtımın durumunu gözden geçirebilirsiniz.

>[!NOTE]
>Birden çok uzantı birlikte toplanmış ve işlenebilir olsa da, bunlar hizmet temelli olarak yüklenir. İlk uzantı yüklemesi tamamlandıktan sonra, sonraki uzantının yüklenmesi denenir.

## <a name="list-extensions-installed"></a>Yüklü liste uzantıları

Azure portal, Arc etkin sunucunuzdaki sanal makine uzantılarının bir listesini alabilir. Bunları görmek için aşağıdaki adımları gerçekleştirin.

1. Tarayıcınızdan [Azure Portal](https://portal.azure.com)gidin.

2. Portalda, **sunucular-Azure Arc** ' a gidin ve listeden karma makinenizi seçin.

3. **Uzantılar**' ı seçin ve yüklü uzantıların listesi döndürülür.

    ![Seçilen makineye dağıtılan VM uzantısını Listele](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Uzantıyı kaldır

Azure portal bir yay etkin sunucusundan bir veya daha fazla uzantıyı kaldırabilirsiniz. Bir uzantıyı kaldırmak için aşağıdaki adımları gerçekleştirin.

1. Tarayıcınızdan [Azure Portal](https://portal.azure.com)gidin.

2. Portalda, **sunucular-Azure Arc** ' a gidin ve listeden karma makinenizi seçin.

3. **Uzantılar**' ı seçin ve ardından yüklü uzantılar listesinden bir uzantı seçin.

4. **Kaldır** ' ı seçin ve doğrulamanız istendiğinde, devam etmek için **Evet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md)veya [Azure Resource Manager şablonlarını](manage-vm-extensions-template.md)kullanarak VM uzantılarını dağıtabilir, yönetebilir ve kaldırabilirsiniz.

- Sorun giderme bilgileri, [VM genişletmeleri sorunlarını gider kılavuzunda](troubleshoot-vm-extensions.md)bulunabilir.