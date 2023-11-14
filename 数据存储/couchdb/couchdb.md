https://www.w3cschool.cn/couchdb/

使用HTTP请求头，你可以使用CouchDB沟通。

- **GET** -这种格式是用来获取特定项目。为了得到不同的项目，你必须发送特定的URL模式。在使用这种GET请求CouchDB的，我们可以得到静态项目，数据库文件和配置，并在JSON文件的形式统计信息（在大多数情况下）。
- **HEAD** - HEAD方法用于获取一个GET请求的HTTP头无响应的主体。
- **POST** -发布请求被用来上传的数据。在使用POST请求CouchDB的，你可以设置值，上传文件，设置文件值，还可以启动特定的管理命令。
- **PUT** -使用PUT请求，可以创建新的对象，数据库，文档，视图和设计文档。
- **DELETE** -使用delete要求，您可以删除文档，视图和设计文档。
- **COPY** -使用复制的方法，你可以复制文件和对象。

HTTP头应供给至得到正确的格式和编码。发送请求到CouchDB的服务器，可以与请求一起发送HTTP请求头。以下是不同的HTTP请求头。

- **Content-type** -这个头部是用来指定我们提供给服务器的请求一起的数据的内容类型。主要是我们一起发送内容与请求的类型将是MIME类型或JSON（应用/ JSON）。强烈推荐使用上的请求内容类型。

- ***\*Accept\****-这头部用于指定服务器，数据类型的客户端可以理解的列表，以便服务器将发送使用这些数据类型，它的响应。一般来说在这里，您可以发送客户接受MIME数据类型，用冒号分隔的列表。

  虽然，利用接受CouchDB中的查询是不需要的，强烈建议确保返回的数据可以由客户端来处理。



这些是由服务器发送的响应的头部。这些头部文件提供了有关由服务器发送响应为内容的信息。

- **Content-type** - 这头指定的MIME类型由服务器返回的数据。对于大多数的请求，返回的MIME类型为text / plain的。
- **Cache-control** - 这头表明有关处理由服务器发送的信息的客户端。 CouchDB的大多是返回的必重新验证，这表明信息应尽可能进行重新验证。
- ***\*Content-length\**** - 此头返回由该服务器发送的，以字节为单位的内容的长度。
- **ETAG** - 这个头是用来显示一个文档的修订，或视图。

```sh
curl 
-X GET
		http://ip:port/  # couchdb 版本信息
		/_utils/ # 交互 Futon
		/_all_dbs/  #获取创建的所有数据库的列表
		/<database_name>  #获取数据库信息
		/<database_name>/<document_id>  #获取数据库中指定id的文档信息
-X PUT 
		/<database_name>  # 创建数据库
		/<database_name>/<document_id> -d '{}'  # 创建指定id内容为json的文档
		/<database_name>/<document_id>/ -d '{}' # 更新
		/<database_name>/<document_id>/<filename>?rev=<rev_id> --data-binary @filename -H "Content-Type:image/jpg"
		
-X DELETE
		/<database_name>  # 删除数据库	
		/<database_name>/<document_id>?rev=<_rev>  # 删除数据库中指定id的文档
```



Futon是CouchDB的内置的基于Web的管理界面。 它提供了一个简单的图形界面，您可以使用它与CouchDB进行交互。 它是一个朴素的接口，它提供对所有CouchDB功能的完全访问。

数据库：

- 创建数据库。
- 销毁数据库。

文件：

- 创建文档。
- 更新文档。
- 编辑文档。
- 删除文档。
