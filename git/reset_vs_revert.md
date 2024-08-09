# Reset
> 커밋 내역을 남겨두지 않고 모두 삭제하며 이전으로 돌아가는 것

# Revert
> 커밋 내역을 삭제하지 않은 채 이전 커밋을 되돌리는 것

# Reset vs Revert
1. reset은 reset했다는 ***기록***을 남기지 않고 돌아가지만 revert는 해당 커밋을 되돌렸다는 ***기록***을 남김
2. reset은 이전 커밋으로 되돌아가기 때문에 ***현재 시점부터 이전 커밋 사이의 커밋***들이 사라지지만, revert는 해당 커밋만 되돌리기 때문에 ***Head와 이전 커밋 사이의 커밋***들이 유지됨

### 예시
다음과 같이 3개의 커밋이 존재하고 각각의 커밋에서 file01~03.txt을 생성했다.

![스크린샷 2024-08-09 오후 7 33 22](https://github.com/user-attachments/assets/faa02fbb-e58b-48f1-9335-e3ca74ab0a30)

'add file01.txt'커밋에 대해 reset을 진행해보자
```
git reset --hard 7d46463
```
> git reset --hard [커밋해시] : Working Directory는 유지, Staging Area는 삭제
> 
> git reset --hard [커밋해시] : Working Directory는 유지, Staging Area에는 돌아간 시점 ~ 현재 까지의 작업 내용이 추가
>

![image](https://github.com/user-attachments/assets/9333cd01-7866-4061-a968-275fcd1482b5)
![image](https://github.com/user-attachments/assets/37289fdf-ac93-4348-ab4e-f0d651dcf5e6)
reset 결과로 현재 ~ 'add file01.txt' 이전 커밋인 'add file02.txt', 'add file03.txt' 커밋이 사라지고
file01.txt만 남게 되었다.

<br>

다음으로 revert를 진행해보자
```
git revert 7d46463
```
위 명령어를 통해 'add file01.txt'커밋을 revert를 진행하면 다음과 같이 revert에 대한 커밋을 저장하게 된다. 
![image](https://github.com/user-attachments/assets/0ccf72af-c0da-49a6-aa72-e49dfe0728db)

결론적으로 file01.txt는 사라지고 이전 커밋들은 유지되기 때문에 file02, file03만 남게된다.

![스크린샷 2024-08-09 오후 7 39 02](https://github.com/user-attachments/assets/6c0337fd-7e1a-46a1-aadd-3665b69135fa)
![스크린샷 2024-08-09 오후 7 39 11](https://github.com/user-attachments/assets/92ab58d8-9be4-412d-a9fb-e5e977f4dd16)

# 결론
- Reset은 현업 시 팀원들의 커밋을 삭제 시킬 수 있어 매우 조심히 사용해야 한다. 하지만 기록이 남지 않아 매우 깔끔하다. 혼자 사용하는 브랜치에서만 사용하자.
- Revert는 특정 커밋을 되돌리고 되돌린다는 커밋을 남긴다. 특정 시점으로 돌아가는 Reset과 달리 특정 커밋만을 되돌리기 때문에 충돌 가능성이 있다.
