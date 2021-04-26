---
title: Azure DevTest Labs kaynakların dağıtılmış işbirliğine dayalı geliştirmesi
description: DevTest Labs kaynaklarını geliştirmek üzere dağıtılmış ve işbirliğine dayalı bir geliştirme ortamı ayarlamak için en iyi yöntemleri sağlar.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: caf4bd13f2ec9c45db392a027db269b492cbd802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550085"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs kaynaklarının dağıtılmış ve işbirliğine dayalı olarak geliştirilmesi için en iyi uygulamalar
Dağıtılmış işbirliğine dayalı geliştirme, farklı ekiplerin veya kişilerin bir kod tabanı geliştirmesini ve bakımını sağlar. Geliştirme süreci başarılı olmak için bilgi oluşturma, paylaşma ve tümleştirme özelliğine bağlıdır. Bu anahtar geliştirme ilkesi, Azure DevTest Labs içinde kullanılabilir. Bir laboratuvarda, bir kuruluş içindeki farklı laboratuvarlar arasında yaygın olarak dağıtılan birkaç kaynak türü vardır. Farklı kaynak türleri iki alana odaklanılmıştır:

- Laboratuvar içinde dahili olarak depolanan kaynaklar (laboratuvar tabanlı)
- [Laboratuvara bağlı dış depolarda](devtest-lab-add-artifact-repo.md) depolanan kaynaklar (kod deposu tabanlı). 

Bu belgede, tüm düzeylerde özelleştirme ve kalite sağlamaya çalışırken birden fazla ekip arasında işbirliği ve dağıtıma izin veren bazı en iyi yöntemler açıklanmaktadır.

## <a name="lab-based-resources"></a>Laboratuvar tabanlı kaynaklar

### <a name="custom-virtual-machine-images"></a>Özel sanal makine görüntüleri
Labs 'e gecelik esasına göre dağıtılan özel resimlerden oluşan ortak bir kaynağınız olabilir. Ayrıntılı bilgi için bkz. [görüntü fabrikası](image-factory-create.md).    

### <a name="formulas"></a>Formüller
[Formüller](devtest-lab-manage-formulas.md) laboratuvara özeldir ve bir dağıtım mekanizmasına sahip değildir. Laboratuvar üyeleri formüllerin tüm geliştirilmesini ister. 

## <a name="code-repository-based-resources"></a>Kod deposu tabanlı kaynaklar
Kod depolarını, yapıtları ve ortamları temel alan iki farklı özellik vardır. Bu makale, özellikleri ve kuruluş düzeyindeki veya ekip düzeyindeki kullanılabilir yapıtları ve ortamları özelleştirebilme olanağı sağlamak üzere depoları ve iş akışını en verimli şekilde ayarlamaya yönelik olarak, özelliklerin üzerine gider.  Bu iş akışı standart [kaynak kodu denetimi dallanma stratejisini](/azure/devops/repos/tfvc/branching-strategies-with-tfvc)temel alır. 

### <a name="key-concepts"></a>Önemli kavramlar
Yapıtlar için kaynak bilgileri, meta verileri, betikleri içerir. Ortamlar için kaynak bilgileri, PowerShell betikleri, DSC betikleri, ZIP dosyaları ve benzeri destekleyici dosyaları içeren meta veriler ve Kaynak Yöneticisi şablonlar içerir.  

### <a name="repository-structure"></a>Depo yapısı  
Kaynak kodu denetimi (SCC) için en yaygın yapılandırma, laboratuvarlarda kullanılan kod dosyalarını (Kaynak Yöneticisi şablonlar, meta veriler ve betikler) depolamak için çok katmanlı bir yapı ayarlamaya yöneliktir. Özellikle, farklı iş düzeyleri tarafından yönetilen kaynakları depolamak için farklı depolar oluşturun:   

- Şirket genelinde kaynaklar.
- İş birimi/bölüm genelinde kaynaklar
- Ekibe özel kaynaklar.

Bu düzeylerin her biri, ana dalın üretim kalitesi olması gereken farklı bir depoya bağlanır. Her depodaki [dallar](/azure/devops/repos/git/git-branching-guidance) , söz konusu kaynakların (yapıtlar veya şablonlar) geliştirilmesi için kullanılır. Aynı anda birden çok depo ve birden çok dalı kuruluşun laboratuvarlarına kolayca bağlayabilmeniz için bu yapı, DevTest Labs ile iyi bir şekilde hizalanır. Aynı adlar, açıklama ve yayımcı olduğunda karışıklık oluşmasını önlemek için depo adı Kullanıcı arabirimine (UI) dahil edilir.
     
Aşağıdaki diyagramda iki depo gösterilmektedir: BT bölümünün tuttuğu bir şirket deposu ve R&D bölümü tarafından tutulan bir bölüm deposu.

![Örnek dağıtılabilir ve işbirliğine dayalı bir geliştirme ortamı](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Bu katmanlı yapı, bir laboratuvara bağlı birden çok depo olduğunda daha fazla esneklik sağlayan ana dalda daha yüksek düzeyde kalite sağlarken geliştirmeye olanak sağlar.

## <a name="next-steps"></a>Sonraki adımlar    
Aşağıdaki makalelere bakın:

- [Azure Portal](devtest-lab-add-artifact-repo.md) veya [Azure Kaynak Yönetimi şablonu](add-artifact-repository.md) aracılığıyla laboratuvara bir depo ekleme
- [DevTest Labs yapıları](devtest-lab-artifact-author.md)
- [DevTest Labs ortamları](devtest-lab-create-environment-from-arm.md).
