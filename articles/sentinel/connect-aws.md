---
title: AWS Cloudtraizizi Azure Sentinel 'e bağlayın | Microsoft Docs
description: AWS Cloudiz verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 61bee1618092788da33549ec322dc4ab7130de16
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244688"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Azure Sentinel 'i AWS Cloudtrato 'a bağlama

AWS Cloudiz olaylarınızın tümünü Azure Sentinel 'e göndermek için AWS bağlayıcısını kullanın. Bu bağlantı işlemi, AWS Cloudtraı ve Azure Sentinel arasında bir güven ilişkisi oluşturarak AWS kaynak günlüklerinizi Azure Sentinel 'e erişimi devreder. Bu, AWS günlüklerinde Azure Sentinel 'e erişim izni veren bir rol oluşturarak AWS 'de gerçekleştirilir.

## <a name="prerequisites"></a>Prerequisites

Azure Sentinel çalışma alanında yazma izninizin olması gerekir.

> [!NOTE]
> Azure Sentinel, tüm bölgelerde Cloudtraizetkinlikleri toplar. Bir bölgeden diğerine olay akışı yapmanız önerilmez.

## <a name="connect-aws"></a>AWS 'yi bağlama 


1. Azure Sentinel 'de, **veri bağlayıcıları** ' nı seçin ve ardından tablodaki **Amazon Web Services** satırı seçin ve sağdaki AW bölmesinde **bağlayıcı sayfasını aç**' a tıklayın.

1. Aşağıdaki adımları kullanarak **yapılandırma** bölümündeki yönergeleri izleyin.
 
1.  Amazon Web Services konsolunuza **güvenlik, kimlik & uyumluluk**altında **IAM**' i seçin.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  **Roller** ' i seçin ve **rol oluştur**' u seçin.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  **Başka BIR AWS hesabı seçin.** **Hesap kimliği** alanına, Azure Sentinel portalındaki AWS Bağlayıcısı sayfasında bulunan **Microsoft hesap kimliğini** (**123412341234**) girin.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  **Dış kimlik gerektir** ' in seçildiğinden emin olun ve ardından Azure Sentinel portalındaki AWS Bağlayıcısı sayfasında bulunan dış kimliği (çalışma alanı kimliği) girin.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  **Ekleme izinleri ilkesi** altında **Awscmontrailreadonlyaccess**' ı seçin.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Bir etiket girin (Isteğe bağlı).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Sonra bir **rol adı** girin ve **rol oluştur** düğmesini seçin.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Roller listesinde, oluşturduğunuz rolü seçin.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  **Rol ARN**'yi kopyalayın. Azure Sentinel portalında, Amazon Web Services Bağlayıcısı ekranında, alanı **eklemek Için role** yapıştırın ve **Ekle**' ye tıklayın.

    ![AWS9](./media/connect-aws/aws-9.png)

1. AWS olayları için Log Analytics ilgili şemayı kullanmak için, **Awscses izi**aratın.



## <a name="next-steps"></a>Sonraki adımlar
Bu belgede AWS Cloudtraizini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.

