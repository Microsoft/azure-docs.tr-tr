---
title: Azure Service Fabric ile güvenli WCF tabanlı hizmet iletişimi | Microsoft Docs
description: Azure Service Fabric kümesinde çalışan güvenilir hizmetler için WCF tabanlı iletişimleri güvenli hale getirme hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: 31a7a3a42436f3a818fcf48f2af5ca395fa02386
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170411"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Bir hizmet için güvenli WCF tabanlı iletişimler
Güvenlik, iletişimin en önemli yönlerinden biridir. Reliable Services uygulama çerçevesi, güvenliği artırmak için kullanabileceğiniz, önceden oluşturulmuş birkaç iletişim yığını ve aracı sağlar. Bu makalede, hizmet uzaktan iletişimini kullanırken güvenliğin nasıl iyileştireceğiniz ele ılırsınız.

Güvenilir hizmetler için WCF tabanlı bir iletişim yığınının nasıl ayarlanacağını açıklayan mevcut bir [örnek](service-fabric-reliable-services-communication-wcf.md) kullanıyoruz. WCF tabanlı bir iletişim yığını kullanırken bir hizmetin güvenliğinin sağlanmasına yardımcı olmak için şu adımları izleyin:

1. Hizmet için, oluşturduğunuz WCF iletişim dinleyicisinin (`WcfCommunicationListener`) güvenliğinin sağlanmasına yardımcı olmanız gerekir. Bunu yapmak için `CreateServiceReplicaListeners` yöntemini değiştirin.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. İstemcide, önceki [örnekte](service-fabric-reliable-services-communication-wcf.md) oluşturulan `WcfCommunicationClient` sınıfı değişmeden kalır. Ancak `WcfCommunicationClientFactory` ' in `CreateClientAsync` yöntemini geçersiz kılmanız gerekir:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    WCF iletişim istemcisi oluşturmak için `SecureWcfCommunicationClientFactory` kullanın (`WcfCommunicationClient`). Hizmeti yöntemlerini çağırmak için istemcisini kullanın.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

Sonraki adım olarak, [Reliable Services içinde OWIN Ile Web API 'sini](service-fabric-reliable-services-communication-webapi.md)okuyun.
