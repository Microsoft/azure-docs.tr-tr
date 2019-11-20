---
title: 'Öğretici: Azure Relay kullanarak REST öğreticisi'
description: 'Öğretici: REST tabanlı arabirim sunan bir Azure Service Bus geçişi ana bilgisayar uygulaması oluşturun.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718839"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Öğretici: Azure WCF Geçişi REST öğreticisi

Bu öğreticide, REST tabanlı arabirim sunan bir Azure Relay ana bilgisayar uygulamasının nasıl oluşturulacağı açıklanmaktadır. REST, HTTP istekleri üzerinden Service Bus API'lerine erişmek için web tarayıcısı gibi bir web istemcisi sunar.

Öğretici, Azure Relay bir REST hizmeti oluşturmak için Windows Communication Foundation (WCF) REST programlama modelini kullanır. Daha fazla bilgi için bkz. [WCF REST programlama modeli](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) ve [Hizmetleri tasarlama ve uygulama](/dotnet/framework/wcf/designing-and-implementing-services).

Bu öğreticide aşağıdaki görevleri gerçekleştirebilirsiniz:

> [!div class="checklist"]
>
> * Bu öğretici için önkoşulları yükler.
> * Geçiş ad alanı oluşturun.
> * REST tabanlı bir WCF hizmeti sözleşmesi tanımlayın.
> * REST tabanlı WCF sözleşmesini uygulayın.
> * REST tabanlı WCF hizmetini barındırın ve çalıştırın.
> * Hizmeti çalıştırın ve test edin.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* [Visual Studio 2015 veya üzeri](https://www.visualstudio.com). Bu öğreticideki örneklerde Visual Studio 2019 kullanılır.
* .NET için Azure SDK. [SDK İndirmeleri sayfasından](https://azure.microsoft.com/downloads/)bu uygulamayı yükler.

## <a name="create-a-relay-namespace"></a>Geçiş ad alanı oluşturma

Azure'da geçiş özelliklerini kullanmaya başlamak için öncelikle bir hizmet ad alanı oluşturmanız gerekir. Ad alanı, uygulamanızda bulunan Azure kaynaklarını adreslemek için içeriğin kapsamını belirleyen bir kapsayıcı sunar. [Buradaki yönergeleri](relay-create-namespace-portal.md) izleyerek bir Geçiş ad alanı oluşturun.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Azure Relay ile kullanılacak REST tabanlı bir WCF hizmet sözleşmesi tanımlayın

Bir WCF REST stili hizmeti oluşturduğunuzda, sözleşmeyi tanımlamanız gerekir. Sözleşmede ana bilgisayarın hangi işlemleri desteklediği belirtilir. Bir hizmet işlemi, bir Web hizmeti yöntemine benzer. C++, C#Veya Visual Basic arabirimiyle bir sözleşme tanımlayın. Arabirimdeki her yöntem belirli bir hizmet işlemine karşılık gelir. Her arabirime [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) özniteliğini uygulayın ve her bir işleme [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) özniteliğini uygulayın. 

> [!TIP]
> [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) öğesine sahip bir arabirimdeki bir yöntem [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute)içermiyorsa, bu yöntem gösterilmez. Bu görevler için kullanılan kod, yordamı izleyen örnekte görüntülenir.

Bir WCF sözleşmesi ile REST stili sözleşme arasındaki birincil fark, [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute)öğesine bir özelliğin eklenmesinin bir özelliğidir. Bu özellik sayesinde arabiriminizdeki bir yöntem ile arabirimin diğer tarafındaki bir yöntemi eşleyebilirsiniz. Bu örnek, bir yöntemi `HTTP GET`bağlamak için [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) özniteliğini kullanır. Bu yaklaşım, Service Bus arabirime gönderilen komutları doğru bir şekilde almasını ve yorumlamasını sağlar.

### <a name="to-create-a-contract-with-an-interface"></a>Arabirim içeren bir sözleşme oluşturmak için

1. Microsoft Visual Studio yönetici olarak başlatın. Bunu yapmak için, Visual Studio program simgesine sağ tıklayın ve **yönetici olarak çalıştır**' ı seçin.
1. Visual Studio 'da **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, Için C# konsol uygulaması (.NET Framework) öğesini seçin ve **İleri**' yi seçin.
1. Projeyi *ımagelistener*olarak adlandırın. Varsayılan **konumu**kullanın ve ardından **Oluştur**' u seçin.

   Bir C# proje Için, Visual Studio bir *program.cs* dosyası oluşturur. Bu sınıf, bir konsol uygulaması projesinin doğru şekilde derlenmesi için gerekli olan boş `Main()` yöntemi içerir.

1. **Çözüm Gezgini**, **ımagelistener** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.
1. **Araştır**' ı seçin, sonra **windowsazure. ServiceBus**öğesini arayıp seçin. **Yükler**' i seçin ve kullanım koşullarını kabul edin.

    Bu adım Service Bus ve *System. ServiceModel. dll*' ye başvurular ekler. Bu paket, Service Bus kitaplıklarına ve WCF `System.ServiceModel`başvuruları otomatik olarak ekler.

1. Projeye `System.ServiceModel.Web.dll` için açıkça bir başvuru ekleyin. **Çözüm Gezgini**, proje klasörü altındaki **Başvurular** ' a sağ tıklayın ve **Başvuru Ekle**' yi seçin.
1. **Başvuru Ekle**' de **çerçeve** ' yi seçin ve **Search**'te *System. ServiceModel. Web* girin. **System.ServiceModel.Web** öğesinin onay kutusunu işaretleyin ve **Tamam**'a tıklayın.

Ardından, projede aşağıdaki kod değişikliklerini yapın:

1. *Program.cs* dosyasının en üstüne aşağıdaki `using` deyimlerini ekleyin.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel), WCF'nin temel özelliklerine programlama erişimi sağlayan ad alanıdır. WCF Geçişi, hizmet sözleşmelerini tanımlamak için WCF 'nin birçok nesnesini ve özniteliğini kullanır. Bu ad alanını geçiş uygulamalarınızın çoğunda kullanırsınız.
    * [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) , Azure Relay ve istemci Web tarayıcısı ile iletişim kurduğunuz nesne olan kanalı tanımlamaya yardımcı olur.
    * [System. ServiceModel. Web](/dotnet/api/system.servicemodel.web) , Web tabanlı uygulamalar oluşturmanıza olanak sağlayan türleri içerir.

1. `ImageListener` ad alanını `Microsoft.ServiceBus.Samples`olarak yeniden adlandırın.

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Ad alanı bildiriminin açma küme ayracından hemen sonra, `IImageContract` adlı yeni bir arabirim tanımlayın ve `ServiceContractAttribute` özniteliğini arabirime `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`değeri ile uygulayın. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Kodunuzun kapsamında kullandığınız ad alanı ile ad alanı değeri farklılık gösterir. Ad alanı değeri bu sözleşme için benzersiz bir tanımlayıcıdır ve sürüm bilgilerine sahip olmalıdır. Daha fazla bilgi için bkz. [Hizmet Sürümü Oluşturma](/dotnet/framework/wcf/service-versioning). Ad alanını açıkça belirlemek, varsayılan ad alanı değerinin sözleşme adına eklenmesini engeller.

1. `IImageContract` arabirimi içinde, `IImageContract` sözleşmesinin arabirimde sunduğu tek bir işlem için bir yöntem bildirin ve `OperationContract` özniteliğini ortak Service Bus sözleşmesinin bir parçası olarak sunmak istediğiniz yönteme uygulayın.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. `OperationContract` özniteliğinde, `WebGet` değerini ekleyin.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   `WebGet` değerini eklemek, geçiş hizmetinin HTTP GET isteklerini `GetImage`yönlendirmesine ve `GetImage` dönüş değerlerini `HTTP GETRESPONSE` yanıtına çevirebilmesine olanak sağlar. Öğreticide daha sonra bu yönteme erişmek ve görüntüyü tarayıcıda göstermek için bir Web tarayıcısı kullanacaksınız.

1. `IImageContract` tanımından hemen sonra, `IImageContract` ve `IClientChannel` arabirimlerinden devralma işlemini gerçekleştiren bir kanal bildirin.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Kanal, hizmet ve istemcilerin bilgileri birbirlerine göndermek için kullandıkları WCF nesnesidir. Daha sonra, kanalı ana uygulamanızda oluşturursunuz. Azure Relay daha sonra bu kanalı kullanarak HTTP GET isteklerini tarayıcıdan `GetImage` uygulamanıza geçirin. Geçiş Ayrıca, `GetImage` dönüş değerini alıp istemci tarayıcısı için bir `HTTP GETRESPONSE` dönüştürmek üzere kanalı kullanır.

1. İşin şu ana kadarki doğruluğunu onaylamak için **build** > **Build Solution** öğesini seçin.

### <a name="example-that-defines-a-wcf-relay-contract"></a>WCF Geçişi sözleşmesini tanımlayan örnek

Aşağıdaki kod, WCF Geçişi bir sözleşmeyi tanımlayan temel bir arabirimi gösterir.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>REST tabanlı WCF hizmet sözleşmesini uygulama

REST stili WCF Geçişi bir hizmet oluşturmak için önce bir arabirim kullanarak sözleşmeyi oluşturun. Bir sonraki adım ise bu arabirimi uygulamaktır. Bu yordam, Kullanıcı tanımlı `IImageContract` arabirimini uygulayan `ImageService` adlı bir sınıf oluşturmayı içerir. Sözleşmeyi uyguladıktan sonra, bir *app. config* dosyası kullanarak arabirimi yapılandırırsınız. Yapılandırma dosyası, uygulama için gereken bilgileri içerir. Bu bilgilere hizmetin adı, sözleşmenin adı ve geçiş hizmeti ile iletişim kurmak için kullanılan protokol türü dahildir. Bu görevler için kullanılan kod, yordamı izleyen örnekte görüntülenir.

Önceki adımlarda olduğu gibi, REST stili bir sözleşme ve WCF Geçişi sözleşmesi uygulama arasında çok az fark vardır.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>REST stilinde Service Bus sözleşmesini uygulama

1. `ImageService` arabiriminin tanımından hemen sonra `IImageContract` adlı yeni bir sınıf oluşturun. `ImageService` sınıfı, `IImageContract` arabirimini uygular.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Diğer arabirim uygulamalarına benzer şekilde, tanımı farklı bir dosyada uygulayabilirsiniz. Ancak bu öğreticide uygulama, arabirim tanımı ve `Main()` yöntemiyle aynı dosyada görünmelidir.

1. Sınıfın bir WCF sözleşmesinin uygulaması olduğunu göstermek için `IImageService` sınıfına [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) özniteliğini uygulayın.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Daha önce belirtildiği gibi, bu ad alanı geleneksel bir ad alanı değildir. Sözleşmeyi tanımlayan WCF mimarisinin bir parçasıdır. Daha fazla bilgi için bkz. [veri sözleşmesi adları](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Projenize bir *. jpg* görüntüsü ekleyin. Bu dosya, hizmetin alıcı tarayıcıda görüntülediği bir resimdir.

   1. Projenize sağ tıklayın ve **Ekle**' yi seçin.
   1. Ardından **Varolan öğe**' yi seçin.
   1. Uygun bir. jpg öğesine gitmek için **Varolan öğe Ekle** ' yi kullanın ve ardından **Ekle**' yi seçin. Dosya eklenirken **dosya adı**' nın yanındaki aşağı açılan listeden **tüm dosyalar** ' ı seçin.

   Bu öğreticinin geri kalanı görüntünün adının *Image. jpg*olduğunu varsayar. Farklı bir dosyanız varsa, görüntüyü yeniden adlandırmanız veya kodunuzu telafi olarak değiştirmeniz gerekir.

1. Çalışan Hizmetin görüntü dosyasını **bulaÇözüm Gezgini** emin olmak için, ' de görüntü dosyasına sağ tıklayın ve ardından **Özellikler**' i seçin. **Özellikler**' de, **daha yeniyse kopyalamak**için **Çıkış Dizinine Kopyala** ' yı ayarlayın.

1. Projeye *System. Drawing. dll* derlemesine başvuru eklemek için [arabirimi ile bir anlaşma oluşturmak için](#to-create-a-contract-with-an-interface) içindeki yordamını kullanın.

1. Aşağıdaki ilişkili `using` deyimlerini ekleyin:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. `ImageService` sınıfında, bit eşlemi yükleyen ve istemci tarayıcısına göndermek için hazırlanırken aşağıdaki oluşturucuyu ekleyin:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Önceki koddan hemen sonra, görüntüyü içeren bir HTTP iletisi döndürmek için aşağıdaki `GetImage` yöntemini `ImageService` sınıfına ekleyin.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Bu uygulama, görüntüyü almak için `MemoryStream` kullanır ve tarayıcıya akışa hazırlar. Akış konumunu sıfır olarak başlatır, akış içeriğini bir *. jpg*olarak bildirir ve bilgileri akışlar.

1. **Build** > **Build Solution**öğesini seçin.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Service Bus üzerinde web hizmetini çalıştırmak için yapılandırma tanımlama

1. **Çözüm Gezgini**' de, **app. config** dosyasına çift tıklayarak dosyayı Visual Studio düzenleyicisinde açın.

    *App. config* dosyası hizmet adını, uç noktayı ve bağlamayı içerir. Uç nokta Azure Relay, istemcilerin ve ana bilgisayarların birbirleriyle iletişim kurması için kullanıma sunduğu konumdur. Bağlama, iletişim kurmak için kullanılan protokolün türüdür. Buradaki temel fark, yapılandırılmış hizmet uç noktasının bir [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) bağlamasını ifade ettiği bir noktadır.

1. `<system.serviceModel>` XML öğesi, bir veya birden çok hizmeti tanımlayan WCF öğesidir. Burada, hizmet adını ve uç noktayı tanımlamak için kullanılır. `<system.serviceModel>` öğesinin en altında, ancak hala `<system.serviceModel>`içinde, aşağıdaki içeriğe sahip bir `<bindings>` öğesi ekleyin:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Bu içerik, uygulamada kullanılan bağlamaları tanımlar. Birden çok bağlama tanımlayabilirsiniz, ancak bu öğretici için yalnızca bir tane tanımlamanız yeterlidir.

    Önceki kod, `relayClientAuthenticationType` `None`olarak ayarlanan bir [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) bağlamasını WCF geçişi tanımlar. Bu ayar, bu bağlamayı kullanan bir uç noktanın istemci kimlik bilgisi gerektirmediğini belirtir.

1. `<bindings>` öğesinden sonra `<services>` öğesini ekleyin. Bağlamalara benzer şekilde tek bir yapılandırma dosyasında birden çok hizmet tanımlayabilirsiniz. Ancak bu öğreticide yalnızca bir tane tanımlayacaksınız.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Bu içerik, önceden tanımlanmış varsayılan `webHttpRelayBinding`kullanan bir hizmeti yapılandırır. Ayrıca, bir sonraki adımda tanımlanan varsayılan `sbTokenProvider`de kullanır.

1. `<services>` öğeden sonra, `SAS_KEY` paylaşılan erişim Imzası (SAS) anahtarıyla değiştirerek aşağıdaki içeriğe sahip bir `<behaviors>` öğesi oluşturun. [Azure Portal][Azure portal]bir SAS anahtarı almak için bkz. [Yönetim kimlik bilgilerini alma](service-bus-relay-tutorial.md#get-management-credentials).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Hala *app. config*dosyasında, `<appSettings>` öğesinde, tüm bağlantı dizesi değerini portaldan daha önce edindiğiniz bağlantı dizesiyle değiştirin.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Tüm çözümü derlemek için **derleme** > **derleme çözümünü** seçin.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>REST tabanlı WCF hizmet sözleşmesini uygulayan örnek

Aşağıdaki kod, `WebHttpRelayBinding` bağlamayı kullanarak Service Bus çalıştıran REST tabanlı bir hizmetin sözleşme ve hizmet uygulamasını gösterir.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Aşağıdaki örnek, hizmetiyle ilişkili *app. config* dosyasını gösterir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Azure Relay kullanmak için REST tabanlı WCF hizmetini barındırın

Bu bölümde, WCF Geçişi ile bir konsol uygulaması kullanarak bir Web hizmetinin nasıl çalıştırılacağı açıklanmaktadır. Bu bölümde yazılan kodun tüm listesi, yordamı izleyen örnekte görüntülenir.

### <a name="to-create-a-base-address-for-the-service"></a>Hizmet için taban adresi oluşturma

1. `Main()` işlev bildiriminde, projenizin ad alanını depolamak için bir değişken oluşturun. `yourNamespace`, daha önce oluşturduğunuz geçiş ad alanının adıyla değiştirdiğinizden emin olun.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus, benzersiz bir URI oluşturmak için ad alanınızdaki adı kullanır.

1. Ad alanını temel alan hizmetin taban adresine yönelik `Uri` örneğini oluşturun.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Web hizmeti ana bilgisayarını oluşturma ve yapılandırma

Hala `Main()`, bu bölümde daha önce oluşturulan URI adresini kullanarak Web hizmeti ana bilgisayarını oluşturun.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Hizmet ana bilgisayarı, ana bilgisayar uygulamasının örneğini oluşturan WCF nesnesidir. Bu örnek, bu dosyayı oluşturmak istediğiniz konağın türü olan bir `ImageService`ve ayrıca konak uygulamasını göstermek istediğiniz adresi geçirir.

### <a name="to-run-the-web-service-host"></a>Web hizmeti ana bilgisayarını çalıştırma

1. Hala `Main()`, hizmeti açmak için aşağıdaki satırı ekleyin.

    ```csharp
    host.Open();
    ```

    Hizmet artık çalışır durumdadır.

1. Hizmetin çalıştığını ve nasıl durdurulacağını belirten bir ileti görüntüleyin.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Bu işlemi bitirdiğinizde, hizmet ana bilgisayarını kapatın.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Hizmet sözleşmesi ve uygulama örneği

Aşağıdaki örnek, hizmet sözleşmesini ve bu öğreticinin önceki kısımlarında yer alan uygulamayı içerir ve hizmeti bir konsol uygulamasında barındırır. Aşağıdaki kodu *ımagelistener. exe*adlı bir yürütülebilir dosyada derleyin.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Hizmeti çalıştırma ve test etme

Çözümü derledikten sonra uygulamayı çalıştırmak için şunları yapın:

1. Hizmeti çalıştırmak için F5 ' i seçin veya yürütülebilir dosya konumuna ( *ImageListener\bin\Debug\ImageListener.exe*) gidin. Bir sonraki adım için gerektiğinden uygulamayı çalışır durumda tutun.
1. Görüntüye bakmak için komut istemindeki adresi kopyalayıp bir tarayıcıya yapıştırın.
1. İşiniz bittiğinde, uygulamayı kapatmak için komut istemi penceresinde ENTER ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Relay hizmetini kullanan bir uygulama oluşturduğunuza göre, daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Geçiş nedir?](relay-what-is-it.md)
* [Azure WCF Geçişi kullanarak şirket içi WCF REST hizmetini dış istemciye kullanıma sunma](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
