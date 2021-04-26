---
title: Kusurda WAF Gateway gerecinizi Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Sentinel 'e NYıVA WAF günlüklerini çekmek için, n, n, n, NBU ağ geçidi bağlayıcısını kullanmayı öğrenin. Çalışma kitaplarında kusuru WAF verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı izleyin.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698442"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Kusurda WAF Gateway gerecinizi Azure Sentinel 'e bağlama

> [!IMPORTANT]
> NSIS WAF Gateway Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede, bir Azure Sentinel WAF Gateway gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. Bu kusurda WAF Gateway veri Bağlayıcısı, çalışma kitaplarında verileri görüntüleyebilmeniz, özel uyarılar oluşturmak için onu kullanarak ve araştırmayı iyileştirebilmeniz için Azure Sentinel ile, nvva WAF Gateway günlüklerinizi kolayca bağlamanıza olanak tanır. Bu, bir Linux-biçimli syslog, Linux tabanlı bir günlük ileticisi ve Log Analytics aracısıdır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanınızda okuma ve yazma izinlerine sahip olmanız gerekir.

- Çalışma alanı için paylaşılan anahtarlar için okuma izninizin olması gerekir. [Çalışma alanı anahtarları hakkında daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Azure Sentinel 'e, kusurda WAF ağ geçidi günlükleri gönderme

Günlük dosyalarını Azure Sentinel 'e almak için, FSA biçimindeki syslog iletilerini Linux tabanlı bir günlük iletme sunucusuna (rsyslog veya Syslog-ng çalıştıran) göndermek üzere, bağımsız ağ geçidi gerecinizi yapılandırın. Bu sunucuda Log Analytics Aracısı yüklü olacaktır ve Aracı günlükleri Azure Sentinel çalışma alanınıza iletir.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden, **NVA WAF Gateway (Önizleme)** öğesini seçin ve ardından **bağlayıcı sayfasını açın**.

1. **Yapılandırma** altında, **yönergeler** sekmesindeki yönergeleri izleyin:

    1. **1 altında. Linux Syslog Aracısı yapılandırması** -çalışır durumda bir günlük ileticisi yoksa veya başka bir tane gerekiyorsa bu adımı izleyin. Daha ayrıntılı yönergeler ve açıklamalar için bkz. 1. Adım: Azure Sentinel belgelerindeki [günlük ileticisini dağıtma](connect-cef-agent.md) .

    1. **2 altında. Ortak olay biçimi (CEF) günlüklerini Syslog aracısına ilet** -bu bağlayıcı, bir **eylem arabirimi** ve bir eylem IÇIN, **SecureSphere MX** yönetim konsolunda oluşturulacak şekilde **ayarlanmış** olması gerekir. Azure Sentinel 'de, nınva [WAF Gateway uyarı günlüğünü etkinleştirmek](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)için, NVA yönergelerini izleyin. Bu yapılandırma aşağıdaki öğeleri içermelidir:
        - Günlük hedefi – günlük iletme sunucunuzun ana bilgisayar adı ve/veya IP adresi
        - Protokol ve bağlantı noktası – TCP 514
        - Günlük biçimi – CEF
        - Günlük türleri: tümü kullanılabilir

    1. 3 ' ün altında **. Bağlantıyı doğrulama** -bağlayıcı sayfasına komutu kopyalayarak ve günlük ileticisinde çalıştırarak veri alımını doğrulayın. Daha ayrıntılı yönergeler ve açıklamalar için bkz. [3. Adım:](connect-cef-verify.md) Azure Sentinel belgelerindeki bağlantıyı doğrulama.

        Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler **günlüklerde**, **Azure Sentinel** bölümünde, *commonsecuritylog* tablosunda görünür.

Log Analytics 'da, 1. isva WAF ağ geçidi verilerini sorgulamak için, aşağıdakileri seçtiğiniz şekilde, diğer filtreleri uygulayarak sorgu penceresine kopyalayın:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Daha kullanışlı sorgu örnekleri için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Sentinel 'e, diğimiz bir ağ geçidini nasıl bağlayakullanacağınızı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .