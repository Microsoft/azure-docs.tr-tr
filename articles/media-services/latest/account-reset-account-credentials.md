---
title: Hesap kimlik bilgilerinizi sıfırlayın-CLı
description: Azure CLI betiğini kullanarak hesabınızın kimlik bilgilerini sıfırlayın ve app.config ayarlarını geri alın.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: d5604f177484d33255d2923d72b00fae124c0f9a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783602"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLı örneği: hesap kimlik bilgilerini sıfırlayın

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makaledeki Azure CLI betiği, hesabınızın kimlik bilgilerini sıfırlamayı ve app.config ayarlarını geri almayı gösterir.

## <a name="prerequisites"></a>Önkoşullar

[Media Services hesabı oluşturun](./create-account-howto.md).

## <a name="example-script"></a>Örnek betik

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Sonraki adımlar

* [az AMS](/cli/azure/ams)
* [Kimlik bilgilerini sıfırlama](/cli/azure/ams/account/sp#az_ams_account_sp_reset_credentials)
