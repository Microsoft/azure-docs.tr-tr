---
title: Güvenlik ilkeleriyle çalışma | Microsoft Docs
description: Bu makalede, Azure Güvenlik Merkezi 'nde güvenlik ilkeleriyle nasıl çalışılacağı açıklanır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906863"
---
# <a name="working-with-security-policies"></a>Güvenlik ilkeleriyle çalışma

Bu makalede güvenlik ilkelerinin nasıl yapılandırıldığı ve Güvenlik Merkezi 'nde nasıl görüntüleneceği açıklanmaktadır. 

## <a name="introduction-to-security-policies"></a>Güvenlik ilkelerine giriş

Güvenlik ilkesi, iş yüklerinizin istenen yapılandırmasını tanımlar ve şirketinizin veya düzenleyicilerinin güvenlik gereksinimleriyle uyumlu olmanıza yardımcı olur.

Azure Güvenlik Merkezi, seçtiğiniz ilkelere göre güvenlik önerilerini sağlar. Güvenlik Merkezi ilkeleri, Azure Ilkesinde oluşturulan ilke girişimlerini temel alır. İlkelerinizi yönetmek ve ilkeleri yönetim grupları ve birden çok abonelik arasında ayarlamak için [Azure ilkesi](../governance/policy/overview.md) 'ni kullanabilirsiniz.

Güvenlik Merkezi güvenlik ilkeleriyle çalışma için aşağıdaki seçenekleri sunar:

* **Yerleşik varsayılan Ilkeyi görüntüleyin ve düzenleyin** -Güvenlik Merkezi 'ni etkinleştirdiğinizde, tüm güvenlik merkezi kayıtlı aboneliklerine (ücretsiz veya standart katmanlara) ' ASC default ' adlı yerleşik bir girişim otomatik olarak atanır. Bu girişimi özelleştirmek için, içindeki ilkeleri tek tek etkinleştirebilir veya devre dışı bırakabilirsiniz. Kullanıma hazır seçenekleri anlamak için [yerleşik güvenlik ilkeleri](security-center-policy-definitions.md) listesine bakın.

* **Kendi özel Ilkelerinizi ekleyin** -aboneliğinize uygulanan güvenlik girişimlerini özelleştirmek istiyorsanız, bunu Güvenlik Merkezi içinde yapabilirsiniz. Daha sonra, makineleriniz oluşturduğunuz ilkeleri izleyememesi durumunda öneriler alacaksınız. Özel ilkeler oluşturma ve atama hakkında yönergeler için bkz. [özel güvenlik Ilkeleri kullanma](custom-security-policies.md).

* **Mevzuat uyumluluk Ilkeleri ekleme** -Güvenlik Merkezi 'nin mevzuat uyumluluk panosu, ortamınızdaki tüm değerlendirmelerin durumunu belirli bir standart veya yönetmeliği bağlamında gösterir (örneğin, Azure CIS, nıst SP 800-53 R4, SWIFT CSP) CSCF-V2020). Daha fazla bilgi için bkz. [Mevzuat uyumluluğunuzu artırma](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Güvenlik ilkelerinizi yönetme

Güvenlik Merkezi'nde güvenlik ilkelerinizi görüntüleme:

1. **Güvenlik Merkezi** panosunda **güvenlik ilkesi**' ni seçin.

    ![İlke Yönetimi bölmesi](./media/security-center-policies/security-center-policy-mgt.png)

   **İlke yönetimi** ekranında yönetim gruplarının, aboneliklerin ve çalışma alanlarının yanı sıra yönetim grubu yapınızı da görebilirsiniz.

1. İlkelerini görüntülemek istediğiniz aboneliği veya yönetim grubunu seçin.

1. Bu abonelik veya yönetim grubu için güvenlik ilkesi sayfası görüntülenir. Kullanılabilir ve atanan ilkeleri gösterir.

   ![ilke ekranı](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Varsayılan ilkenize göre "MG devralınmış" etiketi varsa, ilke bir yönetim grubuna atandı ve görüntülemekte olduğunuz aboneliğin Devralındığı anlamına gelir.


1. Bu sayfadaki kullanılabilir seçenekler arasından seçim yapın:

    1. Sektör ilkeleriyle çalışmak için **daha fazla standartlar Ekle**' ye tıklayın. Daha fazla bilgi için bkz. [dinamik uyumluluk paketlerine güncelleştirme](update-regulatory-compliance-packages.md).

    1. Özel girişimler atamak ve yönetmek için **özel girişimler Ekle**' ye tıklayın. Daha fazla bilgi için bkz. [özel güvenlik Ilkeleri kullanma](custom-security-policies.md).

    1. Varsayılan ilkeyi görüntülemek ve düzenlemek için, **geçerli Ilkeyi görüntüle** ' ye tıklayın ve aşağıda açıklandığı gibi devam edin. 

       ![ilke ekranı](./media/security-center-policies/policy-screen.png)
       
       Bu **güvenlik ilkesi** ekranı, seçtiğiniz abonelik veya yönetim grubunda atanan ilkeler tarafından gerçekleştirilen eylemi yansıtır.
       
       * En üstteki bağlantıları kullanarak abonelik veya yönetim grubuna uygulanan bir ilke **atamasını** açın. Bu bağlantılar atamaya erişmenizi ve ilkeyi düzenlemenizi veya devre dışı bırakmanızı sağlar. Örneğin, belirli bir ilke atamasının Endpoint Protection 'ın etkin bir şekilde reddediyor olduğunu görürseniz, ilkeyi düzenlemek veya devre dışı bırakmak için bağlantıyı kullanın.
       
       * İlke listesinde, ilkenin etkin olan uygulamasını aboneliğinizde veya yönetim grubunuzda görebilirsiniz. Kapsama uygulanan her ilkenin ayarları dikkate alınır ve ilke tarafından alınan eylemlerin toplu sonucu gösterilir. Örneğin, bir ilkenin bir atamasında devre dışıysa, ancak başka bir tane de Auditınotexist olarak ayarlandıysa, kümülatif efekt Auditınotexist uygular. Her zaman daha etkin olan etkilerden önceliklidir.
       
       * İlkelerin etkisi: Append, Audit, Auditınotexists, deny, DeployIfNotExists, Disabled. Efektlerin nasıl uygulandığı hakkında daha fazla bilgi için bkz. [ilke efektlerini anlama](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Atanan ilkeleri görüntülediğinizde, birden çok atamayı görebilir ve her atamanın kendi kendine nasıl yapılandırıldığını görebilirsiniz.


## <a name="who-can-edit-security-policies"></a>Güvenlik ilkelerini kimler düzenleyebilir?

Azure Ilke portalından, REST API aracılığıyla veya Windows PowerShell kullanarak güvenlik ilkelerini düzenleyebilirsiniz.

Güvenlik Merkezi, Azure 'daki kullanıcılara, gruplara ve hizmetlere atanabilen yerleşik roller sağlayan rol tabanlı Access Control (RBAC) kullanır. Kullanıcılar Güvenlik Merkezi 'ni açtıklarında yalnızca erişimi olan kaynaklarla ilgili bilgileri görürler. Bu, kullanıcılara kaynak aboneliğine *sahip*, *katkıda*bulunan veya *okuyucu* rolünün atandığı anlamına gelir. Bu rollerin yanı sıra, iki özel güvenlik merkezi rolü vardır:

- **Güvenlik okuyucusu**: öneriler, uyarılar, ilke ve sistem durumunu içeren, ancak değişiklik yapaistemler Için Güvenlik Merkezi 'ne yönelik görünüm haklarına sahiptir.
- **Güvenlik Yöneticisi**: *güvenlik okuyucusu*ile aynı görünüm haklarına sahip olan ve ayrıca güvenlik ilkesini güncelleştirebilir ve öneriler ile uyarıları kapatabilir.


## <a name="disable-security-policies"></a>Güvenlik ilkelerini devre dışı bırak
Varsayılan güvenlik ilkesi ortamınız için uygun olmayan bir öneri üretiyorsa, öneriyi gönderen ilke tanımını devre dışı bırakarak bunu durdurabilirsiniz.
Öneriler hakkında daha fazla bilgi için bkz. [güvenlik önerilerini yönetme](security-center-recommendations.md).

1. Güvenlik Merkezi 'nde, **ilke & uyumluluk** bölümünde **güvenlik ilkesi**' ne tıklayın.

   ![ilke yönetimi](./media/tutorial-security-policy/policy-management.png)

2. Öneriyi devre dışı bırakmak istediğiniz abonelik veya yönetim grubuna tıklayın.

   > [!NOTE]
   > Bir yönetim grubunun ilkelerini aboneliklerine uygulayacağını unutmayın. Bu nedenle, bir aboneliğin ilkesini devre dışı bırakırsanız ve abonelik hala aynı ilkeyi kullanan bir yönetim grubuna aitse, ilke önerilerini almaya devam edersiniz. İlke hala yönetim düzeyinden uygulanır ve öneriler yine de oluşturulacaktır.

1. **Geçerli Ilkeyi görüntüle**' ye tıklayın.

   ![ilkeyi devre dışı bırak](./media/tutorial-security-policy/view-effective-policy.png)

1. Atanan ilkeye tıklayın.

   ![ilkeyi devre dışı bırak](./media/tutorial-security-policy/security-policy.png)

1. **Parametreler** bölümünde, devre dışı bırakmak istediğiniz öneriyi çağıran ilkeyi arayın ve açılan listeden **devre dışı** ' yı seçin.

   ![ilkeyi devre dışı bırak](./media/tutorial-security-policy/disable-policy.png)

1. **Save (Kaydet)** düğmesine tıklayın.

   > [!NOTE]
   > İlke devre dışı bırakma değişikliklerinin etkili olması 12 saate kadar sürebilir.



## <a name="next-steps"></a>Sonraki adımlar
Bu makalede güvenlik ilkeleri hakkında bilgi edindiniz. İlgili bilgiler için aşağıdaki makalelere bakın:

* PowerShell kullanarak ilkelerin nasıl ayarlanacağı hakkında yönergeler için bkz [. hızlı başlangıç: Azure PowerShell modülünü kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma](../governance/policy/assign-policy-powershell.md)

* Azure Ilkesinde bir güvenlik ilkesinin nasıl düzenleneceği hakkında yönergeler için bkz. [uyumluluğu zorlamak için Ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md).

* Azure Ilkesi 'ni kullanarak abonelikler arasında veya yönetim gruplarında ilke ayarlama hakkında yönergeler için bkz. [Azure ilkesi nedir?](../governance/policy/overview.md)