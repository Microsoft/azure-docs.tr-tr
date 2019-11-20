---
title: Azure Lab Services kullanarak derin öğrenime odaklanan bir laboratuvar ayarlayın | Microsoft Docs
description: Linux 'ta kabuk betiği oluşturmaya yönelik bir laboratuvarı ayarlamayı öğrenin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0aa30c114153521258842fc5f1e9150053b766f4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332294"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Azure Lab Services kullanarak doğal dil işlemede derin öğrenmeye odaklanan bir laboratuvar kurun
Bu makalede, Azure Lab Services kullanarak doğal dil işleme (NLP) için derin öğrenime odaklanan bir laboratuvarı nasıl ayarlayabileceğiniz gösterilmektedir. Doğal dil işleme (NLP), çeviri, konuşma tanıma ve diğer dil anlama özelliklerine sahip bilgisayarları sağlayan yapay zeka (AI) biçimidir.  

Bir NLP sınıfı alan öğrenciler bir Linux sanal makinesi (VM) alır ve yazılı insan dilini çözümlemek için kullanılan derin öğrenme modelleri geliştirmek üzere sinir ağ algoritmalarının nasıl uygulanacağını öğrenin. 

## <a name="lab-configuration"></a>Laboratuvar yapılandırması
Bu Laboratuvarı kurmak için başlamak üzere bir Azure aboneliğine sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Azure aboneliğiniz olduğunda, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilir veya var olan bir laboratuvar hesabını kullanabilirsiniz. Yeni bir laboratuvar hesabı oluşturmak için aşağıdaki öğreticiye bakın: [Laboratuvar hesabı ayarlama öğreticisi](tutorial-setup-lab-account.md).
 
Laboratuvar hesabını oluşturduktan sonra laboratuvar hesabında aşağıdaki ayarları etkinleştirin: 

| Laboratuvar hesabı ayarı | Yönergeler |
| ----------- | ------------ |  
| Market görüntüleri | Laboratuvar hesabınızda kullanmak için Veri Bilimi Sanal Makinesi Linux (Ubuntu) görüntüsünü etkinleştirin.  Yönergeler için aşağıdaki makalelere bakın: [Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtin](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators). | 

Yeni bir laboratuvar oluşturmak ve aşağıdaki ayarları uygulamak için [Bu öğreticiyi](tutorial-setup-classroom-lab.md) izleyin:

| Laboratuvar ayarları | Değer/yönergeler | 
| ------------ | ------------------ |
| Sanal makine (VM) boyutu | Küçük GPU (Işlem). Bu boyut, yapay zeka ve derin öğrenme gibi yoğun işlem yoğunluğu ve yoğun ağ kullanımı gerektiren uygulamalar için idealdir. |
| VM görüntüsü | [Linux için veri bilimi sanal makinesi (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Bu görüntü, Machine Learning ve veri bilimi için derin öğrenme çerçeveleri ve araçları sağlar. Bu görüntüdeki yüklü araçların tam listesini görüntülemek için şu makaleye bakın: [DSVM 'ye nelerin dahil olduğu?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Uzak Masaüstü bağlantısı 'nı etkinleştir | Etkinleştirebilir. <p>Bu ayarın etkinleştirilmesi, öğretmenler ve öğrencilerin uzak masaüstü (RDP) kullanarak kendi sanal makinelerine (VM) bağlanmasına imkan tanır.</p><p>**Önemli**: RDP, Linux görüntüsü için veri bilimi sanal makinesi zaten yüklü ve yapılandırılmış. Sonuç olarak, öğretmenler/öğrenciler ek adımlar olmadan VM 'lere RDP aracılığıyla bağlanabilir. Ayrıca, grafik masaüstüne bağlanmanız gerekiyorsa, bu görüntüde [X2Go sunucusu](https://wiki.x2go.org/doku.php/doc:newtox2go) zaten sanal makinede yüklü olmalıdır. Öğrenciler, X2Go istemcisini yerel makinelerine yüklemelidir ve bağlanmak için istemcisini kullanmalıdır. Daha fazla bilgi için aşağıdaki kılavuzlara bakın: <ul><li>[Linux için Veri Bilimi Sanal Makinesi erişme](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[RDP ve GUI paketlerini yüklemek için şablon VM 'sine bağlanma](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Linux görüntüsü için Veri Bilimi Sanal Makinesi, bu tür bir sınıf için gereken ayrıntılı öğrenme çerçevelerini ve araçları sağlar. Sonuç olarak, şablon makine oluşturulduktan sonra daha fazla özelleştirmeniz gerekmez. Bu, öğrencilerin kullanması için yayımlanabilir. Şablonu laboratuvarda yayımlamak için şablon sayfasında **Yayımla** düğmesini seçin.  

## <a name="cost"></a>Maliyet
Bu laboratuvarın maliyetini tahmin etmek isterseniz, aşağıdaki örneği kullanabilirsiniz: 

20 saatlik zamanlanan sınıf süresi ve ev ödevi veya atamalar için 10 saatlik kota içeren 25 öğrencideki bir sınıf için, laboratuvarın fiyatı-25 öğrencilertir * (20 + 10) saat * 139 Lab birimi * 0,01 saat başına USD = 1042,5 ABD Doları

Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç
Bu makale, doğal dil işleme sınıfı için Laboratuvar oluşturma adımlarında size kılavuzluk eden adımlar. Diğer derin öğrenme sınıfları için benzer bir kurulum kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Sonraki adımlar herhangi bir laboratuvarı ayarlamak için ortaktır:

- [Kullanıcı ekleme](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Öğrenciler Için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users). 

