---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69520890"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Noktadan siteye bağlantı için sertifika oluşturmak üzere kendi iç PKI kök sertifika yetkilimi kullanabilir miyim?

Evet. Önceden, yalnızca otomatik olarak imzalanan kök sertifikalar kullanılabiliyordu. 20 kök sertifika yükleyebilirsiniz.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Azure Key Vault sertifikaları kullanabilir miyim?

Hayır.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Sertifikaları oluşturmak için hangi araçları kullanabilirim?

Kurumsal PKI çözümünüzü (dahili PKI'nizi), Azure PowerShell'i, MakeCert'i ve OpenSSL'yi kullanabilirsiniz.

### <a name="certsettings"></a>Sertifika ayarları ve parametreler için yönergeler var mı?

* **Dahili PKI/Kurumsal PKI çözümü:** [Sertifikaları oluşturma](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) adımlarına bakın.

* **Azure PowerShell:** Adımlar için [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) makalesine bakın.

* **MakeCert:** Adımlar için [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) makalesine bakın.

* **OpenSSL:** 

    * Sertifikaları dışarı aktarırken kök sertifikayı Base64'e dönüştürdüğünüzden emin olun.

    * İstemci sertifikası için:

      * Özel anahtarı oluştururken uzunluğu 4096 olarak belirtin.
      * Sertifikayı oluştururken, *-extensions* parametresini *usr_cert* olarak belirtin.
