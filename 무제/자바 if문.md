
if(num.equals(num2)){}
num과 num2을 비교해 둘이 같으면 true반한
틀리면 false반한

String num = "아버지";
String num2 = "아버지"; 
num.equals(num2)는 true반한

String name1 = "아버지";
String name2 = "어머니"; 
name1.equals(naem2)는 false반한


equals() 값(내용)을 비교
`Object` 클래스에서 상속된 메서드지만,  
보통 `String`, `Integer`, `List` 등에서 **재정의(override)** 되어 있습니다.

숫자 비교할때는 == 을 쓴다

if(){}