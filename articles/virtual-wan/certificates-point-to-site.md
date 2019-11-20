---
title: Azure sanal WAN Kullanıcı VPN bağlantıları için sertifikaları oluşturma ve dışarı aktarma | Microsoft Docs
description: Windows 10 veya Windows Server 2016 ' de otomatik olarak imzalanan bir kök sertifika oluşturun, ortak anahtarı dışarı aktarın ve PowerShell 'i kullanarak istemci sertifikaları oluşturun.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514920"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Sanal WAN Kullanıcı VPN bağlantıları için sertifikaları oluşturma ve dışarı aktarma

Kullanıcı VPN bağlantıları, kimlik doğrulaması için sertifikaları kullanır. Bu makalede, otomatik olarak imzalanan bir kök sertifika oluşturma ve Windows 10 veya Windows Server 2016 ' de PowerShell kullanarak istemci sertifikaları oluşturma işlemlerinin nasıl yapılacağı gösterilir.

Bu makaledeki adımları Windows 10 veya Windows Server 2016 çalıştıran bir bilgisayarda gerçekleştirmeniz gerekir. Sertifika oluşturmak için kullandığınız PowerShell cmdlet 'leri işletim sisteminin bir parçasıdır ve Windows 'un diğer sürümlerinde çalışmaz. Windows 10 veya Windows Server 2016 bilgisayarı yalnızca sertifikaları oluşturmak için gereklidir. Sertifikalar oluşturulduktan sonra, bunları karşıya yükleyebilir veya desteklenen herhangi bir istemci işletim sistemine yükleyebilirsiniz.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

[Kullanıcı VPN bağlantısı Için sanal WAN adımlarıyla](virtual-wan-about.md) devam edin
