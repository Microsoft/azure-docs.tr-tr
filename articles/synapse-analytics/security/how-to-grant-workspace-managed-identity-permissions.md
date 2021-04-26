---
title: SYNAPSE çalışma alanındaki yönetilen kimliğe izin verme
description: Azure SYNAPSE çalışma alanında yönetilen kimlik için izinlerin nasıl yapılandırılacağını açıklayan bir makale.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459000"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Çalışma alanı yönetilen kimliğine izin verme

Bu makalede, Azure SYNAPSE çalışma alanındaki yönetilen kimliğe nasıl izin vereceğiniz öğretilir. İzinler, sırasıyla, çalışma alanındaki adanmış SQL havuzlarının erişimine izin verir ve Azure portal aracılığıyla depolama hesabı ADLS 2..

>[!NOTE]
>Bu çalışma alanı yönetilen kimliği, bu belgenin geri kalanı aracılığıyla yönetilen kimlik olarak anılacaktır.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Adanmış SQL havuzuna yönetilen kimlik izinleri verme

Yönetilen kimlik, çalışma alanındaki adanmış SQL havuzlarına izinler verir. İzinler verildiğinde, adanmış SQL havuzuyla ilgili etkinlikleri gerçekleştiren işlem hatlarını düzenleyebilirsiniz. Azure portal kullanarak bir Azure SYNAPSE çalışma alanı oluşturduğunuzda, adanmış SQL havuzlarında yönetilen kimlik denetımı izinleri verebilirsiniz.

Azure SYNAPSE çalışma alanınızı oluştururken **güvenlik** ' i seçin. Ardından, **SQL havuzlarına erişmek için işlem hatları (çalışma alanının sistem tarafından atanmış kimliği olarak çalışır)** seçeneğini belirleyin.

![Adanmış SQL havuzlarında DENETIM izni](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>ADLS 2. depolama hesabına yönetilen kimlik izinleri verme

Azure SYNAPSE çalışma alanı oluşturmak için bir ADLS 2. depolama hesabı gereklidir. Azure SYNAPSE çalışma alanında Spark havuzlarını başarıyla başlatmak için, Azure SYNAPSE Managed Identity, bu depolama hesabındaki *Depolama Blobu veri katılımcısı* rolüne ihtiyaç duyuyor. Azure 'daki işlem hattı düzenlemesi bu rolden da faydalanır.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Çalışma alanı oluşturma sırasında yönetilen kimliğe izin verme

Azure SYNAPSE, Azure portal kullanarak Azure SYNAPSE çalışma alanını oluşturduktan sonra, yönetilen kimliğe Depolama Blobu veri katılımcısı rolünü vermeye çalışacaktır. **Temel bilgiler** sekmesinde ADLS 2. depolama hesabı ayrıntılarını sağlarsınız.

![Çalışma alanı oluşturma akışındaki temel bilgiler sekmesi](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

**Hesap adı** ve **dosya sistemi adında** ADLS 2. depolama hesabı ve FileSystem ' ı seçin.

![ADLS 2. depolama hesabı ayrıntıları sağlama](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Çalışma alanı oluşturan ADLS 2. depolama hesabının **sahibiyseniz** Azure SYNAPSE, *Depolama Blobu veri katılımcısı* rolünü yönetilen kimliğe atayacaktır. Girdiğiniz depolama hesabı ayrıntılarının altında aşağıdaki iletiyi görürsünüz.

![Başarılı Depolama Blobu veri katılımcısı ataması](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Çalışma alanı Oluşturucusu ADLS 2. depolama hesabının sahibi değilse, Azure SYNAPSE, *Depolama Blobu veri katılımcısı* rolünü yönetilen kimliğe atamaz. Depolama hesabı ayrıntılarının altında görünen ileti, çalışma alanı oluşturucuyu, *Depolama Blobu veri katılımcısı* rolünü yönetilen kimliğe vermek için yeterli izinlere sahip olmadıkları konusunda bilgilendirir.

![Başarısız Depolama Blobu veri katılımcısı ataması](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

İleti durumu gibi, *Depolama Blobu veri katılımcısı* yönetilen kimliğe atanmadığı sürece Spark havuzları oluşturamazsınız.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Çalışma alanı oluşturulduktan sonra yönetilen kimliğe izin ver

Çalışma alanı oluşturma sırasında, yönetilen kimliğe *Depolama Blobu verilerini katkıda* bulunan ' i atamadıysanız, ADLS 2. depolama hesabının **sahibi** bu rolü kimliğe el ile atar. Aşağıdaki adımlar el ile atamayı gerçekleştirmenize yardımcı olur.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>1. Adım: ADLS 2. depolama hesabına gidin Azure portal

Azure portal, ADLS 2. Depolama hesabını açın ve sol gezinden **genel bakış** ' ı seçin. Yalnızca *Depolama Blobu veri katılımcısı* rolünü kapsayıcı veya dosya sistemi düzeyinde atamanız gerekir. **Kapsayıcıları** seçin.  
![ADLS 2. depolama hesabına genel bakış](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>2. Adım: kapsayıcıyı seçin

Yönetilen kimliğin, çalışma alanı oluşturulduğunda sağlanmış olan kapsayıcıya (dosya sistemi) veri erişimi olmalıdır. Bu kapsayıcıyı veya dosya sistemini Azure portal bulabilirsiniz. Azure portal ' de Azure SYNAPSE çalışma alanını açın ve sol gezinmede **genel bakış** sekmesini seçin.
![ADLS 2. depolama hesabı kapsayıcısı](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Yönetilen kimliğe *Depolama Blobu veri katılımcısı* rolünü vermek için aynı kapsayıcı veya dosya sistemi ' ni seçin.
![Seçtiğiniz kapsayıcıyı veya dosya sistemini gösteren ekran görüntüsü.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>3. Adım: erişim denetimi 'ne gitme

**Access Control (IAM)** seçeneğini belirleyin.

![Erişim denetimi (ıAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>4. Adım: yeni bir rol ataması ekleme

**+ Ekle**'yi seçin.

![Yeni rol ataması Ekle](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>5. Adım: Azure rolünü seçin

**Depolama Blobu veri katılımcısı** rolünü seçin.

![Azure rolünü seçin](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>6. Adım: Azure AD güvenlik sorumlusunu seçme

**Azure AD Kullanıcı, Grup veya hizmet sorumlusu** ' nı seçerek açılan **erişime ata** ' yı seçin.

![AAD güvenlik sorumlusu seçin](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>7. Adım: yönetilen kimliği arama

Yönetilen kimliğin adı aynı zamanda çalışma alanı adıdır. **Select**' de Azure SYNAPSE çalışma alanı adınızı girerek yönetilen kimliğinizi arayın. Yönetilen kimliği listelenmiş olarak görmeniz gerekir.

![Yönetilen Kimliği bulma](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>8. Adım: yönetilen kimliği seçme

**Seçilen üyelerin** yönetilen kimliğini seçin. Rol atamasını eklemek için **Kaydet** ' i seçin.

![Yönetilen kimliği seçin](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>9. Adım: Depolama Blobu veri katılımcısı rolünün yönetilen kimliğe atandığını doğrulayın

**Access Control (IAM)** öğesini seçin ve ardından **rol atamaları**' nı seçin.

![Rol atamasını doğrula](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Yönetilen kimliğinizi, Depolama Blobu **veri katılımcısı rolü** ' nün altında listelenmiş olan *depolama blob verileri katkıda* bulunan rolüyle görmeniz gerekir. 
![ADLS 2. depolama hesabı kapsayıcısı seçimi](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Sonraki adımlar

[Çalışma alanı yönetilen kimliği](./synapse-workspace-managed-identity.md) hakkında daha fazla bilgi edinin
