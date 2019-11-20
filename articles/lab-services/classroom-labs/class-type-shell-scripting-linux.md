---
title: Azure Lab Services bir Linux Shell betik Laboratuvarı ayarlama | Microsoft Docs
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
ms.openlocfilehash: 9502a81149df11ffa378bf8ffc2c377f4bb22d7f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177122"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Linux 'ta kabuk betiği oluşturmaya yönelik bir laboratuvar kurun
Bu makalede, Linux 'ta kabuk betiği oluşturmaya yönelik bir laboratuvarın nasıl ayarlanacağı gösterilmektedir. Komut dosyası, yöneticilerin yinelenen görevleri önlemeyi sağlayan, sistem yönetiminin yararlı bir parçasıdır. Bu örnek senaryoda, sınıfı geleneksel Bash betiklerini ve gelişmiş betikleri içerir. Gelişmiş betikler, Bash komutlarını ve Ruby 'yi birleştiren betiklerdir. Bu yaklaşım, Ruby 'nin kabuğa ve Bash komutlarına karşı etkileşimde bulunmak için veri geçmesini sağlar. 

Bu komut dosyası sınıflarını alan öğrenciler Linux 'un temel bilgilerini öğrenmenizi ve Bash Shell komut dosyası hakkında bilgi sahibi olmak için bir Linux sanal makinesi alır. Linux sanal makinesi, uzak masaüstü erişimi etkinleştirilmiş ve [gedit](https://help.gnome.org/users/gedit/stable/) ve [Visual Studio Code](https://code.visualstudio.com/) metin düzenleyicileri yüklüyken birlikte gelir.

## <a name="lab-configuration"></a>Laboratuvar yapılandırması
Bu Laboratuvarı kurmak için başlamak üzere bir Azure aboneliğine sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Azure aboneliğiniz olduğunda, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilir veya var olan bir laboratuvar hesabını kullanabilirsiniz. Yeni bir laboratuvar hesabı oluşturmak için aşağıdaki öğreticiye bakın: [Laboratuvar hesabı ayarlama öğreticisi](tutorial-setup-lab-account.md).

Laboratuvar hesabını oluşturduktan sonra laboratuvar hesabında aşağıdaki ayarları etkinleştirin: 

| Laboratuvar hesabı ayarı | Yönergeler |
| ----------- | ------------ |  
| Market görüntüleri | Laboratuar hesabınızda kullanmak için [Ubuntu Server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) görüntüsünü etkinleştirin. Daha fazla bilgi için bkz. [Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtme](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators) | 

Yeni bir laboratuvar oluşturmak ve aşağıdaki ayarları uygulamak için [Bu öğreticiyi](tutorial-setup-classroom-lab.md) izleyin:

| Laboratuvar ayarları | Değer/yönergeler | 
| ------------ | ------------------ |
| Sanal makine (VM) boyutu | Küçük  |
| VM görüntüsü | [Ubuntu Server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Uzak Masaüstü bağlantısı 'nı etkinleştir | Etkinleştirebilir. <p>Bu ayarın etkinleştirilmesi, öğretmenler ve öğrencilerin, Uzak Masaüstü (RDP) kullanarak VM 'lerine bağlanmasına imkan tanır. Daha fazla bilgi için bkz. [Azure Lab Services bir laboratuvarda Linux sanal makineleri için Uzak Masaüstü 'Nü etkinleştirme](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Masaüstü ve xrdp 'yi yükler
[Ubuntu server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) görüntüsünde varsayılan olarak uzak masaüstü sunucusu yüklü değil. Uzak Masaüstü Protokolü aracılığıyla bağlanmak üzere şablon makinesinde gerekli olan paketleri yüklemek için, [Uzak Masaüstü 'Nü yüklemek ve yapılandırmak Için Azure 'da bir LINUX VM 'ye bağlanma](../../virtual-machines/linux/use-remote-desktop.md) makalesindeki yönergeleri izleyin.

## <a name="install-ruby"></a>Ruby’yi yükleme
Ruby, Bash betiklerle birleştirilebilecek açık kaynaklı bir dinamik dildir. Bu bölümde, [Ruby](https://www.ruby-lang.org/)'nin en son sürümünü yüklemek için `apt-get` ' ın nasıl kullanılacağı gösterilmektedir.

1. Aşağıdaki komutları çalıştırarak güncelleştirmeleri yükler:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  [Ruby](https://www.ruby-lang.org/)'yi yükler.  Ruby, Bash betiklerle birleştirilebilecek açık kaynaklı bir dinamik dildir. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Geliştirme araçları 'nı yükler
Bu bölümde, birkaç metin Düzenleyicisi 'nin nasıl yükleneceği gösterilmektedir. Gedit, GNOME masaüstü ortamı için varsayılan metin düzenleyicisidir. Genel amaçlı metin düzenleyicisi olarak tasarlanmıştır. Visual Studio Code, hata ayıklama ve kaynak denetimi tümleştirmesi için destek içeren bir metin düzenleyicisidir.

> [!NOTE]
> Kullanılabilir birkaç farklı metin Düzenleyicisi vardır. Visual Studio Code ve gedit yalnızca iki örnektir.

1. [Gedit](https://help.gnome.org/users/gedit/stable/)'yi yükler.

    ```bash
    sudo apt-get install gedit
    ```
1. [Visual Studio Code](https://code.visualstudio.com/)'i yükler.  Visual Studio Code, Snap Store kullanılarak yüklenebilir.  Alternatif yükleme seçenekleri için bkz. [Visual Studio Code alternatif İndirmeleri](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Şablon artık güncellenir ve Laboratuvarı tamamlaması için gereken programlama diline ve geliştirme araçlarına sahiptir. Şablon görüntüsü artık laboratuvara yayımlanabilir. Şablonu laboratuvarda yayımlamak için şablon sayfasında **Yayımla** düğmesini seçin.  

## <a name="cost"></a>Maliyet 
Bu laboratuvarın maliyetini tahmin etmek isterseniz, aşağıdaki örneği kullanabilirsiniz:
 
20 saatlik zamanlanan sınıf süresi ve ev ödevi veya atamalar için 10 saatlik kota içeren 25 öğrenciye sahip bir sınıf için, laboratuvar fiyatı şöyle olacaktır: 

25 öğrenci * (20 + 10) saat * 20 laboratuvar birimi * 0,01 ABD Doları/150 saat

Fiyatlandırma hakkında daha fazla bilgi için şu belgede bulunabilir: [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç
Bu makale, komut dosyası sınıfları için Laboratuvar oluşturma adımlarında size yardımcı olacak. Bu makale, Linux makinesinde Ruby betik araçları ayarlamaya odaklanırken, Linux üzerinde Python gibi diğer betik sınıfları için de aynı kurulum kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
Sonraki adımlar herhangi bir laboratuvarı ayarlamak için ortaktır:

- [Kullanıcı ekleme](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Öğrenciler Için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users). 





