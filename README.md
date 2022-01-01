<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 작성하여 PR을 보내주세요.

### 1단계 - 화면 응답 개선하기
1. 성능 개선 결과를 공유해주세요 (Smoke, Load, Stress 테스트 결과)

2. 어떤 부분을 개선해보셨나요? 과정을 설명해주세요

---

### 2단계 - 조회 성능 개선하기
1. 인덱스 적용해보기 실습을 진행해본 과정을 공유해주세요
```
select SQL_NO_CACHE e.사원번호, e.이름, s.연봉, p.직급명, m.입출입시간, m.지역, m.입출입구분 from 사원 e
inner join 
(select r.사원번호, r.지역, r.입출입구분, max(입출입시간) as 입출입시간 from 사원출입기록 r
inner join (
	select s.사원번호, s.연봉, p.직급명 from 부서관리자 dm
	inner join 부서 d
	on d.부서번호 = dm.부서번호
	inner join 급여 s
	on dm.사원번호 = s.사원번호
	inner join 직급 p
	on s.사원번호 = p.사원번호
	where d.비고 = 'active' 
	and s.종료일자 = '9999-01-01'
    and p.종료일자 = '9999-01-01'
	order by 연봉 desc
	limit 0, 5
) t
on r.사원번호 = t.사원번호
where r.입출입구분 = 'O'
group by r.사원번호, r.지역, r.입출입구분
order by null) m
on m.사원번호 = e.사원번호
inner join 직급 p
on e.사원번호 = p.사원번호
inner join 급여 s
on e.사원번호 = s.사원번호
where p.종료일자 = '9999-01-01'
and s.종료일자 = '9999-01-01';
```

```
사원출입기록에 사원번호, 지역, 입출입구분 복합칼럼을 가지는 인덱스 추가
```

![explain](https://user-images.githubusercontent.com/16433283/147825136-3523cd4f-dcca-4182-a4ad-7038d86340f9.png)
![image](https://user-images.githubusercontent.com/16433283/147825126-6a2e6078-abcf-40b4-998d-1b6ed8b04e62.png)


Coding as a Hobby 와 같은 결과를 반환하세요.

```
select SQL_NO_CACHE hobby, round(count(*) / (select count(*) from programmer p1) * 100,1) as rate from programmer p
group by hobby
order by null;
```

```
programmer 테이블에 hobby 인덱스 추가
programmer 테이블에 id primary key 추가
```

![hobby](https://user-images.githubusercontent.com/16433283/147824510-bee72740-3c1c-4acb-829b-efd96271cdca.png)
![image](https://user-images.githubusercontent.com/16433283/147824558-3fc71022-ab11-4359-9896-b2d806178796.png)


프로그래머별로 해당하는 병원 이름을 반환하세요. (covid.id, hospital.name)
```
select c.id, h.name from covid c
inner join programmer p
on c.programmer_id = p.id
inner join hospital h
on c.hospital_id = h.id;

```

```
covid 테이블의 id primary key 추가
hispital 테이블의 id primary key 추가
```

![프로그래머별 병원반환](https://user-images.githubusercontent.com/16433283/147815163-d54c5828-9a6a-4825-979a-a8238f523de8.png)
![image](https://user-images.githubusercontent.com/16433283/147824702-61595100-10c8-4743-a643-ca50cc3bad2b.png)


프로그래밍이 취미인 학생 혹은 주니어(0-2년)들이 다닌 병원 이름을 반환하고 user.id 기준으로 정렬하세요. (covid.id, hospital.name, user.Hobby, user.DevType, user.YearsCoding)
```
select SQL_NO_CACHE c.id, h.name, p.hobby, p.dev_type, p.years_coding from programmer p
inner join covid c 
on p.id = c.programmer_id
inner join hospital h
on c.hospital_id = h.id
where hobby = 'YES' 
and (p.years_coding = '0-2 years'
or student = 'Yes, full-time')
order by p.id;
```

```
covid 테이블에 programmer_id index 추가
```

![프로그래밍이취미](https://user-images.githubusercontent.com/16433283/147845166-44fc3c07-8ceb-4b48-b32b-b0813052a128.png)
![image](https://user-images.githubusercontent.com/16433283/147845170-170d8291-4ce8-4c96-8be4-55ddccbb6488.png)


서울대병원에 다닌 20대 India 환자들을 병원에 머문 기간별로 집계하세요. (covid.Stay)
```
select SQL_NO_CACHE c.stay, count(*) from programmer p
inner join covid c 
on p.id = c.programmer_id
inner join hospital h
on c.hospital_id = h.id
inner join member m
on p.member_id = m.id
where h.name = '서울대병원'
and m.age >= 20 and m.age < 30
and p.country = 'India'
group by c.stay
order by null
;
```

![인도](https://user-images.githubusercontent.com/16433283/147818428-da8e5ecd-7a6d-46d0-ae1a-28cd15e2bb6e.png)
![image](https://user-images.githubusercontent.com/16433283/147824872-c832a5b4-6dfe-41c7-8cfa-4633be9d3edb.png)


서울대병원에 다닌 30대 환자들을 운동 횟수별로 집계하세요. (user.Exercise)
```
select SQL_NO_CACHE p.exercise, count(*) from programmer p
inner join covid c 
on p.id = c.programmer_id
inner join hospital h
on c.hospital_id = h.id
inner join member m
on p.member_id = m.id
where h.name = '서울대병원'
and m.age >= 30 and m.age < 40
group by p.exercise
order by null
;
```

![운동](https://user-images.githubusercontent.com/16433283/147816839-52fd35d2-f9fa-42a0-821f-97a5cc75ee37.png)
![image](https://user-images.githubusercontent.com/16433283/147824887-fbb9f4e6-6940-4053-92ae-04c7c5721e80.png)


2. 페이징 쿼리를 적용한 API endpoint를 알려주세요
/stations?offset=0&size=10
