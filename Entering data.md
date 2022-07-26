# Entering data
### When you need information from people, design ways that make it easy for them to provide it without making mistakes. 
사람들에게 정보를 필요로 할 때, 그들에게 실수를 만들지 않고 쉽게 제공하는 방법들을 설계해라
![](https://i.imgur.com/NlyhmM1.jpg)
Entering information can be a tedious process regardless of the interaction method people use. Improve the experience by pre-gathering as much information as possible to minimize the amount of data people need to supply and by supporting all available input methods so people can choose the method that works for them

정보를 입력하는 것은 사람들이 사용하는 상호작용 메서드에 상관없이 지루한 과정일 수 있다. 가능한 많은 정보를 사전수집 하므로써 사람들이 제공해야 하는 정보의 양을 줄이고, 가능한 모든 입력 메서드를 지원 하므로써 사람들이 그들이 사용 할 메서드를 선택할 수 있도록 하여 경험을 향상시킨다.

## Best practices

**Get information from the system whenever possible.** Don't ask people to provide information that you can gather automatically - such as from settings- or with their permission, such as their location or calendar information

가능하면 언제든지 시스템에서 정보를 얻어라! "설정이나 위치, 달력정보 등의 허가" 에서 자동으로 얻을 수 있는 정보에 대해 사람들에게 묻지 말아라

**Be clear about the data you need.** For example, you might display a prompt in a text field - like "username@company.com" - or provide an introductory label that describes the information, like "Email." You can also prefill fields with reasonable default values, which can minimize decision making and speed data entry

필요한 정보를 명확히 하라. 예를들어 textfield에 prompt를 띄운다 "username@company.com" 또는 정보를 묘사하는 소개 레이블을 제공하라 "Email". 또한 합당한 기본값으로 미리 채워진 field를 사용할 수 있다. 이는 의사결정을 최소화 하고 데이터 입력 속도를 높일 수 있다.

**Use a secure text-entry field when appropriate**. If your app or game needs sensitive data, use a field that obscures people's input as they enter it, typically by displaying a small filled circle symbol for each character. For developer guidance, see SecureField. In tvOS, you can also configure a digit entry view to obscure the numerals people enter (for developer guidance, see isSecureDigitEntry)

적절한 경우 안전한 텍스트 입력 필드를 사용해라. 만약 앱이나 게임이 민감한 데이터를 필요로 한다면, 사람들이 입력할 때 불명료하게 보이도록 하는 field를 사용해라, 일반적으로 각 character에 작은 채워진 원 symbol을 나타낸다. 개발자 가이드는 see SecureField. tvOS 에서는 digit entry view 도 사용할 수 있다.

**Never prepopulate a password field.** Always ask people to enter their password or use biometric or keychain authentication. For guidance, see Managing accounts

절대 비밀번호 창을 미리채우기 말아라. 항상 패스워드 또는 생체신호, 키체인 인증을 통해 패스워드를 입력하도록 요청해라. Managing accounts 에 가이드가 나와있다.

**Dynamically validate field values.** People can get frustrated when they have to go back and correct mistakes after filling out a lengthy form. When you verify values as soon as people enter them - and provide feedback as soon as you detect a problem - you give them the opportunity to correct errors right away. For numeric data in particular, consider using a number formatter, which automatically configures a text field to accept only numeric values. You can also configure a formatter to display the value in a specific way, such as with a certain number of decimal places, as a percentage, or as currency.

필드값을 동적으로 검증해라. 사람들은 긴 형태의 폼을 채워놓은 뒤 실수를 고치기 위해 뒤로가기를 해야할 때 낙담할 수 있다. 사람들이 입력 하자마자 값을 검증하고, 문제를 감지 하자마자 피드백을 제공해준다면, 에러를 바로 고칠 기회를 주는 것이다. 특히 숫자 데이터에, 텍스트필드에 오직 숫자값만 받을 수 있게 자동으로 구성해주는 "numberFormatter" 사용을 고려해봐라. 또한 값을 구제적인 방법으로 보여주기 위해 formatter를 구성할 수 있다. 예를들면 특정 소수점 이하 자릿수, 백분율 또는 통화와 같이.

**When possible, offer choices instead of requiring text entry.** It's usually easier and more efficient to choose from lists of options than to type information, even when a keyboard is conveniently abvailable. When it makes sense, condider using a picker, menu, or other selection component to give people an easy way to provide the information you need.

가능하면, 텍스트 입력을 요구하는 대신에 선택을 제공해라. 선택 리스트에서 고르는 것은 보통 정보를 입력하는 것보다 쉽고 효과적이다, 심지어 키보드가 편하게 사용 가능함에도 말이다. 그게 말이 될 때 , 사람들에게 쉬운 방법으로 정보(내가 필요한)를 제공하도록 picker, menu 또는 다른 선택요소를 사용을 고려해라.

**As much as possible, let people provide data by dragging and dropping it or by pasting it.** Supporting these interactions can ease data entry and make your experience feel more integrated with the rest of the system.

가능한 많이 사람들에게 드래그엔 드롭 또는 붙여넣기 방식으로 정보를 제공하도록 만들어라. 이 상호작용을 지원하는 것은 데이터 입력을 쉽게하고, 나머지 시스템들과 더욱 통합되었다고 느끼도록 한다.

**When data is truly required, enable advancement only after collecting it.** Before enabling a Next or Continue button, make sure all required fields have values. When the button becomes enabled, people know that it's time to proceed.

데이터가 정말로 요구된다면, 오직 데이터를 받은 뒤에만 다음을 활성화해라. 다음 또는 계속 버튼을 활성화 하기 전에, 요구되는 모든 필드에 값이 있는지 확실히 한다. 버튼이 활성화 된다면, 사람들은 다음으로 넘어갈 차례라는 것을 안다.
