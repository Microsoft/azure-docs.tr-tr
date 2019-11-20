---
title: Python ve Azure Cloud Services kullanmaya başlama | Microsoft Belgeleri
description: Web rolleri ve çalışan rolleri dahil olmak üzere Azure Cloud Services oluşturmak üzere Visual Studio için Python Araçları’nı kullanma hakkında genel bilgi edinin.
services: cloud-services
documentationcenter: python
author: georgewallace
ms.service: cloud-services
ms.devlang: python
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 981b1cc4a7adb98ba68ebf3a7673b7116479e704
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68359561"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Visual Studio için Python web ve çalışan rolleri içeren Python Araçları

Bu makalede, [Visual Studio için Python Araçları][Python Tools for Visual Studio] ile Python web ve çalışan rollerini kullanmaya genel bir bakış sunulmuştur. Visual Studio’yu kullanarak Python kullanan temel bir Bulut Hizmetinin nasıl oluşturulup dağıtılacağını öğrenin.

## <a name="prerequisites"></a>Önkoşullar
* [Visual Studio 2013, 2015 veya 2017](https://www.visualstudio.com/)
* [Visual Studio için Python Araçları][Python Tools for Visual Studio] (PTVS)
* [VS 2013 Için Azure SDK Tools][Azure SDK Tools for VS 2013] veya  
[VS 2015 Için Azure SDK Tools][Azure SDK Tools for VS 2015] veya  
[VS 2017 için Azure SDK Tools][Azure SDK Tools for VS 2017]
* [Python 2.7 32 bit][Python 2.7 32-bit] veya [Python 3.5 32 bit][Python 3.5 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Python web ve çalışan rolleri nelerdir?
Azure, uygulamaları çalıştırmak için üç bilgi işlem modeli sağlar: Azure App Service, [Azure sanal makineler][execution model-vms]ve [Azure Cloud Services][execution model-cloud services] [Web Apps özelliği][execution model-web sites]. Python bu üç modeli de destekler. Web ve çalışan rolleri içeren Cloud Services *Hizmet Olarak Platform (PaaS)* sunar. Web rolü, bir bulut hizmetinde ön uç web uygulamalarını barındırmak için özel Internet Information Services (IIS) web sunucusu sağlar. Çalışan rolü ise kullanıcı etkileşimi ve girişinden bağımsız zaman uyumsuz, uzun çalışan ve kalıcı görevleri çalıştırabilir.

Daha fazla bilgi için bkz. [Bulut Hizmeti nedir?].

> [!NOTE]
> *Basit bir web sitesi mi oluşturmak istiyorsunuz?*
> Senaryonuz yalnızca basit bir web sitesi ön ucu içeriyorsa, Azure Uygulama Hizmeti’ndeki basit Web Apps özelliğini kullanmayı düşünün. Web siteniz büyüdükçe ve gereksinimleriniz değiştikçe kolayca Bulut Hizmetleri’ne yükseltebilirsiniz. Azure App Service’teki Web Apps özelliğini geliştirme hakkındaki makaleler için [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/)’ne bakın.
> <br />
> 
> 

## <a name="project-creation"></a>Proje oluşturma
Visual Studio’da, **Python** altındaki **Yeni Proje** iletişim kutusunda **Azure Bulut Hizmeti**’ni seçebilirsiniz.

![Yeni Proje İletişim Kutusu](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Azure Bulut Hizmeti sihirbazında, yeni web ve çalışan rolleri oluşturabilirsiniz.

![Azure Bulut Hizmeti İletişim Kutusu](./media/cloud-services-python-ptvs/new-service-wizard.png)

Çalışan rolü şablonu, Azure depolama hesabına veya Azure Service Bus’a bağlamak üzere demirbaş kod ile birlikte gelir.

![Bulut Hizmeti Çözümü](./media/cloud-services-python-ptvs/worker.png)

Herhangi bir zamanda mevcut bulut hizmetine web veya çalışan rolleri ekleyebilirsiniz.  Çözümünüze ister mevcut projeleri ekleyin, ister yeni projeler oluşturun.

![Rol Komutu Ekleme](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Bulut hizmetiniz farklı dillerde uygulanan roller içerebilir.  Örneğin, Python veya C# çalışan rolleri ile Django kullanılarak uygulanan bir Python web rolünüz olabilir.  Service Bus kuyruklarını veya depolama kuyruklarını kullanarak rolleriniz arasında kolaya iletişim kurabilirsiniz.

## <a name="install-python-on-the-cloud-service"></a>Bulut hizmetine Python yükleme
> [!WARNING]
> Visual Studio ile yüklenen ayar betikleri (bu makalenin son güncelleştirildiği tarihte) çalışmamaktadır. Bu bölümde geçici bir çözüm açıklanmaktadır.
> 
> 

Ayar betikleri ile ilgili temel sorun python yüklememesidir. İlk olarak, [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) dosyasında iki [başlangıç görevi](cloud-services-startup-tasks.md) tanımlayın. İlk görev (**PrepPython.ps1**) Python çalışma zamanını indirir ve yükler. İkinci görev (**PipInstaller.ps1**) sahip olabileceğiniz tüm bağımlılıkları yüklemek üzere pip çalıştırır.

Aşağıdaki betikler Python 3.5 hedeflenerek yazılmıştır. Python 2.x sürümünü kullanmak istiyorsanız **PYTHON2** değişken dosyasını iki başlangıç görevi ve `<Variable name="PYTHON2" value="<mark>on</mark>" />` çalışma zamanı görevi için **açık** olarak ayarlayın.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

**PYTHON2** ve **PYPATH** değişkenlerinin çalışan başlangıç görevine eklenmesi gerekir. **PYPATH** değişkeni yalnızca **PYTHON2** değişkeni **açık** olarak ayarlandığında kullanılır.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Örnek ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Ardından, rolünüzün **./bin** klasöründe **PrepPython.ps1** ve **PipInstaller.ps1** dosyalarını oluşturun.

#### <a name="preppythonps1"></a>PrepPython.ps1
Bu betik python yükler. **PYTHON2** ortam değişkeni **açık** olarak ayarlanırsa Python 2.7 yüklenir, aksi takdirde Python 3.5 yüklenir.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Bu betik pip çağırır ve tüm bağımlılıkları **requirements.txt** dosyasına yükler. **PYTHON2** ortam değişkeni **açık** olarak ayarlanırsa Python 2.7 kullanılır, aksi takdirde Python 3.5 kullanılır.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>LaunchWorker.ps1’i değiştirme
> [!NOTE]
> Bir **çalışan rolü** projesinde, başlangıç dosyasını yürütmek için **LauncherWorker.ps1** dosyası gereklidir. Bir **web rolü** projesinde ise başlangıç dosyası, bunun yerine proje özelliklerinde tanımlanır.
> 
> 

**bin\LaunchWorker.ps1** başlangıçta çok fazla hazırlık çalışması yapmak için oluşturulmuştur, ancak gerçekten çalışmamaktadır. Bu dosyanın içeriğini aşağıdaki betikle değiştirin.

Bu betik python projenizden **worker.py** dosyasını çağırır. **PYTHON2** ortam değişkeni **açık** olarak ayarlanırsa Python 2.7 kullanılır, aksi takdirde Python 3.5 kullanılır.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
Visual Studio şablonları **./bin** klasöründe bir **ps.cmd** dosyası oluşturmuş olmalıdır. Bu kabuk betiği yukarıdaki PowerShell sarmalayıcı betiklerini çağırır ve çağrılan PowerShell sarmalayıcısının adına göre günlük kaydı yapar. Bu dosya oluşturulmadıysa içinde olması gerekenler aşağıda verilmiştir. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Yerel olarak çalıştırma
Bulut hizmeti projenizi başlangıç projesi olarak ayarlar ve F5 tuşuna basarsanız, bulut hizmeti yerel Azure öykünücüsünde çalışır.

PTVS öykünücüde başlatmayı desteklese de, hata ayıklama (örneğin, kesme noktaları) çalışmaz.

Web ve çalışan rollerinizin hatalarını ayıklamak için rol projesini başlangıç projesi olarak ayarlayabilir ve bunun hatalarını ayıklayabilirsiniz.  Ayrıca, birden fazla başlangıç projesi ayarlayabilirsiniz.  Çözüme sağ tıklayın ve ardından **Başlangıç Projelerini Ayarla**’yı seçin.

![Çözüm Başlangıç Projesi Özellikleri](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Azure’da Yayımlama
Yayımlamak için, çözümdeki bulut hizmeti projesine sağ tıklayın ve ardından **Yayımla**’yı seçin.

![Microsoft Azure Yayımlama Oturumu Açma](./media/cloud-services-python-ptvs/publish-sign-in.png)

Sihirbazı izleyin. Gerekirse uzak masaüstünü etkinleştirin. Uzak masaüstü hata ayıklamanız gerektiğinde yararlıdır.

Yapılandırma ayarları bittiğinde **Yayımla**’ya tıklayın.

Çıktı penceresinde devam eden bazı işlemler görünür, ardından Microsoft Azure Etkinlik Günlüğü penceresini görürsünüz.

![Microsoft Azure Etkinlik Günlüğü Penceresi](./media/cloud-services-python-ptvs/publish-activity-log.png)

Dağıtımın tamamlanması birkaç dakika sürer, ardından web ve/veya çalışan rolleri Azure üzerinde çalışır!

### <a name="investigate-logs"></a>Günlükleri araştırma
Bulut hizmeti sanal makinesi başlatılıp Python’u yükledikten sonra herhangi bir hata iletisini bulmak için günlüklere bakabilirsiniz. Bu günlükler **C:\Resources\Directory\\{role}\LogFiles** klasöründe bulunur. Betiğin Python’un yüklü olup olmadığını algılamaya çalışmasından itibaren **PrepPython.err.txt** dosyasında en az bir hata olur ve **PipInstaller.err.txt** eskimiş bir pip sürümünü şikayet edebilir.

## <a name="next-steps"></a>Sonraki adımlar
Visual Studio için Python Araçları’ndaki web ve çalışan rolleri ile çalışma hakkında daha ayrıntılı bilgi için PTVS belgelerine bakın:

* [Bulut Hizmeti Projeleri][Cloud Service Projects]

Web ve çalışan rollerinizden Azure Storage veya Service Bus gibi Azure hizmetlerini kullanma hakkında daha ayrıntılı bilgi için aşağıdaki makalelere göz atın:

* [Blob Hizmeti][Blob Service]
* [Tablo Hizmeti][Table Service]
* [Kuyruk Hizmeti][Queue Service]
* [Service Bus Kuyrukları][Service Bus Queues]
* [Service Bus Konu Başlıkları][Service Bus Topics]

<!--Link references-->

[Bulut Hizmeti nedir?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: https://aka.ms/ptvsdocs
[Cloud Service Projects]: https://docs.microsoft.com/visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/
