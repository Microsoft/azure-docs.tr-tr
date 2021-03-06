---
title: Sfctl kullanarak Azure Service Fabric uygulamalarını yönetme
description: Azure Service Fabric CLı kullanarak Azure Service Fabric kümesinden uygulama dağıtmayı ve kaldırmayı öğrenin
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84711043"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLı (sfctl) kullanarak bir Azure Service Fabric uygulamasını yönetme

Azure Service Fabric kümesinde çalışan uygulamalar oluşturmayı ve silmeyi öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* Service Fabric CLı 'yi yükler. Sonra Service Fabric kümenizi seçin. Daha fazla bilgi için bkz. [SERVICE fabrıc CLI kullanmaya başlama](service-fabric-cli.md).

* Dağıtım için bir Service Fabric uygulama paketi hazırlayın. Bir uygulamayı yazma ve paketleme hakkında daha fazla bilgi için [Service Fabric uygulama modeliyle](service-fabric-application-model.md)ilgili okuyun.

## <a name="overview"></a>Genel Bakış

Yeni bir uygulama dağıtmak için şu adımları izleyin:

1. Service Fabric görüntü deposuna bir uygulama paketi yükleyin.
2. Uygulama türü sağlayın.
3. Görüntü deposu içeriğini silin.
4. Bir uygulama belirtin ve oluşturun.
5. Hizmet belirtin ve oluşturun.

Mevcut bir uygulamayı kaldırmak için şu adımları izleyin:

1. Uygulamayı silin.
2. İlişkili uygulama türünün sağlamasını kaldır.

## <a name="deploy-a-new-application"></a>Yeni bir uygulama dağıtma

Yeni bir uygulama dağıtmak için aşağıdaki görevleri doldurun:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Yeni bir uygulama paketini görüntü deposuna yükleme

Uygulama oluşturmadan önce, uygulama paketini Service Fabric görüntü deposuna yükleyin.

Örneğin, uygulama paketiniz `app_package_dir` dizinde ise, dizini karşıya yüklemek için aşağıdaki komutları kullanın:

```shell
sfctl application upload --path ~/app_package_dir
```

Büyük uygulama paketleri için `--show-progress` karşıya yükleme işleminin ilerlemesini görüntüleme seçeneğini belirtebilirsiniz.

### <a name="provision-the-application-type"></a>Uygulama türünü sağlama

Karşıya yükleme tamamlandığında, uygulamayı sağlayın. Uygulamayı sağlamak için aşağıdaki komutu kullanın:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

Değeri, `application-type-build-path` uygulama paketinizi yüklediğiniz dizinin adıdır.

### <a name="delete-the-application-package"></a>Uygulama paketini silme

Uygulama başarıyla kaydedildikten sonra uygulama paketini kaldırmanız önerilir.  Uygulama paketlerini görüntü deposundan silme sistem kaynaklarını boşaltır.  Kullanılmayan uygulama paketlerinin tutulması disk depolama alanı tüketir ve uygulama performans sorunlarına yol açar. 

Görüntü deposundan uygulama paketini silmek için şu komutu kullanın:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path` uygulamayı oluştururken karşıya yüklediğiniz dizinin adı olmalıdır.

### <a name="create-an-application-from-an-application-type"></a>Uygulama türünden uygulama oluşturma

Uygulamayı sağlamadıktan sonra, aşağıdaki komutu kullanarak uygulamanızı adlandırın ve oluşturun:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` , uygulama örneği için kullanmak istediğiniz addır. Daha önce sağlanan uygulama bildiriminden ek parametreler edinebilirsiniz.

Uygulama adının önekiyle başlaması gerekir `fabric:/` .

### <a name="create-services-for-the-new-application"></a>Yeni uygulama için hizmet oluşturma

Bir uygulama oluşturduktan sonra uygulamadan hizmetler oluşturun. Aşağıdaki örnekte, uygulamamızda yeni bir durum bilgisiz hizmeti oluşturacağız. Bir uygulamadan oluşturabileceğiniz hizmetler, önceden sağlanan uygulama paketindeki bir hizmet bildiriminde tanımlanmıştır.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Uygulama dağıtımını ve sistem durumunu doğrulama

Her şeyin sağlıklı olduğunu doğrulamak için aşağıdaki sistem durumu komutlarını kullanın:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Hizmetin sağlıklı olduğunu doğrulamak için, hem hizmetin hem de uygulamanın sistem durumunu almak için benzer komutları kullanın:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Sağlıklı hizmetler ve uygulamaların değeri vardır `HealthState` `Ok` .

## <a name="remove-an-existing-application"></a>Mevcut bir uygulamayı kaldır

Bir uygulamayı kaldırmak için aşağıdaki görevleri doldurun:

### <a name="delete-the-application"></a>Uygulamayı silme

Uygulamayı silmek için şu komutu kullanın:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Uygulama türünü sağlamayı kaldır

Uygulamayı sildikten sonra, artık gerekmiyorsa uygulama türünün sağlamasını kaldırabilirsiniz. Uygulama türünü sağlamayı kaldırmak için aşağıdaki komutu kullanın:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Tür adı ve tür sürümü, daha önce sağlanan uygulama bildirimindeki ad ve sürümle aynı olmalıdır.

## <a name="upgrade-application"></a>Uygulamayı yükselt

Uygulamanızı oluşturduktan sonra, uygulamanızın ikinci bir sürümünü sağlamak için aynı adım kümesini tekrarlayabilirsiniz. Ardından, Service Fabric bir uygulama yükseltmesinde uygulamanın ikinci sürümünü çalıştırmaya geçiş yapabilirsiniz. Daha fazla bilgi için [uygulama yükseltmeleri Service Fabric](service-fabric-application-upgrade.md)belgelerine bakın.

Bir yükseltme gerçekleştirmek için önce uygulamanın sonraki sürümünü, önceki ile aynı komutları kullanarak sağlayın:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Daha sonra, izlenen bir otomatik yükseltme gerçekleştirmek önerilir, aşağıdaki komutu çalıştırarak yükseltmeyi başlatın:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Yükseltmeler, var olan parametreleri herhangi bir ayarla belirtilen şekilde geçersiz kılar. Gerekirse, uygulama parametreleri Upgrade komutuna bağımsız değişken olarak geçirilmelidir. Uygulama parametreleri JSON nesnesi olarak kodlanmalıdır.

Daha önce belirtilen parametreleri almak için `sfctl application info` komutunu kullanabilirsiniz.

Uygulama yükseltmesi devam ederken, bu durum komutu kullanılarak alınabilir `sfctl application upgrade-status` .

Son olarak, bir yükseltme devam ediyorsa ve iptal edilmesi gerekiyorsa, `sfctl application upgrade-rollback` yükseltme işlemini geri almak için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Service Fabric CLı temelleri](service-fabric-cli.md)
* [Linux üzerinde Service Fabric kullanmaya başlama](service-fabric-get-started-linux.md)
* [Service Fabric uygulama yükseltmesi başlatma](service-fabric-application-upgrade.md)
