---
title: Azure HPC önbelleği için Azure CLı önkoşulları
description: Azure CLı 'yı kullanarak bir Azure HPC önbelleği oluşturmak veya değiştirmek için kurulum adımları
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0b8e1158bc60c4cceea508db988000fe952a90a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864298"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure HPC Önbelleği için Azure CLI’yı ayarlama

Azure HPC önbelleği oluşturmak veya yönetmek için Azure CLı kullanmadan önce ortamınızı hazırlamak üzere bu adımları izleyin.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache, Azure CLı 'nın 2,7 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="set-default-resource-group-optional"></a>Varsayılan kaynak grubunu ayarla (isteğe bağlı)

HPC-Cache komutlarının çoğu, önbelleğin kaynak grubunu geçirmenize gerek duyar. Varsayılan kaynak grubunu [az configure](/cli/azure/reference-index#az_configure)kullanarak ayarlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı uzantısını yükledikten ve oturum açarak Azure HPC önbellek sistemleri oluşturup yönetmek için Azure CLı kullanabilirsiniz.

* [Azure HPC önbelleği oluşturma](hpc-cache-create.md)
* [Azure CLı HPC-önbellek belgeleri](/cli/azure/hpc-cache)
