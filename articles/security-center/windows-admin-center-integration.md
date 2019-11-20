---
title: Windows Yönetim Merkezi 'ni Azure Güvenlik Merkezi ile tümleştirme | Microsoft Docs
description: Bu makalede, Azure Güvenlik Merkezi 'nin Windows Yönetim Merkezi ile nasıl tümleştirileceği açıklanmaktadır.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 842c7c81e3bf9615eb56d50ee2d6fce794845b6e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960706"
---
# <a name="integrate-azure-security-center-with-windows-admin-center-preview"></a>Azure Güvenlik Merkezi 'Ni Windows Yönetim Merkezi ile tümleştirme (Önizleme)

Windows Yönetim Merkezi, Windows sunucularınız için bir yönetim aracıdır. Sistem yöneticilerinin en sık kullanılan yönetim araçlarının çoğunluğuna erişebilmeleri için tek bir konumdur. Windows Yönetim Merkezi 'nin içinden, şirket içi sunucularınızı doğrudan Azure Güvenlik Merkezi 'ne ekleyebilirsiniz. Daha sonra, güvenlik önerilerinizi ve uyarılarınızı bir Özet olarak doğrudan Windows Yönetim Merkezi deneyimi ' nde görüntüleyebilirsiniz.

> [!NOTE]
> Windows Yönetim Merkezi tümleştirmesini etkinleştirmek için Azure aboneliğiniz ve ilişkili Log Analytics çalışma alanının her ikisi de Güvenlik Merkezi 'nin standart katmanının etkinleştirilmiş olması gerekir.
> Daha önce bunu abonelikte ve çalışma alanında kullanmadıysanız Standart katman ilk 30 gün boyunca ücretsizdir. Daha fazla bilgi için bkz. [fiyatlandırma bilgileri sayfası](security-center-pricing.md).
>

Windows Yönetim Merkezi 'nden Azure Güvenlik Merkezi 'ne bir sunucu başarıyla eklendi, şunları yapabilirsiniz:

* Windows Yönetim Merkezi 'nde Güvenlik Merkezi uzantısının içindeki güvenlik uyarılarını ve önerilerini görüntüleme
* Güvenlik duruşunu görüntüleyin ve Azure portal (veya bir API aracılığıyla) Güvenlik Merkezi 'nde Windows Yönetim Merkezi tarafından yönetilen sunucularınızın ek ayrıntılı bilgilerini alın

Bu iki aracı birleştirerek Güvenlik Merkezi, kaynak: Windows Yönetim Merkezi 'nde yönetilen şirket içi sunucuları, VM 'lerinizi ve ek PaaS iş yüklerinizi koruyan tüm güvenlik bilgilerinizi görüntülemek için tek bir cam bölmesidir.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Windows Yönetim Merkezi tarafından yönetilen sunucuları güvenlik merkezi 'ne ekleme

1. Windows Yönetim Merkezi 'nden sunucularınızdan birini seçin ve **Araçlar** bölmesinde Azure Güvenlik Merkezi uzantısını seçin:

    ![Windows Yönetim Merkezi 'nde Azure Güvenlik Merkezi uzantısı](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Sunucu zaten güvenlik merkezi eklendi, kurulum penceresi görünmez.

1. **Azure 'Da oturum aç**' a tıklayın ve kurulumu yapın.
    Windows Yönetim Merkezi uzantısını Azure Güvenlik Merkezi 'ne ekleme ![](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Sunucunuzu Güvenlik Merkezi 'ne bağlamak için yönergeleri izleyin. Gerekli ayrıntıları girdikten ve onaylandıktan sonra, güvenlik merkezi aşağıdakilerin tümünün doğru olduğundan emin olmak için gerekli yapılandırma değişikliklerini yapar:
    * Bir Azure Gateway kaydedilir.
    * Sunucuda, raporlamak için bir çalışma alanı ve ilişkili bir abonelik vardır.
    * Güvenlik Merkezi 'nin standart katmanı Log Analytics çözümü çalışma alanında etkinleştirilmiştir. Bu çözüm, bu çalışma alanına rapor veren *Tüm* sunucular ve sanal makineler Için Güvenlik Merkezi 'nin Standart katman özelliklerini sağlar.
    * Abonelik üzerinde sanal makine için Güvenlik Merkezi 'nin Standart katman fiyatlandırması etkinleştirilmiştir.
    * Microsoft Monitoring Agent (MMA) sunucuya yüklendi ve seçilen çalışma alanına raporlamak üzere yapılandırıldı. Sunucu zaten başka bir çalışma alanına bildirirse, yeni seçilen çalışma alanına da rapor vermek için yapılandırılır.

    > [!NOTE]
    > Önerilerin görünmesi için ekleme biraz zaman alabilir. Aslında, sunucu etkinliklerinize bağlı olarak *herhangi bir* uyarı almazsınız. Uyarılarınızın doğru şekilde çalıştığını sınamak için test uyarıları oluşturmak üzere [Uyarı doğrulama yordamındaki](security-center-alert-validation.md)yönergeleri izleyin.


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Windows Yönetim Merkezi 'nde güvenlik önerilerini ve uyarılarını görüntüleme

Eklendi bir kez, uyarı ve önerilerinizi doğrudan Windows Yönetim Merkezi 'nin Azure Güvenlik Merkezi alanında görüntüleyebilirsiniz. Azure portal görüntülemek için bir öneri veya uyarıya tıklayın. Burada, daha fazla bilgi edinirsiniz ve sorunları nasıl düzelteceğinizi öğreneceksiniz.

[Windows Yönetim Merkezi 'nde görülen Güvenlik Merkezi önerilerini ve uyarılarını ![](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Güvenlik Merkezi 'nde Windows Yönetim Merkezi tarafından yönetilen sunucular için güvenlik önerilerini ve uyarılarını görüntüleme
Azure Güvenlik Merkezi 'nden:

* Tüm Windows Yönetim Merkezi sunucularınızın güvenlik önerilerini görüntülemek için, **işlem & uygulamaları** açın ve **VM 'ler ve bilgisayarlar** sekmesine tıklayın. listeyi aşağıda gösterildiği gibi "sunucu" kaynağına göre filtreleyin:

    [![Windows Yönetim Merkezi tarafından yönetilen sunucular için güvenlik önerilerini görüntüleme](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Tüm Windows Yönetim Merkezi sunucularınızın güvenlik uyarılarını görüntülemek için **güvenlik uyarılarını**açın. **Filtre** ' ye tıklayın ve **yalnızca** "Azure dışı" ' ın seçili olduğundan emin olun:

    ![Windows Yönetim Merkezi tarafından yönetilen sunucular için güvenlik uyarılarını filtreleme](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Windows Yönetim Merkezi tarafından yönetilen sunucular için güvenlik uyarılarını görüntüleme](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)