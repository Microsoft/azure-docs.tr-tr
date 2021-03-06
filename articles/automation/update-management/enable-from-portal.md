---
title: Azure portal Azure Otomasyonu Güncelleştirme Yönetimi etkinleştirme
description: Bu makalede Azure portal Güncelleştirme Yönetimi nasıl etkinleştirileceği açıklanır.
services: automation
ms.subservice: update-management
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089c5fea6ac4a6fc4fb25af2d631335ef51cf4cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054915"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Azure portalından Güncelleştirme Yönetimi’ni etkinleştirme

Bu makalede, Azure portal göz atarak VM 'Ler için [güncelleştirme yönetimi](overview.md) özelliğini nasıl etkinleştirebileceğinizi açıklanmaktadır. Azure VM 'lerini ölçekli olarak etkinleştirmek için, Güncelleştirme Yönetimi kullanarak mevcut bir Azure VM 'yi etkinleştirmeniz gerekir.

VM 'lerinizi yönetmek için kullanabileceğiniz kaynak gruplarının sayısı [Kaynak Yöneticisi dağıtım limitleriyle](../../azure-resource-manager/templates/deploy-to-resource-group.md)sınırlıdır. Güncelleştirme dağıtımlarıyla karıştırılmamalıdır Kaynak Yöneticisi dağıtımlar, dağıtım başına beş kaynak grubu ile sınırlıdır. Log Analytics çalışma alanını, Otomasyon hesabını ve ilgili kaynakları yapılandırmak için bu kaynak gruplarından ikisi ayrılmıştır. Bu, Güncelleştirme Yönetimi tarafından yönetim için seçilecek üç kaynak grubu ile birlikte kalır. Bu sınır, bir Otomasyon özelliği tarafından yönetilebilen kaynak gruplarının sayısını değil, yalnızca eşzamanlı kurulum için geçerlidir.

> [!NOTE]
> Güncelleştirme Yönetimi etkinleştirilirken, bir Log Analytics çalışma alanını ve bir Otomasyon hesabını bağlamak için yalnızca belirli bölgeler desteklenir. Desteklenen eşleme çiftlerinin bir listesi için bkz. [Otomasyon hesabı ve Log Analytics çalışma alanı Için bölge eşleme](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.
* Makineleri yönetmek için [Otomasyon hesabı](../automation-security-overview.md).
* Bir [sanal makine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Adresinden Azure 'da oturum açın https://portal.azure.com .

## <a name="enable-update-management"></a>Güncelleştirme Yönetimi’ni etkinleştirme

1. Azure portal, **sanal makineler**' e gidin.

2. **Sanal makineler** sayfasında, güncelleştirme yönetimi eklenecek VM 'leri seçmek için onay kutularını kullanın. Tek seferde en fazla üç farklı kaynak grubu için makine ekleyebilirsiniz. Otomasyon hesabınızın konumu ne olduğuna bakılmaksızın, Azure VM 'Leri herhangi bir bölgede bulunabilir.

    ![VM 'lerin listesi](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Farklı aboneliklerden, konumlardan ve kaynak gruplarından VM 'Leri seçmek için filtre denetimlerini kullanın. Bir listedeki tüm sanal makineleri seçmek için üstteki onay kutusuna tıklayabilirsiniz.

    [![Güncelleştirme yönetimi etkinleştir](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. **Hizmetler** ' i seçin ve güncelleştirme yönetimi özelliği için **güncelleştirme yönetimi** ' yi seçin.

4. Sanal makinelerin listesi, yalnızca aynı abonelikte ve konumda bulunan sanal makineleri gösterecek şekilde filtrelenmiştir. Sanal makinelerinizde üçten fazla kaynak grubu varsa, ilk üç kaynak grubu seçilir.

5. Mevcut bir Log Analytics çalışma alanı ve Otomasyon hesabı varsayılan olarak seçilidir. Farklı bir Log Analytics çalışma alanı ve Otomasyon hesabı kullanmak istiyorsanız, özel yapılandırma sayfasından seçmek için **özel** ' i seçin. Bir Log Analytics çalışma alanı seçtiğinizde, bir Otomasyon hesabıyla bağlanıp bağlanmadığını belirlemek için bir onay yapılır. Bağlı bir Otomasyon hesabı bulunursa, aşağıdaki ekranı görürsünüz. İşiniz bittiğinde **Tamam**’ı seçin.

    [![Çalışma alanını ve hesabı seçin](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Seçilen çalışma alanı bir Otomasyon hesabına bağlı değilse, aşağıdaki ekranı görürsünüz. Bir Otomasyon hesabı seçin ve bittiğinde **Tamam** ' ı seçin.

    ![Çalışma alanı yok](media/enable-from-portal/no-workspace.png)

7. Etkinleştirmek istemediğiniz tüm sanal makinelerin seçimini kaldırın. Etkinleştirimeyen VM 'Lerin zaten seçimi kaldırılmış.

8. Özelliği etkinleştirmek için **Etkinleştir** ' i seçin. Güncelleştirme Yönetimi etkinleştirildikten sonra, bunlardan güncelleştirme değerlendirmesini görüntüleyebilmeniz 15 dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

* VM 'Ler için Güncelleştirme Yönetimi kullanmak için bkz. [VM 'niz için güncelleştirmeleri ve düzeltme eklerini yönetme](manage-updates-for-vm.md).
* Genel Güncelleştirme Yönetimi hatalarıyla ilgili sorunları gidermek için bkz. [güncelleştirme yönetimi sorunlarını giderme](../troubleshoot/update-management.md).
* Windows Update Aracısı sorunlarını gidermek için bkz. [Windows Update Aracısı sorunlarını giderme](../troubleshoot/update-agent-issues.md).
* Linux Güncelleştirme Aracısı sorunlarını gidermek için bkz. [Linux Güncelleştirme Aracısı sorunlarını giderme](../troubleshoot/update-agent-issues-linux.md).