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


##### 코드

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
