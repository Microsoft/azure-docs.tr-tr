---
title: Azure DevTest Labs için hizmet olarak platform (PaaS) hizmetlerini kullanın | Microsoft Docs
description: Azure DevTest Labs ' de hizmet olarak platform (pass) hizmetlerini nasıl kullanacağınızı öğrenin.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: a80a54f3dc760d80f713db9857cbef0c580e66d6
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621378"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Azure DevTest Labs 'de hizmet olarak platform (PaaS) hizmetlerini kullanın
PaaS, ortam özelliği aracılığıyla DevTest Labs 'de desteklenir. DevTest Labs içindeki ortamlar, bir git deposundaki önceden yapılandırılmış Azure Resource Manager şablonları tarafından desteklenir. Ortamlar PaaS ve IaaS kaynaklarını içerebilir. Bunlar, birlikte çalışmak üzere özelleştirilmiş sanal makineler, veritabanları, sanal ağlar ve Web uygulamaları gibi Azure kaynaklarını içerebilen karmaşık sistemler oluşturmanıza imkan tanır. Bu şablonlar, kaynak kodu denetimi kullanılarak ortamların tutarlı dağıtım ve gelişmiş yönetimine olanak tanır. 

Düzgün bir şekilde ayarlanan sistem aşağıdaki senaryolara izin verir: 

- Geliştiricilerin bağımsız ve birden çok ortamları olması
- Farklı yapılandırmalarda zaman uyumsuz olarak test etme
- Herhangi bir şablon değişikliği olmadan hazırlama ve üretim işlem hatları ile tümleştirme
- Aynı laboratuvarda hem geliştirme makinelerini hem de ortamların yönetilmesi, yönetim ve maliyet raporlama kolaylığını geliştirir.  

DevTest Labs kaynak sağlayıcısı, laboratuvar kullanıcısının adına kaynaklar oluşturur, bu nedenle PaaS kaynaklarının kullanımını etkinleştirmek için laboratuvar kullanıcısına ek izin verilmesi gerekmez. Aşağıdaki görüntüde, laboratuvarda bir ortam olarak Service Fabric kümesi gösterilmektedir.

![Bir ortam olarak Service Fabric kümesi](./media/create-environment-service-fabric-cluster/cluster-created.png)

Ortamları ayarlama hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonlarıyla çoklu VM ortamları ve PaaS kaynakları oluşturma](devtest-lab-create-environment-from-arm.md). DevTest Labs, sizin tarafınızdan bir dış GitHub kaynağına bağlanmak zorunda kalmadan ortamlar oluşturmak için kullanabileceğiniz Azure Resource Manager şablonlarının ortak bir deposuna sahiptir. Ortak ortamlar hakkında daha fazla bilgi için bkz. [Azure DevTest Labs ortak ortamları yapılandırma ve kullanma](devtest-lab-configure-use-public-environments.md).

Büyük kuruluşlarda, geliştirme ekipleri genellikle özelleştirilmiş/yalıtılmış test ortamları gibi ortamları sağlar. Bir iş birimi veya bölüm içindeki tüm takımlar tarafından kullanılacak ortamlar olabilir. BT grubu, kuruluştaki tüm takımlar tarafından kullanılabilecek ortamlarını sağlamak isteyebilir.  

## <a name="customizations"></a>Özelleştirmeler

#### <a name="sandbox"></a>Alanda 
Laboratuvar sahibi, laboratuvar ortamlarını özelleştirerek kullanıcının rolünü **okuyucudan** kaynak grubu içinde **katkıda bulunan** olarak değiştirebilir. Bu özellik, laboratuvarın **yapılandırma ve ilkeleri** altındaki **Laboratuvar ayarları** sayfasıdır. Roldeki bu değişiklik, kullanıcının bu ortamda kaynak eklemesine veya kaldırmasına izin verir. Erişimi daha fazla kısıtlamak istiyorsanız Azure ilkeleri ' ni kullanın. Bu işlevsellik, abonelik düzeyinde erişim olmadan kaynakları veya yapılandırmayı özelleştirmenizi sağlar.

#### <a name="custom-tokens"></a>Özel belirteçler
Kaynak grubunun dışında olan ve şablonun erişebileceği ortamlara özel bazı özel laboratuvar bilgileri vardır. Bunlardan bazıları şunlardır: 

- Laboratuvar ağı kimliği
- Konum
- Kaynak Yöneticisi şablon dosyalarının depolandığı depolama hesabı. 
 
#### <a name="lab-virtual-network"></a>Laboratuvar sanal ağı
[Laboratuvarın sanal ağına bağlanan ortamlar](connect-environment-lab-virtual-network.md) , Kaynak Yöneticisi şablonunuzun `$(LabSubnetId)` belirtecini kullanacak şekilde nasıl değiştirileceğini açıklar. Bir ortam oluşturulduğunda, `$(LabSubnetId)` belirteci, **sanal makine oluşturma seçeneğinde kullanım** seçeneğinin **true**olarak ayarlandığı ilk alt ağ işaretiyle değiştirilmiştir. Ortamımızın daha önce oluşturulan ağları kullanmasına izin verir. Test, hazırlama ve üretim olarak aynı Kaynak Yöneticisi şablonlarını kullanmak istiyorsanız, bir Kaynak Yöneticisi Şablon parametresinde varsayılan değer olarak `$(LabSubnetId)` kullanın. 

#### <a name="environment-storage-account"></a>Ortam depolama hesabı
DevTest Labs [iç içe Kaynak Yöneticisi şablonlarının](../azure-resource-manager/resource-group-linked-templates.md)kullanımını destekler. [[Ortamları test etmek için iç içe Azure Resource Manager şablonları dağıtma](deploy-nested-template-environments.md) makalesi, `_artifactsLocation` ve `_artifactsLocationSasToken` belirteçlerinin, ana şablonun iç içe yerleştirilmiş bir klasöründe veya aynı klasörde bulunan bir kaynak yöneticisi ŞABLONUNA bir URI oluşturmak için nasıl kullanılacağını açıklar. Bu iki belirteç hakkında daha fazla bilgi için [Azure Resource Manager – En Iyi Yöntemler kılavuzunun](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md) **dağıtım yapıtları** bölümüne bakın.

## <a name="user-experience"></a>Kullanıcı deneyimi

## <a name="developer"></a>Geliştirici
Geliştiriciler, belirli bir ortamı oluşturmak için bir VM oluşturmak üzere aynı iş akışını kullanır. Ortam ve makine görüntüsünü seçip şablon için gerekli olan bilgileri girin. Bir ortama sahip her geliştirici, değişikliklerin dağıtımına ve geliştirilmiş iç döngü hata ayıklamasına olanak tanır. Ortam, en son şablon kullanılarak herhangi bir zamanda oluşturulabilir.  Bu özellik, sistemin el ile oluşturulmasını veya hata sınamadan kurtarmasını önlemek için ortamların yok edilmesine ve yeniden oluşturulmasına olanak sağlar.  

### <a name="testing"></a>Test Etme
DevTest Labs ortamları, belirli kod ve yapılandırmaların zaman uyumsuz olarak test edilmesine olanak tanır. Yaygın olarak kullanılan uygulama, tek tek çekme isteğinden kod ile ortamı ayarlamaya ve otomatikleştirilmiş testleri başlatmaya yardımcı olur. Otomatikleştirilmiş test tamamlandığında, belirli bir ortama karşı el ile test gerçekleştirebilirsiniz. Genellikle bu işlem CI/CD işlem hattının bir parçası olarak yapılır. 

## <a name="management-experience"></a>Yönetim deneyimi

### <a name="cost-tracking"></a>Maliyet Izleme
Maliyet izleme özelliği, genel maliyet eğilimi kapsamında farklı ortamlarda Azure kaynaklarını içerir. Kaynaklara göre maliyet, ortamdaki farklı kaynakları bozmaz, ancak ortamı tek bir maliyet olarak görüntüler.

### <a name="security"></a>Güvenlik
DevTest Labs ile düzgün şekilde yapılandırılmış bir Azure aboneliği [, yalnızca laboratuvar aracılığıyla Azure kaynaklarına erişimi sınırlayabilir](devtest-lab-add-devtest-user.md). Ortamlar sayesinde, laboratuvar sahibi kullanıcıların diğer Azure kaynaklarına erişim izni vermeden, onaylı yapılandırmalara sahip PaaS kaynaklarına erişmelerine izin verebilir. Laboratuvar kullanıcılarının ortamları özelleştiren senaryoda, laboratuvar sahibi katılımcı erişimine izin verebilir. Katkıda bulunan erişimi, laboratuvar kullanıcısına yalnızca yönetilen kaynak grubu içinde Azure kaynağı ekleme veya kaldırma izni verir. Daha kolay izleme ve yönetim sağlamak için kullanıcının aboneliğe erişimine izin verir.

### <a name="automation"></a>Automation
Otomasyon, büyük ölçekli, etkili bir ekosistem için önemli bir bileşendir. Otomasyon, abonelikler ve laboratuvarlar genelinde birden çok ortamı yönetmeyi veya izlemeyi işlemek için gereklidir.

### <a name="cicd-pipeline"></a>CI/CD işlem hattı
DevTest Labs 'deki PaaS hizmetleri, erişimin laboratuvar tarafından denetlendiği odaklı dağıtımlar sunarak CI/CD işlem hattının artırılmasına yardımcı olabilir.

## <a name="next-steps"></a>Sonraki adımlar
Ortamlar hakkındaki ayrıntılar için aşağıdaki makalelere bakın: 

- 
- [Azure Resource Manager şablonları ile çoklu VM ortamları ve PaaS kaynakları oluşturma](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs ortak ortamları yapılandırma ve kullanma](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs içinde kendi içinde Service Fabric kümeyle bir ortam oluşturun](create-environment-service-fabric-cluster.md)
- [Azure DevTest Labs ortamında bir ortamı laboratuvarınızın sanal ağına bağlama](connect-environment-lab-virtual-network.md)
- [Ortamları Azure DevOps CI/CD işlem hatlarınızla tümleştirin](integrate-environments-devops-pipeline.md)
 





