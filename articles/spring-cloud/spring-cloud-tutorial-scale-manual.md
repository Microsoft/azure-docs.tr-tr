---
title: "Öğretici: Azure Spring Cloud 'da bir uygulamayı ölçeklendirme | Microsoft Docs"
description: Bu öğreticide, Azure yay bulutu 'nda Azure portal bir uygulamayı nasıl ölçeklentireceğinizi öğreneceksiniz.
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132894"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Öğretici: Azure Spring Cloud 'da bir uygulamayı ölçeklendirme

Bu öğreticide, Azure portal Azure Spring Cloud Panosu kullanılarak herhangi bir mikro hizmet uygulamasının nasıl ölçeklendirileceğini gösterilmektedir. Sanal CPU 'ların (vCPU 'Lar) sayısını ve bellek miktarını değiştirerek uygulamanızı yukarı ve aşağı ölçeklendirin. Uygulamanın örnek sayısını değiştirerek uygulamanızı içinde ve dışarı ölçeklendirin. İşiniz bittiğinde, hizmetinizdeki her bir uygulama için hızlı el ile ayarlamalar yapmayı öğrenirsiniz. Ölçeklendirme, saniyeler içinde etkili olur ve herhangi bir kod değişikliği veya yeniden dağıtım gerektirmez.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 
* Dağıtılmış bir Azure yay bulut hizmeti örneği.  Başlamak için [hızlı](spring-cloud-quickstart-launch-app-cli.md) başlarımızı izleyin.
* Bu hizmet örneğinde en az bir uygulama zaten oluşturulmuş.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Azure portal ölçek sayfasına gidin

1. [Azure Portal](https://portal.azure.com)’da oturum açın.

1. Azure Spring Cloud **genel bakış** sayfanıza gidin.

1. Hizmetinizi içeren kaynak grubunu seçin.

1. Sol taraftaki menüdeki **Ayarlar** başlığı altındaki **uygulamalar** sekmesine gidin.

1. Ölçeklendirmek istediğiniz uygulamayı seçin. Bu örnekte, "Account-Service" adlı uygulamayı ölçeklendireceğiz. Bu, sizi uygulamanın **genel bakış** sayfasına götürür.

1. Sol taraftaki menüdeki **Ayarlar** başlığının altındaki **Ölçek** sekmesine gidin. Aşağıdaki bölümde gösterilen ölçeklendirme öznitelikleri için seçenekler görmeniz gerekir.

## <a name="scale-your-application"></a>Uygulamanızı ölçeklendirme

Ölçeklendirme özniteliklerini değiştirebilirsiniz. Aşağıdaki notları aklınızda tutun.

* **CPU 'lar**: her uygulama örneği için izin verilen en fazla CPU sayısı 4 ' ü. Bir uygulama için toplam CPU sayısı, burada ayarlanan değer, uygulama örneklerinin sayısıyla çarpılır.

* **Bellek/GB**: izin verilen maksimum bellek miktarı, uygulama örneği başına 8gb 'dir.  Bir uygulama için toplam bellek miktarı, burada ayarlanan değer, uygulama örneklerinin sayısıyla çarpılır.

* **Uygulama örneği sayısı**: Standart katmanda 20 ' ye kadar örnek ölçeklendirebilirsiniz. Bu değer, mikro hizmet uygulamasının ayrı çalışan örneklerinin sayısını değiştirir.

Ölçeklendirme ayarlarınızı uygulamak için **Kaydet** düğmesine tıkladığınızdan emin olun.

![Azure portal hizmeti ölçeklendirme](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Birkaç saniye sonra, yaptığınız değişiklikler, **uygulama örnekleri** sekmesinde daha fazla ayrıntı Ile **genel bakış** sayfasında görüntülenir. ölçeklendirme, herhangi bir kod değişikliği veya yeniden dağıtım gerektirmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Spring Cloud uygulamalarınızı el ile ölçeklendirmenizi öğrendiniz.  Uygulamanızı nasıl izleyeceğinizi öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Uygulamanızı izlemeyi öğrenin](spring-cloud-tutorial-distributed-tracing.md)
