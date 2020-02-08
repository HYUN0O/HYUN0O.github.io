---
categories: 
  - Study
tags:
   - Flask
   - Python
---
Python으로 구동되는 웹 프레임워크 중 가장 유명하고, 널리 쓰이는 프레임워크를 고르라면 Django, 그 다음으로 Flask를 택할 것 이다.

Django는 기능이 많고, 강력하다.

웹 어플리케이션을 개발하기 위한 많은 기능들을 Django에서 지원하기 때문에 대규모 어플리케이션 개발에도 충분히 사용 가능하다는 점이 장점이다.

하지만 오늘은 Flask에 대해 다루어보려고 한다. 이유는 간단하다. Flask는 **쉽고, 가볍고, 간단한** 웹 프레임워크를 표방하기 때문이다.

이번에는 가볍게 서버를 띄우고, 이후 어플리케이션 개발을 위한 서버 개설이 목적이므로 Flask를 이용해보겠다.

# Google Colab에서 Flask 서버 띄우기 실습

Google Colab의 가장 큰 장점중 하나는, 웬만한 패키지가 이미 설치되어 있기 때문에, 바로 import하여 사용할 수 있다는 점이다.

기본적인 Google Colab의 환경설정은 [이 곳](https://theorydb.github.io/dev/2019/08/23/dev-ml-colab/#colab-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95)을 참조하는 것이 좋을 것 같다. 이미 위 블로그에 충분히 친절하게 설명되어 있기에, 새로운 포맷으로 설명을 늘여놓지는 않아도 될 것 같다.

## ☝ Hello, World!

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return 'Hello, World!'
```

일반적인 ide 개발 환경이라면, 위 처럼 Flask를 import하고 hello world를 return 하는 것 만으로 `http://127.0.0.1:5000/`에서 출력된 내용을 확인할 수 있지만, 우리는 구글 코랩 환경에서 구현된 코드이므로, 외부에서도 접근할 수 있는 URL 주소가 필요하다.

이를 위해 `ngrok`을 이용해보자.

```python
pip install flask-ngrok
```

위의 코드를 실행하면, flask에서 사용되는 ngrok를 설치할 수 있다.

`ngrok`는 로컬 서버를 안전한 터널을 통해 인터넷에서도 접근할 수 있도록 하는 도구라고 생각하면 된다. 


```python
from flask_ngrok import run_with_ngrok
from flask import Flask
app = Flask(__name__)
run_with_ngrok(app)

@app.route("/")
def home():
	return "Hello, World!"

app.run()
```

위 코드를 ipynb 파일에 붙여넣고, 실행해보자! 그럼 아래 그림과 같이 실행 될 것이다.

![(그림1)](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200207/01.jpg?raw=true)

빨간색으로 네모 표시 되어있는곳이 URL 주소이다. 무료버전 ngrok에서는 이 주소를 고정할 수 없고, 매 실행마다 변경된다.

하지만, 테스트를 위해 사용하는데는 문제가 없으니 사용하겠다.

![(그림2)](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200207/02.jpg?raw=true)

해당 주소를 클릭해서 들어가보면 위와 같은 결과가 나온다면 정상적으로 서버를 띄운 것이다.

고정 도메인 주소를 사용하고 싶다면 유료로 이용하거나, 로컬 환경이나 클라우드 서비스를 이용하자.

## 🤞 html 파일 읽어서 render하기

매 결과값 마다 코드에 지저분하게 html 태그들을 주르륵 나열하면 소스 파일 1개가 수천, 수만줄로 늘어나고, 가독성 또한 발할라로 날아갈 것이 분명하다.

이는 페이지의 내용을 html 파일로 만들고, `render_template()`함수를 이용하는 것으로 해결할 수 있다.

```python
from flask_ngrok import run_with_ngrok
from flask import Flask, render_template
app = Flask(__name__, template_folder='/templates')
run_with_ngrok(app)

@app.route("/")
def home():
	return render_template('home.html')
	
app.run()
```

위 코드는 기존 코드와 다른 점이 세 가지 있다.

1. `flask`에서 `Flask`를 import할 때, `render_template()`를 함께 import시킴.

`render_template()`함수는 html파일을 읽어오고, 값을 전달하는 역할을 수행한다.  
이 함수는 flask에 포함되어 있기 때문에, import시켜서 이용할 수 있게 한다.

2. app를 정의 할 때, 매개변수로 `template_folder="/templates"`가 추가됨.

`template_folder` 를 통해 폴더를 지정해두면, 해당 폴더에서 render_template함수가 html 파일을 호출하기 떄문이다.  
이 경우는 최상위 폴더의 `/templates`라는 폴더를 미리 지정해두고, 해당 폴더에 html 파일들을 저장해두었다.

3. home()이 `render_template('home.html')`을 return함.

`template_folder`에 지정해 둔 폴더에서 해당 html파일을 찾아서 return한다.  
나는 미리 `home.html`파일을 최상위 폴더에 templates라는 폴더 안에 아래와 같이 설정해두었다.

해당 폴더는 재시작하면 사라지니, 오래 보관할 목적이라면 구글 드라이브에 저장하는 것이 좋다.

**/templates/home.html**
```html
<html>
	<head>
	    <title>
	        hello, world!
	    </title>
	    <style>
	    </style>
	</head>
	<body>
	    <h1>Hello, World! - from render_template</h1>
	</body>
</html>
```

html 문법에 대한 이야기는 길어지니, 아래 실행 결과를 직접 확인해보자.

![(그림3)](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200207/03.jpg?raw=true)

## 🤟 값 전달 하기

html 파일 내에서 {{ 변수명 }}을 사용하면 변수를 화면에 출력할 수 있다. 우선 고정 값을 전달해보자.

```python
# .....

@app.route("/")
def home():
	id = "danggai"
	return render_template('home.html', id=id)
```

**/templates/home.html**
```html
<!-- ..... -->
	<body>
	    <h1>Hello, {{ id }}!</h1> - from render_template
	</body>
</html>
```

위의 코드를 실행하면 아래와 같은 결과를 얻을 수 있다.

![(그림4)](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200207/04.jpg?raw=true)

render_template의 2번째 인자 부터는 값을 전달하는데 사용되고, `(html에서 쓰일 변수 명) = (파이썬 코드 내의 변수명)`의 꼴로 값을 전달한다.

form을 사용하여 이번에는 값을 입력받아서 페이지로 전달해보자.

```python
#..... 
@app.route("/")
def home(id = None):
    return render_template('home.html', id=id)

@app.route("/auth")
def login(id = None):
    userList = ["HDG", "JKJ", "JBJ", "YHR"]
    id = request.args.get('id')
    return render_template('home.html', id=id, userList=userList)
  
app.run()
```

**/templates/home.html**
```html
<!-- ..... -->
	<body>
	    {% if id == None or id == "" %}
	        <h1>사용자 이름을 입력해주세요.</h1><br>
	    {% else %}
	        {% if id in userList %}
	            <h1>환영합니다, {{ id }} 님. </h1><br>
	        {% else %}
	            <h1>{{ id }} 님은 유효하지 않은 사용자입니다. </h1><br>
	        {% endif %}
	    {% endif %}
	    <form method="GET" action="/auth">
	        <div>
	            <label for="id">ID: </label>
	            <input type="text" name="id">
	        </div>
	        <div class='button'>
	            <button type="submit">입력</button>
	        </div>
	    </form>
	</body>
</html>
```

![(그림5)](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200207/05.jpg?raw=true)
![(그림6)](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200207/06.jpg?raw=true)
![(그림7)](https://github.com/danggai/danggai.github.io/blob/master/assets/image/20200207/07.jpg?raw=true)

위 코드를 사용하면 `form`을 이용하여 id 값을 받고, `GET`메소드를 통해 id 값을 전달한다. 해당 id 값은 `{url}/auth` 경로로 전달되고, `userList`안에 존재하는 id라면 환영을, 아니라면 유효하지 않다는 답을 내놓게 된다.

새 페이지로 연동할 때 생성자로 `id=None`를 지정하면, id를 입력받지 못했을 때는 id라는 변수에 `None` 값을 할당할 수 있다.

html 파일 내에서 {%%}을 이용하면 반복문, 조건문 등 로직 수행이 가능해진다. 입력받은 유저가 userList안에 있는지, id를 입력하였는지를 판단하여 상황에 맞는 출력을 하도록 코드를 작성하였다.

또한, 값 전달을 `GET`메소드 뿐만 아닌 `POST`메소드를 통해 할 수도 있다.

## 🍀 GET, POST

두 메소드는 값을 전달한다는 공통점을 가지고 있지만, GET 메소드는 주소창에 전달하는 값이 보이고, POST 메소드는 보이지 않는다는 차이점이 있다.

또한, GET 메소드는 서버에 캐시를 남기고, POST 메소드는 그러지 않는다는 차이점도 존재한다.

**GET 메소드 사용**
```html
<!-- .... -->
	<form method="GET" action="/auth">
		<div>
		    <label for="id">ID: </label>
		    <input type="text" name="id">
<!-- .... -->
```
```python
# ....
# @app.route("/auth", methods = ["GET"]) # 이 코드도 작동함.
@app.route("/auth")
def login(id = None):
	id = request.args.get('id')
# ....
```

위 처럼 GET 메소드를 통해 전달하면, `request.args.get()`을 통해 값을 전달받는다.

또한, `app.route`에 메소드를 지정해주어도 되고, 안해주어도 된다.

**POST 메소드 사용**
```html
<!-- .... -->
	<form method="POST" action="/auth">
		<div>
		    <label for="id">ID: </label>
		    <input type="text" name="id">
<!-- .... -->
```
```python
# ....
@app.route("/auth", methods = ["POST"])
def login(id = None):
	id = request.form['id']
# ....
```

하지만 POST 메소드의 경우에는 `request.form['id']`을 통해 값을 전달받으며`app.route`에 메소드를 명시하지 않으면 405오류가 발생하며, 값을 전달받을 수 없다.

```python
@app.route("/auth", methods= ["POST", "GET"])
```

두 메소드를 모두 사용하고 싶다면 위 코드를 사용하면 된다.

물론, 이 경우는 `request.method`에 따라 다르게 값을 받도록 하는 코드가 반드시 필요하다.

## 👏 마치며

오늘은 간단히 Google Colab에서 Flask를 이용하여 서버를 띄우고, 값을 주고 받는 방법에 대하여 알아보았다.

다음 시간에 Flask에 대해 다를 때는 json 꼴로 반환하고, 해당 반환형을 이용하는 방법에 대하여도 알아보자!


## raw code

**server_example.ipynb**
```python
from flask_ngrok import run_with_ngrok
from flask import Flask, render_template, request, jsonify
app = Flask(__name__, template_folder='/content/drive/My Drive/Colab Notebooks/templates')
run_with_ngrok(app)

@app.route("/")
def home(id = None):
    return render_template('hello.html', id=id)

@app.route("/auth")
def login(id = None):
    userList = ["HDG", "JKJ", "JBJ", "YHR"]
    id = request.args.get('id')
    return render_template('hello.html', id=id, userList=userList)
    
@app.route("/auth/json")
def authJson(id = None):
    id = request.args.get('id')
    return jsonify(id = id)
  
app.run()
```

**templates/hello.html**
```html
<html>
	<head>
	    <title>
	        hello, world!
	    </title>
	    <style>
	    </style>
	</head>
	<body>
	    {% if id == None or id == "" %}
	        <h1>사용자 이름을 입력해주세요.</h1><br>
	    {% else %}
	        {% if id in userList %}
	            <h1>환영합니다, {{ id }} 님. </h1><br>
	        {% else %}
	            <h1>{{ id }} 님은 유효하지 않은 사용자입니다. </h1><br>
	        {% endif %}
	    {% endif %}
	    <form method="GET" action="/auth">
	        <div>
	            <label for="id">ID: </label>
	            <input type="text" name="id">
	        </div>
	        <div class='button'>
	            <button type="submit">로그인</button>
	        </div>
	    </form>
	    <form method="GET" action="/auth">
	        <div>
	            <label for="id">ID: </label>
	            <input type="text" name="id">
	        </div>
	        <div class='button'>
	            <button type="submit">입력</button>
	        </div>
	    </form>
	</body>
</html>
```

