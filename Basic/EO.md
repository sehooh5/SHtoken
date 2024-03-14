#  EO



### ERC-20(Ethereum Request for Comment20)

- ERC-20은 일종의 일련번호

- ERC-20은 이더리움 블록체인 네트워크에서 정한 표준 토큰 스펙

- EIPs에서 관리하는 공식 프로토콜

- ERC-20 토큰은 이더리움과 교환할 수 있고 이더리움 지갑으로 전송이 가능하다

- ERC20에 대한 표준 문서 [[링크](https://eips.ethereum.org/EIPS/eip-20)]

- Sample code : 

  ```
  // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.17;
  
  import "./IERC20.sol";
  
  contract ERC20 is IERC20 {
      //총 공급량 변수 선언 
      uint public totalSupply;
      //소유자별로 토큰 잔액을 내부 테이블로 구현한다. 
      //이러면 토큰 컨트랙트에서 토큰을 소유한 사람을 추적할 수 있다. 
      mapping(address => uint) public balanceOf;
      //허용량의 데이터 매핑이다. 
      // ERC20 토큰을 사용하면 토큰 소유자가 권한을 위임자에게 위임하여 소유자의 잔액에서 특정 금액을 지출할 수 있다.  
      // ERC20 컨트랙트는 기본 키가 토큰 소유자의 주소고 지출자 주소와 허용 한도에 매핑되는 2차원 매핑으로 허용량을 추적한다. 
      mapping(address => mapping(address => uint)) public allowance;
      // 사람이 읽을 수 있는 토큰 
      string public name = "SHEO2";
      //사람이 읽을 수 있는 기호 
      string public symbol = "SHEO2";
      //토큰 양을 나눌 수 있는 소수 자릿수 반환 
      uint8 public decimals = 18;
  
      //출금 함수 
      function transfer(address recipient, uint amount) external returns (bool) {
          balanceOf[msg.sender] -= amount;
          balanceOf[recipient] += amount;
          emit Transfer(msg.sender, recipient, amount);
          return true;
      }
  
      //approve 후 transferFrom을 사용하는 트랜잭션 워크플로우 
      //토큰 소유자가 제어를 다른 주소에 위임할 수 있게 해준다. 이것은 제어를 토큰 배포 컨트랙트에 위임하는데 
      //가장 많이 사용되지만 거래소에서도 많이 사용된다. 
      function approve(address spender, uint amount) external returns (bool) {
          allowance[msg.sender][spender] = amount;
          emit Approval(msg.sender, spender, amount);
          return true;
      }
  
      //approve 후 transferFrom을 사용하는 트랜잭션 워크플로우 
      function transferFrom(
          address sender,
          address recipient,
          uint amount
      ) external returns (bool) {
          allowance[sender][msg.sender] -= amount;
          balanceOf[sender] -= amount;
          balanceOf[recipient] += amount;
          emit Transfer(sender, recipient, amount);
          return true;
      }
  
      //새 토큰을 만드는 함수 
      //ERC20 표준함수는 아니지만 많은 경우에 볼 수 있음
      function mint(uint amount) external {
          balanceOf[msg.sender] += amount;
          totalSupply += amount;
          emit Transfer(address(0), msg.sender, amount);
      }
  
      //토큰을 소각하는 함수 
      //ERC20 표준함수는 아니지만 많은 경우에 볼 수 있음
      function burn(uint amount) external {
          balanceOf[msg.sender] -= amount;
          totalSupply -= amount;
          emit Transfer(msg.sender, address(0), amount);
      }
  }
  ```





### IERC20

- ERC20 의 인터페이스

- Sample code : 

  ```
  // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.17;
  
  // https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.0.0/contracts/token/ERC20/IERC20.sol
  interface IERC20 {
      
      //현재 존재하는 이 토큰의 전체 개수를 리턴한다. 
      function totalSupply() external view returns (uint); 
  
      //주소가 주어지면 해당 주소의 토큰 잔액을 반환한다. 
      function balanceOf(address account) external view returns (uint);
  
      //주소와 금액이 주어지면 해당 주소로 토큰의 양을 전송한다. 
      //전송을 실행하는 주소의 잔액에서 전송을 실행한다. 
      function transfer(address recipient, uint amount) external returns (bool);
  
      //소유자 주소와 지출자(spender) 주소가 주어지면 지출자가 출금할 수 있도록 소유자가 승인한 잔액을 리턴한다. 
      function allowance(address owner, address spender) external view returns (uint);
  
      //수취인 주소와 금액이 주어지면 그 주소가 승인을 한 계정에서 최대 금액까지 여러 번 송금할 수 있도록 승인한다. 
      function approve(address spender, uint amount) external returns (bool);
  
      //보낸 사람, 받는 사람 및 금액이 주어지면 한 계정에서 다른 계정으로 토큰을 전송한다. 
      //approve와 함께 조합해서 사용한다. 
      function transferFrom(
          address sender,
          address recipient,
          uint amount 
      )external returns (bool);
  
      //전송이 성공하면(transfer or transferFrom 호출) 이벤트가 트리거된다. 0 값 전송의 경우에도 마찬가지
      //토큰이 이동할때마다 로그를 남긴다. 
      event Transfer(address indexed from, address indexed to, uint value);
      //approve 함수가 실행되면 로그를 남긴다. 
      //approve를 성공적으로 호출하면 이벤트가 기록된다. 
      event Approval(address indexed owner, address indexed spender, uint value);
  }
  ```

- 키워드 정보

  - internal: internal은 스마트 컨트랙트의 interface로 비공개한다는 것입니다. 계약서(Contract)의 해당 내용을 비공개한다는 의미이며, 계약서의 내부에서만 사용하는 함수라는 것을 표시합니다. 상태변수(state variable)는 internal이 기본값입니다. 함수나 상태 변수에 접근 제어자를 쓰지 않고 비워둔다면 internal로 설정됩니다. 계약서 자신과 상속받은 계약서만 사용할 수 있습니다.
  - external: external은 스마트 컨트랙트의 interface로 공개한다는 것입니다. 계약서(Contract)의 해당 내용을 공개한다는 의미이며, 계약서의 외부에서 사용하는 함수라는 것을 표시합니다. 상태변수(state variable)는 external일 수 없습니다. 계약서 내부에서 사용할 경우 this를 사용해서 접근해야 합니다.
  - public: 공개 함수입니다. 공개 기능은 계약 인터페이스의 일부이며 내부적으로 또는 메시지를 통해 호출할 수 있습니다. 공개 상태 변수의 경우 자동 getter 함수가 생성됩니다.
  - private: 비공개 함수입니다. 비공개함수는 계약서 내부에서도 자신만 사용하는 함수라는 것을 표시합니다. 상태변수와 함수 모두 파생된 계약이 아닌 정의된 계약에서만 볼 수 있습니다
  - pure: storage에서 변수를 읽어오거나 쓰지 않는 함수임을 명시합니다.
  - constant, view : 상태를 변경하지 않는 함수임을 명시합니다.
  - payable: 입금을 받을 수 있는 함수임을 명시합니다..