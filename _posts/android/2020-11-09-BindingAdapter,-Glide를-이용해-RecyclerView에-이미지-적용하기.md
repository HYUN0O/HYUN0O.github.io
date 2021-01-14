---
categories: 
   - Study
tags: 
   - Databinding
   - Glide
---



현재 내가 개발중인 앱은 Databinding, MVVM 패턴, Glide등을 적용하여 개발 중에 있다.

기존에 사용하던 방식으로는 각각의 Adapter에서 `GlideApp`를 호출하고, `onBindViewHolder`에서 `holder`에 삽입해주는 함수를 구현하여야 하는데, 이 경우 같은 동작을 하는 함수들의 중복이 계속하여 발생한다.

중복 발생으로 인해, RecyclerView나, GridView등에서 이미지를 삽입하는 방식이 획일화 되어있지 못할 수 있고, 이후 수정이 필요할 때 여러 Adapter에서 하나씩 하나씩 수정해줘야하는 문제가 발생한다.

이런 이유로, 해당 기능을 BindingAdapter을 이용해 모듈화 해보고자 한다.

## 1. AS-IS

내 앱에서 기존에 이미지를 읽어오고, 읽어온 이미지를 RecyclerView/GridView에 적용하는 과정은 다음과 같았다.

1. Adapter에 `imageUrl`을 지닌 item을 전달.
2. Adapter 내 `onBindViewHolder`에서 `GlideApp`을 호출해, `holder`에 적용. 

2번 과정에서 holder에 적용하는 과정을 거치기 때문에, 각각의 View마다 지니는 Adapter에 각각 Glide를 호출하는 코드를 작성하여야 했었다.

## 2. TO-BE

BindingAdapter을 이용하여 xml에서 url을 입력 받고, Glide를 통해 입력 받은 URL에서 이미지를 불러오도록 수정하고자 한다.

이 경우, 이미지를 각각의 RecyclerView/GridView에 적용하는 과정은 다음과 같이 축소된다.

1. xml 파일에서 ImageView에 item.imageUrl 추가

## 3. 적용 과정

```kotlin
object CommonBindingAdapters{
	@BindingAdapter("app:imageUrl","app:placeholder")
	@JvmStatic fun loadImage(imageView: ImageView, url: String, placeholder: Drawable){
        GlideApp.with(imageView.context)
            .load(url)
            .placeholder(placeholder)
            .error(placeholder)
            .diskCacheStrategy(DiskCacheStrategy.NONE)
            .apply(RequestOptions().fitCenter())
            .into(imageView)
    }
}
```

@BindingAdapter을 명시하여 이 함수가 BindingAdapter임을 확인시키고,

Java의 Static에 대응하는 @JvmStatic를 추가하여 외부에서 접근이 가능하도록 설정한다.

- placeholder() : 이미지가 로딩 되기 전에 보여주는 **임시 이미지**
- error() : 이미지 불러오기에 **실패**했을 때 보여주는 이미지
- diskCacheStrategy() : 불러온 이미지를 디스크 캐시에 저장할지 여부를 지정

```xml
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    >
    <data>
        <variable
            name="item"
            type="com.danggai.app.bindingAdapterSample.item.SampleItem"
            />
    </data>
    
    <android.support.constraint.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <androidx.appcompat.widget.AppCompatImageView
			android:id="@+id/iv_image2"
			android:scaleType="fitXY"
			app:imageUrl="@{item.img_url}"
			app:placeholder="@{@drawable/icon_placeholder}"
			app:layout_constraintBottom_toBottomOf="parent"
			app:layout_constraintLeft_toLeftOf="parent"
			app:layout_constraintRight_toRightOf="parent"
			app:layout_constraintTop_toTopOf="parent"
			/>
        
    </android.support.constraint.ConstraintLayout>
</layout>
```

DataBinding을 적용하였으므로, `<layout>`으로 감싸고 `<data>`태그를 이용해 바인딩 한 객체의 데이터와 BindingAdapter를 이용해 사용할 준비를 한다.

이 코드가 성공적으로 빌드되면, RecyclerView나 GridView내의 아이템에서도 추가적인 코드 작성 필요 없이 Glide를 이용해 이미지 연동이 가능해진다.



