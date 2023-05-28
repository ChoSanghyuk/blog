



DAO에서 SQL Exceptiondl 발생 했을 때, AOP에서 받아서 그대로 exception을 삼키고자 했으나, 의도한 대로 안됨

예를 들어 DAO에서 Exception을 throw 하고

AOP에서 Exception을 받아 에러 로그만 발생하고 종료한다고 했을 때, 그대로 Exception throw한 것이 끝나는 것이 아님



DAO을 감싸는 service 혹은 Util 클래스에서도 DAO가 Exception을 throw한다고 명시되어 있기에, Exception 핸들링이 필요함

즉, AOP에서도 처리하고, Util 클래스에도 Exception이 전달됨. 



그리고  DAO에서 Exception을 throw 하지 않으면, Exception에 대한 처리가 없는 상태에서 Exception 발생해서 그대로 프로그램 종료됨/