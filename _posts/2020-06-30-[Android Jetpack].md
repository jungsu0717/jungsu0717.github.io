---
title: "Android Jetpack"
date: 2020-06-30 11:48:28 -0400
categories: Android
---

# 1) 목차
* Android Jetpack 이란?
* Jetpack Components
* LiveData 란?
* LiveData 사용 예시

# 2) Android Jetpack 이란?

* Google I/O 2018 에 소개되었으며 Android Architecture (AAC), Kotlin 지원 도구, support library v4, v7, v8, v17 등으로 파편화된 라이브러리를 통합한 도구 및 지침 세트로 Androidx 기반의 구조로 변경
*  androidx.* 네임스페이스
*  modern architecture 적용을 빠르게 지원
*  복잡한 백그라운드 작업과 데이터 Life cycle에 시간을 쏟지 않고 오로지 앱 고유 기능의 개발에 집중하도록 지원
*  <https://developer.android.com/jetpack?hl=ko> 에서 자세한 내용 참고

# 3) Jetpack Components

* Jetpack 은 기능에 따라 크게 네 가지의 컴포넌트로 구분 할 수 있으며 독립적인 활용이 가능

기능	설명
Architecture	a. 구글에서 제안하는 Android Architecture 를 구현할 수 있는 기능들로 구성 
b. View를 포함한 UI 요소의 lifecycle management 를 비롯하여 LiveData, ViewModel, Room 등의 기능이 여기에 포함
Foundation	a. 안드로이드 시스템의 핵심 기능을 담당하는 컴포넌트
b. AppCompat, Kotlin Extension, Multidex 등이 여기에 포함
Behavior	a. 앱의 동작과 관련된 컴포넌트
b. Notification, 다운로드 매니저, Permission 관리 기능 등이 여기에 포함
UI	a. UI개발과 사용의 일관성을 보장해주는 컴포넌트
b. Animation, Fragment, Layout 등이 여기에 포함
윤정수 > Android Jetpack > jetpack-image.png
출처 : https://velog.io/@tura/android-jetpack-introduction

# 3) LiveData 란?
Android Jetpack 중 Architecture 컴포넌트에 포함
Live Cycle 을 알고있는 DataType 으로서 Observer 패턴을 따른다.
데이터의 변경이 일어났을때 콜백 으로 받아 처리 가능하다.
단일 효과를 기대하기 어렵지만, Databinding, ViewModel, RoomDatabase 등과 접목하여 사용하면 수십줄의 소스코드를 1~2줄로 바꾸는 기적을 보여줌!!


# 4) LiveData 사용 예시 
ViewModel 에서 LiveData 선언

// MainViewModel.class

~~~java
public class MainViewModel extends BaseViewModel<MainView> {

// LiveData 선언 : LiveData 를 사용할 때 MutableLiveData 를 사용한다.
	private final MutableLiveData<ResSI1000> lvResSI1000;
	
	private void setLiveData(ResSI1000 mainData){
    		lvResSI1000.setValue(mainData);
	}
} 
~~~

* LiveData의 Event 처리 *
LiveData의 Event에는 setValue 와 postValue 가 있는데 setValue에서는 UI Thread즉 Main Thread에서 실행하고 postValue에서는 Background Thread에서 처리한다.


Activity 에서 LiveData Observing

~~~java
// MainActivity.class
public class MainActivity extends BaseActivity<ActivityMainBinding, MainViewModel> implements MainView, HasSupportFragmentInjector {

private void subscribeToLiveData() {

    // Live Data 를 Observe
    viewModel.getLiveData()
		// Live Data CallBack 처리
		.observe(this, data -> viewModel.setOvResSI1000(data)
	);
}
~~~


ViewModel + DataBinding과 접목하여 사용


~~~xml
// activity_main.xml
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>
		// ViewModel 선언
        <variable name="viewmodel" type="MainViewModel" />
    </data>

	<TextView
		// title 값이 변경되는대로 TextView UI 는 자동으로 변경됨
     		android:text="@{viewmodel.lvResSI1000.title}"
	</TextView>
</layout>
~~~

