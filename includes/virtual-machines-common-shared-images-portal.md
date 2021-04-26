---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7bf71f55e1b49a9280b25cfcc01090afbd0c42db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750609"
---
## <a name="create-an-image-gallery"></a>Görüntü galerisi oluşturma

Görüntü Galerisi, görüntü paylaşımını etkinleştirmek için kullanılan birincil kaynaktır. Galeri adı için izin verilen karakterler büyük veya küçük harflerden, rakamlardan, noktalardan ve noktalardan oluşur. Galeri adı tire içeremez.  Galeri adları, aboneliğiniz dahilinde benzersiz olmalıdır. 

Aşağıdaki örnek *Mygallerrg* kaynak grubunda *MyGallery* adlı bir galeri oluşturur.

1. https://portal.azure.com adresinden Azure portalında oturum açın.
1. Arama kutusundaki **paylaşılan görüntü galerisini** yazın ve sonuçlarda **paylaşılan görüntü Galerisi** ' ni seçin.
1. **Paylaşılan görüntü Galerisi** sayfasında, **Ekle**' ye tıklayın.
1. **Paylaşılan görüntü Galerisi oluştur** sayfasında, doğru aboneliği seçin.
1. **Kaynak grubu**' nda **Yeni oluştur** ' u seçin ve ad için *mygallerrg* yazın.
1. **Ad** alanına Galeri adı Için *MyGallery* yazın.
1. **Bölge** için varsayılan ' i bırakın.
1. *Test için görüntü galerim* gibi, galerinin kısa bir açıklamasını yazabilirsiniz. sonra **gözden geçir + oluştur**' a tıklayın.
1. Doğrulama geçtikten sonra **Oluştur**' u seçin.
1. Dağıtım bittiğinde **Kaynağa Git**' i seçin.


## <a name="create-an-image-definition"></a>Görüntü tanımı oluşturma 

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar içinde oluşturulan görüntü sürümleri hakkındaki bilgileri yönetmek için kullanılır. Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](../articles/virtual-machines/shared-image-galleries.md#image-definitions).

Galerinizin içinde galeri görüntü tanımını oluşturun. Bu örnekte, Galeri görüntüsü *Myımagedefinition* olarak adlandırılmıştır.

1. Yeni görüntü galerinizin sayfasında, sayfanın üst kısmından **Yeni bir görüntü tanımı Ekle** ' yi seçin. 
1. **Yeni görüntü tanımını paylaşılan görüntüye Ekle galerisinde**, **bölge** için *Doğu ABD*' yi seçin.
1. **Görüntü tanımı adı** Için *myımagedefinition* yazın.
1. **İşletim sistemi** için, kaynak sanal makinenize göre doğru seçeneği belirleyin.  
1. **VM oluşturma** IÇIN kaynak sanal makinenize göre seçeneğini belirleyin. Çoğu durumda bu, *Gen 1* olacaktır. Daha fazla bilgi için bkz. [2. nesil sanal makineler Için destek](../articles/virtual-machines/generation-2.md).
1. **İşletim sistemi durumu** için, kaynak sanal makinenize göre seçeneğini belirleyin. Daha fazla bilgi için bkz. [Genelleştirilmiş ve özelleştirilmiş](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).
1. **Yayımcı** Için *MyPublisher* yazın. 
1. **Teklif** Için *myteklifini* yazın.
1. **SKU** Için *mysku* yazın.
1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin.
1. Görüntü tanımı doğrulamayı geçtikten sonra **Oluştur**' u seçin.
1. Dağıtım bittiğinde **Kaynağa Git**' i seçin.


## <a name="create-an-image-version"></a>Görüntü sürümü oluşturma

Yönetilen görüntüden bir görüntü sürümü oluşturun. Bu örnekte, görüntü sürümü *1.0.0* ve hem *Orta Batı ABD* hem de *Orta Güney ABD* veri merkezlerine çoğaltılır. Çoğaltma için hedef bölge seçerken, *kaynak* bölgeyi çoğaltma için hedef olarak da dahil etmeniz gerektiğini unutmayın.

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bir görüntü sürümü oluşturma adımları, kaynağın genelleştirilmiş bir görüntü veya özelleştirilmiş bir VM 'nin anlık görüntüsü olmasına bağlı olarak biraz farklıdır. 

### <a name="option-generalized"></a>Seçenek: Genelleştirilmiş

1. Görüntü tanımınızın sayfasında, sayfanın üst kısmından **Sürüm Ekle** ' yi seçin.
1. **Bölge**' de, yönetilen görüntünüzün depolandığı bölgeyi seçin. Görüntü sürümlerinin oluşturuldukları yönetilen görüntüyle aynı bölgede oluşturulması gerekir.
1. **Ad** için *1.0.0* yazın. Görüntü sürümü adı *büyük* olmalıdır. *küçük*. tamsayılar kullanarak *yama* biçimi. 
1. **Kaynak görüntüde**, açılan listeden kaynak yönetilen görüntünüzü seçin.
1. **En son dışında tut**' da, varsayılan değerini *Hayır* olarak bırakın.
1. **Yaşam tarihi sonu** için, takvimdeki birkaç ay sonra takvimden bir tarih seçin.
1. **Çoğaltma** bölümünde **varsayılan çoğaltma sayısını** 1 olarak bırakın. Kaynak bölgeye çoğaltma yapmanız gerekir, bu nedenle ilk çoğaltmayı varsayılan olarak bırakın ve sonra *Doğu ABD* olacak ikinci bir çoğaltma bölgesi seçin.
1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin. Azure yapılandırmayı doğrulayacaktır.
1. Görüntü sürümü doğrulamayı geçtiğinde **Oluştur**' u seçin.
1. Dağıtım bittiğinde **Kaynağa Git**' i seçin.

Görüntünün tüm hedef bölgelere çoğaltılması biraz zaman alabilir.

### <a name="option-specialized"></a>Seçenek: özelleştirilmiş

1. Görüntü tanımınızın sayfasında, sayfanın üst kısmından **Sürüm Ekle** ' yi seçin.
1. **Bölge**' de, anlık görüntünün depolandığı bölgeyi seçin. Görüntü sürümlerinin oluşturuldukları kaynakla aynı bölgede oluşturulması gerekir.
1. **Ad** için *1.0.0* yazın. Görüntü sürümü adı *büyük* olmalıdır. *küçük*. tamsayılar kullanarak *yama* biçimi. 
1. **Işletim sistemi diski anlık görüntüsü**' nde, açılan LISTEDEN kaynak VM 'nizden anlık görüntüyü seçin. Kaynak sanal makinenizin eklemek istediğiniz bir veri diski varsa, açılan listeden doğru **LUN** numarasını seçin ve ardından **veri diski anlık görüntüsü** için veri diskinin anlık görüntüsünü seçin. 
1. **En son dışında tut**' da, varsayılan değerini *Hayır* olarak bırakın.
1. **Yaşam tarihi sonu** için, takvimdeki birkaç ay sonra takvimden bir tarih seçin.
1. **Çoğaltma** bölümünde **varsayılan çoğaltma sayısını** 1 olarak bırakın. Kaynak bölgeye çoğaltma yapmanız gerekir, bu nedenle ilk çoğaltmayı varsayılan olarak bırakın ve sonra *Doğu ABD* olacak ikinci bir çoğaltma bölgesi seçin.
1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin. Azure yapılandırmayı doğrulayacaktır.
1. Görüntü sürümü doğrulamayı geçtiğinde **Oluştur**' u seçin.
1. Dağıtım bittiğinde **Kaynağa Git**' i seçin.

## <a name="share-the-gallery"></a>Galeriyi paylaşma

Görüntü Galerisi düzeyinde erişimi paylaşmanızı öneririz. Aşağıda, az önce oluşturduğunuz galerinin paylaşılması adım adım gösterilmektedir.

1. Yeni görüntü galerinizin sayfasında, soldaki menüde, **erişim denetimi (IAM)** seçeneğini belirleyin. 
1. **Rol ataması Ekle** altında **Ekle**' yi seçin. **Rol Ekleme atama** bölmesi açılır. 
1. **Rol** altında **okuyucu**' yı seçin.
1. **Erişim ata**' nın altında, varsayılan **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı bırakın.
1. **Seç**' in altında, davet etmek istediğiniz kişinin e-posta adresini yazın.
1. Kullanıcı kuruluşunuzun dışındaysa, **Bu kullanıcıya Microsoft ile işbirliği yapmasına olanak tanıyan bir e-posta gönderileceğini belirten** iletiyi görürsünüz. E-posta adresine sahip kullanıcıyı seçin ve ardından **Kaydet**' e tıklayın.

Kullanıcı kuruluşunuzun dışındaysa, kuruluşa katılması için bir e-posta daveti alırlar. Kullanıcının daveti kabul etmesi gerekir, bu durumda Galeri ve tüm görüntü tanımlarını ve sürümlerini kaynak listesinde görebilirler.
