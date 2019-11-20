---
title: Azure DevTest Labs bir VM için oto kapatma ayarlarını yapılandırma | Microsoft Docs
description: Sanal makine (VM) için otomatik kapatma ayarlarını yapılandırarak VM 'nin kullanımda olmadığında otomatik olarak kapatılmasını sağlayabilirsiniz.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361576"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Azure DevTest Labs bir VM için oto kapatma ayarlarını yapılandırma
Azure DevTest Labs, her bir laboratuvarın ilkelerini (ayarlarını) yöneterek laboratuvarlarınızdaki, maliyetleri denetlemenizi ve en aza indirmenizi sağlar. Bu makalede, laboratuvar hesabı için oto kapatma ilkesini yapılandırma ve laboratuvar hesabındaki bir laboratuvar için oto kapatma ayarlarını yapılandırma gösterilmektedir. Her laboratuvar ilkesini ayarlama hakkında bilgi için bkz. [Azure DevTest Labs laboratuvar Ilkelerini tanımlama](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Laboratuvar için oto kapatma ilkesi
Laboratuvar sahibi olarak, laboratuvarınızda tüm VM 'Ler için bir kapalı zamanlama yapılandırabilirsiniz. Bunu yaparak, kullanılmayan makinelerden (boşta) maliyetlerin tasarruf edebilirsiniz. Tüm laboratuar sanal makinelerinizde bir kapalı ilke uygulayabilir, ancak laboratuvar kullanıcılarınıza bireysel makineler için bir zamanlama ayarlama çabalarını kaydedebilirsiniz. Bu özellik, laboratuvar zamanlamanızda ilke ayarlamanıza olanak sağlayarak laboratuvar kullanıcılarınıza denetim olmadan tam denetim sunmadan başlayarak ilkeyi ayarlamanıza olanak sağlar. Laboratuvar sahibi olarak, aşağıdaki adımları uygulayarak bu ilkeyi yapılandırabilirsiniz:

1. Laboratuvarınızın giriş sayfasında **yapılandırma ve ilkeler**' i seçin.
2. Sol menünün **zamanlamalar** bölümünde **otomatik kapatılma ilkesi** ' ni seçin.
3. Seçeneklerden birini belirleyin. Aşağıdaki bölümlerde bu seçenekler hakkında daha fazla ayrıntı verilmektedir: Küme ilkesi yalnızca laboratuvarda oluşturulan yeni VM 'ler için geçerlidir; zaten var olan VM 'lere uygulanmaz. 

    ![İlke seçeneklerini otomatik olarak kapat](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Laboratuvar için oto kapatma ayarlarını yapılandırma
Oto kapatma ilkesi, bu laboratuvarın VM 'lerinin kapanması için gereken süreyi belirtmenize izin vererek laboratuvar harcanmasının en aza indirilmesine yardımcı olur.

Bir laboratuvarın ilkelerini görüntülemek (ve değiştirmek) için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
3. Laboratuvarlar listesinden istediğiniz Laboratuvarı seçin.   
4. **Yapılandırma ve ilkeler '** i seçin.

    ![İlke ayarları bölmesi](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Laboratuvarın **yapılandırma ve ilkeler** bölmesinde, **zamanlamalar**altında **otomatik olarak kapanıyor** ' ı seçin.
   
    ![Otomatik kapatma](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Bu ilkeyi **etkinleştirmek ve devre** dışı bırakmak için **Açık** seçeneğini belirleyin.
7. Bu ilkeyi etkinleştirirseniz, geçerli laboratuvardaki tüm VM 'Leri kapatmak için saati (ve saat dilimini) belirtin.
8. Belirtilen oto kapatma saatinden 15 dakika önce bildirim gönderme seçeneği için Evet veya **Hayır** **değerini** belirtin. **Evet**' i seçerseniz, bildirimin nakledilmesini veya gönderilmesini istediğiniz yeri belirten bir Web kancası URL uç noktası veya e-posta adresi girin. Kullanıcı bildirimi alır ve kapatmaya gecikme seçeneği verilir. Daha fazla bilgi için [Bildirimler](#notifications) bölümüne bakın. 
9. **Kaydet**’i seçin.

    Varsayılan olarak, bu ilke, etkinleştirildikten sonra geçerli laboratuvardaki tüm VM 'Ler için geçerlidir. Bu ayarı belirli bir VM 'den kaldırmak için, VM 'nin Yönetim bölmesini açın ve kendi **oto kapatma** ayarını değiştirin.

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Bir VM için oto kapatma ayarlarını yapılandırma

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Kullanıcı bir zamanlama ayarlar ve devre dışı bırakabilirsiniz
Bu ilke seçeneği laboratuvar için ayarlandıysa, kullanıcılar Laboratuvar zamanlamasını geçersiz kılabilir veya devre dışı bırakabilirsiniz. Bu seçenek, laboratuvar kullanıcılarına VM 'lerinin otomatik kapatılma zamanlaması üzerinde tam denetim verir. Laboratuvar kullanıcıları, sanal makine otomatik kapatılma zamanlaması sayfasında değişiklik görmez.

![İlke seçeneğini otomatik kapat-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Kullanıcı bir zamanlama ayarlıyor ve devre dışı bırakılamaz
Bu ilke seçeneği laboratuvar için ayarlandıysa, kullanıcılar Laboratuvar zamanlamasını geçersiz kılabilir. Ancak otomatik kapatma ilkesini devre dışı bırakıamazlar. Bu seçenek, laboratuvarınızda bulunan her makinenin otomatik kapatılma zamanlaması altında olmasını sağlar. Laboratuvar kullanıcıları, sanal makinelerinin otomatik kapatma zamanlamasını güncelleştirebilir ve kapalı bildirimleri ayarlayabilir.

![İlke seçeneğini otomatik kapat-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Kullanıcının Laboratuvar Yöneticisi tarafından ayarlanan zamanlama üzerinde denetimi yok
Bu ilke seçeneği laboratuvar için ayarlandıysa, kullanıcılar Laboratuvar zamanlamasını geçersiz kılamaz veya devre dışı kılmaz. Bu seçenek, laboratuvardaki her makine için zamanlamaya göre tüm denetimi laboratuvar yöneticisine sunar. Laboratuvar kullanıcıları, sanal makineleri için yalnızca otomatik olarak kapanmaya yönelik bildirimler ayarlayabilir.

![İlke seçeneğini otomatik kapat-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Bildirimler
Laboratuvar sahibi tarafından ayarlanan bir kez, bir sanal makine etkilenirse, oto kapanmadan önce, laboratuvar kullanıcılarına 15 dakika boyunca bildirim gönderilir. Bu seçenek, laboratuvar kullanıcılarına kapatmadan önce işlerini kaydetme şansı sağlar. Bildirim Ayrıca, aşağıdaki eylemler için her VM için bağlantılar sağlar:

- Bu süre için oto kapatma işlemini atla
- Sanal makine üzerinde çalışmaya devam edebilmesi için, bir saat veya 2 saat boyunca oto kapatılmasını erteler.

Bildirim, yapılandırılmış Web kancası uç noktası veya otomatik kapatma ayarlarındaki laboratuvar sahipleri tarafından belirtilen bir e-posta adresi aracılığıyla gönderilir. Web kancaları, belirli olaylara abone olan tümleştirmeler oluşturmanıza veya ayarlamanıza olanak sağlar. Bu olaylardan biri tetiklendiğinde, DevTest Labs Web kancası tarafından yapılandırılan URL 'ye bir HTTP POST yükü gönderir. Web kancaları hakkında daha fazla bilgi için bkz. [Web kancası veya API Azure Işlevi oluşturma](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Birçok uygulama (örneğin, bolluk, Azure Logic Apps vb.) tarafından kapsamlı bir şekilde desteklendiğinden Web kancalarını kullanmanızı öneririz ve bildirim göndermek için kendi yolunuzu uygulamanıza olanak tanır. Azure Logic Apps kullanarak e-postalardan oto kapatma bildirimi alma örneği için bkz.[e-posta bildirimleri alan bir mantıksal uygulama oluşturma](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>Sonraki adımlar
Bkz. [Azure DevTest Labs laboratuvar için oto kapatma Ilkelerini yönetme](devtest-lab-auto-shutdown.md)
