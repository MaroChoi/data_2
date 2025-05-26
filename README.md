[서론]

Column 설명 : 

변수명	          설명	                                                              변수 유형	             변수 역할
ai_usage_hours	  생성형 AI 도구 사용 시간 (단위: 시간)	                              연속형(quantitative)	 독립 변수
sleep_hours	      수면 시간 (최근 하루 기준)	                                          연속형	               독립 변수
distractions	    작업 중 방해 요소 수 (개수 기준)	                                    연속형	               독립 변수
coffee_intake_mg	카페인 섭취량 (mg 기준)	                                            연속형	               독립 변수
commits	          코드 커밋 수 (과제 제출 수 등)	                                      연속형	               파생 변수 구성용
bugs_reported	    오류 발생 횟수 (버그 리포트 수)	                                    연속형	               파생 변수 구성용
cognitive_load	  인지 부하 (1~10점 척도, 높을수록 피로)	                              연속형	               파생 변수 구성용
task_success	    과제 성공 여부 (0: 실패, 1: 성공)	                                  이진형(binary)	       종속 변수 (분류 분석)
QPI (파생)	      양적 생산성 지표 = commits - bugs_reported	                        연속형	               종속 변수 (회귀분석)
LPI (파생)	       질적 생산성 지표 = 0.5×task_success + 0.5×(1 - cognitive_load/10)	연속형	               종속 변수 (회귀분석)


양적 생산량 (QPI)
QPI = commits – bugs_reported
과제 또는 작업의 ‘양적 생산성’을 평가할 때는 ‘얼마나 많이 했는가’만 보면 안 됨
예: 커밋 20번 했지만, 그 중 오류가 15개라면 “성과”라고 보기 어려움

그래서 **성과량(commits)**에서 **실패량(bugs)**을 뺀 “순생산성(net productivity)” 개념이 필요함


질적 생산성 (LPI)
LPI = 0.5 × task_success + 0.5 × (1 – cognitive_load / 10)
질적 생산성은 단순히 ‘성공했냐’가 아니라, ‘얼마나 집중해서 잘 해냈는가’도 포함해야 함

task_success: 과제가 성공했는가? → 결과 기반 지표
cognitive_load: 과제 수행 중 인지적 피로/부담 → 과정 기반 지표

cognitive_load는 1~10점 사이로 높을수록 피로, 효율이 떨어진다고 가정
→ 이를 **(1 – cognitive_load / 10)**으로 역변환하여 "집중 효율"로 바꿈
두 요소를 0.5씩 가중합한 이유는:

task_success는 성과, cognitive_load는 질의 효율성 → 둘 다 질적 성과 구성요소로 보고 동등하게 반영

[본론]

다항 선형 1번 : 다항 회귀 변수 와 양적 생산량의 관계
X_multi = df[['ai_usage_hours', 'sleep_hours', 'distractions', 'coffee_intake_mg']]
y_multi = df['QPI']

1. 통계적 유의성 판단 (F-test, p-values)
F-statistic	47.65	매우 높음 → 전체 모델 유의미함
Prob (F-statistic)	6.76e-34	p < 0.001 → 모델 전체 유의함
각 변수의 p-value	ai_usage_hours: 0.026, sleep_hours: 0.001, coffee_intake_mg: <0.001	모두 통계적으로 유의

결론: 대부분의 변수는 유의미하게 QPI에 영향을 주고 있음

2. 설명력 (R-squared)
R² = 0.278 (27.8%)
→ AI 사용 + 환경변수 4개로 생산성의 27.8%를 설명
→ 교육/사회 분야에선 상당히 높은 수준

결론: 모델의 설명력도 꽤 설득력 있음 (특히 행동/심리 데이터 기준)

3. 모델의 안정성
지표	값	해석
Durbin-Watson = 2.078	거의 이상적 (2에 가까움) → 오토코릴레이션 없음	
Cond. No. = 3.03e+03	다중공선성 경계 수준이지만 심각하지 않음	
Jarque-Bera p = 0.943	정규성 만족 (잔차 분포 문제 없음)	

결론: 회귀 전제 조건(선형성, 독립성, 정규성 등)도 문제 없음

4. 해석 가이드 (실무/발표용)
변수	계수	p값	해석
ai_usage_hours	+0.258	0.026	AI 사용 시간 ↑ → QPI 유의미하게 증가
sleep_hours	+0.244	0.001	수면 시간 ↑ → QPI 증가, 강한 영향력
distractions	-0.048	0.470	통계적으로 유의하지 않음
coffee_intake_mg	+0.0095	<0.001	카페인 ↑ → QPI 증가, 다만 해석은 주의

시각화 첨부

다항 선형 2번 : 다항 회귀 변수 와 질적 생산량의 관계
