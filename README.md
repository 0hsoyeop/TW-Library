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
- 개발인원: 5인
- 개발기간: *2023-03-06 ~ 2023-03-11*

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
4. 저장한 데이터를 사용하여 도서 관리 시스템을 운영하고<br>회원과 관리자, 도서 관련 정보를 갱신할 수 있다.

### 주요 데이터 파일
<table>
  <tr>
    <td>bannab.txt</td>
    <td>반납한 도서와 회원의 정보</td>
  </tr>
  <tr>
    <td>books.txt</td>
    <td>전체 도서 리스트</td>
  </tr>
  <tr>
    <td>member.txt</td>
    <td>회원 데이터</td>
  </tr>
  <tr>
    <td>rentLog.txt</td>
    <td>회원/비회원의 도서 대출 정보</td>
  </tr>
</table>


### 구현기능
- 회원 검색 및 삭제
- 도서 등록
- 도서 수정
- 도서 삭제

---
### 화면구성

#### 1. 메인화면
![메인화면](https://github.com/0hsoyeop/TW-Library/assets/131536077/d43d5826-9194-4fd4-ac6d-754c02870930)

#### 2. 도서검색 🔍
- 관리자, 회원, 비회원 공통 기능
- 책 제목, 저자, 출판사로 **검색 옵션을 지정**
- 선택한 옵션으로 **키워드 검색**하여 검색 결과 출력
- 검색 결과는 10개씩 **페이징 처리**
![책 검색](https://github.com/0hsoyeop/TW-Library/assets/131536077/2ba13653-8fd3-4da2-b5ec-20d48e6751d9)



#### 3. 도서대출 📖
- 앞서 도서 검색을 마친 후, 목록에서 도서 번호 확인하여** 원하는 책의 번호 입력**
- 도서 대출 확인 메시지에 **Y를 입력하면 대출 완료**
- 도서 대출 확인 메시지에 **N 입력하면, 메인화면으로 이동**
- 1인당 2권까지 대출 가능
- 이미 2권 대출 중인 경우 대출 불가
![회원 대출](https://github.com/0hsoyeop/TW-Library/assets/131536077/9401781f-00f2-4d6d-b5af-83a7270eb332)

---

### 담당 업무 상세보기
#### 신규도서 등록
- 책 제목, 저자, 출판사, 장르, 가격, 수량을 입력
- 기존 도서와 일치하지 않을 경우, 신규 도서로 등록
  
  ![image](https://github.com/0hsoyeop/TW-Library/assets/131536077/34587d66-2df5-4f31-91f9-3ca7dd0162fc)



#### 1. 책 데이터(books.txt)에 등록된 책인지 검사
- 등록할 책 제목, 저자, 출판사 장르, 가격을 입력한 후 BookVO 변수 bTemp에 저장하여 도서 데이터 파일(books.txt)을 대상으로 중복검사를 실행한다.
- BookVO: 책 정보를 가짐(제목, 저자, 출파사 등..)
- 제목, 저자, 출판사가 모두 동일하면 같은 책으로 간주한다.
- 이미 존재하는 책은 books.txt에 저장된 행의 인덱스를, 신규 도서는 0을 반환한다.
  
```java
private static void addExistBook(BookVO bTemp) {

	Scanner scan = new Scanner(System.in);
	System.out.print("동일한 책이 존재합니다. 수량을 입력하십시오. (ex: 3권일 경우 -> 3): ");
	int count = scan.nextInt();
	scan.skip("\r\n");
	BookVO bExist = BookDAO.getList().get(getIndex(bTemp));
	bExist.setCount(bExist.getCount() + count);
	System.out.println("도서 등록을 완료하였습니다.");
	UI.pause();
}//addExistBook
```

#### 2. 이미 있는 책 수량 변경
- getIndex() 메소드 리턴값이 0일 때 실행하는 메소드 
	- books.txt에 이미 동일한 정보의 데이터가 있다는 의미
- 해당 인덱스의 데이터 수량을 setCount()로 변경

```java
private static void addExistBook(BookVO bTemp) {
	Scanner scan = new Scanner(System.in);
	System.out.print("동일한 책이 존재합니다. 수량을 입력하십시오. (ex: 3권일 경우 -> 3): ");
	int count = scan.nextInt();
	scan.skip("\r\n");

	BookVO bExist = BookDAO.getList().get(getIndex(bTemp));
	bExist.setCount(bExist.getCount() + count);
	System.out.println("도서 등록을 완료하였습니다.");
	UI.pause();
}//addExistBook
```



#### 3. 신규 도서 등록하기
#### books.txt 몇번째 행에 저장할지 인덱스 구하기
- 장르별로 책은 50권씩 기본으로 등록되어 있다.
-  books.txt에 저장된 도서 목록의 장르별 개수를 각각 computer, art, science, inmoon, textbook에 저장
- 신규도서는 입력받은 장르의 책 개수를 getAddIndex()로 구한 뒤, 장르별 마지막 인덱스에 1을 더하여 새 인덱스를 구함

```java
//새로 등록할 책의 장르에 따라 인덱스 부여
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

#### 최종 등록하기
- 책 정보를 가진 BookVO 타입 변수에 setXXX() 메소드로 입력받은 값을 저장한다.

```java

	// 신규 도서를 책 목록(books.txt)에 추가하는 메소드
	private static void addNewBook(BookVO bTemp) {
		Scanner scan = new Scanner(System.in);
		System.out.print("수량을 입력하십시오 (ex: 3권일 경우 -> 3): ");
		int count = scan.nextInt();
		scan.skip("\r\n");
		bTemp.setCount(count);
		//새로 등록할 책의 정보를 bNew에 저장
		BookVO bNew = new BookVO();

		//bTemp에 임시 저장하였던 정보를 bNew에 대입
		BookDAO.getList().add(getAddIndex(bTemp), bTemp);

		bNew.setNum(bTemp.getNum());
		bNew.setTitle(bTemp.getTitle());
		bNew.setAuth(bTemp.getAuth());
		bNew.setPub(bTemp.getPrice());
		bNew.setGenre(bTemp.getGenre());
		bNew.setPrice(bTemp.getPrice());
		bNew.setCount(bTemp.getCount());

		System.out.println("신규 도서 등록이 완료되었습니다.");
		UI.pause();

	}//addNewBook

```



---
## 개발 스토리
### 회고
<table>
    <tr>
        <td>💡 첫 프로젝트, 빠르게 지나간 5일</td>
    </tr>
</table>

<img src = "https://github.com/0hsoyeop/TW-Library/assets/131536077/b98bbb1e-6c4c-4787-99f1-62298df49f23" width="540px" height="360px">



제 첫 프로젝트는 Java를 사용한 콘솔 프로젝트였습니다. 팀원들과 서로 모르는 부분을 알려주고 다같이 코드리뷰를 통해서 오류를 수정하는 과정이 매력적으로 느껴졌습니다.

저는 콘솔 프로젝트를 하면서 이런 것들을 가장 많이 고민했습니다.

- **필요한 데이터는 무엇일까?**
    
    도서 관리에 필요한 데이터가 무엇일지 고민하는 단계에서 ‘이제 정말 프로젝트를 하는구나!’ 하는 생각이 들었습니다. 실생활에서 접하는 상황을 Java에서 다루기 위해 어떤 항목으로, 어떤 형식으로 데이터를 구성할지 생각하면서 앞으로 구현할 코드에 대해 체계적으로 구상할 수 있었습니다. 예상치 못한 오류가 생겨서 확인해보니 더미 데이터에 오타가 있거나 형식이 다른 경우가 있었습니다. 개발을 원할하게 하기 위해서는 데이터를 수집하는 단계에서부터 꼼꼼하게 접근해야한다는 것도 깨달았습니다.

    - **DAO와 VO가 뭘까?**
    
    콘솔 프로젝트에서는 데이터 베이스를 사용하지 않고 lib 폴더에 .txt 형식으로 데이터를 저장해서 사용하였습니다.  BufferedReader로 데이터를 한 줄씩 읽는 방식으로 코드를 구현하였다. 
     그런데 경력이 있던 팀원 한 분이 DAO, VO를 사용해보는 건 어떤지 제안하셨습니다.  처음 듣는 개념에 다들 당황하니, 그분은 쉽게 풀어서 이렇게 알려주셨습니다.
    
    > ***DAO는 데이터를 쓰거나 수정하는 작업을 하고 VO는 데이터 변수를 저장 해놓는 곳이에요!***
    > 
    
    당시에는 자바를 배운지 얼마 되지 않은 시점이었기 때문에 VO와 DAO의 의미를 이해하기가 조금 어려웠습니다. **‘왜 두 개를 나눠서 쓰는걸까?’** 개발을 하면서 데이터를 읽고 쓸 때마다 VO에다 데이터를 수정하는 코드를 잘못 적기도 하고, 읽기 작업을 또 다른 파일에 새로 만들어서 쓰다가 지우기도 하며 시행착오가 많았습니다. 첫 프로젝트가 끝나갈 때까지 여전히 어리둥절했지만, 이렇게 데이터를 다루는 코드를 분리하여 사용함으로써 클래스마다 담당하는 업무가 명확해졌다는 것은 느낄 수 있었습니다. 그리고 이 방법은 웹 서버를 배울 때 MVC 모델을 이해하는데 큰 도움이 되었습니다.
