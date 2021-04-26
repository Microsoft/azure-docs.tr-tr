---
title: Sophos Cloud OPX verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Sentinel 'e günlük çekmek için Sophos Cloud optimize x bağlayıcısını nasıl kullanacağınızı öğrenin <PRODUCT NAME> . <PRODUCT NAME>Çalışma kitaplarında verileri görüntüleme, uyarı oluşturma ve araştırmayı geliştirme.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700936"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Sophos Cloud OPX 'nizi Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Sophos Cloud optimizasyonu x Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Sophos Cloud OPX Bağlayıcısı, tüm Sophos Cloud optimize x güvenlik çözüm günlüklerinizi Azure Sentinel ile kolayca bağlamanıza, panoları görüntülemenizi, özel uyarılar oluşturmanızı ve araştırmayı iyileştirmenize olanak tanır.  Bu özellik, kuruluşunuzun bulut güvenliği ve uyumluluk duruşuna daha fazla öngörü sağlar ve bulut güvenlik işlemi yeteneklerini geliştirir. Sophos Cloud OPX ve Azure Sentinel arasındaki tümleştirme REST API kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Sophos Cloud OPX 'i yapılandırma ve bağlama

Sophos Cloud OPX, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **Sophos Cloud OPX (Önizleme)** öğesini seçin.

1. **Bağlayıcı sayfasını aç**' ı seçin.

1. Bağlayıcı sayfasından **çalışma alanı kimliği** ve **birincil anahtarı** kopyalayın ve kaydedin.

1. Sophos ' deki yönergeleri izleyerek [Microsoft Azure Sentinel ile tümleştirin](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (üçüncü adımdan itibaren).

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler *SophosCloudOptix_CL* tablosundaki **özel Günlükler** bölümünün altındaki **günlüklerde** görüntülenir.

Sophos Cloud OPX verilerini sorgulamak için `SophosCloudOptix_CL` sorgu penceresine girin. Bazı yararlı sorgu örnekleri için bağlayıcı sayfasında **sonraki adımlar** sekmesine ve [Sophos belgelerine](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)bakın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Sophos Cloud optimizasyonu x 'i Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
