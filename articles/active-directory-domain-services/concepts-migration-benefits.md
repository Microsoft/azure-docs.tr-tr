---
title: Azure AD Domain Services 'de klasik dağıtım geçişinin avantajları | Microsoft Docs
description: Klasik Azure Active Directory Domain Services dağıtımını Kaynak Yöneticisi dağıtım modeline geçirme avantajları hakkında daha fazla bilgi edinin
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: justinha
ms.openlocfilehash: 8cc5f5ebf389d35df02474d0561dc7827cde4d0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620095"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services içindeki klasik to Kaynak Yöneticisi dağıtım modelinden geçişin avantajları

Azure Active Directory Domain Services (Azure AD DS), klasik dağıtım modelini kullanan mevcut bir yönetilen etki alanını Kaynak Yöneticisi dağıtım modeline geçirebilmenizi sağlar. Kaynak Yöneticisi dağıtım modelini kullanan Azure AD DS yönetilen etki alanları, hassas parola ilkesi, denetim günlükleri ve hesap kilitleme koruması gibi ek özellikler sağlar.

Bu makalede geçiş avantajları özetlenmektedir. Başlamak için bkz. [Klasik sanal ağ modelinden Kaynak Yöneticisi Azure AD Domain Services geçirme][howto-migrate].

> [!NOTE]
> 2017 ' de Azure AD Domain Services Azure Resource Manager ağda barındırana bilgisayar için kullanılabilir duruma geldi. Bu tarihten sonra, Azure Resource Manager modern yeteneklerini kullanarak daha güvenli bir hizmet oluşturuyoruz. Azure Resource Manager dağıtımları klasik dağıtımları tamamen yerine getirmek için Azure AD DS klasik sanal ağ dağıtımları 1 Mart 2023 ' de kullanımdan kaldırılacaktır.
>
> Daha fazla bilgi için bkz. [resmi kullanımdan kaldırma bildirimi](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Geçiş avantajları

Geçiş işlemi, klasik dağıtım modelini kullanan mevcut bir yönetilen etki alanını alır ve Kaynak Yöneticisi dağıtım modelini kullanacak şekilde geçer. Yönetilen bir etki alanını klasik ' ten Kaynak Yöneticisi dağıtım modeline geçirdiğinizde, makineleri yönetilen etki alanına ekleme veya yönetilen etki alanını silme ve sıfırdan bir tane oluşturma ihtiyacını ortadan kaldırmak zorunda kalmaz. VM 'Ler, geçiş işleminin sonunda yönetilen etki alanına katılalmaya devam eder.

Geçişten sonra Azure AD DS, yalnızca aşağıdaki gibi Kaynak Yöneticisi dağıtım modelini kullanan etki alanları için kullanılabilen birçok özellik sağlar:

* [Hassas parola ilkesi desteği][password-policy].
* Azure AD ve Azure AD Domain Services arasında daha hızlı eşitleme hızları.
* [Azure AD][attributes]  -  *Yöneticisi* ve *EmployeeID* ile eşitlendiğinde iki yeni öznitelik.
* [SKU 'yu yükselttiğinizde][skus]daha yüksek güç alan denetleyicilere erişin.
* AD hesabı kilitleme koruması.
* [Yönetilen etki alanındaki uyarılar Için e-posta bildirimleri][email-alerts].
* [Azure çalışma kitaplarını ve Azure izleyici 'Yi kullanarak denetim günlüklerini ve oturum açma etkinliğini görüntüleyin][workbooks].
* Desteklenen bölgelerde [Azure kullanılabilirlik alanları][availability-zones].
* [Azure dosyaları][azure-files], [HD Insights][hd-insights]ve [Windows sanal masaüstü][wvd]gibi diğer Azure ürünleriyle tümleştirmeler.
* Destek, daha fazla telemetri erişimine sahiptir ve daha etkili bir şekilde sorun gidermeye yardımcı olabilir.
* Yönetilen etki alanı denetleyicilerindeki veriler için [Azure yönetilen disklerini][managed-disks] kullanarak bekleyen şifreleme.

Kaynak Yöneticisi dağıtım modeli kullanan yönetilen etki alanları, en son yeni özelliklerle güncel kalmanıza yardımcı olur. Klasik dağıtım modelini kullanan yönetilen etki alanları için yeni özellikler kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için bkz. [Klasik sanal ağ modelinden Kaynak Yöneticisi Azure AD Domain Services geçirme][howto-migrate].

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
