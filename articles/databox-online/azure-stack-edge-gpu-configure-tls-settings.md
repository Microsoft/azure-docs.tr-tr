---
title: Windows istemcilerinde TLS 1,2 'yi Yapılandırma Azure Stack Edge Pro GPU cihazına erişme
description: Windows istemcilerinde Azure Stack Edge Pro GPU cihazına erişen TLS 1,2 ' nin nasıl yapılandırılacağı açıklanmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 47a6d3bbebdf3b2b14b1c40d7ea8fc93b4d19c6f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565342"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro cihazına erişen Windows istemcilerinde TLS 1,2 yapılandırma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro cihazınıza erişmek için bir Windows istemcisi kullanıyorsanız, istemcinizin TLS 1,2 ' i yapılandırmanız gerekir. Bu makalede, Windows istemcisinde TLS 1,2 ' i yapılandırmaya yönelik kaynaklar ve yönergeler sağlanmaktadır. 

Burada sunulan yönergeler, Windows Server 2016 çalıştıran bir istemcide gerçekleştirilen teste dayalıdır.

## <a name="configure-tls-12-for-current-powershell-session"></a>Geçerli PowerShell oturumu için TLS 1,2 yapılandırma

İstemcinizi TLS 1,2 ' i yapılandırmak için aşağıdaki adımları uygulayın.

1. PowerShell 'i yönetici olarak çalıştırın.
2. Geçerli PowerShell oturumunda TLS 1,2 ayarlamak için şunu yazın:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>İstemcide TLS 1,2 yapılandırma

Ortamınız için sistem genelinde TLS 1,2 ayarlamak istiyorsanız, bu belgelerdeki yönergeleri izleyin:

- [Genel-TLS 1,2 nasıl etkinleştirilir](/windows-server/security/tls/tls-registry-settings#tls-12)
- [İstemcilerde TLS 1,2 nasıl etkinleştirilir](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Site sunucularında ve uzak site sistemlerinde TLS 1,2 'yi etkinleştirme](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [TLS/SSL (Schannel SSP) protokolleri](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Şifre paketleri](/windows-server/security/tls/tls-registry-settings#tls-12): özellikle [TLS şifre paketi sırasını yapılandırma](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) şu listeden geçerli şifre paketlerinizi listelediğinizden ve yok edin:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Kayıt defteri ayarlarını doğrudan düzenleyerek da bu şifre paketlerini ekleyebilirsiniz.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Elips eğrileri ayarlama

    Şu listeden geçerli elips eğrilerini listelediğinizden ve yok edin.

    - P-256 
    - P-384

    Bu elips eğrileri de kayıt defteri ayarlarını doğrudan düzenleyerek ekleyebilirsiniz.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [MIN RSA anahtar değişim boyutunu 2048 olarak ayarlayın](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Sonraki adımlar

[Azure Resource Manager Bağlan](./azure-stack-edge-gpu-connect-resource-manager.md)