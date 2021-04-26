---
title: Istek üzerine düzeltme noktası e-posta güvenliği verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Sentinel 'e POD e-posta güvenlik günlüklerini çekmek için Isteğe bağlı bir e-posta güvenliği veri bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında POD e-posta güvenliği verilerini görüntüleyin, uyarı oluşturun ve araştırmayı geliştirebilirsiniz.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580455"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Düzeltme noktası Isteğe bağlı e-posta güvenliği (POD) çözümünü Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Isteğe bağlı düzeltme noktası e-posta güvenliği Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede, düzeltme noktası Isteğe bağlı e-posta güvenliği gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. POD Data Connector, POD günlüklerinizi kolayca Azure Sentinel 'e bağlamanıza olanak tanır. böylece, verileri çalışma kitaplarında görüntüleyebilir, özel uyarılar oluşturmak için kullanabilir ve araştırmayı geliştirebilirsiniz.  Isteğe bağlı bir Isteğe bağlı e-posta güvenliği ve Azure Sentinel arasındaki tümleştirme WebSocket API 'sini kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında okuma ve yazma izninizin olması gerekir.

- Çalışma alanı için paylaşılan anahtarlar için okuma izninizin olması gerekir. [Çalışma alanı anahtarları hakkında daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- İşlev Uygulaması oluşturmak için Azure Işlevleri üzerinde okuma ve yazma izinlerine sahip olmanız gerekir. [Azure işlevleri hakkında daha fazla bilgi edinin](../azure-functions/index.yml).

- Şu WebSocket API kimlik bilgilerine sahip olmanız gerekir: Redakpointclusterıd, Redakpointtoken. [WebSocket API 'si hakkında daha fazla bilgi edinin](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Istek üzerine düzeltme noktasını yapılandırma ve bağlama e-posta güvenliği

Düzeltme noktası Isteğe bağlı e-posta güvenliği, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** Galerisi ' nden, **istek üzerine Isteğe bağlı e-posta güvenliğini (Önizleme)** ve ardından **bağlayıcı sayfası**' nı açın.

1. Bağlayıcı sayfasının **yapılandırma** bölümünde açıklanan adımları izleyin.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler aşağıdaki tablolardaki *özel Günlükler* altında **günlüklerde** görünür:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Bazı yararlı örnek sorgular için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 60 dakika kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Istek üzerine bir Isteğe bağlı e-posta güvenliğini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .