---
title: PHP için Azure Web ve çalışan rolleri oluşturma
description: Bir Azure bulut hizmetinde PHP Web ve çalışan rolleri oluşturmaya ve PHP çalışma zamanını yapılandırmaya yönelik kılavuz.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 0b02662658b2199a13f117ad95c6402f9b2974f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95519855"
---
# <a name="create-php-web-and-worker-roles"></a>PHP web ve çalışan rolleri oluşturma

## <a name="overview"></a>Genel Bakış

Bu kılavuzda, bir Windows geliştirme ortamında PHP Web veya çalışan rolleri oluşturma, "yerleşik" sürümlerde php 'nin belirli bir sürümünü seçme, PHP yapılandırmasını değiştirme, uzantıları etkinleştirme ve son olarak Azure 'a dağıtma işlemlerinin nasıl yapılacağı gösterilir. Ayrıca, sağladığınız bir Web veya çalışan rolünün PHP çalışma zamanını (özel yapılandırma ve uzantılarla birlikte) kullanacak şekilde nasıl yapılandırılacağını açıklar.

Azure, uygulamaları çalıştırmak için üç işlem modeli sağlar: Azure App Service, Azure sanal makineleri ve Azure Cloud Services. Tüm üç model PHP 'yi destekler. Web ve çalışan rollerini içeren Cloud Services, *hizmet olarak platform (PaaS)* sağlar. Bir bulut hizmetinde web rolü, ön uç Web uygulamalarını barındırmak için adanmış bir Internet Information Services (IIS) Web sunucusu sağlar. Çalışan rolü, Kullanıcı etkileşimi veya girişten bağımsız olarak zaman uyumsuz, uzun süreli veya kalıcı görevleri çalıştırabilir.

Bu seçenekler hakkında daha fazla bilgi için bkz. [Azure tarafından sunulan işlem barındırma seçenekleri](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>PHP için Azure SDK 'sını indirin

[Php Için Azure SDK](https://github.com/Azure/azure-sdk-for-php) çeşitli bileşenlerden oluşur. Bu makalede bunların ikisi de kullanılır: Azure PowerShell ve Azure öykünücüleri. Bu iki bileşen Microsoft Web Platformu Yükleyicisi aracılığıyla yüklenebilir. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/).

## <a name="create-a-cloud-services-project"></a>Cloud Services projesi oluşturma

PHP Web veya çalışan rolü oluşturmanın ilk adımı bir Azure hizmeti projesi oluşturmaktır. bir Azure hizmeti projesi, Web ve çalışan rolleri için mantıksal bir kapsayıcı görevi görür ve projenin [hizmet tanımı (. csdef)] ve [hizmet yapılandırma (. cscfg)] dosyalarını içerir.

Yeni bir Azure hizmeti projesi oluşturmak için, Azure PowerShell yönetici olarak çalıştırın ve aşağıdaki komutu yürütün:

```powershell
PS C:\>New-AzureServiceProject myProject
```

Bu komut, `myProject` Web ve çalışan rolleri ekleyebileceğiniz yeni bir dizin () oluşturur.

## <a name="add-php-web-or-worker-roles"></a>PHP Web veya çalışan rolleri ekleme

Bir projeye PHP web rolü eklemek için, projenin kök dizini içinden aşağıdaki komutu çalıştırın:

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

Bir çalışan rolü için şu komutu kullanın:

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> `roleName`Parametresi isteğe bağlıdır. Atlanırsa, rol adı otomatik olarak oluşturulur. Oluşturulan ilk web rolü olacaktır `WebRole1` , ikincisi olur `WebRole2` ve bu şekilde devam eder. Oluşturulan ilk çalışan rolü olacaktır `WorkerRole1` , ikincisi olur `WorkerRole2` ve bu şekilde devam eder.
>
>

## <a name="use-your-own-php-runtime"></a>Kendi PHP çalışma zamanını kullanma

Bazı durumlarda, yerleşik bir PHP çalışma zamanı seçmek ve yukarıda açıklandığı gibi yapılandırmak yerine kendi PHP çalışma zamanını sağlamak isteyebilirsiniz. Örneğin, geliştirme ortamınızda kullandığınız bir Web veya çalışan rolünde aynı PHP çalışma zamanını kullanabilirsiniz. Bu, uygulamanın üretim ortamınızdaki davranışı değiştirememesini kolaylaştırır.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Kendi PHP çalışma zamanını kullanmak için bir Web rolü yapılandırma

Bir Web rolünü sağladığınız PHP çalışma zamanını kullanacak şekilde yapılandırmak için aşağıdaki adımları izleyin:

1. Bu konuda daha önce açıklandığı gibi bir Azure hizmeti projesi oluşturun ve bir PHP web rolü ekleyin.
2. `php` `bin` Web rolünüzün kök dizinindeki klasörde bir klasör oluşturun ve ardından php çalışma zamanını (tüm ikili dosyalar, yapılandırma dosyaları, alt klasörler vb.) `php` klasöre ekleyin.
3. SEÇIM PHP çalışma zamanının [SQL Server IÇIN php Için Microsoft sürücülerini][sqlsrv drivers]kullanması durumunda, Web rolünüzü, sağlandığında [SQL Server Native Client 2012][sql native client] yükleyecek şekilde yapılandırmanız gerekecektir. Bunu yapmak için, [sqlncli.msi x64 yükleyicisini] `bin` Web rolünüzün kök dizinindeki klasöre ekleyin. Sonraki adımda açıklanan başlatma betiği, rol sağlandığında yükleyiciyi sessizce çalıştıracaktır. PHP çalışma zamanı SQL Server için PHP için Microsoft sürücülerini kullanmıyorsa, sonraki adımda gösterilen betikten aşağıdaki satırı kaldırabilirsiniz:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Sayfaların isteklerini işlemek için PHP çalışma zamanını kullanmak üzere [Internet Information Services (IIS)][iis.net] yapılandıran bir başlangıç görevi tanımlayın `.php` . Bunu yapmak için, `setup_web.cmd` dosyayı `bin` bir metin düzenleyicisinde (Web rolünüzün kök dizini dosyasında) açın ve içeriğini aşağıdaki komut dosyasıyla değiştirin:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Uygulama dosyalarınızı Web rolünüzün kök dizinine ekleyin. Bu, Web sunucusunun kök dizini olacaktır.
6. Uygulamanızı [Yayımla](#publish-your-application) bölümünde açıklandığı gibi uygulamanızı yayımlayın.

> [!NOTE]
> `download.ps1`Betik ( `bin` Web rolünün kök dizini klasöründe), yukarıdaki php çalışma zamanını kullanmak için yukarıda açıklanan adımları izledikten sonra silinebilir.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Bir çalışan rolünü kendi PHP çalışma zamanını kullanacak şekilde yapılandırma

Sağladığınız bir PHP çalışma zamanını kullanmak üzere bir çalışan rolü yapılandırmak için aşağıdaki adımları izleyin:

1. Bu konuda daha önce açıklandığı gibi bir Azure hizmeti projesi oluşturun ve bir PHP Worker rolü ekleyin.
2. `php`Çalışan rolünün kök dizininde bir klasör oluşturun ve ardından php çalışma zamanını (tüm ikili dosyalar, yapılandırma dosyaları, alt klasörler vb.) `php` klasöre ekleyin.
3. SEÇIM PHP çalışma zamanının [SQL Server IÇIN php Için Microsoft sürücülerini][sqlsrv drivers]kullanması durumunda, çalışan rolünüzü, sağlandığında [SQL Server Native Client 2012][sql native client] yükleyecek şekilde yapılandırmanız gerekecektir. Bunu yapmak için, [sqlncli.msi x64 yükleyicisini] çalışan rolünün kök dizinine ekleyin. Sonraki adımda açıklanan başlatma betiği, rol sağlandığında yükleyiciyi sessizce çalıştıracaktır. PHP çalışma zamanı SQL Server için PHP için Microsoft sürücülerini kullanmıyorsa, sonraki adımda gösterilen betikten aşağıdaki satırı kaldırabilirsiniz:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. `php.exe`Rol sağlandığında çalıştırılabilirinizi çalışan rolünün yol ortam değişkenine ekleyen bir başlangıç görevi tanımlayın. Bunu yapmak için, `setup_worker.cmd` dosyayı (çalışan rolünün kök dizininde) bir metin düzenleyicisinde açın ve içeriğini aşağıdaki komut dosyasıyla değiştirin:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Uygulama dosyalarınızı, çalışan rolünüzün kök dizinine ekleyin.
6. Uygulamanızı [Yayımla](#publish-your-application) bölümünde açıklandığı gibi uygulamanızı yayımlayın.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Uygulamanızı işlem ve depolama öykünücülerinde çalıştırma

Azure öykünücüleri, Azure uygulamanızı buluta dağıtmadan önce test etmek için kullanabileceğiniz bir yerel ortam sağlar. Öykünücüler ve Azure ortamı arasında bazı farklılıklar vardır. Bunu daha iyi anlamak için bkz. [geliştirme ve test Için Azure depolama öykünücüsünü kullanma](storage/common/storage-use-emulator.md).

İşlem öykünücüsünü kullanmak için PHP 'nin yerel olarak yüklü olması gerektiğini unutmayın. İşlem öykünücüsü, uygulamanızı çalıştırmak için yerel PHP yüklemenizi kullanacaktır.

Projenizi Öykünücülerde çalıştırmak için, projenizin kök dizininden aşağıdaki komutu yürütün:

```powershell
PS C:\MyProject> Start-AzureEmulator
```

Aşağıdakine benzer bir çıktı görürsünüz:

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Uygulamanızı Öykünücüde çalışan bir Web tarayıcısı açıp çıktıda gösterilen yerel adrese ( `http://127.0.0.1:81` Yukarıdaki örnek çıktıda) göz atarak görebilirsiniz.

Öykünücüleri durdurmak için şu komutu yürütün:

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>Uygulamanızı yayımlama

Uygulamanızı yayımlamak için, önce [Import-Azuikinci adı SettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) cmdlet 'ini kullanarak yayımlama ayarlarınızı içeri aktarmanız gerekir. Daha sonra [Publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) cmdlet 'ini kullanarak uygulamanızı yayımlayabilirsiniz. Oturum açma hakkında daha fazla bilgi için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [php Geliştirici Merkezi](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[hizmet tanımı (. csdef)]: /previous-versions/azure/reference/ee758711(v=azure.100)
[hizmet yapılandırması (. cscfg)]: /previous-versions/azure/reference/ee758710(v=azure.100)
[iis.net]: https://www.iis.net/
[sql native client]: /sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 yükleyicisi]: https://go.microsoft.com/fwlink/?LinkID=239648