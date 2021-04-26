---
title: Azure Cloud Services 'de başlangıç görevlerini çalıştırma (klasik) | Microsoft Docs
description: Başlangıç görevleri, bulut hizmeti ortamınızı uygulamanız için hazırlamaya yardımcı olur. Bu, başlangıç görevlerinin nasıl çalıştığını ve nasıl yapılacağını öğretir
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 25190075bdd13bd4b75dd82c97ee06ee60f4c26c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743194"
---
# <a name="how-to-configure-and-run-startup-tasks-for-an-azure-cloud-service-classic"></a>Azure bulut hizmeti (klasik) için başlangıç görevlerini yapılandırma ve çalıştırma

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bir rol başlamadan önce işlemleri gerçekleştirmek için başlangıç görevleri kullanabilirsiniz. Gerçekleştirmek isteyebileceğiniz işlemler, bir bileşeni yüklemeyi, COM bileşenlerini kaydetmeyi, kayıt defteri anahtarlarını ayarlamayı veya uzun süre çalışan bir işlemi başlatmayı içerir.

> [!NOTE]
> Başlangıç görevleri, sanal makinelere yalnızca bulut hizmeti Web ve çalışan rolleri için geçerli değildir.
> 
> 

## <a name="how-startup-tasks-work"></a>Başlangıç görevleri nasıl çalışır?
Başlangıç görevleri, rolleriniz başlamadan önce uygulanan ve [Başlangıç] öğesindeki [görev] öğesi kullanılarak [ServiceDefinition. csdef] dosyasında tanımlanan eylemlerdir. Genellikle başlangıç görevleri Batch dosyalarıdır, ancak konsol uygulamaları veya PowerShell betiklerini Başlatan toplu iş dosyaları da olabilirler.

Ortam değişkenleri bilgileri bir başlangıç görevine iletir ve yerel depolama alanı, bir başlangıç görevinin dışına bilgi geçirmek için kullanılabilir. Örneğin, bir ortam değişkeni yüklemek istediğiniz bir programın yolunu belirtebilir ve dosyalar daha sonra rolleriniz tarafından okunabilebileceği yerel depolamaya yazılabilir.

Başlangıç göreviniz, bilgileri ve hataları **Temp** ortam değişkeni tarafından belirtilen dizine kaydedebilir. Başlangıç görevi sırasında, **Temp** ortam değişkeni *C: \\ Resources \\ Temp \\ [GUID] olarak çözümlenir. [ roleName] bulutta çalışırken \\ roletemp* dizini.

Başlangıç görevleri, yeniden başlatmalar arasında birçok defa da yürütülebilir. Örneğin, her rol döngüsünde başlangıç görevi çalıştırılır ve rol döngüleri her zaman yeniden başlatma içermeyebilir. Başlangıç görevleri, sorunsuz bir şekilde birkaç kez çalışmasına izin verecek şekilde yazılmalıdır.

Başlatma işleminin tamamlanabilmesi için başlangıç görevlerinin bir **ERRORLEVEL** (veya çıkış kodu) ile bitmesi gerekir. Başlangıç görevi sıfır olmayan bir **ERRORLEVEL** ile bitiyorsa, rol başlatılmaz.

## <a name="role-startup-order"></a>Rol başlangıç sırası
Aşağıda, Azure 'daki rol başlatma yordamı listelenmektedir:

1. Örnek, **Başlangıç** olarak işaretlendi ve trafik almıyor.
2. Tüm başlangıç görevleri, **TaskType** özniteliğine göre yürütülür.
   
   * **Basit** görevler tek seferde birer zaman zaman yürütülür.
   * **Arka plan** ve **ön plan** görevleri, başlangıç görevine paralel olarak zaman uyumsuz olarak başlatılır.  
     
     > [!WARNING]
     > IIS başlangıç işlemindeki başlangıç görevi aşamasında tam olarak yapılandırılmamış olabilir, bu nedenle role özgü veriler kullanılamayabilir. Role özgü veriler gerektiren başlangıç görevlerinin [Microsoft. WindowsAzure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100))kullanması gerekir.
     > 
     > 
3. Rol ana bilgisayar işlemi başlatılır ve site IIS 'de oluşturulur.
4. [Microsoft. WindowsAzure. ServiceRuntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) yöntemi çağırılır.
5. Örnek, olarak **işaretlendi ve trafik** örneğe yönlendirilir.
6. [Microsoft. WindowsAzure. ServiceRuntime. RoleEntryPoint. Run](/previous-versions/azure/reference/ee772746(v=azure.100)) yöntemi çağırılır.

## <a name="example-of-a-startup-task"></a>Başlangıç görevi örneği
Başlangıç görevleri, **görev** öğesinde [ServiceDefinition. csdef] dosyasında tanımlanır. **CommandLine** özniteliği, başlangıç toplu işlem dosyası veya konsol komutunun adını ve parametrelerini belirtir, **ExecutionContext** özniteliği başlangıç görevinin ayrıcalık düzeyini belirtir ve **TaskType** özniteliği, görevin nasıl yürütüleceğini belirtir.

Bu örnekte, başlangıç görevi için **Myversionnumber adlı** bir ortam değişkeni oluşturulur ve "**1.0.0.0**" değerine ayarlanır.

**ServiceDefinition. csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

Aşağıdaki örnekte, **Startup. cmd** toplu iş dosyası, "geçerli sürüm 1.0.0.0" satırını temp ortam değişkeni tarafından belirtilen dizindeki StartupLog.txt dosyasına yazar. `EXIT /B 0`Satır, başlangıç görevinin sıfır **ERRORLEVEL** ile sona ermesini sağlar.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> Visual Studio 'da, başlangıç toplu iş dosyanızın Azure 'da projenize doğru şekilde dağıtıldığından emin olmak için başlangıç toplu iş dosyanız için **Çıkış Dizinine Kopyala** özelliği **her zaman Kopyala** olarak ayarlanmalıdır. (Web rolleri için **AppRoot \\ bin** **ve çalışan rolleri için AppRoot** ).
> 
> 

## <a name="description-of-task-attributes"></a>Görev özniteliklerinin açıklaması
Aşağıdaki, [ServiceDefinition. csdef] dosyasındaki **görev** öğesinin özniteliklerini açıklar:

**commandLine** -başlangıç görevinin komut satırını belirtir:

* Komutu, başlangıç görevini başlatan isteğe bağlı komut satırı parametreleriyle.
* Bu, genellikle bir. cmd veya. bat toplu iş dosyasının dosya adıdır.
* Görev, \\ dağıtım Için AppRoot bin klasörüne görelidir. Ortam değişkenleri, görevin yolunu ve dosyasını belirlemede genişletilmez. Ortam genişletmesi gerekliyse, başlangıç görevinizi çağıran küçük bir. cmd betiği oluşturabilirsiniz.
* Bir konsol uygulaması veya [PowerShell betiği](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)Başlatan bir toplu iş dosyası olabilir.

**ExecutionContext** -başlangıç görevinin ayrıcalık düzeyini belirtir. Ayrıcalık düzeyi sınırlı olabilir veya yükseltilebilir:

* **az**  
  Başlangıç görevi, rolüyle aynı ayrıcalıklarla çalışır. [Çalışma zamanı] öğesi için **ExecutionContext** özniteliği de **sınırlıysa**, kullanıcı ayrıcalıkları kullanılır.
* **kaynağa**  
  Başlangıç görevi, yönetici ayrıcalıklarıyla çalışır. Bu, başlangıç görevlerinin programları yüklemesine, IIS yapılandırma değişiklikleri yapmasına, kayıt defteri değişiklikleri yapmasına ve rolün kendisi için ayrıcalık düzeyini arttırmadan diğer yönetici düzeyindeki görevlere izin verir.  

> [!NOTE]
> Bir başlangıç görevinin ayrıcalık düzeyinin, rolün kendisi ile aynı olması gerekmez.
> 
> 

**TaskType** -bir başlangıç görevinin yürütülme şeklini belirtir.

* **MPLE**  
  Görevler, [ServiceDefinition. csdef] dosyasında belirtilen sırada birer birer zaman uyumlu olarak yürütülür. Bir **basit** başlangıç görevi sıfır **ERRORLEVEL** ile sona erdiğinde, bir sonraki **basit** başlangıç görevi yürütülür. Yürütülecek daha **basit** başlangıç görevi yoksa, rol kendisi başlatılır.   
  
  > [!NOTE]
  > **Basit** görev sıfır olmayan bir **ERRORLEVEL** ile bitiyorsa, örnek engellenir. Sonraki **basit** başlangıç görevleri ve rol kendisi başlatılmaz.
  > 
  > 
  
    Toplu iş dosyanızın sıfır **ERRORLEVEL** ile bitdiğinden emin olmak için, `EXIT /B 0` Batch dosyası işleminizin sonunda komutunu yürütün.
* **background**  
  Görevler, rolün başlangıcında paralel olarak zaman uyumsuz olarak yürütülür.
* **ön plan**  
  Görevler, rolün başlangıcında paralel olarak zaman uyumsuz olarak yürütülür. **Ön** plan ve **arka plan** görevi arasındaki önemli fark, bir ön plan görevinin, görev sonlanana kadar rolün geri dönüşümünü veya kapatılmasını önlediği bir **ön plana** sahip olur. **Arka plan** görevlerinde Bu kısıtlama yoktur.

## <a name="environment-variables"></a>Ortam değişkenleri
Ortam değişkenleri, bir başlangıç görevine bilgi geçirmek için bir yoldur. Örneğin, yüklenecek bir programı içeren bir Blobun yolunu ya da rolünüzün kullanacağı bağlantı noktası numaralarını veya başlangıç görevinin özelliklerini denetlemek için ayarları ekleyebilirsiniz.

Başlangıç görevleri için iki tür ortam değişkeni vardır; [roleenvironment] sınıfının üyelerine dayanan statik ortam değişkenleri ve ortam değişkenleri. Her ikisi de [ServiceDefinition. csdef] dosyasının [ortam] bölümünde bulunur ve her ikisi de [değişken] öğesi ve **ad** özniteliğini kullanır.

Statik ortam değişkenleri, [değişken] öğesinin **değer** özniteliğini kullanır. Yukarıdaki örnekte, "**1.0.0.0**" statik değeri olan **myversionnumber** ortam değişkeni oluşturulur. Diğer bir örnek, **stagingorproduction** ortam değişkeninin değerine göre farklı başlangıç eylemleri gerçekleştirmek üzere "**hazırlama**" veya "**Üretim**" değerlerine el Ile ayarlayabileceğiniz bir **stagingorproduction** ortam değişkeni oluşturmak olacaktır.

RoleEnvironment sınıfının üyelerini temel alan ortam değişkenleri, [değişken] öğesinin **Value** özniteliğini kullanmaz. Bunun yerine, [Roleınstancevalue] alt öğesi uygun **XPath** özniteliği değeri olan [roleenvironment] sınıfının belirli bir üyesini temel alan bir ortam değişkeni oluşturmak için kullanılır. Çeşitli [Roleenvironment] değerlerine erişim için **XPath** özniteliğinin değerleri [burada](cloud-services-role-config-xpath.md)bulunabilir.

Örneğin, örnek işlem öykünücüsünde çalışırken "**true**" ve bulutta çalışırken "**false**" olan bir ortam değişkeni oluşturmak Için aşağıdaki [değişkeni] ve [roleınstancevalue] öğelerini kullanın:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Sonraki adımlar
Bulut hizmetinizdeki bazı [yaygın başlangıç görevlerini](cloud-services-startup-tasks-common.md) nasıl gerçekleştireceğinizi öğrenin.

Bulut hizmetinizi [paketleyin](cloud-services-model-and-package.md) .  

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Görev]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Başlangıç]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Çalışma Zamanı]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Ortam]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Değişken]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[Roleınstancevalue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)