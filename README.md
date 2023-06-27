# TW-Library 
## 태완도서관 도서관리 전산 시스템 프로젝트
### 주제
파일 입출력 기반의 데이터 처리 자바 콘솔 프로젝트 (도서 목록 제공, 도서 대출·반납 관리 자동화 구현)

### 목적
콘솔 환경에서 도서 관리에 적합한 UI를 제공하고 데이터를 저장 및 관리하는 기능을 구현하고자 함

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

### 관리자 기능
#### 도서 등록
- 등록할 책 제목, 저자, 출판사 장르, 가격을 입력한 후 BookVO 변수 bTemp에 저장하여 도서 데이터 파일(books.txt)을 대상으로 중복검사를 실행한다.
- 이미 등록된 책일 경우 기존 수량(getCount())에 1을 더하여 등록한다.
- 신규 도서는 앞서 입력한 책 정보에 수량을 setCount()로 지정한다. 
- 장르별로 책은 50권씩 기본으로 등록되어 있다. 
- 신규도서는 입력받은 장르의 책 개수를 getAddIndex()로 구한 뒤, 수량을 입력하여 그만큼의 책 번호(index)를 부여한다.


### 코드

```java
package com.twlibrary.service;
import java.util.Scanner;
import com.twlibrary.dao.BookDAO;
import com.twlibrary.ui.UI;
import com.twlibrary.vo.BookVO;
/**
 * <p>[관리자]-[도서 등록] 화면에서 사용하는 클래스입니다. 
 * Main 클래스에서 addBook()을 호출하여 도서등록을 할 수 있습니다. 
 * 등록할 책의 정보를 입력받은 뒤 getIndex() 메소드에서 중복검사를 하고 
 * 기존에 있는 책이라면 addExistBook() 메소드 실행, 중복된 책이 없으면 getAddIndex()로 책 목록에 추가할 위치를 구한 뒤 
 * addNewBook()로 책의 정보를 BookVO에 새로 등록합니다.</p>
 * <p>(예) BookAddService.addBook(); </p> 
 * @author 오소엽
 *
 */
public class BookAddService {
	
	public static void addBook() {
		BookVO bTemp = new BookVO();	//등록할 책의 정보를 임시로 저장할 BookVo
		Scanner scan = new Scanner(System.in);
		System.out.println("등록할 책의 정보를 입력하십시오.");
		System.out.print("책 제목: ");
		String title = scan.nextLine();
		System.out.print("저자: ");
		String author= scan.nextLine();
		System.out.print("출판사: ");
		String pub = scan.nextLine();
		System.out.print("장르: ");
		String genre = scan.nextLine();
		System.out.print("가격: ");
		String price = scan.nextLine();
		
		bTemp.setTitle(title);
		bTemp.setAuth(author);
		bTemp.setPub(pub);
		bTemp.setGenre(genre);
		bTemp.setPrice(price);
		
		getIndex(bTemp);
		
		if(getIndex(bTemp) == -1) { //해당하는 책이 없다면 => 신규도서 등록
			addNewBook(bTemp);
		
		} else { 					//해당하는 책이 이미 있다면 => 기존 도서 등록
			addExistBook(bTemp);
		}
		
	}//getBookInfo
	
	/**
	 * getIndex() 메소드 실행 후 리턴값이 -1이면 기존에 있는 도서를 추가하는 메소드입니다. 
	 * 
	 * @param 새로 등록할 책의 정보를 저장한 bTemp
	 */
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
	
	/**신규 도서를 책 목록(books.txt)에 추가하는 메소드입니다.
	 * 
	 * @param 새로 등록할 책의 정보를 저장한 bTemp
	 */
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
	/**<p>새로 등록할 책이 책 목록(books.txt)에 존재하는지 검사하는 메소드입니다.
	 * 매개변수로는 임시저장한 bTemp를 받고 int를 반환하는 메소드입니다. <br>
	 * 책 제목, 저자, 출판사가 동일하면 같은 책으로 간주합니다.
	 * 
	 * </p>
	 * 
	 * @param 새로 등록할 책의 정보를 저장한 bTemp
	 * @return 책이 존재하지 않으면 -1을, 존재하면 그 위치의 인덱스를 반환합니다.
	 */
	public static int getIndex(BookVO bTemp) {
		int index = -1;
		
		for(int i=0; i<BookDAO.getList().size(); i++) {
			BookVO b = BookDAO.getList().get(i);
			if(bTemp.getTitle().equals(b.getTitle()) &&
					bTemp.getAuth().equals(b.getAuth())&&
					bTemp.getPub().equals(b.getPub())) {
				
				index = BookDAO.getList().indexOf(b);
			}
		}
	
			
		return index;
		
	} //getIndex;
	
	
	/**<p>신규 도서를 책 목록(books.txt)에 저장할 위치를 구하는 메소드입니다. 
	 * 매개변수로 BookVO를 받고 인덱스값을 int로 반환합니다.</p> 
	 * 
	 *<p>신규 도서를 책 목록(books.txt)에 저장할 인덱스를 구하는 메소드입니다. 
	 *매개변수로 BookVO를 받고 인덱스 값을 int로 반환합니다.</p>  
	 *<p>books.txt에 저장된 도서 목록의 장르별 개수를 각각  computer, art, science, inmoon, textbook에 저장합니다. 
	 *새로 등록할 책의 정보를 임시저장한 BookVO bTemp의 장르를 '컴퓨터', '예술', '과학', '인문', '수험서'와 비교합니다. 
	 *장르별 목록의 마지막 인덱스는 (장르별 개수 -1)입니다. </p> 
	 *<p>(예) '컴퓨터' 장르 50개가 books.txt에 행마다 존재 => 마지막 인덱스 값은 49 </p>
	 *장르별 마지막 인덱스에 해당하는 BookVO 객체 b2를 생성합니다. b2의 고유번호에 1을 더하여 새 인덱스를 구합니다. </p>
	 * 
	 * @param 관리자가 입력한 책의 정보를 저장한 bTemp
	 * @return books.txt에 새로 저장할 위치를 반환
	 */
	public static int getAddIndex(BookVO bTemp) {
		
		int computer = 0;
		int art = 0;
		int science = 0;
		int inmoon = 0;
		int textbook = 0;
		
		int addIndex = -1;
		int newNum = 0;
		
		
		
		//책 목록을 한줄씩 읽어서 장르별 개수를 셈
		for(BookVO b : BookDAO.getList()) {
			if(b.getGenre().equals("컴퓨터")) {
				computer++;
			} else if (b.getGenre().equals("예술")) {
				art++;
			} else if (b.getGenre().equals("과학")) {
				science++;
			} else if (b.getGenre().equals("인문")) {
				inmoon++;
			} else if (b.getGenre().equals("수험서")) {
				textbook++;
			}
		}
		
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
		
		return addIndex;
		
	}//getAddIndex
	
	
	
}
```
