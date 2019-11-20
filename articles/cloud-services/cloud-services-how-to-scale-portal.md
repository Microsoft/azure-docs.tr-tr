---
title: Portalda bir bulut hizmetini otomatik ölçeklendirme | Microsoft Docs
description: Azure 'da bir bulut hizmeti Web rolü veya çalışan rolü için otomatik ölçek kurallarını yapılandırmak üzere portalını nasıl kullanacağınızı öğrenin.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 7e106dbd237be79be924afadbe893669c4f3daf8
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359612"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Portalda bir bulut hizmeti için otomatik ölçeklendirmeyi yapılandırma

Koşullar, bir ölçek genişletme veya genişletme işlemini tetikleyen bir bulut hizmeti çalışan rolü için ayarlanabilir. Rolün koşulları, rolün CPU, disk veya ağ yüküne bağlı olabilir. Ayrıca, bir ileti kuyruğuna veya aboneliğinizle ilişkili başka bir Azure kaynağının ölçüsüne göre bir koşul da ayarlayabilirsiniz.

> [!NOTE]
> Bu makale, bulut hizmeti Web ve çalışan rollerine odaklanır. Doğrudan bir sanal makine (klasik) oluşturduğunuzda bir bulut hizmetinde barındırılır. Standart bir sanal makineyi bir [kullanılabilirlik kümesiyle](../virtual-machines/windows/classic/configure-availability-classic.md) ilişkilendirerek ölçeklendirebilir ve el ile açıp kapatabilirsiniz.

## <a name="considerations"></a>Dikkat edilmesi gerekenler
Uygulamanız için ölçeklendirmeyi yapılandırmadan önce aşağıdaki bilgileri göz önünde bulundurmanız gerekir:

* Ölçeklendirme, temel kullanımdan etkilenir.

    Daha büyük rol örnekleri daha fazla çekirdek kullanır. Bir uygulamayı yalnızca aboneliğinizin çekirdek limiti dahilinde ölçeklendirebilirsiniz. Örneğin, aboneliğinizin 20 çekirdek sınırına sahip olduğunu varsayalım. İki orta ölçekli bulut hizmeti (Toplam 4 çekirdek) ile bir uygulama çalıştırırsanız, diğer bulut hizmeti dağıtımlarını yalnızca diğer 16 çekirdeklerle ölçeklendirebilirsiniz. Boyutlar hakkında daha fazla bilgi için bkz. [bulut hizmeti boyutları](cloud-services-sizes-specs.md).

* Sıra ileti eşiğine göre ölçeklendirebilirsiniz. Kuyrukları kullanma hakkında daha fazla bilgi için bkz. [kuyruk depolama hizmetini kullanma](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Ayrıca, aboneliğinizle ilişkili diğer kaynakları da ölçeklendirebilirsiniz.

* Uygulamanızın yüksek oranda kullanılabilir olmasını sağlamak için, bunun iki veya daha fazla rol örneğiyle dağıtıldığından emin olmanız gerekir. Daha fazla bilgi için bkz. [hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/).

* Otomatik ölçeklendirme, yalnızca tüm roller **hazırlanıyor** durumunda gerçekleşir.  


## <a name="where-scale-is-located"></a>Ölçek bulunduğu yer
Bulut hizmetinizi seçtikten sonra, bulut hizmeti dikey penceresinin görünür olması gerekir.

1. Bulut hizmeti dikey penceresinde, **Roller ve örnekler** kutucuğunda, bulut hizmetinin adını seçin.   
   **ÖNEMLİ**: Rolün altındaki rol örneğine değil, bulut hizmeti rolüne tıkladığınızdan emin olun.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. **Ölçek** kutucuğunu seçin.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Otomatik ölçeklendirme
Bir rol için ölçek ayarlarını, iki moddan **el ile** veya **Otomatik**olarak yapılandırabilirsiniz. El ile beklenen, örneklerin mutlak sayısını ayarlarsınız. Otomatik olarak, ne kadar ölçeklendirmeniz gerektiğini belirleyen kurallar ayarlamanıza olanak sağlar.

**Ölçek ölçütü** seçeneğini, **zamanlama ve performans kuralları**olarak ayarlayın.

![Profil ve kuralla bulut hizmetleri ölçek ayarları](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Var olan bir profil.
2. Üst profil için bir kural ekleyin.
3. Başka bir profil ekleyin.

**Profil ekle**' yi seçin. Profil, ölçek için hangi modu kullanmak istediğinizi belirler: **her zaman**, **yinelenme**, **sabit tarih**.

Profili ve kuralları yapılandırdıktan sonra üstteki **Kaydet** simgesini seçin.

#### <a name="profile"></a>Profil
Profil, ölçek için minimum ve maksimum örnekleri, Ayrıca bu ölçek aralığı etkin olduğunda da ayarlar.

* **Her zaman**

    Bu örnek aralığını her zaman kullanılabilir durumda tutun.  

    ![Her zaman ölçeklendirilen bulut hizmeti](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Yineleme**

    Haftanın ölçeklenmesi için bir gün kümesi seçin.

    ![Yineleme zamanlaması ile bulut hizmeti ölçeği](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Sabit Tarih**

    Rolü ölçeklendirmek için sabit bir tarih aralığı.

    ![Sabit bir tarihle bulut hizmeti ölçeği](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Profili yapılandırdıktan sonra, profil dikey penceresinin altındaki **Tamam** düğmesini seçin.

#### <a name="rule"></a>Kural
Kurallar bir profile eklenir ve ölçeği tetikleyen bir koşulu temsil eder.

Kural tetikleyicisi, koşullu bir değer ekleyebileceğiniz bulut hizmeti ölçüsünü (CPU kullanımı, disk etkinliği veya ağ etkinliği) temel alır. Ayrıca, tetikleyicinin bir ileti kuyruğuna veya aboneliğinizle ilişkili başka bir Azure kaynağının ölçüsüne göre tetiklemesine sahip olabilirsiniz.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Kuralı yapılandırdıktan sonra, kural dikey penceresinin altındaki **Tamam** düğmesini seçin.

## <a name="back-to-manual-scale"></a>El ile ölçeğe geri dön
[Ölçek ayarlarına](#where-scale-is-located) gidin ve **Ölçek ölçütü** seçeneğini, **el ile girdiğim bir örnek sayısı**olarak ayarlayın.

![Profil ve kuralla bulut hizmetleri ölçek ayarları](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Bu ayar, rolden otomatik ölçeklendirmeyi kaldırır ve ardından örnek sayısını doğrudan ayarlayabilirsiniz.

1. Ölçek (el ile veya otomatik) seçeneği.
2. Ölçeklendirilen örnekleri ayarlamak için bir rol örneği kaydırıcı.
3. Ölçeklendirilecek rolün örnekleri.

Ölçek ayarlarını yapılandırdıktan sonra üstteki **Kaydet** simgesini seçin.
