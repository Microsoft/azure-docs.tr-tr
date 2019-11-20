---
title: Load Balancer bir ön uç Azure portal oluşturun
titlesuffix: Azure Load Balancer
description: Azure Portal ile bölgesel ön ucu ile standart Load Balancer oluşturma hakkında bilgi edinin
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 9a260c267febb3dc851fe0a8ca8a8e351124116b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275148"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Azure Portal kullanarak bölgesel ön ucu ile standart Load Balancer oluşturma

Bu makalede, bir özel ön uç IP yapılandırması ile genel [Standart Load Balancer](https://aka.ms/azureloadbalancerstandard) oluşturma adımları sağlanır. Kullanılabilirlik alanları standart Load Balancer ile nasıl çalıştığını anlamak için bkz: [Standard Load Balancer ve kullanılabilirlik bölgeleri](load-balancer-standard-availability-zones.md). 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!NOTE]
> Kullanılabilirlik bölgeleri, seçili Azure kaynakları ve bölgeler ve sanal makine boyutu aileleri için kullanılabilir. Kullanmaya başlamak nasıl daha fazla bilgi ve hangi Azure kaynakları, bölgeleri ve kullanılabilirlik alanları ile deneyebilirsiniz sanal makine boyutu aileleri için bkz. [kullanılabilirlik alanlarına genel bakış](https://docs.microsoft.com/azure/availability-zones/az-overview). Destek için [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) üzerinden bize ulaşabilir veya [bir Azure destek bileti açabilirsiniz](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Azure'da oturum açma 

[https://portal.azure.com](https://portal.azure.com ) adresinden Azure portalında oturum açın.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Bölgesel bir ön uç IP adresine sahip yük dengeleyici oluşturma

1. Bir tarayıcıdan Azure portalına gidin: [ https://portal.azure.com ](https://portal.azure.com) ve Azure hesabınızla oturum açın.
2. Ekranın sol üst tarafında seçin **kaynak Oluştur** > **ağ** > **yük dengeleyici.**
3. İçinde **yük dengeleyici Oluştur** sayfasındaki **adı** türü **myLoadBalancer**.
4. **Tür** bölümünde **Genel**’i seçin.
5. SKU altında seçin **standart**.
6. Tıklayın **bir genel IP adresi seçin**, tıklayın **Yeni Oluştur**hem de **genel IP adresi oluşturma** sayfasında, adı, türü altında **myPublicIPZonal**, SKU için seçin **standart**, kullanılabilirlik bölgesi için seçin **1**.
    
>[!NOTE] 
> Genel IP Bu adımda oluşturduğunuz standart SKU'nun varsayılan olarak açıktır.

1. İçin **kaynak grubu**, tıklayın **Yeni Oluştur**, Anahtar'a tıklayın ve **myResourceGroupZLB** kaynak grubunun adı.
1. İçin **konumu**seçin **Batı Avrupa**ve ardından **Tamam**. Yük dengeleyici dağıtımı başlar ve birkaç dakika içinde başarıyla tamamlanır.

    ![Azure portal ile bölge yedekli Standart Load Balancer oluşturma](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Sonraki adımlar
- Daha fazla bilgi edinin [Standard Load Balancer ve kullanılabilirlik bölgeleri](load-balancer-standard-availability-zones.md).



