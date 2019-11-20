---
title: Bulut hizmeti modeli ve paketi nedir? | Microsoft Docs
description: Azure 'da bulut hizmeti modelini (. csdef,. cscfg) ve paketi (. cspkg) açıklar
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: 47d031e339b3677e0bf6ddcbad9456041c53c6e2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359545"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Bulut hizmeti modeli nedir ve nasıl paketlarım?
Üç bileşenden bir bulut hizmeti, hizmet tanımı *(. csdef)* , hizmet yapılandırma *(. cscfg)* ve bir hizmet paketi *(. cspkg)* oluşturulur. Hem **ServiceDefinition. csdef** hem de **ServiceConfig. cscfg** dosyaları XML tabanlıdır ve bulut hizmetinin yapısını ve nasıl yapılandırıldığını açıklamaktadır; toplu olarak model olarak adlandırılır. **Servicepackage. cspkg** , **ServiceDefinition. csdef** öğesinden ve diğer şeyler arasında oluşturulan ve tüm gerekli ikili tabanlı bağımlılıkları içeren bir zip dosyasıdır. Azure, hem **Servicepackage. cspkg** hem de **ServiceConfig. cscfg**öğesinden bir bulut hizmeti oluşturur.

Bulut hizmeti Azure 'da çalışmaya başladıktan sonra **ServiceConfig. cscfg** dosyası aracılığıyla yapılandırabilirsiniz, ancak tanımı değiştiremezsiniz.

## <a name="what-would-you-like-to-know-more-about"></a>Hakkında daha fazla bilgi edinmek istiyor musunuz?
* [ServiceDefinition. csdef](#csdef) ve [ServiceConfig. cscfg](#cscfg) dosyaları hakkında daha fazla bilgi edinmek istiyorum.
* Bunu zaten öğrendim ve neleri yapılandıracağım hakkında [bazı örnekler](#next-steps) verin.
* [Servicepackage. cspkg](#cspkg)oluşturmak istiyorum.
* Visual Studio kullanıyorum ve şunu yapmak istiyorum...
  * [Bulut hizmeti oluşturma][vs_create]
  * [Mevcut bir bulut hizmetini yeniden yapılandırın][vs_reconfigure]
  * [Bulut hizmeti projesi dağıtma][vs_deploy]
  * [Bir bulut hizmeti örneğine uzak masaüstü][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition. csdef** dosyası, Azure tarafından bir bulut hizmetini yapılandırmak için kullanılan ayarları belirtir. [Azure hizmet Tanım Şeması (. csdef dosyası)](/previous-versions/azure/reference/ee758711(v=azure.100)) , bir hizmet tanım dosyası için izin verilen biçim sağlar. Aşağıdaki örnek, Web ve çalışan rolleri için tanımlanabilen ayarları gösterir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
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
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Burada kullanılan XML şemasını daha iyi anlamak için [hizmet tanımı şemasına](/previous-versions/azure/reference/ee758711(v=azure.100)) başvurabilirsiniz, ancak burada bazı öğelerin hızlı bir açıklaması verilmiştir:

**Barındıra**  
IıS7 içinde barındırılan Web siteleri veya Web uygulamaları için tanımları içerir.

**Inputendpoints**  
Bulut hizmetiyle iletişim kurmak için kullanılan uç noktalar için tanımları içerir.

**InternalEndpoints**  
Rol örnekleri tarafından birbirleriyle iletişim kurmak için kullanılan uç noktalar için tanımları içerir.

**ConfigurationSettings**  
Belirli bir rolün özellikleri için ayar tanımlarını içerir.

**Sertifikalar**  
Bir rol için gerekli olan sertifikalara yönelik tanımları içerir. Önceki kod örneğinde, Azure Connect yapılandırması için kullanılan bir sertifika gösterilmektedir.

**LocalResources**  
Yerel depolama kaynakları için tanımları içerir. Yerel depolama kaynağı, bir rolün bir örneğinin çalıştığı sanal makinenin dosya sistemindeki ayrılmış bir dizindir.

**İşlemlerinin**  
İçeri aktarılan modüller için tanımları içerir. Önceki kod örneğinde, Uzak Masaüstü Bağlantısı ve Azure Connect için modüller gösterilmektedir.

**Başlangıç**  
Rol başlatıldığında çalıştırılan görevleri içerir. Görevler bir. cmd veya yürütülebilir dosya içinde tanımlanır.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration. cscfg
Bulut hizmetinizin ayarlarının yapılandırması, **ServiceConfiguration. cscfg** dosyasındaki değerlere göre belirlenir. Bu dosyadaki her bir rol için dağıtmak istediğiniz örnek sayısını belirtirsiniz. Hizmet tanımı dosyasında tanımladığınız yapılandırma ayarlarının değerleri, hizmet yapılandırma dosyasına eklenir. Bulut hizmetiyle ilişkili tüm yönetim sertifikaları için parmak izleri dosyasına da eklenir. [Azure hizmet yapılandırma şeması (. cscfg dosyası)](/previous-versions/azure/reference/ee758710(v=azure.100)) , bir hizmet yapılandırma dosyası için izin verilen biçim sağlar.

Hizmet yapılandırma dosyası uygulamayla paketlenemez, ancak Azure 'a ayrı bir dosya olarak yüklenir ve bulut hizmetini yapılandırmak için kullanılır. Bulut hizmetinizi yeniden dağıtmaya gerek kalmadan yeni bir hizmet yapılandırma dosyasını karşıya yükleyebilirsiniz. Bulut hizmeti çalışırken bulut hizmeti yapılandırma değerleri değiştirilebilir. Aşağıdaki örnekte, Web ve çalışan rolleri için tanımlanabilen yapılandırma ayarları gösterilmektedir:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Burada kullanılan XML şemasını daha iyi anlamak için [hizmet yapılandırma şemasına](/previous-versions/azure/reference/ee758710(v=azure.100)) başvurabilirsiniz, ancak aşağıdaki öğelerin hızlı bir açıklaması aşağıda verilmiştir:

**Örnekler**  
Rol için çalışan örneklerin sayısını yapılandırır. Bulut hizmetinizin yükseltmeler sırasında kullanılamaz duruma gelmesine engel olmak için, Web 'e yönelik rollerinizin birden fazla örneğini dağıtmanız önerilir. Birden fazla örnek dağıtarak, bir hizmet için iki veya daha fazla rol örneği dağıtıldığında, Internet 'e yönelik rollere yönelik% 99,95 dış bağlantıyı garanti eden [Azure işlem hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/)yönergelerine bağlı olursunuz. .

**ConfigurationSettings**  
Bir rol için çalışan örneklerin ayarlarını yapılandırır. `<Setting>` Öğelerin adı, hizmet tanımı dosyasındaki ayar tanımlarına uymalıdır.

**Sertifikalar**  
Hizmeti tarafından kullanılan sertifikaları yapılandırır. Önceki kod örneğinde, RemoteAccess modülünün sertifikasının nasıl tanımlanacağı gösterilmektedir. *Parmak izi* özniteliğinin değeri, kullanılacak sertifikanın parmak izine ayarlanmalıdır.

<p/>

> [!NOTE]
> Sertifika için parmak izi, bir metin Düzenleyicisi kullanılarak yapılandırma dosyasına eklenebilir. Ya da değer, Visual Studio 'daki rolün **Özellikler** sayfasının **Sertifikalar** sekmesine eklenebilir.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Rol örnekleri için bağlantı noktaları tanımlama
Azure, bir Web rolüne yalnızca bir giriş noktası sağlar. Tüm trafiğin bir IP adresi üzerinden gerçekleştiği anlamına gelir. Web sitelerinizi, isteği doğru konuma yönlendirmek için konak üstbilgisini yapılandırarak bir bağlantı noktası paylaşacak şekilde yapılandırabilirsiniz. Uygulamalarınızı IP adresindeki iyi bilinen bağlantı noktalarını dinleyecek şekilde de yapılandırabilirsiniz.

Aşağıdaki örnekte bir Web rolü için Web sitesi ve Web uygulaması yapılandırması gösterilmektedir. Web sitesi, bağlantı noktası 80 ' de varsayılan giriş konumu olarak yapılandırılır ve Web uygulamaları, "mail.mysite.cloudapp.net" adlı alternatif bir konak üstbilgisinden istekleri alacak şekilde yapılandırılmıştır.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Rolün yapılandırmasını değiştirme
Hizmeti çevrimdışı yapmadan, Azure 'da çalışırken bulut hizmetinizin yapılandırmasını güncelleştirebilirsiniz. Yapılandırma bilgilerini değiştirmek için yeni bir yapılandırma dosyasını karşıya yükleyebilir veya yapılandırma dosyasını yerinde düzenleyip çalışan hizmetinize uygulayabilirsiniz. Bir hizmetin yapılandırmasında aşağıdaki değişiklikler yapılabilir:

* **Yapılandırma ayarlarının değerlerini değiştirme**  
  Bir yapılandırma ayarı değiştiğinde, örnek çevrimiçi olduğunda bir rol örneği değişikliği uygulayabilir ya da örneği düzgün şekilde geri dönüştürmek ve örnek çevrimdışıyken değişikliği uygulamak için seçim yapabilir.
* **Rol örneklerinin hizmet topolojisini değiştirme**  
  Bir örneğin kaldırılmakta olduğu durumlar dışında, topoloji değişiklikleri çalışan örnekleri etkilemez. Kalan tüm örneklerin genellikle geri dönüştürülmesi gerekmez; Ancak, bir topoloji değişikliğine yanıt olarak rol örneklerini geri dönüşüm seçebilirsiniz.
* **Sertifika parmak izini değiştirme**  
  Yalnızca bir rol örneği çevrimdışı olduğunda bir sertifikayı güncelleştirebilirsiniz. Bir rol örneği çevrimiçi olduğunda bir sertifika eklendiyse, silinirse veya değiştirilirse, Azure düzgün şekilde sertifikayı güncelleştirmek ve değişiklik tamamlandıktan sonra yeniden çevrimiçi duruma getirmek için örneği çevrimdışı duruma getirir.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Hizmet çalışma zamanı olaylarıyla yapılandırma değişikliklerini işleme
[Azure çalışma zamanı kitaplığı](/previous-versions/azure/reference/mt419365(v=azure.100)) , bir rolden Azure ortamıyla etkileşim kurmak için sınıflar sağlayan [Microsoft. WindowsAzure. ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) ad alanını içerir. [Roleenvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) sınıfı, bir yapılandırma değişikliğinden önce ve sonra oluşturulan aşağıdaki olayları tanımlar:

* **Olayı [değiştirme](/previous-versions/azure/reference/ee758134(v=azure.100))**  
  Bu durum, yapılandırma değişikliği bir rolün belirtilen örneğine uygulanmadan önce oluşur ve gerekirse rol örneklerini alma şansı verir.
* **[Değiştirilen](/previous-versions/azure/reference/ee758129(v=azure.100)) olay**  
  Yapılandırma değişikliği bir rolün belirtilen örneğine uygulandıktan sonra gerçekleşir.

> [!NOTE]
> Sertifika değişiklikleri her zaman bir rolün örneklerini çevrimdışı olarak alacak olduğundan, RoleEnvironment 'ı yükseltmez. veya RoleEnvironment. Changed olayları değiştiriliyor.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Azure 'da bir uygulamayı bulut hizmeti olarak dağıtmak için, önce uygulamayı uygun biçimde paketetmeniz gerekir. Paket dosyasını Visual Studio 'ya alternatif olarak oluşturmak için **CSPack** komut satırı aracını ( [Azure SDK](https://azure.microsoft.com/downloads/)ile birlikte yüklenir) kullanabilirsiniz.

**CSPack** , paketin içeriğini tanımlamak için hizmet tanım dosyasının ve hizmet yapılandırma dosyasının içeriğini kullanır. **CSPack** , [Azure Portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy)kullanarak Azure 'a yükleyebileceğiniz bir uygulama paketi dosyası (. cspkg) oluşturur. Varsayılan olarak, paket adlandırılır `[ServiceDefinitionFileName].cspkg`, ancak **CSPack** `/out` seçeneğini kullanarak farklı bir ad belirtebilirsiniz.

**CSPack** şurada bulunur:  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack. exe (Windows üzerinde), SDK ile birlikte yüklenen **Microsoft Azure komut istemi** kısayolunu çalıştırılarak kullanılabilir.  
> 
> Tüm olası anahtar ve komutlarla ilgili belgeleri görmek için CSPack. exe programını tek başına çalıştırın.
> 
> 

<p />

> [!TIP]
> Bulut hizmetinizi **Microsoft Azure Işlem öykünücüsünde**yerel olarak çalıştırın, **/CopyOnly** seçeneğini kullanın. Bu seçenek, uygulamanın ikili dosyalarını, işlem öykünücüsünde çalıştırılabilecekleri bir dizin düzenine kopyalar.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Bulut hizmetini paketlemek için örnek komut
Aşağıdaki örnek bir Web rolü için bilgi içeren bir uygulama paketi oluşturur. Komut kullanılacak hizmet tanımı dosyasını, ikili dosyaların bulunabileceği dizini ve paket dosyasının adını belirtir.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Uygulama hem bir Web rolü hem de bir çalışan rolü içeriyorsa, aşağıdaki komut kullanılır:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Değişkenlerin şu şekilde tanımlandığı yer:

| Değişken | Değer |
| --- | --- |
| \[DirectoryName\] |Azure projesinin. csdef dosyasını içeren kök proje dizini altındaki alt dizin. |
| \[ServiceDefinition\] |Hizmet tanım dosyasının adı. Varsayılan olarak, bu dosya ServiceDefinition. csdef olarak adlandırılır. |
| \[OutputFilename & lt\] |Oluşturulan paket dosyasının adı. Genellikle bu, uygulamanın adına ayarlanır. Dosya adı belirtilmemişse, uygulama paketi ApplicationName \[\]. cspkg olarak oluşturulur. |
| \[RoleName\] |Rolün hizmet tanım dosyasında tanımlanan adı. |
| \[RoleBinariesDirectory] |Rolün ikili dosyalarının konumu. |
| \[VirtualPath\] |Hizmet tanımının siteler bölümünde tanımlanan her bir sanal yol için fiziksel dizinler. |
| \[PhysicalPath\] |Hizmet tanımının site düğümünde tanımlanan her bir sanal yol için içeriğin fiziksel dizinleri. |
| \[RoleAssemblyName\] |Rol için ikili dosyanın adı. |

## <a name="next-steps"></a>Sonraki adımlar
Bir bulut hizmeti paketi oluşturuyor ve şunu yapmak istiyorum...

* [Bir bulut hizmeti örneği için Uzak Masaüstü kurma][remotedesktop]
* [Bulut hizmeti projesi dağıtma][deploy]

Visual Studio kullanıyorum ve şunu yapmak istiyorum...

* [Yeni bir bulut hizmeti oluşturun][vs_create]
* [Mevcut bir bulut hizmetini yeniden yapılandırın][vs_reconfigure]
* [Bulut hizmeti projesi dağıtma][vs_deploy]
* [Bir bulut hizmeti örneği için Uzak Masaüstü kurma][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
