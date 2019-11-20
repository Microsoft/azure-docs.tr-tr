---
title: Azure Sentinel 'de DNS verilerini bağlama | Microsoft Docs
description: Azure Sentinel 'de DNS verilerini bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: c5e58f496176ec0f1b8317c8b862a8ef2ffa434d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262724"
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

Aşağıdaki tabloda bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır:

| **Bağlı kaynak** | **Destek** | **Açıklama** |
| --- | --- | --- |
| [Windows aracıları](../azure-monitor/platform/agent-windows.md) | Evet | Çözüm, Windows aracılarından DNS bilgilerini toplar. |
| [Linux aracıları](../azure-monitor/learn/quick-collect-linux-computer.md) | Hayır | Çözüm, doğrudan Linux aracılarından DNS bilgi toplamaz. |
| [System Center Operations Manager yönetim grubu](../azure-monitor/platform/om-agents.md) | Evet | Çözüm, bağlı Operations Manager yönetim grubundaki aracılardan DNS bilgilerini toplar. Operations Manager aracısından Azure Izleyici 'ye doğrudan bağlantı gerekli değildir. Verileri yönetim grubundan Log Analytics çalışma alanına iletilir. |
| [Azure depolama hesabı](../azure-monitor/platform/collect-azure-metrics-logs.md) | Hayır | Azure depolama çözümü tarafından kullanılmaz. |

### <a name="data-collection-details"></a>Veri koleksiyonu ayrıntıları

Çözüm, Log Analytics aracısını yüklendiği DNS sunucularından DNS envanteri ve DNS ilgili olay verilerini toplar. DNS sunucuları, bölge ve kaynak kayıtları sayısı gibi envanterle ilişkili veri DNS PowerShell cmdlet'lerini çalıştırarak toplanır. Verileri iki günde bir kez güncelleştirilir. İlgili olay verileri neredeyse gerçek zamanlı olarak toplanan [analiz ve Denetim günlükleri](https://technet.microsoft.com/library/dn800669.aspx#enhanc) Gelişmiş DNS günlüğe kaydetme ve tanılama Windows Server 2012 R2 tarafından sağlanan.


## <a name="connect-your-dns-appliance"></a>DNS gerecinizi bağlama

1. Azure Sentinel portalında **veri bağlayıcıları** ' nı seçin ve **DNS (Önizleme)** kutucuğunu seçin.
1. DNS makineleriniz Azure 'da ise:
    1. **Azure Windows sanal makinesinde aracıyı yükler**' e tıklayın.
    1. **Sanal makineler** listesinde, Azure Sentinel 'de akışı yapmak istediğiniz DNS makinesini seçin. Bunun bir Windows sanal makine olduğundan emin olun.
    1. Bu VM için açılan pencerede, **Bağlan**' a tıklayın.  
    1. **DNS Bağlayıcısı** penceresinde **Etkinleştir** ' e tıklayın. 

2. DNS makineniz bir Azure VM değilse:
    1. **Azure dışı makinelerde aracıyı yükler**' e tıklayın.
    1. **Doğrudan aracı** penceresinde, **Windows agent 'ı (64 bit) İndir** veya **Windows Agent 'ı (32 bit) İndir**seçeneğini belirleyin.
    1. Aracıyı DNS makinenize yükler. **Çalışma alanı kimliği**, **birincil anahtar**ve **İkincil anahtarı** kopyalayın ve yükleme sırasında istendiğinde bunları kullanın.

3. DNS günlükleri için Log Analytics ilgili şemayı kullanmak için **Dnsevents**' ı arayın.

## <a name="validate"></a>Doğrula 

Log Analytics, şema **Dnsevents** için arama yapın ve olayların bulunduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, DNS şirket içi gereçlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
