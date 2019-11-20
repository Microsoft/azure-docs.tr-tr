---
title: Azure Application Insights OpenCensus dağıtılmış izleme yerel ileticisi (Önizleme) | Microsoft docs
description: Python ve Azure 'da bulunan dillerdeki dağıtılmış izlemeleri ve yayılmaları iletme ve Azure 'a git Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.openlocfilehash: b0d0bc4d711b05dd2206b7437f1f4c7b3444a0c6
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819207"
---
# <a name="local-forwarder-preview"></a>Yerel iletici (Önizleme)

Yerel iletici, çeşitli SDK 'lardan Application Insights veya [Opencensus](https://opencensus.io/) telemetrisini toplayan ve Application Insights yönlendiren bir aracıdır. Windows ve Linux altında çalıştırma yeteneğine sahiptir. MacOS altında de çalıştırabilirsiniz, ancak şu anda resmi olarak desteklenmez.

## <a name="running-local-forwarder"></a>Yerel iletici çalıştırılıyor

Yerel iletici [GitHub üzerinde açık kaynaklı bir projem](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Birden çok platformda yerel iletici çalıştırmak için çeşitli yollar vardır.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows hizmeti

Windows altında yerel iletici çalıştırmanın en kolay yolu Windows hizmeti olarak yüklemek. Yayın, işletim sistemiyle kolayca kaydedilemeyen bir Windows hizmeti yürütülebilir dosyası (*windowsservicehost/Microsoft. LocalForwarder. WindowsServiceHost. exe*) ile birlikte gelir.

> [!NOTE]
> Yerel iletici hizmeti en az .NET Framework 4,7 gerektirir. .NET Framework 4,7 yoksa hizmet yüklenir, ancak başlatılmaz. .NET Framework 'nin son sürümüne erişmek için **[.NET Framework indirme sayfasını ziyaret edin](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. LF 'yi indirin. GitHub 'daki [Yerel iletici yayın sayfasından](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) windowsservicehost. zip dosyası.

    ![Yerel iletici yayın indirme sayfasının ekran görüntüsü](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Bu örnekte, tanıtım kolaylığı için, yalnızca. zip dosyasını `C:\LF-WindowsServiceHost`yola çıkaracağız.

    Hizmeti kaydetmek ve sistem önyüklemesi sırasında başlatılacak şekilde yapılandırmak için komut satırından yönetici olarak aşağıdakileri çalıştırın:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Şu yanıtı almalısınız:
    
    `[SC] CreateService SUCCESS`
    
    Yeni hizmetinizi hizmetler GUI türü aracılığıyla incelemek için ``services.msc``
        
     ![Yerel iletici hizmetinin ekran görüntüsü](./media/opencensus-local-forwarder/002-services.png)

3. Yeni yerel ileticiye **sağ tıklayıp Başlat ' ı** seçin. Hizmetiniz artık çalışır duruma girer.

4. Hizmet, varsayılan olarak herhangi bir kurtarma eylemi olmadan oluşturulur. Bir hizmet hatasına otomatik yanıtları yapılandırmak için sağ tıklayıp **özellikler** > **Kurtarma** **' yı** seçebilirsiniz.

    Ya da hataların oluşma zaman programlama yoluyla otomatik kurtarma seçeneklerini ayarlamayı tercih ediyorsanız şunları kullanabilirsiniz:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` dosyanız ile aynı konumda, bu örnekte ``LocalForwarder.config``adlı bir dosya ``C:\LF-WindowsServiceHost``. Bu, localforwader yapılandırmasını ayarlamanıza ve dağıtılmış izleme verilerinizin iletilmesini istediğiniz Application Insights kaynağının izleme anahtarını belirtmenize olanak tanıyan bir XML tabanlı dosyadır. 

    İzleme anahtarınızı eklemek için ``LocalForwarder.config`` dosyasını düzenledikten sonra, değişikliklerinizin etkili olması için **Yerel Iletici hizmetini** yeniden başlattığınızdan emin olun.
    
6. İstediğiniz ayarların yerinde olduğunu ve yerel ileticinin izleme verilerini beklendiği şekilde dinlediğini onaylamak için ``LocalForwarder.log`` dosyasına bakın. Dosyanın en altında bulunan görüntüye benzer sonuçlar görmeniz gerekir:

    ![LocalForwarder. log dosyasının ekran görüntüsü](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Konsol uygulaması

Bazı kullanım durumları için, yerel ileticinin bir konsol uygulaması olarak çalıştırılması yararlı olabilir. Yayın, konsol konağının aşağıdaki yürütülebilir sürümleriyle birlikte gelir:
* çerçeveye bağımlı .NET Core ikili */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Bu ikiliyi çalıştırmak için .NET Core çalışma zamanının yüklenmesi gerekir; Ayrıntılar için bu indirme [sayfasına](https://www.microsoft.com/net/download/dotnet-core/2.1) bakın.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* x86 ve x64 platformları için kendi kendine dahil edilen bir .NET Core ikili kümesi. Bunlar .NET Core çalışma zamanının çalıştırılmasını gerektirmez. */ConsoleHost/Win-x86/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/Win-x64/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Windows 'da olduğu gibi, sürüm konsol konağının aşağıdaki yürütülebilir sürümleriyle birlikte gelir:
* çerçeveye bağımlı .NET Core ikili */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Bu ikiliyi çalıştırmak için .NET Core çalışma zamanının yüklenmesi gerekir; Ayrıntılar için bu indirme [sayfasına](https://www.microsoft.com/net/download/dotnet-core/2.1) bakın.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* Linux-64 için kendi kendine dahil edilen bir .NET Core ikili kümesi. Bu bir, .NET Core çalışma zamanının çalıştırılmasını gerektirmez. */ConsoleHost/Linux-x64/Publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Birçok Linux kullanıcısı yerel ileticiyi bir daemon olarak çalıştırmak isteyeceksiniz. Linux sistemleri, hizmet yönetimi için Upstart, SYSV veya systemd gibi çeşitli çözümlerle gelir. Belirli sürümünüz ne olursa olsun, bunu senaryonuz için en uygun şekilde yerel iletici çalıştırmak için kullanabilirsiniz.

Örnek olarak, systemd kullanarak bir Daemon hizmeti oluşturalım. Çerçeveye bağımlı sürümü kullanacağız, ancak aynı zamanda bir kendi içinde de aynı şekilde yapılabilir.

* *localforwarder. Service* adlı aşağıdaki hizmet dosyasını oluşturun ve */lib/systemd/System*dizinine yerleştirin.
Bu örnek, Kullanıcı adınızın SAMPLE_USER olduğunu varsayar ve yerel iletici çerçevesine bağımlı ikili dosyaları ( */consolehost/Publish*) */Home/sample_user/localforwarder_dir*dizinine kopyaladınız.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Systemd 'yi her önyüklemede yerel ileticinin başlamasını istemek için aşağıdaki komutu çalıştırın

```
systemctl enable localforwarder
```

* Systemd 'nin yerel ileticinin hemen başlamasını istemek için aşağıdaki komutu çalıştırın

```
systemctl start localforwarder
```

* /Home/SAMPLE_USER/LOCALFORWARDER_DIR dizinindeki * *. log* dosyalarını inceleyerek hizmeti izleyin.

### <a name="mac"></a>Mac
Yerel iletici macOS ile çalışabilir, ancak şu anda resmi olarak desteklenmez.

### <a name="self-hosting"></a>Kendi kendine barındırma
Yerel iletici Ayrıca, kendi .NET uygulamanızda barındırmanıza olanak tanıyan bir .NET Standard NuGet paketi olarak dağıtılır.

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Yerel ileticisi yapılandırma

* Yerel ileticinin kendi konaklarından (konsol konağı veya Windows hizmet ana bilgisayarı) birini çalıştırırken, **Localforwarder. config** dosyasının yanına yerleştirilir.
* Yerel ileticinin NuGet 'i kendi kendine barındırdığında, kodda aynı biçimdeki yapılandırmanın sağlanması gerekir (bkz. Self-hosting üzerinde bölüm). Yapılandırma sözdizimi için GitHub deposundaki [Localforwarder. config dosyasını](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) denetleyin. 

> [!NOTE]
> Yapılandırma sürümden sürüme değişebilir, bu nedenle kullandığınız sürüme dikkat edin.

## <a name="monitoring-local-forwarder"></a>Yerel ileticisini izleme

İzlemeler, yerel iletici çalıştıran yürütülebilir dosyanın yanında dosya sistemine yazılır (* *. log* dosyalarını arayın). Varsayılan bir yapılandırma yerine kendi yapılandırmanızı sağlamak için, yürütülebilir dosyanın yanına *NLog. config* adlı bir dosya yerleştirebilirsiniz. Biçimin açıklaması için [belgelere](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) bakın.

Hiçbir yapılandırma dosyası sağlanmazsa (varsayılan), yerel iletici [burada](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config)bulunan varsayılan yapılandırmayı kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Census 'i açma](https://opencensus.io/)
