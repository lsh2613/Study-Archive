# 특정 커밋 가져오기
![image](https://github.com/user-attachments/assets/e0fa0398-4c9f-4766-957f-8d4bfa16b3f8)
> 다음 사진과 같이 브랜치들이 있을 때 main 브랜치에서 cherry라는 특정 브랜치만 가져오고 싶을 때 cherry-pick을 사용할 수 있다.

- merge나 rebase를 사용하게 되면 fruit 브랜치의 커밋을 모두 가져오기 때문에 사용할 수 없다. 
- 또한 cherry-pick은 커밋을 옮기는 rebase와 달리 똑같은 커밋을 복제하여 가져오기 때문에 같은 내용의 다른 커밋 두 개가 만들어지는 것이다.

현재 main 브랜치에 존재할 때는 cherry 파일이 없는 것을 확인할 수 있다.

![image](https://github.com/user-attachments/assets/1568bcd8-ab0f-449c-8d94-1a3efcc7f98f)

cherry의 커밋 해시를 통해 chrry-pick을 실행해보자

![image](https://github.com/user-attachments/assets/ba879f55-d1be-4354-9825-470f3e91d6bb)

## 결론
다음과 같이 cherry라는 파일이 생기고 main 브랜치에 cherry가 생겼지만 fruit에도 삭제되지 않고 그대로 있는 것을 확인해볼 수 있다
<br>

![image](https://github.com/user-attachments/assets/3f161144-8bb7-44c3-bbc0-0a80ab399d66)
![image](https://github.com/user-attachments/assets/5be5572a-9a29-479f-84dc-9be1429670f3)
