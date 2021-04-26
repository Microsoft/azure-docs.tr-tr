---
title: Azure Stack Edge Pro GPU paylaşma yönetimi | Microsoft Docs
description: Azure Stack Edge Pro GPU 'unuzda paylaşımları yönetmek için Azure portal nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b79cb1b195d35c1e25dd750476c0dacb296f8010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638824"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'unuzdaki paylaşımları yönetmek için Azure portal kullanma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede Azure Stack Edge Pro 'daki paylaşımların nasıl yönetileceği açıklanır. Azure Stack Edge Pro 'Yu Azure portal veya yerel Web Kullanıcı arabirimi aracılığıyla yönetebilirsiniz. Paylaşımlar ile ilişkili depolama hesabı için, paylaşımları eklemek, silmek, yenilemek veya eşitleme depolama anahtarını kullanmak için Azure portal kullanın. Bu makale Azure Stack Edge Pro GPU, Azure Stack Edge Pro R ve Azure Stack Edge Mini R cihazları için geçerlidir.

## <a name="about-shares"></a>Paylaşımlar hakkında

Azure 'a veri aktarmak için Azure Stack Edge Pro 'unuzda paylaşımlar oluşturmanız gerekir. Azure Stack Edge Pro cihazına eklediğiniz paylaşımlar yerel paylaşımlar olabilir veya verileri buluta ititeden paylaşabilirsiniz.

 - **Yerel paylaşımlar**: verilerin cihazda yerel olarak işlenmesini istiyorsanız bu paylaşımları kullanın.
 - **Paylaşımlar**: cihaz verilerinin buluttaki depolama hesabınıza otomatik olarak iletilmesini istediğinizde bu paylaşımları kullanın. **Yenileme** ve **eşitleme depolama anahtarları** gibi tüm bulut işlevleri paylaşımlar için geçerlidir.


## <a name="add-a-share"></a>Paylaşım ekleme

Paylaşım oluşturmak için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Komut çubuğunda **+ paylaşma Ekle** ' yi seçin.

    ![Paylaşma Ekle seçeneğini belirleyin](media/azure-stack-edge-gpu-manage-shares/add-share-1.png)

2. **Paylaşım Ekle**'de, paylaşım ayarlarını belirtin. Paylaşımınız için benzersiz bir ad sağlayın.
    
    Paylaşım adları yalnızca rakam, küçük harf ve kısa çizgiler içerebilir. Paylaşım adı 3 ile 63 karakter arası uzunlukta olmalı ve bir harf veya rakamla başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır.

3. Paylaşma için bir **tür** seçin. Bu tür SMB veya **NFS**, SMB 'nin varsayılan olarak **olması olabilir.** SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır. SMB paylaşımları mı yoksa NFS paylaşımları mı seçtiğinize bağlı olarak, gösterilen seçenekler biraz farklı olur.

4. Paylaşımın duracağı **Depolama hesabını** sağlamanız gerekir. Henüz kapsayıcı yoksa, depolama hesabında paylaşım adıyla bir kapsayıcı oluşturulur. Kapsayıcı zaten varsa, bu var olan kapsayıcı kullanılır.

5. Açılan listeden, Blok Blobu, Sayfa Blobu veya dosyalardan **Depolama hizmetini** seçin. Seçilen hizmetin türü, verilerin Azure'da hangi biçimde tutulmasını istediğinize bağlıdır. Örneğin, bu örnekte, verilerin Azure 'da blok Blobları olarak bulunmasını istiyoruz, bu nedenle **Blok Blobu** seçiyoruz. **Sayfa Blobu** seçilirse, verilerinizin 512 bayt hizalı olduğundan emin olmanız gerekir. Her zaman 512 bayt hizalı VHD veya VHDX için **Sayfa Blobu** kullanın.

6. Bu adım SMB paylaşımı mı yoksa NFS paylaşımı mı oluşturduğunuza bağlıdır.
    - **BIR SMB paylaşımıyla karşılaşırsanız** , **tüm ayrıcalık Yerel Kullanıcı** alanında **Yeni oluştur** ' u seçin veya **var olanı kullanın**. Yeni bir yerel kullanıcı oluşturuyorsanız, **Kullanıcı adı**, **parola** ve parolayı onaylayın ' i belirtin. Bu, yerel kullanıcıya izinleri atar. Burada izinleri atadıktan sonra, Dosya Gezgini'ni kullanarak bu izinlerde değişiklik yapabilirsiniz.

        ![SMB paylaşımı ekleme](media/azure-stack-edge-gpu-manage-shares/add-smb-share.png)

        Bu paylaşımın verileri için Yalnızca okuma işlemlerine izin ver'i işaretlerseniz, salt okuma kullanıcıları belirtebilirsiniz.
    - **NFS paylaşma oluşturuyorsanız** , paylaşıma erişebilen **Izin verilen istemcilerin IP adreslerini** sağlamanız gerekir.

        ![NFS paylaşımı ekleme](media/azure-stack-edge-gpu-manage-shares/add-nfs-share.png)

7. Sınır işlem modüllerindeki paylaşımlara kolayca erişmek için yerel bağlama noktasını kullanın. Paylaşımın oluşturulduktan sonra otomatik olarak bağlanması için **kenar COMPUTE ile paylaşma kullan** seçeneğini belirleyin. Bu seçenek belirlendiğinde, Edge modülü aynı zamanda yerel bağlama noktasıyla işlem kullanabilir.

8. Paylaşma oluşturmak için **Oluştur** ' a tıklayın. Paylaşım oluşturma işleminin devam ettiği size bildirilir. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** dikey penceresi yeni paylaşımı yansıtacak şekilde güncelleştirilir.

## <a name="add-a-local-share"></a>Yerel bir paylaşma ekleyin

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Komut çubuğunda **+ paylaşma Ekle** ' yi seçin.

    ![Paylaşma Ekle 2 ' yi seçin](media/azure-stack-edge-gpu-manage-shares/add-local-share-1.png)

2. **Paylaşım Ekle**'de, paylaşım ayarlarını belirtin. Paylaşımınız için benzersiz bir ad sağlayın.
    
    Paylaşma adları yalnızca sayı, küçük harf, büyük harf ve kısa çizgi içerebilir. Paylaşım adı 3 ile 63 karakter arası uzunlukta olmalı ve bir harf veya rakamla başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır.

3. Paylaşma için bir **tür** seçin. Bu tür SMB veya **NFS**, SMB 'nin varsayılan olarak **olması olabilir.** SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır. SMB paylaşımları mı yoksa NFS paylaşımları mı seçtiğinize bağlı olarak, gösterilen seçenekler biraz farklı olur.

   > [!IMPORTANT]
   > Kullandığınız Azure depolama hesabının, bir Azure Stack Edge Pro veya Data Box Gateway cihazından yararlanarak bu sunucuda ayarlanmış bir değişiklik olmadığından emin olun. Daha fazla bilgi için bkz. [BLOB depolama için dengesde kullanılabilirlik Ilkelerini ayarlama ve yönetme](../storage/blobs/storage-blob-immutability-policies-manage.md).

4. Sınır işlem modüllerindeki paylaşımlara kolayca erişmek için yerel bağlama noktasını kullanın. Sınır modülünün yerel bağlama noktasıyla işlem kullanabilmesi için **Edge COMPUTE ile paylaşma kullan** ' ı seçin.

5. **Sınır yerel paylaşımları olarak Yapılandır**' ı seçin. Yerel paylaşımlardaki veriler cihazda yerel olarak kalır. Bu verileri yerel olarak işleyebilirsiniz.

6. **Tüm ayrıcalık Yerel Kullanıcı** alanında **Yeni oluştur** ' u seçin veya **var olanı kullanın**.

7. **Oluştur**’u seçin. 

    ![Yerel paylaşma oluştur](media/azure-stack-edge-gpu-manage-shares/add-local-share-2.png)

    Paylaşma oluşturmanın devam ettiğini belirten bir bildirim görürsünüz. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** dikey penceresi yeni paylaşımı yansıtacak şekilde güncelleştirilir.

    ![Güncelleştirme paylaşımları dikey penceresini görüntüle](media/azure-stack-edge-gpu-manage-shares/add-local-share-3.png)
    
    Bu paylaşımın uç işlem modülleri için yerel bağlama noktasını görüntülemek üzere paylaşıma seçin.

    ![Yerel paylaşma ayrıntılarını görüntüle](media/azure-stack-edge-gpu-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Bir paylaşma bağlama

Azure Stack Edge Pro cihazınızda işlem yapılandırmadan önce bir paylaşma oluşturduysanız, bu paylaşıma bağlamanız gerekir. Bir paylaşma bağlamak için aşağıdaki adımları uygulayın.


1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Paylaşımlar listesinden bağlamak istediğiniz paylaşımı seçin. **İşlem Için kullanılan** sütunu, seçili paylaşımın durumunu **devre dışı** olarak gösterir.

    ![Paylaşımı seçme](media/azure-stack-edge-gpu-manage-shares/mount-share-1.png)

2. **Bağla**' yı seçin.

    ![Bağlama seçin](media/azure-stack-edge-gpu-manage-shares/mount-share-2.png)

3. Onay istendiğinde **Evet**' i seçin. Bu, paylaşımdan bağlantı oluşturacak.

    ![Bağlama Onayla](media/azure-stack-edge-gpu-manage-shares/mount-share-3.png)

4. Paylaşım bağlandıktan sonra paylaşımlar listesine gidin. **İşlem Için kullanılan** sütununda, paylaşımın durumunu **etkin** olarak gösteren bir görürsünüz.

    ![Bağlı paylaşıma](media/azure-stack-edge-gpu-manage-shares/mount-share-4.png)

5. Paylaşıma ait yerel bağlama noktasını görüntülemek için yeniden paylaşma ' yı seçin. Edge işlem modülü, bu yerel bağlama noktasını paylaşıma kullanır.

    ![Paylaşımın yerel bağlama noktası](media/azure-stack-edge-gpu-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Bir paylaşımın bağlantısını kaldırma

Bir paylaşımın bağlantısını kesmek için Azure portal aşağıdaki adımları uygulayın.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **bulut depolama ağ geçidi > paylaşımları**' na gidin. Paylaşımlar listesinden, çıkarmak istediğiniz paylaşımı seçin. Çıkardık paylaşımın herhangi bir modül tarafından kullanılmadığından emin olmak istiyorsunuz. Paylaşma bir modül tarafından kullanılıyorsa, ilgili modülle ilgili sorunları görürsünüz.

    ![Paylaşma 2 ' yi seçin](media/azure-stack-edge-gpu-manage-shares/unmount-share-1.png)

2.  Çıkar ' **ı seçin.**

    ![Çıkarma seçin](media/azure-stack-edge-gpu-manage-shares/unmount-share-2.png)

3. Onay istendiğinde **Evet**' i seçin. Bu, paylaşımın bağlantısını çıkaracaktır.

    ![Çıkarma bağlantısını onayla](media/azure-stack-edge-gpu-manage-shares/unmount-share-3.png)

4. Paylaşım kaldırıldıktan sonra paylaşımlar listesine gidin. **İşlem sütunu Için kullanılan** ' ın, paylaşma durumunu **devre dışı** olarak gösterdiğini görürsünüz.

    ![Kaldırılan paylaşma](media/azure-stack-edge-gpu-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Paylaşımı silme

Paylaşımı silmek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Paylaşım listesinde silmek istediğiniz paylaşımı seçin ve üzerine tıklayın.

    ![Paylaşma 3 ' ü seçin](media/azure-stack-edge-gpu-manage-shares/delete-share-1.png)

2. **Sil**'e tıklayın.

    ![Sil'e tıklayın](media/azure-stack-edge-gpu-manage-shares/delete-share-2.png)

3. Onayınız istendiğinde **Evet**’e tıklayın.

    ![Silmeyi onayla](media/azure-stack-edge-gpu-manage-shares/delete-share-3.png)

Paylaşımların listesi, silme işlemini yansıtacak şekilde güncelleştirilir.

## <a name="refresh-shares"></a>Paylaşımları yenileme

Yenile özelliği, bir paylaşımın içeriğini yenilekullanmanıza olanak sağlar. Bir paylaşımı yenilediğinizde bloblar ve dosyalar dahil olmak üzere son yenileme işleminden sonra buluta eklenmiş olan tüm Azure nesnelerini bulmak için bir arama başlatılır. Bu ek dosyalar daha sonra cihazdaki paylaşımın içeriğini yenilemek için indirilir.

> [!IMPORTANT]
> - Yerel paylaşımları yenileyemezsiniz.
> - İzinler ve erişim denetim listeleri (ACL 'Ler) bir yenileme işlemi boyunca korunmaz. 

Paylaşımı yenilemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1.  Azure portalda **Paylaşımlar** sayfasına gidin. Yenilemek istediğiniz paylaşımı seçin ve üzerine tıklayın.

    ![Paylaşma 4 seçin](media/azure-stack-edge-gpu-manage-shares/refresh-share-1.png)

2.  **Yenile**'ye tıklayın. 

    ![Yenile'ye tıklayın](media/azure-stack-edge-gpu-manage-shares/refresh-share-2.png)
 
3.  Onayınız istendiğinde **Evet**’e tıklayın. Şirket içi paylaşımın içeriğinin yenilenmesi için bir iş başlatılır.

    ![Yenilemeyi onaylama](media/azure-stack-edge-gpu-manage-shares/refresh-share-3.png)
 
4.   Yenileme işlemi devam ederken bağlam menüsündeki Yenile seçeneği gri renkte gösterilir. Yenileme işinin durumunu görüntülemek için iş bildirimine tıklayın.

5.   Yenileme süresi Azure kapsayıcısındaki ve cihazdaki dosya sayısına göre değişir. Yenileme işlemi başarıyla tamamlandıktan sonra paylaşım zaman damgası güncelleştirilir. Yenilemede kısmi hatalar olsa da işlem başarılı kabul edilir ve zaman damgası güncelleştirilir. Yenileme hatası günlükleri de güncelleştirilir.

![Güncelleştirilmiş zaman damgası](media/azure-stack-edge-gpu-manage-shares/refresh-share-4.png)
 
Hata varsa bir uyarı görüntülenir. Uyarıda sorunun nedeni ve düzeltme adımları yer alır. Uyarıda ayrıca güncelleştirme veya silme işleminin başarısız olduğu dosyalar da dahil olmak üzere hatanın ayrıntılı bir özetinin yer aldığı bir dosyaya bağlantı da verilir.

## <a name="sync-pinned-files"></a>Sabitlenen dosyaları eşitle 

Sabitlenmiş dosyaları otomatik olarak eşitlemek için Azure portal aşağıdaki adımları uygulayın:
 
1. Mevcut bir Azure depolama hesabı seçin. 

2. **Kapsayıcılar** ' a gidin ve bir kapsayıcı oluşturmak Için **+ kapsayıcı** ' yı seçin. Bu kapsayıcıyı *newcontainer* olarak adlandırın. **Ortak erişim düzeyini** kapsayıcı olarak ayarlayın.

    ![Sabitlenmiş dosyalar için otomatik eşitleme 1](media/azure-stack-edge-gpu-manage-shares/image-1.png)

3. Kapsayıcı adını seçin ve aşağıdaki meta verileri ayarlayın:  

    - Name = "sabitlendi" 
    - Değer = "true" 

    ![Sabitlenen dosyalar için otomatik eşitleme 2](media/azure-stack-edge-gpu-manage-shares/image-2.png)
 
4. Cihazınızda yeni bir paylaşma oluşturun. Mevcut kapsayıcı seçeneğini belirleyerek onu sabitlenmiş kapsayıcıya eşleyin. Paylaşımdan salt okuma olarak işaretleyin. Yeni bir kullanıcı oluşturun ve bu paylaşıma ait Kullanıcı adını ve karşılık gelen parolayı belirtin.  

    ![Sabitlenmiş dosyalar için otomatik eşitleme 3](media/azure-stack-edge-gpu-manage-shares/image-3.png)
 
5. Azure portal, oluşturduğunuz kapsayıcıya gidin. Meta verilerin sabitlenmiş olarak ayarlandığı newcontainer öğesine sabitlendiği dosyayı karşıya yükleyin. 

6. Bu Azure depolama kapsayıcısının sabitleme ilkesini indirmek üzere cihaz için Azure portal **verileri Yenile** ' yi seçin.  

    ![Sabitlenmiş dosyalar için otomatik eşitleme 4](media/azure-stack-edge-gpu-manage-shares/image-4.png)
 
7. Cihazda oluşturulan yeni paylaşıma erişin. Depolama hesabına yüklenen dosya artık yerel paylaşıma indirilir. 

    Cihazın bağlantısı kesildiğinde veya yeniden bağlandığında, yenileme tetikler. Yenile, yalnızca değişmiş olan dosyaları getirir. 


## <a name="sync-storage-keys"></a>Depolama anahtarlarını eşitleme

Depolama anahtarlarınız değiştirildiyse eşitlemeniz gerekir. Eşitleme, cihazın depolama hesabınızın en son anahtarlarını almasına yardımcı olur.

Depolama erişim anahtarınızı eşitlemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Kaynağınızın **Genel bakış** sayfasına gidin. Paylaşım listesinden eşitlemek istediğiniz depolama hesabıyla ilişkilendirilmiş olan bir paylaşımı seçin ve üzerine tıklayın.

    ![İlgili depolama hesabı ile paylaşma seçin](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-1.png)

2. **Depolama anahtarını eşitle**'ye tıklayın. Onayınız istendiğinde **Evet**’e tıklayın.

     ![Eşitleme depolama anahtarını seçin](media/azure-stack-edge-gpu-manage-shares/sync-storage-key-2.png)

3. Eşitleme tamamlandıktan sonra iletişim kutusunu kapatın.

>[!NOTE]
> Bu işlemi her depolama hesabı için yalnızca bir kez yapmanız gerekir. Tüm paylaşımlar aynı depolama hesabına aitse bu eylemi tüm paylaşımlar için tekrarlamanız gerekmez.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal aracılığıyla kullanıcıları yönetme](azure-stack-edge-gpu-manage-users.md) hakkında bilgi edinin.