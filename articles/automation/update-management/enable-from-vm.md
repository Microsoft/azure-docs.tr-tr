---
title: Azure VM 'den Azure Otomasyonu Güncelleştirme Yönetimi etkinleştirme
description: Bu makalede bir Azure VM 'den Güncelleştirme Yönetimi nasıl etkinleştirileceği açıklanır.
services: automation
ms.subservice: update-management
ms.date: 11/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 39b5b1f988a118e609015f19a086fda434797356
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99050271"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Azure VM’den Güncelleştirme Yönetimi’ni etkinleştirme

Bu makalede, bir veya daha fazla Azure sanal makinesi (VM) üzerinde [güncelleştirme yönetimi](overview.md) özelliğini nasıl etkinleştirebileceğinizi anlatmaktadır. Azure VM 'lerini ölçekli olarak etkinleştirmek için, Güncelleştirme Yönetimi kullanarak mevcut bir Azure VM 'yi etkinleştirmeniz gerekir.

> [!NOTE]
> Güncelleştirme Yönetimi etkinleştirilirken, bir Log Analytics çalışma alanını ve bir Otomasyon hesabını bağlamak için yalnızca belirli bölgeler desteklenir. Desteklenen eşleme çiftlerinin bir listesi için bkz. [Otomasyon hesabı ve Log Analytics çalışma alanı Için bölge eşleme](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.
* Makineleri yönetmek için [Otomasyon hesabı](../automation-security-overview.md).
* Bir [sanal makine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="enable-the-feature-for-deployment"></a>Dağıtım için özelliği etkinleştirin

1. [Azure Portal](https://portal.azure.com), **sanal makineler** ' i seçin veya giriş sayfasından **sanal makineler** ' i arayıp seçin.

2. Güncelleştirme Yönetimi etkinleştirmek istediğiniz VM 'yi seçin. Otomasyon hesabınızın konumu ne olduğuna bakılmaksızın, VM 'Ler herhangi bir bölgede bulunabilir. Siz

3. VM sayfasında, **işlemler** altında **Konuk + ana bilgisayar güncelleştirmeleri**' ni seçin.

    ![Sol bölmeden Konuk + ana bilgisayar güncelleştirmelerini seçin](media/enable-from-vm/select-guest-and-os-updates.png)

4. `Microsoft.OperationalInsights/workspaces/read`VM 'nin bir çalışma alanı için etkinleştirilip etkinleştirilmediğini belirleme izninizin olması gerekir. Gerekli ek izinler hakkında daha fazla bilgi edinmek için bkz. [makineleri etkinleştirmek için gereken izinler](../automation-role-based-access-control.md#feature-setup-permissions). Aynı anda birden çok makineyi etkinleştirmeyi öğrenmek için bkz. [Otomasyon hesabından güncelleştirme yönetimi etkinleştirme](./enable-from-automation-account.md).

5. Etkinleştir Güncelleştirme Yönetimi sayfasında, Log Analytics çalışma alanı ve Otomasyon hesabı ' nı seçin ve Güncelleştirme Yönetimi etkinleştirmek için **Etkinleştir** ' e tıklayın. Güncelleştirme Yönetimi etkinleştirildikten sonra, VM 'den güncelleştirme değerlendirmesini görüntüleyebilmeniz için 15 dakika sürebilir.

    ![Güncelleştirme Yönetimi’ni etkinleştirme](media/enable-from-vm/enable-update-management.png)

## <a name="next-steps"></a>Sonraki adımlar

* VM 'Ler için Güncelleştirme Yönetimi kullanmak için bkz. [Azure VM 'leriniz için güncelleştirmeleri ve düzeltme eklerini yönetme](manage-updates-for-vm.md).

* Genel Güncelleştirme Yönetimi hatalarıyla ilgili sorunları gidermek için bkz. [güncelleştirme yönetimi sorunlarını giderme](../troubleshoot/update-management.md).