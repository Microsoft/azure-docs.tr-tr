---
title: İşletim sistemi ve çalışma zamanı düzeltme temposunda
description: Azure App Service işletim sistemi ve çalışma zamanlarını, uygulamalarınızın hangi çalışma zamanlarını ve düzeltme eki düzeyini ve güncelleştirme duyurularını nasıl alabileceğinizi öğrenin.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8b876760ee2bafc855345878a28c38ec3b35daad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047890"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Azure App Service işletim sistemi ve çalışma zamanı düzeltme eki uygulama

Bu makalede, [App Service](overview.md)işletim sistemi veya yazılımla ilgili belirli sürüm bilgilerini nasıl alacağınız gösterilmektedir. 

App Service, işletim sistemi ve uygulama yığınının Azure tarafından yönetilebilmesi anlamına gelen bir hizmet olarak platform; yalnızca uygulamanızı ve verilerini yönetirsiniz. [Azure sanal makinelerde](../virtual-machines/index.yml)işletim sistemi ve uygulama yığını üzerinde daha fazla denetim bulabilirsiniz. Göz önünde bulundurularak, bunun gibi daha fazla bilgi edinmek için App Service bir kullanıcı olarak yararlı olur.

-   İşletim sistemi güncelleştirmeleri nasıl ve ne zaman uygulanır?
-   App Service önemli güvenlik açıklarına karşı Düzeltme Eki (sıfır gün gibi) nedir?
-   Uygulamalarınızı hangi işletim sistemi ve çalışma zamanı sürümlerini çalıştırıyor?

Güvenlik nedenleriyle, güvenlik bilgilerinin belirli özellikleri yayımlanmaz. Bununla birlikte, bu makale, süreçte saydamlığı en üst düzeye çıkarmakla ve güvenlikle ilgili duyurular veya çalışma zamanı güncelleştirmelerinde güncel kalabilmeniz için amaçlar makalesine sahiptir.

## <a name="how-and-when-are-os-updates-applied"></a>İşletim sistemi güncelleştirmeleri nasıl ve ne zaman uygulanır?

Azure, App Service kaynaklarını çalıştıran fiziksel sunucular ve konuk sanal makineler (VM) olmak üzere iki düzeyde işletim sistemi düzeltme eki uygulamayı yönetir. Her ikisi de aylık olarak güncelleştirilir. Bu, aylık [Düzeltme Eki Salı](/security-updates/) zamanlamaya göre hizalanır. Bu güncelleştirmeler, Azure hizmetlerinin yüksek kullanılabilirlik SLA 'sını garanti eden bir şekilde otomatik olarak uygulanır. 

Güncelleştirmelerin nasıl uygulandığı hakkında ayrıntılı bilgi için bkz. [Demystifying The Magic in app SERVICE OS Updates](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Azure önemli güvenlik açıklarına nasıl yöneliktir?

Önemli güvenlik açıkları, [sıfır günlük güvenlik açıkları](https://wikipedia.org/wiki/Zero-day_(computing))gibi anında düzeltme eki gerektirdiğinde, yüksek öncelikli güncelleştirmeler bir servis talebine göre işlenir.

[Azure Güvenlik blogu](https://azure.microsoft.com/blog/topics/security/)'nı ziyaret ederek Azure 'daki kritik güvenlik duyuruları ile güncel kalın. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Desteklenen dil çalışma zamanları ne zaman güncellenir, eklendi veya kullanım dışı?

Desteklenen dil çalışma zamanlarının (ana, ikincil veya düzeltme ekinin) yeni kararlı sürümleri düzenli aralıklarla App Service örneklerine eklenir. Bazı güncelleştirmeler var olan yüklemenin üzerine yazılır, diğerleri de mevcut sürümlere göre yan yana yüklenir. Üzerine yazma yüklemesi, uygulamanızın güncelleştirilmiş çalışma zamanında otomatik olarak çalışacağı anlamına gelir. Yan yana yükleme, yeni bir çalışma zamanı sürümünden yararlanmak için uygulamanızı el ile geçirmeniz gereken anlamına gelir. Daha fazla bilgi için alt bölümleri inceleyin.

Çalışma zamanı güncelleştirmeleri ve kullanım dışı bırakılıklar buradan duyurulur:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Buradaki bilgiler App Service bir uygulamada yerleşik olarak bulunan dil çalışma zamanları için geçerlidir. App Service için karşıya yüklediğiniz özel bir çalışma zamanı, örneğin, el ile yükseltmediğiniz sürece değişmeden kalır.
>
>

### <a name="new-patch-updates"></a>Yeni düzeltme eki güncelleştirmeleri

.NET, PHP, Java SDK veya Tomcat sürümüne yönelik düzeltme eki güncelleştirmeleri, mevcut yüklemenin en son sürümü üzerine yazılarak otomatik olarak uygulanır. Node.js Patch güncelleştirmeleri, var olan sürümler (bir sonraki bölümde yer aldığı birincil ve ikincil sürümlere benzer) ile yan yana yüklenir. Yeni Python düzeltme eki sürümleri, yerleşik Python yüklemeleri ile yan yana [site uzantıları](https://azure.microsoft.com/blog/azure-web-sites-extensions/)üzerinden el ile yüklenebilir.

### <a name="new-major-and-minor-versions"></a>Yeni birincil ve ikincil sürümler

Yeni bir ana veya ikincil sürüm eklendiğinde, mevcut sürümlere göre yan yana yüklenir. Uygulamanızı yeni sürüme el ile yükseltebilirsiniz. Çalışma zamanı sürümünü bir yapılandırma dosyasında ( `web.config` ve gibi `package.json` ) yapılandırdıysanız, aynı yöntemle yükseltmeniz gerekir. Çalışma zamanı sürümünüzü yapılandırmak için bir App Service ayarı kullandıysanız, aşağıdaki örneklerde gösterildiği gibi, bunu [Azure Portal](https://portal.azure.com) veya [Cloud Shell](../cloud-shell/overview.md)bir [Azure CLI](/cli/azure/get-started-with-azure-cli) komutu çalıştırarak değiştirebilirsiniz:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.8 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Kullanım dışı sürümler  

Eski bir sürüm kullanım dışı olduğunda, çalışma zamanı sürüm yükseltmenizi uygun şekilde planlayabilmeniz için kaldırma tarihi duyurulur. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Örneklerim üzerinde işletim sistemi ve çalışma zamanı güncelleştirme durumunu nasıl sorgularım?  

Kritik IŞLETIM sistemi bilgileri Access 'ten kilitlenirken (bkz. [Azure App Service işletim sistemi işlevselliği](operating-system-functionality.md)), [kudu konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) , işletim sistemi sürümü ve çalışma zamanı sürümleriyle ilgili App Service örneğinizi sorgulamanızı sağlar. 

Aşağıdaki tabloda, uygulamalarınızı çalıştıran Windows ve dil çalışma zamanının sürümlerinin nasıl yapılacağı gösterilmektedir:

| Bilgi | Nerede bulunacak | 
|-|-|
| Windows sürümü | Bkz. `https://<appname>.scm.azurewebsites.net/Env.cshtml` (sistem bilgisi altında) |
| .NET sürümü | `https://<appname>.scm.azurewebsites.net/DebugConsole`' De, komut isteminde aşağıdaki komutu çalıştırın: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| .NET Core sürümü | `https://<appname>.scm.azurewebsites.net/DebugConsole`' De, komut isteminde aşağıdaki komutu çalıştırın: <br> `dotnet --version` |
| PHP sürümü | `https://<appname>.scm.azurewebsites.net/DebugConsole`' De, komut isteminde aşağıdaki komutu çalıştırın: <br> `php --version` |
| Varsayılan Node.js sürümü | [Cloud Shell](../cloud-shell/overview.md), aşağıdaki komutu çalıştırın: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python sürümü | `https://<appname>.scm.azurewebsites.net/DebugConsole`' De, komut isteminde aşağıdaki komutu çalıştırın: <br> `python --version` |  
| Java sürümü | `https://<appname>.scm.azurewebsites.net/DebugConsole`' De, komut isteminde aşağıdaki komutu çalıştırın: <br> `java -version` |  

> [!NOTE]  
> `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages` ["KB" yamaları](/security-updates/SecurityBulletins/securitybulletins) üzerindeki bilgilerin depolandığı kayıt defteri konumuna erişim, kilitlenir.
>
>

## <a name="more-resources"></a>Diğer kaynaklar

[Güven Merkezi: güvenlik](https://www.microsoft.com/en-us/trustcenter/security)  
[Azure App Service üzerinde 64 bit ASP.NET Core](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
