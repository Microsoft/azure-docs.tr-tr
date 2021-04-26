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
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244934"
---
Aşağıdaki adımlar için aşağıdaki yapılandırma kullanılmıştır:

- Bilgisayar: Ubuntu Server 18,04
- Bağımlılıklar: strongSwan


Gerekli Strongswa yapılandırmasını yüklemek için aşağıdaki komutları kullanın:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Azure komut satırı arabirimini yüklemek için aşağıdaki komutu kullanın:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Azure CLı 'yı yüklemeye ilişkin ek yönergeler](/cli/azure/install-azure-cli-apt)