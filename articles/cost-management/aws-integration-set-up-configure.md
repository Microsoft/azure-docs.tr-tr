---
title: Azure maliyet yönetimi ile AWS maliyet ve kullanım raporu tümleştirmesini ayarlama ve yapılandırma
description: Bu makalede, Azure maliyet yönetimi ile AWS maliyet ve kullanım raporu tümleştirmesini ayarlama ve yapılandırma işlemleri adım adım anlatılmaktadır.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: deb13b833707849bcbce8bcae7b05aeb5e0bce3b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338869"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>AWS maliyet ve kullanım raporu tümleştirmesini ayarlama ve yapılandırma

Amazon Web Services (AWS) maliyet ve kullanım raporu (CUR) Tümleştirmesi sayesinde AWS harcamalarınızı Azure maliyet yönetimi 'nde izleyip kontrol edersiniz. Tümleştirme, Azure ve AWS için harcamalarınızı izleyip denetlediğiniz Azure portal tek bir konuma izin verir. Bu makalede, tümleştirme ve yapılandırma maliyetlerini analiz etmek ve bütçeleri gözden geçirmek için Azure maliyet yönetimi özelliklerini kullanabilmeniz için tümleştirmenin nasıl ayarlanacağı ve yapılandırılacağı açıklanmaktadır.

Maliyet yönetimi, rapor tanımlarını almak ve rapor GZIP CSV dosyalarını indirmek için AWS erişim kimlik bilgilerinizi kullanarak bir S3 demeti içinde depolanan AWS maliyet ve kullanım raporunu işler.

## <a name="create-a-cost-and-usage-report-in-aws"></a>AWS 'de maliyet ve kullanım raporu oluşturma

Maliyet ve kullanım raporu kullanmak, AWS maliyetlerini toplamak ve işlemek için, AWS tarafından önerilen bir yoldur. Daha fazla bilgi için bkz. [AWS maliyet ve kullanım raporu](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) belgeleri.

AWS 'de faturalandırma ve Maliyet Yönetimi konsolunun **maliyet & kullanım raporları** sayfasını kullanarak aşağıdaki adımlarla bir maliyet ve kullanım raporu oluşturun:

1. AWS yönetim konsolunda oturum açın ve [faturalandırma ve maliyet yönetimi konsolunu](https://console.aws.amazon.com/billing)açın.
2. Gezinti bölmesinde, **maliyet & kullanım raporları**' nı seçin.
3. **Rapor oluştur**' u seçin.
4. **Rapor adı**için raporunuz için bir ad girin.
5. **Ek rapor ayrıntıları**' nın altında, **kaynak kimliklerini içer**' i seçin.
6. **Veri yenileme ayarları**IÇIN, AWS 'nin faturanızı tamamladıktan sonra hesabınıza para iadesi, krediler veya destek ücretleri uygulayacağından, AWS maliyet ve kullanım raporunun yenilenmesini isteyip istemediğinizi seçin. Bir rapor yenilendiğinde, Amazon S3 'e yeni bir rapor yüklenir. Ayarı seçili olarak bırakmanız önerilir.
7. **İleri**’yi seçin.
8. **S3 demeti**için **Yapılandır**' ı seçin.
9. S3 demetini Yapılandır iletişim kutusunda aşağıdaki görevlerden birini yapın:
    1. Aşağı açılan listeden mevcut bir demet seçin ve **İleri ' yi**seçin.
    2. Bir demet adı ve yeni bir demet oluşturmak istediğiniz bölgeyi girip **İleri ' yi**seçin.
10. **Bu ilkenin doğru olduğunu onayladım**' ı seçin ve ardından **Kaydet**' e tıklayın.
11. Seçim Rapor yolu ön eki için, raporun adının sonuna eklemek istediğiniz rapor yolu önekini girin.
Ön ek belirtmezseniz, varsayılan ön ek rapor için belirttiğiniz addır. Tarih aralığı `/report-name/date-range/` biçimine sahiptir.
12. **Zaman birimi**için **saatlik**' ı seçin.
13. **Rapor sürümü oluşturma**için, raporun her bir sürümünün önceki sürümün üzerine yazmasını isteyip istemediğinizi veya ek yeni raporlar istediğinizi seçin.
14. **İçin veri tümleştirmesini etkinleştir**için seçim gerekmiyor.
15. **Sıkıştırma**için **GZIP**' i seçin.
16. **İleri**’yi seçin.
17. Raporunuzun ayarlarını inceledikten sonra, **gözden geçir ve Tamam**' ı seçin.

    Rapor adı ' nı aklınızda edin. Daha sonraki adımlarda kullanacaksınız.

AWS 'nin Amazon S3 sepete rapor sunmaya başlaması 24 saate kadar sürebilir. Teslim başladıktan sonra AWS, AWS maliyet ve kullanım raporu dosyalarını günde en az bir kez güncelleştirir. Teslimin başlamasını beklemeden AWS ortamınızı yapılandırmaya devam edebilirsiniz.

## <a name="create-a-role-and-policy-in-aws"></a>AWS 'de rol ve ilke oluşturma

Azure maliyet yönetimi, maliyet ve kullanım raporunun günde birkaç kez bulunduğu S3 demetini 'ne erişir. Hizmetin yeni verileri denetlemek için kimlik bilgilerine erişmesi gerekir. Maliyet yönetiminin bu erişime izin vermek için AWS 'de bir rol ve ilke oluşturursunuz.

Maliyet yönetiminde bir AWS hesabına rol tabanlı erişimi etkinleştirmek için, rol AWS konsolunda oluşturulur. AWS konsolundan _ARN_ ve _dış kimliği_ rolüne sahip olmanız gerekir. Daha sonra, bunları maliyet yönetimi 'nde **AWS Bağlayıcısı oluştur** sayfasında kullanırsınız.

Yeni rol oluşturma Sihirbazı 'nı kullanın:

1. AWS konsolunuza oturum açın ve **Hizmetler**' i seçin.
2. Hizmetler listesinde, **IAM**öğesini seçin.
3. **Roller** ' i seçin ve ardından **rol oluştur**' u seçin.
4. Sonraki sayfada **başka BIR AWS hesabı**seçin.
5. **Hesap kimliği**alanına **432263259397**girin.
6. **Seçenekler**' de, **dış kimlik gerektir ' i seçin (üçüncü taraf bu rolü varsayacak en iyi yöntem)** .
7. **Dış kimlik**ALANıNA dış kimliği girin. Dış KIMLIK, AWS rolü ile Azure maliyet yönetimi arasındaki paylaşılan bir geçiş kodu. Aynı dış KIMLIK aynı zamanda maliyet yönetimi 'ndeki **yeni bağlayıcı** sayfasında da kullanılır. Örneğin, bir dış KIMLIK _Companyname1234567890123_benzerdir.

    > [!NOTE]
    > **MFA gerektir**seçimini değiştirmeyin. Temizlenmiş kalmalıdır.
8. İleri **' yi seçin: İzinler**.
9. **Ilke oluştur**' u seçin. Yeni bir tarayıcı sekmesi açılır. Bu, ilke oluşturduğunuz yerdir.
10. **Hizmet Seç**' i seçin.

Maliyet ve kullanım raporu için izinleri yapılandırın:

1. **Maliyet ve kullanım raporu**girin.
2. @No__t **erişim düzeyi**seçin-1**okuma** > **DescribeReportDefinitions**. Bu adım maliyet yönetiminin, GEÇERLI raporların ne şekilde tanımlandığını okumasına ve rapor tanımı önkoşulu ile eşleşip eşleşmediğine nasıl izin verdiğini sağlar.
3. **Ek Izinler Ekle**' yi seçin.

S3 demet ve nesneleriniz için izinleri yapılandırın:

1. **Hizmet Seç**' i seçin.
2. **S3**girin.
3. @No__t-1**liste** > **listbucket** **erişim düzeyini**seçin. Bu eylem, S3 demetini içindeki nesnelerin listesini alır.
4. **Erişim düzeyi** > **okuma** > **GetObject**' i seçin. Bu eylem faturalandırma dosyalarının indirilmesini sağlar.
5. **Kaynakları**seçin.
6. **Demet seçin – ARN ekleyin**.
7. **Demet adı**' nda, geçerli dosyaları depolamak için kullanılan demeti girin.
8. **Nesne seçin – ARN ekleyin**.
9. **Demet adı**' nda, geçerli dosyaları depolamak için kullanılan demeti girin.
10. **Nesne adı**' nda **herhangi birini**seçin.
11. **Ek Izinler Ekle**' yi seçin.

Maliyet Gezgini için izinleri yapılandırın:

1. **Hizmet Seç**' i seçin.
2. **Maliyet Gezgini hizmeti**girin.
3. **Tüm maliyet Gezgini hizmet eylemlerini seçin (CE: \*)** . Bu eylem, koleksiyonun doğru olduğunu doğrular.
4. **Ek Izinler Ekle**' yi seçin.

AWS kuruluşları için izin ekleme:

1. **Kuruluşları**girin.
2. @No__t **erişim düzeyi**seçin-1**liste** > **listaccounts**. Bu eylem, hesapların adlarını alır.
3. **Inceleme ilkesi**' nde, yeni ilke için bir ad girin. Doğru bilgileri girdiğinizden emin olun ve ardından **Ilke oluştur**' u seçin.
4. Önceki sekmeye geri dönün ve tarayıcınızın Web sayfasını yenileyin. Arama çubuğunda yeni ilkenizi arayın.
5. İleri **' yi seçin: Gözden**geçirin.
6. Yeni rol için bir ad girin. Doğru bilgileri girdiğinizden emin olun ve ardından **rol oluştur**' u seçin.

    Rolü oluştururken önceki adımlarda kullanılan rol ve dış KIMLIK ' i göz önünde bulabilirsiniz. Bunları daha sonra Azure maliyet yönetimi bağlayıcısını ayarlarken kullanacaksınız.

JSON ilkesi aşağıdaki örneğe benzemelidir. _Bucketname_ değerini S3 demet 'larınızın adıyla değiştirin.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Azure 'da yeni bir AWS Bağlayıcısı ayarlama

AWS bağlayıcısını oluşturmak ve AWS maliyetlerinizi izlemeye başlamak için aşağıdaki bilgileri kullanın:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Maliyet yönetimi + faturalandırma** > **maliyet yönetimi**' ne gidin.
3. **Ayarlar**altında **bulut bağlayıcıları ' nı (Önizleme)** seçin.  
    ](./media/aws-integration-setup-configure/cloud-connectors-preview01.png) @no__t bulut bağlayıcıları (Önizleme) ayarını gösteren 0 örnek).
4. Bağlayıcı oluşturmak için sayfanın üst kısmında **+ Ekle** ' yi seçin.
5. **AWS Bağlayıcısı oluştur** sayfasında, **görünen ad**alanına Bağlayıcınız için bir ad girin.  
    AWS Bağlayıcısı oluşturmaya yönelik sayfanın 0Örneği @no__t @ no__t-1
6. İsteğe bağlı olarak, varsayılan yönetim grubunu seçin. Tüm bulunan bağlı hesapları depolayacaktır. Daha sonra ayarlayabilirsiniz.
7. Önizlemenin süresi dolduğu zaman sürekli olarak emin olmak istiyorsanız, **faturalandırma** bölümünde, **genel kullanıma sunulmasıyla otomatik olarak% 1 oranında ücretlendirmeyi** seçin. Otomatik seçeneğini belirlerseniz, bir faturalandırma aboneliği seçmeniz gerekir.
8. **ARN rolü**IÇIN, AWS 'de rolü ayarlarken kullandığınız değeri girin.
9. **Dış kimlik**IÇIN, AWS 'de rolü ayarlarken kullandığınız değeri girin.
10. **Rapor adı**için AWS 'de oluşturduğunuz adı girin.
11. **İleri** ' yi ve ardından **Oluştur**' u seçin.

Yeni AWS kapsamlarının, AWS birleştirilmiş hesabının, AWS bağlı hesaplarının ve maliyet verilerinin görünmesi birkaç saat sürebilir.

Bağlayıcıyı oluşturduktan sonra, ona erişim denetimi atamanızı öneririz. Kullanıcılara yeni bulunan kapsamlar için izinler atanır: AWS birleştirilmiş hesabı ve AWS bağlı hesapları. Bağlayıcıyı oluşturan kullanıcı bağlayıcının sahibidir, birleştirilmiş hesap ve tüm bağlı hesaplardır.

Bulma gerçekleştikten sonra kullanıcılara bağlayıcı izinleri atama, mevcut AWS kapsamlarına izin atamaz. Bunun yerine, yalnızca yeni bağlı hesaplara izin atanır.

## <a name="take-additional-steps"></a>Ek adımlar alın

- Henüz yapmadıysanız [Yönetim gruplarını ayarlayın](../governance/management-groups/overview.md#initial-setup-of-management-groups).
- Kapsam seçiciye yeni kapsamların eklendiğinden emin olun. En son verileri görüntülemek için **Yenile** ' yi seçin.
- **Bulut bağlayıcıları** sayfasında, bağlayıcıyı seçin ve bağlı hesabı yönetim gruplarına atamak için **faturalandırma hesabına git** ' i seçin.

## <a name="manage-cloud-connectors"></a>Bulut bağlayıcılarını yönetme

**Bulut bağlayıcıları** sayfasında bir bağlayıcı seçtiğinizde şunları yapabilirsiniz:

- AWS birleştirilmiş hesabının bilgilerini görüntülemek için **faturalandırma hesabına git** ' i seçin.
- Bağlayıcının rol atamasını yönetmek için **Access Control** ' ı seçin.
- Bağlayıcıyı güncelleştirmek için **Düzenle** ' yi seçin. AWS hesap numarasını değiştiremezsiniz, çünkü rol ARN ' de görünür. Ancak, yeni bir bağlayıcı oluşturabilirsiniz.
- Maliyet yönetiminin bağlayıcı ayarlarını kullanarak veri toplayabildiğini doğrulamak için doğrulama testini yeniden çalıştırmak için **Doğrula** ' yı seçin.

![Oluşturulan AWS bağlayıcıları örnek listesi](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Azure Yönetim gruplarını ayarlama

Azure aboneliklerinizi ve AWS bağlantılı hesaplarınızı aynı yönetim grubuna yerleştirip, platformlar arası sağlayıcı bilgilerini görebileceğiniz tek bir konum oluşturun. Azure ortamınızı zaten yönetim gruplarıyla yapılandırmadıysanız, bkz. [yönetim gruplarının ilk kurulumu](../governance/management-groups/overview.md#initial-setup-of-management-groups).

Maliyetleri ayırmak istiyorsanız, yalnızca AWS bağlantılı hesaplarını tutan bir yönetim grubu oluşturabilirsiniz.

## <a name="set-up-an-aws-consolidated-account"></a>AWS birleştirilmiş hesabı ayarlama

AWS birleştirilmiş hesabı, birden çok AWS hesabı için faturalandırma ve ödemeyi birleştirir. Ayrıca, AWS bağlantılı hesabı gibi davranır.

![AWS birleştirilmiş hesabı için örnek Ayrıntılar](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Sayfasında şunları yapabilirsiniz:

- AWS bağlı hesaplarının bir yönetim grubuyla ilişkilendirmesini toplu olarak güncelleştirmek için **Güncelleştir** ' i seçin.
- Kapsam için rol atamasını ayarlamak üzere **Access Control** ' yi seçin.

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS birleştirilmiş hesabının izinleri

Varsayılan olarak, bir AWS birleştirilmiş hesabının izinleri, AWS Bağlayıcısı izinlerine göre hesabın oluşturulmasına göre ayarlanır. Bağlayıcı Oluşturucusu, sahibidir.

AWS birleştirilmiş hesabının **erişim düzeyi** sayfasını kullanarak erişim düzeyini yönetirsiniz. Bununla birlikte, AWS bağlantılı hesaplar AWS birleştirilmiş hesabına izin aktarılmaz.

## <a name="set-up-an-aws-linked-account"></a>AWS bağlantılı hesabı ayarlama

AWS bağlı hesabı AWS kaynaklarının oluşturulduğu ve yönetildiği yerdir. Bağlı bir hesap da güvenlik sınırı olarak davranır.

Bu sayfadan şunları yapabilirsiniz:

- Bir AWS bağlantılı hesabının bir yönetim grubuyla ilişkilendirmesini güncelleştirmek için **Güncelleştir** ' i seçin.
- Kapsam için bir rol ataması ayarlamak üzere **Access Control** ' yi seçin.

![AWS bağlı hesap sayfası örneği](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS bağlı hesabının izinleri

Varsayılan olarak, AWS bağlantılı hesabına yönelik izinler, AWS bağlayıcı izinlerine göre oluşturma sırasında ayarlanır. Bağlayıcı Oluşturucusu, sahibidir. Erişim düzeyini AWS bağlantılı hesabının **erişim düzeyi** sayfasını kullanarak yönetirsiniz. AWS bağlantılı hesaplar bir AWS birleştirilmiş hesabından izinleri almıyor.

AWS bağlı hesapları her zaman izinleri ait oldukları yönetim grubundan devralınır.

## <a name="next-steps"></a>Sonraki adımlar

- AWS maliyet ve kullanım raporu tümleştirmesini ayarlayıp yapılandırdığınıza göre, [AWS maliyetlerini ve kullanımını yönetmeye](aws-integration-manage.md)devam edin.
- Maliyet analizinden daha fazla bilginiz varsa bkz. [Maliyet Analizi hızlı başlangıç ile maliyetleri araştırma ve analiz etme](quick-acm-cost-analysis.md) .
- Azure 'daki bütçeleri tanımıyorsanız, bkz. [Azure bütçeleri oluşturma ve yönetme](tutorial-acm-create-budgets.md).
