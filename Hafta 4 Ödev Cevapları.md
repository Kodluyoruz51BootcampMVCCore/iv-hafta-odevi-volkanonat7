### ViewBag 

ViewBag ASP.NET MVC3 te C# 4 ile gelen dynamic anahtar kelimesinin getirdiği bir yeniliktir. ViewData’nın dinamik(run time binding) halidir.

```c#
ViewBag.SuccessMessage = "Success!"; //Controller

<h3>@ViewBag.SuccessMessage</h3> //View
```



### ViewData

Controller sınıfları ile View sayfalları arasında ViewDataDictionary(ViewData) sınıfı ile taşınmaktadır. ViewData nesnelerine veri aktarıp, okuyabiliriz.

```c#
ViewData["success"]="success" //controller;

<h3>@ViewData["success"]</h3> //View
```

#### ViewBag vs ViewData vs TempData

Bu üç nesne arasındaki küçük ve kritik farklar vardır. Örneğin ViewBag nesnesi dynamic tipinde bir nesne olduğundan bununla alakalı hatalar compile time’da değil run time da yakalanır. Teknik anlamda ViewData nesnesinden farkı yoktur. Sözdizim olarak farklıdır.

En büyük ve önemli fark TempData ile diğer ikisi arasındadır. ViewData ve ViewBag nesnesi o anki HTTP istek içerisinde geçerlidir. Yaşam döngüsü bir sonraki isteğe kadardır. Ama TempData bir alt HTTP istek içinde geçerlidir. Yaşam döngüsü o anki ve bir sonraki HTTP istek içerinde geçerlidir.

#### AddMVC - AddMVCCore - AddDateAnnotations nedir?

- AddMVC : Mvc hizmetlerini ekler.Startup.cs içine yazılabilir.
- AddMVCCore : MVC uygulamasının çalışması için gereken tüm temel hizmetleri sağlar.Bazı yaptığı hizmetler:controller activation services,action constraints,filter pipeline... Gelen bir HTTP çağrısını işleyebilme açısından işlevseldir, ancak birkaç temel özelliği yoktur. Örneğin, veri yoluyla model doğrulaması, yetkilendirmeyle aynı şekilde etkinleştirilmez.Startup.cs içine yazılabilir.
- AddDateAnnotations : MVC uygulamasında veri tabanı tablolarını Code First yöntemi ile oluşturmaya başladığımızda yapılan validasyon işlemlerine Data Annotations denir.

### SnapShot Nedir, Neden Kullanılır ?

Projemize Migrations eklemeye başladığımızda Solution içerisinde yeni bir dosya olduğuna dikkat etmemiz gerekir. Bu dosya <DbContext>Snapshot.cs  olarak adlandırılır. Bu dosya hazırda olan şemanın anlık bir görüntüsünü içerir. Modelinizi veritabanı şemasına çeviren tek bir sınıf gibidir. Entity FrameWork bir Migrations oluşturduktan sonra, ilk önce data model ve snapshot dosyası arasındaki farkı aramaya başlar. Ayrıca Migration'ı remove etmek istediğimizde de kullanılır.

Snapshot ekipler için oldukça önemlidir. Aynı data model üzerinden çalışan birden fazla programcı düşünün. Herkes birçok değişiklik yapabilir model üzerinde, bu değişiklikler arasındaki farklılıkları senkronize etmenin bir yolu olması gereklidir. Bunu snapshot ile senkronize edebiliriz. Fakat bu çoğu zaman işe yarasa da her zaman doğru şekilde çalışmasını beklemek hata olur. Bu değişiklikleri her zaman senkronize edebilmek imkansızdır. Örneğin; bir kişi bir sütunun adını değiştirirken diğer kişi o sütunu komple silebilir. 

#### Jquery Calender--> [Datapicker](https://jqueryui.com/datepicker/) --> DueAt'i takvim tipinde eklemek nasıl yapılır?

- Solution Explorer> dependencies kısmından sağ tıklayıp Manage Nuget Package e tıklayarak arama kısmından FullCalendar eklentisini bulup seçiyoruz ve install ediyoruz.

- Takvim üzerine verileri çekebilmek için models klasörüne class ekliyoruz.

- ```c#
  namespace FullCalendar.Models {
      public class CalendarEvent {
          public int id { get; set; }
          public string title { get; set; }
          public string start { get; set; }
          public string end { get; set; }
          public string color { get; set; }
          public bool allDay { get; set; }
      }
  }
  ```

- Conrollers klasörüne yeni bir class ekliyoruz.

- ```c#
  namespace FullCalendar.Controllers
  {
      public class CalendarController : Controller
      {
          //
          // GET: /Calendar/
  
          public ActionResult Index()
          {
  
              return View();
          }
  
          public JsonResult GetCalendarEvents() {
              List<CalendarEvent> eventItems = new List<CalendarEvent>();
              int i = 0, n = 9;
              for (i = 0; i < n; i++) {
                  AddItem(eventItems);
              }
  
              return Json(eventItems, JsonRequestBehavior.AllowGet);
          }
  
          Random rnd = new Random();
          public void AddItem(List<CalendarEvent> eventItems) {
              CalendarEvent item = new CalendarEvent();
  
              DateTime startDate = new DateTime(DateTime.Now.Year, DateTime.Now.Month, random.Next(1, 30));
  
              item.id = rnd.Next(1, 100);
              item.start = startDate.ToString("s");
              item.end = startDate.AddDays(rnd.Next(1, 5)).ToString("s");
              item.allDay = true;
              item.color = "red";
              item.title = "Calendar Item " + item.id;
              eventItems.Add(item);
          }
  
      }
  }
  ```

- Burdaki işlem CalenderEvent nesnesine 10 elemanlı bir liste oluşturmak.

Bu listeyi takvime çekmek için:

- Viewa index sayfamızı oluşturuyoruz.

```c#
<!DOCTYPE html>

<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Index</title>

    <link href="~/Content/fullcalendar.min.css" rel="stylesheet" />
</head>
<body>
    <div id="calendar">

    </div>

    <script src="~/Scripts/jquery-2.0.0.min.js"></script>
    <script src="~/Scripts/moment.min.js"></script>
    <script src="~/Scripts/moment-with-locales.min.js"></script>
    <script src="~/Scripts/fullcalendar/fullcalendar.min.js"></script>
    <script src="~/Scripts/fullcalendar/locale/tr.js"></script>
    <script src="~/Scripts/fullcalendar/locale-all.js"></script>

    <script>
        $(document).ready(function () {
            GetCalendarEvents();
        });

        function GetCalendarEvents() {
            debugger;
            $('#calendar').fullCalendar({
                lang: 'tr',
                header: {
                    left: 'prev,next today',
                    center: 'title',
                    right: 'month,agendaWeek,agendaDay'
                },
                editable: true,
                events: '/Calendar/GetCalendarEvents/',
                eventClick: function (event) {
                    alert('Item ID: ' + event.id  + "\nItem Title: " + event.title);
                }
            });
        }
    </script>

</body>
</html>
```

#### First- FirstOrDefault ve Single- SingleOrDefault nedir? Aralarındaki farkı araştırınız.

- First : Dönen değerlerden ilkini getirir yoksa hata verir.
- FirstOrDefault : Dönen değerlerden ilkini getirir yoksa Hata olarak tipin varsayılan değeri döndürür.
- Single : Tek bir değer dönüyorsa kullanırız. Birden fazla değer dönüyorsa hata verir.
- SingleOrDefault : Tek bir değer dönüyorsa kullanırız. Hata olarak tipin varsayılan değeri döndürür.

#### En Kısa Null Check Nasıl Yapılır ?

IsNullOrEmpty() methodu ile kontrol sağlanır. Bool tipinde bir değer döndürür.

#### Partial View Nedir ?

Bir işlemi birden fazla kez yapacaksak bir kalıp kullanırız. Öğreğin oluşturacağımız bir resim galerisini web sitesinde birden fazla sayfada kullanacağımızı düşünelim. Aynı galeriyi her sayfa için tekrar tekrar oluşturmak gereksiz ve zaman kaybıdır. Tam da burada Partial View imdadımıza yetişiyor. Partial View kendi başına hiçbir işlevi olmayan bir yapıdır. Bulunduğu sayfa içerisinde çalışır. Asp.Net Web Forms mimarisinde ki karşılığı User Control’dür.

#### Authentication

- Veri güvenliği tabiridir. Herhangi bir internet kullanıcısının , uygulamanın veya programın sisteme erişirken kim olduğunu kanıtlama işlemidir.
- Kimlik doğrulama kavramı ile aynı şeydir.
- Kullanıcının girdiği kimlik bilgileri giriş yapmak istediği sistemdeki veri tabanında bulunan bilgilerle karşılaştırılır bilgiler eşleştiğinde giriş izni verilir.

###### Temel Kimlik Dogrulaması

- HTTP temel kimlik doğrulaması sunucunun istemciden kimlik bilgilerini istediği basit bir doğrulama şeklidir.

###### Form Kimlik Dogrulaması

- Doğrulama işlemi için tarayıcıda bulanan çerezleri kullanır.
- Çerez bilgisi yok ise kullanıcı sisteme girmek zorundadır.
- Giriş yapmış bir kullanıcının çerezlerin geçerlilik süresi dolduğunda kullanıcı tekrar giriş sayfasına döner.

###### OAuth 2

- Web uygulamalarının Facebook, GtiHub gibi hizmetlerdeki kullanıcı hesaplarına sınırlı olarak erişim elde etmeyi sağlar.
- Kullanıcının, kimlik doğrulamasını kullanıcı hesabını barındıran hizmete devredip üçüncü taraf uygulamalarının hesabına erişmesini izin vermesi ile çalışır.

###### Identity Framework

- Genişletilebilir ve özelleştirilebilir bir yapıya sahiptir.
- Facebook, Google servisleri, Twitter üzerinden üyelik gerçekleştirmeyi sağlar.
- Azure ile kolay entegre olur.
- Güncellenebilir ve yönetilebilir.

#### Razor Pages vs MVC

- Razor pages ve MVC' nin dosya gruplandırması farklıdır.

- MVC de işlevsellik önemlidir ve dosyalar işlevine göre sınıflandırılır.

- Razor Pages ise dosyaları çözdüğü soruna ve amaca göre sınıflandırır.

- Razor Pages sayfaları MVC kullanan herhangi bir ASP .NET Core uygulamasına otomatik olarak dahil edilir.

- MVC' yi Core Web uygulamasına AddMvc () yöntemi ile dahil ederiz.

  ```c#
  public class Startup
  {
      public Startup(IConfiguration configuration)
      {
          Configuration = configuration;
      }
  
      public IConfiguration Configuration { get; }
  
      public void ConfigureServices(IServiceCollection services)
      {
          services.AddMvc();
      }
  ```

- Razor Pages, yapı bakımından ASP .Net Web formlarına benzer.
- Razor Pages MVC'ye göre daha organize diyebiliriz.

