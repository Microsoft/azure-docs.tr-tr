---
title: Media Services PlayReady lisans şablonuna genel bakış
description: Bu konu, PlayReady lisanslarını yapılandırmak için kullanılan bir PlayReady lisans şablonuna genel bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: aab7966b3e7592407bb00d0c81634c5fbb3cc4a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008293"
---
# <a name="media-services-playready-license-template-overview"></a>Media Services PlayReady lisans şablonuna genel bakış

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services, artık PlayReady lisanslarını sunmaya yönelik bir hizmet sağlar. Oynatıcı (örneğin, Silverlight) PlayReady korumalı içeriğinizi yürütmeye çalıştığında, lisans almak için lisans teslim hizmetine bir istek gönderilir. Lisans hizmeti isteği onayladığında, istemciye gönderilen lisansı yayınlar ve belirtilen içeriğin şifresini çözmek ve yürütmek için kullanılır.

Media Services Ayrıca, PlayReady lisanslarınızı yapılandırmak için kullanabileceğiniz API 'Ler de sağlar. Lisanslar, bir kullanıcı tarafından korunan içeriği yürütmeye çalıştığında PlayReady dijital hak yönetimi (DRM) çalışma zamanının uygulanmasını istediğiniz hakları ve kısıtlamaları içerir.
Burada, şunları belirtebileceğiniz PlayReady lisans kısıtlamalarına örnek verilmiştir:

* Lisansın geçerli olduğu tarih ve saat.
* Lisansın süresi dolarsa tarih saat değeri. 
* Lisansın, istemcide kalıcı depolamaya kaydedilmesi için. Kalıcı lisanslar genellikle içeriğin çevrimdışı olarak kayıttan yürütülmesine izin vermek için kullanılır.
* Bir oyuncunun içeriğinizi çalmak için sahip olması gereken en düşük güvenlik düzeyi. 
* Ses \ video içeriği için çıkış denetimlerine ait çıkış koruma düzeyi. 
* Daha fazla bilgi için [PlayReady Uyumluluk kuralları](https://www.microsoft.com/playready/licensing/compliance/) belgesinde "çıkış denetimleri" bölümüne (3,5) bakın.

> [!NOTE]
> Şu anda yalnızca PlayReady lisansının PlayRight 'ı yapılandırabilirsiniz. Bu hak gereklidir. PlayRight, istemciye içeriği kayıttan yürütme yeteneği sağlar. Ayrıca, kayıttan yürütmeye özgü kısıtlamaları yapılandırmak için PlayRight 'yi de kullanabilirsiniz. Daha fazla bilgi için bkz. [Playreadyplayright](media-services-playready-license-template-overview.md#PlayReadyPlayRight).
> 
> 

Media Services kullanarak PlayReady lisanslarını yapılandırmak için Media Services PlayReady lisans şablonunu yapılandırmanız gerekir. Şablon XML olarak tanımlanmıştır.

Aşağıdaki örnek, temel bir akış lisansını yapılandıran en basit (ve en yaygın) şablonu gösterir. Bu lisansla, istemcileriniz PlayReady korumalı içeriğinizi kayıttan yürütebilir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <PlayRight />
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

XML, "PlayReady lisans şablonu XML şeması" bölümünde tanımlanan PlayReady lisans şablonu XML şemasına uyar.

Media Services Ayrıca, XML 'den ve seri durumdan çıkarmak için kullanılabilecek bir dizi .NET sınıfı tanımlar. Ana sınıfların açıklaması için, bkz. Lisans şablonlarını yapılandırmak için kullanılan [.net sınıflarına Media Services](media-services-playready-license-template-overview.md#classes) .

PlayReady lisans şablonunu yapılandırmak için .NET sınıfları kullanan uçtan uca bir örnek için bkz. [PlayReady dinamik şifrelemesini ve lisans teslimi hizmetini kullanma](media-services-protect-with-playready-widevine.md).

## <a name="media-services-net-classes-that-are-used-to-configure-license-templates"></a><a id="classes"></a>Lisans şablonlarını yapılandırmak için kullanılan .NET sınıflarını Media Services
Aşağıdaki sınıflar, Media Services PlayReady lisans şablonlarını yapılandırmak için kullanılan ana .NET sınıflarıdır. Bu sınıflar, [PlayReady lisans şablonu XML şemasında](media-services-playready-license-template-overview.md#schema)tanımlanan türlerle eşlenir.

[Mediaserviceslicensetemplateserializer](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_mediaserviceslicensetemplateserializer) sınıfı, Media Services LISANS şablonu XML 'e ve öğesinden seri hale getirmek ve seri durumdan çıkarmak için kullanılır.

### <a name="playreadylicenseresponsetemplate"></a>PlayReadyLicenseResponseTemplate
[Playreadylicenseresponsetemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicenseresponsetemplate): Bu sınıf, kullanıcıya geri gönderilen yanıtın şablonunu temsil eder. Lisans sunucusu ve uygulama arasında özel bir veri dizesi alanı içerir (özel uygulama mantığı için yararlı olabilir). Ayrıca bir veya daha fazla lisans şablonu listesi içerir.

Şablon hiyerarşisindeki "en üst düzey" sınıfı olarak, yanıt şablonu lisans şablonlarının bir listesini içerir. Lisans şablonları, şablon verilerini oluşturan tüm diğer sınıfların (doğrudan veya dolaylı olarak) serileştirilmesi için içerir.

### <a name="playreadylicensetemplate"></a>PlayReadyLicenseTemplate
[Playreadylicensetemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicensetemplate): Bu sınıf, kullanıcılara döndürülecek PlayReady lisanslarını oluşturmak için kullanılan bir lisans şablonunu temsil eder. Bu, lisandaki içerik anahtarındaki verileri içerir. Ayrıca, içerik anahtarı kullanıldığında PlayReady DRM çalışma zamanının zorunlu olması gereken tüm hakları veya kısıtlamaları da içerir.

### <a name="playreadyplayright"></a><a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[Playreadyplayright](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadyplayright): Bu sınıf, bir PlayReady lisansının playright 'ı temsil eder. Kullanıcıya, lisansta ve PlayRight üzerinde yapılandırılmış tüm kısıtlamalara (kayıttan yürütmeye özgü ilke için) içerik konusunu kayıttan yürütme yeteneği verir. Bir PlayRight üzerindeki ilkenin çoğu, içeriğin oynatılabilecek çıkış türlerini denetleyen çıkış kısıtlamalarına sahiptir. Ayrıca, belirli bir çıkış kullanıldığında yerine verilmesi gereken tüm kısıtlamaları da içerir. Örneğin, DigitalVideoOnlyContentRestriction etkinleştirilirse, DRM çalışma zamanı yalnızca videonun dijital çıkışlar üzerinde görüntülenmesine izin verir. (Analog video çıkışları içeriği geçirmeye izin verilmez.)

> [!IMPORTANT]
> Bu tür kısıtlamalar güçlü olabilir, ancak aynı zamanda tüketici deneyimini de etkileyebilir. Çıkış korumaları çok kısıtlayıcıysa, içerik bazı istemcilerde oynatılamaz olabilir. Daha fazla bilgi için bkz. [PlayReady Uyumluluk kuralları](https://www.microsoft.com/playready/licensing/compliance/).
> 
> 

Silverlight tarafından desteklenen koruma düzeylerine bir örnek için bkz. [Çıkış korumaları Için Silverlight desteği](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838192(v=vs.95)).

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>PlayReady lisans şablonu XML şeması
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
  <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
  <xs:complexType name="AgcAndColorStripeRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
  <xs:simpleType name="ContentType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Unspecified" />
      <xs:enumeration value="UltravioletDownload" />
      <xs:enumeration value="UltravioletStreaming" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
  <xs:complexType name="ExplicitAnalogTelevisionRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
  <xs:complexType name="PlayReadyContentKey">
    <xs:sequence />
  </xs:complexType>
  <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
  <xs:complexType name="ContentEncryptionKeyFromHeader">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:PlayReadyContentKey">
        <xs:sequence />
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
  <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:PlayReadyContentKey">
        <xs:sequence>
          <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
  <xs:complexType name="PlayReadyLicenseResponseTemplate">
    <xs:sequence>
      <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
  <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
  <xs:complexType name="PlayReadyLicenseTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
      <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
      <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
  <xs:simpleType name="PlayReadyLicenseType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Nonpersistent" />
      <xs:enumeration value="Persistent" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
  <xs:complexType name="PlayReadyPlayRight">
    <xs:sequence>
      <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
  <xs:simpleType name="UnknownOutputPassingOption">
    <xs:restriction base="xs:string">
      <xs:enumeration value="NotAllowed" />
      <xs:enumeration value="Allowed" />
      <xs:enumeration value="AllowedWithVideoConstriction" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
  <xs:complexType name="ScmsRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
</xs:schema>
```

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]