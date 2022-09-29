
# UndoManager

[Apple Docs](https://developer.apple.com/documentation/foundation/undomanager)
###### tags: `TIL`

> A general-purpose recoder of operations that enables undo and redo

실행 취소와 실행을 가능하게 하는 범용 작업 레코더.

### Declaration
```swift
class UndoManager: NSObject
```

### Overview

>You register an undo operation by calling one of the methods described in Registering Undo Operations. You specify the name of the object that's changing(or the owner of that object) and provide a closure, method, or invocation to revert its state

당신은 [Registering Undo Operations](https://developer.apple.com/documentation/foundation/undomanager#1663976)에 설명되어 있는 메서드중 하나를 호출해서 실행취소 연산을 등록한다.

> After you register an undo operation, you can call undo() on the undo manager to revert to the state of the last undo operation. When undoing an action, UndoManager saves the operations you reverted to so that you can call redo() automatically

실행취소 연산을 등록한 후, 당신은 마지막 undo연산으로 돌아가기 위해 undo manager의 undo() 를 호출할 수 있다. 액션을 실행취소할 때, 당신이 redo()를 자동으로 호출할 수 있도록 UndoManager는 당신이 되돌리는 작업을 저장한다.

> UndoManager is typically used by apps with UI interactions. For example, UIKit implements undo and redo in its text view object, making it easy for you to undo and redo actions in objects along the responder chain. UndoManager also serves as a general-purpose state manager, which can be used to undo and redo many kinds of actions. For example, an interactive command-line uility could use this class to undo the last command run, or a networking library could undo a request by sending another request that invalidates the previous one.

UndoManager는 일반적으로 UI 상호작용에 많이 쓰인다. 예를들어, UIKit은 textView 객체에 undo와 redo를 구현한다. 이는 응답체인을 따라 객체에서의 undo, redo 사용 을 쉽게 만든다. UndoManager는 또한 많은 종류의 액션을 undo/redo하는데 사용될 수 있는 general-purpos state manager 역할을 한다. 예를들어, 예를 들어, interactive comment-line 유틸리티는 이 클래스를 사용하여 마지막 명령 실행을 실행 취소할 수 있거나, 네트워킹 라이브러리는 이전 명령을 무효화하는 다른 요청을 보내 요청을 취소할 수 있습니다.
