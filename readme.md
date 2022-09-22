
# ObjectFreeList
빠른 할당 해제를 위한 free list <br>
내부 자료구조로 stack을 사용 <br>
멀티 스레드에서의 활용을 위해 내부적으로 lock을 걸고 사용 <br>

## 사전 세팅
- common.h
	- OBJECT_FREE_LIST_DEBUG
		> define 시 디버그 기능 활성화
- dump
	- 덤프 생성을 위해 사용 전에 CDump 객체 생성자 호출 필요

## 디버그 기능
 1. 할당 및 해제 시, 해당 노드의 alloc, free 함수 호출 위치 저장
 2. free 호출 시 아래 항목 체크
	 > 중복 해제 <br>
	 > 언더 / 오버 플로우 체크 <br>
 3. 소멸자에서 메모리 누수되는 노드 출력

## 함수
 - CObjectFreeList(bool, bool)
    > 생성자 <br><br>
    > 첫번째 인자로 할당 시 생성자 호출 여부를 설정. <br>
    true로 하면 최초 할당시에는 생성자 호출하지 않고, 실제로 allocObject를 호출할 때 생성자가 호출됨 <br>
    false로 하면 최초 할당시에만 생성자 호출됨 <br><br>
    > 두번째 인자로 해제 시 소멸자 호출 여부를 설정. <br>
    true로 하면 freeObject 호출할 때 소멸자를 호출함<br>
    false로 하면 실제로 메모리가 해제될 때 소멸자를 호출함
 - T* allocObject()
	 > 할당 요청, T 타입 포인터가 리턴
 - void freeObject(T* data)
     > 해제 요청, allocObject에서 리턴된 포인터를 넘겨줘야함
 - unsigned int getCapacity()
     > 실제로 할당된 전체 노드 수
 - int getUsedCount()
     > 사용자가 클래스에서 할당 받은 노드 수

## 사용법
```cpp

CDump dump;

struct stTest {
	int val1;
	int val2;
};

void main() {

	CObjectFreeList<stTest> freeList(false, false);

	stTest* ptr = freeList.allocObject();
	ptr->val1 = 1;
	freeList.freeObject(ptr);

}
```
