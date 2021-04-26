---
title: Windows sanal masaüstü sorunlarını gidermeye genel bakış-Azure
description: Windows sanal masaüstü ortamı ayarlanırken sorun gidermeye yönelik genel bakış.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1108b736c9e2358ee57b34d7e88d2b841da71010
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445355"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Sorun giderme genel bakış, geri bildirim ve Windows sanal masaüstü desteği

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)bakın.

Bu makalede, bir Windows sanal masaüstü ortamı ayarlarken karşılaşabileceğiniz sorunlara genel bir bakış sağlanır ve sorunları çözmeye yönelik yollar sağlanır.

## <a name="report-issues"></a>Sorun bildirme

Azure Resource Manager tümleştirme ile Windows sanal masaüstü için sorunları bildirmek veya özellikler önermek için [Windows sanal masaüstü teknik topluluğu](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)' nu ziyaret edin. En iyi uygulamaları tartışmak veya yeni özellikleri önermek ve Oylamak için Tech Community 'yi kullanabilirsiniz.

Yardım isteyen bir gönderi yaptığınızda veya yeni bir özellik önerdiğinde, konuyu mümkün olduğunca ayrıntılı olarak tanımladığınızdan emin olun. Ayrıntılı bilgiler, diğer kullanıcıların sorularınızı yanıtlamasını veya oy önerdiğinize ilişkin özelliği anlamanıza yardımcı olabilir.

## <a name="escalation-tracks"></a>Yükseltme izleri

Başka bir şey yapmadan önce, Azure hizmetinizin düzgün çalıştığından emin olmak için Azure [durum sayfasını](https://status.azure.com/status) ve [Azure hizmet durumunu](https://azure.microsoft.com/features/service-health/) kontrol ettiğinizden emin olun.

Uzak Masaüstü istemcisi 'ni kullanarak bir ortam ayarlarken karşılaşabileceğiniz sorunları belirlemek ve çözmek için aşağıdaki tabloyu kullanın. Ortamınız kurulduktan sonra, yaygın senaryolara yönelik sorunları belirlemek için yeni [Tanılama hizmetimizi](diagnostics-role-service.md) kullanabilirsiniz.

| **Sorun**                                                            | **Önerilen Çözüm**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Oturum Ana bilgisayar havuzu Azure sanal ağ (VNET) ve hızlı rota ayarları               | [Bir Azure destek Isteği açın](https://azure.microsoft.com/support/create-ticket/)ve uygun hizmeti seçin (ağ kategorisi altında). |
| Windows sanal masaüstü ile birlikte sunulan Azure Resource Manager şablonları kullanılmıyorsa, oturum ana bilgisayar havuzu sanal makinesi (VM) oluşturma | [Bir Azure destek Isteği açın](https://azure.microsoft.com/support/create-ticket/)ve ardından hizmet Için **Windows sanal masaüstü** ' nü seçin. <br> <br> Windows sanal masaüstü ile birlikte sunulan Azure Resource Manager şablonlarıyla ilgili sorunlar için, bkz. [konak havuzu oluşturma](troubleshoot-set-up-issues.md)'nın Azure Resource Manager şablon hataları bölümü. |
| Azure portal Windows sanal masaüstü oturumu ana bilgisayarı ortamını yönetme    | [Bir Azure destek Isteği açın](https://azure.microsoft.com/support/create-ticket/). <br> <br> Uzak Masaüstü Hizmetleri/Windows sanal masaüstü PowerShell 'i kullanırken yönetim sorunları için, bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md) veya [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet için **Windows sanal masaüstü** ' nü seçin, sorun türü için **yapılandırma ve yönetim** ' i seçin, ardından sorun alt türü için **PowerShell 'i kullanarak ortamı yapılandırma sorunları** ' nı seçin. |
| Konak havuzlarına ve uygulama gruplarına bağlı Windows sanal masaüstü yapılandırmasını yönetme (uygulama grupları)      | [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md)'e bakın veya [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet Için **Windows sanal masaüstü 'nü** seçip ilgili sorun türünü seçin.|
| FSLogix profil kapsayıcılarını dağıtma ve yönetme | Bkz. [FSLogix ürünleri Için sorun giderme kılavuzu](/fslogix/fslogix-trouble-shooting-ht/) ve bu sorunu çözmezse, [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet için **Windows sanal masaüstü** ' nü seçin, sorun türü için **fslogix** ' i seçin ve ardından uygun sorun alt türünü seçin. |
| Başlangıç sırasında uzak masaüstü istemcileri hatalı çalışma                                                 | Bkz. [Uzak Masaüstü Istemcisinde sorun giderme](troubleshoot-client.md) ve bu sorunu çözmezse,  [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet için **Windows sanal masaüstü** ' nü seçin ve ardından sorun türü için **Uzak Masaüstü istemcileri** ' ni seçin.  <br> <br> Ağ sorunu varsa, kullanıcılarınızın ağ yöneticisiyle iletişim kurabilmesi gerekir. |
| Bağlı ancak akış yok                                                                 | [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection.md)'nın Kullanıcı bağlantılarını kullanarak sorun giderme, [ancak hiçbir şey (akış yok) bölümü görüntülenir](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) . <br> <br> Kullanıcılarınız bir uygulama grubuna atandıysa,  [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/), hizmet Için **Windows sanal masaüstü** ' nü seçin ve ardından sorun türü için **Uzak Masaüstü istemcileri** ' ni seçin. |
| Ağ nedeniyle akış bulma sorunları                                            | Kullanıcılarınızın ağ yöneticisiyle iletişim kurabilmesi gerekir. |
| İstemcileri bağlama                                                                    | Bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection.md) ve bu sorunu çözmezse, bkz. [oturum ana bilgisayar sanal makine yapılandırması](troubleshoot-vm-configuration.md). |
| Uzak uygulama veya masaüstü yanıtlama hızı                                      | Sorunlar belirli bir uygulama veya ürüne bağlıysa, bu üründen sorumlu olan ekibe başvurun. |
| Lisanslama iletileri veya hataları                                                          | Sorunlar belirli bir uygulama veya ürüne bağlıysa, bu üründen sorumlu olan ekibe başvurun. |
| Üçüncü taraf kimlik doğrulama yöntemleriyle veya araçlarıyla ilgili sorunlar | Üçüncü taraf sağlayıcınızın Windows sanal masaüstü senaryolarını desteklediğini ve bilinen sorunlarla ilgili olarak bunları yaklaşımını doğrulayın. |
| Windows sanal masaüstü için Log Analytics kullanan sorunlar | Tanılama şeması ile ilgili sorunlar için [bir Azure destek isteği açın](https://azure.microsoft.com/support/create-ticket/).<br><br>Log Analytics sorguları, görselleştirme veya diğer sorunlar için Log Analytics altında uygun sorun türünü seçin. |
| M365 uygulamaları kullanma sorunları | [M365 Yönetim Merkezi yardım seçeneklerinden](/microsoft-365/admin/contact-support-for-business-products/)biriyle M365 yönetim merkezine başvurun. |

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ortamında bir konak havuzu oluştururken oluşan sorunları gidermek için bkz. [konak havuzu oluşturma](troubleshoot-set-up-issues.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration.md).
- Windows sanal masaüstü Aracısı veya oturum bağlantısıyla ilgili sorunları gidermek için bkz. [Genel Windows sanal masaüstü Aracısı sorunlarını giderme](troubleshoot-agent.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection.md).
- Uzak Masaüstü istemcileriyle ilgili sorunları gidermek için bkz [. uzak masaüstü Istemcisinde sorun giderme](troubleshoot-client.md)
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](environment-setup.md).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../azure-resource-manager/management/view-activity-logs.md).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](../azure-resource-manager/templates/deployment-history.md).
