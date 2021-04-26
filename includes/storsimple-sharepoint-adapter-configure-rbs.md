---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 45b2ac73ffcb4b777572f9ad62d8aae0e8277e1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554664"
---
> [!NOTE]
> SharePoint KÇY yapılandırması için StorSimple bağdaştırıcısında değişiklik yaparken, Domain Admins grubuna ait olan bir kullanıcı hesabıyla oturum açmış olmanız gerekir. Ayrıca, merkezi yönetim ile aynı konakta çalışan bir tarayıcıdan yapılandırma sayfasına erişmeniz gerekir.
> 
> 

#### <a name="to-configure-rbs"></a>KÇY 'yi yapılandırmak için
1. SharePoint Merkezi Yönetim sayfasını açın ve **sistem ayarları**' na gidin. 
2. **Azure StorSimple** bölümünde, **StorSimple bağdaştırıcısını Yapılandır**' a tıklayın.
   
    ![StorSimple bağdaştırıcısını yapılandırma](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. **StorSimple bağdaştırıcısını Yapılandır** sayfasında:
   
   1. **Yolu düzenlemenizi etkinleştir** onay kutusunun seçili olduğundan emin olun.
   2. Metin kutusuna BLOB deposunun evrensel adlandırma kuralı (UNC) yolunu yazın.
      
      > [!NOTE]
      > BLOB Mağazası birimi, StorSimple cihazında yapılandırılmış bir Iscsı biriminde barındırılmalıdır.

   3. Uzak depolama için yapılandırmak istediğiniz her içerik veritabanlarının altındaki **Etkinleştir** düğmesine tıklayın.
      
      > [!NOTE]
      > BLOB deposunun tüm Web ön uç (WFE) sunucuları tarafından paylaşılması ve erişilebilir olması ve SharePoint sunucu grubu için yapılandırılmış kullanıcı hesabının paylaşıma erişimi olması gerekir.
      
      ![KÇY sağlayıcısını etkinleştir](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      KÇY 'yi etkinleştirdiğinizde veya devre dışı bıraktığınızda, aşağıdaki iletiyi de görürsünüz.
      
      ![StorSimple bağdaştırıcısını Yapılandır devre dışı bırak](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Yapılandırmayı uygulamak için **Güncelleştir** düğmesine tıklayın. **Güncelleştir** düğmesine TıKLADıĞıNıZDA, KÇY yapılandırma durumu tüm WFE sunucularında güncelleştirilir ve tüm grup KÇY etkin olur. Aşağıdaki ileti görünür.
      
      ![Bağdaştırıcı yapılandırma iletisi](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > SharePoint grubu için KÇY 'yi çok fazla sayıda veritabanı (200 'den fazla) ile yapılandırıyorsanız, SharePoint Yönetim Merkezi Web sayfası zaman aşımına uğrar. Böyle bir durumla karşılaşırsanız, sayfayı yenileyin. Bu, yapılandırma işlemini etkilemez.

4. Yapılandırmayı doğrulayın:
   
   1. SharePoint Yönetim Merkezi Web sitesinde oturum açın ve **StorSimple bağdaştırıcısını Yapılandır** sayfasına gidin.
   2. Girdiğiniz ayarlarla eşleştiğinden emin olmak için yapılandırma ayrıntılarına bakın. 
5. KÇY 'nin doğru şekilde çalıştığını doğrulayın:
   
   1. Belgeyi SharePoint 'e yükleyin. 
   2. Yapılandırdığınız UNC yoluna gidin. KÇY dizin yapısının oluşturulduğundan ve karşıya yüklenen nesneyi içerdiğinden emin olun.
6. Seçim `Migrate()` Mevcut blob Içeriğini StorSimple cihazına geçirmek için, SharePoint 'e dahil edilen MICROSOFT KÇY PowerShell cmdlet 'ini kullanabilirsiniz. Daha fazla bilgi için bkz. [sharepoint 2013 'de KÇY 'ye veya dışına Içerik geçirme][6] veya [KÇY (SharePoint Foundation 2010) içine veya dışına içerik geçirme][7].
7. Seçim Test yüklemelerinde, Blobların içerik veritabanından şu şekilde taşındığını doğrulayabilirsiniz: 
   
   1. SQL Management Studio başlatın.
   2. ListBlobsInDB_2010. SQL veya ListBlobsInDB_2013. SQL sorgusunu aşağıda gösterildiği gibi çalıştırın.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      KÇY doğru şekilde yapılandırıldıysa, karşıya yüklenen ve KÇY ile başarıyla externalized bir nesne için Sizeofcontentındb sütununda NULL bir değer görünmelidir.
8. Seçim KÇY 'yi yapılandırdıktan ve tüm BLOB içeriğini StorSimple cihazına taşıdıktan sonra içerik veritabanını cihaza taşıyabilirsiniz. İçerik veritabanını taşımayı seçerseniz, cihazdaki içerik veritabanı depolama alanını birincil birim olarak yapılandırmanızı öneririz. Daha sonra, içerik veritabanını StorSimple cihazına geçirmek için kurulu SQL Server en iyi yöntemlerini kullanın. 
   
   > [!NOTE]
   > İçerik veritabanını cihaza taşımak yalnızca StorSimple 8000 serisi için desteklenir (5000 veya 7000 Serisi için desteklenmez).
   
   Blob 'Ları ve içerik veritabanını StorSimple cihazında ayrı birimlerde depoluiyorsanız, bunları aynı birim kapsayıcısında yapılandırmanızı öneririz. Bu, birlikte yedeklenmelerini sağlar.
   
   > [!WARNING]
   > KÇY 'yi etkinleştirmediyseniz, içerik veritabanını StorSimple cihazına taşımayı önermiyoruz. Bu, test edilmemiş bir yapılandırmadır.
   
9. Sonraki adıma gidin: [çöp toplamayı yapılandırma](#configure-garbage-collection).

[6]: /SharePoint/administration/migrate-content-into-or-out-of-rbs
[7]: /previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)