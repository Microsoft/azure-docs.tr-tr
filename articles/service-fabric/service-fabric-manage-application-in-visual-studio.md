---
title: Visual Studio'da uygulamaları yönetme
description: Azure Service Fabric uygulamalarınızı ve hizmetlerinizi oluşturmak, geliştirmek, paketlemek, dağıtmak ve hatalarını ayıklamak için Visual Studio 'Yu kullanın.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574556"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Service Fabric uygulamalarınızı yazmayı ve yönetmeyi kolaylaştırmak için Visual Studio 'Yu kullanın
Azure Service Fabric uygulamalarınızı ve hizmetlerinizi Visual Studio aracılığıyla yönetebilirsiniz. [Geliştirme ortamınızı ayarladıktan](service-fabric-get-started.md)sonra Visual Studio 'yu kullanarak, yerel geliştirme kümenize Service Fabric uygulamalar oluşturabilir, hizmetler ekleyebilir veya paket, kayıt ve dağıtım uygulamaları dağıtabilirsiniz.

## <a name="deploy-your-service-fabric-application"></a>Service Fabric uygulamanızı dağıtma
Varsayılan olarak, bir uygulama dağıtmak aşağıdaki adımları tek bir basit işlemde birleştirir:

1. Uygulama paketi oluşturma
2. Uygulama paketini görüntü deposuna yükleme
3. Uygulama türü kaydediliyor
4. Çalışan tüm uygulama örnekleri kaldırılıyor
5. Uygulama örneği oluşturma

Visual Studio 'da **F5** tuşuna basmak uygulamanızı dağıtır ve hata ayıklayıcıyı tüm uygulama örneklerine ekler. Hata ayıklama olmadan bir uygulamayı dağıtmak için **CTRL + F5** kullanabilirsiniz veya yayımlama profilini kullanarak yerel veya uzak bir kümeye yayımlayabilirsiniz.

### <a name="application-debug-mode"></a>Uygulama hata ayıklama modu
Visual Studio, hata ayıklamanın bir parçası olarak Visual Studios 'ın uygulama dağıtımını nasıl işlemesini denetleyen **uygulama hata ayıklama modu** adlı bir özellik sağlar.

#### <a name="to-set-the-application-debug-mode-property"></a>Uygulama hata ayıklama modu özelliğini ayarlamak için
1. Service Fabric uygulama projesinin (*. sfproj) kısayol menüsünde **Özellikler** ' i seçin (veya **F4** tuşuna basın).
2. **Özellikler** penceresinde, **uygulama hata ayıklama modu** özelliğini ayarlayın.

![Uygulama hata ayıklama modu özelliğini ayarla][debugmodeproperty]

#### <a name="application-debug-modes"></a>Uygulama hata ayıklama modları

1. **Uygulamayı Yenile** Bu mod, hata ayıklarken kodunuzda hızla değişiklik yapmanızı ve hata ayıklamanızı ve statik Web dosyalarını düzenlemenizi destekler. Bu mod yalnızca yerel geliştirme kümeniz 1 düğümlü modda olduğunda işe yarar. Bu, varsayılan uygulama hata ayıklama modudur.
2. **Uygulamayı kaldır** , hata ayıklama oturumu sona erdiğinde uygulamanın kaldırılmasına neden olur.
3. **Otomatik yükseltme** Hata ayıklama oturumu sona erdiğinde uygulama çalışmaya devam eder. Sonraki hata ayıklama oturumu dağıtımı yükseltme olarak değerlendirir. Yükseltme işlemi, önceki bir hata ayıklama oturumunda girdiğiniz tüm verileri korur.
4. **Uygulamayı koru** Hata ayıklama oturumu sona erdiğinde uygulama kümede çalışmaya devam eder. Sonraki hata ayıklama oturumunun başlangıcında uygulama kaldırılır.

**Otomatik yükseltme** verileri için Service Fabric uygulama yükseltme özellikleri uygulanarak korunur. Uygulamaları yükseltme hakkında daha fazla bilgi ve gerçek bir ortamda yükseltme gerçekleştirme hakkında daha fazla bilgi için, bkz. [Service Fabric Application Upgrade](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Service Fabric uygulamanıza hizmet ekleme
İşlevlerini genişletmek için uygulamanıza yeni hizmetler ekleyebilirsiniz. Hizmetin uygulama paketinize eklendiğinden emin olmak için, hizmeti **Yeni Fabric hizmeti...** menü öğesi aracılığıyla ekleyin.

![Yeni bir Service Fabric hizmeti ekleme][newservice]

Uygulamanıza eklemek için Service Fabric bir proje türü seçin ve hizmet için bir ad belirtin.  Kullanılacak hizmet türüne karar vermenize yardımcı olması için [hizmetiniz için bir çerçeve seçme](service-fabric-choose-framework.md) konusuna bakın.

![Uygulamanıza eklemek için bir Service Fabric hizmet projesi türü seçin][addserviceproject]

Yeni hizmet çözümünüze ve var olan uygulama paketinize eklenir. Hizmet başvuruları ve varsayılan bir hizmet örneği uygulama bildirimine eklenerek, uygulamayı bir dahaki sefer dağıttığınızda hizmetin oluşturulup başlatılmasına neden olur.

![Yeni hizmet, uygulama bildiriminize eklenir][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Service Fabric uygulamanızı paketleme
Uygulamayı ve hizmetlerini bir kümeye dağıtmak için bir uygulama paketi oluşturmanız gerekir.  Paket, belirli bir düzende uygulama bildirimini, hizmet bildirimlerini ve diğer gerekli dosyaları düzenler.  Visual Studio, ' pkg ' dizininde uygulama projesinin klasöründe paketi kurar ve yönetir.  **Uygulama** bağlam menüsünde **paket** ' i tıklamak uygulama paketini oluşturur veya güncelleştirir.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Bulut gezginini kullanarak uygulamaları ve uygulama türlerini kaldırma
Visual Studio içinden, **Görünüm** menüsünden başlatabilmeniz Için, bulut Gezgini 'ni kullanarak temel küme yönetim işlemleri gerçekleştirebilirsiniz. Örneğin, uygulamaları silebilir ve yerel veya uzak kümelerdeki uygulama türlerini sağlamayı kaldırabilirsiniz.

![Uygulamayı kaldırma][removeapplication]

> [!TIP]
> Daha zengin bir küme yönetimi işlevselliği için bkz. [Service Fabric Explorer kümenizi görselleştirme](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric uygulama modeli](service-fabric-application-model.md)
* [Uygulama dağıtımını Service Fabric](service-fabric-deploy-remove-applications.md)
* [Birden çok ortam için uygulama parametrelerini yönetme](service-fabric-manage-multiple-environment-app-configuration.md)
* [Service Fabric uygulamanızda hata ayıklama](service-fabric-debugging-your-application.md)
* [Service Fabric Explorer kullanarak kümenizi görselleştirme](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
