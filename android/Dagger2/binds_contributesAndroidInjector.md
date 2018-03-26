# Dagger2 Annotations: @Binds & @ContributesAndroidInjector

[https://proandroiddev.com/dagger-2-annotations-binds-contributesandroidinjector-a09e6a57758f](https://proandroiddev.com/dagger-2-annotations-binds-contributesandroidinjector-a09e6a57758f)

This article / is a part of the “Dagger / and the Dahaka”[series](https://proandroiddev.com/dagger-2-android-defeat-the-dahaka-b1c542233efc). / In this article / we will briefly / look at / two annotations / :`@Binds`and `@ContributesAndroidInjector`

> 여기 아티클은 / Dagger의 부분이다. / 그리고 Dahaka의 시리즈이다. / 이 아티클은 / 우리는 잠시 / 살펴볼것이다. / 두가지 어노테이션을 / @Binds 와 @ContributesAndroidInjecotor

여기 아티클은 Dagger2의 한 부분이며 Dahaka의 시리즈이다. 이 글에서 우리는 잠시 두가지 어노테이션인 @Binds 와 @ContributesAndroidInjector 를 잠깐 살펴볼 것이다. 

It is not mandatory / to have read / the previous articles / to understand this/  , but / a basic understanding of Dagger / is a must.

> 이것은 의무가 아니다 / 무조건 읽어야 하는 건 / 이전 아티클을 / 이것을 이해 하기위해 / 하지만 / 대거의 기본 이해는 / 필요하다.

이 글을 읽기 위해서 이전 글들을 읽을 필요는 없지만 대거의 기본 이해는 필요하다. 

Dagger has many helpful Annotations / which can not only reduce / the code you write / , but also make / your generated code /  much more optimized / and one of those annotations / is`@Binds`

> 대거는 많은 도움을 줄만한 어노테이션을 가지고 있다 / 오직 줄이기 위하기 위하지 않는 / 당신이 적는 코드들을 / 하지만 또한 
>
> 만든다 / 당신의 생성된 코드을 / 더 최적화된 / 여러개의 어노테이션 중 하나 / 는 @Binds 이다

당신이 적는 코드들을 도와주는 특수한 유용한 어노테이션이 있다. 하지만 또한 당신의 적는 코드들을 더 최적화 시키고  여러개의 어노테이션 중 하나는 @Binds 이다. 

# @Binds

This annotation / provides / a replacement of`@Provides`methods / which simply return / the injected parameter. / Let’s take / an example,

> 이 어노테이션은 / 제공한다 / @Provides 메소드의 교체를 / injected 된 파라미터를 간단하기 리턴하는 / 예를 한번 보자.

이 어노테이션은 Injected 된 파라미터를 제공해주는 @Provide 함수를 교체해준다. 예를 들어 보자. 

We / have / a`LoginPresenter /`which implements`LoginContract.Presenter` / . Without`@Binds`, / the provider method / for it / will be something / as follows :

> 우리는 / 가진다 / LoginPresenter를 / LoginContract.presenter를 상속받는 / @Binds 없이 / 제공하는 함수는 / 그것을 위해 / 다음과 같이 될것이다.

우리는 LoginContract.Presenter를 상속받는 LoginPresenter를 가지고 @Binds를 provide 함수를 가진 예를 살펴보자. 

```
@Provides
public LoginContract.Presenter 
  provideLoginPresenter(LoginPresenter loginPresenter) {
    return loginPresenter;
}
```

In the above case / , we / can instead use/ `@Binds`annotation / and make / the above method**`abstract`**

> 위의 경우에 / 우리는 / 대신 사용가능하다 / @Binds 어노테이션을 / 만든다 / 그리고 상단에 abstract 가진 함수를

우리는 위의 경우에 @Binds 어노테이션을 대신 사용가능하고 앞에 abstract를 붙여준다. 

```
@Binds
public abstract LoginContract.Presenter
  provideLoginPresenter(LoginPresenter loginPresenter);
```

Of course / , we / will also need to mark / our Module / as abstract / in this case, / which is more efficient / than a concrete one and / thus / makes`@Binds /`more efficient.

> 물론 / 우리는 / 표시가 필요할 것이다 / 우리의 모듈에 / 추상화으로써 / 이 경우에는 / 더 효율적이고 / 더 구체적이고 / 그러므로 / @Binds 를 만든다. / 더 효과적인

물론 우리는 이 경우에는 모듈을 추상적으로 표시해야 합니다.  그것은 더 효율적이고 더 구체적이고 그러므로 @Binds을 보다 효율적으로 만들 수 있다.

























:





