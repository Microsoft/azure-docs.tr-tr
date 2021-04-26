---
title: Kullanıcı analiz araçlarının sorunlarını giderme-Azure Application Insights
description: Sorun giderme kılavuzu-Application Insights ile site ve uygulama kullanımını analiz etme.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a1d77016fdf94de4fdd574b0d4cbd22d6b0b8490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024753"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Application Insights 'de Kullanıcı davranışı analiz araçlarının sorunlarını giderme
[Application Insights ' deki Kullanıcı davranışı analizi araçları](usage-overview.md)hakkında sorularınız var: [Kullanıcılar, oturumlar, etkinlikler](usage-segmentation.md), [funyls](usage-funnels.md), [Kullanıcı akışları](usage-flows.md), [bekletme](usage-retention.md)veya cohorts İşte bazı yanıtlar.

## <a name="counting-users"></a>Kullanıcıları sayma
**Kullanıcı davranışı analiz araçları uygulamamın bir Kullanıcı/oturum olduğunu gösteriyor, ancak uygulamamın çok sayıda kullanıcı/oturum olduğunu biliyorum. Bu hatalı sayıları nasıl giderebilirim?**

Application Insights tüm telemetri olaylarının, standart özelliklerinden ikisi olarak [anonim bir kullanıcı kimliği](./data-model-context.md) ve [oturum kimliği](./data-model-context.md) vardır. Varsayılan olarak, tüm kullanım analizi araçları, kullanıcıları ve oturumları bu kimliklere göre sayar. Bu standart özellikler, uygulamanızın her bir kullanıcısı ve oturumu için benzersiz kimliklerle doldurulmazsa, Kullanım Analizi araçlarında yanlış Kullanıcı ve oturum sayısı görürsünüz.

Bir Web uygulamasını izliyorsanız, en kolay çözüm [Application Insights JavaScript SDK 'sını](./javascript.md) uygulamanıza eklemektir ve komut dosyası parçacığının izlemek istediğiniz her sayfada yüklü olduğundan emin olun. JavaScript SDK 'Sı otomatik olarak anonim kullanıcı ve oturum kimliklerini oluşturur ve ardından, bu kimliklerle telemetri olaylarını, uygulamanızdan gönderildikleri şekilde doldurur.

Bir Web hizmetini (Kullanıcı arabirimi olmadan) izliyorsanız, [Anonim Kullanıcı kimliği ve oturum kimliği özelliklerini](./usage-overview.md) hizmetinizin benzersiz kullanıcı ve oturumlarınıza göre dolduran bir telemetri başlatıcısı oluşturun.

Uygulamanız [kimliği doğrulanmış kullanıcı kimlikleri](./api-custom-events-metrics.md#authenticated-users)gönderiyorsa, kullanıcılar aracında kimliği doğrulanmış kullanıcı kimlikleri ' ne göre sayım yapabilirsiniz. "Göster" açılan menüsünde "kimliği doğrulanmış kullanıcılar" ı seçin.

Kullanıcı davranışı analiz araçları şu anda anonim kullanıcı KIMLIĞI, kimliği doğrulanmış kullanıcı KIMLIĞI veya oturum KIMLIĞI dışındaki özelliklere göre kullanıcıları veya oturumları saymayı desteklememektedir.

## <a name="naming-events"></a>Olayları adlandırma
**Uygulamamın binlerce farklı sayfa görünümü ve özel olay adı vardır. Aralarında ayrım yapmak zordur ve Kullanıcı davranışı analiz araçlarının çoğu zaman yanıt vermemeye başladı. Bu adlandırma sorunlarını nasıl giderebilirim?**

Sayfa görünümü ve özel olay adları, Kullanıcı davranışı analiz araçları boyunca kullanılır. Bu araçlardan değer elde etmek için olayların adlandırılması iyi bir öneme sahiptir. Amaç çok az, fazla genel ada ("düğme tıklandı") sahip olmak ve çok fazla sayıda, aşırı özgü ada sahip olmak (http:/www.contoso.com/index "içinde" Düzenle düğmesi tıklandı ") arasında bir dengedir \/ .

Uygulamanızın gönderdiği sayfa görünümünde ve özel olay adlarında herhangi bir değişiklik yapmak için uygulamanızın kaynak kodunu değiştirmeniz ve yeniden dağıtmanız gerekir. **Application Insights 'Deki tüm telemetri verileri 90 gün boyunca depolanır ve silinemez**, böylece olay adlarında yaptığınız değişiklikler, tam olarak bildirimde bulunur 90 gün sürer. Ad değişikliklerinden sonra 90 gün boyunca, hem eski hem de yeni olay adları telemetrinizde görünür, bu nedenle sorguları ayarlayın ve ekipleriniz içinde iletişim kurun.

Uygulamanız çok fazla sayfa görünümü adı gönderiyorsa, Bu sayfa görünümü adlarının kodda el ile mi belirtilmediğini veya Application Insights JavaScript SDK 'Sı tarafından otomatik olarak gönderilip gönderilmediğini denetleyin:

* Sayfa görünümü adları, [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)kullanılarak kodda el ile belirtilirse, adı daha az özel olacak şekilde değiştirin. URL 'YI Sayfa görünümünün adına yerleştirme gibi yaygın hatalardan kaçının. Bunun yerine, API 'nin URL parametresini kullanın `trackPageView` . Sayfa görünümü adından diğer ayrıntıları özel özelliklere taşıyın.

* Application Insights JavaScript SDK 'Sı otomatik olarak sayfa görünümü adlarını gönderiyorsa, sayfa görünüm adlarını el ile göndermek için sayfalarınızın başlıklarını veya anahtarını değiştirebilirsiniz. SDK, her sayfanın [başlığını](https://developer.mozilla.org/docs/Web/HTML/Element/title) varsayılan olarak sayfa görünümü adı olarak gönderir. Başlıklarınızı daha genel olacak şekilde değiştirebilirsiniz, ancak bu değişiklik, bu değişikliğin sahip olduğu diğer etkileri ve diğer etkileri de olabilir. Sayfa görünümü adlarını API ile el ile belirtmek `trackPageView` otomatik olarak toplanan adları geçersiz kılar, bu nedenle, sayfa başlıklarını değiştirmeden telemetri içinde daha fazla genel ad gönderebilirsiniz.   

Uygulamanız çok fazla özel olay adı gönderiyorsa, koddaki adı daha az özel olacak şekilde değiştirin. Bu durumda, URL 'Leri ve diğer sayfa başına ya da dinamik bilgileri doğrudan özel olay adlarına yerleştirmekten kaçının. Bunun yerine, bu ayrıntıları API ile özel olayın özel özelliklerine taşıyın `trackEvent` . Örneğin, yerine, `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` gibi bir şey öneririz `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` .

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanıcı davranışı analiz araçlarına genel bakış](usage-overview.md)

## <a name="get-help"></a>Yardım alın
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)