---
title: Windows sanal masaüstü (klasik) sorun gidermeye genel bakış-Azure
description: Windows sanal masaüstü (klasik) kiracı ortamı ayarlarken sorun gidermeye yönelik genel bakış.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: efc96f03d93227cfffbb533bf1ce1786a0a47d14
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444165"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Windows sanal masaüstü (klasik) sorun gidermeye genel bakış, geri bildirim ve destek

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../troubleshoot-set-up-overview.md)bakın.

Bu makalede, bir Windows sanal masaüstü kiracı ortamı ayarlarken karşılaşabileceğiniz sorunlara genel bir bakış sağlanır ve sorunları çözmeye yönelik yollar sağlanır.

## <a name="provide-feedback"></a>Geribildirim gönderme

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="escalation-tracks"></a>Yükseltme izleri

Uzak Masaüstü istemcisi 'ni kullanarak bir kiracı ortamı ayarlarken karşılaşabileceğiniz sorunları belirlemek ve çözmek için aşağıdaki tabloyu kullanın. Kiracınız kurulduktan sonra, yaygın senaryolara yönelik sorunları belirlemek için yeni [Tanılama hizmetimizi](diagnostics-role-service-2019.md) kullanabilirsiniz.

>[!NOTE]
> Ürün ekibi ve etkin topluluk üyeleriyle ilgili sorunlarınızı tartışmak için ziyaret edebildikleri bir teknik topluluk forumumuz vardır. Bir tartışmaya başlamak için [Windows sanal masaüstü teknik Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

| **Sorun**                                                            | **Önerilen Çözüm**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Windows sanal masaüstü kiracısı oluşturma                                                    | Bir Azure kesintisi varsa, [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/); Aksi halde [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet Için **Windows sanal masaüstü** ' nü seçin, sorun türü için **dağıtım** ' yi seçin ve ardından sorun alt türü Için **Windows sanal masaüstü kiracısı oluşturma sorunları** ' nı seçin.|
| Azure portal Market şablonlarına erişme       | Bir Azure kesintisi varsa, [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/). <br> <br> Azure Market Windows sanal masaüstü şablonları serbestçe kullanılabilir.|
| GitHub 'dan Azure Resource Manager şablonlarına erişme                                  | [Kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues-2019.md)'Nın [Windows sanal masaüstü oturumu ana bilgisayarı VM 'leri oluşturma](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms) bölümüne bakın. Sorun çözülmedi, [GitHub destek ekibine](https://github.com/contact)başvurun. <br> <br> GitHub 'daki şablona erişildikten sonra hata oluşursa, [Azure desteği](https://azure.microsoft.com/support/create-ticket/)'ne başvurun.|
| Oturum Ana bilgisayar havuzu Azure sanal ağ (VNET) ve hızlı rota ayarları               | [Bir Azure destek Isteği açın](https://azure.microsoft.com/support/create-ticket/)ve uygun hizmeti seçin (ağ kategorisi altında). |
| Windows sanal masaüstü ile birlikte sunulan Azure Resource Manager şablonları kullanılmıyorsa, oturum ana bilgisayar havuzu sanal makinesi (VM) oluşturma | [Bir Azure destek Isteği açın](https://azure.microsoft.com/support/create-ticket/)ve ardından hizmet için **Windows çalıştıran sanal makine** ' yi seçin. <br> <br> Windows sanal masaüstü ile birlikte sunulan Azure Resource Manager şablonlarıyla ilgili sorunlar için, bkz. [kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues-2019.md)'Nın Windows sanal masaüstü kiracı bölümü oluşturma. |
| Azure portal Windows sanal masaüstü oturumu ana bilgisayarı ortamını yönetme    | [Bir Azure destek Isteği açın](https://azure.microsoft.com/support/create-ticket/). <br> <br> Uzak Masaüstü Hizmetleri/Windows sanal masaüstü PowerShell 'i kullanırken yönetim sorunları için, bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell-2019.md) veya [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet için **Windows sanal masaüstü** ' nü seçin, sorun türü için **yapılandırma ve yönetim** ' i seçin, ardından sorun alt türü için **PowerShell 'i kullanarak kiracı yapılandırma sorunları** ' nı seçin. |
| Konak havuzlarına ve uygulama gruplarına bağlı Windows sanal masaüstü yapılandırmasını yönetme (uygulama grupları)      | [Windows sanal masaüstü PowerShell](troubleshoot-powershell-2019.md)'e bakın veya [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet Için **Windows sanal masaüstü 'nü** seçip ilgili sorun türünü seçin.|
| FSLogix profil kapsayıcılarını dağıtma ve yönetme | Bkz. [FSLogix ürünleri Için sorun giderme kılavuzu](/fslogix/fslogix-trouble-shooting-ht/) ve bu sorunu çözmezse, [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet için **Windows sanal masaüstü** ' nü seçin, sorun türü için **fslogix** ' i seçin ve ardından uygun sorun alt türünü seçin. |
| Başlangıç sırasında uzak masaüstü istemcileri hatalı çalışma                                                 | Bkz. [Uzak Masaüstü Istemcisinde sorun giderme](../troubleshoot-client.md) ve bu sorunu çözmezse,  [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet için **Windows sanal masaüstü** ' nü seçin ve ardından sorun türü için **Uzak Masaüstü istemcileri** ' ni seçin.  <br> <br> Ağ sorunu varsa, kullanıcılarınızın ağ yöneticisiyle iletişim kurabilmesi gerekir. |
| Bağlı ancak akış yok                                                                 | [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection-2019.md)'nın Kullanıcı bağlantılarını kullanarak sorun giderme, [ancak hiçbir şey (akış yok) bölümü görüntülenir](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) . <br> <br> Kullanıcılarınız bir uygulama grubuna atandıysa,  [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet Için **Windows sanal masaüstü** ' nü seçin ve ardından sorun türü için **Uzak Masaüstü istemcileri** ' ni seçin. |
| Ağ nedeniyle akış bulma sorunları                                            | Kullanıcılarınızın ağ yöneticisiyle iletişim kurabilmesi gerekir. |
| İstemcileri bağlama                                                                    | Bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection-2019.md) ve bu sorunu çözmezse, bkz. [oturum ana bilgisayar sanal makine yapılandırması](troubleshoot-vm-configuration-2019.md). |
| Uzak uygulama veya masaüstü yanıtlama hızı                                      | Sorunlar belirli bir uygulama veya ürüne bağlıysa, bu üründen sorumlu olan ekibe başvurun. |
| Lisanslama iletileri veya hataları                                                          | Sorunlar belirli bir uygulama veya ürüne bağlıysa, bu üründen sorumlu olan ekibe başvurun. |
| GitHub 'da Windows sanal masaüstü araçları kullanılırken sorunlar (Azure Resource Manager şablonları, Tanılama Aracı, yönetim aracı) | Sorunları bildirmek için [Uzak Masaüstü Hizmetleri Azure Resource Manager şablonlar](https://github.com/Azure/RDS-Templates/blob/master/README.md) bölümüne bakın. |

## <a name="next-steps"></a>Sonraki adımlar

- Bir Windows sanal masaüstü ortamında kiracı ve konak havuzu oluştururken oluşan sorunları gidermek için bkz. [kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues-2019.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration-2019.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection-2019.md).
- Uzak Masaüstü istemcileriyle ilgili sorunları gidermek için bkz [. uzak masaüstü Istemcisinde sorun giderme](../troubleshoot-client.md)
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell-2019.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](environment-setup-2019.md).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../../azure-resource-manager/management/view-activity-logs.md).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](../../azure-resource-manager/templates/deployment-history.md).
