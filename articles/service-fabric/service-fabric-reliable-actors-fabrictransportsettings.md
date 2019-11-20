---
title: Azure Service Fabric aktörleri içindeki FabricTransport ayarlarını değiştirme | Microsoft Docs
description: Azure Service Fabric aktör iletişim ayarlarını yapılandırma hakkında bilgi edinin.
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: c840280e0a9662cd0175feed04f37119443efc3c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170560"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Reliable Actors için FabricTransport ayarlarını yapılandırma

Yapılandırabileceğiniz ayarlar şunlardır:
- C#: [Fabrictransportremotingsettings](
https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java: [Fabrictransportremotingsettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

FabricTransport varsayılan yapılandırmasını aşağıdaki yollarla değiştirebilirsiniz.

## <a name="assembly-attribute"></a>Derleme özniteliği

[FabricTransportActorRemotingProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN) özniteliğinin aktör istemci ve aktör hizmeti derlemelerine uygulanması gerekir.

Aşağıdaki örnekte, FabricTransport OperationTimeout ayarlarının varsayılan değerinin nasıl değiştirileceği gösterilmektedir:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   İkinci örnek, FabricTransport MaxMessageSize ve Operationtimeoutınseconds varsayılan değerlerini değiştirir.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Yapılandırma paketi

Varsayılan yapılandırmayı değiştirmek için bir [yapılandırma paketi](service-fabric-application-and-service-manifests.md) kullanabilirsiniz.

> [!IMPORTANT]
> Linux düğümlerinde, sertifikaların ped biçimli olması gerekir. Linux için sertifikaları bulma ve yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Linux 'ta sertifikaları yapılandırma](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>Aktör hizmeti için FabricTransport ayarlarını yapılandırma

Settings. xml dosyasına bir TransportSettings bölümü ekleyin.

Varsayılan olarak, aktör kodu SectionName 'i "&lt;ActorName @ no__t-1TransportSettings" olarak arar. Bu bulunamazsa, SectionName 'i "TransportSettings" olarak denetler.

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>Aktör istemci derlemesi için FabricTransport ayarlarını yapılandırma

İstemci bir hizmetin parçası olarak çalışmıyorsa, Client. exe dosyası ile aynı konumda bir "&lt;Client exe Name&gt;.settings.xml" dosyası oluşturabilirsiniz. Ardından bu dosyaya bir TransportSettings bölümü ekleyin. SectionName "TransportSettings" olmalıdır.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* Ikincil sertifikayla güvenli aktör hizmeti/Istemcisi için FabricTransport ayarları yapılandırılıyor.
  İkinci sertifika bilgileri, CertificateFindValuebySecondary parametresi eklenerek eklenebilir.
  Dinleyici TransportSettings için örnek aşağıda verilmiştir.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   Aşağıda, Istemci TransportSettings için örnek verilmiştir.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * Aktör hizmeti/Istemcisinin konu adını kullanarak güvenliğini sağlamak için FabricTransport ayarlarını yapılandırma.
    Kullanıcının FindBySubjectName olarak findType sağlaması, Certificateıssuerthumbbaskılar ve CertificateRemoteCommonNames değerlerini eklemesi gerekir.
    Dinleyici TransportSettings için örnek aşağıda verilmiştir.

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    Aşağıda, Istemci TransportSettings için örnek verilmiştir.

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
