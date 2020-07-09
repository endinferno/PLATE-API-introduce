# <center>PLATE-API</center>

**Version: 1.0**



## <center>接口规范介绍</center>

本规范对PLATE平台的服务内容编写、相关参数、响应进行定义。目前支持`Javascript`语言，并提供借口操作请求和响应范例。

以下所有全局变量、对象、全局函数的命名方式都采用小驼峰式命名法. 如:`firstName`



## <center>信令服务器</center>

> 用户可以自定义自己的信令服务器，也可以使用官方定义的信令服务器

### <span id="信令服务器全局变量表">全局变量表</span>

**变量名**

`sslOption`

**变量类型**

字典(`dict`)

	**变量解释**

	该变量用于自定义用户SSL证书，用于建立自定义信令服务器及进行加密连接.

	**参数列表**

	```javascript
{
key: fs.readFileSync('文件路径'),
		 cert: fs.readFileSync('文件路径'),
}
```

**参数解释**

`key`表示证书的`key`.

`cert`表示证书的`cert`，可以是`cert`格式或者`pem`格式文件.



---

**变量名**

`serveOption`

**变量类型**

字典(`dict`)

	**变量解释**

	该变量用于建立用户自定义信令服务器.

	**参数列表**

	```javascript
{
port: 9000,
		  ssl: sslOption,
		  path: '/',
}
```



---

**变量名**

`connOption`

**变量类型**

字典(`dict`)

	**变量解释**

	该变量用于用户连接信令服务器，进行注册、数据通道建立的基础工作，默认进行加密连接.

	**参数列表**

	```javascript
{
host: '47.93.23.141',
		  port: 9000,
		  path: '/',
		  debug: 3,
}
```

**参数解释**

`host`表示信令服务器的公网IP. (Default:`47.93.23.141`)

`port`表示信令服务器的对应端口. (Default:`9000`)

`path`表示信令服务器响应请求的根路径. (Default: `/`)

	`debug`表示信令服务器的调试输出等级，等级排序为`no logs;only errors;errors and warnings;all logs`，分别对应`0;1;2;3`. (Default: `3`)



## <center>用户注册</center>

	> 用户注册采用构造对象的方式

	**构造对象名**

	`plateUser`

	**构造对象解释**

	该对象用于在信令服务器进行注册，对象建立成功即说明已经在信令服务器注册成功.

	**构造对象参数列表**

	```javascript
(plateUserID, connOption)
	```

	**构造对象参数解释**

	`plateUserID`表示在信令服务器注册的唯一编码，若编码冲突，则会返回空对象.

`connOption`表示信令服务器的连接参数，具体内容请见[信令服务器全局变量表](#信令服务器全局变量表)

	**返回值**

	当对象在信令服务器注册成功，返回对应的对象.

	若注册失败，返回一个空对象.

	**备注**

	建议用户将其设置为全局变量，方便随时调用获取.

	或者在该变量离开作用域之前，将对象与信令服务器的连接断开.



## <center>用户连接</center>

	**对象名**

	`plateDataConn`

	**对象解释**

	该对象用于表征用户之间的数据通道，对象存在即说明数据通道已成功建立.

	**备注**

	该对象无法采用构造对象的方式来得到，只能通过以下的`connect`以及`connectAll`方法来得到.

	得到该方法的前提是需要拥有`plateUser`对象并已成功在信令服务器注册.



	---

	**对象方法名**

	`isOpen`

	**对象方法解释**

	该方法隶属于`plateDataConn`对象，该方法可以判断对象内是否存在已开放的数据通道.

	**对象方法参数列表**

	无

	**返回值**

	`true`或`false`，表示是否存在已开放的数据通道.



	---

	**对象方法名**

	`on`

	**对象方法解释**

	该方法隶属于`plateDataConn`对象，该方法定义了数据通道连接后用户希望执行的动作.

	**对象方法参数列表**

	```javascript
(event, callback)
	```

	**对象方法参数解释**

	`event`表示事件选项，有以下选择

	* 'open'
	* 表示数据通道打开时.
	* 'close'
	* 表示数据通道关闭时.
	* 'error'
	* 表示数据通道出错时.
	* 'data'
	* 表示数据通道收到数据时.

	`callback`表示回调函数，可由用户自定义，其中在`open`以及`close`事件下，回调函数无函数参数；在`error`事件下，回调函数存在一个函数参数`err`；在`data`事件下，回调函数存在一个函数参数`data`.

	**返回值**

	无



	---

	**对象方法名**

	`connect`

	**对象方法解释**

	该方法隶属于`plateUser`对象，可以在拥有该对象后，调用该对象的`connect`方法来建立数据通道.

	**对象方法参数列表**

	```javascript
(targetUserID)
	```

	**对象方法参数解释**

	`targetUserID`表示当前用户希望建立单数据通道连接的用户ID，该ID需要在信令服务器已注册.

	**返回值**

	若对象希望连接的对方ID已在信令服务器注册，则返回`plateUserConn`对象.

	若对方ID未注册，则返回空对象.



	---

	**对象方法名**

	`connect`

	**对象方法解释**

	该方法隶属于`plateUser`对象，可以在拥有该对象后，调用该对象的`connect`方法来建立数据通道.

	**对象方法参数列表**

	```javascript
(targetUserID)
	```

	**对象方法参数解释**

	`targetUserID`表示当前用户希望建立单数据通道连接的用户ID，该ID需要在信令服务器已注册.

	**返回值**

	若对象希望连接的对方ID已在信令服务器注册，则返回`plateUserConn`对象.

	若对方ID未注册，则返回空对象.



	---

	**对象方法名**

	`connectAll`

	**对象方法解释**

	该方法隶属于`plateUser`对象，可以在拥有该对象后，调用该方法的`connectAll`方法，来建立一个广播方式的数据通道.

	**对象方法参数列表**

	无

	**返回值**

	不论当前信令服务器是否有其他用户已注册，都会返回一个`plateDataConn`对象.

	**备注**

	每当有新的用户在同一个信令服务器注册，都会隐式地建立一条数据通道连接并添加到`plateDataConn`对象内.



## <center>数据传输</center>

	**对象方法名**

	`send`

	**对象方法解释**

	该方法隶属于`plateDataConn`对象，用于在对应的数据通道中传输数据.

	**对象方法参数列表**

	```javascript
(data)
	```

	**对象方法参数解释**

	`data`表示希望传输的数据，在底层会被编码为二进制包的方式传输.

	**返回值**

	无

	**备注**

	推荐用户以`Json`的方式传输数据.



	---

### 更方便的方法

	**全局函数名**

	`sendMessage`

	**全局函数解释**

	该方法为全局函数，用于传输数据.

	**全局函数参数列表**

	```javascript
(plateUser, plateDataConn, targetUserID, action, data)
	```

	**全局变量参数解释**

	`plateUser`表示`plateUser`对象，需要确保该对象已经存在，否则函数会直接退出.

	`plateDataConn`表示`plateDataConn`数据通道对象，无需确保该对象是否为空或非空，若为非空，函数内会主动建立数据通道.

	`targetUserID`表示希望建立连接数据通道的对象，可以是单个`targetUserID`，也可以是`targetUserID`组成的列表.

	`action`表示传递事件动作，该动作由用户自定义，可以用来区分数据的导向.

	`data`表示传输的具体数据，该动作由用户自定义.

	**备注**

	`action`并非先前提到的`open`、`close`、`error`、`data`，可以与这些定义重名.



## <center>数据通道关闭</center>

	**对象方法名**

	`close`

	**对象方法解释**

	该方法隶属于`plateDataConn`对象，用来关闭数据通道.

	**对象方法参数列表**

	无

	**返回值**

	无



## <center>用户注销</center>

	**对象方法名**

	`close`

	**对象方法解释**

	该方法隶属于`plateUser`对象，用来注销在信令服务器的注册.

	**对象方法参数列表**

	无

	**返回值**

	无



## <center>前端组件</center>

	**全局函数名**

	`canvasInit`

	**全局函数解释**

	该方法为全局函数，用于初始化画板操作.

	**全局函数参数列表**

	```javascript
(canvasID, mouseDownAction, mouseMoveAction, MouseUpAction)
	```

	**全局变量参数解释**

	`canvasID`表示前端设置的`canvas`的`id`.

	`mouseDownAction`表示回调函数，可为空，为画板内鼠标按下的操作，回调函数含有一个函数参数`e`，其中包含了鼠标的位置坐标信息.

	`mouseMoveAction`表示回调函数，可为空，画板内鼠标移动的操作，回调函数含有一个函数参数`e`，其中包含了鼠标的位置坐标信息.

	`mouseUpAction`表示回调函数，可为空，画板内鼠标抬起的操作，回调函数含有一个函数参数`e`，其中包含了鼠标的位置坐标信息.

	**备注**

	画板元素在前端可以以`canvas`形式实现，`mouseDownAction`、`mouseMoveAction`、`mouseUpAction`都有默认的行为，对画板行为做了整体的设计；并且初始化了画板数据的数据通道.



	---

	**全局函数名**

	`imageBoardInit`

	**全局函数解释**

	该方法为全局函数，用于初始化图板操作.

	**全局函数参数列表**

	```javascript
(imageBoardID, imageBoardCallback)
	```

	**全局变量参数解释**

	`imageBoardID`表示前端设置的`imageboard`的`id`.

	`imageBoardCallback`表示回调函数，可为空，可以执行用户自定义的画板操作，回调函数含有一个`imageboard`函数参数.

	**备注**

	图板元素在前端可以以`div`形式实现，图板有默认的行为，对图板行为做了整体的设计，并且初始化了图板数据的数据通道.



	---

	**全局函数名**

	`videoInit`

	**全局函数解释**

	该方法为全局函数，用于初始化视频接口操作.

	**全局函数参数列表**

	```javascript
(videoID, videoCallback, audioCallback)
	```

	**全局变量参数解释**

	`videoID`表示前端设置的`video`的`id`.

	`videoCallback`表示回调函数，可为空，可以执行用户对视频流的自定义的操作，回调函数含有一个视频流`videoStream`函数参数.

	`audioCallback`表示回调函数，可为空，可以执行用户对音频流的自定义的操作，回调函数含有一个音频流`audioStream`函数参数.

	**备注**

	视频元素在前端可以以`video`形式实现，视频流、音频流处理有默认的行为，对视频流、音频流行为做了整体的设计，并且初始化了视频流、音频流数据的数据通道.



	---

	**全局函数名**

	`chatBoardInit`

	**全局函数解释**

	该方法为全局函数，用于初始化聊天面板操作.

	**全局函数参数列表**

	```javascript
(chatBoardID, chatMessageCallback)
	```

	**全局变量参数解释**

	`chatBoardID`表示前端设置的`chatBoard`的`id`.

	`chatMessageCallback`表示回调函数，可为空，可以执行用户对对话数据的自定义的操作，回调函数含有一个对话数据`messageData`函数参数.

	**备注**

	聊天面板元素在前端可以以`div`形式实现，对话数据处理有默认的行为，对对话数据行为做了整体的设计，并且初始化了对话数据的数据通道.



