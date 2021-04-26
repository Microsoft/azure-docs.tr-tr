---
title: CEF verilerini Azure Sentinel önizlemesine bağlamak için Güvenlik çözümünüzü yapılandırın | Microsoft Docs
description: Güvenlik çözümünüzü, CEF verilerini Azure Sentinel 'e bağlamak üzere nasıl yapılandıracağınızı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 60ce503d4a89f245f28d5034924cb8c89c926b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771303"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2. Adım: Güvenlik çözümünüzü CEF iletileri gönderecek şekilde yapılandırma

Bu adımda, CEF aracısına Günlükler göndermek için güvenlik çözümünüz üzerinde gerekli yapılandırma değişikliklerini gerçekleştirirsiniz.

## <a name="configure-a-solution-with-a-connector"></a>Bağlayıcı ile çözüm yapılandırma

Güvenlik çözümünüz zaten mevcut bir bağlayıcınız varsa, bağlayıcıya özgü yönergeleri aşağıdaki gibi kullanın:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Akamai güvenlik olayları](connect-akamai-security-events.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Denetim Noktası](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Forcepoint ürünleri](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [Kusurda WAF ağ geçidi](connect-imperva-waf-gateway.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [Kablox ağ Forensics platformu](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Diğer herhangi bir çözümü yapılandırma

Belirli güvenlik çözümünüz için bir bağlayıcı yoksa, günlükleri CEF aracısına iletmek için aşağıdaki genel yönergeleri kullanın.

1. Çözümünüzü CEF iletileri gönderecek şekilde yapılandırma adımları için ilgili yapılandırma makalesine gidin. Çözümünüz listede yoksa, Gereç bu değerleri, Log Analytics aracısına bağlı olarak, gerecin gerekli biçimdeki Azure Sentinel Syslog aracısına göndermesi için ayarlamanız gerekir. Bu parametreleri Azure Sentinel aracısında Syslog arka plan programı 'nda da değiştirdiğiniz sürece, aracısında değiştirebilirsiniz.
    - Protokol = TCP
    - Bağlantı noktası = 514
    - Biçim = CEF
    - IP adresi-CEF iletilerini bu amaçla ayrıldığınızdan sanal makinenin IP adresine gönderdiğinizden emin olun.

   Bu çözüm Syslog RFC 3164 veya RFC 5424 ' ü destekler.

1. Log Analytics CEF olaylarını aramak için `CommonSecurityLog` sorgu penceresine girin.

1. 3. Adım: [bağlantıyı doğrulama](connect-cef-verify.md)adımına geçin.

> [!NOTE]
> **TimeGenerated alanının kaynağını değiştirme**
>
> - Varsayılan olarak, Log Analytics Aracısı şemadaki *TimeGenerated* alanını, aracının Syslog arka plan programından olayı aldığı zamana göre doldurur. Sonuç olarak kaynak sistemde olayın oluşturulma zamanı Azure Sentinel'de kaydedilmez.
>
> - Ancak, komut dosyasını indirecek ve çalıştıracak aşağıdaki komutu çalıştırabilirsiniz `TimeGenerated.py` . Bu betik, Log Analytics aracısını aracı tarafından alındığı zaman yerine, *TimeGenerated* alanını olayın kaynak sistemindeki özgün zamanına göre doldurmak üzere yapılandırır.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede CEF gereçlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [CEF ve CommonSecurityLog alan eşlemesi](cef-name-mapping.md)hakkında bilgi edinin.
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.