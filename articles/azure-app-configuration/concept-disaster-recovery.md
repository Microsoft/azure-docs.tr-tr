---
title: Azure uygulama yapılandırması dayanıklılık ve olağanüstü durum kurtarma | Microsoft Docs
description: Azure uygulama yapılandırması ile dayanıklılık ve olağanüstü durum kurtarma uygulama genel bakış.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706498"
---
# <a name="resiliency-and-disaster-recovery"></a>Dayanıklılık ve olağanüstü durum kurtarma

Şu anda, Azure uygulama yapılandırması bölgesel bir hizmettir. Her yapılandırma deposu, belirli bir Azure bölgesinde oluşturulur. Bölge genelinde kaybı, bu bölgedeki tüm depoları etkiler. Uygulama yapılandırması başka bir bölgeye otomatik yük devretme sunmaz. Bu makalede, nasıl birden çok yapılandırma deposu Azure bölgeleri arasında uygulamanızın coğrafi dayanıklılığı artırmak için kullanabileceğiniz üzerinde genel rehberlik sağlar.

## <a name="high-availability-architecture"></a>Yüksek kullanılabilirlik mimarisi

Bölgeler arası yedeklilik hayata geçirmek için farklı bölgelerde birden çok uygulama yapılandırma deposu oluşturmanız gerekir. Bu kurulum ile birincil deponun erişilemez hale gelirse geri dönemedi en az bir ek yapılandırma deposu uygulamanız var. Aşağıdaki diyagram topoloji uygulamanız ve onun birincil ve ikincil yapılandırma depoları arasında gösterir:

![Coğrafi olarak yedekli depoları](./media/geo-redundant-app-configuration-stores.png)

Uygulamanızın yapılandırmasını paralel birincil ve ikincil depolarında yükler. Bunun yapılması, başarıyla yapılandırma verilerini alma şansını artırır. Veriler her iki depolarında eşitlenmiş tutmak için sorumlu olursunuz. Aşağıdaki bölümler, coğrafi dayanıklılık uygulamanıza nasıl oluşturabileceğinizi açıklar.

## <a name="failover-between-configuration-stores"></a>Yapılandırma depoları arasında yük devretme

Teknik olarak, uygulamanızın bir yük devretme Yürütülüyor değil. Aynı anda iki uygulama yapılandırması mağazalardan aynı kümesi yapılandırma verilerini almaya çalışıyor. Kodunuzu ikincil Mağaza'dan önce yükler ve ardından birincil depolama şekilde düzenleyin. Bu yaklaşım, kullanılabilir olduğunda yapılandırma verilerini birincil depolama öncelik kazanır sağlar. Aşağıdaki kod parçacığı, .NET Core CLI bu düzenleme nasıl uygulayacağınıza dair gösterir:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    }
```

Bildirim `optional` parametresi geçirildi `AddAzureAppConfiguration` işlevi. Ayarlandığında `true`, bu parametre, işlevi bir yapılandırma verileri yüklenemiyor devam etmek başarısız olan uygulamasının başlatmasını engeller.

## <a name="synchronization-between-configuration-stores"></a>Yapılandırma depoları arasında eşitleme

Tüm, coğrafi olarak yedekli yapılandırma depoları aynı veri kümesi olması önemlidir. Kullanabileceğiniz **dışarı** birincil Mağaza'dan isteğe bağlı olarak ikincil veri kopyalamak için uygulama yapılandırmasında işlevi. Bu işlev, Azure portalı ve CLI kullanılabilir.

Azure portalında aşağıdaki adımları izleyerek başka bir yapılandırma deposuna yönelik bir değişiklik gönderebilirsiniz.

1. Git **içeri/dışarı aktarma** sekmesine tıklayın ve **dışarı** > **uygulama yapılandırması** > **hedef**  >  **Bir kaynak seçin**.

2. Açılan yeni dikey pencerede, abonelik, kaynak grubu ve kaynak ikincil deponuzun adını belirtin ve ardından **Uygula**.

3. İkincil deponuza dışarı aktarmak istediğiniz hangi yapılandırma verilerini seçebilmeniz UI güncelleştirilir. Varsayılan saat değeri olduğu gibi bırakın ve hem **etiketten** ve **etikete** aynı değere. **Uygula**’yı seçin.

4. Tüm yapılandırma değişiklikleri için önceki adımı yineleyin.

Bu dışarı aktarma işlemi otomatikleştirmek için Azure CLI'yı kullanın. Aşağıdaki komut, tek bir yapılandırma değişikliği ikincil birincil depodan dışarı aktarma işlemini gösterir:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, uygulamanızın çalışma zamanı sırasında uygulama yapılandırması için coğrafi dayanıklılık sağlamak büyütmek öğrendiniz. Ayrıca, uygulama yapılandırmasını yapılandırma verilerinden derleme veya dağıtım zamanında gömebilirsiniz. Daha fazla bilgi için [bir CI/CD işlem hattı ile tümleştir](./integrate-ci-cd-pipeline.md).

