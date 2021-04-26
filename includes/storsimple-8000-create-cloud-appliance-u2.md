---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8b827a80059cecadd7b76263f13dd4a0d141fc7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559082"
---
#### <a name="to-create-a-cloud-appliance"></a>Bulut gereci oluşturmak için

1. Azure portalında **StorSimple Cihaz Yöneticisi** hizmetine gidin.
2. **Cihazlar** dikey penceresine gidin. Hizmet özeti dikey penceresindeki komut çubuğundan **Bulut gereci oluştur**’a tıklayın.
    ![StorSimple bulut gereci oluşturma](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. **Bulut gereci oluşturma** dikey penceresinde aşağıdaki ayrıntıları belirtin.
   
    ![StorSimple bulut gereci oluştur 2](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Ad**: Bulut gereciniz için benzersiz bir ad.
   2. **Model**: Bulut gerecinin modelini seçin. 8010 cihazlar 30 TB Standart Depolama sunarken 8020 cihazlar 64 TB Premium Depolama sunar. Öğe düzeyinde alma senaryolarını yedeklerden dağıtmak için 8010’u belirtin. Yüksek performanslı, düşük gecikme süreli iş yükleri dağıtmak veya olağanüstü durum kurtarmaya yönelik ikincil bir cihaz olarak kullanmak için 8020’yi seçin.
   3. **Sürüm**: Bulut gerecinin sürümünü seçin. Sürüm, bulut uygulaması oluşturmak için kullanılan sanal disk görüntüsünün sürümüne karşılık gelir. Hangi fiziksel cihazın yükünü devredebileceğiniz veya verilerini kopyalayabileceğiniz bulut gerecinin sürümüne bağlı olduğundan, bulut gerecinin uygun bir sürümünü oluşturmanız önemlidir.
   4. **Sanal Ağ**: Bu bulut gereciyle kullanmak istediğiniz bir sanal ağ belirtin. Premium Storage kullanılıyorsa, Premium Storage hesabıyla desteklenen bir sanal ağ seçmelisiniz. Desteklenmeyen sanal ağlar açılan listede gri olur. Desteklenmeyen bir sanal ağı seçerseniz bir uyarı alırsınız.
   5. **Alt Ağ**: Seçili sanal ağa bağlı olarak, açılan listede ilişkili alt ağlar görüntülenir. Bulut gerecinize bir alt ağ atayın.
   6. **Depolama hesabı**: Sağlama sırasında bulut gerecinin görüntüsünü barındıracak bir depolama hesabı seçin. Bu depolama hesabı bulut gereci ve sanal ağla aynı bölgede olmalıdır. Fiziksel cihaz veya bulut gereci tarafından veri depolama için kullanılmamalıdır. Varsayılan olarak, bu amaca yönelik olarak yeni bir depolama hesabı oluşturulur. Ancak, bu kullanım için uygun bir depolama hesabınızın zaten olduğunu biliyorsanız, bunu listeden seçebilirsiniz. Premium bulut gereci oluşturuluyorsa, açılan listede yalnızca Premium Depolama hesapları görüntülenir.
      
      > [!NOTE]
      > Bulut gereci yalnızca Azure Storage hesaplarıyla çalışabilir.
    
   7. Bulut gerecinde depolanan verilerin bir Microsoft veri merkezinde barındırıldığını anladığınızı belirtmek için onay kutusunu seçin.
       * Yalnızca bir fiziksel cihaz kullandığınızda, şifreleme anahtarınızın cihazınızla tutulur; Bu nedenle, Microsoft şifresini çözemez.

       * Bulut gereci kullandığınızda, hem şifreleme anahtarı, hem de şifre çözme anahtarı Microsoft Azure’da depolanır. Daha fazla bilgi edinmek için bkz. [bulut gereci kullanımıyla ilgili güvenlik konuları](../articles/storsimple/storsimple-8000-security.md).
   8. Bulut gerecini sağlamak için **Oluştur**’a tıklayın. Cihazın hazır olması yaklaşık 30 dakika sürebilir. Bulut gereci başarıyla oluşturulduğunda size bildirilir. Cihazlar dikey penceresine gittiğinizde, cihaz listesi bulut gerecini görüntüleyecek şekilde güncelleştirilir. Gerecin durumu **Kuruluma hazır** olur.
      
      ![StorSimple Cloud Appliance kuruluma hazır](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)