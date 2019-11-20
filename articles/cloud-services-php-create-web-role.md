---
title: PHP için Azure Web ve çalışan rolleri oluşturma
description: Bir Azure bulut hizmetinde PHP Web ve çalışan rolleri oluşturmaya ve PHP çalışma zamanını yapılandırmaya yönelik kılavuz.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 82bb5f153a2c70d3b26f295925f8e48693bc49b9
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146860"
---
# <a name="create-php-web-and-worker-roles"></a>PHP web ve çalışan rolleri oluşturma

## <a name="overview"></a>Genel Bakış

Bu kılavuzda, bir Windows geliştirme ortamında PHP Web veya çalışan rolleri oluşturma, "yerleşik" sürümlerde php 'nin belirli bir sürümünü seçme, PHP yapılandırmasını değiştirme, uzantıları etkinleştirme ve son olarak Azure 'a dağıtma işlemlerinin nasıl yapılacağı gösterilir. Ayrıca, sağladığınız bir Web veya çalışan rolünün PHP çalışma zamanını (özel yapılandırma ve uzantılarla birlikte) kullanacak şekilde nasıl yapılandırılacağını açıklar.

Azure, uygulamaları çalıştırmak için üç bilgi işlem modeli sağlar: Azure App Service, Azure sanal makineleri ve Azure Cloud Services. Tüm üç model PHP 'yi destekler. Web ve çalışan rollerini içeren Cloud Services, *hizmet olarak platform (PaaS)* sağlar. Bir bulut hizmetinde web rolü, ön uç Web uygulamalarını barındırmak için adanmış bir Internet Information Services (IIS) Web sunucusu sağlar. Çalışan rolü, Kullanıcı etkileşimi veya girişten bağımsız olarak zaman uyumsuz, uzun süreli veya kalıcı görevleri çalıştırabilir.

Bu seçenekler hakkında daha fazla bilgi için bkz. [Azure tarafından sunulan işlem barındırma seçenekleri](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>PHP için Azure SDK'sını indirme

[Php Için Azure SDK](https://github.com/Azure/azure-sdk-for-php) çeşitli bileşenlerden oluşur. Bu makale, bunlardan ikisini kullanır: Azure PowerShell ve Azure öykünücüleri. Bu iki bileşen Microsoft Web Platformu Yükleyicisi aracılığıyla yüklenebilir. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Cloud Services projesi oluşturma

PHP Web veya çalışan rolü oluşturmanın ilk adımı bir Azure hizmeti projesi oluşturmaktır. bir Azure hizmeti projesi, Web ve çalışan rolleri için mantıksal bir kapsayıcı görevi görür ve projenin [hizmet tanımı (. csdef)] ve [hizmet yapılandırma (. cscfg)] dosyalarını içerir.

Yeni bir Azure hizmeti projesi oluşturmak için, Azure PowerShell yönetici olarak çalıştırın ve aşağıdaki komutu yürütün:

    PS C:\>New-AzureServiceProject myProject

Bu komut, Web ve çalışan rolleri ekleyebileceğiniz`myProject`yeni bir dizin () oluşturur.

## <a name="add-php-web-or-worker-roles"></a>PHP Web veya çalışan rolleri ekleme

Bir projeye PHP web rolü eklemek için, projenin kök dizini içinden aşağıdaki komutu çalıştırın:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Bir çalışan rolü için şu komutu kullanın:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` Parametresi isteğe bağlıdır. Atlanırsa, rol adı otomatik olarak oluşturulur. Oluşturulan `WebRole1`ilk web rolü olacaktır, ikincisi `WebRole2`olur ve bu şekilde devam eder. Oluşturulan `WorkerRole1`ilk çalışan rolü olacaktır, ikincisi `WorkerRole2`olur ve bu şekilde devam eder.
>
>

## <a name="specify-the-built-in-php-version"></a>Yerleşik PHP sürümünü belirtin

Bir projeye PHP Web veya çalışan rolü eklediğinizde, projenin yapılandırma dosyaları değiştirilir. Bu, PHP 'nin dağıtıldığında uygulamanızın her bir Web veya çalışan örneğine yüklenebilmesini sağlar. Varsayılan olarak yüklenecek PHP sürümünü görmek için şu komutu çalıştırın:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Yukarıdaki komutun çıktısı aşağıda gösterilene benzer şekilde görünür. Bu örnekte, `IsDefault` bayrağı PHP 5.3.17 için olarak `true` ayarlanır ve bu, varsayılan php sürümünün yüklü olacağını gösterir.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

PHP çalışma zamanı sürümünü, listelenen PHP sürümlerinden herhangi birine ayarlayabilirsiniz. Örneğin, PHP sürümünü (adı `roleName`olan bir rol için) 5.4.0 olarak ayarlamak için aşağıdaki komutu kullanın:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Kullanılabilir PHP sürümleri gelecekte değişebilir.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Yerleşik PHP çalışma zamanını özelleştirme

Yukarıdaki adımları uyguladığınızda yüklü olan php çalışma zamanının yapılandırması üzerinde, `php.ini` ayarların değiştirilmesi ve uzantılarının etkinleştirilmesi da dahil olmak üzere tüm denetiminiz vardır.

Yerleşik PHP çalışma zamanını özelleştirmek için şu adımları izleyin:

1. Web rolünüzün `php` `bin` dizinine adlı yeni bir klasör ekleyin. Bir çalışan rolü için rolü rolün kök dizinine ekleyin.
2. `php` Klasöründe adlı`ext`başka bir klasör oluşturun. Bu klasörde etkinleştirmek istediğiniz `php_mongo.dll` uzantıdosyalarını(ör.)yerleştirin.`.dll`
3. `php` Klasöre bir `php.ini` dosya ekleyin. Tüm özel uzantıları etkinleştirin ve bu dosyada PHP yönergelerini ayarlayın. Örneğin, `display_errors` `php_mongo.dll` uzantıyı açmak ve etkinleştirmek istiyorsanız `php.ini` , dosyanızın içeriği aşağıdaki gibi olacaktır:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Sağladığınız `php.ini` dosyada açıkça ayarlanmamanız gereken ayarlar, otomatik olarak varsayılan değerlerine ayarlanır. Ancak, bir dosyanın tamamını `php.ini` ekleyebileceğinizi aklınızda bulundurun.
>
>

## <a name="use-your-own-php-runtime"></a>Kendi PHP çalışma zamanını kullanma

Bazı durumlarda, yerleşik bir PHP çalışma zamanı seçmek ve yukarıda açıklandığı gibi yapılandırmak yerine kendi PHP çalışma zamanını sağlamak isteyebilirsiniz. Örneğin, geliştirme ortamınızda kullandığınız bir Web veya çalışan rolünde aynı PHP çalışma zamanını kullanabilirsiniz. Bu, uygulamanın üretim ortamınızdaki davranışı değiştirememesini kolaylaştırır.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Kendi PHP çalışma zamanını kullanmak için bir Web rolü yapılandırma

Bir Web rolünü sağladığınız PHP çalışma zamanını kullanacak şekilde yapılandırmak için aşağıdaki adımları izleyin:

1. Bu konuda daha önce açıklandığı gibi bir Azure hizmeti projesi oluşturun ve bir PHP web rolü ekleyin.
2. Web rolünüzün kök `php` dizinindeki `bin` klasörde bir klasör oluşturun ve ardından php çalışma zamanını (tüm ikili dosyalar, yapılandırma dosyaları, alt klasörler `php` vb.) klasöre ekleyin.
3. SEÇIM PHP çalışma zamanının [SQL Server IÇIN php Için Microsoft sürücülerini][sqlsrv drivers]kullanması durumunda, Web rolünüzü, sağlandığında [SQL Server Native Client 2012][sql native client] yükleyecek şekilde yapılandırmanız gerekecektir. Bunu yapmak için, [Sqlncli. msi x64 yükleyicisi] `bin` Web rolünüzün kök dizinindeki klasöre ekleyin. Sonraki adımda açıklanan başlatma betiği, rol sağlandığında yükleyiciyi sessizce çalıştıracaktır. PHP çalışma zamanı SQL Server için PHP için Microsoft sürücülerini kullanmıyorsa, sonraki adımda gösterilen betikten aşağıdaki satırı kaldırabilirsiniz:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. `.php` Sayfaların isteklerini işlemek için php çalışma zamanını kullanmak üzere [Internet Information Services (IIS)][iis.net] yapılandıran bir başlangıç görevi tanımlayın. Bunu yapmak için, `setup_web.cmd` dosyayı bir metin düzenleyicisinde ( `bin` Web rolünüzün kök dizini dosyasında) açın ve içeriğini aşağıdaki komut dosyasıyla değiştirin:

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
> Betik `download.ps1` (`bin` Web rolünün kök dizini klasöründe), yukarıdaki php çalışma zamanını kullanmak için yukarıda açıklanan adımları izledikten sonra silinebilir.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Bir çalışan rolünü kendi PHP çalışma zamanını kullanacak şekilde yapılandırma

Sağladığınız bir PHP çalışma zamanını kullanmak üzere bir çalışan rolü yapılandırmak için aşağıdaki adımları izleyin:

1. Bu konuda daha önce açıklandığı gibi bir Azure hizmeti projesi oluşturun ve bir PHP Worker rolü ekleyin.
2. Çalışan rolünün `php` kök dizininde bir klasör oluşturun ve ardından php çalışma zamanını (tüm ikili dosyalar, yapılandırma dosyaları, alt klasörler vb.) `php` klasöre ekleyin.
3. SEÇIM PHP çalışma zamanının [SQL Server IÇIN php Için Microsoft sürücülerini][sqlsrv drivers]kullanması durumunda, çalışan rolünüzü, sağlandığında [SQL Server Native Client 2012][sql native client] yükleyecek şekilde yapılandırmanız gerekecektir. Bunu yapmak için, [Sqlncli. msi x64 yükleyicisi] çalışan rolünün kök dizinine ekleyin. Sonraki adımda açıklanan başlatma betiği, rol sağlandığında yükleyiciyi sessizce çalıştıracaktır. PHP çalışma zamanı SQL Server için PHP için Microsoft sürücülerini kullanmıyorsa, sonraki adımda gösterilen betikten aşağıdaki satırı kaldırabilirsiniz:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Rol sağlandığında çalıştırılabilirinizi `php.exe` çalışan rolünün yol ortam değişkenine ekleyen bir başlangıç görevi tanımlayın. Bunu yapmak için, `setup_worker.cmd` dosyayı (çalışan rolünün kök dizininde) bir metin düzenleyicisinde açın ve içeriğini aşağıdaki komut dosyasıyla değiştirin:

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

    PS C:\MyProject> Start-AzureEmulator

Aşağıdakine benzer bir çıktı görürsünüz:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Uygulamanızı Öykünücüde çalışan bir Web tarayıcısı açıp çıktıda gösterilen yerel adrese (`http://127.0.0.1:81` yukarıdaki örnek çıktıda) göz atarak görebilirsiniz.

Öykünücüleri durdurmak için şu komutu yürütün:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Uygulamanızı yayımlama

Uygulamanızı yayımlamak için, önce [Import-Azuikinci adı SettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) cmdlet 'ini kullanarak yayımlama ayarlarınızı içeri aktarmanız gerekir. Daha sonra [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) cmdlet 'ini kullanarak uygulamanızı yayımlayabilirsiniz. Oturum açma hakkında daha fazla bilgi için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/overview).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [php Geliştirici Merkezi](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[hizmet tanımı (. csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[hizmet yapılandırma (. cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[Sqlncli. msi x64 yükleyicisi]: https://go.microsoft.com/fwlink/?LinkID=239648
