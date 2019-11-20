---
title: Azure DNS özel bölgelerin sanal ağ bağlantısı alt kaynağı nedir?
description: Azure DNS özel bölgesine sanal ağ bağlantısı alt kaynağına genel bakış
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: c5675dea2676122d96276b7cdb90b4c3c5340c7a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961235"
---
# <a name="what-is-a-virtual-network-link"></a>Sanal ağ bağlantısı nedir?

Azure 'da özel bir DNS bölgesi oluşturduktan sonra herhangi bir sanal ağdan hemen erişilemez. Bu ağda barındırılan bir VM 'nin özel DNS bölgesine erişebilmesi için onu bir sanal ağa bağlamanız gerekir.
Özel bir DNS bölgesini bir sanal ağla bağlamak için özel DNS bölgesi altında bir sanal ağ bağlantısı oluşturmanız gerekir. Her özel DNS bölgesi, bir sanal ağ bağlantısı alt kaynakları koleksiyonuna sahiptir. Bu kaynakların her biri, bir sanal ağ bağlantısını temsil eder.

Bir sanal ağı, bir kayıt sanal ağı veya bir çözümleme sanal ağı olarak özel bir DNS bölgesine bağlayabilirsiniz.

## <a name="registration-virtual-network"></a>Kayıt sanal ağı

Özel bir DNS bölgesi ve bir sanal ağ arasında [bağlantı oluşturduğunuzda](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) , sanal MAKINELER için DNS kayıtlarının yeniden [kaydedilmesini](./private-dns-autoregistration.md) açma seçeneğiniz vardır. Bu seçeneği belirlerseniz, sanal ağ özel DNS bölgesi için bir kayıt sanal ağı olur. Ağda dağıttığınız sanal makineler için bir DNS kaydı otomatik olarak oluşturulur. Sanal ağda zaten dağıttığınız sanal makineler için DNS kayıtları oluşturulur. Sanal ağ perspektifinden, özel DNS bölgesi söz konusu sanal ağın kayıt bölgesi olur.
Bir özel DNS bölgesinin birden çok kayıt sanal ağı olabilir, ancak her sanal ağın kendisiyle ilişkili tam olarak bir kayıt bölgesi olabilir.

## <a name="resolution-virtual-network"></a>Çözümleme sanal ağı

Özel bir DNS bölgesi altında bir sanal ağ bağlantısı oluşturduğunuzda ve DNS kaydı oto kaydını etkinleştirmemelidir seçeneğini belirlediğinizde, sanal ağ yalnızca bir sanal ağ olarak değerlendirilir. Bu tür ağlarda dağıtılan sanal makineler için DNS kayıtları, bağlantılı özel DNS bölgesinde otomatik olarak oluşturulmaz. Ancak, bu tür bir ağda dağıtılan sanal makineler DNS kayıtlarını özel DNS bölgesinden başarıyla sorgulayabilir. Bu kayıtlar sizin tarafınızdan el ile oluşturulabilir veya özel DNS bölgesi ile kayıt ağları olarak bağlanmış diğer sanal ağlardan doldurulabilir.
Bir özel DNS bölgesinin birden çok çözünürlükte sanal ağı olabilir ve bir sanal ağ ile ilişkili birden fazla çözümleme bölgesi olabilir.

## <a name="limits"></a>Değerleri

Kaç tane kayıt ve çözüm ağı olduğunu anlamak için özel DNS bölgelerine nasıl bağlayabileceğinizi [Azure DNS sınırlara](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits) bakın

## <a name="other-considerations"></a>Diğer konular

* Klasik dağıtım modeli kullanılarak dağıtılan sanal ağlar desteklenmez.

* Özel bir DNS bölgesi ve bir sanal ağ arasında yalnızca bir bağlantı oluşturabilirsiniz.

* Özel bir DNS bölgesi altındaki her sanal ağ bağlantısı, özel DNS bölgesinin bağlamı içinde benzersiz bir ada sahip olmalıdır. Farklı özel DNS bölgelerinde aynı ada sahip bağlantılara sahip olabilirsiniz.

* Bir sanal ağ bağlantısı oluşturduktan sonra, sanal ağ bağlantısı kaynağının "bağlantı durumu" alanını denetleyin. Sanal ağın boyutuna bağlı olarak, bağlantının işlem yapılmadan ve bağlantı durumunun *tamamlandı*olarak değiştiği birkaç dakika sürebilir.

* Bir sanal ağı sildiğinizde, farklı özel DNS bölgelerinde onunla ilişkili tüm sanal ağ bağlantıları ve otomatik kaydedilmiş DNS kayıtları otomatik olarak silinir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) kullanarak bir sanal ağı özel bir DNS bölgesine bağlamayı öğrenin

* [Azure PowerShell](./private-dns-getstarted-powershell.md) veya [Azure CLI](./private-dns-getstarted-cli.md)kullanarak Azure DNS bir özel bölge oluşturmayı öğrenin.

* Azure DNS özel bölgelerle gerçekleştirilen bazı yaygın [özel bölge senaryoları](./private-dns-scenarios.md) hakkında bilgi edinin.

* Belirli işlemler için bekleneceğiniz belirli davranış dahil olmak üzere Azure DNS özel bölgeler hakkında sık sorulan sorular ve yanıtlar için, bkz. [özel DNS SSS](./dns-faq-private.md).
