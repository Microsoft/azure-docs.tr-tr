---
title: CEF verilerini Azure Sentinel önizlemesine bağlama | Microsoft Docs
description: Bir Linux makinesini günlük ileticisi olarak kullanarak Azure Sentinel 'e ortak olay biçimi (CEF) iletileri gönderen bir dış çözümü bağlayın.
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 54fd6c0c085c0055f3114fde606f8f7d2f2e055e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772068"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ortak olay biçimini kullanarak dış çözümünüzü bağlama

CEF iletileri gönderen bir dış çözümü bağladığınızda, Azure Sentinel ile bağlantı kurmak için üç adım vardır:

1. Adım: [Syslog/CEF ileticisi dağıtarak CEF 'Yi bağlama](connect-cef-agent.md) adım 2: [çözüme özgü adımları gerçekleştirme](connect-cef-solution-config.md) adım 3: [bağlantıyı doğrulama](connect-cef-verify.md)

Bu makalede bağlantının nasıl çalıştığı, önkoşulları listeleyen ve güvenlik çözümleri için Syslog 'nin en üstünde ortak olay biçimi (CEF) iletileri göndermek için bir mekanizma dağıtma adımları gösterilmektedir. 

> [!NOTE] 
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

Bu bağlantıyı yapmak için, Gereç ve Azure Sentinel arasındaki iletişimi desteklemek üzere bir Syslog Iletici sunucusu dağıtmanız gerekir.  Sunucu, Linux için Log Analytics Aracısı yüklü olan adanmış bir Linux makinesinden (VM veya şirket içi) oluşur. 

Aşağıdaki diyagramda, Azure 'da bir Linux sanal makinesi söz konusu olduğunda kurulum açıklanmaktadır:

 ![Azure 'da CEF](./media/connect-cef/cef-syslog-azure.png)

Alternatif olarak, başka bir bulutta veya şirket içi bir makinede bir VM kullanıyorsanız bu kurulum mevcut olacaktır: 

 ![Şirket içi CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Güvenlik konuları

Makinenin güvenliğini kuruluşunuzun güvenlik ilkesine göre yapılandırdığınızdan emin olun. Örneğin, ağınızı kurumsal ağ güvenlik ilkenize göre olacak şekilde yapılandırabilir ve gereksinimlerinize göre uyum sağlamak için arka plan programındaki bağlantı noktalarını ve protokolleri değiştirmelisiniz. Aşağıdaki yönergeleri kullanarak makinenizin güvenlik yapılandırmasını geliştirebilirsiniz:  [Azure 'Da GÜVENLI VM](../virtual-machines/security-policy.md), [ağ güvenliği için en iyi uygulamalar](../security/fundamentals/network-best-practices.md).

Syslog kaynağı ve Syslog Ileticisi arasındaki TLS iletişimini kullanmak için Syslog Daemon 'u (rsyslog veya Syslog-ng) TLS 'de iletişim kurmak üzere yapılandırmanız gerekir: TLS [-rsyslog Ile Syslog trafiğini şifreleme](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [günlük iletilerini TLS – Syslog-NG ile şifreleme](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Önkoşullar

Günlük ileticisi olarak kullandığınız Linux makinenin aşağıdaki işletim sistemlerinden birini çalıştırdığından emin olun:

- 64 bit
  - CentOS 7 ve 8, ikincil sürümler dahil (6 değil)
  - Amazon Linux 2017,09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 ve 8, ikincil sürümler dahil (6 değil)
  - Borçlu GNU/Linux 8 ve 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS ve 18,04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32 bit
  - CentOS 7 ve 8, ikincil sürümler dahil (6 değil)
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 ve 8, ikincil sürümler dahil (6 değil)
  - Borçlu GNU/Linux 8 ve 9
  - Ubuntu Linux 14,04 LTS ve 16,04 LTS
 
- Daemon sürümleri
  - Syslog-NG: 2,1-3.22.1
  - Rsyslog: V8
  
- Syslog RFC 'Leri destekleniyor
  - Syslog RFC 3164
  - Syslog RFC 5424
 
Makinenizin aynı zamanda aşağıdaki gereksinimleri karşıladığından emin olun: 

- Kapasite
  - Makinenizin en az **4 CPU çekirdeği ve 8 GB RAM** olması gerekir.

    > [!NOTE]
    > - **Rsyslog** Daemon kullanan tek bir günlük iletici makinesi, toplanan **saniyede 8500 olay kadar (EPS)** desteklenen kapasiteye sahiptir.

- İzinler
  - Makinenizde yükseltilmiş izinleriniz (sudo) olmalıdır. 

- Yazılım gereksinimleri
  - Makinenizde Python 2,7 veya 3 ' ün yüklü olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel 'in güvenlik çözümleri ve gereçlerden CEF günlüklerini nasıl topladığı hakkında daha fazla öğrendiniz. Çözümünüzü Azure Sentinel 'e bağlamayı öğrenmek için aşağıdaki makalelere bakın:

- 1. Adım: [Syslog/CEF ileticisi dağıtarak CEF 'Yi bağlama](connect-cef-agent.md)
- 2. Adım: [çözüme özgü adımlar gerçekleştirme](connect-cef-solution-config.md)
- 3. Adım: [bağlantıyı doğrulama](connect-cef-verify.md)

Azure Sentinel 'de topladığınız verilerle ne yapacaklarınız hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [CEF ve CommonSecurityLog alan eşlemesi](cef-name-mapping.md)hakkında bilgi edinin.
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.