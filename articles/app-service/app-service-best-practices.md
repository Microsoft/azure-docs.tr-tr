---
title: En iyi yöntemler-Azure App Service
description: Azure App Service için en iyi uygulamaları ve sorun gidermeyi öğrenin.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: c40191c8682d6ff93f70e0853e767c89248ae887
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071611"
---
# <a name="best-practices-for-azure-app-service"></a>Azure Uygulama Hizmeti için En İyi Uygulamalar
Bu makalede [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)kullanımı için en iyi yöntemler özetlenmektedir. 

## <a name="colocation"></a>Birlikte bulundurma
Bir Web uygulaması ve bir veritabanı gibi bir çözüm oluşturan Azure kaynakları farklı bölgelerde bulunuyorsa, aşağıdaki etkilere sahip olabilir:

* Kaynaklar arasındaki iletişimde artan gecikme süresi
* [Azure fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/data-transfers)belirtildiği gibi giden veri aktarımı çapraz bölgesi için parasal ücretler.

Aynı bölgedeki birlikte bulundurma, Web uygulaması, içerik veya verileri tutmak için kullanılan bir veritabanı veya depolama hesabı gibi bir çözüm oluşturan Azure kaynakları için idealdir. Kaynak oluştururken, bunların aynı Azure bölgesinde olduklarından emin olun, aksi takdirde bunların belirli bir iş veya tasarım nedeninden olmaması gerekir. Premium App Service plan uygulamaları için mevcut olan [App Service kopyalama özelliğini](app-service-web-app-cloning.md) kullanarak, App Service bir uygulamayı veritabanınızdan aynı bölgeye taşıyabilirsiniz.   

## <a name="memoryresources"></a>Uygulamalar beklenenden daha fazla bellek tükettiği zaman
Bir uygulamanın, izleme veya hizmet önerileri aracılığıyla beklendiği gibi beklenenden daha fazla bellek tükettiğini fark ettiğinizde, [App Service otomatik düzeltme özelliğini](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)göz önünde bulundurun. Otomatik düzeltme özelliğinin seçeneklerinden biri, bir bellek eşiğine göre özel eylemleri ele alır. İşlemler, çalışan işlemini geri dönüşüme göre, bellek dökümü aracılığıyla bellek dökümü aracılığıyla araştırma yapmak için e-posta bildirimlerinin spektrumu üzerine yayılıyor. Otomatik düzeltme, Web. config ile ve [App Service destek site uzantısı](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)için bu blog gönderisine açıklandığı gibi kolay bir kullanıcı arabirimi aracılığıyla yapılandırılabilir.   

## <a name="CPUresources"></a>Uygulamalar beklenenden daha fazla CPU tüketir
Bir uygulamanın beklenenden daha fazla CPU tükettiğini veya izleme ya da hizmet önerileri aracılığıyla gösterildiği gibi yinelenen CPU artışlarına yönelik olduğunu fark ettiğinizde, App Service planını ölçeklendirin veya ölçeklendirerek ölçeği değerlendirin. Uygulamanızın durum bilgisi varsa yalnızca ölçeği artırma tek seçenektir, ancak uygulamanız durum bilgisiz ise ölçeği genişletme, daha fazla esneklik ve daha yüksek ölçekli potansiyel bir değer sağlar. 

"Durum bilgisi olan" vs "durum bilgisiz" uygulamaları hakkında daha fazla bilgi için bu videoyu izleyebilirsiniz: [Azure App Service üzerinde ölçeklenebilir uçtan uca çok katmanlı bir uygulama planlama](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). App Service ölçeklendirme ve otomatik ölçeklendirme seçenekleri hakkında daha fazla bilgi için bkz. [Azure App Service bir Web uygulamasını ölçeklendirme](manage-scale-up.md).  

## <a name="socketresources"></a>Yuva kaynakları tükendiğinde
Giden TCP bağlantılarının tüketilmesinin yaygın bir nedeni, TCP bağlantılarını yeniden kullanmak için uygulanmayan veya HTTP-Keep-LIAS gibi daha yüksek düzey bir protokol kullanılmayan istemci kitaplıklarının kullanılmasının yaygın bir nedenidir. Dışarı giden bağlantıların verimli bir şekilde yeniden kullanılması için kodunuzda yapılandırılmış veya erişilebilir olduklarından emin olmak için App Service planınızdaki uygulamalar tarafından başvurulan her bir kitaplıkların belgelerini gözden geçirin. Ayrıca, bağlantı sızıntısına engel olmak için uygun oluşturma ve yayınlama ya da temizleme için kitaplık belge kılavuzunu izleyin. Bu tür istemci kitaplıkları araştırmalar devam ederken, etki çok örneğe ölçeklendirerek etki azaltılabilir.

### <a name="nodejs-and-outgoing-http-requests"></a>Node. js ve giden http istekleri
Node. js ve çok sayıda giden http isteği ile çalışırken, HTTP-canlı tutma ile ilgilenirken önemli olur. Kodunuzu daha kolay hale getirmek için [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` paketini kullanabilirsiniz.

İşleyicide hiçbir `http` şey yapsanız bile her zaman yanıtı işleyin. Yanıtı doğru bir şekilde işlemezseniz, daha fazla yuva kullanılamadığından uygulamanız sonunda takılmalıdır.

Örneğin, `http` veya `https` paketiyle çalışırken:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Birden çok çekirdekli bir makinede Linux üzerinde App Service çalıştırıyorsanız, uygulamanızı yürütmek üzere birden çok Node. js işlemini başlatmak için PM2 kullanmak daha iyi bir uygulamadır. Bunu, kapsayıcınıza bir başlangıç komutu belirterek yapabilirsiniz.

Örneğin, dört örneği başlatmak için:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Uygulama yedeğiniz başarısız olmaya başladığında
Uygulama yedeklemenin başarısız olmasının en yaygın iki nedeni şunlardır: Geçersiz depolama ayarları ve geçersiz veritabanı yapılandırması. Bu arızalar genellikle depolama veya veritabanı kaynaklarında yapılan değişiklikler ya da bu kaynaklara erişim için değişiklikler (örneğin, yedekleme ayarlarında seçilen veritabanı için güncelleştirilmiş kimlik bilgileri) olduğunda meydana gelir. Yedeklemeler genellikle bir zamanlamaya göre çalışır ve depolama alanına (Yedeklenen dosyaların çıktısını almak için) ve veritabanlarına (yedeklemeye dahil edilecek içeriği kopyalamak ve okumak için) erişim gerektirir. Bu kaynaklardan birine erişim başarısız olma sonucu, tutarlı yedekleme hatası olacaktır. 

Yedekleme hataları oluştuğunda, hangi tür hataların olduğunu anlamak için en son sonuçları gözden geçirin. Depolama erişimi hatalarında, yedekleme yapılandırmasında kullanılan depolama ayarlarını gözden geçirin ve güncelleştirin. Veritabanı erişimi hatalarında, uygulama ayarlarının parçası olarak bağlantı dizelerinizi gözden geçirin ve güncelleştirin; ardından, gerekli veritabanlarını düzgün şekilde içerecek şekilde yedekleme yapılandırmanızı güncelleştirmeye devam edin. Uygulama yedeklemeleri hakkında daha fazla bilgi için bkz. [Azure App Service bir Web uygulamasını yedekleme](manage-backup.md).

## <a name="nodejs"></a>Yeni Node. js uygulamaları Azure App Service dağıtıldığında
Node. js uygulamaları için Azure App Service varsayılan yapılandırması, en yaygın uygulamaların ihtiyaçlarına en iyi şekilde uyum sağlamak için tasarlanmıştır. Node. js uygulamanızın yapılandırması, performansı iyileştirmek veya CPU/bellek/ağ kaynakları için kaynak kullanımını iyileştirmek üzere kişiselleştirilmiş ayarlama avantajına sahip olursa, bkz. [Azure App Service üzerindeki düğüm uygulamaları Için en iyi uygulamalar ve sorun giderme kılavuzu](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Bu makalede, Node. js uygulamanız için yapılandırmanız gerekebilecek ıısnode ayarları açıklanmakta, uygulamanızın karşılaştığı çeşitli senaryolar veya sorunlar açıklanmakta ve bu sorunların nasıl ele alınacağını gösterilmektedir.

