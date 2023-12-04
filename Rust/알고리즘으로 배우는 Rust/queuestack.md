# [Rust] [백준] questStack (24511)



## 문제 요약

큐와 스택으로 이루어진 큐스택이 있다. 이 큐스택 내부의 큐와 스택은 초기에 하나의 원소만 들고 있으며, 하나의 원소가 들어오면 하나의 원소를 pop하여 다음 자료구조로 넘긴다. 원소 배열에서 하나씩 큐스택에 넣었을 때, 최종적으로 반환되는 원소들을 출력하는 문제이다.

https://www.acmicpc.net/problem/24511







```rust
use std::io;
use std::collections::VecDeque;

fn main() {

    let buf = io::read_to_string(io::stdin()).unwrap();
    let mut input  = buf.split_ascii_whitespace();
    
    let n:usize = input.next().unwrap().parse().unwrap();
    let is_queue:Vec<u32> = (0..n).map(|_| input.next().unwrap().parse().unwrap()).collect();
    let store:Vec<u32> = (0..n).map(|_| input.next().unwrap().parse().unwrap()).collect();
    let m:usize = input.next().unwrap().parse().unwrap();
    let input:Vec<u32> = (0..m).map(|_| input.next().unwrap().parse().unwrap()).collect();

    let rtn = solve(n, is_queue, store, input);
    for e in rtn {
        print!("{} ", e);
    }
}

pub fn solve(n:usize, is_queue:Vec<u32> , store:Vec<u32>, inputs:Vec<u32>) -> Vec<u32>{ 
  
  let mut rtn:Vec<u32> = Vec::with_capacity(inputs.len()); 
  let mut deque: VecDeque<u32> = VecDeque::new();

  for i in 0..n{
    if is_queue[i].eq(&0) { // eq는 참조끼리 비교
      deque.push_front(store[i]);
    }
  }
  for i in 0..inputs.len() {
    deque.push_back(inputs[i]);
    rtn.push(deque.pop_front().unwrap());
  }
  
  rtn
}
```

