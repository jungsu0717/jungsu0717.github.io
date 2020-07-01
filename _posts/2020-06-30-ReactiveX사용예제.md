---
title: "Reactive Programming"
date: 2020-06-30 11:48:28 -0400
categories: Reactive Programming
---

# 1. 목차
-------
* Reactive Programming 이란?
* Reactive Programming.. Why?
* 예제
* RxJS + vue-rx


# 2. Reactive Programming 이란?
-----------
> Reactive programming is programming with asynchronous data streams.
> You can listen to that stream and react accordingly.

* Reactive Programing은 기본적으로 모든 것을 스트림(stream)으로 본다. 이벤트, ajax call, 등 모든 데이터의 흐름을 시간순서에 의해 전달되어지는 스트림으로 처리한다. 즉, 스트림이란, 시간순서에 의해 전달되어진 값들의 collection 정도로 이해해 보자.
* 각각의 스트림은 새로 만들어(branch)져서 새로운 스트림이 될 수도 있고, 여러개의 스트림이 합쳐(merge) 질수 도 있다.
* 스트림은 map, filter과 같은 함수형 메소드를 이용하여, immutable하게 처리할 수 있다.
* 스트림을 listening 함으로써 데이터의 결과값을 얻는다. 이를 subscribe라고 표현한다.

# 3. Reactive Programming.. Why?
------
> Apps nowadays have an abundancy of real-time events of every kind that enable a highly interactive experience to the user. 
> We need tools for properly dealing with that, and Reactive Programming is an answer.

* 함수형으로 만들기 때문에, 하나의 함수는 그 역할 자체에 집중할수 있다.
* Promise의 장점을 극대화할 수 있다.
* Reactive Programming에서 갑자기 Promise를 이야기하는 이유는, RxJS의 Observable이 Promise와 개념적으로 유사하다. 
* 차이가 있다면 Promise는 단 하나의 value를 다룰 수 있지만, Observable은 다수의 value를 다룰 수 있다. (Single Class 와 Observable 의 차이와 비슷)

~~~javascript
myObservable.subscribe(successFn, errorFn);
myPromise.then(successFn, errorFn);
~~~

# 4. 예제
------

## 4-1) RxJS + vue-rx

~~~javascript
/*
 * RxJS + vue-rx
 * - Promise 기반의 axios Request 요청 및 UI Update 예제
 * - 구독자가 없을 경우 실행되지 않는 Cold Observable
 * - Operator 종류 확인
 */
 
<div id="app">
    <input type="text" placeholder="Search" v-model="search">
    // <v-stream> 은 vue-rx 에서 제공되는 디렉티브, Vue 스트림 시작점
    <button v-stream:click="search$">Search</button><br>
    <ul>
        <li v-for="item in results" : key="item">
            {{ item }}
        </li>
    </ul>
</div>
 
<script>
    // Request 호출
    new Vue({
        el: '#app',
        data: {
            search: ''
        },
        methods: {
        searchData (term) {
            return axios.get('http://ko.wikipedia.org/w/api.php', {
            params:{
                action: 'opensearch',
                origin: '*',
                search: term
                }
            })
        }
    },
 
    // Rx JS
    Rx.Observable.fromEvent(input, 'keyup')     // fromEvent 로 input element에서 keyup 이벤트 관찰
      .pluck('target', 'value')                 // input element에서 value 값을 가져옴
      .filter(text => text.length > 2)            // 조건문 추가 (if-else)
      .debounce(200 /* ms */)                   // 200ms 만큼 딜레이 후 Observable 을 반환
      .distinctUntilChanged()                   // 중복 Event 를 제거 {10, 20, 20, 20} => {10, 20}
      .flatMapLatest(searchData)                // 기존 Observable을 대신해서 가장 마지막의 새로운 Observable 로 반환
      .subscribe(                               // 위의 Observable을 구독, UI 업데이트
        ({ data }) => {
            results.innerHTML = ''
            const searchList = data[1]
            searchList.forEach(item => {
                const li = document.createElement('li')
                li.appendChild(document.createTextNode(item))
                results.appendChild(li)
            })
        },
        (error) => {
            console.log(error)
        }
    );
 
    // vue-rx 
    domStreams: ['search$'],                                // 위의 Vue 스트림을 인스턴스 내에서 사용 가능
    subscriptions() {                                       // vue-rx plugin 에서 제공하는 subscribe
        return {
            results: this.$watchAsObservable('search')      // watchAsObservable 로 Data 변화 발생 탐지
                .pluck('target', 'value')                  
                .filter(text => text.length > 2)         
                .debounce(200 /* ms */)                
                .distinctUntilChanged()                
                .flatMapLatest(searchData)
                .map(({ data }) => data[1])             
        }
    }
</script>
~~~

## 4-2) RxSwift + RxFlow

~~~swift
/*
 * RxSwift + RxFlow
 */

// QuizViewModel.swift
fileprivate var questionList : BehaviorRelay<[QuestionListData]>                  // QuestionListData Object 변수 선언
 
// Api 호출
func getData(){
        return quizSv.getSI2000()
        .mapObject(type: BaseResData.self)                                          // return value 를 Base Response Data 로 맵핑
        .map({ (baseResData) -> ResSI2000 in                                     // Base Response Data 를 각각의 Response Data 로 맵핑
            guard let value = self.isSuccess(ResSI2000.self, baseResData) else {
                return ResSI2000()
            }
            self.resData.accept(value)
            return value
        }).subscribe(onNext: { (resSI2000) in                                      
            self.isEmpty.accept(resSI2000.questionList.count == 0)
            self.questionList.accept(resSI2000.questionList)                        // QuestionListData 변수 값 추가 (onNext 이벤트 발생)
             
            self.isLoading.onNext(false)
            self.isLoading.onCompleted()
             
        }).disposed(by: disposeBag)                                                 // Disposed 로 Life Cycle 내부에서 구독 해제 명령
    }
 
 
// Controller 에서 받을 Getter 선언
func getQuestionList() -> BehaviorRelay<[QuestionListData]>{
        return questionList
}
 
// QuizViewController.swift
 
guard let viewModel = viewModel else {
            return
        }
 
 
viewModel.getQuestionList()
            .bind(to: collectionView.rx.items(cellIdentifier: cellID, cellType: QuizCVCell.self)) { row, data, cell in              // 위의 결과값을 CollectionView 에 Binding
                 
                cell.setData(self, data, false, row + 1, naviBtnCallBack: { (quizBtnType) in                                        // CollectionView 각 Cell UI 설정
                     
                    switch quizBtnType{
                         
                    // 다음문제 비활성화
                    case .NEXT_QUIZ_NORMAL:
                        Toast(text: "정답을 선택해주세요.").show()
                        break
                         
                    // 다음문제 활성화
                    case .NEXT_QUIZ_PRESSED:
                        self.collectionView.scrollToItem(at: IndexPath(item: row + 1, section: 0), at: .left, animated: true)
                        self.progressSequence.accept(1)
                        break
                         
                    // 결과보기 비활성화
                    case .SHOW_RESULT_NORMAL:
                        Toast(text: "정답을 선택해주세요.").show()
                        break
                         
                    // 결과보기 활성화
                    case .SHOW_RESULT_PRESSED:
                        self.steps.accept(SisaStep.showResultDialog(self.sendData))
                        break
                         
                    default :
                        break
                         
                    }
                     
                }, isUsePaging: { (isUseScroll) in
                     
                    // ScrollView 사용 여부 callback
                    self.collectionView.isScrollEnabled = isUseScroll
                     
                }) { (answer) in
                     
                    if answer != ""{
                        data.answer = answer
                        self.sendData[row] = data
                    }
                }
                 
                cell.setStackViewWidth(cell.frame.width)
                 
                 
        }.disposed(by: disposeBag)              // 구독 해제
 
/**
 * 번외로 Click Listener 처럼 특정 동작에도 Rx 를 접목할 수 있음.
 */
 
  // QuizViewController.swift
  btn_close.rx.tap.bind { [weak self] in            // btn_close id 값을 가진 버튼에 tap event 구독
      self?.steps.accept(SisaStep.quizCloseClick)   // RxFlow 의 Step에 Event 발생
  }.disposed(by: disposeBag)                        // Life Cycle 종료 시 구독 해제

~~~
