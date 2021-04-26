---
title: İşlerinizi zamanlayın
description: Görevlerinizi yönetmek için iş zamanlamayı kullanın.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 4661c9fc22868870af147998467c9f355f30580e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219713"
---
# <a name="schedule-jobs-for-efficiency"></a>İşleri verimlilik için zamanlama

Batch işlerinin zamanlanması, diğer görevlere bağımlılıkları olan hesap görevlerini yaparken önce çalıştırmak istediğiniz işlerin önceliklerini belirlemenizi sağlar. İşlerinizi zamanlayarak, en az miktarda kaynak kullandığınızdan emin olabilirsiniz. Düğüm, gerekli olmadığında kullanımdan kaldırılır, diğer görevlere bağımlı olan görevler, iş akışlarını en iyi duruma getirmek için bir süre sonra sona erdirir. Tek seferde yalnızca bir iş çalışır. Önceki bir işlem tamamlanana kadar yeni bir tane başlatılmaz. İşinizi otomatik tamamlama olarak ayarlayabilirsiniz. 

## <a name="benefit-of-job-scheduling"></a>İş zamanlama avantajı

İşlerin zamanlanması, iş oluşturma için bir zamanlama belirtmektir. İş Yöneticisi görevini kullanarak zamanladığınız görevler bir işle ilişkilendirilir. İş Yöneticisi görevi iş için görevler oluşturacaktır. Bunu yapmak için, iş yöneticisi görevinin Batch hesabıyla kimlik doğrulaması yapması gerekir. AZ_BATCH_AUTHENTICATION_TOKEN erişim belirtecini kullanın. Belirteç, işin geri kalanına erişime izin verir. 

## <a name="use-the-portal-to-schedule-a-job"></a>Bir işi zamanlamak için portalı kullanma

   1. [Azure Portal](https://portal.azure.com/)oturum açın.

   2. İşleri zamanlamak istediğiniz Batch hesabını seçin.

   3. **Ekle** ' yi seçerek yeni bir iş zamanlaması oluşturun ve **temel formu** doldurun.



![İş zamanlama][1]

**İş ZAMANLAMASı kimliği**: Bu iş zamanlaması için benzersiz tanımlayıcı.

**Görünen ad**: işin görünen adının benzersiz olması gerekmez, ancak en fazla 1024 karakter uzunluğunda olmalıdır.

**Şu kadar çalıştırmayın**: işin çalıştırılacağı en erken zamanı belirtir. Bunu ayarlamazsanız, zamanlama işleri hemen çalıştırmaya hazır hale gelir.

**Sonrasında çalıştırma**: burada ayarladığınız zamandan sonra iş çalıştırılmadı. Bir zaman belirtmezseniz, açıkça sonlanana kadar etkin olmaya devam eden yinelenen bir iş zamanlaması oluşturuyorsunuz.

**Yinelenme aralığı**: işler arasındaki süreyi belirtebilirsiniz. Zamanlanan bir zamanda yalnızca bir iş olabilir, bu nedenle bir iş zamanlaması altında yeni bir iş oluşturmak zaman alıyorsa, ancak önceki iş hala çalışıyorsa, Batch hizmeti, önceki iş bitene kadar yeni işi oluşturmaz.  

**Başlangıç penceresi**: burada, zaman aralığını, zamanlamanın bir işin oluşturulması gerektiğini gösterdiği ve tamamlanması gerektiği zamana kadar belirtirsiniz. Geçerli iş kendi penceresi sırasında tamamlanmazsa, bir sonraki iş başlamaz.

Temel formun en altında, işin çalıştırılmasını istediğiniz havuzu belirtirsiniz. Havuz KIMLIĞI bilgilerinizi bulmak için **Güncelleştir**' i seçin. 

![Havuzu belirtin][2]


**Havuz kimliği**: işin çalıştırılacağı havuzu belirler.

**İş yapılandırma görevi**: iş yöneticisi görevinin ve bunları kullanıyorsanız iş hazırlama ve bırakma görevlerinin adını vermek için **Güncelleştir** ' i seçin.

**Öncelik**: işe bir öncelik verin.

**Maksimum duvar saati zamanı**: işin çalışacağı maksimum süreyi ayarlayın. Zaman dilimi içinde tamamlanmazsa toplu iş işi sonlandırır. Bunu ayarlamazsanız, iş için zaman sınırı yoktur.

**En fazla görev yeniden deneme sayısı**: bir görevin en fazla dört kata kaç kez yeniden deneneceğini belirtin. Bu, bir API çağrısının sahip olabileceği yeniden deneme sayısı ile aynı değildir.

**Tüm görevler tamamlandığında**: varsayılan eylem değildir.

**Bir görev başarısız olduğunda**: varsayılan eylem değildir. Yeniden deneme sayısı tükenirse veya görev başlatılırken bir hata oluşursa bir görev başarısız olur. 

**Kaydet**' i seçtikten sonra, sol gezinti bölmesinde **iş zamanlamalarına** giderseniz, **yürütme bilgileri**' ni seçerek işin yürütülmesini izleyebilirsiniz.


## <a name="for-more-information"></a>Daha fazla bilgi edinmek için

Azure CLı kullanarak bir işi yönetmek için, bkz. [az Batch Job-Schedule](/cli/azure/batch/job-schedule).

## <a name="next-steps"></a>Sonraki adımlar

[Diğer görevlere bağımlı görevleri çalıştırmak için görev bağımlılıkları oluşturun](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add-job-schedule-02.png
[2]: ./media/batch-job-schedule/add-job-schedule-03.png


