---
title: Azure Güvenlik Duvarı etkin FTP desteği
description: Azure Güvenlik duvarında etkin FTP varsayılan olarak devre dışıdır. PowerShell, CLı ve ARM şablonunu kullanarak etkinleştirebilirsiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864478"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Güvenlik Duvarı etkin FTP desteği

Etkin FTP ile FTP sunucusu, belirlenen FTP istemci veri bağlantı noktasına veri bağlantısını başlatır. İstemci tarafı ağdaki güvenlik duvarları normalde iç istemci bağlantı noktasına bir dış bağlantı isteğini engeller. Daha fazla bilgi için, bkz. [ACTIVE FTP vs. pasıf FTP ve kesin bir açıklama](https://slacksite.com/other/ftp.html).

Varsayılan olarak, FTP komutu kullanılarak FTP sıçrama saldırılarına karşı korumak için etkin FTP desteği Azure Güvenlik duvarında devre dışıdır `PORT` . Ancak, Azure PowerShell, Azure CLı veya Azure ARM şablonunu kullanarak dağıtırken etkin FTP 'yi etkinleştirebilirsiniz.

Etkin mod FTP 'yi desteklemek için aşağıdaki TCP bağlantı noktalarının açılması gerekir:

- FTP sunucusunun bağlantı noktası 21 her yerde (istemci bağlantı başlatır)
- FTP sunucusunun bağlantı noktası 21-bağlantı noktaları > 1023 (sunucu istemcinin denetim bağlantı noktasına yanıt verir)
- FTP sunucusunun bağlantı noktası 20 ila istemciler üzerinde 1023 > (sunucu, istemcinin veri bağlantı noktasına veri bağlantısı başlatır)
- Sunucuda 1023 numaralı bağlantı > noktalarından FTP sunucusunun bağlantı noktası 20 ' si (istemci, sunucunun veri bağlantı noktasında ACK 'ler gönderir)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kullanarak dağıtmak için `AllowActiveFTP` parametresini kullanın. Daha fazla bilgi için bkz. [ETKIN FTP 'ye Izin ver Ile güvenlik duvarı oluşturma](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

Azure CLı kullanarak dağıtmak için `--allow-active-ftp` parametresini kullanın. Daha fazla bilgi için bkz. [az Network Firewall Create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) şablonu

ARM şablonunu kullanarak dağıtmak için `AdditionalProperties` alanını kullanın:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Daha fazla bilgi için bkz. [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik duvarının nasıl dağıtılacağını öğrenmek için, bkz. [Azure PowerShell kullanarak Azure Güvenlik Duvarı dağıtma ve yapılandırma](deploy-ps.md).