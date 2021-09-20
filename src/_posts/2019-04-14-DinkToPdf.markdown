---
layout: post
title:  "DinkToPdf"
date:   2019-04-14 19:37:58 +0200
categories: docker
---

### DinkToPdf in Docker container - dotnet core 2.2


I was busy with a project and I was required to create a pdf "report" in the project 
and then serve it to a user through the Angular app. So as with all things I created a POC.

Once I got the POC working then I decided to get ready to deploy so naturally I put it into a container.
Thats when things got interesting, after googling around, I found a solution and here is what I came up with:


### Prep

1. Create the development space.

``` 
mkdir PdfDemoSpace
cd PdfDemoSpace 
```

### Dotnet

2. Create the dotnet webapi project

```dotnet new webapi --name PdfDemo``` 

### Dependencies Management

3. Add Dependencies for production

```Copy the lib folder found the git repository: ```
[DinkToPdf-DotnetCore-Docker](https://github.com/leeroya/DinkToPdf-DotnetCore-Docker)

4. Add Dependencies for development

```
cd PdfDemo

dotnet add package DinkToPdf 

dotnet restore
 
```

### Coding

5. Startup.cs

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    services.AddSingleton(typeof(IConverter), new SynchronizedConverter(new PdfTools()));
}
```

6. Adding the dll's required by the package into the output folder:

```
<ItemGroup Condition=" '$(CONFIGURATION)' == 'Debug' ">
    <ContentWithTargetPath Include="..\lib\64bit\libwkhtmltox.dll">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        <TargetPath>libwkhtmltox.dll</TargetPath>
    </ContentWithTargetPath>
</ItemGroup>
    
<ItemGroup Condition=" '$(CONFIGURATION)' == 'Release' ">
    <ContentWithTargetPath Include="..\lib\64bit\libwkhtmltox.dll">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        <TargetPath>libwkhtmltox.dll</TargetPath>
    </ContentWithTargetPath>
</ItemGroup>
```
7. Inject the IConverter into the controller

```
private IConverter _converter;
 
public ValuesController(IConverter converter)
{
    _converter = converter;
}
```

8. Update the get action method to work with the directory and do some demo work:

```
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            if (!Directory.Exists(Path.Combine(Directory.GetCurrentDirectory(), "reports")))
                Directory.CreateDirectory(Path.Combine(Directory.GetCurrentDirectory(), "reports"));

            if(!Directory.Exists(Path.Combine(Directory.GetCurrentDirectory(), "reports", "instanceId")))
                Directory.CreateDirectory(Path.Combine(Directory.GetCurrentDirectory(), "reports", "instanceId"));
            
            var globalSettings = new GlobalSettings
            {
                ColorMode = ColorMode.Color,
                Orientation = Orientation.Portrait,
                PaperSize = PaperKind.A4,
                Margins = new MarginSettings { Top = 10 },
                DocumentTitle = "PDF Report",
                Out = $@"{Path.Combine(Directory.GetCurrentDirectory(), "reports","instanceId")}/Employee_Report.{Guid.NewGuid()}.pdf"
            };
            
            var objectSettings = new ObjectSettings
            {
                PagesCount = true,
                HtmlContent = TemplateGenerator.GetHTMLString(),
                WebSettings = { DefaultEncoding = "utf-8", UserStyleSheet =  Path.Combine(Directory.GetCurrentDirectory(), "assets", "styles.css") },
                HeaderSettings = { FontName = "Arial", FontSize = 9, Right = "Page [page] of [toPage]", Line = true },
                FooterSettings = { FontName = "Arial", FontSize = 9, Line = true, Center = "Report Footer" }
            };
 
            var pdf = new HtmlToPdfDocument()
            {
                GlobalSettings = globalSettings,
                Objects = { objectSettings }
            };
 
            _converter.Convert(pdf);
            
            
            var memory = new MemoryStream();  
            using (var stream = new FileStream(globalSettings.Out, FileMode.Open))  
            {  
                 await stream.CopyToAsync(memory);  
            }  
            memory.Position = 0; 
            return File(memory, GetContentType(globalSettings.Out), Path.GetFileName(globalSettings.Out)); 
        }
        
        private string GetContentType(string path)  
        {  
            var types = GetMimeTypes();  
            var ext = Path.GetExtension(path).ToLowerInvariant();  
            return types[ext];  
        }

        private Dictionary<string, string> GetMimeTypes()
        {
            return new Dictionary<string, string>
            {
                {".txt", "text/plain"},
                {".pdf", "application/pdf"},
                {".doc", "application/vnd.ms-word"},
                {".docx", "application/vnd.ms-word"},
                {".xls", "application/vnd.ms-excel"},
                {".xlsx", "application/vnd.openxmlformatsofficedocument.spreadsheetml.sheet"},  
                {".png", "image/png"},
                {".jpg", "image/jpeg"},
                {".jpeg", "image/jpeg"},
                {".gif", "image/gif"},
                {".csv", "text/csv"}
            };
        }
        
``` 
Some Demo data:

``` 
    public class DataStorage
    {
        public List<Employee> GetAllEmployess()
        {
            return new List<Employee>
            {
                new Employee { Name="Mike", LastName="Turner", Age=35, Gender="Male"},
                new Employee { Name="Sonja", LastName="Markus", Age=22, Gender="Female"},
                new Employee { Name="Luck", LastName="Martins", Age=40, Gender="Male"},
                new Employee { Name="Sofia", LastName="Packner", Age=30, Gender="Female"},
                new Employee { Name="John", LastName="Doe", Age=45, Gender="Male"}
            };
        }
    }
``` 

A Demo object to work with:

``` 
    public class Employee
    {
        public string Name { get; set; }
        public string LastName { get; set; }
        public int Age { get; set; }
        public string Gender { get; set; } 
    }
``` 

Create a template generator class

``` 
    public class TemplateGenerator
    {
        public static string GetHTMLString()
        {
            var employees = new DataStorage().GetAllEmployess();
 
            var sb = new StringBuilder();
            sb.Append(@"
                        <html>
                            <head>
                            </head>
                            <body>
                                <div class='header'><h1>This is the generated PDF report!!!</h1></div>
                                <table align='center'>
                                    <tr>
                                        <th>Name</th>
                                        <th>LastName</th>
                                        <th>Age</th>
                                        <th>Gender</th>
                                    </tr>");
 
            foreach (var emp in employees)
            {
                sb.AppendFormat(@"<tr>
                                    <td>{0}</td>
                                    <td>{1}</td>
                                    <td>{2}</td>
                                    <td>{3}</td>
                                  </tr>", emp.Name, emp.LastName, emp.Age, emp.Gender);
            }
 
            sb.Append(@"
                                </table>
                            </body>
                        </html>");
 
            return sb.ToString();
        }
    }
``` 

### Docker

9. The Dockerfile

``` 
FROM microsoft/dotnet:2.2-sdk AS build-env
WORKDIR /app
COPY . ./
RUN dotnet publish PdfDemo.sln -c Release -o out


FROM microsoft/dotnet:2.2-aspnetcore-runtime

RUN apt update
RUN apt install -y libgdiplus
RUN ln -s /usr/lib/libgdiplus.so /lib/x86_64-linux-gnu/libgdiplus.so
RUN apt-get install -y --no-install-recommends zlib1g fontconfig libfreetype6 libx11-6 libxext6 libxrender1 wget gdebi
RUN wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.stretch_amd64.deb
RUN gdebi --n wkhtmltox_0.12.5-1.stretch_amd64.deb
RUN apt install libssl1.0-dev
RUN ln -s /usr/local/lib/libwkhtmltox.so /usr/lib/libwkhtmltox.so
WORKDIR /app

COPY --from=build-env /app/PdfDemo/out .
COPY lib/64bit .
ENTRYPOINT ["dotnet", "PdfDemo.dll"]
``` 

### Conclusion

This is not a optimised solution but highlights the dependencies required and a basic workflow 
in getting the items working together.
