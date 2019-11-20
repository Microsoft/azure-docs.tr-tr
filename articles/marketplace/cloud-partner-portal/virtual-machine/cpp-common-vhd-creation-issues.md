---
title: Azure Marketi için VHD oluşturma (SSS) sırasında sık karşılaşılan sorunlar
description: VHD oluşturma ve ilişkili sorunlar hakkında sık sorulan sorular.
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 1d4224d8f1f735ecef3d4551e3032b916e81dc3c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826625"
---
# <a name="common-issues-during-vhd-creation-faq"></a>VHD oluşturma sırasında yaygın sorunlar (SSS)

Aşağıdaki sık sorulan sorular (SSS) VM teklifleri için sanal sabit disk (VHD) ve sanal makine (VM) oluşturma sırasında karşılaşılan yaygın sorunları ele alır. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Premium depolamaya yüklenen VHD 'YI kullanarak Azure portal bir VM nasıl oluşturulur?

Azure Marketi Şu anda yönetilen depolama veya Azure Premium Depolama üzerinde bulunan görüntülerden VM tekliflerinin oluşturulmasını desteklememektedir.  Bu depolama seçenekleri hakkında daha fazla bilgi için bkz. [Azure yönetilen disklere genel bakış](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Teklifler için 2. nesil VM 'Leri kullanabilir miyim?

Hayır, yalnızca nesil 1 VHD 'ler desteklenir.  Ancak, 2. nesil VM 'Lerin desteğini araştırmak için Microsoft Azure platform ekibiyle çalışıyoruz.  Farklar hakkında daha fazla bilgi için bkz. [Hyper-V ' d a 1. kuşak veya 2 sanal makine oluşturmalıyım?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Konağın adını nasıl değiştirirsiniz?

Oluşturamazsınız.  VM oluşturulduktan sonra kullanıcılar (sahipler dahil) konağın adını güncelleştiremez.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Uzak Masaüstü hizmetini veya oturum açma parolasını nasıl sıfırlayabilirim?

Aşağıdaki makalelerde, Windows ve Linux tabanlı VM 'Ler için RDS sıfırlamaları gerçekleştirme açıklanmaktadır:   

- [Windows VM’de Uzak Masaüstü hizmetini veya oturum açma parolasını sıfırlama](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Bir Linux VM parolasını veya SSH anahtarını sıfırlama, SSH yapılandırmasını çözme ve VMAccess uzantısını kullanarak disk tutarlılığını denetleme](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Yeni SSH sertifikaları nasıl oluşturulur?

Sertifika oluşturma, [BIR VM teklifi için teknik varlıklar oluşturma](./cpp-create-technical-assets.md)BAŞLıKLı makalede [VM Görüntünüz için paylaşılan erişim Imzası URI 'si al](./cpp-get-sas-uri.md) makalesinde açıklanmaktadır.


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Sanal özel ağ (VPN), VM 'lerim ile çalışacak şekilde nasıl yapılandırılır?

Azure Resource Manager dağıtım modelini kullanıyorsanız, VPN 'yi ayarlama konusunda üç genel seçeneğiniz vardır:
- [Azure portal kullanarak rota tabanlı bir VPN ağ geçidi oluşturma](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [PowerShell kullanarak rota temelli VPN ağ geçidi oluşturma](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [CLı kullanarak rota temelli VPN ağ geçidi oluşturma](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure tabanlı VM 'lerde Microsoft sunucu yazılımını çalıştırmaya yönelik Microsoft destek ilkeleri nelerdir?

Bu destek ilkeleri, [Microsoft Azure sanal makineler Için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)makalesinde ayrıntılı olarak açıklanmıştır.


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Sanal makinelerin kendileriyle ilişkili benzersiz tanımlayıcıları var mı?

Evet, Azure üzerinde barındırılıyorsa.  Azure, oluşturulan her yeni VM kaynağına Azure sanal makinesi benzersiz KIMLIĞI olarak adlandırılan benzersiz bir tanımlayıcı atar.  Daha fazla bilgi için [Azure sanal makinesi BENZERSIZ kimliğini](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)okuyun.  Ayrıca, bu tanımlayıcıyı [liste API 'si](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)aracılığıyla programlı bir şekilde edinebilirsiniz.


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Bir VM 'de, başlangıç görevinde Özel Betik uzantısını nasıl yöneteceksiniz?

Aşağıdaki makalede, Windows sistemlerinde Azure PowerShell modülü, Azure Resource Manager şablonları ve Ayrıntılar sorunlarını giderme adımları kullanılarak özel betik uzantısının nasıl kullanılacağı açıklanır: [Windows Için özel Betik uzantısı](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>32 bitlik uygulamalar veya hizmetler Azure Marketi 'nde destekleniyor mu?

Genel, hayır.  Azure VM 'Leri için desteklenen işletim sistemleri ve standart hizmetler 64 bittir.  Ancak, teknik bir bakış ile 64 bitlik işletim sistemleri, geriye dönük uyumluluk için uygulamaların 32 bit sürümlerini çalıştırmayı destekler.  Ancak, VM çözümünüzün bir parçası olarak 32 bitlik uygulamalar kullanılması desteklenmez ve bu nedenle *kesinlikle önerilmez*.  Bunun yerine, uygulamanızı 64 bitlik bir proje olarak yeniden derleyin.

Daha fazla bilgi için aşağıdaki makalelere bakın:
- [32 bitlik uygulamalar çalıştırma](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure sanal makinelerde 32 bitlik işletim sistemleri için destek](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure sanal makineleri için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>VHD 'lerimin içinden bir görüntü oluşturmaya her seferinde, PowerShell 'de hata `.VHD is already registered with image repository as the resource` alıyorum. Azure 'da bu ada sahip herhangi bir görüntü oluşturamıyorum veya hiç görüntü oluşturmadınız. Bu sorunu çözmek Nasıl yaparım? mı?

Bu sorun genellikle Kullanıcı kilidi olan bir VHD 'den bir VM sağladıysa oluşur.  Bu VHD 'den ayrılmış bir VM olmadığından emin olun ve işlemi yeniden deneyin.  Bu sorun devam ederse, [bulut iş ortağı portalı Için destek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal)bölümünde açıklandığı gibi bir destek bileti açın. 

