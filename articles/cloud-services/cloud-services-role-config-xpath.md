---
title: Cloud Services rol yapılandırması XPath sayfa | Microsoft Docs
description: Ayarları bir ortam değişkeni olarak göstermek için bulut hizmeti rol yapılandırmasında kullanabileceğiniz çeşitli XPath ayarları.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: cd2bdc4fc4b2a135907851ca4d3034430618e0cd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359009"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Rol yapılandırma ayarlarını XPath ile bir ortam değişkeni olarak kullanıma sunma
Bulut hizmeti çalışanı veya Web rolü hizmeti tanım dosyasında, çalışma zamanı yapılandırma değerlerini ortam değişkenleri olarak kullanıma sunabilirsiniz. Aşağıdaki XPath değerleri desteklenir (API değerlerine karşılık gelir).

Bu XPath değerleri, [Microsoft. WindowsAzure. ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) kitaplığı aracılığıyla da kullanılabilir. 

## <a name="app-running-in-emulator"></a>Öykünücüde çalışan uygulama
Uygulamanın öykünücüsünde çalıştığını gösterir.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/Deployment/@emulated" |
| Kod |var x = RoleEnvironment. ıstreamted; |

## <a name="deployment-id"></a>Dağıtım Kimliği
Örnek için dağıtım KIMLIĞINI alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/Deployment/@id" |
| Kod |var DeploymentId = RoleEnvironment. DeploymentId; |

## <a name="role-id"></a>Rol Kimliği
Örnek için geçerli rol KIMLIĞINI alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@id" |
| Kod |var kimliği = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Etki alanını güncelleştir
Örneğin güncelleştirme etki alanını alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kod |var ud = RoleEnvironment. Currentrotaınstance. UpdateDomain; |

## <a name="fault-domain"></a>Hata etki alanı
Örneğin hata etki alanını alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kod |var FD = RoleEnvironment. Currentrotaınstance. FaultDomain; |

## <a name="role-name"></a>Rol adı
Örneklerin rol adını alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@roleName" |
| Kod |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Yapılandırma ayarı
Belirtilen yapılandırma ayarının değerini alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/roleenvironment/currentınstance/ConfigurationSettings/configurationsetting [@name= ' Setting1 ']/@value" |
| Kod |var Setting = RoleEnvironment. GetConfigurationSettingValue ("Setting1"); |

## <a name="local-storage-path"></a>Yerel depolama yolu
Örnek için yerel depolama yolunu alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/roleenvironment/currentınstance/localresources/localresource [@name= ' LocalStore1 ']/@path" |
| Kod |var localResourcePath = RoleEnvironment. GetLocalResource ("LocalStore1"). RootPath |

## <a name="local-storage-size"></a>Yerel depolama boyutu
Örnek için yerel depolamanın boyutunu alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/roleenvironment/currentınstance/localresources/localresource [@name= ' LocalStore1 ']/@sizeInMB" |
| Kod |var Localresourcesizeınmb = RoleEnvironment. GetLocalResource ("LocalStore1"). Maximumsizeınmegabayt; |

## <a name="endpoint-protocol"></a>Uç nokta Protokolü
Örnek için uç nokta protokolünü alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/endpoints/Endpoint [@name= ' Endpoint1 ']/@protocol" |
| Kod |var Prot = RoleEnvironment. Currentrotaınstance. ınstanceendpoints ["Endpoint1"]. Protocol |

## <a name="endpoint-ip"></a>Uç nokta IP 'si
Belirtilen bitiş noktasının IP adresini alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/endpoints/Endpoint [@name= ' Endpoint1 ']/@address" |
| Kod |var adresi = RoleEnvironment. Currentrotaınstance. ınstanceendpoints ["Endpoint1"]. IPEndpoint. Address |

## <a name="endpoint-port"></a>Uç nokta bağlantı noktası
Örnek için uç nokta bağlantı noktasını alır.

| Type | Örnek |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/endpoints/Endpoint [@name= ' Endpoint1 ']/@port" |
| Kod |var bağlantı noktası = RoleEnvironment. Currentrotaınstance. ınstanceendpoints ["Endpoint1"]. IPEndpoint. Port; |

## <a name="example"></a>Örnek
`TestIsEmulated` [Burada XPath@emulated değeri](#app-running-in-emulator)olarak ayarlanan adlı bir ortam değişkeni ile bir başlangıç görevi oluşturan bir çalışan rolü örneği verilmiştir. 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Sonraki adımlar
[ServiceConfiguration. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) dosyası hakkında daha fazla bilgi edinin.

Bir [Servicepackage. cspkg](cloud-services-model-and-package.md#servicepackagecspkg) paketi oluşturun.

Bir rol için [Uzak Masaüstü 'nü](cloud-services-role-enable-remote-desktop-new-portal.md) etkinleştirin.

