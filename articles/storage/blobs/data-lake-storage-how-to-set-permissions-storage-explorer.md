---
title: Azure Depolama Gezgini Data Lake Storage 2. için izinleri ayarlama
description: Bu şekilde, Azure Data Lake Storage 2. özellikli depolama hesabınızın içindeki dosyalar ve dizinler üzerinde Azure Depolama Gezgini izinleri ayarlamayı öğreneceksiniz.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 2cc93ad9ae1d9d6d21064a2a80c3d62bf661dabc
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992289"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ile Azure Depolama Gezgini kullanarak dosya ve Dizin düzeyi izinleri ayarlama

Azure Data Lake Storage 2. depolanan dosyalar ayrıntılı izinleri ve erişim denetimi listesi (ACL) yönetimini destekler. Ayrıntılı izinler ve ACL Yönetimi birlikte, verilerinize erişimi çok ayrıntılı bir düzeyde yönetmenizi sağlar.

Bu makalede Azure Depolama Gezgini için kullanmayı öğreneceksiniz:

> [!div class="checklist"]
> * Dosya düzeyi izinlerini ayarla
> * Dizin düzeyindeki izinleri ayarla
> * Erişim denetim listesine kullanıcı veya grup ekleme

## <a name="prerequisites"></a>Önkoşullar

İşlemin en iyi şekilde belirtilmesini sağlamak için [Azure Depolama Gezgini hızlı](data-lake-storage-Explorer.md)başlangıçmızı tamamlamanızı istiyoruz. Bu, depolama hesabınızın en uygun durumda (kapsayıcı oluşturulmuş ve karşıya yüklenen veriler) yer almasını sağlar.

## <a name="managing-access"></a>Erişimi yönetme

Kapsayıcının kökündeki izinleri ayarlayabilirsiniz. Bunu yapmak için, tek yapmanız gereken haklara sahip Azure Depolama Gezgini oturum açmanız gerekir (bağlantı dizesi ile aksine). Kapsayıcıya sağ tıklayın ve Izinleri Yönet iletişim kutusunu getirerek **Izinleri Yönet**' i seçin.

![Microsoft Azure Depolama Gezgini-dizin erişimini yönetme](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

**Izni Yönet** iletişim kutusu, sahip ve sahipler grubu için izinleri yönetmenizi sağlar. Ayrıca, izinleri yönetebilmeniz için erişim denetim listesine yeni kullanıcılar ve gruplar eklemenize olanak tanır.

Erişim denetim listesine yeni bir kullanıcı veya grup eklemek için **Kullanıcı veya Grup Ekle** alanını seçin.

Listeye eklemek istediğiniz karşılık gelen Azure Active Directory (AAD) girişini girin ve **Ekle**' yi seçin.

Kullanıcı veya grup artık **Kullanıcılar ve gruplar:** alanında görünür ve bu da izinlerini yönetmeye başlamanızı sağlar.

> [!NOTE]
> AAD 'de bir güvenlik grubu oluşturmak ve tek kullanıcılar yerine grupta izinleri sürdürmek için en iyi uygulamadır ve önerilir. Bu öneriye ilişkin ayrıntılar ve diğer en iyi uygulamalar için bkz. [Data Lake Storage 2. için en iyi uygulamalar](data-lake-storage-best-practices.md).

Atayabileceğiniz iki izin kategorisi vardır: erişim ACL 'Leri ve varsayılan ACL 'Ler.

* **Erişim**: ACL denetim erişimi bir nesneye erişin. Dosya ve dizinlerin her ikisi de erişim ACL 'Lerine sahiptir.

* **Varsayılan**: Bu dizin altında oluşturulan tüm alt öğelere ilişkin erişim ACL 'Lerini belirleyen bir dizinle ilişkili ACL 'lerin bir şablonu. Dosyaların varsayılan ACL 'Leri yok.

Bu kategorilerin her ikisinde de, dosyalara veya dizinlere atayabileceğiniz üç izin vardır: **Okuma**, **yazma**ve **yürütme**.

>[!NOTE]
> Burada seçimler yapıldığında, dizin içindeki mevcut olan herhangi bir öğe için izinler ayarlanmamaktadır. Dosya zaten varsa, her bir öğeye gitmeniz ve izinleri el ile ayarlamanız gerekir.

Tek tek dizinlerde ve tek tek dosyalar üzerinde izinleri yönetebilir, bu da size daha iyi erişim denetimi sağlar. Her iki dizin ve dosya için izinleri yönetme işlemi yukarıda açıklanan şekilde aynıdır. İzinlerini yönetmek istediğiniz dosya veya dizine sağ tıklayın ve aynı süreci izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır, **Azure Depolama Gezgini**kullanarak dosya ve dizinlerde izinlerin nasıl ayarlanacağını öğrendiniz. Varsayılan ACL 'ler, erişim ACL 'Leri, davranışları ve bunlara karşılık gelen izinler dahil olmak üzere ACL 'Ler hakkında daha fazla bilgi edinmek için konu üzerinde kavramsal makalemize devam edin.

> [!div class="nextstepaction"]
> [Azure Data Lake Storage 2. Nesil'de Erişim Denetimi](data-lake-storage-access-control.md)
