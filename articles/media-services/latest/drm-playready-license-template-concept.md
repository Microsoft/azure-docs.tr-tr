---
title: Microsoft PlayReady lisans şablonu Media Services
description: PlayReady lisans şablonuyla Azure Media Services v3 ve PlayReady lisanslarını yapılandırma hakkında bilgi edinin.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a6ce4795ae334f0f6c95d0782a06c96cc5a833d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069124"
---
# <a name="media-services-v3-with-playready-license-template"></a>PlayReady lisans şablonuyla v3 Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services içeriğinizi **Microsoft PlayReady** ile şifrelemenizi sağlar. Media Services, PlayReady lisanslarını teslim etmek için de bir hizmet sağlar. PlayReady lisanslarını yapılandırmak için Media Services API 'Leri kullanabilirsiniz. Bir oyuncu PlayReady korumalı içeriğinizi yürütmeye çalıştığında, lisans almak için lisans teslim hizmetine bir istek gönderilir. Lisans hizmeti isteği onayladığında, istemciye gönderilen lisansı yayınlar ve belirtilen içeriğin şifresini çözmek ve yürütmek için kullanılır.

PlayReady lisansları, bir kullanıcı tarafından korunan içeriği yürütmeye çalıştığında PlayReady dijital hak yönetimi (DRM) çalışma zamanının uygulanmasını istediğiniz hakları ve kısıtlamaları içerir. Burada, şunları belirtebileceğiniz PlayReady lisans kısıtlamalarına örnek verilmiştir:

* Lisansın geçerli olduğu tarih ve saat.
* Lisansın süresi dolarsa tarih saat değeri. 
* Lisansın, istemcide kalıcı depolamaya kaydedilmesi için. Kalıcı lisanslar genellikle içeriğin çevrimdışı olarak kayıttan yürütülmesine izin vermek için kullanılır.
* Bir oyuncunun içeriğinizi çalmak için sahip olması gereken en düşük güvenlik düzeyi. 
* Ses \ video içeriği için çıkış denetimlerine ait çıkış koruma düzeyi. 
* Daha fazla bilgi için [PlayReady Uyumluluk kuralları](https://www.microsoft.com/playready/licensing/compliance/) belgesinde "çıkış denetimleri" bölümüne (3,5) bakın.

> [!NOTE]
> Şu anda yalnızca PlayReady lisansının PlayRight 'ı yapılandırabilirsiniz. Bu hak gereklidir. PlayRight, istemciye içeriği kayıttan yürütme yeteneği sağlar. Ayrıca, kayıttan yürütmeye özgü kısıtlamaları yapılandırmak için PlayRight 'yi de kullanabilirsiniz. 
> 

Bu konuda, Media Services ile PlayReady lisanslarının nasıl yapılandırılacağı açıklanmaktadır.

## <a name="basic-streaming-license-example"></a>Temel akış lisansı örneği

Aşağıdaki örnek, temel bir akış lisansını yapılandıran en basit (ve en yaygın) şablonu gösterir. Bu lisansla, istemcileriniz PlayReady korumalı içeriğinizi kayıttan yürütebilir. 

XML, [PlayReady lisans şablonu XML şeması](#schema) bölümünde tanımlanan PlayReady LISANS şablonu XML şemasına uyar.

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

## <a name="use-media-services-apis-to-configure-license-templates"></a><a id="classes"></a>Lisans şablonlarını yapılandırmak için Media Services API 'Leri kullanma

Media Services, PlayReady lisans şablonunu yapılandırmak için kullanabileceğiniz türler sağlar. 

Aşağıdaki kod parçacığı, PlayReady lisans şablonunu yapılandırmak için .NET sınıflarını Media Services kullanır. Sınıflar, [Microsoft. Azure. Management. Media. modeller](/dotnet/api/microsoft.azure.management.media.models) ad alanında tanımlanmıştır. Kod parçacığı, PlayReady lisansının PlayRight 'ı yapılandırır. PlayRight, kullanıcıya, lisansta ve PlayRight üzerinde yapılandırılmış herhangi bir kısıtlamayla içerik konusunu kayıttan yürütmeyi sağlar (kayıttan yürütmeye özgü ilke için). Bir PlayRight üzerindeki ilkenin çoğu, içeriğin yürütülebildiğinden çıkış türlerini denetleyen çıkış kısıtlamasıyla ilgilidir. Ayrıca, belirli bir çıkış kullanıldığında yerine verilmesi gereken tüm kısıtlamaları da içerir. Örneğin, DigitalVideoOnlyContentRestriction etkinleştirilirse, DRM çalışma zamanı yalnızca videonun dijital çıkışlar üzerinde görüntülenmesine izin verir. (Analog video çıkışları içeriği geçirmeye izin verilmez.)

> [!IMPORTANT]
> PlayReady lisansında güçlü kısıtlamalar vardır. Çıkış korumaları çok kısıtlayıcıysa, içerik bazı istemcilerde oynatılamaz olabilir. Daha fazla bilgi için bkz. [PlayReady Uyumluluk kuralları](https://www.microsoft.com/playready/licensing/compliance/).

### <a name="configure-playready-license-template-with-net"></a>.NET ile PlayReady lisans şablonunu yapılandırma

```csharp
ContentKeyPolicyPlayReadyLicense objContentKeyPolicyPlayReadyLicense;
objContentKeyPolicyPlayReadyLicense = new ContentKeyPolicyPlayReadyLicense
{
    AllowTestDevices = true,
    BeginDate = new DateTime(2016, 1, 1),
    ContentKeyLocation = new ContentKeyPolicyPlayReadyContentEncryptionKeyFromHeader(),
    ContentType = ContentKeyPolicyPlayReadyContentType.UltraVioletStreaming,
    LicenseType = drmSettings.EnableOfflineMode ? ContentKeyPolicyPlayReadyLicenseType.Persistent : ContentKeyPolicyPlayReadyLicenseType.NonPersistent,
    PlayRight = new ContentKeyPolicyPlayReadyPlayRight
    {
        ImageConstraintForAnalogComponentVideoRestriction = true,
        ExplicitAnalogTelevisionOutputRestriction = new ContentKeyPolicyPlayReadyExplicitAnalogTelevisionRestriction(true, 2),
        AllowPassingVideoContentToUnknownOutput = ContentKeyPolicyPlayReadyUnknownOutputPassingOption.Allowed,
        FirstPlayExpiration = TimeSpan.FromSeconds(20.0),
    }
};
```

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

## <a name="next-steps"></a>Sonraki adımlar

[DRM ile koruma](drm-protect-with-drm-tutorial.md)
