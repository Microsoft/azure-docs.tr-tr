---
title: Azure Sertifikalı cihaz programı-öğretici-sertifika programınızı seçme
description: Cihazınız için doğru sertifika programlarını seçmeye yönelik adım adım kılavuz
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: b6ab8a4f971a065764731abddf72e26c628ba6cb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969804"
---
# <a name="tutorial-select-your-certification-program"></a>Öğretici: sertifika programınızı seçin

Azure Sertifikalı cihaz programını seçerken Tebrikler! Sertifikalı cihazların ekosistemimize katılmanız bizim için heyecanlıyız. Başlamak için önce hangi sertifika programlarının cihaz olanaklarınıza en iyi şekilde uygun olduğunu belirlemelisiniz.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Cihazınız için en iyi sertifika programını seçin

## <a name="selecting-a-certification-program-for-your-device"></a>Cihazınız için bir sertifika programı seçme

Tüm cihazlar, [**Azure Sertifikalı cihaz**](./program-requirements-azure-certified-device.md) sertifikası tarafından belirtilen temel gereksinimleri karşılamak için gereklidir. Diğer üç sertifika rozetleri bu programda derleme ve farklı bir müşteri değeri sunma. Üç artımlı rozetden birini veya daha fazlasını seçebilirsiniz (IoT Tak ve Kullan, Edge yönetimli ve Edge güvenli çekirdek * Önizleme).

1. Aşağıdaki tabloda bulunan her sertifika programı gereksinimini gözden geçirin. Her program için ayrıntılı gereksinimler, başlıklardan eklenebilir.

    |Gereksinim|[IoT Tak Çalıştır](./program-requirements-edge-secured-core.md)|[Sınır yönetiliyor](./program-requirements-edge-managed.md)|[Edge güvenli-çekirdek](./program-requirements-edge-secured-core.md)|
    ---|---|---|---
    | İşlemci | Herhangi biri|MPU/CPU|MPU/CPU|
    | İşletim Sistemi | Herhangi biri|[Katman 1 işletim sistemi](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true))|[Katman 1 işletim sistemi](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    | IoT Edge çalışma zamanı | Desteklenmez |Gerekli|Gerekli|
    | IoT için Defender | Desteklenmez|Gerekli|Gerekli|
    | ADU/Windows Update | Desteklenmez|Gerekli|Gerekli|

1. Cihazınızın ne gerektiğini anladığınızı öğrendikten sonra, programın teknik gereksinimlerini gözden geçirin. Bu, Azure Sertifikalı cihaz sertifikası veya üç artımlı rozetden biriyle temel sertifikanın bir birleşimi olabilir. 

## <a name="next-steps"></a>Sonraki adımlar

Şimdi cihazınızı oluşturmaya başlamaya hazırsınız! Projenize başlamak için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
>[Öğretici: projenizi oluşturma](tutorial-01-creating-your-project.md)
