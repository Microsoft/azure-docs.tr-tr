---
title: Azure Cloud Services rollerine .NET 'i yükler | Microsoft Docs
description: Bu makalede, bulut hizmeti Web ve çalışan rollerinizin .NET Framework el ile nasıl yükleneceği açıklanır.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: gwallace
ms.openlocfilehash: 25151f154b9806646406639df3efd7616e53f6bf
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359642"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Azure Cloud Services rollerine .NET 'i yükler
Bu makalede, Azure Konuk IŞLETIM sistemiyle birlikte gelmeyen .NET Framework sürümlerinin nasıl yükleneceği açıklanır. Bulut hizmeti Web ve çalışan rollerinizi yapılandırmak için konuk işletim sisteminde .NET kullanabilirsiniz.

Örneğin, .NET 4.6.2 'i Konuk işletim sistemi ailesi 4 ' te yükleyebilirsiniz. Bu sürüm, .NET 4,6 'nin herhangi bir sürümüyle birlikte gelmeyecektir. (Konuk işletim sistemi ailesi 5, .NET 4,6 ile gelir.) Azure Konuk işletim sistemi sürümleri hakkında en son bilgiler için bkz. [Azure Konuk işletim sistemi sürüm haberleri](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Azure SDK 2,9, Konuk işletim sistemi ailesi 4 veya önceki sürümlerde .NET 4,6 dağıtımı hakkında bir kısıtlama içerir. [Microsoft docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) sitesinde kısıtlama için bir çözüm bulunur.

Web ve çalışan rolleriniz üzerinde .NET yüklemek için, bulut hizmeti projenizin bir parçası olarak .NET web yükleyicisini dahil edin. Rolün başlangıç görevlerinin bir parçası olarak Yükleyiciyi başlatın. 

## <a name="add-the-net-installer-to-your-project"></a>Projenize .NET yükleyicisi ekleyin
.NET Framework web yükleyicisini indirmek için, yüklemek istediğiniz sürümü seçin:

* [.NET 4,8 Web Yükleyicisi](https://dotnet.microsoft.com/download/thank-you/net48)
* [.NET 4.7.2 Web Yükleyicisi](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET 4.6.2 Web Yükleyicisi](https://www.microsoft.com/download/details.aspx?id=53345)

Bir *Web* rolü için yükleyiciyi eklemek için:
  1. **Çözüm Gezgini**, bulut hizmeti projenizdeki **Roller** ' in altında *Web* rolünüze sağ tıklayıp**Yeni klasör** **Ekle** > ' yi seçin. **Bin**adlı bir klasör oluşturun.
  2. Bin klasörüne sağ tıklayın ve**Varolan öğe** **Ekle** > ' yi seçin. .NET yükleyicisini seçin ve bin klasörüne ekleyin.
  
Bir *çalışan* rolü için yükleyiciyi eklemek için:
* *Çalışan* rolünüze sağ tıklayın ve**Varolan öğe** **Ekle** > ' yi seçin. .NET yükleyicisini seçin ve role ekleyin. 

Bu şekilde, rol içerik klasörüne dosyalar eklendiğinde, bulut hizmeti paketinize otomatik olarak eklenir. Dosyalar daha sonra sanal makinede tutarlı bir konuma dağıtılır. Tüm rollerin yükleyicinin bir kopyasına sahip olması için bulut hizmetinizdeki her bir Web ve çalışan rolü için bu işlemi tekrarlayın.

> [!NOTE]
> Uygulamanız .NET 4,6 hedefliyorsa bile, bulut hizmeti rolünüze .NET 4.6.2 yüklemelisiniz. Konuk işletim sistemi, Bilgi Bankası [güncelleştirme 3098779](https://support.microsoft.com/kb/3098779) ve [güncelleştirme 3097997](https://support.microsoft.com/kb/3097997)' i içerir. .NET uygulamalarınızı, Bilgi Bankası güncelleştirmelerinin üzerine .NET 4,6 yüklenmişse sorun oluşabilir. Bu sorunlardan kaçınmak için sürüm 4,6 yerine .NET 4.6.2 ' yi yüklemek. Daha fazla bilgi için bkz. [Bilgi Bankası makalesi 3118750](https://support.microsoft.com/kb/3118750) ve [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Yükleyici dosyaları ile rol içerikleri][1]

## <a name="define-startup-tasks-for-your-roles"></a>Rolleriniz için başlangıç görevlerini tanımlama
Bir rol başlamadan önce işlemleri gerçekleştirmek için başlangıç görevleri kullanabilirsiniz. .NET Framework, başlangıç görevinin bir parçası olarak yüklenmesi, herhangi bir uygulama kodu çalıştırılmadan önce çerçevenin yüklenmesini sağlar. Başlangıç görevleri hakkında daha fazla bilgi için bkz. [Azure 'da başlangıç görevlerini çalıştırma](cloud-services-startup-tasks.md). 

1. Aşağıdaki içeriği, tüm roller için **WebRole** veya **workerrole** düğümünün altındaki ServiceDefinition. csdef dosyasına ekleyin:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Önceki yapılandırma, .NET Framework yüklemek için konsol `install.cmd` komutunu yönetici ayrıcalıklarıyla çalıştırır. Yapılandırma ayrıca **Netfxınstall**adlı bir **localStorage** öğesi oluşturur. Başlangıç betiği, bu yerel depolama kaynağını kullanmak için Temp klasörünü ayarlar. 
    
    > [!IMPORTANT]
    > Framework 'ün doğru yüklemesini sağlamak için bu kaynağın boyutunu en az 1.024 MB olarak ayarlayın.
    
    Başlangıç görevleri hakkında daha fazla bilgi için bkz. [Genel Azure Cloud Services başlangıç görevleri](cloud-services-startup-tasks-common.md).

2. **Install. cmd** adlı bir dosya oluşturun ve aşağıdaki Install betiğini dosyaya ekleyin.

   Betik, kayıt defterini sorgulayarak .NET Framework belirtilen sürümünün makinede zaten yüklü olup olmadığını denetler. .NET sürümü yüklü değilse, .NET Web Yükleyicisi açılır. Herhangi bir sorunu gidermeye yardımcı olmak için, betik tüm etkinlikleri, **ınstalllogs** yerel depolama alanında depolanan startuptasklog (geçerli tarih ve saat). txt dosyasına kaydeder.
   
   > [!IMPORTANT]
   > Install. cmd dosyasını oluşturmak için Windows Notepad gibi temel bir metin düzenleyicisi kullanın. Bir metin dosyası oluşturmak ve uzantıyı. cmd olarak değiştirmek için Visual Studio kullanıyorsanız, dosya yine de bir UTF-8 bayt sırası işareti içeriyor olabilir. Bu işaret, betiğin ilk satırı çalıştırıldığında hata oluşmasına neden olabilir. Bu hatayı önlemek için betiğin ilk satırını, bayt sırası işleme tarafından atlanacak bir REM ifadesinin önüne alın. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Bu konunun önceki kısımlarında açıklandığı gibi **Çözüm Gezgini** ' de**var olan öğe** **Ekle** > ' 'yi kullanarak her role Install. cmd dosyasını ekleyin. 

    Bu adım tamamlandıktan sonra, tüm rollerin .NET yükleyici dosyası ve Install. cmd dosyası olması gerekir.

   ![Tüm dosyaları içeren rol içerikleri][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Başlangıç günlüklerini blob depolamaya aktarmak için tanılamayı yapılandırın
Yükleme sorunlarını giderme işlemini basitleştirmek için, başlangıç betiği veya .NET yükleyicisi tarafından oluşturulan günlük dosyalarını Azure Blob depolama alanına aktarmak üzere Azure Tanılama yapılandırabilirsiniz. Bu yaklaşımı kullanarak, rol üzerinde Uzak Masaüstü yerine, blob depolamadan günlük dosyalarını indirerek günlükleri görüntüleyebilirsiniz.


Tanılamayı yapılandırmak için, Diagnostics. wadcfgx dosyasını açın ve **dizinler** düğümüne aşağıdaki içeriği ekleyin: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Bu XML, **Netfxinstall** kaynağındaki günlük dizinindeki dosyaları **Netfx-Install** blob kapsayıcısındaki tanılama depolama hesabına aktarmak üzere tanılamayı yapılandırır.

## <a name="deploy-your-cloud-service"></a>Bulut hizmetinizi dağıtın
Bulut hizmetinizi dağıtırken, önceden yüklenmemişse, başlangıç görevleri .NET Framework yükler. Çerçeve yüklenirken bulut hizmeti rolleriniz *meşgul* durumunda. Çerçeve yüklemesi için yeniden başlatma gerekiyorsa, hizmet rolleri de yeniden başlatılabilir. 

## <a name="additional-resources"></a>Ek kaynaklar
* [.NET Framework yükleme][Installing the .NET Framework]
* [Hangi .NET Framework sürümlerinin yüklendiğini belirleme][How to: Determine Which .NET Framework Versions Are Installed]
* [.NET Framework yüklemeleri sorunlarını giderme][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
