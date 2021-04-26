---
title: Windows sanal masaüstü Aracısı 'nı kullanmaya başlama
description: Windows sanal masaüstü Aracısı ve güncelleştirme işlemlerine genel bakış.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 529a86712994aae91a554589d383cc748f79d07f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520114"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Windows sanal masaüstü Aracısı 'nı kullanmaya başlama

Windows sanal masaüstü hizmeti çerçevesinde, üç ana bileşen vardır: Uzak Masaüstü istemcisi, hizmet ve sanal makineler. Bu sanal makineler, Windows sanal masaüstü aracısının ve aracı önyükleme yükleyicilerinin yüklendiği müşteri aboneliğinde bulunur. Aracı, hizmet ile sanal makineler arasında ara Communicator görevi görür ve bağlantıyı etkinleştirir. Bu nedenle, aracı yükleme, güncelleştirme veya yapılandırma ile ilgili herhangi bir sorun yaşıyorsanız, sanal makineleriniz hizmete bağlanamayacak. Aracı önyükleme Aracısı, aracıyı yükleyen çalıştırılabilir dosyadır. 

Bu makale, aracı yükleme ve güncelleştirme işlemlerine ilişkin kısa bir genel bakış sunar.

>[!NOTE]
>Bu belge FSLogix Aracısı veya uzak masaüstü Istemci Aracısı için değildir.


## <a name="initial-installation-process"></a>İlk yükleme işlemi

Windows sanal masaüstü Aracısı, başlangıçta iki şekilde yüklenir. Azure portal ve Azure Marketi 'nde sanal makineler (VM) sağlarsanız, aracı ve aracı önyükleme yükleyicisi otomatik olarak yüklenir. PowerShell kullanarak VM 'Ler sağlarsanız, [PowerShell ile bir Windows sanal masaüstü konak havuzu oluştururken](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)aracı ve aracı önyükleme yükleyicisi. msi dosyalarını el ile indirmeniz gerekir. Aracı yüklendikten sonra, Windows sanal masaüstü yan yana yığınını ve Genfiliz Izleme aracısını yüklenir. Yan yana yığın bileşeni, kullanıcıların ters sunucudan istemciye bağlantıları güvenli bir şekilde kurmasını sağlamak için gereklidir. Geneva Izleme Aracısı aracının sistem durumunu izler. Bu bileşenlerden üçü, uçtan uca Kullanıcı bağlantısının düzgün şekilde çalışması için gereklidir.

>[!IMPORTANT]
>Windows sanal masaüstü aracısını, yan yana yığını ve Genfiliz Izleme aracısını başarıyla yüklemek için [gereklı URL listesinde](safe-url-list.md#virtual-machines)listelenen tüm URL 'lerin engellemesini kaldırmanız gerekir. Windows Sanal Masaüstü hizmetini kullanmak için bu URL 'Lerin engellemesini kaldırma işlemi gereklidir.

## <a name="agent-update-process"></a>Aracı güncelleştirme işlemi

Windows sanal masaüstü hizmeti, bir güncelleştirme kullanılabilir olduğunda aracıyı günceller. Aracı güncelleştirmeleri, önceki sorunlara yönelik yeni işlevselliği veya düzeltmeleri içerebilir. Sanal makinelerinizin bağlantı veya güvenlik kaybı olmamasını sağlamak için aracının en son kararlı sürümünü her zaman yüklemiş olmanız gerekir. Windows sanal masaüstü aracısının ilk sürümü yüklendikten sonra, aracı, aracı, yığın veya izleme bileşeninin daha yeni bir sürümü olup olmadığını anlamak için Windows Sanal Masaüstü hizmetini düzenli aralıklarla sorgular. Bileşenlerden birinin daha yeni bir sürümü zaten dağıtılmışsa, güncelleştirilmiş bileşen fışıklandırma sistemi tarafından otomatik olarak yüklenir.

Aracının yeni sürümleri, tüm Azure aboneliklerine weeklong dönemdeki düzenli aralıklarla dağıtılır. Bu güncelleştirme dönemlerinde "fışıkları" adı verilir. Bir uçuş gerçekleştiğinde, konak havuzunuzdaki sanal makineler, farklı zamanlarda aracı güncelleştirmesini alabilir. Tüm aboneliklerdeki tüm VM aracıları dağıtım döneminin sonuna göre güncelleştirilir. Windows sanal masaüstü fışıklandırma sistemi, aracı güncelleştirmesinin kararlılığını ve kalitesini sağlayarak hizmetin güvenilirliğini geliştirir.


Göz önünde bulundurmanız gereken diğer önemli noktalar:

- Konak havuzunuzdaki VM 'Ler farklı zamanlarda aracı güncelleştirmeleri alabileceğinden, fışıklandırma sorunları ve başarısız aracı güncelleştirmeleri arasındaki farkı söylemelisiniz. **Olay Görüntüleyicisi**  >  **Windows günlükleri** uygulamasında sanal makinenizin olay günlüklerine giderseniz  >   ve "kimlik 3277" etiketli bir olay görürseniz, bu da aracı güncelleştirmesinin çalışmadığı anlamına gelir. Bu olayı görmüyorsanız, sanal makine farklı bir uçuşta ve daha sonra güncelleştirilir.
- Geneva Izleme Aracısı en son sürüme güncelleştirdiğinde, yeni izleme Aracısı için yeni bir görev oluşturmadan önce eski GenevaTask görevi bulunur ve devre dışı bırakılır. İzleme aracısının en son sürümünün düzeltilmesi için önceki sürüme geri dönülmesini gerektiren bir sorun olması durumunda izleme aracısının önceki sürümü silinmez. En son sürümde bir sorun varsa, izleme verilerini sunmaya devam etmek için eski izleme Aracısı yeniden etkinleştirilecek. Güncelleştirmeden önce yüklediğiniz son olandan önceki tüm sürümleri, sanal makinenizde silinmeden silinecek.
- VM 'niz tek seferde yan yana yığının üç sürümünü tutar. Bu, güncelleştirmede bir sorun olursa hızlı kurtarma sağlar. Yığının en eski sürümü, yığın her güncelleştirildiğinde VM 'den kaldırılır.

Aracı güncelleştirmesi normalde yeni bir sanal makinede 2-3 dakika sürer ve VM 'nizin bağlantı kaybetmesine veya kapatılmasını kaybetmesine neden olmaz. Bu güncelleştirme işlemi, Azure Resource Manager ile hem Windows sanal masaüstü (klasik) hem de en son Windows sanal masaüstü sürümü için geçerlidir.

## <a name="next-steps"></a>Sonraki adımlar

Artık Windows sanal masaüstü Aracısı hakkında daha iyi anlayışınız olduğuna göre, şu konularda size yardımcı olabilecek bazı kaynaklar aşağıda verilmiştir:

- Aracı veya bağlantıyla ilgili sorunlar yaşıyorsanız, [Windows sanal masaüstü Aracısı sorunları sorun giderme kılavuzuna](troubleshoot-agent.md)göz atın.
