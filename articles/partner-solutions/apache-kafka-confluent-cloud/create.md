---
title: Confluent bulutu için Apache Kafka oluşturma-Azure iş ortağı çözümleri
description: Bu makalede, confluent bulutu için Apache Kafka örneğini oluşturmak üzere Azure portal nasıl kullanılacağı açıklanır.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253799"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Hızlı başlangıç: confluent bulutu için Apache Kafka kullanmaya başlayın

Bu hızlı başlangıçta, confluent bulutu için Apache Kafka örneğini oluşturmak üzere Azure portal kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı. Etkin bir Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)oluşturun.
- Azure aboneliğiniz için _sahip_ veya _katkıda bulunan_ rolüne sahip olmanız gerekir. Azure ve confluent arasındaki tümleştirme yalnızca _sahip_ veya _katkıda bulunan_ erişimi olan kullanıcılar tarafından ayarlanabilir. Başlamadan önce, [uygun erişiminizin olduğunu doğrulayın](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Teklif bul

Confluent bulut uygulaması için Apache Kafka bulmak için Azure portal kullanın.

1. Bir Web tarayıcısında [Azure Portal](https://portal.azure.com/) gidin ve oturum açın.

1. **Market** 'i son zamanlarda ziyaret ediyorsanız, kullanılabilir seçeneklerden simgesini seçin. Aksi takdirde _Market_' i arayın.

    :::image type="content" source="media/marketplace.png" alt-text="Market simgesi.":::

1. **Market** sayfasından, istediğiniz plan türüne göre iki seçeneğiniz vardır. Kullandıkça Öde planına veya taahhüt planına kaydolabilirsiniz. Kullandıkça öde, herkese açık bir şekilde sunulmaktadır. Taahhüt planı, özel bir teklif için onaylanmış müşteriler tarafından kullanılabilir.

   - **Kullandıkça Öde** müşterileri için, bir _bulutta Apache Kafka_ arayın. Konluent bulutu üzerinde Apache Kafka için teklif seçin.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Market Teklifini arayın.":::

   - **Taahhüt** müşterileri Için, **özel teklifleri görüntülemek** üzere bağlantıyı seçin. Taahhüt, en düşük harcama miktarına kaydolmanızı gerektirir. Bu seçeneği yalnızca hizmetin uzun süre için gerekli olduğunu bildiğiniz durumlarda kullanın.

     :::image type="content" source="media/view-private-offers.png" alt-text="özel teklifleri görüntüleyin.":::

     _Konluent bulutu üzerinde Apache Kafka_ arayın.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Özel teklif ' i seçin.":::

## <a name="create-resource"></a>Kaynak oluşturma

Confluent bulutu üzerinde Apache Kafka teklifini seçtikten sonra, uygulamayı ayarlamaya hazırsınız demektir.

1. Önceki bölümde özel teklifler ' i seçtiyseniz, plan türleri için iki seçeneğiniz vardır:

    - Uyumlu bulut-Kullandıkça öde
    - Taahhüt-COMMIT planı için

   Özel teklifler ' i seçmediyseniz yalnızca Kullandıkça Öde seçeneğine sahip olursunuz.

   Kullanılacak planı seçin ve **Ayarla + abone ol**' u seçin.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Kurulum ve abone olma.":::

1. **Confluent bulut kaynağı** temelleri sayfasında, aşağıdaki değerleri sağlayın. İşiniz bittiğinde **İleri: Etiketler**' i seçin.

    :::image type="content" source="media/setup-basics.png" alt-text="Bir bulut kaynağını ayarlamak için formu.":::

    | Özellik | Açıklama |
    | ---- | ---- |
    | **Abonelik** | Açılan menüden, dağıtılacak Azure aboneliğini seçin. _Sahip_ veya _katkıda bulunan_ erişiminizin olması gerekir. |
    | **Kaynak grubu** | Yeni bir kaynak grubu oluşturmak mı yoksa mevcut bir kaynak grubunu mı kullanmak istediğinizi belirtin. Kaynak grubu, bir Azure çözümüne ilişkin kaynakları tutan bir kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../../azure-resource-manager/management/overview.md). |
    | **Uyumlu kuruluş adı** | Hizmet olarak yazılım (SaaS) kaynağını adlandırın. |
    | **Bölge** | Aşağı açılan menüden şu bölgelerden birini seçin: <br/><br/> Avustralya Doğu, Kanada Orta, Orta ABD, Doğu ABD, Doğu ABD 2, Fransa Orta, Kuzey Avrupa, Güneydoğu Asya, UK Güney, Orta Batı ABD, Batı Avrupa, Batı ABD 2 |
    | **Plan** | **Kullandıkça Öde** veya **taahhüt** seçeneğini belirleyin. |
    | **Fatura dönemi** | Seçilen faturalandırma planına göre önceden doldurulmuştur. |
    | **Fiyat** | Seçili confluent planına göre önceden doldurulmuştur. |

1. **Etiketler**' de, kaynağa uygulamak istediğiniz Etiketler için **ad** ve **değer** çiftlerini belirtin. Etiketleri girdikten sonra, **gözden geçir + oluştur**' u seçin.

    :::image type="content" source="media/setup-tags.png" alt-text="Proje etiketleri ekleyin.":::

1. Verdiğiniz ayarları gözden geçirin. Hazırlanıyor, **Oluştur**' u seçin.

1. Kaynağın oluşturulması birkaç dakika sürer. Dağıtım durumunu **Bildirimler**' de görebilirsiniz. Dağıtım bittikten sonra **genel bakış** sayfasını görüntülemek için kaynağı seçin.

    :::image type="content" source="media/deployment-status.png" alt-text="Dağıtım durumu.":::

   Hata alırsanız, bkz. [sorun giderme Apache Kafka, Confcloud Solutions](troubleshoot.md).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konluent bulut kaynağını yönetme](manage.md)
