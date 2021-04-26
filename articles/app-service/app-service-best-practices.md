---
title: En İyi Uygulamalar
description: Azure App Service 'de çalışan uygulamanız için en iyi uygulamaları ve genel sorun giderme senaryolarını öğrenin.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: 91fd974c730037907258cb4a670f6fa836bfda6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144866"
---
# <a name="best-practices-for-azure-app-service"></a>Azure Uygulama Hizmeti için En İyi Uygulamalar
Bu makalede [Azure App Service](./overview.md)kullanımı için en iyi yöntemler özetlenmektedir. 

## <a name="colocation"></a><a name="colocation"></a>Birlikte bulundurma
Bir Web uygulaması ve bir veritabanı gibi bir çözüm oluşturan Azure kaynakları farklı bölgelerde bulunuyorsa, aşağıdaki etkilere sahip olabilir:

* Kaynaklar arasındaki iletişimde artan gecikme süresi
* [Azure fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/data-transfers)belirtildiği gibi giden veri aktarımı çapraz bölgesi için parasal ücretler.

Aynı bölgedeki birlikte bulundurma, Web uygulaması, içerik veya verileri tutmak için kullanılan bir veritabanı veya depolama hesabı gibi bir çözüm oluşturan Azure kaynakları için idealdir. Kaynak oluştururken, bunların aynı Azure bölgesinde olduklarından emin olun, aksi takdirde bunların belirli bir iş veya tasarım nedeninden olmaması gerekir. Premium App Service plan uygulamaları için mevcut olan [App Service kopyalama özelliğini](app-service-web-app-cloning.md) kullanarak, App Service bir uygulamayı veritabanınızdan aynı bölgeye taşıyabilirsiniz.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Uygulamalar beklenenden daha fazla bellek tükettiği zaman
Bir uygulamanın, izleme veya hizmet önerileri aracılığıyla beklendiği gibi beklenenden daha fazla bellek tükettiğini fark ettiğinizde, [App Service otomatik düzeltme özelliğini](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)göz önünde bulundurun. Otomatik düzeltme özelliğinin seçeneklerinden biri, bir bellek eşiğine göre özel eylemleri ele alır. İşlemler, çalışan işlemini geri dönüşüme göre, bellek dökümü aracılığıyla bellek dökümü aracılığıyla araştırma yapmak için e-posta bildirimlerinin spektrumu üzerine yayılıyor. Otomatik Düzeltme web.config aracılığıyla ve [App Service destek site uzantısı](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)için bu blog gönderisine açıklandığı gibi kolay bir kullanıcı arabirimi aracılığıyla yapılandırılabilir.   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Uygulamalar beklenenden daha fazla CPU tüketir
Bir uygulamanın beklenenden daha fazla CPU tükettiğini veya izleme ya da hizmet önerileri aracılığıyla gösterildiği gibi yinelenen CPU artışlarına yönelik olduğunu fark ettiğinizde, App Service planını ölçeklendirin veya ölçeklendirerek ölçeği değerlendirin. Uygulamanızın durum bilgisi varsa yalnızca ölçeği artırma tek seçenektir, ancak uygulamanız durum bilgisiz ise ölçeği genişletme, daha fazla esneklik ve daha yüksek ölçekli potansiyel bir değer sağlar. 

"Durum bilgisi olan" vs "durum bilgisiz" uygulamaları hakkında daha fazla bilgi için, bu videoyu izleyebilirsiniz: [Azure App Service üzerinde ölçeklenebilir uçtan uca çok katmanlı bir uygulama planlama](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). App Service ölçeklendirme ve otomatik ölçeklendirme seçenekleri hakkında daha fazla bilgi için bkz. [Azure App Service bir Web uygulamasını ölçeklendirme](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>Yuva kaynakları tükendiğinde
Giden TCP bağlantılarının tüketilmesinin yaygın bir nedeni, TCP bağlantılarını yeniden kullanmak için uygulanmayan veya HTTP-Keep-Alive gibi daha üst düzey bir protokol kullanılmayan istemci kitaplıklarının kullanılmasının yaygın bir nedenidir. Dışarı giden bağlantıların verimli bir şekilde yeniden kullanılması için kodunuzda yapılandırılmış veya erişilebilir olduklarından emin olmak için App Service planınızdaki uygulamalar tarafından başvurulan her bir kitaplıkların belgelerini gözden geçirin. Ayrıca, bağlantı sızıntısına engel olmak için uygun oluşturma ve yayınlama ya da temizleme için kitaplık belge kılavuzunu izleyin. Bu tür istemci kitaplıkları araştırmalar devam ederken, etki çok örneğe ölçeklendirerek etki azaltılabilir.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js ve giden http istekleri
Node.js ve çok sayıda giden http isteği ile çalışırken HTTP-Keep-Alive ile ilgilenirken önemli olur. [](https://www.npmjs.com/package/agentkeepalive) `npm` Kodunuzu daha kolay hale getirmek için agentkeepalive paketini kullanabilirsiniz.

`http`İşleyicide hiçbir şey yapsanız bile her zaman yanıtı işleyin. Yanıtı doğru bir şekilde işlemezseniz, daha fazla yuva kullanılamadığından uygulamanız sonunda takılmalıdır.

Örneğin, `http` veya `https` paketiyle çalışırken:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Birden çok çekirdekli bir makinede Linux üzerinde App Service çalıştırıyorsanız, uygulamanızı yürütmek üzere birden çok Node.js işlemini başlatmak için PM2 kullanılması en iyi uygulamadır. Bunu, kapsayıcınıza bir başlangıç komutu belirterek yapabilirsiniz.

Örneğin, dört örneği başlatmak için:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Uygulama yedeğiniz başarısız olmaya başladığında
Uygulama yedeklemenin başarısız olmasının en yaygın iki nedeni şunlardır: Geçersiz depolama ayarları ve geçersiz veritabanı yapılandırması. Bu arızalar genellikle depolama veya veritabanı kaynaklarında yapılan değişiklikler ya da bu kaynaklara erişim için değişiklikler (örneğin, yedekleme ayarlarında seçilen veritabanı için güncelleştirilmiş kimlik bilgileri) olduğunda meydana gelir. Yedeklemeler genellikle bir zamanlamaya göre çalışır ve depolama alanına (Yedeklenen dosyaların çıktısını almak için) ve veritabanlarına (yedeklemeye dahil edilecek içeriği kopyalamak ve okumak için) erişim gerektirir. Bu kaynaklardan birine erişim başarısız olma sonucu, tutarlı yedekleme hatası olacaktır. 

Yedekleme hataları oluştuğunda, hangi tür hataların olduğunu anlamak için en son sonuçları gözden geçirin. Depolama erişimi hatalarında, yedekleme yapılandırmasında kullanılan depolama ayarlarını gözden geçirin ve güncelleştirin. Veritabanı erişimi hatalarında, uygulama ayarlarının parçası olarak bağlantı dizelerinizi gözden geçirin ve güncelleştirin; ardından, gerekli veritabanlarını düzgün şekilde içerecek şekilde yedekleme yapılandırmanızı güncelleştirmeye devam edin. Uygulama yedeklemeleri hakkında daha fazla bilgi için bkz. [Azure App Service bir Web uygulamasını yedekleme](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Yeni Node.js uygulamalar dağıtıldığında Azure App Service
Node.js uygulamalar için Azure App Service varsayılan yapılandırma, en yaygın uygulamaların ihtiyaçlarını en iyi şekilde karşılayacak şekilde hazırlanmıştır. Node.js uygulamanızın yapılandırması, performansı artırmak veya CPU/bellek/ağ kaynakları için kaynak kullanımını iyileştirmek üzere kişiselleştirilmiş ayarlama avantajlarından yararlanacaksa, bkz. [Azure App Service üzerindeki düğüm uygulamaları Için en iyi uygulamalar ve sorun giderme kılavuzu](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Bu makalede, Node.js uygulamanız için yapılandırmanız gerekebilecek ıısnode ayarları açıklanmakta, uygulamanızın karşılaştığı çeşitli senaryolar veya sorunlar açıklanmakta ve bu sorunların nasıl ele alınacağını gösterilmektedir.


## <a name="next-steps"></a>Sonraki Adımlar
En iyi uygulamalar hakkında daha fazla bilgi için [App Service tanılama](./overview-diagnostics.md) ' yı ziyaret ederek kaynağına özgü eyleme dönüştürülebilir en iyi yöntemleri bulabilirsiniz.

- [Azure Portal](https://portal.azure.com)Web uygulamanıza gidin.
- App Service Tanılama ' yı açan sol gezinmede **sorunları Tanıla ve çöz** ' e tıklayın.
- **En Iyi Yöntemler** giriş sayfası kutucuğu seçin.
- En iyi yapılandırma uygulamalarına göre uygulamanızın geçerli durumunu görüntülemek için en iyi yapılandırmanın kullanılabilirlik & performansı veya **en iyi** uygulamaları **Için en iyi yöntemler** ' e tıklayın.

Bu bağlantıyı, kaynağınız için App Service tanılamayı doğrudan açmak için de kullanabilirsiniz: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .