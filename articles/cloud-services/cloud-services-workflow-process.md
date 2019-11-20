---
title: Windows Azure VM mimarisinin iş akışı | Microsoft Docs
description: Bu makalede bir hizmeti dağıtırken iş akışı işlemlerine genel bakış sağlanır.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162159"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Klasik Microsoft Azure VM mimarisi iş akışı 
Bu makalede, bir sanal makine gibi bir Azure kaynağını dağıtırken veya güncelleştirdiğinizde oluşan iş akışı işlemlerine genel bakış sunulmaktadır. 

> [!NOTE]
>Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: Kaynak Yöneticisi ve klasik. Bu makale klasik dağıtım modelini incelemektedir.

Aşağıdaki diyagramda Azure kaynakları mimarisi sunulmaktadır.

![Azure iş akışı](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>İş akışı temelleri
   
**A**. RDFE/FFE, kullanıcıdan dokuya yönelik iletişim yoludur. RDFE (RedDog Front End), Yönetim Portalı ve Visual Studio, Azure MMC gibi Hizmet Yönetim API'si ön ucu olan genel kullanıma açık bir API 'dir.  Kullanıcının tüm istekleri RDFE aracılığıyla gider. FFE (doku ön ucu), istekleri RDFE 'den doku komutlarına çeviren katmandır. RDFE 'den gelen tüm istekler, doku denetleyicilerine ulaşmak için FFE aracılığıyla gider.

**B**. Yapı denetleyicisi, veri merkezindeki tüm kaynakların korunmasından ve izlenmesinden sorumludur. Bu BT, Konuk işletim sistemi sürümü, hizmet paketi, hizmet yapılandırması ve hizmet durumu gibi bilgileri gönderen yapı işletim sistemi üzerinde Fabric Host aracılarıyla iletişim kurar.

**C**. Konak Aracısı konak işletim sistemi üzerinde çalışır ve hedeflenen bir hedef durumuna doğru rolü güncelleştirmek ve konuk aracısında sinyal denetimleri yapmak için konuk işletim sistemini ayarlamaktan ve konuk aracısıyla iletişim kurmaktan sorumludur (WindowsAzureGuestAgent). Konak Aracısı 10 dakika boyunca sinyal yanıtı almazsa, konak Aracısı Konuk işletim sistemini yeniden başlatır.

**C2**. WaAppAgent, WindowsAzureGuestAgent. exe ' yi yüklemeden, yapılandırmadan ve güncelleştirmekten sorumludur.

**D**.  WindowsAzureGuestAgent, aşağıdakilerden sorumludur:

1. Konuk işletim sistemini, güvenlik duvarı, ACL 'Ler, LocalStorage kaynakları, hizmet paketi ve yapılandırma ve Sertifikalar dahil olmak üzere yapılandırma.
2. Rolün altında çalışacağı kullanıcı hesabı için SID ayarlanıyor.
3. Rol durumu dokuya bağlanıyor.
4. WaHostBootstrapper başlatılıyor ve rolü hedef durumunda olduğundan emin olmak için izleme.

**E**. WaHostBootstrapper şu şekilde sorumludur:

1. Rol yapılandırmasını okuma ve rolü yapılandırmak ve çalıştırmak için uygun tüm görev ve işlemleri başlatma.
2. Tüm alt süreçlerini izleme.
3. Rol konak işlemindeki StatusCheck olayı.

**F**. Rol tam bir IIS Web rolü olarak yapılandırıldıysa (SDK 1,2 HWC rolleri için çalıştırılmaz) IISConfigurator çalışır. Bundan sorumludur:

1. Standart IIS Hizmetleri başlatılıyor
2. Web yapılandırmasında yeniden yazma modülünü yapılandırma
3. Hizmet modelinde yapılandırılan rol için AppPool 'ı ayarlama
4. IIS günlüğünü DiagnosticStore LocalStorage klasörünü işaret etmek üzere ayarlama
5. İzinleri ve ACL 'Leri yapılandırma
6. Web sitesi% roleroot%: \sitesroot\0 konumunda bulunur ve AppPool IIS çalıştırmak için bu konuma işaret eder. 

**G**. Başlangıç görevleri, rol modeli tarafından tanımlanır ve WaHostBootstrapper tarafından başlatılır. Başlangıç görevleri arka planda zaman uyumsuz olarak çalışacak şekilde yapılandırılabilir ve konak önyükleyici başlangıç görevini başlatır ve sonra diğer başlangıç görevlerinde devam eder. Başlangıç görevleri Ayrıca, ana bilgisayar önyükleyicisinin başlangıç görevinin çalışmasının bitmesini bekleneceği ve bir sonraki başlangıç görevine devam etmeden önce bir başarı (0) çıkış kodu döndürdüğü basit (varsayılan) modda çalışacak şekilde yapılandırılabilir.

**H**. Bu görevler SDK 'nın bir parçasıdır ve rolün hizmet tanımında (. csdef) eklentiler olarak tanımlanır. Başlangıç görevlerine genişletildiğinde, **diagnosticsagent** ve **Remoteaccessagent** , her biri bir normal ve bir **/blockstartup** parametresine sahip olan iki başlangıç görevi tanımladıklarından benzersizdir. Normal başlangıç görevi, rolün kendisi çalışırken arka planda çalıştırılabilmesi için arka plan başlangıç görevi olarak tanımlanır. WaHostBootstrapper, devam etmeden önce çıkış yapmanızı beklemeleri için, **/Blockstartup** başlangıç görevi basit bir başlangıç görevi olarak tanımlanır. **/Blockstartup** görevi, normal görevin başlatma işleminin bitmesini bekler ve sonra ana bilgisayar önyükleyicisinin devam etmesine izin verir. Bu işlem, yapılandırma ve RDP erişiminin rol işleme başlamadan önce yapılandırılabilmesini sağlayacak (Bu,/blockStartup göreviyle yapılır). Bu Ayrıca, konak önyükleyici başlangıç görevlerini tamamladıktan sonra, tanılama ve RDP erişiminin çalışmaya devam etmesine olanak tanır (Bu normal görev üzerinden yapılır).

**I**. WaWorkerHost, normal çalışan rollerinin Standart ana bilgisayar işlemidir. Bu konak işlemi, tüm rolün dll 'Lerini ve OnStart ve Run gibi giriş noktası kodunu barındırır.

**J**. WaWebHost, SDK 1,2 uyumlu Barındırılstable Web Core (HWC) kullanmak üzere yapılandırıldıysa Web rolleri için Standart ana bilgisayar işlemidir. Roller, hizmet tanımından (. csdef) öğeyi kaldırarak HWC modunu etkinleştirebilir. Bu modda, tüm hizmetin kodu ve DLL 'Leri WaWebHost işleminden çalıştırılır. IIS (W3wp) kullanılmaz ve IIS, WaWebHost. exe içinde barındırıldığından IIS Yöneticisi 'nde yapılandırılmış hiçbir AppPool yok.

**K**. WaIISHost, tam IIS kullanan Web rollerinin rol giriş noktası kodu için ana bilgisayar işlemidir. Bu işlem **Roleentrypoint** sınıfını kullanan bulunan ilk dll 'yi yükler ve kodu bu sınıftan yürütür (OnStart, Run, OnStop). RoleEntryPoint sınıfında oluşturulan tüm **Roleenvironment** olayları (StatusCheck ve Changed gibi) Bu işlemde tetiklenir.

**L**. W3WP, rol tam IIS kullanmak üzere yapılandırıldıysa kullanılan standart IIS çalışan işlemidir. Bu, IISConfigurator 'dan yapılandırılan AppPool 'ı çalıştırır. Burada oluşturulan tüm RoleEnvironment olayları (StatusCheck ve Changed gibi) Bu işlemde oluşturulur. Her iki işlemde da olaylara abone olduğunuzda RoleEnvironment olaylarının her iki konumda (WaIISHost ve W3wp. exe) tetikleneceği unutulmamalıdır.

## <a name="workflow-processes"></a>İş akışı işlemi

1. Bir Kullanıcı, ". cspkg" ve ". cscfg" dosyalarını karşıya yüklemek, bir kaynağa bir yapılandırma değişikliğini durdurmasına veya bir yapılandırma değişikliği yapmasına, vb. gibi bir istek yapar. Bu işlem, Visual Studio Publish özelliği gibi Hizmet Yönetim API'si kullanan Azure portal veya bir araç aracılığıyla yapılabilir. Bu istek, abonelikle ilgili tüm işleri yapmak için RDFE 'ye gider ve sonra isteği FFE ile iletişim kurar. Bu iş akışı adımlarının geri kalanı yeni bir paket dağıtmaktır ve başlatılır.
2. FFE, doğru makine havuzunu bulur (benzeşim grubu veya coğrafi konum, dokudan makine kullanılabilirliği gibi) ve bu makine havuzundaki ana doku denetleyicisiyle iletişim kurar.
3. Yapı denetleyicisi, kullanılabilir CPU çekirdekleri olan bir konak bulur (veya yeni bir konak çalıştırır). Hizmet paketi ve yapılandırma konağa kopyalanır ve yapı denetleyicisi, paketi dağıtmak için ana bilgisayar IŞLETIM sistemindeki ana bilgisayar aracısıyla iletişim kurar (DIP 'Ler, bağlantı noktaları, Konuk işletim sistemi vb.).
4. Konak Aracısı Konuk işletim sistemini başlatır ve konuk aracısıyla iletişim kurar (WindowsAzureGuestAgent). Konak, rolün hedef durumuna doğru çalıştığından emin olmak için konuğa sinyal gönderir.
5. WindowsAzureGuestAgent Konuk işletim sistemini (güvenlik duvarı, ACL 'Ler, LocalStorage vb.) ayarlar, yeni bir XML yapılandırma dosyasını c:\Config dosyasına kopyalar ve ardından WaHostBootstrapper işlemini başlatır.
6. Tam IIS Web rolleri için, WaHostBootstrapper IISConfigurator 'ı başlatır ve web rolü için mevcut tüm uygulama havuzlarını IIS 'den silmesini söyler.
7. WaHostBootstrapper, E:\RoleModel.xml konumundan **Başlangıç** görevlerini okur ve başlangıç görevlerini yürütmeye başlar. WaHostBootstrapper, tüm basit başlangıç görevlerinin bitmesini bekler ve bir "başarılı" iletisi döndürene kadar bekler.
8. Tam IIS Web rolleri için wahostbootstrapper, iisconfigurator 'ın IIS AppPool 'ı yapılandırmasını ve siteyi `E:\Sitesroot\<index>` `<index>` ' a işaret ettiğini, hizmet için tanımlanan `<Sites>` öğe sayısına 0 tabanlı bir dizin olduğunu söyler.
9. WaHostBootstrapper, rol türüne göre ana bilgisayar işlemini başlatacak:
    1. **Çalışan rolü**: WaWorkerHost. exe başlatılır. WaHostBootstrapper, OnStart () yöntemini yürütür. Başlatıldıktan sonra, WaHostBootstrapper Run () yöntemini yürütmeye başlar ve sonra aynı anda rolü hazırlayın ve yük dengeleyici dönüşe koyar (ınputendpoints tanımlanmışsa). Wahostbootsımber daha sonra rol durumunu denetleme döngüsüne gider.
    1. **SDK 1,2 HWC web rolü**: WaWebHost başlatıldı. WaHostBootstrapper, OnStart () yöntemini yürütür. Çağrıldıktan sonra, WaHostBootstrapper Run () yöntemini yürütmeye başlar ve sonra aynı anda rolü, yük dengeleyici döngüsüne koyar. Wawebhost bir ısınma isteği (Get/do.exe. rd_runtime_init) yayınlar. Tüm Web istekleri WaWebHost. exe ' ye gönderilir. Wahostbootsımber daha sonra rol durumunu denetleme döngüsüne gider.
    1. **Tam IIS Web rolü**: aiishost başlatıldı. WaHostBootstrapper, OnStart () yöntemini yürütür. Çağrıldıktan sonra, Run () yöntemini yürütmeye başlar ve sonra aynı anda rolü hazırlayın ve yük dengeleyici dönüşe koyar. Wahostbootsımber daha sonra rol durumunu denetleme döngüsüne gider.
10. Tam bir IIS Web rolüne gelen Web istekleri, IIS 'yi W3WP işlemini başlatacak ve isteğe bağlı bir IIS ortamında olduğu gibi isteği sunacak şekilde tetikler.

## <a name="log-file-locations"></a>Günlük dosyası konumları

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Bu günlük başlatılır, duraklar ve yeni yapılandırma dahil olmak üzere hizmette yapılan değişiklikleri içerir. Hizmet değişmezse, bu günlük dosyasında büyük bir zaman boşlukları görmeyi bekleyebilir.
- C:\logs\waappagent.log  
Bu günlük durum güncelleştirmelerini ve sinyal bildirimlerini içerir ve her 2-3 saniyede bir güncelleştirilir.  Bu günlük, örneğin durumunun geçmiş bir görünümünü içerir ve örneğin, örnek ne zaman hazırlandıysa size bildirir.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**Waııshost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**Iısconfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS günlükleri**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows olay günlükleri**

`D:\Windows\System32\Winevt\Logs`
 



