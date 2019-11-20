---
title: EDIOLGU iletilerinde UNH 2,5 segmentleri-Azure Logic Apps
description: Enterprise Integration Pack ile Azure Logic Apps içindeki UNH 2.5 kesimlerle EDIOLGU iletilerini çözümleyin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c30c35375a45171c938f80e94dd7d9be4c3ee8b1
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679952"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Azure Logic Apps 'de UNH 2.5 segmentiyle EDIOLGU belgelerini işleme

Bir EDIOLGU belgesinde bir UNH 2.5 segmenti varsa, segment şema araması için kullanılır. Örneğin, bu örnek EDIOLGU iletisinde UNH alanı `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Bu iletiyi işlemek için aşağıda açıklanan adımları izleyin:

1. Şemayı güncelleştirin.

1. Anlaşma ayarlarını kontrol edin.

## <a name="update-the-schema"></a>Şemayı güncelleştirme

İletiyi işlemek için, UNH 2.5 kök düğüm adına sahip bir şemayı dağıtmanız gerekir. Örneğin, örnek UNH alanı için şema kök adı `EFACT_D03B_ORDERS_EAN008`. Farklı bir UNH 2.5 kesimine sahip olan her bir `D03B_ORDERS` için, tek bir şemayı dağıtmanız gerekir.

## <a name="add-schema-to-edifact-agreement"></a>EDıOLGU anlaşmasına şema ekle

### <a name="edifact-decode"></a>EDIOLGU kod çözme

Gelen iletinin kodunu çözmek için, EDıOLGU sözleşmesinin alma ayarlarında şemayı ayarlayın:

1. [Azure Portal](https://portal.azure.com)tümleştirme hesabınızı açın.

1. Şemayı tümleştirme hesabınıza ekleyin.

1. Yapılandırma, EDıOLGU sözleşmesinin alma ayarlarında yapılandırılır.

1. EDIOLGU Sözleşmesi ' ni seçin ve **JSON olarak Düzenle**' yi seçin. Alma sözleşmesinin `schemaReferences` bölümüne UNH 2.5 değerini ekleyin:

   ![Sözleşmeyi almak için UNH 2.5 ekleyin](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIOLGU kodlama

Gelen iletiyi kodlamak için, bu şemayı EDıOLGU sözleşmesi gönderme ayarları ' nda yapılandırın

1. [Azure Portal](https://portal.azure.com)tümleştirme hesabınızı açın.

1. Şemayı tümleştirme hesabınıza ekleyin.

1. EDıOLGU sözleşmesinin gönderme ayarlarındaki şemayı yapılandırın.

1. EDIOLGU Sözleşmesi ' ni seçin ve **JSON olarak Düzenle**' ye tıklayın.  Gönderme sözleşmesi 'nde UNH 2.5 değeri ekleyin **schemaReferences**

1. EDIOLGU Sözleşmesi ' ni seçin ve **JSON olarak Düzenle**' yi seçin. UNH 2.5 değerini gönderme sözleşmesinin `schemaReferences` bölümüne ekleyin:

   ![Anlaşmayı göndermek için UNH 2.5 ekleyin](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Tümleştirme hesabı sözleşmeleri](../logic-apps/logic-apps-enterprise-integration-agreements.md) hakkında daha fazla bilgi edinin