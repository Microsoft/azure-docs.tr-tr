---
title: Office 365 için Microsoft Defender (eskiden Office 365 ATP) verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Office 365 verileri için Microsoft Defender 'ın, görünürlük elde etmek ve otomatikleştirilmiş yanıt senaryoları oluşturmak için Azure Sentinel 'e alınması.
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655503"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Office 365 için Microsoft Defender 'daki uyarıları bağlama 

> [!IMPORTANT]
>
> - **Office 365 Için Microsoft Defender, daha** önce **Office 365 gelişmiş TEHDIT koruması (ATP)** olarak biliniyordu.
>
>     Eski adı bir süre için üründe hala kullanımda (Azure Sentinel 'deki veri Bağlayıcısı dahil) görebilirsiniz.
>
> - Office 365 uyarıları için Microsoft Defender 'ın alımı Şu anda genel önizlemededir. Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
[Office 365 Için Microsoft Defender](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) , kuruluşunuzun e-posta iletilerinde, kötü amaçlı URL bağlantılarında ve işbirliği araçlarında bilinmeyen kötü amaçlı yazılımlardan oluşan sıfır gün ve diğer gelişmiş tehditlere karşı korunmasını korumalar. Office 365 uyarıları için Microsoft Defender 'ı Azure Sentinel 'e ekleyerek, güvenlik işlemlerinizin e-posta, dosya paylaşımı ve URL tabanlı tehditler hakkında bilgi kullanabileceksiniz. Daha sonra kuruluşunuz genelinde güvenlik olaylarını daha ayrıntılı şekilde çözümleyebilir ve etkili ve anında yanıt için PlayBook 'lar oluşturabilirsiniz.

Bağlayıcı aşağıdaki uyarıları içeri aktarır:

- Kötü amaçlı olabilecek bir URL 'yi tıklatma algılandı 

- Teslimattan sonra kaldırılan kötü amaçlı yazılımları içeren e-posta

- Teslime URL 'Leri içeren e-posta iletileri teslimattan sonra kaldırıldı 

- Kullanıcı tarafından kötü amaçlı yazılım veya phsi olarak bildirilen e-posta 

- Şüpheli e-posta gönderme desenleri algılandı 

- Kullanıcının e-posta göndermesi kısıtlanan 

Bu uyarılar Office **güvenlik ve Uyumluluk Merkezi**'nde Office müşterileri tarafından görülebilir.

## <a name="prerequisites"></a>Önkoşullar

- Bağlayıcıyı etkinleştirdiğinizde Azure Sentinel çalışma alanında okuma ve yazma izinlerinizin olması gerekir.

- Azure Sentinel çalışma alanının kiracısında genel yönetici veya güvenlik yöneticisi olmanız gerekir.

- [Office 365 ATP planı 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) için geçerli bir lisansa sahip olmanız gerekir (Office 365 E5, Office 365 A5 ve Microsoft 365 E5 lisanslarına dahildir ve ayrı olarak satın alınabilir). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Office 365 için Microsoft Defender 'a bağlanma

Office 365 için Microsoft Defender dağıtılırsa ve ilkeler yapılandırıldıysa, uyarılar kolayca Azure Sentinel 'e alınabilir.

1. Azure Sentinel 'de, gezinti menüsünden **veri bağlayıcıları** ' nı seçin.

1. Bağlayıcılar galerisinde **office 365 Için Microsoft Defender** (yine de *Office 365 Gelişmiş tehdit koruması* olarak adlandırılabilir) seçeneğini belirleyin ve **bağlayıcı sayfasını aç**' ı seçin.

1. **Yapılandırma** bölümünde, **Bağlan**' a tıklayın. 

1. **Olayları oluştur** bölümünde **Etkinleştir**' e tıklayın.

1. Office 365 ATP uyarılarını sorgulamak için ilgili şemayı kullanmak için, **Securityalert** araması yapın ve **sağlayıcı adını** **oatp** olarak belirtin.

1. Office 365 Bağlayıcısı için Microsoft Defender ile paketlenmiş sorgu örnekleri ve analitik kural şablonlarını görmek ve kullanmak için **sonraki adımlar** sekmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Office 365 için Microsoft Defender 'ı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.