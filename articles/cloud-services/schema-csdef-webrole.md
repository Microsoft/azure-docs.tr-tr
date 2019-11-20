---
title: Azure Cloud Services def. WebRole şeması | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: bafc8780368f58a7076ae472636d852d698d276c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941360"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure Cloud Services Definition WebRole şeması
Azure Web rolü, IIS 7 tarafından desteklenen ASP.NET, PHP, Windows Communication Foundation ve FastCGI gibi Web uygulaması programlamasında özelleştirilmiş bir roldür.

Hizmet tanım dosyası için varsayılan uzantı. csdef ' dir.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Web rolü için temel hizmet tanımı şeması  
Bir Web rolü içeren bir hizmet tanımı dosyasının temel biçimi aşağıdaki gibidir.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Şema öğeleri  
Hizmet tanımı dosyası, bu konunun sonraki bölümlerinde ayrıntılı olarak açıklanan bu öğeleri içerir:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Ayar](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Noktalarının](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[Instanceınputendpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Sertifikalar](#Certificates)

[Sertifika](#Certificate)

[İşlemlerinin](#Imports)

[İçeri Aktar](#Import)

[Çalışma zamanı](#Runtime)

[Ortam](#Environment)

[Değişken](#Variable)

[Roleınstancevalue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Barındıra](#Sites)

[Bölgesi](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Lara](#Bindings)

[Bağlama](#Binding)

[Başlangıç](#Startup)

[Görev](#Task)

[Dekiler](#Contents)

[İçeriği](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a>WebRole  
`WebRole` Öğesi, IIS 7 ve ASP.NET tarafından desteklendiği şekilde Web uygulaması programlama için özelleştirilmiş bir rol tanımlar. Bir hizmet, sıfır veya daha fazla web rolü içerebilir.

Aşağıdaki tablo, `WebRole` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Web rolünün adı. Rolün adı benzersiz olmalıdır.|  
|enableNativeCodeExecution|boolean|İsteğe bağlı. Varsayılan değer `true`; yerel kod yürütme ve tam güven varsayılan olarak etkindir. Web rolü için yerel `false` kod yürütmeyi devre dışı bırakmak üzere bu özniteliği olarak ayarlayın ve bunun yerine Azure kısmi güven kullanın.|  
|VMSize|dize|İsteğe bağlı. Role ayrılan sanal makinenin boyutunu değiştirmek için bu değeri ayarlayın. Varsayılan değer `Small` şeklindedir. Daha fazla bilgi için bkz. [sanal makine boyutları Cloud Services](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a>ConfigurationSettings  
`ConfigurationSettings` Öğesi, bir Web rolü için yapılandırma ayarları koleksiyonunu açıklar. Bu öğe, `Setting` öğesinin üst öğesidir.

##  <a name="Setting"></a>Ayarlanmasını  
`Setting` Öğesi bir rol örneği için yapılandırma ayarını belirten bir ad ve değer çiftini tanımlar.

Aşağıdaki tablo, `Setting` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Yapılandırma ayarı için benzersiz bir ad.|  

Bir rolün yapılandırma ayarları, hizmet tanım dosyasında belirtilen ve hizmet yapılandırma dosyasında ayarlanan ad ve değer çiftleridir.

##  <a name="LocalResources"></a>LocalResources  
Öğesi `LocalResources` , bir Web rolü için yerel depolama kaynakları koleksiyonunu açıklar. Bu öğe, `LocalStorage` öğesinin üst öğesidir.

##  <a name="LocalStorage"></a>LocalStorage  
Öğesi `LocalStorage` , çalışma zamanında hizmet için dosya sistemi alanı sağlayan bir yerel depolama kaynağı tanımlar. Rol, sıfır veya daha fazla yerel depolama kaynağı tanımlayabilir.

> [!NOTE]
>  Öğesi, Azure SDK 'sının önceki sürümleriyle uyumluluğu desteklemek `WebRole` için öğesinin bir alt öğesi olarak görünebilir. `LocalStorage`

Aşağıdaki tablo, `LocalStorage` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Yerel depo için benzersiz bir ad.|  
|cleanOnRoleRecycle|boolean|İsteğe bağlı. Rol yeniden başlatıldığında yerel deponun temizlenmesinin gerekip gerekmediğini gösterir. Varsayılan değer `true`.|  
|Sizeınmb|int|İsteğe bağlı. Yerel depo için ayrılacak, MB cinsinden istenen depolama alanı miktarı. Belirtilmemişse, ayrılan varsayılan depolama alanı 100 MB 'tır. Ayrılabilen en az depolama alanı miktarı 1 MB 'tır.<br /><br /> Yerel kaynakların en büyük boyutu sanal makine boyutuna bağımlıdır. Daha fazla bilgi için bkz. [sanal makine boyutları Cloud Services](cloud-services-sizes-specs.md).|  
  
Yerel depolama kaynağına ayrılan dizinin adı, Name özniteliği için belirtilen değere karşılık gelir.

##  <a name="Endpoints"></a>Noktalarının  
`Endpoints` Öğesi, bir rol için giriş (dış), iç ve örnek giriş uç noktaları koleksiyonunu açıklar. Bu öğe `InputEndpoint`, `InternalEndpoint`, ve `InstanceInputEndpoint` öğelerinin üst öğesidir.

Giriş ve Iç uç noktalar ayrı olarak ayrılır. Bir hizmette toplam 25 giriş, iç ve örnek giriş uç noktası bulunabilir ve bu da bir hizmette izin verilen 25 rol arasında ayrılabilir. Örneğin, 5 rol varsa, rol başına 5 giriş uç noktası ayırabilirsiniz veya tek bir role 25 giriş uç noktası ayırabilirsiniz veya her biri 25 rol için 1 giriş uç noktası ayırabilirsiniz.

> [!NOTE]
>  Dağıtılan her rolün rol başına bir örnek olması gerekir. Abonelik için varsayılan sağlama 20 çekirdekle sınırlıdır ve bu nedenle bir rolün 20 örneğiyle sınırlıdır. Uygulamanız varsayılan sağlama tarafından sağlanmasından daha fazla örnek gerektiriyorsa, kotayı artırma hakkında daha fazla bilgi için [faturalandırma, abonelik yönetimi ve kota desteği '](https://azure.microsoft.com/support/options/) ne bakın.

##  <a name="InputEndpoint"></a>Inputendpoint  
`InputEndpoint` Öğesi bir Web rolü için bir dış uç nokta tanımlar.

HTTP, HTTPS, UDP ve TCP uç noktalarının birleşimi olan birden fazla uç nokta tanımlayabilirsiniz. Bir giriş uç noktası için seçtiğiniz herhangi bir bağlantı noktası numarasını belirtebilirsiniz, ancak hizmette her rol için belirtilen bağlantı noktası numaraları benzersiz olmalıdır. Örneğin, bir Web rolünün HTTP için 80 bağlantı noktasını ve HTTPS için bağlantı noktası 443 kullandığını belirtirseniz, ikinci bir Web rolünün HTTP için bağlantı noktası 8080 ' i ve HTTPS için bağlantı noktası 8043 kullandığını belirtebilirsiniz.

Aşağıdaki tablo, `InputEndpoint` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Dış uç nokta için benzersiz bir ad.|  
|protocol|dize|Gerekli. Dış uç nokta için Aktarım Protokolü. Bir Web rolü için `HTTP`olası değerler `UDP`, `HTTPS`, veya `TCP`' dir.|  
|port|int|Gerekli. Dış uç nokta için bağlantı noktası. Seçtiğiniz herhangi bir bağlantı noktası numarasını belirtebilirsiniz, ancak hizmette her bir rol için belirtilen bağlantı noktası numaraları benzersiz olmalıdır.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  
|sertifika|dize|Bir HTTPS uç noktası için gereklidir. Bir `Certificate` öğe tarafından tanımlanan sertifikanın adı.|  
|localPort|int|İsteğe bağlı. Uç noktada iç bağlantılar için kullanılan bir bağlantı noktasını belirtir. `localPort` Öznitelik, uç noktasındaki dış bağlantı noktasını bir roldeki iç bağlantı noktasına eşler. Bu, bir rolün dışarıdan açığa çıkarılan bir bağlantı noktasında iç bileşen ile iletişim kurması gereken senaryolarda yararlıdır.<br /><br /> Belirtilmemişse, değeri `localPort` `port` özniteliğiyle aynı olur. Çalışma zamanı API 'si `localPort` kullanılarak keşfedilen ayrılmamış bir bağlantı noktasını otomatik olarak atamak için değerini "*" olarak ayarlayın.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.<br /><br /> `localPort` Özniteliği yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.|  
|ignoreRoleInstanceStatus|boolean|İsteğe bağlı. Bu özniteliğin değeri olarak `true`ayarlandığında, bir hizmetin durumu yok sayılır ve uç nokta yük dengeleyici tarafından kaldırılmaz. Bu değer, bir `true` hizmetin meşgul örneklerinde hata ayıklama için yararlı olarak ayarlanıyor. Varsayılan değer `false` şeklindedir. **Not:**  Bir uç nokta, rol kullanılmaya devam eden bir durumda olsa bile trafiği alabilir.|  
|loadBalancerProbe|dize|İsteğe bağlı. Giriş uç noktasıyla ilişkili yük dengeleyici araştırmasının adı. Daha fazla bilgi için bkz. [Loadbalanceraraştırma şeması](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a>InternalEndpoint  
`InternalEndpoint` Öğesi bir Web rolüne iç uç nokta tanımlar. İç uç nokta yalnızca hizmet içinde çalışan diğer rol örnekleri için kullanılabilir; hizmet dışındaki istemciler için kullanılamaz. `Sites` Öğesi içermeyen Web rollerinin yalnızca tek bir http, UDP veya TCP iç uç noktası olabilir.

Aşağıdaki tablo, `InternalEndpoint` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. İç uç nokta için benzersiz bir ad.|  
|protocol|dize|Gerekli. İç uç nokta için Aktarım Protokolü. Olası değerler şunlardır `HTTP` `TCP` `ANY`, ,veya.`UDP`<br /><br /> Bir değeri `ANY` herhangi bir protokolün, herhangi bir bağlantı noktasına izin verildiğini belirtir.|  
|port|int|İsteğe bağlı. Uç noktada iç yük dengeli bağlantılar için kullanılan bağlantı noktası. Yük dengeli bir uç nokta iki bağlantı noktası kullanır. Genel IP adresi için kullanılan bağlantı noktası ve özel IP adresinde kullanılan bağlantı noktası. Bunlar genellikle bunlar aynı şekilde ayarlanmıştır, ancak farklı bağlantı noktaları kullanmayı seçebilirsiniz.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.<br /><br /> `Port` Özniteliği yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.|  

##  <a name="InstanceInputEndpoint"></a>Instanceınputendpoint  
`InstanceInputEndpoint` Öğesi bir Web rolüne örnek giriş uç noktası tanımlar. Örnek giriş uç noktası, yük dengeleyicide bağlantı noktası iletme kullanılarak belirli bir rol örneğiyle ilişkilendirilir. Her örnek giriş uç noktası, olası bir bağlantı noktası aralığından belirli bir bağlantı noktasıyla eşleştirilir. Bu öğe, `AllocatePublicPortFrom` öğesinin üst öğesidir.

`InstanceInputEndpoint` Öğesi yalnızca Azure SDK 1,7 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `InstanceInputEndpoint` öğesinin özniteliklerini açıklar.
  
| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Uç nokta için benzersiz bir ad.|  
|localPort|int|Gerekli. Yük dengeleyiciden iletilen gelen trafiği almak için tüm rol örneklerinin dinleyeceği iç bağlantı noktasını belirtir. Olası değerler 1 ile 65535 (dahil) arasında değişir.|  
|protocol|dize|Gerekli. İç uç nokta için Aktarım Protokolü. Olası değerler: `udp` veya `tcp`. Http `tcp` /https tabanlı trafik için kullanın.|  
  
##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom  
Öğesi `AllocatePublicPortFrom` , dış müşteriler tarafından her örnek giriş uç noktasına erişmek için kullanılabilen genel bağlantı noktası aralığını açıklar. Ortak (VIP) bağlantı noktası numarası bu aralıktan ayrılır ve kiracı dağıtımı ve güncelleştirmesi sırasında her bir rol örneği uç noktasına atanır. Bu öğe, `FixedPortRange` öğesinin üst öğesidir.

`AllocatePublicPortFrom` Öğesi yalnızca Azure SDK 1,7 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="FixedPort"></a>FixedPort  
`FixedPort` Öğesi, iç uç nokta için uç noktada yük dengeli bağlantıları sağlayan bağlantı noktasını belirtir.

`FixedPort` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `FixedPort` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|port|int|Gerekli. İç uç nokta için bağlantı noktası. Bu, `FixedPortRange` en düşük ve en fazla aynı bağlantı noktasına ayarlanması ile aynı etkiye sahiptir.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  

##  <a name="FixedPortRange"></a>FixedPortRange  
`FixedPortRange` Öğesi, iç uç noktaya veya örnek giriş uç noktasına atanan bağlantı noktası aralığını belirtir ve uç noktada yük dengeli bağlantılar için kullanılan bağlantı noktasını ayarlar.

> [!NOTE]
>  Öğesi `FixedPortRange` , bulunduğu öğeye göre farklı şekilde çalışır. `FixedPortRange` Öğeöğesiiçindeyse,yükdengeleyicidekitümbağlantınoktalarını,rolünçalıştığıtümsanalmakineleriçinminveMax`InternalEndpoint` özniteliklerinin aralığı içinde açar. `FixedPortRange` Öğesi `InstanceInputEndpoint` öğesinde olduğunda, rolü çalıştıran her bir sanal makinede en az ve en fazla öznitelik aralığı içinde yalnızca bir bağlantı noktası açılır.

`FixedPortRange` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `FixedPortRange` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|dk|int|Gerekli. Aralıktaki en küçük bağlantı noktası. Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  
|en çok|dize|Gerekli. Aralıktaki en fazla bağlantı noktası. Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  

##  <a name="Certificates"></a>Sertifika  
`Certificates` Öğesi bir Web rolü için sertifika koleksiyonunu açıklar. Bu öğe, `Certificate` öğesinin üst öğesidir. Bir rol, ilişkili sayıda sertifikaya sahip olabilir. Sertifikalar öğesini kullanma hakkında daha fazla bilgi için bkz. [hizmet tanımı dosyasını bir sertifikayla değiştirme](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Sertifika  
Öğesi `Certificate` , bir Web rolüyle ilişkili bir sertifika tanımlar.

Aşağıdaki tablo, `Certificate` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Bir https `InputEndpoint` öğesiyle ilişkilendirildiğinde bu sertifikaya başvurmak için kullanılan bir ad.|  
|storeLocation|dize|Gerekli. Bu sertifikanın yerel makinede bulunabileceği sertifika deposunun konumu. Olası değerler şunlardır `CurrentUser`. `LocalMachine`|  
|storeName|dize|Gerekli. Bu sertifikanın yerel makinede bulunduğu sertifika deposunun adı. Olası değerler `My`, yerleşik depo adlarını `TrustedPeople`, `Trust` `CA` `Root`,,, ,`TrustedPublisher`, ,`AuthRoot`, veyaherhangibirözelmağazaadınıiçerir.`AddressBook` `Disallowed` Özel bir mağaza adı belirtilmişse mağaza otomatik olarak oluşturulur.|  
|permissionLevel|dize|İsteğe bağlı. Rol işlemlerine verilen erişim izinlerini belirtir. Yalnızca yükseltilmiş işlemlerin özel anahtara erişip erişebilmesini istiyorsanız izin belirtin `elevated` . `limitedOrElevated`izin, tüm rol işlemlerinin özel anahtara erişmesine izin verir. Olası değerler: `limitedOrElevated` veya `elevated`. Varsayılan değer `limitedOrElevated` şeklindedir.|  

##  <a name="Imports"></a>İşlemlerinin  
Öğesi `Imports` , Konuk işletim sistemine bileşenler ekleyen bir Web rolü için içeri aktarma modülleri koleksiyonunu açıklar. Bu öğe, `Import` öğesinin üst öğesidir. Bu öğe isteğe bağlıdır ve bir rol yalnızca bir içeri aktarma bloğuna sahip olabilir. 

`Imports` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="Import"></a>Aktarmaya  
Öğesi `Import` , Konuk işletim sistemine eklenecek bir modül belirtir.

`Import` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `Import` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|Ladı|dize|Gerekli. İçeri aktarılacak modülün adı. Geçerli içeri aktarma modülleri şunlardır:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Tanılama<br /><br /> RemoteAccess ve RemoteForwarder modülleri, rol örneğinizi Uzak Masaüstü bağlantıları için yapılandırmanıza olanak tanır. Daha fazla bilgi için bkz. [Uzak Masaüstü bağlantısı etkinleştirme](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Tanılama modülü bir rol örneği için tanılama verileri toplamanızı sağlar.|  

##  <a name="Runtime"></a>Çalışma zamanı  
`Runtime` Öğesi, Azure ana bilgisayar işleminin çalışma zamanı ortamını denetleyen bir Web rolü için ortam değişkeni ayarlarının bir koleksiyonunu açıklar. Bu öğe, `Environment` öğesinin üst öğesidir. Bu öğe isteğe bağlıdır ve bir rol yalnızca bir çalışma zamanı bloğuna sahip olabilir.

`Runtime` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `Runtime` öğesinin özniteliklerini açıklar:  

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|executionContext|dize|İsteğe bağlı. Rol Işleminin başlatıldığı bağlamı belirtir. Varsayılan bağlam `limited`.<br /><br /> -   `limited`– İşlem, yönetici ayrıcalıkları olmadan başlatılır.<br />-   `elevated`– İşlem, yönetici ayrıcalıklarıyla başlatılır.|  

##  <a name="Environment"></a>Ortamınızın  
Öğesi `Environment` , bir Web rolü için ortam değişkeni ayarlarının koleksiyonunu açıklar. Bu öğe, `Variable` öğesinin üst öğesidir. Rol herhangi bir sayıda ortam değişkeni kümesine sahip olabilir.

##  <a name="Variable"></a>Değişken  
Öğesi `Variable` , Konuk işletim içinde ayarlanacak bir ortam değişkenini belirtir.

`Variable` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `Variable` öğesinin özniteliklerini açıklar:  

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Ayarlanacak ortam değişkeninin adı.|  
|value|dize|İsteğe bağlı. Ortam değişkeni için ayarlanacak değer. Bir değer özniteliği veya bir `RoleInstanceValue` öğesi dahil etmeniz gerekir.|  

##  <a name="RoleInstanceValue"></a>Roleınstancevalue  
`RoleInstanceValue` Öğesi, değişkenin değerinin alınacağı XPath 'i belirtir.

Aşağıdaki tablo, `RoleInstanceValue` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|XPath|dize|İsteğe bağlı. Örnek için dağıtım ayarlarının konum yolu. Daha fazla bilgi için bkz. [XPath Ile yapılandırma değişkenleri](cloud-services-role-config-xpath.md).<br /><br /> Bir değer özniteliği veya bir `RoleInstanceValue` öğesi dahil etmeniz gerekir.|  

##  <a name="EntryPoint"></a>Noktası  
Öğesi `EntryPoint` , bir rolün giriş noktasını belirtir. Bu öğe, `NetFxEntryPoint` öğelerinin üst öğesidir. Bu öğeler, rol giriş noktası olarak görev yapmak için varsayılan WaWorkerHost. exe dışında bir uygulama belirtmenize olanak tanır.

`EntryPoint` Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="NetFxEntryPoint"></a>NetFxEntryPoint  
Öğesi `NetFxEntryPoint` , bir rol için çalıştırılacak programı belirtir.

> [!NOTE]
>  `NetFxEntryPoint` Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `NetFxEntryPoint` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|assemblyName|dize|Gerekli. Giriş noktasını içeren derlemenin yolu ve dosya adı. Yol,  **\\%roleroot%\approot** klasörüne göredir (içinde `commandLine`  **\\%roleroot%\approot** belirtmeyin, kabul edilir). **% Roleroot%** , Azure tarafından tutulan bir ortam değişkenidir ve rolünüz için kök klasör konumunu temsil eder. %Roleroot%\approot klasörü, rolünüzün uygulama klasörünü temsil eder.  **\\**<br /><br /> HWC rolleri için yol her zaman  **\\%roleroot%\approot\bin** klasörüne görelidir.<br /><br /> Tam IIS ve IIS Express Web rolleri için, derleme  **\\%roleroot%\approot** klasörüne göre bulunamıyorsa,  **\\%roleroot%\approot\bin** aranır.<br /><br /> Tam IIS için bu geri dönüş davranışı önerilen bir uygulama değildir ve gelecekteki sürümlerde kaldırılmış olabilir.|  
|targetFrameworkVersion|dize|Gerekli. Derlemenin oluşturulduğu .NET Framework sürümü. Örneğin: `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a>Barındıra  
Öğesi `Sites` , bir Web rolünde barındırılan Web siteleri ve Web uygulamaları koleksiyonunu açıklar. Bu öğe, `Site` öğesinin üst öğesidir. Bir `Sites` öğe belirtmezseniz Web rolünüzün eski web rolü olarak barındırıldığı ve Web rolünüzde barındırılan bir Web siteniz olabilir. Bu öğe isteğe bağlıdır ve bir rol yalnızca bir siteler bloğuna sahip olabilir.

`Sites` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="Site"></a>Bölgesi  
`Site` Öğesi, Web rolünün bir parçası olan bir Web sitesini veya Web uygulamasını belirtir.

`Site` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `Site` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Web sitesinin veya uygulamanın adı.|  
|physicalDirectory|dize|Site kökünün içerik dizininin konumu. Konum, mutlak bir yol veya. csdef konumuna göre belirtilebilir.|  

##  <a name="VirtualApplication"></a>VirtualApplication  
`VirtualApplication` Öğesi Internet Information Services (IIS) 7 ' de bir uygulamayı tanımlar, içerik teslim eden veya http gibi protokoller üzerinden hizmetler sağlayan bir dosya grubudur. IIS 7 ' de bir uygulama oluşturduğunuzda, uygulamanın yolu site URL 'sinin bir parçası haline gelir.

`VirtualApplication` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `VirtualApplication` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Sanal uygulamayı tanımlayacak bir ad belirtir.|  
|physicalDirectory|dize|Gerekli. Geliştirme makinesindeki sanal uygulamayı içeren yolu belirtir. İşlem öykünücüsünde IIS bu konumdan içerik almak üzere yapılandırılmıştır. Azure 'a dağıtım yaparken, fiziksel dizin içeriği hizmetin geri kalanı ile birlikte paketlenir. Hizmet paketi Azure 'a dağıtıldığında, IIS, paketi açılan içeriklerin konumuyla yapılandırılır.|  

##  <a name="VirtualDirectory"></a>Dizin  
Öğesi `VirtualDirectory` , IIS 'de belirttiğiniz bir dizin adı (yol da denir) belirtir ve yerel veya uzak bir sunucuda fiziksel bir dizine eşlenir.

`VirtualDirectory` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `VirtualDirectory` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Sanal dizini tanımlayacak bir ad belirtir.|  
|value|physicalDirectory|Gerekli. Geliştirme makinesindeki Web sitesini veya sanal dizin içeriğini içeren yolu belirtir. İşlem öykünücüsünde IIS bu konumdan içerik almak üzere yapılandırılmıştır. Azure 'a dağıtım yaparken, fiziksel dizin içeriği hizmetin geri kalanı ile birlikte paketlenir. Hizmet paketi Azure 'a dağıtıldığında, IIS, paketi açılan içeriklerin konumuyla yapılandırılır.|  

##  <a name="Bindings"></a>Lara  
Öğesi `Bindings` , bir Web sitesi için bağlama koleksiyonunu açıklar. Bu, `Binding` öğesinin üst öğesidir. Öğesi her `Site` öğe için gereklidir. Uç noktaları yapılandırma hakkında daha fazla bilgi için bkz. [rol örnekleri Için Iletişimi etkinleştirme](cloud-services-enable-communication-role-instances.md).

`Bindings` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="Binding"></a>Bağlayıcısı  
Öğesi `Binding` , bir Web sitesi veya Web uygulamasıyla iletişim kurmak için istekler için gereken yapılandırma bilgilerini belirtir.

`Binding` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|name|dize|Gerekli. Bağlamayı tanımlayacak bir ad belirtir.|  
|Uçnoktaadı|dize|Gerekli. Bağlanacak bitiş noktası adını belirtir.|  
|hostHeader|dize|İsteğe bağlı. Tek bir IP adresi/bağlantı noktası numarası birleşimine sahip birden fazla siteyi, farklı ana bilgisayar adlarıyla barındırmanıza olanak tanıyan bir ana bilgisayar adı belirtir.|  

##  <a name="Startup"></a>Başlangıç  
`Startup` Öğesi, rol başlatıldığında çalışan bir görev koleksiyonu tanımlar. Bu öğe, `Variable` öğesinin üst öğesi olabilir. Rol başlangıç görevlerini kullanma hakkında daha fazla bilgi için bkz. [başlangıç görevlerini yapılandırma](cloud-services-startup-tasks.md). Bu öğe isteğe bağlıdır ve bir rol yalnızca bir başlangıç bloğuna sahip olabilir.

Aşağıdaki tabloda `Startup` öğesi özniteliği açıklanmaktadır.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|priority|int|Yalnızca iç kullanım içindir.|  

##  <a name="Task"></a>Görevinin  
`Task` Öğesi, rol başladığında gerçekleşen başlangıç görevini belirler. Başlangıç görevleri, rolü bu Install yazılım bileşenlerini çalıştırmak veya diğer uygulamaları çalıştırmak için hazırlayan görevleri gerçekleştirmek için kullanılabilir. Görevler, `Startup` öğe bloğunda göründükleri sırada yürütülür.

`Task` Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `Task` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|Komut satırı|dize|Gerekli. Çalıştırılacak komutları içeren bir komut dosyası (örneğin, CMD dosyası). Başlangıç komutu ve toplu iş dosyalarının ANSI biçiminde kaydedilmesi gerekir. Dosyanın başlangıcında bir bayt düzeni işaretçisi ayarlanan dosya biçimleri düzgün şekilde çalışmaz.|  
|executionContext|dize|Betiğin çalıştırıldığı bağlamı belirtir.<br /><br /> -   `limited`[Varsayılan] – işlemi barındıran rolle aynı ayrıcalıklarla çalıştırın.<br />-   `elevated`– Yönetici ayrıcalıklarıyla çalıştırın.|  
|taskType|dize|Komutun yürütme davranışını belirtir.<br /><br /> -   `simple`[Varsayılan] – sistem, diğer tüm görevler başlatılmadan önce görevin çıkmasını bekler.<br />-   `background`– Sistem, görevin çıkış gelmesini beklemez.<br />-   `foreground`– Arka plana benzer, ancak tüm ön plan görevleri çıkana kadar rol yeniden başlatılmaz.|  

##  <a name="Contents"></a>Dekiler  
`Contents` Öğesi bir Web rolü için içerik koleksiyonunu açıklar. Bu öğe, `Content` öğesinin üst öğesidir.

`Contents` Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="Content"></a>İçeriði  
`Content` Öğesi, Azure sanal makinesine kopyalanacak içeriğin kaynak konumunu ve kopyalandığı hedef yolu tanımlar.

`Content` Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `Content` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|destination|dize|Gerekli. Azure sanal makinesinde içeriğin yerleştirildiği konum. Bu konum **%Roleroot%\approot**klasörüne göredir.|  

Bu öğe, `SourceDirectory` öğesinin üst öğesidir.

##  <a name="SourceDirectory"></a>SourceDirectory  
`SourceDirectory` Öğesi, içeriğin kopyalandığı yerel dizini tanımlar. Azure sanal makinesine kopyalanacak yerel içeriği belirtmek için bu öğeyi kullanın.

`SourceDirectory` Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, `SourceDirectory` öğesinin özniteliklerini açıklar.

| Öznitelik | Type | Açıklama |  
| --------- | ---- | ----------- |  
|path|dize|Gerekli. İçerikleri Azure sanal makinesine kopyalanacak olan yerel bir dizinin göreli veya mutlak yolu. Dizin yolundaki ortam değişkenlerinin genişletilmesi desteklenir.|  
  
## <a name="see-also"></a>Ayrıca Bkz.
[Bulut hizmeti (klasik) Tanım Şeması](schema-csdef-file.md)
