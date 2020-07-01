---
title: "Swift Input/Output "
date: 2020-06-30 11:48:28 -0400
categories: etc
---

# 기존 소스 방식

~~~swift
class tmp_ViewModel: ViewModel {

    lazy var ob_resData : Observable<ResSI0001> = getData()

    private var sisaList : BehaviorRelay<[SisaListData]> = BehaviorRelay(value: [])

    init() { }

    let disposeBag = DisposeBag()

    let tmpSv = tmp_Sv()    

    var isLoading = BehaviorSubject(value: true)



    private func getData() -> Observable<ResSI0001> {}

    func getGenLists(sisaCgs: [String] = []) -> BehaviorRelay<[SisaListData]>{}

    func getSearchGenLists(sisaCgs: [String], searchText: String) -> Observable<[SisaListData]> {}

    func getCategorySortType() -> Observable<[SisaCgListData]> {}



    func saveBookMark(sisaType: SisaType, idx: Int) {//} -> Observable<> {}
}
~~~

# Input / Output 적용

~~~swift
protocol ViewModelType {

    associatedtype Input

    associatedtype Output

 

    var input: Input { get }

    var output: Output { get }

}

class tmp_ViewModel: ViewModel, ViewModelType {

struct Input {}

    struct Output {

        let sisaLists: Observable<[SisaListData]>    

        let sisaCgList: Observable<[SisaCgListData]>

    }

    

    let input: Input

    let output: Output

    

    private let tmpSv = tmp_Sv()

    private let disposeBag = DisposeBag()



    init() {

        let data = termSv.getSI0001()

            .mapObject(type: BaseResData.self)

            .map({ (baseResData) -> ResSI0001 in

                if let value = Mapper<ResSI0001>().map(JSONObject: baseResData.message?.body) {

                    return value

                } else {

                    return ResSI0001()

                }

            })

        

        let sisaLists = data.map({ $0.sisaList })

        let sisaCgList = data.map({ $0.sisaCgList })

        

        input = Input()

        output = Output(sisaLists: sisaLists, sisaCgList: sisaCgList)

    }

    

    func saveBookMark(sisaType: SisaType, idx: Int) {

        termSv.saveBookMark(sisaType: sisaType, idx: idx)

            .subscribe({ _ in }).disposed(by: disposeBag)

    }
}
~~~


* protocol을 사용하여 임의의 input, output 을 만듦.

* ViewModelType을 상속받은 후 input, output을 struct 변경 후 따로 구분하여 사용

* ViewController에서는 viewmodel.output.xxx 으로 접근 가능.



# 자세한 내용

* 일반화된 패턴이 아닌 가독성 혹은 방향성에 따라 달라질 수 있음.

* 편한 방법을 찾아서 input과 output을 구별 하는 방법으로 쓰면 됨.

<https://mrgamza.tistory.com/509>
