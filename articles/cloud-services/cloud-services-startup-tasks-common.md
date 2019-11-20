---
title: Cloud Services için genel başlangıç görevleri | Microsoft Docs
description: ', Bulut Hizmetleri Web rolünüzde veya çalışan rolünde gerçekleştirmek isteyebileceğiniz ortak başlangıç görevlerinin bazı örneklerini sağlar.'
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 2eb299ad841444a3100eac207b225d5377959f85
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358960"
---
# <a name="common-cloud-service-startup-tasks"></a>Ortak bulut hizmeti başlangıç görevleri
Bu makalede, bulut hizmetinizde gerçekleştirmek isteyebileceğiniz yaygın başlangıç görevlerinin bazı örnekleri verilmiştir. Bir rol başlamadan önce işlemleri gerçekleştirmek için başlangıç görevleri kullanabilirsiniz. Gerçekleştirmek isteyebileceğiniz işlemler, bir bileşeni yüklemeyi, COM bileşenlerini kaydetmeyi, kayıt defteri anahtarlarını ayarlamayı veya uzun süre çalışan bir işlemi başlatmayı içerir. 

Başlangıç görevlerinin nasıl çalıştığını ve özellikle de bir başlangıç görevini tanımlayan girişlerin nasıl oluşturulacağını anlamak için [Bu makaleye](cloud-services-startup-tasks.md) bakın.

> [!NOTE]
> Başlangıç görevleri, sanal makinelere yalnızca bulut hizmeti Web ve çalışan rolleri için geçerli değildir.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Bir rol başlamadan önce ortam değişkenlerini tanımlayın
Belirli bir görev için tanımlanmış ortam değişkenlerine ihtiyacınız varsa, [görev] öğesi içindeki [Ortam] öğesini kullanın.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Değişkenler, dağıtım hakkındaki bir şeye başvurmak için [geçerli bir Azure XPath değeri](cloud-services-role-config-xpath.md) de kullanabilir. `value` Özniteliğini kullanmak yerine bir [roleınstancevalue] alt öğesi tanımlayın.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>IIS başlangıcını AppCmd. exe ile yapılandırma
[Appcmd. exe](https://technet.microsoft.com/library/jj635852.aspx) komut satırı aracı, Azure 'DA başlangıçta IIS ayarlarını yönetmek için kullanılabilir. *Appcmd. exe* , Azure 'da başlangıç görevlerinde kullanmak için yapılandırma ayarlarına uygun, komut satırı erişimi sağlar. Web sitesi ayarları, *Appcmd. exe*' yi kullanarak, uygulamalar ve siteler için eklenebilir, değiştirilebilir veya kaldırılabilir.

Bununla birlikte, bir başlangıç görevi olarak *Appcmd. exe* ' nin kullanımı için birkaç şey izlemeniz gerekir:

* Başlangıç görevleri, yeniden başlatmalar arasında birden çok kez çalıştırılabilir. Örneğin, bir rol geri dönüştürüldüğünde.
* *Appcmd. exe* eylemi birden çok kez gerçekleştirilirse bir hata oluşturabilir. Örneğin, bir bölümü *Web. config* 'e iki kez eklemeye çalışmak bir hata oluşturabilir.
* Başlangıç görevleri sıfır olmayan bir çıkış kodu veya **ERRORLEVEL**döndürmeleri durumunda başarısız olur. Örneğin, *Appcmd. exe* bir hata oluşturduğunda.

Appcmd. exe ' ye yapılan çağrıyı bir  *. cmd* dosyasıyla sarıyorsanız, bu sayede, bu işlemi kolay bir şekilde kontrol etmeniz iyi bir  uygulamadır. Bilinen bir **ERRORLEVEL** yanıtı tespit ederseniz, bunu yoksayabilirsiniz veya geri geçirebilirsiniz.

*Appcmd. exe* tarafından döndürülen ERRORLEVEL, Winerror. h dosyasında listelenir ve [MSDN](/windows/desktop/Debug/system-error-codes--0-499-)'de de görülebilir.

### <a name="example-of-managing-the-error-level"></a>Hata düzeyini yönetme örneği
Bu örnek, hata işleme ve günlüğe kaydetme ile JSON için bir sıkıştırma bölümü ve *Web. config* dosyasına bir sıkıştırma girişi ekler.

[ServiceDefinition.csdef] dosyasının ilgili bölümleri burada gösterilmektedir ve bu sayede, `elevated`  [](/previous-versions/azure/reference/gg557552(v=azure.100)#task) *Appcmd. exe ' deki ayarları değiştirmek için yeterli izinleri vermek için ExecutionContext özniteliği Web. config* dosyası:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

*Startup. cmd* toplu iş dosyası, *Web. config* dosyasına BIR sıkıştırma bölümü ve JSON için bir sıkıştırma girdisi eklemek için *Appcmd. exe* ' yi kullanır. VERIFY 183 için beklenen **ERRORLEVEL** , VERIFY kullanılarak sıfır olarak ayarlanmıştır. EXE komut satırı programı. Beklenmeyen errorlevels, StartupErrorLog. txt dosyasına kaydedilir.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Güvenlik duvarı kuralları ekleme
Azure 'da, etkin olarak iki güvenlik duvarı vardır. İlk güvenlik duvarı, sanal makine ve dış dünya arasındaki bağlantıları denetler. Bu güvenlik duvarı, [ServiceDefinition.csdef] dosyasındaki [Noktalarının] öğesi tarafından denetlenir.

İkinci güvenlik duvarı, sanal makine ile bu sanal makine içindeki süreçler arasındaki bağlantıları denetler. Bu güvenlik duvarı `netsh advfirewall firewall` komut satırı aracı tarafından denetlenebilir.

Azure, rollerinizde başlatılan işlemlere yönelik güvenlik duvarı kuralları oluşturur. Örneğin, bir hizmet veya program başlattığınızda, Azure bu hizmetin Internet ile iletişim kurmasına izin vermek için gerekli güvenlik duvarı kurallarını otomatik olarak oluşturur. Ancak, rolünüzün dışındaki bir işlem (COM+ hizmeti veya Windows zamanlanmış görevi gibi) tarafından başlatılan bir hizmet oluşturursanız, bu hizmete erişime izin vermek için el ile bir güvenlik duvarı kuralı oluşturmanız gerekir. Bu güvenlik duvarı kuralları, bir başlangıç görevi kullanılarak oluşturulabilir.

Bir güvenlik duvarı kuralı oluşturan bir başlangıç görevinin bir [ExecutionContext][görev] **yükseltilmiş**olmalıdır. Aşağıdaki başlangıç görevini [ServiceDefinition.csdef] dosyasına ekleyin.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Güvenlik duvarı kuralını eklemek için, başlangıç toplu iş dosyanızda uygun `netsh advfirewall firewall` komutları kullanmanız gerekir. Bu örnekte, başlangıç görevi TCP bağlantı noktası 80 için güvenlik ve şifreleme gerektirir.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Belirli bir IP adresini engelle
IIS **Web. config** dosyanızı değiştirerek, belırlı bir IP adresi kümesine yönelik bir Azure Web rolü erişimini kısıtlayabilirsiniz. Ayrıca **ApplicationHost. config** dosyasının **ipsecurity** bölümünün kilidini açarak bir komut dosyası kullanmanız gerekir.

**ApplicationHost. config** dosyasının **ipsecurity** bölümünün kilidini açmak için, rol başlangıcında çalışan bir komut dosyası oluşturun. **Başlangıç** olarak adlandırılan Web rolünüzün kök düzeyinde bir klasör oluşturun ve bu klasör içinde, **Startup. cmd**adlı bir toplu iş dosyası oluşturun. Bu dosyayı Visual Studio projenize ekleyin ve paketinize eklendiğinden emin olmak için özellikleri **her zaman Kopyala** olarak ayarlayın.

Aşağıdaki başlangıç görevini [ServiceDefinition.csdef] dosyasına ekleyin.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Bu komutu **Startup. cmd** dosyasına ekleyin:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Bu görev, Web rolü her başlatıldığında **Startup. cmd** toplu iş dosyasının çalıştırılmasını sağlar ve gerekli IPI bölümünün kilidinin açık olmasını sağlar  .

Son olarak, aşağıdaki örnekte gösterildiği gibi, erişim verilen IP adreslerinin bir listesini eklemek için Web rolünüzün **Web. config** dosyasındaki [System. webserver bölümünü](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) değiştirin:

Bu örnek yapılandırma, her bir IP 'nin sunucuya her iki tanımlı dışında erişmesine **izin verir**

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Bu örnek yapılandırma, tüm IP 'Lerin sunucuya erişimini **engeller** , ikisi de tanımlanır.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>PowerShell başlangıç görevi oluşturma
Windows PowerShell betikleri doğrudan [ServiceDefinition.csdef] dosyasından çağrılamaz, ancak bir başlangıç toplu iş dosyası içinden çağrılabilir.

PowerShell (varsayılan olarak) imzasız betikleri çalıştırmaz. Komut dosyanızı imzalayamadığınız takdirde, PowerShell 'i imzasız betikleri çalıştıracak şekilde yapılandırmanız gerekir. İmzasız betikleri çalıştırmak için, **ExecutionPolicy** **Kısıtlamasız**olarak ayarlanmalıdır. Kullandığınız **ExecutionPolicy** ayarı Windows PowerShell sürümünü temel alır.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

PowerShell 2,0 veya 1,0 çalıştıran bir konuk işletim sistemi kullanıyorsanız, sürüm 2 ' yi çalıştırmaya zorlayabilir ve kullanılamıyorsa sürüm 1 ' i kullanın.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Yerel depolamada bir başlangıç görevinden dosyalar oluşturma
Uygulamanız tarafından daha sonra erişilen başlangıç göreviniz tarafından oluşturulan dosyaları depolamak için yerel bir depolama kaynağı kullanabilirsiniz.

Yerel depolama kaynağını oluşturmak için, [ServiceDefinition.csdef] dosyasına bir [localresources] bölümü ekleyin ve ardından [localStorage] alt öğesini ekleyin. Yerel depolama kaynağına, başlangıç göreviniz için benzersiz bir ad ve uygun bir boyut verin.

Başlangıç görevinde yerel bir depolama kaynağı kullanmak için, yerel depolama kaynağı konumuna başvurmak üzere bir ortam değişkeni oluşturmanız gerekir. Ardından, başlangıç görevi ve uygulama dosyaları yerel depolama kaynağına okuyabilir ve yazabilir.

**ServiceDefinition. csdef** dosyasının ilgili bölümleri burada gösterilmektedir:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Örnek olarak, bu **Başlangıç. cmd** toplu iş dosyası yerel depolama konumunda **MyTest. txt** dosyasını oluşturmak için **pathtostartupstorage** ortam değişkenini kullanır.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

[Getlocalresource](/previous-versions/azure/reference/ee772845(v=azure.100)) yöntemini kullanarak Azure SDK 'dan yerel depolama klasörüne erişebilirsiniz.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Öykünücü veya bulutta Çalıştır
Başlangıç göreviniz, bulut üzerinde çalışırken, işlem öykünücüsünde olduğu zaman ile karşılaştırıldığında farklı adımlar gerçekleştirmenizi sağlayabilir. Örneğin, SQL verilerinizin yeni bir kopyasını yalnızca öykünücüsünde çalışırken kullanmak isteyebilirsiniz. Ya da bulut için, öykünücüsünde çalışırken yapmanız gerekmeyen bazı performans iyileştirmeleri yapmak isteyebilirsiniz.

Bu işlem öykünücüsü ve bulutu üzerinde farklı eylemler gerçekleştirebilme özelliği, [ServiceDefinition.csdef] dosyasında bir ortam değişkeni oluşturularak gerçekleştirilebilir. Daha sonra bu ortam değişkenini başlangıç görevinizdeki bir değer için test edersiniz.

Ortam değişkenini oluşturmak için,[roleınstancevalue] öğesi `/RoleEnvironment/Deployment/@emulated` [Değişken]/ekleyin ve bir XPath değeri oluşturun. **% ComputeEmulatorRunning%** ortam değişkeninin `true` değeri, işlem öykünücüsünde ve `false` bulutta çalışırken çalışır.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Görev artık **% ComputeEmulatorRunning%** ortam değişkenini, rolün bulutta mı yoksa öykünücü üzerinde mi çalıştığını temel alarak farklı eylemler gerçekleştirecek şekilde denetleyebilir. İşte bu ortam değişkenini denetleyen bir. cmd kabuk betiği.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Görevin zaten çalıştırıldığını Algıla
Bir yeniden başlatma olmadan, başlangıç görevlerinizin yeniden çalışmasına neden olan rol geri dönüşüm edebilir. Barındırma VM 'sinde bir görevin zaten çalıştırıldığını belirten bir bayrak yoktur. Birden çok kez çalıştırıldıklarından bağımsız olarak bazı görevleriniz olabilir. Ancak, bir görevin birden çok kez çalışmasını engellemeniz gereken bir durumla karşılaşabilirsiniz.

Bir görevin zaten çalıştırılmış olduğunu algılamamanın en kolay yolu, görev başarılı olduğunda **% Temp%** klasöründe bir dosya oluşturmak ve görevin başlangıcında bunu aramak için kullanılır. İşte bunu yapan örnek bir CMD Shell betiği.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>En iyi görev uygulamaları
Web veya çalışan rolünüzün görevini yapılandırırken izlemeniz gereken bazı en iyi uygulamalar aşağıda verilmiştir.

### <a name="always-log-startup-activities"></a>Başlangıç etkinliklerini her zaman günlüğe kaydet
Visual Studio, toplu iş dosyaları arasında adım adım bir hata ayıklayıcı sağlamaz, bu yüzden toplu iş dosyaları işleminde mümkün olduğunca çok veri almanız iyi bir işlemdir. **Stdout** ve **stderr**toplu iş dosyalarının çıkışını günlüğe kaydetmek, toplu iş dosyalarını ayıklamaya ve gidermeye çalışırken önemli bilgiler verebilir. **Stdout** ve **stderr** ' i **% Temp%** ortam değişkeni tarafından işaret edilen dizindeki startuplog. txt dosyasına kaydetmek için, metni `>>  "%TEMP%\\StartupLog.txt" 2>&1` günlüğe kaydetmek istediğiniz belirli satırların sonuna ekleyin. Örneğin, **% PathToApp1Install%** dizininde Setup. exe dosyasını yürütmek için:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

XML 'nizi basitleştirmek için, tüm başlangıç görevlerinizi günlük kaydıyla birlikte çağıran bir sarmalayıcı *cmd* dosyası oluşturabilir ve her bir alt görevin aynı ortam değişkenlerini paylarını sağlayabilirsiniz.

Her bir başlangıç görevinin sonunda kullanımı `>> "%TEMP%\StartupLog.txt" 2>&1` sinir bozucu olabilir. Günlüğe kaydetmeyi işleyen bir sarmalayıcı oluşturarak görev günlüğünü uygulayabilirsiniz. Bu sarmalayıcı, çalıştırmak istediğiniz gerçek toplu iş dosyasını çağırır. Hedef toplu iş dosyasındaki tüm çıktılar *Startuplog. txt* dosyasına yönlendirilir.

Aşağıdaki örnekte, tüm çıktının bir başlangıç toplu iş dosyasından nasıl yeniden yönlendirileceği gösterilmektedir. Bu örnekte, ServerDefinition. csdef dosyası *logwrap. cmd*' yi çağıran bir başlangıç görevi oluşturur. *logwrap. cmd* , tüm çıktıyı **% Temp%\\startuplog. txt**dosyasına yönlendirerek *Startup2. cmd*' yi çağırır.

ServiceDefinition. cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap. cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2. cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

**Startuplog. txt** dosyasındaki örnek çıktı:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> **Startuplog. txt** dosyası *\\c:\resources\temp {rol tanımlayıcısı} \roletemp* klasöründe bulunur.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>ExecutionContext 'i başlangıç görevleri için uygun şekilde ayarlama
Başlangıç göreviyle ilgili ayrıcalıkları uygun şekilde ayarlayın. Bazen, rol normal ayrıcalıklarla çalıştırılsa bile başlangıç görevlerinin yükseltilmiş ayrıcalıklarla çalışması gerekir.

[ExecutionContext][görev] özniteliği başlangıç görevinin ayrıcalık düzeyini ayarlar. Kullanımı `executionContext="limited"` , başlangıç görevinin rolle aynı ayrıcalık düzeyine sahip olduğu anlamına gelir. Kullanımı `executionContext="elevated"` , başlangıç görevinin yönetici ayrıcalıklarına sahip olduğu anlamına gelir ve bu, başlangıç görevinin rolünüze yönetici ayrıcalıkları vermeden yönetici görevleri gerçekleştirmesini sağlar.

Yükseltilmiş ayrıcalıklar gerektiren bir başlangıç görevi örneği, IIS 'yi yapılandırmak için **Appcmd. exe** ' yi kullanan bir başlangıç görevidir. **Appcmd. exe** gerekli `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Uygun taskType 'ı kullanın
[TaskType][görev] özniteliği başlangıç görevinin yürütülme şeklini belirler. Üç değer vardır: **basit**, **arka plan**ve **ön plan**. Arka plan ve ön plan görevleri zaman uyumsuz olarak başlatılır ve basit görevler tek seferde bir kez yürütülür.

**Basit** başlangıç görevlerinde, görevlerin ServiceDefinition. csdef dosyasında listelenme sırasına göre çalıştırılacağı sırayı ayarlayabilirsiniz. **Basit** bir görev sıfır dışında bir çıkış kodu ile bitiyorsa, başlangıç yordamı duraklar ve rol başlatılmaz.

**Arka plan** başlangıç görevleri ve **ön plan** başlangıç görevleri arasındaki fark, ön **plan görevlerinin,** **ön plan** görevi sona erene kadar rolün çalışır durumda tutulması gerektiğidir. Bu Ayrıca, **ön** plan görevi askıda kalırsa veya kilitlenirse, **ön plan** görevi zorlayarak kapanana kadar rolün geri dönüştürülmeyeceği anlamına gelir. Bu nedenle, **ön** plan görevinin özelliğinin gerekli olmadığı durumlar dışında, zaman uyumsuz başlangıç görevleri için **arka plan** görevleri önerilir.

### <a name="end-batch-files-with-exit-b-0"></a>Batch dosyalarını şu çıkış ile sonlandır/B 0
Rol yalnızca basit başlangıç görevinizden gelen **ERRORLEVEL** sıfır ise başlatılır. Tüm Programlar **ERRORLEVEL** (çıkış kodu) doğru olarak ayarlanmadı, bu nedenle toplu iş dosyası her şey doğru şekilde `EXIT /B 0` çalıştırıldıysa bir ile bitmelidir.

Başlatma toplu `EXIT /B 0` iş dosyasının sonunda eksik olan bir rol, yaygın olarak kullanılan bir nedendir.

> [!NOTE]
> İç içe geçmiş toplu iş dosyalarının, `/B` parametresini kullanırken bazen askıda olduğunu fark ettim. [Günlük sarmalayıcı](#always-log-startup-activities)kullandığınızda olduğu gibi, başka bir Batch dosyası geçerli toplu iş dosyanızı çağırırsa, bu askıda kalma sorununun gerçekleşmediğinden emin olmak isteyebilirsiniz. Bu durumda `/B` parametreyi atlayabilirsiniz.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Başlangıç görevlerinin birden çok kez çalışmasını bekliyor
Tüm rol geri dönüştürme işlemi yeniden önyükleme içermez, ancak tüm rol geri dönüştürme işlemleri tüm başlangıç görevlerini çalıştırmayı içerir. Bu, başlangıç görevlerinin herhangi bir sorun olmadan yeniden başlatmalar arasında birden çok kez çalıştırabilmesi gerektiği anlamına gelir. Bu, [önceki bölümde](#detect-that-your-task-has-already-run)ele alınmıştır.

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Rolde erişilmesi gereken dosyaları depolamak için yerel depolamayı kullanın
Başlangıç göreviniz sırasında rolünüz tarafından erişilebilen bir dosya kopyalamak veya oluşturmak istiyorsanız bu dosyanın yerel depolama alanına yerleştirilmesi gerekir. [Yukarıdaki bölüme](#create-files-in-local-storage-from-a-startup-task)bakın.

## <a name="next-steps"></a>Sonraki adımlar
Bulut [Hizmeti modelini ve paketini](cloud-services-model-and-package.md) gözden geçirin

[Görevlerin](cloud-services-startup-tasks.md) nasıl çalıştığı hakkında daha fazla bilgi edinin.

Bulut hizmeti paketinizi [oluşturun ve dağıtın](cloud-services-how-to-create-deploy-portal.md) .

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Görev]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ortam]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Değişken]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[Roleınstancevalue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Noktalarının]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[Roleınstancevalue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
