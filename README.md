# SSC-Internship-Dev-ASP.NET-Application-Setting-Configuration
# Hướng Dẫn Về Application Setting & Configuration Trong ASP.NET Core
## Giới Thiệu
Cấu hình ứng dụng là một phần quan trọng trong bất kỳ ứng dụng nào, đặc biệt là khi bạn cần quản lý các giá trị như kết nối cơ sở dữ liệu, thông số API, hoặc bất kỳ thông tin cấu hình nào khác mà bạn muốn thay đổi mà không cần sửa đổi mã nguồn. Trong ASP.NET Core, việc quản lý cấu hình được thực hiện thông qua các tệp cấu hình như `appsettings.json` và môi trường biến. Bài viết này sẽ hướng dẫn chi tiết cách thiết lập và sử dụng Application Setting & Configuration trong ASP.NET Core.

Tổng Quan Về Application Setting & Configuration

ASP.NET Core cung cấp một hệ thống cấu hình mạnh mẽ và linh hoạt, bao gồm:

1. Tệp `appsettings.json`: Tệp cấu hình chính được sử dụng để lưu trữ các giá trị cấu hình.
2. Environment Variables: Biến môi trường cho phép bạn ghi đè các giá trị cấu hình dựa trên môi trường cụ thể (Development, Staging, Production).
3. User Secrets: Lưu trữ thông tin nhạy cảm như API keys hoặc connection strings trong quá trình phát triển mà không phải đẩy lên source control.
4. Options Pattern: Cách sử dụng các lớp POCO để ánh xạ các giá trị cấu hình.

## 1. Sử Dụng Tệp appsettings.json
Tệp `appsettings.json` là nơi bạn có thể định nghĩa các giá trị cấu hình cơ bản cho ứng dụng của mình.

Ví Dụ: `appsettings.json` Cơ Bản

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=aspnet-MyApp;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
  "MySettings": {
    "AppName": "My ASP.NET Core App",
    "Version": "1.0.0"
  }
}
```
Trong ví dụ này:

- `ConnectionStrings`: chứa chuỗi kết nối tới cơ sở dữ liệu.
- `MySettings`: chứa các cấu hình tùy chỉnh như tên ứng dụng và phiên bản.

Cách Sử Dụng `appsettings.json` Trong Mã

Để truy cập các giá trị từ `appsettings.json`, bạn có thể sử dụng Dependency Injection để đưa `IConfiguration` vào bất kỳ dịch vụ hoặc controller nào.

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _configuration;

    public HomeController(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public IActionResult Index()
    {
        var appName = _configuration["MySettings:AppName"];
        var version = _configuration["MySettings:Version"];

        ViewBag.Message = $"Welcome to {appName} - Version {version}";
        return View();
    }
}
```
## 2. Biến Môi Trường (Environment Variables)
ASP.NET Core cho phép bạn dễ dàng ghi đè các cấu hình trong `appsettings.json` bằng cách sử dụng biến môi trường.

Ví Dụ: Thiết Lập Biến Môi Trường

Giả sử bạn muốn ghi đè giá trị `AppName` trong môi trường sản xuất:

```bash
export MySettings__AppName="My Production App"
```
Trong mã, ASP.NET Core sẽ tự động sử dụng giá trị này nếu biến môi trường được thiết lập.

## 3. User Secrets (Bí Mật Người Dùng)
Trong quá trình phát triển, bạn có thể sử dụng `User Secrets` để lưu trữ thông tin nhạy cảm mà không phải đẩy lên source control.

Thiết Lập User Secrets

1. Chuột phải vào dự án trong Visual Studio > Manage User Secrets.
2. Tệp `secrets.json` sẽ mở ra, và bạn có thể thêm các cấu hình bí mật tại đây.
Ví Dụ: `secrets.json`

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=myserver;Database=mydb;User=myuser;Password=mypassword;"
  }
}
```
## 4. Sử Dụng Options Pattern
Options Pattern cho phép bạn ánh xạ các giá trị cấu hình vào các lớp POCO (Plain Old CLR Objects).

Tạo Lớp Cấu Hình

```csharp
public class MySettings
{
    public string AppName { get; set; }
    public string Version { get; set; }
}
```
Đăng Ký Lớp Cấu Hình Trong `Program.cs`

```csharp
builder.Services.Configure<MySettings>(builder.Configuration.GetSection("MySettings"));
```
Sử Dụng Lớp Cấu Hình Trong Mã

```csharp
public class HomeController : Controller
{
    private readonly MySettings _mySettings;

    public HomeController(IOptions<MySettings> mySettings)
    {
        _mySettings = mySettings.Value;
    }

    public IActionResult Index()
    {
        ViewBag.Message = $"Welcome to {_mySettings.AppName} - Version {_mySettings.Version}";
        return View();
    }
}
```
## 5. Tổng kết
Quản lý cấu hình trong ASP.NET Core là một nhiệm vụ quan trọng và cần thiết để phát triển các ứng dụng linh hoạt và bảo mật. Việc hiểu rõ cách làm việc với `appsettings.json`, biến môi trường, user secrets và sử dụng Options Pattern sẽ giúp bạn dễ dàng tùy chỉnh và mở rộng ứng dụng của mình. Trong bài viết tiếp theo, chúng ta sẽ đi sâu vào Middlewares – một trong những thành phần quan trọng trong pipeline xử lý yêu cầu của ASP.NET Core.
