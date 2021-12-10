# MB_Spring

<h2> index </h2>

- c태그를 이용해서 로그인을 해야만 글목록 회원 목록이 보이게 했다.
- 조건을 걸어 관리자인 admin로 로그인을 해야지만 전체회원 목록이 보인다.
- 일반 사용자로 로그인 할 시 mypage가 보인다.
- 로그인을 안하면 환영합니다와 회원가입이 보인다.
<c:choose>
	<c:when test="${sessionScope.loginId eq 'admin'}">
		<a href="/member/findAll">전체회원목록</a> <br>
		<a href="/board/paging?page=${page}">글목록</a><br>
		로그인:${sessionScope.loginId}
	</c:when>
	<c:when test="${sessionScope.loginId != null }"> 
		로그인:${sessionScope.loginId}
		<a href="/member/mypage?m_number=${loginNumber}">MyPage</a><br>
		<a href="/board/paging?page=${page}">글목록</a><br>
	</c:when>
	<c:otherwise>
		<h2>환영합니다</h2>
		<a href="/member/save">회원가입</a>
	</c:otherwise>
</c:choose>


## view-member
<h2> save </h2>
-jquery를 사용하기 위한 문장
<script src="https://code.jquery.com/jquery-3.6.0.js"></script>

	function idDp(){
		const id = document.getElementById("m_id").value;
		const Ch = document.getElementById("idCh");
		
		$.ajax({
			type:'post',
			url: 'idDp',
			data: {'m_id':id},
			datatype: 'text',
			success: function(result){
				if(result=="ok"){
					Ch.style.color='green';
					Ch.innerHTML='GOOD';
				}else{
					Ch.style.color='red';
					Ch.innerHTML='이미 있는 아이디 입니다.';
				}
			},
			erroe: function(){
				console.log("이러지마 제발");
			}
		});
		
		
	}


	<form acction="save" method="post" enctype="multipart/form-data">
  - onblur로 함수를 호출함.
	아이디: <input type="text" name= "m_id" onblur="idDp()" id="m_id">
			<span id="idCh"></span>
      
 <h2>login</h2>
 - 로그인 하는 input이 있음
 
 <h2>mypage</h2>
 번호로 받아온 값들을 출력하게 했고 수정하는 링크를 만듬.
 
 <h2>update</h2>
 비밀번호가 일치해야 수정을 가능하게 만듬
 	function update(){
		const pw=document.getElementById('pw').value;
		const pwDB='${update.m_pw}';
		
		if(pw==pwDB){
			update_form.submit()
		}else{
			alert('비밀번호가 틀립니다.')
		}
	}
  
  ## view - board
  
  <h2>insert</h2>
  글을 등록 할 수 있게함.
  
  <h2>findAll</h2>
  
 - 제목을 누르면 글 상세목록이 나오게함.
 <a href="/board/detail?b_number=${b.b_number}">${b.b_title}</a>
 
 - c태그를 이용해서 관리자는 삭제만 사용자는 자기글 삭제 및 수정만 가능하게 했음
          <c:choose>
						<c:when test="${sessionScope.loginId eq 'admin'}">
							<td><a href="delete?b_number=${b.b_number}">삭제</a></td>
						</c:when>
						
						<c:when test="${sessionScope.loginId eq b.b_writer}">
							<td><a href="delete?b_number=${b.b_number}">삭제</a></td>
							<td><a href="update?b_number=${b.b_number}">수정</a></td>
						</c:when>
					</c:choose>
          
- 페이징
<div>
		<c:choose>
			<c:when test="${paging.page<=1 }">
				[이전]&nbsp;
			</c:when>
			<c:otherwise>
				<a href="paging?page=${paging.page-1}">[이전]</a>
			</c:otherwise>
		</c:choose>

		<c:forEach begin="${paging.startPage}" end="${paging.endPage}" var="i"
			step="1">
			<c:choose>
				<c:when test="${i eq paging.page}">
					${i}
				</c:when>

				<c:otherwise>
					<a href="paging?page=${i}">${i}</a>
				</c:otherwise>
			</c:choose>
		</c:forEach>

		<c:choose>
			<c:when test="${paging.page>=paging.maxPage}">
				[다음]
			</c:when>
			<c:otherwise>
				<a href="paging?page=${paging.page+1}">[다음]</a>
			</c:otherwise>
		</c:choose>
	</div>

<h2>detail</h2>
- 댓글을 작성하고 작성된 모든 댓글을 볼 수 있게했음.
<div id="comment-writer">
	<input type="hidden" id="b_number" value="${detail.b_number }">
		<input type="text" id="c_writer" value="${sessionScope.loginId}" readonly>
		<textarea type="text" id="c_contents"  name="c_contents" row="15px" placeholder="내용"></textarea>
		<button id="comment-write-btn">댓글등록</button>
	</div>

	<div id="comment-list">
		<table>
			<thead>
				<tr>
					<th>댓글번호</th>
					<th>작성자</th>
					<th>내용</th>
					<th>작성시간</th>
				</tr>
			</thead>

			<tbody>
				<c:forEach items="${commentList}" var="comment">
					<tr>
						<td>${comment.c_number}</td>
						<td>${comment.c_writer}</td>
						<td>${comment.c_contents}</td>
						<td>${comment.c_date}</td>
					</tr>
				</c:forEach>
			</tbody>
		</table>
	</div>
</body>

<script>
	$("#comment-write-btn").click(function() {
		console.log("댓글등록버튼 클릭");
		const writer = document.getElementById('c_writer').value;
		const commentContents = document.getElementById('c_contents').value;
		console.log(commentContents);
		const boardNumber = document.getElementById('b_number').value;
		console.log(commentContents, boardNumber);
		
		$.ajax({
			type : 'post',
			url : '/comment/insert',
			data : {
				'c_writer' : writer,
				'c_contents' : commentContents,
				'b_number' : boardNumber
			},
			dataType : "json",
			success : function(result) {
				let output = "<table>";
				output += "<tr><th>댓글번호</th>";
				output += "<th>작성자</th>";
				output += "<th>내용</th>";
				output += "<th>작성시간</th></tr>";
				for ( let i in result) {
					output += "<tr>";
					output += "<td>" + result[i].c_number + "</td>";
					output += "<td>" + result[i].c_writer + "</td>";
					output += "<td>" + result[i].c_contents + "</td>";
					output += "<td>" + result[i].c_date + "</td>";
					output += "</tr>";
				}
				output += "</table>";
				document.getElementById('comment-list').innerHTML = output;
				document.getElementById('c_contents').value = '';
			},
			error : function() {
				console.log('?')
			}
		});
	});

<h2>update</h2>
-작성자는 자기 아이디가 나오게끔 함.
<input type="text" name="b_writer" value="${sessionScope.loginId}" readonly>

## Controller-Member
- save
  1. save 페이를 요청하고 save에서 @ModelAttribute를 통해 MemberDTO의 값을 담는다.
 
 - 아이디 중복체크
  1. @RequestParam을 통해 m_id를 요청하고 서비스임플에서 결과가 null이면 통과 아니면 통과하지 못한다.
  2. @ResponseBody=> 리턴을 result로 했는데 이 result는 1의 결과이다. ResponseBody를 통해 서비스 임플에서
    받아온 결과 값 그대로를 리턴한다.
    
  - login
  1. 로그인 페이지를 요청하고 로그인 페이지에서 save와 같이 DTO 값을 담는다.
  2. 맵퍼에서 아이디와 비밀번호 값을 비교 후 서비스 임플에서 null값이 나오면 session.setAttribute를 통해 
      m.getM_id()와 loginMember의 정보를 유지시킨다
  3. 로그인에 성공할 경우 페이징으로 처리된 글목록으로 넘어가고 아니면 로그인페이제 머문다.
  
  - logout
  1. HttpSession 으로 저장되어있는 로그인정보를 session에 담는다.
  2. session.invalidate를 통해 로그아웃을 한다.
  
  - findAll
  1. 전체 목록을 출력하는 것으로 List사용
  2. Model model을 통해서 넘겨받은 리스트 값을 model.addAttribute("key", value)로 저장한다.
  
  -delete
  1. m_number를 @RequestParam을 통해 요청
  
  - mypage
  1. m_number를 @RequestParam을 통해 요청
  2. 하나의 결과만 출력하는 것이므로 List가 아니라 DTO
  3. Model 을통해 리ㅣ턴받은 값을 저장
  
  -update
  1. mypage에 있는 값을 불러옴
  2. 그 값을 Model을 통해 감아줌
  3. 수정한 내용을 MemberDTO에 담음.
  4. redirect:/member?m_number="+m.getM_number로 리턴을 해줌
  
  ## Controller-Board
  
  -insert
  1. 글등록페이지 요청 후 글등록 값을 디티오에 담은 후 페이징 처리
  2. return "redirect:/board/paging"
  
  -paging
  1.@RequestParam(value = "page", required = false, defaultValue = "1") int page
    실패해도 처리가 되도록했고 기본값을 1로 지정해 놓음
   2. PageDTO 에 페이징 값을 담음
   3. List<BoardDTO b> 를 통해 사용자가 선택한 페이지의 모든 리스트가 나오게함
   4. model을 통해 List, paging 값을 담음
   5. 서비스임프
   private static final int PAGE_LIMIT = 3; -> 한 화면에 보여질 갯수
	private static final int BLOCK_LIMIT = 5;
  
  	@Override
	public List<BoardDTO> pagingList(int page) {
		int pagingStart = (page-1)*PAGE_LIMIT;
		Map<String, Integer> pagingParam = new HashMap<String, Integer>();
		pagingParam.put("start", pagingStart);
		pagingParam.put("limit", PAGE_LIMIT);
		List<BoardDTO> pagingList = br.pagingList1(pagingParam);
		
		return pagingList;
	}

	@Override
	public PageDTO paging(int page) {
		int boardCount = br.boardCount();
		int maxPage = (int)(Math.ceil((double)boardCount/PAGE_LIMIT));
		int startPage =(((int)(Math.ceil((double)page/BLOCK_LIMIT)))-1)*BLOCK_LIMIT+1;
		int endPage = startPage + BLOCK_LIMIT-1;
		if(endPage>maxPage)
			endPage = maxPage;
		PageDTO paging = new PageDTO();
		paging.setPage(page);
		paging.setStartPage(startPage);
		paging.setEndPage(endPage);
		paging.setMaxPage(maxPage);
		return paging;
	}
  
  - search
  1. searchtype와 keyword 값을 요청
  2. List 에searchtype와 keyword의 값을 담음
  3. model을 통해 값을 저장
  
  -detail
  1. 상세보기위한 글 번호를 요청
  2. 조회수를 늘려줘야하는데 mapper에서 b_hits=b_hits+1을 해줌
  3. List<CommentDTO>를 통해서 댓글 전체 목록도 출력되게함.
  4. model을 통해 댓글과 상세값을 저장
  
  - delete
  1. 하나의 값만 삭제하므로 BoardDTO에 값을 담고 m_number를 요청
  
  -update
  1. 멤버에서와 비슷하게 상세글 볼 번호를 요청 하고 디테이을 가져옴
  2. 그 값을 model에 저장
  3. 수정후 model을 통해 값을 담음
  
  ##Controller-Comment
  
  - insert
  1. 매개변수 타입이 List인 이유는 리턴을 해줄때 그 값을들 넘겨줘야하기 때문이다.
  2. 댓글을 저장하기위해 DTO에 
   
   
