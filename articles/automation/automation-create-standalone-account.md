---
title: Tek başına Azure Otomasyonu hesabı oluşturma
description: Bu makalede, Azure Otomasyonu 'nda örnek bir güvenlik sorumlusu kimlik doğrulaması oluşturma, test etme ve kullanma adımları gösterilmektedir.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cdea358daa3bd0f9e738a0454613ea774a0e6dc
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146641"
---
# <a name="create-a-standalone-azure-automation-account"></a>Tek başına Azure Otomasyonu hesabı oluşturma

Bu makalede, Azure portal Azure Otomasyonu hesabının nasıl oluşturulacağı gösterilmektedir. Ek yönetim çözümlerini kullanmadan veya Azure Izleyici günlükleriyle tümleştirmeden Otomasyon hakkında bilgi edinmek için Portal Otomasyonu hesabını kullanabilirsiniz. Gelecekte herhangi bir noktada runbook işlerinin gelişmiş izlenmesi için, bu yönetim çözümlerini ekleyebilir veya Azure Izleyici günlükleriyle tümleştirebilirsiniz.

Otomasyon hesabı ile Azure Resource Manager veya klasik dağıtım modelinde kaynakları yöneterek runbook 'ları doğrulayabilirsiniz. Bir Otomasyon Hesabı belirli bir kiracı için kaynakları tüm bölgelerde ve aboneliklerde yönetebilir.

Azure portal bir Otomasyon hesabı oluşturduğunuzda, bu hesaplar otomatik olarak oluşturulur:

* **Farklı Çalıştır hesabı**. Bu hesap aşağıdaki görevleri yapar:
  * Azure Active Directory (Azure AD) içinde bir hizmet sorumlusu oluşturur.
  * Bir sertifika oluşturur.
  * Runbook 'ları kullanarak Azure Resource Manager kaynaklarını yöneten, katkıda bulunan rol tabanlı Access Control (RBAC) atar.

Sizin için oluşturulan bu hesaplarla, Otomasyon gereksinimlerinizi desteklemek için Runbook 'ları oluşturmaya ve dağıtmaya hızlı bir başlangıç yapabilirsiniz.

## <a name="permissions-required-to-create-an-automation-account"></a>Otomasyon hesabı oluşturmak için gereken izinler

Bir Otomasyon hesabı oluşturmak veya güncelleştirmek ve bu makalede açıklanan görevleri gerçekleştirmek için aşağıdaki ayrıcalıklara ve izinlere sahip olmanız gerekir:

* Bir Otomasyon hesabı oluşturmak için, Azure AD Kullanıcı hesabınızın, Microsoft için **sahip rolüne eşdeğer izinlere sahip bir role eklenmesi gerekir. Otomasyon** kaynakları. Daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda rol tabanlı Access Control](automation-role-based-access-control.md).
* Azure Portal,**Kullanıcı ayarlarını** **Yönet** >  **Azure Active Directory** > altında **uygulama kayıtları** **Evet**olarak ayarlanırsa, Azure AD kiracınızdaki yönetici olmayan kullanıcılar [Etkin kayıt yapabilir Dizin uygulamaları](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). **Uygulama kayıtları** **Hayır**olarak ayarlanırsa, bu EYLEMI gerçekleştiren kullanıcının Azure AD 'de Genel yönetici olması gerekir.

Aboneliğin genel yönetici/ortak yönetici rolüne eklenmeden önce aboneliğin Active Directory örneğinin bir üyesi değilseniz, konuk olarak Active Directory eklenir. Bu senaryoda, bu iletiyi **Otomasyon hesabı ekle** sayfasında görürsünüz: "Oluşturma izniniz yok."

Önce bir Kullanıcı genel yönetici/ortak yönetici rolüne eklenirse, bunları aboneliğin Active Directory örneğinden kaldırabilir ve sonra bunları Active Directory tam kullanıcı rolüne ekleyebilirsiniz.

Kullanıcı rollerini doğrulamak için:

1. Azure portal **Azure Active Directory** bölmesine gidin.
1. **Kullanıcı ve gruplar**'ı seçin.
1. **Tüm kullanıcılar**' ı seçin.
1. Belirli bir Kullanıcı seçtikten sonra **profil**' i seçin. Kullanıcı profili altındaki **Kullanıcı türü** özniteliğinin değeri **konuğa**sahip olmamalıdır.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Azure portal yeni bir Otomasyon hesabı oluşturun

Azure portal bir Azure Otomasyonu hesabı oluşturmak için aşağıdaki adımları izleyin:

1. Abonelik yöneticileri rolünün üyesi ve aboneliğin ortak Yöneticisi olan bir hesapla Azure portal oturum açın.
1. **+ Kaynak oluştur**' u seçin.
1. **Otomasyon**araması yapın. Arama sonuçlarında **Otomasyon**' u seçin.

   ![Azure Marketi 'nde Otomasyon & denetimini arama ve seçme](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Sonraki ekranda **Oluştur**' u seçin.

   ![Otomasyon hesabı ekle](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > **Otomasyon hesabı ekle** bölmesinde aşağıdaki iletiyi görürseniz, hesabınız abonelik yöneticileri rolünün bir üyesi ve aboneliğin ortak Yöneticisi değildir.
   >
   > ![Otomasyon hesabı ekleme uyarısı](media/automation-create-standalone-account/create-account-without-perms.png)

1. **Otomasyon hesabı ekle** bölmesindeki **ad** kutusuna yeni otomasyon hesabınız için bir ad girin. Bu ad, seçildikten sonra değiştirilemez. *Otomasyon hesabı adları, bölge ve kaynak grubu başına benzersizdir. Silinen Otomasyon hesaplarının adları hemen kullanılamayabilir.*
1. Birden fazla aboneliğiniz varsa, **abonelik** kutusunda, yeni hesap için kullanmak istediğiniz aboneliği belirtin.
1. **Kaynak grubu**için, yeni veya mevcut bir kaynak grubu girin veya seçin.
1. **Konum**Için bir Azure veri merkezi konumu seçin.
1. **Azure farklı çalıştır hesabı oluştur** seçeneği için **Evet** ' in seçili olduğundan emin olun ve ardından **Oluştur**' u seçin.

   > [!NOTE]
   > **Azure farklı çalıştır hesabı oluştur**' **u seçerek farklı** Çalıştır hesabı oluşturmayı seçerseniz, **Otomasyon hesabı ekle** bölmesinde bir ileti görüntülenir. Hesap Azure portal oluşturulsa da hesabın klasik dağıtım modeli aboneliğinizde veya Azure Resource Manager abonelik dizin hizmetinde ilgili bir kimlik doğrulama kimliği yoktur. Bu nedenle, Otomasyon hesabının aboneliğinizdeki kaynaklara erişimi yoktur. Bu, bu hesaba başvuran runbook 'ların bu dağıtım modellerindeki kaynaklara göre kimlik doğrulaması yapmasını ve görevler gerçekleştirmesini engeller.
   >
   > ![Otomasyon hesabı ekleme uyarısı](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Hizmet sorumlusu oluşturulmadığında katkıda bulunan rolü atanmaz.
   >

1. Otomasyon hesabı oluşturma işleminin ilerlemesini izlemek için, menüsünde **Bildirimler**' i seçin.

### <a name="resources-included"></a>Kaynaklar dahil

Otomasyon hesabı başarıyla oluşturulduğunda bazı kaynaklar sizin için otomatik olarak oluşturulur. Oluşturulduktan sonra, bu runbook 'ları korumak istemiyorsanız güvenli bir şekilde silinebilir. Farklı Çalıştır hesapları, bir runbook 'ta hesabınızda kimlik doğrulaması yapmak için kullanılabilir ve başka bir tane oluşturmadığınız ya da bunları gerektirmediğiniz sürece bu durumda bırakılmalıdır. Aşağıdaki tabloda Farklı Çalıştır hesabının kaynakları özetlenmektedir.

| Resource | Açıklama |
| --- | --- |
| AzureAutomationTutorial Runbook |Farklı Çalıştır hesabını kullanarak kimlik doğrulamanın nasıl yapılacağını gösteren örnek bir grafik runbook. Runbook tüm Kaynak Yöneticisi kaynaklarını alır. |
| AzureAutomationTutorialScript Runbook |Farklı Çalıştır hesabını kullanarak kimlik doğrulaması yapılacağını gösteren örnek bir PowerShell runbook 'u. Runbook tüm Kaynak Yöneticisi kaynaklarını alır. |
| AzureAutomationTutorialPython2 Runbook |Farklı Çalıştır hesabını kullanarak kimlik doğrulaması yapılacağını gösteren örnek bir Python runbook. Runbook, abonelikte bulunan tüm kaynak gruplarını listeler. |
| AzureRunAsCertificate |Otomasyon hesabı oluşturulduğunda veya var olan bir hesap için bir PowerShell betiği kullanılarak otomatik olarak oluşturulan bir sertifika varlığı. Sertifika, runbook 'lardan Azure Resource Manager kaynaklarını yönetebilmeniz için Azure ile kimlik doğrulaması yapar. Bu sertifikanın bir yıllık kullanım ömrü vardır. |
| AzureRunAsConnection |Otomasyon hesabı oluşturulduğunda veya var olan bir hesap için bir PowerShell betiği kullanılarak otomatik olarak oluşturulan bağlantı varlığı. |

## <a name="classic-run-as-accounts"></a>Klasik farklı çalıştır hesapları

Klasik farklı çalıştır hesapları, varsayılan olarak, bir Azure Otomasyonu hesabı oluşturduğunuzda artık oluşturulmaz. Hala klasik bir Farklı Çalıştır hesabına ihtiyacınız varsa lütfen aşağıdaki adımları gerçekleştirin.

1. **Otomasyon hesabı** sayfasından **Hesap ayarları**altında **Farklı Çalıştır hesapları** ' nı seçin.
2. **Azure klasik farklı çalıştır hesabı**' nı seçin.
3. Klasik farklı çalıştır hesabı oluşturma işlemine devam etmek için **Oluştur** ' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* Grafik yazma hakkında daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda grafik yazma](automation-graphical-authoring-intro.md).
* PowerShell runbook'ları kullanmaya başlamak için bkz. [İlk PowerShell runbook’um](automation-first-runbook-textual-powershell.md).
* PowerShell iş akışı runbook'larını kullanmaya başlamak için bkz. [İlk PowerShell iş akışı runbook uygulamam](automation-first-runbook-textual.md).
* Python2 runbook'larını kullanmaya başlamak için bkz. [İlk Python2 runbook'um](automation-first-runbook-textual-python2.md).

