---
title: PowerShell Azure Işlevlerini yerel olarak hata ayıklama
description: PowerShell kullanarak işlevleri geliştirmeyi anlayın.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 5b396ef6b00d53a313ed4fb426685c12e2c1549d
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981838"
---
# <a name="debug-powershell-azure-functions-locally"></a>PowerShell Azure Işlevlerini yerel olarak hata ayıklama

Azure Işlevleri, işlevlerinizi PowerShell betikleri olarak geliştirmenize olanak sağlar.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

PowerShell işlevlerinizde, aşağıdaki standart geliştirme araçlarını kullanarak herhangi bir PowerShell komut dosyası gibi yerel olarak hata ayıklaması yapabilirsiniz:

* [Visual Studio Code](https://code.visualstudio.com/): Microsoft 'un tam PowerShell geliştirme deneyimi sunan PowerShell uzantısıyla ücretsiz, hafif ve açık kaynaklı metin düzenleyici.
* Bir PowerShell konsolu: başka bir PowerShell işleminde hata ayıklamak için kullandığınız komutları kullanarak hata ayıklayın.

[Azure Functions Core Tools](functions-run-local.md) , PowerShell işlevleri dahil olmak üzere Azure işlevlerinde yerel hata ayıklamayı destekler.

## <a name="example-function-app"></a>Örnek işlev uygulaması

Bu makalede kullanılan işlev uygulamasının tek bir HTTP ile tetiklenen işlevi vardır ve aşağıdaki dosyalara sahiptir:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Bu işlev uygulaması, [PowerShell hızlı](functions-create-first-function-powershell.md)başlangıcını tamamladığınızda aldığınız birine benzer.

@No__t-0 ' daki işlev kodu aşağıdaki komut dosyası gibi görünür:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>İliştirme noktasını ayarlama

Herhangi bir PowerShell işlevinde hata ayıklamak için, hata ayıklayıcının eklenmesi için işlevin durdurulması gerekir. @No__t-0 cmdlet 'i yürütmeyi durduruyor ve hata ayıklayıcıyı bekler.

Tek yapmanız gereken, `if` ifadesinin hemen üzerinde `Wait-Debugger` cmdlet 'ine aşağıdaki gibi bir çağrı eklemektir:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Hata ayıklama `if` ifadesinde başlar. 

@No__t-0 ile, artık Visual Studio Code veya PowerShell konsolunu kullanarak işlevlerde hata ayıklaması yapabilirsiniz.

## <a name="debug-in-visual-studio-code"></a>Visual Studio Code hata ayıkla

Visual Studio Code PowerShell işlevlerinizi hata ayıklaması yapmak için aşağıdakilerin yüklü olması gerekir:

* [Visual Studio Code için PowerShell uzantısı](/powershell/scripting/components/vscode/using-vscode)
* [Visual Studio Code için Azure İşlevleri uzantısı](functions-create-first-function-vs-code.md)
* [PowerShell Core 6,2 veya üzeri](/powershell/scripting/install/installing-powershell-core-on-windows)

Bu bağımlılıkları yükledikten sonra var olan bir PowerShell Işlevleri projesini yükleyin veya [Ilk PowerShell işlevleri projenizi oluşturun](functions-create-first-function-powershell.md).

>[!NOTE]
> Projenizde gerekli yapılandırma dosyaları yoksa, bunları eklemeniz istenir.

### <a name="set-the-powershell-version"></a>PowerShell sürümünü ayarlama

PowerShell Core, Windows PowerShell ile yan yana yüklenir. PowerShell uzantısını, Visual Studio Code için PowerShell Uzantısı ile kullanılacak PowerShell sürümü olarak ayarlayın.

1. Komut paleti ' ni göstermek için F1 ' e basın, sonra `Session` ' ı arayın.

1. **PowerShell: oturum menüsünü göster**' i seçin.

1. **Geçerli oturumunuz** **PowerShell Core 6**değilse, **geçiş yap: PowerShell Core 6**' ı seçin.

Açık bir PowerShell dosyanız olduğunda, pencerenin sağ alt tarafında yeşil renkte görüntülendiğini görürsünüz. Bu metnin seçilmesi oturum menüsünü de görüntüler. Daha fazla bilgi edinmek için, [Uzantısı ile kullanılacak PowerShell 'in bir sürümünü seçme](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)bölümüne bakın.

### <a name="start-the-function-app"></a>İşlev uygulamasını başlatma

Hata ayıklayıcıyı iliştirmek istediğiniz işlevde `Wait-Debugger` ' ın ayarlandığını doğrulayın.  @No__t-0 eklendikten sonra, Visual Studio Code kullanarak işlev uygulamanızda hata ayıklaması yapabilirsiniz.

**Hata Ayıkla** bölmesini seçin ve ardından **PowerShell işlevine ekleyin**.

![sý](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Hata ayıklamayı başlatmak için F5 tuşuna da basabilirsiniz.

Hata ayıklamayı Başlat işlemi aşağıdaki görevleri yapar:

* İşlev uygulamanız tarafından istenen tüm Azure Işlevleri uzantılarını yüklemek için terminalde `func extensions install` ' i çalıştırır.
* İşlev uygulamasını Işlevler ana bilgisayarında başlatmak için terminalde `func host start` ' i çalıştırır.
* PowerShell hata ayıklayıcısını, Işlevler çalışma zamanı içindeki PowerShell çalışma alanına ekleyin.

>[!NOTE]
> Visual Studio Code doğru hata ayıklama deneyimini sağlamak için PSWorkerInProcConcurrencyUpperBound 'in 1 olarak ayarlandığından emin olmanız gerekir. Bu varsayılandır.

İşlev uygulamanız çalışırken, HTTP ile tetiklenen işlevi çağırmak için ayrı bir PowerShell konsoluna ihtiyacınız vardır.

Bu durumda, PowerShell konsolu istemcdir. @No__t-0 işlevi tetiklemek için kullanılır.

Bir PowerShell konsolunda aşağıdaki komutu çalıştırın:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Yanıtın hemen döndürülmeyeceğini fark edeceksiniz. Çünkü `Wait-Debugger`, hata ayıklayıcıyı iliştirmiştir ve PowerShell yürütmesi, daha kısa sürede kesme moduna geçti. Bunun nedeni, daha sonra açıklanacak olan [Breakall kavramıdır](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place). @No__t-0 düğmesine bastıktan sonra, hata ayıklayıcı artık `Wait-Debugger` ' den sonra satırı keser.

Bu noktada, hata ayıklayıcı iliştirilir ve tüm normal hata ayıklayıcı işlemlerini yapabilirsiniz. Visual Studio Code hata ayıklayıcıyı kullanma hakkında daha fazla bilgi için [resmi belgelere](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)bakın.

Devam ettikten ve komut dosyanızı tamamen çağırdığınızda şunu görürsünüz:

* @No__t-0 olan PowerShell konsolu bir sonuç döndürdü
* Visual Studio Code 'de PowerShell Tümleşik konsolu bir betiğin yürütülmesini bekliyor

Daha sonra aynı işlevi çağırdığınızda, PowerShell uzantısında hata ayıklayıcı `Wait-Debugger` ' dan sonra kesilir.

## <a name="debugging-in-a-powershell-console"></a>PowerShell konsolunda hata ayıklama

>[!NOTE]
> Bu bölümde [Azure Functions Core Tools belgeleri](functions-run-local.md) okuduğunuzu ve işlev uygulamanızı başlatmak için `func host start` komutunun nasıl kullanılacağını bildiğiniz varsayılmaktadır.

Bir konsolu açın, işlev uygulamanızın dizinine `cd` ' ı açın ve şu komutu çalıştırın:

```sh
func host start
```

İşlev uygulaması çalışır ve `Wait-Debugger` yerinde, işleme ekleyebilirsiniz. İki PowerShell konsolundan daha ihtiyacınız vardır.

Konsollardan biri istemci gibi davranır. Bundan sonra, işlevi tetiklemek için `Invoke-RestMethod` ' ı çağırın. Örneğin, aşağıdaki komutu çalıştırabilirsiniz:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

@No__t-0 sonucu bir yanıt döndürmediğine dikkat edin. PowerShell çalışma şimdi bir hata ayıklayıcının eklenmesini bekliyor. Şimdi bu iliştirilme.

Diğer PowerShell konsolunda aşağıdaki komutu çalıştırın:

```powershell
Get-PSHostProcessInfo
```

Bu cmdlet aşağıdaki çıktı gibi görünen bir tablo döndürür:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Tablodaki öğe için `ProcessId` ' a `dotnet` olarak `ProcessName` ' i unutmayın. Bu işlem, işlev uygulamanız.

Sonra, aşağıdaki kod parçacığını çalıştırın:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Başlatıldıktan sonra, hata ayıklayıcı kesilir ve aşağıdaki çıktıya benzer bir şey gösterir:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Bu noktada, [PowerShell hata ayıklayıcısında](/powershell/module/microsoft.powershell.core/about/about_debuggers)bir kesme noktasında duruyoruz. Buradan, tüm olağan hata ayıklama işlemlerini, adım adım, adımla, devam etme, çıkış ve diğer diğer işlemleri yapabilirsiniz. Konsolda kullanılabilen hata ayıklama komutlarının tam kümesini görmek için `h` veya `?` komutlarını çalıştırın.

@No__t-0 cmdlet 'ini kullanarak bu düzeyde kesme noktaları da ayarlayabilirsiniz.

Devam edip komut dosyanızı tamamen çağırdığınızda şunu görürsünüz:

* @No__t (0) çalıştırdığınız PowerShell konsolu artık bir sonuç döndürdü.
* @No__t çalıştırdığınız PowerShell konsolu, bir betiğin yürütülmesini bekliyor.

Aynı işlevi yeniden çağırabilirsiniz (örneğin `Invoke-RestMethod` kullanarak) ve hata ayıklayıcı, `Wait-Debugger` komutundan hemen sonra kesilir.

## <a name="considerations-for-debugging"></a>Hata ayıklama konuları

Işlev kodunuzda hata ayıklarken aşağıdaki sorunları aklınızda bulundurun.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`, hata ayıklayıcının beklenmedik bir yerde kesintiye neden olabilir

PowerShell uzantısı, `Debug-Runspace` kullanır ve bu da PowerShell 'in `BreakAll` özelliğine bağımlıdır. Bu özellik, PowerShell 'in yürütülen ilk komutta durmasını söyler. Bu davranış, hata ayıklanan çalışma alanı içinde kesme noktaları ayarlama fırsatı sağlar.

Azure Işlevleri çalışma zamanı, `run.ps1` betiğinizi gerçekten çağırmadan önce birkaç komut çalıştırır, bu nedenle hata ayıklayıcı `Microsoft.Azure.Functions.PowerShellWorker.psm1` veya `Microsoft.Azure.Functions.PowerShellWorker.psd1` ' den sonra sona erecek olabilir.

Bu kesme gerçekleşmelidir, bu kesme noktasını atlamak için `continue` veya `c` komutunu çalıştırın. Ardından, beklenen kesme noktasında durursunuz.

## <a name="next-steps"></a>Sonraki adımlar

PowerShell kullanarak Işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Azure Işlevleri PowerShell Geliştirici Kılavuzu](functions-reference-powershell.md).
