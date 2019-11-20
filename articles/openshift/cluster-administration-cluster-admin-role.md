---
title: Azure Red Hat OpenShift kümesi yönetici rolü | Microsoft Docs
description: Azure Red Hat OpenShift Küme Yöneticisi rolünün atanması ve kullanımı
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936920"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift müşteri yöneticisi rolü

Azure Red Hat OpenShift kümesinin küme yöneticisidir. Hesabınızın izinleri arttı ve Kullanıcı tarafından oluşturulan tüm projelere erişimi vardır.

Hesabınız OSA-Müşteri Yöneticisi yetkilendirme rolüne bağlıysa, bir projeyi otomatik olarak yönetebilir.

> [!Note] 
> OSA-müşteri-yönetici kümesi rolü, küme yönetici kümesi rolüyle aynı değildir.


Örneğin, bir dizi fiil (`create`) ile ilişkili eylemleri bir kaynak adı kümesi üzerinde (`templates`) çalıştırabilirsiniz. Bu rollerin ayrıntılarını ve fiil ve kaynak kümelerini görüntülemek için aşağıdaki komutu çalıştırın:

`$ oc describe clusterrole/osa-customer-admin`

Fiil adları, tüm doğrudan `oc` komutlarına eşlenmeyebilir. Bunlar, gerçekleştirebileceğiniz CLı işlemleri türlerine daha fazla eş alabilir. 

Örneğin, `list` fiili olması, kaynak adının (`oc get`) tüm nesnelerinin bir listesini görüntüleyebilen anlamına gelir. @No__t-0 fiili, adını biliyorsanız belirli bir nesnenin ayrıntılarını görüntüleyebilen anlamına gelir (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Müşteri Yöneticisi rolünü yapılandırma

Müşteri Yöneticisi rolünü yalnızca küme oluşturma sırasında, `--customer-admin-group-id` ' ı girerek yapılandırabilirsiniz. Azure Active Directory ve Yöneticiler grubunu yapılandırma hakkında bilgi edinmek için bkz. [Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Sonraki adımlar

OSA-müşteri-yönetici rolünü yapılandırın:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi](howto-aad-app-configuration.md)
