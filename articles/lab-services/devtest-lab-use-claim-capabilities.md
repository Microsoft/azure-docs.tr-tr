---
title: Azure DevTest Labs için talep yeteneklerini kullanma | Microsoft Docs
description: Azure DevTest Labs talep/talep dışı yeteneklerini kullanmaya yönelik farklı senaryolar hakkında bilgi edinin
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861436"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Azure DevTest Labs 'da talep yeteneklerini kullanma
Azure DevTest Labs hizmeti geliştiricilerin ve Test edicilerin verimliliğini ve verimliliğini artırır. Bu makale Azure DevTest Labs ' de sanal makineleri talep etme veya talebi geri alma imkanına odaklanır. Ayrıca, bu özelliğin Kullanıcı deneyimini geliştirmenin çeşitli yollarını listeler. Bu özelliğin kullanılabileceği farklı senaryolara bakmadan önce **, neler olduğunu** ve nasıl çalıştığını inceleyelim.

## <a name="claimable-machines"></a>Çakışan makineler
Çakışan bir makine, sahip olmadan bir laboratuvarda oluşturulan bir sanal makinedir (VM). Makine talep edildikten sonra, Kullanıcı o VM için tam bir seçenek aralığına sahip olur. Bir Kullanıcı bir makine talep ettiği zaman, birkaç değişiklik yapılır. VM, çakışan **sanal makineler** listesinden Azure Portal **sanal makineler** listesine taşınır. 

Kullanıcı VM 'ye bağlanabilir, yapıtları özelleştirebilir, makineleri yeniden başlatabilir, durdurabilir veya talebe geri alabilir. Bir VM 'nin çakışılabilir olması için birkaç yol vardır:

- Bir makine oluşturun ve bunu, çakışan havuza taşınacak şekilde talep edin. 
- [Gelişmiş ayarları](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/)kullanarak bir VM oluşturun ve paylaşılan havuza yerleştirin.

Talep/talep dışı yeteneklerin etkin şekilde kullanılabileceği iki durum vardır. İlk durumda, düzgün şekilde tasarlanmasını ve yürütülmesini sağlamak için daha fazla öngörülebilir ve planlama yapmanız gerekir. İkincisi ise daha durumsal. Farklı durumlarda bazı örnekler aşağıda verilmiştir.

## <a name="designed-use-of-claimable-machines"></a>Tasarlanan makinelerin kullanımı

- **Yazılım geliştirme/test etme:** Geliştiricilerin ve Test edicilerin, yapılandırılmış makineleri ve talep edilmemiş bir durumda olmasını sağlayarak daha üretken olmasına izin verin. Farklı yapılandırmalara sahip bir VM kümesine sahip olmak, gerekli araçlar ve en son kodla, kullanıcıların bir makineyi ayarlama süresini harcamaya gerek kalmadan bir VM talep etmesine ve çalışmaya başlamasını sağlar. VM 'Ler istenmeden önce makineler sağlanır ancak kapalı, daha az kullanılan makinelere sahip olma maliyetini en aza indiriyor. VM 'Ler gerektiğinde, bir Kullanıcı, makineyi Başlatan VM 'yi talep etmez. Yeni bir sanal makine oluşturmak genellikle daha kolay ve kimaper olduğundan, bu durumda işlenmemiş seçeneği yararlı değildir.
- **Derslik/laboratuvarlar:** Öğrencilerin Azure portal kullanarak bir makineye hemen bağlanabilmesi için bir sınıf veya laboratuvar için önceden yapılandırılmış VM 'Ler vardır.  Bir öğrenciye bir VM talep eden laboratuvar, hiç kimse aynı makineyi talep edebilir. Bu işlemi otomatik hale getirmek, belirtilen ortama sahip gerekli makine sayısının kullanılabilir olmasını sağlar. Öğrenciler hiç gösterilmez veya geç çalışıyorsa, oturum en az maliyetle karşılanana kadar, istenen makineler kullanılabilir tutulabilir. Önceki Kullanıcı tamamlandığında VM bilinmeyen bir durumda olduğundan, bu senaryoda talep kaldırma seçeneği geçerli değildir.
- **Gösterim** Laboratuvardaki makinelerin belirli ortamlarla ayarlandığı gösterileri için makineleri kullanın. Bu özellik, birden çok kişinin aynı anda veya bir konferans gibi rastgele zamanlarda bir tanıtım sağlayan yararlı olabilir. Tanıtım, makinenin durumunu değiştirmemesi ve kullanıcıların bir sonraki tanıtım için bir VM 'yi el ile bir sanal makine döndürmesine izin vermek için bu durumda, bu durumda, unclaim seçeneği yararlı olabilir. Talep edilmemiş makinenin sağlanması ve en düşük maliyetli olması halinde VM 'Ler, daha uzun süreler için laboratuvarda bırakılabilir.
- **Geçici/sözleşmeli çalışanlar:** Kullanıcıların bir makine kullanmasına izin verin. Ayrıldıklarında, veri kaybı olmadan sanal makineyi çakışan havuza geri döndürürler. VM karşılanmadı, başka bir Kullanıcı VM 'yi talep edebilir veya daha fazla bilgi için makineyi inceleyebilir veya gözden geçirebilir.
- **Genel olarak:** Tek bir kaynağın, belirli bir temposunda üzerinde VM 'Leri otomatik olarak yapılandırma ve dağıtma özelliği birçok farklı durumda yararlıdır. Talep/talep özelliğinin, bir küme yapılandırması ile talep edilmemiş bir durumda VM 'Leri oluşturmak için otomatikleştirilmiş bir işlem yaparak kullanıcıların daha verimli olmasına yardımcı olan birkaç farklı durum vardır. Yapılandırma (ler), gereksinimlerinize bağlı olarak farklı işletim sistemleri, diller, diskler veya [başka yazılımlar (yapıtlar)](devtest-lab-artifact-author.md) içerebilir. Laboratuvardan bir VM talep etme özelliği, laboratuvar kullanıcısına makineyi yapılandırma sırasında zaman veya çaba harcamadan doğru şekilde yapılandırılmış bir sistem almasına izin verir. Laboratuvar Yöneticisi, oluşturulan makinelerin sayısını artırmak, makineleri temizlemek ve yapılandırmaların önceliğini tespit etmek için VM 'lerin istenen durumunu kullanabilir. [Görüntü fabrikası](image-factory-create.md) , birden çok laboratuvarın VM 'leri ve görüntülerini oluşturmak için otomatikleştirilmiş bir işleme yönelik iyi bir örnektir. Betikler, uygun değişikliklere sahip olan veya özel bir sistem oluşturmaya yönelik bir başvuru olarak kullanılan aşağıdaki durumlardan herhangi birini yürütecek şekilde değiştirilebilir.

## <a name="situational-use-of-claimable-machines"></a>Durumsal si makinelerin kullanımı

- Kullanıcıların makinenin bir sürümünden diğerine geçmesine izin veren ve daha sonra makineyi kullanıma alacak şekilde bilmesini istemeyen talep/talep dışı özelliğini kullanın.
- Belirli bir makine yapılandırmasının hata üretebileceği bir senaryonun geliştirilmesi, sınanması ve hata ayıklaması, daha sonra başka bir geliştiricinin makineyi talep edebilmesine ve çalışmaya devam etmesine izin vermek için makinenin isteği geri alınamaz. Bu özellik özellikle dünyanın farklı alanlarında uzaktan çalışırken daha fazla insan için yararlıdır. 
- Ekip üyeleri, tek bir ortamla çalışabilir. Örneğin, otomatik olarak olmayan bir karmaşık ortamı el ile ayarlayabilir veya yalnızca görüntüler gibi tek bir giriş için değişiklikleri işleyebilmek için kaynak oluşturamazsınız. Geçmişte bu sorun, adanmış bir makinenin çalışır durumda olduğundan ve ile birlikte daðýtýlýr. Claımable özelliği, yerleşik kullanıcı erişim denetimi ve kullanılabilir olduğunda görsel kimlik sağlayarak el ile gerçekleştirilen işleme yönelik bir geliştirmedir. Talep edilmeyen zaman, maliyetleri azaltmak için VM 'nin sağlanması gerekir.
- Bir VM 'ye bağlı bir veri diskine sahip olmak. Her disk ~ 1 TB 'a kadar veri, verileri kopyalamak veya çoğaltmak zorunda kalmadan büyük hacimde veri geçirilmesine izin verir. VM başlangıçta büyük miktarda veri içeren bir bağlı disk ile oluşturulur.  Böylece, herhangi bir Kullanıcı makineyi talep altına alabilir ve verilere erişebilir. İşiniz bittiğinde, sanal makinenin diğer kullanıcılara makineye izin vermek için talebi geri al.

En yaygın olarak makineye erişim elde eden, çakışan makineler kullanmanın bazı uyarıları vardır. Makine etki alanına katılmış ise, makinenin daha önce erişim izni verilmiş olması gerekir, genellikle sanal makine oluşturulduğunda laboratuvardaki tüm kullanıcıları kapsayan bir gruba erişim verilerek yapılır. Makine etki alanına katılmış değilse, kullanıcıyı yönetici olarak eklemek için ortak depodaki **sanal makine parola** yapısının çalıştırılması gerekir.  Yapıtlar makine başlatıldıktan veya talep edildikten sonra bile uygulanabilir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleye bakın: [Azure DevTest Labs çakışan VM 'Ler oluşturun ve yönetin](devtest-lab-add-claimable-vm.md)
