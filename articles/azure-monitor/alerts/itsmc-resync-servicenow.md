---
title: ServiceNow eşitleme sorunlarını el ile çözme
description: ServiceNow bağlantısını sıfırlayın Microsoft Azure içindeki uyarılar ServiceNow 'ı çağırabilir
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: 664f1522775d96b813b7cd99bdffdb26630497f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037447"
---
# <a name="how-to-manually-fix-sync-problems"></a>Eşitleme sorunlarını el ile çözme

Azure Izleyici, üçüncü taraf BT hizmet yönetimi (ıSM) sağlayıcılarına bağlanabilir. ServiceNow, bu sağlayıcılardan biridir.

Güvenlik nedenleriyle, ServiceNow ile bağlantınız için kullanılan kimlik doğrulama belirtecini yenilemeniz gerekebilir.
Bağlantıyı yeniden etkinleştirmek ve belirteci yenilemek için aşağıdaki eşitleme sürecini kullanın:

1. Üst arama başlığında çözümü arayın ve ilgili çözümleri seçin

    ![En üstteki arama başlığını ve ilgili çözümlerin nerede seçdiğinin gösterildiği ekran görüntüsü.](media/itsmc-resync-servicenow/solution-search-8-bit.png)

1. Çözüm ekranında, abonelik filtresindeki "Tümünü Seç" i seçin ve ardından "ServiceDesk" olarak filtreleyin

    ![Tümünü Seç ' in nerede ve ServiceDesk tarafından filtreleneceğini gösteren ekran görüntüsü.](media/itsmc-resync-servicenow/solutions-list-8-bit.png)

1. ITSM bağlantınızın çözümünü seçin.
1. Sol başlıkta ıTSM bağlantısı ' nı seçin.

    ![ITSM bağlantılarının nerede seçdiğinin gösterildiği ekran görüntüsü.](media/itsmc-resync-servicenow/itsm-connector-8-bit.png)

1. Listedeki her bağlayıcıyı seçin. 
    1. Yapılandırmak için bağlayıcı adına tıklayın
    1. Artık kullanımda olmayan bağlayıcıları Sil

    1. İş ortağı türüne göre [bu tanımlara](./itsmc-connections.md) göre alanları güncelleştirin

    1. Eşitle 'ye tıklayın

       ![Eşitle düğmesini vurgulayan ekran görüntüsü.](media/itsmc-resync-servicenow/resync-8-bit-2.png)

    1. Kaydet 'e tıklayın

        ![Yeni bağlantı](media/itsmc-resync-servicenow/save-8-bit.png)

f.    İşlemin başlayıp başlamadığına bakmak için bildirimleri gözden geçirin.
