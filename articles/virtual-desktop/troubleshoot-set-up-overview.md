---
title: Windows sanal masaüstü sorunlarını gidermeye genel bakış-Azure
description: Windows sanal masaüstü kiracı ortamı ayarlarken sorun gidermeye yönelik genel bakış.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: 7f80fb07bbed3bdb478bce557241a92cd571cc83
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904892"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Sorun çözümüne genel bakış, geri bildirim ve destek

Bu makalede, bir Windows sanal masaüstü kiracı ortamı ayarlarken karşılaşabileceğiniz sorunlara genel bir bakış sağlanır ve sorunları çözmeye yönelik yollar sağlanır.

## <a name="provide-feedback"></a>Geri bildirimde bulunma

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="escalation-tracks"></a>Yükseltme izleri

Uzak Masaüstü istemcisi 'ni kullanarak bir kiracı ortamı ayarlarken karşılaşabileceğiniz sorunları belirlemek ve çözmek için aşağıdaki tabloyu kullanın. Kiracınız kurulduktan sonra, yaygın senaryolara yönelik sorunları belirlemek için yeni [Tanılama hizmetimizi](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) kullanabilirsiniz.

>[!NOTE]
> Ürün ekibi ve etkin topluluk üyeleriyle ilgili sorunlarınızı tartışmak için ziyaret edebildikleri bir teknik topluluk forumumuz vardır. [Windows sanal masaüstü teknik Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret ederek 

| **Konuda**                                                            | **Önerilen çözüm**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Kiracı oluşturma                                                    | Bir Azure kesintisi varsa [Azure desteği](https://azure.microsoft.com/support/options/)'ne başvurun; Aksi takdirde, **Windows sanal masaüstü (işlem) için bir destek isteği açın**.|
| Azure portal Market şablonlarına erişme       | Bir Azure kesintisi varsa [Azure desteği](https://azure.microsoft.com/support/options/)'ne başvurun. <br> <br> Azure Market Windows sanal masaüstü şablonları serbestçe kullanılabilir.|
| GitHub 'dan Azure Resource Manager şablonlarına erişme                                  | [Kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues.md)'Nın "Windows sanal masaüstü oturumu ana bilgisayarı VM 'leri oluşturma" bölümüne bakın. Sorun çözülmedi, [GitHub destek ekibine](https://github.com/contact)başvurun. <br> <br> GitHub 'daki şablona erişildikten sonra hata oluşursa, [Azure desteği](https://azure.microsoft.com/support/options/)'ne başvurun.|
| Oturum Ana bilgisayar havuzu Azure sanal ağ (VNET) ve hızlı rota ayarları               | **Azure desteği 'ne başvurun (ağ)** . |
| Windows sanal masaüstü ile birlikte sunulan Azure Resource Manager şablonları kullanılmıyorsa, oturum ana bilgisayar havuzu sanal makinesi (VM) oluşturma | **Azure desteği 'ne başvurun (işlem)** . <br> <br> Windows sanal masaüstü ile birlikte sunulan Azure Resource Manager şablonlarıyla ilgili sorunlar için, bkz. [kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues.md)'Nın Windows sanal masaüstü kiracı bölümü oluşturma. |
| Azure portal Windows sanal masaüstü oturumu ana bilgisayarı ortamını yönetme    | **Azure desteği**'ne başvurun. <br> <br> Uzak Masaüstü Hizmetleri/Windows sanal masaüstü PowerShell 'i kullanırken yönetim sorunları için, bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md) veya **Windows sanal masaüstü (işlem) için bir destek isteği açın**. |
| Konak havuzlarına ve uygulama gruplarına bağlı Windows sanal masaüstü yapılandırmasını yönetme (uygulama grupları)      | Windows [sanal masaüstü PowerShell](troubleshoot-powershell.md)'e bakın veya **Windows sanal masaüstü (işlem) için bir destek isteği açın**. <br> <br> Sorunlar örnek grafik kullanıcı arabirimine (GUI) bağlıysa, Yammer topluluğuna ulaşın.|
| Başlangıç sırasında uzak masaüstü istemcileri hatalı çalışma                                                 | Bkz. [Uzak Masaüstü istemci bağlantıları](troubleshoot-client-connection.md) ve bu sorunu çözmezse, **Windows sanal masaüstü (işlem) için bir destek isteği açın**.  <br> <br> Ağ sorunu varsa, kullanıcılarınızın ağ yöneticisiyle iletişim kurabilmesi gerekir. |
| Bağlı ancak akış yok                                                                 | [Uzak Masaüstü istemci bağlantılarının](troubleshoot-client-connection.md)"Kullanıcı bağlantıları, ancak hiçbir şey görüntülenmiyor (akış yok)" bölümünü kullanarak sorun giderin. <br> <br> Kullanıcılarınız bir uygulama grubuna atandıysa, **Windows sanal masaüstü (işlem) için bir destek isteği açın**. |
| Ağ nedeniyle akış bulma sorunları                                            | Kullanıcılarınızın ağ yöneticisiyle iletişim kurabilmesi gerekir. |
| İstemcileri bağlama                                                                    | Bkz. [Uzak Masaüstü istemci bağlantıları](troubleshoot-client-connection.md) ve bu sorun çözülmezse, bkz. [oturum ana bilgisayar sanal makine yapılandırması](troubleshoot-vm-configuration.md). |
| Uzak uygulama veya masaüstü yanıtlama hızı                                      | Sorunlar belirli bir uygulama veya ürüne bağlıysa, bu üründen sorumlu olan ekibe başvurun. |
| Lisanslama iletileri veya hataları                                                          | Sorunlar belirli bir uygulama veya ürüne bağlıysa, bu üründen sorumlu olan ekibe başvurun. |
| GitHub 'da Windows sanal masaüstü araçları kullanılırken sorunlar (Azure Resource Manager şablonları, Tanılama Aracı, yönetim aracı) | Sorunları raporlamak için [Uzak Masaüstü Hizmetleri ARM şablonlarına](https://github.com/Azure/RDS-Templates/blob/master/README.md) bakın. |

## <a name="next-steps"></a>Sonraki adımlar

- Bir Windows sanal masaüstü ortamında kiracı ve konak havuzu oluştururken oluşan sorunları gidermek için bkz. [kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Uzak Masaüstü istemci bağlantıları](troubleshoot-client-connection.md).
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
