---
title: Azure için HTTPS üzerinden Windows Uzaktan Yönetimi | Azure Marketi
description: Azure 'da barındırılan, Windows tabanlı bir VM 'nin PowerShell ile uzaktan yönetilebilmesi için nasıl yapılandırılacağı açıklanmaktadır.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: e39f83b2ed715afbfff69770c151cfc4d527105d
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132268"
---
# <a name="windows-remote-management-over-https"></a>HTTPS üzerinden Windows Uzaktan Yönetimi

Bu bölümde, Azure 'da barındırılan, Windows tabanlı bir VM 'nin PowerShell ile uzaktan yönetilebilmesi ve dağıtılması için nasıl yapılandırılacağı açıklanmaktadır.  PowerShell uzaktan iletişimini etkinleştirmek için hedef VM 'nin bir Windows Uzaktan Yönetimi (WinRM) HTTPS uç noktasını kullanıma sunması gerekir.  PowerShell uzaktan iletişimi hakkında daha fazla bilgi için bkz. [uzak komutları çalıştırma](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  WinRM hakkında daha fazla bilgi için bkz. [Windows Uzaktan Yönetimi](https://docs.microsoft.com/windows/desktop/WinRM/portal).

"Klasik" Azure yaklaşımlardan birini (Azure Service Manager portalı veya kullanım dışı bırakılmış [azure hizmet yönetim API'si](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))) kullanarak bir VM oluşturduysanız, otomatik olarak bir WinRM uç noktası ile yapılandırılır.  Ancak, aşağıdaki "modern" Azure yaklaşımının birini kullanarak bir VM oluşturursanız, VM 'niz HTTPS üzerinden WinRM için *yapılandırılmayacak.*

- [Azure ile uyumlu bır VHD oluşturma](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd) bölümünde açıklandığı gibi, genellikle onaylanan bir tabandan [Azure Portal](https://portal.azure.com/)kullanma
- [Azure Resource Manager şablonlarını kullanma](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Azure PowerShell veya Azure CLı komut kabuğunu kullanma.  Örnekler için bkz. [hızlı başlangıç: Azure 'Da PowerShell Ile Windows sanal makinesi oluşturma](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) ve [hızlı başlangıç: Azure CLI ile Linux sanal makinesi oluşturma](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Bu WinRM uç noktası, VM [görüntünüzü onaylama](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)bölümünde AÇıKLANDıĞı gibi VM 'yi ekleme için sertifika araç setini çalıştırmak için de gereklidir.

Buna karşılık, genellikle Linux VM 'Ler bir SSH konsolundan [Azure CLI](https://docs.microsoft.com/cli/azure) veya Linux komutları kullanılarak uzaktan yönetilir.  Azure, [LINUX sanal makinenizde betikleri çalıştırmak](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)için çeşitli alternatif yöntemler de sağlar.  Daha karmaşık senaryolar için, Windows veya Linux tabanlı VM 'Ler için kullanabileceğiniz çeşitli otomasyon ve tümleştirme çözümleri vardır.


## <a name="configure-and-deploy-with-winrm"></a>WinRM ile yapılandırma ve dağıtma

Windows tabanlı bir VM için WinRM uç noktası, geliştirmenin iki farklı aşamasında yapılandırılabilir:

- Oluşturma sırasında-VM 'nin mevcut bir VHD 'ye dağıtılması sırasında.  Bu, yeni teklifler için tercih edilen yaklaşımdır.  Bu yaklaşım, sağlanan Azure Resource Manager şablonlarını kullanarak ve özelleştirilmiş PowerShell betikleri çalıştırarak bir Azure sertifikası oluşturulmasını gerektirir.
- Dağıtımdan sonra, Azure 'da barındırılan mevcut bir VM 'de.  Azure üzerinde dağıtılmış bir VM çözümünüz varsa ve bu yaklaşım için pencere uzak yönetimini etkinleştirmeniz gerekiyorsa bu yaklaşımı kullanın.  Bu yaklaşım, Azure portal ve hedef VM üzerinde bir betiğin yürütülmesi için el ile yapılan değişiklikler gerektirir.


## <a name="next-steps"></a>Sonraki adımlar
Yeni bir VM oluşturuyorsanız, [sanal makinenizin VHD 'lerinden dağıtımı](./cpp-deploy-vm-vhd.md)sırasında WinRM 'yi etkinleştirebilirsiniz.  Aksi halde, WinRM mevcut bir VM 'de etkinleştirilebilir
