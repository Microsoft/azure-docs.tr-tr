---
title: 'Öğretici: Azure WCF Geçişi kullanarak şirket içi WCF REST hizmetini dış istemciye kullanıma sunma'
description: 'Öğretici: WCF Geçişi kullanarak bir istemci ve hizmet uygulaması oluşturun.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: e2dd0448dfed55450a6319936f49831e5d6d77f3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718851"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Öğretici: Azure WCF Geçişi kullanarak şirket içi WCF REST hizmetini dış istemciye kullanıma sunma

Bu öğreticide, Azure Relay kullanarak WCF Geçişi istemci uygulaması ve hizmetinin nasıl oluşturulacağı açıklanmaktadır. [Service Bus mesajlaşma](../service-bus-messaging/service-bus-messaging-overview.md)kullanan benzer bir öğretici için bkz. [Service Bus kuyrukları kullanmaya başlama](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Bu öğreticide çalışmak, bir WCF Geçişi istemci ve hizmet uygulaması oluşturma adımlarını kavramak için size kılavuzluk sağlar. Özgün WCF karşılıklarına benzer şekilde, bir hizmet, bir veya daha fazla uç nokta sunan bir yapıdır. Her uç nokta bir veya daha fazla hizmet işlemini kullanıma sunar. Bir hizmetin uç noktası hizmetin bulunabileceği bir adres, istemcinin hizmetle iletişiminde paylaşması gereken bilgileri içeren bir bağlama ve hizmet tarafından istemcilerine sağlanan işlevselliği tanımlayan bir sözleşme belirtir. WCF ve WCF Geçişi arasındaki temel fark, uç noktanın bilgisayarınızda yerel olarak değil bulutta sunulmasıdır.

Bu öğreticideki bölüm dizisinde çalıştıktan sonra çalışan bir hizmete sahip olacaksınız. Ayrıca hizmetin işlemlerini çağırabilen bir istemceceksiniz. 

Bu öğreticide aşağıdaki görevleri gerçekleştirebilirsiniz:

> [!div class="checklist"]
>
> * Bu öğretici için önkoşulları yükler.
> * Geçiş ad alanı oluşturun.
> * Bir WCF hizmet sözleşmesi oluşturun.
> * WCF sözleşmesini uygulayın.
> * Geçiş hizmetine kaydolmak için WCF hizmetini barındırın ve çalıştırın.
> * Hizmet sözleşmesi için bir WCF istemcisi oluşturun.
> * WCF istemcisini yapılandırın.
> * WCF istemcisini uygulayın.
> * Uygulamaları çalıştırın.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* [Visual Studio 2015 veya üzeri](https://www.visualstudio.com). Bu öğreticideki örneklerde Visual Studio 2019 kullanılır.
* .NET için Azure SDK. [SDK İndirmeleri sayfasından](https://azure.microsoft.com/downloads/)bu uygulamayı yükler.

## <a name="create-a-relay-namespace"></a>Geçiş ad alanı oluşturma

İlk adım, bir ad alanı oluşturmak ve [paylaşılan erişim imzası (SAS)](../service-bus-messaging/service-bus-sas.md) anahtarı elde etmek için kullanılır. Ad alanı, geçiş hizmeti aracılığıyla kullanıma sunulan her uygulama için bir uygulama sınırı sağlar. Bir hizmet ad alanı oluşturulduğunda sistem tarafından bir SAS anahtarı otomatik olarak oluşturulur. Hizmet ad alanı ve SAS anahtarı birleşimi, bir uygulamaya erişimin kimliğini doğrulamak için Azure kimlik bilgilerini sağlar.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>WCF hizmet sözleşmesi tanımlama

Hizmet sözleşmesi, hizmetin desteklediği işlemleri belirtir. İşlemler Web hizmeti yöntemleri veya işlevleridir. Sözleşmeler; C++, C# veya Visual Basic arabirimi tanımlamasıyla oluşturulur. Arabirimdeki her yöntem belirli bir hizmet işlemine karşılık gelir. Her arabirimde [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) özniteliğinin ve her işlemde de [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) özniteliğinin uygulanmış olması gerekir. [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) özniteliğine sahip bir arabirimdeki bir yöntemde [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) özniteliği yoksa, bu yöntem gösterilmez. Bu görevlere ilişkin kod, aşağıdaki yordamın altındaki örnekte sağlanır. Sözleşmelerin ve hizmetlerin daha büyük bir tartışması için bkz. [Hizmetleri tasarlama ve uygulama](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Arabirim ile geçiş sözleşmesi oluşturma

1. Microsoft Visual Studio yönetici olarak başlatın. Bunu yapmak için, Visual Studio program simgesine sağ tıklayın ve **yönetici olarak çalıştır**' ı seçin.
1. Visual Studio 'da **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, Için C# konsol uygulaması (.NET Framework) öğesini seçin ve **İleri**' yi seçin.
1. Projeyi *yankı hizmetini* adlandırın ve **Oluştur**' u seçin.

   ![Konsol uygulaması oluşturma][2]

1. **Çözüm Gezgini**, projeye sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **NuGet Paket Yöneticisi**' nde, **Araştır**' ı seçin, ardından arama yapın ve **windowsazure. ServiceBus**öğesini seçin. **Yükler**' i seçin ve kullanım koşullarını kabul edin.

    ![Service Bus paketi][3]

   Bu paket, Service Bus kitaplıklarına ve WCF `System.ServiceModel`başvuruları otomatik olarak ekler. [System.ServiceModel](/dotnet/api/system.servicemodel), WCF'nin temel özelliklerine programlamayla erişmenizi sağlayan ad alanıdır. Service Bus, hizmet sözleşmelerini tanımlamak için WCF'nin birçok nesnesini ve özniteliklerini kullanır.

1. Aşağıdaki `using` deyimlerini *program.cs*üst kısmına ekleyin:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. `EchoService` olan varsayılan ad alanı adını `Microsoft.ServiceBus.Samples` olarak değiştirin.

   > [!IMPORTANT]
   > Bu öğretici, C# [WCF istemcisini yapılandırma](#configure-the-wcf-client) bölümünde yapılandırma dosyasında kullanılan anlaşma tabanlı yönetilen türün ad alanı olan `Microsoft.ServiceBus.Samples` ad alanını kullanır. Bu örneği oluştururken istediğiniz ad alanını belirtebilirsiniz. Ancak, uygulama yapılandırma dosyasında sözleşmenin ve hizmetin ad alanlarını buna uygun olarak değiştirmediğiniz takdirde öğretici çalışmayacaktır. *App. config* dosyasında belirtilen ad alanı C# dosyalarınızda belirtilen ad alanıyla aynı olmalıdır.
   >

1. `Microsoft.ServiceBus.Samples` ad alanı bildiriminden hemen sonra, ancak ad alanı içinde, `IEchoContract` adlı yeni bir arabirim tanımlayın ve `https://samples.microsoft.com/ServiceModel/Relay/`ad alanı değeri olan arabirime `ServiceContractAttribute` özniteliğini uygulayın. Ad alanı bildiriminden sonra aşağıdaki kodu yapıştırın:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Kodunuzun kapsamında kullandığınız ad alanı ile ad alanı değeri farklılık gösterir. Ad alanı değeri bu sözleşme için benzersiz bir tanımlayıcı olarak kullanılır. Ad alanını açıkça belirlemek, varsayılan ad alanı değerinin sözleşme adına eklenmesini engeller.

   > [!NOTE]
   > Genellikle, hizmet sözleşmesi ad alanı sürüm bilgilerini barındıran bir adlandırma şeması içerir. Sürüm bilgilerini hizmet sözleşmesi ad alanına dahil etmek, hizmetlerin yeni bir ad alanı içeren yeni bir hizmet sözleşmesi tanımlayarak ve bu sözleşmeyi yeni bir uç noktada kullanıma sunarak büyük değişiklikleri yalıtmalarına olanak sağlar. Bu şekilde istemciler, eski hizmet sözleşmesini, güncelleştirilmeleri gerekmeden kullanmaya devam edebilir. Sürüm bilgileri, bir tarihten veya bir derleme numarasından oluşabilir. Daha fazla bilgi için bkz. [Hizmet Sürümü Oluşturma](/dotnet/framework/wcf/service-versioning). Bu öğretici için, hizmet sözleşmesi ad alanının adlandırma şeması sürüm bilgisi içermez.
   >

1. `IEchoContract` arabirimi içinde, `IEchoContract` sözleşmesinin arabirimde sunduğu tek bir işlem için bir yöntem bildirin ve `OperationContractAttribute` özniteliğini ortak WCF Geçişi sözleşmesinin bir parçası olarak göstermek istediğiniz yönteme aşağıdaki gibi uygulayın :

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. `IEchoContract` arabirimi tanımından hemen sonra, aşağıda belirtildiği şekilde `IEchoContract` ve `IClientChannel` arabirimlerinden devralma işlemini gerçekleştiren bir kanal bildirin:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Kanal, ana bilgisayar ve istemcinin bilgileri birbirlerine göndermek için kullandıkları WCF nesnesidir. Daha sonra, iki uygulama arasındaki bilgileri yankılanması için kanala karşı kod yazacaksınız.

1. İş için **derleme** > **derleme çözümünü** seçin ya da CTRL + SHIFT + B ' yi seçerek çalışmanızın doğruluğunu onaylayın.

### <a name="example-of-a-wcf-contract"></a>WCF sözleşmesinin örneği

Aşağıdaki kod, WCF Geçişi bir sözleşmeyi tanımlayan temel bir arabirimi gösterir.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Oluşturulması tamamlandığına göre arabirimi uygulayabilirsiniz.

## <a name="implement-the-wcf-contract"></a>WCF sözleşmesini uygulama

Azure geçişi oluşturmak için öncelikle bir arabirim kullanarak sözleşmeyi oluşturmanız gerekir. Arabirimi oluşturma hakkında daha fazla bilgi için önceki bölüme bakın. Sonraki yordam, arabirimini uygular. Bu görev, Kullanıcı tanımlı `IEchoContract` arabirimini uygulayan `EchoService` adlı bir sınıf oluşturmayı içerir. Arabirimi uyguladıktan sonra, bir *app. config* yapılandırma dosyası kullanarak arabirimi yapılandırırsınız. Yapılandırma dosyası, uygulama için gereken bilgileri içerir. Bu bilgilere hizmetin adı, sözleşmenin adı ve geçiş hizmeti ile iletişim kurmak için kullanılan protokol türü dahildir. Bu görevler için kullanılan kod, yordamı izleyen örnekte verilmiştir. Hizmet sözleşmesinin nasıl uygulanacağı hakkında daha genel bir tartışma için bkz. [hizmet sözleşmelerini uygulama](/dotnet/framework/wcf/implementing-service-contracts).

1. `EchoService` arabiriminin tanımından hemen sonra `IEchoContract` adlı yeni bir sınıf oluşturun. `EchoService` sınıfı, `IEchoContract` arabirimini uygular.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Diğer arabirim uygulamalarına benzer şekilde, tanımı farklı bir dosyada uygulayabilirsiniz. Ancak bu öğreticide uygulama, arabirim tanımı ve `Main()` yöntemiyle aynı dosyadadır.

1. [ arabirimine ](/dotnet/api/system.servicemodel.servicebehaviorattribute)ServiceBehaviorAttribute`IEchoContract` özniteliğini uygulayın. Öznitelik, hizmet adını ve ad alanını belirtir. Bunu yaptıktan sonra `EchoService` sınıfı şu şekilde görünür:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. `Echo` sınıfındaki `IEchoContract` arabiriminde tanımlanan `EchoService` yöntemini uygulayın.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. **Yapı** > **derleme çözümünü** seçin ya da CTRL + SHIFT + B ' yi seçin.

### <a name="define-the-configuration-for-the-service-host"></a>Hizmet ana bilgisayarı için yapılandırmayı tanımlayın

Yapılandırma dosyası bir WCF yapılandırma dosyasına benzer. Hizmet adını, uç noktasını ve bağlamayı içerir. Uç nokta Azure Relay, istemcilerin ve ana bilgisayarların birbirleriyle iletişim kurması için kullanıma sunduğu konumdur. Bağlama, iletişim kurmak için kullanılan protokolün türüdür. Temel fark, bu yapılandırılmış hizmet uç noktasının, .NET Framework parçası olmayan bir [Nettcprelaybinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) bağlamasına başvurduğu bir noktadır. [Nettcprelaybinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) , hizmet tarafından tanımlanan bağlamalardan biridir.

1. **Çözüm Gezgini**' de, **app. config** dosyasına çift tıklayarak dosyayı Visual Studio düzenleyicisinde açın.
1. `<appSettings>` öğesinde, yer tutucuları hizmet ad alanınızdaki adla ve önceki adımların birinde kopyaladığınız SAS anahtarı ile değiştirin.
1. `<system.serviceModel>` etiketleri içinde, bir `<services>` öğesi ekleyin. Tek bir yapılandırma dosyasında birden çok geçiş uygulaması tanımlayabilirsiniz. Ancak bu öğreticide yalnızca bir adet tanımlanır.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. `<services>` öğesi içinde, hizmetin adını tanımlamak için `<service>` öğesi ekleyin.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. `<service>` öğesi içinde, uç nokta sözleşmesinin konumunu ve uç noktaya yönelik bağlama türünü tanımlayın.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Uç nokta, istemcinin ana bilgisayar uygulamasını nerede arayacağını tanımlar. Bu öğretici daha sonra, Azure Relay aracılığıyla konağı tam olarak sunan bir URI oluşturmak için bu adımı kullanır. Bağlama, geçiş hizmeti ile iletişim kurmak için protokol olarak TCP kullandığımızda bildirir.

1. İş için **derleme** > **derleme çözümünü** seçin ya da CTRL + SHIFT + B ' yi seçerek çalışmanızın doğruluğunu onaylayın.

### <a name="example-of-implementation-of-a-service-contract"></a>Hizmet sözleşmesinin uygulanmasına örnek

Aşağıdaki kod, hizmet sözleşmesinin uygulamasını gösterir.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Aşağıdaki kod, hizmet ana bilgisayarıyla ilişkili *app. config* dosyasının temel biçimini gösterir.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Geçiş hizmetine kaydolmak için WCF hizmetini barındırma ve çalıştırma

Bu adım Azure Relay bir hizmetin nasıl çalıştırılacağını açıklar.

### <a name="create-the-relay-credentials"></a>Geçiş kimlik bilgilerini oluşturma

1. `Main()` içinde, konsol penceresinden okunan ad alanını ve SAS anahtarını depolayabileceğiniz iki değişken oluşturun.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Daha sonra projenize erişmek için SAS anahtarı kullanılacaktır. Ad alanı, Hizmet URI'si oluşturmak için `CreateServiceUri` öğesine bir parametre olarak geçirilir.

1. Bir [Transportclientendpointbehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) nesnesi kullanarak, kimlik bilgisi türü olarak bir SAS anahtarı kullandığınızı bildirin. Aşağıdaki kodu son adımda eklenen koddan hemen sonra ekleyin.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Hizmet için bir temel adres oluşturun

Önceki bölümde eklediğiniz koddan sonra, hizmetin temel adresi için bir `Uri` örneği oluşturun. Bu URI; Service Bus şemasını, ad alanını ve hizmet arabiriminin yolunu belirtir.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"SB" değeri Service Bus şeması için bir kısaltmadır. Protokol olarak TCP kullandığınızı gösterir. Bu düzen Ayrıca yapılandırma dosyasında, bağlama olarak [Nettcprelaybinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) belirtildiğinde daha önce belirtilmiştir.

Bu öğretici için URI şudur: `sb://putServiceNamespaceHere.windows.net/EchoService`

### <a name="create-and-configure-the-service-host"></a>Hizmet konağını oluşturma ve yapılandırma

1. Hala `Main()`çalışıyor, bağlantı modunu `AutoDetect`olarak ayarlayın.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Bağlantı modu, hizmetin geçiş hizmeti ile iletişim kurmak için kullandığı protokolü açıklar; HTTP veya TCP. `AutoDetect`varsayılan ayarı kullanarak, hizmet varsa TCP üzerinden Azure Relay bağlanmaya çalışır ve TCP kullanılabilir değilse HTTP. Bu sonuç, hizmetin istemci iletişimi için belirttiği protokolden farklıdır. Bu protokol, kullanılan bağlamaya göre belirlenir. Örneğin, bir hizmet, uç noktasının HTTP üzerinden istemcilerle iletişim kuracağını belirten [Basichttprelaybinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) bağlamasını kullanabilir. Aynı hizmet, hizmetin TCP üzerinden Azure Relay iletişim kurduğu şekilde `ConnectivityMode.AutoDetect` belirtebilir.

1. Bu bölümün önceki kısımlarında oluşturduğunuz URI'yi kullanarak hizmet ana bilgisayarını oluşturun.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Hizmet ana bilgisayarı, hizmetin örneğini oluşturan WCF nesnesidir. Burada, oluşturmak istediğiniz hizmet türü, `EchoService` türü ve ayrıca hizmeti kullanıma sunmak istediğiniz adrese geçitirsiniz.

1. *Program.cs* dosyasının en üstünde, [System. ServiceModel. Description](/dotnet/api/system.servicemodel.description) ve [Microsoft. ServiceBus. Description](/dotnet/api/microsoft.servicebus.description)'a başvurular ekleyin.

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. `Main()` öğesine geri dönüp, genel erişimi etkinleştirmek için uç noktayı yapılandırın.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Bu adım geçiş hizmetine, projenizin atom akışını inceleyerek uygulamanızın herkese açık bir şekilde bulunamadığını bildirir. `DiscoveryType` `private`olarak ayarlarsanız, istemci hizmete erişmeye devam edebilir. Ancak, hizmet `Relay` ad alanını ararken görünmez. Bunun yerine, istemcinin uç nokta yolunu önceden bilmesi gerekir.

1. Service kimlik bilgilerini *app. config* dosyasında tanımlanan hizmet uç noktalarına uygulayın:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Daha önce belirtildiği gibi, yapılandırma dosyasında birden çok hizmet ve uç nokta bildirdiniz. Birden çok hizmet ve uç nokta bildirirseniz bu kod yapılandırma dosyasına çapraz geçiş yapar ve kimlik bilgilerinin uygulanacağı tüm uç noktalara yönelik arama yapar. Bu öğretici için yapılandırma dosyasında yalnızca bir uç nokta bulunur.

### <a name="open-the-service-host"></a>Hizmet konağını açın

1. Hala `Main()`, hizmeti açmak için aşağıdaki satırı ekleyin.

    ```csharp
    host.Open();
    ```

1. Kullanıcıyı hizmetin çalıştığı konusunda bilgilendirin ve hizmetin nasıl kapatılacağını açıklayın.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Bu işlemi bitirdiğinizde, hizmet ana bilgisayarını kapatın.

    ```csharp
    host.Close();
    ```

1. Projeyi derlemek için CTRL + SHIFT + B ' yi seçin.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Konsol uygulamasında bir hizmeti barındıran örnek

Tamamlanmış hizmet kodunuz aşağıdaki gibi görünmelidir. Kod, öğreticideki önceki adımlardan hizmet sözleşmesini ve uygulamayı içerir ve hizmeti bir konsol uygulamasında barındırır.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Hizmet sözleşmesi için bir WCF istemcisi oluşturma

Sonraki görev, bir istemci uygulaması oluşturmak ve daha sonra uygulamanız gereken hizmet sözleşmesini tanımlamaktır. Bu adımlar, bir hizmet oluşturmak için kullanılan adımlara benzer: bir sözleşme tanımlama, bir *app. config* dosyasını düzenleme, geçiş hizmetine bağlanmak için kimlik bilgilerini kullanma ve bu şekilde devam eder. Bu görevler için kullanılan kod, aşağıdaki yordamın altındaki örnekte sağlanır.

1. İstemci için geçerli Visual Studio çözümünde yeni bir proje oluşturun:

   1. **Çözüm Gezgini**, geçerli çözüme (proje değil) sağ tıklayın ve > **Yeni proje** **Ekle** ' yi seçin.
   1. **Yeni Proje Ekle**' de, Için C#konsol uygulaması (.NET Framework) öğesini seçin ve **İleri**' yi seçin.
   1. Projeyi *yankı istemcisini* adlandırın ve **Oluştur**' u seçin.

1. **Çözüm Gezgini**, **yankı istemci** projesinde, zaten açık değilse dosyayı düzenleyicide açmak için **program.cs** öğesine çift tıklayın.
1. `EchoClient` olan varsayılan ad alanı adını `Microsoft.ServiceBus.Samples` olarak değiştirin.
1. [Service Bus NuGet paketini](https://www.nuget.org/packages/WindowsAzure.ServiceBus)yükler:

   1. **Çözüm Gezgini**, **yankı istemcisi** ' ne sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.
   1. **Araştır**' ı seçin, ardından **windowsazure. ServiceBus**öğesini arayıp seçin. **Yükler**' i seçin ve kullanım koşullarını kabul edin.

      ![Service Bus paketini yükler][4]

1. *Program.cs* dosyasına [System. ServiceModel](/dotnet/api/system.servicemodel) ad alanı için `using` bir ifade ekleyin.

    ```csharp
    using System.ServiceModel;
    ```

1. Hizmet sözleşmesi tanımını ad alanına aşağıdaki örnekte gösterilen şekilde ekleyin. Bu tanım, **hizmet** projesinde kullanılan tanımla aynıdır. Bu kodu `Microsoft.ServiceBus.Samples` ad alanının üst kısmına ekleyin.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. İstemcisini derlemek için CTRL + SHIFT + B ' yi seçin.

### <a name="example-of-the-echoclient-project"></a>Yankı Istemci projesi örneği

Aşağıdaki kod, **yankı istemci** projesindeki *program.cs* dosyasının geçerli durumunu gösterir.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>WCF istemcisini yapılandırma

Bu adımda, daha önce Bu öğreticide oluşturulan Hizmete erişen temel bir istemci uygulaması için bir *app. config* dosyası oluşturacaksınız. Bu *app. config* dosyası, bitiş noktasının sözleşmesini, bağlamasını ve adını tanımlar. Bu görevler için kullanılan kod, aşağıdaki yordamın altındaki örnekte sağlanır.

1. **Çözüm Gezgini**, **yankı istemci** projesinde **app. config** dosyasına çift tıklayarak dosyayı Visual Studio düzenleyicisinde açın.
1. `<appSettings>` öğesinde, yer tutucuları hizmet ad alanınızdaki adla ve önceki adımların birinde kopyaladığınız SAS anahtarı ile değiştirin.
1. `system.serviceModel` öğesi içinde, bir `<client>` öğesi ekleyin.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Bu kod, bir WCF stili istemci uygulaması tanımlamanız gerektiğini bildirir.

1. `client` öğesi içinde adı, sözleşmeyi ve uç noktaya yönelik bağlama türünü tanımlayın.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Bu kod, uç noktanın adını tanımlar. Ayrıca, hizmette tanımlanan sözleşmeyi ve istemci uygulamanın Azure Relay ile iletişim kurmak için TCP kullanmasını da tanımlar. Uç nokta adı, bir sonraki adımda bu uç nokta yapılandırmasını hizmet URI'si ile bağlamak için kullanılır.

1. **Tümünü kaydet** > **Dosya** ' yı seçin.

### <a name="example-of-the-appconfig-file"></a>App. config dosyası örneği

Aşağıdaki kod, Echo istemcisi için *app. config* dosyasını gösterir.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>WCF istemcisini uygulama

Bu bölümde, daha önce Bu öğreticide oluşturduğunuz Hizmete erişen temel bir istemci uygulaması uygulayacağınızı unutmayın. Hizmetle benzer şekilde, istemci, Azure Relay erişmek için aynı işlemlerin çoğunu da yapar:

* Bağlantı modunu ayarlar.
* Ana bilgisayar hizmetinin yer aldığı URI'yi oluşturur.
* Güvenlik kimlik bilgilerini tanımlar.
* Bağlantıya kimlik bilgilerini uygular.
* Bağlantıyı açar.
* Uygulamaya özgü görevleri gerçekleştirir.
* Bağlantıyı kapatır.

Ancak, temel farklardan biri, istemci uygulamanın geçiş hizmetine bağlanmak için bir kanal kullanmalarından biridir. Hizmet bir **ServiceHost**çağrısı kullanır. Bu görevler için kullanılan kod, aşağıdaki yordamın altındaki örnekte sağlanır.

### <a name="implement-a-client-application"></a>Bir istemci uygulaması uygulama

1. Bağlantı modunu `AutoDetect` olarak ayarlayın. Aşağıdaki kodu `Main()`EchoClient**uygulamasının** yöntemine ekleyin.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Konsoldan okunan hizmet ad alanı ve SAS anahtarına yönelik değerleri tutması için değişkenleri tanımlayın.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Geçiş projenizde konağın konumunu tanımlayan URI 'yi oluşturun.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Hizmet ad alanı uç noktanız için kimlik bilgileri nesnesini oluşturun.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. *App. config* dosyasında açıklanan yapılandırmayı yükleyen kanal fabrikasını oluşturun.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Kanal fabrikası, hizmet ve istemcinin iletişim kurmak için kullandığı bir kanal oluşturan WCF nesnesidir.

1. Kimlik bilgilerini uygulayın.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Kanalı oluşturup hizmet tarafından kullanılması için açın.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Yankı için işlevselliği ve temel kullanıcı arabirimini yazın.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Kod, kanal nesnesinin örneğini hizmet için bir proxy olarak kullanır.

1. Kanalı ve fabrikayı kapatın.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Bu öğretici için örnek kod

Tamamlanan kodunuz aşağıdaki gibi görünmelidir. Bu kod, bir istemci uygulamasının nasıl oluşturulduğunu, hizmet işlemlerinin nasıl çağrılacağını ve işlem çağrısının tamamlanmasının ardından istemcinin nasıl kapatılmasını gösterir.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

1. Çözümü derlemek için CTRL + SHIFT + B ' yi seçin. Bu eylem, önceki adımlarda oluşturduğunuz istemci projesini ve hizmet projesini oluşturur.
1. İstemci uygulamasını çalıştırmadan önce hizmet uygulamasının çalıştığından emin olmanız gerekir. **Çözüm Gezgini**, **yankı hizmeti** çözümüne sağ tıklayın ve ardından **Özellikler**' i seçin.
1. **Özellik sayfaları**' nda **, ortak özellikler** > **Başlangıç projesi**' nde, sonra **birden çok başlangıç**projesi seçin. **EchoService** çözümünün liste başında olduğundan emin olun.
1. **EchoService** ve **EchoClient** projeleri için **Eylem** kutusunu **Başlat** olarak ayarlayın.

    ![Proje özellik sayfaları][5]

1. **Proje bağımlılıklarını**seçin. **Projeler**' de, **yankı istemcisi**' ni seçin. **Öğesine bağlı**olarak, **yankı hizmeti** 'nin seçili olduğundan emin olun.

    ![Proje bağımlılıkları][6]

1. **Özellik sayfalarını**kapatmak için **Tamam ' ı** seçin.
1. Her iki projeyi de çalıştırmak için F5 ' i seçin.
1. Her iki konsol penceresi de açılır ve sizden ad alanı adı ister. Önce hizmetin çalışması gerekir, bu nedenle **yankı hizmeti** konsol penceresinde ad alanını girin ve ardından ENTER ' u seçin.
1. Ardından, konsolu SAS anahtarınızı ister. SAS anahtarını girin ve ENTER ' u seçin.

    Konsol penceresinden örnek çıktı sunulur. Buradaki değerler yalnızca örnektir.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Hizmet uygulaması, aşağıdaki örnekte görüldüğü şekilde konsol penceresini dinlediği adrese yazdırır.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. **EchoClient** konsol penceresinde, az önce hizmet uygulaması için girdiğiniz bilgileri girin. İstemci uygulaması için aynı hizmet ad alanını ve SAS anahtarı değerlerini girin.
1. Bu değerleri girdikten sonra istemci hizmete yönelik bir kanal açar ve aşağıdaki konsol çıktısı örneğinde görüldüğü şekilde bazı metinler girmenizi ister.

    `Enter text to echo (or [Enter] to exit):`

    Hizmet uygulamasına göndermek için bir metin girin ve ENTER ' u seçin. Bu metin, Echo hizmet işlemi aracılığıyla hizmete gönderilir ve aşağıdaki örnek çıktıda görüldüğü şekilde hizmet konsol penceresinde görünür.

    `Echoing: My sample text`

    İstemci uygulaması, `Echo` işleminin dönüş değerini, diğer bir deyişle orijinal metni alır ve konsol penceresine yazdırır. Aşağıdaki metin, istemci konsolu penceresinden alınan örnek çıktıdır.

    `Server echoed: My sample text`

1. Bu şekilde istemciden hizmete metin iletileri göndermeye devam edebilirsiniz. İşiniz bittiğinde, her iki uygulamayı da sonlandırmak için istemci ve hizmet konsolu penceresinde ENTER ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

Şu öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Şirket içi WCF REST hizmetini ağınızın dışındaki bir istemciye kullanıma sunma](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
