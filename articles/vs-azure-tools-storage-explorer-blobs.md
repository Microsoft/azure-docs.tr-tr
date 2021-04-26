---
title: Depolama Gezgini ile Azure Blob depolama kaynaklarını yönetme | Microsoft Docs
description: Depolama Gezgini ile Azure Blob depolama kaynaklarını yönetme. Blob kapsayıcısı oluşturun, blob kapsayıcı içeriklerini görüntüleyin, blob kapsayıcısını silin veya kopyalayın ve daha fazlasını yapın.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 48f00d964a87790b8d8c9b1d8eceaed26d15199e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531976"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Azure Blob Depolama kaynaklarını Depolama Gezgini'yle yönetme

## <a name="overview"></a>Genel Bakış

[Azure Blob depolama](./storage/blobs/storage-quickstart-blobs-dotnet.md) , http veya HTTPS aracılığıyla dünyanın her yerinden erişilebilen metin veya ikili veriler gibi büyük miktarda yapılandırılmamış veriyi depolamaya yönelik bir hizmettir.
Verileri genel olarak herkese açık kullanıma sunmak veya uygulama verilerini özel olarak depolamak için Blob Storage’ı kullanabilirsiniz. Bu makalede, blob kapsayıcıları ve bloblarla çalışmak için Depolama Gezgini kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için şunlar gereklidir:

* [Depolama Gezgini’ni indirip yükleme](https://www.storageexplorer.com)
* [Azure depolama hesabına veya hizmetine bağlanma](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Blob kapsayıcısı oluşturma

Tüm Bloblar yalnızca blob 'ların mantıksal gruplandırması olan bir blob kapsayıcısında yer almalıdır. Hesap sınırsız sayıda kapsayıcı içerebilir ve her kapsayıcı sınırsız sayıda blob depolayabilirler.

Aşağıdaki adımlarda Depolama Gezgini içinde bir blob kapsayıcısının nasıl oluşturulacağı gösterilmektedir.

1. Depolama Gezgini'ni açın.
2. Sol bölmede, blob kapsayıcısını oluşturmak istediğiniz depolama hesabını genişletin.
3. **BLOB kapsayıcıları**' na sağ tıklayın ve bağlam menüsünden **BLOB kapsayıcısı oluştur**' u seçin.

   ![Blob kapsayıcıları Oluştur bağlam menüsü][0]
4. **BLOB kapsayıcıları** klasörünün altında bir metin kutusu görünür. Blob kapsayıcınızın adını girin. Blob kapsayıcıları adlandırma kuralları ve kısıtlamaları hakkında bilgi için bkz. [kapsayıcı oluşturma](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .

   ![Blob kapsayıcıları oluştur metin kutusu][1]
5. Blob kapsayıcısını oluşturmak için veya iptal etmek için **ESC** **tuşuna basın** . Blob kapsayıcısı başarıyla oluşturulduktan sonra, seçili depolama hesabı için **BLOB kapsayıcıları** klasörü altında görüntülenir.

   ![Blob kapsayıcısı oluşturuldu][2]

## <a name="view-a-blob-containers-contents"></a>Blob kapsayıcısının içeriğini görüntüleme

Blob kapsayıcıları, Bloblar ve klasörler (Ayrıca blob 'lar de içerebilir) içerir.

Aşağıdaki adımlarda Depolama Gezgini içindeki bir blob kapsayıcısının içeriğini görüntüleme gösterilmektedir:

1. Depolama Gezgini'ni açın.
2. Sol bölmede, görüntülemek istediğiniz blob kapsayıcısını içeren depolama hesabını genişletin.
3. Depolama hesabının **BLOB kapsayıcıları**' nı genişletin.
4. Görüntülemek istediğiniz blob kapsayıcısını sağ tıklatın ve bağlam menüsünden, **BLOB kapsayıcı Düzenleyicisi 'Ni aç**' ı seçin.
   Ayrıca görüntülemek istediğiniz blob kapsayıcısına çift tıklayabilirsiniz.

   ![Blob kapsayıcı düzenleyici bağlam menüsünü aç][19]
5. Ana bölmede blob kapsayıcısının içeriği görüntülenir.

   ![Blob kapsayıcı Düzenleyicisi][3]

## <a name="delete-a-blob-container"></a>Blob kapsayıcısını silme

Blob kapsayıcıları, gerektiğinde kolayca oluşturulup silinebilir. (Tek tek Blobları silmeyi görmek için [bir blob kapsayıcısında Blobları yönetme](#managing-blobs-in-a-blob-container)bölümüne bakın.)

Aşağıdaki adımlarda Depolama Gezgini içindeki bir blob kapsayıcısının nasıl silineceği gösterilmektedir:

1. Depolama Gezgini'ni açın.
2. Sol bölmede, görüntülemek istediğiniz blob kapsayıcısını içeren depolama hesabını genişletin.
3. Depolama hesabının **BLOB kapsayıcıları**' nı genişletin.
4. Silmek istediğiniz blob kapsayıcısını sağ tıklatın ve bağlam menüsünden **Sil**' i seçin.
   Şu anda seçili olan blob kapsayıcısını silmek için **Sil** 'e de basabilirsiniz.

   ![Blob kapsayıcısı bağlam menüsünü Sil][4]
5. Onay iletişim kutusunda **Evet**’i seçin.

   ![Blob kapsayıcısını silme onayı][5]

## <a name="copy-a-blob-container"></a>Blob kapsayıcısını kopyalama

Depolama Gezgini, panoya bir blob kapsayıcısı kopyalamanızı ve sonra bu blob kapsayıcısını başka bir depolama hesabına yapıştırmayı sağlar. (Ayrı Blobları nasıl kopyalayaöğrenmek için, [BLOB kapsayıcısında Blobları yönetme](#managing-blobs-in-a-blob-container)bölümüne bakın.)

Aşağıdaki adımlarda bir blob kapsayıcısının bir depolama hesabından diğerine nasıl kopyalanacağı gösterilmektedir.

1. Depolama Gezgini'ni açın.
2. Sol bölmede, kopyalamak istediğiniz blob kapsayıcısını içeren depolama hesabını genişletin.
3. Depolama hesabının **BLOB kapsayıcıları**' nı genişletin.
4. Kopyalamak istediğiniz blob kapsayıcısını sağ tıklatın ve bağlam menüsünden, **BLOB kapsayıcısını Kopyala**' yı seçin.

   ![Blob kapsayıcısı bağlam menüsünü Kopyala][6]
5. Blob kapsayıcısını yapıştırmak istediğiniz istenen "hedef" depolama hesabına sağ tıklayın ve bağlam menüsünden, **BLOB kapsayıcısını Yapıştır**' ı seçin.

   ![Blob kapsayıcısı bağlam menüsünü Yapıştır][7]

## <a name="get-the-sas-for-a-blob-container"></a>Blob kapsayıcısı için SAS alma

[Paylaşılan erişim imzası (SAS)](./storage/common/storage-sas-overview.md), depolama hesabınızdaki kaynaklara temsilci erişimi sağlar.
Başka bir deyişle, hesap erişim anahtarlarınızı paylaşmak zorunda kalmadan, depolama hesabınızdaki nesnelere belirli bir süre için ve belirli bir izin kümesiyle sınırlı istemci izinleri verebilirsiniz.

Aşağıdaki adımlarda bir blob kapsayıcısı için SAS oluşturma gösterilmektedir:

1. Depolama Gezgini'ni açın.
2. Sol bölmede, SAS almak istediğiniz blob kapsayıcısını içeren depolama hesabını genişletin.
3. Depolama hesabının **BLOB kapsayıcıları**' nı genişletin.
4. İstediğiniz blob kapsayıcısına sağ tıklayın ve bağlam menüsünden **paylaşılan erişim Imzası al**' ı seçin.

   ![SAS bağlam menüsünü al][8]
5. **Paylaşılan Erişim İmzası** iletişim kutusunda ilkeyi, başlangıç ve sona erme tarihlerini, saat dilimini ve kaynak için istediğiniz erişim düzeylerini belirtin.

   ![SAS seçeneklerini al][9]
6. SAS seçeneklerini belirtmeyi tamamladığınızda **Oluştur**’u seçin.
7. İkinci bir **paylaşılan erişim imzası** iletişim kutusu daha sonra blob kapsayıcısını, depolama kaynağına erişmek IÇIN kullanabileceğiniz URL ve querystrings ile birlikte listeler.
   Panoya kopyalamak istediğiniz URL’nin yanındaki **Kopyala** öğesini seçin.

   ![SAS URL 'Lerini Kopyala][10]
8. İşiniz bittiğinde **Kapat**’ı seçin.

## <a name="manage-access-policies-for-a-blob-container"></a>Blob kapsayıcısı için erişim Ilkelerini yönetme

Aşağıdaki adımlarda bir blob kapsayıcısı için erişim ilkelerini yönetme (ekleme ve kaldırma) gösterilmektedir:

1. Depolama Gezgini'ni açın.
2. Sol bölmede, erişim ilkelerini yönetmek istediğiniz blob kapsayıcısını içeren depolama hesabını genişletin.
3. Depolama hesabının **BLOB kapsayıcıları**' nı genişletin.
4. İstediğiniz blob kapsayıcısını seçin ve bağlam menüsünden **erişim Ilkelerini Yönet**' i seçin.

   ![Erişim ilkelerini yönet bağlam menüsü][11]
5. **Erişim ilkeleri** iletişim kutusu seçili blob kapsayıcısı için önceden oluşturulmuş olan tüm erişim ilkelerini listeler.

   ![Erişim Ilkesi seçenekleri][12]
6. Erişim ilkesi yönetim görevine bağlı olarak aşağıdaki adımları izleyin:

   * **Yeni bir erişim ilkesi ekleme** - **Ekle**’yi seçin. Oluşturulduktan sonra, **Erişim İlkeleri** iletişim kutusunda yeni eklenen erişim ilkesi (varsayılan ayarlarla birlikte) gösterilir.
   * **Erişim Ilkesini düzenleyin** -istediğiniz düzenlemeleri yapın ve **Kaydet**' i seçin.
   * **Erişim ilkesini kaldırma** - Kaldırmak istediğiniz erişim ilkesinin yanındaki **Kaldır** öğesini seçin.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Blob kapsayıcısı için genel erişim düzeyini ayarlama

Varsayılan olarak, her blob kapsayıcısı "genel erişim yok" olarak ayarlanır.

Aşağıdaki adımlarda, bir blob kapsayıcısı için genel erişim düzeyinin nasıl ayarlanacağı gösterilmektedir.

1. Depolama Gezgini'ni açın.
2. Sol bölmede, erişim ilkelerini yönetmek istediğiniz blob kapsayıcısını içeren depolama hesabını genişletin.
3. Depolama hesabının **BLOB kapsayıcıları**' nı genişletin.
4. İstediğiniz blob kapsayıcısını seçin ve bağlam menüsünden **genel erişim düzeyini ayarla**' yı seçin.

   ![Genel erişim düzeyi bağlam menüsünü ayarla][13]
5. **Kapsayıcı genel erişim düzeyini ayarla** iletişim kutusunda istenen erişim düzeyini belirtin.

   ![Genel erişim düzeyi seçeneklerini ayarla][14]
6. **Uygula**’yı seçin.

## <a name="managing-blobs-in-a-blob-container"></a>Blob kapsayıcısında blob 'ları yönetme

Blob kapsayıcısını oluşturduktan sonra bu blob kapsayıcısına bir blob yükleyebilir, yerel bilgisayarınıza bir blob indirebilir, yerel bilgisayarınızda bir blob açabilir ve çok daha fazlasını yapabilirsiniz.

Aşağıdaki adımlarda blob kapsayıcısı içindeki Blobların (ve klasörlerinin) nasıl yönetileceği gösterilmektedir.

1. Depolama Gezgini'ni açın.
2. Sol bölmede, yönetmek istediğiniz blob kapsayıcısını içeren depolama hesabını genişletin.
3. Depolama hesabının **BLOB kapsayıcıları**' nı genişletin.
4. Görüntülemek istediğiniz blob kapsayıcısına çift tıklayın.
5. Ana bölmede blob kapsayıcısının içeriği görüntülenir.

   ![Blob kapsayıcısını görüntüle][3]
6. Ana bölmede blob kapsayıcısının içeriği görüntülenir.
7. Gerçekleştirmek istediğiniz göreve bağlı olarak aşağıdaki adımları izleyin:

   * **Blob kapsayıcısına dosya yükleme**

     1. Ana bölmedeki araç çubuğunda **karşıya yükle**' yi ve ardından açılan menüden **dosyaları karşıya yükle** ' yi seçin.

        ![Dosyaları karşıya yükleme menüsü][15]
     2. **Dosyaları Karşıya Yükle** iletişim kutusunda, **Dosyalar** metin kutusunun sağ tarafındaki üç noktayı (**…**) seçerek karşıya yüklemek istediğiniz dosyaları belirleyin.

        ![Karşıya dosya yükleme seçenekleri][16]
     3. **BLOB türünün** türünü belirtin. Daha fazla bilgi için bkz. [kapsayıcı oluşturma](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. İsteğe bağlı olarak, seçili dosyaların karşıya yükleneceği bir hedef klasör belirtin. Hedef klasör mevcut değilse, oluşturulur.
     5. **Karşıya Yükle**’yi seçin.
   * **Blob kapsayıcısına bir klasör yükleme**

     1. Ana bölmedeki araç çubuğunda **karşıya yükle**' yi ve ardından açılan menüden **klasörü karşıya yükle** ' yi seçin.

        ![Klasörü karşıya yükle menüsü][17]
     2. **Klasörü karşıya yükle** iletişim kutusunda, **Klasör** metin kutusunun sağ tarafındaki üç noktayı (**…**) seçerek içeriklerini karşıya yüklemek istediğiniz klasörü belirleyin.

        ![Klasör yükleme seçenekleri][18]
     3. **BLOB türünün** türünü belirtin. Daha fazla bilgi için bkz. [kapsayıcı oluşturma](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. İsteğe bağlı olarak, seçili klasörün içeriklerinin yükleneceği bir hedef klasör belirtin. Hedef klasör mevcut değilse, oluşturulur.
     5. **Karşıya Yükle**’yi seçin.
   * **Yerel bilgisayarınıza blob indirme**

     1. İndirmek istediğiniz blobu seçin.
     2. Ana bölmedeki araç çubuğunda **İndir**’i seçin.
     3. **İndirilen blob 'un kaydedileceği yeri belirtin** iletişim kutusunda, blob 'un indirilmesini istediğiniz konumu ve vermek istediğiniz adı belirtin.  
     4. **Kaydet**’i seçin.
   * **Yerel bilgisayarınızda bir blob açın**

     1. Açmak istediğiniz blobu seçin.
     2. Ana bölmedeki araç çubuğunda **Aç**’ı seçin.
     3. Blob, blob 'un temel alınan dosya türü ile ilişkili uygulama kullanılarak indirilir ve açılır.
   * **Bir blobu panoya kopyalama**

     1. Kopyalamak istediğiniz blobu seçin.
     2. Ana bölmedeki araç çubuğunda **Kopyala**’yı seçin.
     3. Sol bölmede başka bir blob kapsayıcısına gidin ve ana bölmede görüntülemek için çift tıklayın.
     4. Ana bölmedeki araç çubuğunda, Blobun bir kopyasını oluşturmak için **Yapıştır** ' ı seçin.
   * **Blobu silme**

     1. Silmek istediğiniz blobu seçin.
     2. Ana bölmedeki araç çubuğunda **Sil**’i seçin.
     3. Onay iletişim kutusunda **Evet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [En son Depolama Gezgini yayın notlarını ve videolarını](https://www.storageexplorer.com) görüntüleyin.
* [Azure bloblarını, tablolarını, kuyruklarını ve dosyalarını kullanarak uygulama oluşturma](https://azure.microsoft.com/documentation/services/storage/) hakkında bilgi edinin.

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png