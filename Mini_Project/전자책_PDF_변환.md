# [Python] 전자책 캡처하여 PDF로 변환하기



본격적으로 Spring 공부를 하기 위해 교보문고에서 eBook을 구매하였는데, 문제가 생겼다. 









cv2 설치 시 에러 

ERROR: Could not find a version that satisfies the requirement cv2 => pip install opencv-python





pytesseract 실행 시

pytesseract.pytesseract.TesseractNotFoundError: tesseract is not installed or it's not in your PATH. See README file for more information.

=> https://github.com/tesseract-ocr/tesseract/releases

=> 환경변수-> 시스템 변수 추가

https://ddolcat.tistory.com/953





pdf_canvas.drawImage(img, x=0, y=0, width=letter[0], height=letter[1])

width, height에 맞게 사진을 조정함

=> letter과 사이즈가 맞지 않은 경우 확대 축소 되기에, 비율을 맞추는 것이 중요

