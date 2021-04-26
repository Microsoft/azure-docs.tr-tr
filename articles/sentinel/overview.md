---
title: Azure Sentinel nedir? | Microsoft Belgeleri
description: Azure Sentinel, ölçeklenebilir, bulut Yerel güvenlik bilgileri olay yönetimi (SıEM) ve güvenlik Orchestration otomatik yanıtı (SOAR) çözümü hakkında bilgi edinin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 4bc5732c1b2349f34297ecaacc7eb3176fdba611
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570494"
---
# <a name="what-is-azure-sentinel"></a>Azure Sentinel nedir?

Microsoft Azure Sentinel, ölçeklenebilir, bulutta yerel, **güvenlik bilgileri olay yönetimi (SıEM)** ve **güvenlik Orchestration otomatik yanıtı (Soar)** çözümüdür. Azure Sentinel, bir uyarı algılama, tehdit görünürlüğü, proaktif arama ve tehdit yanıtı için tek bir çözüm sunarak kuruluş genelinde akıllı güvenlik Analizi ve tehdit bilgileri sunar. 

Azure Sentinel, çok daha fazla gelişmiş saldırı, uyarı hacimlerini artırma ve uzun çözüm süresi çerçevelerinden oluşan, kurumsal hafifletmesini, kuruluş genelinde büyük bir bakış görünümünüz.

- Hem şirket içinde hem de birden çok bulutta, tüm kullanıcılar, cihazlar, uygulamalar ve altyapıda **bulut ölçeğinde veri toplayın** . 

- **Daha önce algılanmayan tehditleri algılayın** ve Microsoft 'un analizlerini ve benzersiz tehdit bilgilerini kullanarak yanlış pozitif sonuçları en aza indirin. 

- **Yapay zeka ile tehditleri araştırın** ve ölçekteki şüpheli etkinlikler Için, Microsoft 'ta bulunan siber güvenlik çalışmalarına dokunduktan fazla bilgi için arama yapın. 

- Ortak görevlerin yerleşik düzenlemesi ve otomasyonu ile **olaylara hızla yanıt verin** .

![Azure Sentinel temel özellikleri](./media/overview/core-capabilities.png)

Azure Sentinel, mevcut Azure hizmetlerinin tam aralığında oluşturma, Log Analytics ve Logic Apps gibi kanıtlanmış temelleri yerel olarak içerir. Azure Sentinel, araştırma ve algılamalarınızı AI ile zenginleştirir ve Microsoft 'un tehdit bilgileri akışını sağlar ve kendi tehdit zekaızın bilgilerini almanıza olanak sağlar. 

## <a name="connect-to-all-your-data"></a>Tüm verilerinize bağlanın

Azure Sentinel 'de, ilk olarak [güvenlik kaynaklarınıza bağlanmanız](connect-data-sources.md)gerekir. Azure Sentinel, Microsoft çözümleri için çok sayıda bağlayıcıyla sunulur ve Microsoft 365 Defender (eski adıyla Microsoft tehdit koruması) çözümleri ve Office 365, Azure AD, kimlik için Microsoft Defender (eski adıyla Azure ATP) ve Microsoft Cloud App Security gibi Microsoft 365 kaynakları dahil gerçek zamanlı tümleştirme sağlar. Ayrıca, Microsoft dışı çözümler için daha geniş güvenlik ekosistemine yerleşik bağlayıcılar vardır. Ayrıca, veri kaynaklarınızı Azure Sentinel 'e bağlamak için ortak olay biçimi, syslog veya REST API de kullanabilirsiniz. 

![Veri Toplayıcılar](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Çalışma Kitapları

Veri kaynaklarınızı Azure Sentinel 'e [bağladıktan](quickstart-onboard.md) sonra, özel çalışma kitapları oluşturma konusunda çok yönlülük sağlayan Azure Izleyici çalışma kitapları Ile Azure Sentinel tümleştirmesini kullanarak verileri izleyebilirsiniz. Çalışma kitapları Azure Sentinel 'de farklı şekilde görüntülenirken, [Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturmayı](../azure-monitor/visualize/workbooks-overview.md)görmeniz yararlı olabilir. Azure Sentinel, verileriniz genelinde özel çalışma kitapları oluşturmanızı sağlar ve ayrıca, bir veri kaynağını bağladığınızda verilerinize hızlı bir şekilde Öngörüler elde etmenizi sağlayan yerleşik çalışma kitabı şablonlarıyla birlikte gelir.

![Panolar](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analiz

Azure Sentinel, paraziti azaltmanıza ve inceleyecek ve araştırmanız gereken uyarı sayısını en aza indirmenize yardımcı olmak için, [uyarıları olaylar ile ilişkilendirmek için analiz](tutorial-detect-threats-built-in.md)kullanır. **Olaylar** , araştırıp çözebileceğiniz, uygulanabilir olabilecek bir tehdit oluşturan ilgili uyarı gruplarıdır. Yerleşik bağıntı kurallarını olduğu gibi kullanın veya kendi kendinize oluşturmak için bir başlangıç noktası olarak kullanın. Azure Sentinel Ayrıca, ağ davranışınızı eşlemek için makine öğrenimi kuralları sağlar ve kaynaklarınızın tamamında bozukluklar olup olmadığına bakar. Bu analizler, farklı varlıklar hakkında düşük doğruluk uyarılarını potansiyel yüksek kaliteli güvenlik olaylarına birleştirerek noktaları birbirine bağlanır.

![Olaylar](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Güvenlik Otomasyonu & düzenleme

Ortak görevlerinizi otomatikleştirin ve Azure hizmetleriyle ve mevcut araçlarınızla tümleştirilen [PlayBook 'lar ile güvenlik düzenlemesini kolaylaştırın](tutorial-respond-threats-playbook.md) . Azure Logic Apps temel alınarak geliştirilen Azure Sentinel otomasyonu ve düzenleme çözümü, yeni teknolojiler ve tehditler ortaya çıktı olarak ölçeklenebilir Otomasyonu sağlayan yüksek düzeyde genişletilebilir bir mimari sağlar. Azure Logic Apps ile PlayBook 'lar oluşturmak için, büyüyen bir yerleşik PlayBook Galerisi arasından seçim yapabilirsiniz. Bunlar Azure işlevleri gibi hizmetler için [200 + bağlayıcı](../connectors/apis-list.md) içerir. Bağlayıcılar kodda, ServiceNow, Jira, Zendesk, HTTP istekleri, Microsoft ekipleri, bolluk, Windows Defender ATP ve Cloud App Security özel mantık uygulamanıza olanak tanır.

Örneğin, ServiceNow bilet sistemi kullanıyorsanız, iş akışlarınızı otomatikleştirmek için Azure Logic Apps kullanmak için sunulan araçları kullanabilir ve belirli bir olay algılandığında ServiceNow içinde bir bilet açabilirsiniz.

![Playbook'lar](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Araştırma

Şu anda önizleme aşamasında olan Azure Sentinel [derin araştırma](tutorial-investigate-cases.md) araçları, olası bir güvenlik tehdidi kapsamında kapsamı anlamanıza ve kök nedenini bulmanıza yardımcı olur. Belirli bir varlık için ilginç sorular sormak üzere etkileşimli grafikte bir varlık seçebilir ve bu varlığa ve bu varlığın bağlantı ayrıntılarına gidebilir ve bu da tehdidin temel nedenine ulaşın. 

![Araştırma](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Avlanma

Azure Sentinel 'in, bir uyarı tetiklenene kadar, kuruluşunuzun veri kaynakları genelinde güvenlik tehditleri için güvenli bir şekilde araştırma yapma imkanı sunan, MITRE çerçevesini temel alan, [arama ve sorgu araçlarını](hunting.md)kullanın. Hangi Arayıcı sorgusunun olası saldırılara yönelik yüksek değerli Öngörüler sağladığını bulduktan sonra, sorgunuza göre özel algılama kuralları oluşturabilir ve bu öngörüleri güvenlik olay yanıtlamalarınıza uyarı olarak bırakabilirsiniz. Arama yaparken, ilginç olaylar için yer işaretleri oluşturabilir, daha sonra bu kullanıcılara geri dönebilmeniz, başkalarıyla paylaşmanız ve daha sonra incelemek üzere etkileyici bir olay oluşturmak için bunları diğer ilişkili olaylarla gruplandırmanıza olanak tanır.

![Arama özelliğine genel bakış](./media/overview/hunting.png)

## <a name="community"></a>Topluluk

Azure Sentinel topluluğu, tehdit algılama ve otomasyonu için güçlü bir kaynaktır. Microsoft Güvenlik analistlerimiz sürekli olarak yeni çalışma kitapları, PlayBook 'lar oluşturma ve ekleme, sorguları yönetme ve daha fazlası oluşturup, ortamınızda kullanabilmeniz için bunları topluluğa aktarıyoruz. Özel çalışma kitapları oluşturmak, Azure Sentinel için sorguları, not defterlerini ve PlayBook 'ları yakalamak üzere özel topluluk GitHub [deposundan](https://aka.ms/asicommunity) örnek içerik indirebilirsiniz. 

![Kullanıcı topluluğunu keşfet](./media/overview/community.png)

## <a name="next-steps"></a>Sonraki adımlar

- Azure Sentinel ile çalışmaya başlamak için Microsoft Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/free/) için kaydolabilirsiniz.
- [Verilerinizi Azure Sentinel 'e](quickstart-onboard.md) [ekleme ve verilerinize ilişkin görünürlük alma ve olası tehditler](quickstart-get-visibility.md)hakkında bilgi edinin.