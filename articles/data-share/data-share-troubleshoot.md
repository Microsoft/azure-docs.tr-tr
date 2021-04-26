---
title: Azure Veri Paylaşımı sorunlarını giderme
description: Azure veri paylaşımında veri paylaşımları oluştururken veya alırken davetiye ve hatalarla ilgili sorunları nasıl giderebileceğinizi öğrenin.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964516"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Azure veri paylaşımında sık karşılaşılan sorunları giderme 

Bu makalede, Azure veri paylaşımında yaygın sorunların nasıl giderileceği açıklanmaktadır. 

## <a name="azure-data-share-invitations"></a>Azure Data Share davetleri 

Bazı durumlarda, yeni kullanıcılar bir e-posta davetinde **daveti kabul et** ' i seçerken boş bir davetiye listesi görebilirler. 

:::image type="content" source="media/no-invites.png" alt-text="Boş davetiye listesini gösteren ekran görüntüsü.":::

Bu sorunun nedeni aşağıdakilerden biri olabilir:

* **Azure veri paylaşma hizmeti, Azure kiracısında herhangi bir Azure aboneliğinin kaynak sağlayıcısı olarak kayıtlı değildir.** Bu sorun, Azure kiracınızda veri paylaşma kaynağı olmadığında meydana gelir. 

    Azure Veri Paylaşımı kaynağı oluşturduğunuzda, bu kaynak Azure aboneliğinizde kaynak sağlayıcısını otomatik olarak kaydeder. Veri paylaşma hizmetini aşağıdaki adımları kullanarak el ile kaydedebilirsiniz. Bu adımları tamamlayabilmeniz için, Azure aboneliği için [katkıda bulunan rolüne](../role-based-access-control/built-in-roles.md#contributor) ihtiyacınız vardır. 

    1. Azure portalında **Abonelikler** sayfasına gidin.
    1. Azure veri paylaşma kaynağını oluşturmak için kullanmak istediğiniz aboneliği seçin.
    1. **Kaynak sağlayıcıları**' nı seçin.
    1. **Microsoft. DataShare** için arama yapın.
    1. **Kaydet**’i seçin.

* **Davet, Azure oturum açma e-posta adresiniz yerine e-posta diğer adınızla gönderilir.** Azure veri paylaşma hizmetini zaten kaydettiniz veya Azure kiracısında bir veri paylaşma kaynağı oluşturduysanız, ancak yine de daveti göremiyorsanız, e-posta diğer adınız alıcı olarak listelenmiş olabilir. Veri sağlayıcınızla iletişim kurun ve davetin e-posta diğer adınızla değil, Azure oturum açma e-posta adresinize gönderilmesini sağlayın.

* **Davet zaten kabul edildi.** E-postadaki bağlantı sizi Azure portal **veri paylaşma** sayfasına götürür. Bu sayfa yalnızca bekleyen davetleri listeler. Kabul edilen davetler sayfada görünmüyor. Alınan paylaşımları görüntülemek ve hedef Azure Veri Gezgini kümesi ayarınızı yapılandırmak için, daveti kabul etmek için kullandığınız veri paylaşımı kaynağına gidin.

## <a name="creating-and-receiving-shares"></a>Paylaşımlar oluşturma ve alma

Yeni bir paylaşma oluşturduğunuzda, veri kümeleri eklediğinizde veya veri kümelerini eşlediğinizde aşağıdaki hatalar görünebilir:

* Veri kümeleri eklenemedi.
* Veri kümeleri eşleştirilemedi.
* Veri paylaşma kaynağı x erişimi y 'ye verilemedi.
* X için uygun izinleriniz yok.
* Seçtiğiniz kaynaklardan bir veya daha fazlasına Azure veri paylaşma hesabı için yazma izinleri ekleyemedik.

Azure veri deposu için yeterli izniniz yoksa, bu hatalardan birini görebilirsiniz. Daha fazla bilgi için bkz. [Roller ve gereksinimler](concepts-roles-permissions.md). 

Azure veri deposundan verileri paylaşmak veya almak için yazma izninizin olması gerekir. Bu izin, genellikle katkıda bulunan rolünün bir parçasıdır. 

Verileri paylaşıyorsunuz veya Azure veri deposundan ilk kez veri alıyorsanız, *Microsoft. Authorization/role atama/yazma* iznine de ihtiyacınız vardır. Bu izin genellikle sahip rolünün bir parçasıdır. Azure veri deposu kaynağını oluşturmuş olsanız bile, kaynağın sahibi olmanız gerekmez. 

Uygun izinleriniz varsa, Azure Data Share hizmeti otomatik olarak veri paylaşımının kaynağı tarafından yönetilen kimliğin veri deposuna erişmesine izin verir. Bu işlem birkaç dakika sürebilir. Bu gecikme nedeniyle hata yaşarsanız birkaç dakika sonra yeniden deneyin.

SQL tabanlı paylaşım için ek izinler gerekir. Önkoşullar hakkında bilgi için bkz. [SQL kaynaklarından paylaşma](how-to-share-from-sql.md).

## <a name="snapshots"></a>Anlık Görüntüler
Bir anlık görüntü çeşitli nedenlerle başarısız olabilir. Anlık görüntünün başlangıç saatini ve sonra her bir veri kümesinin durumunu seçerek ayrıntılı bir hata iletisi açın. 

Anlık görüntüler genellikle şu nedenlerden dolayı başarısız olur:

* Veri paylaşımında kaynak veri deposundan okuma veya hedef veri deposuna yazma izni yok. Daha fazla bilgi için bkz. [Roller ve gereksinimler](concepts-roles-permissions.md). İlk kez bir anlık görüntü aktarıyorsanız, veri paylaşımının kaynağı Azure veri deposuna erişmek için birkaç dakika gerekebilir. Birkaç dakika sonra yeniden deneyin.
* Kaynak veri deposu veya hedef veri deposu ile veri paylaşma bağlantısı, bir güvenlik duvarı tarafından engelleniyor.
* Paylaşılan bir veri kümesi, kaynak veri deposu veya hedef veri deposu silindi.

Depolama hesapları için anlık görüntü çalışırken bir dosya kaynak üzerinde güncelleştirildiğinden bir anlık görüntü başarısız olabilir. Sonuç olarak, hedefte 0 baytlık bir dosya görünebilir. Kaynak güncelleştirme sonrasında, anlık görüntüler başarılı olmalıdır.

SQL kaynakları için bir anlık görüntü şu nedenlerden dolayı başarısız olabilir:

* Veri paylaşma izni veren kaynak SQL betiği veya hedef SQL betiği çalıştırılmadı. Ya da Azure SQL veritabanı veya Azure SYNAPSE Analytics (eskiden Azure SQL veri ambarı) için, betik Azure Active Directory kimlik doğrulaması yerine SQL kimlik doğrulaması kullanılarak çalışır.  
* Kaynak veri deposu veya hedef SQL veri deposu duraklatıldı.
* Anlık görüntü işlemi veya hedef veri deposu SQL veri türlerini desteklemiyor. Daha fazla bilgi için bkz. [SQL kaynaklarından paylaşma](how-to-share-from-sql.md#supported-data-types).
* Kaynak veri deposu veya hedef SQL veri deposu diğer süreçler tarafından kilitlidir. Azure veri paylaşımında bu veri depoları kilitlenemez. Ancak bu veri depolarındaki mevcut kilitler anlık görüntü başarısız olabilir.
* Hedef SQL tablosuna bir yabancı anahtar kısıtlaması tarafından başvuruluyor. Bir anlık görüntü sırasında, hedef tablo kaynak verilerdeki bir tabloyla aynı ada sahipse, Azure veri paylaşımında tablo bırakılır ve yeni bir tablo oluşturulur. Hedef SQL tablosuna bir yabancı anahtar kısıtlaması başvuruluyorsa tablo bırakılamaz.
* Hedef CSV dosyası oluşturulur, ancak veriler Excel 'de okunamaz. Kaynak SQL tablosu Ingilizce olmayan karakterler içeren veriler içerdiğinde bu sorunu görebilirsiniz. Excel 'de **veri al** sekmesini SEÇIN ve CSV dosyasını seçin. Dosya kaynağı **65001: Unicode (UTF-8)** öğesini seçin ve ardından verileri yükleyin.

## <a name="updated-snapshot-schedules"></a>Güncelleştirilmiş anlık görüntü zamanlamaları
Veri sağlayıcısı gönderilen paylaşımın anlık görüntü zamanlamasını güncelleştirdikten sonra, veri tüketicisi önceki anlık görüntü zamanlamasını devre dışı bırakmalıdır. Ardından, alınan paylaşımın güncelleştirilmiş anlık görüntü zamanlamasını etkinleştirin. 

## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [veri paylaşımı](share-your-data.md) öğreticisine devam edin. 

Veri alma hakkında bilgi edinmek için, [verileri kabul etme ve alma](subscribe-to-data-share.md) öğreticisine geçin.