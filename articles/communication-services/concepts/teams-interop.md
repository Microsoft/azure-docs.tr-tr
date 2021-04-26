---
title: Ekipte birlikte çalışabilirlik toplantısı
titleSuffix: An Azure Communication Services concept document
description: Ekip toplantılarına katılarak
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cf6553cd7c59febd19f9654e31188f127b8eb065
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276765"
---
# <a name="teams-interoperability"></a>Teams ile birlikte çalışabilirlik

[!INCLUDE [Public Preview](../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> [Ekipte kiracı birlikte çalışabilirliğini](../concepts/teams-interop.md)etkinleştirmek/devre dışı bırakmak için [Bu formu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)doldurun.

Azure Iletişim Hizmetleri, Microsoft ekipleriyle etkileşime geçen özel toplantı deneyimleri oluşturmak için kullanılabilir. Iletişim Hizmetleri çözümünüzün kullanıcıları ses, video, sohbet ve ekran paylaşımı üzerinde takımlar katılımcıları ile etkileşime geçebilir.

Takımlar birlikte çalışabilirliği, kullanıcıları takımlar toplantılarına bağlayan özel uygulamalar oluşturmanıza olanak tanır. Özel uygulamalarınızın kullanıcılarının, bu özelliği denemek için Azure Active Directory kimlikleri veya takımlar lisanslarına sahip olması gerekmez. Bu, çalışanları (ekiplere tanıdık gelebilir) ve harici kullanıcıları (özel bir uygulama deneyimi kullanarak) sorunsuz bir toplantı deneyimiyle birlikte getirmek için idealdir. Örnek:

1. Çalışanlar bir toplantıyı zamanlamak için takımlar kullanır 
1. Toplantı ayrıntıları, özel uygulamanız aracılığıyla dış kullanıcılarla paylaşılır.
   * **Graph API kullanma** Özel Iletişim Hizmetleri uygulamanız, paylaşılan toplantı ayrıntılarına erişmek için Microsoft Graph API 'Lerini kullanır. 
   * **Diğer seçenekleri kullanma** Örneğin, Toplantı bağlantınız Microsoft ekiplerinde takviminizden kopyalanabilir.
1. Dış kullanıcılar, takım toplantısına katmak için özel uygulamanızı kullanır (Iletişim Hizmetleri çağırma ve sohbet SDK 'Ları aracılığıyla)

Bu kullanım örneği için üst düzey mimari şöyle görünür: 

![Takımlar için mimari birlikte çalışma](./media/call-flows/teams-interop.png)

Belirli takımlar, kabarık, birlikte modu ve bir arada bulunan odalar gibi özellikler de yalnızca takımlar kullanıcıları tarafından kullanılabilir olacaktır, ancak özel uygulamanız toplantının çekirdek ses, video, sohbet ve ekran paylaşım özelliklerine erişebilir. Toplantı sohbeti, çağrıdayken özel uygulama kullanıcılarınız tarafından erişilebilecektir. Çağrıdan çıkmadan veya çağrıdan önce iletileri gönderemezler veya alamaz. 

Bir Iletişim Hizmetleri kullanıcısı takımlar toplantısına katıldığında, çağıran SDK aracılığıyla verilen görünen ad takımlar kullanıcıları tarafından gösterilir. Iletişim Hizmetleri kullanıcısı başka şekilde ekiplerde anonim bir kullanıcı gibi ele alınacaktır.  Özel uygulamanız, takımlar toplantılarını korumak için Kullanıcı kimlik doğrulamasını ve diğer güvenlik önlemlerini göz önünde bulundurmalıdır. Anonim kullanıcıların toplantılara katılmasına imkan tanıyan güvenlik etkilerine karşı dikkatli olun ve anonim kullanıcılara sunulan özellikleri yapılandırmak için [takımlar güvenlik kılavuzunu](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) kullanın.

İletişim hizmetleri ekiplerinin birlikte çalışması şu anda özel önizlemededir. Genel olarak kullanılabilir olduğunda, Iletişim Hizmetleri kullanıcıları "dış erişim kullanıcıları" gibi kabul edilir. [Microsoft ekiplerinde kuruluşunuzun dışındaki kişilerle çağrı, sohbet ve işbirliği yapma](/microsoftteams/communicate-with-users-from-other-organizations)hakkında daha fazla bilgi edinin.

İletişim Hizmetleri kullanıcıları, [Toplantı ayarlarında](/microsoftteams/meeting-settings-in-teams)anonim birleşimler etkinleştirildiği sürece zamanlanan takımlar toplantılarına katılabilir. Toplantı bir kanal için zamanlanırsa, Iletişim Hizmetleri kullanıcıları sohbet 'e katılamaz veya ileti gönderemez veya alamaz.

## <a name="teams-in-government-clouds-gcc"></a>Kamu bulutlarındaki takımlar (GCC)
Azure Iletişim Hizmetleri birlikte çalışabilirliği, şu anda [Microsoft 365 kamu bulutları (GCC)](/MicrosoftTeams/plan-for-government-gcc) kullanılarak gerçekleştirilen takımlar dağıtımıyla uyumlu değildir. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Arama uygulamanızla bir Teams toplantısına katılma](../quickstarts/voice-video-calling/get-started-teams-interop.md)
