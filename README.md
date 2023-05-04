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

### 구현기능
- 회원 검색 및 삭제
- 도서 등록
- 도서 수정
- 도서 삭제

---

### 구현내용
#### 관리자 기능
#### 회원 검색 및 삭제
- 회원의 이름을 입력하여 회원 정보를 검색하고, 회원 데이터 파일(member.txt)에서 검색어와 일치하는 회원의 정보를 가져온다.
- 검색된 회원 중 동명이인이 있을 경우 회원 번호를 입력하여 삭제할 회원을 선택한다.
- 회원 삭제를 선택하면 회원 데이터 파일(member.txt)에서 해당 회원의 정보가 삭제된다.
- 책을 한 권이라도 대출 중인 회원은 반납이 완료될 때까지 회원 정보를 삭제할 수 없다. 


### 코드

```java
package com.twlibrary.service;

import java.util.ArrayList;
import java.util.Scanner;
import com.twlibrary.dao.MemberDAO;
import com.twlibrary.dao.RentLogDAO;
import com.twlibrary.save.MemberSave;
import com.twlibrary.vo.MemberVO;
import com.twlibrary.vo.RentLogVO;

/**[관리자]-[회원삭제]
 * <p> Main 클래스에서 doRemove()를 호출하면 회원정보를 삭제합니다.<br>
 * 하위 메소드는 실행 순서대로 isExist(), removeOrNot(), findRemoveIndex(), pause()가 있습니다. 
 * </p>
 * <p>회원을 검색하고 회원 번호를 입력하여 삭제할 회원을 선택합니다. 이때 대출 중인 회원은 삭제할 수 없습니다.
 * 회원 정보가 일치하고 대출 중인 책이 없다면, 회원 목록에서 관리자가 입력한 번호와 동일한 고유번호를 가진 회원을 삭제합니다.</p>
 * (예) MemberRemoveService.doRemove();
 * 
 * @author 오소엽, 김태완
 *
 */
public class MemberRemoveSerivce {
	 
	private static int removeIndex = -1;	//회원목록(member.txt)에서 삭제할 행의 번호
	private static int index = -1;			//removeIndex를 도출하기 위한 임시 변수
	
	//동명이인의 정보를 저장할 ArrayList
	private static ArrayList<MemberVO> sameName = new ArrayList<MemberVO>();

	/**
	 * <p>Main 클래스에서 호출하여 회원정보를 삭제하는 메소드입니다.</p>
	 * (예) MemberRemoveService.doRemove(); 
	 */
	public static void doRemove() {
		Scanner scan = new Scanner(System.in);
		System.out.print("검색할 회원의 이름을 입력하세요: ");
		String name = scan.nextLine();
		int size = isExist(name);
		findRemoveIndex(size);
	}//doRemove

	
	//동명이인의 수를 매개변수로 받음
	/**
	 * <p>동명이인을 저장하는 ArrayList sameName의 크기를 매개변수로 받아 removeIndex를 구하는 메소드입니다. 
	 * 대출 중인 책이 있으면 삭제할 수 없다는 메세지를 출력하고, 
	 * 대출 중인 책이 없을 경우 회원 목록에서 삭제할 인덱스를 removeIndex에 저장합니다. </p>
	 * 
	 * @param size : sameName의 크기, 즉 동명이인의 수
	 */
	public static void findRemoveIndex(int size) {

		if (size == 0) {
			System.out.println("존재하지 않는 회원입니다.");
			pause();
			
		} else if (size == 1) { //동명이인 없을 경우
			MemberVO m;
			Scanner scan = new Scanner(System.in);
			System.out.print("삭제하려는 회원의 아이디를 입력하세요: ");
			String inputID = scan.nextLine();
			String book = "";
			RentLogVO r;
			boolean flag = true;
			for(int j=0; j<RentLogDAO.getList().size(); j++) {
				r=RentLogDAO.getList().get(j);
					if(inputID.equals(r.getId())) {
						flag=false;
						book+=r.getBookTitle()+"\n";
					}
			}
			if(!flag) {
				System.out.println("대출한 책이 있어 삭제 불가능");
				System.out.println("빌리고 있는 책 목록");
				System.out.println(book);
				sameName = new ArrayList<MemberVO>();
				doRemove();
			} else {
				System.out.println("빌리고 있는 책이 없습니다.");
				//회원목록에서 일치하는 id가 있으면 그 때의 인덱스를 removeIndex에 저장하고, 그 행을 삭제하도록
				for (int i = 0; i < MemberDAO.getList().size(); i++) {
					if (inputID.equals(MemberDAO.getList().get(i).getId())) {
						removeIndex = i;
					}
				}
				book = "";
				
				while (removeOrNot(removeIndex)){}
			}
		} else if (size >= 2) { //동명이인이 존재할 경우
			MemberVO m;
			Scanner scan = new Scanner(System.in);
			System.out.print("삭제하려는 회원의 아이디를 입력하세요: ");
			String inputID = scan.nextLine();
			String book = "";
			RentLogVO r;
			boolean flag = true;
			for(int j=0; j<RentLogDAO.getList().size(); j++) {
				r=RentLogDAO.getList().get(j);
					if(inputID.equals(r.getId())) {
						flag=false;
						book+=r.getBookTitle()+"\n";
					}
			}
			if(!flag) {
				System.out.println("대출한 책이 있어 삭제 불가능");
				System.out.println("빌리고 있는 책 목록");
				System.out.println(book);
				sameName = new ArrayList<MemberVO>();
				doRemove();
			} else {
				System.out.println("빌리고 있는 책이 없습니다.");
				//회원목록에서 일치하는 id가 있으면 그 때의 인덱스를 removeIndex에 저장하고, 그 행을 삭제하도록
				for (int i = 0; i < MemberDAO.getList().size(); i++) {
					if (inputID.equals(MemberDAO.getList().get(i).getId())) {
						removeIndex = i;
					}
				}
				book = "";
				
				while (removeOrNot(removeIndex)){}
			}
			
		}
	} // remove

	
	/** <p>회원목록(member.txt)에서 제거할 행의 인덱스를 매개변수로 받아 회원삭제 여부를 결정하는 메소드입니다. 
	 * 회원을 삭제하거나 삭제 취소를 하는 경우 false를, 잘못된 값을 입력하는 경우 true를 리턴합니다. </p> 
	 * 
	 * @param index : member.txt에서 제거할 행의 인덱스
	 * @return 
	 */
	private static boolean removeOrNot(int index) {
		boolean b = false;
		Scanner scan = new Scanner(System.in);
		System.out.print("해당 회원의 정보를 삭제하시겠습니까? (Y/N): ");
		String input = scan.nextLine();

		if (input.equals("Y")) {
			System.out.println("삭제를 완료했습니다.");
			MemberDAO.getList().remove(removeIndex);
			removeIndex = -1;
			pause();
			b = false;
			return b;
		} else if (input.equals("N")) {
			System.out.println("삭제를 취소했습니다.");
			sameName.clear();
			pause();
			b = false;
			return b;
		} else {
			System.out.println("잘못된 입력입니다. 다시 시도하세요.");
			b = true;
			return b;
		}
	}

	/** <p>회원을 검색하여 일치하는 회원이 있는지 검사하는 메소드입니다. 관리자가 입력한 회원 이름을 매개변수로 받고 
	 * 그 회원이 회원 목록 상 몇번째에 행에 위치하는지 인덱스 값을 int로 반환합니다. 일치하는 회원이 있다면 해당 회원 정보를 
	 * 동명이인을 검사하는 sameName에 추가합니다. sameName의 크기가 1이면 일치하는 회원이 1명이므로 그 회원의 인덱스를 removeIndex에 저장합니다.
	 * sameName의 크기만큼 회원 정보 출력을 반복합니다.</p>
	 * 
	 * 
	 * @param name : 검색할 회원의 이름
	 * @return : 검색한 회원이 회원목록(member.txt)에서 위치하는 인덱스 값
	 */
	public static int isExist(String name) {
		MemberVO m;
		RentLogVO r;

		//member.txt의 몇번재 행에 일치하는 이름이 존재하는지
		for (int i = 0; i <MemberDAO.getList().size(); i++) {
			m = MemberDAO.getList().get(i); //i번째 행을 가져옴
			if (name.equals(m.getName())) { //i번째의 이름이 관리자가 입력한 이름과 같으면
				sameName.add(m); //회원목록의 i번째 행의 회원정보를 sameName에 추가
				index = i;
			}
		}
		
		if (sameName.size() ==1 ) {
			removeIndex = index;
		}

		System.out.println("[번호]\t[이름]\t\t[아이디]\t\t[생년월일]");

		for (int i = 0; i < sameName.size(); i++) { // 동명이인 목록 검사
			m = MemberDAO.getList().get(i);

			System.out.printf("%04d\t%-8s\t%-12s\t%-10s\n", i + 1, sameName.get(i).getName(),
					sameName.get(i).getId(), sameName.get(i).getBirth());
		}
		System.out.println();

		return sameName.size();

	}// isExist

	/**
	 * 메인화면으로 이동할 때 delay를 주는 메소드입니다.
	 */
	public static void pause() {
		Scanner scan = new Scanner(System.in);
		System.out.println("메인화면으로 돌아가시려면 [엔터]를 입력해주세요.");
		System.out.println("메인화면으로 이동합니다...");
		scan.nextLine();
	}// pause

}
```
---

#### 관리자 기능
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
