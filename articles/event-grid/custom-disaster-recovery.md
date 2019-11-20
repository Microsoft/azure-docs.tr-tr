---
title: Azure Event Grid özel konularda olağanüstü durum kurtarma
description: Azure Event Grid bağlı tutmak için bölgesel kesintilerden nasıl devam leyeceğinizi öğrenin.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: 7020fb167539e8ad16cc6c386f58e38326dec43b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790273"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Event Grid özel konular için kendi olağanüstü durum kurtarmayı oluşturun
Olağanüstü durum kurtarma, uygulama işlevselliğinin önemli bir kaybından kurtarılmasına odaklanır. Bu öğretici, Event Grid hizmeti belirli bir bölgede sağlıksız hale gelirse, olay mimarinizi kurtarmak üzere nasıl ayarlayabileceğinizi size yol gösterecektir.

Bu öğreticide, Event Grid özel konular için etkin-Pasif yük devretme mimarisi oluşturmayı öğreneceksiniz. İki bölgede konu ve aboneliklerinizi yansıtarak ve ardından bir konu sağlıksız hale geldiğinde bir yük devretmeyi yöneterek yük devretmeyi gerçekleştirirsiniz. Bu öğreticideki mimari tüm yeni trafikle başarısız olur. Bu kurulumla farkında olmak önemlidir. daha önce uçuştaki olaylar, güvenliği aşılan bölge yeniden sağlıklı olana kadar kurtarılmaz.

> [!NOTE]
> Event Grid, şimdi sunucu tarafında otomatik coğrafi olağanüstü durum kurtarmayı (GeoDR) destekler. Yük devretme işlemi üzerinde daha fazla denetim istiyorsanız, hala istemci tarafı olağanüstü durum kurtarma mantığı uygulayabilirsiniz. Otomatik GeoDR hakkında daha fazla bilgi için, bkz. [Azure Event Grid sunucu tarafı coğrafi olağanüstü durum kurtarma](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>İleti uç noktası oluşturma

Yük devretme yapılandırmanızı test etmek için, olaylarınızı almak üzere bir uç noktaya ihtiyacınız vardır. Uç nokta, yük devretme altyapınızın bir parçası değildir, ancak sınamayı kolaylaştırmak için olay işleyicimiz olarak görev yapar.

Sınamayı basitleştirmek için, olay iletilerini görüntüleyen [önceden oluşturulmuş bir Web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtın. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

1. Çözümü aboneliğinize dağıtmak için **Azure'a Dağıt**'ı seçin. Azure portalında parametre değerlerini girin.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarıyla gerçekleştirildikten sonra, web uygulamanızı görüntüleyip çalıştığından emin olun. Web tarayıcısında şu adrese gidin: `https://<your-site-name>.azurewebsites.net`
Daha sonra ihtiyacınız olacak şekilde bu URL 'YI aklınızda olduğunuzdan emin olun.

1. Siteyi görürsünüz ancak henüz yayımlanmış olay yoktur.

   ![Yeni siteyi görüntüleme](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Birincil ve ikincil konularınızı oluşturun

İlk olarak iki Event Grid konu başlığı oluşturun. Bu konular, birincil ve ikincil gibi davranır. Varsayılan olarak, olaylarınız birincil konusundan akacaktır. Birincil bölgede bir hizmet kesintisi varsa, ikincilinizi alır.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. 

1. Ana Azure menüsünün sol üst köşesinden **tüm hizmetler** > **Event Grid** ara ' yı seçin > **Event Grid konular**' ı seçin.

   ![Event Grid konuları menüsü](./media/custom-disaster-recovery/select-topics-menu.png)

    İleride daha kolay erişim sağlamak için Event Grid konular ' ın yanındaki yıldızı seçerek kaynak menüsüne ekleyin.

1. Event Grid konuları menüsünde **+ Ekle** ' yi seçerek birincil konuyu oluşturun.

   * Konuya mantıksal bir ad verin ve izlemeyi kolaylaştırmak için sonek olarak "-Primary" ekleyin.
   * Bu konunun bölgesi, birincil bölgesidir.

     ![Event Grid konu birincil oluşturma iletişim kutusu](./media/custom-disaster-recovery/create-primary-topic.png)

1. Konu oluşturulduktan sonra, bu sayfaya gidin ve **Konu uç noktasını**kopyalayın. URI 'ye daha sonra ihtiyacınız olacak.

    ![Event Grid birincil konu başlığı](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Daha sonra da ihtiyacınız olacak konunun erişim anahtarını alın. Kaynak menüsünde **erişim tuşları** ' na tıklayın ve anahtarı 1 ' i kopyalayın.

    ![Birincil konu anahtarını al](./media/custom-disaster-recovery/get-primary-access-key.png)

1. Konu dikey penceresinde **+ olay aboneliği** ' ne tıklayarak, ön koşullar bölümünde yaptığınız olay alıcısı Web sitesinden abone olmayı bağlayan bir abonelik oluşturun.

   * Olay aboneliğine bir mantıksal ad verin ve izlemeyi kolaylaştırmak için sonek olarak "-Primary" ekleyin.
   * Uç nokta türü Web kancası ' nu seçin.
   * Uç noktasını olay alıcıınızın olay URL 'SI olarak ayarlayın, şöyle bir şey görünmelidir: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Birincil olay aboneliğini Event Grid](./media/custom-disaster-recovery/create-primary-es.png)

1. İkinci konuyu ve aboneliğinizi oluşturmak için aynı akışı tekrarlayın. Bu kez, daha kolay izleme için "-PRIMARY" sonekini "-Secondary" ile değiştirin. Son olarak, farklı bir Azure bölgesine yerleştirdiğinizden emin olun. İstediğiniz yere koyabilmeniz mümkün olsa da, [Azure eşlenmiş bölgelerini](../best-practices-availability-paired-regions.md)kullanmanız önerilir. İkincil konu ve aboneliğin farklı bir bölgeye yerleştirilmesi, birincil bölge aşağı gitse bile yeni olaylarınızın akmasını sağlar.

Şimdi şunları yapmalısınız:

   * Test için bir olay alıcısı Web sitesi.
   * Birincil bölgenizdeki birincil konu.
   * Birincil bir olay aboneliği, birincil konuyu olay alıcısı Web sitesine bağlayan.
   * İkincil bölgenizdeki ikincil konu.
   * Birincil konuyu olay alıcısı Web sitesine bağlayan ikincil bir olay aboneliği.

## <a name="implement-client-side-failover"></a>İstemci tarafı yük devretmeyi uygulama

Bölgesel olarak yedekli konu ve abonelik kurulumuna sahip olduğunuza göre, istemci tarafı yük devretmeyi uygulamaya hazırsınız demektir. Bunu yapmanın birkaç yolu vardır, ancak tüm yük devretme uygulamalarının ortak bir özelliği olacaktır: bir konu artık sağlıklı değilse, trafik diğer konuya yönlendirilir.

### <a name="basic-client-side-implementation"></a>Temel istemci tarafı uygulama

Aşağıdaki örnek kod, öncelikle birincil konu başlığında her zaman yayımlamayı deneyecek basit bir .NET yayımcısıdır. Başarılı olmazsa ikincil konunun yükünü devreder. Her iki durumda da, `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`bir GET yaparak diğer konunun Sağlık API 'sini de denetler. **/Api/Health** uç NOKTASıNDA bir get yapıldığında sağlıklı bir konu, her zaman **200 Tamam** ile yanıt vermelidir.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Deneyin

Tüm bileşenlerinizin hazır olduğuna göre, yük devretme uygulamanızı test edebilirsiniz. Yukarıdaki örneği Visual Studio Code veya sık kullandığınız ortamda çalıştırın. Aşağıdaki dört değeri, başlıklarınızın uç noktaları ve anahtarlarıyla değiştirin:

   * primaryTopic konusu-birincil konunun uç noktası.
   * secondaryTopic-ikincil konu başlığı için uç nokta.
   * primaryTopicKey-birincil konu başlığı için anahtar.
   * secondaryTopicKey-ikincil konu başlığı için anahtar.

Olay yayımcısını çalıştırmayı deneyin. Test olaylarınızı aşağıdaki gibi Event Grid görüntüleyiciniz içinde görmeniz gerekir.

![Birincil olay aboneliğini Event Grid](./media/custom-disaster-recovery/event-grid-viewer.png)

Yük devretmenin çalıştığından emin olmak için, birincil konu anahtarınıza ait birkaç karakteri değiştirerek, bu anahtarı artık geçerli olmayacak hale getirebilirsiniz. Yayımcıyı yeniden çalıştırmayı deneyin. Event Grid görüntüleyicide yeni olayları görmeye devam etmelisiniz, ancak konsolunuza baktığınızda, artık ikincil konu aracılığıyla yayımlanmakta olduklarını görürsünüz.

### <a name="possible-extensions"></a>Olası uzantılar

Bu örneği ihtiyaçlarınıza göre genişletmek için birçok yol vardır. Yüksek hacimli senaryolar için konunun sistem durumu API 'sini bağımsız olarak düzenli olarak denetlemek isteyebilirsiniz. Bu şekilde, bir konu daha aşağı gidiyor olması halinde, her bir tek yayımlama ile onu denetlemeniz gerekmez. Bir konunun sağlıklı olmadığını öğrendikten sonra, ikincil konuyu yayımlamak için varsayılan olarak ' yi kullanabilirsiniz.

Benzer şekilde, özel gereksinimlerinize göre yeniden çalışma mantığı uygulamak isteyebilirsiniz. En yakın veri merkezine yayımlama işlemi, gecikme süresini azaltmanız açısından önemli olursa, yük devredilen bir konunun Sağlık API 'sini düzenli olarak araştırmanız gerekir. Yeniden sağlıklı olduktan sonra, daha yakın veri merkezine yeniden çalışmaya yönelik güvenli olduğunu bilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Http uç noktasında olay alma](./receive-events.md) hakkında bilgi edinin
- [Olayların karma bağlantılar nasıl yönlendirileceğini](./custom-event-to-hybrid-connection.md) öğrenin
- [Azure DNS ve Traffic Manager kullanarak olağanüstü durum kurtarma](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager) hakkında bilgi edinin
