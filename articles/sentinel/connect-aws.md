---
title: AWS Cloudtraizizi Azure Sentinel 'e bağlayın | Microsoft Docs
description: AWS Cloudtraı ve Azure Sentinel arasında bir güven ilişkisi oluşturarak AWS kaynak günlüklerine Azure Sentinel erişimini atamak için AWS bağlayıcısını kullanın.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 3d24fa1ea046a860feb40d09a6d0a57c79371450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99807606"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Azure Sentinel 'i AWS Cloudtrato 'a bağlama

AWS Cloudiziz yönetimi olaylarınızın Azure Sentinel 'e akışını sağlamak için AWS bağlayıcısını kullanın. Bu bağlantı işlemi, AWS Cloudtraı ve Azure Sentinel arasında bir güven ilişkisi oluşturarak AWS kaynak günlüklerinizi Azure Sentinel 'e erişimi devreder. Bu, AWS günlüklerinde Azure Sentinel 'e erişim izni veren bir rol oluşturarak AWS 'de gerçekleştirilir.

> [!NOTE]
> AWS Cloudizinin LookupEvents API 'sinde [yerleşik sınırlamaları](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) vardır. Hesap başına saniye başına iki işlem (TPS) ve her bir sorgu en fazla 50 kayıt döndürebilir. Sonuç olarak tek bir kiracı sürekli bir bölgede saniyede 100'den fazla kayıt oluşturursa veri alımında birikmelere ve gecikmelere yol açar.

## <a name="prerequisites"></a>Önkoşullar

Azure Sentinel çalışma alanında yazma izninizin olması gerekir.

> [!NOTE]
> Azure Sentinel, tüm bölgelerde Cloudizyönetim olayları toplar. Bir bölgeden diğerine olay akışı yapmanız önerilmez.

## <a name="connect-aws"></a>AWS’yi bağlama 


1. Azure Sentinel 'de, **veri bağlayıcıları** ' nı seçin ve ardından tablodaki **Amazon Web Services** satırı seçin ve sağdaki AW bölmesinde **bağlayıcı sayfasını aç**' a tıklayın.

1. Aşağıdaki adımları kullanarak **yapılandırma** bölümündeki yönergeleri izleyin.
 
1.  Amazon Web Services konsolunuza **güvenlik, kimlik & uyumluluk** altında **IAM**' i seçin.

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

1. AWS olayları için Log Analytics ilgili şemayı kullanmak için, **Awscses izi** aratın.

    > [!IMPORTANT]
    > 1 Aralık 2020 itibariyle **Awsrequestıd** alanı **AwsRequestId_** alanla değiştirilmiştir (eklenen alt çizgi ' yi aklınızda bulunur). Eski **Awsrequestıd** alanındaki veriler, müşterinin belirtilen veri bekletme döneminin sonuna kadar korunur.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede AWS Cloudtraizini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
