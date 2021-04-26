---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devex-tag-azurecli
ms.openlocfilehash: 1a22c63eb01abc3775b2bf299d8464323e69c372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386707"
---
1. [az login](/cli/azure/) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin. Oturum açma hakkında daha fazla bilgi için bkz. [Azure CLI kullanmaya başlama](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Birden çok Azure aboneliğiniz varsa, hesabın aboneliklerini listeleyin.

   ```azurecli
   az account list --all
   ```
3. Kullanmak istediğiniz aboneliği belirtin.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
