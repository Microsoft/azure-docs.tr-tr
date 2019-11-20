---
title: Yerel önbelleğe genel bakış-Azure App Service | Microsoft Docs
description: Bu makalede, yerel önbellek özelliğinin Azure App Service durumunu etkinleştirme, yeniden boyutlandırma ve sorgulama açıklanmaktadır
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4dffa7dcafe4aabe3e8dcb56d4f5084d0c6ef821
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819673"
---
# <a name="azure-app-service-local-cache-overview"></a>Azure App Service yerel önbelleğe genel bakış

> [!NOTE]
> Yerel önbellek, [Linux üzerinde App Service](containers/app-service-linux-intro.md)gibi işlev uygulamalarında veya kapsayıcılı App Service uygulamalarda desteklenmez.


Azure App Service içerik Azure Storage 'da depolanır ve bir içerik paylaşımında dayanıklı bir biçimde ortaya çıkmış olur. Bu tasarımın çeşitli uygulamalarla çalışması amaçlanmıştır ve aşağıdaki özniteliklere sahiptir:  

* İçerik, uygulamanın birden çok sanal makine (VM) örneği arasında paylaşılır.
* İçerik dayanıklı olabilir ve uygulamalar çalıştırılarak değiştirilebilir.
* Günlük dosyaları ve tanılama veri dosyaları aynı paylaşılan içerik klasörü altında bulunabilir.
* Yeni içerik yayımlandığında içerik klasörü doğrudan güncelleştirilir. Aynı içeriği SCM Web sitesi ve çalışan uygulama aracılığıyla hemen görüntüleyebilirsiniz (genellikle ASP.NET gibi bazı teknolojiler, en son içeriği almak için bazı dosya değişikliklerinde uygulama yeniden başlatması başlatır).

Birçok uygulama bu özelliklerden birini veya tümünü kullanırken, bazı uygulamalar yalnızca yüksek kullanılabilirliğe sahip olan yüksek performanslı, salt okunurdur bir içerik deposuna ihtiyaç duyar. Bu uygulamalar, belirli bir yerel önbelleğin bir VM örneğinden yararlanabilir.

Azure App Service yerel önbellek özelliği, içeriğinizin bir Web rolü görünümünü sağlar. Bu içerik, zaman uyumsuz olarak site başlatması oluşturulan depolama içeriğinizin yazma-atma önbelleğidir. Önbellek hazırsa, site önbelleğe alınan içeriğe karşı çalışmaya geçiş yapılır. Yerel önbellekte çalışan uygulamalar aşağıdaki avantajlara sahiptir:

* Bunlar, Azure depolama 'daki içeriğe erişirken oluşan gecikmelerin dengeskalarına göre yapılır.
* Bunlar, planlı yükseltmelere veya planlanmamış aşağı saatlere ve içerik paylaşımında sunan sunucularda gerçekleşen Azure depolama ile ilgili diğer kesintilere karşı bir şekilde etkilenmez.
* Depolama paylaşımının değişikliklerinden dolayı daha az uygulama yeniden başlatmaları vardır.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Yerel önbelleğin App Service davranışını nasıl değiştirdiği
* _D:\home_ , uygulama başlatıldığında VM örneğinde oluşturulan yerel önbelleğe işaret eder. _D:\Local_ , VM 'ye özgü geçici depolamaya işaret etmeye devam ediyor.
* Yerel önbellek, sırasıyla _D:\home\site_ ve _D:\home\siteextensions_konumundaki paylaşılan içerik deposunun _/site_ ve _/siteextensions_ klasörlerinin tek seferlik bir kopyasını içerir. Dosyalar, uygulama başlatıldığında yerel önbelleğe kopyalanır. Her bir uygulama için iki klasörün boyutu varsayılan olarak 300 MB ile sınırlıdır, ancak bunu 2 GB 'a kadar artırabilirsiniz.
* Yerel önbellek okuma-yazma ' dır. Ancak, uygulama sanal makineleri taşıdığında veya yeniden başlatıldığında herhangi bir değişiklik atılır. Görev açısından kritik verileri içerik deposunda depolayan uygulamalar için yerel önbellek kullanmayın.
* _D:\home\logfiles_ ve _d:\home\data_ günlük dosyalarını ve uygulama verilerini içerir. İki alt klasör, sanal makine örneğinde yerel olarak depolanır ve paylaşılan içerik deposuna düzenli olarak kopyalanır. Uygulamalar, günlük dosyalarını ve verileri bu klasörlere yazarak kalıcı hale getirebilirler. Ancak, paylaşılan içerik deposuna yapılan kopya en iyi çaba olduğundan, bir VM örneğinin ani kilitlenmesi nedeniyle günlük dosyaları ve verilerin kaybedilmesi mümkündür.
* [Günlük akışı](troubleshoot-diagnostic-logs.md#stream-logs) , en iyi çaba kopyasından etkilenir. Akışlı günlüklerde bir dakikalık gecikmeye kadar gözlemleyebilirsiniz.
* Paylaşılan içerik deposunda, yerel önbelleği kullanan uygulamalar için _LogFiles_ ve _veri_ klasörlerinin klasör yapısında bir değişiklik vardır. Burada "benzersiz tanımlayıcı" ve zaman damgasının adlandırma modelini izleyen alt klasörler vardır. Alt klasörlerin her biri, uygulamanın çalıştığı veya çalıştırıldığı bir VM örneğine karşılık gelir.
* _D:\home_ 'taki diğer klasörler yerel önbellekte kalır ve paylaşılan içerik deposuna kopyalanmaz.
* Desteklenen herhangi bir yöntem aracılığıyla uygulama dağıtımı, doğrudan kalıcı paylaşılan içerik deposuna yayınlar. Yerel önbellekteki _D:\home\site_ ve _D:\home\siteextensions_ klasörlerini yenilemek için uygulamanın yeniden başlatılması gerekiyor. Yaşam döngüsünü sorunsuz hale getirmek için bu makalenin ilerleyen kısımlarında bulunan bilgilere bakın.
* SCM sitesinin varsayılan içerik görünümü, paylaşılan içerik deposundan olmaya devam eder.

## <a name="enable-local-cache-in-app-service"></a>App Service 'de yerel önbelleği etkinleştir
Yerel önbelleği, ayrılmış uygulama ayarları birleşimini kullanarak yapılandırırsınız. Aşağıdaki yöntemleri kullanarak bu uygulama ayarlarını yapılandırabilirsiniz:

* [Azure portalda](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Azure portal kullanarak yerel önbelleği yapılandırma
<a name="Configure-Local-Cache-Portal"></a>

Bu uygulama ayarını kullanarak Web uygulaması başına temelinde yerel önbelleği etkinleştirin: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure portal App Settings: yerel önbellek](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Azure Resource Manager kullanarak yerel önbelleği yapılandırma
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Yerel önbellekteki boyut ayarını değiştir
Varsayılan olarak, yerel önbellek boyutu **1 GB**'tır. Bu, içerik deposundan kopyalanmış olan/site ve/siteextensions klasörlerinin yanı sıra yerel olarak oluşturulan tüm Günlükler ve veri klasörlerini içerir. Bu sınırı artırmak için `WEBSITE_LOCAL_CACHE_SIZEINMB`uygulama ayarını kullanın. Uygulama başına en fazla **2 GB** (2000 MB) boyutunu artırabilirsiniz.

## <a name="best-practices-for-using-app-service-local-cache"></a>App Service yerel önbellek kullanmaya yönelik en iyi uygulamalar
Yerel önbelleği, [hazırlama ortamları](../app-service/deploy-staging-slots.md) özelliğiyle birlikte kullanmanızı öneririz.

* `WEBSITE_LOCAL_CACHE_OPTION` *yapışkan* uygulama ayarı `Always` değerini **Üretim** yuvasıyla ekleyin. `WEBSITE_LOCAL_CACHE_SIZEINMB`kullanıyorsanız, üretim yuvasına yapışkan bir ayar olarak da ekleyin.
* **Hazırlama** yuvası oluşturun ve hazırlama yuvalamanıza yayımlayın. Hazırlama yuvasını, üretim yuvası için yerel önbelleğin avantajlarından yararlanmanız durumunda, hazırlama için sorunsuz bir yapı-dağıtma-test yaşam döngüsünü etkinleştirmek üzere yerel önbellek kullanacak şekilde ayarlayamazsınız.
* Sitenizi hazırlama yuvasıyla test edin.  
* Hazırsanız, hazırlama ve üretim yuvalarınız arasında bir [değiştirme işlemi](../app-service/deploy-staging-slots.md#Swap) yapın.  
* Yapışkan ayarlar bir yuvaya bir yuva adı ve yapışkan olarak verilebilir. Bu nedenle, hazırlama yuvası üretime takas edildiğinde yerel önbellek uygulama ayarlarını devralır. Yeni takas edilen üretim yuvası, birkaç dakika sonra yerel önbellekte çalışır ve değiştirme sonrasında yuva ısınma bir parçası olarak görünür olur. Bu nedenle, yuva takası tamamlandığında, üretim yuva yerel önbellekte çalışır.

## <a name="frequently-asked-questions-faq"></a>Sık sorulan sorular (SSS)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Yerel önbelleğin uygulamam için geçerli olup olmadığını nasıl anlayabilirim?
Uygulamanızın yüksek performanslı, güvenilir bir içerik deposu olması gerekiyorsa, çalışma zamanında kritik verileri yazmak için içerik deposunu kullanmaz ve toplam boyutta 2 GB 'den küçükse yanıt "Evet" olur! /SITE ve/siteextensions klasörlerinizin toplam boyutunu almak için, "Azure Web Apps disk kullanımı" site uzantısını kullanabilirsiniz.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Sitemin yerel önbellek kullanmaya geçmiş olup olmadığını nasıl anlayabilirim?
Yerel önbellek özelliğini hazırlama ortamları ile kullanıyorsanız, yerel önbellek çarpana kadar değiştirme işlemi tamamlanmaz. Sitenizin yerel önbelleğe karşı çalışıp çalışmadığını denetlemek için, `WEBSITE_LOCALCACHE_READY`çalışan işlem ortamı değişkenini kontrol edebilirsiniz. Çalışan işlem ortamı değişkenine birden çok örnek üzerinde erişmek için [çalışan işlem ortamı değişkeni](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) sayfasındaki yönergeleri kullanın.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Yeni değişiklikler yayımladım, ancak Uygulamam bunlara sahip görünmüyor. Neden?
Uygulamanız yerel önbellek kullanıyorsa, en son değişiklikleri almak için sitenizi yeniden başlatmanız gerekir. Bir üretim sitesindeki değişiklikleri yayınlamak istemiyor musunuz? Önceki en iyi yöntemler bölümündeki yuva seçeneklerine bakın.

### <a name="where-are-my-logs"></a>Günlüklerim nerede?
Yerel önbellek ile günlüklerinizi ve veri klasörlerinizi biraz farklı bir şekilde görünür. Ancak, alt klasörlerin yapısı aynı kalır, ancak alt klasörler "benzersiz VM tanımlayıcısı" ve zaman damgası biçiminde bir alt klasör altına alınır.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Yerel Önbelleğim etkin, ancak Uygulamam hala yeniden başlatıldı. Neden? Sık kullanılan uygulama yeniden başlatmalarının bulunduğu yerel önbellek hakkında yardım ediyorum.
Yerel önbellek, depolamayla ilgili uygulama yeniden başlatmalarının engellenmesine yardımcı olur. Bununla birlikte, uygulamanız hala sanal makinenin planlanan altyapı yükseltmeleri sırasında yeniden başlatmaları devam edebilir. Yerel önbellek etkinken karşılaşabileceğiniz genel uygulama yeniden başlatmaları daha az olmalıdır.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Yerel önbellek, tüm dizinlerin daha hızlı yerel sürücüye kopyalanmasını mi dışlıyor?
Depolama içeriğini kopyalayan adımın bir parçası olarak, depo adlı herhangi bir klasör hariç tutulur. Bu, site içeriğiniz uygulamanın gün için günlük işlem için gerekli olmayan bir kaynak denetimi deposu içerebilen senaryolara yardımcı olur. 
