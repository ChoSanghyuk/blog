# [Rust] [백준] questStack (24511)



## 문제 요약

큐와 스택으로 이루어진 큐스택이 있다. 이 큐스택 내부의 큐와 스택은 초기에 하나의 원소만 들고 있으며, 하나의 원소가 들어오면 하나의 원소를 pop하여 다음 자료구조로 넘긴다. 원소 배열에서 하나씩 큐스택에 넣었을 때, 최종적으로 반환되는 원소들을 출력하는 문제이다.



### 문제 링크

https://www.acmicpc.net/problem/24511





## 풀이

### 풀이법

스택은 가장 최근에 들어온 원소를 pop하기 때문에 스택 자료구조는 무시하고, 큐인 자료구조만 신경쓰면 된다. 또한, 하나의 원소씩만 들고 있는 여러 개의 큐는 사실상 하나의 큐와 동일하게 동작한다. 이에, 문제에서 제시하는 큐스택 내부의 자료구조를 전부 초기화하는 것이 아닌 하나의 큐만 초기화하면 된다. 대신, 큐스택 내부의 마지막 큐의 원소가 동작 상 제일 먼저 pop되기 때문에, 큐스택에서 뒤에 있는 큐의 원소들이 초기화한 큐의 앞쪽에 위치하도록 세팅하는 것이 필요하다.



### 풀이순서

1. console에서 입력받아, 문제에서 제시한 형태로 변수들을 초기화시킨다.
2. 큐스택에서 큐인 자료구조의 원소들만 Deque에 넣어주되, 먼저 접근되는 큐들의 원소가 Deque 뒤쪽에 위치할 수 있도록 앞 쪽으로 원소를 삽입한다.
3. 이후부터는 큐의 동작처럼, 뒤쪽에 입력된 원소를 넣고, 앞단의 원소를 pop하여 별도 Vec에 저장한다.
4. Vec에 저장된 원소들을 형태에 맞게 출력한다.



### 코드

```rust
use std::io;
use std::collections::VecDeque;

fn main() {
	
    // 1. console에서 입력받아, 문제에서 제시한 형태로 변수들을 초기화시킨다.
    let buf = io::read_to_string(io::stdin()).unwrap();
    let mut input  = buf.split_ascii_whitespace();
    
    let n:usize = input.next().unwrap().parse().unwrap();
    let is_queue:Vec<u32> = (0..n).map(|_| input.next().unwrap().parse().unwrap()).collect();
    let store:Vec<u32> = (0..n).map(|_| input.next().unwrap().parse().unwrap()).collect();
    let m:usize = input.next().unwrap().parse().unwrap();
    let input:Vec<u32> = (0..m).map(|_| input.next().unwrap().parse().unwrap()).collect();

    // 2~3
    let rtn = solve(n, is_queue, store, input);
    
    // 4.Vec에 저장된 원소들을 형태에 맞게 출력한다.
    for e in rtn {
        print!("{} ", e);
    }
}

pub fn solve(n:usize, is_queue:Vec<u32> , store:Vec<u32>, inputs:Vec<u32>) -> Vec<u32>{ 
  
  let mut rtn:Vec<u32> = Vec::with_capacity(inputs.len()); 
  let mut deque: VecDeque<u32> = VecDeque::new();

  // 2. 큐스택에서 큐인 자료구조의 원소들만 Deque에 넣어준다.
  // 이때, 먼저 접근되는 큐들의 원소가 Deque 뒤쪽에 위치할 수 있도록 앞 쪽으로 원소를 삽입한다.
  for i in 0..n{
    if is_queue[i].eq(&0) { // eq는 참조끼리 비교
      deque.push_front(store[i]);
    }
  }
  // 이후부터는 큐의 동작처럼, 뒤쪽에 입력된 원소를 넣고, 앞단의 원소를 pop하여 별도 Vec에 저장한다.
  for i in 0..inputs.len() {
    deque.push_back(inputs[i]);
    rtn.push(deque.pop_front().unwrap());
  }
  
  rtn
}
```



## 학습한 내용



### Console에서 입력 읽어 변환하기

- 한 줄 읽어오기

```rust
let mut store= String::new();
io::stdin().read_line(&mut store).unwrap();
```

- buffer에 console 전체 읽어오기

```rust
let buf = io::read_to_string(io::stdin()).unwrap(); // console에 모두 입력 후 EOF(window는 crtl+z) 입력 필요
```

:bulb: buffer를 공백단위로 나누어, Integer / Vec로 변환하기

```rust
let buf = io::read_to_string(io::stdin()).unwrap(); // buffer에 저장
let mut input  = buf.split_ascii_whitespace();	// 공백 단위로 쪼갬. SplitAsciiWhitespace라는 struc 구조로 반환
let n:usize = input.next().unwrap().parse().unwrap(); // 개별 String parsing
let is_queue:Vec<u32> = (0..n).map(|_| input.next().unwrap().parse().unwrap()).collect(); // Vec으로 변환
```



### 두 변수 비교 : PartialEq

- PartialEq : 동일성 확인을 실제로 수행하는 trait
- 구조

```rust
impl PartialEq for Puzzle {
    fn eq(&self, other: &Self) -> bool {
        (self.num_pieces == other.num_pieces) && (self.name == other.name)
    }
}
```

- 활용

```rust
is_queue[i].eq(&0) // is_queue는 Vec<u32> 타입의 변수
```

:bulb: `eq` 메소드는 `immutable reference` 타입의 self와 파라미터를 받음



### Deque 

- import

```rust
use std::collections::VecDeque;
```

- 초기화

```rust
let mut deque: VecDeque<u32> = VecDeque::new();
```

- 메소드

```rust
deque.push_front(store[i]); // 좌측 push
deque.push_back(inputs[i]); // 우측 push

deque.pop_front().unwrap(); // 좌측 pop (Option 반환)
deque.pop_back().unwrap(); // 좌측 pop (Option 반환)
```

