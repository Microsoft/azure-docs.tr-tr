---
title: Kullanılmayan ıTSM bağlayıcılarını Sil
description: Bu makalede, ıTSM bağlayıcılarının ve onunla ilişkili eylem gruplarının nasıl silineceği hakkında bir açıklama sunulmaktadır.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387947"
---
# <a name="delete-unused-itsm-connectors"></a>Kullanılmayan ıTSM bağlayıcılarını Sil

Kullanılmayan BT hizmet yönetimi (ıTSSM) bağlayıcılarını silme işleminin iki aşaması vardır. ITSM bağlayıcısıyla ilişkili tüm eylemleri siler ve sonra bağlayıcının kendisini silersiniz. Bağlayıcı olmayan eylemler aboneliğinizde hatalara neden olabileceğinden, önce eylemleri silersiniz.

## <a name="delete-associated-actions"></a>İlişkili eylemleri Sil

1. Azure portal, **İzle**' yi seçin.
  
    ![Izleyici seçiminin ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. **Uyarıları** seçin.
   
    ![Uyarı seçiminin ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. **Işlemleri Yönet**' i seçin.
   
    ![Işlemleri Yönet seçiminin ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Silmek istediğiniz ıTSM bağlayıcısıyla ilişkili bir eylem grubu seçin. Bu makalede bir Cherwell Connector örneği kullanılmaktadır.
   
    ![Cherwell bağlayıcısıyla ilişkili eylemlerin ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Bilgileri gözden geçirin ve ardından **Eylem grubunu sil**' i seçin.

    ![Eylem grubu bilgilerinin ve Grup silme düğmesinin ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Bağlayıcıyı silme

1. Arama çubuğunda, **ServiceDesk** için arama yapın. Sonra kaynak listesinden **ServiceDesk** ' yi seçin.

    ![ServiceDesk arama ve seçme ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. **ITSM bağlantıları**' nı seçin ve ardından Cherwell bağlayıcısını seçin.

    ![Cherwell ı T Bağlayıcısı 'nın ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. **Sil**’i seçin.

    ![I T S M bağlayıcısının Sil düğmesinin ekran görüntüsü.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Sonraki adımlar

* [ITSM bağlayıcısında sorun giderme](./itsmc-resync-servicenow.md)
