AppBar Challengers \#1



아래와 같은 앱바를 만든다고 가정해보자. 

![](/assets/flutter_appbar_layout_1.png)

```flutter
Scaffold(
  appBar: AppBar(
    title: Row(
      mainAxisAlignment: MainAxisAlignment.start,
      children: <Widget>[
        Icon(
          Icons.people,
          color: Colors.white,
        ),
        Expanded(
          child: Text('등록대상자', textAlign: TextAlign.center)
        ),
      ],
    ),
    actions: <Widget>[
      IconButton(
        icon: Icon(
          Icons.search,
          color: Colors.white
        ),
      )
    ],
  ),
```



