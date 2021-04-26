---
title: Azure Lab Services için laboratuvarları kullanın-
description: Bu makalede, eğitim senaryolarında Azure 'da laboratuvarları oluşturmak için Azure DevTest Labs nasıl kullanılacağı açıklanır.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f9eb3d44d470a978e676f6268cd693b283dd2703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433556"
---
# <a name="use-labs-for-trainings"></a>Seyahat için laboratuvarları kullanma
Azure Labs hizmetleri, eğitimciler (öğretmenler, mesleler, tramacılar veya eğitim yardımcıları vb.) sayesinde, daha önceden yapılandırılmış öğrenme ortamları sağlamak için hızlı ve kolay bir şekilde çevrimiçi laboratuvar oluşturabilir. Her bir eğitimin eğitim için aynı ve yalıtılmış ortamları kullanabiliyor. Bu ilkeler, eğitim ortamlarının yalnızca ihtiyaç duydukları ve sanal makineler gibi yeterli kaynak (eğitim için gerekli) içermesi durumunda her bir şekilde kullanılabilir olmasını sağlamak için uygulanabilir. 

![Sınıf Laboratuvarı](./media/classroom-labs-scenarios/classroom.png)

Labs, herhangi bir sanal ortamda eğitim yapmak için gereken aşağıdaki gereksinimleri karşılar: 

- Eğitim ortamlarını hızlı bir şekilde temin edebilirsiniz
- Her eğitim makinesi aynı olmalıdır
- Diğer işlemlerde oluşturulan VM 'Leri göremez
- Eğitimin, eğitim için ihtiyaç duydukları miktardan daha fazla VM alamaz ve ayrıca onları kullandıklarında sanal makineleri kapatması için maliyeti denetleyin
- Eğitim laboratuvarı her bir Trae ile kolayca paylaşabilirsiniz
- Eğitim laboratuvarı yeniden kullanın ve tekrar

Bu makalede, daha önce açıklanan eğitim gereksinimlerini karşılamak için kullanılabilecek çeşitli Azure Lab Services özellikleri ve eğitim için bir laboratuvar kurmak için izleyebileceğiniz ayrıntılı adımları öğreneceksiniz.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Laboratuvar hesabını laboratuvar hesabı Yöneticisi olarak oluşturma
Azure Lab Services kullanmanın ilk adımı Azure portal laboratuvar hesabı oluşturmaktır. Laboratuvar hesabı Yöneticisi laboratuvar hesabını oluşturduktan sonra, yönetici, **Laboratuvar Oluşturucu** rolüne Labs oluşturmak isteyen kullanıcıları ekler. Eğitimciler, öğrenciler için sanal makinelere sahip laboratuvarları, öğretme Kursu için alıştırmaları yapması için oluşturur. Ayrıntılar için bkz. [Laboratuvar hesabı oluşturma ve yönetme](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-labs"></a>Laboratuvar oluşturma ve yönetme
Laboratuvar hesabındaki laboratuvar Oluşturucu rolünün bir üyesi olan bir eğitimci, laboratuvar hesabında bir veya daha fazla Laboratuvarı oluşturabilir. Kursta alýþtýrmalarý gerçekleştirmek için gerekli tüm yazılımlarla bir şablon VM 'si oluşturup yapılandırırsınız. Bir sınıf laboratuvarı oluşturmak için kullanılabilir görüntülerden kullanıma hazır bir görüntü seçer ve ardından Laboratuvar için gereken yazılımı yükleyerek özelleştirebilirsiniz. Ayrıntılar için bkz. [Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Kullanım ayarlarını ve ilkeleri yapılandırma
Laboratuvar Oluşturucu, laboratuvar kullanıcılarına kullanıcı ekleyebilir veya kaldırabilir, laboratuvar kullanıcılarına göndermek için kayıt bağlantısını alabilir, Kullanıcı başına ayrı kotalar ayarlama, laboratuvardaki kullanılabilir sanal makine sayısını güncelleştirme ve daha fazlası gibi ilkeler ayarlama. Ayrıntılar için bkz. [kullanım ayarlarını ve Ilkeleri yapılandırma](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Zamanlamaları oluşturma ve yönetme
Zamanlamalar, laboratuvardaki VM 'Lerin otomatik olarak başlamasını ve belirli bir zamanda kapatılmasını sağlamak için bir derslik Laboratuvarı yapılandırmanıza olanak tanır. Tek seferlik bir zamanlama veya yinelenen bir zamanlama tanımlayabilirsiniz. Ayrıntılar için bkz. [Labs için zamanlama oluşturma ve yönetme](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Şablon VM 'si ayarlama ve yayımlama
Laboratuvardaki şablon, tüm kullanıcıların sanal makinelerinin oluşturulduğu bir temel sanal makine görüntüsüdür. Yalnızca eğitim katılımcılarına sağlamak istediğiniz şekilde yapılandırmak için şablon VM 'yi ayarlayın. Laboratuvar kullanıcılarının görebileceği bir ad ve açıklama belirtebilirsiniz. Daha sonra, şablon VM 'nin örneklerini laboratuvar kullanıcılarınız için kullanılabilir hale getirmek üzere şablonu yayımlayabilirsiniz. Bir şablonu yayımladığınızda Azure Lab Services, şablonu kullanarak laboratuvarda sanal makineler oluşturur. Bu işlemde oluşturulan sanal makine sayısı, laboratuvarda izin verilen maksimum kullanıcı sayısıyla aynıdır. Laboratuvarın kullanım ilkesinde bu maksimum değeri ayarlayabilirsiniz. Tüm sanal makineler, şablonla aynı yapılandırmaya sahiptir. Ayrıntılar için bkz. [şablon sanal makinelerini ayarlama ve yayımlama](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Sınıf laboratuvarında VM 'Leri kullanma
Bir öğrenci veya eğitim katılımcısı laboratuvara kaydolur ve kursa yönelik alıştırmaları yapmak için VM 'ye bağlanır. Ayrıntılar için bkz. [sınıf laboratuvarına erişme](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Sonraki adımlar
Laboratuarda laboratuvar hesabı oluşturmaya başlayın: [öğreticideki yönergeleri izleyerek Azure Lab Services bir laboratuvar hesabı kurun](tutorial-setup-lab-account.md).