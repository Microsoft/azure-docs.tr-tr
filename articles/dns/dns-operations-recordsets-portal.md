---
title: Azure DNS ile DNS kayıt kümelerini ve kayıtlarını yönetme
description: Azure DNS, etki alanınızı barındırırken DNS kayıt kümelerini ve kayıtlarını yönetme yeteneği sağlar.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 22ceba69ecf865d906021068a39a9d273b842ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965740"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Azure portal kullanarak DNS kayıtlarını ve kayıt kümelerini yönetme

Bu makalede, Azure portal kullanarak DNS bölgeniz için kayıt kümelerini ve kayıtları nasıl yöneteceğiniz gösterilmektedir.

DNS kayıt kümeleri ve bireysel DNS kayıtları arasındaki farkı anlamak önemlidir. Kayıt kümesi, aynı ada sahip ve aynı türde olan bir bölgedeki kayıt koleksiyonudur. Daha fazla bilgi için, bkz. [Azure Portal kullanarak DNS kayıt kümeleri ve kayıtları oluşturma](./dns-getstarted-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Yeni bir kayıt kümesi ve kayıt oluştur

Azure portal bir kayıt kümesi oluşturmak için, bkz. [Azure Portal kullanarak DNS kayıtları oluşturma](./dns-getstarted-portal.md).

## <a name="view-a-record-set"></a>Kayıt kümesini görüntüleme

1. Azure portal, **DNS bölgesi** dikey penceresine gidin.
2. Kayıt kümesini arayın ve seçin. Bu, kayıt kümesi özelliklerini açar.

    ![Kayıt kümesi ara](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Kayıt kümesine yeni bir kayıt ekleyin

Herhangi bir kayıt kümesine 20 ' ye kadar kayıt ekleyebilirsiniz. Kayıt kümesi iki özdeş kayıt içeremez. Boş kayıt kümeleri (sıfır kayıtla birlikte) oluşturulabilir, ancak Azure DNS ad sunucularında görünmez. CNAME türündeki kayıt kümeleri, en çok bir kayıt içerebilir.

1. DNS bölgeniz için **kayıt kümesi özellikleri** dikey penceresinde, kayıt eklemek istediğiniz kayıt kümesine tıklayın.

    ![Bir kayıt kümesi seçin](./media/dns-operations-recordsets-portal/selectset500.png)

2. Alanları doldurarak kayıt kümesi özelliklerini belirtin.

    ![Kayıt ekleme](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Ayarlarınızı kaydetmek için dikey pencerenin en üstündeki **Kaydet** ' e tıklayın. Ardından dikey pencereyi kapatın.
4. Köşede kaydın kaydedilmesini görürsünüz.

    ![Kayıt kümesi kaydediliyor](./media/dns-operations-recordsets-portal/saving150.png)

Kayıt kaydedildikten sonra, **DNS bölgesi** dikey penceresindeki değerler yeni kaydı yansıtır.

## <a name="update-a-record"></a>Kaydı güncelleştirme

Varolan bir kayıt kümesindeki bir kaydı güncelleştirdiğinizde, güncelleştirebilmeniz için kullanabileceğiniz alanlar, üzerinde çalıştığınız kaydın türüne bağlıdır.

1. Kayıt kümesi için **kayıt kümesi özellikleri** dikey penceresinde kaydı arayın.
2. Kaydı değiştirin. Bir kaydı değiştirdiğinizde, kayıt için kullanılabilir ayarları değiştirebilirsiniz. Aşağıdaki örnekte, **IP adresi** alanı SEÇILIDIR ve IP adresi değiştirilme sürecinde olur.

    ![Bir kaydı değiştirme](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Ayarlarınızı kaydetmek için dikey pencerenin en üstündeki **Kaydet** ' e tıklayın. Sağ üst köşede, kaydın kaydedildiğini belirten bildirimi görürsünüz.

    ![Kayıt kümesi kaydedildi](./media/dns-operations-recordsets-portal/saved150.png)

Kayıt kaydedildikten sonra, **DNS bölgesi** dikey penceresindeki kayıt kümesine ilişkin değerler güncelleştirilmiş kaydı yansıtır.

## <a name="remove-a-record-from-a-record-set"></a>Kayıt kümesinden kayıt kaldırma

Kayıt kümesinden kayıtları kaldırmak için Azure portal kullanabilirsiniz. Kayıt kümesinden son kaydı kaldırmanın kayıt kümesini silmediğini unutmayın.

1. Kayıt kümesi için **kayıt kümesi özellikleri** dikey penceresinde kaydı arayın.
2. Kaldırmak istediğiniz kayda tıklayın. Ardından **Kaldır**' ı seçin.

    ![Kayıt kaldırma](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Ayarlarınızı kaydetmek için dikey pencerenin en üstündeki **Kaydet** ' e tıklayın.
4. Kayıt kaldırıldıktan sonra, **DNS bölgesi** dikey penceresindeki kaydın değerleri kaldırma işleminin yansıtılacaktır.

## <a name="delete-a-record-set"></a><a name="delete"></a>Kayıt kümesini silme

1. Kayıt kümesi için **kayıt kümesi özellikleri** dikey penceresinde **Sil**' e tıklayın.

    ![Kayıt kümesini silme](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Kayıt kümesini silmek isteyip istemediğinizi soran bir ileti görüntülenir.
3. Adın, silmek istediğiniz kayıt kümesiyle eşleştiğinden emin olun ve ardından **Evet**' e tıklayın.
4. **DNS bölgesi** dikey penceresinde, kayıt kümesinin artık görünür olmadığını doğrulayın.

## <a name="work-with-ns-and-soa-records&quot;></a>NS ve SOA kayıtlarıyla çalışma

Otomatik olarak oluşturulan NS ve SOA kayıtları, diğer kayıt türlerinden farklı şekilde yönetilir.

### <a name=&quot;modify-soa-records&quot;></a>SOA kayıtlarını değiştirme

Tepesinde (Name = &quot;") bölgesinde otomatik olarak oluşturulan SOA kayıt kümesine kayıt ekleyemez veya buradan kayıt kaldıramazsınız \@ . Bununla birlikte, SOA kaydındaki parametrelerden herhangi birini ("ana bilgisayar" hariç) ve kayıt kümesi TTL 'sini de değiştirebilirsiniz.

### <a name="modify-ns-records-at-the-zone-apex"></a>Tepesinde bölgesinde NS kayıtlarını değiştirme

Tepesinde bölgesinde ayarlanan NS kaydı, her DNS bölgesiyle otomatik olarak oluşturulur. Bu, bölgeye atanan Azure DNS ad sunucularının adlarını içerir.

Birden fazla DNS sağlayıcısıyla ortak barındırma etki alanlarını desteklemek için bu NS kayıt kümesine ek ad sunucuları ekleyebilirsiniz. Bu kayıt kümesi için TTL ve meta verileri de değiştirebilirsiniz. Ancak, önceden doldurulmuş Azure DNS adı sunucularını kaldıramaz veya değiştiremezsiniz.

Bunun yalnızca tepesinde bölgesindeki NS kayıt kümesi için geçerli olduğunu unutmayın. Bölgesindeki diğer NS kayıt kümeleri (alt alanları temsilci atamak için kullanılan), kısıtlama olmadan değiştirilebilir.

### <a name="delete-soa-or-ns-record-sets"></a>SOA veya NS kayıt kümelerini silme

Bölge oluşturulduğunda otomatik olarak oluşturulan bölge tepesinde (Name = "") konumundaki SOA ve NS kayıt kümelerini silemezsiniz \@ . Bölgeyi sildiğinizde otomatik olarak silinir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure DNS hakkında daha fazla bilgi için bkz. [Azure DNS genel bakış](dns-overview.md).
* DNS 'yi otomatikleştirme hakkında daha fazla bilgi için bkz. [.NET SDK kullanarak DNS bölgeleri ve kayıt kümeleri oluşturma](dns-sdk.md).
* Ters DNS kayıtları hakkında daha fazla bilgi için bkz. [Azure 'da ters DNS ve desteğe genel bakış](dns-reverse-dns-overview.md).
* Azure DNS diğer ad kayıtları hakkında daha fazla bilgi için bkz. [Azure DNS diğer ad kayıtlarına genel bakış](dns-alias.md).