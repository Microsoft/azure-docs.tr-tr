---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9c18a2c74d03a636a0865f3008eb421ab8d7412d
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781502"
---
1. Portalda **+Kaynak oluştur**’a tıklayın.
2. Arama kutusuna **Yerel ağ geçidi** yazıp aramak için **Enter** tuşuna basın. Böylece sonuçların bir listesi döndürülür. **Yerel ağ geçidi**’ne ve sonra **Oluştur** düğmesine tıklayarak **Yerel ağ geçidi oluştur** sayfasını açın.

   ![Yerel ağ geçidi oluşturma](./media/vpn-gateway-add-lng-rm-portal-include/local-network-gateway.png "Yerel ağ geçidi oluşturma")

3. **Yerel ağ geçidi oluştur** sayfasında, yerel ağ geçidiniz için değerlerleri belirtin.

   - **Ada** Yerel ağ geçidi nesneniz için bir ad belirtin.
   - **IP adresi:** Bu, Azure 'un bağlanmasını istediğiniz VPN cihazının genel IP adresidir. Geçerli bir genel IP adresi belirtin. IP adresini şu anda bilmiyorsanız örnekte gösterilen değerleri kullanabilirsiniz ancak geri dönüp yer tutucu IP adresinizi VPN cihazınızın genel IP adresiyle değiştirmeniz gerekir. Aksi halde Azure bağlantı kuramaz.
   - **Adres Alanı**, bu yerel ağın temsil ettiği ağa ilişkin adres aralıkları anlamına gelir. Birden fazla adres alanı aralığı ekleyebilirsiniz. Burada belirttiğiniz aralıkların, bağlanmak istediğiniz diğer ağların aralıklarıyla çakışmadığından emin olun. Azure, belirttiğiniz adres aralığını şirket içi VPN cihazının IP adresine yönlendirir. *Şirket içi sitenize bağlanmak istiyorsanız, burada, örnekte gösterilen değerleri değil, kendi değerlerinizi kullanın*.
   - **BGP ayarlarını yapılandır:** Yalnızca BGP yapılandırılırken kullanın. Aksi takdirde, bu seçeneği işaretlemeyin.
   - **Aboneliğiniz** Doğru aboneliğin gösterildiğini doğrulayın.
   - **Kaynak grubu:** Kullanmak istediğiniz kaynak grubunu seçin. Yeni bir kaynak grubu oluşturabilir veya önceden oluşturduğunuz birini seçebilirsiniz.
   - **Konumuna** Bu nesnenin oluşturulacağı konumu seçin. VNet'inizin bulunduğu konumu seçebilirsiniz ancak bu zorunlu değildir.

4. Değerleri belirtmeyi tamamladığınızda, ağ geçidini oluşturmak için **Oluştur** düğmesine tıklayın.
