# TW-Library 
## 태완도서관 도서관리 전산 시스템 프로젝트
### 주제
파일 입출력 기반의 데이터 처리 자바 콘솔 프로젝트 (도서 목록 제공, 도서 대출·반납 관리 자동화 구현)

### 목적
콘솔 환경에서 도서 관리에 적합한 UI를 제공하고 데이터를 저장 및 관리하는 기능을 구현하고자 함

### 사용언어
<img src="https://img.shields.io/badge/Java-007396?style=flat&logo=Java&logoColor=white" />

### 개발도구
<img src="https://img.shields.io/badge/Eclipse IDE-2C2255?style=flat&logo=eclipseide&logoColor=white"/>

### 개발기간 및 인원
- 개발인원: 4인
- 개발기간: *2023-03-06 ~ 2023-03-11 (5일)*

### 개발환경
<table>
  <tr>
    <td>2S version (platform)</td>
    <td>Windows Intel(R) i5-8250 CPU, 8GB, x64</td>
  </tr>
  <tr>
    <td>JAVA version(Language)</td>
    <td>JDK 11.0.2</td>
  </tr>
  <tr>
    <td>Eclipse version(Development tool)</td>
    <td>Version: 2021-03 (4.19.0)</td>
  </tr>
</table>

### 개요
1. 사용자는 회원, 비회원 관리자로 구분한다.
2. 회원과 비회원은 대출과 반납 서비스를 이용할 수 있다.
3. 회원은 희망도서 신청 기능과 이벤트 참여 등의 부가적인 기능을 제공한다.
4. 저장한 데이터를 사용됨
-  books.txt에 저장된 도서 목록의 장르별 개수를 각각 computer, art, science, inmoon, textbook에 저장
- 장르별 마지막 인덱스에 해당하는 BookVO 객체 b2의 고유번호에 1을 더하여 새 인덱스를 구함
```sql
if (bTemp.getGenre().equals("컴퓨터")) {
			addIndex = computer;
			
			BookVO b2 = BookDAO.getList().get(addIndex-1);
			newNum = Integer.parseInt(b2.getNum())+1;
			
			bTemp.setNum(String.valueOf(newNum));
			
		} else if (bTemp.getGenre().equals("예술")) {
			addIndex = computer + art;
			BookVO b2 = BookDAO.getList().get(addIndex-1);
			newNum = Integer.parseInt(b2.getNum())+1;
			
			bTemp.setNum(String.valueOf(newNum));
			

		} else if (bTemp.getGenre().equals("과학")) {
			addIndex = computer + art + science;
			BookVO b2 = BookDAO.getList().get(addIndex-1);
			newNum = Integer.parseInt(b2.getNum())+1;
			
			bTemp.setNum(String.valueOf(newNum));
			
		} else if (bTemp.getGenre().equals("인문")) {
			addIndex = computer + art + science + inmoon;
			BookVO b2 = BookDAO.getList().get(addIndex-1);
			newNum = Integer.parseInt(b2.getNum())+1;
			
			bTemp.setNum(String.valueOf(newNum));
			
		} else if (bTemp.getGenre().equals("수험서")) {
			addIndex = computer + art + science + inmoon + textbook;
			BookVO b2 = BookDAO.getList().get(addIndex-1);
			newNum = Integer.parseInt(b2.getNum())+1;
			
			bTemp.setNum(String.valueOf(newNum));
		
		}
		
```

