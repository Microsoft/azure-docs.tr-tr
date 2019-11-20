---
title: Paylaşılan erişim Imzaları ile erişim denetimi Azure Service Bus | Microsoft Docs
description: Paylaşılan erişim Imzaları ile ilgili Service Bus erişim denetimine genel bakış, Azure Service Bus ile SAS yetkilendirmesi hakkında ayrıntılar.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: ac240fee9a71714f2c7368b43e60f4e6c5d7093d
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013061"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Paylaşılan erişim Imzaları ile erişim denetimi Service Bus

*Paylaşılan erişim imzaları* (SAS) Service Bus mesajlaşma için birincil güvenlik mekanizmasıdır. Bu makalede SAS ve bunların nasıl çalıştığı ve bunların platformdan bağımsız bir şekilde nasıl kullanılacağı açıklanmaktadır.

SAS koruyucuları, yetkilendirme kurallarına göre Service Bus erişimi sağlar. Bunlar bir ad alanında ya da bir mesajlaşma varlığında (geçiş, kuyruk veya konu) yapılandırılır. Yetkilendirme kuralı bir ada sahiptir, belirli haklara göre ilişkilendirilir ve bir çift şifreleme anahtarı taşır. Bir SAS belirteci oluşturmak için Service Bus SDK aracılığıyla veya kendi kodunuzda kural adını ve anahtarını kullanın. İstemci daha sonra istenen işlem için kanıtlamak üzere Service Bus belirtecini geçirebilir.

> [!NOTE]
> Azure Service Bus, Azure Active Directory (Azure AD) kullanarak bir Service Bus ad alanına ve varlıklarına erişim yetkisi verme desteği sağlar. Azure AD tarafından döndürülen OAuth 2,0 belirtecini kullanarak kullanıcıları veya uygulamaları yetkilendirmek, paylaşılan erişim imzaları (SAS) üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Azure AD ile, belirteçlerin kodunuzda depolanması ve olası güvenlik açıklarına karşı risk altında olması gerekmez.
>
> Microsoft, mümkün olduğunda Azure Service Bus uygulamalarınızın Azure AD 'yi kullanmasını önerir. Daha fazla bilgi için aşağıdaki makalelere bakın:
> - [Azure Service Bus varlıklara erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama ve yetkilendirme](authenticate-application.md).
> - [Azure Service Bus kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>SAS 'ye Genel Bakış

Paylaşılan erişim Imzaları, basit belirteçler kullanan talep tabanlı bir yetkilendirme mekanizmasıdır. SAS kullanarak, anahtarlar hiçbir şekilde hatta hiçbir şekilde geçirilmez. Anahtarlar, daha sonra hizmet tarafından doğrulanabileolabilecek bilgileri şifreli olarak imzalamak için kullanılır. SAS, istemcinin bir yetkilendirme kuralı adının ve eşleşen bir anahtarın hemen elinde bulunduğu bir Kullanıcı adı ve parola şemasına benzer şekilde kullanılabilir. SAS, bir güvenlik belirteci hizmetinden herhangi bir zamana sınırlı ve imzalı erişim belirtecini, bu da oturum açmadan bir yerde değil, bir Federasyon güvenlik modeline benzer şekilde de kullanılabilir.

Service Bus içinde SAS kimlik doğrulaması, ilişkili erişim hakları olan adlandırılmış [paylaşılan erişim yetkilendirme kurallarıyla](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) ve bir çift birincil ve ikincil şifreleme anahtarı ile yapılandırılır. Anahtarlar, Base64 gösteriminde 256 bitlik değerlerdir. Kuralları, Service Bus [geçişleri](../service-bus-relay/relay-what-is-it.md), [kuyrukları](service-bus-messaging-overview.md#queues)ve [konuları](service-bus-messaging-overview.md#topics), ad alanı düzeyinde yapılandırabilirsiniz.

[Paylaşılan erişim imza](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) belirteci, seçilen yetkilendirme kuralının adını, ERIŞILECEK kaynağın URI 'sini, bir süre sonu anında ve bir HMAC-SHA256 şifreleme imzasını, birincil ya da Seçilen yetkilendirme kuralının ikincil şifreleme anahtarı.

## <a name="shared-access-authorization-policies"></a>Paylaşılan erişim yetkilendirme Ilkeleri

Her bir Service Bus ad alanı ve her Service Bus varlığı, kurallarından oluşan bir paylaşılan erişim yetkilendirme ilkesine sahiptir. Ad alanı düzeyindeki ilke, bireysel ilke yapılandırmasından bağımsız olarak ad alanındaki tüm varlıklar için geçerlidir.

Her yetkilendirme ilkesi kuralı için üç bilgi parçasına karar verirsiniz: **ad**, **kapsam**ve **haklar**. **Ad** yalnızca bu şekilde yapılır; Bu kapsam içindeki benzersiz bir ad. Kapsam yeterince kolay: Bu, söz konusu kaynağın URI 'sidir. Service Bus ad alanı için kapsam, gibi tam etki alanı adıdır (FQDN) `https://<yournamespace>.servicebus.windows.net/`.

İlke kuralına göre Rights basıp basmadığını değerlendirerek, şunların bir birleşimi olabilir:

* Varlığa ileti gönderme hakkını ' Gönder '-Yapılandırmacılar
* ' Dinleyin '-dinleme (geçiş) veya alma (kuyruk, abonelik) ve tüm ilgili ileti işleme hakkını derler
* ' Yönet '-varlık oluşturma ve silme dahil olmak üzere ad alanının topolojisini yönetme hakkını yapılandırın

' Yönet ' hakkı ' Send ' ve ' Receive ' haklarını içerir.

Bir ad alanı veya varlık ilkesi, her biri temel hakları ve gönderme ve dinleme birleşimini kapsayan, en fazla 12 paylaşılan erişim yetkilendirme kuralını tutabilir, üç kural kümesine yer sağlar Bu sınır, SAS ilke deposunun bir kullanıcı veya hizmet hesabı deposu olması amaçlanmayan alt çizgileri çizer. Uygulamanızın Kullanıcı veya hizmet kimliklerine göre Service Bus erişim vermesi gerekiyorsa, bir kimlik doğrulama ve erişim denetiminden sonra SAS belirteçleri veren bir güvenlik belirteci hizmeti uygulamalıdır.

Bir yetkilendirme kuralına *birincil anahtar* ve *İkincil anahtar*atanır. Bunlar şifreli olarak güçlü anahtarlardır. Onları kaybetmeyin veya sızmayın; [Azure Portal][Azure portal]her zaman kullanılabilir. Oluşturulan anahtarlardan birini kullanabilir ve istediğiniz zaman yeniden oluşturabilirsiniz. İlkede bir anahtarı yeniden oluşturursanız veya değiştirirseniz, o anahtara göre daha önce verilen tüm belirteçler anında geçersiz olur. Ancak, bu tür belirteçlere göre oluşturulan devam eden bağlantılar, belirtecin süresi dolana kadar çalışmaya devam edecektir.

Bir Service Bus ad alanı oluşturduğunuzda, ad alanı için **RootManageSharedAccessKey** adlı bir ilke kuralı otomatik olarak oluşturulur. Bu ilke, tüm ad alanı için yönetme izinlerine sahiptir. Bu kuralı bir yönetim **kök** hesabı gibi işlemeniz önerilir ve uygulamanızda kullanmayın. Portalda ad alanı için **yapılandırma** sekmesinde PowerShell veya Azure CLI aracılığıyla ek ilke kuralları oluşturabilirsiniz.

## <a name="configuration-for-shared-access-signature-authentication"></a>Paylaşılan erişim Imzası kimlik doğrulaması yapılandırması

[Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) kuralını Service Bus ad alanları, kuyruklar veya konular üzerinde yapılandırabilirsiniz. Bir Service Bus abonelikte [Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) yapılandırması şu anda desteklenmiyor, ancak aboneliklerde erişim sağlamak için bir ad alanı veya konu üzerinde yapılandırılan kuralları kullanabilirsiniz. Bu yordamı gösteren bir çalışan örnek için, [Service Bus abonelikleri Ile paylaşılan erişim imzası (SAS) kimlik doğrulamasını kullanma](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) örneği ' ne bakın.

!['LARININ](./media/service-bus-sas/service-bus-namespace.png)

Bu şekilde, hem S1 hem de Sendruleq ' ın yalnızca S1 kuyruğuna ve *Sendrulet* 'e uyguladığı, bu şekilde, duılens, *sendrulens*ve *listenrulens* yetkilendirme kuralları her ikisi de S1 ve sıra için geçerlidir. konulu T1.

## <a name="generate-a-shared-access-signature-token"></a>Paylaşılan erişim Imza belirteci oluştur

Bir yetkilendirme kuralı adı adına erişimi olan tüm istemciler ve imzalama anahtarlarından biri bir SAS belirteci oluşturabilir. Belirteç, aşağıdaki biçimde bir dize oluşturarak oluşturulur:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Token süre sonu anında. Belirtecin süresi sona erdiğinde 1 Ocak `00:00:00 UTC` 1970 (UNIX dönemi) üzerinden dönem bu yana bir saniye yansıtarak tamsayı.
* **`skn`** -Yetkilendirme kuralının adı.
* **`sr`** -Erişilmekte olan kaynağın URI 'SI.
* **`sig`** İmza.

, `signature-string` Kaynak URI üzerinden (önceki bölümde açıklandığı gibi**kapsam** ) ve CRLF ile ayrılmış olarak belirteç süre sonu anlık öğesinin dize gösterimine göre hesaplanan SHA-256 karmasıdır.

Karma hesaplama aşağıdaki sözde koda benzer ve 256 bit/32 baytlık karma değer döndürür.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Belirteç, karma değeri aynı parametrelerle yeniden hesaplanabilmesi, böylece veren 'in geçerli bir imzalama anahtarına sahip olduğu doğrulanıyor.

Kaynak URI 'SI, erişimin talep aldığı Service Bus kaynağın tam URI 'sidir. Örneğin, `http://<namespace>.servicebus.windows.net/<entityPath>` veya `sb://<namespace>.servicebus.windows.net/<entityPath>`,,,. `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3` 

**URI [yüzde kodlamalı](https://msdn.microsoft.com/library/4fkewx0t.aspx)olmalıdır.**

İmzalama için kullanılan paylaşılan erişim yetkilendirme kuralı, bu URI tarafından belirtilen varlıkta veya hiyerarşik üst öğelerinden biri ile yapılandırılmış olmalıdır. Örneğin, `http://contoso.servicebus.windows.net/contosoTopics/T1` veya `http://contoso.servicebus.windows.net` önceki örnekte.

SAS belirteci, `<resourceURI>` `signature-string`içinde kullanılan tüm kaynaklar için geçerlidir.

## <a name="regenerating-keys"></a>Anahtarlar yeniden oluşturuluyor

[Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nesnesinde kullanılan anahtarları düzenli olarak yeniden oluşturmanız önerilir. Anahtarları yavaş bir şekilde döndürebilmeniz için birincil ve ikincil anahtar yuvaları vardır. Uygulamanız birincil anahtarı genel olarak kullanıyorsa, birincil anahtarı ikincil anahtar yuvasına kopyalayabilir ve ardından birincil anahtarı yeniden oluşturabilirsiniz. Daha sonra yeni birincil anahtar değeri, ikincil yuvada eski birincil anahtar kullanılarak erişmeye devam eden istemci uygulamalarına yapılandırılabilir. Tüm istemciler güncelleştirildikten sonra, son olarak eski birincil anahtarı devre dışı bırakmak için ikincil anahtarı yeniden oluşturabilirsiniz.

Bir anahtarın güvenliğinin aşıldığını biliyorsanız veya kuşkulanıyorsanız ve anahtarları iptal etmeniz gerekiyorsa, bir [Sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)öğesinin [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) ve [secondarykey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) anahtarlarını yeni anahtarlarla değiştirerek yeniden oluşturabilirsiniz. Bu yordam, eski anahtarlarla imzalanan tüm belirteçleri geçersiz kılar.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Service Bus ile paylaşılan erişim Imzası kimlik doğrulaması

Aşağıdaki gibi açıklanan senaryolar, yetkilendirme kurallarının yapılandırılması, SAS belirteçlerinin oluşturulması ve istemci yetkilendirmesi içerir.

Yapılandırmayı gösteren ve SAS yetkilendirmesi kullanan bir Service Bus uygulamasının tam çalışma örneği için, bkz. [Service Bus Ile paylaşılan erişim imzası kimlik doğrulaması](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ad alanlarında veya konularda güvenli Service Bus aboneliklerde yapılandırılan SAS Yetkilendirme kurallarının kullanımını gösteren ilgili bir örnek buradan edinilebilir: [Service Bus abonelikleri Ile paylaşılan erişim imzası (SAS) kimlik doğrulamasını kullanma](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Bir varlıktaki paylaşılan erişim yetkilendirme kurallarına erişme

Service Bus .NET Framework kitaplıklarıyla, karşılık gelen bir Service Bus kuyruğu veya konu başlığında yapılandırılan [Microsoft. ServiceBus. Messaging. SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nesnesine karşılık gelen [](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) [Queuedescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) veya [topicdescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

Aşağıdaki kod, bir sıranın yetkilendirme kurallarının nasıl ekleneceğini gösterir.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Paylaşılan erişim Imzası yetkilendirmesini kullanma

Azure .NET SDK 'sını Service Bus .NET kitaplıklarıyla kullanan uygulamalar, [Sharedaccesssignaturetokenprovider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) sınıfı aracılığıyla SAS yetkilendirmesi kullanabilir. Aşağıdaki kod, Service Bus kuyruğuna ileti göndermek için belirteç sağlayıcısının kullanımını gösterir. Burada gösterilen kullanım için alternatif olarak, belirteç sağlayıcı fabrikası yöntemine daha önce verilen bir belirteci geçirebilirsiniz.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Belirteç sağlayıcıyı, diğer istemcilere geçirilecek belirteçleri vermek için doğrudan de kullanabilirsiniz.

Bağlantı dizeleri bir kural adı (*Sharedaccesskeyname*) ve kural anahtarı (*Sharedaccesskey*) veya daha önce verilen bir belirteç (*sharedaccesssignature*) içerebilir. Bir bağlantı dizesini kabul eden herhangi bir oluşturucuya veya fabrika yöntemine geçirilen bağlantı dizesinde mevcut olduğunda, SAS belirteç sağlayıcısı otomatik olarak oluşturulur ve doldurulur.

Service Bus geçişleri ile SAS yetkilendirmesi kullanmak için Service Bus ad alanında yapılandırılmış SAS anahtarlarını kullanabileceğinizi unutmayın. Ad alanında ( [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) bir nesne olarak açıkça bir[](/dotnet/api/microsoft.servicebus.namespacemanager) geçiş oluşturursanız, bu geçiş için SAS kurallarını ayarlayabilirsiniz. Service Bus abonelikleriyle SAS yetkilendirmesi kullanmak için, Service Bus bir ad alanında veya bir konuda yapılandırılmış SAS anahtarlarını kullanabilirsiniz.

## <a name="use-the-shared-access-signature-at-http-level"></a>Paylaşılan erişim Imzasını kullanın (HTTP düzeyinde)

Artık Service Bus bir varlık için paylaşılan erişim Imzaları oluşturmayı bildiğinize göre, bir HTTP GÖNDERISI gerçekleştirmeye hazırsınız:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Bu, her şey için kullanılabilir olduğunu unutmayın. Bir kuyruk, konu veya abonelik için SAS oluşturabilirsiniz.

Bir göndereni veya istemciye bir SAS belirteci verirseniz, bu anahtara doğrudan sahip olmaz ve karmayı geri çeviremez. Bu nedenle, ne kadar erişebileceklerini ve ne kadar süreyle erişebileceğini kontrol edersiniz. Anımsanması gereken önemli bir şey, ilkedeki birincil anahtarı değiştirirseniz, bundan oluşturulan tüm paylaşılan erişim Imzalarının geçersiz kılındığını unutmayın.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Paylaşılan erişim Imzasını kullanın (AMQP düzeyinde)

Önceki bölümde, Service Bus veri göndermek için bir HTTP POST isteğiyle SAS belirtecini nasıl kullanacağınızı gördünüz. Bildiğiniz gibi, birçok senaryoda performans nedenleriyle kullanılacak tercih edilen protokol olan Gelişmiş İleti Sıraya Alma Protokolü (AMQP) kullanarak Service Bus erişebilirsiniz. AMQP ile SAS belirteci kullanımı, 1,0 ' 2013 den bu yana çalışan taslak ve bugün Azure tarafından iyi şekilde desteklenen belge [AMQP talep tabanlı güvenlik sürüm](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) ' de açıklanmaktadır.

Service Bus verileri gönderilmeye başlamadan önce, Yayımcı, bir AMQP iletisinin içindeki SAS belirtecini **$CBS** adlı iyi tanımlanmış bir AMQP düğümüne göndermelidir (tüm SAS belirteçlerini almak ve doğrulamak üzere hizmet tarafından kullanılan bir "özel" kuyruğu olarak görebilirsiniz). Yayımcının AMQP iletisi içinde **ReplyTo** alanını belirtmesi gerekir; Bu, hizmetin, bir yayımcının belirteç doğrulama sonucuyla (yayımcı ve hizmet arasında basit bir istek/yanıt düzeniyle) yanıt veren düğümüdür. Bu yanıt düğümü, AMQP 1,0 belirtiminde açıklanan "anında" uzak düğüm oluşturma "hakkında konuşuyor. SAS belirtecinin geçerli olup olmadığını kontrol ettikten sonra yayımcı, hizmete veri gönderilmeye başlayabilir ve çalışmaya başlayabilir.

Aşağıdaki adımlarda, [AMQP.net Lite](https://github.com/Azure/amqpnetlite) kitaplığı kullanılarak AMQP protokolü ile SAS belirtecinin nasıl gönderileceği gösterilmektedir. Bu, resmi Service Bus SDK 'Yı (örneğin, WinRT, .NET Compact Framework, .NET mikro Framework ve mono) C\#'de geliştirmeye yönelik olarak kullanıyorsanız faydalıdır. Tabii ki bu kitaplık, HTTP düzeyinde nasıl çalıştığını (bir HTTP POST isteği ve "Authorization" üst bilgisinde gönderilen SAS belirteci ile birlikte) gördüğünüz gibi, talep tabanlı güvenliğin AMQP düzeyinde nasıl çalıştığını anlamanıza yardımcı olmak için yararlıdır. AMQP hakkında ayrıntılı bilgiye ihtiyacınız yoksa, resmi Service Bus SDK 'sını .NET Framework uygulamalarla kullanabilirsiniz. Bu işlem sizin için yapılır.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Yöntemi `PutCbsToken()` , hizmetine TCP bağlantısını temsil eden [AMQP .net Lite kitaplığı](https://github.com/Azure/amqpnetlite)tarafından sağlandığı bir *bağlantı* (AMQP bağlantı sınıfı örneği) ve göndermek için SAS belirteci olan *sastoken* parametresi alır.

> [!NOTE]
> Bağlantının, **SASL kimlik doğrulama MEKANIZMASı anonim olarak ayarlanmış şekilde** oluşturulması önemlıdır (SAS belirtecini göndermeniz gerekmiyorsa Kullanıcı adı ve parola Ile varsayılan düz değil).
>
>

Ardından, Yayımcı, SAS belirtecini göndermek ve hizmeti yanıt (belirteç doğrulama sonucu) almak için iki AMQP bağlantısı oluşturur.

AMQP iletisi bir dizi özellik ve basit bir iletiden daha fazla bilgi içerir. SAS belirteci, iletinin gövdesidir (oluşturucusunu kullanarak). **"ReplyTo"** özelliği, alıcı bağlantısında doğrulama sonucunu almak için düğüm adına ayarlanır (isterseniz adını değiştirebilir ve hizmet tarafından dinamik olarak oluşturulur). Son üç uygulama/özel özellik, hizmet tarafından yürütülmesi gereken işlem türünü belirtmek için kullanılır. CBS taslak belirtiminde açıklandığı gibi, bunların **işlem adı** ("put-token"), **belirtecin türü** (Bu durumda, a `servicebus.windows.net:sastoken`) ve belirtecin uygulandığı **hedef kitlelerinin "adı"** (tüm varlık) olması gerekir.

Gönderen bağlantısına SAS belirtecini gönderdikten sonra, yayımcı alıcı bağlantısındaki yanıtı okumalı olmalıdır. Yanıt, bir HTTP durum kodu ile aynı değerleri içerebilen **"Status-Code"** adlı bir uygulama özelliği olan basıt bır AMQP iletisidir.

## <a name="rights-required-for-service-bus-operations"></a>Service Bus işlemler için gereken haklar

Aşağıdaki tabloda Service Bus kaynakları üzerinde çeşitli işlemler için gereken erişim hakları gösterilmektedir.

| Çalışma | Talep gerekiyor | Talep kapsamı |
| --- | --- | --- |
| **Namespace** | | |
| Ad alanında yetkilendirme kuralını yapılandırma |Yönet |Herhangi bir ad alanı adresi |
| **Hizmet kayıt defteri** | | |
| Özel Ilkeleri listeleme |Yönet |Herhangi bir ad alanı adresi |
| Ad alanı dinlemeye başla |Dinle |Herhangi bir ad alanı adresi |
| Ad alanındaki bir dinleyiciye ileti gönderme |Gönder |Herhangi bir ad alanı adresi |
| **Sıradaki** | | |
| Kuyruk oluştur |Yönet |Herhangi bir ad alanı adresi |
| Bir kuyruk silme |Yönet |Geçerli bir sıra adresi |
| Kuyrukları listeleme |Yönet |/$Resources/Queues |
| Sıra açıklamasını alma |Yönet |Geçerli bir sıra adresi |
| Bir kuyruğun yetkilendirme kuralını yapılandırma |Yönet |Geçerli bir sıra adresi |
| Sıraya gönder |Gönder |Geçerli bir sıra adresi |
| Kuyruktan ileti alma |Dinle |Geçerli bir sıra adresi |
| İletiyi Peek-kilit modunda aldıktan sonra iletileri iptal edin veya doldurun |Dinle |Geçerli bir sıra adresi |
| Daha sonra alımı için bir ileti erteleyin |Dinle |Geçerli bir sıra adresi |
| İletiyi yok harfe göre |Dinle |Geçerli bir sıra adresi |
| İleti kuyruğu oturumuyla ilişkili durumu alma |Dinle |Geçerli bir sıra adresi |
| İleti kuyruğu oturumuyla ilişkili durumu ayarlama |Dinle |Geçerli bir sıra adresi |
| Daha sonra teslim için bir ileti zamanlayın; Örneğin, [Schedulemessageasync ()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Dinle | Geçerli bir sıra adresi
| **Konu** | | |
| Konu oluştur |Yönet |Herhangi bir ad alanı adresi |
| Konuyu silme |Yönet |Herhangi bir geçerli konu adresi |
| Konuları listeleme |Yönet |/$Resources/konular |
| Konu açıklamasını alın |Yönet |Herhangi bir geçerli konu adresi |
| Bir konu için yetkilendirme kuralını yapılandırma |Yönet |Herhangi bir geçerli konu adresi |
| Konuya gönder |Gönder |Herhangi bir geçerli konu adresi |
| **Abonelik** | | |
| Abonelik oluştur |Yönet |Herhangi bir ad alanı adresi |
| Aboneliği silin |Yönet |../myTopic/Subscriptions/mySubscription |
| Abonelikleri listeleme |Yönet |../ myTopic/abonelikleri |
| Abonelik açıklamasını al |Yönet |../myTopic/Subscriptions/mySubscription |
| İletiyi Peek-kilit modunda aldıktan sonra iletileri iptal edin veya doldurun |Dinle |../myTopic/Subscriptions/mySubscription |
| Daha sonra alımı için bir ileti erteleyin |Dinle |../myTopic/Subscriptions/mySubscription |
| İletiyi yok harfe göre |Dinle |../myTopic/Subscriptions/mySubscription |
| Konu bir oturumla ilişkili durumu Al |Dinle |../myTopic/Subscriptions/mySubscription |
| Konu bir oturumla ilişkili durumu ayarlama |Dinle |../myTopic/Subscriptions/mySubscription |
| **kuralları** | | |
| Kural oluşturma |Yönet |../myTopic/Subscriptions/mySubscription |
| Kuralı silme |Yönet |../myTopic/Subscriptions/mySubscription |
| Kuralları listeleme |Yönetme veya dinleme |../myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma hizmeti hakkında daha fazla bilgi edinmek için aşağıdaki konu başlıklarına bakın.

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyruklarını kullanma](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
