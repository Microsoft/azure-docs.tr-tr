---
title: Veri & depolama önerileri-Azure Güvenlik Merkezi
description: Bu belge, Azure Güvenlik Merkezi 'ndeki, verilerinizi ve Azure SQL hizmetinizi korumanıza ve güvenlik ilkeleriyle uyumlu kalmanıza yardımcı olan önerilere yöneliktir.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: memildin
ms.openlocfilehash: 21da7c49b158345894ee7fdc164d205bcefe1640
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663902"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde Azure veri ve depolama hizmetlerini koruma
Bu konu başlığı altında, veri ve depolama kaynakları için güvenlik önerilerini görüntüleme ve uygulama işlemlerinin nasıl yapılacağı gösterilmektedir. Azure Güvenlik Merkezi, Azure kaynaklarınızın güvenlik durumunu analiz edilirken bu önerileri buldu.

## <a name="view-your-data-security-information"></a>Veri güvenliği bilgilerinizi görüntüleyin

1. **Kaynak güvenliği durumu** bölümünde, **veri ve depolama kaynakları**' na tıklayın.

    ![Veri & depolama kaynakları](./media/security-center-monitoring/click-data.png)

    Veri **güvenliği** sayfası, veri kaynaklarına yönelik önerilerle açılır.

    ![Veri Kaynakları](./media/security-center-monitoring/sql-overview.png)

    Bu sayfadan şunları yapabilirsiniz:

    * **Genel bakış** sekmesine tıkladığınızda, düzeltilen tüm veri kaynakları önerileri listelenir. 
    * Her sekmeye tıklayın ve önerileri kaynak türüne göre görüntüleyin.

    > [!NOTE]
    > Depolama şifrelemesi hakkında daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Veri kaynağı üzerindeki öneriyi Düzeltme

1. Kaynak sekmeleriyle bir kaynağa tıklayın. Bilgi sayfası, düzeltilen önerilerin listelenmesi halinde açılır.

    ![Kaynak bilgileri](./media/security-center-monitoring/sql-recommendations.png)

2. Bir öneriye tıklayın. Öneri sayfası açılır ve öneriyi uygulamak için **düzeltme adımlarını** görüntüler.

   ![Düzeltme adımları](./media/security-center-monitoring/remediate1.png)

3. **Eylem al**' a tıklayın. Kaynak ayarları sayfası görüntülenir.

    ![Öneriyi etkinleştir](./media/security-center-monitoring/remediate2.png)

4. **Düzeltme adımlarını** Izleyin ve **Kaydet**' e tıklayın.

## <a name="data-and-storage-recommendations"></a>Veri ve depolama önerileri

|Kaynak türü|Güvenlik puanı|Öneri|Açıklama|
|----|----|----|----|
|Depolama hesabı|20|Depolama hesaplarına Güvenli aktarım etkinleştirilmelidir|Güvenli aktarım, depolama hesabınızı yalnızca güvenli bağlantılardan (HTTPS) istekleri kabul edecek şekilde zorlayan bir seçenektir. HTTPS, sunucu ile hizmet arasında kimlik doğrulaması sağlar ve aradaki bağlantıyı izinsiz izleme, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı korur.|
|Redis|20|Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir|Redsıs için yalnızca SSL aracılığıyla bağlantıları Azure önbelleğine etkinleştirin. Güvenli bağlantı kullanımı, sunucu ve hizmet arasında kimlik doğrulaması sağlar ve aradaki bağlantıyı izinsiz izleme, gizlice dinleme ve oturum ele geçirme gibi ağ katmanı saldırılarına karşı korur.|
|SQL|15|SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir|Rest verilerini korumak ve uyumluluk gereksinimlerini karşılamak için saydam veri şifrelemesini etkinleştirin.|
|SQL|15|SQL Server denetimi etkinleştirilmelidir|Azure SQL sunucuları için denetimi etkinleştirin. (Yalnızca Azure SQL hizmeti. , Sanal makinelerinizde çalışan SQL içermez.)|
|Data Lake Analytics|5|Data Lake Analytics tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|Data Lake Store|5|Azure Data Lake Store tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|SQL|30|SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir|SQL güvenlik açığı değerlendirmesi, veritabanınızı güvenlik açıklarına karşı tarar ve yanlış yapılandırma, aşırı izin ve korunmayan gizli veriler gibi en iyi uygulamalardan sapmaları gösterir. Bulunan güvenlik açıklarının çözümlenmesi, veritabanı güvenlik hazırlılığını büyük ölçüde iyileştirebilir.|
|SQL|20|SQL Server için bir Azure AD yöneticisi sağlama|Azure AD kimlik doğrulamasını etkinleştirmek için SQL sunucunuz için bir Azure AD yöneticisi sağlayın. Azure AD kimlik doğrulaması, veritabanı kullanıcıları ve diğer Microsoft Hizmetleri için Basitleştirilmiş izin yönetimi ve merkezi kimlik yönetimine izin verebilir.|
|Depolama hesabı|15|Güvenlik Duvarı ve sanal ağ yapılandırmalarına sahip depolama hesaplarına erişim kısıtlı olmalıdır|Depolama hesabı güvenlik duvarı ayarlarınızda Kısıtlanmamış ağ erişimini denetleyin. Bunun yerine, ağ kurallarını yalnızca izin verilen ağların uygulamalarının depolama hesabına erişebilmesi için yapılandırın. Belirli Internet veya şirket içi istemcilerden gelen bağlantılara izin vermek için, belirli Azure sanal ağlarından veya genel Internet IP adresi aralıklarına giden trafiğe erişim izni verebilirsiniz.|
|Depolama hesabı|1|Depolama hesaplarının yeni Azure Resource Manager kaynaklarına geçirilmesi gerekir|Depolama hesaplarınız için yeni Azure Resource Manager v2 kullanarak, daha güçlü erişim denetimi (RBAC), daha iyi denetim, Kaynak Yöneticisi tabanlı dağıtım ve idare, yönetilen kimliklere erişim, için Anahtar Kasası erişimi gibi güvenlik geliştirmeleri sağlayın. daha kolay güvenlik yönetimi için gizlilikler ve Azure AD tabanlı kimlik doğrulaması ve Etiketler ve kaynak grupları desteği.|

## <a name="see-also"></a>Ayrıca bkz.
Diğer Azure kaynak türlerine uygulanan öneriler hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Azure Güvenlik Merkezi'nde makinelerinizi ve uygulamalarınızı koruma](security-center-virtual-machine-protection.md)
* [Azure Güvenlik Merkezi'nde ağınızı koruma](security-center-network-recommendations.md)

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
