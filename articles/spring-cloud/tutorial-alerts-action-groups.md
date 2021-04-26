---
title: 'Öğretici: uyarıları ve eylem gruplarını kullanarak Azure yay bulut kaynaklarını Izleme | Microsoft Docs'
description: Yay bulutu uyarılarını nasıl kullanacağınızı öğrenin.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: d12a48729616a5181f019f84f19779390e736cb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879202"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Öğretici: uyarıları ve eylem gruplarını kullanarak yay bulut kaynaklarını Izleme

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Azure yay bulut uyarıları, kullanılabilir depolama, istek hızı veya veri kullanımı gibi koşullara göre kaynakları izlemeyi destekler. Bir uyarı, oran veya koşullar tanımlı belirtimleri karşılıyorsa bildirim gönderir.

Bir uyarı ardışık düzeni kurmak için iki adım vardır: 
1. E-posta, SMS, runbook veya Web kancası gibi bir uyarı tetiklendiğinde gerçekleştirilecek eylemlerle bir eylem grubu ayarlayın. Eylem grupları, farklı uyarılar arasında yeniden kullanılabilir.
2. Uyarı kurallarını ayarlayın. Kurallar, ölçüm düzenlerini hedef kaynağa, ölçüme, koşula, zaman toplamaya, vb. göre eylem gruplarıyla bağlar.

## <a name="prerequisites"></a>Önkoşullar

Azure Spring gereksinimlerine ek olarak, bu öğreticideki yordamlar dağıtılan bir Azure yay bulutu örneğiyle birlikte çalışır.  Başlamak için [hızlı](spring-cloud-quickstart.md) başlangıcı izleyin.

Aşağıdaki yordamlar, bir yay bulutu örneğinin sol gezinti bölmesindeki **Uyarılar** seçeneğinden başlayarak hem **Eylem grubunu** hem de **uyarıyı** başlatır. (Yordam, Azure portal **Izlemeye genel bakış** sayfasından da başlayabilir.) 

Bir kaynak grubundan Spring Cloud örneğinize gidin. Sol bölmedeki **Uyarılar** ' ı seçin ve ardından **eylemleri Yönet**' i seçin:

![Ekran görüntüsü portalı kaynak grubu sayfası](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Eylem grubunu ayarlama

Yeni bir **eylem grubu** başlatma yordamına başlamak için **+ eylem grubu Ekle**' yi seçin.

![Ekran görüntüsü portalı eylem grubu Ekle](media/alerts-action-groups/action-1.png)

**Eylem grubu Ekle** sayfasında:

 1. Bir **eylem grubu adı** ve **kısa ad** belirtin.

 1. **Abonelik** ve **kaynak grubunu** belirtin.

 1. **Eylem adını** belirtin.

 1. **Eylem türünü** seçin.  Bu işlem, etkinleştirme sırasında gerçekleştirilecek eylemi tanımlamak için sağdaki başka bir bölmeyi açar.

 1. Sağ bölmedeki seçenekleri kullanarak eylemi tanımlayın.  Bu durum, e-posta bildirimini kullanır.

 1. Sağ eylem bölmesinde **Tamam** ' ı tıklatın.

 1. **Eylem grubu Ekle** Iletişim kutusunda **Tamam** ' a tıklayın. 

  ![Ekran görüntüsü portalı eylemini tanımla](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Uyarı ayarlama 

Önceki adımlar, e-posta kullanan bir **eylem grubu** oluşturdu. Telefon bildirimi, Web kancaları ve Azure işlevleri gibi işlemler de kullanabilirsiniz. Aşağıdaki adımlar bir **Uyarı** yapılandırır.

1. **Uyarılar** sayfasına geri gidin ve **Uyarı kurallarını yönet**' e tıklayın.

   ![Ekran görüntüsü portalı uyarı tanımla](media/alerts-action-groups/alerts-2.png)

1. Uyarının **kaynağını** seçin.

1. **+ Yeni uyarı kuralı**' na tıklayın.

   ![Ekran görüntüsü portalı yeni uyarı kuralı](media/alerts-action-groups/alerts-3.png)

1. **Kural oluştur** sayfasında, **kaynağı** belirtin.

1. **Koşul** ayarı, **bahar bulut** kaynaklarınızı izlemeye yönelik birçok seçenek sağlar.  **Sinyal mantığını Yapılandır** bölmesini açmak için **Ekle** ' ye tıklayın.

1. Bir koşul seçin. Bu örnek, **SISTEM CPU kullanımı yüzdesini** kullanır.

   ![Ekran görüntüsü portalı yeni uyarı kuralı 2](media/alerts-action-groups/alerts-3-1.png)

1. **Eşik değerini** izleyici olarak ayarlamak için **sinyal mantığını Yapılandır** bölmesini aşağı kaydırın.

   ![Ekran görüntüsü portalı yeni uyarı kuralı 3](media/alerts-action-groups/alerts-3-2.png)

1. **Bitti**’ye tıklayın.

   İzlemek için kullanılabilen koşulların ayrıntıları için bkz. [Kullanıcı Portalı ölçümleri seçenekleri](spring-cloud-concept-metrics.md#user-metrics-options).

1. **Eylemler** altında **eylem grubu seç**' e tıklayın. **Eylemler** bölmesinden önceden tanımlanmış **Eylem grubunu** seçin.

   ![Ekran görüntüsü portalı yeni uyarı kuralı 4](media/alerts-action-groups/alerts-3-3.png) 

1. Aşağı kaydırın ve **Uyarı ayrıntıları**' nın altında, uyarı kuralını adlandırın.

1. **Önem derecesini** ayarlayın.

1. **Uyarı kuralı oluştur**’a tıklayın.

   ![Ekran görüntüsü portalı yeni uyarı kuralı 5](media/alerts-action-groups/alerts-3-4.png)

1. Yeni uyarı kuralının etkinleştirildiğini doğrulayın.

   ![Ekran görüntüsü portalı yeni uyarı kuralı 6](media/alerts-action-groups/alerts-4.png)

**Ölçüm** sayfası kullanılarak da bir kural oluşturulabilir:

![Ekran görüntüsü portalı yeni uyarı kuralı 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure yay bulutu uygulaması için uyarıları ve eylem gruplarını ayarlamayı öğrendiniz. Eylem grupları hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Azure portalında eylem gruplarını oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md)

> [!div class="nextstepaction"]
> [Eylem gruplarında SMS uyarı davranışı](../azure-monitor/alerts/alerts-sms-behavior.md)