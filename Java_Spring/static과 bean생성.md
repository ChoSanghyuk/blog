



static 메소드가 bean 생성보다 빠르게 실행

=> static 메소드 내에서 applicationContextProvier.getApplicationContext().getBean() 실행 시, 

applicationContextProvier.getApplicationContext() 가 null값.