---
title: Cloud Services roller için iletişim | Microsoft Docs
description: Cloud Services rol örneklerinde, diğer rol örnekleri arasında veya aralarında iletişim kuran bu noktalar için tanımlı uç noktalar (http, https, TCP, UDP) bulunabilir.
services: cloud-services
documentationcenter: ''
author: georgewallace
manager: carmonm
ms.service: cloud-services
ms.topic: article
ms.date: 12/14/2016
ms.author: gwallace
ms.openlocfilehash: 74ef5567becee27b4af837a6977119d7cf0f3e4b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359094"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Azure 'da rol örnekleri için iletişimi etkinleştirme
Bulut hizmeti rolleri, iç ve dış bağlantılarla iletişim kurar. İç bağlantılara **iç uç noktalar**çağrıldığında dış bağlantılara **giriş uç noktaları** denir. Bu konuda, uç noktalar oluşturmak için [hizmet tanımının](cloud-services-model-and-package.md#csdef) nasıl değiştirileceği açıklanmaktadır.

## <a name="input-endpoint"></a>Giriş uç noktası
Giriş uç noktası, dışarıdaki bir bağlantı noktasını kullanıma sunmak istediğinizde kullanılır. Uç nokta için hem dış hem de iç bağlantı noktası için geçerli olan protokol türünü ve uç noktası bağlantı noktasını belirtirsiniz. İsterseniz, uç nokta için [localport](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) özniteliğiyle farklı bir iç bağlantı noktası belirtebilirsiniz.

Giriş uç noktası şu protokolleri kullanabilir: **http, https, TCP, UDP**.

Bir giriş uç noktası oluşturmak için **ınputendpoint** alt öğesini bir Web veya çalışan rolünün **endpoints** öğesine ekleyin.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Örnek giriş uç noktası
Örnek giriş uç noktaları, giriş uç noktalarına benzerdir, ancak yük dengeleyicide bağlantı noktası iletmeyi kullanarak her bir rol örneği için genel kullanıma yönelik belirli bağlantı noktalarını eşlemenizi sağlar. Tek bir genel kullanıma yönelik bağlantı noktası veya bağlantı noktası aralığı belirtebilirsiniz.

Örnek giriş uç noktası yalnızca protokol olarak **TCP** veya **UDP** kullanabilir.

Örnek giriş uç noktası oluşturmak için, bir Web ya da çalışan rolünün **endpoints** öğesine **ınstanceınputendpoint** alt öğesini ekleyin.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>İç uç nokta
İç uç noktalar, örnek-örnek iletişimi için kullanılabilir. Bağlantı noktası isteğe bağlıdır ve atlanırsa, uç noktaya dinamik bir bağlantı noktası atanır. Bir bağlantı noktası aralığı kullanılabilir. Rol başına beş iç uç nokta sınırı vardır.

İç uç nokta şu protokolleri kullanabilir: **http, TCP, UDP, any**.

İç giriş uç noktası oluşturmak için, **InternalEndpoint** alt öğesini bir Web veya çalışan rolünün **endpoints** öğesine ekleyin.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Bir bağlantı noktası aralığı da kullanabilirsiniz.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Çalışan rolleri ile Web rolleri
Hem çalışan hem de Web rolleriyle çalışırken uç noktalarla bir küçük farklılık vardır. Web rolü, **http** protokolünü kullanan en az bir giriş uç noktası içermelidir.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>.NET SDK kullanarak bir uç noktaya erişme
Azure yönetilen Kitaplığı, çalışma zamanında iletişim kuracak rol örnekleri için yöntemler sağlar. Rol örneği içinde çalışan koddan, diğer rol örneklerinin ve bunların uç noktalarının varlığı ve geçerli rol örneğiyle ilgili bilgiler hakkında bilgi alabilirsiniz.

> [!NOTE]
> Yalnızca bulut hizmetinizde çalışan ve en az bir iç uç nokta tanımlayan rol örnekleri hakkında bilgi alabilirsiniz. Farklı bir hizmette çalışan rol örnekleri hakkında veri edinemezsiniz.
> 
> 

Bir rolün örneklerini almak için [örnekler](/previous-versions/azure/reference/ee741904(v=azure.100)) özelliğini kullanabilirsiniz. Önce geçerli rol örneğine bir başvuru döndürmek için [Currentropaınstance](/previous-versions/azure/reference/ee741907(v=azure.100)) öğesini kullanın ve ardından role bir başvuru döndürmek için [rol](/previous-versions/azure/reference/ee741918(v=azure.100)) özelliğini kullanın.

.NET SDK aracılığıyla programlı bir rol örneğine bağlandığınızda, uç nokta bilgilerine erişmek oldukça kolaydır. Örneğin, belirli bir rol ortamına zaten bağlandıktan sonra, belirli bir uç noktanın bağlantı noktasını şu kodla alabilirsiniz:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

**Örnekler** özelliği, **roleınstance** nesnelerinin bir koleksiyonunu döndürür. Bu koleksiyon her zaman geçerli örneği içerir. Rol bir iç uç nokta tanımlamıyorsa, koleksiyon geçerli örneği içerir ancak diğer örnekleri içermez. Rol için hiçbir iç uç noktanın tanımlanmadığı durumda, koleksiyondaki rol örneklerinin sayısı her zaman 1 olur. Rol bir iç uç nokta tanımlıyorsa, örnekleri çalışma zamanında keşfedilir ve koleksiyondaki örneklerin sayısı, hizmet yapılandırma dosyasındaki rol için belirtilen örnek sayısına karşılık gelir.

> [!NOTE]
> Azure yönetilen Kitaplığı, diğer rol örneklerinin sistem durumunu belirlemek için bir yol sunmaz, ancak hizmetiniz bu işlevselliğe ihtiyaç duyuyorsa bu durum değerlendirmelerini kendiniz de uygulayabilirsiniz. Rol örnekleri çalıştırma hakkında bilgi edinmek için [Azure tanılama](cloud-services-dotnet-diagnostics.md) kullanabilirsiniz.
> 
> 

Bir rol örneğindeki iç uç noktanın bağlantı noktası numarasını öğrenmek için, [ınstanceendpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) özelliğini kullanarak uç nokta adlarını ve bunlara KARŞıLıK gelen IP adreslerini ve bağlantı noktalarını Içeren bir sözlük nesnesi döndürebilirsiniz. [IPEndPoint](/previous-versions/azure/reference/ee741919(v=azure.100)) özelliği, belirtilen uç nokta için IP adresini ve bağlantı noktasını döndürür. **Publicıpendpoint** özelliği, yük dengeli bir uç nokta için bağlantı noktasını döndürür. **Publicıpendpoint** özelliğinin IP adresi bölümü kullanılmıyor.

Rol örneklerini tekrardan yineleyen bir örnek aşağıda verilmiştir.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Hizmet tanımı aracılığıyla kullanıma sunulan uç noktayı alan ve bağlantıları dinlemeye başlayan bir çalışan rolü örneği aşağıda verilmiştir.

> [!WARNING]
> Bu kod, yalnızca dağıtılan bir hizmet için çalışır. Azure Işlem öykünücüsünde çalışırken, doğrudan bağlantı noktası uç noktaları (**ınstanceınputendpoint** öğeleri) oluşturan hizmet yapılandırma öğeleri yok sayılır.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Rol iletişimini denetlemek için ağ trafiği kuralları
İç uç noktaları tanımladıktan sonra, rol örneklerinin birbirleriyle nasıl iletişim kurabildiğini denetlemek için ağ trafiği kuralları (oluşturduğunuz uç noktalara göre) ekleyebilirsiniz. Aşağıdaki diyagramda, rol iletişimini denetlemek için bazı yaygın senaryolar gösterilmektedir:

![Ağ trafiği kuralları senaryoları](./media/cloud-services-enable-communication-role-instances/scenarios.png "Ağ trafiği kuralları senaryoları")

Aşağıdaki kod örneğinde, önceki diyagramda gösterilen roller için rol tanımları gösterilmektedir. Her rol tanımı, tanımlı en az bir iç uç nokta içerir:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Roller arasındaki iletişimin kısıtlaması, hem sabit hem de otomatik olarak atanan bağlantı noktalarının iç uç noktalarında gerçekleşebilir.
> 
> 

Varsayılan olarak, bir iç uç nokta tanımlandıktan sonra, iletişim herhangi bir rolden herhangi bir kısıtlama olmadan bir rolün iç uç noktasına akabilir. İletişimi kısıtlamak için, hizmet tanımı dosyasındaki **ServiceDefinition** öğesine bir **NetworkTrafficRules** öğesi eklemeniz gerekir.

### <a name="scenario-1"></a>Senaryo 1
Yalnızca **WebRole1** ile **WorkerRole1**arasında ağ trafiğine izin verir.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Senaryo 2
Yalnızca **WebRole1** ile **WorkerRole1** ve **WorkerRole2**arasındaki ağ trafiğine izin verir.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Senaryo 3
Yalnızca **WebRole1** ile **WorkerRole1**ve **WorkerRole1** arasındaki ağ trafiğine izin **verir.**

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Senaryo 4
Yalnızca **WebRole1** ile **WorkerRole1**, **WebRole1** , **WorkerRole2**ve **WorkerRole1** arasında ağ trafiğine izin **verir.**

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Yukarıda kullanılan öğelere yönelik bir XML şeması başvurusu [burada](/previous-versions/azure/reference/gg557551(v=azure.100))bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar
Bulut hizmeti [modeli](cloud-services-model-and-package.md)hakkında daha fazla bilgi edinin.

