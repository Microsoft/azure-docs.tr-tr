---
title: Azure Site Recovery kullanarak fiziksel sunucuların olağanüstü durum kurtarması için yapılandırma sunucusunu ayarlayın | Microsoft Docs '
description: Bu makalede, şirket içi fiziksel sunucuların Azure 'a olağanüstü durum kurtarması için şirket içi yapılandırma sunucusunun nasıl ayarlanacağı açıklanır.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 327e995a8fe2f66903548fba054804768d2538ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001582"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Fiziksel sunucuların Azure'a olağanüstü durum kurtarması gerçekleştirmesi için yapılandırma sunucusunu ayarlama

Bu makalede, şirket içi ortamınızı Windows veya Linux çalıştıran fiziksel sunucuları Azure 'a çoğaltmaya başlayacak şekilde ayarlama açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Makalesinde zaten sahip olduğunuz varsayılır:
- [Azure Portal](https://portal.azure.com "Azure portalı")bir kurtarma hizmetleri Kasası.
- Yapılandırma sunucusunun yükleneceği fiziksel bilgisayar.
- Yapılandırma sunucusunu yüklemekte olduğunuz makinede TLS 1,0 ' yi devre dışı bırakırsanız, TLs 1,2 ' nin etkinleştirildiğinden ve .NET Framework sürüm 4,6 veya üzeri makinede yüklü olduğundan emin olun (güçlü şifreleme etkin). [Daha fazla bilgi edinin](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Yapılandırma sunucusu minimum gereksinimleri
Aşağıdaki tabloda bir yapılandırma sunucusu için en düşük donanım, yazılım ve ağ gereksinimleri listelenmektedir.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS tabanlı ara sunucular yapılandırma sunucusu tarafından desteklenmez.

## <a name="choose-your-protection-goals"></a>Koruma hedeflerinizi seçin

1. Azure portal, **Kurtarma Hizmetleri** kasaları dikey penceresine gidip kasanızı seçin.
2. Kasanın **kaynak** menüsünde **, başlangıç**  >  **Site Recovery**  >  **1. Adım: altyapı**  >  **koruma hedefini** hazırlama ' ya tıklayın.

    ![Koruma hedefinin nerede seçdiğinin gösterildiği ekran görüntüsü.](./media/physical-azure-set-up-source/choose-goals.png)
3. **Koruma hedefi**' nde, **Azure 'a** ve **sanallaştırılmamış/diğer**' i seçin ve ardından **Tamam**' a tıklayın.

    ![Hedefleri seçme](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Kaynak ortamı ayarlama

1. **Kaynağı hazırla** bölümünde, bir yapılandırma sunucunuz yoksa, bir tane eklemek Için **+ yapılandırma sunucusu** ' na tıklayın.

   ![Yapılandırma sunucusunun nasıl seçdiğinin gösterildiği ekran görüntüsü.](./media/physical-azure-set-up-source/plus-config-srv.png)
2. **Sunucu Ekle** dikey penceresinde, **sunucu türü**' nde **Configuration Server** ' ın göründüğünden emin olun.
4. Site Recovery Birleşik kurulum yükleme dosyasını indirin.
5. Kasa kayıt anahtarını indirin. Birleşik kurulumu çalıştırdığınızda kayıt anahtarı gereklidir. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

    ![Kaynağı ayarlama](./media/physical-azure-set-up-source/set-source2.png)
6. Yapılandırma sunucusu olarak kullandığınız makinede, yapılandırma sunucusunu, işlem sunucusunu ve ana hedef sunucuyu yüklemek için **Azure Site Recovery Birleşik kurulum 'u** çalıştırın.

#### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery Birleşik kurulum 'U Çalıştır

> [!TIP]
> Bilgisayarınızın sistem saatindeki sürenin yerel saatten beş dakikadan uzun olması durumunda yapılandırma sunucusu kaydı başarısız olur. Yüklemeyi başlatmadan önce sistem saatinizi bir [saat sunucusuyla](/windows-server/networking/windows-time-service/windows-time-service-top) eşitler.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Yapılandırma sunucusu bir komut satırı ile yüklenebilir. [Daha fazla bilgi edinin](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Genel sorunlar

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım Azure 'da [hedef ortamınızı ayarlamayı](physical-azure-set-up-target.md) içerir.
