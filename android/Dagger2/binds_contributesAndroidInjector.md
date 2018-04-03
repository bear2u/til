# 1일차

# Dagger2 Annotations: @Binds & @ContributesAndroidInjector

[https://proandroiddev.com/dagger-2-annotations-binds-contributesandroidinjector-a09e6a57758f](https://proandroiddev.com/dagger-2-annotations-binds-contributesandroidinjector-a09e6a57758f)

This article / is a part of the “Dagger / and the Dahaka”[series](https://proandroiddev.com/dagger-2-android-defeat-the-dahaka-b1c542233efc). / In this article / we will briefly / look at / two annotations / :`@Binds`and `@ContributesAndroidInjector`

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

We / have / a`LoginPresenter /`which implements`LoginContract.Presenter` / . Without`@Binds`, / the provider method / for it / will be something / as follows :

> 우리는 / 가진다 / LoginPresenter를 / LoginContract.presenter를 상속받는 / @Binds 없이 / 제공하는 함수는 / 그것을 위해 / 다음과 같이 될것이다.

우리는 LoginContract.Presenter를 상속받는 LoginPresenter를 가지고 @Binds를 provide 함수를 가진 예를 살펴보자.

```
@Provides
public LoginContract.Presenter 
  provideLoginPresenter(LoginPresenter loginPresenter) {
    return loginPresenter;
}
```

In the above case / , we / can instead use/ `@Binds`annotation / and make / the above method`abstract`

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

# 2일차

**What / makes our abstract Module / more performant?**

> 무엇이 / 우리 추상화 모듈이 만들다 / 더 성능이 좋게?

무엇이 추상화 모듈이 좀 더 성능이 좋게 만들까?

`@Provides`methods / are instance methods / and / they need / an instance of our module / in order to / be invoked.

> @provides 메소드는 / 인스턴스 메소드이다. / 그리고 / 그들은 필요하다 / 모듈의 인스턴스이다 / 하기위해 / 적용되다.

@Provides 메소드는 인스턴스 메소드들이고 그들은 적용되기 위해 우리의 모듈의 인스턴스을 필요로 한다.

If / our Module / is abstract / and / contains / @Binds methods/ , dagger / will not instantiate / our module / and / instead / directly / use / the Provider of our injected parameter / \(LoginPresenter / in the above case\).

> 만약에 / 우리의 모듈이 / 추상적이다 / 그리고 / 포함한다 / @Binds 메소드를 / dagger는 / 인스턴스를 안할것이다 / 우리의 모듈 / 그리고 / 대신에 / 직접적으로 / 사용한다 / 우리의 주입된 파라미터의 제공처로 / \(LoginPresenter / 위의 경우에는 \)

만약 우리의 모듈이 추상적이고 @Binds 함수를 포함하면 대거는 우리의 모듈에 대해 인스턴스화를 안할 것이고 대신에 직접적으로 주입된 파라미터의 제공자로 사용될 것이다.  ...\(어렵네..\)

**What if / your module / contains / both @Provides / and / @Binds methods?**

> 만약 어떨까? / 당신의 모듈이 / 포함한다 / @Provide와 @Binds 함수를 둘다 /

만약 당신의 모듈이 @Provide 와 @binds 함수들을 둘다 포함한다면 어떻게 될까?

Dagger documentation / has / great FAQ questions / on @Binds vs @Provides methods/ . In case,/  your Module / has / both @Provides and @Binds methods / , you / have / two options :

> 대거 문서는 / 가진다 / 휼륭한 FAQ 질문들을 / @Binds 와 @Provides 함수들을 / 이 경우에는 / 당신의 모듈 / 가진다 / @Provides 와 @Binds 함수들을 / 당신은 / 가진다 / 두가지 옵션을

대거 문서는 @Binds 와 @Provides 함수들에 대한 휼륭한 FAQ 문서들을 가지고 있다. 당신의 모듈의 경우에는 두가지 함수들을 가진다. 당신은 두가지 옵션을 가진다.

Simplest / would be / to mark / your @Provides / instance / methods / as static.

> 가장간단하게 / 될 것이다. / 마크 하기위해 / 당신의 @Provides / 인스턴스 / 함수들을 / 전역으로

가장 간단하게 당신의 @Provides 인스턴스 함수들을 전역 함수로서 표시하게 될 것이다.

If / it is necessary / to keep / them / as instance methods,/  then / you can split / your module / into two / and / extract out / all the @Binds / methods / into an abstract Module.

> 만약에 / 필요로 하다 / 유지하기위해 / 그것들을 / 인스턴스 함수로서 / 그렇다면 / 당신은 나눌수 있다/ 당신의 모듈을  / 두개로 / 그리고 / 꺼내다 / @Binds 함수 모두를 / 추상화 모듈안으로

만약에 그것들을 인스턴스 함수로 유지한다면 당신은 너의 모듈을 2개로 나눌수 있고 추상화 모듈안으로 @binds 함수 모두를  추출할수 있다.



# 3일차

To know / about more such optimizations / and even others / which the recent version / 2.12 of dagger / has brought / along with it. I / recommend / watching “Optimizing Dagger on Android” / by Ron Shapiro, Google.

> 알기 위해선 / 더 많은 최적화에 대해 / 심지어 다른 것들 / 최근 버전에 대한 / 2.12 대거 버전 / 은 가져왔다 / 그것과 함께 / 나는 / 추천한다 / Android dagger 최적화를 보기를 / by Ron Shaprio , google

더 많은 최적화에 대해서 알고 싶고 2.12 대거의 최신 버전은 심지어 다른 것도 가져왔다. 나는 영상을 보는 것을 추천한다. 

# @ContributesAndroidInjector

If you / have seen / a basic implementation of dagger-android / , you / would know / that it introduces / some amount / of boilerplate code. /  When / you are trying to / switch to dagger-android / or converting / your current app / to use it. 

> 만약 당신이 / 본적이 있다면 / dagger-android의 기본 구현을 / 당신은 / 알것이다 / 그것은 소개한다 / 조금의 양 / 보일러 플레이트의 / 당신이 시도할때 / dagger-android로 바꾸는 걸 / 또는 변환하는 것을 / 당신의 현재 앱에 / 그것을 사용하기 위해

만약 당신이 dagger-android의 기본 구현을 본 적이 있다면 당신은 조금의 보일러 플레이트의 양이 있단걸 알게 될 것이다. 

당신이 시도할때 dagger-android 로 바꾸는 걸 시도할때 또는 그것을 사용하기 위해 당신의 현재 앱을 변환할때

The most cumbersome / I think / is having / to create separate sub-components / for each of / your Activities / , / Fragments/, Services / , etc. and adding / those / to the  injectorFactories of your DispatchingAndroidInjector / \(by using @IntoSet\)

> 다루기 힘든건 / 내가 생각하기에 / 가지는 것이다 / 분리된 sub-components들을 만들기 위해 / 각각의 / 당신의 activity , fragments, 서비스등.. / 그리고 추가한다 / 그러한것들을 / 인당신의 DispatchingAndroidInject의  젝션공장에

내가 생각하기에 가장 다루기 힘든건 당신의 엑티비티, 프래그먼트, 서버시등 각각의 분리된 보조 컴포넌트들을 만들기 위해 가지는 것이다. 그리고 당신의 DispatchingAndroidnjector의 InjectorFactories에 추가하는 것이다. 

Dagger Android / introduced / an annotation / which can reduce / the Component Binds / IntoSet Subcomponent ActivityKey FragmentKey etc. / boilerplate for you.

Dagger Android는 Subcomponent Activity와 fragment 등을 필요한 보일러플레이트들을 줄이기 위해 어노테이션을 소개한다. 

If / you / have a simple module /like the following, /you /can then /let dagger /handle the rest.

> 만약에 / 당신이 / 심플한 모듈을 가진다 / 따르는 것 같은 / 당신이 할수 있고 dagger 가 나머지를 처리한다.

만약 당신이 나머지를 처리할 수 있는 간단한 모듈을 가진다. 

```
@Module
public abstract class LoginModule {

    @Binds
    public abstract LoginContract.Presenter
  provideLoginPresenter(LoginPresenter loginPresenter);

}
```

### All you need to do / is write / the following snippet / inside the Module / of the component, / which is going to be / the super-component / of the generated LoginComponent. / Example, / If you / have an AppComponent /and you /want dagger to generate /a LoginSubcomponent /for your LoginActivity, /you will write /the following /snippet inside your AppModule.

> 당신이 필요하기 위해 필요한 모든 건 / 적는 것이다 / 다음과 같은 조각 / 모듈안에 / 컴포넌트의 / 되기 위해 / super-compoent / LoginComponent가 생성된 / 예를 들어 / 만약 당신이  / Appcompoent를 가진다 / 그리고 당신은 생성하기 위한 대거 / LoginSubcomponent / 당신의 로근 엑티비티에 대해 / 당신은 적는다 / 따르는 / 당신의 앱모듈안에 스닛트

당신이 하기 필요한 모든건 LoginComponent을 생성하는 것의 부모 컴포넌트가 되기 위한 컴포넌트의 모듈을 따르면 된다. 

예를 들어 당신은 AppComponent를 가지고 싶다면 당신은 LoginSubcomponent를 만들기 위해 당신은 당신의 앱모듈안에 스크립트를 적을 것이다. 

```
@ContributesAndroidInjector(modules = LoginModule.class)
abstract LoginActivity loginActivity();
```









