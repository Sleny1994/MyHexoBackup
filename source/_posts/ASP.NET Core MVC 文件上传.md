---
title: ASP.NET Core MVC 文件上传
date: 2023-08-22
tags:
- CSharp
- NET
categories:
- CSharp
---

在实际应用中，文件上传是非常常用的功能，主要分为单文件上传、多文件上传、文件与其它内容混合上传、大文件上传

## IFormFile
- 表示使用HttpRequest发送的文件，可用于文件流的接收，是用于处理或保存文件的C#表示形式
- 文件上传使用的磁盘和内存取决于并发文件上传的数量和大小
- 如果尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃
- 如果文件上传的大小或频率会消耗应用资源，请使用流式传输
- 对于小文件的上传，一般采用IFormFile；大文件的上传，采用流式上传
### 属性
- ContentDisposition：获取已上传文件的原始Content-Disposition标头
- ContentType：获取已上传文件的原始Content-Type标头
- FileName：从Content-Disposition标头获取文件名
- Headers：获取已上传文件的标头字典
- Length：获取文件长度（以字节为单位）
- Name：从Content-Disposition标头获取表单字段名称
### 方法
- CopyTo(Stream)：将上传的文件的内容复制到流中target
- CopyToAsync(Stream, CancellationToken)：将上传的文件的内容异步复制到target流中
- OpenReadStream()：打开请求流以读取上传的文件

## 单个文件上传
1. 文件上传视图
- 文件上传通过form表单，采用post方式，加密类型为multiparty/form-data
- 文件上传采用input控件，类型为file
```c#
<form method="post" enctype="multipart/form-data" action="/File/OneFileUpload">
    <h1>单文件上传</h1>
    <div>
        <span>文件：</span>
        <input type="file" name="file" />
    </div>
    <input type="submit" value="上传" />
</form>
```
2. 后台处理方法
```c#
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;

namespace MvcDemo.Controllers
{
    public class FileController : Controller
    {
        // 注入IWebHostEnvironment类型,获取站点信息接口
        private readonly IWebHostEnvironment _webHostEnvironment;

        public FileController(IWebHostEnvironment webHostEnvironment)
        {
            _webHostEnvironment = webHostEnvironment;
        }

        public IActionResult Index()
        {
            return View();
        }

        public IActionResult OneFileUpload(IFormFile file)
        {
            var path = Path.Combine(_webHostEnvironment.ContentRootPath, "uploads", string.Format("{0}_{1}", DateTime.Now.Ticks, file.FileName));
            using (FileStream fs = new FileStream(path, FileMode.Create))
            {
                file.CopyTo(fs);
            }
            return Ok("上传成功");
        }
    }
}
```
- 方法中的参数IFormFile file用于接收客户端上传的文件，其它file和视图中上传控件的name一一对应，如果错误则无法上传
- _webHostEnvironment为控制器通过接口注入的IWebHostEnvironment类型的获取站点信息接口，主要用于获取站点根目录
- 调用IFormFile的CopyTo方法进行保存，此方法接收Stream类型的参数
- 出现Could not found a part...时，说明文件的路径存在问题，或许是某个文件夹未创建，当路径不存在时，程序不会自动创建

## 多文件上传
1. 多文件上传视图
```c#
// Views/Form.cshtml
<form method="post" enctype="multipart/form-data" action="/File/MoreFileUpload">
    <h1>多文件上传</h1>
    <div>
        <span>文件：</span>
        // input空间在类型为file时表示文件上传，默认是单文件；通过设置multiple属性实现多文件上传
        <input type="file" name="files" multiple />
    </div>
    <input type="submit" value="上传" />
</form>
```
2. 多文件后台处理方法
```c#
public IActionResult MoreFileUpload(IFormFile[] files)
{
    foreach (var file in files)
    {
        var path = Path.Combine(_webHostEnvironment.ContentRootPath, "uploads", string.Format("{0}_{1}", DateTime.Now.Ticks, file.FileName));
        using (FileStream fs = new FileStream(path, FileMode.Create))
        {
            file.CopyTo(fs);
        }
    }

    return Ok("上传成功");
}
```
- 多个文件上传，参数为IFormFile数组类型，可以接收上传文件列表，然后循环获取并进行保存

## 文件文本混合上传
实际应用中，很多时候会出现还需要搭配其它文本说明，如录入产品信息，并上传附件等
1. 创建模型
```c#
// Models/Product.cs
namespace MvcDemo.Models
{
    public class Product
    {
        public string Name { get; set; }

        public IFormFile File { get; set; }
    }
}
```
2. 视图绑定模型
```c#
// Views/Form.cshtml
@model MvcDemo.Models.Product
```
3. 混合文本文件上传视图
```c#
// Views/Form.cshtml
<form method="post" enctype="multipart/form-data" action="/File/FileWithContentUpload">
    <h1>文件文本混合上传</h1>
    <div>
        <span>名称：</span>
        <input type="text" name="Name"  />
    </div>
    <div>
        <span>文件：</span>
        <input type="file" name="File" />
    </div>
    <input type="submit" value="上传" />
</form>
```
4. 后台处理方法
文件文本混合上传，参数为模型Product，通过属性匹配接收参数，获取属性File对应的内存流进行保存
```c#
// Controllers/FileController.cs
// 文件内容混合上传
public IActionResult FileWithContentUpload(Product product)
{
    var file = product.File;
    var path = Path.Combine(_webHostEnvironment.ContentRootPath, "uploads", string.Format("{0}_{1}", DateTime.Now.Ticks, file.FileName));
    using (FileStream fs = new FileStream(path, FileMode.Create))
    {
        file.CopyTo(fs);
    }
    return Ok($"{product.Name} 上传成功");
}
```

## 大文件上传
大文件如何界定，没有统一的标准，根据官网的参数说明：
- 默认情况下，HttpRequest.Form不会缓冲整个请求正文（BufferBody），但会缓冲包含的任何多部分表单文件
- MultipartBodyLengthLimit是缓冲表单文件的最大大小，默认为128MB
- MemoryBufferThreshold指在转换为磁盘上的缓冲区文件之前，内存中的文件缓冲量，默认为64KB
- MemoryBufferThreshold充当小型和大型文件之间的边界，这些文件根据应用资源和方案而引发或降低
- 大文件采用流式传输，可降低上传文件时对内存和存储空间的需求
1. 创建视图
```c#
// Views/Form.cshtml
<form method="post" enctype="multipart/form-data" action="/File/BigFileUpload">
    <h1>大文件上传</h1>
    <div>
        <span>文件：</span>
        <input type="file" name="file" />
    </div>
    <input type="submit" value="上传" />
</form>
```
2. 后台处理办法
```c#
// 创建大文件上传帮助类MultiparRequestHelper
using System;
using System.IO;
using Microsoft.Net.Http.Headers;
namespace MvcDemo
{
    public static class MultipartRequestHelper
    {
        // Content-Type: multipart/form-data; boundary="----WebKitFormBoundarymx2fSWqWSd0OxQqq"
        public static string GetBoundary(MediaTypeHeaderValue contentType, int lengthLimit)
        {
            var boundary = HeaderUtilities.RemoveQuotes(contentType.Boundary).Value;

            if (string.IsNullOrWhiteSpace(boundary))
            {
                throw new InvalidDataException("Missing content-type boundary.");
            }

            if (boundary.Length > lengthLimit)
            {
                throw new InvalidDataException(
                    $"Multipart boundary length limit {lengthLimit} exceeded.");
            }

            return boundary;
        }

        public static bool IsMultipartContentType(string contentType)
        {
            return !string.IsNullOrEmpty(contentType)
                   && contentType.IndexOf("multipart/", StringComparison.OrdinalIgnoreCase) >= 0;
        }

        public static bool HasFileContentDisposition(ContentDispositionHeaderValue contentDisposition)
        {
            // Content-Disposition: form-data; name="myfile1"; filename="Misc 002.jpg"
            return contentDisposition != null
                && contentDisposition.DispositionType.Equals("form-data")
                && (!string.IsNullOrEmpty(contentDisposition.FileName.Value)
                    || !string.IsNullOrEmpty(contentDisposition.FileNameStar.Value));
        }

        // 如果一个section的Header是： Content-Disposition: form-data; name="myfile1"; filename="F:\Misc 002.jpg"
        // 那么本方法返回： Misc 002.jpg
        public static string GetFileName(ContentDispositionHeaderValue contentDisposition)
        {
            return Path.GetFileName(contentDisposition.FileName.Value);
        }

    }
}
```
处理方法BigFileUploadAsync的说明：</br>
- Action中要进行DisableRequestSizeLimit特性说明，否则会有大小限制，[InvalidDataException: Multipart body length limit 16384 exceeded]
- 使用MultipartReader读取窗体的内容，将会读取每个单独的MultipartSection，根据需要处理文件或存储内容
- 读取多部分节后，该操作会执行自己的模型绑定
```c#
// Controllers/FileController.cs
/// 大文件上传
[DisableRequestSizeLimit]
public async Task<IActionResult> BigFileUploadAsync()
{
    var contentType = Request.ContentType;
    if (!MultipartRequestHelper.IsMultipartContentType(contentType))
    {
        ModelState.AddModelError("File",
            $"上传文件类型不对.");
        return BadRequest(ModelState);
    }
    var path = Path.Combine(_webHostEnvironment.ContentRootPath, "uploads");

    var boundary = MultipartRequestHelper.GetBoundary(MediaTypeHeaderValue.Parse(Request.ContentType), _defaultFormOptions.MultipartBoundaryLengthLimit);

    var reader = new MultipartReader(boundary, HttpContext.Request.Body);

    var section = await reader.ReadNextSectionAsync();

    while (section != null)
    {
        var hasContentDispositionHeader =
            ContentDispositionHeaderValue.TryParse(
                section.ContentDisposition, out var contentDisposition);

        if (hasContentDispositionHeader)
        {
            if (!MultipartRequestHelper.HasFileContentDisposition(contentDisposition))
            {
                ModelState.AddModelError("File",
                    $"The request couldn't be processed (Error 2).");

                return BadRequest(ModelState);
            }
            else
            {
                var fileName = MultipartRequestHelper.GetFileName(contentDisposition);
                var loadBufferBytes = 1024;
                //这个是每一次从Http请求的section中读出文件数据的大小，单位是Byte即字节，这里设置为1024的意思是，每次从Http请求的section数据流中读取出1024字节的数据到服务器内存中，然后写入下面targetFileStream的文件流中，可以根据服务器的内存大小调整这个值。这样就避免了一次加载所有上传文件的数据到服务器内存中，导致服务器崩溃。

                using (var targetFileStream = new FileStream(path + "\\" + string.Format("{0}_{1}", DateTime.Now.Ticks, fileName), FileMode.Create, FileAccess.ReadWrite))
                {
                    using (section.Body)
                    {
                        //section.Body是System.IO.Stream类型，表示的是Http请求中一个section的数据流，从该数据流中可以读出每一个section的全部数据，所以我们下面也可以不用section.Body.CopyToAsync方法，而是在一个循环中用section.Body.Read方法自己读出数据（如果section.Body.Read方法返回0，表示数据流已经到末尾，数据已经全部都读取完了），再将数据写入到targetFileStream
                        await section.Body.CopyToAsync(targetFileStream, loadBufferBytes);
                    }
                }
            }
        }
        section = await reader.ReadNextSectionAsync();
    }
    return Ok("上传成功");
}
```
【注】：
- 在文件上传功能中，上传后的文件一般都要进行重命名，否则如果客户端上传相同名称额文件，则可能会被覆盖
- 本例中，在原文件名前添加了时间戳，减少重复率

## 文件上传校验
1. 文件扩展名验证：应在允许的扩展名列表中查找上传的文件的扩展名
2. 文件签名验证：文件的签名由文件开头部分中的前几个字节确定，可以使用这些字节指示扩展名是否与文件内容匹配，示例应用检查一些常见文件类型的文件签名。
3. 文件名安全：切勿使用客户端提供的文件名来将文件保存到物理存储。
4. 文件大小验证：限制上传的文件的大小。

## 文件上传安全
1. 将文件上传到专用文件上传区域，最好是非系统驱动器。使用专用位置便于对上传的文件实施安全限制，禁用对文件上传位置的执行权限。
2. 请勿将上传的文件保存在与应用相同的目录树中。
3. 使用应用确定的安全的文件名。请勿使用用户提供的文件名或上传的文件的不受信任的文件名。当显示不受信任的文件名时 HTML 会对它进行编码，例如，记录文件名或在 UI 中显示（Razor 自动对输出进行 HTML 编码）。
4. 按照应用的设计规范，仅允许已批准的文件扩展名。
5. 验证是否对服务器执行客户端检查，客户端检查易于规避。
6. 检查已上传文件的大小，设置一个大小上限以防止上传大型文件。
7. 文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。
8. 先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。