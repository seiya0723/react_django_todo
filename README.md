
## 解説

[DjangoとReactを組み合わせる方法論と問題の考察](https://noauto-nolife.com/post/django-react-methodology/)

参照: https://www.digitalocean.com/community/tutorials/build-a-to-do-application-using-django-and-react


## Djangoに必要なもの

- Django
- djangorestframework
- django-cors-headers

```
pip install django djangorestframework django-cors-headers
```

## Reactに必要なもの

- react
- bootstrap
- reactstrap
- axios

```
npm install bootstrap@4.6.0 reactstrap@8.9.0 --legacy-peer-deps
npm install axios@0.21.1
```


## 前提

ReactのサーバーとDjangoのサーバーの2つが立ち上がっている。

## 原理

1. Djangoはlocalhost:3000 からのリクエストを許可する。(cors-headesにより許可)
1. Reactはaxiosを使ってDjangoにリクエストを送信する。
1. Djangoはdjangorestframeworkを使って、シリアライズしたjsonをレスポンスとしてReactに返す
1. Reactはjsonを解析してレンダリングする


## 影響

Djangoがやっていることはjsonを返しているだけなので、テンプレートの処理はReactに一任する形になる。
そのため、DTLやDjangoMessageFrameworkなどのDjangoの一部機能が利用できなくなる。

```
{{ request.user }}
```
なども使えなくなる。

ここは、render_to_stringでレンダリングした結果を文字列にして、jsonで返し、Reactがレンダリングする形にしたら良いかと思うが、それで良いのだろうか？(ステートフルではなくなるのでは？)



## 検討・疑問点

- 影響の部分、これはどうするか？
- 現状、Djangoサーバーである localhost:8000 にアクセスしたとき404になる。Reactサーバー(localhost:3000)にリダイレクトしたほうが良い？
- デプロイはどうするのか？ https://noauto-nolife.com/post/django-deploy-ubuntu-venv/ は使えるのか？
- 複数のモデルのjsonを返す場合はどうするのか？viewsets.ModelViewSetでは汎用性に乏しい？
- おそらくModelViewSetでGET、POST、PUT、DELETEなどのメソッドをカバーしていると見られる。ではrestframeworkのroutersはなぜあえて通常のurls.pyと違う書き方をするのか？



