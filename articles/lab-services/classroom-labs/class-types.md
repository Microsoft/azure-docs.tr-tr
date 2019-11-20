---
title: Azure Lab Services üzerinde örnek sınıf türleri | Microsoft Docs
description: ', Azure Lab Services kullanarak laboratuvarları ayarlayabileceğiniz bazı sınıf türlerini sağlar.'
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0e8b203240b4b2d6b67534ab52a7cd4ccf5df571
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976594"
---
# <a name="class-types-overview---azure-lab-services"></a>Sınıf türlerine genel bakış-Azure Lab Services
Azure Lab Services, bulutta ders Laboratuvarı ortamlarını hızlıca ayarlamanıza olanak sağlar. Bu bölümdeki makaleler Azure Lab Services kullanarak çeşitli sınıf laboratuvarlarının nasıl ayarlanacağı hakkında rehberlik sağlar.

## <a name="deep-learning-in-natural-language-processing"></a>Doğal dil işlemede derin öğrenme
Azure Lab Services kullanarak doğal dil işleme (NLP) için derin öğrenmeye odaklanan bir laboratuvar ayarlayabilirsiniz. Doğal dil işleme (NLP), çeviri, konuşma tanıma ve diğer dil anlama özelliklerine sahip bilgisayarları sağlayan yapay zeka (AI) biçimidir. Bir NLP sınıfı alan öğrenciler bir Linux sanal makinesi (VM) alır ve yazılı insan dilini çözümlemek için kullanılan derin öğrenme modelleri geliştirmek üzere sinir ağ algoritmalarının nasıl uygulanacağını öğrenin. 

Bu tür bir laboratuvarın nasıl ayarlanacağı hakkında ayrıntılı bilgi için bkz. [Azure Lab Services kullanarak doğal dil işlemede derin öğrenmeye odaklanan bir laboratuvar ayarlama](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Linux üzerinde kabuk betiği oluşturma
Linux 'ta kabuk betiği oluşturmak için bir laboratuvar ayarlayabilirsiniz. Komut dosyası, yöneticilerin yinelenen görevleri önlemeyi sağlayan, sistem yönetiminin yararlı bir parçasıdır. Bu örnek senaryoda, sınıfı geleneksel Bash betiklerini ve gelişmiş betikleri içerir. Gelişmiş betikler, Bash komutlarını ve Ruby 'yi birleştiren betiklerdir. Bu yaklaşım, Ruby 'nin kabuğa ve Bash komutlarına karşı etkileşimde bulunmak için veri geçmesini sağlar. 

Bu komut dosyası sınıflarını alan öğrenciler Linux 'un temel bilgilerini öğrenmenizi ve Bash Shell komut dosyası hakkında bilgi sahibi olmak için bir Linux sanal makinesi alır. Linux sanal makinesi, uzak masaüstü erişimi etkinleştirilmiş ve [gedit](https://help.gnome.org/users/gedit/stable/) ve [Visual Studio Code](https://code.visualstudio.com/) metin düzenleyicileri yüklüyken birlikte gelir.

Bu tür bir laboratuvarın nasıl ayarlanacağı hakkında ayrıntılı bilgi için bkz. [Linux 'Ta Shell Scripting](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Ahlak hacme 
Ahlak hackinin çatalına odaklanan bir sınıf için laboratuvar ayarlayabilirsiniz. Ahlak hacham topluluk tarafından kullanılan bir uygulama olan sızma testi, birisi kötü niyetli bir saldırganın yararlanmasına yönelik güvenlik açıklarını göstermek üzere sisteme veya ağa erişim kazanmayı denediğinde oluşur. 

Ahlak hacklik bir sınıfta öğrenciler, güvenlik açıklarına karşı savunma için modern teknikler öğrenmesini sağlayabilir. Her öğrenci, iki iç içe sanal makineye sahip bir sanal makine olan ve **Metaspoiltable** görüntüsüne sahip bir sanal makine ve [kalı Linux](https://www.kali.org/) görüntüsüne sahip başka bir makine içeren bir Windows Server konak sanal makinesi Metasplosever sanal makinesi, daha fazla yararlanmak için kullanılır ve kalı sanal makinesi, adli görevlerini yürütmek için gereken araçlara erişim sağlar.

Bu tür bir laboratuvarın nasıl ayarlanacağı hakkında ayrıntılı bilgi için bkz. [ahlak hacme sınıfı öğretmek için laboratuvar ayarlama](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın: 

- [Azure Lab Services kullanarak doğal dil işlemede derin öğrenmeye odaklanan bir laboratuvar kurun](class-type-deep-learning-natural-processing.md)
- [Linux üzerinde kabuk betiği oluşturma](class-type-shell-scripting-linux.md)