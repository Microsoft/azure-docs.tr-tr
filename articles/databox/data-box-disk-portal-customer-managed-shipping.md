---
title: Microsoft Azure Data Box Disk kendinden yönetilen kargo | Verilerdeki Microsoft Docs
description: Azure Data Box Disk cihazlar için kendi kendine yönetilen teslim iş akışını açıklar
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526339"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Azure portal Azure Data Box Disk için kendi kendine yönetilen dağıtımı kullanın

Bu makalede, Azure Data Box Disk sipariş, çekme ve bırakma işlemleri için kendi kendine yönetilen sevkıyat görevleri açıklanmaktadır. Azure portal kullanarak Data Box Disk yönetebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[Azure Data Box disk sipariş](data-box-disk-deploy-ordered.md)ettiğinizde kendi kendine yönetilen sevkıyat bir seçenek olarak kullanılabilir. Otomatik olarak yönetilen kargo yalnızca aşağıdaki bölgelerde kullanılabilir:

* ABD Kamu
* Birleşik Krallık
* Batı Avrupa
* Avustralya
* Japonya
* Singapur
* Güney Kore
* Güney Afrika
* Hindistan (Önizleme)

## <a name="use-self-managed-shipping"></a>Otomatik gönderimi kullanma

Data Box Disk bir sıra yerleştirdiğinizde, kendi kendine yönetilen teslim seçeneğini belirleyebilirsiniz.

1. Azure Data Box Disk sırasında, **kişi ayrıntıları** altında **+ Sevkiyat Adresi Ekle**' yi seçin.

   ![Teslimat adresi ekle seçeneği ile Ilgili kişi ayrıntıları adımını gösteren sipariş Sihirbazı ekran görüntüsü.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Sevkiyat türü seçerken, **kendi kendine yönetilen sevkiyat** seçeneğini belirleyin. Bu seçenek yalnızca, Önkoşullar bölümünde açıklandığı gibi desteklenen bir bölgedeyse kullanılabilir.

3. Sevkiyat adresinizi sağlamadıktan sonra doğrulamanız ve siparişinizi doldurmanız gerekir.

   ![Sevkiyat Adresi Ekle iletişim kutusunun gönderme seçenekleriyle birlikte gelen ve teslim adresi ekle seçeneğinin ekran görüntüsü.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Cihaz hazırlandıktan ve bir e-posta bildirimi aldıktan sonra bir toplama zamanlayabilirsiniz. Azure Data Box Disk sırada **genel bakış** ' a gidin ve ardından **çekme zamanlaması**' nı seçin.

   ![Data Box cihazını toplama için sıralama](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. **Azure Için zamanlama çekme** konusundaki yönergeleri izleyin. Yetkilendirme kodunuzu alabilmeniz için önce, [adbops@microsoft.com](mailto:adbops@microsoft.com) bölgenizin veri merkezinizden cihaz alımı zamanlamak üzere e-posta almalısınız.

   ![Toplamayı zamanlama](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Cihaz piara hesabınızı zamanladıktan sonra, yetkilendirme kodunuzu **Azure Için zamanlama çekme** bölümünde görüntüleyebilirsiniz.

   ![Çekme için yetkilendirme kodu metin kutusu adlı Azure için zaman çizelgesi Seç iletişim kutusunun ekran görüntüsü.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Bu yetkilendirme kodunu bir yere getirin.

   Her güvenlik gereksinimi için, planlama sırasında çekme sırasında, çekme için verilecek kişinin adını sunmak gerekir.

   Ayrıca, çekme için veri merkezine kimlerin gidebilecek ayrıntıları da sağlamanız gerekir. Siz veya iletişim noktası, veri merkezinde doğrulanacak resmi onaylı bir fotoğraf KIMLIĞI içermelidir.

   Cihazı alan kişinin yetkilendirme koduna da sahip olması gerekir. Yetkilendirme kodu, bir çekme veya bir bırakma için benzersizdir ve veri merkezinde onaylanır.

7. Cihaz veri merkezinden alındıktan sonra siparişiniz otomatik olarak **çekme** durumuna getirilir.

   ![Teslim alındı](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Cihaz çekildikten sonra, sitenizdeki Data Box Disk verileri kopyalayabilirsiniz. Veri kopyalama işlemi tamamlandıktan sonra, Data Box Disk göndermeye hazırlanabilirsiniz.

   Veri kopyalamayı tamamladıktan sonra, açılan bir randevuyu zamanlamak için işlemler ile iletişim kurun. Diskleri bırakmak için veri merkezine gelen kişinin ayrıntılarını paylaşmanız gerekir. Ayrıca, veri merkezi 'nin onay sırasında yetkilendirme kodunu doğrulaması gerekir. Azure portal onay için yetkilendirme kodunu, **zamanlama bırakma** bölümündeki bulacaksınız.

   > [!NOTE]
   > Yetkilendirme kodunu e-posta üzerinden paylaşmayın. Bu, yalnızca, açılan liste sırasında veri merkezinde doğrulanmalıdır.

9. Bırakma için bir randevu aldıktan sonra, siparişin Azure portal **Azure veri merkezi 'nde teslim etmeye hazırlanma** durumunda olması gerekir.

   ![Kullanıma alma seçenekleri dışında teslimat adresi Ekle iletişim kutusunun ve teslim adresi ekle seçeneğinin ekran görüntüsü.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. KIMLIĞINIZ ve yetkilendirme kodunuz doğrulandıktan ve cihazı veri merkezinde devre dışı bıraktığınız zaman, sipariş durumu **alınmalıdır**.

    ![Alınan Tamam](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Cihaz alındıktan sonra veri kopyalama devam eder. Kopyalama tamamlandığında, sipariş tamamlanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı Başlangıç: Azure portalını kullanarak Azure Data Box Disk'i dağıtma](data-box-disk-quickstart-portal.md)
