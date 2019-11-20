---
title: Depolama hesaplarını Azure Cloudyn için yapılandırma | Microsoft Docs
description: Bu makalede, Azure depolama hesapları ve AWS depolama demet için Cloudyn nasıl yapılandırılacağını açıklar.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 91377c41699f01eaf57a085ea82e9d7289549990
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969148"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Cloudyn depolama hesaplarını yapılandırma

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Cloudyn portalında, Azure depolama veya AWS depolama demet Cloudyn raporlarını kaydedebilirsiniz. Cloudyn portalında raporlarınızı kaydetme ücretsizdir. Ancak, bulut hizmeti sağlayıcısının depolama alanına raporlarınızı kaydetme isteğe bağlıdır ve ek ücret doğurur. Bu makalede, Azure depolama hesapları ve raporlarınızı depolamak için depolama demetler Amazon Web Services (AWS) yapılandırmanıza yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

Bir Azure depolama hesabı ya da bir Amazon depolama aralığı olmalıdır.

Azure depolama hesabınız yoksa, oluşturmanız gerekir. Azure depolama hesabı oluşturma hakkında daha fazla bilgi için bkz. [depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md).

AWS yoksa basit depolama hizmeti (S3 için) demetine bir oluşturmanız gerekir. Bir S3 demetini oluşturma hakkında daha fazla bilgi için bkz. [bir demet oluşturma](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Azure depolama hesabınızı yapılandırın

Yapılandırdığınız Cloudyn tarafından kullanılmak üzere Azure depolama oldukça basittir. Depolama hesabı ayrıntılarını toplayın ve bunları Cloudyn portalında kopyalayın.

1. [https://portal.azure.com](https://portal.azure.com ) adresinden Azure portalında oturum açın.
2. Tıklayın **tüm hizmetleri**seçin **depolama hesapları**, ardından hesabını seçin ve istediğiniz depolama hesabına gidin.
3. Depolama hesabı sayfanıza altında **ayarları**, tıklayın **erişim anahtarlarını**.
4. Kopyalama, **depolama hesabı adı** ve **bağlantı dizesi** key1 altında.  
   ![Depolama hesabı adı ve bağlantı dizesini kopyalayın](./media/storage-accounts/azure-storage-access-keys.png)  
5. Cloudyn portalını Azure portalından açın veya https://azure.cloudyn.com sayfasına gidip oturum açın.
6. Dişli simgesine tıklayın ve ardından **raporları Depolama Yönetimi**.
7. Tıklayın **yeni Ekle +** ve Microsoft Azure'nın seçili olduğundan emin olun. Azure depolama hesabı adınızı yapıştırın **adı** alan. Yapıştırma, **bağlantı dizesi** karşılık gelen alandaki. Bir kapsayıcı adı girin ve ardından **Kaydet**.  
   ![Azure depolama hesabı adı ve bağlantı yeni bir rapor depolama Kutusu Ekle dize yapıştırın](./media/storage-accounts/azure-cloudyn-storage.png)

   Yeni Azure rapor depolama giriş depolama hesabı listesinde görüntülenir.  
    ![Yeni Azure rapor depolama giriş listesi](./media/storage-accounts/azure-storage-entry.png)


Raporları, artık Azure depolama alanına kaydedebilirsiniz. Herhangi bir raporda tıklayın **eylemleri** seçip **rapor zamanla**. Rapor adı ve ardından kendi URL ekleyebilir veya otomatik olarak oluşturulan URL'yi kullanın. Seçin **depolamaya kaydetme** ve ardından depolama hesabını seçin. Raporu dosya adına eklenmiş bir ön eki girin. CSV veya JSON dosya biçimi seçin ve ardından raporu kaydedin.

## <a name="configure-an-aws-storage-bucket"></a>Bir AWS depolama aralığı yapılandırın

Cloudyn mevcut AWS kimlik bilgileri kullanır: Kullanıcı veya rol, raporları, demetine kaydetmek için. Erişimi test etmek için Cloudyn küçük bir metin dosyası için dosya adına sahip demet kaydetmeyi denediğinde _onay demet permission.txt_.

Cloudyn rolü ya da kullanıcı PutObject izinle, demet için sağladığınız. Ardından, var olan bir demet kullanın veya raporları kaydetmek için yeni bir tane oluşturun. Son olarak, depolama sınıfı yönetmek, yaşam döngüsü kurallar veya gereksiz dosyaları kaldırın nasıl karar verin.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>AWS kullanıcı veya rol izin atama

Yeni bir ilke oluşturduğunuzda, bir rapor için bir S3 demetini kaydetmek için gereken tam izinleri sağlar.

1. AWS konsolunda oturum açın ve seçin **Hizmetleri**.
2. Seçin **IAM** hizmetler listesinden.
3. Seçin **ilkeleri** konsolu ve ardından sol tarafındaki **ilke Oluştur**.
4. Tıklayın **JSON** sekmesi.
5. Aşağıdaki ilke bir rapor için bir S3 demetini kaydetmenize olanak tanır. Aşağıdaki ilke örneği kopyalayıp **JSON** sekmesi. Değiştirin &lt;bucketname&gt; demet adını.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Tıklayın **incelemesi İlkesi**.  
    ![AWS JSON İlkesi örnek bilgiler gösteriliyor](./media/storage-accounts/aws-policy.png)  
7. İnceleme İlkesi sayfasında, ilke için bir ad yazın. Örneğin, _CloudynSaveReport2S3_.
8. Tıklayın **ilkesi oluşturma**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>İlkeyi bir Cloudyn rolü ya da kullanıcı hesabı ekleme

Yeni ilke eklemek için AWS konsolunu açın ve Cloudyn rolü ya da kullanıcı düzenleyin.

1. AWS konsolunda oturum açın ve seçin **Hizmetleri**, ardından **IAM** hizmetler listesinden.
2. Şunlardan birini seçin **rolleri** veya **kullanıcılar** konsolun sol tarafındaki.

**Roller için:**

  1. Cloudyn rol adınızı tıklatın.
  2. Üzerinde **izinleri** sekmesinde **ekleme İlkesi**.
  3. Arama için oluşturduğunuz ilke ve onu seçin ve ardından tıklayın **ekleme İlkesi**.
    ![Cloudyn rolünüze bağlı örnek ilke](./media/storage-accounts/aws-attach-policy-role.png)

**Kullanıcılar için:**

1. Cloudyn kullanıcı seçin.
2. Üzerinde **izinleri** sekmesinde **izinleri eklemek**.
3. İçinde **izni** bölümünden **mevcut ilkeleri doğrudan Ekle**.
4. Arama için oluşturduğunuz ilke ve onu seçin ve ardından tıklayın **sonraki: Gözden geçirme**.
5. Rol adı sayfasına Ekle izinlerini tıklayın **izinleri eklemek**.  
    ![Cloudyn kullanıcı için bağlı örnek ilke](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>İsteğe bağlı: Demet ilkesiyle izin ayarlama

Bir demet İlkesi'ni kullanarak, S3 demetini üzerinde raporlar oluşturma izni de ayarlayabilirsiniz. Klasik S3 Görünümü'nde:

1. Oluşturun veya mevcut bir aralığı seçin.
2. Seçin **izinleri** sekmesine ve ardından **demetine ilke**.
3. Aşağıdaki ilke örneği kopyalayıp yeniden açın. Değiştirin &lt;demet\_adı&gt; ve &lt;Cloudyn\_ilkesine&gt; , sepet, ARN ile. Cloudyn tarafından kullanılan kullanıcı veya rol ARN değiştirin.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. Demet İlkesi Düzenleyicisi'nde tıklayın **Kaydet**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Cloudyn'e AWS rapor depolama ekleme

1. Cloudyn portalını Azure portalından açın veya https://azure.cloudyn.com sayfasına gidip oturum açın.
2. Dişli simgesine tıklayın ve ardından **raporları Depolama Yönetimi**.
3. Tıklayın **yeni Ekle +** ve AWS seçili olduğundan emin olun.
4. Bir hesabı ve depolama aralığı seçin. AWS depolama demet adını otomatik olarak doldurulmuş bileşenidir.  
    ![Örnek bilgiler, yeni bir rapor depolama Kutusu Ekle](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Tıklayın **Kaydet** ve ardından **Tamam**.

    Yeni AWS rapor depolama giriş depolama hesabı listesinde görüntülenir.  
    ![Depolama giriş show depolama hesabı listesinde yeni AWS rapor](./media/storage-accounts/aws-storage-entry.png)


Raporları, artık Azure depolama alanına kaydedebilirsiniz. Herhangi bir raporda tıklayın **eylemleri** seçip **rapor zamanla**. Rapor adı ve ardından kendi URL ekleyebilir veya otomatik olarak oluşturulan URL'yi kullanın. Seçin **depolamaya kaydetme** ve ardından depolama hesabını seçin. Raporu dosya adına eklenmiş bir ön eki girin. CSV veya JSON dosya biçimi seçin ve ardından raporu kaydedin.

## <a name="next-steps"></a>Sonraki adımlar

- Gözden geçirme [anlama Cloudyn raporlarını](understanding-cost-reports.md) temel yapısını ve Cloudyn raporlarını işlevleri hakkında bilgi edinmek için.
