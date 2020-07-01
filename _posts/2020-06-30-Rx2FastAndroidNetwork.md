---
title: "Rx2FastAndroidNetwork"
date: 2020-06-30 11:48:28 -0400
categories: Android
---

# 1. 목차
----------
* Rx2FastAndroidNetwork 란?


# 2. Rx2FastAndroidNetwork 란?
--------

* Maven 에 다음과 같이 소개되어있다. 
~~~
Fast Android Networking is a powerful library for doing any type of networking in Android applications **
~~~

* Rx2FastAndroidNetworking 은 안드로이드 앱에서 모든 유형의 네트워킹을 수행 할 수 있는 강력한 라이브러리
* RxJava와 뛰어난 호환성을 지니고 있음

## 2-1) 적용 배경

~~~java
/**
 * 1) MainViewModel 에서 Api 호출
 */
public void getSisaMainData() {
 
    setIsLoading(true);
    getCompositeDisposable().add(getDataManager()
            .getSI1000(new ReqSI1000(0, localUserInfoData.getOsType(), localUserInfoData.getOsVersion(), localUserInfoData.getDeviceType()))
            .subscribeOn(getSchedulerProvider().io())
            .observeOn(getSchedulerProvider().ui())
            .subscribe(mainData -> {
                lvResSI1000.setValue(mainData);
                setIsLoading(false);
                getNavigator().ProgressOff();
            }, throwable -> {
                getNavigator().ProgressOff();
                getNavigator().checkNetWorkAlert();
            }));
}
 
/**
 * 2) ApiHelper 에서 통신로직 호출
 *
 * @param reqSI1000
 * @return
 */
@Override
public Single<ResSI1000> getSI1000(ReqSI1000 reqSI1000) {
    return returnJsonObjectSingle("SI1000", ResSI1000.class, reqSI1000, ApiEndPoint.API_URL);
}
 
/**
 * 3) Json을 Single Object로 변형하여 Return
 *
 * @param trcode
 * @param resClass
 * @param reqObject
 * @param <T>
 * @return
 */
private <T> Single<T> returnJsonObjectSingle(String trcode, Class<T> resClass, Object reqObject, String baseUrl) {
    return Rx2AndroidNetworking.post(baseUrl)
            .addHeaders(CustomCrypto.getHeaderMap(localUserInfoData.getUserId()))
            .addBodyParameter("message", new BaseReqData<HeaderData, T>().initBase(
                    new HeaderData(trcode, localUserInfoData.isLogin() ? localUserInfoData.getUserId() : "", CommonDefine.APP_CODE, localUserInfoData.getDeviceId(), Util.getVersionName(EduwillApplication.instance.getApplicationContext())), reqObject)
            )
            .build()
            .getJSONObjectSingle()
            .flatMap(value ->
                    Single.just(
                            new GsonBuilder().create().fromJson(
                                    new JsonParser().parse(value.toString())
                                            .getAsJsonObject().get("message")
                                            .getAsJsonObject().get("body"), resClass)
                    )
            );
}
~~~
