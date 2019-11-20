---
title: Fiziksel sunucuları Azure Site Recovery kullanarak azure'a olağanüstü durum kurtarması için yapılandırma sunucusunu ayarlama | Microsoft Docs
description: Bu makalede şirket içi fiziksel sunucularını azure'a olağanüstü durum kurtarma için şirket içi yapılandırma sunucusu nasıl kurulur.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589049"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Fiziksel sunucuları azure'a olağanüstü durum kurtarması için yapılandırma sunucusunu ayarlama

Bu makalede, Azure'da Windows veya Linux çalıştıran fiziksel sunucuları çoğaltmaya başlamak için şirket içi ortamınızı ayarlama açıklar.

## <a name="prerequisites"></a>Önkoşullar

Sahip olduğunuz varsayılır:
- Bir kurtarma Hizmetleri kasası [Azure portalında](https://portal.azure.com "Azure portalında").
- Yapılandırma sunucusunu yüklemek için fiziksel bir bilgisayar.
- Yapılandırma sunucusunu yüklemekte makinede TLS 1.0 devre dışı bıraktık, TLs 1.2 etkin olduğundan ve .NET Framework 4.6 veya üzeri bir sürüm (etkin güçlü şifreleme ile) makinede yüklü emin olun. [Daha fazla bilgi edinin](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Yapılandırma sunucusunun en düşük gereksinimleri
Aşağıdaki tabloda, en düşük donanım, yazılım ve yapılandırma sunucusu için ağ gereksinimleri listelenmektedir.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Yapılandırma sunucusu tarafından HTTPS tabanlı Ara sunucular desteklenmez.

## <a name="choose-your-protection-goals"></a>Koruma hedeflerinizi seçme

1. Azure portalında Git **kurtarma Hizmetleri** dikey kasaları ve kasanızı seçin.
2. İçinde **kaynak** kasa menüsünü **Başlarken** > **Site Recovery** > **1. adım: Altyapıyı hazırlama** > **koruma hedefi**.

    ![Hedefleri seçme](./media/physical-azure-set-up-source/choose-goals.png)
3. İçinde **koruma hedefi**seçin **Azure'a** ve **sanallaştırılmamış/diğer**ve ardından **Tamam**.

    ![Hedefleri seçme](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Kaynak ortamı ayarlama

1. İçinde **kaynağı hazırla**,'a tıklayın, bir yapılandırma sunucusu yoksa, **+ yapılandırma sunucusu** eklemek için.

   ![Kaynağı ayarlama](./media/physical-azure-set-up-source/plus-config-srv.png)
2. İçinde **Sunucusu Ekle** dikey penceresinde bu maddeyi **yapılandırma sunucusu** görünür **sunucu türü**.
4. Site Recovery birleşik Kurulumu yükleme dosyasını indirin.
5. Kasa kayıt anahtarını indir Birleşik Kurulum'u çalıştırdığınızda, kayıt anahtarı gerekir. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

    ![Kaynağı ayarlama](./media/physical-azure-set-up-source/set-source2.png)
6. Yapılandırma sunucusu olarak kullandığınız makinede çalıştırma **Azure Site Recovery birleşik Kurulumu** yapılandırma sunucusu, işlem sunucusu ve ana hedef sunucusu yüklemek için.

#### <a name="run-azure-site-recovery-unified-setup"></a>Çalıştırma Azure Site Recovery birleşik Kurulumu

> [!TIP]
> Bilgisayarınızın sistem saati yerel saat alanlarını beş dakikadan fazla ise yapılandırma sunucusu kaydı başarısız olur. Sistem saatinizi bir [saat sunucusu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) yüklemeyi başlatmadan önce.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Yapılandırma sunucusu, bir komut satırı yüklenebilir. [Daha fazla bilgi edinin](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Genel sorunlar

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım içerir [hedef ortamınızı ayarlarken](physical-azure-set-up-target.md) azure'da.
