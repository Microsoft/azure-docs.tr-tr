---
title: Azure Sentinel 'de DNS verilerini bağlama | Microsoft Docs
description: DNS makinesine bir aracı yükleyerek Windows üzerinde çalışan herhangi bir etki alanı ad sunucusunu (DNS) Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: abecddb6f5469cb4ef463e65d6c74149bf34dca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590227"
---
# <a name="connect-your-domain-name-server"></a>Etki alanı ad sunucunuzu bağlama

> [!IMPORTANT]
> Azure Sentinel 'deki DNS veri Bağlayıcısı Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows üzerinde çalışan herhangi bir etki alanı ad sunucusunu (DNS) Azure Sentinel 'e bağlayabilirsiniz. Bu işlem, DNS makinesine bir aracı yüklenerek yapılır. DNS günlüklerini kullanarak, analitik ve denetim günlüklerini ve DNS sunucularından diğer ilgili verileri toplayarak, çözümleyerek ve birbirleriyle ilişkilendirerek, kuruluşunuzun DNS altyapısına güvenlik, performans ve işlemlerle ilgili Öngörüler elde edebilirsiniz.

DNS günlüğü bağlantısını etkinleştirdiğinizde şunları yapabilirsiniz:
- Kötü amaçlı etki alanı adlarını çözümlemeye çalışır olan istemcileri tanımla
- Eski kaynak kayıtlarını tanımla
- Sık sorgulanan etki alanı adlarını ve DNS istemcilerini belirler
- DNS sunucularında istek yükünü görüntüleme
- Dinamik DNS kaydı başarısızlıklarını görüntüleme

## <a name="connected-sources"></a>Bağlı kaynaklar

Aşağıdaki tabloda, bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır:

| **Bağlı kaynak** | **Destek** | **Açıklama** |
| --- | --- | --- |
| [Windows aracıları](../azure-monitor/agents/agent-windows.md) | Yes | Bu çözüm, Windows aracılarından DNS bilgilerini toplar. |
| [Linux aracıları](../azure-monitor/vm/quick-collect-linux-computer.md) | No | Çözüm, doğrudan Linux aracılarından DNS bilgileri toplamaz. |
| [System Center Operations Manager yönetim grubu](../azure-monitor/agents/om-agents.md) | Yes | Çözüm, bağlı bir Operations Manager yönetim grubundaki aracılardan DNS bilgilerini toplar. Operations Manager aracısından Azure Izleyici 'ye doğrudan bağlantı gerekli değildir. Veriler, yönetim grubundan Log Analytics çalışma alanına iletilir. |
| [Azure depolama hesabı](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | No | Azure depolama, çözüm tarafından kullanılmıyor. |

### <a name="data-collection-details"></a>Veri toplama ayrıntıları

Çözüm, bir Log Analytics aracısının yüklendiği DNS sunucularından DNS envanterini ve DNS olay ile ilgili verileri toplar. DNS PowerShell cmdlet 'leri çalıştırılarak, DNS sunucularının, bölgelerin ve kaynak kayıtlarının sayısı gibi stokla ilgili veriler toplanır. Veriler her iki günde bir güncelleştirilir. Olayla ilgili veriler, Windows Server 2012 R2 'de Gelişmiş DNS günlüğü ve tanılama tarafından verilen [analitik ve denetim günlüklerinden](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) gerçek zamanlı olarak toplanır.


## <a name="connect-your-dns-appliance"></a>DNS gerecinizi bağlama

1. Azure Sentinel portalında **veri bağlayıcıları** ' nı seçin ve **DNS (Önizleme)** kutucuğunu seçin.
1. DNS makineleriniz Azure 'da ise:
    1. **Azure Windows sanal makinesinde aracıyı yükler**' e tıklayın.
    1. **Sanal makineler** listesinde, Azure Sentinel 'de akışı yapmak istediğiniz DNS makinesini seçin. Bunun bir Windows sanal makine olduğundan emin olun.
    1. Bu VM için açılan pencerede, **Bağlan**' a tıklayın.  
    1. **DNS Bağlayıcısı** penceresinde **Etkinleştir** ' e tıklayın. 

2. DNS makineniz bir Azure VM değilse:
    1. **Azure dışı makinelerde aracıyı yükler**' e tıklayın.
    1. **Doğrudan aracı** penceresinde, **Windows agent 'ı (64 bit) İndir** veya **Windows Agent 'ı (32 bit) İndir** seçeneğini belirleyin.
    1. Aracıyı DNS makinenize yükler. **Çalışma alanı kimliği**, **birincil anahtar** ve **İkincil anahtarı** kopyalayın ve yükleme sırasında istendiğinde bunları kullanın.

3. DNS günlükleri için Log Analytics ilgili şemayı kullanmak için **Dnsevents**' ı arayın.

## <a name="validate"></a>Doğrulama 

Log Analytics, şema **Dnsevents** için arama yapın ve olayların bulunduğundan emin olun.

## <a name="troubleshooting"></a>Sorun giderme

Arama sorguları Azure Sentinel 'de görünmüyorsa, sorguların düzgün görüntülenmesi için aşağıdaki adımları izleyin:
1. [Sunucularınızdaki DNS Analizi günlüklerini](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))açın.
2. DNSEvents Log Analytics koleksiyon listenizde göründüğünden emin olun.
3. [Azure DNS ANALYTICS](../azure-monitor/insights/dns-analytics.md)'i açın.
4. Azure DNS Analytics ' te **yapılandırma** altında ayarları değiştirin, kaydedin, sonra gerekirse geri değiştirin ve yeniden kaydedin.
5. Sorguların görüntülendiğinden emin olmak için Azure DNS analizlerini denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, DNS şirket içi gereçlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.