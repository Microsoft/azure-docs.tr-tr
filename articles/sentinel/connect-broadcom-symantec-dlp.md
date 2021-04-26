---
title: Broadcom Symantec veri kaybı önleme (DLP) verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Sentinel 'e Symantec DLP günlüklerini çekmek için Broadcom Symantec DLP veri bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında Symantec DLP verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 4be182b3eee59f7f9d2ef704a3d0f57c6718b45d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044983"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Broadcom Symantec veri kaybı engellemesini (DLP) Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Broadcom Symantec DLP Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede, Broadcom Symantec DLP gerecinizi Azure Sentinel 'e bağlama açıklanmaktadır. Broadcom Symantec DLP veri Bağlayıcısı, Azure Sentinel ile Symantec DLP günlüklerinizi kolayca bağlamanıza olanak tanır, panoları görüntüleyebilir, özel uyarılar oluşturabilir ve araştırmayı geliştirebilirsiniz. Bu özellik, kuruluşunuzun bilgileri hakkında daha fazla öngörü sağlar ve nerede seyahat eder ve güvenlik işlemi yeteneklerini geliştirir. Broadcom Symantec DLP ve Azure Sentinel arasında tümleştirme, CEF biçimli syslog, Linux tabanlı bir günlük ileticisi ve Log Analytics Aracısı kullanımını sağlar. Ayrıca, kusto işlevine dayalı özel olarak oluşturulmuş bir günlük ayrıştırıcısı kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanınızda okuma ve yazma izinlerine sahip olmanız gerekir.

- Çalışma alanı için paylaşılan anahtarlar için okuma izninizin olması gerekir. [Çalışma alanı anahtarları hakkında daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Azure Sentinel 'e Broadcom Symantec DLP günlükleri gönderme

Günlüklerini Azure Sentinel 'e almak için, Broadcom Symantec DLP gerecinizi, CEF biçiminde bir Linux tabanlı günlük iletme sunucusuna (rsyslog veya Syslog-ng çalıştıran) Syslog iletileri gönderecek şekilde yapılandırın. Bu sunucuda Log Analytics Aracısı yüklü olacaktır ve Aracı günlükleri Azure Sentinel çalışma alanınıza iletir.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden, **Broadcom Symantec DLP (Önizleme)** bağlayıcısını seçin ve ardından **bağlayıcı sayfası**' nı açın.

1. **Yapılandırma** altında, **yönergeler** sekmesindeki yönergeleri izleyin:

    1. **1 altında. Linux Syslog Aracısı yapılandırması** -çalışır durumda bir günlük ileticisi yoksa veya başka bir tane gerekiyorsa bu adımı izleyin. Daha ayrıntılı yönergeler ve açıklamalar için bkz. 1. Adım: Azure Sentinel belgelerindeki [günlük ileticisini dağıtma](connect-cef-agent.md) .

    1. **2 altında. Symantec DLP günlüklerini Syslog aracısına ilet** - [Symantec DLP 'yi yapılandırmak](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US)için Broadcom 'in yönergelerini izleyin. Bu yapılandırma aşağıdaki öğeleri içermelidir:
        - Günlük hedefi – günlük iletme sunucunuzun ana bilgisayar adı ve/veya IP adresi
        - Protokol ve bağlantı noktası – TCP 514 (Aksi takdirde önerilir), günlük iletme sunucunuzdaki Syslog Daemon öğesinde paralel değişikliği yaptığınızdan emin olun.
        - Günlük biçimi – CEF
        - Günlük türleri – tüm kullanılabilir veya tüm uygun

    1. 3 ' ün altında **. Bağlantıyı doğrulama** -bağlayıcı sayfasına komutu kopyalayarak ve günlük ileticisinde çalıştırarak veri alımını doğrulayın. Daha ayrıntılı yönergeler ve açıklamalar için bkz. [3. Adım:](connect-cef-verify.md) Azure Sentinel belgelerindeki bağlantıyı doğrulama.

        Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler **günlüklerde**, **Azure Sentinel** bölümünde, *commonsecuritylog* tablosunda görünür.

Bu veri Bağlayıcısı, beklenen şekilde çalışması için bir kusto Işlevini temel alan bir ayrıştırıcıya bağımlıdır. **SymantecDLP** kusto işlevi diğer adını oluşturmak için [Bu adımları izleyin](https://aka.ms/sentinel-symantecdlp-parser) .

Daha sonra, Log Analytics 'daki Broadcom Symantec DLP verilerini sorgulamak için, `SymantecDLP` sorgu penceresinin en üstüne girin.

Bazı yararlı sorgu örnekleri için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Symantec DLP 'yi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .