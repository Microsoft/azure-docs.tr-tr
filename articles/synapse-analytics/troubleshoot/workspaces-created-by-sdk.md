---
title: "Sorun giderme: SDK tarafından oluşturulan çalışma alanları SYNAPSE Studio 'Yu başlatamıyor"
description: SDK tarafından oluşturulan çalışma alanlarını çözümleme adımları SYNAPSE Studio 'Yu başlatamadı
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466952"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>SDK kullanılarak oluşturulan Azure SYNAPSE Analytics çalışma alanları sorunlarını giderme

Bu makalede, bir yazılım geliştirme seti (SDK) ile oluşturulmuş bir Synapse çalışma alanından SYNAPSE Studio 'Yu başlatmaya yönelik sorun giderme adımları sunulmaktadır.


## <a name="prerequisites"></a>Önkoşullar

- SDK kullanılarak oluşturulan SYNAPSE çalışma alanı

## <a name="workaround"></a>Geçici çözüm

SDK tarafından oluşturulan çalışma alanından SYNAPSE Studio 'Yu başlatmak için aşağıdaki adımları izleyin: 
  1.    Çalıştırarak çalışma alanı oluşturun `az synapse workspace create` .
  2.    Çalıştırarak yönetilen kimlik KIMLIĞINI ayıklayın `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    ' İ çalıştırarak, yönetilen kimlik depolama hesabına Depolama Blobu veri katılımcısı rolü verin ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Çalıştırarak güvenlik duvarı kuralı ekleyin ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Sonraki adımlar

* [Azure SYNAPSE nedir?](../overview-what-is.md)
* [Başlarken](../get-started.md)
