---
title: Node. js için en iyi uygulamalar ve sorun giderme-Azure App Service
description: Azure App Service üzerindeki düğüm uygulamaları için en iyi uygulamaları ve sorun giderme adımlarını öğrenin.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: bwren
ms.custom: seodec18
ms.openlocfilehash: 5ef0cf691ae3a199ea82cb8cfa23c386d30551dc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024236"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Azure App Service Windows 'da düğüm uygulamaları için en iyi uygulamalar ve sorun giderme kılavuzu

Bu makalede, Azure App Service ( [ıısnode](https://github.com/azure/iisnode)ile) üzerinde çalışan [düğüm uygulamaları](app-service-web-get-started-nodejs.md) için en iyi uygulamaları ve sorun giderme adımlarını öğreneceksiniz.

> [!WARNING]
> Üretim sitenizde sorun giderme adımlarını kullanırken dikkatli olun. Öneri, üretim dışı bir kurulumda (örneğin, hazırlama yuvanız) ve sorun düzeltildiğinde, hazırlama yuvalarınızı üretim yuvasıyla değiştirme konusunda uygulamanızı sorun gidermeye yöneliktir.
>

## <a name="iisnode-configuration"></a>IıSNODE yapılandırması

Bu [şema dosyası](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) , ıısnode için yapılandırabileceğiniz tüm ayarları gösterir. Uygulamanız için yararlı olan ayarlardan bazıları:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Bu ayar, IIS uygulaması başına başlatılan düğüm işlemlerinin sayısını denetler. Varsayılan değer 1'dir. Değeri 0 olarak değiştirerek VM vCPU sayısı olarak çok sayıda Node. exe ' de başlatabilirsiniz. Makinenizde tüm vCPU 'Ları kullanabilmeniz için önerilen değer çoğu uygulama için 0 ' dır. Node. exe tek iş parçacıklı olduğundan, tek bir Node. exe en fazla 1 vCPU tüketir. Düğüm uygulamanızdan en yüksek performansı almak için tüm vCPU 'Ları kullanmak istersiniz.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Bu ayar Node. exe ' nin yolunu denetler. Bu değeri, Node. exe sürümünüzü gösterecek şekilde ayarlayabilirsiniz.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Bu ayar, ıısnode tarafından her Node. exe ' ye gönderilen en fazla eşzamanlı istek sayısını denetler. Azure App Service, varsayılan değer sonsuz ' dur. Uygulamanızı ne kadar istek alacağını ve uygulamanızın her bir isteği işleme hızını ve ne kadar hızlı bir şekilde bu değeri yapılandırabilirsiniz.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Bu ayar, ıısnode 'un isteği Node. exe ' ye göndermesini sağlamak için, adlandırılmış kanalda bağlantı kuran en fazla kaç kez yeniden deneme olduğunu denetler. NamedPipeConnectionRetryDelay ile birlikte bu ayar, ıısnode içindeki her bir isteğin toplam zaman aşımını belirler. Varsayılan değer Azure App Service 200 ' dir. Saniye cinsinden toplam zaman aşımı = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Bu ayar, isteği adlandırılan kanal üzerinden Node. exe ' ye göndermek için her yeniden deneme arasında geçen süreyi (MS cinsinden) denetler. Varsayılan değer 250 MS 'dir.
Saniye cinsinden toplam zaman aşımı = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

Varsayılan olarak, Azure App Service ıısnode 'daki toplam zaman aşımı süresi 200 \* 250 ms = 50 saniyedir.

### <a name="logdirectory"></a>Günlüğe kaydetme dizini

Bu ayar ıısnode 'un stdout/stderr kaydettiği dizini denetler. Varsayılan değer, ana komut dosyası dizini (Main Server. js ' nin bulunduğu dizin) ile ilişkili iisnode ' dır.

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Bu ayar, düğüm uygulamanızda hata ayıklarken ıısnode 'un hangi node-Inspector sürümünün kullandığını denetler. Şu anda, bu ayar için yalnızca iki geçerli değer olan iisnode-inspector-0.7.3. dll ve iisnode-inspector. dll vardır. Varsayılan değer iisnode-inspector-0.7.3. dll ' dir. İisnode-inspector-0.7.3. dll sürümü node-Inspector-0.7.3 kullanır ve Web yuvalarını kullanır. Azure WebApp 'de Web yuvalarını bu sürümü kullanacak şekilde etkinleştirin. Yeni node-Inspector 'ı kullanmak için iisnode 'un nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz. <https://ranjithblogs.azurewebsites.net/?p=98>.

### <a name="flushresponse"></a>flushResponse

IIS 'nin varsayılan davranışı, yeniden denemeden önce veya yanıtın sonuna kadar, ilk kez geldiği sürece 4 MB 'a kadar veri yanıtı arabelleğe alır. ıısnode, bu davranışı geçersiz kılmak için bir yapılandırma ayarı sunar: ıısnode onu Node. exe ' den aldığı anda yanıt varlık gövdesinin bir parçasını temizlemek için, Web. config dosyasında iisnode/@flushResponse özniteliğini ' true ' olarak ayarlamanız gerekir:

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Yanıt varlık gövdesinin her parçasının temizlenme özelliğini etkinleştirmek, sistemin aktarım hızını %5 oranında azaltan (v 0.1.13 itibariyle) performans yükü ekler. Bu ayarı yalnızca yanıt akışı gerektiren uç noktalar için (örneğin, Web. config içinde `<location>` öğesini kullanarak) en iyi şekilde kullanın

Buna ek olarak, akış uygulamaları için, ıısnode işleyicinizin responseBufferLimit öğesini de 0 olarak ayarlamanız gerekir.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Değişiklikler için izlenen dosyaların noktalı virgülle ayrılmış listesi. Bir dosyada yapılan herhangi bir değişiklik uygulamanın geri dönüşüm yapmasına neden olur. Her giriş, bir isteğe bağlı dizin adından ve gerekli bir dosya adından oluşur ve bu, ana uygulama giriş noktasının bulunduğu dizine göre belirlenir. Yalnızca dosya adı bölümünde joker karakterler kullanılabilir. Varsayılan değer `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Varsayılan değer false'tur. Etkinleştirilirse, düğüm uygulamanız adlandırılmış bir kanala bağlanarak (ııSNODE\_DENETIM\_kanal) ve "geri dönüşüm" iletisi gönderebilir. Bu, W3wp 'in düzgün şekilde geri dönüştürülmesine neden olur.

### <a name="idlepageouttimeperiod"></a>ıdlepageouttimeperiod

Varsayılan değer 0 ' dır, bu özelliğin devre dışı bırakıldığı anlamına gelir. 0 ' dan büyük bir değere ayarlandığında, ıısnode her ' ıdlepageouttimeperiod ' öğesinin her bir alt işlemini milisaniye olarak bir sayfa olarak çıkar. Sayfa hangi anlama geldiğini anlamak için [belgelere](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) bakın. Bu ayar, yüksek miktarda bellek kullanan uygulamalar için yararlıdır ve RAM 'i boşaltmak için zaman içinde belleği diske eklemek ister.

> [!WARNING]
> Üretim uygulamalarında aşağıdaki yapılandırma ayarlarını etkinleştirirken dikkatli olun. Bu öneri, canlı üretim uygulamalarında etkinleştirilmemelidir.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Varsayılan değer false'tur. True olarak ayarlanırsa, ıısnode, `iisnode-debug` üst bilgi değeri bir URL olduğu her HTTP yanıtına bir HTTP yanıt üst bilgisi `iisnode-debug` ekler. Tek tek tanılama bilgileri parçaları URL parçasına bakarak elde edilebilir, ancak URL bir tarayıcıda açılarak görselleştirme kullanılabilir.

### <a name="loggingenabled"></a>loggingEnabled

Bu ayar, ıisnode tarafından stdout ve stderr 'in günlüğe kaydedilmesini denetler. Iısnode, başlatılan düğüm işlemlerinden stdout/stderr yakalar ve ' logDirectory ' ayarında belirtilen dizine yazar. Bu etkinleştirildikten sonra uygulamanız, günlükleri dosya sistemine yazar ve uygulama tarafından gerçekleştirilen günlüğe kaydetme miktarına bağlı olarak performans olumsuz etkileri olabilir.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Varsayılan değer false'tur. True olarak ayarlandığında, ıısnode, tarayıcınızda HTTP durum kodunu ve Win32 hata kodunu görüntüler. Win32 kodu, bazı sorun türlerinde hata ayıklamaya yardımcı olur.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>hata ayıklama Ggingenabled (canlı üretim sitesinde etkinleştirmeyin)

Bu ayar hata ayıklama özelliğini denetler. Iısnode, Node-Inspector ile tümleşiktir. Bu ayarı etkinleştirdiğinizde, düğüm uygulamanızın hata ayıklamasını etkinleştirirsiniz. Iısnode, bu ayar etkinleştirildikten sonra, düğüm uygulamanıza ilk hata ayıklama isteğindeki ' debuggerVirtualDir ' dizininde düğüm denetçisi dosyaları oluşturur. `http://yoursite/server.js/debug`bir istek göndererek düğüm denetçisini yükleyebilirsiniz. Hata ayıklama URL segmentini ' debuggerPathSegment ' ayarıyla kontrol edebilirsiniz. Varsayılan olarak debuggerPathSegment = ' Debug '. Bir GUID 'ye `debuggerPathSegment` ayarlayabilirsiniz, örneğin, başkaları tarafından keşfedilmesi daha zordur.

Hata ayıklama hakkında daha fazla bilgi için [Windows üzerinde Debug Node. js uygulamalarını](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) okuyun.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Senaryolar ve öneriler/sorun giderme

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Düğüm Uygulamam çok fazla giden çağrı yapıyor

Birçok uygulama, normal işleminin bir parçası olarak giden bağlantılar yapmak istiyor. Örneğin, bir istek geldiğinde, düğüm uygulamanız başka bir REST API ile iletişim kurmak ve isteği işlemek için bazı bilgiler almak istiyor. Http veya https çağrıları yaparken canlı tut aracısını kullanmak istersiniz. Agentkeepalive modülünü, bu giden çağrıları yaparken canlı tut aracılarınız olarak kullanabilirsiniz.

Agentkeepalive modülü, Azure WebApp sanal makinenizde yuvaların yeniden kullanılmasını sağlar. Her giden istek üzerinde yeni bir yuva oluşturmak uygulamanıza ek yük ekler. Uygulamanızın çıkış istekleri için yuvaları yeniden kullanımını, uygulamanızın VM başına ayrılan maxSockets değerini aşmamasını sağlar. Azure App Service öneri, agentKeepAlive maxSockets değerini, VM başına 40 olan Node. exe \* maxSockets/Instance) 160 yuva olarak ayarlamadır.

Örnek [Agentkeepalive](https://www.npmjs.com/package/agentkeepalive) yapılandırması:

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Bu örnek, VM 'niz üzerinde 4 Node. exe ' nin çalıştığını varsayar. VM 'de çalışan farklı sayıda Node. exe dosyası varsa, maxSockets ayarını buna uygun olarak değiştirmeniz gerekir.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Düğüm uygulamamın çok fazla CPU kullanıyor

Portalınızdaki Azure App Service, yüksek CPU tüketimi hakkında bir öneri alabilirsiniz. Ayrıca, belirli [ölçümleri](web-sites-monitor.md)izlemek için izleyicileri de ayarlayabilirsiniz. [Azure Portal PANOSUNDA](../azure-monitor/app/web-monitor-performance.md)CPU kullanımını denetlerken, en yüksek değerleri kaçırmamak için CPU 'nun en yüksek değerlerini denetleyin.
Uygulamanızın çok fazla CPU kullandığını düşünüyorsanız ve nedenini açıklayadıysanız, öğrenmek için düğüm uygulamanızın profilini oluşturabilirsiniz.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>V8-Profiler ile Azure App Service düğüm uygulamanızın profilini oluşturma

Örneğin, profil eklemek istediğiniz bir Merhaba Dünya uygulamasına şu şekilde bakalım:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Hata ayıklama konsolu sitesine gidin `https://yoursite.scm.azurewebsites.net/DebugConsole`

Sitenize/Wwwroot dizinine gidin. Aşağıdaki örnekte gösterildiği gibi bir komut istemi görürsünüz:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Komutunu çalıştırın `npm install v8-profiler`.

Bu komut, düğüm\_modüller dizini ve tüm bağımlılıklarını V8-Profiler ' ın altına yüklenir.
Şimdi, uygulamanızı profilinize yönelik Server. js ' yi düzenleyin.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Önceki kod, WriteConsoleLog işlevini profiller ve sonra profil çıkışını sitenizin Wwwroot altındaki ' profile. cpuprofile ' dosyasına yazar. Uygulamanıza bir istek gönderin. Siteniz Wwwroot altında oluşturulmuş bir ' profile. cpuprofile ' dosyası görürsünüz.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Bu dosyayı indirin ve Chrome F12 araçlarıyla açın. Chrome üzerinde F12 tuşuna basın, sonra **profiller** sekmesini seçin. **Yükle** düğmesini seçin. İndirdiğiniz profile. cpuprofile dosyanızı seçin. Az önce yüklediğiniz profile tıklayın.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Zamanın %95 ' nin WriteConsoleLog işlevi tarafından tüketildiğini görebilirsiniz. Çıktı Ayrıca, soruna neden olan tam satır numaralarını ve kaynak dosyalarını da gösterir.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Düğüm uygulamamın çok fazla bellek kullanıyor

Uygulamanız çok fazla bellek kullanıyorsa, portalınızdaki Azure App Service yüksek bellek tüketimi hakkında bir bildirim görürsünüz. Belirli [ölçümleri](web-sites-monitor.md)izlemek için izleyicileri ayarlayabilirsiniz. [Azure Portal panosunda](../azure-monitor/app/web-monitor-performance.md)bellek kullanımını denetlerken, en yüksek değerleri kaçırmamak için bellek için maksimum değerleri denetlediğinizden emin olun.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Node. js için sızıntı algılama ve yığın farkı

Bellek sızıntılarını belirlemenize yardımcı olması için [node-memwatch](https://github.com/lloyd/node-memwatch) kullanabilirsiniz.
V8-Profiler gibi `memwatch` yükleyebilir ve uygulamanızdaki bellek sızıntılarını belirlemek için kodunuzu yakalayıp fark edebilirsiniz.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>My Node. exe ' nin rastgele bir şekilde dolmak üzere

Node. exe ' nin rastgele kapatılmasının birkaç nedeni vardır:

1. Uygulamanız yakalanamayan özel durumlar üretiliyor – oluşturulan özel durum hakkındaki ayrıntılar için d:\\Home\\LogFiles\\uygulama\\Logging-Errors. txt dosyasını denetleyin. Bu dosya, uygulamanızı hata ayıklamaya ve düzeltmenize yardımcı olacak yığın izlemesine sahiptir.
2. Uygulamanız çok fazla bellek kullanıyor, bu da çalışmaya başlamadan başka süreçler etkiliyor. Toplam VM belleği %100 ' e yakınsa, Node. exe ' nin işlem yöneticisi tarafından sonlandırılabilir. İşlem Yöneticisi, diğer işlemlerin bazı işleri gerçekleştirme şansı almasına izin vermek için bazı işlemleri Kilter. Bu sorunu onarmak için uygulamanızın bellek sızıntıları için profilini yapın. Uygulamanız büyük miktarda bellek gerektiriyorsa, daha büyük bir VM 'ye kadar ölçeği büyütün (VM 'nin kullanabildiği RAM miktarı artar).

### <a name="my-node-application-does-not-start"></a>Düğüm Uygulamam başlamıyor

Uygulamanız başlatıldığında 500 hata döndürüyorsa, birkaç nedenden dolayı şunlar olabilir:

1. Node. exe doğru konumda yok. NodeProcessCommandLine ayarını denetleyin.
2. Ana betik dosyası doğru konumda yok. Web. config ' i denetleyin ve işleyiciler bölümündeki ana betik dosyasının adının ana betik dosyası ile eşleştiğinden emin olun.
3. Web. config yapılandırması doğru değil; ayarların adlarını/değerlerini denetleyin.
4. Soğuk başlangıç: uygulamanızın başlaması çok uzun sürüyor. Uygulamanız (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 saniye daha uzun sürüyorsa, ıısnode 500 hatası döndürür. Iısnode 'un zaman aşımına uğramasını ve 500 hatasını döndürmesini engellemek için bu ayarların değerlerini uygulamanızın başlangıç zamanına uyacak şekilde artırın.

### <a name="my-node-application-crashed"></a>Düğüm Uygulamam kilitlendi

Uygulamanız yakalanamayan özel durumlar üretiliyor – oluşturulan özel durum hakkındaki ayrıntılar için `d:\\home\\LogFiles\\Application\\logging-errors.txt` dosyasını denetleyin. Bu dosyada, uygulamanızı tanılamanıza ve gidermeye yardımcı olacak yığın izlemesi vardır.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Düğüm uygulamamın başlaması çok fazla zaman alıyor (soğuk başlatma)

Uzun uygulama başlangıç sürelerinin yaygın nedeni, düğüm\_modüllerinde çok sayıda dosya olabilir. Uygulama, başlatıldığında bu dosyaların çoğunu yüklemeye çalışır. Varsayılan olarak, dosyalarınız Azure App Service ağ paylaşımında depolandığından, çok sayıda dosyanın yüklenmesi zaman alabilir.
Bu işlemi daha hızlı hale getirmek için bazı çözümler şunlardır:

1. Modüllerinizi yüklemek için npm3 kullanarak düz bağımlılık yapısına sahip olduğunuzdan ve yinelenen bağımlılıklara sahip olduğunuzdan emin olun.
2. Node\_modüllerinizi geç yüklemeyi deneyin ve uygulamanın başlangıcında tüm modülleri yüklemez. Yavaş yükleme modülleri için, modül kodunun ilk yürütmeden önce işlev içinde modüle ihtiyacınız olduğunda, gerekli (' Module ') çağrısı yapılmalıdır.
3. Azure App Service, yerel önbellek adlı bir özellik sunar. Bu özellik, içeriğinizi ağ paylaşımından VM 'deki yerel diske kopyalar. Dosyalar yerel olduğundan, düğüm\_modüllerinin yükleme süresi çok daha hızlıdır.

## <a name="iisnode-http-status-and-substatus"></a>IıSNODE http durumu ve alt durumu

`cnodeconstants` [kaynak dosyası](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) , bir hata nedeniyle ıısnode 'un tüm olası durum/alt durum kombinasyonlarını döndürebilmesini sağlar.

Win32 hata kodunu görmek için uygulamanızın FREB 'sini etkinleştirin (performans nedenleriyle yalnızca üretim dışı sitelerde serbest bırak özelliğini etkinleştirdiğinizden emin olun).

| Http durumu | Http alt durumu | Olası neden? |
| --- | --- | --- |
| 500 |1000 |IISNODE 'a istek gönderilirken bir sorun oluştu: node. exe ' nin başlatılmış olup olmadığını denetleyin. Node. exe başlatıldığında kilitlendi. Web. config yapılandırmanızda hata olup olmadığını denetleyin. |
| 500 |1001 |-Win32Error 0x2-Uygulama URL 'ye yanıt vermiyor. URL yeniden yazma kurallarını denetleyin veya Express uygulamanızda doğru yolların tanımlanmış olup olmadığını denetleyin. -Win32Error 0x6d – adlandırılmış kanal meşgul: Kanal meşgul olduğu için Node. exe istekleri kabul etmiyor. Yüksek CPU kullanımını denetleyin. -Diğer hatalar – Node. exe ' nin kilitlendiğini denetleyin. |
| 500 |1002 |Node. exe kilitlendi – yığın izleme için d:\\Home\\LogFiles\\Logging-Errors. txt dosyasına bakın. |
| 500 |1003 |Kanal yapılandırma sorunu – adlandırılmış kanal yapılandırması yanlış. |
| 500 |1004-1018 |İstek gönderilirken veya Node. exe ' den gelen yanıtı işlerken bir hata oluştu. Node. exe ' nin kilitlendiğini denetleyin. yığın izleme için d:\\Home\\LogFiles\\Logging-Errors. txt dosyasına bakın. |
| 503 |1000 |Daha fazla adlandırılmış kanal bağlantısı ayırmak için yeterli bellek yok. Uygulamanızın neden çok fazla bellek tükettiğini kontrol edin. MaxConcurrentRequestsPerProcess ayar değerini denetleyin. Sonsuz değilse ve çok sayıda isteğiniz varsa, bu hatayı engellemek için bu değeri arttırın. |
| 503 |1001 |Uygulama geri dönüştürülüyor olduğundan Node. exe ' ye istek dağıtılamadı. Uygulama geri dönüştürüldükten sonra, istekler normal olarak sunulmalıdır. |
| 503 |1002 |Gerçek neden için Win32 hata kodunu denetle – Istek bir Node. exe ' ye dağıtılamadı. |
| 503 |1003 |Adlandırılmış kanal çok meşgul – Node. exe ' nin aşırı CPU kullanıp kullanmadığına emin olun |

NODE. exe `NODE_PENDING_PIPE_INSTANCES`adlı bir ayara sahiptir. Azure App Service, bu değer 5000 olarak ayarlanır. Node. exe ' nin, adlandırılmış kanalda her seferinde 5000 isteği kabul edebileceği anlamına gelir. Bu değer, Azure App Service üzerinde çalışan düğüm uygulamalarının çoğu için yeterince iyi olmalıdır. `NODE_PENDING_PIPE_INSTANCES` için yüksek değer Azure App Service için 503,1003 ' i görmemelisiniz

## <a name="more-resources"></a>Diğer kaynaklar

Azure App Service üzerindeki Node. js uygulamaları hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin.

* [Azure App Service’te Node.js web uygulamalarını kullanmaya başlama](app-service-web-get-started-nodejs.md)
* [Azure Uygulama Hizmeti’ndeki bir Node.js web uygulamasına hata ayıklama](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [Azure uygulamalarıyla Node.js Modüllerini kullanma](../nodejs-use-node-modules-azure-apps.md)
* [Azure Uygulama Hizmeti Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Geliştirici Merkezi](../nodejs-use-node-modules-azure-apps.md)
* [Süper Gizli Kudu Hata Ayıklama Konsolunu keşfetme](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
