---
title: Azure Red Hat OpenShift kümesi yönetici rolü | Microsoft Docs
description: Azure Red Hat OpenShift Küme Yöneticisi rolünün atanması ve kullanımı
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636280"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift müşteri yöneticisi rolü

> [!IMPORTANT]
> Azure Red Hat Openshıft 3,11, 30 Haziran 2022 tarihinde kullanımdan kaldırılacaktır. Yeni Azure Red Hat OpenShift 3,11 kümelerinin oluşturulmasına yönelik destek, 30 Kasım 2020 ile devam eder. Aşağıdaki kullanımdan kaldırma, kalan Azure Red Hat Openshıft 3,11 kümeleri güvenlik açıklarını engellemek için kapatılacak.
> 
> [Azure Red Hat OpenShift 4 kümesi oluşturmak](tutorial-create-cluster.md)için bu kılavuzu izleyin.
> Belirli sorularınız varsa [lütfen bizimle iletişime geçin](mailto:arofeedback@microsoft.com).

Azure Red Hat OpenShift kümesinin küme yöneticisidir. Hesabınızın izinleri arttı ve Kullanıcı tarafından oluşturulan tüm projelere erişimi vardır.

Hesabınıza bir müşteri-yönetici-küme yetkilendirme rolü bağlandığında, bir projeyi otomatik olarak yönetebilir.

> [!Note] 
> Müşteri-yönetici-küme kümesi rolü, küme yönetici kümesi rolüyle aynı değildir.

Örneğin, bir dizi fiil () ile ilişkili eylemleri `create` bir kaynak adları kümesi () üzerinde çalışacak şekilde çalıştırabilirsiniz `templates` . Bu rollerin ayrıntılarını ve fiil ve kaynak kümelerini görüntülemek için aşağıdaki komutu çalıştırın:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Fiil adları tüm doğrudan komutlara eşlenmeyebilir `oc` . Bunlar, gerçekleştirebileceğiniz CLı işlemleri türlerine daha fazla eş alabilir. 

Örneğin, fiil olması, `list` kaynak adının () tüm nesnelerinin bir listesini görüntüleyebilen anlamına gelir `oc get` . `get`Fiil, adını () biliyorsanız belirli bir nesnenin ayrıntılarını görüntüleyebilen anlamına gelir `oc describe` .

## <a name="configure-the-customer-administrator-role"></a>Müşteri Yöneticisi rolünü yapılandırma

Müşteri-yönetici-küme kümesi rolünü, bayrağı sağlayarak yalnızca küme oluşturma sırasında yapılandırabilirsiniz `--customer-admin-group-id` . Bu alan Azure portal Şu anda yapılandırılamaz. Azure Active Directory ve Yöneticiler grubunu yapılandırma hakkında bilgi edinmek için bkz. [Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Müşteri Yöneticisi rolünde üyeliği onaylama

Müşteri yöneticisi grubundaki üyeliğinizi onaylamak için OpenShift CLı komutlarını veya ' i deneyin `oc get nodes` `oc projects` . `oc get nodes` , müşteri yönetici kümesi rolüne sahipseniz bir düğüm listesi ve yalnızca müşteri-yönetici-proje rolüne sahipseniz bir izin hatası gösterir. `oc projects` , yalnızca çalıştığınız projelerin aksine kümedeki tüm projeleri gösterir.

Kümenizdeki rol ve izinleri daha fazla incelemek için [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) komutunu kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Müşteri-yönetici-küme kümesi rolünü yapılandırın:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift için Azure Active Directory Tümleştirmesi](howto-aad-app-configuration.md)
