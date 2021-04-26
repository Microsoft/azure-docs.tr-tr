---
title: Azure Stack Edge Pro-FPGA Share Management | Microsoft Docs
description: Azure Stack Edge Pro-FPGA 'de paylaşımları yönetmek için Azure portal nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 7fbb5ca43d2877e2e14914b71df7cc1bcdf27f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898386"
---
# <a name="use-the-azure-portal-to-manage-shares-on-azure-stack-edge-pro-fpga"></a>Azure Stack Edge Pro FPGA üzerinde paylaşımları yönetmek için Azure portal kullanma 

Bu makalede Azure Stack Edge Pro FPGA cihazınızda paylaşımların nasıl yönetileceği açıklanır. Azure Stack Edge Pro FPGA cihazını Azure portal veya yerel Web Kullanıcı arabirimi aracılığıyla yönetebilirsiniz. Paylaşımlar ile ilişkili depolama hesabı için, paylaşımları eklemek, silmek, yenilemek veya eşitleme depolama anahtarını kullanmak için Azure portal kullanın.

## <a name="about-shares"></a>Paylaşımlar hakkında

Azure 'a veri aktarmak için Azure Stack Edge Pro FPGA 'de paylaşımlar oluşturmanız gerekir. Azure Stack Edge Pro cihazına eklediğiniz paylaşımlar yerel paylaşımlar olabilir veya verileri buluta ititeden paylaşabilirsiniz.

 - **Yerel paylaşımlar**: verilerin cihazda yerel olarak işlenmesini istiyorsanız bu paylaşımları kullanın.
 - **Paylaşımlar**: cihaz verilerinin buluttaki depolama hesabınıza otomatik olarak iletilmesini istediğinizde bu paylaşımları kullanın. **Yenileme** ve **eşitleme depolama anahtarları** gibi tüm bulut işlevleri paylaşımlar için geçerlidir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Paylaşım ekleme
> * Paylaşımı silme
> * Paylaşımları yenileme
> * Depolama anahtarını eşitleme

## <a name="add-a-share"></a>Paylaşım ekleme

Paylaşım oluşturmak için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Azure portal, Azure Stack Edge kaynağına gidin ve ardından **bulut depolama ağ geçidi**' ne gidin. **Paylaşımlar** ' a gidin ve ardından komut çubuğunda **+ Paylaşım Ekle** ' yi seçin.

    ![Paylaşma Ekle seçeneğini belirleyin](media/azure-stack-edge-manage-shares/add-share-1.png)

2. **Paylaşım Ekle**'de, paylaşım ayarlarını belirtin. Paylaşımınız için benzersiz bir ad sağlayın.
    
    Paylaşım adları yalnızca rakam, küçük harf ve kısa çizgiler içerebilir. Paylaşım adı 3 ile 63 karakter arası uzunlukta olmalı ve bir harf veya rakamla başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır.

3. Paylaşma için bir **tür** seçin. Bu tür SMB veya **NFS**, SMB 'nin varsayılan olarak **olması olabilir.** SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır. SMB paylaşımları mı yoksa NFS paylaşımları mı seçtiğinize bağlı olarak, gösterilen seçenekler biraz farklı olur.

4. Paylaşımın duracağı **Depolama hesabını** sağlamanız gerekir. Henüz kapsayıcı yoksa, depolama hesabında paylaşım adıyla bir kapsayıcı oluşturulur. Kapsayıcı zaten varsa, bu var olan kapsayıcı kullanılır.

5. Açılan listeden, Blok Blobu, Sayfa Blobu veya dosyalardan **Depolama hizmetini** seçin. Seçilen hizmetin türü, verilerin Azure'da hangi biçimde tutulmasını istediğinize bağlıdır. Örneğin, bu örnekte, verilerin Azure 'da blok Blobları olarak bulunmasını istiyoruz, bu nedenle **Blok Blobu** seçiyoruz. **Sayfa Blobu** seçilirse, verilerinizin 512 bayt hizalı olduğundan emin olmanız gerekir. Her zaman 512 bayt hizalı VHD veya VHDX için **Sayfa Blobu** kullanın.

   > [!IMPORTANT]
   > Kullandığınız Azure depolama hesabının, bir Azure Stack Edge Pro veya Data Box Gateway cihazından yararlanarak bu sunucuda ayarlanmış bir değişiklik olmadığından emin olun. Daha fazla bilgi için bkz. [BLOB depolama için dengesde kullanılabilirlik Ilkelerini ayarlama ve yönetme](../storage/blobs/storage-blob-immutability-policies-manage.md).

6. Bu adım SMB paylaşımı mı yoksa NFS paylaşımı mı oluşturduğunuza bağlıdır.
   - **BIR SMB paylaşımıyla karşılaşırsanız** , **tüm ayrıcalık Yerel Kullanıcı** alanında **Yeni oluştur** ' u seçin veya **var olanı kullanın**. Yeni bir yerel kullanıcı oluşturuyorsanız, **Kullanıcı adı**, **parola** ve parolayı onaylayın ' i belirtin. Bu, yerel kullanıcıya izinleri atar. Burada izinleri atadıktan sonra, Dosya Gezgini'ni kullanarak bu izinlerde değişiklik yapabilirsiniz.

      ![SMB paylaşımı ekleme](media/azure-stack-edge-manage-shares/add-smb-share.png)

        Bu paylaşımın verileri için Yalnızca okuma işlemlerine izin ver'i işaretlerseniz, salt okuma kullanıcıları belirtebilirsiniz.
   - **NFS paylaşma oluşturuyorsanız** , paylaşıma erişebilen **Izin verilen istemcilerin IP adreslerini** sağlamanız gerekir.

      ![NFS paylaşımı ekleme](media/azure-stack-edge-manage-shares/add-nfs-share.png)

7. Sınır işlem modüllerindeki paylaşımlara kolayca erişmek için yerel bağlama noktasını kullanın. Paylaşımın oluşturulduktan sonra otomatik olarak bağlanması için **kenar COMPUTE ile paylaşma kullan** seçeneğini belirleyin. Bu seçenek belirlendiğinde, Edge modülü aynı zamanda yerel bağlama noktasıyla işlem kullanabilir.

8. Paylaşma oluşturmak için **Oluştur** ' a tıklayın. Paylaşım oluşturma işleminin devam ettiği size bildirilir. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** dikey penceresi yeni paylaşımı yansıtacak şekilde güncelleştirilir.

## <a name="add-a-local-share"></a>Yerel bir paylaşma ekleyin

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Komut çubuğunda **+ paylaşma Ekle** ' yi seçin.

   ![Paylaşma Ekle 2 ' yi seçin](media/azure-stack-edge-manage-shares/add-local-share-1.png)

2. **Paylaşım Ekle**'de, paylaşım ayarlarını belirtin. Paylaşımınız için benzersiz bir ad sağlayın.
    
    Paylaşım adları yalnızca rakam, küçük harf ve kısa çizgiler içerebilir. Paylaşım adı 3 ile 63 karakter arası uzunlukta olmalı ve bir harf veya rakamla başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır.

3. Paylaşma için bir **tür** seçin. Bu tür SMB veya **NFS**, SMB 'nin varsayılan olarak **olması olabilir.** SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır. SMB paylaşımları mı yoksa NFS paylaşımları mı seçtiğinize bağlı olarak, gösterilen seçenekler biraz farklı olur.

4. Sınır işlem modüllerindeki paylaşımlara kolayca erişmek için yerel bağlama noktasını kullanın. Sınır modülünün yerel bağlama noktasıyla işlem kullanabilmesi için **Edge COMPUTE ile paylaşma kullan** ' ı seçin.

5. **Sınır yerel paylaşımları olarak Yapılandır**' ı seçin. Yerel paylaşımlardaki veriler cihazda yerel olarak kalır. Bu verileri yerel olarak işleyebilirsiniz.

6. **Tüm ayrıcalık Yerel Kullanıcı** alanında **Yeni oluştur** ' u seçin veya **var olanı kullanın**.

7. **Oluştur**’u seçin. 

   ![Yerel paylaşma oluştur](media/azure-stack-edge-manage-shares/add-local-share-2.png)

    Paylaşma oluşturmanın devam ettiğini belirten bir bildirim görürsünüz. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** dikey penceresi yeni paylaşımı yansıtacak şekilde güncelleştirilir.

   ![Güncelleştirme paylaşımları dikey penceresini görüntüle](media/azure-stack-edge-manage-shares/add-local-share-3.png)
    


## <a name="mount-a-share"></a>Bir paylaşma bağlama

Azure Stack Edge Pro cihazınızda işlem yapılandırmadan önce bir paylaşma oluşturduysanız, bu paylaşıma bağlamanız gerekir. Bir paylaşma bağlamak için aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Paylaşımlar listesinden bağlamak istediğiniz paylaşımı seçin. **İşlem Için kullanılan** sütunu, seçili paylaşımın durumunu **devre dışı** olarak gösterir.

   ![Paylaşma 3 ' ü seçin](media/azure-stack-edge-manage-shares/select-share-mount.png)

2. **Bağla**' yı seçin.

   ![Bağlama seçin](media/azure-stack-edge-manage-shares/select-mount.png)

3. Onay istendiğinde **Evet**' i seçin. Bu, paylaşımdan bağlantı oluşturacak.

   ![Bağlama Onayla](media/azure-stack-edge-manage-shares/confirm-mount.png)

4. Paylaşım bağlandıktan sonra paylaşımlar listesine gidin. **İşlem Için kullanılan** sütununda, paylaşımın durumunu **etkin** olarak gösteren bir görürsünüz.

   ![Bağlı paylaşıma](media/azure-stack-edge-manage-shares/share-mounted.png)

5. Paylaşıma ait yerel bağlama noktasını görüntülemek için yeniden paylaşma ' yı seçin. Edge işlem modülü, bu yerel bağlama noktasını paylaşıma kullanır.

   ![Paylaşımın yerel bağlama noktası](media/azure-stack-edge-manage-shares/share-mountpoint.png) 

## <a name="unmount-a-share"></a>Bir paylaşımın bağlantısını kaldırma

Bir paylaşımın bağlantısını kesmek için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin.

   ![Paylaşma 4 seçin](media/azure-stack-edge-manage-shares/select-share-unmount.png)

2. Paylaşımlar listesinden, çıkarmak istediğiniz paylaşımı seçin. Çıkardık paylaşımın herhangi bir modül tarafından kullanılmadığından emin olmak istiyorsunuz. Paylaşma bir modül tarafından kullanılıyorsa, ilgili modülle ilgili sorunları görürsünüz. Çıkar ' **ı seçin.**

   ![Çıkarma seçin](media/azure-stack-edge-manage-shares/select-unmount.png)

3. Onay istendiğinde **Evet**' i seçin. Bu, paylaşımın bağlantısını çıkaracaktır.

   ![Çıkarma bağlantısını onayla](media/azure-stack-edge-manage-shares/confirm-unmount.png)

4. Paylaşım kaldırıldıktan sonra paylaşımlar listesine gidin. **İşlem sütunu Için kullanılan** ' ın, paylaşma durumunu **devre dışı** olarak gösterdiğini görürsünüz.

   ![Kaldırılan paylaşma](media/azure-stack-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Paylaşımı silme

Paylaşımı silmek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Paylaşım listesinde silmek istediğiniz paylaşımı seçin ve üzerine tıklayın.

   ![Paylaşma 5 ' i seçin](media/azure-stack-edge-manage-shares/delete-share-1.png)

2. **Sil**’i seçin.

   ![Sil ' i seçin](media/azure-stack-edge-manage-shares/delete-share-2.png)

3. Onayınız istendiğinde **Evet**’e tıklayın.

   ![Silmeyi onayla](media/azure-stack-edge-manage-shares/delete-share-3.png)

Paylaşımların listesi, silme işlemini yansıtacak şekilde güncelleştirilir.

## <a name="refresh-shares"></a>Paylaşımları yenileme

Yenile özelliği, bir paylaşımın içeriğini yenilekullanmanıza olanak sağlar. Bir paylaşımı yenilediğinizde bloblar ve dosyalar dahil olmak üzere son yenileme işleminden sonra buluta eklenmiş olan tüm Azure nesnelerini bulmak için bir arama başlatılır. Bu ek dosyalar daha sonra cihazdaki paylaşımın içeriğini yenilemek için indirilir.

> [!IMPORTANT]
>
> - Yerel paylaşımları yenileyemezsiniz.
> - İzinler ve erişim denetim listeleri (ACL 'Ler) bir yenileme işlemi boyunca korunmaz. 

Paylaşımı yenilemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Yenilemek istediğiniz paylaşımı seçin ve üzerine tıklayın.

   ![Paylaşma Seç 6](media/azure-stack-edge-manage-shares/refresh-share-1.png)

2. **Verileri Yenile**' yi seçin.

   ![Yenile 'yi seçin](media/azure-stack-edge-manage-shares/refresh-share-2.png)
 
3. Onay istendiğinde **Evet**' i seçin. Şirket içi paylaşımın içeriğinin yenilenmesi için bir iş başlatılır.

   ![Yenilemeyi onaylama](media/azure-stack-edge-manage-shares/refresh-share-3.png)

4. Yenileme işlemi devam ederken bağlam menüsündeki Yenile seçeneği gri renkte gösterilir. Yenileme işinin durumunu görüntülemek için iş bildirimine tıklayın.

5. Yenileme süresi Azure kapsayıcısındaki ve cihazdaki dosya sayısına göre değişir. Yenileme işlemi başarıyla tamamlandıktan sonra paylaşım zaman damgası güncelleştirilir. Yenilemede kısmi hatalar olsa da işlem başarılı kabul edilir ve zaman damgası güncelleştirilir. Yenileme hatası günlükleri de güncelleştirilir.

   ![Güncelleştirilmiş zaman damgası](media/azure-stack-edge-manage-shares/refresh-share-4.png)
 
Hata varsa bir uyarı görüntülenir. Uyarıda sorunun nedeni ve düzeltme adımları yer alır. Uyarıda ayrıca güncelleştirme veya silme işleminin başarısız olduğu dosyalar da dahil olmak üzere hatanın ayrıntılı bir özetinin yer aldığı bir dosyaya bağlantı da verilir.

## <a name="sync-storage-keys"></a>Depolama anahtarlarını eşitleme

Depolama anahtarlarınız değiştirildiyse eşitlemeniz gerekir. Eşitleme, cihazın depolama hesabınızın en son anahtarlarını almasına yardımcı olur.

Depolama erişim anahtarınızı eşitlemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Paylaşım listesinden eşitlemek istediğiniz depolama hesabıyla ilişkilendirilmiş olan bir paylaşımı seçin ve üzerine tıklayın.

    ![İlgili depolama hesabı ile paylaşma seçin](media/azure-stack-edge-manage-shares/sync-storage-key-1.png)

2. **Depolama anahtarını eşitle**'ye tıklayın. Onayınız istendiğinde **Evet**’e tıklayın.

     ![Eşitleme depolama anahtarını seçin](media/azure-stack-edge-manage-shares/sync-storage-key-2.png)

3. Eşitleme tamamlandıktan sonra iletişim kutusunu kapatın.

>[!NOTE]
> Bu işlemi her depolama hesabı için yalnızca bir kez yapmanız gerekir. Tüm paylaşımlar aynı depolama hesabına aitse bu eylemi tüm paylaşımlar için tekrarlamanız gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal aracılığıyla kullanıcıları yönetme](azure-stack-edge-manage-users.md) hakkında bilgi edinin.