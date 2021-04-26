---
title: Azure Application Gateway sertifikasını yenileme
description: Bir uygulama ağ geçidi dinleyicisiyle ilişkili bir sertifikayı yenilemeyi öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622189"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway sertifikalarını Yenile

Bir noktada, TLS/SSL şifrelemesi için uygulama ağ geçidinizi yapılandırdıysanız sertifikalarınızı yenilemeniz gerekir.

Azure portal, Azure PowerShell ya da Azure CLı kullanarak bir dinleyiciyle ilişkili bir sertifikayı yenileyebilirsiniz:

## <a name="azure-portal"></a>Azure portalı

Portaldan bir dinleyici sertifikasını yenilemek için uygulama ağ geçidi dinleyicilerine gidin. Yenilenmesi gereken bir sertifikaya sahip dinleyiciyi seçin ve ardından **Yenile veya seçili sertifikayı Düzenle**' yi seçin.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="Sertifikayı Yenile":::

Yeni PFX sertifikanızı karşıya yükleyin, bir ad verin, parolayı yazın ve ardından **Kaydet**' i seçin.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell kullanarak sertifikanızı yenilemek için aşağıdaki betiği kullanın:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI’si

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Application Gateway ile TLS yük boşaltma yapılandırma hakkında bilgi edinmek için bkz. [TLS yük boşaltma yapılandırma](./create-ssl-portal.md)