# Film Navigasyonunun Ele Alınması

### Navigasyon Elemanının Yerelleştirilmesi

Sergen, *Movie* tablosu için kod ürettiğinde, ayrıca navigasyon için de bir eleman girdisi oluşturdu. Serene'de navigasyon elemanları özel assembly nitelikleri (attribute) ile tanımlanır.

Aynı klasördeki *MoviePage.cs* dosyasını açınca üst bölümünde aşağıdaki satırı bulacaksınız:

```cs
[assembly:Serenity.Navigation.NavigationLink(int.MaxValue, "MovieDB/Movie", 
    typeof(MovieTutorial.MovieDB.Pages.MovieController))]

namespace MovieTutorial.MovieDB.Pages
{
    using Serenity;
    using Serenity.Web;

```

Bu niteliğin ilk argümanı gösterileceği sırayı belirler. *Movie* kategorisinde tek bir elemanımız olduğu için şimdilik sıralamayla uğraşmamıza gerek yok.

İkinci argüman, "Bölüm Başlığı/Bağlantı Başlığı" formatında navigasyon başlığıdır. Bölüm ve navigasyon elemanları düz bölü (/ - slash) ile birbirinden ayrılır.

Başlığı *Movie Database/Movies* olarak değiştirelim.

```cs
[assembly:Serenity.Navigation.NavigationLink(int.MaxValue, "Movie Database/Movies", 
    typeof(MovieTutorial.MovieDB.Pages.MovieController), icon: "icon-camrecorder")]

namespace MovieTutorial.MovieDB.Pages
{

//..
```

![Navigation Elemanı Başlık ve İkonu](img/movies_navigation_links.png)

Ayrıca navigasyon elemanının ikonunu da *icon-camcorder* olarak değiştirdik. Serene şablonunda iki ikon fontu bulunur, *Simple Line Icons* ve *Font Awesome*. Burada basit çizgi ikonları setinden bir kod kullandık. 

Bu ikonlar ve css sınıflarının bir listesini görmek için aşağıdaki bağlantıyı ziyaret edilebilir:

http://thesabbir.github.io/simple-line-icons/

FontAwesome ise şurada bulunmaktadır:

https://fortawesome.github.io/Font-Awesome/icons/


### Navigasyon Bölümlerinin Sıralanması

*Movie Database* bölümümüz en son üretildiğinden, navigasyon menüsünün en altında görüntüleniyor.

Bölümü, Northwind menüsünün altına taşıyacağız.

Az önce gördüğümüz gibi, Sergen navigasyon elemanını *MoviePage.cs* içinde üretti. Eğer navigasyon elemanları bu şeklide sayfalar içinde dağınık durursa, büyük resmi (tüm navigasyon elemanlarının listesini) bir arada görmek ve kolayca sıralamak mümkün olmayacaktır.

Bu nedenle, elemanı *MovieTutorial.Web/Modules/Common/Navigation/NavigationItems.cs* dosyasındaki merkezi konumumuza taşıyacağız, 

*MoviePage.cs* dosyasından aşağıdaki satırları kesin:

```cs
[assembly:Serenity.Navigation.NavigationLink(int.MaxValue, "Movie Database/Movies", 
    typeof(MovieTutorial.MovieDB.Pages.MovieController), icon: "icon-camrecorder")]
```

*NavigationItems.cs* dosyasına taşıyıp aşağıdaki gibi düzenleyin:

```
using Serenity.Navigation;
using Northwind = MovieTutorial.Northwind.Pages;
using Administration = MovieTutorial.Administration.Pages;
using MovieDB = MovieTutorial.MovieDB.Pages;

[assembly: NavigationLink(1000, "Dashboard", url: "~/", permission: "",
    icon: "icon-speedometer")]

[assembly: NavigationMenu(2000, "Movie Database", icon: "icon-film")]
[assembly: NavigationLink(2100, "Movie Database/Movies", 
    typeof(MovieDB.MovieController), icon: "icon-camcorder")]

[assembly: NavigationMenu(8000, "Northwind", icon: "icon-anchor")]
[assembly: NavigationLink(8200, "Northwind/Customers", 
    typeof(Northwind.CustomerController), icon: "icon-wallet")]
[assembly: NavigationLink(8300, "Northwind/Products", 
    typeof(Northwind.ProductController), icon: "icon-present")]
// ...
```

Bu arada, bir de *film* ikonuna sahip yeni bir navigasyon menüsü (Movie Database) tanımladık. Açıkça tanımlanmış bir navigasyon menünüz olmadığında, Serenity dolaylı olarak bir tane oluşturur, fakat bu durumda menünün sırasını ya da ikonunu düzenleyemezsiniz.

Menüye gösterim sırası olarak *2000* atadık, yani *Dashboard* bağlantısından (1000) hemen sonra, aman Northwind (8000) menüsünden önce gözükecek.

*Movies* bağlantımıza ise gösterim sırası olarak *2100* atadık, ancak şimdilik bunun bir önemi yok, çünkü *Movie Database* altında tek bir menü elemanımız var.

> İlk seviye bağlantılar ve navigasyon menüleri öncelikle kendi aralarında gösterim sıra değerlerine göre sıralanır, ardından ikinci seviye bağantılar kardeşleri (siblings) arasında sıralanır.

Bu değişiklikten sonra şu şekilde gözükmeli:

![Movie Database Menüsü Taşındı](img/movies_navigation_moved.png)


### Visual Studio ile Yaşanan Bazı Sorunların Çözülmesi

Eğer hala farketmediyseniz, Visual Studio siteniz çalışırken kodunu düzenlemenize izin vermez. Hata ayıklamayı (debug) durdurduğunuzda ise sitenizde durur. Bu nedenle tarayıcı pencerenizi açık tutup, derleme sonrasında yenileyemezsiniz.

Bu sorunu çözmek için, *Edit And Continue (Düzenle ve Devam Et)* özelliğini kapatmamız gerekmekte (nedendir bilinmez).

*MovieTutorial.Web* projesine sağ tuşla tıklayın, *Properties (Özellikler)* e tıklayın, Web sekmesinde, *Debugger (Hata Ayıklayıcı) altındaki *Enable Edit And Continue (Düzenle ve Devam Eti Etkinleştir)* seçeneğinin işaretini kaldırın.

Ayrıca, sayfanın tepesindeki mavi ilerleme çubuğu (ki bu bir Pace.js animasyonudur), sanki sürekli birşey yükleniyormuşcasına dönüp durur. Bu da Visual Studio'nun *Browser Link (Tarayıcı Bağlantısı)* özelliğinden kaynaklanmaktadır. Özelliği devre dışı bırakmak için, Visual Studio araç çubuğundaki yenile düğmesine benzer simgesini bulun (Chrome gibi bir tarayıcı ismi yazan çalıştırma düğmesinin hemen yanındaki), çekme menüye tıklayın ve *Enable Browser Link (Tarayıcı Bağlantısını Etkinleştir)* seçeneğinin işaretini kaldırın.

Bu özelliği ayrıca web.config te yapılan bir ayar ile de kapatmak mümkün: 

```xml
<appsettings>
    <add key="vs:EnableBrowserLink" value="false" />
</appsettings>
```

> Serene 1.5.4 ve sonrası bu seçeneği varsayılan olarak web.config'te içerdiğinden, bahsi geçen sorunla karşılaşmamış olabilirsiniz.