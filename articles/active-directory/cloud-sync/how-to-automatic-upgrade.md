---
title: 'Azure AD Connect bulut sağlama Aracısı: otomatik yükseltme | Microsoft Docs'
description: Bu makalede, Azure AD Connect bulut sağlama aracısında yerleşik otomatik yükseltme özelliği açıklanır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c0b47dd358826b843143aaf23c469d852e93b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614280"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect bulut sağlama Aracısı: otomatik yükseltme

Azure Active Directory (Azure AD) bulut sağlama Aracısı yüklemesinin her zaman güncel olduğundan emin olmak otomatik yükseltme özelliği ile oldukça kolaydır.

Aracı buradan yüklendi: "program files\Azure AD Connect sağlama Agent\AADConnectProvisioningAgent.exe"

Sürümünüzü doğrulamak için, yürütülebilir dosyaya sağ tıklayın ve Özellikler ' i ve ardından Ayrıntılar ' ı seçin.

![Aracı dosyası sürümü](media/how-to-automatic-upgrade/agent-1.png)

Aracı güncelleştiricisi buradan yüklendi: "program files\Azure AD Connect sağlama Aracısı Updater\AzureADConnectAgentUpdater.exe"

Sürümünüzü doğrulamak için, yürütülebilir dosyaya sağ tıklayın ve Özellikler ' i ve ardından Ayrıntılar ' ı seçin.

![Aracı Güncelleştirici sürümü](media/how-to-automatic-upgrade/agent-2.png)

## <a name="uninstall-the-agent"></a>Aracıyı kaldırın
Aracıyı kaldırmak için, **Kaldır ' a gidin veya bir programı değiştirin** ve şunları kaldırın:

- **Microsoft Azure AD Aracısı güncelleştiricisi bağlama**
- **Microsoft Azure AD sağlama aracısına bağlanma**
- **Microsoft Azure AD sağlama Aracısı paketini bağlama**

![Aracı kaldırma](media/how-to-automatic-upgrade/agent-3.png)

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)

