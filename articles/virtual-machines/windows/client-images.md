---
title: Azure 'da Windows istemci görüntülerini kullanma
description: Azure 'da geliştirme ve test senaryoları için Windows 7, Windows 8 veya Windows 10 dağıtmak üzere Visual Studio abonelik avantajlarını kullanma
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: 7879e355573d2b9952728828bd3780094a919fe3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869064"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Azure’da geliştirme/test senaryoları için Windows istemcisini kullanma
Azure 'da, uygun bir Visual Studio (eski adıyla MSDN) aboneliğine sahip olduğunuz geliştirme ve test senaryoları için Windows 7, Windows 8 veya Windows 10 Enterprise (x64) kullanabilirsiniz. 

Windows 10 ' u bir üretim ortamında çalıştırmak için bkz. [Azure 'Da çok kiracılı barındırma haklarıyla Windows 10](windows-desktop-multitenant-hosting-deployment.md)' u dağıtma.


## <a name="subscription-eligibility"></a>Abonelik uygunluğu
Etkin Visual Studio aboneleri (Visual Studio aboneliği lisansı almış kişiler), geliştirme ve test amacıyla Windows istemci görüntülerini kullanabilir. Windows istemci görüntüleri, kendi donanımında veya Azure sanal makinelerde kullanılabilir.

Azure Marketi 'nden belirli Windows istemci görüntülerini bulabilirsiniz. Herhangi bir teklif türündeki Visual Studio aboneleri Ayrıca 64 bit Windows 7, Windows 8 veya Windows 10 görüntülerini [hazırlayabilir ve oluşturabilir](prepare-for-upload-vhd-image.md) ve ardından [Azure 'a yükleyebilir](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Uygun teklifler ve istemci görüntüleri
Aşağıdaki tabloda, Azure Marketi aracılığıyla Windows istemci görüntülerini dağıtmaya uygun teklif kimliklerinin ayrıntıları verilmiştir. Windows istemci görüntüleri yalnızca aşağıdaki tekliflere görünür. 

> [!NOTE]
> Görüntü teklifleri, Azure Marketi 'nde **Windows istemcisi** altındadır. Visual Studio aboneleri için kullanılabilen istemci görüntülerini ararken **Windows istemcisini** kullanın. 

| Teklif Adı | Teklif numarası | Kullanılabilir istemci görüntüleri | 
|:--- |:---:|:---:|
| [Kullandıkça Öde Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise (MPN) aboneleri](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Professional aboneler](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Test Professional aboneleri](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Premium with MSDN (avantaj)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise aboneleri](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise (BizSpark) aboneleri](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Kurumsal Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Azure aboneliğinizi denetleyin
Teklif KIMLIĞINIZI bilmiyor değilseniz, Azure portal aracılığıyla elde edebilirsiniz.  
- *Abonelikler* penceresinde: ![ Azure Portal teklif kimliği ayrıntıları](./media/client-images/offer-id-azure-portal.png) 
- Ya da **faturalandırma** ' e ve ardından abonelik kimliğiniz ' ne tıklayın. Teklif KIMLIĞI *faturalandırma* penceresinde görünür. 
- Teklif KIMLIĞINI Azure hesap portalının [' abonelikler ' sekmesinden](https://account.windowsazure.com/Subscriptions) da görüntüleyebilirsiniz: ![ Azure hesap portalından teklif kimliği ayrıntıları](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Sonraki adımlar
Artık VM 'lerinizi [PowerShell](quick-create-powershell.md), [Kaynak Yöneticisi şablonları](ps-template.md)veya [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)kullanarak dağıtabilirsiniz.
