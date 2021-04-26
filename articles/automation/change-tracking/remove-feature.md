---
title: Azure Otomasyonu Değişiklik İzleme ve envanter özelliğini kaldırma
description: Bu makalede Değişiklik İzleme ve envanterin kullanımını durdurmayı ve bir Otomasyon hesabının Log Analytics çalışma alanından nasıl bağlantısını nasıl durdurulacağı açıklanmaktadır.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92210456"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Otomasyon hesabından Değişiklik İzleme ve stoku kaldırma

Azure Otomasyonu Değişiklik İzleme ve envanterini kullanarak sanal makinelerinizin yönetimini etkinleştirdikten sonra, yapılandırmayı durdurmayı ve hesabı ve bağlantılı Log Analytics çalışma alanından kaldırmayı seçebilirsiniz. Bu makalede, yönetilen VM 'Ler, Otomasyon hesabınız ve Log Analytics çalışma alanınızın Değişiklik İzleme ve envanterini tamamen nasıl kaldıracakları açıklanır.

## <a name="sign-into-the-azure-portal"></a>Azure portalda oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="remove-management-of-vms"></a>VM 'lerin yönetimini kaldırma

Değişiklik İzleme ve envanteri kaldırmadan önce, VM 'lerinizi yönetmeyi durdurmanız gerekir. Özelliğin kaydını silmek için [değişiklik izleme VM 'Leri kaldırma](remove-vms-from-change-tracking.md) bölümüne bakın.

## <a name="remove-changetracking-solution"></a>ChangeTracking çözümünü kaldır

Otomasyon hesabının çalışma alanından bağlantısını kaldırabilmeniz için önce Değişiklik İzleme ve stoku tamamen kaldırmak üzere bu adımları izlemeniz gerekir. Değişiklik **izleme çözümünü çalışma** alanından kaldıracaksınız.

1. Azure portal, **tüm hizmetler**' i seçin. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, giriş listenize göre öneriler uygular. **Log Analytics**’i seçin.

2. Log Analytics çalışma alanları listenizde Değişiklik İzleme ve envanteri etkinleştirdiğinizde seçtiğiniz çalışma alanını seçin.

3. Sol tarafta **çözümler**' i seçin.  

4. Çözümler listesinde, **ChangeTracking (çalışma alanı adı)** öğesini seçin. Çözümün **genel bakış** sayfasında **Sil**' i seçin. Onaylamanız istendiğinde **Evet**' i seçin.

## <a name="unlink-workspace-from-automation-account"></a>Çalışma alanının Otomasyon hesabından bağlantısını kaldırma

1. Azure portal **Otomasyon hesapları**' nı seçin.

2. Otomasyon hesabınızı açın ve sol taraftaki **Ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

3. **Çalışma alanının bağlantısını kaldır** sayfasında, **çalışma alanının bağlantısını kaldır** ' ı seçin ve istemlere yanıtlayın.

   ![Çalışma alanının bağlantısını Kaldır sayfası](media/remove-feature/automation-unlink-workspace-blade.png)

Log Analytics çalışma alanının bağlantısını kesmeyi denediğinde, menüdeki **Bildirimler** altında ilerlemeyi izleyebilirsiniz.

Alternatif olarak, Log Analytics çalışma alanınızın, çalışma alanının içinden Otomasyon hesabınızla bağlantısını kaldırabilirsiniz:

1. Azure Portal'da **Log Analytics**'i seçin.

2. Çalışma alanından **Ilgili kaynaklar** altında **Otomasyon hesabı** ' nı seçin.

3. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır**' ı seçin.

Otomasyon hesabının bağlantısını kaldırmayı denediğinde, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu özelliği yeniden etkinleştirmek için bkz. [Otomasyon hesabından değişiklik izleme ve envanteri etkinleştirme](enable-from-automation-account.md), [Azure portal göz atarak değişiklik izleme ve envanteri etkinleştirme](enable-from-portal.md), [runbook 'tan değişiklik izleme ve envanteri ETKINLEŞTIRME](enable-from-runbook.md)ya da [bir Azure VM 'den değişiklik izleme ve envanteri etkinleştirme](enable-from-vm.md).
