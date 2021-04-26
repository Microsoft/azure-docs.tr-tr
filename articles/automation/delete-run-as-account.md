---
title: Azure Otomasyonu farklı çalıştır hesabını silme
description: Bu makalede, PowerShell ile veya Azure portal farklı çalıştır hesabının nasıl silineceği açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056152"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Azure Otomasyonu farklı çalıştır hesabını silme

Azure Otomasyonu 'nda farklı çalıştır hesapları, Otomasyon Runbook 'larını ve diğer otomasyon özelliklerini kullanarak Azure Resource Manager veya Azure klasik dağıtım modelinde kaynakları yönetmeye yönelik kimlik doğrulaması sağlar. Bu makalede, farklı çalıştır veya klasik farklı çalıştır hesabının nasıl silineceği açıklanır. Bu eylemi gerçekleştirdiğinizde Otomasyon hesabı korunur. Farklı Çalıştır hesabını sildikten sonra, Azure portal veya belirtilen PowerShell betiği ile yeniden oluşturabilirsiniz.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silme

1. Azure portalında Otomasyon hesabınızı açın.

2. Sol bölmede hesap ayarları bölümünde **Farklı Çalıştır hesapları** ' nı seçin.

3. Farklı Çalıştır Hesapları özellikleri sayfasında silmek istediğiniz Farklı Çalıştır Hesabını veya Klasik Farklı Çalıştır Hesabını seçin.

4. Seçili hesabın Özellikler bölmesinde **Sil**' e tıklayın.

   ![Farklı Çalıştır hesabını silme](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Hesap silinirken menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Farklı Çalıştır veya klasik farklı çalıştır hesabınızı yeniden oluşturmak için bkz. [Farklı Çalıştır hesapları oluşturma](create-run-as-account.md).